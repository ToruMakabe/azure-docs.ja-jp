---
title: "Azure AD Connect: パススルー認証 - よく寄せられる質問 | Microsoft Docs"
description: "Azure Active Directory パススルー認証に関してよく寄せられる質問への回答です。"
services: active-directory
keywords: "Azure AD Connect パススルー認証, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: d6a405f7245bf1b9635872efd0e29f8361d6a2f6
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/28/2017
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Azure Active Directory パススルー認証: よく寄せられる質問

この記事では、Azure Active Directory (Azure AD) パススルー認証に関してよく寄せられる質問に回答します。 最新のコンテンツを常にチェックしてください。

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Azure AD へのサインイン方法として、パススルー認証、パスワード ハッシュ同期、Active Directory フェデレーション サービス (AD FS) のうちどれを選択すればよいですか。

オンプレミスの環境と組織の要件によって異なります。 Azure AD のさまざまなサインイン方法を比較するには、「[Azure AD Connect ユーザーのサインイン オプション](active-directory-aadconnect-user-signin.md)」をご覧ください。

## <a name="is-pass-through-authentication-a-free-feature"></a>パススルー認証は無料の機能ですか。

パススルー認証は、無料の機能です。 この機能を使用するために Azure AD の有料エディションは不要です。

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>パススルー認証は [Microsoft Azure Germany クラウド](http://www.microsoft.de/cloud-deutschland)および [Microsoft Azure Government クラウド](https://azure.microsoft.com/features/gov/)で使用できますか。


いいえ。 パススルー認証は、世界中の Azure AD のインスタンスでのみ使用できます。

## <a name="does-conditional-accessactive-directory-conditional-access-azure-portalmd-work-with-pass-through-authentication"></a>[条件付きアクセス](../active-directory-conditional-access-azure-portal.md)は、パススルー認証と連携しますか。

はい。 Microsoft Azure Multi-Factor Authentication を含め、すべての条件付きアクセス機能がパススルー認証と連携します。

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>パススルー認証では、"userPrincipalName" の代わりに "Alternate ID" がユーザー名としてサポートされますか。

はい。 `Alternate ID` は、Azure AD Connect で構成されている場合はユーザー名としてサポートされます。 詳細については、「[Azure AD Connect のカスタム インストール](active-directory-aadconnect-get-started-custom.md)」をご覧ください。 すべての Office 365 アプリケーションが `Alternate ID` をサポートしているわけではありません。 サポートの説明については、それぞれのアプリケーションのドキュメントを参照してください。

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>パスワード ハッシュ同期は、パススルー認証のフォールバックとして機能しますか。


いいえ。 パススルー認証は、パスワード ハッシュ同期に自動的にフェールオーバー_されません_。 この同期は、[パススルー認証で現在サポートされていないシナリオ](active-directory-aadconnect-pass-through-authentication-current-limitations.md#unsupported-scenarios)のフォールバックとしてのみ機能します。 ユーザーのサインイン エラーを回避するには、[高可用性](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability)向けにパススルー認証を構成する必要があります。

## <a name="can-i-install-an-azure-ad-application-proxyactive-directory-application-proxy-get-startedmd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>[Azure AD アプリケーション プロキシ](../active-directory-application-proxy-get-started.md) コネクタを、パススルー認証エージェントと同じサーバーにインストールできますか。

はい。 この構成は、パススルー認証エージェント (バージョン 1.5.193.0 以降) のブランド名変更バージョンでサポートされます。

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>どのバージョンの Azure AD Connect とパススルー認証エージェントが必要ですか。

この機能を動作させるには、Azure AD Connect については 1.1.486.0 以降、パススルー認証エージェントについては 1.5.58.0 以降が必要です。 すべてのソフトウェアを、Windows Server 2012 R2 以降が搭載されたサーバーにインストールする必要があります。

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>ユーザーのパスワードの有効期限が切れている場合に、パススルー認証を使用してサインインしようとすると、どうなりますか。

特定のユーザーに対して[パスワード ライトバック](../active-directory-passwords-update-your-own-password.md)を構成済みの場合、パススルー認証を使用してサインインしたユーザーは、パスワードを変更またはリセットできます。 パスワードはオンプレミス Active Directory に想定どおりに書き戻されます。

特定のユーザーにパスワード ライトバックが構成されていない場合、またはユーザーに有効な Azure AD ライセンスが割り当てられていない場合、ユーザーはクラウドでパスワードを更新できません。 自身のパスワードの有効期限が切れた場合でも、そのパスワードを更新することはできません。 代わりに、"このサイトでパスワードを変更することをお客様の組織が許可していません。 組織によって推奨されている方法でパスワードを更新するか、サポートが必要な場合は管理者にお問い合わせください。" というメッセージが表示されます。 ユーザーまたは管理者は、オンプレミスの Active Directory でパスワードをリセットする必要があります。

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>パススルー認証を使用することで、どのようにブルート フォース パスワード攻撃から保護できますか。

詳細は、「[Azure Active Directory パススルー認証: スマート ロックアウト](active-directory-aadconnect-pass-through-authentication-smart-lockout.md)」をご覧ください。

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>パススルー認証エージェントはポート 80 およびポート 443 で何をやり取りしますか。

- 認証エージェントは、すべての機能操作について、ポート 443 で HTTPS 要求を行います。
- 認証エージェントは、SSL 証明書失効リスト (CRL) をダウンロードするために、ポート 80 で HTTP 要求を行います。

     >[!NOTE]
     >この機能に必要なポートの数は、最近の更新で減らされています。 以前のバージョンの Azure AD Connect または認証エージェントを使用している場合は、5671、8080、9090、9091、9350、9352、および 10100 - 10120 の各ポートも開放しておいてください。

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>パススルー認証エージェントは、送信 Web プロキシ サーバーで通信できますか。

はい。 Web プロキシ自動発見 (WPAD) がオンプレミス環境で有効になっている場合、認証エージェントは、ネットワーク上の Web プロキシ サーバーを自動的に検索し、使用しようとします。

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>同じサーバーに複数のパススルー認証エージェントをインストールできますか。

いいえ。1 つのサーバーにインストールできるパススルー認証エージェントは 1 つだけです。 高可用性向けにパススルー認証を構成する必要がある場合は、「[Azure Active Directory パススルー認証: クイック スタート](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability)」の手順に従ってください。

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>既に AD FS を使用して、Azure AD にサインインしています。 これをパススルー認証に切り替えるには、どうすればよいですか。

Azure AD Connect ウィザードを使用して、AD FS をサインイン方法として構成している場合は、ユーザーのサインイン方法をパススルー認証に変更します。 この変更により、テナントでパススルー認証が有効になり、"_すべて_" のフェデレーション ドメインが管理対象ドメインに変換されます。 テナントへの以降のサインイン要求はすべて、パススルー認証によって処理されます。 現時点では、異なるドメイン間で AD FS とパススルー認証を組み合わせて使用する方法は、Azure AD Connect ではサポートされていません。

Azure AD Connect ウィザード "_以外_" で、AD FS がサインイン方法として構成された場合は、ユーザーのサインイン方法をパススルー認証に変更します。 この変更は、**[構成しない]** オプションから行うことができます。 この変更により、テナントでパススルー認証が有効になりますが、すべてのフェデレーション ドメインでは引き続きサインインに AD FS が使用されます。 こうしたフェデレーション ドメインの一部またはすべてを管理対象ドメインに変換するには、PowerShell を使用して手動で行います。 この変更を加えると、管理対象ドメインへのサインイン要求はすべて、パススルー認証 "*のみ*" によって処理されます。

>[!IMPORTANT]
>パススルー認証では、クラウド専用 Azure AD ユーザーのサインインは処理されません。

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>複数フォレスト Active Directory 環境でパススルー認証を使用できますか。

はい。 ご使用の Active Directory フォレスト間にフォレストの信頼があり、名前サフィックス ルーティングが正しく構成されていれば、複数フォレスト環境がサポートされます。

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>いくつのパススルー認証エージェントをインストールする必要がありますか。

複数のパススルー認証エージェントをインストールすることで[高可用性](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability)が確保されます。 ただし認証エージェント間の確定的な負荷分散は提供されません。

テナント上で想定されるサインイン要求のピーク時の負荷および平均的な負荷を検討してください。 ベンチマークとして、1 つの認証エージェントでは、標準的な 4 コア CPU、16 GB RAM サーバー上で 1 秒あたり 300,000 - 400,000 の認証を処理できます。 ほとんどのお客様の場合、高可用性と大容量を確保するには、合計 2 つまたは 3 つの認証エージェントがあれば十分です。

サインインの待機時間を向上させるために、認証エージェントは、ドメイン コントローラーの近くにインストールする必要があります。

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Azure AD Connect が実行されているのとは別のサーバーに、最初のパススルー認証エージェントをインストールできますか。

いいえ、このシナリオはサポートされて "_いません_"。

## <a name="how-many-pass-through-authentication-agents-should-i-install"></a>いくつのパススルー認証エージェントをインストールする必要がありますか。

推奨事項は次のとおりです。

- 合計で 2 ～ 3 の認証エージェントをインストールします。 この構成でほとんどのユーザーの用途に対応できます。
- サインインの待機時間を向上させるには、認証エージェントを、ドメイン コントローラーに、またはドメイン コントローラーのできるだけ近くにインストールします。
- パスワードを検証する必要があるユーザーと同じ Active Directory フォレストに、認証エージェントをインストールしたサーバーが追加されていることを確認します。

>[!NOTE]
>認証エージェントの数は、テナントあたり 12 個に制限されています。

## <a name="how-can-i-disable-pass-through-authentication"></a>パススルー認証を無効にするには、どうすればよいですか。

Azure AD Connect ウィザードを再実行し、ユーザーのサインイン方法を、パススルー認証から別の方法に変更します。 この変更により、テナントのパススルー認証が無効になり、認証エージェントがサーバーからアンインストールされます。 他のサーバーからは、手動で認証エージェントをアンインストールする必要があります。

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>パススルー認証エージェントをアンインストールすると、どうなりますか。

サーバーからパススルー認証エージェントをアンインストールすると、そのサーバーでサインイン要求の受け入れが停止します。 テナントでユーザーのサインインが中断しないようにするには、パススルー認証エージェントをアンインストールする前に、別の認証エージェントが実行されていることを確認します。

## <a name="next-steps"></a>次のステップ
- [現在の制限](active-directory-aadconnect-pass-through-authentication-current-limitations.md): サポートされているシナリオと、サポートされていないシナリオを確認します。
- [クイック スタート](active-directory-aadconnect-pass-through-authentication-quick-start.md): Azure AD パススルー認証を起動および実行します。
- [スマート ロックアウト](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): ユーザー アカウントを保護するようにテナントのスマート ロックアウト機能を構成する方法を確認します。
- [技術的な詳細](active-directory-aadconnect-pass-through-authentication-how-it-works.md): パススルー認証機能のしくみを理解します。
- [トラブルシューティング](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): パススルー認証機能に関する一般的な問題を解決する方法を確認します。
- [セキュリティの詳細](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): パススルー認証機能に関する詳細な技術情報を取得します。
- [Azure AD シームレス SSO](active-directory-aadconnect-sso.md): この補完的な機能の詳細を確認します。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Azure Active Directory フォーラムで、新しい機能の要望を出します。

