---
title: "Kubernetes on Azure のチュートリアル - アプリの準備 | Microsoft Docs"
description: "AKS チュートリアル - アプリの準備"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Docker, コンテナー, マイクロサービス, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 0048f9ab21c09860397989c41920562ebab2f561
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="prepare-application-for-azure-container-service-aks"></a>Azure Container Service (AKS) 用のアプリケーションの準備

この 8 部構成の最初のチュートリアルでは、複数コンテナー アプリケーションを Kubernetes で使用できるように準備します。 手順は次のとおりです。  

> [!div class="checklist"]
> * GitHub からアプリケーション ソースを複製する  
> * アプリケーション ソースからコンテナー イメージを作成する
> * ローカル Docker 環境でアプリケーションをテストする

完了後、次のアプリケーションにローカル開発環境からアクセスできます。

![Azure 上の Kubernetes クラスターの図](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

この後のチュートリアルでは、このコンテナ イメージが Azure Container Registry にアップロードされ、AKS クラスターで実行されます。

## <a name="before-you-begin"></a>開始する前に

このチュートリアルの前提として、コンテナー、コンテナー イメージ、基本 Docker コマンドなど、Docker のコア概念を基本的に理解している必要があります。 必要な場合は、[Docker の入門]( https://docs.docker.com/get-started/)に関するドキュメントでコンテナーの基礎を参照してください。 

このチュートリアルを完了するには、Docker 開発環境が必要です。 Docker では、[Mac](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、または [Linux](https://docs.docker.com/engine/installation/#supported-platforms) システムで Docker を簡単に構成できるパッケージが提供されています。

Azure Cloud Shell には、このチュートリアルの各ステップを完了するのに必要な Docker コンポーネントがすべて含まれているわけではありません。 そのため、完全な Docker 開発環境の使用をお勧めします。

## <a name="get-application-code"></a>アプリケーションのコードを入手する

このチュートリアルで使うサンプル アプリケーションは、基本的な投票アプリです。 アプリケーションは、フロントエンド Web コンポーネントとバックエンド Redis インスタンスで構成されています。 Web コンポーネントは、カスタム コンテナー イメージにパッケージ化されています。 Redis インスタンスでは、Docker Hub から変更されていないイメージを使用します。  

アプリケーションのコピーを開発環境にダウンロードするには、git を使います。

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

ディレクトリを変更して、複製されたディレクトリから作業するようにします。

```console
cd azure-voting-app-redis
```

ディレクトリ内には、アプリケーションのソース コード、事前作成された Docker Compose ファイル、および Kubernetes マニフェスト ファイルがあります。 これらのファイルは、チュートリアル セット全体で使用されます。 

## <a name="create-container-images"></a>コンテナー イメージを作成する

[Docker Compose](https://docs.docker.com/compose/) は、コンテナー イメージと複数のコンテナー アプリケーションのデプロイからのビルドを自動化するために使用できます。

コンテナー イメージの作成、Redis イメージのダウンロード、およびアプリケーションの起動を行うために、`docker-compose.yml` ファイルを実行します。

```console
docker-compose up -d
```

完了したら、[docker images](https://docs.docker.com/engine/reference/commandline/images/) コマンドを使って、作成されたイメージを確認します。

```console
docker images
```

3 つのイメージがダウンロードまたは作成されたことに注目してください。 `azure-vote-front` イメージはアプリケーションを含み、`nginx-flask` イメージをベースとして使用します。 `redis` イメージは、Redis インスタンスを起動するために使用されます。

```
REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

[docker ps](https://docs.docker.com/engine/reference/commandline/ps/) コマンドを実行して、実行中のコンテナーを確認します。

```console
docker ps
```

出力:

```
CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>ローカルでアプリケーションをテストする

ブラウザーで http://localhost:8080 に移動して、実行中のアプリケーションを確認します。

![Azure 上の Kubernetes クラスターの図](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

アプリケーションの機能を検証したので、実行中のコンテナーを停止して削除できます。 コンテナー イメージは削除しないでください。 `azure-vote-front` イメージは、次のチュートリアルで Azure Container Registry インスタンスにアップロードされます。

実行中のコンテナーを停止するには、次を実行します。

```console
docker-compose stop
```

次のコマンドで、停止しているコンテナーとリソースを削除します。

```console
docker-compose down
```

完了すると、Azure Vote アプリケーションを含むコンテナー イメージが完成します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、アプリケーションをテストし、アプリケーション用のコンテナー イメージを作成しました。 次の手順を完了しました。

> [!div class="checklist"]
> * GitHub からアプリケーション ソースを複製する  
> * アプリケーション ソースからコンテナー イメージを作成する
> * ローカル Docker 環境でアプリケーションアプリケーションをテストする

次のチュートリアルでは、Azure Container Registry にコンテナー イメージを格納する方法について学習します。

> [!div class="nextstepaction"]
> [Azure Container Registry にイメージをプッシュする](./tutorial-kubernetes-prepare-acr.md)
