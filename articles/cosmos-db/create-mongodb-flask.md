---
title: Azure Cosmos DB의 API for MongoDB를 사용하여 Python Flask 웹앱 빌드
description: Azure Cosmos DB의 API for MongoDB를 사용하여 연결 및 쿼리할 수 있는 Python Flask 코드 샘플을 제공합니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: 43f4cf7e4008aa01a26c48a8e99f7465eeeb234b
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77061746"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>빠른 시작: Azure Cosmos DB의 MongoDB API를 사용하여 Python 앱 빌드

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.JS](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

이 빠른 시작에서는 Azure Cosmos DB for Mongo DB API 계정 또는 Azure Cosmos DB 에뮬레이터를 사용하여 GitHub에서 복제된 Python Flask To-Do 웹앱을 실행합니다. Azure Cosmos DB는 글로벌 배포 및 수평적 크기 조정 기능을 사용하여 문서, 테이블, 키 값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있는 다중 모델 데이터베이스 서비스입니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 또는 Azure 구독 없이 [무료로 Azure Cosmos DB를 사용](https://azure.microsoft.com/try/cosmosdb/)할 수 있습니다. 또는 [Azure Cosmos DB 에뮬레이터](local-emulator.md)를 사용할 수 있습니다. 
- [Python 3.6+](https://www.python.org/downloads/)
- [Python 확장](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python)이 있는 [Visual Studio Code](https://code.visualstudio.com/Download).

## <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

이제 GitHub에서 Flask-MongoDB 앱을 복제하고 연결 문자열을 설정한 다음, 실행해 보겠습니다. 프로그래밍 방식으로 데이터를 사용하여 얼마나 쉽게 작업할 수 있는지 알게 될 것입니다.

1. 명령 프롬프트를 열고, git-samples라는 새 폴더를 만든 다음 명령 프롬프트를 닫습니다.

    ```bash
    md "C:\git-samples"
    ```

2. Git Bash와 같은 Git 터미널 창을 열고, `cd` 명령을 사용하여 샘플 앱을 설치할 새 폴더로 변경합니다.

    ```bash
    cd "C:\git-samples"
    ```

3. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 명령은 컴퓨터에서 샘플 앱의 복사본을 만듭니다.

    ```bash
    git clone https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample.git
    ```
3. 다음 명령을 실행하여 Python 모듈을 설치합니다.

    ```bash 
    pip install -r .\requirements.txt
    ```
4. Visual Studio Code에서 해당 폴더를 엽니다.

## <a name="review-the-code"></a>코드 검토

이 단계는 선택 사항입니다. 데이터베이스 리소스를 코드로 만드는 방법을 알아보려는 경우 다음 코드 조각을 검토할 수 있습니다. 그렇지 않으면 [웹앱 실행](#run-the-web-app)으로 넘어갈 수 있습니다. 

다음 코드 조각은 모두 *app.py* 파일에서 가져오고 로컬 Azure Cosmos DB 에뮬레이터에 대한 연결 문자열을 사용합니다. 그렇지 않으면 구문 분석할 수 없는 슬래시를 받아들이도록 아래와 같이 암호를 분할해야 합니다.

* MongoDB 클라이언트를 초기화하고, 데이터베이스를 검색하고, 인증합니다.

    ```python
    client = MongoClient("mongodb://127.0.0.1:10250/?ssl=true") #host uri
    db = client.test    #Select the database
    db.authenticate(name="localhost",password='C2y6yDjf5' + r'/R' + '+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw' + r'/Jw==')
    ```

* 컬렉션을 검색하거나 아직 없는 경우 컬렉션을 만듭니다.

    ```python
    todos = db.todo #Select the collection
    ```

* 앱 만들기

    ```Python
    app = Flask(__name__)
    title = "TODO with Flask"
    heading = "ToDo Reminder"
    ```
    
## <a name="run-the-web-app"></a>웹앱 실행

1. Azure Cosmos DB Emulator가 실행 중인지 확인합니다.

2. 터미널 창을 열고 `cd`로 앱이 저장된 디렉터리로 이동합니다.

3. 그런 다음, Flask 앱에 대한 환경 변수를 `set FLASK_APP=app.py`, PowerShell 편집기의 경우 `$env:FLASK_APP = app.py` 또는 Mac을 사용하는 경우 `export FLASK_APP=app.py`로 설정합니다. 

4. `flask run`을 사용하여 앱을 실행하고 *http:\//127.0.0.1:5000/* 으로 이동합니다.

5. 작업을 추가하거나 제거하고, 컬렉션에서 추가하거나 변경된 작업을 확인합니다.

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

라이브 Azure Cosmos DB 계정에 대해 코드를 테스트하려면 Azure Portal로 이동하여 계정을 만듭니다.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>연결 문자열 업데이트

라이브 Azure Cosmos DB 계정에 대해 코드를 테스트하려면 연결 문자열 정보를 가져옵니다. 그런 다음 앱에 복사합니다.

1. Azure Portal의 Azure Cosmos DB 계정에서 왼쪽 탐색 영역의 **연결 문자열**을 선택한 다음, **읽기-쓰기 키**를 선택합니다. 화면 오른쪽에 있는 복사 단추를 사용하여 사용자 이름, 연결 문자열 및 암호를 복사합니다. 

2. 루트 디렉터리에 있는 *app.py* 파일을 엽니다.

3. 포털에서 복사 단추를 사용하여 **사용자 이름** 값을 복사하고 *app.py* 파일의 **이름** 값으로 만듭니다.

4. 그런 다음, 포털에서 **연결 문자열** 값을 복사하여 *app.py* 파일의 **MongoClient** 값으로 만듭니다.

5. 마지막으로, 포털에서 사용자의 **암호** 값을 복사하고 *app.py* 파일의 **암호** 값으로 만듭니다.

이제 Azure Cosmos DB와 통신하는 데 필요한 모든 정보로 앱이 업데이트되었습니다. 이전과 같은 방식으로 실행할 수 있습니다.

## <a name="deploy-to-azure"></a>Deploy to Azure

이 앱을 배포하려면 Azure에서 새 웹앱을 만들고 이 GitHub 리포지토리의 포크로 지속적인 배포를 사용하도록 설정할 수 있습니다. 이 [자습서](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment)에 따라 Azure에서 GitHub를 사용하여 지속적인 배포를 설정합니다.

Azure에 배포할 때 애플리케이션 키를 제거하고 아래 섹션이 주석 처리되어 있지 않은지 확인해야 합니다.

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

그런 다음, MONGOURL, MONGO_PASSWORD 및 MONGO_USERNAME을 애플리케이션 설정에 추가해야 합니다. Azure Web Apps의 애플리케이션 설정에 대한 자세한 내용은 이 [자습서](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings)를 참조하세요.

이 리포지토리의 포크를 만들지 않으려면 아래의 **Azure에 배포** 단추를 선택할 수도 있습니다. 그런 다음, Azure로 이동하고 Azure Cosmos DB 계정 정보를 사용하여 애플리케이션 설정을 지정해야 합니다.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> GitHub 또는 다른 원본 제어 옵션에 코드를 저장하려면 코드에서 연결 문자열을 제거합니다. 대신 웹앱에 대한 애플리케이션 설정을 사용하여 지정할 수 있습니다.

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Cosmos DB for Mongo DB API 계정을 만들고 Azure Cosmos DB 에뮬레이터를 사용하여 GitHub에서 복제된 Python Flask To-Do 웹앱을 실행하는 방법을 알아보았습니다. 이제 사용자의 Azure Cosmos DB 계정에 추가 데이터를 가져올 수 있습니다. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB로 MongoDB 데이터 가져오기](mongodb-migrate.md)
