---
title: "Azure Multi-Factor Authentication - しくみ"
description: "Azure Multi-Factor Authentication を使えば、シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護することが可能です。 2 番目の形式の認証を要求することで追加のセキュリティを提供し、次の一連の容易な確認オプションによって強力な認証を提供します。"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: d14db902-9afe-4fca-b3a5-4bd54b3d8ec5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 1f3495b038171edd713678aef49be9401ab458dd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="how-azure-multi-factor-authentication-works"></a>Azure Multi-Factor Authentication のしくみ
2 段階認証のセキュリティは、レイヤー アプローチによってもたらされます。 攻撃者にとって、多要素認証を侵害することはかなり困難です。 攻撃者にユーザーのパスワードを知られても、信頼できるデバイスを手に入れなければ役に立ちません。 

![追加のセキュリティ確認](./media/multi-factor-authentication-how-it-works/howitworks.png)

Azure Multi-Factor Authentication を使えば、シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護することが可能です。  2 番目の形式の認証を要求することで追加のセキュリティを提供し、次の一連の容易な確認オプションによって強力な認証を提供します。


## <a name="methods-available-for-two-step-verification"></a>2 段階認証で使用できる方法
ユーザーがサインインしようとすると、追加の確認がユーザーに対して行われます。  この 2 番目の確認のために使用できる方法の一覧を次に示します。

| 確認方法 | Description |
| --- | --- |
| 音声通話 |ユーザーの登録済みの電話番号が呼び出されます。 ユーザーは、必要に応じて PIN を入力した後、# キーを押します。 |
| テキスト メッセージ |6 桁のコードを含むテキスト メッセージがユーザーの携帯電話に送信されます。 ユーザーは、サインイン ページでこのコードを入力します。 |
| モバイル アプリの通知 |確認要求がユーザーのスマート フォンに送信されます。 ユーザーは、必要に応じて PIN を入力し、モバイル アプリで **[確認]** を選択します。 |
| モバイル アプリの確認コード |ユーザーのスマート フォンで実行されているモバイル アプリには、30 秒ごとに変更される確認コードが表示されます。 ユーザーは、最新のコードを確認し、サインイン ページでそのコードを入力します。 |
| サード パーティの OATH トークン | サード パーティの確認方法を受け入れるように Azure Multi-Factor Authentication Server を構成できます。 |

Azure Multi-Factor Authentication では、クラウドとサーバーの両方で検証方法を選択できます。 ユーザーが使用できる方法 (電話、テキスト、アプリの通知、またはアプリのコード) を選択できます。 詳細については、「[選択可能な検証方法](multi-factor-authentication-whats-next.md#selectable-verification-methods)」をご覧ください。

## <a name="next-steps"></a>次のステップ

- [Azure Multi-Factor Authentication のさまざまなバージョンと使用方法](multi-factor-authentication-versions-plans.md)を確認する

- Azure MFA を[クラウドとオンプレミス](multi-factor-authentication-get-started.md)のどちらにデプロイするかを選択します。

- [よく寄せられる質問](multi-factor-authentication-faq.md)の回答を確認します。