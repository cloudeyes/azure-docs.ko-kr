---
title: 데이터 중복
titleSuffix: Azure Storage
description: Microsoft Azure Storage 계정의 데이터는 내구성 및 고가용성을 위해 복제됩니다. 중복성 구성에는 LRS (로컬 중복 저장소), ZRS (영역 중복 저장소), GRS (지역 중복 저장소), 읽기 액세스 지역 중복 저장소 (RA-GRS), GZRS (지역 중복 저장소) (미리 보기) 및 읽기 액세스가 포함 됩니다. GZRS (지역 영역 중복 저장소) (미리 보기).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 7ae5f59a1bd96362d5466b2f6363185ba168d942
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255263"
---
# <a name="azure-storage-redundancy"></a>Azure Storage 중복성

Azure Storage는 일시적인 하드웨어 오류, 네트워크 또는 전원 중단, 대규모 자연 재해 등의 계획 되거나 계획 되지 않은 이벤트에서 보호 되도록 항상 데이터의 여러 복사본을 저장 합니다. 중복성은 저장소 계정이 오류 발생 시에도 [Azure Storage에 대 한 SLA (서비스 수준 계약)](https://azure.microsoft.com/support/legal/sla/storage/) 를 충족 하는지 확인 합니다.

시나리오에 가장 적합 한 중복성 옵션을 결정할 때 더 낮은 비용과 높은 가용성 및 내구성 간의 장단점을 고려해 야 합니다. 선택 해야 하는 중복성 옵션을 결정 하는 데 도움이 되는 요소는 다음과 같습니다.  

- 주 지역에서 데이터를 복제 하는 방법
- 지역 재해 로부터 보호 하기 위해 주 지역에 지리적으로 떨어져 있는 두 번째 위치에 데이터가 복제 되는지 여부
- 어떤 이유로 든 주 지역을 사용할 수 없게 되 면 보조 지역의 복제 된 데이터에 대 한 읽기 권한이 응용 프로그램에 필요한 지 여부

## <a name="redundancy-in-the-primary-region"></a>주 지역의 중복성

Azure Storage 계정의 데이터는 항상 주 지역에서 3 번 복제 됩니다. Azure Storage는 주 지역에서 데이터를 복제 하는 방법에 대 한 두 가지 옵션을 제공 합니다.

- **LRS (로컬 중복 저장소)** 는 주 지역의 단일 물리적 위치 내에서 데이터를 동기적으로 세 번 복사 합니다. LRS는 가장 저렴 한 복제 옵션 이지만 고가용성이 필요한 응용 프로그램에는 권장 되지 않습니다.
- **ZRS (영역 중복 저장소)** 는 기본 지역의 세 Azure 가용성 영역에 걸쳐 데이터를 동기적으로 복사 합니다. 고가용성이 필요한 응용 프로그램의 경우 주 지역에서 ZRS를 사용 하 고 보조 지역에도 복제 하는 것이 좋습니다.

### <a name="locally-redundant-storage"></a>LRS(로컬 중복 스토리지)

LRS (로컬 중복 저장소)는 주 지역의 단일 물리적 위치 내에서 데이터를 세 번 복제 합니다. LRS는 지정 된 연도 동안 최소 99.999999999% (11 9)의 개체 내 구성을 제공 합니다.

LRS는 가장 저렴 한 비용의 중복성 옵션이 며 다른 옵션에 비해 최소 내구성을 제공 합니다. LRS는 서버 랙 및 드라이브 오류 로부터 데이터를 보호 합니다. 그러나 데이터 센터 내에서 화재 또는 홍수와 같은 재해가 발생 하면 LRS를 사용 하는 저장소 계정의 모든 복제본이 손실 되거나 복구할 수 없을 수 있습니다. 이러한 위험을 완화 하기 위해 Microsoft에서는 ZRS ( [영역 중복 저장소](#zone-redundant-storage) ), GRS ( [지역 중복 저장소](#geo-redundant-storage) ) 또는 GZRS (지역 중복 저장소) ( [미리 보기)](#geo-zone-redundant-storage-preview) 를 사용 하는 것이 좋습니다.

LRS를 사용 하는 저장소 계정에 대 한 쓰기 요청은 동기적으로 발생 합니다. 쓰기 작업은 3 개의 모든 복제본에 데이터가 기록 된 후에만 성공적으로 반환 됩니다.

LRS는 다음과 같은 시나리오에 적합 합니다.

- 데이터 손실이 발생하더라도 쉽게 재구성할 수 있는 데이터를 애플리케이션에 저장하는 경우 LRS를 선택할 수 있습니다.
- 데이터 거 버 넌 스 요구 사항으로 인해 국가 또는 지역 내 에서만 데이터를 복제 하도록 응용 프로그램을 제한 하는 경우 LRS를 선택할 수 있습니다. 경우에 따라 데이터가 지리적으로 복제 되는 쌍을 이루는 지역이 다른 국가 또는 지역에 있을 수 있습니다. 쌍을 이루는 지역에 대한 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요.

### <a name="zone-redundant-storage"></a>영역 중복 스토리지

ZRS (영역 중복 저장소)는 기본 지역의 세 Azure 가용성 영역에서 Azure Storage 데이터를 동기적으로 복제 합니다. 각 가용성 영역은 독립 된 전원, 냉각 및 네트워킹을 포함 하는 별도의 물리적 위치입니다. ZRS는 지정 된 연도에 99.9999999999% (12 9의) 이상의 Azure Storage 데이터 개체에 대 한 내구성을 제공 합니다.

ZRS를 사용 하면 영역을 사용할 수 없는 경우에도 읽기 및 쓰기 작업을 위해 데이터에 계속 액세스할 수 있습니다. 영역을 사용할 수 없는 경우 Azure 배포한는 DNS 다시 가리키기와 같은 네트워킹 업데이트를 업데이트 합니다. 업데이트를 완료 하기 전에 데이터에 액세스 하는 경우 이러한 업데이트는 응용 프로그램에 영향을 줄 수 있습니다. ZRS에 대 한 응용 프로그램을 디자인 하는 경우 지 수 백오프를 사용 하 여 재시도 정책 구현을 비롯 하 여 일시적인 오류 처리에 대 한 지침을 따르세요.

ZRS를 사용 하는 저장소 계정에 대 한 쓰기 요청은 동기적으로 발생 합니다. 쓰기 작업은 3 개의 가용성 영역에서 모든 복제본에 데이터가 기록 된 후에만 성공적으로 반환 됩니다.

일관성, 내구성 및 고가용성이 필요한 시나리오의 경우 주 지역에서 ZRS를 사용 하는 것이 좋습니다. ZRS는 일시적으로 사용할 수 없게 되는 경우 데이터에 대 한 뛰어난 성능, 짧은 대기 시간 및 복원 력을 제공 합니다. 그러나 ZRS는 여러 영역이 영구적으로 영향을 받는 지역 재해 로부터 데이터를 보호 하지 못할 수 있습니다. 지역 재해 로부터 보호 하기 위해 Microsoft는 주 지역에서 ZRS를 사용 하 고 보조 지역에 데이터를 지역으로 복제 하는 GZRS ( [지역 영역 중복 저장소](#geo-zone-redundant-storage-preview) )를 사용 하는 것이 좋습니다.

다음 표에서는 ZRS를 지 원하는 저장소 계정의 유형을 보여 줍니다.

|    Storage 계정 유형    |    지원되는 지역    |    지원되는 서비스    |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
|    범용 v2<sup>1</sup>    | 동남아시아<br /> 오스트레일리아 동부<br /> 북유럽<br />  서유럽<br /> 프랑스 중부<br /> 일본 동부<br /> 남아프리카 북부<br /> 영국 남부<br /> 미국 중부<br /> 미국 동부<br /> 미국 동부 2<br /> 미국 서부 2    |    블록 Blob<br /> 페이지 blob<sup>2</sup><br /> 파일 공유 (표준)<br /> 테이블<br /> 큐<br /> |
|    BlockBlobStorage<sup>1</sup>    | 서유럽<br /> 미국 동부    |    블록 Blob만 해당    |
|    FileStorage    | 서유럽<br /> 미국 동부    |    Azure Files만    |

<sup>1</sup> 보관 계층은 현재 ZRS 계정에 대해 지원 되지 않습니다.<br />
<sup>2</sup> 가상 컴퓨터에 대 한 Azure managed disks를 포함 하는 저장소 계정은 항상 LRS을 사용 합니다. Azure 관리 되지 않는 디스크는 LRS도 사용 해야 합니다. GRS를 사용 하는 Azure 관리 되지 않는 디스크에 대 한 저장소 계정을 만들 수 있지만, 비동기 지역에서 복제를 통해 일관성 문제가 발생할 수 있기 때문에 권장 되지 않습니다. 관리 되거나 관리 되지 않는 디스크는 ZRS 또는 GZRS를 지원 하지 않습니다. 관리 디스크에 대 한 자세한 내용은 [Azure managed Disks 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)을 참조 하세요.

ZRS를 지 원하는 지역에 대 한 자세한 내용은 [Azure 가용성 영역](../../availability-zones/az-overview.md)의 **지역별 서비스 지원** 을 참조 하세요.

## <a name="redundancy-in-a-secondary-region"></a>보조 지역의 중복성

고가용성이 필요한 응용 프로그램의 경우 주 지역에서 수백 마일 떨어진 보조 지역에 저장소 계정의 데이터를 추가로 복사 하도록 선택할 수 있습니다. 저장소 계정이 보조 지역에 복사 되는 경우 전체 지역 가동 중단 또는 주 지역을 복구할 수 없는 재해가 발생 하더라도 데이터는 지속 됩니다.

스토리지 계정을 만들 때 계정에 대한 기본 지역을 선택합니다. 쌍을 이루는 보조 지역은 주 지역에 따라 결정되며 변경할 수 없습니다. Azure에서 지원 되는 지역에 대 한 자세한 내용은 [azure 지역](https://azure.microsoft.com/global-infrastructure/regions/)을 참조 하세요.

Azure Storage는 보조 지역으로 데이터를 복사 하는 두 가지 옵션을 제공 합니다.

- **GRS (지역 중복 저장소)** 는 LRS를 사용 하 여 주 지역의 단일 물리적 위치 내에서 데이터를 동기적으로 세 번 복사 합니다. 그런 다음 데이터를 보조 지역의 단일 물리적 위치에 비동기적으로 복사 합니다.
- **GZRS (지역 중복 저장소** ) (미리 보기)는 ZRS를 사용 하 여 주 지역의 3 개 Azure 가용성 영역에서 데이터를 동기적으로 복사 합니다. 그런 다음 데이터를 보조 지역의 단일 물리적 위치에 비동기적으로 복사 합니다.

GRS와 GZRS 간의 주요 차이점은 데이터가 주 지역에 복제 되는 방식입니다. 보조 위치 내에서 데이터는 항상 LRS를 사용 하 여 동기적으로 세 번 복제 됩니다.

GRS 또는 GZRS를 사용 하는 경우 보조 지역에 대 한 장애 조치 (failover)가 없으면 보조 위치의 데이터를 읽기 또는 쓰기 액세스에 사용할 수 없습니다. 보조 위치에 대 한 읽기 액세스의 경우 읽기 액세스 지역 중복 저장소 (RA-GRS) 또는 읽기 액세스 지역 중복 저장소 (RA-GZRS)를 사용 하도록 저장소 계정을 구성 합니다. 자세한 내용은 [보조 지역의 데이터에 대 한 읽기 액세스](#read-access-to-data-in-the-secondary-region)를 참조 하세요.

주 지역을 사용할 수 없게 되 면 보조 지역 (미리 보기)으로 장애 조치 (failover) 하도록 선택할 수 있습니다. 장애 조치 (failover)가 완료 되 면 보조 지역은 주 지역이 되며 데이터를 다시 읽고 쓸 수 있습니다. 재해 복구에 대 한 자세한 내용 및 보조 지역으로 장애 조치 하는 방법에 대 한 자세한 내용은 [재해 복구 및 계정 장애 조치 (failover) (미리 보기)](storage-disaster-recovery-guidance.md)를 참조 하세요.

> [!IMPORTANT]
> 데이터는 보조 지역에 비동기적으로 복제 되기 때문에 주 지역에 영향을 주는 오류로 인해 주 지역을 복구할 수 없는 경우 데이터가 손실 될 수 있습니다. 주 지역에 대 한 최신 쓰기와 보조 지역에 대 한 마지막 쓰기 사이의 간격을 RPO (복구 지점 목표) 라고 합니다. RPO는 데이터를 복구할 수 있는 시점을 나타냅니다. 현재는 데이터를 보조 지역으로 복제 하는 데 걸리는 시간에 대 한 SLA는 없지만 일반적으로는 15 분 미만의 RPO가 Azure Storage 됩니다.

### <a name="geo-redundant-storage"></a>지역 중복 스토리지

GRS (지역 중복 저장소)는 LRS를 사용 하 여 주 지역의 단일 물리적 위치 내에서 데이터를 동기적으로 세 번 복사 합니다. 그런 다음 주 지역에서 수백 마일 떨어져 있는 보조 지역의 단일 물리적 위치에 데이터를 비동기적으로 복사 합니다. GRS는 지정 된 연도 동안 최소 99.99999999999999% (16 9)의 Azure Storage 데이터 개체에 대 한 내구성을 제공 합니다.

쓰기 작업은 먼저 기본 위치로 커밋되고 LRS를 사용 하 여 복제 됩니다. 그런 다음 업데이트는 보조 지역에 비동기식으로 복제 됩니다. 데이터가 보조 위치에 기록되는 경우 LRS를 사용하여 해당 위치 내에도 복제됩니다.

### <a name="geo-zone-redundant-storage-preview"></a>지리적 영역 중복 저장소 (미리 보기)

GZRS (지역 중복 저장소) (미리 보기)는 지역에서 복제에서 제공 하는 지역 가동 중단 으로부터 보호 하 여 가용성 영역 간 중복성으로 제공 되는 고가용성을 결합 합니다. GZRS 저장소 계정의 데이터는 주 지역에 있는 세 개의 [Azure 가용성 영역](../../availability-zones/az-overview.md) 에 복사 되며 지역 재해 로부터 보호 하기 위해 보조 지역에도 복제 됩니다. Microsoft는 재해 복구에 대 한 최대 일관성, 내구성 및 가용성, 뛰어난 성능 및 복원 력을 필요로 하는 응용 프로그램에 GZRS를 사용 하는 것이 좋습니다.

GZRS 저장소 계정을 사용 하 여 가용성 영역을 사용할 수 없게 되거나 복구할 수 없는 경우 계속 해 서 데이터를 읽고 쓸 수 있습니다. 또한 전체 지역 가동 중단 또는 주 지역을 복구할 수 없는 재해가 발생 한 경우에도 데이터를 지속적으로 사용할 수 있습니다. GZRS는 지정 된 연도 동안 최소 99.99999999999999% (16 9의) 개체 내 구성을 제공 하도록 설계 되었습니다.

범용 v2 저장소 계정만 GZRS 및 RA-GZRS을 지원 합니다. 애플리케이션 계정 유형에 대한 자세한 내용은 [Azure Storage 계정 개요](storage-account-overview.md)를 참조하세요. GZRS 및 RA-GZRS는 블록 blob, 페이지 blob (VHD 디스크 제외), 파일, 테이블 및 큐를 지원 합니다.

GZRS 및 RA-GZRS는 현재 다음 지역에서 미리 보기로 제공 됩니다.

- 동남아시아
- 북유럽
- 서유럽
- 일본 동부
- 영국 남부
- 미국 동부
- 미국 동부 2
- 미국 중부
- 미국 서부 2

Microsoft는 추가 Azure 지역에서 GZRS 및 RA GZRS을 계속 사용할 수 있습니다. 지원 되는 지역에 대 한 자세한 내용은 [Azure 서비스 업데이트](https://azure.microsoft.com/updates/) 페이지를 정기적으로 확인 하세요.

미리 보기 가격 책정에 대 한 자세한 내용은 [blob](https://azure.microsoft.com/pricing/details/storage/blobs), [파일](https://azure.microsoft.com/pricing/details/storage/files/), [큐](https://azure.microsoft.com/pricing/details/storage/queues/)및 [테이블](https://azure.microsoft.com/pricing/details/storage/tables/)에 대 한 GZRS 미리 보기 가격 책정을 참조 하세요.

> [!IMPORTANT]
> 프로덕션 워크 로드에는 미리 보기 기능을 사용 하지 않는 것이 좋습니다.

## <a name="read-access-to-data-in-the-secondary-region"></a>보조 지역의 데이터에 대 한 읽기 액세스

지역 중복 저장소 (GRS 또는 GZRS 사용)는 지역 가동 중단 으로부터 보호 하기 위해 보조 지역의 다른 물리적 위치로 데이터를 복제 합니다. 그러나 고객이 나 Microsoft가 주 지역에서 보조 지역으로 장애 조치 (failover)를 시작 하는 경우에만 데이터를 읽을 수 있습니다. 보조 지역에 대 한 읽기 액세스를 사용 하도록 설정 하면 주 지역을 사용할 수 없게 되는 경우 데이터를 읽을 수 있습니다. 보조 지역에 대 한 읽기 액세스의 경우 읽기 액세스 지역 중복 저장소 (RA-GRS) 또는 읽기 액세스 지역 중복 저장소 (RA-GZRS)를 사용 하도록 설정 합니다.

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>보조 데이터베이스에 대 한 읽기 액세스를 위한 응용 프로그램 디자인

저장소 계정이 보조 지역에 대 한 읽기 액세스로 구성 된 경우 어떤 이유로 든 주 지역을 사용할 수 없게 되 면 보조 지역에서 데이터를 읽도록 원활 하 게 이동 하도록 응용 프로그램을 디자인할 수 있습니다. 보조 지역은 항상 읽기 액세스에 사용할 수 있으므로 응용 프로그램을 테스트 하 여 가동 중단 시 보조 데이터베이스에서 읽을 수 있는지 확인할 수 있습니다. 고가용성을 위해 응용 프로그램을 디자인 하는 방법에 대 한 자세한 내용은 [읽기 액세스 지역 중복 저장소를 사용 하 여 항상 사용 가능한 응용 프로그램 디자인](storage-designing-ha-apps-with-ragrs.md)을 참조 하세요.

보조 복제본에 대 한 읽기 액세스를 사용 하도록 설정 하면 저장소 계정의 기본 끝점 뿐만 아니라 보조 끝점에서 데이터를 읽을 수 있습니다. 보조 끝점은 계정 이름에 접미사 *-보조* 를 추가 합니다. 예를 들어 Blob 저장소에 대 한 기본 끝점이 `myaccount.blob.core.windows.net`경우 보조 끝점은 `myaccount-secondary.blob.core.windows.net`됩니다. 저장소 계정에 대 한 계정 액세스 키는 기본 및 보조 끝점에 대해 동일 합니다.

### <a name="check-the-last-sync-time-property"></a>마지막 동기화 시간 속성 확인

데이터는 보조 지역에 비동기식으로 복제 되므로 보조 지역은 주로 주 지역 뒤에 있습니다. 주 지역에서 오류가 발생 하는 경우 주 복제본에 대 한 모든 쓰기는 아직 보조 복제본에 복제 되지 않은 것일 수 있습니다.

보조 지역에 복제 된 쓰기 작업을 확인 하기 위해 응용 프로그램은 저장소 계정에 대 한 **마지막 동기화 시간** 속성을 확인할 수 있습니다. 마지막 동기화 시간 이전에 주 지역에 작성 된 모든 쓰기 작업은 보조 지역에 성공적으로 복제 되어 보조 지역에서 읽을 수 있습니다. 마지막 동기화 시간 이후 주 지역에 작성 된 모든 쓰기 작업은 보조 지역으로 복제 될 수도 있고 그렇지 않을 수도 있습니다. 즉, 읽기 작업에 사용할 수 없습니다.

Azure PowerShell, Azure CLI 또는 Azure Storage 클라이언트 라이브러리 중 하나를 사용 하 여 **마지막 동기화 시간** 속성의 값을 쿼리할 수 있습니다. **마지막 동기화 시간** 속성은 GMT 날짜/시간 값입니다. 자세한 내용은 [저장소 계정에 대 한 마지막 동기화 시간 속성 확인](last-sync-time-get.md)을 참조 하세요.

## <a name="summary-of-redundancy-options"></a>중복성 옵션 요약

다음 표에서는 저장소 계정에 적용 되는 중복성 유형에 따라 지정 된 시나리오에서 지속 되 고 사용 가능한 데이터의 양을 보여 줍니다.

| 시나리오                                                                                                 | LRS                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS (미리 보기)                              |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| 데이터 센터 내의 노드를 사용할 수 없게 됩니다.                                                                 | yes                             | yes                              | yes                                  | yes                                  |
| 전체 데이터 센터(영역 또는 비영역)를 사용할 수 없게 됨                                           | 예                              | yes                              | yes                                  | yes                                  |
| 지역 전체 중단이 발생 합니다.                                                                                     | 예                              | 예                               | yes                                  | yes                                  |
| 주 지역을 사용할 수 없게 되 면 보조 지역의 데이터에 대 한 읽기 액세스 | 예                              | 예                               | 예 (RA-GRS 사용)                                   | 예 (RA-GZRS 사용)                                 |
| 지정 된 연도에 대 한 개체의 내구성 비율<sup>1</sup>                                          | 최소 99.999999999% | 최소 99.9999999999% | 최소 99.99999999999999% | 최소 99.99999999999999% |
| 지원 되는 저장소 계정 유형<sup>2</sup>                                                                   | GPv2, GPv1, BlockBlobStorage, BlobStorage, FileStorage                | GPv2, BlockBlobStorage, FileStorage                             | GPv2, GPv1, BlobStorage                     | GPv2                     |
| 읽기 요청에 대 한 가용성 SLA<sup>1</sup>  | 최소 99.9%(쿨 액세스 계층에 대해 99%) | 최소 99.9%(쿨 액세스 계층에 대해 99%) | GRS에 대 한 최소 99.9% (쿨 액세스 계층의 경우 99%)<br /><br />RA-GRS에 대 한 최소 99.99% (쿨 액세스 계층의 경우 99.9%) | GZRS에 대 한 최소 99.9% (쿨 액세스 계층의 경우 99%)<br /><br />RA-GZRS에 대 한 최소 99.99% (쿨 액세스 계층의 경우 99.9%) |
| 쓰기 요청에 대 한 가용성 SLA<sup>1</sup>  | 최소 99.9%(쿨 액세스 계층에 대해 99%) | 최소 99.9%(쿨 액세스 계층에 대해 99%) | 최소 99.9%(쿨 액세스 계층에 대해 99%) | 최소 99.9%(쿨 액세스 계층에 대해 99%) |

<sup>1</sup> 내구성 및 가용성을 보장 하는 Azure Storage에 대 한 자세한 내용은 [Azure Storage SLA](https://azure.microsoft.com/support/legal/sla/storage/)를 참조 하십시오.

<sup>2</sup> 저장소 계정 유형에 대 한 자세한 내용은 [저장소 계정 개요](storage-account-overview.md)를 참조 하세요.

모든 유형의 저장소 계정에 대 한 모든 데이터는 저장소 계정에 대 한 중복성 옵션에 따라 복사 됩니다. 블록 blob, 추가 blob, 페이지 blob, 큐, 테이블 및 파일을 포함 하는 개체가 복사 됩니다.

각 중복성 옵션에 대 한 가격 책정 정보는 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/)을 참조 하세요.

> [!NOTE]
> Azure Premium 디스크 저장소는 현재 LRS (로컬 중복 저장소)만 지원 합니다. 블록 blob storage 계정은 특정 지역의 LRS (로컬 중복 저장소) 및 ZRS (영역 중복 저장소)를 지원 합니다.

## <a name="data-integrity"></a>데이터 무결성

Azure Storage는 CRCs (순환 중복 검사)를 사용 하 여 저장 된 데이터의 무결성을 정기적으로 확인 합니다. 데이터 손상이 감지 되 면 중복 데이터를 사용 하 여 복구 됩니다. 또한 Azure Storage는 데이터를 저장 하거나 검색할 때 데이터 패킷의 손상을 검색 하기 위해 모든 네트워크 트래픽에 대 한 체크섬을 계산 합니다.

## <a name="see-also"></a>참고 항목

- [저장소 계정에 대 한 마지막 동기화 시간 속성 확인](last-sync-time-get.md)
- [저장소 계정에 대 한 중복성 옵션 변경](redundancy-migration.md)
- [RA-GRS 스토리지를 사용하여 항상 사용 가능한 애플리케이션 설계](../storage-designing-ha-apps-with-ragrs.md)
- [재해 복구 및 계정 장애 조치 (failover) (미리 보기)](storage-disaster-recovery-guidance.md)
