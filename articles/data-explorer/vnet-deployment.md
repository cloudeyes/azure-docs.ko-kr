---
title: Virtual Network에 Azure 데이터 탐색기 배포
description: Virtual Network에 Azure 데이터 탐색기를 배포 하는 방법을 알아봅니다.
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 5a2731e26ba4f371177cf2ae649f0695f27e6304
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096761"
---
# <a name="deploy-azure-data-explorer-into-your-virtual-network"></a>Virtual Network에 Azure 데이터 탐색기 배포

이 문서에서는 Azure 데이터 탐색기 클러스터를 사용자 지정 Azure Virtual Network에 배포할 때 표시 되는 리소스에 대해 설명 합니다. 이 정보는 클러스터를 Virtual Network (VNet)의 서브넷에 배포 하는 데 도움이 됩니다. Azure Virtual Network에 대 한 자세한 내용은 [azure Virtual Network 란?](/azure/virtual-network/virtual-networks-overview) 을 참조 하세요.

   ![vnet 다이어그램](media/vnet-deployment/vnet-diagram.png)

Azure 데이터 탐색기는 VNet (Virtual Network)의 서브넷에 클러스터를 배포 하는 것을 지원 합니다. 이 기능을 통해 다음을 수행할 수 있습니다.

* Azure 데이터 탐색기 클러스터 트래픽에 nsg ( [네트워크 보안 그룹](/azure/virtual-network/security-overview) ) 규칙을 적용 합니다.
* 온-프레미스 네트워크를 Azure 데이터 탐색기 클러스터의 서브넷에 연결 합니다.
* [서비스 엔드포인트](/azure/virtual-network/virtual-network-service-endpoints-overview)를 사용 하 여 데이터 연결 원본 ([이벤트 허브](/azure/event-hubs/event-hubs-about) 및 [Event Grid](/azure/event-grid/overview))을 보호 합니다.

## <a name="access-your-azure-data-explorer-cluster-in-your-vnet"></a>VNet에서 Azure 데이터 탐색기 클러스터에 액세스

각 서비스에 대 한 다음 IP 주소 (엔진 및 데이터 관리 서비스)를 사용 하 여 Azure 데이터 탐색기 클러스터에 액세스할 수 있습니다.

* **개인 IP**: VNet 내의 클러스터에 액세스 하는 데 사용 됩니다.
* **공용 IP**: 관리 및 모니터링을 위해 VNet 외부에서 클러스터에 액세스 하는 데 사용 되며, 클러스터에서 시작 된 아웃 바운드 연결의 원본 주소로 사용 됩니다.

서비스에 액세스 하기 위해 생성 되는 DNS 레코드는 다음과 같습니다. 

* `[clustername].[geo-region].kusto.windows.net` (엔진) `ingest-[clustername].[geo-region].kusto.windows.net` (데이터 관리)는 각 서비스의 공용 IP에 매핑됩니다. 

* `private-[clustername].[geo-region].kusto.windows.net` (엔진) `private-ingest-[clustername].[geo-region].kusto.windows.net` (데이터 관리)는 각 서비스에 대 한 개인 IP에 매핑됩니다.

## <a name="plan-subnet-size-in-your-vnet"></a>VNet에서 서브넷 크기를 계획 합니다.

Azure 데이터 탐색기 클러스터를 호스트 하는 데 사용 되는 서브넷의 크기는 서브넷이 배포 된 후에 변경할 수 없습니다. VNet에서 Azure 데이터 탐색기는 각 VM에 대해 하나의 개인 IP 주소를 사용 하 고 내부 부하 분산 장치 (엔진 및 데이터 관리)에 대해 두 개의 개인 IP 주소를 사용 합니다. 또한 Azure 네트워킹은 각 서브넷에 대해 5 개의 IP 주소를 사용 합니다. Azure 데이터 탐색기는 데이터 관리 서비스용 두 Vm을 프로 비전 합니다. 엔진 서비스 Vm은 사용자 구성 확장 용량 마다 프로 비전 됩니다.

총 IP 주소 수:

| 사용 | 주소 수 |
| --- | --- |
| 엔진 서비스 | 인스턴스당 1 개 |
| 데이터 관리 서비스 | 2 |
| 내부 부하 분산 장치 | 2 |
| Azure 예약 주소 | 5 |
| **합계** | **#engine_instances + 9** |

> [!IMPORTANT]
> Azure 데이터 탐색기 배포 된 후에는 서브넷 크기를 변경할 수 없으므로 미리 계획 해야 합니다. 따라서 필요에 따라 서브넷 크기를 예약 합니다.

