 <properties
	pageTitle="IntelliJ IDEA 用 HDInsight プラグインを使用して Spark Scala アプリケーションを作成する | Microsoft Azure"
	description="スタンドアロン Scala アプリケーションを作成して HDInsight Spark クラスターで実行する方法を説明します。"
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/14/2016" 
	ms.author="nitinme"/>


# IntelliJ IDEA 用の HDInsight Tools プラグインを使用して HDInsight Spark Linux クラスター向けの Spark Scala アプリケーションを作成する (プレビュー)

この記事では、IntelliJ IDEA 用の HDInsight プラグインを使用して、Scala で記述された Spark アプリケーションを開発し、HDInsight Spark クラスターに送信するための詳細な手順を説明します。このプラグインは、次のような使い方があります。

* Scala Spark アプリケーションを開発して HDInsight Spark クラスターに送信する
* Azure HDInsight Spark クラスター リソースにアクセスする
* Scala Spark アプリケーションをローカルで開発して実行する

また、[こちら](https://mix.office.com/watch/1nqkqjt5xonza)のビデオに従って、開始することもできます。

>[AZURE.IMPORTANT] このツールを使用すると、Linux 上で HDInsight Spark クラスター専用のアプリケーションを作成し、送信することができます。


**前提条件**

* Azure サブスクリプション。[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* HDInsight Linux での Apache Spark クラスター。手順については、「[Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](hdinsight-apache-spark-jupyter-spark-sql.md)」を参照してください。
* Oracle Java Development kit。[ここ](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)からインストールできます。
* IntelliJ IDEA。この記事では、バージョン 15.0.1 を使用します。[ここ](https://www.jetbrains.com/idea/download/)からインストールできます。

## IntelliJ IDEA 用の HDInsight Tools プラグインのインストール

1. IntelliJ IDEA の [Welcome] \(ようこそ) 画面が表示されたら、**[Configure]** (構成) をクリックし、**[Plugins]** (プラグイン) をクリックします。

2. 次の画面の左下隅にある **[Browse Repositories]** (リポジトリの参照) をクリックします。**[Browse Repositories]** (リポジトリの参照) ダイアログ ボックスが開いたら、**HDInsight** を検索し、**[Microsoft Azure HDInsight Tools for IntelliJ (Preview)]** (Microsoft Azure HDInsight Tools for IntelliJ (プレビュー)) を選択して、**[Install]** (インストール) をクリックします。プラグインは、Scala プラグインに依存しています。そのため、Scala プラグインがインストールされていない場合は、Scala プラグインもインストールするように求められます。

	![HDInsight プラグインのインストール](./media/hdinsight-apache-spark-intellij-tool-plugin/install-hdinsight-plugin.png)

3. メッセージが表示されたら、**[Restart IntelliJ IDEA]** (IntelliJ IDEA の再起動) ボタンをクリックして、IDE を再起動します。


## HDInsight Spark クラスターでの Spark Scala アプリケーションの実行

1. IntelliJ IDEA を起動し、新しいプロジェクトを作成します。[New Project (新規プロジェクト)] ダイアログ ボックスで、次の選択を行い、**[Next (次へ)]** をクリックします。

	![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

	* 左側のウィンドウで、**[HDInsight]** をクリックします。
	* 右側のウィンドウで、**[Spark on HDInsight (Scala)]** (HDInsight の Spark (Scala)) を選択します。
	* **[次へ]** をクリックします。

2. 次のウィンドウで、プロジェクトの詳細を指定します。

	* プロジェクト名とプロジェクトの場所を指定します。
	* **Project SDK** の場合、Java のバージョンは必ず 7 より新しいものを指定します。
	* **Scala SDK** の場合、**[Create]** (作成) をクリックし、**[Download]** (ダウンロード) をクリックして、使用する Scala のバージョンを選択します。**バージョン 2.11.x は使用しないでください**。このサンプルでは、バージョン **2.10.6** を使用します。

		![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)

	* **Spark SDK** の場合は、[ここ](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409)から SDK をダウンロードして使用してください。これを無視し、代わりに [Spark Maven リポジトリ](http://mvnrepository.com/search?q=spark)を使用できますが、Spark アプリケーションの開発に適した maven リポジトリがインストールされていることを確認してください (たとえば、Spark ストリーミングを使用する場合は、Spark ストリーミング部分がインストールされていることを確認する必要があります。また、Scala 2.10 とマークされているリポジトリを使用していることを確認してください。Scala 2.11 とマークされているリポジトリは使用しないでください)。

		![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-project-details.png)

	* **[完了]** をクリックします。

3. Spark プロジェクトでは、アーティファクトが自動的に作成されます。また、次の手順に従って、独自のアーティファクトを作成することもできます。

	1. **[File (ファイル)]** メニューの **[Project Structure (プロジェクトの構造)]** をクリックします。
	2. **[Project Structure (プロジェクトの構造)]** ダイアログ ボックスで、**[Artifacts (アーティファクト)]** をクリックし、プラス記号をクリックします。ポップアップ ダイアログ ボックスで、**[JAR]** をクリックし、**[Empty]** (空) をクリックします。

		![JAR の作成](./media/hdinsight-apache-spark-intellij-tool-plugin/create-jar-1.png)

	3. JAR ファイルの名前を入力します (例: **MyClusterApp**)。[Available Elements] \(使用可能な要素) ウィンドウで、**['MyClusterApp' compile output]** ('MyClusterApp' コンパイル出力) を右クリックし、**[Put into Output Root]** (出力ルートに入れる) をクリックします。

		![JAR の作成](./media/hdinsight-apache-spark-intellij-tool-plugin/create-jar-2.png)

	4. **[Apply]** (適用) をクリックし、**[OK]** をクリックします。

4. アプリケーション ソース コードを追加します。

	1. **Project Explorer** で、**[src]** を右クリックし、**[New]** (新規) をポイントして、**[Scala class]** (Scala クラス) をクリックします。

		![ソース コードの追加](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

	2. **[Create New Scala Class]** (新規 Scala クラスの作成) ダイアログ ボックスで、名前を指定し、**[Kind]** (種類) として **[Object]** (オブジェクト) を選択して、**[OK]** をクリックします。

		![ソース コードの追加](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

	3. **MyClusterApp.scala** ファイルで、次のコードを貼り付けます。このコードでは、HVAC.csv (すべての HDInsight Spark クラスターで使用可能) からデータを読み取り、CSV の 7 番目の列に 1 桁の数字のみが含まれる行を取得し、出力をクラスター用の既定のストレージ コンテナーの下にある **/HVACOut** に書き込みます。


			import org.apache.spark.SparkConf
			import org.apache.spark.SparkContext

			object MyClusterApp{
			  def main (arg: Array[String]): Unit = {
			    val conf = new SparkConf().setAppName("MyClusterApp")
			    val sc = new SparkContext(conf)

			    val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

			    //find the rows which have only one digit in the 7th column in the CSV
				val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

			    rdd1.saveAsTextFile("wasb:///HVACOut")
			  }

			}

5. HDInsight Spark クラスターでアプリケーションを実行します。

	1. **Project Explorer** で、プロジェクト名を右クリックし、**[Submit Spark Application to HDInsight]** (HDInsight への Spark アプリケーションの送信) を選択します。

		![Spark アプリケーションの送信](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

	2. Azure サブスクリプションの資格情報を入力するよう求められます。**[Spark Submission]** (Spark 送信) ダイアログ ボックスで、次の値を入力します。

		![Spark アプリケーションの送信](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

		* **[Spark clusters (Linux only)]** (Spark クラスター (Linux のみ)) では、アプリケーションを実行する HDInsight Spark クラスターを選択します。

		* IntelliJ プロジェクトからアーティファクトを選択するか、ハード ディスクからアーティファクトを選択する必要があります。

		* **[Main class name]** (メイン クラス名) テキスト ボックスで、省略記号 (![省略記号](./media/hdinsight-apache-spark-intellij-tool-plugin/ellipsis.png)) をクリックし、アプリケーションのソース コード内のメイン クラスを選択して、**[OK]** をクリックします。

			![Spark アプリケーションの送信](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)

		* この例のアプリケーション コードでは、コマンドライン引数を必要とせず、JAR またはファイルを参照することもないので、残りのテキスト ボックスは空のままでかまいません。

		* **[Submit]** をクリックします。

	3. ウィンドウの下部にある **[Spark Submission]** (Spark 送信) タブで、進行状況の表示が開始されます。\[Spark Submission] (Spark 送信) ウィンドウにある赤いボタンをクリックして、アプリケーションを停止することもできます。

        ![Spark Application Result](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

    次のセクションでは、IntelliJ IDEA 用の HDInsight プラグインを使用してジョブ出力にアクセスする方法を説明します。


## IntelliJ 用の HDInsight プラグインを使用して HDInsight Spark クラスターにアクセスし、その管理を行います。

HDInsight プラグインを使用してさまざまな操作を実行できます。

### クラスターのストレージ コンテナーにアクセスする

1. **[View]** (ビュー) メニューの **[Tool Windows]** (ツール ウィンドウ) をポイントし、**[HDInsight Explorer]** をクリックします。メッセージが表示されたら、Azure サブスクリプションにアクセスするための資格情報を入力します。

2. **[HDInsight]** ルート ノードを展開して、使用できる HDInsight Spark クラスターの一覧を表示します。

3. クラスター名を展開して、ストレージ アカウントと、クラスターの既定のストレージ コンテナーを表示します。

	![クラスター ストレージへのアクセス](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-access-storage.png)

4. クラスターに関連付けられているストレージ コンテナー名をクリックします。右側のウィンドウに、**HVACOut** という名前のフォルダーが表示されます。フォルダーをダブルクリックして開くと、**part-*** ファイルが表示されます。それらのファイルのいずれかを開いて、アプリケーションの出力を確認します。

### Spark History Server へのアクセス

1. **HDInsight Explorer** で、Spark クラスター名を右クリックし、**[Open Spark History UI]** (Spark 履歴 UI を開く) を選択します。入力を求められたら、クラスターの管理者資格情報を入力します。これらは、クラスターのプロビジョニング時に指定済みである必要があります。

2. Spark History Server ダッシュ ボードでは、実行が終了したばかりのアプリケーションを、アプリケーション名によって探すことができます。上記のコードでは、`val conf = new SparkConf().setAppName("MyClusterApp")` を使用してアプリケーション名を設定しています。したがって、Spark アプリケーション名は **MyClusterApp** です。

### Ambari ポータルの起動

**HDInsight Explorer** で、Spark クラスター名を右クリックし、**[Open Cluster Management Portal (Ambari)]** (クラスター管理ポータルを開く (Ambari)) を選択します。入力を求められたら、クラスターの管理者資格情報を入力します。これらは、クラスターのプロビジョニング時に指定済みである必要があります。

### Azure サブスクリプションの管理

既定では、HDInsight プラグインは、すべての Azure サブスクリプションからの Spark クラスターを一覧表示します。必要に応じて、クラスターにアクセスするサブスクリプションを指定できます。**HDInsight Explorer** で、**[HDInsight]** ルート ノードを右クリックし、**[Manage Subscriptions]** (サブスクリプションの管理) をクリックします。ダイアログ ボックスで、アクセスしないサブスクリプションに対するチェック ボックスをオフにし、**[Close]** (閉じる) をクリックします。Azure サブスクリプションからログオフする場合は、**[Sign Out]** (サインアウト) をクリックするという方法もあります。


## Spark Scala アプリケーションのローカルでの実行

IntelliJ IDEA 用の HDInsight Tools プラグインを使用すると、ワークステーション上で Spark Scala アプリケーションをローカルに実行することができます。通常、そのようなアプリケーションは、ストレージ コンテナーなどのクラスター リソースにアクセスする必要がなく、ローカルで実行しテストすることができます。

### 前提条件

Windows コンピューターでローカル Spark Scala アプリケーションを実行しているときに、[SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) に説明されている、Windows 上に WinUtils.exe が不足しているために発生する例外が発生する場合があります。このエラーを回避するには、[実行可能ファイルをここからダウンロード](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)して、**C:\\WinUtils\\bin** などの場所に保存する必要があります。次に、環境変数 **HADOOP\_HOME** を追加し、この変数の値を **C\\WinUtils** に設定します。

### ローカル Spark Scala アプリケーションの実行	 

1. IntelliJ IDEA を起動し、新しいプロジェクトを作成します。[New Project (新規プロジェクト)] ダイアログ ボックスで、次の選択を行い、**[Next (次へ)]** をクリックします。

	![Spark Scala アプリケーションの作成](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run.png)

	* 左側のウィンドウで、**[HDInsight]** をクリックします。
	* 右側のウィンドウで、**[Spark on HDInsight Local Run Sample (Scala)]** (HDInsight の Spark のローカル実行サンプル (Scala)) を選択します。
	* **[次へ]** をクリックします。

2. 「HDInsight Spark クラスターでの Spark Scala アプリケーションの実行」セクションの手順に従って、必要な設定を構成します。

3. ワークステーションでアプリケーションをローカルに実行します。Scala アプリケーションを右クリックし、**[Run 'LogQuery']** ('LogQuery' の実行) をクリックします。下部の **[Run]** (実行) タブに次のような出力が表示されます。

	![Spark Application local run result](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run-result.png)

## HDInsight ツール プラグインを使用できるように、既存の IntelliJ IDEA アプリケーションを変換します。

HDInsight ツール プラグインに対応するように、IntelliJ IDEA で作成した既存の Spark Scala アプリケーションを変換することもできます。これにより、ツールを使用して HDInsight Spark クラスターにアプリケーションを送信できます。そのためには、以下の手順を実行します。

1. IntelliJ IDEA で作成した既存の Spark Scala アプリケーションについては、関連付けられている .iml ファイルを開きます。
2. ルート レベルに、次のような **module** 要素が表示されます。

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

3. **module** 要素が次のようになるように、この要素を編集して `UniqueKey="HDInsightTool"` を追加します。

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

4. 変更を保存します。これで、アプリケーションは HDInsight ツール プラグインと互換性を持つようになります。これをテストするには、Project Explorer でプロジェクト名を右クリックします。ポップアップ メニューで、**[Submit Spark Application to HDInsight]** (HDInsight への Spark アプリケーションの送信) のオプションが表示されるようになったはずです。

## フィードバックと既知の問題

現在、Spark の出力を直接表示することはサポートされておらず、その実現に取り組んでいます。

ご提案やフィードバックがある場合、またはこのツールを使用していて問題が発生した場合は、hdivstool@microsoft.com に電子メールをお送りください。

## <a name="seealso"></a>関連項目


* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)

### シナリオ

* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)

* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)

* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### アプリケーションの作成と実行

* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)

### ツールと拡張機能

* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### リソースの管理

* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0420_2016-->