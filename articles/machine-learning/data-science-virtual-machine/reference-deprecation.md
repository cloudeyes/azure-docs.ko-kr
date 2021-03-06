---
title: '참조: Data Science Virtual Machine 이미지 사용 중단'
titleSuffix: Azure Data Science Virtual Machine
description: Azure Data Science Virtual Machine에 영향을 주는 결함에 대 한 세부 정보
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 71ce8519dca6fa71340b1c34fe3a6891ccf5753f
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525790"
---
# <a name="reference-deprecation-of-dsvm-images"></a>참조: DSVM 이미지 사용 중단

아래에서는 Azure Data Science Virtual Machine에서 예정 된 결함를 처리 하기 위한 제안 사항을 설명 합니다.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: 데이터 디스크 마이그레이션

2019 년 12 월 31 일에 Windows 2012 DSVM 이미지를 지원 하지 않습니다. 기존 Windows 2012 DSVM에서 Windows 2016 DSVM으로 데이터 디스크를 마이그레이션하려면 다음 단계를 수행 합니다.

1. [여기](./provision-vm.md#create-your-dsvm)에 표시 된 지침에 따라 새 Windows 2016 dsvm을 만듭니다.
1. [이러한 지침](../../virtual-machines/windows/detach-disk.md)을 사용 하 여 Windows 2012 이미지에서 기존 데이터 디스크를 분리 합니다.
1. [이러한 지침](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)을 사용 하 여 이전 단계의 디스크를 Windows 2016 이미지에 연결 합니다.
