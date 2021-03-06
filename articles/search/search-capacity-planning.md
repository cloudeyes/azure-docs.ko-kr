---
title: 쿼리 및 인덱스 워크 로드의 용량 조정
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 파티션 및 복제본 컴퓨터 리소스를 조정 합니다. 여기서 각 리소스는 청구 가능한 검색 단위로 가격이 책정 됩니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: e2ba5301b81b1a6f5de696ab4587cd8ff43e3c68
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462567"
---
# <a name="adjust-capacity-in-azure-cognitive-search"></a>Azure Cognitive Search의 용량 조정

특정 가격 책정 계층에서 [검색 서비스 및 잠금을 프로 비전](search-create-service-portal.md) 하기 전에 몇 분 정도 걸리며, 서비스에서 복제본 및 파티션의 역할을 이해 하 고, 리소스 수요에서 급증 및 dip를 수용할 수 있도록 서비스를 조정 하는 방법을 알아보세요.

Capacity는 [선택 하는 계층](search-sku-tier.md) 의 기능 (계층은 하드웨어 특성을 결정)과 프로젝션 된 워크 로드에 필요한 복제본 및 파티션 조합입니다. 조정의 계층 및 크기에 따라 용량을 추가 하거나 축소 하는 데에는 15 분에서 몇 시간까지 걸릴 수 있습니다. 

