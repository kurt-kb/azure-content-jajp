<properties
	pageTitle="CDN - ファイル圧縮によるパフォーマンスの向上"
	description="ファイルを圧縮して、ファイル転送速度とページ読み込みパフォーマンスを向上させることができます。"
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/11/2016"
	ms.author="casoper"/>

# ファイル圧縮によるパフォーマンスの向上

圧縮は、サーバーからファイルを送信する前にファイル サイズを減らすことができるので、ファイルの転送速度を改善し、ページ読み込みのパフォーマンスを向上するには簡単で効率的な方法です。帯域幅のコストが軽減され、ユーザーへの応答も改善されます。

圧縮を有効にするには、2 つの方法があります。

- 配信元サーバーで圧縮を有効にします。この方法では、CDN は圧縮ファイルをパス スルーし、要求したクライアントに対して圧縮ファイルを配信します。
- CDN エッジ サーバーで直接、圧縮を有効にします。この方法では、配信元サーバーで圧縮されていない場合でも、CDN でファイルが圧縮され、エンド ユーザーに渡されます。

> [AZURE.IMPORTANT] CDN 構成の変更がネットワーク全体に反映されるまでには時間がかかります。<b>Azure CDN from Akamai</b> プロファイルの場合、通常、反映は 1 分以内で完了します。<b>Azure CDN from Verizon</b> プロファイルの場合、通常、反映は 90 分以内で完了します。今回初めて CDN エンドポイントの圧縮を設定した場合は、トラブルシューティングの前に、圧縮設定が確実に POP に反映されるように 1 ～ 2 時間の待機時間を検討してください。

## 圧縮の有効化

> [AZURE.NOTE] Standard および Premium CDN レベルでは同じ圧縮機能が提供されますが、ユーザー インターフェイスは異なります。Standard と Premium CDN レベルの違いの詳細については、[Azure CDN の概要](cdn-overview.md)に関する記述を参照してください。

### Standard レベル

> [AZURE.NOTE] このセクションは、**Azure CDN Standard from Verizon** プロファイルと **Azure CDN Standard from Akamai** プロファイルに適用されます。

1. CDN プロファイル ブレードで、管理する CDN エンドポイントをクリックします。

	![CDN プロファイル ブレード エンドポイント](./media/cdn-file-compression/cdn-endpoints.png)

	CDN エンドポイントのブレードが開きます。

2. **[構成]** をクリックします。

	![[CDN プロファイル] ブレードの [管理] ボタン](./media/cdn-file-compression/cdn-config-btn.png)

	CDN 構成ブレードが開きます。

3. **[圧縮]** をオンにします。

	![CDN の圧縮オプション](./media/cdn-file-compression/cdn-compress-standard.png)

4. 既定の種類を使用するか、ファイルの種類を削除または追加して一覧を変更します。
	
	> [AZURE.TIP] ZIP、MP3、MP4、JPG などの圧縮形式に圧縮を適用することは可能ですが、推奨されません。
	
5. 変更したら、**[保存]** をクリックします。

### Premium レベル

> [AZURE.NOTE] このセクションは、**Azure CDN Premium from Verizon** プロファイルに適用されます。

1. [CDN プロファイル] ブレードで、**[管理]** をクリックします。

	![[CDN プロファイル] ブレードの [管理] ボタン](./media/cdn-file-compression/cdn-manage-btn.png)

	CDN 管理ポータルが開きます。

2. **[HTTP ラージ]** タブ、**[キャッシュの設定]** フライアウトの順にマウスのカーソルを合わせます。**[圧縮]** をクリックします。

	圧縮のオプションが表示されます。

	![ファイル圧縮](./media/cdn-file-compression/cdn-compress-files.png)

3. **[Compression Enabled]** ラジオ ボタンをクリックして圧縮を有効にします。**[ファイルの種類]** テキストボックスで、コンマ区切りのリスト (スペースなし) として圧縮する MIME の種類を入力します。
		
	> [AZURE.TIP] ZIP、MP3、MP4、JPG などの圧縮形式に圧縮を適用することは可能ですが、推奨されません。

4. 変更したら、**[更新]** をクリックします。


## 圧縮のルール

次の表に、すべてのシナリオでの Azure CDN 圧縮動作を示します。

> [AZURE.IMPORTANT] **Azure CDN from Verizon** (Standard と Premium) の場合、条件を満たすファイルのみが圧縮されます。圧縮を実行可能にするためのファイルの条件は、次のとおりです。
>
> - 128 バイトより大きい
> - 1 MB 未満
> 
> **Azure CDN from Akamai** の場合、すべてのファイルが圧縮対象です。
>
> すべての Azure CDN 製品で、ファイルの MIME タイプは、[圧縮用に構成されている](#enabling-compression)必要があります。
>
> **Azure CDN from Verizon** プロファイル (Standard と Premium) は、**gzip**、**deflate**、または **bzip2** エンコーディングをサポートしています。**Azure CDN from Akamai** プロファイルは、**gzip** エンコーディングのみをサポートしています。
>
> **Azure CDN from Akamai** エンドポイントは、クライアントの要求に関係なく、常に配信元から **gzip** でエンコードされたファイルを要求します。

### 圧縮が無効であるか、ファイルの圧縮が実行不可能

|クライアントから要求された形式 (Accept-Encoding ヘッダー経由)|キャッシュされたファイルの形式|クライアントへの CDN の応答|メモ|
|----------------|-----------|------------|-----|
|圧縮|圧縮|圧縮| |
|圧縮|非圧縮|非圧縮| |	
|圧縮|キャッシュなし|圧縮または非圧縮|配信元の応答に依存|
|非圧縮|圧縮|非圧縮| |
|非圧縮|非圧縮|非圧縮| |	
|非圧縮|キャッシュなし|非圧縮| |

### 圧縮が有効でファイルの圧縮が実行可能

|クライアントから要求された形式 (Accept-Encoding ヘッダー経由)|キャッシュされたファイルの形式|クライアントへの CDN の応答|メモ|
|----------------|-----------|------------|-----|
|圧縮|圧縮|圧縮|サポートされている形式間での CDN のトランスコード|
|圧縮|非圧縮|圧縮|CDN が圧縮を実行|
|圧縮|キャッシュなし|圧縮|配信元から非圧縮が返された場合、CDN が圧縮を実行|
|非圧縮|圧縮|非圧縮|CDN が展開を実行|
|非圧縮|非圧縮|非圧縮| |	
|非圧縮|キャッシュなし|非圧縮| |	

## メモ
1. Media Services CDN 対応ストリーミング エンドポイントについて、圧縮は既定で application/vnd.ms-sstr+xml、application/dash+xml、application/vnd.apple.mpegurl、application/f4m+xml というコンテンツの種類に対応しています。これらの種類の圧縮は、Azure ポータルを使用して有効/無効にすることはできません。  

## 関連項目
- [CDN ファイルの圧縮のトラブルシューティング](cdn-troubleshoot-compression.md)    

<!---HONumber=AcomDC_0518_2016-->