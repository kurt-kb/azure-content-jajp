<properties
   pageTitle="StorSimple Virtual Array をデプロイする - Hyper-V でプロビジョニングする"
   description="StorSimple Virtual Array のデプロイの 2 番目のチュートリアルには、Hyper-V での仮想デバイスのプロビジョニングが含まれます。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/26/2016"
   ms.author="alkohli"/>

# StorSimple Virtual Array をデプロイする - Hyper-V で Virtual Array をプロビジョニングする

![](./media/storsimple-ova-deploy2-provision-hyperv/hyperv4.png)

## 概要

このプロビジョニング チュートリアルは、2016 年 3 月の一般公開 (GA) リリースを実行する Microsoft Azure StorSimple Virtual Array (StorSimple オンプレミス仮想デバイスまたはStorSimple 仮想デバイスとも呼ばれます) に適用されます。このチュートリアルでは、Windows Server 2012 R2、Windows Server 2012、または Windows Server 2008 R2 の Hyper-V を実行するホスト システムに StorSimple Virtual Array をプロビジョニングする方法について説明します。この記事は、Azure クラシック ポータルだけでなく、Microsoft Azure Government Cloud での StorSimple Virtual Arrays のデプロイに適用されます。

仮想デバイスをプロビジョニングして構成するには、管理者特権が必要です。プロビジョニングと初期セットアップは、完了するまでに約 10 分かかることがあります。


## プロビジョニングの前提条件

ここでは、Windows Server 2012 R2、Windows Server 2012、または Windows Server 2008 R2 の Hyper-V を実行するホスト システムに仮想デバイスをプロビジョニングする際の前提条件について説明します。

### StorSimple Manager サービスの場合

開始する前に次の点を確認します。

-   [StorSimple Virtual Array のポータルの準備](storsimple-ova-deploy1-portal-prep.md)に関するページの手順がすべて完了していること。

-   Azure ポータルから Hyper-V の仮想デバイスのイメージをダウンロードしていること。詳細については、「[手順 3: 仮想デバイスのイメージをダウンロードする](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image)」を参照してください。

	> [AZURE.IMPORTANT] StorSimple Virtual Array で実行されているソフトウェアは、Storsimple Manager サービスとの組み合わせでのみ使用できます。

### StorSimple 仮想デバイスの場合

仮想デバイスをデプロイする前に次の点を確認します。

-   Windows Server 2008 R2 以降の Hyper-V を実行し、デバイスのプロビジョニングに使用できるホスト システムへのアクセス権があること。

-   ホスト システムで、次のリソースを仮想デバイスのプロビジョニング専用に使用できること。

	-   最小で 4 コア。

	-   少なくとも 8 GB の RAM。

	-   ネットワーク インターフェイス 1 つ。

	-   システム データ用の 500 GB の仮想ディスク。

### データセンターのネットワークの場合

作業を始める前に、StorSimple 仮想デバイスをデプロイしてデータセンター ネットワークを適切に構成するためのネットワーク要件を確認します。詳細については、[StorSimple Virtual Array のネットワーク要件](storsimple-ova-system-requirements.md#networking-requirements)に関するセクションをご覧ください。

## プロビジョニングの手順

仮想デバイスをプロビジョニングして接続するには、次の手順を実行する必要があります。

1.  ホスト システムに仮想デバイスの最小要件を満たすための十分なリソースがあることを確認します。

2.  ハイパーバイザーで仮想デバイスをプロビジョニングします。

3.  仮想デバイスを起動し、IP アドレスを取得します。

これらの各手順を以下のセクションで説明します。

## 手順 1: ホスト システムが仮想デバイスの最小要件を満たしていることを確認する

仮想デバイスを作成するには、次の要件が必要です。

-   Windows Server 2012 R2、Windows Server 2012、または Windows Server 2008 R2 SP1 にインストールされた Hyper-V ロール。

-   ホストに接続されている Microsoft Windows クライアント上の Microsoft Hyper-V マネージャー。

仮想デバイスを作成している基盤となるハードウェア (ホスト システム) で、次のリソースを仮想デバイス専用に使用できることを確認する必要があります。

- 最小で 4 コア。
- 少なくとも 8 GB の RAM。
- ネットワーク インターフェイス 1 つ。
- システム データ用の 500 GB の仮想ディスク。

## 手順 2: ハイパーバイザーで仮想デバイスをプロビジョニングする

ハイパーバイザーでデバイスをプロビジョニングするには、次の手順を実行します。

#### 仮想デバイスをプロビジョニングするには

1.  Windows Server ホストで、仮想デバイスのイメージをローカル ドライブにコピーします。これは、Azure ポータルからダウンロードしたイメージ (VHD または VHDX) です。このイメージは手順の後半で使用するため、コピーした場所をメモしておきます。

2.  **サーバー マネージャー**を開きます。右上隅の **[ツール]** をクリックし、**[Hyper-V マネージャー]** を選択します。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image1.png)

	Windows Server 2008 R2 を実行している場合は、Hyper-V マネージャーを開きます。サーバー マネージャーで、**[ロール] > [Hyper-V] > [Hyper-V マネージャー]** をクリックします。

