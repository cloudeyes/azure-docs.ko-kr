---
title: 가상 네트워크 만들기 - 빠른 시작 - Azure Portal
titleSuffix: Azure Virtual Network
description: '빠른 시작: Azure Portal에서 가상 네트워크를 만듭니다. 이러한 네트워크를 사용 하면 Vm과 같은 Azure 리소스가 서로 안전 하 게 통신할 수 있습니다.'
author: KumudD
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2020
ms.author: kumud
ms.openlocfilehash: 1a1593566b8bdb72f322d64c1ee99c7018f49329
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128755"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 가상 네트워크 만들기

이 빠른 시작에서는 Azure Portal를 사용 하 여 가상 네트워크를 만드는 방법에 대해 알아봅니다. 두 Vm (가상 머신)을 배포 합니다. 다음으로 Vm 간에 안전 하 게 통신 하 고 인터넷에서 Vm에 연결 합니다. 가상 네트워크는 Azure에서 프라이빗 네트워크의 기본 구성 요소입니다. Vm과 같은 Azure 리소스에서 서로 안전 하 게 통신 하 고 인터넷과 통신할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

1. Azure Portal 메뉴에서 **리소스 만들기**를 선택합니다. Azure Marketplace에서 **네트워킹** > **가상 네트워크**를 선택합니다.

1. **가상 네트워크 만들기**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | Subscription | 구독을 선택합니다.|
    | Resource group | **새로 만들기**를 선택하고 *myResourceGroup*을 입력한 다음, **확인**을 선택합니다. |
    | 속성 | *myVirtualNetwork*를 입력합니다. |
    | 위치 | **미국 동부**를 선택합니다.|

1. **다음: IP 주소**를 선택 하 고, **IPv4 주소 공간**에 *10.1.0.0/16*을 입력 합니다.

1. 서브넷 **추가**를 선택 하 고 서브넷 **이름** 에 *myvirtualsubnet* 을, **서브넷 주소 범위**에 *10.1.0.0/24* 를 입력 합니다.

1. **추가**를 선택한 다음 **검토 + 만들기**를 선택 합니다. 나머지 항목은 기본값으로 유지하고 **만들기**를 선택합니다.

1. **가상 네트워크 만들기**에서 **만들기**를 선택 합니다.

## <a name="create-virtual-machines"></a>가상 머신 만들기

가상 네트워크에 두 개의 VM을 만듭니다.

### <a name="create-the-first-vm"></a>첫 번째 VM 만들기

1. Azure Portal 메뉴에서 **리소스 만들기**를 선택합니다.

1. Azure Marketplace에서 **컴퓨팅** > **Windows Server 2019 데이터 센터**를 선택합니다. **만들기**를 선택합니다.

1. **가상 머신 만들기 - 기본 사항**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup**을 선택합니다. 이전 섹션에서이 리소스 그룹을 만들었습니다. |
    | **인스턴스 세부 정보** |  |
    | 가상 머신 이름 | *myVm1*을 입력합니다. |
    | 지역 | **미국 동부**를 선택합니다. |
    | 가용성 옵션 | 기본적으로 **인프라 중복성이 필요 하지 않습니다**. |
    | 이미지 | **Windows Server 2019 Datacenter**의 기본값입니다. |
    | 크기 | **STANDARD d s 1 v2**로 기본 합니다. |
    | **관리자 계정** |  |
    | 사용자 이름 | 선택한 사용자 이름을 입력합니다. |
    | 암호 | 선택한 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.|
    | 암호 확인 | 암호를 다시 입력 합니다. |
    | **인바운드 포트 규칙** |  |
    | 공용 인바운드 포트 | **선택한 포트 허용**을 선택합니다. |
    | 인바운드 포트 선택 | *HTTP (80)* 및 *RDP (3389)* 를 입력 합니다. |
    | **비용 절감** |  |
    | 이미 Windows 라이선스가 있나요? | 기본값은 **아니요**입니다. |

1. **다음: 디스크**를 선택 합니다.

1. **가상 머신 만들기-디스크**에서 기본값을 유지 하 고 **다음: 네트워킹**을 선택 합니다.

1. **가상 머신 만들기 - 네트워킹**에서 다음 정보를 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 가상 네트워크 | 기본값은 **myVirtualNetwork**입니다. |
    | 서브넷 | **Myvirtualsubnet (10.1.0.0/24)** 으로 기본 합니다. |
    | 공용 IP | 기본값 **은 (신규) myVm-ip**입니다. |
    | NIC 네트워크 보안 그룹 추가 | 기본값은 **기본입니다.** |
    | 공용 인바운드 포트 | **선택한 포트를 허용**하는 기본값입니다. |
    | 인바운드 포트 선택 | **HTTP** 및 **RDP**를 기본값으로 합니다.

1. **다음: 관리**를 선택 합니다.

1. **가상 머신 만들기 - 관리**에서 **진단 스토리지 계정**에 대해 **새로 만들기**를 선택합니다.

