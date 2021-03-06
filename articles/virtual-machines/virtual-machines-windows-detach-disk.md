<properties
	pageTitle="Windows VM からデータ ディスクを切断する | Microsoft Azure"
	description="Resource Manager デプロイ モデルを使用する Azure 仮想マシンから、データ ディスクを切断する方法について説明します。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/02/2016"
	ms.author="cynthn"/>



# Windows 仮想マシンからディスクを切断する方法


仮想マシンに接続されたデータ ディスクが不要になった場合、そのディスクは簡単に切断できます。そうすれば、ディスクは仮想マシンから削除されますが、ストレージからは削除されません。

> [AZURE.WARNING] ディスクを切断した場合、自動的には削除されません。Premium Storage のサブスクリプションにお申込みいただいている場合は、ディスクのストレージ料金が引き続き発生します。詳細については[Premium Storage 利用時の料金と課金](../storage/storage-premium-storage.md#pricing-and-billing)に関する記事を参照してください。

再びディスク上の既存のデータを使用する場合は、同じ仮想マシンや別の仮想マシンに再接続できます。


## ポータルを使用してデータ ディスクを切断する方法

1. ポータル ハブで **[仮想マシン]** を選択します。

2. 切断するデータ ディスクが接続されている仮想マシンを選択し、**[すべての設定]**をクリックします。

3. **[設定]** ブレードで、**[ディスク]** を選択します。

4. **[ディスク]**ブレードで、切断するデータ ディスクを選択します。

5. データ ディスクのブレードで、**[切断]**をクリックします。


	![[切断] ボタンが表示されたスクリーンショット。](./media/virtual-machines-windows-detach-disk/detach-disk.png)

ディスクはストレージに残りますが、仮想マシンからは切断されています。


## PowerShell を使用してデータ ディスクを切断する方法

この例では、最初のコマンドで Get-AzureRmVM コマンドレットを使用して、リソース グループ**RG11** の**MyVM07** という名前の仮想マシンを取得します。コマンドは仮想マシンを変数**$VirtualMachine** に保存します。

2 番目のコマンドは、DataDisk3 という名前のデータ ディスクを仮想マシンから削除します。

最後のコマンドは仮想マシンの状態を更新し、データ ディスク削除のプロセスを完了します。

	$VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" 
	Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
	Update-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" -VM $VirtualMachine


詳細については、「[Remove-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx)」を参照してください。

## 次のステップ

データ ディスクを再利用する場合は、[別の VM にそのデータ ディスクをアタッチ](virtual-machines-windows-attach-disk-portal.md)します。

<!---HONumber=AcomDC_0608_2016-->