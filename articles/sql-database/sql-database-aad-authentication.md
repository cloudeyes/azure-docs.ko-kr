---
title: Azure Active Directory
description: SQL Database, Managed Instance 및 SQL Data Warehouse에서 인증에 Azure Active Directory를 사용하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 02/20/2019
ms.openlocfilehash: bc779df06d92d2483755ae888fda121b8e493a18
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269160"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql"></a>SQL에서 인증을 위해 Azure Active Directory 인증 사용

Azure Active Directory 인증은 Azure AD(Azure Active Directory)의 ID를 사용하여 Azure [SQL Database](sql-database-technical-overview.md), [Managed Instance](sql-database-managed-instance.md) 및 [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)에 연결하는 메커니즘입니다. 

> [!NOTE]
> 이 항목은 Azure SQL 서버 및 Azure SQL 서버에서 생성된 SQL Database와 SQL Data Warehouse 데이터베이스에 적용됩니다. 간단히 하기 위해 SQL Database는 SQL Database와 SQL Data Warehouse를 참조할 때 사용됩니다.

Azure AD 인증을 사용하면 데이터베이스 사용자 및 다른 Microsoft 서비스의 ID를 하나의 중앙 위치에서 관리할 수 있습니다. 중앙 ID 관리는 데이터베이스 사용자 관리를 위한 단일 위치를 제공하며 권한 관리를 간소화합니다. 이점은 다음과 같습니다.

- SQL Server 인증에 대한 대안을 제공합니다.
- 데이터베이스 서버 전체에서 사용자 ID의 확산을 중지합니다.
- 한 곳에서 암호를 회전할 수 있습니다.
- 고객이 외부(Azure AD) 그룹을 사용하여 데이터베이스 사용 권한을 관리할 수 있습니다.
- Windows 통합 인증 또는 Azure Active Directory에서 지원하는 기타 인증을 사용하여 암호 저장을 제거할 수 있습니다.
- Azure AD 인증에서는 포함된 데이터베이스 사용자를 통해 데이터베이스 수준에서 ID를 인증합니다.
- Azure AD는 SQL Database에 연결되는 애플리케이션에 대한 토큰 기반 인증을 지원합니다.
- Azure AD 인증은 도메인 동기화 없이 로컬 Azure Active Directory에 대해 ADFS(도메인 페더레이션) 또는 기본 사용자/암호 인증을 지원합니다.
- Azure AD는 MFA(Multi-Factor Authentication)를 포함하는 Active Directory 유니버설 인증을 사용하는 SQL Server Management Studio를 통해 연결하도록 지원합니다.  MFA는 전화 통화, 문자 메시지, 모바일 앱 알림 등의 여러 가지 간편한 검증 옵션을 제공하는 강력한 인증을 포함합니다. 자세한 내용은 [SQL Database 및 SQL Data Warehouse를 사용한 Azure AD MFA에 대한 SSMS 지원](sql-database-ssms-mfa-authentication.md)을 참조하세요.
- Azure AD는 Active Directory 대화형 인증을 사용하는 유사한 SSDT(SQL Server Data Tools) 연결을 지원합니다. 자세한 내용은 [SSDT(SQL Server Data Tools)의 Azure Active Directory 지원](/sql/ssdt/azure-active-directory)을 참조하세요.

> [!NOTE]  
> Azure VM에서 실행되는 SQL Server에 연결하는 경우 Azure Active Directory 계정은 사용할 수 없습니다. 대신 도메인 Active Directory 계정을 사용합니다.  

구성 단계에는 Azure Active Directory 인증의 구성 및 사용을 위한 다음 절차가 포함됩니다.

1. Azure AD를 만들고 채웁니다.
2. 옵션: 현재 Azure 구독과 연결된 Active Directory를 연결하거나 변경합니다.
3. Azure SQL Database 서버, Managed Instance 또는 [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/)에 대한 Azure Active Directory 관리자를 만듭니다.
4. 클라이언트 컴퓨터를 구성합니다.
5. Azure AD ID에 매핑된 데이터베이스에서 포함된 데이터베이스 사용자를 만듭니다.
6. Azure AD ID를 사용하여 데이터베이스에 연결합니다.

> [!NOTE]
> Azure AD를 만들고 채운 후 Azure SQL Database, Managed Instance 및 SQL Data Warehouse에서 Azure AD를 구성하는 방법은 [Azure SQL Database에서 Azure AD 구성](sql-database-aad-authentication-configure.md)을 참조하세요.

