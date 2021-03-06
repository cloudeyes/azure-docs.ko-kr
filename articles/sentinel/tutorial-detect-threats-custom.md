---
title: Azure 센티널을 사용 하 여 의심 스러운 위협을 검색 하는 사용자 지정 분석 규칙 만들기 | Microsoft Docs
description: 이 자습서를 사용 하 여 Azure 센티널에서 의심 스러운 위협을 검색 하는 사용자 지정 분석 규칙을 만드는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: cea7429ecea105355b0afe306bfa334e55d5d9c4
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77585110"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>자습서: 의심 스러운 위협을 검색 하는 사용자 지정 분석 규칙 만들기

 [데이터 원본](quickstart-onboard.md) 를 Azure 센티널에 연결한 후에는 사용자 환경에서 특정 기준을 검색 하 고 조건이 일치 하는 경우 조사를 수행할 수 있도록 인시던트를 생성할 수 있는 사용자 지정 규칙을 만들 수 있습니다. 이 자습서에서는 사용자 지정 규칙을 만들어 Azure 센티널에서 위협을 검색 하는 방법을 안내 합니다.

이 자습서는 Azure 센티널로 위협을 검색 하는 데 도움이 됩니다.
> [!div class="checklist"]
> * 분석 규칙 만들기
> * 위협 응답 자동화

## <a name="create-custom-analytic-rules"></a>사용자 지정 분석 규칙 만들기

사용자 환경에서 의심 스러운 위협 및 비정상 유형을 검색 하는 데 도움이 되는 사용자 지정 분석 규칙을 만들 수 있습니다. 규칙은 사용자에 게 즉시 알림이 표시 되도록 하 여 위협을 심사 하 고 조사 하 고 수정할 수 있습니다.

1. Azure Sentinel 아래의 Azure Portal에서 **Analytics**를 선택합니다.

