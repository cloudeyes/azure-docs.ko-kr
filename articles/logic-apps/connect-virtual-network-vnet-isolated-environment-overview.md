---
title: Azure virtual network에 대 한 액세스
description: ISEs (integration service environment)가 Azure 가상 네트워크에 액세스 하는 방법에 대 한 개요 (Vnet)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: 9d5e0c088fe773f16e1fc57f292ca812906aa09c
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127254"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>ISE(통합 서비스 환경)를 사용하여 Azure Logic Apps에서 Azure Virtual Network 리소스에 액세스

경우에 따라 논리 앱은 [Azure virtual network](../virtual-network/virtual-networks-overview.md)내에 있는 vm (가상 머신), 기타 시스템 또는 서비스와 같은 보안 리소스에 액세스 해야 합니다. 이 액세스를 설정 하기 위해 [ISE ( *integration service environment* )를 만들](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)수 있습니다. ISE는 전용 리소스를 사용 하 고 "전역" 다중 테 넌 트 Logic Apps 서비스와 별도로 실행 되는 Logic Apps 서비스의 격리 된 인스턴스입니다.

고유 하 게 분리 된 개별 인스턴스에서 논리 앱을 실행 하면 다른 Azure 테 넌 트가 응용 프로그램의 성능에 미치는 영향을 줄일 수 있습니다 .이는 ["잡음이 있는 환경" 효과](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)라고도 합니다. ISE는 다음과 같은 이점도 제공 합니다.

* 다중 테 넌 트 서비스의 논리 앱에서 공유 하는 고정 IP 주소와는 별개의 고정 IP 주소입니다. 대상 시스템과 통신 하도록 단일 공용, 정적 및 예측 가능한 아웃 바운드 IP 주소를 설정할 수도 있습니다. 이런 방식으로 각 ISE에 대 한 해당 대상 시스템에서 추가 방화벽을 설정할 필요가 없습니다.

* 실행 지속 시간, 저장소 보존, 처리량, HTTP 요청 및 응답 시간 제한, 메시지 크기 및 사용자 지정 커넥터 요청에 대 한 제한이 증가 합니다. 자세한 내용은 [Azure Logic Apps에 대 한 제한 및 구성](logic-apps-limits-and-config.md)을 참조 하세요.

> [!NOTE]
> 일부 Azure virtual network는 azure에서 호스트 되는 Azure Storage, Azure Cosmos DB, Azure SQL Database, 파트너 서비스 또는 고객 서비스와 같은 Azure PaaS 서비스에 대 한 액세스를 제공 하기 위해 개인 끝점 ([Azure 개인 링크](../private-link/private-link-overview.md))을 사용 합니다. 논리 앱에서 개인 끝점을 사용 하는 가상 네트워크에 액세스 해야 하는 경우에는 ISE 내에서 해당 논리 앱을 만들고, 배포 하 고, 실행 해야 합니다.

ISE를 만들 때 Azure는 해당 ISE를 Azure 가상 네트워크에 *삽입* 하거나 배포 합니다. 그런 다음 액세스 해야 하는 논리 앱 및 통합 계정의 위치로이 ISE를 사용할 수 있습니다.

