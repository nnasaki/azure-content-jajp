<properties
pageTitle="Shared Access Signature を使用してデータへの HDInsight のアクセスを制限する"
description="Azure Storage の BLOB に格納されたデータへの HDInsight のアクセスを制限するために、Shared Access Signature を使用する方法について説明します。"
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="04/19/2016"
ms.author="larryfr"/>

#Azure Storage の Shared Access Signature を使用して HDInsight でデータへのアクセスを制限する

HDInsight はデータ ストレージとして Azure BLOB ストレージを使用します。HDInsight はクラスターの既定のストレージとして使用する BLOB に対してフル アクセスを持つ必要がありますが、クラスターで使用される他の BLOB に格納されたデータへのアクセス許可を制限することができます。たとえば、一部のデータを読み取り専用にすることができます。Shared Access Signature を使用して、これを実行することができます。

Shared Access Signature (SAS) は、データへのアクセスを制限できる Azure Storage アカウントの機能です。たとえば、データに読み取り専用のアクセス権を提供します。このドキュメントでは、SAS を使用して、HDInsight から BLOB コンテナーに対して、読み取り専用および一覧専用のアクセス権を有効にする方法を学習します。

##必要条件

* Azure サブスクリプション

* C# または Python。C# のサンプル コードは、Visual Studio のソリューションとして提供されます。

    * Visual Studio は、バージョン 2013 または 2015 である必要があります
    
    * Python は、バージョン 2.7 以上である必要があります

* Linux ベースの HDInsight クラスターまたは [Azure PowerShell][powershell] - 既存の Linux ベースのクラスターがある場合は、Ambari を使用して、クラスターに Shared Access Signature を追加することができます。ない場合は、Azure PowerShell を使用して新しいクラスターを作成し、クラスターの作成時に Shared Access Signature を追加することができます。

* サンプル ファイルは、[https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature) にあります。このリポジトリには、次が含まれます。

    * HDInsight で使用するストレージ コンテナー、保存済みのポリシーおよび SAS を作成できる Visual Studio プロジェクト
    
    * HDInsight で使用するストレージ コンテナー、保存済みのポリシーおよび SAS を作成できる Python スクリプト
    
    * 新しい HDInsight クラスターを作成して、SAS を使用するように構成できる PowerShell スクリプト

##Shared Access Signature

Shared Access Signature には、次の 2 つのフォームがあります。

* アドホック: 開始時刻、有効期限、および SAS へのアクセス許可がすべて、SAS URI で指定されます (または、開始時刻を省略した場合は、暗黙で指定されます)。

* 保存されているアクセス ポリシー: 保存されているアクセス ポリシーは、リソース コンテナー (BLOB コンテナー、テーブル、キュー、ファイル共有) で定義されており、これを使用して、1 つ以上の Shared Access Signature のコンテナーを管理できます。保存されているアクセス ポリシーに SAS を関連付けると、SAS は、保存されているアクセス ポリシーに定義されている制約 (開始時刻、有効期限、およびアクセス許可) を継承します。

1 つの重要なシナリオ、失効では、この 2 つの形式の相違点が重要です。SAS は URL であるため、取得したユーザーはだれでも、どのユーザーが最初に要求したかに関係なく、SAS を使用できます。SAS が一般ユーザーに発行された場合は、世界中のだれでも使用できます。配布された SAS は、次の 4 つの状況のいずれかになるまで有効です。

1. SAS に指定された有効期限に達した。

2. 保存されているアクセス ポリシーに指定された、SAS が参照する有効期限に達した (保存されているアクセス ポリシーが参照される場合、かつ有効期限が指定されている場合)。これは、期間が経過したため、または保存されているアクセス ポリシーの有効期限を過去の日時に変更したために発生します。このような有効期限の変更は、SAS を失効させる方法の 1 つです。

3. SAS の参照先である保存されているアクセス ポリシーが削除されている。これは、SAS を失効させる、もう 1 つの方法です。保存されているアクセス ポリシーを、完全に同じ名前で再作成すると、そのアクセス ポリシーに関連付けられたアクセス許可に従って、すべての既存の SAS トークンが再び有効になります (SAS の有効期限が過ぎていないことが前提です)。SAS を失効させるつもりで、将来の時間を有効期限に指定してアクセス ポリシーを再作成する場合は必ず、異なる名前を使用してください。

