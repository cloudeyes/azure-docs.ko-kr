---
title: 기본 내부에서 표준 내부-Azure Load Balancer로 업그레이드
description: 이 문서에서는 Azure 내부 Load Balancer를 기본 SKU에서 표준 SKU로 업그레이드 하는 방법을 보여 줍니다.
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 02/23/2020
ms.author: irenehua
ms.openlocfilehash: c2c909d8ef2be982d4dd4a70b5f35d03e8e71418
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77659971"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Azure 내부 Load Balancer 업그레이드-아웃 바운드 연결이 필요 하지 않음
[Azure 표준 Load Balancer](load-balancer-overview.md) 는 영역 중복성을 통해 다양 한 기능 및 고가용성 집합을 제공 합니다. Load Balancer SKU에 대 한 자세한 내용은 [비교 표](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus)를 참조 하세요.

업그레이드에는 두 단계가 있습니다.

1. 구성 마이그레이션
2. 표준 Load Balancer 백 엔드 풀에 Vm 추가

이 문서에서는 구성 마이그레이션을 다룹니다. Vm을 백 엔드 풀에 추가 하는 것은 특정 환경에 따라 달라질 수 있습니다. 그러나 일부 고급 일반 권장 사항이 [제공 됩니다](#add-vms-to-backend-pools-of-standard-load-balancer).

## <a name="upgrade-overview"></a>업그레이드 개요

다음을 수행 하는 Azure PowerShell 스크립트를 사용할 수 있습니다.

* 지정 하는 위치에 표준 내부 SKU Load Balancer을 만듭니다. [아웃 바운드 연결은](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) 표준 내부 Load Balancer에서 제공 되지 않습니다.
* 기본 SKU Load Balancer의 구성을 새로 만든 표준 Load Balancer에 원활 하 게 복사 합니다.

### <a name="caveatslimitations"></a>Caveats\Limitations

* 스크립트는 아웃 바운드 연결이 필요 하지 않은 내부 Load Balancer 업그레이드만 지원 합니다. 일부 Vm에 대 한 [아웃 바운드 연결이](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) 필요한 경우에는이 [페이지](upgrade-InternalBasic-To-PublicStandard.md) 에서 지침을 참조 하세요. 
* 표준 Load Balancer에 새 공용 주소가 있습니다. 기존 기본 Load Balancer와 연결 된 IP 주소는 Sku가 다르기 때문에 표준 Load Balancer로 원활 하 게 이동할 수 없습니다.
* 표준 부하 분산 장치를 다른 지역에 만든 경우 이전 지역의 기존 Vm을 새로 만든 표준 Load Balancer에 연결할 수 없습니다. 이 제한 사항을 해결 하려면 새 지역에 새 VM을 만들어야 합니다.
* Load Balancer 프런트 엔드 IP 구성 또는 백 엔드 풀이 없는 경우 스크립트를 실행 하는 동안 오류가 발생할 수 있습니다. 비어 있지 않은지 확인 하세요.

## <a name="download-the-script"></a>스크립트 다운로드

[PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0)에서 마이그레이션 스크립트를 다운로드 합니다.
## <a name="use-the-script"></a>스크립트 사용

로컬 PowerShell 환경 설정 및 기본 설정에 따라 다음과 같은 두 가지 옵션을 사용할 수 있습니다.

* Azure Az 모듈이 설치 되어 있지 않거나 Azure Az 모듈을 제거 하지 않는 경우 가장 좋은 방법은 `Install-Script` 옵션을 사용 하 여 스크립트를 실행 하는 것입니다.
* Azure Az modules를 유지 해야 하는 경우에는 스크립트를 다운로드 하 여 직접 실행 하는 것이 가장 좋습니다.

Azure Az 모듈이 설치 되어 있는지 확인 하려면 `Get-InstalledModule -Name az`를 실행 합니다. 설치 된 Az 모듈이 표시 되지 않는 경우 `Install-Script` 메서드를 사용할 수 있습니다.

### <a name="install-using-the-install-script-method"></a>Install-Script 메서드를 사용 하 여 설치

이 옵션을 사용 하려면 컴퓨터에 Azure Az 모듈이 설치 되어 있지 않아야 합니다. 설치 된 경우 다음 명령에서 오류를 표시 합니다. Azure Az modules를 제거 하거나 다른 옵션을 사용 하 여 스크립트를 수동으로 다운로드 하 여 실행할 수 있습니다.
  
다음 명령을 사용하여 스크립트를 실행합니다.

`Install-Script -Name AzureILBUpgrade`

또한이 명령은 필요한 Az modules을 설치 합니다.  

### <a name="install-using-the-script-directly"></a>스크립트를 사용 하 여 직접 설치

일부 Azure Az 모듈이 설치 되어 있고 제거할 수 없는 경우 (또는 제거 하지 않으려는 경우) 스크립트 다운로드 링크의 **수동 다운로드** 탭을 사용 하 여 스크립트를 수동으로 다운로드할 수 있습니다. 이 스크립트는 원시 nupkg 파일로 다운로드 됩니다. 이 nupkg 파일에서 스크립트를 설치 하려면 [수동 패키지 다운로드](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)를 참조 하세요.

스크립트를 실행하려면

1. `Connect-AzAccount`를 사용 하 여 Azure에 연결 합니다.

1. `Import-Module Az`를 사용 하 여 Az 모듈을 가져옵니다.

1. 필수 매개 변수를 검사 합니다.

   * **rgName: [String]: 필수** – 기존 기본 Load Balancer 및 새 표준 Load Balancer에 대 한 리소스 그룹입니다. 이 문자열 값을 찾으려면 Azure Portal로 이동 하 고, 기본 Load Balancer 원본을 선택 하 고, 부하 분산 장치에 대 한 **개요** 를 클릭 합니다. 리소스 그룹은 해당 페이지에 있습니다.
   * **Oldlbname: [String]: 필수** – 업그레이드 하려는 기존 기본 분산 장치의 이름입니다. 
   * **newlocation: [String]: Required** -표준 Load Balancer을 만들 위치입니다. 다른 기존 리소스와의 연결을 개선 하기 위해 선택한 기본 Load Balancer의 동일한 위치를 표준 Load Balancer에 상속 하는 것이 좋습니다.
   * **newLBName: [String]: Required** – 만들려는 표준 Load Balancer의 이름입니다.
1. 적절 한 매개 변수를 사용 하 여 스크립트를 실행 합니다. 완료 하는 데 5 ~ 7 분 정도 걸릴 수 있습니다.

    **예제**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>표준 Load Balancer 백 엔드 풀에 Vm 추가

먼저 기본 내부 Load Balancer에서 마이그레이션할 정확한 구성을 사용 하 여 스크립트가 새 표준 내부 Load Balancer를 만들었는지 확인 합니다. Azure Portal에서이를 확인할 수 있습니다.

표준 Load Balancer를 통해 적은 양의 트래픽을 수동 테스트로 보내야 합니다.
  
새로 만든 표준 내부 Load Balancer의 백 엔드 풀에 Vm을 추가 하는 방법에 대 한 몇 가지 시나리오와 각 구성에 대 한 권장 사항은 다음과 같습니다.

* **기존 vm을 기존 기본 내부 Load Balancer의 백 엔드 풀에서 새로 만든 표준 내부 Load Balancer의 백 엔드 풀로 이동**합니다.
    1. 이 빠른 시작의 작업을 수행하려면 [Azure Portal](https://portal.azure.com)에 로그인해야 합니다.
 
    1. 왼쪽 메뉴에서 **모든 리소스** 를 선택한 다음, 리소스 목록에서 **새로 만든 표준 Load Balancer** 를 선택 합니다.
   
    1. **설정**에서 **백 엔드 풀**을 선택합니다.
   
    1. 기본 Load Balancer의 백 엔드 풀과 일치 하는 백 엔드 풀을 선택 하 고 다음 값을 선택 합니다. 
      - **가상 컴퓨터**: 기본 Load Balancer의 일치 하는 백 엔드 풀에서 vm을 드롭다운 및 선택 합니다.
    1. **저장**을 선택합니다.
    >[!NOTE]
    >공용 ip가 있는 Vm의 경우에는 동일한 IP 주소를 보장 하지 않는 표준 IP 주소를 먼저 만들어야 합니다. 기본 Ip에서 Vm의 연결을 끊고 새로 만든 표준 IP 주소에 연결 합니다. 그런 다음 표준 Load Balancer의 백 엔드 풀에 Vm을 추가 하는 지침을 따를 수 있습니다. 

* **새로 만든 표준 내부 Load Balancer의 백 엔드 풀에 추가할 새 vm을 만듭니다**.
    * VM을 만들고 표준 Load Balancer와 연결 하는 방법에 대 한 자세한 지침은 [여기](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal#create-virtual-machines)에서 찾을 수 있습니다.

## <a name="common-questions"></a>일반적인 질문

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>V1에서 v 2로 구성을 마이그레이션하기 위한 Azure PowerShell 스크립트에 제한이 있나요?

예. [주의 사항/제한 사항](#caveatslimitations)을 참조 하세요.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>또한 Azure PowerShell 스크립트는 기본 Load Balancer에서 새로 만든 표준 Load Balancer으로 트래픽을 전환 하나요?

아니요. Azure PowerShell 스크립트는 구성만 마이그레이션합니다. 실제 트래픽 마이그레이션은 사용자의 책임 이며 컨트롤에 있습니다.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>이 스크립트를 사용 하는 경우 몇 가지 문제가 발생 했습니다. 도움을 받으려면 어떻게 해야 하나요?
  
slbupgradesupport@microsoft.com에 전자 메일을 보내거나 Azure 지원으로 지원 사례를 열거나 둘 다 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Standard Load Balancer에 대한 자세한 정보](load-balancer-overview.md)