![통합 서비스 환경 선택](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

논리 앱은 논리 앱과 동일한 ISE에서 실행 되는 다음 항목을 사용 하 여 가상 네트워크 내부 또는 연결 된 리소스에 액세스할 수 있습니다.

* HTTP 트리거 또는 작업과 같은 **핵심**레이블이 지정 된 기본 제공 트리거 또는 동작입니다.
* 해당 시스템 또는 서비스에 대 한 **ISE**레이블 커넥터
* 사용자 지정 커넥터

또한 ISE에서 논리 앱을 사용 하 여 **핵심** 또는 **ise** 레이블이 없는 커넥터를 계속 사용할 수 있습니다. 이러한 커넥터는 다중 테 넌 트 Logic Apps 서비스에서 대신 실행 됩니다. 자세한 내용은 다음 섹션을 참조 하세요.

* [격리 된 다중 테 넌 트 비교](#difference)
* [통합 서비스 환경에서 연결](../connectors/apis-list.md#integration-service-environment)
* [ISE 커넥터](../connectors/apis-list.md#ise-connectors)

> [!IMPORTANT]
> ISE에서 실행 되는 논리 앱, 기본 제공 트리거, 기본 제공 작업 및 커넥터는 소비 기반 요금제와는 다른 요금제를 사용 합니다. 자세한 내용은 [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md#fixed-pricing)을 참조 하세요. 가격 책정에 대 한 자세한 내용은 [Logic Apps 가격 책정](../logic-apps/logic-apps-pricing.md)을 참조 하세요.

이 개요에서는 ISE에서 논리 앱에 Azure virtual network에 대 한 직접 액세스를 제공 하 고 ISE와 다중 테 넌 트 Logic Apps 서비스의 차이점을 비교 하는 방법에 대해 자세히 설명 합니다.

<a name="difference"></a>

## <a name="isolated-versus-multi-tenant"></a>격리 된 다중 테 넌 트 비교

ISE에서 논리 앱을 만들고 실행 하는 경우 다중 테 넌 트 Logic Apps 서비스와 동일한 사용자 환경 및 유사한 기능을 사용할 수 있습니다. 다중 테 넌 트 Logic Apps 서비스에서 사용할 수 있는 동일한 기본 제공 트리거, 작업 및 관리 되는 커넥터를 모두 사용할 수 있습니다. 일부 관리 되는 커넥터는 추가 ISE 버전을 제공 합니다. Ise 커넥터와 ise가 아닌 커넥터 간의 차이점은 ise 내에서 작업할 때 실행 되는 위치와 논리 앱 디자이너에 있는 레이블이 있습니다.

![ISE에 레이블이 있거나 없는 커넥터](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* 기본 제공 트리거 및 작업은 **핵심** 레이블을 표시 합니다. 항상 논리 앱과 동일한 ISE에서 실행 됩니다. **Ise** 레이블을 표시 하는 관리 커넥터는 논리 앱과 동일한 ISE 에서도 실행 됩니다.

  예를 들어 ISE 버전을 제공 하는 일부 커넥터는 다음과 같습니다.

  * Azure Blob Storage, File Storage 및 Table Storage
  * Azure Queues, Azure Service Bus, Azure Event Hubs 및 IBM MQ
  * FTP 및 SFTP-SSH
  * SQL Server, Azure SQL Data Warehouse, Azure Cosmos DB
  * AS2, X12 및 EDIFACT

* 추가 레이블을 표시 하지 않는 관리 되는 커넥터는 항상 다중 테 넌 트 Logic Apps 서비스에서 실행 되지만 ISE에서 호스트 되는 논리 앱에서 여전히 이러한 커넥터를 사용할 수 있습니다.

<a name="on-premises"></a>

### <a name="access-to-on-premises-systems"></a>온-프레미스 시스템에 대 한 액세스

Azure 가상 네트워크에 연결 된 온-프레미스 시스템 또는 데이터 원본에 액세스 하기 위해 ISE의 논리 앱은 다음 항목을 사용할 수 있습니다.

* HTTP 동작

* ISE-해당 시스템용으로 레이블이 지정 된 커넥터

  > [!NOTE]
  > [Ise (integration service environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)에서 SQL Server 커넥터를 사용 하 여 Windows 인증을 사용 하려면 커넥터의 비 ise 버전을 [온-프레미스 데이터 게이트웨이와](../logic-apps/logic-apps-gateway-install.md)함께 사용 합니다. ISE 레이블이 지정 된 버전은 Windows 인증을 지원 하지 않습니다.

* 사용자 지정 커넥터

  * 온-프레미스 데이터 게이트웨이를 필요로 하는 사용자 지정 커넥터를 사용 하 고 ISE 외부에서 커넥터를 만든 경우 ISE의 논리 앱 에서도 이러한 커넥터를 사용할 수 있습니다.

  * ISE에서 만든 사용자 지정 커넥터는 온-프레미스 데이터 게이트웨이와 작동 하지 않습니다. 그러나 이러한 커넥터는 ISE를 호스트 하는 가상 네트워크에 연결 된 온-프레미스 데이터 원본에 직접 액세스할 수 있습니다. 따라서 ISE의 논리 앱은 이러한 리소스와 통신할 때 데이터 게이트웨이가 필요 하지 않을 수 있습니다.

가상 네트워크에 연결 되어 있지 않거나 ISE로 레이블이 지정 되지 않은 온-프레미스 시스템의 경우 논리 앱이 해당 시스템에 연결 하기 전에 먼저 [온-프레미스 데이터 게이트웨이를 설정](../logic-apps/logic-apps-gateway-install.md) 해야 합니다.

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE Sku

ISE를 만들 때 개발자 SKU 또는 프리미엄 SKU를 선택할 수 있습니다. 이러한 Sku 간의 차이점은 다음과 같습니다.

* **개발자**

  는 실험, 개발 및 테스트에 사용할 수 있지만 프로덕션 또는 성능 테스트에는 사용할 수 없는 저렴 한 ISE를 제공 합니다. 개발자 SKU에는 고정 월별 가격에 대 한 기본 제공 트리거 및 작업, 표준 커넥터, 엔터프라이즈 커넥터 및 단일 [무료 계층](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 통합 계정이 포함 되어 있습니다. 그러나이 SKU에는 SLA (서비스 수준 계약), 용량을 확장 하는 옵션 또는 재활용 중 중복성을 포함 하지 않습니다. 즉, 지연 또는 가동 중지 시간이 발생할 수 있습니다.

* **Premium**

  는 프로덕션에 사용할 수 있는 ISE를 제공 하 고 SLA 지원, 기본 제공 트리거 및 작업, 표준 커넥터, 엔터프라이즈 커넥터, 단일 [표준 계층](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 통합 계정, 용량을 확장 하는 옵션 및 고정 된 월별 가격을 재활용 하는 동안 중복성을 포함 합니다.

> [!IMPORTANT]
> SKU 옵션은 ISE를 만들 때만 사용할 수 있으며 나중에 변경할 수 없습니다.

가격 책정 요금은 [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 참조 하세요. ISEs에 대 한 가격 책정 및 청구의 작동 방식에 대 한 자세한 [Logic Apps 내용은 가격 책정 모델](../logic-apps/logic-apps-pricing.md#fixed-pricing)을 참조 하세요.

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>ISE 끝점 액세스

ISE를 만들 때 내부 또는 외부 액세스 끝점 중 하나를 사용 하도록 선택할 수 있습니다. 선택은 ISE의 논리 앱에 대 한 요청 또는 webhook 트리거가 가상 네트워크 외부에서 호출을 받을 수 있는지 여부를 결정 합니다.

이러한 끝점은 논리 앱의 실행 기록에서 입/출력에 액세스할 수 있는 방법에도 영향을 줍니다.

* **내부**: *가상 네트워크 내부 에서만* 실행 기록에서 논리 앱의 입력 및 출력을 보고 액세스할 수 있는 ISE에서 논리 앱에 대 한 호출을 허용 하는 개인 끝점

* **외부**: *가상 네트워크 외부의*실행 기록에서 논리 앱의 입력 및 출력을 보고 액세스할 수 있는 ISE에서 논리 앱에 대 한 호출을 허용 하는 공용 끝점입니다. NSGs (네트워크 보안 그룹)를 사용 하는 경우 실행 기록의 입력 및 출력에 대 한 액세스를 허용 하는 인바운드 규칙으로 설정 되어 있는지 확인 합니다. 자세한 내용은 [ISE에 대 한 액세스 사용](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)을 참조 하세요.

> [!IMPORTANT]
> 액세스 끝점 옵션은 ISE를 만들 때만 사용할 수 있으며 나중에 변경할 수 없습니다.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>ISE와의 통합 계정

ISE(통합 서비스 환경) 내에서 논리 앱을 통해 통합 계정을 사용할 수 있습니다. 그러나 해당 통합 계정은 연결된 논리 앱과 *동일한 ISE*를 사용해야 합니다. ISE의 논리 앱은 같은 ISE에 있는 통합 계정만 참조할 수 있습니다. 통합 계정을 만들 때는 통합 계정의 위치로 ISE를 선택할 수 있습니다. ISE를 사용 하 여 통합 계정에 대 한 가격 책정 및 청구 작업 방법을 알아보려면 [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md#fixed-pricing)을 참조 하세요. 가격 책정 요금은 [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps에서 Azure virtual networks에 연결](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)에 대해 자세히 알아보기
* [Azure 서비스에 대한 가상 네트워크 통합](../virtual-network/virtual-network-for-azure-services.md)에 대해 알아보기
