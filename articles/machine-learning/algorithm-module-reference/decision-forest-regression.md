---
title: '의사 결정 포리스트 회귀: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning의 의사 결정 포리스트 회귀 모듈을 사용 하 여 의사 결정 트리의 앙상블을 기반으로 회귀 모델을 만드는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 6d326b3cb5a964e43ce77ee459533a4271d5ed73
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919943"
---
# <a name="decision-forest-regression-module"></a>의사 결정 포리스트 회귀 모듈

이 문서에서는 Azure Machine Learning designer (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 의사 결정 트리의 앙상블을 기반으로 하는 회귀 모델을 만듭니다.

모델을 구성한 후에는 레이블이 지정 된 데이터 집합 및 [모델 학습](./train-model.md) 모듈을 사용 하 여 모델을 학습 해야 합니다. 그러면 학습된 모델을 예측에 사용할 수 있습니다. 

## <a name="how-it-works"></a>작동 방법

의사 결정 트리는 리프 노드(의사 결정)에 도달할 때까지 이진 트리 데이터 구조를 트래버스하며 각 인스턴스에 대해 일련의 간단한 테스트를 수행하는 비파라메트릭 모델입니다.

의사 결정 트리를 사용하는 경우의 이점은 다음과 같습니다.

- 학습 및 예측 중에 계산과 메모리 사용량 측면에서 모두 효율적입니다.

- 비선형 의사 결정 경계를 표시할 수 있습니다.

- 통합 기능 선택 및 분류를 수행하며 불필요한 데이터가 많은 기능이 있는 경우 복원이 가능합니다.

이 회귀 모델은 의사 결정 트리의 앙상블로 구성되어 있습니다. 회귀 의사 결정 포리스트의 각 트리는 가우스 분포를 예측으로 출력 합니다. 모델의 모든 트리에 대해 결합 된 분포와 가장 가까운 가우스 분포를 찾기 위해 트리의 앙상블 대해 집계가 수행 됩니다.

이 알고리즘과 해당 구현에 대 한 이론적인 프레임 워크에 대 한 자세한 내용은 [의사 결정 포리스트: 분류, 회귀, 밀도 예측, 다 기관의 학습 및 반 감독 학습을 위한 통합 프레임 워크](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#) 문서를 참조 하세요.

## <a name="how-to-configure-decision-forest-regression-model"></a>의사 결정 포리스트 회귀 모델을 구성 하는 방법

1. **의사 결정 포리스트 회귀** 모듈을 파이프라인에 추가 합니다. 이 모듈은 디자이너의 **Machine Learning**, **모델 초기화**및 **재발**아래에서 찾을 수 있습니다.

2. 모듈 속성을 열고, **재샘플링 방법**의 경우 개별 트리를 만드는 데 사용 되는 메서드를 선택 합니다.  **모음 만들기** 에서 선택 하거나 **복제할**수 있습니다.

    - **모음 만들기**: 모음 만들기를 *부트스트랩 집계*라고도 합니다. 회귀 의사 결정 포리스트의 각 트리는 예측을 통해 가우스 분포를 출력 합니다. 집계는 개별 트리에서 반환 하는 모든 분포를 결합 하 여 지정 된 가우스 분포의 조합에 대해 처음 두 분이 일치 하는 가우스을 찾는 것입니다.

         자세한 내용은 [부트스트랩 집계](https://wikipedia.org/wiki/Bootstrap_aggregating)를 위한 위키백과 항목을 참조 하세요.

    - **복제: 복제**에서 각 트리는 정확히 동일한 입력 데이터에 대해 학습 됩니다. 각 트리 노드에 사용 되는 분할 조건자의 결정은 임의로 유지 되 고 트리는 다양 합니다.

         **복제** 옵션을 사용 하는 학습 프로세스에 대 한 자세한 내용은 [Computer Vision 및 의료 이미지 분석에 대 한 의사 결정 포리스트를 참조 하세요. Criminisi 및 J. Shotton입니다. Springer 2013.](https://research.microsoft.com/projects/decisionforests/)

3. **강사 모드 만들기** 옵션을 설정 하 여 모델을 학습 하는 방법을 지정 합니다.

    - **단일 매개 변수**

      모델을 어떻게 구성할지 아는 경우 특정 값 집합을 인수로 제공할 수 있습니다. 실험을 통해 이러한 값을 알았거나 지침으로 받았을 수 있습니다.

    - **매개 변수 범위**: 가장 적합 한 매개 변수를 잘 모르겠으면 매개 변수 스윕을 실행 하려는 경우이 옵션을 선택 합니다. 반복할 값의 범위를 선택 하 고 [모델 조정 하이퍼 매개 변수 변수](tune-model-hyperparameters.md) 는 제공 된 모든 설정의 가능한 조합에 대해 반복 하 여 최적의 결과를 생성 하는 하이퍼 매개 변수를 결정 합니다. 



4. **의사 결정 트리 수**의 경우 앙상블에서 만들 의사 결정 트리의 총 수를 지정 합니다. 추가 의사 결정 트리를 만들면 적용 범위가 확대될 수 있지만 학습 시간이 증가됩니다.

    > [!TIP]
    > 또한이 값은 학습 된 모델을 시각화할 때 표시 되는 트리 수를 제어 합니다. 단일 트리를 보거나 인쇄 하려면 값을 1로 설정 하면 됩니다. 그러나이는 한 개의 트리 (초기 매개 변수 집합이 있는 트리)만 생성 되며 추가 반복은 수행 되지 않음을 의미 합니다.

5. **의사 결정 트리의 최대 깊이**에 대해 의사 결정 트리의 최대 깊이를 제한 하는 숫자를 입력 합니다. 트리의 수준을 늘리면 정밀도는 높아질 수 있지만 학습 시간이 더 길어지고 과잉 맞춤이 발생할 수 있습니다.

6. **노드당 임의 분할 수**의 경우 트리의 각 노드를 빌드할 때 사용할 분할 수를 입력 합니다. *분할* 은 트리 (노드)의 각 수준에서 무작위로 분할 된 기능을 의미 합니다.

7. **리프 노드당 최소 샘플 수**는 트리에서 터미널 노드 (리프)를 만드는 데 필요한 최소 사례 수를 지정 합니다.

     이 값을 늘려 새 규칙을 작성하기 위한 임계값을 늘립니다. 예를 들어, 기본값이 1이면 단일 사례만으로도 새 규칙을 하나 작성할 수 있습니다. 값을 5로 늘리면 학습 데이터에 동일한 조건을 만족하는 사례가 다섯 개 이상 있어야 합니다.


9. 레이블이 지정 된 데이터 집합을 연결 하 고, 둘 이상의 결과가 포함 된 단일 레이블 열을 선택 하 고, [모델 학습](./train-model.md)에 연결 합니다.

    - **강사 모드 만들기** 옵션을 **단일 매개 변수로**설정한 경우 [모델 학습](./train-model.md) 모듈을 사용 하 여 모델을 학습 합니다.

   

10. 파이프라인을 실행합니다.

### <a name="results"></a>결과

학습 완료 후:

+ 학습 된 모델의 스냅숏을 저장 하려면 학습 모듈을 선택한 다음 오른쪽 패널에서 **출력** 탭으로 전환 합니다. 아이콘 **등록 모델**을 클릭 합니다.  모듈 트리에서 저장 된 모델을 모듈로 찾을 수 있습니다. 

## <a name="next-steps"></a>다음 단계

Azure Machine Learning [사용할 수 있는 모듈 집합](module-reference.md) 을 참조 하세요. 