4. SAS の作成に使用したアカウント キーが再度生成された。これを行うと、そのアカウント キーを使用するすべてのアプリケーション コンポーネントが、別の有効なアカウント キー、または新しく再生成されたアカウント キーを使用するよう更新されるまで、認証に失敗します。

> [AZURE.IMPORTANT] Shared Access Signature URI は、署名の作成に使用されたアカウント キーと、保存済みのアクセス ポリシー (存在する場合) に関連付けられます。保存済みのアクセス ポリシーが指定されていない場合、Shared Access Signature を取り消すにはアカウント キーを変更する以外に方法はありません。

必要に応じて、署名を取り消したり、有効期限を延長したりできるように、常に保存済みのアクセス ポリシーを使用することをお勧めします。ドキュメント内のこれらの手順は、保存済みのアクセス ポリシーを使用して、SAS を生成します。

Shared Access Signature の詳細については、「[SAS モデルについて](../storage/storage-dotnet-shared-access-signature-part-1.md)」を参照してください。

##保存済みのポリシーを作成して SAS を生成する

現在は、プログラムを使用して保存済みのポリシーを作成する必要があります。保存済みのポリシーおよび SAS を作成する C# と Python のサンプルは両方とも、[https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature) にあります。

###C# を使用して保存済みのポリシーと SAS を作成する

1. Visual Studio でソリューションを開きます。

2. ソリューション エクスプローラーで __[SASToken]__ プロジェクトを右クリックし、__[プロパティ]__ を選択します。

3. __[設定]__ を選択し、次のエントリに値を追加します。

    * StorageConnectionString: 保存済みのポリシーと SAS を作成するストレージ アカウントの接続文字列。`myaccount` がストレージ アカウントの名前で、`mykey` がストレージ アカウントのキーである場合、形式は `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` になります。
    
    * ContainerName: アクセスを制限するストレージ アカウントのコンテナー。
    
    * SASPolicyName: 作成される保存済みのポリシーに使用する名前。
    
    * FileToUpload: コンテナーにアップロードされるファイルのパス。
    
4. プロジェクトを実行します。コンソール ウィンドウが表示され、SAS が生成されると、次のような情報が表示されます。

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
        
    SAS ポリシー トークンを保存します。HDInsight クラスターでストレージ アカウントに関連付けるときに、これが必要になります。また、ストレージ アカウント名とコンテナー名も必要になります。
    
###Python を使用して保存済みのポリシーと SAS を作成する

1. SASToken.py ファイルを開き、次の値を変更します。

    * policy\_name: 作成される保存済みのポリシーに使用する名前。
    
    * storage\_account\_name: ストレージ アカウントの名前。
    
    * storage\_account\_key: ストレージ アカウントのキー。
    
    * storage\_container\_name: アクセスを制限するストレージ アカウントのコンテナー。
    
    * example\_file\_path: コンテナーにアップロードされるファイルのパス。

2. スクリプトを実行します。スクリプトが完了すると、次のような SAS トークンが表示されます。

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
    
    SAS ポリシー トークンを保存します。HDInsight クラスターでストレージ アカウントに関連付けるときに、これが必要になります。また、ストレージ アカウント名とコンテナー名も必要になります。
    
##HDInsight での SAS の使用

HDInsight クラスターを作成するときは、プライマリ ストレージ アカウントを指定する必要があり、任意で追加のストレージ アカウントを指定することもできます。これらのストレージを追加する両方の方法で、使用するストレージ アカウントとコンテナーへのフル アクセスが必要です。

コンテナーへのアクセスを制限するために Shared Access Signature を使用するには、カスタム エントリをクラスターの __core-site__ 構成に追加する必要があります。

* __Windows ベース__または __Linux ベース__の HDInsight クラスターでは、PowerShell を使用してクラスターを作成するときにこれを実行できます。

* __Linux ベース__の HDInsight クラスターでは、Ambari を使用してクラスターを作成した後に構成を変更します。

###SAS を使用する新しいクラスターの作成

SAS を使用する HDInsight クラスターを作成する例は、リポジトリの `CreateCluster` ディレクトリにあります。この例を使用するには、次の手順を実行してください。