1.  **Hyper-V マネージャー**のスコープ ウィンドウで、システム ノードを右クリックしてコンテキスト メニューを開き、**[新規]**、**[仮想マシン]** の順にクリックします。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image2.png)

1.  仮想マシンの新規作成ウィザードの **[開始する前に]** ページで **[次へ]** をクリックします。

1.  **[名前と場所を指定]** ページで、仮想デバイスの**名前**を入力します。**[次へ]** をクリックします。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image4.png)

1.  **[世代の指定]** ページで、デバイスのイメージの種類を選択し、**[次へ]** をクリックします。Windows Server 2008 R2 を使用している場合、このページは表示されません。

    * Windows Server 2012 以降の .vhdx イメージをダウンロードした場合は、**[第 2 世代]** を選択します。
    * Windows Server 2008 R2 以降の .vhd イメージをダウンロードした場合は、**[第 1 世代]** を選択します。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image5.png)

1.  **[メモリの割り当て]** ページで、**8192 MB** 以上の**起動メモリ**を指定します。動的メモリは有効にしないでください。**[次へ]** をクリックします。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image6.png)

1.  **[ネットワークの構成]** ページで、インターネットに接続されている仮想スイッチを指定し、**[次へ]** をクリックします。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image7.png)

1.  **[仮想ハード ディスクの接続]** ページで、**[既存の仮想ハード ディスクを使用する]** を選択し、仮想デバイスのイメージ (.vhdx または .vhd) の場所を指定して、**[次へ]** をクリックします。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image8m.png)

1.  **[概要]** を確認し、**[完了]** をクリックして仮想マシンを作成します。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image9.png)

1.  最小要件を満たすには、4 コアが必要です。4 つの仮想プロセッサを追加するには、**[Hyper-V マネージャー]** ウィンドウでホスト システムを選択し、右側のウィンドウの **[仮想マシン]** の一覧で作成した仮想マシンを見つけます。マシン名を選択して右クリックし、**[設定]** を選択します。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image10.png)

1.  **[設定]** ページの左側のウィンドウで **[プロセッサ]** をクリックします。右側のウィンドウで、**[仮想プロセッサの数]** を 4 (またはそれ以上) に設定します。**[適用]** をクリックします。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image11.png)

1.  最小要件を満たすには、500 GB の仮想データ ディスクを追加する必要もあります。**[設定]** ページで次の操作を行います。

    1.  左側のウィンドウで **[SCSI コントローラー]** を選択します。
    2.  右側のウィンドウで **[ハード ドライブ]** を選択し、**[追加]** をクリックします。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image12.png)

1.  **[ハード ドライブ]** ページで **[仮想ハード ディスク]** を選択し、**[新規]** をクリックします。**仮想ハード ディスクの新規作成ウィザード**が開始されます。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image13.png)

1.  仮想ハード ディスクの新規作成ウィザードの **[開始する前に]** ページで、**[次へ]** をクリックします。

1.  **[ディスク フォーマットの選択]** ページで、既定のオプションの **[VHDX]** 形式をそのまま使用します。**[次へ]** をクリックします。Windows Server 2008 R2 を実行している場合、この画面は表示されません。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image15.png)

1.  **[ディスクの種類の選択]** ページで、仮想ハード ディスクの種類を **[容量可変]** に設定します (推奨)。**[固定サイズ]** を選択しても動作しますが、待機時間が長くなる可能性があります。**[差分]** は使用しないことをお勧めします。**[次へ]** をクリックします。Windows Server 2012 R2 および Windows Server 2012 では、**[容量可変]** が既定値です。Windows Server 2008 R2 では、**[固定サイズ]** が既定値です。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image16.png)

1.  **[名前と場所の指定]** ページで、データ ディスクの**名前**と**場所**を入力します (場所は参照することもできます)。**[次へ]** をクリックします。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image17.png)

1.  **[ディスクの構成]** ページで、**[新しい空の仮想ハード ディスクを作成する]** を選択し、サイズを **500 GB** (またはそれ以上) に指定します。**[次へ]** をクリックします。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image18.png)

1.  **[概要]** ページで仮想データ ディスクの詳細を確認し、問題がなければ **[完了]** をクリックしてディスクを作成します。ウィザードが終了し、仮想ハード ディスクがコンピューターに追加されます。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image19.png)

2.  **[設定]** ページが再び表示されます。**[OK]** をクリックして **[設定]** ページを閉じ、[Hyper-V マネージャー] ウィンドウに戻ります。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image20.png)

## 手順 3: 仮想デバイスを起動し、IP アドレスを取得する

仮想デバイスを起動して接続するには、次の手順を実行します。

