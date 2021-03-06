---
title: Microsoft Azure StorSimple 8600 장치 설치
description: 소프트웨어를 배포하고 구성하기 전에 StorSimple 8600 디바이스를 개봉, 랙 탑재, 케이블 연결하는 방법을 설명합니다.
author: alkohli
ms.assetid: 3d82ba5f-3e34-40dc-9c33-50f952bc6be8
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 8d118e71b29b54b034788c37916091ad8f8b996f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79254756"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>StorSimple 8600 디바이스 개봉, 랙 탑재, 케이블 연결

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>개요
Microsoft Azure StorSimple 8600은 이중 인클로저 디바이스이며 기본 및 EBOD 인클로저로 구성되어 있습니다. 이 자습서는 StorSimple 소프트웨어를 구성하기 전에 StorSimple 8600 디바이스 하드웨어를 개봉하고 랙에 탑재하고 케이블로 연결하는 방법을 설명합니다.

## <a name="unpack-your-storsimple-8600-device"></a>StorSimple 8600 디바이스 개봉
다음 단계는 8600 StorSimple 스토리지 디바이스를 개봉하는 방법을 명확하고 자세하게 설명합니다. 이 디바이스는 2개의 상자에 제공되는데 하나에는 기본 인클로저, 다른 하나에는 EBOD 인클로저가 들어 있습니다. 이러한 두 상자는 단일 상자에 배치됩니다.

### <a name="prepare-to-unpack-your-device"></a>디바이스의 개봉 준비
디바이스를 개봉하기 전에 다음 정보를 검토합니다.