복제본 및 파티션 할당을 수정 하는 경우 Azure Portal를 사용 하는 것이 좋습니다. 포털은 계층의 최대 한도 미만으로 유지 되는 허용 가능한 조합에 제한을 적용 합니다. 그러나 스크립트 기반 또는 코드 기반 프로 비전 접근 방식이 필요한 경우에는 [Azure PowerShell](search-manage-powershell.md) 또는 [관리 REST API](https://docs.microsoft.com/rest/api/searchmanagement/services) 대체 해결 방법이 됩니다.

## <a name="terminology-replicas-and-partitions"></a>용어: 복제본 및 파티션

|||
|-|-|
|*파티션* | 읽기/쓰기 작업(예: 인덱스를 다시 작성하거나 새로 고치는 경우)을 위한 인덱스 스토리지 및 I/O를 제공합니다. 각 파티션에는 총 인덱스의 공유가 있습니다. 3 개의 파티션을 할당 하면 인덱스가 1/3로 나뉩니다. |
|*복제본* | 검색 서비스의 인스턴스로 쿼리 작업을 부하 분산하는 데 주로 사용됩니다. 각 복제본은 인덱스의 복사본 하나입니다. 3 개의 복제본을 할당 하는 경우 쿼리 요청을 처리 하는 데 사용할 수 있는 인덱스의 복사본 3 개가 있습니다.|

## <a name="when-to-add-nodes"></a>노드를 추가 하는 경우

처음에는 서비스에 하나의 파티션과 하나의 복제본으로 구성 된 최소 수준의 리소스가 할당 됩니다. 

단일 서비스에는 모든 워크로드(인덱싱 및 쿼리)를 처리할 만큼 충분한 리소스가 있어야 합니다. 작업은 백그라운드에서 실행 되지 않습니다. 쿼리 요청이 자연스럽 게 자주 발생 하지 않는 시간에 대 한 인덱싱을 예약할 수 있지만, 서비스는 다른 작업에 대해 하나의 작업에 우선 순위를 지정 하지 않습니다. 또한 특정 중복성은 서비스 또는 노드가 내부적으로 업데이트 될 때 쿼리 성능을 향상 합니다.

일반적으로 검색 응용 프로그램은 특히 서비스 작업이 쿼리 작업을 중심으로 수행 되는 경우 파티션 보다 많은 복제본이 필요 합니다. [고가용성](#HA) 섹션에 그 이유가 설명되어 있습니다.

복제본 또는 파티션을 더 추가 하면 서비스 실행 비용이 늘어납니다. 더 많은 노드를 추가 하는 청구 의미를 이해 하려면 [가격 계산기](https://azure.microsoft.com/pricing/calculator/) 를 확인 해야 합니다. [아래 차트](#chart) 는 특정 구성에 필요한 검색 단위 수를 상호 참조 하는 데 도움이 될 수 있습니다.

## <a name="how-to-allocate-replicas-and-partitions"></a>복제본 및 파티션을 할당 하는 방법

1. [Azure Portal](https://portal.azure.com/) 에 로그인하고 검색 서비스를 선택합니다.

1. **설정**에서 **크기 조정** 페이지를 열어 복제본 및 파티션을 수정 합니다. 

   다음 스크린샷은 하나의 복제본과 파티션으로 프로 비전 된 표준 서비스를 보여 줍니다. 아래쪽의 수식은 사용 되는 검색 단위의 수를 나타냅니다 (1). 단가가 $100 (실제 가격이 아님) 인 경우이 서비스를 실행 하는 월간 비용은 평균적으로 $100이 됩니다.

   ![현재 값을 보여 주는 크기 조정 페이지](media/search-capacity-planning/1-initial-values.png "현재 값을 보여 주는 크기 조정 페이지")

1. 슬라이더를 사용 하 여 파티션 수를 늘리거나 줄입니다. 아래쪽의 수식은 사용 중인 검색 단위 수를 나타냅니다.

   이 예제에서는 두 개의 복제본과 파티션이 있는 두 개의 용량을 사용 합니다. 검색 단위 수를 확인 합니다. 요금 청구 수식은 복제본이 파티션 (2 x 2)을 곱하는 것 이므로 4입니다. 서비스를 실행 하는 비용의 두 배가 넘는 용량을 늘립니다. 검색 단위 비용이 $100 인 경우 이제 새로운 월간 청구는 $400이 됩니다.

   각 계층의 현재 단가 비용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)를 참조 하세요.

   ![복제본 및 파티션 추가](media/search-capacity-planning/2-add-2-each.png "복제본 및 파티션 추가")

1. **저장** 을 클릭 하 여 변경 내용을 확인 합니다.

   ![크기 조정 및 청구 변경 내용 확인](media/search-capacity-planning/3-save-confirm.png "크기 조정 및 청구 변경 내용 확인")

   용량 변경을 완료 하는 데 몇 시간이 걸립니다. 프로세스가 시작 된 후에는 취소할 수 없으며 복제본 및 파티션 조정을 위한 실시간 모니터링은 없습니다. 그러나 변경 내용이 진행 되는 동안에는 다음 메시지가 표시 됩니다.

   ![포털의 상태 메시지](media/search-capacity-planning/4-updating.png "포털의 상태 메시지")

> [!NOTE]
> 서비스를 프로 비전 한 후에는 더 높은 계층으로 업그레이드할 수 없습니다. 새 계층에서 검색 서비스를 만들고 인덱스를 다시 로드해야 합니다. 서비스 프로 비전에 대 한 도움말 [은 포털에서 Azure Cognitive Search 서비스 만들기](search-create-service-portal.md) 를 참조 하세요.
>
> 또한 파티션과 복제본은 서비스에 의해 배타적으로 관리 됩니다. 프로세서 선호도의 개념이 없거나 특정 노드에 작업을 할당 하는 것은 아닙니다.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>파티션 및 복제본 조합

기본 서비스는 정확히 한 개의 파티션과 최대 세 개의 복제본을 가질 수 있으며 최대 세 개의 SU로 제한됩니다. 유일하게 조정할 수 있는 리소스는 복제본입니다. 쿼리에서 고가용성을 위해 최소 두 개의 복제본이 필요합니다.

모든 표준 및 저장소 최적화 search 서비스는 36-SU 제한에 따라 다음과 같은 복제본 및 파티션의 조합을 가정할 수 있습니다. 

|   | **파티션 1개** | **파티션 2개** | **파티션 3개** | **파티션 4개** | **파티션 6개** | **파티션 12개** |
| --- | --- | --- | --- | --- | --- | --- |
| **복제본 1개.** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **복제본 2개** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **복제본 3개** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **복제본 4개** |4 SU |8 SU |12 SU |16 SU |24 SU |N/A |
| **복제본 5개** |5 SU |10 SU |15 SU |20 SU |30 SU |N/A |
| **복제본 6개** |6 SU |12 SU |18 SU |24 SU |36 SU |N/A |
| **복제본 12개** |12 SU |24 SU |36 SU |N/A |N/A |N/A |

SU, 가격 책정 및 용량에 대해서는 Azure Websites에 자세히 설명되어 있습니다. 자세한 내용은 [가격 정보](https://azure.microsoft.com/pricing/details/search/)를 참조하세요.

> [!NOTE]
> 복제본 및 파티션 수는 12를 고르게 나눌 수 있는 값입니다(특히 1, 2, 3, 4, 6, 12). 이는 Azure Cognitive Search에서 각 인덱스를 12 개의 분할 미리 나누어 모든 파티션에서 동일한 부분에 분산 될 수 있기 때문입니다. 예를 들어, 서비스에 파티션이 세 개 있는 상태에서 인덱스를 하나 만들 경우 각 파티션에는 분할된 인덱스가 4개가 포함됩니다. Azure Cognitive Search 분할의 인덱스는 구현 세부 정보 이며, 이후 릴리스에서 변경 될 수 있습니다. 지금은 그 숫자가 12이지만 이후에 12가 아닌 값으로 바뀔 수도 있습니다.
>

<a id="HA"></a>

## <a name="high-availability"></a>고가용성

확장이 쉽고 비교적 빠르기 때문에, 일반적으로 파티션 하나와 복제본 한두 개로 시작한 후 쿼리 볼륨이 커지면 확장하는 것이 좋습니다. 쿼리 작업은 복제본에서 주로 실행됩니다. 고가용성이나 처리량이 더 필요한 경우에는 추가 복제본이 필요할 가능성이 높습니다.

고가용성을 위한 일반적인 권장 사항은 다음과 같습니다.

* 읽기 전용 작업(쿼리)의 고가용성을 위한 복제본 두 개

* 읽기/쓰기 워크로드의 고가용성을 위한 복제본 세 개 이상(개별 문서가 추가, 업데이트 또는 삭제됨에 따라 쿼리 및 인덱싱)

Azure Cognitive Search에 대 한 SLA (서비스 수준 계약)는 문서 추가, 업데이트 또는 삭제로 구성 된 쿼리 작업 및 인덱스 업데이트를 대상으로 합니다.

기본 계층은 하나의 파티션과 세 개의 복제본에 우선합니다. 인덱싱 및 쿼리 처리량 모두에 대한 수요 변동에 즉시 응답하기 위한 유연성이 필요한 경우 표준 계층 중 하나를 사용하는 것이 좋습니다.  저장소 요구 사항이 쿼리 처리량 보다 훨씬 더 신속 하 게 증가 하는 경우 저장소에 최적화 된 계층 중 하나를 고려 합니다.

## <a name="disaster-recovery"></a>재해 복구

현재, 재해 복구를 위한 기본 제공 메커니즘은 없습니다. 파티션이나 복제본을 제거하는 것은 재해 복구 목표를 달성하기 위한 전략으로 올바르지 않습니다. 가장 일반적인 방법은 다른 지역에서 두 번째 검색 서비스를 설정하여 서비스 수준에서 중복성을 추가하는 것입니다. 인덱스를 다시 작성하는 동안 가용성과 마찬가지로 리디렉션 또는 장애 조치 논리를 사용자 코드에서 가져와야 합니다.

## <a name="estimate-replicas"></a>복제본 예상

프로덕션 서비스에서는 SLA를 위해 3 개의 복제본을 할당 해야 합니다. 쿼리 성능이 저하 되는 경우 더 큰 쿼리 작업을 지원 하 고 여러 복제본에 대 한 요청의 부하를 분산 하기 위해 인덱스의 추가 복사본을 온라인 상태로 만들 수 있습니다.

쿼리 로드를 수용 하는 데 필요한 복제본 수에 대 한 지침은 제공 하지 않습니다. 쿼리 성능은 쿼리 및 경쟁 작업의 복잡성에 따라 달라 집니다. 복제본을 추가하면 성능이 확실히 증가되지만 결과가 반드시 비례하지 않습니다. 복제본을 세 개 추가한다고 해서 처리량이 3배가 되는 것은 아닙니다.

솔루션에 대 한 QPS을 예측 하는 방법에 대 한 지침은 [성능 조정](search-performance-optimization.md)및 [쿼리 모니터링](search-monitor-queries.md) 을 참조 하세요.

## <a name="estimate-partitions"></a>파티션 추정

[선택한 계층](search-sku-tier.md) 은 파티션 크기와 속도를 결정 하 고 각 계층은 다양 한 시나리오에 맞는 특성 집합을 중심으로 최적화 됩니다. 더 높은 수준의 계층을 선택 하는 경우 S1로 이동 하는 경우 보다 더 많은 파티션이 필요할 수 있습니다. 자신에 게 전달 되는 테스트를 통해 대답 해야 하는 질문 중 하나는 하위 계층에서 프로 비전 되는 서비스에서 더 크고 비용이 많이 드는 파티션이 2 개 보다 저렴 한 파티션에 비해 더 나은 성능을 얻을 수 있는지 여부입니다.

거의 실시간으로 진행되는 데이터 새로 고침을 필요로 하는 검색 애플리케이션은 복제본보다 비례적으로 더 많은 파티션이 필요합니다. 파티션을 추가하면 많은 수의 컴퓨팅 리소스로 읽기/쓰기 작업이 분산됩니다. 또한 추가 인덱스와 문서를 저장하기 위한 더 많은 디스크 공간이 보장됩니다.

인덱스가 클수록 쿼리하는 데 더 오래 걸립니다. 따라서 파티션을 증분 방식으로 증가시킬 때마다 더 작지만 비례적으로 복제본도 늘려야 합니다. 쿼리의 복잡성과 쿼리 용량은 쿼리 실행이 얼마나 빠르게 진행되는지에 영향을 줍니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Cognitive Search에 대 한 가격 책정 계층 선택](search-sku-tier.md)