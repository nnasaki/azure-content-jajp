<properties
   pageTitle="Visual Studio で SQL Data Warehouse に接続する | Microsoft Azure"
   description="SQL Data Warehouse に接続して、クエリを実行する"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/13/2016"
   ms.author="sonyama;barbkess"/>

# Visual Studio で SQL Data Warehouse に接続する

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)

このチュートリアルでは、Visual Studio の SQL Server Data Tools (SSDT) 拡張機能を使用して、Azure SQL Data Warehouse にわずか数分で接続する方法について説明します。接続後に、簡単なクエリを実行します。

## 前提条件

+ SQL Data Warehouse に含まれる AdventureWorksDW サンプル データ。このデータベースの作成方法については、[SQL Data Warehouse の作成][]に関するページを参照してください。
+ Visual Studio の SQL Server Data Tools。インストールの手順とオプションの詳細については、[Visual Studio と SSDT のインストール][]に関するページを参照してください。

## 手順 1. Azure SQL サーバーの完全修飾名を検索する

SQL Data Warehouse データベースは、Azure SQL Server に関連付けられます。データベースに接続する際に、サーバーの完全修飾名 (**servername**.database.windows.net*) が必要になります。

完全修飾サーバー名を検索するには

1. [Azure ポータル][]にアクセスします。
2. **[SQL データベース]** をクリックし、接続するデータベースをクリックします。この例では、AdventureWorksDW サンプル データベースを使用します。
3. サーバーの完全名を見つけます。

    ![Full server name][1]

## 手順 2. SQL Data Warehouse に接続する

1. Visual Studio 2013 または 2015 を開きます。
2. SQL Server オブジェクト エクスプローラーを開きます。これを行うには、**[表示]**、**[SQL Server オブジェクト エクスプローラー]** の順に選択します。

    ![SQL Server Object Explorer][2]

3. **[SQL Server の追加]** アイコンをクリックします。

    ![Add SQL Server][3]

4. [サーバーへの接続] ウィンドウのフィールドに入力します。

    ![サーバーへの接続][4]

    - **[サーバー名]**: 前の手順で特定した**サーバー名**を入力します。
    - **[認証]**: **[SQL Server 認証]** または **[Active Directory 統合認証]** を選択します。
    - **[ユーザー名]** と **[パスワード]**: 先ほど [SQL Server 認証] を選択した場合は、ユーザー名とパスワードを入力します。
    - **[接続]** をクリックします。

5. 確認のために、Azure SQL Server を展開します。サーバーに関連付けられているデータベースが表示されます。AdventureWorksDW を展開すると、サンプル データベース内のテーブルが表示されます。

    ![Explore AdventureWorksDW][5]

## 手順 3. サンプル クエリを実行する

これで、データベースへの接続が確立されました。次はクエリを記述してみましょう。

1. SQL Server オブジェクト エクスプローラーでデータベースを右クリックします。

2. **[新しいクエリ]** を選択します。新しいクエリ ウィンドウが開きます。

    ![新しいクエリ][6]

3. 次の TSQL クエリをクエリ ウィンドウにコピーします。

    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```

4. クエリを実行します。そのためには、緑色の矢印をクリックするか、`CTRL`+`SHIFT`+`E` のショートカット キーを使用します。

    ![Run query][7]

5. クエリ結果を確認します。この例では、FactInternetSales テーブルに 60,398 行が含まれています。

    ![Query results][8]

## 次のステップ

これで接続してクエリを実行することができます。[Power BI でデータを視覚化][]してみてください。

Windows 認証を使用するように環境を構成する方法については、「[Azure Active Directory 認証を使用して SQL Database または SQL Data Warehouse に接続する][]」を参照してください。

<!--Arcticles-->
[SQL Data Warehouse の作成]: sql-data-warehouse-get-started-provision.md
[Visual Studio と SSDT のインストール]: sql-data-warehouse-install-visual-studio.md
[Azure Active Directory 認証を使用して SQL Database または SQL Data Warehouse に接続する]: ../sql-database/sql-database-aad-authentication.md
[Power BI でデータを視覚化]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md

<!--Other-->
[Azure ポータル]: https://portal.azure.com

<!--Image references-->

[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png
[2]: ./media/sql-data-warehouse-get-started-connect/open-ssdt.png
[3]: ./media/sql-data-warehouse-get-started-connect/add-server.png
[4]: ./media/sql-data-warehouse-get-started-connect/connection-dialog.png
[5]: ./media/sql-data-warehouse-get-started-connect/explore-sample.png
[6]: ./media/sql-data-warehouse-get-started-connect/new-query2.png
[7]: ./media/sql-data-warehouse-get-started-connect/run-query.png
[8]: ./media/sql-data-warehouse-get-started-connect/query-results.png

<!---HONumber=AcomDC_0518_2016-->