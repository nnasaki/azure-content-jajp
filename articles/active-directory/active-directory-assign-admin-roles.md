<properties
	pageTitle="Azure Active Directory の管理者ロールの割り当て | Microsoft Azure"
	description="Azure Active Directory で管理者ロールが使用できる機能と、管理者ロールを割り当てる方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="curtand"/>

# Azure Active Directory (Azure AD) の管理者ロールの割り当て

会社の規模によっては、さまざまな役割を果たす複数の管理者を選任することができます。これらの管理者は、Azure ポータルまたは Azure クラシック ポータルでさまざまな機能にアクセスでき、ロールに応じて、ユーザーの作成または編集、他のユーザーへの管理者ロールの割り当て、ユーザー パスワードのリセット、ユーザー ライセンスの管理、ドメインの管理などを行うことができます。

管理者ロールを Office 365 ポータルと Azure クラシック ポータルのどちらで割り当てたのか、Windows PowerShell 用 Azure AD モジュールを使用して割り当てたのかに関係なく、管理者ロールが割り当てられたユーザーは、組織がサブスクライブしているすべてのクラウド サービスで同じ権限を持つということを理解しておくことが重要です。

次の管理者ロールを使用できます。

- **課金管理者**: 購入、サブスクリプションの管理、サポート チケットの管理、サービス正常性の監視を行います。

- **グローバル管理者**: すべての管理機能にアクセスできます。Azure アカウントにサインアップしたユーザーがグローバル管理者になります。他の管理者ロールを割り当てることができるのはグローバル管理者だけです。会社に複数のグローバル管理者が存在してかまいません。

- **パスワード管理者**: パスワードのリセット、サービス要求の管理、サービス正常性の監視を行います。パスワード管理者がリセットできるのは、ユーザーと他のパスワード管理者のパスワードだけです。

- **サービス管理者**: サービス要求の管理とサービス正常性の監視を行います。

	> [AZURE.NOTE] サービス管理者のロールをユーザーに割り当てるには、グローバル管理者がまずサービスで管理権限をユーザーに割り当て、次に Azure クラシック ポータルでサービス管理者ロールをそのユーザーに割り当ててください。

- **ユーザー管理者**: パスワードのリセット、サービス正常性の監視、ユーザー アカウント、ユーザー グループ、およびサービス要求の管理を行います。ユーザー管理の管理者の権限には、いくつかの制限が適用されます。たとえば、この管理者は、グローバル管理者を削除することも、他の管理者を作成することもできません。また、課金管理者、グローバル管理者、サービス管理者のパスワードをリセットすることもできません。

## 管理者の権限

### 課金管理者

できること | できないこと
------------- | -------------
<p>会社情報とユーザー情報の表示</p><p>Office サポート チケットの管理</p><p>Office 製品の課金操作と購入操作の実行</p> | <p>ユーザー パスワードのリセット</p><p>ユーザー ビューの作成と管理</p><p>ユーザーとグループの作成、編集、削除、およびユーザー ライセンスの管理</p><p>ドメインの管理</p><p>会社情報の管理</p><p>他のユーザへの管理者ロールの委任</p><p>ディレクトリ同期の使用</p>

### グローバル管理者

できること | できないこと
------------- | -------------
<p>会社情報とユーザー情報の表示</p><p>Office サポート チケットの管理</p><p>Office 製品の課金操作と購入操作の実行</p> <p>ユーザー パスワードのリセット</p><p>ユーザー ビューの作成と管理</p><p>ユーザーとグループの作成、編集、削除、およびユーザー ライセンスの管理</p><p>ドメインの管理</p><p>会社情報の管理</p><p>他のユーザーへの管理者ロールの委任</p><p>ディレクトリ同期の使用</p><p>Multi-Factor Authentication の有効化または無効化</p> | 該当なし

### パスワード管理者

