---
title: "Azure Search のインデクサー | Microsoft Docs"
description: "Azure SQL Database、Azure Cosmos DB、または Azure Storage をクロールして検索可能なデータを抽出し、Azure Search インデックスを作成します。"
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 10/17/2017
ms.author: heidist
ms.openlocfilehash: c1d393064313ea65d5226378172530115c338f86
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2017
---
# <a name="indexers-in-azure-search"></a>Azure Search のインデクサー
> [!div class="op_single_selector"]
>
> * [概要](search-indexer-overview.md)
> * [ポータル](search-import-data-portal.md)
> * [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
> * [Azure Cosmos DB](search-howto-index-documentdb.md)
> * [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
> * [Azure Table Storage](search-howto-indexing-azure-tables.md)
>

Azure Search の *インデクサー* は、検索可能なデータとメタデータを外部データ ソースから抽出し、インデックスとデータ ソース間のフィールドのマッピングに基づいてインデックスを作成するクローラーです。 この方法は、インデックスにデータをプッシュするコードを記述することなく、サービスがデータをプルするため、「プル モデル」と呼ばれることもあります。

インデクサーは、データ ソースの種類またはプラットフォームに基づきます。Azure 上の SQL Server、Cosmos DB、Azure Table Storage、Blob Storage などを対象としたインデクサーがあります。

データ取り込みの唯一の手段としてインデクサーを使用したり、インデクサーの使用を含む手法を組み合わせて使用したりして、インデックス内のフィールドの一部だけを読み込むことができます。

インデクサーは、オンデマンドで実行することも、または 15 分ごとに実行される定期的なデータ更新スケジュールで実行することもできます。 より頻繁に更新するには、Azure Search と外部データ ソースの両方のデータを同時に更新するプッシュ モデルが必要です。

## <a name="approaches-for-creating-and-managing-indexers"></a>インデクサーの作成と管理の方法

インデクサーの作成と管理は、次の方法で行うことができます。

* [Portal のデータのインポート ウィザード](search-get-started-portal.md)
* [サービス REST API](https://msdn.microsoft.com/library/azure/dn946891.aspx)
* [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx)

最初は、新しいインデクサーがプレビュー機能として発表されます。 プレビュー機能は、まず API (REST および .NET) に導入され、その後、一般公開を経てポータルに統合されます。 新しいインデクサーを評価する場合は、コードの作成を検討してください。

## <a name="basic-configuration-steps"></a>基本的な構成手順
インデクサーで実行できる機能は、データ ソースごとに異なります。 そのためインデクサーやデータ ソースの構成には、インデクサーの種類ごとに異なる点があります。 しかし基本的な成り立ちと要件は、すべてのインデクサーに共通です。 以降、すべてのインデクサーに共通の手順について取り上げます。

### <a name="step-1-create-a-data-source"></a>手順 1: データ ソースを作成する
インデクサーは、接続文字列や場合によっては資格情報などの情報を保持している "*データ ソース*" からデータをプルします。 現在、次のデータ ソースがサポートされています。

* [Azure SQL Database または Azure 仮想マシン上の SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-documentdb.md)
* 選択したコンテンツ タイプの [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)

データ ソースは、それを使用するインデクサーとは別に構成および管理されます。これは、1 つのデータ ソースを複数のインデクサーで使用して、一度に複数のインデックスを読み込めることを意味します。

### <a name="step-2-create-an-index"></a>手順 2: インデックスを作成する
インデクサーは、データの取り込みに関連したいくつかのタスクを自動化しますが、そこにはインデックスの作成は含まれていません。 前提条件として、定義済みのインデックスが必要です。加えてそのフィールドは、外部データ ソース内のフィールドと一致している必要があります。 インデックス構築の詳細については、[インデックスの作成 (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) に関するページを参照してください。 フィールドの関連付けについては、「[Azure Search インデクサーのフィールド マッピング](search-indexer-field-mappings.md)」を参照してください。

### <a name="step-3-create-and-schedule-the-indexer"></a>手順 3: インデクサーを作成してスケジュールを設定する
インデクサーの定義とは、インデックスやデータ ソース、スケジュールを指定する構文です。 インデクサーは、データ ソースが同じサブスクリプションに属していれば、他のサービスのデータ ソースであっても参照できます。 インデクサー構築の詳細については、「 [Create Indexer (Azure Search REST API) (インデクサーの作成 (Azure Search REST API))](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)」を参照してください。

## <a name="next-steps"></a>次のステップ
基本的な概念の説明は以上です。次のステップとしてデータ ソースの種類ごとの要件とタスクを確認してください。

* [Azure SQL Database または Azure 仮想マシン上の SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-documentdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [Azure Search BLOB インデクサーを使用した CSV BLOB のインデックス作成](search-howto-index-csv-blobs.md)
* [Azure Search BLOB インデクサーを使用した JSON BLOB のインデックス作成](search-howto-index-json-blobs.md)