1. テキスト エディターで `CreateCluster\HDInsightSAS.ps1` ファイルを開き、このドキュメントの先頭にある次の値を変更します。

        # Replace 'mycluster' with the name of the cluster to be created
        $clusterName = 'mycluster'
        # Valid values are 'Linux' and 'Windows'
        $osType = 'Linux'
        # Replace 'myresourcegroup' with the name of the group to be created
        $resourceGroupName = 'myresourcegroup'
        # Replace with the Azure data center you want to the cluster to live in
        $location = 'North Europe'
        # Replace with the name of the default storage account to be created
        $defaultStorageAccountName = 'mystorageaccount'
        # Replace with the name of the SAS container created earlier
        $SASContainerName = 'sascontainer'
        # Replace with the name of the SAS storage account created earlier
        $SASStorageAccountName = 'sasaccount'
        # Replace with the SAS token generated earlier
        $SASToken = 'sastoken'
        # Set the number of worker nodes in the cluster
        $clusterSizeInNodes = 2
        
    たとえば、`'mycluster'` を作成するクラスターの名前に変更します。ストレージ アカウントと SAS トークンを作成するときに、SAS の値は、前の手順の値と一致する必要があります。
    
    値を変更したら、ファイルを保存します。

1. 新しい Azure PowerShell プロンプトを開きます。Azure PowerShell に慣れていない場合、またはインストールしていない場合は、「[Azure PowerShell のインストールと構成][powershell]」を参照してください。

2. プロンプトから次を使用して、Azure サブスクリプションを認証します。

        Login-AzureRmAccount
    
    プロンプトが表示されたら、Azure サブスクリプションのアカウントでログインします。
    
    ログインが複数の Azure サブスクリプションに関連付けられている場合は、`Select-AzureRmSubscription` を使用して、使用するサブスクリプションを選択します。

2. プロンプトから、ディレクトリを HDInsightSAS.ps1 ファイルを含む `CreateCluster` ディレクトリに移動します。次を使用して、スクリプトを実行します。
        
        .\HDInsightSAS.ps1
    
    このスクリプトを実行すると、リソース グループとストレージ アカウントを作成するときに、PowerShell プロンプトにログ出力されます。次に、HDInsight クラスターの HTTP ユーザーを入力するように求められます。これは、クラスターへの HTTP(S) アクセスのセキュリティを保護するために使用されるユーザー アカウントです。
    
    Linux ベースのクラスターを作成している場合、SSH ユーザー アカウント名とパスワードを求めるメッセージも表示されます。これは、クラスターへのリモート ログインに使用されます。
    
    > [AZURE.IMPORTANT] HTTP(S) または SSH のユーザー名とパスワードを求められると、次の条件を満たすパスワードを指定する必要があります。
    >
    > - 10 文字以上にする
    > - 数字を 1 つ以上含める
    > - 英数字以外を 1 文字以上含める
    > - 大文字または小文字を 1 文字以上含める


このスクリプトが完了するにはしばらく時間がかかります。通常、約 15 分です。エラーが発生することなく、このスクリプトが完了すると、クラスターが作成されています。

###既存のクラスターを更新して SAS を使用する

既存の Linux ベースのクラスターがある場合は、次の手順を使用して、SAS を __core-site__ 構成に追加することができます。

1. クラスターの Ambari Web UI を開きます。このページのアドレスは、https://YOURCLUSTERNAME.azurehdinsight.net です。入力を要求されたら、クラスターを作成するときに使用した管理者名 (admin,) とパスワードを使用して認証します。

2. Ambari Web UI の左側から、__[HDFS]__ を選択して、ページ中央にある __[Configs]__ タブを選択します。

3. __[詳細設定]__ タブをクリックし、__[カスタム core-site]__ セクションが表示されるまでスクロールします。

4. __[カスタム core-site]__ セクションを展開して、最後までスクロールし、__[プロパティの追加...]__ リンクを選択します。__[キー]__ と __[値]__ フィールドに、次の値を使用します。

    * __キー__: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
    
    * __値__: 以前に実行した C# または Python アプリケーションが返した SAS
    
    __CONTAINERNAME__ を C# または SAS のアプリケーションで使用したコンテナー名に置き換えます。__STORAGEACCOUNTNAME__ を使用したストレージ アカウント名に置き換えます。

