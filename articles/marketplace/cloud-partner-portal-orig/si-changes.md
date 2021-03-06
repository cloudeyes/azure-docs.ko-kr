---
title: 판매자 통찰력 릴리스 정보
description: Seller Insights 기능 변경 내용에 대한 정보를 제공합니다.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 3/3/2019
ms.author: pabutler
ms.openlocfilehash: 4f7e461241793b27d838c04311509f43932df291
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808952"
---
# <a name="seller-insights-release-notes"></a>Seller Insights 릴리스 정보 

(릴리스 날짜: 3 월 1 일, 2019)

이 문서에서는 [클라우드 파트너 포털](https://cloudpartner.azure.com/#insights)의 Seller Insights 기능 변경 내용에 대한 정보를 제공합니다.

## <a name="release-highlights-for-march-1-2019"></a>2019 년 3 월 1 일부 터 릴리스 하이라이트

* 요약에 추가 된 *고객 추세*
* 요약의 *상위 5 개 고객* 은 고객이 보유 한 모든 Azure 구독을 반영 합니다.
* *한 눈에 월간 주문에서 이동 하는* 요약의 *활성 주문 추세 & 표준화 된 사용 추세*
* *지급 조정 보고서* 가 업데이트 됨
* 지급에서 *상위 5 명의 고객* 은 고객이 보유 한 모든 Azure 구독을 반영 합니다.
* 고객 ID로 업데이트 된 *사용 현황 보고서*
* *고객 재직 기간* 주문 & 사용량은 고객이 보유 한 모든 Azure 구독을 반영 합니다.


(릴리스 날짜: 2018년 7월 28일)

## <a name="release-highlights-for-july-28-2018"></a>2018 년 7 월 28 일에 대 한 릴리스 하이라이트


-   *예상 가격*에서 환율이 적용된 고객 요금을 확인할 수 있습니다.
-   *예상 지급액*에서는 지급 가능액을 미리 확인할 수 있습니다.
-  *사용량 기준 식별자*는 고객 사용량과 주문 및 지급액의 정확한 데이터를 제공합니다.
-   *일 단위 사용량*에서는 고객 사용량을 더 세분화하여 자세히 확인할 수 있습니다.


### <a name="changes-to-data-structure-and-taxonomy"></a>데이터 구조 및 분류 변경 내용

아래 표에는 이번 릴리스에서 추가되거나 크게 변경된 메트릭이 나와 있습니다. 

| **새 용어**                   |    **정의**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| 가격(CC)                     | 지정된 SKU의 사용 단위 가격(고객 통화)입니다.       |
| 예상 요금 소계(CC) | 지정 된 SKU (고객의 통화)의 사용 단위 수량에 대 한 예상 연장 요금입니다. 반올림 또는 버림 오류로 인해 이 값은 정확하지 않을 수도 있습니다.   |
| PC(게시자 통화)        | 게시자가 지급용으로 선택한 기본 설정 통화입니다.                               |
| 예상 가격(PC)           | 날짜 사용에 대 한 외부 exchange 변환을 기반으로 지정 된 SKU에 대 한 예상 가격은 계산 됩니다 (게시자의 통화). 반올림 또는 버림 오류로 인해 이 값은 정확하지 않을 수도 있습니다.   |
| 예상 연장 요금(PC) | 날짜 사용에 대 한 외부 exchange 변환을 기반으로 지정 된 SKU에 대 한 예상 연장 요금은 게시자의 통화에서 계산 됩니다. 반올림 또는 버림 오류로 인해 이 값은 정확하지 않을 수도 있습니다. |
| 예상 지급액(PC)          | 사용량이 계산 된 날짜에 대 한 외부 교환 변환 (게시자의 통화)에 따라 지정 된 SKU에 대 한 사용 단위 수량에 대 한 예상 지불입니다. 반올림 또는 버림 오류로 인해 이 값은 정확하지 않을 수도 있습니다.   |
| 사용량 기준                | 지급 보고서의 항목과 연결된 특정 SKU를 고객이 사용한 기간(1일 이상)의 식별자입니다. |
|  |  |
