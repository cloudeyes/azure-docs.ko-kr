---
title: Azure Site Recovery를 사용 하 여 보조 사이트로의 VMware/물리적 재해 복구 지원
description: Azure Site Recovery를 사용한 VMware VM 및 물리적 서버와 보조 사이트 간의 재해 복구 지원이 요약되어 있습니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: cabd3f7693c6b6b86bf0324bdafdfe1377d1ece8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256797"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>VMware VM 또는 물리적 서버에서 보조 사이트로 재해 복구하기 위한 지원 매트릭스

이 문서에는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 VMware VM 또는 Windows/Linux 물리적 서버에서 보조 VMware 사이트로 재해 복구하는 경우 지원되는 사항이 요약되어 있습니다.

- VMware VM 또는 물리적 서버를 Azure에 복제하려면 [이 지원 매트릭스](vmware-physical-azure-support-matrix.md)를 검토하세요.
- Hyper-V VM을 보조 사이트에 복제하려면 [이 지원 매트릭스](hyper-v-azure-support-matrix.md)를 검토하세요.

> [!NOTE]
> 온-프레미스 VMware VM 및 물리적 서버 복제는 InMage Scout에서 제공합니다. InMage Scout는 Azure Site Recovery 서비스 구독에 포함되어 있습니다.

## <a name="end-of-support-announcement"></a>지원 종료 알림
온-프레미스 VMware 또는 물리적 데이터 센터 간의 복제에 대 한 Site Recovery 시나리오는 지원 종료에 도달 하 고 있습니다.

- 8 월 2018부터, Recovery Services 자격 증명 모음에서 시나리오를 구성할 수 없으며, 자격 증명 모음에서 InMage Scout software를 다운로드할 수 없습니다. 기존 배포는 계속 지원됩니다.
- - 2020년 12월 31일부터 시나리오가 지원되지 않습니다.
기존 파트너는 지원이 종료될 때까지 시나리오에 새 고객을 온보드할 수 있습니다.
- 2018년 및 2019년에 두 가지 업데이트가 릴리스될 예정입니다.

    - 업데이트 7: 네트워크 구성 및 규정 준수 문제를 수정하고 TLS 1.2 지원을 제공합니다.
    - 업데이트 8: Linux 운영 체제 RHEL/CentOS 7.3/7.4/7.5 및 SUSE 12에 대한 지원이 추가됩니다.
    - 업데이트 8 이후 추가적인 업데이트는 릴리스되지 않습니다. 업데이트 8에 추가된 운영 체제에 대한 핫픽스 지원 및 최상의 노력을 기반으로 한 버그 수정이 제한됩니다.

## <a name="host-servers"></a>호스트 서버

**운영 체제** | **세부 정보**
--- | ---
vCenter Server | vCenter 5.5, 6.0 및 6.5<br/><br/> 6\.0 또는 6.5를 실행하는 경우 5.5 기능만 지원됩니다.


## <a name="replicated-vm-support"></a>복제된 VM 지원

다음 표에서는 Site Recovery를 사용하여 복제된 컴퓨터에 대한 운영 체제 지원을 요약합니다. 워크로드를 지원되는 운영 체제에서 실행할 수 있습니다.

**운영 체제** | **세부 정보**
--- | ---
Windows Server | 64비트 Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1 이상.
Linux | Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2 <br/><br/> Red Hat 호환 커널 또는 UEK3(Unbreakable Enterprise Kernel Release 3)을 실행하는 Oracle Enterprise Linux 6.4, 6.5, 6.8 <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4 


## <a name="linux-machine-storage"></a>Linux 컴퓨터 스토리지

다음 스토리지를 포함한 Linux 컴퓨터만을 복제할 수 있습니다.

- 파일 시스템(EXT3, ETX4, ReiserFS, XFS)
- 다중 경로 소프트웨어 디바이스 매퍼
- 볼륨 관리자(LVM2)
- HP CCISS 컨트롤러 스토리지가 있는 물리적 서버는 지원되지 않습니다.
- ReiserFS 파일 시스템은 SUSE Linux Enterprise Server 11 SP3에서만 지원됩니다.

## <a name="network-configuration---hostguest-vm"></a>네트워크 구성 - 호스트/게스트 VM

**Configuration** | **지원됨**  
--- | --- 
호스트 - NIC 팀 | yes 
호스트 - VLAN | yes 
호스트 - IPv4 | yes 
호스트 - IPv6 | 예 
게스트 VM - NIC 팀 | 예
게스트 VM - IPv4 | yes
게스트 VM - IPv6 | 예
게스트 VM - Windows/Linux - 고정 IP 주소 | yes
게스트 VM - 다중 NIC | yes


## <a name="storage"></a>스토리지

### <a name="host-storage"></a>호스트 스토리지

**Storage(호스트)** | **지원됨** 
--- | --- 
NFS | yes 
SMB 3.0 | 해당 없음 
SAN(ISCSI) | yes 
다중 경로(MPIO) | yes 

### <a name="guest-or-physical-server-storage"></a>게스트 또는 물리적 서버 스토리지

**Configuration** | **지원됨** 
--- | --- 
VMDK | yes 
VHD/VHDX | 해당 없음 
2세대 VM | 해당 없음 
공유 클러스터 디스크 | yes 
암호화된 디스크 | 예 
UEFI| yes 
NFS | 예 
SMB 3.0 | 예 
RDM | yes 
디스크 > 1TB | yes 
스트라이프 디스크 포함 볼륨 > 1TB<br/><br/> LVM | yes 
스토리지 공간 | 예 
디스크 핫 추가/제거 | yes 
디스크 제외 | yes 
다중 경로(MPIO) | 해당 없음 

## <a name="vaults"></a>자격 증명 모음

**동작** | **지원됨** 
--- | --- 
리소스 그룹 간에 자격 증명 모음 이동(동일 구독 내 또는 구독 간에) | 예 
리소스 그룹 간에 스토리지, 네트워크, Azure VM 이동(동일 구독 내 또는 구독 간에) | 예 

## <a name="mobility-service-and-updates"></a>Mobility 서비스 및 업데이트

Mobility 서비스는 온-프레미스 VMware 서버 또는 물리적 서버와 보조 사이트 간 복제를 조정합니다. 복제를 설정할 때 Mobility 서비스 및 기타 구성 요소의 최신 버전이 필요합니다.

| **Update** | **세부 정보** |
| --- | --- |
|Scout 업데이트 | Scout 업데이트는 누적입니다. <br/><br/> 최신 Scout 업데이트에 대해 [알아보고 다운로드](vmware-physical-secondary-disaster-recovery.md#updates) |
|구성 요소 업데이트 | Scout 업데이트에는 RX 서버, 구성 서버, 프로세스 및 마스터 대상 서버, vContinuum 서버 및 보호하려면 원본 서버를 포함하여 모든 구성 요소에 대한 업데이트가 포함되어 있습니다.<br/><br/> [자세히 알아봅니다](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).|


## <a name="next-steps"></a>다음 단계

[InMage Scout 사용자 가이드](https://aka.ms/asr-scout-user-guide)를 다운로드합니다.

- [VMM 클라우드의 Hyper-V VM에서 보조 사이트로 복제](tutorial-vmm-to-vmm.md)
- [VMware VM 및 물리적 서버를 보조 사이트에 복제](tutorial-vmware-to-vmware.md)
