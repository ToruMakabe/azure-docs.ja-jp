---
title: "Azure HDInsight 上の Apache Spark クラスターのリソースを管理する | Microsoft Docs"
description: "Azure HDInsight で Spark クラスターのリソースを管理してパフォーマンスを向上させる方法について説明します。"
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 9da7d4e3-458e-4296-a628-77b14643f7e4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: jgao
ms.openlocfilehash: 3c98150239134c686ac8edebd3c477bec8be7dd8
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/29/2017
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Azure HDInsight での Apache Spark クラスターのリソースの管理 

この記事では、Ambari UI、YARN UI、Spark History Server など、Spark クラスターに関連付けられているインターフェイスにアクセスする方法を説明します。 クラスターの構成をチューニングしてパフォーマンスを最適化する方法についても取り上げます。

**前提条件:**

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* HDInsight での Apache Spark クラスター。 手順については、 [Azure HDInsight での Apache Spark クラスターの作成](apache-spark-jupyter-spark-sql.md)に関するページを参照してください。

## <a name="how-do-i-launch-the-ambari-web-ui"></a>Ambari Web UI の起動方法
1. [Azure Portal](https://portal.azure.com/) のスタート画面で Spark クラスターのタイルをクリックします (スタート画面にピン留めしている場合)。 **[すべて参照]** > **[HDInsight クラスター]** でクラスターに移動することもできます。
2. Spark クラスターについては、**[ダッシュボード]** をクリックします。 入力を求められたら、Spark クラスターの管理者資格情報を入力します。

    ![Ambari を起動する](./media/apache-spark-resource-manager/hdinsight-launch-cluster-dashboard.png "Resource Manager を開始する")
3. これにより、スクリーンショットのように、Ambari Web UI が起動されます。

    ![Ambari Web UI](./media/apache-spark-resource-manager/ambari-web-ui.png "Ambari Web UI")   

## <a name="how-do-i-launch-the-spark-history-server"></a>Spark History Server の起動方法
1. [Azure Portal](https://portal.azure.com/) のスタート画面で Spark クラスターのタイルをクリックします (スタート画面にピン留めしている場合)。
2. クラスター ブレードの **[クイック リンク]** で、**[クラスター ダッシュボード]** をクリックします。 **[クラスター ダッシュボード]** ブレードで **[Spark History Server]** をクリックします。

    ![Spark History Server](./media/apache-spark-resource-manager/launch-history-server.png "Spark History Server")

    入力を求められたら、Spark クラスターの管理者資格情報を入力します。

## <a name="how-do-i-launch-the-yarn-ui"></a>Yarn UI の起動方法
Spark クラスターで現在実行されているアプリケーションを監視するには、YARN UI を使用することができます。

1. クラスター ブレードから **[クラスター ダッシュボード]** をクリックし、**[YARN]** をクリックします。

    ![Launch YARN UI](./media/apache-spark-resource-manager/launch-yarn-ui.png)

   > [!TIP]
   > Ambari UI から YARN UI を起動してもかまいません。 Ambari UI を起動するには、クラスター ブレードから **[クラスター ダッシュボード]** をクリックし、**[HDInsight クラスター ダッシュボード]** をクリックします。 Ambari UI から **[YARN]**、**[クイック リンク]** の順にクリックし、アクティブな Resource Manager をクリックして、**[Resource Manager UI]** をクリックします。
   >
   >

## <a name="what-is-the-optimum-cluster-configuration-to-run-spark-applications"></a>Spark アプリケーションを実行するための最適なクラスター構成とは
アプリケーションの要件に応じて Spark を構成するための主要なパラメーターは、`spark.executor.instances`、`spark.executor.cores`、`spark.executor.memory` の 3 つです。 Executor は、Spark アプリケーション用に起動されるプロセスです。 ワーカー ノードで動作し、アプリケーションのタスクを実行する役割を担います。 それぞれのクラスターで使用される Executor の既定の数とサイズは、ワーカー ノードの数とワーカー ノードのサイズに基づいて計算され、 この情報はクラスターのヘッド ノード上の `spark-defaults.conf` に保存されます。

3 つの構成パラメーターは、クラスター レベルで (クラスター上で動作するすべてのアプリケーションに対して) 構成できるほか、個々のアプリケーションに対して指定することもできます。

### <a name="change-the-parameters-using-ambari-ui"></a>Ambari UI を使用したパラメーターの変更
1. Ambari UI から **[Spark]**、**[Configs]\(構成\)** の順にクリックし、**[Custom spark-defaults]** を展開します。

    ![Set parameters using Ambari](./media/apache-spark-resource-manager/set-parameters-using-ambari.png)
2. 一連の既定値は、クラスター上で 4 つの Spark アプリケーションを同時実行することを想定して決められています。 これらの値は、次に示したようにユーザー インターフェイスから変更できます。

    ![Set parameters using Ambari](./media/apache-spark-resource-manager/set-executor-parameters.png)
3. **[保存]** をクリックして構成の変更を保存します。 変更に関係したサービスをすべて再開するよう求めるメッセージがページの上部に表示されます。 **[Restart (再開)]**をクリックします。

    ![Restart services](./media/apache-spark-resource-manager/restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Jupyter Notebook で実行するアプリケーションのパラメーター変更
Jupyter Notebook で実行しているアプリケーションについては、 `%%configure` マジックを使用して構成に変更を加えることができます。 そのような変更は、できればアプリケーションの冒頭で、1 つ目のコード セルを実行する前に記述してください。 これを行うと、Livy セッションの作成時に、確実に構成が適用されます。 アプリケーションの終盤で構成の変更が生じた場合は、 `-f` パラメーターを使用する必要があります。 ただしその場合、アプリケーションのすべての進捗が失われます。

以下のスニペットは、Jupyter で実行しているアプリケーションの構成を変更する方法を示しています。

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

例の列で示されているように、構成パラメーターは JSON 文字列として渡し、マジックの後の次の行に置く必要があります。

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>spark-submit を使用して送信されたアプリケーションのパラメーター変更
次のコマンドは、 `spark-submit`を使用して送信されたバッチ アプリケーションの構成パラメーターを変更する例です。

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>cURL を使用して送信されたアプリケーションのパラメーター変更
次のコマンドは、cURL を使用して送信されたバッチ アプリケーションの構成パラメーターを変更する例です。

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="how-do-i-change-these-parameters-on-a-spark-thrift-server"></a>これらのパラメーターを Spark Thrift サーバーで変更する方法
Spark Thrift サーバーを使用すると、Spark クラスターに JDBC/ODBC でアクセスし、Spark SQL クエリを実行することができます。 Power BI や Tableau などのツールは、 ODBC プロトコルを使用して Spark Thrift サーバーとやり取りし、Spark アプリケーションとして Spark SQL クエリを実行します。 Spark クラスターを作成すると、Spark Thrift サーバーの 2 つのインスタンスが起動されます (ヘッド ノードごとに 1 つ)。 YARN UI には、各 Spark Thrift サーバーが Spark アプリケーションとして表示されます。

Spark Thrift サーバーでは、Spark の Dynamic Executor Allocation が使用されるため、 `spark.executor.instances` は使用されません。 代わりに、Executor 数の指定に `spark.dynamicAllocation.minExecutors` と `spark.dynamicAllocation.maxExecutors` が使用されます。 Executor のサイズ変更には、構成パラメーターとして `spark.executor.cores` と `spark.executor.memory` が使用されます。 次の手順に示すように、これらのパラメーターは変更できます。

* `spark.dynamicAllocation.minExecutors`、`spark.dynamicAllocation.maxExecutors`、`spark.executor.memory` の各パラメーターを更新するには、**Advanced spark-thrift-sparkconf** カテゴリを展開します。

    ![Configure Spark thrift server](./media/apache-spark-resource-manager/spark-thrift-server-1.png)    
* `spark.executor.cores` パラメーターを更新するには、**Custom spark-thrift-sparkconf** カテゴリを展開します。

    ![Configure Spark thrift server](./media/apache-spark-resource-manager/spark-thrift-server-2.png)

### <a name="how-do-i-change-the-driver-memory-of-the-spark-thrift-server"></a>Spark Thrift サーバーのドライバーのメモリを変更する方法
ヘッド ノードの RAM の合計サイズが 14 GB を超える場合、Spark Thrift サーバーのドライバーのメモリはヘッド ノードの RAM サイズの 25% に構成されます。 ドライバーのメモリ構成は、以下のように Ambari UI を使用して変更できます。

* Ambari UI から **[Spark]**、**[Configs (構成)]** の順にクリックし、**[Advanced spark-env]** を展開して、**[spark_thrift_cmd_opts]** の値を指定します。

    ![Configure Spark thrift server RAM](./media/apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="i-do-not-use-bi-with-spark-cluster-how-do-i-take-the-resources-back"></a>Spark クラスターで BI は使用しません。 リソースを取り戻すにはどうすればよいですか?
Spark の動的割り当てを使用するため、Thrift サーバーから利用できるリソースは、2 つのアプリケーション マスターのリソースだけです。 これらのリソースの領域を解放するには、クラスター上で実行されている Thrift サーバー サービスを停止する必要があります。

1. Ambari UI の左ペインで **[Spark]**をクリックします。
2. 次のページで、 **[Spark Thrift Servers]**をクリックします。

    ![Restart thrift server](./media/apache-spark-resource-manager/restart-thrift-server-1.png)
3. Spark Thrift サーバーが実行されている 2 つのヘッド ノードが表示されます。 いずれかのヘッド ノードをクリックしてください。

    ![Restart thrift server](./media/apache-spark-resource-manager/restart-thrift-server-2.png)
4. そのヘッド ノードで実行されているすべてのサービスが次のページに一覧表示されます。 一覧内の Spark Thrift サーバーの横にあるドロップダウン ボタンをクリックし、 **[Stop (停止)]**をクリックします。

    ![Restart thrift server](./media/apache-spark-resource-manager/restart-thrift-server-3.png)
5. もう一方のヘッド ノードについても同じ手順を繰り返します。

## <a name="my-jupyter-notebooks-are-not-running-as-expected-how-can-i-restart-the-service"></a>Jupyter Notebook が期待どおりに実行されていません。 サービスを再起動するには、どうすればよいですか?
上の手順に従って、Ambari Web UI を起動します。 左側のナビゲーション ウィンドウで、**[Jupyter]**、**[Service Actions]**、**[Restart All]** の順にクリックします。 これで、すべてのヘッドノードで Jupyter サービスが開始されます。

    ![Restart Jupyter](./media/apache-spark-resource-manager/restart-jupyter.png "Restart Jupyter")

## <a name="how-do-i-know-if-i-am-running-out-of-resources"></a>リソースが残り少なくなっているかどうかはどうすればわかりますか?
上の手順に従って、Yarn UI を起動します。 画面上部の [Cluster Metrics] テーブルで、**[Memory Used]** と **[Memory Total]** の列の値を確認します。 2 つの値が非常に近い場合は、次のアプリケーションを開始するためのリソースが十分でない可能性があります。 **[VCores Used]** と **[VCores Total]** の列でも同じことが言えます。 また、メイン ビューに、**[ACCEPTED]** の状態のまま、**[RUNNING]** にも **[FAILED]** の状態にも移行していないアプリケーションがある場合も、開始するためのリソースが十分でないことを示している可能性があります。

    ![Resource Limit](./media/apache-spark-resource-manager/resource-limit.png "Resource Limit")

## <a name="how-do-i-kill-a-running-application-to-free-up-resource"></a>リソースを解放するために実行中のアプリケーションを強制終了するにはどうすればよいですか?
1. Yarn UI の左側のパネルで**[Running]** をクリックします。 実行中のアプリケーションの一覧から、強制終了するアプリケーションを決定し、**[ID]** をクリックします。

    ![App1 の強制終了](./media/apache-spark-resource-manager/kill-app1.png "App1 の強制終了")

2. 右上隅の **[Kill Application]** をクリックして、**[OK]** をクリックします。

    ![App2 の強制終了](./media/apache-spark-resource-manager/kill-app2.png "App2 の強制終了")

## <a name="see-also"></a>関連項目
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>データ アナリスト向け

* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](apache-spark-ipython-notebook-machine-learning.md)
* [Spark with Machine Learning: Use Spark in HDInsight to predict food inspection results (Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する)](apache-spark-machine-learning-mllib-ipython.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](apache-spark-custom-library-website-log-analysis.md)
* [HDInsight での Spark を使用した Application Insight テレメトリ データ分析](apache-spark-analyze-application-insight-logs.md)
* [分散型深層学習用に Azure HDInsight Spark で Caffe を使用する](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>Spark 開発者向け

* [Scala を使用してスタンドアロン アプリケーションを作成する](apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](apache-spark-livy-rest-interface.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark Scala アプリケーションを作成し、送信する](apache-spark-intellij-tool-plugin.md)
* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](apache-spark-eventhub-streaming.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark アプリケーションをリモートでデバッグする](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](apache-spark-jupyter-notebook-install-locally.md)
