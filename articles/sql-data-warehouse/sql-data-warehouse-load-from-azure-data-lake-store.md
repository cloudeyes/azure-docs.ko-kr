---
title: 자습서 Azure Data Lake Storage에서 데이터 로드
description: PolyBase 외부 테이블을 사용 하 여 SQL analytics에 대 한 Azure Data Lake Storage에서 데이터를 로드 합니다.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 03/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: b0b9cffe0b69545a6d0219941b48ac9eb0f399b3
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300590"
---
# <a name="load-data-from-azure-data-lake-storage-for-sql-analytics"></a>SQL Analytics에 대 한 Azure Data Lake Storage에서 데이터 로드
이 가이드에서는 PolyBase 외부 테이블을 사용 하 여 Azure Data Lake Storage에서 데이터를 로드 하는 방법을 설명 합니다. Data Lake Storage에 저장 된 데이터에 대해 임시 쿼리를 실행할 수 있지만 최상의 성능을 위해 데이터를 가져오는 것이 좋습니다. 

> [!NOTE]  
> 로드 하는 대신 현재 공개 미리 보기로 제공 되는 [복사 문이](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) 있습니다.  COPY 문은 가장 뛰어난 유연성을 제공 합니다. COPY 문에 대 한 피드백을 제공 하려면 메일을 sqldwcopypreview@service.microsoft.com메일 그룹으로 보냅니다.
>
> [!div class="checklist"]

> * Data Lake Storage에서 로드 하는 데 필요한 데이터베이스 개체를 만듭니다.
> * Data Lake Storage 디렉터리에 연결 합니다.
> * 데이터 웨어하우스에 데이터를 로드 합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에
이 자습서를 시작하기 전에 최신 버전의 SSMS([SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms))를 다운로드하여 설치합니다.

이 자습서를 실행하려면 다음이 필요합니다.

* SQL 풀. [SQL 풀 만들기 및 데이터 쿼리](create-data-warehouse-portal.md)를 참조 하세요.
* Data Lake Storage 계정. [Azure Data Lake Storage 시작을](../data-lake-store/data-lake-store-get-started-portal.md)참조 하세요. 이 저장소 계정에는 저장소 계정 키, Azure Directory 응용 프로그램 사용자 또는 저장소 계정에 대 한 적절 한 RBAC 역할이 있는 AAD 사용자를 로드 하려면 다음 자격 증명 중 하나를 구성 하거나 지정 해야 합니다. 

