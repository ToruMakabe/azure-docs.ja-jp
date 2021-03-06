---
title: "Azure での Windows VM のメンテナンス通知の処理 | Microsoft Docs"
description: "Azure で実行されている Windows 仮想マシンのメンテナンス通知を表示し、セルフサービス メンテナンスを開始します。"
services: virtual-machines-windows
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: zivr
ms.openlocfilehash: 80c029866f3d28712be823692f3bf4ce6e210405
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2017
---
# <a name="handling-planned-maintenance-notifications-for-windows-virtual-machines"></a>Windows 仮想マシンに対する計画メンテナンスの通知の処理

Azure は、定期的に更新を行い、仮想マシンのホスト インフラストラクチャの信頼性、パフォーマンス、セキュリティの向上に努めています。 更新とは、ホスティング環境の修正、ハードウェアのアップグレードや使用停止などの変更のことです。 これらの更新のほとんどは、ホストされている仮想マシンに影響を及ぼすことなく実行されます。 ただし、更新による影響が生じる場合もあります。

- Azure では、再起動を伴わないメンテナンスの場合、インプレース移行を使用して、ホストの更新中に VM を一時的に停止させます。

- 再起動を伴うメンテナンスの場合は、メンテナンスの予定日時が知らされます。 このような場合は、都合に応じて自分自身でメンテナンスを開始できる時間枠が与えられます。


再起動が必要な計画済みメンテナンスは、段階的にスケジュールされます。 各段階のスコープ (リージョン) はそれぞれ異なります。

- 段階はお客様への通知で始まります。 既定では、サブスクリプションの所有者と共同所有者に通知が送信されます。 通知の受信者は追加できます。また、電子メール、SMS、Webhook などのメッセージング オプションを通知に追加できます。  
- 通知後すぐに、セルフサービス期間が設定されます。 この期間中には、この段階に含まれている仮想マシンを確認し、プロアクティブな再デプロイを使用してメンテナンスを開始できます。 
- セルフサービス期間が過ぎると、予定メンテナンス期間が始まります。 この時点で、Azure は、仮想マシンに対して必要なメンテナンスをスケジュールし、適用します。 

2 つの期間を用意した目的は、Azure によってメンテナンスが自動的に開始される時期を把握した上で、メンテナンスを開始して仮想マシンを再起動するのに必要な時間を十分に確保できるようにすることにあります。


Azure ポータル、PowerShell、REST API、CLI を使用して、VM のメンテナンス期間のクエリを実行し、セルフサービス メンテナンスを開始することができます。

 > [!NOTE]
 > メンテナンスを試みたが失敗に終わった場合は、Azure によって VM に**スキップ済み**というマークが付けられ、予定メンテナンス期間中に VM は再起動されません。 その代わりに、後で、新しいスケジュールに関する連絡があります。 


