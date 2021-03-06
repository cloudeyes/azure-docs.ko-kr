---
title: Application Insights에 대한 릴리스 주석 | Microsoft Docs
description: Application Insights에서 배포 또는 빌드 표식을 메트릭 탐색기 차트에 추가합니다.
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: e0e2a106b276110e13b3c68889e4d1d349ba73a4
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666516"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Application Insights의 메트릭 차트에 대한 주석

[메트릭 탐색기](../../azure-monitor/app/metrics-explorer.md) 차트의 주석은 새 빌드를 배포한 위치 또는 기타 중요 한 이벤트를 표시 합니다. 주석을 사용 하면 변경 내용이 응용 프로그램의 성능에 어떤 영향을 미칠 수 있는지 쉽게 확인할 수 있습니다. [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/tasks/) 빌드 시스템에서 자동으로 만들 수 있습니다. PowerShell에서 주석을 만들어 원하는 이벤트에 대한 플래그를 지정하는 주석을 만들 수도 있습니다.

> [!NOTE]
> 이 문서에서는 사용하지 않는 **클래식 메트릭 환경**을 반영합니다. 주석은 현재 클래식 환경 및 **[workbook](../../azure-monitor/app/usage-workbooks.md)** 에서만 사용할 수 있습니다. 현재 메트릭 환경에 대해 자세히 알아보려면 [Azure 메트릭 탐색기의 고급 기능](../../azure-monitor/platform/metrics-charts.md)을 참조 하세요.

![주석 예제](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Azure Pipelines 빌드를 사용 하 여 주석 릴리스

릴리스 주석은 Azure DevOps의 클라우드 기반 Azure Pipelines 서비스에 대 한 기능입니다.

### <a name="install-the-annotations-extension-one-time"></a>주석 확장 설치(한 번)
릴리스 주석을 만들 수 있으려면 Visual Studio Marketplace에서 사용할 수 있는 여러 Azure DevOps 확장 중 하나를 설치 해야 합니다.

1. [Azure DevOps](https://azure.microsoft.com/services/devops/) 프로젝트에 로그인 합니다.
   
1. Visual Studio Marketplace [릴리스 주석 확장](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) 페이지에서 Azure devops 조직을 선택 하 고 **설치** 를 선택 하 여 azure devops 조직에 확장을 추가 합니다.
   
   ![Azure DevOps 조직을 선택한 다음 설치를 선택 합니다.](./media/annotations/1-install.png)
   
Azure DevOps 조직에 대해 확장을 한 번만 설치 하면 됩니다. 이제 조직의 모든 프로젝트에 대 한 릴리스 주석을 구성할 수 있습니다.

### <a name="configure-release-annotations"></a>릴리스 주석 구성

각 Azure Pipelines 릴리스 템플릿에 대 한 별도의 API 키를 만듭니다.

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 응용 프로그램을 모니터링 하는 Application Insights 리소스를 엽니다. 또는 [새 Application Insights 리소스를 만듭니다](../../azure-monitor/app/app-insights-overview.md).
   
1. **API 액세스** 탭을 열고 **Application Insights ID**를 복사 합니다.
   
   ![API 액세스에서 응용 프로그램 ID를 복사 합니다.](./media/annotations/2-app-id.png)

1. 별도의 브라우저 창에서 Azure Pipelines 배포를 관리 하는 릴리스 템플릿을 열거나 만듭니다.
   
1. **작업 추가**를 선택한 후 메뉴에서 **Application Insights 릴리스 주석** 작업을 선택 합니다.
   
   ![작업 추가를 선택 하 고 Application Insights 릴리스 주석을 선택 합니다.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > 릴리스 주석 작업은 현재 Windows 기반 에이전트만 지원 합니다. Linux, macOS 또는 다른 유형의 에이전트에서는 실행 되지 않습니다.
   
1. **API 액세스** 탭에서 복사한 Application Insights Id를 **응용 프로그램 id**아래에 붙여 넣습니다.
   
   ![Application Insights ID 붙여넣기](./media/annotations/4-paste-app-id.png)
   
1. **Api 액세스** Application Insights 창으로 돌아가서 **api 키 만들기**를 선택 합니다. 
   
   ![API 액세스 탭에서 API 키 만들기를 선택 합니다.](./media/annotations/5-create-api-key.png)
   
1. **API 키 만들기** 창에서 설명을 입력 하 고 **주석 작성**을 선택한 다음 **키 생성**을 선택 합니다. 새 키를 복사합니다.
   
   ![API 키 만들기 창에서 설명을 입력 하 고 주석 작성을 선택한 다음 키 생성을 선택 합니다.](./media/annotations/6-create-api-key.png)
   
1. 릴리스 템플릿 창의 **변수** 탭에서 **추가** 를 선택 하 여 새 API 키에 대 한 변수 정의를 만듭니다.

1. **이름**에 `ApiKey`를 입력 하 고 **값**아래에 **API 액세스** 탭에서 복사한 api 키를 붙여넣습니다.
   
   ![Azure DevOps 변수 탭에서 추가를 선택 하 고, 변수 이름을 ApiKey로 하 고, API 키를 값 아래에 붙여 넣습니다.](./media/annotations/7-paste-api-key.png)
   
1. 주 릴리스 템플릿 창에서 **저장** 을 선택 하 여 템플릿을 저장 합니다.

## <a name="view-annotations"></a>주석 보기
이제 릴리스 템플릿을 사용 하 여 새 릴리스를 배포할 때마다 주석이 Application Insights으로 전송 됩니다. 주석은 **메트릭 탐색기**의 차트에 표시 됩니다.

주석 마커 (연한 회색 화살표)를 선택 하 여 요청자, 소스 제어 분기, 릴리스 파이프라인 및 환경을 비롯 한 릴리스에 대 한 세부 정보를 엽니다.

![릴리스 주석 마커를 선택 합니다.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>PowerShell에서 사용자 지정 주석 만들기
GitHub의 [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) PowerShell 스크립트를 사용 하 여 Azure devops를 사용 하지 않고 원하는 모든 프로세스에서 주석을 만들 수 있습니다. 

1. [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)의 로컬 복사본을 만듭니다.
   
1. 이전 절차의 단계를 사용 하 여 Application Insights ID를 가져오고 Application Insights **Api 액세스** 탭에서 api 키를 만듭니다.
   
1. 다음 코드를 사용 하 여 PowerShell 스크립트를 호출 하 여 꺾쇠 괄호 자리 표시자를 값으로 바꿉니다. `-releaseProperties`은 선택적입니다. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

예를 들어 이전에 주석을 만들 수 있도록 스크립트를 수정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [작업 항목 만들기](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [PowerShell을 사용한 Automation](../../azure-monitor/app/powershell.md)
