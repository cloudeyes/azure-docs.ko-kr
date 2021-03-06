---
title: Azure Automation의 내 첫 번째 PowerShell 워크플로 Runbook
description: PowerShell 워크플로를 사용하여 간단한 텍스트 Runbook의 생성, 테스트, 게시 과정을 안내하는 자습서입니다.
keywords: Powershell 워크플로, Powershell 워크플로 예제, 워크플로 Powershell
services: automation
ms.subservice: process-automation
ms.date: 09/24/2018
ms.topic: conceptual
ms.openlocfilehash: a5a1cad3179063f75a5d9a19567624180b5793a1
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367264"
---
# <a name="my-first-powershell-workflow-runbook"></a>내 첫 번째 PowerShell 워크플로 Runbook

> [!div class="op_single_selector"]
> * [그래픽](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell 워크플로](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

이 자습서는 Azure Automation에서 [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)을 만드는 과정을 안내합니다. Runbook 작업의 상태를 추적 하는 방법을 학습 하는 동안 테스트 하 고 게시 하는 간단한 runbook으로 시작 합니다. 그런 다음 Azure 가상 머신을 시작 하 여 실제로 Azure 리소스를 관리 하도록 runbook을 수정 합니다. 마지막으로 runbook 매개 변수를 추가 하 여 runbook을 더욱 강력 하 게 만듭니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대 한 Az module 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조 하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트 하는 방법을](automation-update-azure-modules.md)사용 하 여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* [Automation 계정](automation-offering-get-started.md) . 이 계정은 가상 머신을 시작하고 중지할 수 있는 권한이 있어야 합니다.
* Azure 가상 머신. 이 컴퓨터를 중지 하 고 시작 했으므로 프로덕션 VM이 아니어야 합니다.

## <a name="step-1---create-new-runbook"></a>1 단계-새 runbook 만들기

먼저 `Hello World`텍스트를 출력 하는 간단한 runbook을 만듭니다.

1. Azure Portal에서 Automation 계정을 엽니다.

   Automation 계정 페이지는 이 계정의 리소스 간략히 보기를 제공합니다. 사용자에게는 이미 일부 자산이 있어야 합니다. 이러한 자산의 대부분은 새 Automation 계정에 자동으로 포함 되는 모듈입니다. 또한 구독과 연결 된 자격 증명 자산이 있어야 합니다.
 
1. **프로세스 자동화** 아래에서 **runbook** 을 선택 하 여 runbook 목록을 엽니다.
1. **Runbook 만들기**를 선택 하 여 새 runbook을 만듭니다.
1. Runbook 이름을 **MyFirstRunbook-Workflow**로 지정합니다.
1. 이 경우 [PowerShell 워크플로 runbook](automation-runbook-types.md#powershell-workflow-runbooks)을 만듭니다. **Runbook 형식**에 대해 **PowerShell 워크플로** 를 선택 합니다.
1. **만들기** 를 클릭하여 Runbook을 만들고 그래픽 편집기를 엽니다.

## <a name="step-2---add-code-to-the-runbook"></a>2단계 - Runbook에 코드 추가

Runbook에 직접 코드를 입력 하거나 라이브러리 컨트롤에서 cmdlet, runbook 및 자산을 선택 하 고 관련 매개 변수를 사용 하 여 runbook에 추가할 수 있습니다. 이 자습서에서는 runbook에 직접 코드를 입력 합니다.

1. Runbook은 현재 필수 `Workflow` 키워드, runbook의 이름 및 전체 워크플로를 포함 하는 중괄호를 사용 하 여 비어 있습니다.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. 중괄호 사이에 `Write-Output "Hello World"`을 입력 합니다.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. **저장**을 클릭하여 Runbook을 저장합니다.

## <a name="step-3---test-the-runbook"></a>3 단계 - runbook 테스트

프로덕션 환경에서 사용할 수 있도록 runbook을 게시 하기 전에 테스트 하 여 제대로 작동 하는지 확인 해야 합니다. Runbook 테스트는 초안 버전을 실행 하 고 해당 출력을 대화형으로 볼 수 있도록 합니다.

1. 테스트 **창** 을 선택 하 여 테스트 창을 엽니다.
1. **시작** 을 클릭 하 여 테스트를 시작 합니다. 옵션을 테스트 합니다.
1. [Runbook 작업이](automation-runbook-execution.md) 만들어지고 해당 상태가 창에 표시 됩니다.

   작업 상태는 `Queued`로 시작 되며, 작업은 클라우드의 runbook worker를 사용할 수 있을 때까지 대기 중임을 나타냅니다. 작업 자가 작업을 클레임 할 때 상태가 `Starting`로 변경 됩니다. 마지막으로 runbook이 실제로 실행 되기 시작 하면 상태가 `Running` 됩니다.

1. Runbook 작업이 완료 되 면 테스트 창에 해당 출력이 표시 됩니다. 이 경우 `Hello World`표시 됩니다.

   ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)

