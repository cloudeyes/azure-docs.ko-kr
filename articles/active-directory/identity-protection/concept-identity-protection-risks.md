---
title: 위험 이란? Azure AD ID 보호
description: Azure AD ID 보호 위험 설명
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 775ff6b3ba003bed22ccd5a42cb4da005c4dbb69
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79253690"
---
# <a name="what-is-risk"></a>위험 이란?

Azure AD ID 보호의 위험 검색에는 디렉터리의 사용자 계정과 관련 하 여 식별 된 의심 스러운 작업이 포함 됩니다.

Id 보호를 사용 하면 조직에서 강력한 리소스에 액세스 하 여 이러한 의심 스러운 작업을 신속 하 게 확인 하 고 대응할 수 있습니다. 

![위험한 사용자 및 로그인을 보여 주는 보안 개요](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

## <a name="risk-types-and-detection"></a>위험 유형 및 검색

두 가지 유형의 위험 **사용자** 와 **로그인** 및 두 가지 유형의 검색 또는 계산을 **실시간** 및 **오프 라인으로**설정 합니다.

### <a name="user-risk"></a>사용자 위험

사용자 위험은 지정 된 id 또는 계정이 손상 될 확률을 나타냅니다. 

이러한 위험은 보안 연구원, 법률 집행 전문가, Microsoft의 보안 팀 및 기타 신뢰할 수 있는 출처를 비롯 하 여 Microsoft의 내부 및 외부 위협 인텔리전스 소스를 사용 하 여 오프 라인으로 계산 됩니다.

| 위험 검색 | Description |
| --- | --- |
| 유출된 자격 증명 | 이 위험 검색 유형은 사용자의 유효한 자격 증명이 누수 되었음을 나타냅니다. 사이버 범죄자가 합법적인 사용자의 유효한 암호를 손상시키는 경우 범죄자는 종종 이러한 자격 증명을 공유합니다. 이 공유는 일반적으로 진한 웹에 공개적으로 게시 하거나, 사이트를 붙여넣거나, 블랙 market에서 자격 증명을 거래 및 판매 하 여 수행 됩니다. Microsoft 유출 자격 증명 서비스가 진한 웹에서 사용자 자격 증명을 획득 하거나, 사이트를 붙여넣거나, 다른 원본에서 사용자 자격 증명을 획득 하는 경우 Azure AD 사용자의 현재 유효한 자격 증명을 확인 하 여 유효한 일치 항목을 찾습니다. |
| Azure AD 위협 인텔리전스 | 이 위험 검색 유형은 지정 된 사용자에 게 비정상적인 사용자 활동을 나타내며 Microsoft의 내부 및 외부 위협 인텔리전스 소스를 기반으로 하는 알려진 공격 패턴과 일치 합니다. |

### <a name="sign-in-risk"></a>로그인 위험

로그인 위험은 지정 된 인증 요청이 id 소유자에 의해 권한이 부여 되지 않은 확률을 나타냅니다. 

이러한 위험은 Microsoft의 내부 및 외부 위협 인텔리전스 원본 (보안 연구원, 법률 집행 전문가, Microsoft의 보안 팀 및 기타 신뢰할 수 있는 원본 포함)을 사용 하 여 실시간으로 또는 계산 된 오프 라인으로 계산 될 수 있습니다.

| 위험 검색 | 검색 유형 | Description |
| --- | --- | --- |
| 익명 IP 주소 | 실시간 | 이 위험 검색 유형은 익명 IP 주소에서의 로그인을 나타냅니다 (예:가 나 브라우저가 나 익명 VPN). 악의적일 수 있는 의도로 IP 주소, 위치, 디바이스 등의 로그인 원격 분석 정보를 숨기려는 사용자가 대개 이러한 IP 주소를 사용합니다. |
| 비정상적 이동 | 오프라인 | 이 위험 검색 유형은 지리적으로 멀리 떨어진 위치에서 시작 되는 두 개의 로그인을 식별 합니다 .이는 과거의 동작을 고려 하 여 사용자에 대 한 위치 중 하나 이상이 불규칙 해질 수도 있습니다. 다른 요인 중에서 이 Machine Learning 알고리즘은 두 번의 로그인 간 시간과 사용자가 첫 번째 위치에서 두 번째 위치로 이동하는 데 걸리는 시간을 고려하여 서로 다른 사용자가 동일한 자격 증명을 사용하고 있음을 나타냅니다. <br><br> 이 알고리즘은 조직의 다른 사용자가 정기적으로 사용하는 VPN 및 위치와 같은 불가능한 이동 조건에 영향을 주는 확실한 "가양성"을 무시합니다. 시스템에는 새 사용자의 로그인 동작을 학습 하는 동안 14 일 또는 10 개의 로그인 중 가장 이른 초기 학습 기간이 있습니다. |
| 맬웨어 연결 IP 주소 | 오프라인 | 이 위험 검색 유형은 봇 서버와 적극적으로 통신 하는 것으로 알려진 맬웨어에 감염 된 IP 주소의 로그인을 나타냅니다. 이 검색은 봇 서버가 활성 상태인 동안 봇 서버와 연결 된 IP 주소에 대해 사용자 장치의 IP 주소를 상호 연결 하 여 결정 됩니다. |
| 일반적이지 않은 로그인 속성 | 실시간 | 이러한 위험 검색 유형은 비정상적인 로그인을 찾기 위해 과거의 로그인 기록 (IP, 위도/경도 및 ASN)을 고려 합니다. 시스템은 사용자가 사용한 이전 위치에 대 한 정보를 저장 하 고 이러한 "친숙 한" 위치를 고려 합니다. 로그인이 익숙한 위치 목록에 없는 위치에서 발생 하는 경우 위험 검색이 트리거됩니다. 새로 만든 사용자는 알고리즘이 사용자의 행동을 파악 하는 동안 익숙하지 않은 로그인 속성 위험 검색을 해제 하는 일정 기간 동안 "학습 모드"로 설정 됩니다. 학습 모드 기간은 동적 이며, 사용자의 로그인 패턴에 대 한 충분 한 정보를 수집 하는 데 얼마나 많은 시간이 사용 되는지에 따라 달라 집니다. 최소 기간은 5 일입니다. 오랫동안 사용 하지 않는 사용자는 학습 모드로 돌아갈 수 있습니다. 또한 시스템은 익숙한 디바이스 및 익숙한 위치에 지리적으로 가까운 위치에서 시도한 로그인을 무시합니다. <br><br> 기본 인증이나 레거시 프로토콜의 경우에도 이 검색이 실행됩니다. 이러한 프로토콜에는 클라이언트 ID와 같은 최신 속성이 없으므로 가양성을 줄일 수 있는 원격 분석 정보가 제한적으로만 제공됩니다. 따라서 고객은 최신 인증 방식으로 이전하는 것이 좋습니다. |
| 관리자가 사용자의 손상 확인 | 오프라인 | 이 검색은 관리자가 위험한 사용자 UI 또는 riskyUsers API를 사용 하 여 ' 사용자가 손상 확인 '을 선택 했음을 나타냅니다. 이 사용자가 확인 한 관리자를 확인 하려면 UI 또는 API를 통해 사용자의 위험 기록을 확인 합니다. |
| 악성 IP 주소 | 오프라인 | 이 검색은 악성 IP 주소에서의 로그인을 나타냅니다. Ip 주소 또는 다른 IP 평판에서 받은 잘못 된 자격 증명으로 인해 높은 실패율에 기반 하 여 IP 주소를 악의적으로 간주 합니다. |
| 의심 스러운 수신함 조작 규칙 | 오프라인 | 이 검색은 [MCAS (Microsoft Cloud App Security)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules)에 의해 검색 됩니다. 이 검색은 사용자의 받은 편지함에 메시지 또는 폴더를 삭제 하거나 이동 하는 의심 스러운 규칙이 설정 된 경우 사용자 환경을 프로 파일링 하 고 경고를 트리거합니다. 이는 사용자 계정이 손상 되 고, 메시지가 의도적으로 숨겨져 있으며, 조직에서 스팸 또는 맬웨어를 배포 하는 데 사서함이 사용 되 고 있음을 나타낼 수 있습니다. |
| 불가능 한 이동 | 오프라인 | 이 검색은 [MCAS (Microsoft Cloud App Security)](/cloud-app-security/anomaly-detection-policy#impossible-travel)에 의해 검색 됩니다. 이 검색은 사용자가 첫 번째 위치에서 두 번째 위치로 이동하는 데 걸리는 시간보다 짧은 기간 내에 지리적으로 먼 위치에서 시작되는 두 개의 사용자 활동(단일 또는 복수 세션)을 식별합니다. 이는 다른 사용자가 동일한 자격 증명을 사용하고 있음을 나타냅니다. |

### <a name="other-risk-detections"></a>기타 위험 감지

| 위험 검색 | 검색 유형 | Description |
| --- | --- | --- |
| 추가 위험이 검색 됨 | 실시간 또는 오프 라인 | 이 검색은 위의 프리미엄 검색 중 하나가 검색 되었음을 나타냅니다. 프리미엄 검색은 Azure AD Premium P2 고객만 볼 수 있으므로 Azure AD Premium P2 라이선스가 없는 고객에 대 한 "추가 위험이 검색 되었습니다." 라는 제목의 제목입니다. |

## <a name="next-steps"></a>다음 단계

- [위험을 완화하는 데 사용할 수 있는 정책](concept-identity-protection-policies.md)

- [보안 개요](concept-identity-protection-security-overview.md)
