---
title: 시계열 ID를 선택 하기 위한 모범 사례-Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights 미리 보기에서 시계열 ID를 선택할 때의 모범 사례에 대해 알아봅니다.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: a62c2460698408f6a2bfa51c6638bdeaf88bb31f
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083534"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>시계열 ID 선택 모범 사례

이 문서에서는 Azure Time Series Insights 미리 보기 환경에 대 한 시계열 ID의 중요도와이를 선택 하기 위한 모범 사례를 요약 합니다.

## <a name="choose-a-time-series-id"></a>시계열 ID 선택

적절 한 시계열 ID를 선택 하는 것이 중요 합니다. 시계열 ID 선택은 데이터베이스에 대한 파티션 키를 선택하는 것과 같습니다. Time Series Insights 미리 보기 환경을 만들 때 필요 합니다. 

> [!IMPORTANT]
> 시계열 Id는 다음과 같습니다.
> * *대/소문자를 구분* 하는 속성: 문자 및 문자/소문자나는 검색, 비교, 업데이트 및 분할 시 사용 됩니다.
> * *변경할* 수 없는 속성: 만든 후에는 변경할 수 없습니다.

> [!TIP]
> 이벤트 원본이 IoT hub 인 경우 시계열 ID는 ***iothub***가 될 수 있습니다.

주요 모범 사례는 다음과 같습니다.

* 고유 값이 많은 파티션 키를 선택 합니다 (예: 수백 또는 수천). 대부분의 경우 JSON의 장치 ID, 센서 ID 또는 태그 ID 일 수 있습니다.
* 시계열 ID는 [시계열 모델](./time-series-insights-update-tsm.md)의 리프 노드 수준에서 고유해야 합니다.
* 시계열 ID의 속성 이름 문자열에 대 한 문자 제한은 128입니다. 시계열 ID의 속성 값에 대 한 문자 제한은 1024입니다.
* 시계열 ID의 고유한 속성 값이 누락 된 경우 null 값으로 처리 되 고 고유성 제약 조건의 동일한 규칙을 따릅니다.
* 시계열 ID로 최대 *세 개의* 키 속성을 선택할 수도 있습니다. 해당 조합은 시계열 ID를 나타내는 복합 키가 됩니다.  
  > [!NOTE]
  > 세 가지 주요 속성은 문자열 이어야 합니다.
  > 한 번에 하나의 속성 대신이 복합 키에 대해 쿼리해야 합니다.

## <a name="select-more-than-one-key-property"></a>키 속성을 두 개 이상 선택

다음 시나리오에서는 시간 계열 ID로 둘 이상의 키 속성을 선택 하는 방법을 설명 합니다.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>예제 1: 고유 키가 있는 시계열 ID

* 자산의 레거시 대규모 있습니다. 각에는 고유한 키가 있습니다.
* 한 가지는 속성 **deviceId**에 의해 고유 하 게 식별 됩니다. 다른의 경우 고유한 속성은 **objectId**입니다. 두 쪽 모두 다른 제의 고유한 속성을 포함 하지 않습니다. 이 예제에서는 두 개의 키 **deviceId** 및 **objectId**를 고유 키로 선택 합니다.
* Null 값이 허용 되 고 이벤트 페이로드에 속성의 존재 여부가 null 값으로 계산 되지 않습니다. 이는 각 이벤트 소스의 데이터에 고유한 시계열 ID가 있는 두 개의 이벤트 원본에 대 한 데이터 전송을 처리 하는 적절 한 방법 이기도 합니다.

### <a name="example-2-time-series-id-with-a-composite-key"></a>예 2: 복합 키가 있는 시계열 ID

* 동일한 자산 집합 내에서 여러 속성이 고유해야 합니다. 
* 스마트 건물의 제조업체 이며 모든 방에 센서를 배포 합니다. 각 방에는 일반적으로 **sensorId**에 대해 동일한 값이 있습니다. 예는 **sensor1**, **sensor2**및 **sensor3**입니다.
* 건물이 **flrRm**속성의 사이트에서 겹치는 층과 방 번호를 갖고 있습니다. 이러한 숫자에는 **1a**, **2b**및 **3a**와 같은 값이 있습니다.
* **Redmond**, **바르셀로나**및 **도쿄**와 같은 값을 포함 하는 속성 **위치가**있습니다. 고유성을 만들려면 시계열 ID 키로 **sensorId**, **flrRm**및 **location**의 세 가지 속성을 지정 합니다.

원시 이벤트 예제:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

Azure Portal에서 다음과 같이 복합 키를 입력할 수 있습니다. 

```JSON
[{"name":"sensorId","type":"String"},{"name":"flrRm","type":"String"},{"name":"location","type":"string"}]
```

## <a name="next-steps"></a>다음 단계

* [데이터 모델링](./time-series-insights-update-tsm.md)에 대해 자세히 알아보세요.

* [Azure Time Series Insights 미리 보기 환경을](./time-series-insights-update-plan.md)계획 합니다.