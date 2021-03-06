---
title: 권장 보안 방법
description: Azure 위임 리소스 관리를 사용하는 경우 보안 및 액세스 제어를 고려하는 것이 중요합니다.
ms.date: 07/11/2019
ms.topic: conceptual
ms.openlocfilehash: 8972d6548eccb1006d90bfcbb4dba8c01b05a981
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270707"
---
# <a name="recommended-security-practices"></a>권장 보안 방법

Azure 위임 리소스 관리를 사용하는 경우 보안 및 액세스 제어를 고려하는 것이 중요합니다. 테넌트의 사용자가 고객 구독 및 리소스 그룹에 직접 액세스할 수 있으므로 테넌트의 보안을 유지하는 단계를 수행하는 것이 좋습니다. 또한 고객의 리소스를 효과적으로 관리하는 데 필요한 액세스만 허용할 수도 있습니다. 이 항목에서는 이 작업을 수행하는 데 도움이 되는 권장 사항을 제공합니다.

## <a name="require-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 필요

[Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (2 단계 확인이 라고도 함)는 공격자가 여러 인증 단계를 수행 하 여 계정에 액세스 하는 것을 방지 하는 데 도움이 됩니다. 고객 리소스에 대한 액세스 권한이 있는 사용자를 포함하여 서비스 공급자 테넌트의 모든 사용자에 대해 Multi-factor Authentication을 요구해야 합니다.

또한 고객에게 테넌트에서도 Azure Multi-Factor Authentication을 구현하도록 요청하는 것이 좋습니다.

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>최소 권한 원칙을 사용하여 그룹에 사용 권한 할당

관리를 용이하게 하려면 고객의 리소스를 관리하는 데 필요한 각 역할에 대해 Azure AD 사용자 그룹을 사용하는 것이 좋습니다. 이렇게 하면 해당 사용자에게 직접 사용 권한을 할당하는 대신, 필요에 따라 그룹에서 개별 사용자를 추가하거나 제거할 수 있습니다.

권한 구조를 만들 때 사용자가 작업을 완료하는 데 필요한 권한만 갖도록 하여 실수로 인한 오류 발생 가능성을 줄일 수 있게 최소 권한 원칙을 따라야 합니다.

예를 들어 다음과 같은 구조를 사용하려고 할 수 있습니다.

|그룹 이름  |Type  |principalId  |역할 정의  |역할 정의 ID  |
|---------|---------|---------|---------|---------|
|설계자     |사용자 그룹         |\<principalId\>         |참가자         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|평가     |사용자 그룹         |\<principalId\>         |판독기         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|VM 전문가     |사용자 그룹         |\<principalId\>         |VM 참가자         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|Automation     |SPN(서비스 주체 이름)         |\<principalId\>         |참가자         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

이러한 그룹을 만든 후 필요에 따라 사용자를 할당할 수 있습니다. 실제로 액세스 권한이 필요한 사용자만 추가합니다. 그룹 멤버 자격을 정기적으로 검토하고 더 이상 적절하지 않거나 포함할 필요가 없는 모든 사용자를 제거해야 합니다.

[퍼블릭 관리형 서비스 제품을 통해 고객을 온보딩](../how-to/publish-managed-services-offers.md)할 경우 포함하는 모든 그룹(또는 사용자 또는 서비스 주체)은 해당 플랜을 구입하는 모든 고객에 대해 동일한 사용 권한을 갖습니다. 각 고객에 작동할 다른 그룹을 할당하려면 각 고객에게 단독으로 사용되는 별도의 프라이빗 플랜을 게시하거나 Azure Resource Manager 템플릿을 사용하여 고객을 개별적으로 온보딩해야 합니다. 예를 들어, 액세스 권한이 매우 제한된 퍼블릭 플랜을 게시한 다음, 고객이 사용자 지정된 Azure Resource Manager 템플릿을 사용하여 추가 액세스를 위해 해당 리소스를 직접 온보딩하도록 하여 필요에 따라 추가 액세스 권한을 부여할 수 있습니다.


## <a name="next-steps"></a>다음 단계

- [Azure Multi-Factor Authentication을 배포합니다](../../active-directory/authentication/howto-mfa-getstarted.md).
- [테넌트 간 관리 환경](cross-tenant-management-experience.md)에 대해 알아봅니다.
