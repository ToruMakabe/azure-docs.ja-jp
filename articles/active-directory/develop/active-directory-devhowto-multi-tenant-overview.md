---
title: "すべての Azure AD ユーザーがサインイン可能なアプリを構築する方法"
description: "任意の Azure Active Directory テナントのユーザーがサインイン可能なアプリケーション (マルチテナント アプリケーションと呼ばれます) を構築する手順について段階を追って説明します。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 35af95cb-ced3-46ad-b01d-5d2f6fd064a3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: b409aa762b60c6bed0ee26f4b9fa7c347d9eb997
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-sign-in-any-azure-active-directory-ad-user-using-the-multi-tenant-application-pattern"></a>マルチテナント アプリケーション パターンを使用してすべての Azure Active Directory (AD) ユーザーがサインインできるようにする方法
ソフトウェアを複数組織向けのサービス アプリケーションとして提供する場合、すべての Azure AD テナントからのサインインを受け入れるようにアプリケーションを構成できます。  Azure AD では、この構成はアプリケーションのマルチテナント化と呼ばれます。  すべての Azure AD テナントのユーザーは、アプリケーションで自分のアカウントを使用することに同意すれば、そのアプリケーションにサインインできるようになります。  

独自のアカウント システムを使用するか、他のクラウド プロバイダーの他の種類のサインインをサポートする既存のアプリケーションがある場合、任意のテナントからの Azure AD サインインの追加は簡単です。 アプリケーションを登録し、OAuth2、OpenID Connect、SAML を使用してサインイン コードを追加し、アプリケーションに [Microsoft でサインイン] ボタンを配置するだけです。 アプリケーションのブランド化の詳細を確認するには、次のボタンをクリックしてください。

[![[サインイン] ボタン][AAD-Sign-In]][AAD-App-Branding]

この記事では、Azure AD のシングル テナント アプリケーションの構築に慣れていることを前提としています。  まだ慣れていない場合は、[開発者ガイドのホームページ][AAD-Dev-Guide]に戻って、いずれかのクイック スタートをお試しください。

アプリケーションを Azure AD マルチテナント アプリケーションに変換する手順は、次の 4 つだけです。

1. アプリケーション登録をマルチテナントに更新する
2. /common エンドポイントに要求を送信するようにコードを更新する 
3. 複数の issuer 値を処理するようにコードを更新する
4. ユーザーおよび管理者の同意について理解し、コードに適切な変更を加える

それでは、各手順の詳細を見ていきましょう。 すぐに、[こちらのマルチテナント サンプルの一覧][AAD-Samples-MT]を参照してもかまいません。

## <a name="update-registration-to-be-multi-tenant"></a>登録をマルチテナントに更新する
既定では、Azure AD の Web アプリケーション/API の登録はシングル テナントです。  登録をマルチテナントにするには、[Azure ポータル][AZURE-portal]のアプリケーション登録のプロパティ ページで [マルチテナント] スイッチを見つけて、[はい] に設定します。

Azure AD でアプリケーションをマルチテナントにするには、そのアプリケーションのアプリケーション ID URI をグローバルに一意なものにする必要もあります。 アプリケーション ID URI は、プロトコル メッセージでアプリケーションを識別する手段の 1 つです。  シングル テナント アプリケーションの場合、アプリケーション ID URI はそのテナント内で一意であれば十分です。  マルチテナント アプリケーションの場合、Azure AD ですべてのテナントからそのアプリケーションを検索できるように、アプリケーション ID URI はグローバルに一意である必要があります。  グローバルな一意性は、アプリケーション ID URI のホスト名を Azure AD テナントの検証済みドメインと一致するものに設定することで実現できます。  たとえば、テナントの名前が contoso.onmicrosoft.com である場合、有効なアプリケーション ID URI は `https://contoso.onmicrosoft.com/myapp`のようになります。  また、テナントの検証済みドメインが `contoso.com` である場合は、有効なアプリケーション ID URI は `https://contoso.com/myapp` のようになります。  アプリ ID URI がこのパターンに従っていない場合、アプリケーションのマルチテナントとしての設定は失敗します。

ネイティブ クライアントの登録は、既定でマルチテナントです。  ネイティブ クライアントのアプリケーション登録については、マルチテナントにする操作を行う必要はありません。

## <a name="update-your-code-to-send-requests-to-common"></a>/common に要求を送信するようにコードを更新する
シングル テナント アプリケーションでは、サインイン要求はテナントのサインイン エンドポイントに送信されます。 たとえば、contoso.onmicrosoft.com のエンドポイントは次のようになります。

    https://login.microsoftonline.com/contoso.onmicrosoft.com