1. 캔버스로 돌아가려면 테스트 창을 닫습니다.

## <a name="step-4---publish-and-start-the-runbook"></a>4 단계 - runbook 게시 및 시작

만든 runbook은 아직 초안 모드입니다. 프로덕션 환경에서 실행 하려면 먼저 게시 해야 합니다. Runbook을 게시하면 초안 버전으로 기존의 게시된 버전을 덮어씁니다. 이 경우에 Runbook을 방금 만들었으므로 아직 게시된 버전이 없습니다.

1. **게시**를 클릭하여 Runbook을 게시한 다음 확인 메시지가 표시되면 **예**를 클릭합니다.
1. **Runbook 페이지에서** runbook을 보려면 왼쪽으로 스크롤하고 **제작 상태** 필드는 **게시**됨으로 설정 되어 있는지 확인 합니다.
1. **MyFirstRunbook**에 대 한 페이지를 보려면 오른쪽으로 다시 스크롤합니다.

   위쪽의 옵션을 사용 하 여 runbook을 시작 하거나, 이후 시작 시간을 예약 하거나, HTTP 호출을 통해 runbook을 시작할 수 있도록 [webhook](automation-webhooks.md) 를 만들 수 있습니다.

1. **시작**을 선택하고 Runbook을 시작할지 확인하는 메시지가 표시되면 **예**를 선택합니다.

   ![Runbook 시작](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. 만든 runbook 작업에 대 한 작업 창이 열립니다. 이 경우 작업의 진행 상황을 볼 수 있도록 창을 열어 둡니다.

1. 작업 상태는 **작업 요약**에 표시 됩니다. 이 상태는 runbook을 테스트할 때 보았던 상태와 일치 합니다.

   ![작업 요약](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Runbook 상태가 `Completed`표시 되 면 **출력**을 클릭 합니다. 출력 페이지가 열리고 `Hello World` 메시지를 볼 수 있습니다.

   ![작업 요약](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. 출력 페이지를 닫습니다.

1. **모든 로그** 를 클릭하여 Runbook 작업에 대한 스트림 창을 엽니다. 출력 스트림에 `Hello World`만 표시 되어야 합니다. Runbook에서 기록 하는 경우 스트림 창에는 자세한 정보 및 오류 스트림과 같은 runbook 작업에 대 한 다른 스트림이 표시 될 수 있습니다.

   ![작업 요약](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. **MyFirstRunbook** 페이지로 돌아가려면 스트림 창 및 작업 창을 닫습니다.
1. **리소스** 에서 **작업** 을 클릭 하 여이 Runbook에 대 한 작업 페이지를 엽니다. 이 페이지에는 runbook에서 만든 모든 작업이 나열 됩니다. 작업을 한 번만 실행 했으므로 하나의 작업만 표시 됩니다.

   ![교육](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. 작업 이름을 클릭 하 여 runbook을 시작할 때 표시 된 것과 동일한 작업 창을 엽니다. 이 창을 사용 하 여 runbook에 대해 생성 된 작업의 세부 정보를 볼 수 있습니다.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>5 단계-Azure 리소스를 관리 인증 추가

지금까지 Runbook을 테스트 하고 게시했지만, 딱히 유용하지는 않습니다. Azure 리소스를 관리하려고 합니다. 구독에 대 한 자격 증명을 사용 하 여 인증 하지 않는 한이 작업을 수행할 수 없습니다. 인증은 `Connect-AzAccount` cmdlet을 사용 합니다.

>[!NOTE]
>PowerShell runbook의 경우 `Add-AzAccount` 및 `Add-AzureRMAccount` `Connect-AzAccount`에 대 한 별칭입니다. 이러한 cmdlet을 사용 하거나 Automation 계정의 모듈을 최신 버전으로 [업데이트할](automation-update-azure-modules.md) 수 있습니다. 새 Automation 계정을 방금 만든 경우에도 모듈을 업데이트 해야 할 수 있습니다.

1. **MyFirstRunbook** 페이지로 이동 하 여 **편집**을 클릭 하 여 텍스트 편집기를 엽니다.
2. `Write-Output` 줄을 삭제 합니다.
3. 중괄호 사이의 빈 줄에 커서를 놓습니다.
4. Automation 실행 계정을 사용 하 여 인증을 처리 하는 다음 코드를 입력 하거나 복사 하 여 붙여넣습니다.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

1. Runbook을 테스트할 수 있도록 **테스트 창**을 클릭합니다.
1. **시작** 을 클릭하여 테스트를 시작합니다. 완료 되 면 다음과 유사한 출력이 표시 됩니다. 계정에서 기본 정보를 표시 합니다. 이 작업은 자격 증명이 유효한 지 확인 합니다.

   ![Authenticate](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>6단계 - 가상 머신을 시작하기 위한 코드 추가

이제 runbook이 Azure 구독을 인증 하므로 리소스를 관리할 수 있습니다. 가상 머신을 시작 하는 명령을 추가 해 보겠습니다. Azure 구독에서 VM을 선택할 수 있으며, 지금은 runbook에서 해당 이름을 하드 코딩 합니다. 여러 구독에서 리소스를 관리 하는 경우 `AzContext` 매개 변수를 [AzContext](/powershell/module/az.accounts/get-azcontext) cmdlet과 함께 사용 해야 합니다.

1. 아래와 같이 [new-azvm](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0
) cmdlet에 대 한 호출을 입력 하 여 시작할 VM의 이름 및 리소스 그룹 이름을 제공 합니다. 

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures that you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Get-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

1. Runbook을 저장한 다음, 테스트할 수 있도록 **테스트 창**을 클릭합니다.
1. **시작** 을 클릭하여 테스트를 시작합니다. 완료 되 면 VM이 시작 되었는지 확인 합니다.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>7 단계 - runbook에 입력 매개 변수를 추가 합니다.

Runbook은 현재 runbook에 하드 코드 된 VM을 시작 합니다. Runbook이 시작 될 때 VM을 지정할 수 있는 경우 더 유용 합니다. Runbook에 입력 매개 변수를 추가 하 여 해당 기능을 제공 해 보겠습니다.

1. `VMName` 및 `ResourceGroupName` 매개 변수에 대 한 변수를 runbook에 추가 하 고 아래와 같이 `Start-AzVM` cmdlet을 사용 하 여 변수를 사용 합니다.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Runbook을 저장하고 테스트 창을 엽니다. 이제 테스트에 있는 두 개의 입력 변수에 값을 제공할 수 있습니다.
3. 창을 닫습니다.
4. **게시** 를 클릭하여 Runbook의 새 버전을 게시합니다.
5. 시작 된 VM을 중지 합니다.
6. **시작** 을 클릭하여 runbook을 시작합니다. 
7. 시작할 VM에 대 한 **VMNAME** 및 **RESOURCEGROUPNAME** 값을 입력 합니다.

   ![Runbook 시작](media/automation-first-runbook-textual/automation-pass-params.png)

8. Runbook이 완료 되 면 VM이 시작 되었는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

* 언어 참조 및 학습 모듈을 비롯 한 PowerShell에 대 한 자세한 내용은 [Powershell 문서](https://docs.microsoft.com/powershell/scripting/overview)를 참조 하세요.
* 그래픽 runbook을 시작 하려면 [내 첫 번째 그래픽 runbook](automation-first-runbook-graphical.md)을 참조 하세요.
* PowerShell Runbook을 시작하려면 [내 첫 번째 PowerShell Runbook](automation-first-runbook-textual-powershell.md)을 참조하세요.
* Runbook 형식 및 해당 장점 및 제한 사항에 대 한 자세한 내용은 [runbook types Azure Automation](automation-runbook-types.md)를 참조 하세요.
* PowerShell 스크립트 지원 기능에 대 한 자세한 내용은 [Azure Automation의 네이티브 PowerShell 스크립트 지원](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)을 참조 하세요.