## <a name="trust-architecture"></a>트러스트 아키텍처

다음의 간략한 다이어그램에서는 Azure SQL Database에서 Azure AD 인증을 사용하는 솔루션 아키텍처를 요약 설명합니다. SQL Data Warehouse에 동일한 개념이 적용됩니다. Azure AD 기본 사용자 암호를 지원하기 위해 클라우드 부분 및 Azure AD/Azure SQL Database만 고려합니다. 페더레이션 인증(또는 Windows 자격 증명에 대한 사용자/암호)을 지원하려면 ADFS 블록과의 통신이 필요합니다. 화살표는 통신 경로 나타냅니다.

![aad auth 다이어그램][1]

다음 다이어그램은 토큰을 제출하여 클라이언트가 데이터베이스에 연결할 수 있는 페더레이션, 신뢰 및 호스팅 관계를 나타냅니다. 이 토큰은 Azure AD를 통해 인증되고 데이터베이스에서 신뢰됩니다. 고객 1은 기본 사용자가 있는 Azure Active Directory 또는 페더레이션된 사용자가 있는 Azure AD를 나타낼 수 있습니다. 고객 2는 가져온 사용자를 포함하는 가능한 해결 방법을 나타냅니다. 이 예에서는 Azure Active Directory와 동기화되는 ADFS로 페더레이션된 Azure Active Directory에서 가져옵니다. Azure AD 인증을 사용하는 데이터베이스에 액세스하려면 호스팅 구독이 Azure AD에 연결되어 있어야 합니다. Azure SQL Database 또는 SQL Data Warehouse를 호스트하는 SQL Server를 만들려면 동일한 구독을 사용해야 합니다.

![구독 관계][2]

## <a name="administrator-structure"></a>관리자 구조

Azure AD 인증을 사용할 때는 SQL Database 서버 및 Managed Instance에 대해 SQL Server 관리자와 Azure AD 관리자 등, 두 관리자 계정이 있습니다. SQL Data Warehouse에 동일한 개념이 적용됩니다. Azure AD 계정을 기반으로 하는 관리자만 사용자 데이터베이스에서 최초 Azure AD 포함 데이터베이스 사용자를 만들 수 있습니다. Azure AD 관리자 로그인은 Azure AD 사용자나 Azure AD 그룹이 될 수 있습니다. 관리자가 그룹 계정일 경우 모든 그룹 구성원이 사용할 수 있으므로 해당 SQL Server 인스턴스에 대해 여러 Azure AD 관리자가 가능합니다. 그룹 계정을 관리자로 사용하면 SQL Database에서 사용자나 권한을 변경하지 않고도 Azure AD의 그룹 구성원을 중앙에서 추가 및 제거할 수 있으므로 관리 효율성이 향상됩니다. 한 번에 하나의 Azure AD 관리자(그룹 또는 사용자)를 구성할 수 있습니다.

![관리자 구조][3]

## <a name="permissions"></a>사용 권한

새 사용자를 만들려면 데이터베이스에서 `ALTER ANY USER` 권한이 있어야 합니다. `ALTER ANY USER` 권한은 아무 데이터베이스 사용자에게나 부여할 수 있습니다. 서버 관리자 계정과, 해당 데이터베이스에 대한 `ALTER ANY USER` 또는 `CONTROL ON DATABASE` 권한이 있는 데이터베이스 사용자와, `ALTER ON DATABASE` 데이터베이스 역할 그룹의 구성원도 `db_owner` 권한을 보유할 수 있습니다.

Azure SQL Database, Managed Instance 또는 SQL Data Warehouse에 포함된 데이터베이스 사용자를 만들려면 Azure AD ID를 사용하여 데이터베이스 또는 인스턴스에 연결해야 합니다. 최초 포함 데이터베이스 사용자를 만들려면 Azure AD 관리자(데이터베이스 소유자)를 사용하여 데이터베이스에 연결해야 합니다. 이 내용은 [SQL Database 또는 SQL Data Warehouse에서 Azure Active Directory 인증 구성 및 관리](sql-database-aad-authentication-configure.md)에서 설명합니다. 모든 Azure AD 인증은 Azure Active Directory 관리자가 Azure SQL Database 또는 SQL Data Warehouse 서버에 대해 생성된 경우에만 가능합니다. Azure Active Directory 관리자가 서버에서 제거된 경우 SQL Server 내에 이전에 만든 기존 Azure Active Directory 사용자는 Azure Active Directory 자격 증명을 사용하여 데이터베이스에 더 이상 연결할 수 없습니다.