テナントのエンドポイントに要求が送信されることで、そのテナントのユーザー (またはゲスト) は当該テナントのアプリケーションにサインインできます。  マルチテナント アプリケーションでは、アプリケーションがユーザーのサインイン元のテナントを事前に知ることができないため、テナントのエンドポイントに要求を送信できません。  このため、要求は、すべての Azure AD テナントと多重通信するエンドポイントに送信されます。

    https://login.microsoftonline.com/common

Azure AD は、/common エンドポイントで要求を受信するとユーザーのサインインを行い、結果としてユーザーのサインイン元のテナントを特定します。  /common エンドポイントは、Azure AD でサポートされるすべての認証プロトコル (OpenID Connect、OAuth 2.0、SAML 2.0、WS-Federation) に対応しています。

その後のアプリケーションに対するサインイン応答には、ユーザーを表すトークンが含まれます。  アプリケーションは、トークンの issuer 値に基づいてユーザーのサインイン元のテナントを特定できます。  /common エンドポイントから応答が返された場合、トークン内の issuer 値はユーザーのテナントに対応しています。  /common エンドポイントはテナントや発行者ではなく、マルチプレクサーであることに注意してください。  /common を使用する場合、アプリケーションのトークン検証ロジックを、このことを考慮するように更新する必要があります。 

既に説明したとおり、マルチテナント アプリケーションは、Azure AD アプリケーションのブランド化ガイドラインに従って、一貫したサインイン エクスペリエンスをユーザーに提供する必要があります。 アプリケーションのブランド化の詳細を確認するには、次のボタンをクリックしてください。

[![[サインイン] ボタン][AAD-Sign-In]][AAD-App-Branding]

それでは、/common エンドポイントの使い方とコードの実装について、さらに詳しく見てみましょう。

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>複数の issuer 値を処理するようにコードを更新する
Web アプリケーションと Web API は、Azure AD からトークンを受信して検証します。  

> [!NOTE]
> ネイティブ クライアント アプリケーションは、Azure AD にトークンを要求して受信する際に API にトークンを送信し、API で検証が行われます。  ネイティブ アプリケーションではトークンを検証しないため、トークンを不透明なものとして処理する必要があります。
> 
> 

アプリケーションで Azure AD から受信したトークンが検証される仕組みを見てみましょう。  シングル テナント アプリケーションは通常、次のようなエンドポイント値を取得します。

    https://login.microsoftonline.com/contoso.onmicrosoft.com

そして、この値を使用して、次のようなメタデータ URL (この例では OpenID Connect) を作成します。

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

さらに、この URL を使用して、トークンの検証に使用する 2 種類の重要な情報である、テナントの署名キーと issuer 値をダウンロードします。  各 Azure AD テナントは、次のような形をした一意の issuer 値を持ちます。

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

ここで、GUID の値は、テナントのテナント ID を名前変更できるようにしたものです。  前述の `contoso.onmicrosoft.com` のメタデータ リンクをクリックすると、ドキュメントでこの issuer 値を確認できます。

シングル テナント アプリケーションでトークンを検証する際、トークンの署名を、メタデータ ドキュメントからの署名キーに照らしてチェックします。 このテストにより、トークン内の issuer 値が、メタデータ ドキュメント内で見つかった値に一致することを確認できます。

/common エンドポイントはテナントに対応しておらず発行者でもないため、/common のメタデータの issuer 値を確認すると、実際の値の代わりに次のようなテンプレート URL が表示されます。

    https://sts.windows.net/{tenantid}/

このため、マルチテナント アプリケーションでは、メタデータの issuer 値をトークンの `issuer` 値と照合するだけでは、トークンの検証を行うことができません。  マルチテナント アプリケーションには、issuer 値のテナント ID の部分に基づいて issuer 値が有効であるかどうかを判定するロジックが必要になります。  

たとえば、マルチテナント アプリケーションで、アプリケーションのサービスにサインアップしている特定のテナントからのサインインのみを許可するには、トークンの issuer 値または `tid` 要求値のいずれかを調べて、サブスクライバーのリストにテナントが含まれていることを確認する必要があります。  マルチテナント アプリケーションではユーザーのみを処理して、テナントに基づくアクセスの判定を行わない場合は、issuer 値を完全に無視することができます。

