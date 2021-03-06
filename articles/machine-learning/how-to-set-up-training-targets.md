---
title: 모델 학습에 계산 대상 사용
titleSuffix: Azure Machine Learning
description: 기계 학습 모델 학습의 학습 환경(컴퓨팅 대상)을 구성합니다. 학습 환경을 쉽게 전환할 수 있습니다. 로컬로 학습을 시작합니다. 규모 확장이 필요한 경우 클라우드 기반 컴퓨팅 대상으로 전환합니다.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/13/2020
ms.custom: seodec18
ms.openlocfilehash: 209ed755a7ef83b67170ef75911f93cdda742caa
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79368199"
---
# <a name="set-up-and-use-compute-targets-for-model-training"></a>모델 학습을 위한 계산 대상 설정 및 사용 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning를 사용 하면 [__계산 대상__](concept-azure-machine-learning-architecture.md#compute-targets)이라고 하는 다양 한 리소스 또는 환경에서 모델을 학습 시킬 수 있습니다. 컴퓨팅 대상은 로컬 컴퓨터 또는 클라우드 리소스(예: Azure Machine Learning Compute, Azure HDInsight 또는 원격 가상 머신)일 수 있습니다.  ["모델 배포 위치 및 방법"](how-to-deploy-and-where.md)의 설명에 따라 모델 배포용 컴퓨팅 대상을 만들 수도 있습니다.

Azure Machine Learning SDK, Azure Machine Learning studio, Azure CLI 또는 Azure Machine Learning VS Code 확장을 사용 하 여 계산 대상을 만들고 관리할 수 있습니다. 다른 서비스 (예: HDInsight 클러스터)를 통해 생성 된 계산 대상이 있는 경우 Azure Machine Learning 작업 영역에 연결 하 여 사용할 수 있습니다.
 
이 문서에서는 모델 학습에 다양한 컴퓨팅 대상을 사용하는 방법을 알아봅니다.  모든 컴퓨팅 대상에 대한 단계는 동일한 워크플로를 따릅니다.
1. 아직 없는 경우 계산 대상을 __만듭니다__ .
2. 작업 영역에 컴퓨팅 대상을 __연결__합니다.
3. 해당 스크립트에 필요한 Python 환경과 패키지 종속성을 포함하도록 컴퓨팅 대상을 __구성__합니다.


>[!NOTE]
> 이 문서의 코드는 Azure Machine Learning SDK 버전 1.0.74를 사용 하 여 테스트 되었습니다.

## <a name="compute-targets-for-training"></a>학습용 컴퓨팅 대상

Azure Machine Learning 다양 한 계산 대상에서 다양 한 지원을 제공 합니다. 일반적인 모델 개발 수명 주기는 작은 양의 데이터에 대한 개발/실험으로 시작합니다. 이 단계에서는 로컬 환경을 사용하는 것이 좋습니다. 예를 들어 로컬 컴퓨터 또는 클라우드 기반 VM입니다. 더 큰 데이터 세트를 기반으로 학습을 확장하거나 분산 학습을 수행할 경우 Azure Machine Learning 컴퓨팅을 사용하여 실행을 제출할 때마다 자동 크기 조정되는 단일 또는 다중 노드 클러스터를 만드는 것이 좋습니다. 다음 설명대로 다양한 시나리오 지원이 달라질 수는 있지만 고유한 컴퓨팅 리소스를 연결할 수도 있습니다.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]


> [!NOTE]
> Azure Machine Learning 컴퓨팅은 영구 리소스로 생성되거나 실행을 요청할 때 동적으로 생성될 수 있습니다. 실행에 기반한 생성은 학습 실행이 완료된 후 컴퓨팅 대상을 제거하므로 이 방법으로 생성된 컴퓨팅 대상을 다시 사용할 수 없습니다.

## <a name="whats-a-run-configuration"></a>실행 구성이란?

학습 시에는 보통 로컬 컴퓨터에서 시작한 후 나중에 다른 컴퓨팅 대상에서 해당 학습 스크립트를 실행합니다. Azure Machine Learning를 사용 하면 스크립트를 변경 하지 않고도 다양 한 계산 대상에서 스크립트를 실행할 수 있습니다.

