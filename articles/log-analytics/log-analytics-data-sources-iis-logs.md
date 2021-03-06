<properties
   pageTitle="Log Analytics の IIS ログ | Microsoft Azure"
   description="インターネット インフォメーション サービス (IIS) は、Log Analytics によって収集されるログ ファイルにユーザーの利用状況を格納します。この記事では、IIS ログの収集を構成する方法と OMS リポジトリに作成されるレコードの詳細について説明します。"
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/11/2016"
   ms.author="bwren" />

# Log Analytics の IIS ログ
インターネット インフォメーション サービス (IIS) は、Log Analytics によって収集されるログ ファイルにユーザーの利用状況を格納します。

![IIS ログ](media/log-analytics-data-sources-iis-logs/overview.png)

## IIS ログの構成
Log Analytics は IIS によって作成されたログ ファイルからエントリを収集するため、[ログを収集するように IIS を構成](https://technet.microsoft.com/library/hh831775.aspx)し、Log Analytics が収集するフィールドを選択する必要があります。IIS は既定ではすべてのフィールドを収集しないため、必要に応じて追加のフィールドを手動で追加します。

Log Analytics は、W3C 形式で格納されている IIS ログ ファイルのみをサポートします。ログは NCSA または IIS のネイティブ形式では収集されません。

Log Analytics の IIS ログは、[Log Analytics の [設定] の [データ] メニュー](log-analytics-data-sources.md/configuring-data-sources)から構成します。必要な構成は、**[Collect W3C format IIS log files]** (W3C 形式の IIS ログ ファイルを収集する) を選択することのみです。

IIS ログの収集を有効にする場合、各サーバーに IIS ログ ロールオーバー設定を構成することをお勧めします。


## データ収集

Log Analytics は約 15 分おきに接続されたソースから IIS ログのエントリを収集します。エージェントは、収集元の場所を各イベント ログに記録します。エージェントが一定時間オフラインになった場合、Log Analytics は中止した箇所からイベントの収集を再開します。オフラインの間に作成されたイベントも収集されます。


## IIS ログ レコードのプロパティ

IIS ログ レコードの型は **W3CIISLog** になり、次の表に示すプロパティがあります。

| プロパティ | 説明 |
|:--|:--|
| Computer | イベント収集元のコンピューターの名前。 |
| cIP | クライアントの IP アドレス。 |
| csMethod | 要求のメソッド (GET、POST など) |
| csReferer | 現在のサイトに到達するまでにユーザーがリンクをフォローした元のサイト |
| csUserAgent | クライアントのブラウザーの種類。 |
| csUserName | サーバーにアクセスした認証されたユーザーの名前。匿名ユーザーはハイフンで示されます。 |
| csUriStem | 要求のターゲット (Web ページなど) |
| csUriQuery | クライアントが実行を試行していたクエリ (存在する場合) |
| ManagementGroupName | SCOM エージェントの管理グループの名前。その他のエージェントの場合、これは AOI-<workspace ID> です。 |
| RemoteIPCountry | クライアントの IP アドレスの国。 |
| RemoteIPLatitude | クライアントの IP アドレスの緯度。 |
| RemoteIPLongitude | クライアントの IP アドレスの経度。 |
| scStatus | HTTP 状態コード。 |
| scSubStatus | 副状態エラー コード。 |
| scWin32Status | Windows 状態コード。 |
| sIP | Web サーバーの IP アドレス。 |
| SourceSystem | OpsMgr |
| sPort | クライアントが接続されているサーバー上のポート。 |
| sSiteName | IIS サイトの名前。 |
| TimeGenerated | エントリがログに記録された日付と時刻。 |
| TimeTaken | 要求の処理にかかった時間 (ミリ秒) |

## IIS ログのログ検索

次の表は、IIS ログ レコードを取得するログ クエリのさまざまな例をまとめたものです。

| クエリ | 説明 |
|:--|:--|
| Type=IISLog | IIS ログのすべてのレコード。 |
| Type=IISLog EventLevelName=error | 重大度が「エラー」のすべての Windows イベント。 |
| Type=W3CIISLog | Measure count() by cIP | クライアントの IP アドレス別の IIS ログ エントリの数。 |
| Type=W3CIISLog csHost="www.contoso.com" | Measure count() by csUriStem | ホスト www.contoso.com の URL 別の IIS ログ エントリの数。 |
| Type=W3CIISLog | Measure Sum(csBytes) by Computer | top 500000| 各 IIS コンピューターによって受信された合計バイト数。 |

## 次のステップ

- 分析のために別の[データ ソース](log-analytics-data-sources.md)を収集するように Log Analytics を構成します。
- [ログ検索](log-analytics-log-searches.md)について学習し、データ ソースとソリューションから収集されたデータを分析します。
- IIS ログで検出された重要な状態を事前に通知するように、Log Analytics のアラートを構成します。

<!---HONumber=AcomDC_0518_2016-->