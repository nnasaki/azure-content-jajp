<properties
    pageTitle="スキーマが異なるクラウド データベース間のクエリ | Microsoft Azure"
    description="列方向のパーティションでデータベース間クエリを設定する方法"    
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/28/2016"
    ms.author="torsteng;sidneyh" />

# スキーマが異なるクラウド データベース間のクエリ (プレビュー)

![異なるデータベースのテーブルにまたがるクエリ][1]

列方向にパーティション分割されたデータベースでは、データベースごとに異なるテーブル セットを使用します。これは、異なるデータベースではスキーマが異なることを意味します。たとえば、あるデータベースに在庫に関するすべてのテーブルが含まれていて、別のデータベースには会計に関連するすべてのテーブルが含まれているケースが該当します。

## 前提条件

* ユーザーは、ALTER ANY EXTERNAL DATA SOURCE アクセス許可を所有している必要があります。このアクセス許可は、ALTER DATABASE アクセス許可に含まれています。
* ALTER ANY EXTERNAL DATA SOURCE アクセス許可は、基になるデータ ソースを参照するために必要です。

## 概要

**注**: 行方向のパーティション分割とは異なり、これらの DDL ステートメントは、エラスティック データベース クライアント ライブラリを介したシャード マップを使ったデータ層の定義に依存しません。

1. [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CREATE EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CREATE EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx) 


## データベース スコープのマスター キーと資格情報の作成 

この資格情報は、リモート データベースに接続するために、エラスティック クエリによって使用されます。

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]
 
**注**: *<username>* にサフィックス *"@servername"* が含まれていないことを確認してください。

## 外部データ ソースの作成

構文:

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

**重要**: TYPE パラメーターを **RDBMS** に設定する必要があります。

### 例 

次の例では、外部データ ソースに対して CREATE ステートメントを使用する方法について説明します。

	CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
	WITH 
	( 
		TYPE=RDBMS, 
		LOCATION='myserver.database.windows.net', 
		DATABASE_NAME='ReferenceData', 
		CREDENTIAL= SqlUser 
	); 
 
現在の外部データ ソースの一覧を取得するには:

    select * from sys.external_data_sources; 

### 外部テーブル 

構文:

	CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])     
	{ WITH ( <rdbms_external_table_options> ) } 
	)[;] 
	
	<rdbms_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>, 
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 

### 例  

	CREATE EXTERNAL TABLE [dbo].[customer]( 
		[c_id] int NOT NULL, 
		[c_firstname] nvarchar(256) NULL, 
		[c_lastname] nvarchar(256) NOT NULL, 
		[street] nvarchar(256) NOT NULL, 
		[city] nvarchar(256) NOT NULL, 
		[state] nvarchar(20) NULL, 
		[country] nvarchar(50) NOT NULL, 
	) 
	WITH 
	( 
	       DATA_SOURCE = RemoteReferenceData 
	); 

次の例では、外部テーブルの一覧を現在のデータベースから取得する方法を示します。

	select * from sys.external_tables; 

### 解説

エラスティック クエリでは、既存の外部テーブル構文を拡張して、RDBMS 型の外部データ ソースを使用する外部テーブルを定義します。列方向のパーティション分割のための外部テーブルの定義は、次の側面に対応しています。

* **スキーマ**: 外部テーブル DDL は、クエリで使用できるスキーマを定義します。外部テーブル定義に指定するスキーマは、実際のデータが格納されているリモート データベース内のテーブルのスキーマに一致する必要があります。 

* **リモート データベース参照**: 外部テーブル DDL は、外部データ ソースを参照します。外部データ ソースは、実際のテーブル データが格納されているリモート データベース論理サーバー名とデータベース名を指定します。

前のセクションで説明したように外部データ ソースを使用する、外部テーブルを作成するための構文を次に示します。

DATA\_SOURCE 句は、外部テーブルに使用される外部データ ソース (つまり、列方向のパーティション分割の場合はリモート データベース) を定義します。

SCHEMA\_NAME 句と OBJECT\_NAME 句は、外部テーブル定義をリモート データベース上の別のスキーマのテーブルまたは別の名前を持つテーブルにマップする機能をそれぞれ提供します。これは、リモート データベースのカタログ ビューまたは DMV に対して外部テーブルを定義する場合や、リモート テーブル名が既にローカルに取得されている場合に便利です。