できること | できないこと
------------- | -------------
<p>会社情報とユーザー情報の表示</p><p>Office サポート チケットの管理</p><p>ユーザー パスワードのリセット</p> | <p>Office 製品の課金操作と購入操作の実行</p><p>ユーザー ビューの作成と管理</p><p>ユーザーとグループの作成、編集、削除、およびユーザー ライセンスの管理</p> <p>ドメインの管理</p><p>会社情報の管理</p><p>他のユーザーへの管理者ロールの委任</p><p>ディレクトリ同期の使用</p>

### サービス管理者

できること | できないこと
------------- | -------------
<p>会社情報とユーザー情報の表示</p><p>Office サポート チケットの管理</p> | <p>ユーザー パスワードのリセット</p><p>Office 製品の課金操作と購入操作の実行</p><p>ユーザー ビューの作成と管理</p> <p>ユーザーとグループの作成、編集、削除、およびユーザー ライセンスの管理</p><p>ドメインの管理</p><p>会社情報の管理</p><p>他のユーザーへの管理者ロールの委任</p><p>ディレクトリ同期の使用</p>

### ユーザー管理者

できること | できないこと
------------- | -------------
<p>会社情報とユーザー情報の表示</p><p>Office サポート チケットの管理</p><p>ユーザー パスワードのリセット (制限付き)。課金管理者、グローバル管理者、サービス管理者のパスワードをリセットすることはできません。</p><p>ユーザー ビューの作成と管理</p><p>ユーザーとグループの作成、編集、削除、およびユーザー ライセンスの管理 (制限付き)。グローバル管理者を削除することも、他の管理者を作成することもできません。</p> | <p>Office 製品の課金操作と購入操作の実行</p><p>ドメインの管理</p><p>会社情報の管理</p><p>他のユーザーへの管理者ロールの委任</p><p>ディレクトリ同期の使用</p><p>Multi-Factor Authentication の有効化または無効化</p>

## グローバル管理者ロールの詳細

グローバル管理者は、すべての管理機能にアクセスできます。既定では、Azure サブスクリプションにサインアップしたユーザーには、ディレクトリのグローバル管理者ロールが割り当てられます。他の管理者ロールを割り当てることができるのはグローバル管理者だけです。

## 管理者ロールの割り当てまたは削除

1. Azure クラシック ポータルで **[Active Directory]** をクリックし、該当する組織のディレクトリ名をクリックします。

2. **[ユーザー]** ページで、編集するユーザーの表示名をクリックします。

3. **[組織のロール]** リストで、このユーザーに割り当てる管理者ロールを選択します。既存の管理者ロールを削除する場合は、**[ユーザー]** を選択します。

4. **[連絡用電子メール アドレス]** ボックスに、電子メール アドレスを入力します。この電子メール アドレスは、パスワードのセルフリセットなどの重要な通知に使用されるので、ユーザーは、Azure にアクセスできるかどうかにかかわらず、この電子メール アカウントにアクセスできる必要があります。

5. **[許可]** または **[ブロック]** を選択して、サービスへのサインインとアクセスをユーザーに許可するかどうかを指定します。

6. **[利用場所]** ボックスの一覧で場所を指定します。

7. 操作が完了したら、**[保存]** をクリックします。

## 次のステップ

- Azure サブスクリプションの管理者を変更する方法の詳細については、「[Azure 管理者ロールを追加または変更する方法](../billing-add-change-azure-subscription-administrator.md)」を参照してください。

- Microsoft Azure でリソース アクセスを制御する方法の詳細については、「[Azure でのリソース アクセスについて](active-directory-understanding-resource-access.md)」を参照してください。

- Azure Active Directory と Azure サブスクリプションの関係の詳細については、「[Azure サブスクリプションを Azure Active Directory に関連付ける方法](active-directory-how-subscriptions-associated-directory.md)」を参照してください。

- [ユーザーの管理](active-directory-create-users.md)

- [パスワードの管理](active-directory-manage-passwords.md)

- [グループの管理](active-directory-manage-groups.md)

<!---HONumber=AcomDC_0427_2016-->