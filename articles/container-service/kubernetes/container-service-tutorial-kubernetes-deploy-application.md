---
title: (사용되지 않음) Azure Container Service 자습서 - 애플리케이션 배포
description: Azure Container Service 자습서 - 애플리케이션 배포
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 2c2d50da4328ff07c3d2fda4a8721839aa3aa6e7
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274065"
---
# <a name="deprecated-run-applications-in-kubernetes"></a>(사용되지 않음) Kubernetes에서 애플리케이션 실행

> [!TIP]
> Azure Kubernetes Service를 사용하는 이 자습서의 업데이트된 버전은 [자습서: AKS(Azure Kubernetes Service)에서 애플리케이션 실행](../../aks/tutorial-kubernetes-deploy-application.md)을 참조하세요.

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

7개 중 4단계인 이 자습서에서는 애플리케이션 예제를 Kubernetes 클러스터에 배포합니다. 완료되는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * Kubernetes 매니페스트 파일 업데이트
> * Kubernetes에서 애플리케이션 실행
> * 애플리케이션 테스트

후속 자습서에서는 이 애플리케이션을 규모 확장하고, Kubernetes 클러스터를 모니터링하도록 Log Analytics를 구성합니다.

이 자습서에서는 Kubernetes 개념에 대한 기본적인 이해가 있다고 가정하며 Kubernetes에 대한 자세한 정보는 [Kubernetes 설명서](https://kubernetes.io/docs/home/)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

이전 자습서에서는 애플리케이션을 컨테이너 이미지에 패키지하고, Azure Container Registry에 이러한 이미지를 업로드하고, Kubernetes 클러스터를 만들었습니다. 

이 자습서를 완료하려면 미리 작성된 `azure-vote-all-in-one-redis.yml` Kubernetes 매니페스트 파일이 필요합니다. 이전 자습서에서 애플리케이션 소스 코드와 함께 이 파일을 다운로드했습니다. 리포지토리를 복제했으며 디렉터리를 복제된 리포지토리로 변경했는지 확인하세요.

이러한 단계를 수행하지 않은 경우 수행하려면 [자습서 1 - 컨테이너 이미지 만들기](./container-service-tutorial-kubernetes-prepare-app.md)로 돌아갑니다. 

## <a name="update-manifest-file"></a>매니페스트 파일 업데이트

이 자습서에서는 ACR(Azure Container Registry)을 사용하여 컨테이너 이미지를 저장했습니다. 애플리케이션을 실행하기 전에 Kubernetes 매니페스트 파일에서 ACR 로그인 서버 이름을 업데이트해야 합니다.

[az acr list](/cli/azure/acr#az-acr-list) 명령을 사용하여 ACR 로그인 서버 이름을 가져옵니다.

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

매니페스트 파일은 로그인 서버 이름 `microsoft`를 사용하여 미리 작성되었습니다. 원하는 텍스트 편집기에서 파일을 엽니다. 이 예제에서는 `vi`를 사용하여 파일을 엽니다.

```bash
vi azure-vote-all-in-one-redis.yml
```

`microsoft`는 ACR 로그인 서버 이름으로 바꿉니다. 이 값은 매니페스트 파일의 줄 **47**에 있습니다.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

파일을 저장하고 닫습니다.

## <a name="deploy-application"></a>애플리케이션 배포

[kubectl create](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create) 명령을 사용하여 애플리케이션을 실행합니다. 이 명령은 매니페스트 파일을 구문 분석하고 정의된 Kubernetes 개체를 만듭니다.

```console
kubectl create -f azure-vote-all-in-one-redis.yml
```

출력:

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>애플리케이션 테스트

인터넷에 애플리케이션을 노출하는 [Kubernetes 서비스](https://kubernetes.io/docs/concepts/services-networking/service/)가 생성됩니다. 이 프로세스는 몇 분 정도 걸릴 수 있습니다. 

진행 상태를 모니터링하려면 `--watch` 인수와 함께 [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) 명령을 사용합니다.

```console
kubectl get service azure-vote-front --watch
```

처음에는 `azure-vote-front` 서비스에 대한 **EXTERNAL-IP**가 `pending`으로 표시됩니다. EXTERNAL-IP 주소가 `pending`에서 `IP address`로 변경되면 `CTRL-C`를 사용하여 kubectl 조사식 프로세스를 중지합니다.

```output
NAME               CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   10.0.42.158   <pending>     80:31873/TCP   1m
azure-vote-front   10.0.42.158   52.179.23.131 80:31873/TCP   2m
```

애플리케이션을 보려면 외부 IP 주소로 이동합니다.

![Azure의 Kubernetes 클러스터 이미지](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 투표 애플리케이션을 Azure Container Service Kubernetes 클러스터에 배포했습니다. 완료된 작업은 다음과 같습니다.  

> [!div class="checklist"]
> * Kubernetes 매니페스트 파일 다운로드
> * Kubernetes에서 애플리케이션 실행
> * 애플리케이션 테스트

다음 자습서로 이동하여 Kubernetes 애플리케이션과 기본 Kubernetes 인프라를 모두 크기 조정하는 방법에 대해 알아봅니다. 

> [!div class="nextstepaction"]
> [Kubernetes 애플리케이션 및 인프라 크기 조정](./container-service-tutorial-kubernetes-scale.md)