次の DDL ステートメントは、ローカル カタログから既存の外部テーブル定義を削除します。リモート データベースには影響しません。

	DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**CREATE/DROP EXTERNAL TABLE** に対するアクセス許可: 外部テーブル DDL に ALTER ANY EXTERNAL DATA SOURCE アクセス許可が必要です。これは、基になるデータ ソースを参照する場合にも必要です。

## セキュリティに関する考慮事項
外部テーブルへのアクセス権を持つユーザーは、外部データ ソース定義に指定された資格情報の下で、基になるリモート テーブルへのアクセス権を自動的に取得します。外部データ ソースの資格情報を介した特権の不要な昇格を回避するために、外部テーブルへのアクセスを慎重に管理する必要があります。外部テーブルへのアクセスは、通常の SQL 権限を使用して、通常のテーブルの場合と同様に許可または禁止することができます。


## 例: 列方向にパーティション分割されたデータベースのクエリ 

次のクエリは、注文と注文明細行用の 2 つのローカル テーブルと、顧客用のリモート テーブルの間で 3 方向の結合を実行します。これは、エラスティック クエリの参照データのユース ケースの例を示します。

	SELECT  	
	 c_id as customer,
	 c_lastname as customer_name,
	 count(*) as cnt_orderline, 
	 max(ol_quantity) as max_quantity,
	 avg(ol_amount) as avg_amount,
	 min(ol_delivery_d) as min_deliv_date
	FROM customer 
	JOIN orders 
	ON c_id = o_c_id
	JOIN  order_line 
	ON o_id = ol_o_id and o_c_id = ol_c_id
	WHERE c_id = 100


## T-SQL リモート実行のストアド プロシージャ: sp\_execute\_remote

エラスティック クエリには、シャードへの直接アクセスを提供するストアド プロシージャも導入されています。このストアド プロシージャは [sp\_execute\_remote](https://msdn.microsoft.com/library/mt703714) と呼ばれ、リモート データベースでリモート ストアド プロシージャまたは T-SQL コードを実行するときに使用できます。使用できるパラメーターは次のとおりです。

* データ ソース名 (nvarchar): RDBMS 型の外部データ ソースの名前。 
* クエリ (nvarchar): 各シャードで実行する T-SQL クエリ。 
* パラメーター宣言 (nvarchar) (省略可能): (sp\_executesql などの) クエリ パラメーターで使用される、パラメーターのデータ型定義を含む文字列。 
* パラメーター値のリスト (省略可能): (sp\_executesql などの) パラメーター値のコンマ区切りリスト。

sp\_execute\_remote では、起動パラメーターで指定された外部データ ソースを使用して、指定された T-SQL ステートメントをリモート データベースで実行します。shardmap マネージャー データベースとリモート データベースへの接続には、外部データ ソースの資格情報を使用します。

例:

	EXEC sp_execute_remote
		N'MyExtSrc',
		N'select count(w_id) as foo from warehouse' 


  
## ツールの接続性

通常の SQL Server 接続文字列を使用して、BI およびデータ統合ツールを、エラスティック クエリが有効でかつ外部テーブルが定義されている SQL DB サーバー上のデータベースに接続できます。ご使用のツールのデータ ソースとして SQL Server がサポートされていることを確認してください。次に、ツールを使用して接続する他の SQL Server データベースと同様に、エラスティック クエリ データベースと外部テーブルを参照します。

## ベスト プラクティス 
 
* SQL DB のファイアウォール構成で Azure Services のアクセスを有効にすることで、エラスティック クエリ エンドポイント データベースにリモート データベースへのアクセスが許可されていることを確認します。さらに、外部データ ソース定義に指定された資格情報を使ってリモート データベースに正常にログインでき、リモート テーブルへのアクセス許可を取得できることを確認します。  

* エラスティック クエリは、計算の大部分をリモート データベース上で実行できるクエリに最適です。通常、最適なクエリ パフォーマンスが得られるのは、リモート データベース上で評価可能な選択的なフィルター述語を使用した場合、またはリモート データベース上で完全に実行できる結合を使用した場合となります。その他のクエリ パターンでは、リモート データベースから大量のデータを読み込むことが必要になる場合があり、パフォーマンスが低下する可能性があります。


## 次のステップ

行方向にパーティション分割されたデータベース (シャード化されたデータベースとも呼ばれます) を照会する場合は、[シャード化された (行方向にパーティション分割された) クラウド データベース間のクエリ](sql-database-elastic-query-horizontal-partitioning.md)に関する記事をご覧ください。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->

<!---HONumber=AcomDC_0504_2016-->