---
title: Active Directory 인증-Azure Database for MySQL
description: 을 사용 하 여 인증 Azure Active Directory의 개념에 대해 알아봅니다 Azure Database for MySQL
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 960536c3f80aa7870d6f2056d8e95cd1a4338dfe
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299025"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>MySQL을 사용 하 여 인증을 위해 Azure Active Directory 사용

Azure ad (Microsoft Azure Active Directory) 인증은 Azure AD에 정의 된 id를 사용 하 여 Azure Database for MySQL에 연결 하는 메커니즘입니다.
Azure AD 인증을 사용 하 여 중앙 위치에서 데이터베이스 사용자 id 및 기타 Microsoft 서비스를 관리할 수 있습니다. 그러면 권한 관리가 간단해 집니다.

> [!IMPORTANT]
> Azure Database for MySQL에 대 한 Azure AD 인증은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure AD를 사용 하는 이점은 다음과 같습니다.

- 일관 된 방식으로 Azure 서비스에서 사용자 인증
- 한 장소에서 암호 정책 및 암호 회전 관리
- Azure Active Directory에서 지원 되는 여러 형태의 인증으로 암호를 저장할 필요가 없습니다.
- 고객이 외부(Azure AD) 그룹을 사용하여 데이터베이스 사용 권한을 관리할 수 있습니다.
- Azure AD 인증은 MySQL 데이터베이스 사용자를 사용 하 여 데이터베이스 수준에서 id를 인증 합니다.
- Azure Database for MySQL에 연결 하는 응용 프로그램에 대 한 토큰 기반 인증 지원

Azure Active Directory 인증을 구성 하 고 사용 하려면 다음 프로세스를 사용 합니다.

1. 필요에 따라 사용자 id를 사용 하 여 Azure Active Directory를 만들고 채웁니다.
2. 필요에 따라 현재 Azure 구독과 연결 된 Active Directory를 연결 하거나 변경 합니다.
3. Azure Database for MySQL 서버에 대 한 Azure AD 관리자를 만듭니다.
4. 데이터베이스에서 Azure AD id로 매핑된 데이터베이스 사용자를 만듭니다.
5. Azure AD id에 대 한 토큰을 검색 하 고 로그인 하 여 데이터베이스에 연결 합니다.

> [!NOTE]
> Azure AD를 만들고 채운 후 Azure Database for MySQL를 사용 하 여 Azure AD를 구성 하는 방법을 알아보려면 [Azure Database for MySQL에 대해 AZURE ad를 사용 하 여 구성 및 로그인](howto-configure-sign-in-azure-ad-authentication.md)을 참조 하세요.

## <a name="architecture"></a>Architecture

다음 고급 다이어그램은 Azure Database for MySQL에서 Azure AD 인증을 사용 하 여 인증을 작동 하는 방법을 요약 합니다. 화살표는 통신 경로 나타냅니다.

![인증 흐름][1]

## <a name="administrator-structure"></a>관리자 구조

Azure AD 인증을 사용 하는 경우 MySQL 서버에 대해 두 개의 관리자 계정이 있습니다. 원본 MySQL 관리자와 Azure AD 관리자 Azure AD 계정을 기반으로 하는 관리자만 사용자 데이터베이스에서 최초 Azure AD 포함 데이터베이스 사용자를 만들 수 있습니다. Azure AD 관리자 로그인은 Azure AD 사용자나 Azure AD 그룹이 될 수 있습니다. 관리자가 그룹 계정인 경우 모든 그룹 구성원에서 사용할 수 있으며 MySQL 서버에 대해 여러 Azure AD 관리자를 사용할 수 있습니다. 그룹 계정을 관리자로 사용 하면 MySQL 서버에서 사용자 또는 사용 권한을 변경 하지 않고도 Azure AD에서 그룹 구성원을 중앙에서 추가 하 고 제거할 수 있으므로 관리 효율성이 향상 됩니다. 한 번에 하나의 Azure AD 관리자(그룹 또는 사용자)를 구성할 수 있습니다.

![관리자 구조][2]

## <a name="permissions"></a>사용 권한

Azure AD를 사용 하 여 인증할 수 있는 새 사용자를 만들려면 Azure AD 관리자 여야 합니다. 이 사용자는 특정 Azure Database for MySQL 서버에 대 한 Azure AD 관리자 계정을 구성 하 여 할당 됩니다.