この記事の末尾の「[関連コンテンツ](#related-content)」セクションに記載されているマルチテナントのサンプルでは、Azure AD テナントがサインインできるようにするために issuer 値の検証が無効化されています。

それでは、マルチテナント アプリケーションにユーザーがサインインする場合のユーザー エクスペリエンスについて見ていきましょう。

## <a name="understanding-user-and-admin-consent"></a>ユーザーおよび管理者の同意について
Azure AD のアプリケーションにユーザーがサインインするには、そのアプリケーションがユーザーのテナントに表示される必要があります。  このようにすることで、組織では、ユーザーがテナントからアプリケーションにサインインする場合に一意のポリシーを適用するなどの操作を行うことができます。  シングル テナント アプリケーションの場合、この登録は簡単であり、[Azure ポータル][AZURE-portal] でのアプリケーションの登録時に行われます。

マルチテナント アプリケーションの場合、最初のアプリケーションの登録は、開発者が使用する Azure AD テナントに保存されます。  ユーザーが初めて別のテナントからこのアプリケーションにサインインすると、Azure AD により、アプリケーションで要求されるアクセス許可に同意するかどうかを尋ねられます。  同意した場合、アプリケーションを表す "*サービス プリンシパル*" と呼ばれるものがユーザーのテナントに作成され、サインインを続行できます。 また、アプリケーションに対するユーザーの同意を記録するデリゲートが、ディレクトリに作成されます。 アプリケーションのアプリケーション オブジェクトおよびサービス プリンシパル オブジェクトの詳細と、それらの関係については、「[アプリケーション オブジェクトおよびサービス プリンシパル オブジェクト][AAD-App-SP-Objects]」を参照してください。

![Consent to single-tier app][Consent-Single-Tier] 

この同意は、アプリケーションから要求されるアクセス許可によって異なります。  Azure AD では、アプリケーション専用アクセス許可と委任アクセス許可の 2 種類がサポートされます。

* 委任アクセス許可を付与されると、アプリケーションは、サインイン済みのユーザーとして、そのユーザーが実行可能な操作の一部を行うことができます。  たとえば、アプリケーションに対し、サインイン済みユーザーのカレンダーを読み取る委任アクセス許可を付与できます。
* アプリケーション専用アクセス許可は、アプリケーションの ID に直接付与されます。  たとえば、アプリケーションに、アプリケーションにサインインしているユーザーに関係なく、テナントのユーザーの一覧を読み取るアプリケーション専用アクセス許可を付与できます。

アクセス許可には、通常のユーザーが同意できるものと、テナント管理者の同意が必要なものがあります。 

### <a name="admin-consent"></a>管理者の同意
アプリケーション専用アクセス許可では、常にテナント管理者の同意が必要になります。  アプリケーションがアプリケーション専用アクセス許可を要求する場合に、ユーザーがそのアプリケーションにサインインしようとすると、このユーザーは同意できないことを示すエラー メッセージが表示されます。

一部の委任アクセス許可でも、テナント管理者の同意が必要になります。  たとえば、サインイン済みユーザーとして Azure AD に書き戻しを行うアクセス許可には、テナント管理者の同意が必要です。  アプリケーション専用アクセス許可と同様に、管理者の同意が必要な委任アクセス許可を要求するアプリケーションに通常のユーザーがサインインしようとすると、アプリケーションでエラーが発生します。  アクセス許可に管理者の同意が必要かどうかは、リソースを公開した開発者により決定されており、リソースのドキュメントに記載されています。  この記事の「 [関連コンテンツ](#related-content) 」セクションに、Azure AD Graph API および Microsoft Graph API で利用可能なアクセス許可に関するトピックへのリンクがあります。

アプリケーションで管理者の同意が必要なアクセス許可を使用する場合、ジェスチャ (管理者がアクションを開始できるボタンやリンク) を設定する必要があります。  通常、この操作に対してアプリケーションから送信される要求は OAuth2/OpenID Connect 承認要求ですが、この要求には `prompt=admin_consent` クエリ文字列パラメーターも含まれています。  管理者が同意し、ユーザーのテナントにサービス プリンシパルが作成されると、以降のサインイン要求では `prompt=admin_consent` パラメーターは不要になります。 管理者は要求されたアクセス許可を許容可能と判断しているため、その時点からは、テナント内の他のユーザーが同意を求められることはありません。

`prompt=admin_consent` パラメーターは、管理者の同意を必要としないアクセス許可を要求するアプリケーションでも使用できます。 これは、そのアプリケーターが、テナント管理者が 1 回 "サインアップする" と、その時点からは他のユーザーが同意を求められないエクスペリエンスを必要とする場合に実行されます。

アプリケーションが管理者の同意を必要とし、管理者はサインインしたが、`prompt=admin_consent` パラメーターが送信されない場合、管理者は**自分のユーザー アカウントについてのみ**アプリケーションへの同意に成功します。  通常のユーザーは、依然としてアプリケーションにサインインして同意することができません。  この機能は、他のユーザーのアクセスを許可する前に、テナント管理者がアプリケーションを調査できるようにしたい場合に役立ちます。

テナント管理者は、通常ユーザーによるアプリケーションへの同意を無効にすることができます。  通常ユーザーによる同意が無効化された場合、テナントでのアプリケーションのセットアップには常に管理者の同意が必要になります。  通常ユーザーによる同意を無効化した状態でアプリケーションのテストを行うには、[Azure ポータル][AZURE-portal]の Azure AD のテナント構成セクションにある構成スイッチを設定します。

> [!NOTE]
> 一部のアプリケーションでは、初めは通常ユーザーによる同意を許可してから、その後管理者に対して、管理者の同意が必要なアクセス許可を要求する必要があります。  現時点では、Azure AD での 1 回のアプリケーション登録でこのような処理を行う方法はありません。  次に予定している Azure AD Resource Manager デプロイメント モデル エンドポイントでは、アプリケーションが登録時ではなく、実行時にアクセス許可を要求できるようになります。それにより、このシナリオが可能になります。  詳細については、『[Azure AD App Model Resource Manager deployment model Developer Guide (Azure AD App Model Resource Manager デプロイメント モデル開発者ガイド)][AAD-V2-Dev-Guide]』を参照してください。
> 
> 

### <a name="consent-and-multi-tier-applications"></a>同意と多層アプリケーション
一部のアプリケーションは多層化されており、それぞれの層が個別の Azure AD 登録で表されている場合があります。  たとえば、Web API を呼び出すネイティブ アプリケーションや、Web API を呼び出す Web アプリケーションなどです。  どちらの場合でも、クライアント (ネイティブ アプリケーションまたは Web アプリケーション) は、リソース (Web API) を呼び出すアクセス許可を要求します。  クライアントがユーザーのテナントに対する同意を得られるようにするには、アクセス許可を要求されるリソースがすべて、あらかじめユーザーのテナントに存在する必要があります。  この条件が満たされない場合、Azure AD は、最初にリソースを追加する必要があることを示すエラーを返します。

**1 つのテナント内の複数の階層**

この処理は、論理アプリケーションが 2 つ以上のアプリケーション登録 (別々のクライアントとリソースなど) で構成されている場合に問題になる可能性があります。  まずユーザーのテナントにリソースを追加するにはどうすればいいのでしょうか。  Azure AD では、ワンステップでクライアントとリソースが同意されるようにすることによって、この状況に対応します。 ユーザーには、同意ページにクライアントとリソースの両方によって要求されたアクセス許可の合計が表示されます。  この動作を有効にするには、リソースのアプリケーション登録で、アプリケーションのマニフェストにクライアントのアプリケーション ID を `knownClientApplications` として含める必要があります。  次に例を示します。

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

このプロパティは、リソース [アプリケーションのマニフェスト][AAD-App-Manifest]によって更新できます。 この方法については、この記事の末尾の「[関連コンテンツ](#related-content)」セクションにある多層ネイティブ クライアントによる Web API 呼び出しのサンプルを参照してください。 次の図は、1 つのテナントに登録されている多層アプリケーションのための同意の概要を示しています。

![Consent to multi-tier known client app][Consent-Multi-Tier-Known-Client] 

**複数のテナント内の複数の階層**

同様のケースは、アプリケーションの各層を別々のテナントに登録する場合にも起こります。  たとえば、Office 365 Exchange Online API を呼び出すネイティブ クライアント アプリケーションを構築する場合を考えます。  ネイティブ アプリケーションを開発するため、また開発後にユーザーのテナントでこのネイティブ アプリケーションを実行するために、Exchange Online のサービス プリンシパルが存在する必要があります。  この場合、開発者とユーザーは、テナントでサービス プリンシパルを作成するために、Exchange Online を購入する必要があります。  

API が Microsoft 以外の組織によって作成されている場合、この API の開発者は、ユーザーがユーザーのテナントでアプリケーションに対して同意する手段を提供する必要があります。 推奨される設計は、サード パーティー開発者向けに、サインアップを実装する Web クライアントとしても機能できるような API を構築することです。

1. 前述のセクションに従って、API がマルチテナント アプリケーションの登録およびコード要件を実装していることを確認します。
2. API のスコープとロールの公開に加えて、登録に、"サインインとユーザー プロファイルの読み取り" Azure AD アクセス許可 (既定で提供) が含まれていることを確認します。
3. 前述の[管理者の同意](#admin-consent)のガイダンスに従って、Web クライアントでサインイン/サインアップ ページを実装します。 
4. ユーザーがアプリケーションに同意し、テナントにサービス プリンシパルと同意の委任のリンクが作成されたら、ネイティブ アプリケーションで API のトークンを取得できます。

次の図に、異なるテナントに登録されている多層アプリケーションの同意の概要を示します。

![Consent to multi-tier multi-party app][Consent-Multi-Tier-Multi-Party] 

### <a name="revoking-consent"></a>同意の取り消し
ユーザーおよび管理者は、次の方法により、いつでもアプリケーションに対する同意を取り消すことができます。

* ユーザーは、[[アクセス パネル アプリケーション]][AAD-Access-Panel] リストから個々のアプリケーションを削除することで、アプリケーションに対するアクセス許可を取り消すことができます。
* 管理者は、[Azure ポータル][AZURE-portal]の Azure AD の管理セクションで Azure AD からアプリケーションを削除することで、アプリケーションに対するアクセス許可を取り消すことができます。

管理者が、テナント内のすべてのユーザーについてアプリケーションに対して同意した場合、ユーザーは個別にアクセス許可を取り消すことはできません。  アクセス許可を取り消すことができるのは管理者のみであり、取り消しの対象はすべてのアプリケーションのみになります。

### <a name="consent-and-protocol-support"></a>同意とプロトコルのサポート
Azure AD では、同意は OAuth、OpenID Connect、WS-Federation、および SAML プロトコルでサポートされています。  SAML プロトコルと WS-Federation プロトコルでは `prompt=admin_consent` パラメーターがサポートされないため、管理者の同意は OAuth と OpenID Connect でのみ使用可能です。

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>マルチテナント アプリケーションとアクセス トークンのキャッシュ
マルチテナント アプリケーションでは、Azure AD で保護されている API を呼び出すアクセス トークンを取得することもできます。  マルチテナント アプリケーションで Active Directory Authentication Library (ADAL) を使用する際によくあるエラーは、最初は /common を使用してユーザーのトークンを要求し、応答を受信してから、その後も /common を使用して同じユーザーのトークンを要求することです。  Azure AD からの応答は /common ではなくテナントから送信されるため、ADAL ではトークンがテナントから送信されたものとしてキャッシュされます。 ユーザーのアクセス トークンを取得するためのその後の /common への呼び出しでは、キャッシュ エントリが見つからないため、ユーザーはもう一度サインインするように求められます。  キャッシュが見つからない問題を回避するために、サインイン済みのユーザーに対する以降の呼び出しは、テナントのエンドポイントに向けて行われるようにしてください。

## <a name="next-steps"></a>次のステップ
この記事では、任意の Azure Active Directory テナントからユーザーをサインインさせることのできるアプリケーションを構築する方法を説明しました。 アプリと Azure Active Directory の間でのシングル サインオンを有効にした後、Office 365 のように、アプリケーションを更新して Microsoft リソースによって公開される API にアクセスすることもできます。 したがって、パーソナライズされたエクスペリエンスをアプリケーションに提供できます。たとえば、プロファイル画像や次の予定などのコンテキスト情報をユーザーに表示できます。 Azure Active Directory や、Exchange、SharePoint、OneDrive、OneNote、Planner、Excel などの Office 365 サービスへの API 呼び出しを行う方法の詳細については、[Microsoft Graph API][MSFT-Graph-overview] を参照してください。


## <a name="related-content"></a>関連コンテンツ
* [マルチテナント アプリケーションのサンプル][AAD-Samples-MT]
* [アプリケーションのブランド化ガイドライン][AAD-App-Branding]
* [Azure AD 開発者ガイド][AAD-Dev-Guide]
* [アプリケーション オブジェクトおよびサービス プリンシパル オブジェクト][AAD-App-SP-Objects]
* [Azure Active Directory とアプリケーションの統合][AAD-Integrating-Apps]
* [同意フレームワークの概要][AAD-Consent-Overview]
* [Microsoft Graph API のアクセス許可スコープ][MSFT-Graph-permision-scopes]
* [Azure AD Graph API のアクセス許可スコープ][AAD-Graph-Perm-Scopes]

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]: ./active-directory-branding-guidelines.md
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Consent-Overview]: ./active-directory-integrating-applications.md#overview-of-the-consent-framework
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: https://graph.microsoft.io/en-us/docs/overview/overview
[MSFT-Graph-permision-scopes]: https://graph.microsoft.io/en-us/docs/authorization/permission_scopes

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-single-tier.png
[Consent-Multi-Tier-Known-Client]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-known-clients.png
[Consent-Multi-Tier-Multi-Party]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-multi-party.png

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AAD-V2-Dev-Guide]: ../active-directory-appmodel-v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken














