---
title: Azure 앱 구성에 전용 끝점 사용
description: 개인 끝점을 사용 하 여 앱 구성 저장소 보호
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: f18672b9e3a368a833fc8cba279d748dfe3c2a9e
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79366771"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>Azure 앱 구성에 전용 끝점 사용

Azure 앱 구성에 대해 [개인 끝점](../private-link/private-endpoint-overview.md) 을 사용 하 여 VNet (가상 네트워크)의 클라이언트가 [개인 링크](../private-link/private-link-overview.md)를 통해 데이터에 안전 하 게 액세스할 수 있도록 할 수 있습니다. 개인 끝점은 앱 구성 저장소에 대 한 VNet 주소 공간의 IP 주소를 사용 합니다. VNet의 클라이언트와 앱 구성 저장소 간의 네트워크 트래픽은 Microsoft 백본 네트워크의 개인 링크를 사용 하 여 VNet을 통과 하 여 공용 인터넷에 대 한 노출을 제거 합니다.

앱 구성 저장소에 대해 개인 끝점을 사용 하면 다음을 수행할 수 있습니다.
- 공용 끝점의 앱 구성에 대 한 모든 연결을 차단 하도록 방화벽을 구성 하 여 응용 프로그램 구성 세부 정보를 보호 합니다.
- Vnet에서 데이터가 이스케이프 되지 않도록 VNet (가상 네트워크)에 대 한 보안을 강화 합니다.
- 개인 피어 링을 사용 하 여 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [연결할 expressroutes](../expressroute/expressroute-locations.md) 를 사용 하 여 VNet에 연결 하는 온-프레미스 네트워크에서 앱 구성 저장소에 안전 하 게 연결 합니다.

> [!NOTE]
> Azure 앱 구성은 개인 끝점을 공개 미리 보기로 사용 하도록 제공 합니다. 퍼블릭 미리 보기 제품을 통해 고객은 공식 릴리스 전에 새로운 기능을 시험해 볼 수 있습니다.  퍼블릭 미리 보기 기능 및 서비스는 프로덕션 용도로 사용되지 않습니다.

## <a name="conceptual-overview"></a>개념적 개요

개인 끝점은 VNet ( [Virtual Network](../virtual-network/virtual-networks-overview.md) )의 Azure 서비스에 대 한 특별 한 네트워크 인터페이스입니다. 앱 구성 저장소에 대 한 개인 끝점을 만들 때 VNet과 구성 저장소의 클라이언트 간에 보안 연결을 제공 합니다. 개인 끝점에는 VNet의 IP 주소 범위에서 IP 주소가 할당 됩니다. 개인 끝점과 구성 저장소 간의 연결은 보안 개인 링크를 사용 합니다.

VNet의 응용 프로그램은 **다른 방법으로 사용 하는 것과 동일한 연결 문자열 및 권한 부여 메커니즘을 사용 하 여**개인 끝점을 통해 구성 저장소에 연결할 수 있습니다. 전용 끝점은 앱 구성 저장소에서 지 원하는 모든 프로토콜과 함께 사용할 수 있습니다.

앱 구성에서 서비스 끝점을 지원 하지 않지만 [서비스 끝점](../virtual-network/virtual-network-service-endpoints-overview.md)을 사용 하는 서브넷에서 개인 끝점을 만들 수 있습니다. 서브넷의 클라이언트는 서비스 끝점을 사용 하 여 다른 사용자에 게 액세스 하는 동안 개인 끝점을 사용 하 여 앱 구성 저장소에 안전 하 게 연결할 수 있습니다.  

VNet에서 서비스에 대 한 개인 끝점을 만들 때 서비스 계정 소유자에 게 승인 요청을 보냅니다. 개인 끝점의 생성을 요청 하는 사용자도 계정의 소유자 인 경우이 동의 요청은 자동으로 승인 됩니다.

