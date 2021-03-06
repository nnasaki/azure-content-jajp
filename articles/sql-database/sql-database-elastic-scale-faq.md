<properties 
	pageTitle="Azure SQL Elastic Scale の FAQ | Microsoft Azure" 
	description="Azure SQL Database の Elastic Scale に関してよく寄せられる質問。" 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/03/2016" 
	ms.author="sidneyh"/>

# エラスティック データベース ツールに関する FAQ 

#### シャードごとにシングルテナントはあるが、シャーディング キーがない場合、スキーマ情報にどのようにしてシャーディング キーを取り込むのですか。

スキーマ情報オブジェクトは、分割や結合といったシナリオの場合にのみ使用します。アプリケーションがシングルテナントである場合は、Split Merge ツールは必要ないため、スキーマ情報オブジェクトを取り込む必要はありません。

#### データベースをプロビジョニングし、既にシャード マップ マネージャーもあります。このデータベースをシャードとして登録するにはどうしたらいいですか。

「**[エラスティック データベース クライアント ライブラリを使用してアプリケーションに共有を追加する](sql-database-elastic-scale-add-a-shard.md)**」を参照してください。

#### エラスティック データベースデータベース ツールにはどの程度のコストがかかりますか。

エラスティック データベース クライアント ライブラリの使用にコストは発生しません。コストが発生するのは、シャードとシャード マップ マネージャーに使用した Azure SQL データベースと、Split Merge ツールにプロビジョニングした Web/worker ロールに対してのみです。

#### 別のサーバーからシャードを追加した場合に、資格情報が機能しないのはどうしてですか。
資格情報は「User ID=username@servername」の形式ではなく、単純に「User ID = username」を使用します。また、「username」ログインがシャードで権限を持っていることを確認します。

#### アプリケーションを起動するたびに、シャード マップ マネージャーを作成してシャードを取り込む必要がありますか。

必要ありません。シャード マップ マネージャー (たとえば **[ShardMapManagerFactory.CreateSqlShardMapManager](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)**) の作成は 1 回だけの操作です。アプリケーションは、アプリケーションの起動時に **[ShardMapManagerFactory.TryGetSqlShardMapManager()](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)** 呼び出しを使用する必要があります。アプリケーション ドメインごとにそのような呼び出しを 1 回行います。

#### エラスティック データベース ツールの使い方について質問がある場合、どこに問い合わせればよいですか。 

[Azure SQL Database](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) フォーラムからお問い合わせください。

#### シャーディング キーを使用してデータベース接続を取得する場合でも、同じシャードの別のシャーディング キーでデータを照会できますか。設計によって異なりますか。

Elastic Scale API を使用すると、シャーディング キーの接続データベースに接続できますが、シャーディング キーのフィルター機能は利用できません。提供されるシャーディング キーの範囲を制限するには、必要に応じて、**WHERE** 句をクエリに追加します。

#### 自分のシャード セットのシャードごとに異なる Azure データベース エディションを使用できますか。

できます。シャードは個別のデータベースであるため、あるシャードに Premium エディションを使用し、別のシャードに Standard エディションを使用できます。さらに、シャードの有効期間中に、シャードのエディションを何度もスケール アップまたはスケール ダウンできます。

#### 分割や結合といった操作の実行時に、Split Merge ツールではデータベースがプロビジョニング (または削除) されますか。 

されません。**分割**操作の場合、適切なスキーマを持ったターゲット データベースが存在し、シャード マップ マネージャーに登録されている必要があります。**結合**操作の場合、シャード マップ マネージャーからシャードを削除してから、データベースを削除する必要があります。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=AcomDC_0504_2016-->