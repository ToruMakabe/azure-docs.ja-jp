---
title: "Kubernetes on Azure のチュートリアル - アプリケーションのスケーリング | Microsoft Docs"
description: "AKS チュートリアル - アプリケーションのスケーリング"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Docker, コンテナー, マイクロサービス, Kubernetes, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: f060b37d5ae02dfd53f513b134692186024cf727
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="scale-application-in-azure-container-service-aks"></a>Azure Container Service (AKS) でのアプリケーションのスケーリング

ここまでチュートリアルに従って進めてきた場合は、AKS で Kubernetes クラスターが動作していて、Azure Vote アプリをデプロイしてあります。

このチュートリアルでは、8 つあるうちの 5 番目のパートで、アプリのポッドをスケールアウトし、ポッドの自動スケーリングを試します。 また、Azure VM ノードの数をスケーリングして、クラスターがワークロードをホストする容量を変更する方法についても説明します。 次のタスクを行います。

> [!div class="checklist"]
> * Kubernetes の Azure ノードのスケーリング
> * Kubernetes ポッドの手動スケーリング
> * アプリのフロントエンドを実行する自動スケール ポッドの構成

その後のチュートリアルでは、Azure Vote アプリケーションが更新され、Operations Management Suite を Kubernetes クラスターを監視するように構成します。

## <a name="before-you-begin"></a>開始する前に

前のチュートリアルでは、アプリケーションをコンテナー イメージにパッケージ化し、このイメージを Azure Container Registry にアップロードして、Kubernetes クラスターを作成しました。 その後、Kubernetes クラスターでアプリケーションを実行しました。

これらの手順を実行していない場合で、行いたい場合は、「[チュートリアル 1 – コンテナー イメージを作成する](./tutorial-kubernetes-prepare-app.md)」に戻ってください。

## <a name="scale-aks-nodes"></a>AKS ノードのスケーリング

前のチュートリアルでコマンドを使って Kubernetes クラスターを作成した場合、そのクラスターには 1 つのノードがあります。 クラスターのコンテナー ワークロードを増減する場合は、ノードの数を手動で調整できます。

次の例では、*myK8sCluster* という名前の Kubernetes クラスターのノードの数を 3 に増やしています。 コマンドが完了するまでに数分かかります。

```azurecli
az aks scale --resource-group=myResourceGroup --name=myK8SCluster --node-count 3
```

次のように出力されます。

```
"agentPoolProfiles": [
  {
    "count": 3,
    "dnsPrefix": null,
    "fqdn": null,
    "name": "myK8sCluster",
    "osDiskSizeGb": null,
    "osType": "Linux",
    "ports": null,
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_D2_v2",
    "vnetSubnetId": null
  }
```

## <a name="manually-scale-pods"></a>ポッドを手動でスケーリングする

ここまでで、Azure Vote フロントエンドと Redis インスタンスをデプロイし、それぞれに 1 つのレプリカを作成しました。 これを確認するには、[kubectl get](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) コマンドを実行します。

```azurecli
kubectl get pods
```

出力:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

[kubectl scale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#scale) コマンドを使って、`azure-vote-front` のデプロイに含まれるポッドの数を手動で変更します。 この例では、数を 5 に増やします。

```azurecli
kubectl scale --replicas=5 deployment/azure-vote-front
```

[kubectl get pods](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) を実行して、Kubernetes がポッドを作成していることを確認します。 しばらくすると、追加したポッドが実行するようになります。

```azurecli
kubectl get pods
```

出力:

```
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>ポッドを自動スケールする

Kubernetes は[ポッドの水平自動スケーリング](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)をサポートしており、CPU 使用率などの選ばれたメトリックに応じて、デプロイのポッドの数を調整します。

自動スケーラーを使うには、ポッドで CPU の要求と制限が定義されている必要があります。 `azure-vote-front` のデプロイでは、フロントエンド コンテナーは 0.25 CPU を要求します。上限は 0.5 CPU です。 設定は次のようになります。

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

次の例では、[kubectl autoscale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#autoscale) コマンドを使って、`azure-vote-front` のデプロイメントのポッド数を自動スケーリングします。 ここでは、CPU 使用率が 50% を超えると、自動スケーラーはポッドを最大 10 個まで増やします。


```azurecli
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

自動スケーラーの状態を見るには、次のコマンドを実行します。

```azurecli
kubectl get hpa
```

出力:

```
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Azure Vote アプリの負荷が最低になって数分が経過すると、ポッド レプリカの数は自動的に 3 に減少します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Kubernetes クラスターの異なるスケーリング機能を使いました。 次のタスクを行いました。

> [!div class="checklist"]
> * Kubernetes ポッドの手動スケーリング
> * アプリのフロントエンドを実行する自動スケール ポッドの構成
> * Kubernetes の Azure ノードのスケーリング

次のチュートリアルに進んで、Kubernetes でのアプリケーションの更新について学習してください。

> [!div class="nextstepaction"]
> [Kubernetes でアプリケーションを更新する](./tutorial-kubernetes-app-update.md)