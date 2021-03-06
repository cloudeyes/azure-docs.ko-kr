---
title: Azure Migrate를 사용 하 여 온-프레미스 서버에서 앱, 역할 및 기능 검색
description: Azure Migrate Server 평가를 사용 하 여 온-프레미스 서버에서 앱, 역할 및 기능을 검색 하는 방법을 알아봅니다.
ms.topic: article
ms.date: 11/20/2019
ms.openlocfilehash: 7c3a6360e051b8cb694393695e75578ce2c135ec
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846323"
---
# <a name="discover-machine-apps-roles-and-features"></a>컴퓨터 앱, 역할 및 기능 검색

이 문서에서는 Azure Migrate: 서버 평가를 사용 하 여 온-프레미스 서버에서 응용 프로그램, 역할 및 기능을 검색 하는 방법을 설명 합니다.

앱 인벤토리를 검색 하 고 온-프레미스 컴퓨터에서 실행 되는 역할/기능을 사용 하면 워크 로드에 맞게 조정 된 Azure에 대 한 마이그레이션 경로를 파악 하 고 계획할 수 있습니다.

> [!NOTE]
> 앱 검색은 현재 VMware Vm에 대해서만 지원 되며 검색 으로만 제한 됩니다. 앱 기반 평가는 아직 제공 되지 않습니다. 온-프레미스 VMware Vm, Hyper-v Vm 및 물리적 서버에 대 한 컴퓨터 기반 평가

Azure Migrate를 사용 하는 앱 검색: 서버 평가는 에이전트 없이 수행 됩니다. 컴퓨터 및 Vm에 아무 것도 설치할 필요가 없습니다. 서버 평가는 Azure Migrate 어플라이언스를 사용 하 여 컴퓨터 게스트 자격 증명과 함께 검색을 수행 합니다. 어플라이언스는 VMware Api를 사용 하 여 VMware 컴퓨터에 원격으로 액세스 합니다.


## <a name="before-you-start"></a>시작하기 전에

1. Azure Migrate 프로젝트를 [만들었는지](how-to-add-tool-first-time.md) 확인 합니다.
2. Azure Migrate: 서버 평가 도구를 프로젝트에 [추가](how-to-assess.md) 했는지 확인 합니다.
4. Azure Migrate 어플라이언스를 사용 하 여 VMware Vm을 검색 하 고 평가 하기 위한 [vmware 요구 사항을](migrate-support-matrix-vmware.md#vmware-requirements) 확인 하세요.
5. Azure Migrate 어플라이언스를 배포 하기 위한 [요구 사항을](migrate-appliance.md) 확인 합니다.
6. 응용 프로그램 검색에 대 한 [지원 및 요구 사항을 확인](migrate-support-matrix-vmware.md#application-discovery) 합니다.

## <a name="prepare-for-app-discovery"></a>앱 검색 준비

1. [어플라이언스 배포를 준비](tutorial-prepare-vmware.md)합니다. 준비에는 어플라이언스 설정을 확인 하 고 어플라이언스에서 vCenter Server 액세스 하는 데 사용할 계정을 설정 하는 작업이 포함 됩니다.
2. 앱, 역할 및 기능을 검색할 컴퓨터에 대 한 관리자 권한이 있는 사용자 계정 (Windows 및 Linux 서버에 대해 각각 하나씩)이 있는지 확인 합니다.
3. [Azure Migrate 어플라이언스를 배포](how-to-set-up-appliance-vmware.md) 하 여 검색을 시작 합니다. 어플라이언스를 배포 하려면 OVA 템플릿을 다운로드 하 고 VMware로 가져와 vmware VM으로 어플라이언스를 만듭니다. 어플라이언스를 구성한 다음 Azure Migrate에 등록 합니다.
2. 어플라이언스를 배포할 때 연속 검색을 시작 하려면 다음을 지정 합니다.
    - 연결 하려는 vCenter Server의 이름입니다.
    - VCenter Server에 연결 하기 위해 어플라이언스에 대해 만든 자격 증명입니다.
    - Windows/Linux Vm에 연결 하기 위해 어플라이언스에 대해 만든 계정 자격 증명입니다.

어플라이언스를 배포 하 고 자격 증명을 제공한 후 어플라이언스는 앱, 기능 및 역할의 검색과 함께 VM 메타 데이터 및 성능 데이터의 연속 검색을 시작 합니다.  앱 검색 기간은 보유 하 고 있는 Vm 수에 따라 달라 집니다. 500 Vm의 앱 검색은 일반적으로 1 시간 정도 걸립니다.

## <a name="review-and-export-the-inventory"></a>인벤토리 검토 및 내보내기

검색이 완료 되 면 앱 검색용 자격 증명을 제공한 경우 Azure Portal에서 앱 인벤토리를 검토 하 고 내보낼 수 있습니다.

1. **Azure Migrate 서버** > **Azure Migrate: 서버 평가**에서 표시 된 수를 클릭 하 여 검색 된 **서버** 페이지를 엽니다.

    > [!NOTE]
    > 또한이 단계에서는 검색 된 컴퓨터에 대 한 종속성 매핑을 선택적으로 설정 하 여 평가 하려는 컴퓨터에서 종속성을 시각화할 수 있습니다. [자세히 알아보기](how-to-create-group-machine-dependencies.md).

2. **검색 된 응용 프로그램**에서 표시 된 수를 클릭 합니다.
3. **응용 프로그램 인벤토리에서**검색 된 앱, 역할 및 기능을 검토할 수 있습니다.
4. 인벤토리를 내보내려면 **검색 된 서버**에서 **앱 인벤토리 내보내기**를 클릭 합니다.

앱 인벤토리는 Excel 형식으로 내보내고 다운로드 됩니다. **응용 프로그램 인벤토리** 시트에는 모든 컴퓨터에서 검색 된 모든 앱이 표시 됩니다.

## <a name="next-steps"></a>다음 단계

- 검색 된 서버의 리프트 및 시프트 마이그레이션에 대 한 [평가를 만듭니다](how-to-create-assessment.md) .
- [Azure Migrate: 데이터베이스 평가](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017)를 사용 하 여 SQL Server 데이터베이스를 평가 합니다.