1. **스토리지 계정 만들기**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 속성 | *myvmstorageaccount*를 입력합니다. 이 이름을 사용하는 경우 고유한 이름을 만듭니다.|
    | 계정 종류 | 기본값은 **저장소 (범용 v1)** 입니다. |
    | 성능 | **Standard**로 기본 합니다. |
    | 복제 | 기본값 **은 LRS (로컬 중복 저장소)** 입니다. |

1. **확인**을 선택 하 고 **검토 + 만들기**를 선택 합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

1. **유효성 검사 통과** 메시지가 표시되면 **만들기**를 선택합니다.

### <a name="create-the-second-vm"></a>두 번째 VM 만들기

이전 섹션의 절차를 반복 하 여 다른 가상 컴퓨터를 만듭니다.

> [!IMPORTANT]
> **가상 컴퓨터 이름**에 *myVm2*을 입력 합니다.
>
> **진단 저장소 계정**에는 **myvmstorageaccount**를 만드는 대신 선택 해야 합니다.

## <a name="connect-to-a-vm-from-the-internet"></a>인터넷에서 VM에 연결

*myVm1*을 만든 후 인터넷에 연결합니다.

1. Azure Portal에서를 검색 하 고 *myVm1*를 선택 합니다.

1. **연결**, **RDP**를 차례로 선택 합니다.

    ![가상 머신에 연결](./media/quick-create-portal/connect-to-virtual-machine.png)

    **연결** 페이지가 열립니다.

1. **RDP 파일 다운로드**를 선택합니다. Azure에서 원격 데스크톱 프로토콜( *.rdp*) 파일을 만들고, 컴퓨터에 다운로드합니다.

1. RDP 파일을 엽니다. 메시지가 표시되면 **연결**을 선택합니다.

1. VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다.

    > [!NOTE]
    > **추가 선택 사항** > **다른 계정 사용**을 선택하여 VM을 만들 때 입력한 자격 증명을 지정해야 할 수도 있습니다.

1. **확인**을 선택합니다.

1. 로그인 할 때 인증서 경고가 표시 될 수 있습니다. 인증서 경고가 표시되면 **예** 또는 **계속**을 선택합니다.

1. VM 데스크톱이 나타나면 최소화하여 로컬 데스크톱으로 돌아갑니다.

## <a name="communicate-between-vms"></a>VM 간 통신

1. *myVm1*의 원격 데스크톱에서 PowerShell을 엽니다.

1. `ping myVm2`를 입력합니다.

    이 출력과 유사한 메시지를 받게 됩니다.

    ```output
    Pinging myVm2.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.clouda
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.1.0.5:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    `ping`이 ICMP(Internet Control Message Protocol)를 사용하기 때문에 `ping`에 실패합니다. 기본적으로 ICMP는 Windows 방화벽에서 허용되지 않습니다.

1. 이후 단계에서 *myVm2*가 *myVm1*을 ping할 수 있도록 하려면 다음 명령을 입력합니다.

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    이 명령은 Windows 방화벽에서 ICMP 인바운드를 허용합니다.

1. *myVm1*에 대한 원격 데스크톱 연결을 닫습니다.

1. [인터넷에서 VM에 연결](#connect-to-a-vm-from-the-internet)의 단계를 다시 수행하지만 *myVm2*에 연결합니다.

1. 명령 프롬프트에서 `ping myvm1`을 입력합니다.

    다음과 같은 메시지가 반환됩니다.

    ```output
    Pinging myVm1.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 1ms, Average = 0ms
    ```

    3단계에서 *myVm1* VM의 Windows 방화벽을 통해 ICMP를 허용했으므로 *myVm1*에서 회신을 받습니다.

1. *myVm2*에 대한 원격 데스크톱 연결을 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서는 기본 가상 네트워크와 두 개의 VM을 만들었습니다. 인터넷에서 하나의 VM에 연결하고 두 VM 간에 안전하게 통신했습니다.

가상 네트워크 및 VM을 다 사용했으면 리소스 그룹과 리소스 그룹에 포함된 모든 리소스를 삭제합니다.

1. *Myresourcegroup*을 검색 하 고 선택 합니다.

1. **리소스 그룹 삭제**를 선택합니다.

1. *리소스 그룹 이름 입력*에 대해 **myResourceGroup**을 입력하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

가상 네트워크 설정에 대해 자세히 알아보려면 [가상 네트워크 만들기, 변경 또는 삭제](manage-virtual-network.md)를 참조 하세요.

기본적으로 Azure는 Vm 간의 보안 통신을 허용 합니다. Azure는 인터넷을 통해 Windows Vm에 대 한 인바운드 원격 데스크톱 연결만 허용 합니다. VM 네트워크 통신의 유형에 대해 자세히 알아보려면 [네트워크 트래픽 필터링](tutorial-filter-network-traffic.md)을 참조 하세요.
