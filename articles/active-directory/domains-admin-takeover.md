---
title: "Azure Active Directory にある非管理対象ディレクトリ (シャドウ テナント) を管理者が引き継ぐ | Microsoft Docs"
description: "Azure Active Directory にある非管理対象ディレクトリ (シャドウ テナント) の DNS ドメイン名を引き継ぐ方法です。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: b9f01876-29d1-4ab8-8b74-04d43d532f4b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: ee100fa86d78840a3b6a6bbf9453954c054931c2
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2017
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Azure Active Directory の非管理対象ディレクトリを管理者として引き継ぐ
この記事では、Azure Active Directory (Azure AD) の非管理対象ディレクトリにある DNS ドメイン名を引き継ぐ 2 つの方法について説明します。 セルフサービス ユーザーは、Azure AD を使用しているクラウド サービスにサインアップするときに、電子メールのドメインに基づいて管理されていない Azure AD ディレクトリに追加されます。 サービスに対するセルフサービス ("バイラル") サインアップについては、「[Azure のセルフサービス サインアップについて]()」をご覧ください。

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>非管理対象ディレクトリを引き継ぐ方法を決定する
管理者の引き継ぎのプロセスでは、「[カスタム ドメイン名を Azure Active Directory に追加する](add-custom-domain.md)」で説明されているように、所有権を証明できます。 次のセクションでは管理者エクスペリエンスを詳細に説明しますが、概要を以下に示します。

