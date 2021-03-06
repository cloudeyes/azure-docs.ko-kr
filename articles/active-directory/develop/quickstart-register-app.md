---
title: '빠른 시작: Microsoft id 플랫폼을 사용 하 여 앱 등록 | Microsoft'
description: 이 빠른 시작에서는 Microsoft id 플랫폼을 사용 하 여 응용 프로그램을 추가 하 고 등록 하는 방법을 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 03/09/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: dc719064166be917d868c7b7fee6b126b4099840
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78892716"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>빠른 시작: Microsoft id 플랫폼을 사용 하 여 응용 프로그램 등록

이 빠른 시작에서는 Azure Portal에서 **앱 등록** 환경을 사용 하 여 응용 프로그램을 등록 합니다. 앱이 Microsoft id 플랫폼과 통합 되어 있습니다. 엔터프라이즈 개발자 및 SaaS (software as a service) 공급자는 Microsoft id 플랫폼과 통합 될 수 있는 상용 클라우드 서비스 또는 lob (기간 업무) 응용 프로그램을 개발할 수 있습니다. 통합은 이러한 서비스에 대 한 보안 로그인 및 권한 부여를 제공 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="register-a-new-application-using-the-azure-portal"></a>Azure Portal을 사용하여 새 애플리케이션 등록

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 계정이 둘 이상의 테 넌 트에 대 한 액세스를 제공 하는 경우 오른쪽 위 모서리에서 계정을 선택 합니다. 포털 세션을 원하는 Azure AD 테 넌 트로 설정 합니다.
1. **Azure Active Directory**를 검색하고 선택합니다. **관리**에서 **앱 등록**를 선택 합니다.
1. **새 등록**을 선택합니다.
1. **응용 프로그램 등록**에서 사용자에 게 표시할 의미 있는 응용 프로그램 이름을 입력 합니다.
1. 응용 프로그램을 사용할 수 있는 사용자를 다음과 같이 지정 합니다.

    | 지원되는 계정 유형 | Description |
    |-------------------------|-------------|
    | **이 조직 디렉터리의 계정만** | 업무용(LOB) 애플리케이션을 빌드하는 경우 이 옵션을 선택합니다. 디렉터리에 응용 프로그램을 등록 하지 않는 경우에는이 옵션을 사용할 수 없습니다.<br><br>이 옵션은 Azure AD 단일 테넌트에 매핑됩니다.<br><br>이 옵션은 디렉터리 외부에서 앱을 등록 하는 경우를 제외 하 고는 기본값입니다. 디렉터리 외부에 앱을 등록할 경우 기본값은 Azure AD 다중 테넌트 및 개인 Microsoft 계정입니다. |
    | **모든 조직 디렉터리의 계정** | 모든 비즈니스 및 교육용 고객을 대상으로 하려는 경우 이 옵션을 선택합니다.<br><br>이 옵션은 Azure AD 전용 다중 테넌트에 매핑됩니다.<br><br>앱을 Azure AD 전용 단일 테 넌 트로 등록 한 경우에는 Azure AD 다중 테 넌 트로 업데이트 하 고 **인증** 페이지를 통해 단일 테 넌 트로 다시 업데이트할 수 있습니다. |
    | **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정** | 가장 폭넓은 고객을 대상으로 하려면 이 옵션을 사용합니다.<br><br>이 옵션은 Azure AD 다중 테넌트 및 개인 Microsoft 계정에 매핑됩니다.<br><br>Azure AD 다중 테 넌 트 및 개인 Microsoft 계정으로 앱을 등록 한 경우 UI에서이 설정을 변경할 수 없습니다. 대신 애플리케이션 매니페스트 편집기를 사용하여 지원되는 계정 유형을 변경해야 합니다. |

1. **URI 리디렉션 (선택 사항)** 에서 빌드 중인 앱의 유형 ( **웹** 또는 **공용 클라이언트 (모바일 & 데스크톱)** 을 선택 합니다. 그런 다음 응용 프로그램에 대 한 리디렉션 URI 또는 회신 URL을 입력 합니다.

    * 웹 애플리케이션의 경우 앱의 기준 URL을 제공합니다. 예를 들어 `https://localhost:31544`은 로컬 컴퓨터에서 실행 중인 웹앱의 URL일 수 있습니다. 사용자는 이 URL을 사용하여 웹 클라이언트 애플리케이션에 로그인합니다.
    * 공용 클라이언트 애플리케이션의 경우 Azure AD에서 토큰 응답을 반환하는 데 사용하는 URI를 제공합니다. 애플리케이션에 고유하게 해당되는 값을 입력합니다(예: `myapp://auth`).

    웹 응용 프로그램 또는 네이티브 응용 프로그램에 대 한 예제는 [Microsoft id 플랫폼](https://docs.microsoft.com/azure/active-directory/develop)의 빠른 시작을 참조 하세요.

1. 작업을 마쳤으면 **등록**을 선택합니다.

    ![Azure Portal에서 새 응용 프로그램을 등록 하는 화면을 보여 줍니다.](./media/quickstart-add-azure-ad-app-preview/new-app-registration.png)

Azure AD는 앱에 고유한 응용 프로그램 또는 클라이언트 ID를 할당 합니다. 포털이 응용 프로그램의 **개요** 페이지를 엽니다. 응용 프로그램에 기능을 추가 하려면 브랜딩, 인증서 및 비밀, API 권한 등을 비롯 한 기타 구성 옵션을 선택할 수 있습니다.

![새로 등록 된 앱 개요 페이지의 예](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)

## <a name="next-steps"></a>다음 단계

웹 Api에 액세스 하는 방법을 알아보려면 다음 문서로 이동 합니다.
> [!div class="nextstepaction"]
> [빠른 시작: 웹 Api에 액세스 하도록 클라이언트 응용 프로그램 구성](quickstart-configure-app-access-web-apis.md)

* 사용 권한에 대 한 자세한 내용은 [Microsoft id 플랫폼 끝점의 사용 권한 및 동의](v2-permissions-and-consent.md)를 참조 하세요.

* 웹 Api를 노출 하려면 [빠른 시작: 웹 api를 노출 하도록 응용 프로그램 구성](quickstart-configure-app-expose-web-apis.md)을 참조 하세요.

* 지원 되는 계정을 관리 하려면 [빠른 시작: 응용 프로그램에서 지 원하는 계정 수정](quickstart-modify-supported-accounts.md)을 참조 하세요.

* 앱을 빌드하고 기능을 추가 하려면 [Microsoft id 플랫폼](https://docs.microsoft.com/azure/active-directory/develop)의 빠른 시작을 참조 하세요.

* 등록된 애플리케이션 및 이들 간의 관계를 나타내는 두 개의 Azure AD 개체에 대한 자세한 내용은 [애플리케이션 개체 및 서비스 주체 개체](app-objects-and-service-principals.md)를 참조하세요.

* 앱을 개발할 때 사용 해야 하는 브랜딩 지침에 대해 자세히 알아보려면 [응용 프로그램에 대 한 브랜딩 지침](howto-add-branding-in-azure-ad-apps.md)을 참조 하세요.