![경고 아이콘](./media/storsimple-safety/IC740879.png)![높은 가중치 아이콘](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **경고가 발생 했습니다.**

1. 수동으로 처리하는 경우 반드시 두 사람이 디바이스의 무게를 지탱해야 합니다. 완전히 구성된 인클로저의 최고 무게는 32kg입니다.
2. 평평한 지면에 상자를 놓습니다.

다음 단계를 따라 디바이스의 포장을 풉니다.

#### <a name="to-unpack-your-device"></a>디바이스를 개봉하려면
1. 상자와 포장 발포 폼에 찌그러짐, 자르기, 물에 의한 손상 또는 기타 손상이 없는지 검사합니다. 상자나 포장이 심각하게 손상된 경우에는 상자를 열지 마세요. 디바이스가 올바르게 작동하는지 여부를 평가하려면 [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md)하세요.
2. 외부 상자를 연 다음 기본 인클로저와 EBOD 인클로저에 해당하는 두 상자를 꺼냅니다. 이제 기본 인클로저와 EBOD 인클로저를 개봉할 수 있습니다. 다음 단계는 인클로저 중 하나의 개봉된 상태를 보여 줍니다.
   
    ![스토리지 디바이스 개봉하기](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
   
    **스토리지 디바이스가 개봉된 상태**
   
   | 레이블 | Description |
   | --- | --- |
   |   1 |포장 상자 |
   |   2 |SAS 케이블(보조 프로그램 및 케이블 트레이) |
   |   3 |하단 발포 폼 |
   |   4 |디바이스 |
   |   5 |상단 발포 폼 |
   |   6 |액세서리 상자 |
3. 두 상자를 개봉한 후 다음이 있는지 확인합니다.
   
   * 기본 인클로저 1개(기본 인클로저와 EBOD 인클로저가 두 개의 별도 상자에 있음)
   * EBOD 인클로저 1개
   * 전원 코드 4개(각 상자에 2개씩)
   * SAS 케이블 2개(기본 인클로저를 EBOD 인클로저에 연결할 때 사용)
   * 크로스 오버 이더넷 케이블 1개
   * 직렬 콘솔 케이블 2개
   * 직렬 액세스에 대한 직렬-USB 변환기 1개
   * 10GbE 네트워크 인터페이스와 함께 사용하기 위한 4개의 QSFP-대-SFP+어댑터
   * 랙 탑재 키트 2개(탑재 하드웨어가 있는사이드 레일 4개, 2개는 각각 기본 인클로저와  EBOD 인클로저용), 각 상자에 1개
   * 시작 설명서
     
     위에 나열된 항목 중에 수신하지 않은 것이 있다면 [Microsoft 지원에 문의하세요](storsimple-8000-contact-microsoft-support.md).  

다음 단계는 디바이스를 랙에 탑재하는 것입니다.

## <a name="rack-mount-your-storsimple-8600-device"></a>StorSimple 8600 디바이스 랙 탑재
다음 단계를 따라 전면/후면 포스트가 있는 표준 19인치 랙에 StorSimple 8600 스토리지 디바이스를 설치하세요. 이 디바이스에는 기본 인클로저 및 EBOD 인클로저, 두 개의 인클로저가 있습니다. 양쪽 모두 랙에 탑재해야 합니다.

설치는 여러 단계로 구성되어 있으며, 각 단계는 다음 절차에서 설명합니다.

> [!IMPORTANT]
> StorSimple 디바이스는 올바른 작동을 위해 랙을 탑재해야 합니다.
> 
> 

### <a name="site-preparation"></a>사이트 준비
인클로저는 전면 및 후면 포스트가 있는 표준 19인치 랙에 설치해야 합니다. 다음 절차를 사용하여 랙 설치를 준비합니다.

#### <a name="to-prepare-the-site-for-rack-installation"></a>랙 설치를 위해 사이트를 준비하려면
1. 기본 및 EBOD 인클로저를 평평하고 안정적이고 고른 작업대(또는 이와 유사한)에 안전하게 놓습니다.
2. 설정하려는 사이트에 독립적인 소스에서 공급되는 표준 AC 전원이 있거나 무정전 전원 공급 장치(UPS)와 랙 전원 분배 장치(PDU)가 있는지 확인합니다.
3. 인클로저를 탑재하려는 랙에 하나의 4U(2 X 2U) 슬롯이 있는지 확인합니다.

![경고 아이콘](./media/storsimple-safety/IC740879.png)![높은 가중치 아이콘](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **경고가 발생 했습니다.**

 디바이스를 수동으로 설치하는 경우 반드시 두 사람이 디바이스의 무게를 지탱해야 합니다. 완전히 구성된 인클로저의 최고 무게는 32kg입니다.

### <a name="rack-prerequisites"></a>랙 필수 구성 요소
이 인클로저는 표준 19인치 랙 캐비닛 설치용으로 설계되었습니다.

* 랙 포스트에서 포스트까지 최소 깊이는 27.84 인치
* 디바이스의 최대 무게는 32kg
* 최대 역 압력은 5 파스칼(0.5mm 수면계)

### <a name="rack-mounting-rail-kit"></a>랙 탑재 레일 키트
19인치 랙 캐비닛에 사용할 탑재 레일 세트가 제공됩니다. 이들 레일은 최대 인클로저 무게를 견딜 수 있는지 테스트를 거쳤습니다. 이러한 레일은 또한 랙 내의 공간 손실 없이 여러 인클로저의 설치를 허용합니다. EBOD 인클로저를 먼저 설치합니다.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>레일에 EBOD 인클로저를 설치하려면
1. 내부 레일이 디바이스에 설치되지 않은 경우에만 이 단계를 수행합니다. 일반적으로 내부 레일은 공장에서 설치됩니다. 레일이 설치되지 않은 경우에는 왼쪽 레일과 오른쪽 레일의 슬라이드를 인클로저 섀시의 측면에 설치합니다. 각 면에서 6개의 미터 나사를 사용하여 연결합니다. 방향을 잡는 데 도움이 되도록 레일 슬라이드는 **LH – Front(전면)** 및 **RH – Front(전면)** 라고 표시되어 있으며 인클로저의 뒷면 쪽에 부착된 끝은 테이퍼 모양입니다.
   
    ![인클로저 섀시에 레일 슬라이드 장착](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
   
    **레일 슬라이드를 인클로저의 측면에 연결**
   
   | 레이블 | Description |
   | --- | --- |
   |  1 |M 3 x 4 단추 머리 나사 |
   |  2 |섀시 슬라이드 |
2. 왼쪽 레일 및 오른쪽 레일 어셈블리를 랙 캐비닛 수직재에 연결합니다. 브라켓에 **LH**, **RH**, **This side up(위쪽)** 이 표시되어 있어서 방향을 올바르게 잡을 수 있습니다.
3. 레일 어셈블리의 앞면/뒷면에서 레일 핀을 찾으세요. 랙 포스트 사이에 맞도록 레일을 확장하고 앞면/뒷면 랙 포스트 수직재의 구멍에 핀을 삽입합니다. 레일 어셈블리가 평평한지 확인합니다.
4. 제공된 미터 나사 2개를 사용하여 레일 어셈블리를 랙 수직재에 고정합니다. 앞면과 뒷면에 하나씩 나사를 사용합니다.
5. 다른 레일 어셈블리에 대해서도 이들 단계를 반복합니다.
   
     ![랙 캐비닛에 레일 슬라이드 장착](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **레일 어셈블리를 랙에 연결**
   
   | 레이블 | Description |
   | --- | --- |
   |   1 |고정 나사 |
   |   2 |정사각형 구멍 전면 랙 포스트 나사 |
   |   3 |왼쪽 전면 레일 위치 핀 |
   |   4 |고정 나사 |
   |   5 |왼쪽 후면 레일 위치 핀 |

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>EBOD 인클로저 랙 탑재
방금 설치한 랙 레일을 사용하여 다음 단계에 따라 EBOD 인클로저를 랙에 탑재합니다.

#### <a name="to-mount-the-ebod-enclosure"></a>EBOD 인클로저를 탑재하려면
1. 누군가의 도움을 받아서 인클로저를 들어 올려 랙 레일에 맞춥니다.
2. 주의를 기울여 인클로저를 레일에 삽입하고 랙 캐비닛으로 완전히 밀어 넣습니다.
   
    ![랙에 디바이스 삽입](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **랙에 인클로저 탑재**
3. 왼쪽 및 오른쪽 전면 플랜지 캡을 당겨서 제거합니다. 플랜지 캡은 플랜지에 쉽게 부착됩니다.
4. 왼쪽 및 오른쪽 각 플랜지를 통해 제공된 십자 머리 나사 하나를 설치하여 인클로저를 랙에 고정합니다.
5. 플랜지 캡을 제자리에 밀어 넣어 설치합니다.
   
     ![플랜지 캡 설치](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **플랜지 캡 설치**
   
   | 레이블 | Description |
   | --- | --- |
   |   1 |인클로저 잠금 나사 |

### <a name="mounting-the-primary-enclosure-in-the-rack"></a>기본 인클로저 랙에 탑재
EBOD 인클로저의 탑재가 완료되면 동일한 단계를 따라 기본 인클로저를 탑재해야 합니다.

> [!NOTE]
> * 기본 인클로저와 EBOD 인클로저 사이에서 랙에 몇 개의 빈 슬롯이 있을 수도 있습니다.
> * 제공된 2m SAS 케이블을 사용하여 기본 인클로저를 EBOD 인클로저에 연결합니다.
> * EBOD 장치에 대한 헤드 장치의 상대적인 배치에는 제약이 없습니다. 따라서 기본 인클로저를 맨 위 슬롯과 아래의 EBOD 인클로저에 놓을 수 있습니다. 그 반대의 경우도 마찬가지입니다.
> 
> 

다음 단계는 디바이스를 전원, 네트워크 및 직렬 액세스에 케이블로 연결하는 것입니다.

## <a name="cable-your-storsimple-8600-device"></a>StorSimple 8600 디바이스 케이블 연결
다음 절차는 StorSimple 8600 디바이스를 전원, 네트워크 및 직렬 연결을 위해 케이블로 연결하는 방법을 설명합니다.

### <a name="prerequisites"></a>사전 요구 사항
디바이스를 케이블로 연결하려면 다음이 필요합니다.

* 포장을 완전히 개봉한 기본 인클로저와 EBOD 인클로저
* 디바이스와 함께 제공되는 전원 케이블 4개(2개는 각각 기본 인클로저와 EBOD 인클로저용)
* EBOD 인클로저를 기본 인클로저에 연결하기 위해 디바이스와 함께 제공되는 SAS 케이블 2개
* 전력 분배 장치(PDU) 2개에 대한 액세스(권장)
* 네트워크 케이블
* 제공된 직렬 케이블
* PC에 적절한 드라이버가 설치된 직렬-USB 변환기(필요한 경우)
* 10GbE 네트워크 인터페이스와 함께 사용하기 위해 제공된 4개의 QSFP-대-SFP+어댑터
* [StorSimple 디바이스의 10GbE 네트워크 인터페이스에 대해 지원되는 하드웨어](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>SAS 및 전원 케이블 연결
이 디바이스에는 기본 인클로저 및 EBOD 인클로저가 있습니다. 장치를 Serial Attached SCSI(SAS) 연결 및 전원에 모두 케이블 연결해야 합니다.

처음으로 이 디바이스를 설정할 때 SAS 케이블 연결을 먼저 수행한 다음, 전원 케이블 연결에 대한 단계를 완료합니다.

[!INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>네트워크 케이블 연결
디바이스는 활성-대기 구성입니다. 항상 하나의 컨트롤러 모듈이 활성 상태이고 모든 디스크와 네트워크 작업을 처리하는 동안 다른 컨트롤러 모듈은 대기 상태입니다. 하나의 컨트롤러에 장애가 발생하면 대기 컨트롤러가 즉시 활성화되어 모든 디스크 및 네트워킹 작업을 계속 진행합니다.

이러한 예비 컨트롤러 장애 조치를 지원하려면 다음 단계에 표시된 것처럼 디바이스 네트워크를 케이블로 연결해야 합니다.

#### <a name="to-cable-for-network-connection"></a>케이블로 네트워크를 연결하려면
1. 디바이스에는 각 컨트롤러에 4개의 1Gbps 이더넷 포트와 2개의 10Gbps 이더넷 포트, 모두 6개의 네트워크 인터페이스가 있습니다. 다음 그림을 참조하여 디바이스의 백플레인에 있는 데이터 포트를 식별하세요.
   
     ![8600 디바이스의 백플레인](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **데이터 포트를 표시하는 디바이스의 뒷면**
   
   | 레이블 | Description |
   | --- | --- |
   |   0,1,4,5 |1GbE 네트워크 인터페이스 |
   |   2,3 |10GbE 네트워크 인터페이스 |
   |   6 |직렬 포트 |
2. 네트워크 케이블 연결에 대해서는 다음 다이어그램을 참조하세요. (최소 네트워크 구성은 파란색 실선으로 표시 됩니다. 높은 가용성과 성능을 위해 필요한 추가 구성을 점선으로 표시했습니다.)

![4U 디바이스를 네트워크에 케이블 연결](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**디바이스에 네트워크 케이블 연결**

| 레이블 | Description |
| --- | --- |
| A |인터넷 액세스 LAN |
| b |컨트롤러 0 |
| C |PCM 0 |
| D |컨트롤러 1 |
| E |PCM 1 |
| F |EBOD 컨트롤러 0 |
| G |EBOD 컨트롤러 1 |
| H, I |호스트(예: 파일 서버) |
| 0-5 |네트워크 인터페이스 |
| 6 |기본 인클로저 |
| 7 |EBOD 인클로저 |

디바이스 케이블을 연결하는 경우 다음과 같은 최소한의 구성이 필요합니다.

* 클라우드 액세스에 대한 하나의 네트워크 인터페이스 및 iSCSI에 대한 하나의 네트워크 인터페이스로 각 컨트롤러에 연결된 적어도 두 개의 네트워크 인터페이스. DATA 0 포트는 디바이스의 직렬 콘솔을 통해 자동으로 활성화 및 구성됩니다. DATA 0 이외에도 Azure 클래식 포털을 통해 다른 데이터 포트를 구성해야 합니다. 이 경우 DATA 0 포트를 기본 LAN(인터넷에 연결된 네트워크)에 연결합니다. 의도한 역할에 따라 다른 데이터 포트를 네트워크의 SAN/iSCSI LAN(VLAN) 세그먼트에 연결할 수 있습니다.
* 컨트롤러에서 장애가 발생하는 경우 가용성을 보장하려면 각 컨트롤러 상의 동일한 인터페이스와 관련 네트워크를 연결합니다 예를 들어, 컨트롤러 하나에 대해 DATA 0과 DATA 3을 연결하도록 설정했다면 다른 컨트롤러에서도 해당 DATA 0과 DATA 3을 연결해야 합니다.

높은 가용성과 성능을 위해 유의하십시오.

* 가능하면 각 컨트롤러에서 클라우드 액세스(1GbE)에 대한 네트워크 인터페이스의 쌍 및 iSCSI(10GbE 권장)에 대한 다른 쌍을 구성합니다.
* 가능하면 각 컨트롤러의 네트워크 인터페이스를 2개의 다른 스위치에 연결하여 스위치 장애 시 가용성을 보장합니다. 이 그림은 2개의 다른 스위치에 연결된 각 컨트롤러에서 2개의 10GbE 네트워크 인터페이스, DATA 2 및 DATA 3을 나타냅니다. 자세한 정보는 **StorSimple 디바이스에 대한 고가용성 요구 사항** 아래의 [네트워크 인터페이스](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple)를 참조하세요.

> [!NOTE]
> 10GbE 네트워크 인터페이스에서 SFP + 트랜시버를 사용하는 경우 제공된 QSFP-SFP + 어댑터를 사용합니다. 자세한 내용은 [StorSimple 디바이스의 10GbE 네트워크 인터페이스에 대해 지원되는 하드웨어](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)로 이동합니다.
> 
> 

### <a name="serial-port-cabling"></a>직렬 포트 케이블 연결
다음 단계를 수행하여 직렬 포트를 케이블로 연결하세요.

#### <a name="to-cable-for-serial-connection"></a>케이블로 직렬 연결을 하려면
1. 디바이스에는 각 컨트롤러에 렌치 아이콘으로 식별되는 직렬 포트가 있습니다. 직렬 포트를 찾으려면 디바이스의 뒷면에 있는 데이터 포트를 보여 주는 그림을 참조하세요.
2. 디바이스 백플레인에서 활성 컨트롤러를 찾으세요. 파란색 LED가 깜빡이면 컨트롤러가 활성 상태인 것입니다.
3. 제공된 직렬 케이블(필요한 경우 노트북용 USB-직렬 변환기 사용)을 사용하여 콘솔 또는 컴퓨터(디바이스에 터미널 에뮬레이션이 있는)를 활성 컨트롤러의 직렬 포트에 연결합니다.
4. 컴퓨터에 (디바이스와 함께 제공된) 직렬-USB 드라이버를 설치합니다.
5. 다음과 같이 직렬 연결을 설정합니다.
   
   * 115,200 baud
   * 8 데이터 비트
   * 1 정지 비트
   * 패리티 없음
   * 흐름 제어 **없음**
6. 콘솔에서 Enter 키를 눌러 연결이 작동하는지 확인합니다. 그러면 직렬 콘솔 메뉴가 나타납니다.

> [!NOTE]
> **정전 시 관리:** 디바이스가 원격 데이터센터나 접근이 제한된 컴퓨터실에 설치되어 있다면 두 컨트롤러에 대한 직렬 연결이 항상 직렬 콘솔 스위치 또는 비슷한 장비에 연결되어 있는지 확인해야 합니다. 이렇게 하면 네트워크 중단 또는 예기치 않은 장애 발생 시 대역 외 원격 제어 및 지원 작업이 허용됩니다.
> 
> 

이제 전원, 네트워크 액세스 및 직렬 연결을 위한 디바이스의 케이블 연결이 완료되었습니다. 다음 단계는 디바이스에서 소프트웨어를 구성하는 것입니다.

## <a name="next-steps"></a>다음 단계
이제 [온-프레미스 StorSimple 디바이스를 배포 및 구성](storsimple-8000-deployment-walkthrough-u2.md)할 준비가 되었습니다.