#### 仮想デバイスを起動するには

1.  仮想デバイスを起動します。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image21.png)

1.  デバイスが起動したら、デバイスを選択して右クリックし、**[接続]** を選択します。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image22.png)

1.  デバイスの準備ができるまでに 5 ～ 10 分かかる場合があります。進行状況を示すステータス メッセージがコンソールに表示されます。デバイスの準備ができたら **[アクション]** に移動します。`Ctrl + Alt + Delete` キーを押して仮想デバイスにログインします。既定のユーザーは *StorSimpleAdmin* で、既定のパスワードは *Password1* です。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image23.png)

1.  セキュリティ上の理由から、デバイス管理者のパスワードは初回ログオン後に有効期限が切れます。パスワードを変更するように促されます。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image24.png)

	8 文字以上を含むパスワードを入力します。パスワードは、4 つの要件 (大文字、小文字、数字、および特殊文字) のうち少なくとも 3 つを満たす必要があります。確認のためにパスワードを再入力します。パスワードが変更されたことが通知されます。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image25.png)

1.  パスワードが正常に変更されると、仮想デバイスが再起動することがあります。デバイスが起動するのを待ちます。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image26.png)

 	デバイスの Windows PowerShell コンソールが、進行状況バーと共に表示されます。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image27.png)

1.  手順 6 ～ 8 は、非 DHCP 環境での起動時にのみ適用されます。DHCP 環境の場合は、手順 6 ～ 8 をスキップし、手順 9 に進みます。非 DHCP 環境でデバイスを起動した場合は、次の画面が表示されます。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image28m.png)

 	次にネットワークを構成する必要があります。

1.  `Get-HcsIpAddress` コマンドを使用して、仮想デバイスで有効になっているネットワーク インターフェイスの一覧を表示します。デバイスで有効になっているネットワーク インターフェイスが 1 つの場合、このインターフェイスに割り当てられる既定の名前は `Ethernet` です。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image29m.png)

1.  `Set-HcsIpAddress` コマンドレットを使用してネットワークを構成します。例を次に示します。

 	`Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

 	![](./media/storsimple-ova-deploy2-provision-hyperv/image30.png)

1.  初期セットアップが完了し、デバイスが再起動すると、デバイスのバナー テキストが表示されます。デバイスを管理するため、バナー テキストに表示される IP アドレスと URL をメモしておきます。この IP アドレスを使用して、仮想デバイスの Web UI に接続し、ローカル セットアップと登録を行います。

	![](./media/storsimple-ova-deploy2-provision-hyperv/image31m.png)



1. (省略可能) デバイスを Government Cloud にデプロイする場合にのみ、この手順を実行します。デバイスで米国連邦情報処理標準 (FIPS) モードを有効にできるようになります。FIPS 140 標準には、機密データ保護のために米国連邦政府のコンピューター システムに使用することが承認されている暗号化アルゴリズムが定義されています。
	1. FIPS モードを有効にするには、次のコマンドレットを実行します。

		`Enter-HcsFIPSMode`

	2. FIPS モードを有効にした後はデバイスを再起動して、暗号化の検証が有効になるようにします。

		> [AZURE.NOTE] デバイスで FIPS モードを有効または無効にすることができます。デバイスの FIPS モードと非 FIPS モードを切り替えることはサポートされていません。

デバイスが最小構成要件を満たしていない場合は、バナー テキストにエラーが表示されます (下記参照)。最小要件を満たすための十分なリソースを確保するようにデバイスの構成を変更する必要があります。その後、再起動し、デバイスに接続します。「[手順 1: ホスト システムが仮想デバイスの最小要件を満たしていることを確認する](#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements)」にある最小構成要件を参照してください。

![](./media/storsimple-ova-deploy2-provision-hyperv/image32.png)

ローカル Web UI を使用した初期構成時に他のエラーが発生した場合は、[ローカル Web UI を使用した StorSimple Virtual Array の管理](storsimple-ova-web-ui-admin.md)に関する記事の次のワークフローを参照してください。

-   診断テストを実行して [Web UI のセットアップのトラブルシューティング](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors)を行う。

-   [ログ パッケージを生成してログ ファイルを表示する](storsimple-ova-web-ui-admin.md#generate-a-log-package)。

![動画アイコン](./media/storsimple-ova-deploy2-provision-hyperv/video_icon.png) **ビデオ**

Hyper-V で StorSimple Virtual Array をプロビジョニングする方法を説明したビデオをご覧ください。

> [AZURE.VIDEO create-a-storsimple-virtual-array]

## 次のステップ

-   [StorSimple Virtual Array をファイル サーバーとして設定する](storsimple-ova-deploy3-fs-setup.md)

-   [StorSimple Virtual Array を iSCSI サーバーとして設定する](storsimple-ova-deploy3-iscsi-setup.md)

<!---HONumber=AcomDC_0601_2016-->