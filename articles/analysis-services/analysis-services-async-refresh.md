---
title: Azure Analysis Services 모델에 대한 비동기 새로 고침 | Microsoft Docs
description: Azure Analysis Services REST API를 사용 하 여 모델 데이터의 비동기 새로 고침을 코딩 하는 방법을 설명 합니다.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6457f062a40e60a491220fcf977585e8b07445b2
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273714"
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>REST API를 사용한 비동기 새로 고침

REST 호출을 지원하는 프로그래밍 언어를 사용하여 Azure Analysis Services 테이블 형식 모델에서 비동기 데이터 새로 고침 작업을 수행할 수 있습니다. 여기에는 쿼리 스케일 아웃을 위한 읽기 전용 복제본의 동기화가 포함됩니다. 

데이터 새로 고침 작업은 데이터 볼륨, 파티션 사용 최적화 수준 등을 비롯 한 다양 한 요인에 따라 다소 시간이 걸릴 수 있습니다. 이러한 작업은 일반적으로 [TOM](https://docs.microsoft.com/analysis-services/tom/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (테이블 형식 개체 모델), [PowerShell](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference) cmdlet 또는 [Tmsl](https://docs.microsoft.com/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) (테이블 형식 모델 스크립팅 언어)을 사용 하는 등의 기존 메서드를 사용 하 여 호출 되었습니다. 그러나 이러한 메서드는 종종 신뢰할 수 없는, 장기 실행 HTTP 연결을 요구할 수 있습니다.

Azure Analysis Services용 REST API에서는 데이터 새로 고침 작업을 비동기적으로 실행할 수 있습니다. REST API를 사용하면 클라이언트 애플리케이션에서의 장기 실행 HTTP 연결이 필요하지 않습니다. 안정성을 위한 기타 기본 제공 기능(예: 자동 다시 시도 및 일괄 처리 커밋)도 있습니다.

## <a name="base-url"></a>기준 URL

기준 URL은 다음 형식을 따릅니다.

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

예를 들어 미국 서 부 Azure 지역에 있는 `myserver`서버에서 AdventureWorks 라는 모델을 생각해 보겠습니다. 서버 이름은 다음과 같습니다.

```
asazure://westus.asazure.windows.net/myserver 
```

이 서버 이름의 기준 URL은 다음과 같습니다.

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

기준 URL을 사용하면, 다음 매개 변수에 따라 리소스 및 작업을 추가할 수 있습니다. 

![비동기 새로 고침](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- **s**로 끝나는 모든 항목은 컬렉션입니다.
- **()** 로 끝나는 모든 항목은 함수입니다.
- 다른 모든 항목은 리소스/개체입니다.

예를 들어 새로 고침 컬렉션에서 POST 동사를 사용하여 새로 고침 작업을 수행할 수 있습니다.

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>인증

모든 호출은 권한 부여 헤더에서 유효한 Azure Active Directory(OAuth 2) 토큰으로 인증되어야 하며 다음과 같은 요구 사항을 충족해야 합니다.

- 토큰은 사용자 토큰 또는 애플리케이션 서비스 사용자여야 합니다.
- 토큰에는 올바른 대상이 `https://*.asazure.windows.net`으로 설정되어 있어야 합니다.
- 사용자 또는 애플리케이션은 서버 또는 모델에서 요청된 호출을 수행하기 위한 충분한 권한이 있어야 합니다. 사용 권한 수준은 서버의 모델 또는 관리 그룹 내 역할에 의해 결정됩니다.

    > [!IMPORTANT]
    > 현재 **서버 관리자** 역할 권한이 필요합니다.

## <a name="post-refreshes"></a>POST /refreshes

새로 고침 작업을 수행하려면 /refreshes 컬렉션에서 POST 동사를 사용하여 컬렉션에 새로 고침 항목을 새로 추가합니다. 응답의 Location 헤더에는 새로 고침 ID가 포함됩니다. 클라이언트 애플리케이션은 비동기적이므로, 연결을 끊은 후 나중에 필요할 때 상태를 확인할 수 있습니다.

모델에 대해 한 번에 하나의 새로 고침 작업만 허용됩니다. 현재 실행 중인 새로 고침 작업이 있으며 다른 작업이 제출되면 409 충돌 HTTP 상태 코드가 반환됩니다.

본문은 다음과 같을 수 있습니다.

```
{
    "Type": "Full",
    "CommitMode": "transactional",
    "MaxParallelism": 2,
    "RetryCount": 2,
    "Objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer"
        },
        {
            "table": "DimDate"
        }
    ]
}
```

### <a name="parameters"></a>매개 변수

매개 변수를 지정할 필요는 없습니다. 기본값이 적용됩니다.

| 속성             | Type  | Description  |기본값  |
|------------------|-------|--------------|---------|
| `Type`           | 열거형  | 수행할 처리 형식입니다. 이 형식은 TMSL [새로 고침 명령](https://docs.microsoft.com/analysis-services/tmsl/refresh-command-tmsl) 형식인 full, clearValues, calculate, dataOnly, automatic 및 defragment에 맞춰 정렬됩니다. Add 형식은 지원되지 않습니다.      |   automatic      |
| `CommitMode`     | 열거형  | 개체가 일괄로 커밋될지 또는 완료될 때만 커밋될지를 결정합니다. 모드에는 default, transactional, partialBatch가 포함됩니다.  |  transactional       |
| `MaxParallelism` | Int   | 이 값은 처리 명령을 동시에 실행할 최대 스레드 수를 결정합니다. 이 값은 TMSL [시퀀스 명령](https://docs.microsoft.com/analysis-services/tmsl/sequence-command-tmsl)에 설정될 수 있는 MaxParallelism 속성에 맞춰 정렬되거나 다른 메서드를 사용하여 정렬됩니다.       | 10        |
| `RetryCount`     | Int   | 작업이 실패하기 전에 다시 시도하는 횟수를 나타냅니다.      |     0    |
| `Objects`        | Array | 처리해야 하는 개체의 배열입니다. 각 개체에 전체 테이블을 처리할 때는 "table"이, 파티션을 처리할 때는 "partition"이 포함됩니다. 개체를 지정하지 않으면 전체 모델이 새로 고쳐집니다. |   전체 모델 처리      |

CommitMode는 partialBatch와 같습니다. 시간까지 걸릴 수 있는 큰 데이터 세트의 초기 로드를 수행하는 경우에 사용됩니다. 하나 이상의 일괄 처리를 성공적으로 커밋한 후 새로 고침 작업이 실패하면, 성공적으로 커밋된 일괄 처리는 커밋된 상태로 유지됩니다(성공적으로 커밋된 일괄 처리는 롤백되지 않음).

> [!NOTE]
> 작성 당시의 일괄 처리 크기는 MaxParallelism 값이지만, 이 값은 변경될 수 있습니다.

### <a name="status-values"></a>상태 값

|상태 값  |Description  |
|---------|---------|
|`notStarted`    |   작업이 아직 시작 되지 않았습니다.      |
|`inProgress`     |   작업이 진행 중입니다.      |
|`timedOut`     |    사용자가 지정한 시간 제한에 따라 작업 시간이 초과 되었습니다.     |
|`cancelled`     |   사용자 또는 시스템에 의해 작업이 취소 되었습니다.      |
|`failed`     |   작업이 실패했습니다.      |
|`succeeded`      |   작업이 성공했습니다.      |

## <a name="get-refreshesrefreshid"></a>GET /refreshes/\<refreshId>

새로 고침 작업의 상태를 확인하려면 새로 고침 ID에 GET 동사를 사용합니다. 응답 본문의 예는 다음과 같습니다. 작업이 진행 중인 경우 상태에서 `inProgress` 반환 됩니다.

```
{
    "startTime": "2017-12-07T02:06:57.1838734Z",
    "endTime": "2017-12-07T02:07:00.4929675Z",
    "type": "full",
    "status": "succeeded",
    "currentRefreshType": "full",
    "objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer",
            "status": "succeeded"
        },
        {
            "table": "DimDate",
            "partition": "DimDate",
            "status": "succeeded"
        }
    ]
}
```

## <a name="get-refreshes"></a>GET /refreshes

모델에 대한 새로 고침 작업 내역 목록을 가져오려면 /refreshes 컬렉션에 GET 동사를 사용합니다. 응답 본문의 예는 다음과 같습니다. 

> [!NOTE]
> 작성 당시, 지난 30일 간의 새로 고침 작업이 저장되고 반환되지만, 이 값은 달라질 수 있습니다.

```
[
    {
        "refreshId": "1344a272-7893-4afa-a4b3-3fb87222fdac",
        "startTime": "2017-12-09T01:58:04.76",
        "endTime": "2017-12-09T01:58:12.607",
        "status": "succeeded"
    },
    {
        "refreshId": "474fc5a0-3d69-4c5d-adb4-8a846fa5580b",
        "startTime": "2017-12-07T02:05:48.32",
        "endTime": "2017-12-07T02:05:54.913",
        "status": "succeeded"
    }
]
```

## <a name="delete-refreshesrefreshid"></a>DELETE /refreshes/\<refreshId>

진행 중인 새로 고침 작업을 취소하려면 새로 고침 ID에 DELETE 동사를 사용합니다.

## <a name="post-sync"></a>POST /sync

새로 고침 작업을 수행한 후 쿼리 확장을 위해 새 데이터를 복제본과 동기화 해야 할 수 있습니다. 모델에 대 한 동기화 작업을 수행 하려면/sync 함수에서 POST 동사를 사용 합니다. 응답의 Location 헤더에는 동기화 작업 ID가 포함됩니다.

## <a name="get-sync-status"></a>GET /sync status

동기화 작업의 상태를 확인하려면 작업 ID를 매개 변수로 전달하여 GET 동사를 사용합니다. 응답 본문의 예는 다음과 같습니다.

```
{
    "operationId": "cd5e16c6-6d4e-4347-86a0-762bdf5b4875",
    "database": "AdventureWorks2",
    "UpdatedAt": "2017-12-09T02:44:26.18",
    "StartedAt": "2017-12-09T02:44:20.743",
    "syncstate": 2,
    "details": null
}
```

`syncstate`의 값:

- 0: 복제 중. 데이터베이스 파일을 대상 폴더에 복제하고 있습니다.
- 1: 리하이드레이션 중. 데이터베이스가 읽기 전용 서버 인스턴스에서 리하이드레이션되고 있습니다.
- 2: 완료됨. 동기화 작업이 완료되었습니다.
- 3: 실패. 동기화 작업이 실패했습니다.
- 4: 종료하는 중 동기화 작업이 완료되었으나 정리 단계를 수행하고 있습니다.

## <a name="code-sample"></a>코드 샘플

다음은 [GitHub의 RestApiSample](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample)을 시작하기 위한 C# 코드 샘플입니다.

### <a name="to-use-the-code-sample"></a>코드 샘플을 사용하려면

1.  리포지토리를 복제하거나 다운로드합니다. RestApiSample 솔루션을 엽니다.
2.  **client.BaseAddress = …** 줄을 찾은 후 [기준 URL](#base-url)을 제공합니다.

코드 샘플에서는 [서비스 주체](#service-principal) 인증을 사용 합니다.

### <a name="service-principal"></a>서비스 주체

서비스 주체를 설정하고 Azure AS에서 필요한 사용 권한을 할당하는 방법에 대한 자세한 정보는 [서비스 주체 만들기 - Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md) 및 [서버 관리자 역할에 서비스 주체 추가](analysis-services-addservprinc-admins.md)를 참조하세요. 이 단계를 완료한 다음, 다음과 같은 추가 단계를 완료합니다.

1.  코드 예제에서 **string authority = …** 를 찾은 후 **common**을 조직의 테넌트 ID로 바꿉니다.
2.  ClientCredential 클래스가 자격 증명 개체를 인스턴스화하는 데 사용되도록 주석 처리하거나 주석 처리를 해제합니다. \<App ID> 및 \<App Key> 값이 안전한 방식으로 액세스되는지 확인하고, 그렇지 않은 경우 서비스 사용자에 대해 인증서 기반 인증을 사용합니다.
3.  예제를 실행합니다.


## <a name="see-also"></a>참고 항목

[샘플](analysis-services-samples.md)   
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)   


