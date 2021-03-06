---
title: Azure Backup 보고서 구성
description: Log Analytics 및 Azure 통합 문서를 사용 하 여 Azure Backup에 대 한 보고서 구성 및 보기
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 651d1383f0f292895ed95c91bafd5206d4f04c2c
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2020
ms.locfileid: "78161204"
---
# <a name="configure-azure-backup-reports"></a>Azure Backup 보고서 구성

백업 관리자의 일반적인 요구 사항은 장기간에 걸쳐 있는 데이터를 기반으로 백업에 대 한 정보를 얻는 것입니다. 이러한 솔루션에는 사용 되는 클라우드 저장소의 할당 및 예측, 백업과 복원 감사, 다양 한 세분성 수준에서 주요 추세 식별 등을 위한 여러 사용 사례가 있을 수 있습니다.

현재 Azure Backup에서는 [Azure Monitor 로그](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) 와 [Azure 통합 문서](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)를 활용 하 여 전체 백업 공간에서 백업에 대 한 풍부한 통찰력을 얻을 수 있도록 하는 보고 솔루션을 제공 합니다. 이 문서에서는 백업 보고서를 구성 하 고 보는 방법을 설명 합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

* Backup 보고서는 azure vm, azure vm의 SQL, Azure Vm, Azure Backup 에이전트 (MARS), Azure Backup Server (MABS) 및 System Center DPM에서 SAP HANA/ASE에 대해 지원 됩니다.
* DPM 워크 로드의 경우 Backup 보고서는 DPM 버전 5.1.363.0 이상 및 에이전트 버전 2.0.9127.0 이상에서 지원 됩니다.
* MABS 워크 로드의 경우 Backup 보고서는 MABS Version 13.0.415.0 이상 및 에이전트 버전 2.0.9170.0 이상에 대해 지원 됩니다.
* 사용자가 액세스할 수 있는 LA (Log Analytics) 작업 영역으로 데이터를 전송 하는 동안 모든 백업 항목, 자격 증명 모음, 구독 및 지역에서 백업 보고서를 볼 수 있습니다. 
* 고객의 구독에 대 한 위임 된 액세스 권한이 있는 [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) 사용자 인 경우 azure Lighthouse에서 이러한 보고서를 사용 하 여 모든 테 넌 트의 보고서를 볼 수 있습니다.
* 로그 백업 작업에 대 한 데이터는 현재 보고서에 표시 되지 않습니다.

## <a name="getting-started"></a>시작

보고서 사용을 시작 하려면 아래에서 설명 하는 세 가지 단계를 따르세요.

1. **LA (Log Analytics) 작업 영역을 만들거나 기존 작업 영역을 사용 합니다.**

하나 이상의 LA 작업 영역을 설정 하 여 백업 보고 데이터를 저장 해야 합니다. 이 LA 작업 영역을 만들 수 있는 위치 및 구독은 자격 증명 모음이 존재 하는 위치 및 구독과는 독립적입니다. 