* 管理されていない Azure ディレクトリの ["内部" 管理者の引き継ぎ](#internal-admin-takeover)を実行、その非管理対象ディレクトリのグローバル管理者として追加されます。 管理しているその他のディレクトリには、ユーザー、ドメイン、またはサービス プランは移行されません。

* 管理されていない Azure ディレクトリの ["外部" 管理者の引き継ぎ](#external-admin-takeover)を実行すると、その非管理対象ディレクトリの DNS ドメイン名が管理対象の Azure ディレクトリに追加されます。 ドメイン名を追加するとき、ユーザーが引き続き中断されずにサービスにアクセスできるように、リソースに対するユーザーのマッピングが管理対象の Azure ディレクトリに作成されます。 

## <a name="internal-admin-takeover"></a>内部管理者の引き継ぎ

Office 365 など SharePoint と OneDrive が搭載されている一部の製品では、外部引き継ぎをサポートしていません。 このシナリオに該当する場合、または管理されていない ("シャドウ") テナントの作成をセルフサービス サインアップを使用したユーザーから引き継ぎたいと考えている管理者の場合は、内部管理者の引き継ぎを使ってこれを実行できます。

1. Power BI などのサインアップを使って、管理されていないテナントにユーザー コンテキストを作成します。 わかりやすい例にするために、この手順では Power BI を使用した方法を仮定します。

2. [Power BI サイト](https://powerbi.com)を開き、**[無料体験を開始する]** を選びます。 たとえば、`admin@fourthcoffee.xyz` のように、組織のドメイン名を使用しているユーザー アカウントを入力します。 認証コードを入力したら、電子メールで確認コードをチェックします。

3. Power BI からの確認の電子メールで、**[Yes, that's me]\(はい、私です\)** を選びます。

4. Power BI ユーザー アカウントで、[Office 365 管理センター](https://portal.office.com/adminportal/Home)にサインインします。 管理されていないテナントで既に確認済みのドメイン名の **[管理者になる]**  に誘導するメッセージを受信します。 **[Yes, I want to be the admin]\(はい、管理者になります\)** を選択します。
  
  ![[管理者になる] の最初のスクリーン ショット](./media/domains-admin-takeover/become-admin-first.png)
  
5. TXT レコードを追加して、ドメイン名のレジストラーでドメイン名 **fourthcoffee.xyz** を所有していることを証明します。 この例では、GoDaddy.com になります。
  
  ![ドメイン名の txt レコードを追加する](./media/domains-admin-takeover/become-admin-txt-record.png)

DNS TXT レコードがドメイン名のレジストラーで確認済みの場合、Azure AD テナントを管理できます。

上記の手順を完了すると、Office 365 の Fourth Coffee テナントのグローバル管理者になります。 お使いの他の Azure サービスとドメイン名を統合するには、ドメイン名を Office 365 から削除して、Azure の別の管理対象テナントに追加できます。

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>Azure AD の管理対象テナントにドメイン名を追加する 

1. [Office 365 管理センター](https://portal.office.com/adminportal/Home)を開きます。
2. **[ユーザー]** タブを選択し、カスタム ドメイン名を使用していない *user@fourthcoffeexyz.onmicrosoft.com* のような名前で、新しいユーザー アカウントを作成します。 
3. 新しいユーザー アカウントに Azure AD テナントのグローバル管理者特権が付与されていることを確認します。
4. Office 365 管理センターで **[ドメイン]** タブを開き、ドメイン名を選択して **[削除]** を選びます。 
  
  ![Office 365 からドメイン名を削除する](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. 削除されたドメイン名を参照しているユーザーまたはグループが Office 365 にある場合、.onmicrosoft.com ドメインに名前を変更する必要があります。 ドメイン名を強制的に削除した場合、すべてのユーザーの名前が自動的に変更されます。この例では、*user@fourthcoffeexyz.onmicrosoft.com* になります。
  
6. Azure AD テナントのグローバル管理者であるアカウントを使って、[Azure AD 管理センター](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)にサインインします。
  
7. **[カスタム ドメイン名]** を選択してドメイン名を追加します。 DNS TXT レコードを入力して、ドメイン名の所有権を確認する必要があります。 
  
  ![Azure AD に追加されたドメイン](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Office 365 テナントに割り当てられているライセンスを所有する、Power BI または Azure Rights Management サービスのユーザーは、ドメイン名が削除された場合、ダッシュ ボードを保存しておく必要があります。 *user@fourthcoffee.xyz* ではなく、*user@fourthcoffeexyz.onmicrosoft.com* のようなユーザー名でサインインする必要があります。

## <a name="external-admin-takeover"></a>外部管理者の引き継ぎ

Azure サービスまたは Office 365 を使って既にテナントを管理している場合、別の Azure AD テナントで既に確認されているカスタム ドメイン名を追加することはできません。 ただし、Azure AD で管理されているテナントから、外部管理者の引き継ぎとして管理されていないテナントを引き継ぐことは可能です。 一般的な手順については、[カスタム ドメインの Azure AD への追加](add-custom-domain.md)に関する記事に従ってください。

ドメイン名の所有権を確認するときは、Azure AD では、管理されていないテナントからドメイン名を削除し、既存のテナントに移動します。 非管理対象ディレクトリの外部管理者の引き継ぎには、内部管理者の引き継ぎと同じ DNS TXT の確認プロセスを行う必要があります。 相違点は、ドメイン名と共に次の内容も移行されることです。

- ユーザー
- サブスクリプション
- ライセンスの割り当て
 
ドメイン名の外部管理者の引き継ぎに対する [**ForceTakeover** オプション](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option)は、Power BI および Azure RMS の 2 つのサービスのみでサポートされています。

### <a name="support-for-external-admin-takeover"></a>外部管理者の引き継ぎのサポート
外部管理者の引き継ぎは、次のオンライン サービスによってサポートされています。

- Power BI
- Azure Rights Management Service (RMS)
- Exchange Online

サポートされているサービス プランは次のとおりです。

- Power BI Free
- Power BI Pro
- PowerApps Free
- PowerFlow Free
- Azure Rights Management Service Basic (RMS)
- Azure Rights Management Service Enterprise (RMS)
- Microsoft Stream
- Dynamics 365 無料試用版

外部管理者の引き継ぎは、たとえば、Office の無償のサブスクリプションや Office Basic SKU 経由など、SharePoint、OneDrive、または Skype For Business を含むサービス プランを保持しているサービスではサポートされません。

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>ForceTakeover オプションの Azure AD PowerShell コマンドレット
「[PowerShell の例](#powershell-example)」で使用されているコマンドレットを以下に示します。


コマンドレット | 使用法 
------- | -------
`connect-msolservice` | 指示に従い、管理対象テナントにサインインします。
`get-msoldomain` | 現在のテナントに関連付けられているドメイン名を表示します。
`new-msoldomain –name <domainname>` | "未確認" (DNS の確認がまだ実行されていない) としてテナントにドメイン名を追加します。
`get-msoldomain` | ドメイン名は、管理対象テナントに関連付けられているドメイン名の一覧に含まれるようになりましたが、"**未確認**" として表示されます。
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | ドメインの新しい DNS TXT レコード (MS=xxxxx) に含める情報を提示します。 TXT レコードのプロパゲートに一定の時間がかかるため、確認が直ちに行われるわけではありません。数分間待機してから、**-ForceTakeover** オプションを検討します。 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>お使いのドメイン名がまだ確認済みではない場合、**-ForceTakeover** オプションを使って続行できます。 TXT レコードが作成され、引き継ぎプロセスが開始されたことを確認します。<li>**- ForceTakeover** オプションは、引き継ぎをブロックしている Office 365 サービスが管理されていないテナントに含まれている場合など、外部管理者の引き継ぎを強制的に行う場合のみ、コマンドレットに追加する必要があります。
`get-msoldomain` | ドメインの一覧に、ドメイン名が "**確認済み**" と表示されるようになりました。

### <a name="powershell-example"></a>PowerShell の例

1. セルフ サービス プランに対応するために使用された資格情報を使用して Azure AD に接続します。
  ````
    import-module MSOnline
    $msolcred = get-credential
    
    connect-msolservice -credential $msolcred
  ````
2. ドメイン一覧を、次のコマンドレットで取得します。
  
  ````
    Get-MsolDomain
  ````
3. 次のように Get-MsolDomainVerificationDns コマンドレットを実行してチャレンジを作成します。
  ````
    Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
  
    For example:
  
    Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
  ````

4. このコマンドから返される値 (チャレンジ) をコピーします。 For example:
  ````
    MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
  ````
5. パブリック DNS 名前空間で、前の手順でコピーした値を含む DNS txt レコードを作成します。 このレコードの名前は親ドメインの名前です。このリソース レコードを Windows Server の DNS ロールを使用して作成する場合は、[レコード名] を空のままにして、テキスト ボックスに値を貼り付けます。
6. 次のように Confirm-MsolDomain コマンドレットを実行してチャレンジを確認します。
  
  ````
    Confirm-MsolEmailVerifiedDomain -DomainName *your_domain_name*
  ````
  
  For example:
  
  ````
    Confirm-MsolEmailVerifiedDomain -DomainName contoso.com
  ````

チャレンジがクリアされると、エラーなしでプロンプトに戻ります。

## <a name="next-steps"></a>次のステップ
* [Azure AD にカスタム ドメイン名を追加する](add-custom-domain.md)
* [Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure コマンドレット リファレンス](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
