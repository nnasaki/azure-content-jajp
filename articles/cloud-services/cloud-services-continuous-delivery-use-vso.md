<properties
	pageTitle="Visual Studio Team Services を使用した Azure での継続的な配信 | Microsoft Azure"
	description="Visual Studio Team Services チーム プロジェクトを自動的にビルドして Azure App Service の Web アプリ機能またはクラウド サービスにデプロイするための構成方法について説明します。"
	services="cloud-services"
	documentationCenter=".net"
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="05/08/2016"
	ms.author="tarcher"/>

# Visual Studio Team Services を使用した Azure への継続的な配信

Visual Studio Team Services チーム プロジェクトを自動的にビルドして Azure Web アプリまたはクラウド サービスにデプロイするように構成できます。*オンプレミスの* Team Foundation Server を使用した継続的なビルドおよびデプロイ システムのセットアップ方法については、「[Continuous Delivery for Cloud Applications in Azure (Azure での Cloud Services の継続的な配信)](cloud-services-dotnet-continuous-delivery.md)」を参照してください。

このチュートリアルは、Visual Studio 2013 と Azure SDK がインストール済みであることを前提としています。Visual Studio 2013 がない場合は、**www.visualstudio.com** で[無料利用の開始](http://www.visualstudio.com)リンクをクリックしてダウンロードします。Azure SDK を[ここ](http://go.microsoft.com/fwlink/?LinkId=239540)からインストールしてください。

> [AZURE.NOTE] このチュートリアルを完了するには、Visual Studio Team Services アカウントが必要です。[Visual Studio Team Services アカウントは無料で開く](http://go.microsoft.com/fwlink/p/?LinkId=512979)ことができます。

Visual Studio Team Services を使用してクラウド サービスを自動的にビルドして Azure にデプロイするようにセットアップするには、次の手順に従います。

## 1: チーム プロジェクトを作成する

[この](http://go.microsoft.com/fwlink/?LinkId=512980)手順に従って、チーム プロジェクトを作成し、Visual Studio にリンクさせます。このチュートリアルでは、ソース管理ソリューションとして Team Foundation バージョン管理 (TFVC) を使用していることを前提としています。バージョン管理に Git を使用する場合は、「[このチュートリアルの Git バージョン](http://go.microsoft.com/fwlink/p/?LinkId=397358)」を参照してください。

## 2: プロジェクトをソース管理にチェックインする

1. Visual Studio で、デプロイするソリューションを開くか、新しいソリューションを作成します。
このチュートリアルの手順に従って、Web アプリまたはクラウド サービス (Azure アプリケーション) をデプロイできます。
新しいソリューションを作成する場合は、新しい Azure クラウド サービス プロジェクト、または新しい ASP.NET MVC プロジェクトを作成します。
プロジェクトが .NET Framework 4 または 4.5 をターゲットにしていることを確認し、クラウド サービス プロジェクトを作成している場合は、ASP.NET MVC の Web ロールおよび worker ロールのプロジェクトを追加して、Web ロールに対応するインターネット アプリケーションを選択します。確認メッセージが表示されたら、**[インターネット アプリケーション]** を選択します。
Web アプリを作成する場合は、ASP.NET Web アプリケーション プロジェクトのテンプレートを選択し、次に MVC を選択します。「[Azure アプリ サービスでの ASP.NET Web アプリの作成](../app-service-web/web-sites-dotnet-get-started.md)」を参照してください。

	> [AZURE.NOTE] Visual Studio Team Services では、現在、Visual Studio Web アプリケーションの CI デプロイメントのみをサポートしています。Web サイト プロジェクトはサポート対象外です。

1. ソリューションのコンテキスト メニューを開き、**[ソリューションをソース管理に追加]** を選択します。

	![][5]

1. 既定値を受け入れるか変更し、**[OK]** を選択します。処理が完了すると、ソース管理アイコンが**ソリューション エクスプローラー**に表示されます。

	![][6]

1. ソリューションのショートカット メニューを開き、**[チェックイン]** を選択します。

	![][7]

1. **チーム エクスプローラー**の **[保留中の変更]** にチェックインのコメントを入力し、**[チェックイン]** を選択します。

	![][8]

	チェックインをするときは、特定の変更を含むまたは除外するためのオプションに注意してください。必要な変更が除外されている場合は、**[すべて含む]** を選択します。

	![][9]

## 3: プロジェクトを Azure に接続する

1. ソース コードが含まれる VS Team Services チーム プロジェクトが完成したので、チーム プロジェクトを Azure に接続することができます。[Azure クラシック ポータル](http://go.microsoft.com/fwlink/?LinkID=213885)で、クラウド サービスまたは Web アプリを選択するか、新たに作成します。作成するには、左下にある **[+]** アイコンを選択し、**[クラウド サービス]** または **[Web アプリ]** を選択して、**[簡易作成]** を選択します。**[Visual Studio Team Services を使用した発行の設定]** を選択します。

	![][10]

1. ウィザードで、テキスト ボックスに Visual Studio Team Services アカウントの名前を入力し、**[今すぐ承認]** をクリックします。サインインを求められることがあります。

	![][11]

1. **[接続要求]** ポップアップ ダイアログで **[承諾]** を選択して、Azure が VS Team Services でチーム プロジェクトを構成することを許可します。

	![][12]

1. 承認が成功すると、VSTS チーム プロジェクトのリストが含まれるドロップダウンが表示されます。前の手順で作成したチーム プロジェクトの名前を選択し、ウィザードのチェックマーク ボタンを選択します。

	![][13]

1. プロジェクトがリンクされると、変更を VSTS チーム プロジェクトにチェックインするための手順が表示されます。次のチェックイン時に、VSTS はプロジェクトをビルドして Azure にデプロイします。これを試すには、**[Visual Studio 2012 からチェックインする]** をクリックし、**[Visual Studio 2012 を起動]** をクリックします (またはこれと同等の、ポータル画面下部にある **[Visual Studio]** をクリックします)。

	![][14]

## 4: リビルドをトリガーし、プロジェクトを再デプロイする

1. Visual Studio の**チーム エクスプローラー**で、**[ソース管理エクスプローラー]** を選択します。

	![][15]

1. ソリューション ファイルの保存場所に移動し、ファイルを開きます。

	![][16]

1. **ソリューション エクスプローラー**で、ファイルを開いて変更します。たとえば、MVC Web ロールの Views\\Shared フォルダーにある `_Layout.cshtml` ファイルを変更します。

	![][17]

1. サイトのロゴを編集し、**Ctrl + S** キーを押してファイルを保存します。

	![][18]

1. **チーム エクスプローラー**で、**[保留中の変更]** を選択します。

	![][19]

1. コメントを入力し、**[チェックイン]** を選択します。

	![][20]

1. **[ホーム]** を選択して、**チーム エクスプローラー**のホーム ページに戻ります。

	![][21]

1. **[ビルド]** を選択して、処理中のビルドを表示します。

	![][22]

	**チーム エクスプローラー**に、チェックインのためにビルドが開始されたことが示されます。

	![][23]

1. 処理中のビルドの名前をダブルクリックして、ビルドの進行に合わせて詳細ログを表示します。

	![][24]

1. ビルドの処理中、ウィザードを使用して TFS を Azure にリンクしたときに作成されたビルド定義を調べてください。ビルド定義のショートカット メニューを開き、**[ビルド定義の編集]** を選択します。

	![][25]

	**[トリガー]** タブを見ると、既定ではチェックインごとにビルドを行うようにビルド定義が設定されていることがわかります。

	![][26]

	**[プロセス]** タブを見ると、デプロイメント環境がクラウド サービスまたは Web アプリの名前に設定されていることがわかります。Web アプリを操作している場合は、ここで示されているものとは異なるプロパティが表示されます。

	![][27]

1. 既定値と異なる値を使用する場合は、プロパティに対して希望の値を指定します。Azure 発行のプロパティは **[デプロイメント]** セクションにあります。

	次の表は、**[デプロイメント]** セクションで使用可能なプロパティを示しています。

	|プロパティ|既定値|
	|---|---|
	|信頼されていない証明書を許可|false の場合、SSL 証明書はルート証明機関によって署名される必要があります。|
	|アップグレードの許可|新規作成の代わりに、既存のデプロイメントを更新するデプロイメントを許可します。IP アドレスを保持します。|
	|削除しない|true の場合、既存の関連のないデプロイメントを上書きしません (アップグレードは許可)。|
	|デプロイメント設定へのパス|リポジトリのルート フォルダーを基準とした Web アプリの .pubxml ファイルへのパス。クラウド サービスでは無視されます。|
	|Sharepoint デプロイメント環境|サービス名と同じ。|
	|Azure デプロイメント環境|Web アプリ名またはクラウド サービス名。|

1. 複数のサービス構成 (.cscfg ファイル) を使用している場合は、**[Build、Advanced、MSBuild 引数]** 設定で目的のサービス構成を指定できます。たとえば、ServiceConfiguration.Test.cscfg を使用するには、MSBuild 引数ライン オプション `/p:TargetProfile=Test` を設定します。

	![][38]

	このころまでには、ビルドが正常に完了しています。

	![][28]

1. ビルド名をダブルクリックすると、関連付けられた単体テスト プロジェクトのテスト結果を含む **[ビルドの概要]** が表示されます。

	![][29]

1. [Azure クラシック ポータル](http://go.microsoft.com/fwlink/?LinkID=213885)では、ステージング環境が選択されると、関連付けられたデプロイが **[デプロイ]** タブに表示されます。

	![][30]

1.	目的のサイトの URL に移動します。Web アプリの場合は、コマンド バーの **[参照]** ボタンをクリックします。クラウド サービスのステージング環境を示す **[ダッシュボード]** ページの **[概要]** セクションで URL を選択します。クラウド サービス向けの継続的な統合からのデプロイメントは、既定ではステージング環境に発行されます。**[代替クラウド サービス環境]** プロパティを **[運用]** に設定することで、これを変更できます。このスクリーンショットでは、クラウド サービスのダッシュボード ページでサイト URL が表示される場所を示しています。

	![][31]

	新しいブラウザー タブが開いて、実行中のサイトが表示されます。

	![][32]

	クラウド サービスの場合は、プロジェクトにその他の変更を加えると、さらにビルドが実行され、複数のデプロイメントが累積されます。最新のデプロイは [アクティブ] とマークされます。

	![][33]

## 5: 以前のビルドを再デプロイする

この手順の内容は、クラウド サービスのみに適用され、省略可能です。Azure クラシック ポータルで以前のデプロイメントを選択し、**[再デプロイ]** をクリックしてサイトを以前のチェックインに戻します。これによって、TFS で新しいビルドが開始され、デプロイメント履歴に新しいエントリが作成されます。

![][34]

## 6: 運用デプロイを変更する

この手順の内容は、クラウド サービスのみに適用され、Web アプリには適用されません。準備が整ったら、Azure クラシック ポータルで **[スワップ]** を選択してステージング環境を運用環境へ昇格できます。新たにデプロイされたステージング環境は運用に昇格され、以前の運用環境がある場合、運用環境はステージング環境になります。運用環境とステージング環境でアクティブなデプロイメントは異なることはありますが、最近のビルドのデプロイメント履歴は環境にかかわらず同じです。

![][35]

## 7: 単体テストを実行する

この手順の内容は、Web アプリのみに適用され、クラウド サービスには適用されません。デプロイメントにクオリティ ゲートを配置するには、単体テストを実行できます。失敗した場合は、デプロイメントを停止できます。

1.  Visual Studio で、単体テスト プロジェクトを追加します。

	![][39]

1.  テストするプロジェクトにプロジェクト参照を追加します。

	![][40]

1.  複数の単体テストを追加します。開始するには、常に合格するダミーのテストを試してみてください。

		```
		using System;
		using Microsoft.VisualStudio.TestTools.UnitTesting;

		namespace UnitTestProject1
		{
		    [TestClass]
		    public class UnitTest1
		    {
		        [TestMethod]
		        [ExpectedException(typeof(NotImplementedException))]
		        public void TestMethod1()
		        {
		            throw new NotImplementedException();
		        }
		    }
		}
		```

1.  ビルド定義を編集し、**[プロセス]** タブを選択して、**[テスト]**·ノードを展開します。

1.  **[テスト失敗時にビルドを失敗させる]** を True に設定します。これは、テストに合格しない限り、デプロイメントが行われないことを意味します。

	![][41]

1.  新しいビルドがキューに追加されます。

	![][42]

	![][43]

1. ビルドの進行中は、進行状況を確認します。

	![][44]

	![][45]

1. ビルドが完了したら、テスト結果を確認します。

	![][46]

	![][47]

1.  失敗するテストを作成してみます。最初のテストをコピーして新しいテストを追加し、その名前を変更して、NotImplementedException が予期される例外であるコード行をコメントアウトします。

		```
		[TestMethod]
		//[ExpectedException(typeof(NotImplementedException))]
		public void TestMethod2()
		{
		    throw new NotImplementedException();
		}
		```

1. 変更をチェックインし、新しいビルドをキューに追加します。

	![][48]

1. テスト結果を表示して失敗の詳細を確認します。

	![][49]

	![][50]

## 次のステップ
VSTS での単位テストの詳細については、[「ビルド プロセスでのテストの実行」](http://go.microsoft.com/fwlink/p/?LinkId=510474) を参照してください。Git を使用している場合は、「[Git でコードを共有する](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx)」および「[Azure App Service での GIT による継続的なデプロイ](../app-service-web/web-sites-publish-source-control.md)」を参照してください。Visual Studio Team Services の詳細については、[Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861) に関するページをご覧ください。

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso/tfs1.png
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png

[5]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
[6]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
[7]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
[8]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
[9]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
[10]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
[11]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
[12]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
[13]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
[19]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
[20]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
[21]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
[22]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
[23]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
[28]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso/AdvancedMSBuildSettings.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
[46]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
[49]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
[50]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG

<!---HONumber=AcomDC_0511_2016-->