---
title: "Azure Analysis Services のスケールアウト | Microsoft Docs"
description: "Azure Analysis Services サーバーをスケールアウトによってレプリケートします"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: owend
ms.openlocfilehash: a97f9648efef7f07659110d720c200dcd0a241a9
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2017
---
# <a name="azure-analysis-services-scale-out"></a>Azure Analysis Services のスケールアウト

スケールアウトによって、クエリ プール内の複数の*クエリ レプリカ*間でクライアント クエリを分散して、負荷の高いクエリのワークロードを実行している間の応答時間を短縮できます。 クエリ プールから処理を分離して、クライアント クエリが処理操作の悪影響を受けないようにすることもできます。 スケールアウトを構成するには Azure Portal から行うか、または Analysis Services の REST API を使用します。

## <a name="how-it-works"></a>動作のしくみ

一般的なサーバーのデプロイでは、1 つのサーバーで、処理サーバーとクエリ サーバーの両方の役割を果たします。 サーバー上のモデルに対するクライアント クエリの数が、加入しているサーバー プランのクエリ プロセッシング ユニット (QPU) を超過した場合、またはモデル処理が負荷の高いクエリのワークロードと同時に発生した場合、パフォーマンスが低下することがあります。 

スケールアウトすることで、クエリ プールを 1 つと、クエリ レプリカを追加で最大 7 つ (サーバーを入れて合計 8 つ) まで作成可能です。 重要な場面では、QPU の需要を満たせるようにクエリ レプリカの数をスケーリングできます。また、処理サーバーはいつでもクエリ プールから分離できます。 

クエリ プール内にあるクエリ レプリカの数に関わらず、ワークロードの処理はクエリ レプリカ間で分散されません。 単一のサーバーが処理サーバーとして機能します。 クエリ レプリカは、クエリ プール内の各レプリカ間で同期されているモデルに対するクエリにのみ機能します。 

処理操作が完了したときに、処理サーバーとクエリ レプリカ サーバーの間で同期が行われる必要があります。 処理操作を自動化する場合は、処理操作が問題なく完了した際に同期操作が行われるよう構成することが重要です。 同期は、ポータルで手動で実行するか、PowerShell または REST API を使用して実行することができます。

> [!NOTE]
> スケールアウトは Standard 価格レベルのサーバーで使用できます。 各クエリ レプリカは、お使いのサーバーと同じ料金で課金されます。

> [!NOTE]
> スケールアウトでは、サーバーで使用可能なメモリの量は増えません。 メモリを増やすには、プランをアップグレードする必要があります。

## <a name="monitor-qpu-usage"></a>QPU の使用状況を監視する

 お使いのサーバーでスケールアウトが必要かどうかを判断するには、Azure Portal でメトリックを使用して監視します。 QPU が頻繁に上限に達するようであれば、モデルに対するクエリの数が、お使いのプランに設定されている QPU の制限を超過していることになります。 クエリ スレッド プール キュー内のキュー数が、利用可能な QPU を超過した場合も、クエリ プールのジョブ キュー長のメトリックは増加します。 詳細については、[サーバー メトリックの監視](analysis-services-monitor.md)に関する記事をご覧ください。

## <a name="configure-scale-out"></a>スケールアウトを構成する

### <a name="in-azure-portal"></a>Azure Portal

1. ポータルで、**[スケールアウト]** をクリックします。スライダーを使用してクエリ レプリカ サーバーの数を選択します。 選択したレプリカの数は既存のサーバーの数に追加されます。

2. **[処理中のサーバーと照会中のプールを分けてください]** で、[はい] を選択してクエリ サーバーから処理中のサーバーを除外します。

   ![スケールアウト スライダー](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. **[保存]** をクリックして新しいクエリ レプリカ サーバーをプロビジョニングします。 

プライマリ サーバー上の表形式モデルはレプリカ サーバーと同期されます。 同期が完了すると、クエリ プールがレプリカ サーバー間で受信クエリの分散を開始します。 


## <a name="synchronization"></a>同期 

新しいクエリ レプリカをプロビジョニングするときに、Azure Analysis Services はすべてのレプリカ間でモデルを自動的にレプリケートします。 ポータルまたは REST API を使用して、手動で同期を行うこともできます。 モデルを処理する際は、クエリ レプリカ全体で更新が同期されるよう、同期を実行してください。

### <a name="in-azure-portal"></a>Azure Portal

**[概要]** > 対象のモデル >**[Synchronize model]\(モデルの同期\)** の順に選択します。

![スケールアウト スライダー](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API
**sync** 操作を使用します。

#### <a name="synchronize-a-model"></a>モデルを同期する   
`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>同期状態を取得する  
`GET https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

### <a name="powershell"></a>PowerShell
PowerShell から sync を実行するため、最新の 5.01 以上の AzureRM モジュールに[更新](https://github.com/Azure/azure-powershell/releases)します。 [Sync-AzureAnalysisServicesInstance](https://docs.microsoft.com/en-us/powershell/module/azurerm.analysisservices/sync-azureanalysisservicesinstance) を使用します。

## <a name="connections"></a>接続

サーバーの [概要] ページに、サーバー名が 2 つ表示されます。 サーバーのスケールアウトをまだ構成していない場合は、両方のサーバー名は同じものとして機能します。 サーバーのスケールアウトを構成したら、接続の種類に応じて適切なサーバー名を指定する必要があります。 

Power BI Desktop、Excel、カスタム アプリなどのエンドユーザー クライアントの接続については、**[サーバー名]** を使用します。 

SSMS、SSDT、および PowerShell、Azure 関数アプリ、AMO の接続文字列については、**[管理サーバー名]** を使用します。 管理サーバー名は特殊な `:rw` (読み取り/書き込み) 修飾子を含みます。 すべての処理操作は管理サーバーで発生します。

![サーバー名](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="related-information"></a>関連情報

[サーバー メトリックの監視](analysis-services-monitor.md)   
[Azure Analysis Services を管理する](analysis-services-manage.md) 