## <a name="azure-ad-features-and-limitations"></a>Azure AD 기능 및 제한 사항

- Azure AD의 다음 멤버를 Azure SQL Server 또는 SQL Data Warehouse에서 프로비저닝할 수 있습니다.

  - 기본 멤버: 고객 도메인 또는 관리되는 도메인 내 Azure AD에서 만든 멤버입니다. 자세한 내용은 [Azure AD에 고유한 도메인 이름을 추가](../active-directory/active-directory-domains-add-azure-portal.md)를 참조하세요.
  - 도메인 구성원 멤버: 페더레이션 도메인으로 Azure AD에 만들어진 멤버입니다. 자세한 내용은 [Microsoft Azure는 이제 Windows Server Active Directory와의 페더레이션 지원](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)을 참조하세요.
  - 네이티브 또는 페더레이션 도메인 멤버인 다른 Azure AD에서 가져온 멤버입니다.
  - 보안 그룹으로 만들어진 Active Directory 그룹.

- `db_owner` 서버 역할을 가진 그룹에 포함된 Azure AD 사용자는 Azure SQL Database 및 Azure SQL Data Warehouse에 **[CREATE DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** 구문을 사용할 수 없습니다. 다음 오류가 표시됩니다.

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    개별 Azure AD 사용자에게 직접 `db_owner` 역할을 부여하여 **CREATE DATABASE SCOPED CREDENTIAL** 문제를 해결하세요.

- Azure AD 보안 주체에 따라 실행될 경우 다음 시스템 함수는 NULL 값을 반환합니다.

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="managed-instances"></a>Managed Instance

- Azure AD 서버 보안 주체(로그인) 및 사용자는 [Managed Instance](sql-database-managed-instance.md)의 미리 보기 기능으로 지원됩니다.
- Azure AD 그룹에 매핑된 Azure AD 서버 보안 주체(로그인)를 데이터베이스 소유자로 설정하는 기능은 [Managed Instance](sql-database-managed-instance.md)에서 지원되지 않습니다.
    - 이것이 확장되어 그룹이 `dbcreator` 서버 역할의 일부로 추가되면 이 그룹의 사용자는 Managed Instance에 연결하여 새 데이터베이스를 만들 수 있지만 데이터베이스에 액세스할 수는 없습니다. 새 데이터베이스 소유자는 Azure AD 사용자가 아닌 SA이기 때문입니다. 개별 사용자가 `dbcreator` 서버 역할에 추가되면 이 문제가 발생하지 않습니다.
- Azure AD 서버 보안 주체(로그인)의 경우 SQL 에이전트 관리 및 작업 실행이 지원됩니다.
- 데이터베이스 백업 및 복원 작업은 Azure AD 서버 보안 주체(로그인)에서 실행될 수 있습니다.
- Azure AD 서버 보안 주체(로그인) 및 인증 이벤트와 관련된 모든 문을 감사할 수 있습니다.
- sysadmin 서버 역할의 구성원인 Azure AD 서버 보안 주체(로그인)의 관리자 전용 연결이 지원됩니다.
    - SQLCMD 유틸리티 및 SQL Server Management Studio를 통해 지원됩니다.
- 로그온 트리거는 Azure AD 서버 보안 주체(로그인)에서 발생하는 로그온 이벤트에 지원됩니다.
- Service Broker 및 DB 메일은 Azure AD 서버 보안 주체(로그인)를 사용하여 설정할 수 있습니다.


## <a name="connecting-using-azure-ad-identities"></a>Azure AD ID를 사용하여 연결

Azure Active Directory 인증에서는 Azure AD ID를 사용하여 데이터베이스에 연결하는 다음 방법을 지원합니다.

- Azure Active Directory 암호
- Azure Active Directory 통합
- Azure Active Directory MFA 지원을 통한 유니버설 인증
- 애플리케이션 토큰 인증 사용

Azure AD 서버 보안 주체(로그인)(**공개 미리 보기**)에 대해 지원되는 인증 방법은 다음과 같습니다.

- Azure Active Directory 암호
- Azure Active Directory 통합
- Azure Active Directory MFA 지원을 통한 유니버설 인증


### <a name="additional-considerations"></a>기타 고려 사항

- 관리 효율성을 높일 수 있게 관리자 권한으로 전용 Azure AD 그룹을 프로비전하는 것이 좋습니다.   
- Azure SQL Database 서버 또는 Azure SQL Data Warehouse에 대해 한 번에 하나의 Azure AD 관리자(그룹 또는 사용자)만 구성할 수 있습니다.
  - Managed Instance에 대한 Azure AD 서버 보안 주체(로그인)(**공개 미리 보기**)를 추가하면 `sysadmin` 역할에 추가할 수 있는 Azure AD 서버 보안 주체(로그인)가 여러 개 생성될 수 있습니다.
- SQL Server에 대한 Azure AD 관리자만 Azure Active Directory 계정을 사용하여 Azure SQL Database 서버, Managed Instance 또는 Azure SQL Data Warehouse에 처음 연결할 수 있습니다. Active Directory 관리자가 이후의 Azure AD 데이터베이스 사용자를 구성할 수 있습니다.   
- 연결 제한 시간은 30초로 설정하는 것이 좋습니다.   
- SQL Server 2016 Management Studio 및 Visual Studio 2015용 SQL Server Data Tools(버전 14.0.60311.1 2016년 4월 이상)는 Azure Active Directory 인증을 지원합니다. Azure AD 인증은 **.NET Framework Data Provider for SqlServer**(.NET Framework 4.6 버전 이상)에서 지원됩니다. 따라서 이러한 도구 및 데이터 계층 애플리케이션(DAC 및 .BACPAC)의 최신 버전에서는 Azure AD 인증을 사용할 수 있습니다.   
- 버전 15.0.1부터 [sqlcmd 유틸리티](/sql/tools/sqlcmd-utility) 및 [bcp 유틸리티](/sql/tools/bcp-utility)는 MFA를 사용하는 Active Directory 대화형 인증을 지원합니다.
- Visual Studio 2015용 SQL Server Data Tools는 Data Tools의 2016년 4월 버전 이상이 필요합니다(버전 14.0.60311.1). 현재 Azure AD 사용자는 SSDT 개체 탐색기에 표시되지 않습니다. 해결 방법으로 [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx)에서 사용자를 봅니다.   
- [SQL Server용 Microsoft JDBC 드라이버 6.0](https://www.microsoft.com/download/details.aspx?id=11774)은 Azure AD 인증을 지원합니다. 또한 [연결 속성 설정](https://msdn.microsoft.com/library/ms378988.aspx)을 참조하세요.   
- PolyBase는 Azure AD 인증을 사용하여 인증할 수 없습니다.   
- Azure AD 인증은 Azure Portal **데이터베이스 가져오기** 및 **데이터베이스 내보내기** 블레이드로 SQL Database에서 지원됩니다. Azure AD 인증을 사용한 가져오기 및 내보내기도 PowerShell 명령에서 지원됩니다.   
- Azure AD 인증은 CLI를 사용하여 SQL Database, Managed Instance 및 SQL Data Warehouse에 대해 지원됩니다. 자세한 내용은 [SQL Database 또는 SQL Data Warehouse에서 Azure Active Directory 인증 구성 및 관리](sql-database-aad-authentication-configure.md) 및 [SQL Server - az sql server](https://docs.microsoft.com/cli/azure/sql/server)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure AD를 만들고 채운 후 Azure SQL Database 및 Azure SQL Data Warehouse에서 Azure AD를 구성하는 방법은 [SQL Database, Managed Instance 또는 SQL Data Warehouse에서 Azure Active Directory 인증 구성 및 관리](sql-database-aad-authentication-configure.md)를 참조하세요.
- Managed Instance에 Azure AD 서버 보안 주체(로그인)를 사용하는 방법에 대한 자습서는 [Managed Instance를 사용하는 Azure AD 서버 보안 주체(로그인)](sql-database-managed-instance-aad-security-tutorial.md)를 참조하세요.
- SQL Database의 로그인, 사용자, 데이터베이스 역할 및 권한에 대 한 개요는 [로그인, 사용자, 데이터베이스 역할 및 사용 권한](sql-database-manage-logins.md)을 참조 하세요.
- 데이터베이스 보안 주체에 대한 자세한 내용은 [보안 주체](https://msdn.microsoft.com/library/ms181127.aspx)를 참조하세요.
- 데이터베이스 역할에 대한 자세한 내용은 [데이터베이스 역할](https://msdn.microsoft.com/library/ms189121.aspx)을 참조하세요.
- Managed Instance에 대한 Azure AD 서버 보안 주체(로그인)를 만드는 구문은 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)을 참조하세요.
- SQL Database의 방화벽 규칙에 대한 자세한 내용은 [SQL Database 방화벽 규칙](sql-database-firewall-configure.md)을 참조하세요.

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png
