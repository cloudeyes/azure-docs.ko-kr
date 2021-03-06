---
title: Azure Lab Services PowerShell 모듈 | Microsoft Docs
description: 이 문서에서는 Azure Lab Services에서 아티팩트를 관리 하는 데 도움이 되는 PowerShell 모듈에 대 한 정보를 제공 합니다.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2019
ms.author: spelluru
ms.openlocfilehash: cc82355ee43f9fee4f9c2e1bb1bcc0481e4dcea3
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73609399"
---
# <a name="azlabservices-powershell-module-preview"></a>Az Services PowerShell module (preview)
Az. 연구원 서비스는 Azure Lab services의 관리를 간소화 하는 PowerShell 모듈입니다. 랩 계정, 랩, Vm 및 이미지를 생성, 쿼리, 업데이트 및 삭제 하는 구성 가능한 기능을 제공 합니다. 이 모듈에 대 한 자세한 내용은 [GitHub의 Az 서비스 홈 페이지](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)를 참조 하세요.

> [!NOTE]
> 이 모듈은 **미리 보기**상태입니다. 

## <a name="example-command"></a>예제 명령
PowerShell 명령을 사용 하 여 모든 랩에서 실행 중인 모든 Vm을 중지 하는 예는 다음과 같습니다.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>시작
1. 컴퓨터에 존재 하지 않는 경우 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 를 설치 합니다. 
2. [.Psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) 를 컴퓨터에 다운로드 합니다.
3. 모듈을 가져옵니다.

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. 다음 명령을 실행 하 여 구독의 모든 랩을 나열 합니다.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>다음 단계
[GitHub의 Az 서비스 홈 페이지](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)를 참조 하세요.
