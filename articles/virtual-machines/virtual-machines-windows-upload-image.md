<properties
	pageTitle="リソース マネージャー向けに Windows VM をアップロードする | Microsoft Azure"
	description="Resource Manager デプロイメント モデルで使用する Windows の仮想マシン イメージをアップロードする方法を説明します。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/06/2016"
	ms.author="dkshir"/>

# Resource Manager デプロイメント向けに Windows VM イメージを Azure にアップロードする


この記事では、Windows オペレーティング システムの仮想ハード ディスク (VHD) をアップロードして、Azure Resource Manager デプロイメント モデルを使用して新しい Windows Virtual Machines (VM) を作成する方法について説明します。Azure でのディスクと VHD の詳細については、「[仮想マシン用のディスクと VHD について](virtual-machines-linux-about-disks-vhds.md)」を参照してください。



## 前提条件

この記事では、以下のことを前提としています。

- **Azure サブスクリプション** - まだお持ちでない場合は、[無料で Azure アカウントを開いて](/pricing/free-trial/?WT.mc_id=A261C142F)、[MSDN サブスクライバ―の特典を有効にします](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。

- **Azure PowerShell バージョン 1.0 以降** - インストールされていない場合は、「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」を参照してください。

- **Windows を実行する仮想マシン** - オンプレミスの仮想マシンを作成するための多くのツールがあります。たとえば、「[Hyper-V の役割のインストールと仮想マシンの構成](http://technet.microsoft.com/library/hh846766.aspx)」を参照してください。Azure でサポートされている Windows オペレーティング システムの詳細については、「[Microsoft Azure Virtual Machines のマイクロソフト サーバー ソフトウェアのサポート](https://support.microsoft.com/kb/2721672)」を参照してください。


## VM が適切なファイル形式になっていることを確認します。

Azure では、VHD ファイル形式で保存された[第 1 世代の仮想マシン](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx)のイメージのみが受け入れられます。VHD のサイズは、固定およびメガバイトの整数にする必要があります (例: 8 で割り切ることのできる数)。VHD のサイズの上限は、1,023 GB です。

- VHDX 形式の Windows VM イメージがある場合は、次のいずれかを使用して VHD に変換します。

	- Hyper-V: Hyper-V を開いて、左側でローカル コンピューターを選択します。次に、その上にあるメニューで、**[アクション]**、**[ディスクの編集...]** の順にクリックします。**[次へ]** をクリックし、次のオプションを順番に入力または選択していきます (*VHDX ファイルのパス* > **[変換]** > **[VHD]** > **[固定サイズ]** > *新しい VHD ファイルのパス*)。**[完了]** をクリックして閉じます。

	- [Convert-VHD PowerShell コマンドレット](http://technet.microsoft.com/library/hh848454.aspx): 詳細については、ブログの投稿「[Hyper-V .vhdx を .vhd ファイル形式に変換する](https://blogs.technet.microsoft.com/cbernier/2013/08/29/converting-hyper-v-vhdx-to-vhd-file-formats-for-use-in-windows-azure/)」を参照してください。

- [VMDK ファイル形式](https://en.wikipedia.org/wiki/VMDK)の Windows VM イメージがある場合は、[Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497) を使用して VHD に変換できます。詳細については、ブログ記事「[VMware VMDK から Hyper-V VHD への変換方法](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx)」を参照してください。


## VHD のアップロードを準備する

このセクションでは、Windows 仮想マシンを一般化する方法を説明します。特に重要なのは、すべての個人アカウント情報を削除することです。通常、この VM イメージを使用して似た仮想マシンを迅速にデプロイする場合は、これを行う必要があります。Sysprep の詳細については、「[Sysprep の使用方法: 紹介](http://technet.microsoft.com/library/bb457073.aspx)」を参照してください。

1. Windows 仮想マシンへのサインイン

2. 管理者としてコマンド プロンプト ウィンドウを開きます。ディレクトリを **%windir%\\system32\\sysprep** に変更し、`sysprep.exe` を実行します。

3. **[システム準備ツール]** ダイアログ ボックスで、次の操作を行います。

	1. **[システム クリーンアップ アクション]** で **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、**[一般化する]** チェック ボックスがオンになっていることを確認します。

	2. **[シャットダウン オプション]** の **[シャットダウン]** を選択します。

	3. **[OK]** をクリックします。

	![Sysprep の開始](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)

</br> <a id="createstorage"></a>
## Azure Storage アカウントの作成または検索

VM イメージをアップロードするには、Azure にストレージ アカウントが必要です。既存のストレージ アカウントを選択することも、新しいストレージ アカウントを作成することもできます。これには、Azure ポータルまたは PowerShell のいずれかを使用します。

### Azure ポータルを使用して、Azure ストレージ アカウントを作成するには

1. [ポータル](https://portal.azure.com)にサインインします。

2. **[参照]**、**[ストレージ アカウント]** の順にクリックします。

3. このイメージのアップロードに使用するストレージ アカウントがあるかどうかを確認します。ストレージ アカウントの名前をメモしておきます。既存のストレージ アカウントを使用する場合は、「[VM イメージのアップロード](#uploadvm)」セクションに進みます。

4. 新しいストレージ アカウントを作成する場合は、**[追加]** をクリックして次の情報を入力します。

	1. ストレージ アカウントの**名前**を入力します。3 ～ 24 文字で小文字と数字のみを含めることができます。この名前は、ストレージ アカウントの BLOB、ファイル、その他のリソースにアクセスするときに使用する URL の一部になります。
	
	2. **デプロイメント モデル**として *[Resource Manager]* を選択します。

	3. 適切な **[アカウントの種類]**、**[パフォーマンス]**、および **[レプリケーション]** の値を選択します。情報アイコンをポイントすると、これらの値の詳細を確認することができます。

	4. **リソース グループ**で *[+ 新規]* を選択するか、既存のリソース グループを選択します。新しいリソース グループを作成する場合、その名前を入力します。

	5. ストレージ アカウントの**場所**を選択して、**[作成]** をクリックします。作成したアカウントが **[ストレージ アカウント]** パネルに表示されます。

		![ストレージ アカウントの詳細の入力](./media/virtual-machines-windows-upload-image/portal_create_storage_account.png)

	6. このステップと次のステップでは、このストレージ アカウントに BLOB コンテナーを作成する方法を説明します。イメージをアップロードして、イメージ用に新しい BLOB コンテナーを作成するには、PowerShell コマンドを使用することもできるため、このステップは省略可能です。BLOB コンテナーを自分で作成しない場合は、「[VM イメージのアップロード](#uploadvm)」セクションに進んでください。作成する場合は、**[サービス]** タイルの **[BLOB]** をクリックします。

		![BLOB サービス](./media/virtual-machines-windows-upload-image/portal_create_blob.png)

	7. BLOB のパネルが表示されたら、**[+ コンテナー]** をクリックして、新しい BLOB ストレージ コンテナーを作成します。コンテナーの名前と、アクセスの種類を入力します。

		![新しい BLOB の作成](./media/virtual-machines-windows-upload-image/portal_create_container.png)

  		> [AZURE.NOTE] 既定では、コンテナーはプライベートであり、アカウント所有者のみがアクセスできます。コンテナー内の BLOB にはパブリック読み取りアクセスを許可し、コンテナーのプロパティやメタデータにはアクセスを許可しない場合は、**[BLOB]** オプションを使用します。コンテナーと BLOB に完全パブリック読み取りアクセスを許可するには、**[コンテナー]** オプションを使用します。

	8. **[BLOB サービス]** パネルに、新しい BLOB コンテナーが表示されます。このコンテナーの URL をメモしておいてください。PowerShell コマンドでイメージをアップロードするときに必要になります。URL の長さと画面の解像度によって、URL の一部が表示されない場合があります。このような場合は、右上隅の **[最大化]** アイコンをクリックして、パネルを最大化します。


### PowerShell を使用して、Azure ストレージ アカウントを作成するには

1. Azure PowerShell 1.0.x を開き、Azure アカウントにサインインします。

		Login-AzureRmAccount

	このコマンドではポップアップ ウィンドウが開くので、Azure の資格情報を入力します。

2. 既定で選択されているサブスクリプション ID が作業するものと異なる場合は、次のいずれかのコマンドを使用して、適切なサブスクリプションを設定します。

		Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	または

		Select-AzureRmSubscription -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	Azure アカウントのサブスクリプションを検索するには、`Get-AzureRmSubscription` コマンドを使用します。

3. このサブスクリプションで使用可能なストレージ アカウントを検索します。

		Get-AzureRmStorageAccount

	既存のストレージ アカウントを使用する場合は、「[VM イメージのアップロード](#uploadvm)」セクションに進みます。

4. このイメージを保持する新しいストレージ アカウントを作成する場合は、次の手順に従います。

	1. このストレージ アカウントのリソース グループがあることを確認します。サブスクリプションのすべてのリソース グループを検索するには、次のコマンドを使用します。

			Get-AzureRmResourceGroup

	2. 新しいリソース グループを作成する場合は、次のコマンドを使用します。

			New-AzureRmResourceGroup -Name YourResourceGroup -Location "West US"

	3. このリソース グループに新しいストレージ アカウントを作成するには、次のコマンドを使用します。

			New-AzureRmStorageAccount -ResourceGroupName YourResourceGroup -Name YourStorageAccountName -Location "West US" -Type "Standard_GRS"


</br> <a id="uploadvm"></a>

## ストレージ アカウントに VM イメージをアップロードする

Azure PowerShell で次の手順を行って、ストレージ アカウントに VM イメージをアップロードします。イメージは、このアカウントの BLOB ストレージ コンテナーにアップロードされます。既存のコンテナーを使用することも、新しいコンテナーを作成することもできます。

1. `Login-AzureRmAccount` を使用して、Azure PowerShell 1.0.x にサインインします。前のセクションで説明したように、`Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"` を使用して、適切なサブスクリプションを使用していることを確認します。

2. 次のように [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) コマンドレットを使用して、ストレージ アカウントに汎用 Azure VHD を追加します。

		Add-AzureRmVhd -ResourceGroupName YourResourceGroup -Destination "<StorageAccountURL>/<BlobContainer>/<TargetVHDName>.vhd" -LocalFilePath <LocalPathOfVHDFile>

	各値の説明:
	- **StorageAccountURL** は、ストレージ アカウントの URL です。通常、`https://YourStorageAccountName.blob.core.windows.net` のような形式になります。*YourStorageAccountName* は、使用するストレージ アカウントの名前に置き換えてください。
	- **BlobContainer** は、イメージを格納する BLOB コンテナーです。指定された名前の BLOB コンテナーが見つからなかった場合、コマンドレットによって新しいコンテナーが作成されます。
	- **TargetVHDName** は、保存するイメージの名前です。
	- **LocalPathOfVHDFile** は、ローカル コンピューター上の .vhd ファイルの完全なパスと名前です。

	`Add-AzureRmVhd` の実行に成功すると、次のようになります。

		C:\> Add-AzureRmVhd -ResourceGroupName testUpldRG -Destination https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd -LocalFilePath "C:\temp\WinServer12.vhd"
		MD5 hash is being calculated for the file C:\temp\WinServer12.vhd.
		MD5 hash calculation is completed.
		Elapsed time for the operation: 00:03:35
		Creating new page blob of size 53687091712...
		Elapsed time for upload: 01:12:49

		LocalFilePath           DestinationUri
		-------------           --------------
		C:\temp\WinServer12.vhd https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd

	このコマンドは、ネットワーク接続や VHD ファイルのサイズによっては、完了に時間がかかります。

</br>
## アップロードしたイメージから新しい VM をデプロイする

アップロードしたイメージを使用して、新しい Windows VM を作成できます。次の手順では、Azure PowerShell と上の手順でアップロードした VM イメージを使用して、新しい Virtual Network に VM を作成する方法を示します。

>[AZURE.NOTE] VM イメージは、作成される実際の仮想マシンと同じストレージ アカウント内に存在する必要があります。

### ネットワーク リソースを作成する

次のサンプル PowerShell スクリプトを使用して、新しい VM 用に仮想ネットワークと NIC を設定します。**$** 記号によって表される変数には、実際のアプリケーションに適した値を使用します。

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### 新しい仮想マシンを作成する

次の PowerShell スクリプトでは、仮想マシンの構成を設定し、アップロードした VM イメージを新しいインストールのソースとして使用する方法を示します。</br>

	#Enter a new user name and password in the pop-up window for the following
	$cred = Get-Credential

	#Get the storage account where the uploaded image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	#Use "Get-Help New-AzureRmVMConfig" to know the available options for -VMsize
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A4"

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from the image (-CreateOption fromImage), and give the URL of the uploaded image VHD for the -SourceImageUri parameter
	#You can find this URL in the result of the Add-AzureRmVhd cmdlet above
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

たとえば、ワークフローは次のようになります。

		C:\> $pipName = "testpip6"
		C:\> $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
		C:\> $subnet1Name = "testsub6"
		C:\> $nicname = "testnic6"
		C:\> $vnetName = "testvnet6"
		C:\> $subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix
		C:\> $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig
		C:\> $nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
		C:\> $vmName = "testupldvm6"
		C:\> $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A4"
		C:\> $computerName = "testupldcomp6"
		C:\> $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
		C:\> $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
		C:\> $osDiskName = "testupos6"
		C:\> $osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
		C:\> $urlOfUploadedImageVhd = "https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd"
		C:\> $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows
		C:\> $result = New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
		C:\> $result
		RequestId IsSuccessStatusCode StatusCode ReasonPhrase
		--------- ------------------- ---------- ------------
		                         True         OK OK

新しく作成された VM は、[Azure ポータル](https://portal.azure.com)の **[参照]** の **[仮想マシン]**、または次の PowerShell コマンドで確認できます。

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## 次のステップ

Azure PowerShell を使用して新しい仮想マシンを管理する方法については、「[Azure Resource Manager と PowerShell を使用した仮想マシンの管理](virtual-machines-windows-ps-manage.md)」を参照してください。

<!---HONumber=AcomDC_0511_2016-->