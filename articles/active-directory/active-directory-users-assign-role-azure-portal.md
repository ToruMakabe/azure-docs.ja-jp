---
title: "Azure Active Directory でユーザーを管理者ロールに割り当てる | Microsoft Docs"
description: "Azure Active Directory でユーザーの管理情報を変更する方法について説明します。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: a1ca1a53-50d8-4bf0-ae8f-73fa1253e2d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.openlocfilehash: 290f2e837cae9bdb95cf8acb486938632927ca8c
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2017
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>Azure Active Directory でユーザーを管理者ロールに割り当てる
この記事では、Azure Active Directory (Azure AD) でユーザーに管理者ロールを割り当てる方法について説明します。 組織内の新しいユーザーの追加については、[Azure Active Directory への新しいユーザーの追加](active-directory-users-create-azure-portal.md)に関する記事をご覧ください。 既定では、追加されたユーザーに管理者のアクセス許可はありませんが、ロールはいつでも割り当てることができます。

## <a name="assign-a-role-to-a-user"></a>ユーザーにロールを割り当てる
1. ディレクトリのグローバル管理者のアカウントで [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
2. **[ユーザーとグループ]** を選択します。

   ![ユーザー管理を開く](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)
3. **[すべてのユーザー]** を選択します。

   ![すべてのユーザー グループを開く](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)
1. 一覧からユーザーを選択します。
2. 選択したユーザーで、**[ディレクトリの役割]** を選び、**[ディレクトリの役割]** 一覧のロールにユーザーを割り当てます。 ユーザーおよび管理者のロールの詳細については、「 [Azure AD での管理者ロールの割り当て](active-directory-assign-admin-roles-azure-portal.md)」を参照してください。

      ![ユーザーをロールに割り当てる](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)
6. **[ 保存]** を選択します。

## <a name="next-steps"></a>次のステップ
* [ユーザーの追加](active-directory-users-create-azure-portal.md)
* [新しい Azure Portal でのユーザーのパスワードのリセット](active-directory-users-reset-password-azure-portal.md)
* [ユーザーの勤務先情報の変更](active-directory-users-work-info-azure-portal.md)
* [ユーザー プロファイルの管理](active-directory-users-profile-azure-portal.md)
* [Azure AD でのユーザーの削除](active-directory-users-delete-user-azure-portal.md)