## <a name="service-endpoints-for-connecting-to-azure-data-explorer"></a>Azure 데이터 탐색기에 연결 하기 위한 서비스 끝점

[Azure 서비스 끝점](/azure/virtual-network/virtual-network-service-endpoints-overview) 을 사용 하면 azure 다중 테 넌 트 리소스를 가상 네트워크로 보호할 수 있습니다.
Azure 데이터 탐색기 클러스터를 서브넷에 배포 하면 Azure 데이터 탐색기 서브넷에 대 한 기본 리소스를 제한 하는 동시에 [이벤트 허브](/azure/event-hubs/event-hubs-about) 또는 [Event Grid](/azure/event-grid/overview) 를 사용 하 여 데이터 연결을 설정할 수 있습니다.

> [!NOTE]
> [저장소](/azure/storage/common/storage-introduction) 및 [이벤트 허브]와 함께 eventgrid 설치 프로그램을 사용 하는 경우 구독에 사용 되는 저장소 계정은 [방화벽 구성](/azure/storage/common/storage-network-security)에서 신뢰할 수 있는 azure platform 서비스를 허용 하는 동시에 azure 데이터 탐색기의 서브넷에 대 한 서비스 끝점을 사용 하 여 잠글 수 있지만 이벤트 허브는 신뢰할 수 있는 [azure 플랫폼 서비스](/azure/event-hubs/event-hubs-service-endpoints)를 지원 하지 않으므로 서비스 끝점을 사용 하도록 설정할 수

## <a name="dependencies-for-vnet-deployment"></a>VNet 배포에 대 한 종속성

### <a name="network-security-groups-configuration"></a>네트워크 보안 그룹 구성

[NSG (네트워크 보안 그룹)](/azure/virtual-network/security-overview) 는 VNet 내에서 네트워크 액세스를 제어 하는 기능을 제공 합니다. Azure 데이터 탐색기는 HTTPs (443) 및 TDS (1433) 라는 두 개의 끝점을 사용 하 여 액세스할 수 있습니다. 클러스터의 관리, 모니터링 및 적절 한 작업을 수행 하기 위해 이러한 끝점에 대 한 액세스를 허용 하도록 다음 NSG 규칙을 구성 해야 합니다.

#### <a name="inbound-nsg-configuration"></a>인바운드 NSG 구성