1. 상단 메뉴 모음에서 **+ 만들기** 를 선택 하 고 **예약 된 쿼리 규칙**을 선택 합니다. 그러면 **분석 규칙 마법사**가 열립니다.

    ![예약 된 쿼리 만들기](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. **일반** 탭에서 고유한 **이름**및 **설명을**입력 합니다. **전술** 필드에서 규칙을 분류 하기 위한 공격 범주 중에서 선택할 수 있습니다. 필요에 따라 경고 **심각도** 를 설정 합니다. 규칙을 만들 때 **상태** 는 기본적으로 **사용 하도록 설정** 되어 있습니다. 즉, 규칙 만들기를 완료 한 후 즉시 실행 됩니다. 즉시 실행 하지 않으려면 **사용 안 함**을 선택 합니다. 규칙은 **활성 규칙** 탭에 추가 되 고 필요할 때 해당 규칙에서 사용 하도록 설정할 수 있습니다.

    ![사용자 지정 분석 규칙 만들기 시작](media/tutorial-detect-threats-custom/general-tab.png)

1. **규칙 논리 설정** 탭에서 **규칙 쿼리** 필드에 직접 쿼리를 작성 하거나 Log Analytics에서 쿼리를 만든 다음 복사 하 여 붙여 넣을 수 있습니다.
 
   ![Azure 센티널에서 쿼리 만들기](media/tutorial-detect-threats-custom/settings-tab.png)

   - 오른쪽의 **결과 미리 보기** 영역을 참조 하세요. 여기서 Azure 센티널은 쿼리가 생성 하는 결과 (로그 이벤트)의 수를 표시 하 고 쿼리를 작성 하 고 구성 하는 즉시 변경 합니다. 그래프는 **쿼리 일정** 섹션의 설정에 따라 결정 되는 정의 된 기간 동안의 결과 수를 보여 줍니다.
    - 쿼리에서 너무 많거나 너무 자주 발생 하는 경고를 트리거하는 경우에는 **경고 임계값** 섹션에서 기준을 설정할 수 있습니다.

      Azure 활동에서 비정상 리소스를 만들 때 경고를 표시 하는 샘플 쿼리는 다음과 같습니다.

      `AzureActivity
     \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
     \| where ActivityStatus == "Succeeded"
     \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

      > [!NOTE]
      > 쿼리 길이는 1 ~ 007e; 1만 자 여야 하며 "검색 \*" 또는 "union \*"을 포함할 수 없습니다.

    1. **엔터티 매핑** 섹션을 사용 하 여 쿼리 결과의 매개 변수를 Azure 센티널에서 인식할 수 있는 엔터티로 연결 합니다. 이러한 엔터티는 **인시던트 설정** 탭에서 인시던트에 경고를 그룹화 하는 등 추가 분석을 위한 기반을 형성 합니다.
    1. **쿼리 일정** 섹션에서 다음 매개 변수를 설정 합니다.

       1. 쿼리가 실행 되는 빈도 (5 분 마다, 하루에 한 번)를 제어 하려면 **매번 쿼리 실행** 을 설정 합니다.

       1. **마지막에서 조회 데이터** 를 설정 하 여 쿼리가 적용 되는 데이터의 기간을 결정 합니다. 예를 들어 지난 10 분의 데이터 또는 지난 6 시간의 데이터를 쿼리할 수 있습니다.

       > [!NOTE]
       > 이러한 두 설정은 한 지점까지 서로 독립적입니다. 간격 보다 긴 시간 동안 쿼리를 실행할 수 있지만 (겹치는 쿼리를 포함 하는 경우), 검사 기간을 초과 하는 간격으로 쿼리를 실행할 수 없습니다. 그렇지 않으면 전체 쿼리 범위에서 간격이 발생할 수 있습니다.

    1. **경고 임계값** 섹션을 사용 하 여 기준을 정의 합니다. 예를 들어 쿼리 결과가 실행 될 때마다 경고를 생성 하는 **1000 경우에** 만 경고를 생성 하는 경우 1000에만 경고를 생성 하도록 설정 하는 경우 **경고 생성** 을 설정 합니다. 이 필드는 필수 필드 이므로 기준을 설정 하지 않으려는 경우 (즉, 경고에서 모든 이벤트를 등록 하려는 경우) 숫자 필드에 0을 입력 합니다.

    1. 경고를 받은 후 쿼리 간격을 초과 하는 기간 동안이 규칙의 작업을 일시 중단 하려는 **경우에는** **억제** 섹션에서 **경고 생성 후 쿼리 실행 중지** 설정을 켤 수 있습니다. 이 옵션을 설정 하는 경우 실행 중지 **쿼리** 를 쿼리 실행을 중지 해야 하는 시간 (최대 24 시간)으로 설정 해야 합니다.

1. **인시던트 설정** 탭에서 Azure 센티널이 경고를 조치 가능한 인시던트에 설정 하는지 여부와 방법을 선택할 수 있습니다. 이 탭만 있으면 Azure 센티널은 각 및 경고에서 별도의 단일 인시던트를 만듭니다. 이 탭의 설정을 변경 하 여 인시던트를 만들지 않거나 여러 경고를 단일 인시던트에 그룹화 하도록 선택할 수 있습니다.

    1. **인시던트 설정** 섹션에서 **이 분석 규칙에 의해 트리거된 경고에서 인시던트 만들기** 가 기본적으로 **사용**으로 설정 됩니다. 즉, Azure 센티널은 규칙에 의해 트리거되는 각 경고와 개별 인시던트 하나를 만듭니다.<br></br>이 규칙을 사용 하 여 인시던트가 생성 되는 것을 원하지 않는 경우 (예:이 규칙이 후속 분석에 대 한 정보를 수집 하는 경우) **사용 안 함**으로 설정 합니다.

    1. **경고 그룹화** 섹션에서 비슷하거나 반복 되는 경고 그룹에서 단일 인시던트를 생성 하려는 경우 **이 분석 규칙에 의해 트리거되는 그룹 관련 경고** 를 **사용**하도록 설정 된 인시던트에 설정 하 고 다음 매개 변수를 설정 합니다.

    1. **선택한 시간 프레임 내에 생성 된 경고로 그룹 제한**:<br></br> 유사 하거나 반복 되는 경고를 그룹화 할 시간 프레임을 결정 합니다. 이 시간 프레임 내에 해당 하는 모든 경고는 아래 그룹화 설정에 따라 인시던트 또는 인시던트 집합을 전체적으로 생성 합니다. 이 시간 프레임 이외의 경고는 별도의 인시던트 또는 인시던트 집합을 생성 합니다.

    2. **이 분석 규칙에 의해 트리거되는 경고를 단일 인시던트**로 그룹화 합니다. 경고를 그룹화 할 기준을 선택 합니다.

        - **모든 엔터티가 일치 하는 경우 경고를 단일 인시던트에 그룹화 합니다**. <br></br>위의 규칙 논리 설정 탭에 정의 된 대로 매핑된 각 엔터티에 대해 동일한 값을 공유 하는 경우 경고가 함께 그룹화 됩니다. 권장 설정입니다.

        - **이 규칙에 의해 트리거되는 모든 경고를 단일 인시던트에 그룹화 합니다**. <br></br>이 규칙에 의해 생성 된 모든 경고는 동일한 값을 공유 하지 않더라도 함께 그룹화 됩니다.

        - **선택한 엔터티가 일치 하는 경우 경고를 단일 인시던트에 그룹화 합니다**. <br></br>매핑된 엔터티 중 일부에 대해 동일한 값을 공유 하는 경우 (드롭다운 목록에서 선택할 수 있음) 경고는 함께 그룹화 됩니다. 예를 들어 원본 또는 대상 IP 주소를 기반으로 별도의 인시던트를 만들려는 경우이 설정을 사용 하는 것이 좋습니다.

    3. **닫힌 일치 인시던트 다시 열기**: 인시던트가 종결 되 고 (기본 문제가 해결 됨) 이후에 해당 인시던트에 그룹화 된 다른 경고가 생성 되 면이 설정을 **사용** 으로 설정 하 여 종료 된 인시던트가 다시 열리고 경고가 새 인시던트를 만들도록 하려면 사용 **안 함** 으로 유지 합니다.

1. 자동화 된 **응답** 탭에서 사용자 지정 규칙에 의해 경고가 생성 될 때 자동으로 실행 하려는 플레이 북를 선택 합니다. 플레이 북을 만들고 자동화 하는 방법에 대 한 자세한 내용은 [위협에 대응](tutorial-respond-threats-playbook.md)을 참조 하세요.

1. **검토 및 만들기** 를 선택 하 여 새 경고 규칙에 대 한 모든 설정을 검토 한 후 **만들기를 선택 하 여 경고 규칙을 초기화**합니다.
  
1. 경고가 생성 되 면 **활성 규칙**의 테이블에 사용자 지정 규칙이 추가 됩니다. 이 목록에서 각 규칙을 사용 하거나 사용 하지 않도록 설정 하거나 삭제할 수 있습니다.

1. 만든 경고 규칙의 결과를 보려면 **인시던트 페이지로 이동 하 여** 인시던트를 조사 하 고, [인시던트를 조사](tutorial-investigate-cases.md)하 고, 위협을 수정할 수 있습니다.


> [!NOTE]
> Azure 센티널에서 생성 된 경고는 [Microsoft Graph 보안](https://aka.ms/securitygraphdocs)을 통해 사용할 수 있습니다. 자세한 내용은 [Microsoft Graph 보안 경고 설명서](https://aka.ms/graphsecurityreferencebetadocs)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 센티널을 사용 하 여 위협 감지를 시작 하는 방법을 배웠습니다.

위협에 대 한 응답을 자동화 하는 방법을 알아보려면 [Azure 센티널에서 자동화 된 위협 응답을 설정](tutorial-respond-threats-playbook.md)합니다.

