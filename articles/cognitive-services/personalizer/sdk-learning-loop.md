---
title: '빠른 시작: SDK를 통한 학습 루프 만들기 및 사용 - Personalizer'
description: 이 빠른 시작에서는 클라이언트 SDK를 사용하여 기술 자료를 만들고 관리하는 방법을 보여줍니다.
ms.topic: quickstart
ms.date: 01/15/2020
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: 7ebe22227b4323b2e6b1c3fc9ca31e171d1d97cd
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77524872"
---
# <a name="quickstart-personalizer-client-library"></a>빠른 시작: Personalizer 클라이언트 라이브러리

이 빠른 시작에서는 Personalizer 서비스를 사용하여 맞춤형 콘텐츠를 표시합니다.

Personalizer 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다.

 * 순위 API - 콘텐츠 및 컨텍스트에 대해 제공하는 실시간 정보에 따라 콘텐츠 항목에서 가장 적합한 항목을 선택합니다.
 * 보상 API - 비즈니스 요구에 따라 보상 점수를 확인한 다음, 이 API를 사용하여 Personalizer로 보냅니다. 이 점수는 1은 양호, 0은 나쁨과 같은 단일 값일 수 있으며, 비즈니스 요구에 따라 생성하는 알고리즘일 수 있습니다.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# SDK](./includes/quickstart-sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js SDK](./includes/quickstart-sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python SDK](./includes/quickstart-sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[Personalizer의 작동 원리](how-personalizer-works.md)

* [Personalizer란?](what-is-personalizer.md)
* [Personalizer를 사용할 수 있는 위치](where-can-you-use-personalizer.md)
* [문제 해결](troubleshooting.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)에서 확인할 수 있습니다.
