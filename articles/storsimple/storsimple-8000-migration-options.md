---
title: StorSimple 5000-7000 시리즈 장치에서 데이터 마이그레이션 옵션
description: StorSimple 5000-7000 시리즈에서 데이터를 마이그레이션하는 옵션에 대해 간략히 설명합니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 44a05ecb273bdf7582300c3b6a9110e2ada0994c
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471825"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>StorSimple 5000-7000 시리즈에서 데이터를 마이그레이션하는 옵션 

> [!IMPORTANT]
> 2019 7 월 9 일에 StorSimple 5000/7000 시리즈는 EOS (지원 종료) 상태에 도달 합니다. StorSimple 5000/7000 시리즈 고객은 이 문서에 설명된 대안 제품 중 하나로 마이그레이션하는 것이 좋습니다.

StorSimple 5000-7000 시리즈에 대한 지원은 2019년 7월에 [중단](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series)될 예정입니다. StorSimple 5000-7000 시리즈를 실행하고 있는 고객에게는 다른 Azure 자체 하이브리드 서비스로 업그레이드할 수 있는 옵션이 있습니다. 이 문서에서는 데이터를 마이그레이션하는 데 사용할 수 있는 Azure 하이브리드 옵션에 대해 설명합니다. 

## <a name="migration-options"></a>마이그레이션 옵션

StorSimple 5000-7000 시리즈를 사용하는 고객에게는 Azure 또는 타사 옵션이 있습니다.

### <a name="azure-options"></a>Azure 옵션

#### <a name="upgrade-to-storsimple-8000-series"></a>StorSimple 8000 시리즈로 업그레이드

StorSimple 8000 시리즈로 업그레이드하여 StorSimple 플랫폼에서 계속 사용합니다.  이 업그레이드 경로를 사용하려면 고객이 5000-7000 시리즈 디바이스를 8000 시리즈로 교체해야 합니다. 데이터는 마이그레이션 도구를 사용하여 5000-7000 시리즈 디바이스에서 마이그레이션됩니다. 마이그레이션이 성공적으로 완료되면 StorSimple 8000 시리즈 디바이스에서 데이터를 Azure Blob Storage에 계속 계층화합니다. 

StorSimple 8000 시리즈를 사용하여 데이터를 마이그레이션하는 방법에 대한 자세한 내용은 [StorSimple 5000-7000 시리즈에서 8000 시리즈 디바이스로 데이터 마이그레이션](storsimple-8000-migrate-from-5000-7000.md)을 참조하세요.

#### <a name="migrate-to-azure-file-sync"></a>Azure 파일 동기화로 마이그레이션

이 새 마이그레이션 옵션을 사용하면 고객이 조직의 파일 공유를 Azure Files에 저장할 수 있습니다. 그러면 이러한 파일 공유에서 AFS(Azure 파일 동기화)를 사용하여 온-프레미스 액세스를 위해 중앙 집중화됩니다. AFS는 Windows Server 호스트에 배포할 수 있습니다. 그러면 실제 데이터 마이그레이션이 호스트 복사본으로 수행되거나 마이그레이션 도구를 사용하여 수행됩니다.

데이터를 Azure 파일 동기화로 마이그레이션하는 방법에 대한 자세한 내용은 [StorSimple 5000-7000 시리즈에서 Azure 파일 동기화로 데이터 마이그레이션](storsimple-5000-7000-afs-migration.md)을 참조하세요.

### <a name="third-party-options"></a>타사 옵션

#### <a name="migrate-to-panzura-freedom-nas"></a>Panzura Freedom NAS로 마이그레이션

StorSimple 5000-7000 고객은 Azure에서 데이터를 유지하기 위해 Panzura Freedom NAS로 마이그레이션하도록 선택할 수 있습니다. Panzura Freedom 솔루션은 데이터 센터, 사무실, 공용 및 프라이빗 클라우드를 포괄한 NAS 솔루션을 제공합니다. 솔루션을 통해 NFS, SMB 및 모바일 클라이언트에 대한 로컬, 하이브리드 및 클라우드 내 데이터 워크플로를 활성화할 수 있습니다. 