**실행 구성**내에서 각 계산 대상에 대 한 환경을 정의 하기만 하면 됩니다.  그런 다음 다른 컴퓨팅 대상에서 학습 실험을 실행하려는 경우에 해당 컴퓨팅에 대한 실행 구성을 지정합니다. 환경을 지정 하 고 구성 실행에 바인딩하는 방법에 대 한 자세한 내용은 [학습 및 배포를 위한 환경 만들기 및 관리](how-to-use-environments.md)를 참조 하세요.

이 문서의 끝부분에서 [실험 제출](#submit)에 대해 자세히 알아보세요.

## <a name="whats-an-estimator"></a>평가기?

인기 있는 프레임 워크를 사용 하 여 모델 학습을 용이 하 게 하기 위해 Azure Machine Learning Python SDK는 더 높은 수준의 추상화 인 평가기 클래스를 제공 합니다.  이 클래스를 사용 하 여 간편 하 게 실행 구성을 생성할 수 있습니다. 제네릭 [평가기](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) 을 만들고 사용 하 여 선택한 학습 프레임 워크 (예: scikit)를 사용 하는 학습 스크립트를 제출할 수 있습니다. 환경 또는 RunConfiguration 개체와 같은 포함 된 개체를 자동으로 생성 하므로 학습에 평가기를 사용 하는 것이 좋습니다. 이러한 개체를 만드는 방법에 대 한 더 많은 제어를 제공 하 고 실험 실행을 위해 설치할 패키지를 지정 하려면 [다음 단계](#amlcompute) 를 수행 하 여 Azure Machine Learning 계산에서 runconfiguration 개체를 사용 하 여 학습 실험을 제출 합니다.

PyTorch, TensorFlow 및 체 이너 Azure Machine Learning 작업의 경우 이러한 프레임 워크 사용을 간소화 하기 위해 각 [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)및 [체 이너](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) 추정 제공 됩니다.

자세한 내용은 [추정를 사용 하 여 ML 모델 학습](how-to-train-ml-models.md)을 참조 하세요.

## <a name="whats-an-ml-pipeline"></a>ML 파이프라인 이란?

ML 파이프라인을 사용 하 여 간단한, 속도, 이식성 및 재사용으로 워크플로를 최적화할 수 있습니다. Azure Machine Learning를 사용 하 여 파이프라인을 빌드하는 경우 인프라 및 자동화 대신 전문 지식, 기계 학습에 집중할 수 있습니다.

ML 파이프라인은 파이프라인에서 고유한 계산 단위인 여러 **단계**에서 생성 됩니다. 각 단계는 독립적으로 실행 되 고 격리 된 계산 리소스를 사용할 수 있습니다. 이를 통해 여러 데이터 과학자가 과도 한 처리 시간이 소모 계산 리소스 없이 동시에 동일한 파이프라인에서 작업할 수 있으며 각 단계에 대해 다른 계산 형식/크기를 쉽게 사용할 수 있습니다.

> [!TIP]
> ML 파이프라인은 모델을 학습 하는 경우 실행 구성 또는 추정를 사용할 수 있습니다.

ML 파이프라인은 모델을 트레인 할 수 있지만 학습 후 모델을 학습 하 고 배포 하기 전에 데이터를 준비할 수도 있습니다. 파이프라인의 주요 사용 사례 중 하나는 일괄 처리 점수 매기기입니다. 자세한 내용은 [파이프라인: machine learning 워크플로 최적화](concept-ml-pipelines.md)를 참조 하세요.

## <a name="set-up-in-python"></a>Python에서 설정

아래의 섹션을 통해 다음과 같은 컴퓨팅 대상을 구성합니다.

* [로컬 컴퓨터](#local)
* [Azure Machine Learning 컴퓨팅](#amlcompute)
* [원격 가상 머신](#vm)
* [Azure HDInsight](#hdinsight)


### <a id="local"></a>로컬 컴퓨터

1. **만들기 및 연결**: 로컬 컴퓨터를 학습 환경으로 사용 하기 위해 계산 대상을 만들거나 연결할 필요가 없습니다.  

1. **구성**: 로컬 컴퓨터를 계산 대상으로 사용 하는 경우 [개발 환경](how-to-configure-environment.md)에서 교육 코드를 실행 합니다.  해당 환경에 필요한 Python 패키지가 이미 있으면 사용자 관리 환경을 사용합니다.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

이제 계산을 연결 하 고 실행을 구성 했으므로 다음 단계는 [학습 실행을 제출](#submit)하는 것입니다.

### <a id="amlcompute"></a>Azure Machine Learning 컴퓨팅

Azure Machine Learning 컴퓨팅은 사용자가 단일 또는 다중 노드 컴퓨팅을 손쉽게 만들 수 있는 관리형 컴퓨팅 인프라입니다. 작업 영역 지역 내에서 컴퓨팅은 작업 영역에서 다른 사용자와 공유할 수 있는 리소스로 만들어집니다. 작업이 제출될 때 컴퓨팅이 자동으로 확장되어 Azure Virtual Network에 배치될 수 있습니다. 컴퓨팅은 컨테이너화된 환경에서 실행되며 모델 종속성을 [Docker 컨테이너](https://www.docker.com/why-docker)로 패키지합니다.

Azure Machine Learning 컴퓨팅을 사용하여 클라우드의 CPU 또는 GPU 컴퓨팅 노드 클러스터에 학습 프로세스를 배포할 수 있습니다. GPU를 포함하는 VM 크기에 대한 자세한 내용은 [GPU 최적화 가상 머신 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)를 참조하세요.

Azure Machine Learning 컴퓨팅에는 할당할 수 있는 코어 수와 같은 기본적인 제한이 있습니다. 자세한 내용은 [Azure 리소스에 대한 할당량 관리 및 요청](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas)을 참조하세요.


실행을 예약할 때 주문형으로 또는 영구적 리소스로 Azure Machine Learning 컴퓨팅 환경을 만들 수 있습니다.

#### <a name="run-based-creation"></a>실행 기반 만들기

Azure Machine Learning 컴퓨팅을 런타임에 컴퓨팅 대상으로 만들 수 있습니다. 실행에 대해 컴퓨팅이 자동으로 만들어집니다. 실행이 완료되면 컴퓨팅이 자동으로 삭제됩니다. 

> [!IMPORTANT]
> Azure Machine Learning 컴퓨팅의 실행 기반 만들기는 현재 미리 보기로 제공됩니다. 하이퍼 매개 변수 튜닝 또는 자동화된 Machine Learning을 사용 중인 경우에는 실행 기반 만들기를 사용하지 마세요. 하이퍼 매개 변수 튜닝 또는 자동화된 기계 학습을 사용하려면 [영구적 컴퓨팅](#persistent) 대상을 대신 만듭니다.

1.  **만들기, 연결 및 구성**: 실행 기반 생성은 실행 구성을 사용 하 여 계산 대상을 만들고 연결 하 고 구성 하는 데 필요한 모든 단계를 수행 합니다.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


이제 계산을 연결 하 고 실행을 구성 했으므로 다음 단계는 [학습 실행을 제출](#submit)하는 것입니다.

#### <a id="persistent"></a>영구적 컴퓨팅

영구적 Azure Machine Learning 컴퓨팅은 작업 전반에서 다시 사용할 수 있습니다. 컴퓨팅은 작업 영역의 다른 사용자와 공유할 수 있고 작업 간에 유지됩니다.

1. **만들기 및 연결**: Python에서 영구 Azure Machine Learning 계산 리소스를 만들려면 **vm_size** 및 **max_nodes** 속성을 지정 합니다. 그런 다음, Azure Machine Learning은 다른 속성에 스마트 기본값을 사용합니다. 사용되지 않는 경우 컴퓨팅은 0개 노드로 자동 축소됩니다.   필요에 따라 작업을 실행하기 위해 전용 VM이 만들어집니다.
    
    * **vm_size**: Azure Machine Learning Compute에서 만든 노드의 vm 패밀리입니다.
    * **max_nodes**: Azure Machine Learning Compute에서 작업을 실행할 때 자동으로 크기를 조정 하는 최대 노드 수입니다.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Azure Machine Learning 컴퓨팅을 만들 때 여러 고급 속성을 구성할 수도 있습니다. 속성을 사용하면 고정 크기로 또는 구독의 기존 Azure Virtual Network 내에서 영구적 클러스터를 만들 수 있습니다.  자세한 내용은 [AmlCompute 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    )를 참조하세요.
    
   또는 [Azure Machine Learning studio](#portal-create)에서 영구 Azure Machine Learning 계산 리소스를 만들고 연결할 수 있습니다.

1. **구성**: 영구 계산 대상에 대 한 실행 구성을 만듭니다.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

이제 계산을 연결 하 고 실행을 구성 했으므로 다음 단계는 [학습 실행을 제출](#submit)하는 것입니다.


### <a id="vm"></a>원격 가상 머신

Azure Machine Learning은 자신만의 컴퓨팅 리소스를 가져와서 작업 영역에 연결하는 기능을 지원합니다. 이러한 리소스 종류 중 하나는 Azure Machine Learning에서 액세스할 수 있는 임의의 원격 VM입니다. 리소스는 조직 또는 온-프레미스에 있는 Azure VM 또는 원격 서버일 수 있습니다. 특히 IP 주소 및 자격 증명(사용자 이름 및 암호 또는 SSH 키)이 지정되면 원격 실행에 액세스 가능한 VM을 사용할 수 있습니다.

시스템 빌드 conda 환경, 기존 Python 환경 또는 Docker 컨테이너를 사용할 수 있습니다. Docker 컨테이너에서 실행하려면 Docker 엔진이 VM에서 실행 되어야 합니다. 이 기능은 로컬 머신보다 더 유연한 클라우드 기반 개발/실험 환경을 원하는 경우에 특히 유용합니다.

이 시나리오에서 선택하는 Azure VM으로 Azure Data Science Virtual Machine(DSVM)을 사용합니다. 이 VM은 Azure에서 미리 구성된 데이터 과학 및 AI 개발 환경입니다. 이 VM은 전체 수명 주기 기계 학습을 위해 큐레이팅된 도구 및 프레임워크 옵션을 제공합니다. Azure Machine Learning과 함께 DSVM을 사용하는 방법에 관한 자세한 내용은 [개발 환경 구성](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm)을 참조하세요.

1. **만들기**: 모델을 학습 하는 데 사용 하기 전에 dsvm을 만듭니다. 이 리소스를 만들려면 [Linux(Ubuntu) 용 Data Science Virtual Machine 프로비전](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)을 참조하세요.

    > [!WARNING]
    > Azure Machine Learning은 Ubuntu를 실행하는 가상 머신만 지원합니다. VM을 만들거나 기존 VM을 선택하는 경우 Ubuntu를 사용하는 VM을 선택해야 합니다.

1. **연결**: 기존 가상 컴퓨터를 계산 대상으로 연결 하려면 가상 컴퓨터에 대 한 FQDN (정규화 된 도메인 이름), 사용자 이름 및 암호를 제공 해야 합니다. 이 예제에서는 \<fqdn>을 VM의 공용 FQDN 또는 공용 IP 주소로 바꿉니다. \<사용자 이름> 및 \<암호>를 VM에 대한 SSH 사용자 이름 및 암호로 바꿉니다.

    > [!IMPORTANT]
    > 다음 Azure 지역은 VM의 공용 IP 주소를 사용 하 여 가상 머신 연결을 지원 하지 않습니다. 대신 `resource_id` 매개 변수를 사용 하 여 VM의 Azure Resource Manager ID를 사용 합니다.
    >
    > * 미국 동부
    > * 미국 서부 2
    > * 미국 중남부
    >
    > 다음 문자열 형식을 사용 하 여 구독 ID, 리소스 그룹 이름 및 VM 이름을 사용 하 여 VM의 리소스 ID를 생성할 수 있습니다. `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`.


   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   attach_config = RemoteCompute.attach_configuration(address='<fqdn>',
                                                    ssh_port=22,
                                                    username='<username>',
                                                    password="<password>")
   # If in US East, US West 2, or US South Central, use the following instead:
   # attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
   #                                                 ssh_port=22,
   #                                                 username='<username>',
   #                                                 password="<password>")

   # If you authenticate with SSH keys instead, use this code:
   #                                                  ssh_port=22,
   #                                                  username='<username>',
   #                                                  password=None,
   #                                                  private_key_file="<path-to-file>",
   #                                                  private_key_passphrase="<passphrase>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   또는 [Azure Machine Learning studio를 사용 하 여](#portal-reuse)dsvm을 작업 영역에 연결할 수 있습니다.

1. **구성**: dsvm 계산 대상에 대 한 실행 구성을 만듭니다. Docker 및 conda는 DSVM에서 학습 환경을 만들고 구성하는 데 사용됩니다.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


이제 계산을 연결 하 고 실행을 구성 했으므로 다음 단계는 [학습 실행을 제출](#submit)하는 것입니다.

### <a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight는 빅 데이터 분석을 위한 인기 있는 플랫폼입니다. 플랫폼은 모델을 학습하는 데 사용할 수 있는 Apache Spark를 제공합니다.

1. **만들기**: HDInsight 클러스터를 사용 하 여 모델을 학습 하기 전에 만듭니다. HDInsight 클러스터에서 Spark를 만들려면 [HDInsight에서 Spark 클러스터 만들기](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql)를 참조하세요. 

    클러스터를 만들 때 SSH 사용자 이름 및 암호를 지정해야 합니다. 이러한 값은 HDInsight를 컴퓨팅 대상으로 사용할 때 필요하므로 기록해 둡니다.
    
    클러스터를 만든 후 호스트이름 \<clustername>.azurehdinsight.net과 연결합니다. 여기서 \<clustername>은 클러스터에 대해 사용자가 제공한 이름입니다. 

1. **연결**: hdinsight 클러스터를 계산 대상으로 연결 하려면 hdinsight 클러스터에 대 한 호스트 이름, 사용자 이름 및 암호를 제공 해야 합니다. 다음 예제에서는 SDK를 사용하여 작업 영역에 클러스터를 연결합니다. 예제에서는 \<clustername>을 클러스터의 이름으로 바꿉니다. \<username> 및 \<password>를 클러스터에 대한 SSH 사용자 이름 및 암호로 바꿉니다.

    > [!IMPORTANT]
    > 다음 Azure 지역은 클러스터의 공용 IP 주소를 사용 하 여 HDInsight 클러스터를 연결 하는 것을 지원 하지 않습니다. 대신 `resource_id` 매개 변수를 사용 하 여 클러스터의 Azure Resource Manager ID를 사용 합니다.
    >
    > * 미국 동부
    > * 미국 서부 2
    > * 미국 중남부
    >
    > 클러스터의 리소스 ID는 다음 문자열 형식을 사용 하 여 구독 ID, 리소스 그룹 이름 및 클러스터 이름을 사용 하 여 생성할 수 있습니다. `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`.

   ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase
    attach_config = HDInsightCompute.attach_configuration(address='<clustername>-ssh.azurehdinsight.net', 
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    # If you are in US East, US West 2, or US South Central, use the following instead:
    # attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
    #                                                      ssh_port=22, 
    #                                                      username='<ssh-username>', 
    #                                                      password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   또는 [Azure Machine Learning studio를 사용 하 여](#portal-reuse)HDInsight 클러스터를 작업 영역에 연결할 수 있습니다.

1. **구성**: hdi 계산 대상에 대 한 실행 구성을 만듭니다. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


이제 계산을 연결 하 고 실행을 구성 했으므로 다음 단계는 [학습 실행을 제출](#submit)하는 것입니다.


### <a id="azbatch"></a>Azure Batch 

Azure Batch은 클라우드에서 대규모 병렬 및 HPC (고성능 컴퓨팅) 응용 프로그램을 효율적으로 실행 하는 데 사용 됩니다. AzureBatchStep은 Azure Machine Learning 파이프라인에서 사용 하 여 Azure Batch 컴퓨터 풀에 작업을 제출할 수 있습니다.

Azure Batch를 계산 대상으로 연결 하려면 Azure Machine Learning SDK를 사용 하 고 다음 정보를 제공 해야 합니다.

-    **Azure Batch compute name**: 작업 영역 내에서 계산에 사용 되는 식별 이름입니다.
-    **Azure Batch 계정 이름**: Azure Batch 계정의 이름
-    **리소스 그룹**: Azure Batch 계정이 포함 된 리소스 그룹입니다.

다음 코드는 Azure Batch를 계산 대상으로 연결 하는 방법을 보여 줍니다.

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

## <a name="set-up-in-azure-machine-learning-studio"></a>Azure Machine Learning studio에서 설정

Azure Machine Learning studio에서 작업 영역과 연결 된 계산 대상에 액세스할 수 있습니다.  Studio를 사용 하 여 다음을 수행할 수 있습니다.

* 작업 영역에 연결된 [컴퓨팅 대상 보기](#portal-view)
* 작업 영역에서 [컴퓨팅 대상 만들기](#portal-create)
* 작업 영역 외부에서 만든 [컴퓨팅 대상 연결](#portal-reuse)


대상을 만들고 작업 영역에 연결한 후 `ComputeTarget` 개체와 함께 실행 구성에서 대상을 사용합니다. 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a id="portal-view"></a>컴퓨팅 대상 보기


작업 영역에 대한 컴퓨팅 대상을 확인하려면 다음 단계를 사용합니다.

1. [Azure Machine Learning studio](https://ml.azure.com)로 이동 합니다.
 
1. __애플리케이션__에서 __컴퓨팅__을 선택합니다.

    [![보기 계산 탭](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a id="portal-create"></a>컴퓨팅 대상 만들기

컴퓨팅 대상의 목록을 보려면 이전 단계를 수행합니다. 그런 다음, 다음과 같은 단계를 사용하여 컴퓨팅 대상을 만듭니다. 

1. 더하기 기호(+)를 선택하여 컴퓨팅 대상을 추가합니다.

    ![컴퓨팅 대상 추가](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. 컴퓨팅 대상의 이름을 입력합니다. 

1. **학습**에 사용할 컴퓨팅 유형으로 __Machine Learning 컴퓨팅__을 선택합니다. 

    >[!NOTE]
    >Azure Machine Learning Compute는 Azure Machine Learning studio에서 만들 수 있는 유일한 관리 되는 계산 리소스입니다.  다른 모든 컴퓨팅 리소스는 만든 후에 연결할 수 있습니다.

1. 양식을 작성합니다. 필요한 속성, 특히 컴퓨팅을 스핀업하는 데 사용하는 **VM 제품군** 및 **최대 노드**에 대한 값을 입력합니다.  

1. __만들기__를 선택합니다.


1. 목록에서 컴퓨팅 대상을 선택하여 만들기 작업의 상태를 봅니다.

    ![만들기 작업 상태를 보려면 컴퓨팅 대상을 선택합니다.](./media/how-to-set-up-training-targets/View_list.png)

1. 그러면 컴퓨팅 대상의 세부 정보가 표시됩니다. 

    ![컴퓨팅 대상 세부 정보 보기](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a id="portal-reuse"></a>컴퓨팅 대상 연결

Azure Machine Learning 작업 영역 외부에서 만든 계산 대상을 사용 하려면 연결 해야 합니다. 컴퓨팅 대상을 연결하면 작업 영역에서 사용할 수 있습니다.

컴퓨팅 대상의 목록을 보려면 이전에 설명한 단계를 수행합니다. 그런 다음, 아래 단계에 따라 컴퓨팅 대상을 연결합니다. 

1. 더하기 기호(+)를 선택하여 컴퓨팅 대상을 추가합니다. 
1. 컴퓨팅 대상의 이름을 입력합니다. 
1. __학습__용으로 연결할 컴퓨팅 유형을 선택합니다.

    > [!IMPORTANT]
    > 모든 계산 형식이 Azure Machine Learning studio에서 연결 될 수 있는 것은 아닙니다. 현재 학습용으로 연결할 수 있는 컴퓨팅 유형은 다음과 같습니다.
    >
    > * 원격 VM
    > * Azure Databricks(기계 학습 파이프라인에 사용)
    > * Azure Data Lake Analytics(기계 학습 파이프라인에 사용)
    > * Azure HDInsight

1. 양식을 입력하고 필요한 속성에 대한 값을 입력합니다.

    > [!NOTE]
    > Microsoft에서는 암호보다 더 안전한 SSH 키를 권장합니다. 암호는 무차별 암호 대입 공격에 취약합니다. SSH 키는 암호화 서명을 사용합니다. Azure Virtual Machines에 사용할 SSH 키를 만드는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.
    >
    > * [Linux 또는 macOS에서 SSH 키를 만들고 사용](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Windows에서 SSH 키를 만들고 사용](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. __연결__을 선택합니다. 
1. 목록에서 컴퓨팅 대상을 선택하여 연결 작업의 상태를 봅니다.

## <a name="set-up-with-cli"></a>CLI를 사용 하 여 설정

Azure Machine Learning에 대 한 [CLI 확장](reference-azure-machine-learning-cli.md) 을 사용 하 여 작업 영역과 연결 된 계산 대상에 액세스할 수 있습니다.  CLI를 사용하여 다음을 수행할 수 있습니다.

* 관리되는 컴퓨팅 대상 만들기
* 관리되는 컴퓨팅 대상 업데이트
* 관리되지 않는 컴퓨팅 대상 연결

자세한 내용은 [리소스 관리](reference-azure-machine-learning-cli.md#resource-management)를 참조하세요.

## <a name="set-up-with-vs-code"></a>VS Code로 설정

Azure Machine Learning에 대 한 [VS Code 확장](tutorial-train-deploy-image-classification-model-vscode.md#configure-compute-targets) 을 사용 하 여 작업 영역과 연결 된 계산 대상에 액세스 하 고 만들고 관리할 수 있습니다.

## <a id="submit"></a>Azure Machine Learning SDK를 사용 하 여 학습 실행 제출

실행 구성을 만든 후 실행 구성을 사용하여 해당 실험을 실행합니다.  학습 실행을 제출하는 코드 패턴은 모든 유형의 컴퓨팅 대상에서 동일합니다.

1. 실행할 실험 만들기
1. 실행을 제출합니다.
1. 실행이 완료될 때까지 기다립니다.

> [!IMPORTANT]
> 학습 실행을 제출 하면 학습 스크립트가 포함 된 디렉터리의 스냅숏이 만들어지고 계산 대상으로 전송 됩니다. 또한 작업 영역에 실험의 일부로 저장 됩니다. 파일을 변경 하 고 실행을 다시 제출 하면 변경 된 파일만 업로드 됩니다.
>
> 파일이 스냅숏에 포함 되지 않도록 하려면 디렉터리에 [.gitignore](https://git-scm.com/docs/gitignore) 또는 `.amlignore` 파일을 만들고 파일을 추가 합니다. `.amlignore` 파일은 [.gitignore](https://git-scm.com/docs/gitignore) 파일과 동일한 구문과 패턴을 사용 합니다. 두 파일이 모두 있는 경우 `.amlignore` 파일이 우선적으로 적용 됩니다.
> 
> 자세한 내용은 [스냅샷](concept-azure-machine-learning-architecture.md#snapshots)을 참조하세요.

### <a name="create-an-experiment"></a>실험 만들기

먼저 작업 영역에서 실험을 만듭니다.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>실험 제출

`ScriptRunConfig` 개체와 함께 실험을 제출합니다.  이 개체는 다음을 포함합니다.

* **source_directory**: 학습 스크립트를 포함 하는 원본 디렉터리
* **스크립트**: 학습 스크립트를 식별 합니다.
* **run_config**: 실행 구성으로, 학습의 발생 위치를 정의 합니다.

예를 들어 [로컬 대상](#local) 구성을 사용하려면:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

[amlcompute 대상](#amlcompute)과 같은 다른 실행 구성을 사용하여 다른 컴퓨팅 대상에서 실행되도록 동일한 실험을 전환합니다.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

> [!TIP]
> 이 예에서는 기본적으로 학습에 계산 대상의 노드 하나를 사용 합니다. 둘 이상의 노드를 사용 하려면 실행 구성의 `node_count`을 원하는 노드 수로 설정 합니다. 예를 들어 다음 코드는 학습에 사용 되는 노드 수를 4로 설정 합니다.
>
> ```python
> src.run_config.node_count = 4
> ```

또는

* `Estimator`추정기를 사용하여 ML 모델 학습[에 표시된 대로 ](how-to-train-ml-models.md) 개체와 함께 실험을 제출합니다.
* 하이퍼 [매개 변수 조정을](how-to-tune-hyperparameters.md)위해 하이퍼 드라이브 실행을 제출 합니다.
* [VS Code 확장](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model)을 통해 실험을 제출 합니다.

자세한 내용은 [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) 및 [runconfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) 설명서를 참조 하세요.

## <a name="create-run-configuration-and-submit-run-using-azure-machine-learning-cli"></a>Azure Machine Learning CLI를 사용 하 여 실행 구성 만들기 및 실행 제출

[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 및 [Machine Learning CLI 확장](reference-azure-machine-learning-cli.md) 을 사용 하 여 실행 구성을 만들고 여러 계산 대상에서 실행을 제출할 수 있습니다. 다음 예에서는 기존 Azure Machine Learning 작업 영역 있고 `az login` CLI 명령을 사용 하 여 Azure에 로그인 한 것으로 가정 합니다. 

### <a name="create-run-configuration"></a>실행 구성 만들기

실행 구성을 만드는 가장 간단한 방법은 machine learning Python 스크립트를 포함 하는 폴더를 탐색 하 고 CLI 명령을 사용 하는 것입니다.

```azurecli
az ml folder attach
```

이 명령은 다양 한 계산 대상에 대 한 템플릿 실행 구성 파일을 포함 하는 하위 폴더 `.azureml`를 만듭니다. 이러한 파일을 복사 및 편집 하 여 Python 패키지를 추가 하거나 Docker 설정을 변경 하는 등의 방법으로 구성을 사용자 지정할 수 있습니다.  

### <a name="structure-of-run-configuration-file"></a>실행 구성 파일의 구조

실행 구성 파일은 다음 섹션을 포함 하는 YAML 형식입니다.
 * 실행할 스크립트 및 해당 인수
 * 계산 대상 이름 ("local" 또는 작업 영역 아래의 계산 이름)입니다.
 * 실행을 실행 하기 위한 매개 변수: 프레임 워크, 분산 된 실행에 대 한 communicator, 최대 기간 및 계산 노드 수입니다.
 * 환경 섹션. 이 섹션의 필드에 대 한 자세한 내용은 [학습 및 배포를 위한 환경 만들기 및 관리](how-to-use-environments.md) 를 참조 하세요.
   * 실행을 위해 설치할 Python 패키지를 지정 하려면 [conda environment 파일](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually)을 만들고 __condaDependenciesFile__ 필드를 설정 합니다.
 * 기록 세부 정보를 실행 하 여 로그 파일 폴더를 지정 하 고 출력 컬렉션을 사용 하거나 사용 하지 않도록 설정 하 고 기록 스냅숏을 실행 합니다.
 * 선택한 프레임 워크에 특정 한 구성 세부 정보입니다.
 * 데이터 참조 및 데이터 저장소 세부 정보
 * 새 클러스터를 만들기 위한 Machine Learning 컴퓨팅 관련 된 구성 세부 정보입니다.

Full .runconfig 스키마에 대 한 예제 [JSON 파일](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) 을 참조 하세요.

### <a name="create-an-experiment"></a>실험 만들기

먼저 실행에 대 한 실험을 만듭니다.

```azurecli
az ml experiment create -n <experiment>
```

### <a name="script-run"></a>스크립트 실행

스크립트 실행을 제출 하려면 명령을 실행 합니다.

```azurecli
az ml run submit-script -e <experiment> -c <runconfig> my_train.py
```

### <a name="hyperdrive-run"></a>HyperDrive 실행

Azure CLI에서 HyperDrive를 사용 하 여 매개 변수 튜닝 실행을 수행할 수 있습니다. 먼저 다음과 같은 형식으로 하이퍼 드라이브 구성 파일을 만듭니다. 하이퍼 매개 변수 튜닝 매개 변수에 대 한 자세한 내용은 [모델에 대 한 하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md) 문서를 참조 하세요.

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

실행 구성 파일과 함께이 파일을 추가 합니다. 그런 다음를 사용 하 여 하이퍼 드라이브 실행을 제출 합니다.
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

.Runconfig 및 HyperDrive 구성의 *매개 변수 공간* 에 있는 *arguments* 섹션을 참조 하십시오. 학습 스크립트에 전달할 명령줄 인수를 포함 합니다. .Runconfig의 값은 각 반복에 대해 동일 하 게 유지 되는 반면, HyperDrive 구성의 범위는 반복 됩니다. 두 파일에 같은 인수를 지정 하지 마십시오.

이러한 ```az ml``` CLI 명령 및 전체 인수 집합에 대 한 자세한 내용은 [참조 설명서](reference-azure-machine-learning-cli.md)를 참조 하세요.

<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Git 추적 및 통합

원본 디렉터리가 로컬 Git 리포지토리 인 학습 실행을 시작 하면 리포지토리에 대 한 정보가 실행 기록에 저장 됩니다. 자세한 내용은 [Azure Machine Learning에 대 한 Git 통합](concept-train-model-git-integration.md)을 참조 하세요.

## <a name="notebook-examples"></a>Notebook 예제

이러한 Notebook에서 다양한 컴퓨팅 대상으로 학습하는 예를 참조하세요.
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계

* [자습서: 모델 학습](tutorial-train-models-with-aml.md) 은 관리 되는 계산 대상을 사용 하 여 모델을 학습 합니다.
* 하이퍼 [매개 변수를 효율적으로 조정](how-to-tune-hyperparameters.md) 하 여 더 나은 모델을 만드는 방법에 대해 알아봅니다.
* 모델을 학습했으면 [모델을 배포하는 방법 및 위치](how-to-deploy-and-where.md)를 알아봅니다.
* [RunConfiguration 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) SDK 참조를 확인합니다.
* [Azure 가상 네트워크와 Azure Machine Learning 사용](how-to-enable-virtual-network.md)
