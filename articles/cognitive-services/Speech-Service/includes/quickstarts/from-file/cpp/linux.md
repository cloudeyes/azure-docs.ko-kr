---
title: '빠른 시작: 오디오 파일에서 음성 인식, C++(Linux) - Speech Service'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: erhopf
ms.openlocfilehash: dcd34170668230b1e1a3224844d8779b8f51d1db
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77446443"
---
## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음을 확인해야 합니다.

> [!div class="checklist"]
> * [Azure Speech 리소스 만들기](../../../../get-started.md)
> * [개발 환경 설정](../../../../quickstarts/setup-platform.md?tabs=linux)
> * [빈 샘플 프로젝트 만들기](../../../../quickstarts/create-project.md?tabs=linux)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>샘플 코드 추가

1. `helloworld.cpp`라는 C++ 원본 파일을 만들고 다음 코드를 파일에 붙여넣습니다.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-file/helloworld/helloworld.cpp#code)]

1. 이 새 파일에서 `YourSubscriptionKey` 문자열을 음성 서비스 구독 키로 바꿉니다.

1. 문자열 `YourServiceRegion`을 구독과 연결된 [지역](https://aka.ms/speech/sdkregion)의 **영역 식별자**로 바꿉니다(예: 평가판 구독의 경우 `westus`).

1. `whatstheweatherlike.wav` 문자열을 고유한 파일 이름으로 바꿉니다.

> [!NOTE]
> Speech SDK는 기본적으로 언어에 en-us를 사용하여 인식합니다. 원본 언어 선택에 대한 자세한 내용은 [음성 텍스트 변환에 대한 원본 언어 지정](../../../../how-to-specify-source-language.md)을 참조하세요.

## <a name="build-the-app"></a>앱 빌드

> [!NOTE]
> 아래 명령을 _단일 명령줄_로 입력합니다. 이 작업을 수행하는 가장 쉬운 방법은 각 명령 옆에 있는 **복사** 단추를 누른 다음, 셸 프롬프트에 붙여넣는 것입니다.

* **x64**(64비트) 시스템에서 다음 명령을 실행하여 애플리케이션을 빌드합니다.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libasound.so.2
  ```

* **x86**(32비트) 시스템에서 다음 명령을 실행하여 애플리케이션을 빌드합니다.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libasound.so.2
  ```

* **ARM64**(64비트) 시스템에서 다음 명령을 실행하여 애플리케이션을 빌드합니다.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/arm64" -l:libasound.so.2
  ```

## <a name="run-the-app"></a>앱 실행

1. Speech SDK 라이브러리를 가리키도록 로더의 라이브러리 경로를 구성합니다.

   * **x64**(64비트) 시스템에서 다음 명령을 입력합니다.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * **x86**(32비트) 시스템에서 다음 명령을 입력합니다.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

   * **ARM64**(64비트) 시스템에서 다음 명령을 입력합니다.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/arm64"
     ```

1. 애플리케이션을 실행합니다.

   ```sh
   ./helloworld
   ```

1. 오디오 파일이 Speech Service로 전송되고 파일의 첫 번째 발화가 텍스트로 변환되어 동일한 창에 표시됩니다.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [footer](./footer.md)]