서비스 계정 소유자는 [Azure Portal](https://portal.azure.com)에서 구성 저장소의 `Private Endpoints` 탭을 통해 동의 요청 및 개인 끝점을 관리할 수 있습니다.

### <a name="private-endpoints-for-app-configuration"></a>앱 구성에 대 한 개인 끝점 

개인 끝점을 만들 때 연결할 앱 구성 저장소를 지정 해야 합니다. 계정 내에 앱 구성 인스턴스가 여러 개 있는 경우 각 저장소에 대해 별도의 개인 끝점이 필요 합니다.

### <a name="connecting-to-private-endpoints"></a>전용 끝점에 연결

Azure는 DNS 확인에 의존 하 여 개인 링크를 통해 VNet에서 구성 저장소로 연결을 라우팅합니다. 앱 구성 저장소를 선택한 다음 **설정** > **액세스 키**를 선택 하 여 Azure Portal에서 연결 문자열을 신속 하 게 찾을 수 있습니다.  

> [!IMPORTANT]
> 공용 끝점에서 사용 하는 것 처럼 개인 끝점을 사용 하 여 앱 구성 저장소에 연결 하려면 동일한 연결 문자열을 사용 합니다. `privatelink` 하위 도메인 URL을 사용 하 여 저장소 계정에 연결 하지 마세요.

## <a name="dns-changes-for-private-endpoints"></a>전용 끝점에 대 한 DNS 변경

개인 끝점을 만들 때 구성 저장소에 대 한 DNS CNAME 리소스 레코드는 접두사가 `privatelink`인 하위 도메인의 별칭으로 업데이트 됩니다. 또한 Azure는 전용 끝점에 대 한 DNS A 리소스 레코드를 사용 하 여 `privatelink` 하위 도메인에 해당 하는 [개인 DNS 영역](../dns/private-dns-overview.md) 을 만듭니다.

VNet 외부에서 끝점 URL을 확인 하면 저장소의 공용 끝점으로 확인 됩니다. 개인 끝점을 호스트 하는 VNet 내에서 확인 되는 경우 끝점 URL은 개인 끝점으로 확인 됩니다.

Azure 방화벽 서비스를 사용 하 여 공용 끝점을 통해 VNet 외부의 클라이언트에 대 한 액세스를 제어할 수 있습니다.

이 접근 방식을 사용 하면 개인 끝점을 호스트 하는 VNet의 클라이언트와 VNet 외부의 클라이언트에 **동일한 연결 문자열을 사용 하 여** 저장소에 액세스할 수 있습니다.

네트워크에서 사용자 지정 DNS 서버를 사용 하는 경우 클라이언트는 개인 끝점 IP 주소에 대 한 서비스 끝점의 FQDN (정규화 된 도메인 이름)을 확인할 수 있어야 합니다. 개인 링크 하위 도메인을 VNet의 개인 DNS 영역에 위임 하도록 DNS 서버를 구성 하거나 개인 끝점 IP 주소를 사용 하 여 `AppConfigInstanceA.privatelink.azconfig.io`에 대 한 A 레코드를 구성 합니다.

> [!TIP]
> 사용자 지정 또는 온-프레미스 DNS 서버를 사용 하는 경우 `privatelink` 하위 도메인의 저장소 이름을 개인 끝점 IP 주소로 확인 하도록 DNS 서버를 구성 해야 합니다. 이렇게 하려면 `privatelink` 하위 도메인을 VNet의 개인 DNS 영역에 위임 하거나 DNS 서버에서 DNS 영역을 구성 하 고 DNS A 레코드를 추가 합니다.

## <a name="pricing"></a>가격

개인 끝점을 사용 하도록 설정 하려면 [표준 계층](https://azure.microsoft.com/pricing/details/app-configuration/) 앱 구성 저장소가 필요 합니다.  개인 링크 가격 정보에 대 한 자세한 내용은 [Azure 개인 링크 가격 책정](https://azure.microsoft.com/pricing/details/private-link)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

앱 구성 저장소에 대 한 개인 끝점을 만드는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [Azure Portal에서 개인 링크 센터를 사용 하 여 개인 끝점을 만듭니다.](../private-link/create-private-endpoint-portal.md)
- [Azure CLI를 사용 하 여 개인 끝점 만들기](../private-link/create-private-endpoint-cli.md)
- [Azure PowerShell를 사용 하 여 개인 끝점 만들기](../private-link/create-private-endpoint-powershell.md)

개인 끝점을 사용 하 여 DNS 서버를 구성 하는 방법을 알아봅니다.

- [Azure 가상 네트워크의 리소스 이름 확인](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [전용 끝점에 대 한 DNS 구성](/azure/private-link/private-endpoint-overview#dns-configuration)
