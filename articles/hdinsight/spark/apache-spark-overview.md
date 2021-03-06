---
title: "Azure HDInsight での Spark の概要 | Microsoft Docs"
description: "この記事では、HDInsight での Spark の概要と、HDInsight で Spark クラスターを使用できるさまざまなシナリオについて説明します。"
keywords: "Apache Spark とは,Spark クラスター,Spark の概要,HDInsight での Spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 82334b9e-4629-4005-8147-19f875c8774e
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/07/2017
ms.author: nitinme
ms.openlocfilehash: 5ab6adb1cc3afb5733307cfb52029f9358904859
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2017
---
# <a name="introduction-to-spark-on-hdinsight"></a>HDInsight での Spark の概要

この記事では、HDInsight での Spark の概要について説明します。 <a href="http://spark.apache.org/" target="_blank">Apache Spark</a> はビッグ データ分析アプリケーションのパフォーマンスを向上するメモリ内の処理をサポートする、オープン ソースの並列処理のフレームワークです。 HDInsight の Spark クラスターは、Azure Storage (WASB) および Azure Data Lake Store と互換性があるため、 Azure に保存されている既存データを Spark クラスターを介して簡単に処理できます。

HDInsight で Spark クラスターを作成するときは、Spark をインストールおよび構成して Azure 計算リソースを作成します。 HDInsight で Spark クラスターを作成するのにかかる時間はわずか約 10 分です。 処理するデータは Azure Storage または Azure Data Lake Store に格納されます。 [HDInsight での Azure Storage の使用](../hdinsight-hadoop-use-blob-storage.md)に関するページを参照してください。

![Spark: 一元化されたフレームワーク](./media/apache-spark-overview/hdinsight-spark-overview.png)

## <a name="spark-vs-traditional-mapreduce"></a>Spark と従来の MapReduce

Spark の速さの秘訣は何でしょうか。 Apache Spark のアーキテクチャは、従来の MapReduce をどう変えることで、データ共有のパフォーマンスを高めているのでしょうか。

![従来の MapReduce と Spark](./media/apache-spark-overview/mapreduce-vs-spark.png)

Spark には、クラスターの計算処理をインメモリで行うための基本的な要素が備わっています。 Spark ジョブは、データをメモリに読み込んでキャッシュしておいて、それを繰り返し照会することができるため、ディスクベースのシステムと比べて処理速度が大幅に向上します。 また Spark は、Scala プログラミング言語との親和性が高く、分散データ セットをローカル コレクションのように扱うことができます。 計算内容をすべて map 処理と reduce 処理に分ける必要がありません。

Spark では、データがメモリ内に存在するため、処理間のデータ共有を高速に行うことができます。 一方 Hadoop では、HDFS を介してデータを共有するため、処理に時間がかかります。

## <a name="what-is-apache-spark-on-azure-hdinsight"></a>Azure HDInsight での Apache Spark とは
Azure HDInsight の Spark クラスターでは、完全に管理された Spark サービスを利用できます。 以下の一覧は、HDInsight で Spark クラスターを作成する利点をまとめたものです。

