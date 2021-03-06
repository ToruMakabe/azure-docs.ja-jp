---
title: "Azure Stack ポリシー モジュールの使用 | Microsoft Docs"
description: "Azure Stack サブスクリプションと同様に動作するように、Azure サブスクリプションを制限する方法を説明します。"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: sngun
ms.openlocfilehash: c9c96d4a536949e0406af53d218949b57517ba87
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2017
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Azure Stack ポリシー モジュールを使用して Azure ポリシー管理する

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack ポリシー モジュールにより、Azure Stack と同じバージョン管理とサービス可用性で、Azure サブスクリプションを構成できます。  このモジュールでは、**New-AzureRMPolicyAssignment** コマンドレットを使用して、サブスクリプションで使用できるリソースの種類とサービスを制限する Azure ポリシーを作成します。  完了すると、Azure サブスクリプションを使用して Azure Stack を対象とするアプリを開発できます。  

## <a name="install-the-module"></a>モジュールのインストール
1. 「[PowerShell for Azure Stack のインストール](azure-stack-powershell-install.md)」の手順 1 の説明に従って、AzureRM PowerShell モジュールの必要なバージョンをインストールします。   
2. [GitHub から Azure Stack ツールをダウンロード](azure-stack-powershell-download.md)します。  
3. [PowerShell を Azure Stack で使用するための構成](azure-stack-powershell-configure-user.md)

4. AzureStack.Policy.psm1 モジュールをインポートします。

   ```PowerShell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-subscription"></a>サブスクリプションにポリシーを適用する
次のコマンドを使用して、Azure サブスクリプションに対して既定のAzure Stack ポリシーを適用できます。 実行する前に、*Azure サブスクリプション名* を自分の Azure サブスクリプションに置き換えます。

```PowerShell
Login-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID

```

## <a name="apply-policy-to-a-resource-group"></a>リソース グループにポリシーを適用する
さらに詳細な方法でポリシーを適用する必要がある場合があります。  たとえば、同じサブスクリプションで他のリソースが実行されている場合があります。  ポリシー適用のスコープを特定のリソース グループに設定することで、Azure リソースを使用して Azure Stack のアプリをテストできます。 実行する前に、*Azure サブスクリプション名* を自分の Azure サブスクリプション名に置き換えます。

```PowerShell
Login-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName

```

## <a name="policy-in-action"></a>実行中のポリシー
Azure ポリシーをデプロイしたら、ポリシーで禁止されているリソースをデプロイしようとすると、エラーが表示されます。  

![ポリシーの制約によるリソースのデプロイの失敗の結果](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>次のステップ
[PowerShell を使用したテンプレートのデプロイ](azure-stack-deploy-template-powershell.md)

[Azure CLI を使用したテンプレートのデプロイ](azure-stack-deploy-template-command-line.md)

[Visual Studio を使用したテンプレートのデプロイ](azure-stack-deploy-template-visual-studio.md)