[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="check-maintenance-status-using-powershell"></a>PowerShell を使用してメンテナンスの状態を確認する

Azure Powershell を使用して、VM のメンテナンスの予定を確認することもできます。 計画済みメンテナンスに関する情報は、[Get AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) コマンドレットに `-status` パラメーターを指定することで取得できます。
 
計画済みメンテナンスがある場合にのみ、メンテナンス情報が返されます。 VM に影響を及ぼすメンテナンスがスケジュールされていない場合、コマンドレットはメンテナンス情報を返しません。 

```powershell
Get-AzureRmVM -ResourceGroupName rgName -Name vmName -Status
```

MaintenanceRedeployStatus では、次のプロパティが返されます。 
| 値 | Description   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | この時点で VM に対してメンテナンスを開始できるかどうかを示します。 ||
| PreMaintenanceWindowStartTime         | VM に対してメンテナンスを開始できる場合、メンテナンスのセルフサービス期間の始まりです。 ||
| PreMaintenanceWindowEndTime           | VM に対してメンテナンスを開始できる場合、メンテナンスのセルフサービス期間の終わりです。 ||
| MaintenanceWindowStartTime            | VM に対してメンテナンスを開始できる場合、予定メンテナンス期間の始まりです。 ||
| MaintenanceWindowEndTime              | VM に対してメンテナンスを開始できる場合、予定メンテナンス期間の終わりです。 ||
| LastOperationResultCode               | VM に対して最後にメンテナンスを試みたときの結果です。 ||



また、VM を指定しないで [Get AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) を実行すると、リソース グループ内のすべての VM のメンテナンス状態を取得することもできます。
 
```powershell
Get-AzureRmVM -ResourceGroupName rgName -Status
```

次の PowerShell 関数は、サブスクリプション ID を取り、メンテナンスがスケジュールされている VM の一覧を出力します。

```powershell

function MaintenanceIterator
{
    Select-AzureRmSubscription -SubscriptionId $args[0]

    $rgList= Get-AzureRmResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        $vmList = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>PowerShell を使用して VM に対するメンテナンスを開始する

**IsCustomerInitiatedMaintenanceAllowed** が true に設定されている場合、前のセクションの関数から取得した情報を使用して、次のように VM に対するメンテナンスを開始します。

```powershell
Restart-AzureRmVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>クラシック デプロイ

クラシック デプロイ モデルを使用してデプロイされたレガシ VM がまだある場合は、PowerShell を使用して、VM を照会し、メンテナンスを開始できます。

VM のメンテナンスの状態を取得するには、次のように入力します。

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

クラシック VM のメンテナンスを開始するには、次のように入力します。

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```


## <a name="faq"></a>FAQ


**Q: 仮想マシンを今すぐ再起動する必要があるのはなぜですか?**

**A:** Azure プラットフォームの更新とアップグレードの大半は仮想マシンの可用性に影響を及ぼしませんが、Azure でホストされている仮想マシンの再起動を避けられない場合があります。 累積された複数の変更があり、サーバーを再起動する必要がある場合、仮想マシンを再起動することになります。

**Q: 可用性セットを使用して高可用性に関する推奨事項に従えば安全ですか?**

**A:** 可用性セットまたは仮想マシン スケール セットにデプロイされた仮想マシンには、更新ドメイン (UD) の概念があります。 メンテナンスを実行するときに、Azure では UD の制約が遵守され、(同じ可用性セット内の) 別の UD の仮想マシンは再起動されません。  また、Azure は仮想マシンの次のグループに移行する前に少なくとも 30 分待機します。 

高可用性の詳細については、Azure での Windows 仮想マシンの可用性管理に関する記事または Azure での Linux 仮想マシンの可用性管理に関する記事をご覧ください。

**Q: ディザスター リカバリー セットが別のリージョンにあります。安全でしょうか?**

**A:** 各 Azure リージョンは、同じ Geo 内 (米国、ヨーロッパ、アジアなど) の別のリージョンと組み合わされています。 計画的な Azure の更新をペアになっているリージョンの 1 つずつにロールアウトすることで、ダウンタイムとアプリケーション停止のリスクを最小限に抑えことができます。 計画済みメンテナンスの間、ユーザーがメンテナンスを開始する同様の期間が Azure によってスケジュールされる場合があります。ただし、スケジュールされたメンテナンス期間は、ペアになっているリージョン間で異なります。  

Azure リージョンの詳細については、「Azure の仮想マシンのリージョンと可用性について」をご覧ください。  リージョンのペアの完全な一覧はこちらで確認できます。

**Q: 計画済みメンテナンスに関する通知を受け取るにはどうすればよいですか?**

**A:** 計画済みメンテナンス ウェーブは、1 つ以上の Azure リージョンにスケジュールを設定することで開始されます。 開始直後に、電子メール通知がサブスクリプションの所有者に送信されます (サブスクリプションごとに 1 メール)。 この通知の追加のチャネルと受信者は、アクティビティ ログ アラートを使用して構成できます。 計画済みメンテナンスが既にスケジュールされているリージョンに仮想マシンをデプロイした場合、通知を受け取ることはできないため、その VM のメンテナンスの状態を確認する必要があります。

**Q: ポータル、Powershell、CLI に計画済みメンテナンスの情報が表示されません。何が問題なのでしょうか?**

**A:** 計画済みメンテナンスに関する情報は、計画済みメンテナンス ウェーブ中にのみ、影響を受ける VM に提供されます。 つまり、データが表示されない場合、メンテナンス ウェーブが既に完了している (または、まだ開始されていない) か、更新されたサーバーで仮想マシンが既にホストされていると考えられます。

**Q: 仮想マシンのメンテナンスを自分で開始する必要はありますか?**

**A:** 一般に、クラウド サービス、可用性セット、または仮想マシン スケール セットにデプロイされているワークロードには、計画済みメンテナンスに対する回復性があります。  計画メンテナンス中は、指定された時間に 1 つの更新ドメインのみ影響を受けます。 更新ドメインへの影響が必ずしも順番に生じるわけではないことに注意してください。

次のような場合に、お客様ご自身でメンテナンスを開始できます。
- アプリケーションが 1 つの仮想マシンで実行されており、勤務時間外にすべてのメンテナンスを適用する必要がある場合
- SLA の一部として、メンテナンスの時間を調整する必要がある場合
- 可用性セット内でも、各 VM の再起動の間隔を 30 分以上にする必要がある場合
- メンテナンスを迅速に完了するために、アプリケーション全体 (複数の層、複数の更新ドメイン) を停止する場合

**Q: 仮想マシンが影響を受けるのはいつであるかを正確に把握する方法はありますか?**

**A:** スケジュールを設定するときに、数日間の時間枠が設けられています。 ただし、この時間枠内でのサーバー (および VM) の正確な優先順位付けは不明です。 VM の正確な時間を把握したいお客様は、仮想マシン内からスケジュールされたイベントとクエリを使用し、VM が再起動される 10 分前に通知を受け取ることができます。
  
**Q: 仮想マシンの再起動にはどれくらいの時間がかかりますか?**

**A:** VM のサイズによっては、再起動に最大数分かかることがあります。 クラウド サービス、スケール セット、または可用性セットを使用している場合、VM の各グループ (UD) 間に 30 分の間隔が空けられます。 

**Q: クラウド サービス、スケール セット、Service Fabric の場合、エクスペリエンスはどのようなものになりますか?**

**A:** これらのプラットフォームは計画済みメンテナンスの影響を受けますが、影響を受けるのは、常に 1 つのアップグレード ドメイン (UD) の VM だけであることから、これらのプラットフォームを使用しているお客様は安全とみなされます。  

**Q: ハードウェアの使用停止に関する電子メールを受け取りました。これは計画済みメンテナンスと同じですか?**

**A:** ハードウェアの使用停止は計画済みメンテナンス イベントですが、このユース ケースは新しいエクスペリエンスにまだオンボードされていません。  お客様が 2 つの異なる計画済みメンテナンス ウェーブに関する 2 つの類似する電子メールを受け取った場合、混乱が生じることが予想されます。

**Q: VM のメンテナンス情報が表示されません。何が問題だったのでしょうか?**

**A:** VM のメンテナンス情報が表示されない理由はいくつかあります。
1.  Microsoft 社内としてマークされたサブスクリプションを使用している。
2.  VM のメンテナンスがスケジュールされていない。 メンテナンス ウェーブが終了しているか、取り消しまたは変更が行われたため、VM が影響を受けなくなっていると考えられます。
3.  VM リスト ビューに [メンテナンス] 列が追加されていない。 この列は既定のビューに追加されていますが、既定以外の列を表示するように構成しているお客様は、VM リスト ビューに **[メンテナンス]** 列を手動で追加する必要があります。

**Q: VM の 2 回目のメンテナンスがスケジュールされています。なぜですか?**

**A:** メンテナンスの再デプロイが既に完了した後に、VM のメンテナンスがスケジュールされるユース ケースがいくつかあります。
1.  メンテナンス ウェーブが取り消され、別のペイロードで再開された場合。 エラーが発生したペイロードが検出されたため、Microsoft が追加のペイロードをデプロイする必要があったと考えられます。
2.  ハードウェア障害により、VM が別のノードに "*サービス復旧*" された場合
3.  お客様が VM を停止 (割り当てを解除) し、再起動することを選択した場合
4.  お客様が VM の**自動シャットダウン**を有効にした場合


## <a name="next-steps"></a>次のステップ

[スケジュールされたイベント](scheduled-events.md) を使用して VM でメンテナンス イベントを登録する方法を説明します。
