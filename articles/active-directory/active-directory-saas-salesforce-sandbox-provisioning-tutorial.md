---
title: "チュートリアル: Azure Active Directory と Salesforce Sandbox の統合 | Microsoft Docs"
description: "Azure Active Directory と Salesforce Sandbox の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: bab73fda-6754-411d-9288-f73ecdaa486d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: jeedes
ms.openlocfilehash: c6c5d01a831a76c497f42fe4af3d90f8c5dc5903
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2017
---
# <a name="tutorial-configuring-salesforce-sandbox-for-automatic-user-provisioning"></a>チュートリアル: Salesforce Sandbox に自動ユーザー プロビジョニングを構成する

このチュートリアルでは、Azure AD から Salesforce Sandbox にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除するうえで Salesforce Sandbox と Azure AD で実行する必要がある手順について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

*   Azure Active Directory テナント。
*   Salesforce Sandbox for Work または Salesforce Sandbox for Education の有効なテナント。 どちらのサービスにも無料試用版のアカウントを使用できます。
*   Team Admin アクセス許可がある Salesforce Sandbox のユーザー アカウント

## <a name="assigning-users-to-salesforce-sandbox"></a>Salesforce Sandbox へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

プロビジョニング サービスを構成して有効にする前に、Salesforce Sandbox アプリへのアクセスが必要な Azure AD 内のユーザーやグループを決定しておく必要があります。 これを決定したら、[エンタープライズ アプリへのユーザーまたはグループの割り当て](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)に関する手順に従って、これらのユーザーを Salesforce Sandbox アプリに割り当てることができます。

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>ユーザーを Salesforce Sandbox に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Salesforce Sandbox に割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Salesforce Sandbox にユーザーを割り当てるときに、有効なユーザー ロールを選択する必要があります。 "既定のアクセス" ロールはプロビジョニングでは使えません。

> [!NOTE]
> このアプリにより、プロビジョニング プロセスの一環として Salesforce Sandbox からカスタム ロールがインポートされます。顧客はユーザーを割り当てるとき、ロールを選択します。

## <a name="enable-automated-user-provisioning"></a>自動化されたユーザー プロビジョニングを有効にする

このセクションでは、Azure AD を Salesforce Sandbox のユーザー アカウント プロビジョニング API に接続する手順のほか、プロビジョニング サービスを構成して、Azure AD のユーザーとグループの割り当てに基づいて割り当て済みのユーザー アカウントを Salesforce Sandbox で作成、更新、無効化する手順を説明します。

>[!Tip]
>Salesforce Sandbox では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Azure Portal](https://portal.azure.com) で説明されている手順に従ってください。 シングル サインオンは自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="configure-automatic-user-account-provisioning"></a>自動ユーザー アカウント プロビジョニングを構成する

このセクションでは、Active Directory のユーザー アカウントのユーザー プロビジョニングを Salesforce Sandbox に対して有効にする方法について説明します。

1. [Azure Portal](https://portal.azure.com) で、**[Azure Active Directory]、[エンタープライズ アプリ]、[すべてのアプリケーション]** セクションの順に移動します。

2. シングル サインオンのために Salesforce Sandbox を既に構成している場合は、検索フィールドで Salesforce Sandbox のインスタンスを検索します。 構成していない場合は、**[追加]** を選択してアプリケーション ギャラリーで **Salesforce Sandbox** を検索します。 検索結果から Salesforce Sandbox を選択してアプリケーションの一覧に追加します。

3. Salesforce Sandbox のインスタンスを選択してから、**[プロビジョニング]** タブを選択します。

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![プロビジョニング](./media/active-directory-saas-salesforce-sandbox-provisioning-tutorial/provisioning.png)

5. **[管理者資格情報]** セクションに次の構成設定を指定します。
   
    a. **[管理ユーザー名]** ボックスに、Salesforce.com で**システム管理者**プロファイルが割り当てられている Salesforce Sandbox アカウント名を入力します。
   
    b. **[管理パスワード]** テキストボックスに、このアカウントのパスワードを入力します。

6. Salesforce Sandbox のセキュリティ トークンを取得するには、新しいタブを開き、同じ Salesforce Sandbox の管理者アカウントにサインインします。 ページの右上にある自分の名前をクリックし **[Settings]\(設定\)** をクリックします。

     ![Enable automatic user provisioning](./media/active-directory-saas-salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "Enable automatic user provisioning")

7. 左側のナビゲーション ウィンドウで **[私の個人情報]** をクリックして関連するセクションを展開し、**[私のセキュリティ トークンのリセット]** をクリックします。
  
    ![Enable automatic user provisioning](./media/active-directory-saas-salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "Enable automatic user provisioning")

8. **[セキュリティ トークンのリセット]** ページで、**[セキュリティ トークンのリセット]** ボタンをクリックします。

    ![Enable automatic user provisioning](./media/active-directory-saas-salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "Enable automatic user provisioning")

9. この管理アカウントに関連付けられている電子メールの受信トレイを確認します。 新しいセキュリティ トークンが記載された Salesforce Sandbox.com からの電子メールを探します。

10. トークンをコピーして Azure AD のウィンドウに移動し、**[シークレット トークン]** フィールドに貼り付けます。

11. Azure Portal で、**[テスト接続]** をクリックして Azure AD が Salesforce Sandbox アプリに接続できることを確認します。

12. プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力して、チェック ボックスをオンにします。

13. **[保存]** をクリックします。  
    
14.  [マッピング] セクションの **[Azure Active Directory ユーザーを Salesforce Sandbox に同期する]** を選択します。

15. **[属性マッピング]** セクションで、Azure AD から Salesforce Sandbox に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Salesforce Sandbox のユーザー アカウントとの照合に使用されることに注意してください。 [保存] ボタンをクリックして変更をコミットします。

16. Salesforce Sandbox に対して Azure AD プロビジョニング サービスを有効にするには、[設定] セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

17. **[保存]** をクリックします。

これで、[ユーザーとグループ] セクションで Salesforce Sandbox に割り当てたユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、サービスが実行されている限り約 20 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、プロビジョニング サービスによって Salesforce Sandbox アプリに対して実行されたすべてのアクションが記載されています。

これでテスト アカウントを作成できるようになりました。 ここで 20 分間待機し、アカウントが Salesforce に同期されたことを確認します。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)
* [シングル サインオンの構成](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-sandbox-tutorial)