새 Azure AD 데이터베이스 사용자를 만들려면 Azure AD 관리자로 연결 해야 합니다. 이는 [Azure Database for MySQL에 대해 AZURE AD를 사용 하 여 구성 및 로그인](howto-configure-sign-in-azure-ad-authentication.md)에 설명 되어 있습니다.

Azure ad 인증은 Azure Database for MySQL에 대해 Azure AD 관리자를 만든 경우에만 가능 합니다. Azure Active Directory 관리자가 서버에서 제거 된 경우 이전에 만든 기존 Azure Active Directory 사용자가 Azure Active Directory 자격 증명을 사용 하 여 데이터베이스에 더 이상 연결할 수 없습니다.

## <a name="connecting-using-azure-ad-identities"></a>Azure AD ID를 사용하여 연결

Azure Active Directory 인증에서는 Azure AD ID를 사용하여 데이터베이스에 연결하는 다음 방법을 지원합니다.

- Azure Active Directory 암호
- Azure Active Directory 통합
- Azure Active Directory MFA 지원을 통한 유니버설 인증
- Active Directory 응용 프로그램 인증서 또는 클라이언트 암호 사용

Active Directory에 대해 인증 된 후에는 토큰을 검색 합니다. 이 토큰은 로그인에 대 한 암호입니다.

> [!NOTE]
> Active Directory 토큰을 사용 하 여 연결 하는 방법에 대 한 자세한 내용은 [Azure Database for MySQL에 대해 AZURE AD를 사용 하 여 구성 및 로그인](howto-configure-sign-in-azure-ad-authentication.md)을 참조 하세요.

## <a name="additional-considerations"></a>기타 고려 사항

- 한 번에 하나의 Azure AD 관리자만 Azure Database for MySQL 서버에 대해 구성할 수 있습니다.
- MySQL에 대 한 Azure AD 관리자만 처음에 Azure Active Directory 계정을 사용 하 여 Azure Database for MySQL에 연결할 수 있습니다. Active Directory 관리자가 이후의 Azure AD 데이터베이스 사용자를 구성할 수 있습니다.
- 사용자가 Azure AD에서 삭제 되 면 해당 사용자는 더 이상 Azure AD를 사용 하 여 인증할 수 없으므로 해당 사용자에 대 한 액세스 토큰을 획득할 수 없게 됩니다. 이 경우 일치 하는 사용자가 여전히 데이터베이스에 있더라도 해당 사용자를 사용 하 여 서버에 연결할 수 없게 됩니다.
> [!NOTE]
> 삭제 된 Azure AD 사용자에 대 한 로그인은 토큰이 만료 될 때까지 계속 수행할 수 있습니다 (토큰 발급에서 최대 60 분).  Azure Database for MySQL에서 사용자를 제거 하는 경우에도이 액세스 권한이 즉시 해지 됩니다.
- Azure AD 관리자가 서버에서 제거 되는 경우 서버는 더 이상 Azure AD 테 넌 트와 연결 되지 않으므로 서버에 대해 모든 Azure AD 로그인이 사용 되지 않습니다. 동일한 테 넌 트에서 새 Azure AD 관리자를 추가 하면 Azure AD 로그인이 다시 사용 하도록 설정 됩니다.
- Azure Database for MySQL은 사용자 이름을 사용 하는 대신 사용자의 고유한 Azure AD 사용자 ID를 사용 하 여 액세스 토큰과 Azure Database for MySQL 사용자를 일치 시킵니다. 즉, azure ad 사용자가 Azure AD에서 삭제 되 고 이름이 같은 새 사용자를 만든 경우 Azure Database for MySQL는 다른 사용자를 고려 합니다. 따라서 사용자가 Azure AD에서 삭제 되 고 이름이 같은 새 사용자가 추가 된 경우 새 사용자는 기존 사용자와 연결할 수 없게 됩니다.

## <a name="next-steps"></a>다음 단계

- Azure AD를 만들고 채운 후 Azure Database for MySQL를 사용 하 여 Azure AD를 구성 하는 방법을 알아보려면 [Azure Database for MySQL에 대해 AZURE ad를 사용 하 여 구성 및 로그인](howto-configure-sign-in-azure-ad-authentication.md)을 참조 하세요.
- 로그인 및 Azure Database for MySQL에 대 한 데이터베이스 사용자에 대 한 개요는 [Azure Database for MySQL에서 사용자 만들기](howto-create-users.md)를 참조 하세요.

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/admin-structure.png
