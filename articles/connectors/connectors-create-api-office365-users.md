<properties
	pageTitle="PowerApps Enterprise または Logic Apps に Office 365 Users API を追加する | Microsoft Azure"
	description="Office 365 Users API と REST API パラメーターの概要"
	services=""	
	documentationCenter="" 	
	authors="msftman"	
	manager="erikre"	
	editor="" 
	tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="03/16/2016"
ms.author="deonhe"/>

# Office 365 Users API を使ってみる

Office 365 ユーザーに接続して、プロファイルの取得、ユーザーの検索などを行います。Office 365 Users API は、以下のツールから使用できます。

- Logic Apps 
- PowerApps

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/connectors-create-api-office365-users.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-office365-users.md)

&nbsp;

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。


Office 365 ユーザーは、次のことを行えます。

- Office 365 ユーザーから取得したデータに基づいてビジネス フローを構築できます。 
- 直属の部下の取得、上司のユーザーのプロファイルの取得などのアクションを使用できます。また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。たとえば、ユーザーの直属の部下を取得し、この情報を利用して、SQL Azure Database を更新します。 
- PowerApps Enterprise に Office 365 Users API を追加します。追加すると、ユーザーはアプリ内で API を使用できるようになります。 

PowerApps Enterprise に API を追加する方法については、[PowerApps での API の登録](../power-apps/powerapps-register-from-available-apis.md)に関するページをご覧ください。

ロジック アプリに操作を追加する方法については、[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)に関するページをご覧ください。

## トリガーとアクション

Office 365 Users API では、次のアクションを使用できます。トリガーはありません。

| トリガー | アクション|
| --- | --- |
|なし | <ul><li>上司の取得</li><li>自分のプロファイルを取得</li><li>直属の部下を取得</li><li>ユーザー プロファイルの取得</li><li>ユーザーの検索</li></ul>|

すべての API は、JSON および XML 形式のデータに対応します。


## Office 365 Users への接続を作成します

この API をロジック アプリに追加する場合は、Office 365 ユーザー アカウントにサインインして、ロジック アプリでアカウントに接続できるようにする必要があります。

1. Office 365 ユーザー アカウントにサインインします。
2. ロジック アプリが Office 365 アカウントに接続して使用することを許可します。 

接続を作成したら、ユーザー ID など、Office 365 ユーザー プロパティを入力します。これらのプロパティについては、このトピックの **REST API リファレンス**をご覧ください。

>[AZURE.TIP] 他のロジック アプリでも、前述の Office 365 ユーザー接続を使用できます。


## Office 365 Users REST API リファレンス
適用されるバージョン: 1.0。

### 自分のプロファイルを取得 
現在のユーザーのプロファイルを取得します。```GET: /users/me```

この呼び出しには、パラメーターはありません。

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|202|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|403|許可されていません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|


### ユーザー プロファイルの取得 
特定のユーザー プロファイルを取得します。```GET: /users/{userId}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|userId|string|○|path|なし|ユーザー プリンシパル名または電子メール ID|

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|202|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|403|許可されていません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|


### 上司の取得 
指定されたユーザーの上司のユーザー プロファイルを取得します。```GET: /users/{userId}/manager```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|userId|string|○|path|なし|ユーザー プリンシパル名または電子メール ID|

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|202|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|403|許可されていません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|



### 直接の部下を取得 
直接の部下を取得します。```GET: /users/{userId}/directReports```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|userId|string|○|path|なし|ユーザー プリンシパル名または電子メール ID|

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|202|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|403|許可されていません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|



### ユーザーの検索 
ユーザー プロファイルの検索結果を取得します。```GET: /users```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|searchTerm|string|×|query|なし|検索文字列 (以下に適用されます: 表示名、姓を含まない名前、姓、電子メール、メールのニックネーム、ユーザー プリンシパル名)|

#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|202|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|403|許可されていません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|



## オブジェクト定義

#### ユーザー: ユーザー モデル クラス

|プロパティ名 | データ型 |必須
|---|---|---|
|DisplayName|string|×|
|GivenName|string|×|
|Surname|string|×|
|Mail|string|×|
|MailNickname|string|×|
|TelephoneNumber|string|×|
|AccountEnabled|ブール値|×|
|ID|string|○
|UserPrincipalName|string|×|
|学科|string|×|
|JobTitle|string|×|
|MobilePhone|string|×|


## 次のステップ

[ロジック アプリを作成](../app-service-logic/app-service-logic-create-a-logic-app.md)します。

[API リスト](apis-list.md)に戻ります。

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/connectors-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/connectors-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/connectors-create-api-office365-users/contoso-aad-app-configure.PNG

<!---HONumber=AcomDC_0413_2016-->