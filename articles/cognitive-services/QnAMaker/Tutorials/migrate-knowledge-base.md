---
title: 기술 자료 마이그레이션 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 기술 자료를 마이그레이션하려면 한 기술 자료를 내보낸 다음, 다른 기술 자료로 가져와야 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/28/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 942932c229ace82a0bf66da7a5421f936b028088
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302562"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>내보내기-가져오기를 사용하여 기술 자료 마이그레이션

기술 자료를 마이그레이션하려면 한 기술 자료를 내보낸 다음, 다른 기술 자료로 가져와야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* 새 [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md) 설정

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>QnA Maker에서 기술 자료 마이그레이션
1. [QnA Maker 포털](https://qnamaker.ai)에 로그인합니다.
1. 마이그레이션할 원본 기술 자료를 선택 합니다.

1. **설정** 페이지에서 **기술 자료 내보내기** 를 선택 하 여 원래 기술 자료의 내용, 질문, 답변, 메타 데이터, 추가 작업 프롬프트 및 추출 된 데이터 원본 이름을 포함 하는 tsv 파일을 다운로드 합니다.

1. 상단 메뉴에서 **기술 자료 만들기** 를 선택 하 고 _빈_ 기술 자료를 만듭니다. 이 파일은 만들 때 Url 또는 파일을 추가 하지 않기 때문에 비어 있습니다. 이러한 작업은 생성 후 가져오기 단계에서 추가 됩니다.

    기술 자료를 구성 합니다. 새 기술 자료 이름만 설정 합니다. 중복되는 이름과 특수 문자도 지원됩니다.

    파일을 가져올 때 해당 값을 덮어쓰기 때문에 4 단계에서 아무것도 선택 하지 마십시오.

1. 5 단계에서 **만들기**를 선택 합니다.

1. 이 새 기술 자료에서 **설정** 탭을 열고, **기술 자료 가져오기**를 선택합니다. 그러면 질문, 답변, 메타 데이터, 추가 작업 프롬프트가 가져오며 추출 된 데이터 원본 이름이 유지 됩니다.

   > [!div class="mx-imgBorder"]
   > [![기술 자료 가져오기](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. 테스트 패널을 사용하여 새 기술 자료를 **테스트**합니다. [기술 자료 테스트](../How-To/test-knowledge-base.md) 방법을 알아보세요.

1. 기술 자료를 **게시** 하 고 채팅 봇을 만듭니다. [기술 자료 게시](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) 방법을 알아보세요.

## <a name="chat-logs-and-alterations"></a>채팅 로그 및 변경 내용
대/소문자를 구분하지 않는 변경 내용(동의어)은 자동으로 가져오지 않습니다. [V4 api](https://go.microsoft.com/fwlink/?linkid=2092179) 를 사용 하 여 새 기술 자료의 변경 사항을 이동 합니다.

새 기술 자료는 Application Insights를 사용하여 채팅 로그를 저장하기 때문에 채팅 로그를 마이그레이션할 방법이 없습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 편집](../How-To/edit-knowledge-base.md)