5. __[追加]__ ボタンをクリックしてこのキーと値を保存し、__[保存]__ ボタンをクリックして構成の変更を保存します。プロンプトが表示されると、変更の説明 (「SAS ストレージ アクセスの追加」など) を追加し、__[保存]__ をクリックします。

    変更が完了したら、__[OK]__ をクリックします。

    > [AZURE.IMPORTANT] この操作をすると、構成の変更が保存されますが、変更を適用させるには、複数のサービスを再起動する必要があります。

6. Ambari Web UI で、左側の一覧から __[HDFS]__ を選択して、右側の __[サービス アクション]__ ドロップダウン リストから __[すべて再起動]__ を選択します。プロンプトが表示されたら、__[メンテナンス モードの有効化]__ を選択して、[すべて再起動を確認する] を選択します。

    ページの左側の一覧から、この処理を MapReduce2 と YARN のエントリに対して繰り返します。

7. これらが再起動されると、それぞれを選択して、__[サービス アクション]__ ドロップダウンからメンテナンス モードを無効化します。

##制限付きアクセスをテストする

制限付きアクセスがあることを確認するには、次のメソッドを使用します。

* __Windows ベース__の HDInsight クラスターでは、リモート デスクトップを使用してクラスターに接続します。詳細については、「[RDP を使用した HDInsight への接続](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)」を参照してください。

    接続されたら、デスクトップ上の __[Hadoop コマンド ライン]__ アイコンを使用して、コマンド プロンプトを開きます。

* __Linux ベース__の HDInsight クラスターでは、SSH を使用してクラスターに接続します。Linux ベースのクラスターで SSH を使用する方法については、以下のいずれかを参照してください。

    * [Linux、Unix、または OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)
    
クラスターに接続したら、次の手順を使用して、SAS ストレージ アカウント上の項目を読み取りまたは一覧表示のみすることができることを確認します。

1. プロンプトから次のように入力します。__SASCONTAINER__ を SAS ストレージ アカウントに対して作成されたコンテナーの名前に置き換えます。__SASACCOUNTNAME__ を SAS に使用するストレージ アカウントの名前に置き換えます。

        hdfs dfs -ls wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    
    これでコンテナーと SAS の作成時にアップロードされたファイルを含む、コンテナーの内容を一覧表示します。
    
2. 次を使用して、ファイルの内容を読み取ることができることを確認します。前の手順のとおりに、__SASCONTAINER__ と __SASACCOUNTNAME__ を置き換えます。__FILENAME__ を前のコマンドで表示されたファイルの名前に置き換えます。

        hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
        
    これで、ファイルの内容が一覧表示されます。
    
3. 次を使用して、ローカル ファイル システムにファイルをダウンロードします。

        hdfs dfs -get wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    
    ここでは、ファイルは __testfile.txt__ という名前のローカル ファイルにダウンロードされます。

4. 次を使用して、ローカル ファイルを SAS ストレージ上の __testupload.txt__ という名前の新しいファイルにアップロードします。

        hdfs dfs -put testfile.txt wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    
    次のようなメッセージが返されます。
    
        put: java.io.IOException
        
    このエラーは、ストレージの場所が読み取りと一覧表示のみであるために発生します。次を使用して、書き込み可能なクラスターの既定のストレージ上にデータを置きます。
    
        hdfs dfs -put testfile.txt wasb:///testupload.txt
        
    このとき、操作が正常に完了する必要があります。
    
##トラブルシューティング

###タスクはキャンセルされました

__現象__: PowerShell スクリプトを使用してクラスターを作成するときに、次のエラー メッセージが表示される場合があります。

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

__原因__: クラスターの管理者または HTTP ユーザー、または (Linux ベースのクラスターの場合) SSH ユーザーのパスワードを使用する場合に、このエラーが発生する可能性があります。

__解決方法__: 次の条件を満たすパスワードを使用します。

- 10 文字以上にする
- 数字を 1 つ以上含める
- 英数字以外を 1 文字以上含める
- 大文字または小文字を 1 文字以上含める

##次のステップ

HDInsight クラスターにアクセスが制限されたストレージを追加する方法を学習しました。クラスターのデータと連携するその他の方法を確認してください。

* [HDInsight での Hive の使用](hdinsight-use-hive.md)

* [HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)

* [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)

[powershell]: ../powershell-install-configure.md

<!---HONumber=AcomDC_0420_2016-->