---
title: "Azure Resource Manager テンプレートでの Key Vault シークレット | Microsoft Docs"
description: "デプロイメント時にパラメーターとして Key Vault からシークレットを渡す方法について説明します。"
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2017
ms.author: tomfitz
ms.openlocfilehash: e789a234979be877d990665902fd6219ae7ec40b
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2017
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す

デプロイ時に、セキュリティで保護された値 (パスワードなど) をパラメーターとして渡す必要がある場合は、[Azure Key Vault](../key-vault/key-vault-whatis.md) からその値を取得できます。 値を取得するには、キー コンテナーとパラメーター ファイル内のシークレットを参照します。 参照するのは Key Vault ID だけであるため、値が公開されることはありません。 リソースをデプロイするたびに、シークレットの値を手動で入力する必要はありません。 キー コンテナーは、デプロイ先のリソース グループとは異なるサブスクリプションに存在していてもかまいません。 キー コンテナーを参照するときに、サブスクリプション ID を含めます。

キー コンテナーを作成するときには、*enabledForTemplateDeployment* プロパティを *true* に設定します。 この値を true に設定することで、デプロイ時に Resource Manager テンプレートにアクセスを許可します。

## <a name="deploy-a-key-vault-and-secret"></a>Key Vault とシークレットのデプロイ

キー コンテナーとシークレットを作成するには、Azure CLI または PowerShell のいずれかを使用します。 テンプレートのデプロイのために、キー コンテナーが有効にされることに注意してください。 

Azure CLI では、次を使用します。

```azurecli-interactive
vaultname={your-unique-vault-name}
password={password-value}

az group create --name examplegroup --location 'South Central US'
az keyvault create \
  --name $vaultname \
  --resource-group examplegroup \
  --location 'South Central US' \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name $vaultname --name examplesecret --value $password
```

PowerShell では、次を使用します。

```powershell
$vaultname = "{your-unique-vault-name}"
$password = "{password-value}"

New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
New-AzureRmKeyVault `
  -VaultName $vaultname `
  -ResourceGroupName examplegroup `
  -Location "South Central US" `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $vaultname -Name "examplesecret" -SecretValue $secretvalue
```

## <a name="enable-access-to-the-secret"></a>シークレットへのアクセスの有効化

新しいキー コンテナーと既存のキー コンテナーのどちらを使用する場合も、テンプレートをデプロイするユーザーがシークレットにアクセスできることを確認してください。 シークレットを参照するテンプレートをデプロイするユーザーには、キー コンテナーに対する `Microsoft.KeyVault/vaults/deploy/action` アクセス許可が必要です。 このアクセスは、[所有者](../active-directory/role-based-access-built-in-roles.md#owner)ロールと[共同作成者](../active-directory/role-based-access-built-in-roles.md#contributor)ロールが許可します。

## <a name="reference-a-secret-with-static-id"></a>固定 ID でのシークレットの参照

キー コンテナーのシークレットを受け取るテンプレートは、その他のテンプレートに似ています。 これは、**テンプレートではなく、パラメーター ファイルでキー コンテナーを参照する**ためです。 たとえば、次のテンプレートでは、管理者のパスワードを含む SQL データベースがデプロイされます。 パスワード パラメーターは、セキュリティで保護された文字列に設定されます。 しかしこのテンプレートでは、その値がどこから来るかは指定されません。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "[parameters('sqlServerName')]",
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

ここで、前のテンプレートのためにパラメーター ファイルを作成します。 このパラメーター ファイルで、テンプレート内のパラメーターの名前に一致するパラメーターを指定します。 パラメーター値のために、キー コンテナーのシークレットを参照します。 シークレットを参照するには、Key Vault のリソース識別子とシークレットの名前を渡します。 次の例では、Key Vault シークレットが既に存在しているという前提で、リソース ID に静的な値を指定します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminLogin": {
            "value": "exampleadmin"
        },
        "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/<subscription-id>/resourceGroups/examplegroup/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "examplesecret"
            }
        },
        "sqlServerName": {
            "value": "<your-server-name>"
        }
    }
}
```

ここで、テンプレートをデプロイし、パラメーター ファイルを渡します。 Azure CLI では、次を使用します。

```azurecli-interactive
az group create --name datagroup --location "Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-file sqlserver.json \
    --parameters @sqlserver.parameters.json
```

PowerShell では、次を使用します。

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateFile sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>動的 ID でのシークレットの参照

前のセクションでは、Key Vault シークレットの静的リソース ID を渡す方法を紹介しました。 しかし参照すべき Key Vault シークレットがデプロイごとに変わる状況も考えられます。 その場合、パラメーター ファイルでリソース ID をハードコーディングすることはできません。 パラメーター ファイルではテンプレート式が使用できないので、パラメーター ファイルでリソース ID を動的に生成することはできません。

Key Vault シークレットのリソース ID を動的に生成するには、そのシークレットを必要とするリソースを、入れ子になったテンプレートに移す必要があります。 マスター テンプレートに、その入れ子になったテンプレートを追加し、動的に生成されたリソース ID をパラメーターに格納して渡します。 入れ子になったテンプレートは、外部 URI を通じて使用可能である必要があります。 この記事の残りの部分では、ストレージ アカウントに上記のテンプレートを追加し、URI - `https://<storage-name>.blob.core.windows.net/templatecontainer/sqlserver.json` を通じてそれを使用可能であることを前提としています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vaultName": {
        "type": "string"
      },
      "vaultResourceGroup": {
        "type": "string"
      },
      "secretName": {
        "type": "string"
      },
      "adminLogin": {
        "type": "string"
      },
      "sqlServerName": {
        "type": "string"
      }
    },
    "resources": [
    {
      "apiVersion": "2015-01-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "https://<storage-name>.blob.core.windows.net/templatecontainer/sqlserver.json",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(subscription().subscriptionId,  parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          },
          "adminLogin": { "value": "[parameters('adminLogin')]" },
          "sqlServerName": {"value": "[parameters('sqlServerName')]"}
        }
      }
    }],
    "outputs": {}
}
```

上記のテンプレートをデプロイし、パラメーターの値を指定します。

## <a name="next-steps"></a>次のステップ
* Key Vault の全般的な情報については、「[Azure Key Vault の概要](../key-vault/key-vault-get-started.md)」をご覧ください。
* キー シークレットの詳細な参照例については、 [Key Vault の例](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)を参照してください。
