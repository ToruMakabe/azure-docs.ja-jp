---
title: "Azure サービス カタログ マネージ アプリケーションの作成と発行 | Microsoft Docs"
description: "組織のメンバーを対象とする Azure マネージ アプリケーションを作成する方法について説明します。"
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: fd2c60cbc237f6d302616723c745563a3e1afecb
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2017
---
# <a name="publish-a-managed-application-for-internal-consumption"></a>社内従量課金プラン向けマネージ アプリケーションの発行

組織のメンバーを対象とする Azure [マネージ アプリケーション](overview.md)を作成し、発行することができます。 たとえば、IT 部門が、組織標準に確実に準拠するマネージ アプリケーションを発行できます。 こうしたマネージ アプリケーションは、Azure Marketplace ではなく、サービス カタログを利用して入手できます。

サービス カタログ用のマネージ アプリケーションを発行するには、次の操作を行う必要があります。

* マネージ アプリケーションでデプロイするリソースを定義するテンプレートを作成する。
* マネージ アプリケーションをデプロイするときに、ポータルのユーザー インターフェイス要素を定義する。
* 必要なテンプレート ファイルを含む .zip パッケージを作成する。
* どのユーザー、グループ、またはアプリケーションがユーザーのサブスクリプションのリソース グループにアクセスする必要があるかを決める。
* .zip パッケージを指定して ID アクセスを要求するマネージ アプリケーション定義を作成する。

この記事では、マネージ アプリケーションには、ストレージ アカウントだけが含まれます。 これは、マネージ アプリケーションを公開する手順を説明するためです。 詳細な例については、[Azure マネージ アプリケーションのサンプル プロジェクト](sample-projects.md)に関する記事を参照してください。

## <a name="create-the-resource-template"></a>リソース テンプレートの作成

各マネージ アプリケーション定義には、**mainTemplate.json** というファイルが含まれています。 このファイルで、プロビジョニングする Azure リソースを定義します。 テンプレートは、通常の Resource Manager テンプレートと違いはありません。

**mainTemplate.json** というファイルを作成します。 名前は大文字と小文字が区別されます。

