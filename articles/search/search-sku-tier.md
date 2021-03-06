<properties
	pageTitle="Azure Search 用の SKU またはレベルの選択 | Microsoft Azure"
	description="Azure Search は無料、Basic、Standard の各 SKU にプロビジョニングできます。Standard は、複数のリソース構成および容量レベルで使用できます。"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="paulettm"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="06/05/2016"
	ms.author="heidist"/>

# Azure Search 用の SKU またはレベルの選択

Azure Search で [Search サービスを作成する](search-create-service-portal.md)前に、どのレベルまたは SKU でサービスをプロビジョニングするかを決定しておく必要があります。SKU には無料、Basic、および Standard があります。このうち Standard は、複数のリソース構成および容量で使用できます。

容量が大きいほど、サービスの実行にかかるコストも高くなります。この記事の情報は、どの SKU で適切なバランスが得られるかを判断するのに役立ちますが、そのためには少なくとも、作成する予定のインデックスの数とサイズに関する大まかな見積もりと、クエリの量の概算を把握しておく必要があります。見積もりが得られたら、次の手順によってプロセスを簡素化できます。

- **手順 1.** 以降の SKU の説明を読んで、どのようなオプションがあるかを確認します。
- **手順 2.** 一連の質問に回答して、選択肢を絞り込みます。
- **手順 3** ストレージおよび価格に対する厳密な制限を確認しながら、決定内容について検証します。 

## SKU の説明

各 SKU の説明を次の表に示します。ここでは、パーティション、レプリカ、および該当する場合は 1 秒あたりのクエリ数 (QPS) の観点から、それぞれの SKU を簡単に比較しています。レプリカおよびパーティションの概要については、「[Azure Search サービスの制限](search-limits-quotas-capacity.md)」を参照してください。

レベル|容量|対象
----|-----------|-----------
無料|50 MB または 3 個のインデックスと 10,000 個のドキュメント|評価、調査、または小規模なワークロードに使用される、課金なしの共有サービス。他のサブスクライバーと共有されるため、クエリのスループットとインデックス数は、サービスを使用している他のサブスクライバーに基づいて変化します。
Basic|3 個のレプリカと 1 個のパーティション|専用ハードウェア上の小規模な実稼働ワークロード。高可用性。
Standard 1 (S1)|パーティションはそれぞれ 25 GB。<br/><br/>レプリカの QPS は約 15 クエリ/秒。|パーティション (12) とレプリカ (12) の柔軟な組み合わせをサポートし、専用ハードウェア上の中規模の実稼働ワークロードに対して使用されます。最大 36 個の請求可能な検索単位でサポートされる組み合わせで、レプリカとパーティションを割り当てることができます。
Standard 2 (S2)|パーティションはそれぞれ 100 GB。<br/><br/>レプリカの QPS は約 60 クエリ/秒。|S1 と同じ構成を使用しながら、より多くのパーティションとレプリカで、より大規模な実稼働ワークロードを実行できます。
Standard 3 (S3) プレビュー|パーティションはそれぞれ 200 GB。<br/><br/>レプリカの QPS は 60 クエリ/秒以上。|ハイエンド システムでは、最大 12 個のパーティションまたは 12 個のレプリカの構成で、より大規模な実稼働ワークロードを実行できます。 
Standard 3 High Density (S3 HD) プレビュー|200 GB の 1 個のパーティション。<br/><br/>レプリカの QPS は 60 クエリ/秒以上。|小さいインデックスを多数使用するお客様を対象としています。

## SKU を選択するための意思決定パス

次の表は、「[Azure Search サービスの制限](search-limits-quotas-capacity.md)」から制限の一部を抜粋したものです。ここでは、前の表に示される SKU の容量に加え、コンテンツの種類 (インデックスとドキュメント) に関連した追加の制限も含め、SKU の意思決定に影響を与える可能性が最も高い要因を示しています。意思決定の際には、この表を参考にしてください。