이 마이그레이션은 Panzura에서 지원하며, 고객은 [Panzura 웹 사이트](https://panzura.com/storsimple-migration/)에서 마이그레이션 지원을 요청하여 시작할 수 있습니다.

#### <a name="migrate-to-cohesity"></a>Cohesity로 마이그레이션

Cohesity를 사용하면 현재 StorSimple 5000-7000에서 Azure의 Cohesity 데이터 플랫폼으로 데이터를 마이그레이션할 수 있습니다. Cohesity 데이터 플랫폼은 파일, 백업, 개체 및 VM을 단일 클라우드 네이티브 솔루션으로 통합하는 소프트웨어 정의 웹 규모 솔루션입니다. 데이터 플랫폼으로 마이그레이션한 후에는 단일 창을 통해 데이터와 앱을 관리, 보호하고 클라우드에서 코어로 프로비전할 수 있습니다. Cohesity를 사용하면 겨우 세 개의 노드로 시작합니다. 

[Cohesity Data Platform으로의 마이그레이션](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html)에 대해 자세히 알아보세요.

#### <a name="migrate-to-nasuni"></a>Nasuni로 마이그레이션

Nasuni를 사용 하면 StorSimple 5000-7000 고객이 Azure에서 데이터를 쉽게 마이그레이션하고 보관할 수 있습니다.  Nasuni는 Azure 기반 NAS 저장소 솔루션으로, 고객에 게 온-프레미스 솔루션에서 제공 하는 성능 및 보안을 클라우드 경제성 및 규모와 함께 제공 합니다.  Nasuni 및 Azure는 고성능 파일 저장소 외에도, 중앙 집중식 파일 저장소 관리를 사용 하 여 전 세계 데이터를 공유 하 고 공동 작업할 수 있도록 하는 동시에 백업 및 DR을 처리 합니다. 

Nasuni를 통해 쉽게 마이그레이션할 수 있습니다. 지금 바로 시작 해 보세요. https://info.nasuni.com/nasuni-storsimple-migration

#### <a name="migrate-to-talon-fast"></a>Talon FAST로 마이그레이션

Talon를 사용 하면 StorSimple 5000-7000 고객은 보다 큰 함수를 사용 하 여 StorSimple 플랫폼 (무제한 클라우드 리소스에 의해 지원 되는 작은 현장 공간)에서 많은 혜택을 계속 활용할 수 있습니다.  Talon FAST 솔루션을 사용 하면 고객은 Azure에서 데이터를 마이그레이션 및 유지할 수 있으며, 이제는 훨씬 더 작은 소프트웨어 전용 온사이트 공간이 있고 전역 파일 잠금, 글로벌 네임 스페이스 및 다중 사이트 공동 작업과 같은 혜택을 추가 합니다.  Talon는 글로벌 고객과 협력 하 여 사용자 워크플로 또는 환경을 손상 시 키 지 않고 통합 된 Azure 기반 공간으로 온-프레미스 파일 서버 워크 로드를 마이그레이션하는 주요 Azure 에코 시스템 솔루션입니다.  

https://www.talonstorage.com/alliances/microsoft-storsimple에서 클라우드 통합 엔터프라이즈로 발전 하는 방법에 대해 자세히 알아보세요.


## <a name="migration---frequently-asked-questions"></a>마이그레이션 - 질문과 대답

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>17. StorSimple 5000 및 7000 시리즈 디바이스에 대한 서비스는 언제 종료되나요? 

A. StorSimple 5000-7000 시리즈에 대한 서비스는 2019년 7월에 [종료](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series)될 예정입니다. 2019년 7월 이후 서비스가 종료되면 Microsoft는 더 이상 이러한 디바이스의 하드웨어 및 소프트웨어 모두를 지원할 수 없게 됩니다. 이제는 디바이스에서 데이터를 마이그레이션하도록 계획하는 것이 좋습니다.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>17. Azure에 저장된 데이터는 어떻게 되나요?  

A. 새 서비스로 마이그레이션한 후에도 Azure에서 데이터를 계속 사용할 수 있습니다. 


### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>17. StorSimple 디바이스에 로컬로 저장된 데이터는 어떻게 되나요? 

A. 마이그레이션 문서에서 설명한 대로 로컬 디바이스에 있는 데이터는 새 서비스에 복사할 수 있습니다.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>17. StorSimple 5000/7000 시리즈 어플라이언스를 유지하려면 어떻게 되나요? 

A. 서비스는 계속 작동하지만 Microsoft는 더 이상 하드웨어 및 소프트웨어 지원을 제공하지 않습니다. 비즈니스 연속성을 위해 마이그레이션을 사용하는 것이 좋습니다.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>17. StorSimple 5000-7000 시리즈 디바이스에서 데이터를 마이그레이션하는 데 사용할 수 있는 옵션은 무엇인가요? 

A. StorSimple 5000-7000 시리즈 사용자는 시나리오에 따라 다음과 같은 마이그레이션 옵션을 사용할 수 있습니다. 

 - **8000 시리즈로 업그레이드**: StorSimple 플랫폼에서 계속하려면 이 옵션을 사용합니다. 
 - **Azure 파일 동기화로 마이그레이션**: Azure 원시 형식으로 전환하려면 이 옵션을 사용합니다. Azure 파일 동기화는 파일 공유를 중앙 집중식으로 관리하는 데 사용할 수 있습니다. 

Microsoft 지원에 문의하여 여기서 설명하지 않은 마이그레이션 옵션에 대해 논의할 수 있습니다.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>17. 다른 스토리지 솔루션으로 마이그레이션할 수 있나요?

A. 예. 데이터의 호스트 복사본을 사용하여 다른 스토리지 솔루션으로 마이그레이션할 수 있습니다.

### <a name="q-is-migration-supported-by-microsoft"></a>17. Microsoft에서 마이그레이션을 지원하나요? 

A. 5000 또는 7000 시리즈에서 마이그레이션하는 작업은 완벽하게 지원됩니다. 실제로는 먼저 고객 지원팀에 문의한 후에 마이그레이션을 시작하는 것이 좋습니다. 마이그레이션은 현재 지원형 작업입니다. StorSimple 5000-7000 시리즈 디바이스에서 데이터를 마이그레이션하려면 [지원 티켓을 여세요](storsimple-8000-contact-microsoft-support.md).

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>17. 두 가지 마이그레이션 옵션 모두에 대한 가격 책정 모델은 무엇인가요?

A. 마이그레이션 비용은 선택한 옵션에 따라 다릅니다. 마이그레이션 자체에는 추가 비용이 들지 않지만, StorSimple 8000 시리즈로 업그레이드하려는 경우 하드웨어 디바이스 비용이 발생하게 됩니다. 

마찬가지로 Azure 파일 동기화를 사용하는 경우 서비스 독 요금이 적용될 수 있습니다. 고객은 각각의 경우에서 진행 중인 스토리지 비용을 지불해야 합니다. 예상 비용은 다음을 참조하세요. 
- [StorSimple 가격 책정](https://azure.microsoft.com/pricing/details/storsimple/)  
- [AFS 가격 책정]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>17.  마이그레이션을 완료하는 데 얼마나 오래 걸리나요?

A. 데이터 마이그레이션을 완료하는 데 걸리는 시간은 선택한 데이터의 양과 선택한 업그레이드 옵션에 따라 다릅니다. 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>17. StorSimple 8000 시리즈의 지원 종료 날짜는 언제인가요?

A. StorSimple 8000 시리즈의 지원 종료 날짜는 [여기](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)에 게시됩니다.


## <a name="next-steps"></a>다음 단계
 - [StorSimple 5000-7000 시리즈에서 8000 시리즈 디바이스로 데이터 마이그레이션](storsimple-8000-migrate-from-5000-7000.md)
 - [StorSimple 5000-7000 시리즈에서 Azure 파일 동기화로 데이터 마이그레이션](storsimple-5000-7000-afs-migration.md)