次の JSON をファイルに追加します。 このファイルで、ストレージ アカウントを作成するパラメーターを定義し、ストレージ アカウントのプロパティを指定します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        },
        "storageAccountType": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString('storage'))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
        }
    }
}
```

mainTemplate.json ファイルを保存します。

## <a name="create-the-user-interface-definition"></a>ユーザー インターフェイス定義を作成する

Azure Portal で **createUiDefinition.json** ファイルを使用して、マネージ アプリケーションを作成するユーザー用のユーザー インターフェイスを生成します。 ユーザーが各パラメーターの入力をどのように提供するかを定義します。 各種のオプション (ドロップダウン リストなど) やテキスト ボックス、パスワード ボックスなどの入力手段を利用することができます。 マネージ アプリケーションの UI 定義ファイルの作成する方法については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。

**createUiDefinition.json** というファイルを作成します。 名前は大文字と小文字が区別されます。

次の JSON をファイルに追加します。

```json
{
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "storageConfig",
                "label": "Storage settings",
                "subLabel": {
                    "preValidation": "Configure the infrastructure settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Storage settings",
                "elements": [
                    {
                        "name": "storageAccounts",
                        "type": "Microsoft.Storage.MultiStorageAccountCombo",
                        "label": {
                            "prefix": "Storage account name prefix",
                            "type": "Storage account type"
                        },
                        "defaultValue": {
                            "type": "Standard_LRS"
                        },
                        "constraints": {
                            "allowedTypes": [
                                "Premium_LRS",
                                "Standard_LRS",
                                "Standard_GRS"
                            ]
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
            "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
            "location": "[location()]"
        }
    }
}
```

createUIDefinition.json ファイルを保存します。

## <a name="package-the-files"></a>ファイルのパッケージ化

app.zip という .zip ファイルに 2 つのファイルに追加します。 2 つのファイルは .zip ファイルのルートに配置する必要があります。 このファイルをフォルダーに配置すると、マネージ アプリケーション定義を作成するときに、必要なファイルが存在しないことを示すエラーが発生します。 

パッケージは、想定される実行場所からアクセスできる場所にアップロードしてください。 

```powershell
New-AzureRmResourceGroup -Name storageGroup -Location eastus
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob

$ctx = $storageAccount.Context

New-AzureStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzureStorageBlobContent -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

## <a name="create-the-managed-application-definition"></a>マネージ アプリケーション定義の作成

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Azure Active Directory ユーザー グループまたはアプリケーションの作成

次に、顧客に代わってリソースを管理するためのユーザー グループまたはアプリケーションを選択します。 このユーザー グループまたはアプリケーションには、割り当てられているロールに従って、マネージ リソース グループに対するアクセス許可が付与されています。 そのロールには、ロールベースのアクセス制御 (RBAC) の組み込みロール (所有者、共同作成者など) をどれでも使用できます。 リソースを管理するためのアクセス許可は、個々のユーザーに付与することもできますが、ユーザー グループに割り当てるのが一般的です。 新しい Active Directory ユーザー グループの作成する場合は、「[Azure Active Directory でグループを作成し、メンバーを追加する](../active-directory/active-directory-groups-create-azure-portal.md)」を参照してください。

リソースの管理に使用するユーザー グループのオブジェクト ID が必要です。 

![グループ ID の取得](./media/publish-service-catalog-app/get-group-id.png)

### <a name="get-the-role-definition-id"></a>ロール定義 ID の取得

次に、ユーザー、ユーザー グループ、アプリケーションへのアクセス権を付与する RBAC の組み込みのロールのロール定義 ID が必要となります。 通常、所有者、共同作成者、閲覧者のいずれかのロールを使います。 次のコマンドは、所有者ロールのロール定義 ID を取得する方法を示しています。

```powershell
$ownerID=(Get-AzureRmRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>マネージ アプリケーション定義の作成

マネージ アプリケーション定義を保存するリソース グループがまだない場合は、ここで作成します。

```powershell
New-AzureRmResourceGroup -Name appDefinitionGroup -Location westcentralus
```

次に、マネージ アプリケーション定義のリソースを作成します。

```powershell
$blob = Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzureRmManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "<group-id>:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

## <a name="create-the-managed-application-by-using-the-portal"></a>ポータルを使ったマネージ アプリケーションの作成

次はポータルを使用してマネージ アプリケーションをデプロイしてみましょう。 パッケージで作成したユーザー インターフェイスが表示されます。

1. Azure Portal にアクセスします。 **[+ 新規]** を選択して**サービス カタログ**を検索します。

   ![サービス カタログの検索](./media/publish-service-catalog-app/select-new.png)

1. **[サービス カタログの管理されているアプリケーション]** を選択します。

   ![サービス カタログの選択](./media/publish-service-catalog-app/select-service-catalog.png)

1. **[作成]**を選択します。

   ![作成の選択](./media/publish-service-catalog-app/select-create.png)

1. 利用可能なソリューションの一覧から、作成するマネージ アプリケーションを見つけて選択します。 **[作成]**を選択します。

   ![マネージ アプリケーションを見つける](./media/publish-service-catalog-app/find-application.png)

1. マネージ アプリケーションに必要な基本情報を入力します。 サブスクリプションと、マネージ アプリケーションを格納する新しいリソース グループを指定します。 場所は **[米国中西部]** を選択します。 操作が完了したら、**[OK]** をクリックします。

   ![マネージ アプリケーションのパラメーターの指定](./media/publish-service-catalog-app/provide-basics.png)

1. マネージ アプリケーションのリソースに固有の値を指定します。 操作が完了したら、**[OK]** をクリックします。

   ![リソース パラメーターの指定](./media/publish-service-catalog-app/provide-resource-values.png)

1. テンプレートは指定した値を検証します。 検証が成功したら、**[OK]** を選択してデプロイを開始します。

   ![マネージ アプリケーションの検証](./media/publish-service-catalog-app/validate.png)

デプロイが完了すると、マネージ アプリケーションが applicationGroup という名前のリソース グループに配置されます。 ストレージ アカウントは、applicationGroup にハッシュされた文字列値を加えた名前のリソース グループに配置されます。

## <a name="next-steps"></a>次のステップ

* マネージ アプリケーションの概要については、[マネージ アプリケーションの概要](overview.md)に関するページをご覧ください。
* サンプル プロジェクトについては、[Azure マネージ アプリケーションのサンプル プロジェクト](sample-projects.md)に関する記事を参照してください。
* マネージ アプリケーションを Azure Marketplace に発行する方法については、「[Marketplace の Azure マネージ アプリケーション](publish-marketplace-app.md)」を参照してください。
* マネージ アプリケーションの UI 定義ファイルの作成する方法については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。