リソース|無料|Basic|S1|S2|S3 <br/>(プレビュー) |S3 HD <br/>(プレビュー) 
---|---|---|---|----|---|----
サービス レベル アグリーメント (SLA)|いいえ <sup>1</sup> |はい |はい |はい |いいえ <sup>1</sup> |いいえ <sup>1</sup> 
最大パーティション数|該当なし |1 |12 |12 |12|1
SKU ごとに許可されるインデックス数|3|5|50|200|200|1,000
ドキュメントの制限|合計 10,000|サービスあたり 100 万|パーティションあたり 1,500 万 |パーティションあたり 6,000 万|パーティションあたり 1 億 2,000 万 |インデックスあたり 100 万
パーティション サイズ|合計 50 MB|サービスあたり 2 GB|パーティションあたり 25 GB |パーティションあたり 100 GB (サービスあたり最大 1.2 TB)|パーティションあたり 200 GB (サービスあたり最大 2.4 TB)|200 GB (1 個のパーティション)
最大レプリカ数|該当なし |3 |12 |12 |12|12
秒間クエリ|該当なし|レプリカあたり最大 3|レプリカあたり最大 15|レプリカあたり最大 60|レプリカあたり 60 以上|レプリカあたり 60 以上

<sup>1</sup> 無料およびプレビューの SKU には SLA がありません。SKU が一般公開されると、SLA が適用されます。

> [AZURE.NOTE] レプリカとパーティションの最大値には、組み合わせによる最大課金構成である 36 単位が適用されるため、表に示される最大値よりも実際の上限は低くなります。たとえば、最大 12 個のレプリカを使用する場合、使用できるパーティションは最大で 3 個となります (12 * 3 = 36 単位)。同様に、最大数のパーティションを使用する場合は、レプリカの数を 3 個に減らす必要があります。使用できる組み合わせの一覧については、「[Azure Search でクエリとインデックス作成のワークロードに応じてリソース レベルをスケールする](search-capacity-planning.md)」を参照してください。

### SKU の選択についての一般的な質問

次の質問は、ワークロードに対する適切な SKU の決定に役立ちます。

1. **サービス レベル アグリーメント (SLA)** の要件がありますか? SKU の決定を Basic、またはプレビュー以外の Standard に絞り込んでください。
2. **何個のインデックス**が必要ですか? SKU の決定に最も大きな影響を与える変数の 1 つが、各 SKU でサポートされるインデックスの数です。インデックスのサポート内容は、低い価格レベルでは著しく異なります。インデックス数の要件は、どの SKU を選択するかの主要な決定要因となる可能性があります。
3. 各インデックスに**何個のドキュメント**が読み込まれますか? ドキュメントの数とサイズは、インデックスの最終的なサイズを決定します。インデックスの予測サイズを見積もることができると仮定した場合、そのサイズを SKU あたりのパーティション サイズ、およびそのサイズのインデックスを格納するために必要なパーティション数と比較して検討できます。 
4. **予測されるクエリ負荷はどのくらいか**? ストレージの要件を把握した後は、クエリのワークロードについて検討します。S2 SKU および S3 の両方の SKU は同等に近いスループットを提供しますが、プレビュー SKU は SLA の要件から除外されます。 

ほとんどのお客様は、これら 4 つの質問に対する回答に基づいて、特定の SKU を検討するか除外するかを決定できます。それでもまだ、どの SKU が適切であるかがわからない場合は、Azure サポートまでお問い合わせください。

## 意思決定の検証: SKU が十分なストレージと QPS を提供するか

最後の手順として、[価格のページ](https://azure.microsoft.com/pricing/details/search/)と[サービスの制限のサービスごとおよびインデックスごとのセクション](search-limits-quotas-capacity.md)を見直し、サブスクリプションとサービスの制限に照らして見積もりをもう一度確認します。

価格またはストレージの要件が範囲外にある場合は、(たとえば) 複数の小規模なサービス間でワークロードをリファクタリングすることができます。より細かいレベルでは、インデックスを小さく設計し直したり、フィルターを使用してクエリの効率を高めたりすることもできます。

> [AZURE.NOTE] ドキュメントに余分なデータが含まれている場合は、ストレージの要件が過剰になる可能性があります。ドキュメントは、検索可能なデータまたはメタデータのみで構成されているのが理想的です。ドキュメントの肥大化につながるバイナリ データは別の場所 (たとえば Azure テーブルまたは BLOB ストレージ) に保存し、外部データへの URL 参照を保持するフィールドをインデックスに追加します。個々のドキュメントの最大サイズは 16 MB です (1 回の要求で複数のドキュメントを一括アップロードする場合は、それより小さくなります)。詳細については、「[Azure Search サービスの制限](search-limits-quotas-capacity.md)」を参照してください。

## 次のステップ

どの SKU が適切であるかを決定した後は、次の手順に進みます。
- [ポータルで Search サービスを作成する](search-create-service-portal.md)
- [サービスをスケールするためにパーティションとレプリカの割り当てを変更する](search-capacity-planning.md)

<!---HONumber=AcomDC_0608_2016-->