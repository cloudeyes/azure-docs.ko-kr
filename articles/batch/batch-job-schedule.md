---
title: 작업 예약
description: 작업 예약을 사용 하 여 작업을 관리 합니다.
services: batch
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.workload: big-compute
ms.date: 02/20/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 55ea8fb4cc0e65deaa89d718c4a46513716dcf54
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672434"
---
# <a name="schedule-jobs-for-efficiency"></a>효율성을 위해 작업 예약

일괄 처리 작업을 예약 하면 다른 태스크에 대 한 종속성이 있는 태스크를 고려 하는 동안 먼저 실행 하려는 작업의 우선 순위를 지정할 수 있습니다. 작업을 예약 하 여 최소한의 리소스를 사용 하 고 있는지 확인할 수 있습니다. 필요 하지 않은 경우 노드를 해제할 수 있습니다. 다른 작업에 종속 된 작업은 워크플로를 최적화 하는 시간에 분리 됩니다. 한 번에 하나의 작업만 실행 됩니다. 새 항목은 이전 작업이 완료 될 때까지 시작 되지 않습니다. 작업을 자동 완성으로 설정할 수 있습니다. 

## <a name="benefit-of-job-scheduling"></a>작업 예약의 장점

작업 예약의 혜택은 작업 생성 일정을 지정할 수 있다는 것입니다. 작업 관리자 태스크를 사용 하 여 예약 하는 작업은 작업과 연결 됩니다. 작업 관리자 태스크에서 작업에 대 한 태스크를 만듭니다. 이렇게 하려면 작업 관리자 태스크가 Batch 계정으로 인증 해야 합니다. AZ_BATCH_AUTHENTICATION_TOKEN 액세스 토큰을 사용 합니다. 토큰은 작업의 나머지 부분에 대 한 액세스를 허용 합니다. 

## <a name="use-the-portal-to-schedule-a-job"></a>포털을 사용 하 여 작업 예약

   1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.

   2. 작업을 예약 하려는 Batch 계정을 선택 합니다.

   3. **추가** 를 선택 하 여 새 작업 일정을 만들고 **기본 폼**을 완료 합니다.



![작업 예약][1]

**작업 일정 ID**:이 작업 일정에 대 한 고유 식별자입니다.

**표시 이름**: 작업의 표시 이름은 고유 하지 않아도 되지만 최대 길이는 1024 자입니다.

다음 시간 **까지 실행 안 함**: 작업을 실행할 가장 이른 시간을 지정 합니다. 이렇게 설정 하지 않으면 일정이 즉시 작업을 실행할 준비가 됩니다.

**다음 시간 이후에 실행 안 함**: 여기에서 설정한 시간 이후에 실행 되는 작업이 없습니다. 시간을 지정 하지 않으면 명시적으로 종료할 때까지 활성 상태로 유지 되는 되풀이 작업 일정을 만듭니다.

**되풀이 간격**: 작업 간 간격을 지정할 수 있습니다. 예약 된 시간에 작업을 하나만 포함할 수 있습니다. 따라서 작업 일정에 따라 새 작업을 만들 때 이전 작업이 아직 실행 되 고 있는 경우 Batch 서비스는 이전 작업이 완료 될 때까지 새 작업을 만들지 않습니다.  

**시작 창**: 여기에서 일정 시간부터 완료 될 때까지 작업이 생성 되어야 함을 나타내는 시간 간격을 지정할 수 있습니다. 현재 작업이 해당 기간 동안 완료 되지 않으면 다음 작업이 시작 되지 않습니다.

기본 양식 하단에서 작업을 실행 하려는 풀을 지정 합니다. 풀 ID 정보를 찾으려면 **업데이트**를 선택 합니다. 

![풀 지정][2]


**풀 ID**: 작업을 실행할 풀을 식별 합니다.

**작업 구성 작업**: **업데이트** 를 선택 하 여 작업 관리자 태스크의 이름을 표시 하 고 작업 준비 및 해제 태스크 (사용 중인 경우)를 표시 합니다.

**우선 순위**: 작업에 우선 순위를 지정 합니다.

**최대 벽 시계 시간**: 작업을 실행할 수 있는 최대 시간을 설정 합니다. 시간이 프레임 내에서 완료 되지 않으면 Batch는 작업을 종료 합니다. 이 값을 설정 하지 않으면 작업에 대 한 시간 제한이 없습니다.

**최대 태스크 다시 시도 횟수**: 태스크를 최대 4 번까지 다시 시도할 수 있는 횟수를 지정 합니다. 이는 API 호출에 포함 될 수 있는 재시도 횟수와 동일 하지 않습니다.

**모든 작업이 완료 되 면**: 기본값은 작업 없음입니다.

**태스크가 실패 하는 경우**: 기본값은 작업 없음입니다. 재시도 횟수가 소진 되었거나 작업을 시작할 때 오류가 발생 한 경우 태스크가 실패 합니다. 

**저장**을 선택한 후 왼쪽 탐색에서 **작업 일정** 으로 이동 하면 **실행 정보**를 선택 하 여 작업 실행을 추적할 수 있습니다.


## <a name="for-more-information"></a>참조 항목

Azure CLI를 사용 하 여 작업을 관리 하려면 [az batch job-schedule](https://docs.microsoft.com/cli/azure/batch/job-schedule?view=azure-cli-latest)을 참조 하십시오.

## <a name="next-steps"></a>다음 단계

[태스크 종속성을 만들어 다른 태스크에 종속 된 태스크를 실행](batch-task-dependencies.md)합니다.





[1]: ./media/batch-job-schedule/add_job_schedule-02.png
[2]: ./media/batch-job-schedule/add_job_schedule-03.png


