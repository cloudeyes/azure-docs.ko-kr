---
title: Zeppelin 노트북 & Apache Spark 클러스터-Azure HDInsight
description: Azure HDInsight에서 Apache Spark 클러스터와 함께 Zeppelin Notebook을 사용하는 방법에 대한 단계별 지침입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: e313048986beca1991e38ce2e65ea12f954170d2
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598275"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Azure HDInsight에서 Apache Spark 클러스터와 함께 Apache Zeppelin Notebook 사용

HDInsight Spark 클러스터에는 [Apache Spark](https://zeppelin.apache.org/) 작업을 실행하는 데 사용할 수 있는 [Apache Zeppelin](https://spark.apache.org/) Notebook이 포함되어 있습니다. 이 문서에서는 HDInsight 클러스터에서 Zeppelin Notebook을 사용하는 방법에 대해 알아 봅니다.

## <a name="prerequisites"></a>사전 요구 사항

* HDInsight의 Apache Spark. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.
* 클러스터 기본 저장소에 대 한 URI 체계입니다. 이는 `adl://`에 대 한 Azure Data Lake Storage Gen2 또는 Azure Data Lake Storage Gen1 `abfs://` Azure Blob Storage `wasb://` 됩니다. Blob Storage에 대해 보안 전송이 사용 되는 경우 URI는 `wasbs://`됩니다.  자세한 내용은 [Azure Storage에서 보안 전송 필요](../../storage/common/storage-require-secure-transfer.md) 를 참조 하세요.

## <a name="launch-an-apache-zeppelin-notebook"></a>Apache Zeppelin Notebook 시작

1. Spark 클러스터 **개요**에서 **클러스터 대시보드의** **Zeppelin 노트북** 을 선택 합니다. 클러스터에 대 한 관리자 자격 증명을 입력 합니다.  

   > [!NOTE]  
   > 또한 브라우저에서 다음 URL을 열어 클러스터에 대한 Zeppelin Notebook에 도달할 수 있습니다. **CLUSTERNAME** 을 클러스터의 이름으로 바꿉니다.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. 새 Notebook을 만듭니다. 머리글 창에서 **노트북** 으로 이동 하 > **새 노트 만들기를 참조**하세요.

    ![새 Zeppelin 노트북 만들기](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "새 Zeppelin 노트북 만들기")

    노트북의 이름을 입력 한 다음 **메모 만들기**를 선택 합니다.

3. 노트북 머리글이 연결 된 상태를 표시 하는지 확인 합니다. 오른쪽 위 모서리에 녹색 점으로 표시 됩니다.

    ![Zeppelin 노트북 상태](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin 노트북 상태")

4. 샘플 데이터를 임시 테이블에 로드합니다. HDInsight에서 Spark 클러스터를 만들 때 샘플 데이터 파일 `hvac.csv`은 `\HdiSamples\SensorSampleData\hvac`의 연결 된 저장소 계정에 복사 됩니다.

    새 노트북에 기본적으로 만들어지는 빈 단락에 다음 코드 조각을 붙여넣습니다.

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    // Define a schema
    case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

    // Map the values in the .csv file to the schema
    val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
        s => Hvac(s(0),
                s(1),
                s(2).toInt,
                s(3).toInt,
                s(6)
        )
    ).toDF()

    // Register as a temporary table called "hvac"
    hvac.registerTempTable("hvac")
    ```

    **SHIFT + enter** 를 누르거나 단락에 대 한 **재생** 단추를 선택 하 여 코드 조각을 실행 합니다. 단락의 오른쪽 모서리 상태가 준비, 보류 중, 실행 중, 완료 순서로 진행됩니다. 출력은 같은 단락 하단에 표시됩니다. 스크린샷은 다음과 같습니다.

    ![원시 데이터에서 임시 테이블 만들기](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "원시 데이터에서 임시 테이블 만들기")

    또한 각 단락에 제목을 제공할 수도 있습니다. 단락의 오른쪽 모서리에서 **설정** 아이콘 (sprocket)을 선택한 다음 **제목 표시**를 선택 합니다.  

    > [!NOTE]  
    > %spark2 인터프리터는 모든 HDInsight 버전의 Zeppelin 노트북에서 지원되지 않으며 %sh 인터프리터는 HDInsight 4.0 이상에서는 지원되지 않습니다.

5. 이제 `hvac` 테이블에서 Spark SQL 문을 실행할 수 있습니다. 새 단락에 다음 쿼리를 붙여넣습니다. 이 쿼리는 지정된 날짜에서 각 건물에 대한 건물 ID와 대상 및 실제 온도 간의 차이를 검색합니다. **Shift + Enter**를 누릅니다.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    **%sql** 문의 처음 부분은 Notebook에 Livy Scala 인터프리터를 사용하도록 지시합니다.

6. **가로 막대형 차트** 아이콘을 선택 하 여 표시를 변경 합니다.  **가로 막대형 차트**를 선택한 후 표시 되는 **설정**을 사용 하 여 **키**및 **값**을 선택할 수 있습니다.  다음 스크린샷은 출력을 보여 줍니다.

    ![Notebook1를 사용 하 여 Spark SQL 문 실행](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Notebook1를 사용 하 여 Spark SQL 문 실행")

7. 또한 쿼리에 변수를 사용하여 Spark SQL 문을 실행할 수도 있습니다. 다음 코드 조각에서는 쿼리를 사용할 수 있는 값을 사용 하 여 쿼리에서 `Temp`변수를 정의 하는 방법을 보여 줍니다. 쿼리를 처음 실행하면 드롭다운이 변수에 지정한 값으로 자동으로 채워집니다.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    새 단락에 이 코드 조각을 붙여넣고 **Shift + Enter**를 누릅니다. 그런 다음, **임시** 드롭다운 목록에서 **65** 를 선택 합니다.

8. **가로 막대형 차트** 아이콘을 선택 하 여 표시를 변경 합니다.  그런 다음 **설정** 을 선택 하 고 다음과 같이 변경 합니다.

   * **그룹:**  **Targettemp**를 추가 합니다.  
   * **값:** 1(sp1). **날짜**를 제거 합니다.  2. **Temp_diff**를 추가 합니다.  3.  집계를 **SUM** 에서 **AVG**로 변경 합니다.  

     다음 스크린샷은 출력을 보여 줍니다.

     ![Notebook2를 사용 하 여 Spark SQL 문 실행](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Notebook2를 사용 하 여 Spark SQL 문 실행")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Notebook에서 외부 패키지 사용 방법

클러스터에 기본 제공 되지 않는 외부의 커뮤니티 제공 패키지를 사용 하도록 HDInsight의 Apache Spark 클러스터에서 Zeppelin 노트북을 구성할 수 있습니다. 사용할 수 있는 패키지의 전체 목록은 [Maven 리포지토리](https://search.maven.org/) 를 검색할 수 있습니다. 다른 소스에서 사용 가능한 패키지 목록을 가져올 수도 있습니다. 예를 들어 커뮤니티 제공 패키지의 전체 목록은 [Spark 패키지](https://spark-packages.org/)에서 사용할 수 있습니다.

이 문서에서는 Jupyter 노트북에서 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 패키지를 사용 하는 방법을 알아봅니다.

1. 인터프리터 설정을 엽니다. 오른쪽 위 모서리에서 로그인 한 사용자 이름을 선택 하 고 **인터프리터**를 선택 합니다.

    ![인터프리터 시작](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 출력")

2. **Livy2**으로 스크롤한 다음 **편집**을 선택 합니다.

    ![변경 인터프리터 run settings1](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "변경 인터프리터 run settings1")

3. 키 `livy.spark.jars.packages`로 이동 하 여 `group:id:version`형식으로 해당 값을 설정 합니다. 따라서 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 패키지를 사용하려면 키 값을 `com.databricks:spark-csv_2.10:1.4.0`으로 설정해야 합니다.

    ![변경 인터프리터 settings2](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "변경 인터프리터 settings2")

    **저장** 을 선택 하 고 **확인** 을 선택 하 여 Livy 인터프리터를 다시 시작 합니다.

4. 위에서 입력한 키 값에 도달하는 방법을 이해하려면 여기를 참조하세요.

    a. Maven Repository에서 패키지를 찾습니다. 이 문서에서는 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)를 사용 했습니다.

    b. 해당 리포지토리에서 **GroupId**, **ArtifactId** 및 **Version** 값을 수집합니다.

    ![Jupyter 노트북에서 외부 패키지 사용](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Jupyter 노트북에서 외부 패키지 사용")

    다. 콜론( **:** )으로 구분된 세 개의 값을 연결합니다.

        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Zeppelin Notebook 저장 위치

Zeppelin Notebook은 클러스터 헤드 노드에 저장됩니다. 따라서 클러스터를 삭제하면 Notebook도 삭제됩니다. 나중에 다른 클러스터에서 사용하기 위해 Notebook을 유지하려면 작업 실행을 완료 한 후 Notebook을 내보내야 합니다. 노트북을 내보내려면 아래 이미지에 표시 된 것 처럼 **내보내기** 아이콘을 선택 합니다.

![노트북 다운로드](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "노트북 다운로드")

이렇게 하면 Notebook이 다운로드 위치에 JSON 파일로 저장됩니다.

## <a name="use-shiro-to-configure-access-to-zeppelin-interpreters-in-enterprise-security-package-esp-clusters"></a>Shiro를 사용 하 여 Enterprise Security Package (ESP) 클러스터의 Zeppelin 인터프리터에 대 한 액세스를 구성 합니다.
위에서 설명한 것 처럼 `%sh` 인터프리터는 HDInsight 4.0 이상에서 지원 되지 않습니다. 또한 `%sh` 인터프리터는 셸 명령을 사용 하 여 액세스 keytabs와 같은 잠재적인 보안 문제를 도입 하므로 HDInsight 3.6 ESP 클러스터 에서도 제거 되었습니다. 이는 기본적으로 **새 메모 만들기** 를 클릭 하거나 인터프리터 UI에서 `%sh` 인터프리터를 사용할 수 없음을 의미 합니다. 

권한 있는 도메인 사용자는 `Shiro.ini` 파일을 활용 하 여 인터프리터 UI에 대 한 액세스를 제어할 수 있습니다. 따라서 이러한 사용자만 새 `%sh` 인터프리터를 만들고 새 `%sh` 인터프리터 마다 사용 권한을 설정할 수 있습니다. `shiro.ini` 파일을 사용 하 여 액세스를 제어 하려면 다음 단계를 사용 합니다.

1. 기존 도메인 그룹 이름을 사용 하 여 새 역할을 정의 합니다. 다음 예제에서 `adminGroupName`은 AAD의 권한 있는 사용자 그룹입니다. 그룹 이름에 특수 문자나 공백을 사용 하지 마십시오. `=` 뒤의 문자는이 역할에 대 한 사용 권한을 부여 합니다. `*`은 그룹에 모든 권한이 있음을 의미 합니다.

    ```
    [roles]
    adminGroupName = *
    ```

2. Zeppelin 인터프리터에 대 한 액세스에 대 한 새 역할을 추가 합니다. 다음 예제에서는 `adminGroupName`의 모든 사용자에 게 Zeppelin 인터프리터에 대 한 액세스 권한을 부여 하 고 새 인터프리터를 만들 수 있습니다. 쉼표로 구분 된 `roles[]`의 대괄호 사이에 여러 역할을 넣을 수 있습니다. 그런 다음 필요한 권한이 있는 사용자는 Zeppelin 인터프리터에 액세스할 수 있습니다.

    ```
    [urls]
    /api/interpreter/** = authc, roles[adminGroupName]
    ```

## <a name="livy-session-management"></a>Livy 세션 관리

Zeppelin Notebook에서 첫 번째 코드 단락을 실행하면 HDInsight Spark 클러스터에 새로운 Livy 세션이 만들어집니다. 이 세션은 이후에 만드는 모든 Zeppelin Notebook에서 공유됩니다. 어떤 이유로 Livy 세션이 중단 된 경우 (클러스터 재부팅 등) Zeppelin 노트북에서 작업을 실행할 수 없습니다.

이 경우 Zeppelin Notebook에서 작업 실행을 시작하기 전에 다음 단계를 수행해야 합니다.  

1. Zeppelin Notebook에서 Livy 인터프리터를 다시 시작합니다. 이렇게 하려면 오른쪽 위 모서리에서 로그인 한 사용자 이름을 선택 하 여 인터프리터 설정을 열고 **인터프리터**를 선택 합니다.

    ![인터프리터 시작](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 출력")

2. **Livy2**으로 스크롤한 다음 **다시 시작**을 선택 합니다.

    ![Livy 인터프리터를 다시 시작 합니다.](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Zeppelin 인터프리터를 다시 시작 합니다.")

3. 기존 Zeppelin Notebook에서 코드 셀을 실행합니다. 이렇게 하면 HDInsight 클러스터에 새로운 Livy 세션이 만들어집니다.

## <a name="general-information"></a>일반 정보

### <a name="validate-service"></a>서비스 유효성 검사

Ambari에서 서비스의 유효성을 검사 하려면 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary`으로 이동 합니다. 여기서 CLUSTERNAME은 클러스터의 이름입니다.

명령줄에서 서비스의 유효성을 검사 하려면 헤드 노드로 SSH를 수행 합니다. 명령 `sudo su zeppelin`를 사용 하 여 zeppelin으로 사용자를 전환 합니다. 상태 명령:

|명령 |Description |
|---|---|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh status`|서비스 상태.|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh --version`|서비스 버전입니다.|
|`ps -aux | grep zeppelin`|PID를 식별 합니다.|

### <a name="log-locations"></a>로그 위치

|서비스 |경로 |
|---|---|
|zeppelin-서버|/usr/hdp/current/zeppelin-server/|
|서버 로그|/var/log/zeppelin|
|구성 인터프리터, Shiro, site .xml, log4j|/usr/hdp/current/zeppelin-server/conf 또는/etc/zeppelin/conf|
|PID 디렉터리|/var/run/zeppelin|

### <a name="enable-debug-logging"></a>디버그 로깅 활성화

1. `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary`로 이동 합니다. 여기서 CLUSTERNAME은 클러스터의 이름입니다.

1. **CONFIGS** > **Advanced zeppelin-log4j** > **log4j_properties_content**으로 이동 합니다.

1. `log4j.appender.dailyfile.Threshold = DEBUG``log4j.appender.dailyfile.Threshold = INFO`을 수정 합니다.

1. `log4j.logger.org.apache.zeppelin.realm=DEBUG`를 추가합니다.

1. 변경 내용을 저장 하 고 서비스를 다시 시작 합니다.

## <a name="next-steps"></a>다음 단계

[개요: Azure HDInsight에서 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>시나리오

* [BI와 Apache Spark: BI 도구와 함께 HDInsight의 Spark를 사용하여 대화형 데이터 분석 수행](apache-spark-use-bi-tools.md)
* [Machine Learning과 Apache Spark: HVAC 데이터를 사용하여 건물 온도를 분석하는 데 HDInsight의 Spark 사용](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning과 Apache Spark: HDInsight의 Spark를 사용하여 식품 검사 결과 예측](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight의 Apache Spark를 사용한 웹 사이트 로그 분석](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>애플리케이션 만들기 및 실행

* [Scala를 사용하여 독립 실행형 애플리케이션 만들기](apache-spark-create-standalone-application.md)
* [Apache Livy를 사용하여 Apache Spark 클러스터에서 원격으로 작업 실행](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>도구 및 확장

* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Apache Spark Scala 애플리케이션 생성 및 제출](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Apache Spark 애플리케이션을 원격으로 디버그](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight의 Apache Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter 노트북에서 외부 패키지 사용](apache-spark-jupyter-notebook-use-external-packages.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>리소스 관리

* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](apache-spark-job-debugging.md)