##  <a name="create-a-credential"></a>자격 증명 만들기
AAD 통과를 사용 하 여 인증 하는 경우이 섹션을 건너뛰고 "외부 데이터 원본 만들기"를 진행할 수 있습니다. AAD 통과를 사용 하는 경우에는 데이터베이스 범위 자격 증명을 만들거나 지정할 필요가 없지만, AAD 사용자에 게는 저장소 계정에 대 한 적절 한 RBAC 역할 (저장소 Blob 데이터 판독기, 참가자 또는 소유자 역할)이 있어야 합니다. 자세한 내용은 [여기](https://techcommunity.microsoft.com/t5/Azure-SQL-Data-Warehouse/How-to-use-PolyBase-by-authenticating-via-AAD-pass-through/ba-p/862260)에 설명 되어 있습니다. 

Data Lake Storage 계정에 액세스 하려면 자격 증명 암호를 암호화 하는 데이터베이스 마스터 키를 만들어야 합니다. 그런 다음 암호를 저장 하는 데이터베이스 범위 자격 증명을 만듭니다. 서비스 주체 (Azure Directory 응용 프로그램 사용자)를 사용 하 여 인증 하는 경우 데이터베이스 범위 자격 증명은 AAD에 설정 된 서비스 주체 자격 증명을 저장 합니다. 데이터베이스 범위 자격 증명을 사용 하 여 Gen2에 대 한 저장소 계정 키를 저장할 수도 있습니다.

서비스 주체를 사용 하 여 Data Lake Storage에 연결 하려면 **먼저** Azure Active Directory 응용 프로그램을 만들고, 액세스 키를 만들고, Data Lake Storage 계정에 대 한 액세스 권한을 응용 프로그램에 부여 해야 합니다. 지침은 [Active Directory를 사용 하 여 Azure Data Lake Storage에 인증을](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)참조 하세요.

컨트롤 수준 권한이 있는 사용자로 SQL 풀에 로그인 하 고 데이터베이스에 대해 다음 SQL 문을 실행 합니다.

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B (for service principal authentication): Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    -- Always use the OAuth 2.0 authorization endpoint (v1)
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- B (for Gen2 storage key authentication): Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;

-- It should look something like this when authenticating using service principal:
CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>외부 데이터 원본 만들기
이 [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql) 명령을 사용하여 데이터의 위치를 저장합니다. AAD 통과를 사용 하 여 인증 하는 경우 자격 증명 매개 변수가 필요 하지 않습니다. 

```sql
-- C (for Gen1): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen1 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<datalakestoregen1accountname>.azuredatalakestore.net',
    CREDENTIAL = ADLSCredential
);

-- C (for Gen2): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen2 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION='abfs[s]://<container>@<AzureDataLake account_name>.dfs.core.windows.net', -- Please note the abfss endpoint for when your account has secure transfer enabled
    CREDENTIAL = ADLSCredential
);
```

## <a name="configure-data-format"></a>데이터 형식 구성
Data Lake Storage에서 데이터를 가져오려면 외부 파일 형식을 지정 해야 합니다. 이 개체는 Data Lake Storage에서 파일을 작성 하는 방법을 정의 합니다.
전체 목록은 [외부 파일 형식 만들기](/sql/t-sql/statements/create-external-file-format-transact-sql)의 T-SQL 설명서 참조

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store missing values as default for datatype.

CREATE EXTERNAL FILE FORMAT TextFileFormat
WITH
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE
                    )
);
```

## <a name="create-the-external-tables"></a>외부 테이블 만들기
이제 데이터 원본과 파일 형식을 지정했으니 외부 테이블을 만들 준비가 완료되었습니다. 외부 테이블은 외부 데이터와 상호 작용하는 방식입니다. 위치 매개 변수는 파일 또는 디렉터리를 지정할 수 있습니다. 디렉터리를 지정하는 경우 해당 디렉터리의 모든 파일을 로드합니다.

```sql
-- D: Create an External Table
-- LOCATION: Folder under the Data Lake Storage root folder.
-- DATA_SOURCE: Specifies which Data Source Object to use.
-- FILE_FORMAT: Specifies which File Format Object to use
-- REJECT_TYPE: Specifies how you want to deal with rejected rows. Either Value or percentage of the total
-- REJECT_VALUE: Sets the Reject value based on the reject type.

