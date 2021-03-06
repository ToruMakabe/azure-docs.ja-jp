---
title: "Graph API を使用した Azure Cosmos DB Node.js アプリケーションの構築 | Microsoft Docs"
description: "Azure Cosmos DB への接続とデータの照会に使用できる Node.js コード サンプルについて説明します"
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 08/29/2017
ms.author: denlee
ms.openlocfilehash: 361f63141a8bf3f901eee6c93742f1a7fdc4348f
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2017
---
# <a name="azure-cosmos-db-build-a-nodejs-application-by-using-graph-api"></a>Azure Cosmos DB: Graph API を使用した Node.js アプリケーションの構築

Azure Cosmos DB は、Microsoft が提供するグローバルに分散されたマルチモデル データベース サービスです。 Azure Cosmos DB の中核をなすグローバルな分散と水平方向のスケール機能を利用して、ドキュメント、キー/値、およびグラフ データベースをすばやく作成およびクエリできます。 

このクイック スタートの記事では、Azure Portal を使用した Graph API (プレビュー) 用の Azure Cosmos DB アカウント、データベース、およびグラフの作成方法を説明します。 続いてオープンソース [Gremlin Node.js](https://www.npmjs.com/package/gremlin) ドライバーを使用して、コンソール アプリを構築し実行します。

## <a name="prerequisites"></a>前提条件

このサンプルを実行する前に、以下の前提条件を満たしている必要があります。
* [Node.js](https://nodejs.org/en/) バージョン v0.10.29 以降
* [Git](http://git-scm.com/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>How to create a DocumentDB account (DocumentDB アカウントの作成方法)

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>グラフの追加

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

GitHub から Graph API アプリの複製を作成し、接続文字列を設定して実行します。 プログラムでデータを処理することが非常に簡単であることがわかります。 

1. Git ターミナル ウィンドウ (Git Bash など) を開き、`cd` コマンドで作業ディレクトリに移動します。

2. 次のコマンドを実行して、サンプル リポジトリを複製します。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. Visual Studio でソリューション ファイルを開きます。 

## <a name="review-the-code"></a>コードの確認

アプリで何が行われているかを簡単に確認してみましょう。 `app.js` ファイルを開くと、以下のコード行が表示されます。 

* Gremlin クライアントが作成されます。

    ```nodejs
    const client = Gremlin.createClient(
        443, 
        config.endpoint, 
        { 
            "session": false, 
            "ssl": true, 
            "user": `/dbs/${config.database}/colls/${config.collection}`,
            "password": config.primaryKey
        });
    ```

  構成はすべて、[次のセクション](#update-your-connection-string)で編集する `config.js` に含まれています。

* さまざまな Gremlin 操作を実行する一連の関数が定義されています。 その一例を次に示します。

    ```nodejs
    function addVertex1(callback)
    {
        console.log('Running Add Vertex1'); 
        client.execute("g.addV('person').property('id', 'thomas').property('firstName', 'Thomas').property('age', 44).property('userid', 1)", { }, (err, results) => {
          if (err) callback(console.error(err));
          console.log("Result: %s\n", JSON.stringify(results));
          callback(null)
        });
    }
    ```

* 各関数は、Gremlin クエリ文字列パラメーターが指定された `client.execute` メソッドを実行します。 `g.V().count()` の実行方法の例を次に示します。

    ```nodejs
    console.log('Running Count'); 
    client.execute("g.V().count()", { }, (err, results) => {
        if (err) return console.error(err);
        console.log(JSON.stringify(results));
        console.log();
    });
    ```

* このファイルの最後で、`async.waterfall()` メソッドを使用してすべてのメソッドが呼び出されます。 すべてのメソッドは順に実行されます。

    ```nodejs
    try{
        async.waterfall([
            dropGraph,
            addVertex1,
            addVertex2,
            addEdge,
            countVertices
            ], finish);
    } catch(err) {
        console.log(err)
    }
    ```


## <a name="update-your-connection-string"></a>接続文字列の更新

1. config.js ファイルを開きます。 

2. config.js の `config.endpoint` キーに、Azure Portal の **[概要]** ページに表示される **[Gremlin URI]** の値を設定します。 

    `config.endpoint = "GRAPHENDPOINT";`

    ![Azure Portal の [キー] ブレードでアクセス キーを表示およびコピーする](./media/create-graph-nodejs/gremlin-uri.png)

   **[Gremlin URI]** の値が空である場合は、ポータルの **[キー]** ページで値を生成できます。 そのためには、**[URI]** の値を使用し、https:// を削除し、documents を graphs に変更してください。

   Gremlin エンドポイントは、`mygraphdb.graphs.azure.com` のように、プロトコル/ポート番号が付いていないホスト名のみにする必要があります (`https://mygraphdb.graphs.azure.com` や `mygraphdb.graphs.azure.com:433` は不可)。

3. config.js の config.primaryKey の値に、Azure Portal の **[キー]** ページに表示される **[Primary Key]\(主キー\)** の値を設定します。 

    `config.primaryKey = "PRIMARYKEY";`

   ![Azure Portal の [キー] ブレード](./media/create-graph-nodejs/keys.png)

4. データベース名とグラフ (コンテナー) 名を config.database と config.collection の値として入力します。 

たとえば、完成した config.js ファイルの内容は次のようになります。

```nodejs
var config = {}

// Note that this must not have HTTPS or the port number
config.endpoint = "testgraphacct.graphs.azure.com";
config.primaryKey = "Pams6e7LEUS7LJ2Qk0fjZf3eGo65JdMWHmyn65i52w8ozPX2oxY3iP0yu05t9v1WymAHNcMwPIqNAEv3XDFsEg==";
config.database = "graphdb"
config.collection = "Persons"

module.exports = config;
```

## <a name="run-the-console-app"></a>コンソール アプリの実行

1. ターミナル ウィンドウを開き、`cd` コマンドを使用して、プロジェクトに含まれる package.json ファイルのインストール ディレクトリに移動します。

2. `npm install` を実行し、`gremlin`など、必要な npm モジュールをインストールします。

3. ターミナルで `node app.js` を実行し、node アプリケーションを起動します。

## <a name="browse-with-data-explorer"></a>データ エクスプローラーで閲覧する

ここで、Azure Portal のデータ エクスプローラーに戻り、新しいグラフ データの表示、クエリ実行、変更、使用を行うことができます。

データ エクスプローラーで新しいデータベースが **[グラフ]** ウィンドウに表示されます。 データベース、コレクションの順に展開し、**[グラフ]** を選択します。

**[フィルターの適用]** を選択すると、サンプル アプリで生成されたデータが、**[グラフ]** タブ内にある隣のウィンドウに表示されます。

試しに `g.V()` に「`.has('firstName', 'Thomas')`」と入力して、フィルターをテストします。 値の大文字と小文字が区別されることに注意してください。

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-your-resources"></a>リソースをクリーンアップする

このアプリの使用を続ける予定がない場合は、次の手順を実行して、この記事で作成したすべてのリソースを削除します。 

1. Azure Portal の左側のナビゲーション メニューで、**[リソース グループ]** を選択します。 次に、作成したリソースの名前を選択します。 

2. リソース グループ ページで **[削除]** を選択します。 削除するリソースの名前を入力し、**[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

この記事では、Azure Cosmos DB アカウントを作成し、データ エクスプローラーを使用してグラフを作成し、アプリを実行する方法を説明しました。 これで Gremlin を使用して、さらに複雑なクエリを作成し、強力なグラフ トラバーサル ロジックを実装できます。 

> [!div class="nextstepaction"]
> [Gremlin を使用したクエリ](tutorial-query-graph.md)
