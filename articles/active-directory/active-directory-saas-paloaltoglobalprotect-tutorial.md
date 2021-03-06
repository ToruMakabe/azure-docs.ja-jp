---
title: "チュートリアル: Azure Active Directory と Palo Alto Networks - GlobalProtect の統合 | Microsoft Docs"
description: "Azure Active Directory と Palo Alto Networks - GlobalProtect の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 03bef6f2-3ea2-4eaa-a828-79c5f1346ce5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: 88322bc34183d6ee0b68cab7c59379e0f65ebecf
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2017
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---globalprotect"></a>チュートリアル: Azure Active Directory と Palo Alto Networks - GlobalProtect の統合

このチュートリアルでは、Palo Alto Networks - GlobalProtect と Azure Active Directory (Azure AD) を統合する方法について説明します。

Palo Alto Networks - GlobalProtect と Azure AD の統合には、次のメリットがあります。

- Palo Alto Networks - GlobalProtect にアクセスするユーザーを Azure AD で制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Palo Alto Networks - GlobalProtect にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Palo Alto Networks - GlobalProtect の統合を構成するには、次のアイテムが必要です。

- Azure AD サブスクリプション
- Palo Alto Networks - GlobalProtect でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Palo Alto Networks - GlobalProtect の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>ギャラリーからの Palo Alto Networks - GlobalProtect の追加
Azure AD への Palo Alto Networks - GlobalProtect の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Palo Alto Networks - GlobalProtect を追加する必要があります。

**ギャラリーから Palo Alto Networks - GlobalProtect を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Palo Alto Networks - GlobalProtect**」と入力し、結果ウィンドウで **[Palo Alto Networks - GlobalProtect]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Palo Alto Networks - GlobalProtect](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Palo Alto Networks - GlobalProtect で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Palo Alto Networks - GlobalProtect ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Palo Alto Networks - GlobalProtect の関連ユーザーの間で、リンク関係が確立されている必要があります。

Palo Alto Networks - GlobalProtect で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Palo Alto Networks - GlobalProtect で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Palo Alto Networks - GlobalProtect テスト ユーザーの作成](#create-a-palo-alto-networks---globalprotect-test-user)** - Palo Alto Networks - GlobalProtect で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Palo Alto Networks - GlobalProtect アプリケーションでシングル サインオンを構成します。

**Palo Alto Networks - GlobalProtect で Azure AD のシングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Palo Alto Networks - GlobalProtect** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_samlbase.png)

3. **[Palo Alto Networks - GlobalProtect のドメインと URL]** セクションで、次の手順を実行します。

    ![[Palo Alto Networks - GlobalProtect のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_url.png)

    a. **[サインオン URL]** ボックスに、`https://<Customer Firewall URL>` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<Customer Firewall URL>/SAML20/SP` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Palo Alto Networks - GlobalProtect クライアント サポート チーム](https://support.paloaltonetworks.com/support)に連絡してください。 
 
4. Palo Alto Networks - GlobalProtect アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成してください。 この属性の値は、アプリケーション統合ページの **[User Attributer]** セクションで管理できます。 次のスクリーンショットはその例です。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_attribute.png)
    
5. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、上の図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | --- | --- |    
    | username | user.userprincipalname |

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_attribute_05.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[値]** 一覧から、その行に対して表示される値を入力します。 例として user.userprincipalname という値でマップしていますが、実際は適切な値でマップしてください。 
    
    d. **[OK]** をクリックします。


6. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_certificate.png) 

7. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_400.png)

8. 別のブラウザー ウィンドウで Palo Alto サイトを管理者として開きます。

9. **[Device]\(デバイス\)** をクリックします。

    ![Palo Alto シングル サインオンの構成](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

10. 左側のナビゲーション バーから **[SAML Identity Provider]\(SAML ID プロバイダー\)** を選択し、[Import]\(インポート\) をクリックしてメタデータ ファイルをインポートします。

    ![Palo Alto シングル サインオンの構成](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

11. [Import]\(インポート\) ウィンドウで次の操作を実行します。

    ![Palo Alto シングル サインオンの構成](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. **[Profile Name]\(プロファイル名\)** ボックスに名前 (「Azure AD Global Protect」など) を入力します。
    
    b. **[Identity Provider Metadata]\(ID プロバイダーのメタデータ\)** の **[Browse]\(参照\)** をクリックし、Azure Portal からダウンロードした metadata.xml ファイルを選択します。
    
    c. **[OK]**

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-paloaltoglobalprotect-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-paloaltoglobalprotect-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-paloaltoglobalprotect-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-paloaltoglobalprotect-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-palo-alto-networks---globalprotect-test-user"></a>Palo Alto Networks - GlobalProtect テスト ユーザーの作成

Palo Alto Networks - GlobalProtect は Just-In-Time ユーザー プロビジョニングをサポートしているため、ユーザーがシステムに存在しなければ、正常に認証された後、自動的にユーザーが作成されます。 ここでは何も行う必要はありません。 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Palo Alto Networks - GlobalProtect へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Britta Simon を Palo Alto Networks - GlobalProtect に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Palo Alto Networks - GlobalProtect]** を選択します。

    ![アプリケーションの一覧の [Palo Alto Networks - GlobalProtect] リンク](./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_paloaltoglobal_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Palo Alto Networks - GlobalProtect のタイルをクリックすると、Palo Alto Networks - GlobalProtect アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-paloaltoglobalprotect-tutorial/tutorial_general_203.png