| 機能 | Description |
| --- | --- |
| Spark クラスターの作成しやすさ |Azure Portal、Azure PowerShell、または HDInsight .NET SDK を使用すると、HDInsight に新しい Spark クラスターを数分で作成できます。 「 [HDInsight での Spark クラスターの概要](apache-spark-jupyter-spark-sql.md) |
| 使いやすさ |HDInsight の Spark クラスターには、Jupyter Notebook と Zeppelin Notebook が含まれています。 対話型のデータ処理と視覚化にこれらの Notebook を使用できます。|
| REST API |HDInsight の Spark クラスターには、ジョブの送信と監視をリモートで実行する REST API ベースの Spark ジョブ サーバーである [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server) が含まれています。 |
| Azure Data Lake Store のサポート | HDInsight の Spark クラスターは、プライマリ ストレージだけでなく、追加のストレージとして Azure Data Lake Store を使用するように構成できます (HDInsight 3.5 クラスターの場合のみ)。 Data Lake Store の詳細については、「 [Azure Data Lake Store の概要](../../data-lake-store/data-lake-store-overview.md)」を参照してください。 |
| Azure サービスとの統合 |HDInsight の Spark クラスターには、Azure Event Hubs へのコネクタが付属しています。 Spark の一部として既に使用できる [Kafka](http://kafka.apache.org/)に加えて、Event Hubs を使用してストリーミング アプリケーションを作成できます。 |
| R Server のサポート | HDInsight Spark クラスターで R Server をセットアップして、Spark クラスターによって決められた速度で分散 R 計算を実行できます。 詳細については、「 [Get started using R Server on HDInsight (HDInsight で R Server を使ってみる)](../r-server/r-server-get-started.md)」をご覧ください。 |
| サード パーティ製 IDE との統合 | HDInsight には IntelliJ IDEA や Eclipse のような IDE 向けのプラグインが用意されており、このプラグインを使用してアプリケーションを作成し、HDInsight の Spark クラスターに送信することができます。 詳細については、[Azure Toolkit for IntelliJ IDEA の使用](apache-spark-intellij-tool-plugin.md)に関するページと [Azure Toolkit for Eclipse の使用](apache-spark-eclipse-tool-plugin.md)に関するページを参照してください。|
| 同時クエリ |HDInsight の Spark クラスターでは同時クエリがサポートされます。 これにより、1 人のユーザーからの複数のクエリまたは複数のユーザーおよびアプリケーションからの複数のクエリが、同じクラスター リソースを共有できます。 |
| SSD へのキャッシュ |データのキャッシュ先を、メモリまたはクラスター ノードに取り付けられている SSD から選択できます。 メモリへのキャッシュは、クエリのパフォーマンスは最高ですが、コストがかかります。SSD へのキャッシュは、メモリ内のデータセット全体を収めるのに必要なサイズのクラスターを作成する必要なしにクエリのパフォーマンスを向上できる優れたオプションです。 |
| BI ツールとの統合 |HDInsight の Spark クラスターには、データ分析用の [Power BI](http://www.powerbi.com/) や [Tableau](http://www.tableau.com/products/desktop) などの BI ツールへのコネクタが用意されています。 |
| 読み込み済みの Anaconda ライブラリ |HDInsight の Spark クラスターには、Anaconda ライブラリが事前にインストールされています。 [Anaconda](http://docs.continuum.io/anaconda/) は、機械学習、データ分析、視覚化などのための 200 個近いライブラリを提供します。 |
| 拡張性 |作成中にクラスター内のノード数を指定できますが、ワークロードに一致するようにクラスターを拡大、縮小できます。 すべての HDInsight クラスターでは、クラスター内のノード数を変更できます。 また、すべてのデータは Azure Storage または Data Lake Store に格納されるため、Spark クラスターはデータの損失なしで削除できます。 |
| 常時サポート |HDInsight の Spark クラスターでは、エンタープライズ レベルの 24 時間無休体制のサポートと、アップタイム 99.9% の SLA が提供されます。 |

## <a name="spark-cluster-architecture"></a>Spark クラスターのアーキテクチャ

以下の図は、Spark クラスターのアーキテクチャとその動作を示したものです。

![Spark クラスターのアーキテクチャ](./media/apache-spark-overview/spark-architecture.png)

ヘッド ノードには、アプリケーションの数を管理する Spark マスターがあり、アプリケーションは Spark ドライバーにマッピングされます。 Spark マスターは、さまざまな方法ですべてのアプリケーションを管理します。 アプリケーションにワーカー ノード リソースを割り当てるのはクラスター マネージャーです。Spark は、Mesos、YARN、Spark のいずれかのクラスター マネージャーにデプロイすることができます。 HDInsight では、YARN クラスター マネージャーを使って Spark が実行されます。 クラスターのリソースは、HDInsight の Spark マスターによって管理されます。 つまり、ワーカー ノード上の塞がっているリソース (メモリなど) と空いているリソースは、Spark マスターが把握しています。

ドライバーは、ユーザーの main 関数を実行し、ワーカー ノードでさまざまな並列処理を実行した後、 その結果を収集します。 ワーカー ノードによるデータの読み取りと書き込みは、Hadoop 分散ファイル システム (HDFS) との間で行われます。 また、ワーカー ノードは、変換後のデータを Resilient Distributed Dataset (RDD) としてメモリ内にキャッシュします。

Spark マスターに作成されたアプリケーションには、Spark マスターによってリソースが割り当てられ、Spark ドライバーと呼ばれる実行が作成されます。 さらに、Spark ドライバーによって SparkContext が作成され、RDD の作成が開始されます。 RDD のメタデータは、Spark ドライバーに格納されます。

Spark ドライバーは Spark マスターに接続し、ワーカー ノードの Executor プロセス内で実行される個々のタスクの有向グラフ (DAG) にアプリケーションを変換する処理を行います。 アプリケーションはそれぞれ独自の Executor プロセスを取得し、そのプロセスが、アプリケーション全体が終了するまで稼働し続けながら、複数のスレッドでタスクを実行します。

## <a name="what-are-the-use-cases-for-spark-on-hdinsight"></a>HDInsight での Spark の使用例
HDInsight の Spark クラスターでは、以下に挙げる主なシナリオを実現できます。

### <a name="interactive-data-analysis-and-bi"></a>対話型のデータ分析と BI
[チュートリアルを見る](apache-spark-use-bi-tools.md)

HDInsight の Apache Spark では、Azure Storage または Azure Data Lake Store にデータが格納されます。 ビジネス エキスパートや重要な意思決定者は、そのデータを分析してレポートを作成し、分析されたデータから Microsoft Power BI を使用して対話型レポートを作成できます。 アナリストはクラスター ストレージ内の非構造化データや半構造化データから作業を開始し、そのデータのスキーマを Notebook を使用して定義してから、Microsoft Power BI を使用してデータ モデルを作成することができます。 また、HDInsight の Spark クラスターでは Tableau などの多数のサードパーティ製 BI ツールもサポートされるので、データ アナリストやビジネス エキスパート、重要な意思決定者にとって理想的なプラットフォームとなっています。

### <a name="spark-machine-learning"></a>Spark Machine Learning
[チュートリアルを見る: HVAC データを使用して建物の温度を予測する](apache-spark-ipython-notebook-machine-learning.md)

[チュートリアルを見る: 食品検査の結果を予測する](apache-spark-machine-learning-mllib-ipython.md)

Apache Spark には、Spark を基に作成された機械学習ライブラリである [MLlib](http://spark.apache.org/mllib/) が付属し、HDInsight の Spark クラスターから使用できます。 HDInsight の Spark クラスターには、機械学習用のさまざまなパッケージを含む Python ディストリビューションである Anaconda も含まれています。 これと Jupyter および Zeppelin Notebook の組み込みサポートを組み合わせることにより、機械学習アプリケーションを作成するうえで最高の環境が得られます。

### <a name="spark-streaming-and-real-time-data-analysis"></a>Spark のストリーミングおよびリアルタイム データ分析
[チュートリアルを見る](apache-spark-eventhub-streaming.md)

HDInsight の Spark クラスターには、リアルタイム分析ソリューションを構築するための豊富なサポートが用意されています。 Spark には既に Kafka、Flume、Twitter、ZeroMQ、TCP ソケットなどの多数のソースからデータを取り込むためのコネクタがありますが、HDInsight の Spark では Azure Event Hubs からデータを取り込むためのファーストクラスのサポートが追加されます。 Event Hubs は、Azure で最も広く使用されているキュー サービスです。 Event Hubs 向けのサポートがすぐに利用できることから、HDInsight の Spark クラスターは、リアルタイム分析パイプラインを構築するうえで理想的なプラットフォームです。

## <a name="next-steps"></a>Spark クラスターに含まれるコンポーネント
HDInsight の Spark クラスターには、クラスターで使用できる以下のコンポーネントが既定で含まれています。

* [Spark Core](https://spark.apache.org/docs/1.5.1/)。 Spark Core、Spark SQL、Spark ストリーミング API、GraphX、MLlib が含まれます。
* [Anaconda](http://docs.continuum.io/anaconda/)
* [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter Notebook](https://jupyter.org)
* [Zeppelin Notebook](http://zeppelin-project.org/)

HDInsight の Spark クラスターでは、Microsoft Power BI や Tableau などの BI ツールから HDInsight の Spark クラスターに接続するための [ODBC ドライバー](http://go.microsoft.com/fwlink/?LinkId=616229)も提供されます。

## <a name="where-do-i-start"></a>どこから始めるか
まずは HDInsight で Spark クラスターを作成します。 [HDInsight Linux での Spark クラスターの作成と Jupyter を使用した対話型クエリの実行に関するクイックスタート ガイド](apache-spark-jupyter-spark-sql.md)のページを参照してください。 

## <a name="next-steps"></a>次のステップ
### <a name="scenarios"></a>シナリオ
* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](apache-spark-ipython-notebook-machine-learning.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](apache-spark-machine-learning-mllib-ipython.md)
* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](apache-spark-eventhub-streaming.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark Scala アプリケーションを作成し、送信する](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark アプリケーションをリモートでデバッグする](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](apache-spark-job-debugging.md)
* [Azure HDInsight における Apache Spark の既知の問題](apache-spark-known-issues.md)。