| **사용**   | **From**   | **수행할 작업**   | **프로토콜**   |
| --- | --- | --- | --- |
| 관리  |[Adx management addresses](#azure-data-explorer-management-ip-addresses)/AzureDataExplorerManagement (servicetag) | ADX 서브넷: 443  | TCP  |
| 상태 모니터링  | [ADX 상태 모니터링 주소](#health-monitoring-addresses)  | ADX 서브넷: 443  | TCP  |
| ADX 내부 통신  | ADX 서브넷: 모든 포트  | ADX 서브넷: 모든 포트  | 모두  |
| Azure 부하 분산 장치 인바운드 허용 (상태 프로브)  | AzureLoadBalancer  | ADX 서브넷: 80443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>아웃 바운드 NSG 구성

| **사용**   | **From**   | **수행할 작업**   | **프로토콜**   |
| --- | --- | --- | --- |
| Azure Storage에 대 한 종속성  | ADX 서브넷  | 저장소: 443  | TCP  |
| Azure Data Lake에 대 한 종속성  | ADX 서브넷  | AzureDataLake: 443  | TCP  |
| EventHub 수집 및 서비스 모니터링  | ADX 서브넷  | EventHub: 443, 5671  | TCP  |
| 메트릭 게시  | ADX 서브넷  | AzureMonitor: 443 | TCP  |
| Azure Monitor 구성 다운로드  | ADX 서브넷  | [Azure Monitor 구성 끝점 주소](#azure-monitor-configuration-endpoint-addresses): 443 | TCP  |
| Active Directory (해당 하는 경우) | ADX 서브넷 | AzureActiveDirectory: 443 | TCP |
| 인증 기관 | ADX 서브넷 | 인터넷: 80 | TCP |
| 내부 통신  | ADX 서브넷  | ADX 서브넷: 모든 포트  | 모두  |
| `sql\_request` 및 `http\_request` 플러그 인에 사용 되는 포트  | ADX 서브넷  | 인터넷: 사용자 지정  | TCP  |

### <a name="relevant-ip-addresses"></a>관련 IP 주소

#### <a name="azure-data-explorer-management-ip-addresses"></a>Azure 데이터 탐색기 관리 IP 주소

| 지역 | 주소 |
| --- | --- |
| 오스트레일리아 중부 | 20.37.26.134 |
| 오스트레일리아 Central2 | 20.39.99.177 |
| 오스트레일리아 동부 | 40.82.217.84 |
| 오스트레일리아 남동부 | 20.40.161.39 |
| BrazilSouth | 191.233.25.183 |
| 캐나다 중부 | 40.82.188.208 |
| 캐나다 동부 | 40.80.255.12 |
| 인도 중부 | 40.81.249.251 |
| 미국 중부 | 40.67.188.68 |
| 미국 중부 EUAP | 40.89.56.69 |
| 동아시아 | 20.189.74.103 |
| 미국 동부 | 52.224.146.56 |
| 미국 동부2 | 52.232.230.201 |
| 동부 미국 EUAP | 52.253.226.110 |
| 프랑스 중부 | 40.66.57.91 |
| 프랑스 남부 | 40.82.236.24 |
| 일본 동부 | 20.43.89.90 |
| 일본 서부 | 40.81.184.86 |
| 한국 중부 | 40.82.156.149 |
| 한국 남부 | 40.80.234.9 |
| 미국 중북부 | 40.81.45.254 |
| 북유럽 | 52.142.91.221 |
| 남아프리카 북부 | 102.133.129.138 |
| 남아프리카 서부 | 102.133.0.97 |
| 미국 중남부 | 20.45.3.60 |
| 동남아시아 | 40.119.203.252 |
| 인도 남부 | 40.81.72.110 |
| 영국 남부 | 40.81.154.254 |
| 영국 서부 | 40.81.122.39 |
| 미국 중서부 | 52.159.55.120 |
| 서유럽 | 51.145.176.215 |
| 인도 서부 | 40.81.88.112 |
| 미국 서부 | 13.64.38.225 |
| 미국 서부2 | 40.90.219.23 |

#### <a name="health-monitoring-addresses"></a>상태 모니터링 주소

| 지역 | 주소 |
| --- | --- |
| 오스트레일리아 중부 | 191.239.64.128 |
| 오스트레일리아 중부 2 | 191.239.64.128 |
| 오스트레일리아 동부 | 191.239.64.128 |
| 오스트레일리아 남동부 | 191.239.160.47 |
| 브라질 남부 | 23.98.145.105 |
| 캐나다 중부 | 168.61.212.201 |
| 캐나다 동부 | 168.61.212.201 |
| 인도 중부 | 23.99.5.162 |
| 미국 중부 | 168.61.212.201 |
| 미국 중부 EUAP | 168.61.212.201 |
| 동아시아 | 168.63.212.33 |
| 미국 동부 | 137.116.81.189 |
| 미국 동부 2 | 137.116.81.189 |
| 미국 동부 2 EUAP | 137.116.81.189 |
| 프랑스 중부 | 23.97.212.5 |
| 프랑스 남부 | 23.97.212.5 |
| 일본 동부 | 138.91.19.129 |
| 일본 서부 | 138.91.19.129 |
| 한국 중부 | 138.91.19.129 |
| 한국 남부 | 138.91.19.129 |
| 미국 중북부 | 23.96.212.108 |
| 북유럽 | 191.235.212.69 
| 남아프리카 공화국 북부 | 104.211.224.189 |
| 남아프리카 공화국 서 부 | 104.211.224.189 |
| 미국 중남부 | 23.98.145.105 |
| 인도 남부 | 23.99.5.162 |
| 동남아시아 | 168.63.173.234 |
| 영국 남부 | 23.97.212.5 |
| 영국 서부 | 23.97.212.5 |
| 미국 중서부 | 168.61.212.201 |
| 서유럽 | 23.97.212.5 |
| 인도 서부 | 23.99.5.162 |
| 미국 서부 | 23.99.5.162 |
| 미국 서부 2 | 23.99.5.162 |    

#### <a name="azure-monitor-configuration-endpoint-addresses"></a>Azure Monitor 구성 끝점 주소

| 지역 | 주소 |
| --- | --- |
| 오스트레일리아 중부 | 52.148.86.165 |
| 오스트레일리아 중부 2 | 52.148.86.165 |
| 오스트레일리아 동부 | 52.148.86.165 |
| 오스트레일리아 남동쪽 | 52.148.86.165 |
| 브라질 남부 | 13.68.89.19 |
| 캐나다 중부 | 13.90.43.231 |
| 캐나다 동부 | 13.90.43.231 |
| 인도 중부 | 13.71.25.187 |
| 미국 중부 | 52.173.95.68 |
| 미국 중부 EUAP | 13.90.43.231 |
| 동아시아 | 13.75.117.221 |
| 미국 동부 | 13.90.43.231 |
| 미국 동부 2 | 13.68.89.19 |    
| 미국 동부 2 EUAP | 13.68.89.19 |
| 프랑스 중부 | 52.174.4.112 |
| 프랑스 남부 | 52.174.4.112 |
| 일본 동부 | 13.75.117.221 |
| 일본 서 부 | 13.75.117.221 |
| 한국 중부 | 13.75.117.221 |
| 대한민국 남부 | 13.75.117.221 |
| 미국 중 북부 | 52.162.240.236 |
| 유럽 북부 | 52.169.237.246 |
| 남아프리카 공화국 북부 | 13.71.25.187 |
| 남아프리카 공화국 서 부 | 13.71.25.187 |
| 미국 중 남부 | 13.84.173.99 |
| 인도 남부 | 13.71.25.187 |
| 동남 아시아 | 52.148.86.165 |
| 영국 남부 | 52.174.4.112 |
| 영국 서부 | 52.169.237.246 |
| 미국 중서부 | 52.161.31.69 |
| 유럽 서부 | 52.174.4.112 |
| 인도 서 부 | 13.71.25.187 |
| 미국 서 부 | 40.78.70.148 |
| 미국 서 부 2 | 52.151.20.103 |

## <a name="expressroute-setup"></a>Express 경로 설정

Express 경로를 사용 하 여 온-프레미스 네트워크를 Azure Virtual Network에 연결 합니다. 일반적인 설정은 BGP (Border Gateway Protocol) 세션을 통해 기본 경로 (0.0.0.0/0)를 보급 하는 것입니다. 이렇게 하면 트래픽을 삭제 하 여 아웃 바운드 흐름이 중단 될 수 있는 고객의 온-프레미스 네트워크에 전달 될 Virtual Network에서 들어오는 트래픽이 발생 합니다. 이 기본값을 극복 하기 위해 [UDR (사용자 정의 경로](/azure/virtual-network/virtual-networks-udr-overview#user-defined) ) (0.0.0.0/0)을 구성할 수 있으며 다음 홉이 *인터넷*입니다. UDR이 BGP 보다 우선적으로 적용 되므로 트래픽은 인터넷으로 전송 됩니다.

## <a name="securing-outbound-traffic-with-firewall"></a>방화벽을 사용 하 여 아웃 바운드 트래픽 보안

[Azure 방화벽](/azure/firewall/overview) 또는 가상 어플라이언스를 사용 하 여 도메인 이름을 제한 하는 아웃 바운드 트래픽을 보호 하려면 다음 FQDN (정규화 된 도메인 이름)이 방화벽에서 허용 되어야 합니다.

```
prod.warmpath.msftcloudes.com:443
production.diagnostics.monitoring.core.windows.net:443
graph.windows.net:443
*.update.microsoft.com:443
shavamanifestcdnprod1.azureedge.net:443
login.live.com:443
wdcp.microsoft.com:443
login.microsoftonline.com:443
azureprofilerfrontdoor.cloudapp.net:443
*.core.windows.net:443
*.servicebus.windows.net:443
shoebox2.metrics.nsatc.net:443
prod-dsts.dsts.core.windows.net:443
ocsp.msocsp.com:80
*.windowsupdate.com:80
ocsp.digicert.com:80
go.microsoft.com:80
dmd.metaservices.microsoft.com:80
www.msftconnecttest.com:80
crl.microsoft.com:80
www.microsoft.com:80
adl.windows.com:80
crl3.digicert.com:80
```

또한 비대칭 경로 문제를 방지 하기 위해 다음 홉 *인터넷* 을 사용 하는 [관리 주소](#azure-data-explorer-management-ip-addresses) 및 [상태 모니터링 주소](#health-monitoring-addresses) 를 사용 하 여 서브넷에서 [경로 테이블](/azure/virtual-network/virtual-networks-udr-overview) 을 정의 해야 합니다.

예를 들어 **미국 서 부** 지역의 경우 다음 udrs를 정의 해야 합니다.

| 속성 | 주소 접두사 | 다음 홉 |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | 인터넷 |
| ADX_Monitoring | 23.99.5.162/32 | 인터넷 |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용 하 여 VNet에 Azure 데이터 탐색기 클러스터 배포

Azure 데이터 탐색기 클러스터를 가상 네트워크에 배포 하려면 VNet Azure Resource Manager 템플릿에 [azure 데이터 탐색기 클러스터 배포](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) 를 사용 합니다.

이 템플릿은 클러스터, 가상 네트워크, 서브넷, 네트워크 보안 그룹 및 공용 IP 주소를 만듭니다.

## <a name="troubleshooting"></a>문제 해결

이 섹션에서는 [Virtual Network](/azure/virtual-network/virtual-networks-overview)에 배포 되는 클러스터에 대 한 연결, 운영 및 클러스터 생성 문제를 해결 하는 방법에 대해 알아봅니다.

### <a name="access-issues"></a>액세스 문제

공용 (cluster.region.kusto.windows.net) 또는 개인 (private-cluster.region.kusto.windows.net) 끝점을 사용 하 여 클러스터에 액세스 하는 동안 문제가 발생 하 고 가상 네트워크 설치와 관련 된 것으로 의심 되는 경우 다음 단계를 수행 합니다. 문제를 해결 합니다.

#### <a name="check-tcp-connectivity"></a>TCP 연결 확인

첫 번째 단계는 Windows 또는 Linux OS를 사용 하 여 TCP 연결을 확인 하는 것입니다.

# <a name="windows"></a>[Windows](#tab/windows)

   1. 클러스터에 연결 하는 컴퓨터에 [Tcping](https://www.elifulkerson.com/projects/tcping.php) 을 다운로드 합니다.
   2. 다음 명령을 사용 하 여 원본 컴퓨터에서 대상에 대해 Ping을 수행 합니다.

    ```cmd
     C:\> tcping -t yourcluster.kusto.windows.net 443 
    
     ** Pinging continuously.  Press control-c to stop **
    
     Probing 1.2.3.4:443/tcp - Port is open - time=100.00ms
     ```

# <a name="linux"></a>[Linux](#tab/linux)

   1. 클러스터에 연결 하는 컴퓨터에 *netcat* 설치

    ```bash
    $ apt-get install netcat
     ```

   2. 다음 명령을 사용 하 여 원본 컴퓨터에서 대상에 대해 Ping을 수행 합니다.

     ```bash
     $ netcat -z -v yourcluster.kusto.windows.net 443
    
     Connection to yourcluster.kusto.windows.net 443 port [tcp/https] succeeded!
     ```
---

테스트에 실패 하면 다음 단계를 진행 합니다. 테스트에 성공 하면 TCP 연결 문제로 인해 문제가 발생 하지 않습니다. 문제 해결을 위해 [운영 문제로](#cluster-creation-and-operations-issues) 이동 합니다.

#### <a name="check-the-network-security-group-nsg"></a>NSG (네트워크 보안 그룹)를 확인 합니다.

   클러스터의 서브넷에 연결 된 nsg ( [네트워크 보안 그룹](/azure/virtual-network/security-overview) )에 클라이언트 컴퓨터의 IP에서 포트 443에 대 한 액세스를 허용 하는 인바운드 규칙이 있는지 확인 합니다.

#### <a name="check-route-table"></a>경로 테이블 확인

   클러스터의 서브넷에서 방화벽 (기본 경로 ' 0.0.0.0/0 '을 포함 하는 [경로 테이블](/azure/virtual-network/virtual-networks-udr-overview) 을 포함 하는 서브넷)에 강제 터널링을 설정 하는 경우 컴퓨터 IP 주소에 [다음 홉 유형이](/azure/virtual-network/virtual-networks-udr-overview) VirtualNetwork/Internet 인 경로가 있는지 확인 합니다. 이는 비대칭 경로 문제를 방지 하는 데 필요 합니다.

### <a name="ingestion-issues"></a>수집 문제

수집 문제가 발생 하 고 가상 네트워크 설치와 관련 된 것으로 의심 되는 경우 다음 단계를 수행 합니다.

#### <a name="check-ingestion-health"></a>수집 상태 확인

    Check that the [cluster ingestion metrics](/azure/data-explorer/using-metrics#ingestion-health-and-performance-metrics) indicate a healthy state.

#### <a name="check-security-rules-on-data-source-resources"></a>데이터 원본 리소스에 대 한 보안 규칙 확인

메트릭이 데이터 원본에서 처리 된*이벤트 (이벤트* /IoT 허브의 경우 이벤트)를 표시 하지 않는 경우 데이터 원본 리소스 (이벤트 허브 또는 저장소)가 방화벽 규칙이 나 서비스 끝점의 클러스터 서브넷에서 액세스를 허용 하는지 확인 합니다.

#### <a name="check-security-rules-configured-on-clusters-subnet"></a>클러스터의 서브넷에 구성 된 보안 규칙 확인

클러스터의 서브넷에 NSG, UDR 및 방화벽 규칙이 적절히 구성 되어 있는지 확인 합니다. 또한 모든 종속 끝점에 대 한 네트워크 연결을 테스트 합니다. 

### <a name="cluster-creation-and-operations-issues"></a>클러스터 만들기 및 작업 문제

클러스터 만들기 또는 작업 문제가 발생 하 고 가상 네트워크 설치와 관련 된 것으로 의심 되는 경우 다음 단계에 따라 문제를 해결 하십시오.

#### <a name="diagnose-the-virtual-network-with-the-rest-api"></a>REST API를 사용 하 여 가상 네트워크 진단

[ARMClient](https://chocolatey.org/packages/ARMClient) 는 PowerShell을 사용 하 여 REST API를 호출 하는 데 사용 됩니다. 

1. ARMClient에 로그인

   ```powerShell
   armclient login
   ```

1. 진단 작업 호출

    ```powershell
    $subscriptionId = '<subscription id>'
    $clusterName = '<name of cluster>'
    $resourceGroupName = '<resource group name>'
    $apiversion = '2019-11-09'
    
    armclient post "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Kusto/clusters/$clusterName/diagnoseVirtualNetwork?api-version=$apiversion" -verbose
    ```

1. 응답 확인

    ```powershell
    HTTP/1.1 202 Accepted
    ...
    Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    ...
    ```

1. 작업 완료 대기

    ```powershell
    armclient get https://management.azure.com/subscriptions/$subscriptionId/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "[Running/Failed/Completed]",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {...}
    }
    ```
    
   *Status* 속성이 *완료*로 표시 될 때까지 기다렸다가 *속성* 필드에 다음이 표시 되어야 합니다.

    ```powershell
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "Completed",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {
        "Findings": [...]
      }
    }
    ```

검색 결과 *속성이 빈* 결과를 표시 하는 경우 모든 네트워크 테스트가 통과 되 고 연결이 끊어지지 않았음을 의미 합니다. 다음과 같이 오류가 표시 되는 경우: *아웃 바운드 종속성 ' {dependencyName}: {port} '이 충족 되지 않을 수 있습니다 (아웃 바운드)* . 클러스터가 종속 서비스 끝점에 연결할 수 없습니다. 문제를 해결 하려면 다음 단계를 진행 합니다.

#### <a name="check-network-security-group-nsg"></a>NSG (네트워크 보안 그룹) 확인

[VNet 배포에 대 한 종속성](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment) 의 지침에 따라 [네트워크 보안 그룹이](/azure/virtual-network/security-overview) 제대로 구성 되어 있는지 확인 합니다.

#### <a name="check-route-table"></a>경로 테이블 확인

클러스터의 서브넷에서 강제 터널링이 방화벽 (기본 경로 ' 0.0.0.0/0 '을 포함 하는 [경로 테이블](/azure/virtual-network/virtual-networks-udr-overview) 을 포함 하는 서브넷)으로 설정 된 경우 [관리 ip 주소](#azure-data-explorer-management-ip-addresses) 및 [상태 모니터링 ip 주소](#health-monitoring-addresses) 에 [다음 홉 유형](/azure/virtual-network/virtual-networks-udr-overview##next-hop-types-across-azure-tools) *인터넷*을 포함 하는 경로와 ' *관리 ip/32 '* 및 *' 상태-모니터링-i d '* 에 대 한 [원본 주소 접두사가](/azure/virtual-network/virtual-networks-udr-overview#how-azure-selects-a-route) 있는지 확인 합니다. 이는 비대칭 경로 문제를 방지 하는 데 필요 합니다.

#### <a name="check-firewall-rules"></a>방화벽 규칙 확인

방화벽에 대 한 터널 서브넷 아웃 바운드 트래픽을 강제 적용 하는 경우 방화벽을 [사용 하 여 아웃 바운드 트래픽 보안](/azure/data-explorer/vnet-deployment#securing-outbound-traffic-with-firewall)에 설명 된 대로 방화벽 구성에서 모든 종속성 FQDN (예: *blob.core.windows.net*)이 허용 되는지 확인 합니다.