-- DimProduct
CREATE EXTERNAL TABLE [dbo].[DimProduct_external] (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureDataLakeStorage
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>외부 테이블 고려 사항
외부 테이블을 생성하는 것은 쉽지만 논의되어야 하는 몇 가지 미묘한 차이가 있습니다.

외부 테이블은 강력한 형식입니다. 즉, 수집되는 데이터의 각 행은 테이블 스키마 정의를 충족해야 합니다.
행이 스키마 정의와 일치하지 않는 경우 행은 로드에서 거부됩니다.

REJECT_TYPE 및 REJECT_VALUE 옵션을 사용하면 최종 테이블에 있어야 하는 행 수 또는 데이터의 비율을 정의할 수 있습니다. 로드 중 거부 값에 도달하는 경우 로드는 실패합니다. 거부된 행의 가장 일반적인 원인은 스키마 정의 불일치입니다. 예를 들어 파일의 데이터가 문자열일 때 열이 int의 스키마로 잘못 지정된 경우 모든 행을 로드하지 못합니다.

Data Lake Storage Gen1은 RBAC(역할 기반 액세스 제어)를 사용하여 데이터에 대한 액세스를 제어합니다. 즉, 서비스 주체는 위치 매개 변수에서 정의된 디렉터리와 최종 디렉터리 및 파일의 자식 항목에 대해 읽기 권한이 있어야 합니다. 이 경우 PolyBase는 해당 데이터를 인증하고 로드할 수 있습니다. 

## <a name="load-the-data"></a>데이터 로드
Data Lake Storage에서 데이터를 로드 하려면 [SELECT (transact-sql) 문을 CREATE TABLE](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) 사용 합니다. 

CTAS는 새 테이블을 만들고 select 문의 결과와 함께 새 테이블을 정보표시합니다. CTAS는 select 문의 결과에 부합하는 동일한 열과 데이터 형식을 가지도록 새 테이블을 정의합니다. 외부 테이블에서 모든 열을 선택하는 경우 새 테이블은 외부 테이블의 열과 데이터 형식의 복제본이 됩니다.

이 예제에서는 외부 테이블 DimProduct_external에서 DimProduct라는 해시 분산 테이블을 만듭니다.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Columnstore 압축을 최적화합니다.
기본적으로 테이블은 클러스터형 columnstore 인덱스로 정의 됩니다. 로드를 완료한 후 데이터 행 일부는 columnstore로 압축되지 않을 수 있습니다.  여기에는 다양한 이유가 있습니다. 자세한 내용은 [Columnstore 인덱스 관리](sql-data-warehouse-tables-index.md)를 참조하세요.

로드 후 쿼리 성능과 columnstore 압축을 최적화하려면 모든 행을 압축하기 위해 columnstore 인덱스를 강제 적용할 테이블을 다시 빌드합니다.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>통계를 최적화합니다.
로드 직후 단일 열 통계를 만드는 것이 가장 좋습니다. 통계에 대한 몇 가지 선택 사항이 있습니다. 예를 들어 모든 열에 단일 열 통계를 만드는 경우 모든 통계를 다시 작성하는 데 시간이 오래 걸릴 수 있습니다. 쿼리 조건자에 위치하지 않을 특정 열에 대해 알고 있다면 이들 열에 대한 통계 생성 과정은 건너뛸 수 있습니다.

단일 열 통계를 모든 테이블의 모든 열에 대해 만들기로 결정한 경우 `prc_sqldw_create_stats`통계[ 문서에 저장된 프로시저 코드 샘플 ](sql-data-warehouse-tables-statistics.md)를 사용할 수 있습니다.

다음 예제는 통계를 만들기 위한 좋은 출발점이 됩니다. 차원 테이블의 각 열과 팩트 테이블의 각 조인 열의 단일 열 통계를 생성합니다. 이후 언제라도 다른 팩트 테이블 열에 단일 또는 여러 열 통계를 추가할 수 있습니다.

## <a name="achievement-unlocked"></a>목표를 달성했습니다!
데이터 웨어하우스에 데이터를 성공적으로 로드 했습니다. 잘 하셨습니다!

## <a name="next-steps"></a>다음 단계 
이 자습서에서는 Data Lake Storage Gen1에 저장된 데이터에 대한 구조를 정의하는 외부 테이블을 만든 다음, PolyBase CREATE TABLE AS SELECT 문을 사용하여 데이터 웨어하우스에 데이터를 로드했습니다. 

다음 작업을 수행했습니다.
> [!div class="checklist"]
>
> * Data Lake Storage에서 로드 하는 데 필요한 데이터베이스 개체를 만들었습니다.
> * Data Lake Storage 디렉터리에 연결 되어 있습니다.
> * 데이터 웨어하우스에 데이터를 로드 했습니다.
>

데이터 로드는 Azure Synapse Analytics를 사용 하 여 데이터 웨어하우스 솔루션을 개발 하기 위한 첫 번째 단계입니다. 개발 리소스를 확인하세요.

> [!div class="nextstepaction"]
> [데이터 웨어하우징에 대 한 테이블을 개발 하는 방법 알아보기](sql-data-warehouse-tables-overview.md)
