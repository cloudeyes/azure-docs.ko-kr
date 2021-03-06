---
title: Multi-factor AAD 인증 사용
description: Azure SQL Database 및 Azure Synapse는 Active Directory 유니버설 인증을 사용 하 여 SSMS (SQL Server Management Studio)에서의 연결을 지원 합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/06/2020
tags: azure-synapse
ms.openlocfilehash: e9a4aa5b54cf7ed48daf1899bb5801c609dfbf32
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255874"
---
# <a name="using-multi-factor-aad-authentication-with-azure-sql-database-and-azure-synapse-analytics-ssms-support-for-mfa"></a>Azure SQL Database 및 Azure Synapse Analytics에서 Multi-factor AAD 인증 사용 (MFA에 대 한 SSMS 지원)
Azure SQL Database 및 Azure Synapse는 *Active Directory 유니버설 인증*을 사용 하 여 SSMS (SQL Server Management Studio)에서의 연결을 지원 합니다. 이 문서에서는 다양한 인증 옵션의 차이점 및 유니버설 인증 사용 시 관련된 제한사항을 설명합니다. 

**최신 SSMS 다운로드** - 클라이언트 컴퓨터에서 최신 SSMS 버전을 [SSMS(SQL Server Management Studio) 다운로드](https://msdn.microsoft.com/library/mt238290.aspx)에서 다운로드합니다. 


이 문서에서 설명하는 모든 기능에 대해서는 2017년 7월 버전 17.2를 사용합니다.  가장 최근의 연결 대화 상자는 다음 이미지와 비슷하게 표시됩니다.
 
  ![1mfa-universal-connect](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "사용자 이름 상자를 완료 합니다.")  

## <a name="the-five-authentication-options"></a>5가지 인증 옵션  

Active Directory Universal Authentication은 다음과 같은 두 가지 비 대화형 인증 방법을 지원 합니다.
    - `Active Directory - Password` 인증
    - `Active Directory - Integrated` 인증

두 가지 비대화형 인증 모델도 있으며, 이들은 다양한 응용 프로그램(ADO.NET, JDCB, ODC 등)에서 사용할 수 있습니다. 이러한 두 메서드는 팝업 대화 상자를 표시하지 않습니다. 
- `Active Directory - Password` 
- `Active Directory - Integrated` 

대화형 방법은 또한 Azure multi-factor authentication(MFA)을 지원합니다. 
- `Active Directory - Universal with MFA` 


Azure MFA를 사용하면 간단한 로그인 프로세스에 대한 사용자 요구를 충족하는 동시에 데이터 및 애플리케이션에 대한 액세스를 보호할 수 있습니다. 또한 전화 통화, 문자 메시지, PIN을 사용하는 스마트 카드, 모바일 앱 알림 등의 여러 가지 간편한 인증 옵션을 통해 강력한 인증을 제공하므로 사용자는 선호하는 방법을 선택할 수 있습니다. Azure AD를 사용하는 대화형 MFA는 유효성 검사를 위한 팝업 대화 상자를 표시할 수 있습니다.

Multi-Factor Authentication에 대한 설명을 보려면 [Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)을 참조하세요.
[SQL Server Management Studio에 대한 Azure SQL Database multi-factor authentication 구성](sql-database-ssms-mfa-authentication-configure.md)을 참조하세요.

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD 도메인 이름 또는 테넌트 ID 매개 변수   

[SSMS 버전 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)부터 다른 Azure Active 디렉터리에서 현재 Active Directory로 가져온 게스트 사용자는 연결할 때 Azure AD 도메인 이름 또는 테넌트 ID를 제공할 수 있습니다. 게스트 사용자에는 타 Azure AD에서 초대 받은 사용자, outlook.com, hotmail.com, live.com 등의 Microsoft 계정, gmail.com 등의 타 계정이 포함됩니다. 이 정보를 통해 **MFA를 통한 Active Directory 유니버설 인증**에서 올바른 인증 기관을 식별할 수 있습니다. 이 옵션은 outlook.com, hotmail.com 또는 live.com과 같은 Microsoft 계정(MSA)이나 비 MSA 계정을 지원하는 데도 필요합니다. 유니버설 인증을 사용하여 인증을 받으려면 이러한 모든 사용자는 Azure AD 도메인 이름 또는 테넌트 ID를 입력해야 합니다. 이 매개 변수는 Azure 서버와 연결되는 현재 Azure AD 도메인 이름/테넌트 ID를 나타냅니다. 예를 들어 Azure 서버가 사용자 `contosotest.onmicrosoft.com`이 Azure AD 도메인 `joe@contosodev.onmicrosoft.com`에서 가져온 사용자로 호스트되는 Azure AD 도메인 `contosodev.onmicrosoft.com`에 연결되는 경우 이 사용자를 인증하는 데 필요한 도메인 이름은 `contosotest.onmicrosoft.com`입니다. 사용자가 Azure 서버에 연결된 Azure AD의 네이티브 사용자이고 MSA 계정이 아닌 경우 도메인 이름 또는 테넌트 ID는 필요하지 않습니다. 매개 변수를 입력 하려면 (SSMS 버전 17.2부터) **데이터베이스에 연결** 대화 상자에서 MFA 인증을 **사용 하 여 Active Directory-유니버설** 인증을 선택 하 고 **옵션**을 클릭 한 다음 **사용자 이름** 상자를 입력 하 고 **연결 속성** 탭을 클릭 합니다. **AD 도메인 이름 또는 테 넌 트 id** 상자를 확인 하 고 도메인 이름 (**contosotest.onmicrosoft.com**) 또는 테 넌 트 id의 GUID와 같은 인증 기관을 제공 합니다.  
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

SSMS 18.x 이상을 실행 하는 경우에는 18.x 이상에서 자동으로 인식 하므로 게스트 사용자에 게 AD 도메인 이름 또는 테 넌 트 ID가 더 이상 필요 하지 않습니다.

   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Azure AD 기업 간 지원   
게스트 사용자로 Azure AD B2B 시나리오에 대해 지원 되는 azure AD 사용자 ( [AZURE B2B 공동 작업](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)참조)는 현재 azure ad에서 만든 그룹 멤버의 일부로만 SQL Database 및 azure Synapse에 연결 하 고 지정 된 데이터베이스의 transact-sql `CREATE USER` 문을 사용 하 여 수동으로 매핑할 수 있습니다. 예를 들어 `steve@gmail.com`이 Azure AD `contosotest`(Azure Ad 도메인 `contosotest.onmicrosoft.com`)에 초대된 경우 Azure AD 그룹(예: `usergroup`)이 `steve@gmail.com` 구성원을 포함하는 Azure AD에 만들어져야 합니다. 그런 다음 Transact-sql `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` 문을 실행 하 여 Azure AD SQL 관리자 또는 Azure AD DBO에서 특정 데이터베이스 (즉, MyDatabase)에 대해이 그룹을 만들어야 합니다. 데이터베이스 사용자가 만들어지면 `steve@gmail.com` 사용자가 `MyDatabase` SSMS 인증 옵션을 사용하여 `Active Directory – Universal with MFA support`에 로그인할 수 있습니다. 기본적으로 사용자 그룹은 연결 권한만 있으며 추가적인 액세스 권한은 일반적인 방식으로 부여 받아야 합니다. 게스트 사용자인 `steve@gmail.com` 사용자는 확인란을 선택하고 SSMS `contosotest.onmicrosoft.com`연결 속성**대화 상자에서** AD 도메인 이름을 추가해야 합니다. **AD 도메인 이름 또는 테넌트 ID** 옵션은 MFA 연결 옵션이 있는 유니버설에서만 지원되며 그 밖의 경우는 회색으로 표시됩니다.

## <a name="universal-authentication-limitations-for-sql-database-and-azure-synapse"></a>SQL Database 및 Azure Synapse에 대 한 유니버설 인증 제한 사항
- SSMS 및 SqlPackage.exe는 현재 Active Directory 유니버설 인증을 통해 MFA에서 사용할 수 있는 유일한 도구입니다.
- SSMS 버전 17.2는 MFA를 통한 유니버설 인증을 사용하는 다중 사용자 동시 액세스를 지원합니다. 버전 17.0 및 17.1에서는 유니버설 인증을 사용한 SSMS 인스턴스에 대한 로그인이 단일 Azure Active Directory 계정으로 제한되었습니다. 다른 Azure AD 계정으로 로그인하려면 SSMS의 다른 인스턴스를 사용해야 합니다. (이 제한은 Active Directory 유니버설 인증으로 제한됩니다. Active Directory 암호 인증, Active Directory 통합 인증 또는 SQL Server 인증을 사용하여 다른 서버에 로그인할 수 있습니다.)
- SSMS는 개체 탐색기, 쿼리 편집기 및 쿼리 저장소 시각화에 대해 Active Directory 유니버설 인증을 지원합니다.
- SSMS 버전 17.2는 데이터 데이터베이스 내보내기/추출/배포를 위한 DacFx Wizard 마법사 지원을 제공합니다. 특정 사용자가 유니버설 인증을 사용하여 최초 인증 대화 상자를 통해 인증되면 DacFx 마법사가 모든 다른 인증 방법에서와 같은 방식으로 작동합니다.
- SSMS 테이블 디자이너는 유니버설 인증을 지원하지 않습니다.
- 지원되는 버전의 SSMS를 사용해야 한다는 점을 제외하고, Active Directory 유니버설 인증에 대한 추가적인 소프트웨어 요구 사항은 없습니다.  
- 유니버설 인증을 위한 ADAL(Active Directory Authentication Library) 버전이 제공되는 최신 릴리스인 ADAL.dll 3.13.9로 업데이트되었습니다. [Active Directory 인증 라이브러리 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)을 참조하세요.  


## <a name="next-steps"></a>다음 단계

- [SQL Server Management Studio에 대한 Azure SQL Database multi-factor authentication 구성](sql-database-ssms-mfa-authentication-configure.md)을 참조하세요.
- 데이터베이스에 대한 액세스를 부여합니다. [SQL Database 인증 및 권한 부여: 액세스 부여](sql-database-manage-logins.md)  
- 방화벽을 통해 연결할 수 있는지 확인합니다. [Azure Portal을 사용하여 Azure SQL Database 서버 수준 방화벽 규칙 구성](sql-database-configure-firewall-settings.md)  
- [SQL Database 또는 Azure Synapse를 사용 하 여 Azure Active Directory 인증 구성 및 관리](sql-database-aad-authentication-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework(17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [새 Azure SQL Database로 BACPAC 파일 가져오기](../sql-database/sql-database-import.md)  
- [Azure SQL 데이터베이스를 BACPAC 파일로 내보내기](../sql-database/sql-database-export.md)  
- C# 인터페이스 [IUniversalAuthProvider Interface](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- **Active Directory- MFA 유니버설** 인증을 사용할 때 ADAL 추적은 [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)에서 시작할 수 있습니다. 기본적으로 꺼짐인 ADAL 추적은 **도구**, **옵션** 메뉴, **Azure Services**, **Azure Cloud**, **ADAL 출력 창 추적 수준**을 사용하고 **보기** 메뉴의 **출력**을 활성화하여 켤 수 있습니다. 추적은 **Azure Active Directory 옵션**을 선택할 때 출력 창에서 사용할 수 있습니다.  
