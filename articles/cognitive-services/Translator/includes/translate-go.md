---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 2ead85da805bb33247ca54bea51cccc57b0e4e94
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906694"
---
[!INCLUDE [Prerequisites](prerequisites-go.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>프로젝트 만들기 및 필요한 모듈 가져오기

선호하는 IDE 또는 편집기를 사용하여 새 Go 프로젝트를 만듭니다. 그런 다음, 아래 코드 조각을 `translate-text.go`라는 파일의 프로젝트에 복사합니다.

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
    "os"
)
```

## <a name="create-the-main-function"></a>main 함수 만들기

이 샘플에서는 `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` 및 `TRANSLATOR_TEXT_ENDPOINT` 환경 변수에서 Translator Text 구독 키와 엔드포인트를 읽으려고 합니다. 환경 변수에 익숙하지 않은 경우 `subscriptionKey` 및 `endpoint`를 문자열로 설정하고 조건문을 주석으로 처리할 수 있습니다.

이 코드를 프로젝트에 복사합니다.

```go
func main() {
    /*
    * Read your subscription key from an env variable.
    * Please note: You can replace this code block with
    * var subscriptionKey = "YOUR_SUBSCRIPTION_KEY" if you don't
    * want to use env variables. If so, be sure to delete the "os" import.
    */
    if "" == os.Getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY") {
      log.Fatal("Please set/export the environment variable TRANSLATOR_TEXT_SUBSCRIPTION_KEY.")
    }
    subscriptionKey := os.Getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY")
    if "" == os.Getenv("TRANSLATOR_TEXT_ENDPOINT") {
      log.Fatal("Please set/export the environment variable TRANSLATOR_TEXT_ENDPOINT.")
    }
    endpoint := os.Getenv("TRANSLATOR_TEXT_ENDPOINT")
    uri := endpoint + "/translate?api-version=3.0"
    /*
     * This calls our breakSentence function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    translate(subscriptionKey, uri)
}
```

## <a name="create-a-function-to-translate-text"></a>텍스트를 번역하는 함수 만들기

텍스트를 번역하는 함수를 만들겠습니다. 이 함수는 단일 인수인 Translator Text 구독 키를 사용합니다.

```go
func translate(subscriptionKey string, uri string) {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

그런 다음, URL을 생성하겠습니다. URL은 `Parse()` 및 `Query()` 메서드를 사용하여 빌드됩니다. 매개 변수는 `Add()` 메서드와 함께 추가됩니다. 이 샘플에서는 영어를 독일어 및 이탈리아어(`de` 및 `it`)로 번역합니다.

이 코드를 `translate` 함수에 복사합니다.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse(uri)
q := u.Query()
q.Add("to", "de")
q.Add("to", "it")
u.RawQuery = q.Encode()
```

>[!NOTE]
> 엔드포인트, 루트 및 요청 매개 변수에 대한 자세한 내용은 [Translator Text API 3.0: Translate](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate)를 참조하세요.

## <a name="create-a-struct-for-your-request-body"></a>요청 본문의 구조체 만들기

그런 다음, 요청 본문의 익명 구조체를 만들고 `json.Marshal()`을 사용하여 JSON으로 인코딩합니다. 이 코드를 `translate` 함수에 추가합니다.

```go
// Create an anonymous struct for your request body and encode it to JSON
body := []struct {
    Text string
}{
    {Text: "Hello, world!"},
}
b, _ := json.Marshal(body)
```

## <a name="build-the-request"></a>요청 빌드

이제 요청 본문을 JSON으로 인코딩했으므로 POST 요청을 빌드하고 Translator Text API를 호출할 수 있습니다.

```go
// Build the HTTP POST request
req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
if err != nil {
    log.Fatal(err)
}
// Add required headers to the request
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

// Call the Translator Text API
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

Cognitive Services 다중 서비스 구독을 사용하는 경우 요청 매개 변수에 `Ocp-Apim-Subscription-Region`도 포함해야 합니다. [다중 서비스 구독을 사용한 인증에 대해 자세히 알아봅니다](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="handle-and-print-the-response"></a>응답 처리 및 인쇄

이 코드를 `translate` 함수에 추가하여 JSON 응답을 디코딩한 다음, 결과에 형식을 지정하고 결과를 인쇄합니다.

```go
// Decode the JSON response
var result interface{}
if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
    log.Fatal(err)
}
// Format and print the response to terminal
prettyJSON, _ := json.MarshalIndent(result, "", "  ")
fmt.Printf("%s\n", prettyJSON)
```

## <a name="put-it-all-together"></a>모든 요소 결합

이것으로, Translator Text API를 호출하여 JSON 응답을 반환하는 간단한 프로그램이 만들어집니다. 이제 프로그램을 실행해 보겠습니다.

```console
go run translate-text.go
```

코드를 우리 것과 비교하고 싶다면 전체 샘플은 [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go)에 있습니다.

## <a name="sample-response"></a>샘플 응답

성공한 응답은 다음 예제와 같이 JSON으로 반환됩니다.

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "translations": [
      {
        "text": "Hallo Welt!",
        "to": "de"
      },
      {
        "text": "Salve, mondo!",
        "to": "it"
      }
    ]
  }
]
```

## <a name="next-steps"></a>다음 단계

Translator Text API로 할 수 있는 모든 것에 대해 알아보려면 API 참조를 살펴보세요.

> [!div class="nextstepaction"]
> [API 참조](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