LA 작업 영역을 설정 하려면 [Azure Portal에서 Log Analytics 작업 영역 만들기](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 문서를 참조 하세요.

기본적으로 LA 작업 영역의 데이터는 30 일 동안 보존 됩니다. 더 긴 시간 간격으로 데이터를 보려면 LA 작업 영역의 보존 기간을 변경 합니다. 보존 기간을 변경 하려면 [Azure Monitor 로그를 사용 하 여 사용량 및 비용 관리](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage)문서를 참조 하세요.

2. **자격 증명 모음에 대 한 진단 설정 구성:**

Recovery Services 자격 증명 모음과 같은 Azure Resource Manager 리소스는 예약 된 작업 및 사용자가 트리거한 작업에 대 한 정보를 진단 데이터로 기록 합니다. 

Recovery Services 자격 증명 모음의 모니터링 섹션에서 **진단 설정** 을 선택 하 고 Recovery Services 자격 증명 모음 진단 데이터의 대상을 지정 합니다. [진단 이벤트 사용에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events).

![진단 설정 블레이드](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

또한 Azure Backup는 지정 된 범위의 모든 자격 증명 모음에 대 한 진단 설정 구성을 자동화 하는 기본 제공 Azure Policy 제공 합니다. 이 정책을 사용 하는 방법에 대해 알아보려면 다음 문서를 참조 하세요. [대규모로 자격 증명 모음 진단 설정 구성](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)

> [!NOTE]
> 진단을 구성 하 고 나면 초기 데이터 푸시를 완료 하는 데 최대 24 시간이 걸릴 수 있습니다. 데이터를 LA 작업 영역으로 이동 하 고 나면 보고서에 데이터를 즉시 표시 하지 못할 수 있습니다. 현재 부분에 대 한 데이터가 보고서에 표시 되지 않기 때문입니다 (자세한 내용은 [여기](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports)참조). 따라서 Log Analytics에 데이터를 보내도록 자격 증명 모음을 구성한 후 2 일 후에 보고서 보기를 시작 하는 것이 좋습니다.

3. **Azure Portal에 대 한 보고서 보기:**

데이터를 LA에 보내도록 자격 증명 모음을 구성한 후에는 자격 증명 모음 블레이드로 이동 하 고 **Backup reports** 메뉴 항목을 클릭 하 여 백업 보고서를 봅니다. 

![자격 증명 모음 대시보드](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

이 링크를 클릭 하면 백업 보고서 통합 문서가 열립니다.

> [!NOTE]
> * 현재 보고서의 초기 로드는 최대 1 분 정도 걸릴 수 있습니다.
> * Recovery Services 자격 증명 모음은 단지 백업 보고서의 진입점입니다. 백업 보고서 통합 문서가 자격 증명 모음의 블레이드에서 열리면 해당 하는 LA 작업 영역 집합을 선택 하 여 모든 자격 증명 모음에서 집계 된 데이터를 볼 수 있습니다.

다음은 보고서에 포함 된 다양 한 탭에 대 한 설명입니다.

* **요약** -요약 탭에서는 백업 공간에 대 한 개략적인 개요를 제공 합니다. 요약 탭에서 총 백업 항목 수, 사용한 총 클라우드 저장소, 보호 된 인스턴스 수 및 작업 유형별 작업 성공률을 한눈에 볼 수 있습니다. 특정 백업 아티팩트 형식에 대 한 자세한 내용을 보려면 해당 탭으로 이동 하세요.

![요약 탭](./media/backup-azure-configure-backup-reports/summary.png)

* **백업 항목** -백업 항목 탭에서는 백업 항목 수준에서 사용 되는 클라우드 저장소에 대 한 정보 및 추세를 볼 수 있습니다. 예를 들어 Azure VM 백업에서 SQL을 사용 하는 경우 백업 중인 각 SQL 데이터베이스에 사용 되는 클라우드 저장소를 확인할 수 있습니다. 특정 보호 상태의 백업 항목에 대 한 데이터를 표시 하도록 선택할 수도 있습니다. 예를 들어 탭 위쪽의 **보호 중지 됨** 타일을 클릭 하면 아래의 모든 위젯을 필터링 하 여 보호 중지 상태의 백업 항목에 대 한 데이터만 표시 합니다.

![백업 항목 탭](./media/backup-azure-configure-backup-reports/backup-items.png)

* **사용량** -사용량 탭을 사용 하 여 백업에 대 한 주요 청구 매개 변수를 볼 수 있습니다. 이 탭에 표시 되는 정보는 청구 엔터티 (보호 된 컨테이너) 수준에 있습니다. 예를 들어, DPM 서버를 Azure에 백업 하는 경우 DPM 서버에 사용 되는 보호 된 인스턴스 및 클라우드 저장소의 추세를 볼 수 있습니다. 마찬가지로 Azure Backup 또는 Azure Backup SAP HANA에서 SQL을 사용 하는 경우이 탭에서는 이러한 데이터베이스가 포함 된 가상 컴퓨터 수준에서 사용 관련 정보를 제공 합니다.

![사용 탭](./media/backup-azure-configure-backup-reports/usage.png)

* **작업** -작업 탭에서는 하루에 실패 한 작업 수 및 작업 실패의 상위 원인 등의 장기 실행 추세를 볼 수 있습니다. 집계 수준 및 백업 항목 수준에서이 정보를 볼 수 있습니다. 표에서 특정 백업 항목을 클릭 하면 선택한 시간 범위에서 해당 백업 항목에 대해 트리거되는 각 작업에 대 한 자세한 정보를 볼 수 있습니다.

![작업 탭](./media/backup-azure-configure-backup-reports/jobs.png)

* **정책** -정책 탭을 사용 하면 연결 된 항목 수와 같은 모든 활성 정책에 대 한 정보 및 지정 된 정책에 따라 백업 된 항목에서 사용 되는 총 클라우드 저장소에 대 한 정보를 볼 수 있습니다. 특정 정책을 클릭 하면 연결 된 각 백업 항목에 대 한 정보를 볼 수 있습니다.

![정책 탭](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="exporting-to-excel"></a>Excel로 내보내기

모든 위젯 오른쪽 위에 있는 아래쪽 화살표 단추 (테이블/차트)를 클릭 하면 기존 필터가 적용 된 그대로 해당 위젯의 콘텐츠가 Excel 시트로 내보내집니다. 더 많은 테이블 행을 Excel로 내보내려면 각 표 위쪽의 **페이지당 행** 수 드롭다운을 사용 하 여 페이지에 표시 되는 행 수를 늘릴 수 있습니다.

## <a name="pinning-to-dashboard"></a>대시보드에 고정

각 위젯의 위쪽에 있는 고정 아이콘을 클릭 하 여 위젯을 Azure Portal 대시보드에 고정 합니다. 이렇게 하면 필요한 가장 중요 한 정보를 표시 하도록 맞춤형 대시보드를 만들 수 있습니다.

## <a name="cross-tenant-reports"></a>교차 테 넌 트 보고서

여러 테 넌 트 환경에서 구독에 대 한 액세스를 위임 하는 [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) 사용자 인 경우 기본 구독 필터를 사용 하 여 (Azure Portal 오른쪽 위에 있는 필터 아이콘을 클릭 하 여) 데이터를 확인할 모든 구독을 선택할 수 있습니다. 이렇게 하면 테 넌 트 전체에서 LA 작업 영역을 선택 하 여 멀티 테 넌 트 보고서를 볼 수 있습니다.

## <a name="conventions-used-in-backup-reports"></a>백업 보고서에 사용 되는 규칙

* 필터는 각 탭에서 왼쪽에서 오른쪽으로, 위쪽에서 아래쪽으로 작동 합니다. 즉, 필터는 해당 필터의 오른쪽에 배치 된 모든 위젯에만 적용 됩니다. 
* 색이 지정 된 타일을 클릭 하면 해당 타일의 값과 관련 된 레코드에 대 한 타일 아래의 위젯을 필터링 합니다. 예를 들어 백업 항목 탭에서 *보호 중지 됨* 타일을 클릭 하면 아래의 표 및 차트를 필터링 하 여 ' 보호 중지 됨 ' 상태의 백업 항목에 대 한 데이터를 표시 합니다.
* 색이 지정 되지 않은 타일은 클릭할 수 없습니다.
* 현재 일부 날짜에 대한 데이터는 보고서에 표시되지 않습니다. 따라서 선택한 **시간 범위** 값이 ***지난 7 일***이면 보고서에 최근 7 일 (현재 날짜가 포함 되지 않음)에 대 한 레코드가 표시 됩니다.
* 이 보고서에는 선택한 시간 범위에서 **트리거되** 는 작업의 세부 정보 (로그 작업 제외)가 표시 됩니다. 
* 클라우드 저장소 및 보호 된 인스턴스에 대해 표시 되는 값은 선택한 시간 범위의 **끝** 에 있습니다.
* 보고서에 표시 되는 백업 항목은 선택한 시간 범위의 **끝** 에 있는 항목입니다. 선택한 시간 범위 중간에 삭제 된 백업 항목은 표시 되지 않습니다. 백업 정책에도 동일한 규칙이 적용 됩니다.

## <a name="query-load-times"></a>쿼리 로드 시간

백업 보고서의 위젯은 사용자의 LA 작업 영역에서 실행 되는 Kusto 쿼리로 구동 됩니다. 이러한 쿼리는 일반적으로 많은 양의 데이터를 처리 하는 작업을 포함 하 여 다양 한 정보를 사용 하 여 사용자가 대규모 백업 공간에서 보고서를 볼 때 즉시 로드 되지 않을 수 있습니다. 다음 표에서는 보고서를 표시 하는 백업 항목 수와 시간 범위에 따라 여러 위젯이 로드 하는 데 걸리는 대략적인 시간을 보여 줍니다.

| **# 데이터 원본**                         | **시간 간격** | **로드 시간 (대략적인)**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~ 5 K                       | 1개월          | 타일: 5-10 초 <br> 그리드: 5-10 초 <br> 차트: 5-10 초 <br> 보고서 수준 필터: 5-10 초|
| ~ 5 K                       | 3개월          | 타일: 5-10 초 <br> 그리드: 5-10 초 <br> 차트: 5-10 초 <br> 보고서 수준 필터: 5-10 초|
| ~ 10 K                       | 3개월          | 타일: 15-20 초 <br> 그리드: 15-20 초 <br> 차트: 1-2 분 <br> 보고서 수준 필터: 25-30 초|
| ~ 15 K                       | 1개월          | 타일: 15-20 초 <br> 그리드: 15-20 초 <br> 차트: 50-60 초 <br> 보고서 수준 필터: 20-25 초|
| ~ 15 K                       | 3개월          | 타일: 20-30 초 <br> 그리드: 20-30 초 <br> 차트: 2-3 분 <br> 보고서 수준 필터: 50-60 초 |

## <a name="what-happened-to-the-power-bi-reports"></a>Power BI 보고서는 어떻게 되었습니까?
* 이전 Power BI 템플릿 앱 (Azure Storage 계정에서 데이터를 소싱)은 사용 중단 경로에 있습니다. 보고서를 보려면 위에서 설명한 대로 Log Analytics에 자격 증명 모음 진단 데이터를 보내기 시작 하는 것이 좋습니다.

* 또한 진단 데이터를 저장소 계정 또는 LA 작업 영역으로 보내는 V1 스키마도 사용 중단 경로에 있습니다. 즉, V1 스키마를 기반으로 사용자 지정 쿼리 또는 자동화를 작성 한 경우에는 현재 지원 되는 V2 스키마를 사용 하도록 이러한 쿼리를 업데이트 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
[Azure Backup 모니터링 및 보고에 대 한 자세한 정보](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)