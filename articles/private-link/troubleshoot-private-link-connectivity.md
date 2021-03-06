---
title: Azure Private Link 연결 문제 해결
description: 개인 링크 연결을 진단 하는 단계별 지침
services: private-link
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: 1e5253d617c87d5869cebc817da6d265ebfdfa7e
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77539470"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>Azure Private Link 연결 문제 해결

이 문서에서는 Azure 개인 링크 설정에 대 한 연결의 유효성을 검사 하 고 진단 하는 단계별 지침을 제공 합니다.

Azure 개인 링크를 사용 하면 가상 네트워크의 개인 끝점을 통해 Azure Storage, Azure Cosmos DB 및 Azure SQL Database와 같은 Azure PaaS (platform as a service) 서비스와 Azure에서 호스트 되는 고객 또는 파트너 서비스에 액세스할 수 있습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동 하 여 공용 인터넷에서의 노출을 제거 합니다. 가상 네트워크에서 개인 링크 서비스를 만들어 고객에 게 개인적으로 제공할 수도 있습니다.

개인 링크 액세스에 대 한 Azure Load Balancer 표준 계층에서 실행 되는 서비스를 사용 하도록 설정할 수 있습니다. 서비스 소비자는 가상 네트워크 내에 개인 끝점을 만든 다음이 서비스에 매핑하여 개인적으로 액세스할 수 있습니다.

개인 링크와 함께 사용할 수 있는 연결 시나리오는 다음과 같습니다.

- 동일한 지역의 가상 네트워크
- 지역적으로 피어 링 가상 네트워크
- 글로벌 피어 링 가상 네트워크
- VPN 또는 Azure Express 경로 회로를 통한 온-프레미스 고객

## <a name="deployment-troubleshooting"></a>배포 문제 해결

개인 링크 서비스에 대 한 사용자가 선택한 서브넷에서 원본 IP 주소를 선택할 수 없는 경우 문제 해결을 위해 [개인 링크 서비스에서 네트워크 정책 사용 안 함](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) 정보를 검토 합니다.

원본 IP 주소를 선택 하는 서브넷에 대해 **privateLinkServiceNetworkPolicies** 설정을 사용 하지 않도록 설정 했는지 확인 합니다.

## <a name="diagnose-connectivity-problems"></a>연결 문제 진단

개인 링크 설정에 연결 문제가 발생 하는 경우 이러한 단계를 검토 하 여 모든 일반적인 구성이 예상 대로 작동 하는지 확인 합니다.

1. 리소스를 검색 하 여 개인 링크 구성을 검토 합니다.

    a. **개인 링크 센터**로 이동 합니다.

      ![개인 링크 센터](./media/private-link-tsg/private-link-center.png)

    b. 왼쪽 창에서 **개인 링크 서비스**를 선택 합니다.

      ![개인 링크 서비스](./media/private-link-tsg/private-link-service.png)

    다. 진단 하려는 개인 링크 서비스를 필터링 하 고 선택 합니다.

    d. 개인 끝점 연결을 검토 합니다.
     - 연결 하려는 개인 끝점이 **승인** 된 연결 상태와 함께 나열 되는지 확인 합니다.
     - **보류 중인**상태 이면 해당 상태를 선택 하 고 승인 합니다.

       ![개인 끝점 연결](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - 이름을 선택 하 여 연결 하려는 개인 끝점으로 이동 합니다. 연결 상태가 **승인**됨으로 표시 되는지 확인 합니다.

       ![개인 끝점 연결 개요](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - 두 쪽이 모두 승인 되 면 연결을 다시 시도 합니다.

    e. **속성** 탭의 **개요** 탭과 **리소스 ID** 에서 **별칭** 을 검토 합니다.
     - 이 서비스에 대 한 개인 끝점을 만드는 데 사용 하는 **별칭** 및 **리소스 id** 와 **별칭 및** **리소스 id** 정보가 일치 하는지 확인 합니다.

       ![별칭 정보 확인](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![리소스 ID 정보 확인](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. **개요** 탭에서 **표시 유형** 정보를 검토 합니다.
     - 구독이 **표시** 범위에 속하는지 확인 합니다.

       ![표시 유형 정보 확인](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g. **개요** 탭에서 **부하 분산 장치** 정보를 검토 합니다.
     - 부하 분산 장치 링크를 선택 하 여 부하 분산 장치로 이동할 수 있습니다.

       ![부하 분산 장치 정보 확인](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - 부하 분산 장치 설정이 예상 대로 구성 되었는지 확인 합니다.
       - **프런트 엔드 IP 구성을**검토 합니다.
       - **백 엔드 풀**을 검토 합니다.
       - **부하 분산 규칙**을 검토 합니다.

       ![부하 분산 장치 속성 확인](./media/private-link-tsg/pls-ilb-properties.png)

     - 이전 설정에 따라 부하 분산 장치가 작동 하는지 확인 합니다.
       - 부하 분산 장치 백 엔드 풀을 사용할 수 있는 서브넷 이외의 서브넷에서 VM을 선택 합니다.
       - 이전 VM에서 부하 분산 장치 프런트 엔드에 액세스 해 보세요.
       - 연결에서 부하 분산 규칙에 따라 백 엔드 풀에 연결 하면 부하 분산 장치가 작동 합니다.
       - Azure Monitor를 통해 부하 분산 장치 메트릭을 검토 하 여 데이터가 부하 분산 장치를 통과 하는지 확인할 수도 있습니다.

1. [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 를 사용 하 여 데이터 흐름을 확인 합니다.

    a. 개인 링크 서비스 리소스에서 **메트릭**을 선택 합니다.
     - **In** 또는 **bytes Out**을 선택 합니다.
     - 개인 링크 서비스에 연결 하려고 할 때 데이터가 흐르는 경우를 확인 합니다. 약 10 분의 지연이 발생 합니다.

       ![개인 링크 서비스 메트릭 확인](./media/private-link-tsg/pls-metrics.png)

1. 문제가 여전히 해결 되지 않으며 연결 문제가 여전히 있는 경우 [Azure 지원](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) 팀에 문의 하세요.

## <a name="next-steps"></a>다음 단계

 * [개인 링크 서비스 만들기 (CLI)](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)
 * [Azure 개인 끝점 문제 해결 가이드](troubleshoot-private-endpoint-connectivity.md)
