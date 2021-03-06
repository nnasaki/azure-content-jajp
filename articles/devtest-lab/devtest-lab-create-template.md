<properties
	pageTitle="VHD ファイルから DevTest ラボ カスタム イメージを作成する | Microsoft Azure"
	description="DevTest ラボで VM を作成するために使用できるカスタム イメージを VHD ファイルから作成する方法を説明します。"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/08/2016"
	ms.author="tarcher"/>

# VHD ファイルから DevTest ラボ カスタム イメージを作成する

## 概要

[ラボを作成](devtest-lab-create-lab.md)したら、[そのラボに仮想マシン (VM) を追加](devtest-lab-add-vm-with-artifacts.md)することができます。VM を作成する場合は、*ベース*として*カスタム イメージ*または*Marketplace イメージ*を指定します。この記事では、VHD ファイルからカスタム イメージを作成する方法を説明します。なお、この記事のすべての手順を実行するには、有効な VHD ファイルへのアクセス件が必要です。

## カスタム イメージの作成

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。

1. **[参照]** をタップし、一覧の **[DevTest ラボ]** をタップします。

1. ラボの一覧で目的のラボをタップします。

1. 選択したラボの **[設定]** ブレードが表示されます。

1. ラボの **[設定]** ブレードで、**[カスタム イメージ]** をタップします。

    ![カスタム イメージ オプション](./media/devtest-lab-create-template/lab-settings-custom-images.png)

1. **[カスタム イメージ]** ブレードで、**[+ カスタム イメージ]** をタップします。

    ![カスタム イメージの追加](./media/devtest-lab-create-template/add-custom-image.png)

1. カスタム イメージの名前を入力します。この名前は、新しい VM を作成するときにベース イメージの一覧に表示されます。

1. カスタム イメージの説明を入力します。この説明は、新しい VM を作成するときにベース イメージの一覧に表示されます。

1. **[VHD ファイル]** をタップします。

1. 一覧表示されていない VHD ファイルにアクセスする場合は、「[VHD ファイルのアップロード](#upload-a-vhd-file)」セクションの手順に従って VHD ファイルを追加し、完了したら、ここに戻ります。

1. 目的の VHD ファイルを選択します。

1. **[OK]** をタップして **[VHD ファイル]** ブレードを閉じます。

1. **[OS 構成]** をタップします。

1. **[OS 構成]** タブで、**[Windows]** または **[Linux]** を選択します。

1. **[Windows]** を選択した場合は、チェック ボックスを使用して、コンピューターで *Sysprep* が実行されたかどうかを指定します。

1. **[OK]** をタップして、**[OS 構成]** ブレードを閉じます。

1. **[OK]** をタップして、カスタム イメージを作成します。

1. 「[次のステップ](#next-steps)」セクションを参照してください。

##VHD ファイルのアップロード

新しいカスタム イメージを追加するには、VHD ファイルにアクセスする必要があります。

1. **[VHD ファイル]** ブレードで、**[PowerShell を使用して VHD ファイルをアップロード]** をタップします。

    ![イメージのアップロード](./media/devtest-lab-create-template/upload-image-using-psh.png)

1. 次のブレードには、VHD ファイルを Azure サブスクリプションにアップロードする PowerShell スクリプトの変更と実行の手順が表示されます。**注:** VHD ファイルのサイズと接続速度によっては、このプロセスに時間がかかる可能性があります。

##次のステップ

VM を作成するときに使用するカスタム イメージを追加したら、次は [VM をラボに追加](./devtest-lab-add-vm-with-artifacts.md)します。

<!---HONumber=AcomDC_0518_2016-->