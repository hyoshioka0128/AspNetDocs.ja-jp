---
uid: signalr/overview/getting-started/supported-platforms
title: サポートされているプラットフォーム |Microsoft Docs
author: bradygaster
description: この記事では、どのようなクライアントとサーバーは、SignalR でサポートされてについて説明します。
ms.author: bradyg
ms.date: 04/18/2018
ms.assetid: eac31beb-0f46-4afa-9def-e80904dea4f0
msc.legacyurl: /signalr/overview/getting-started/supported-platforms
msc.type: authoredcontent
ms.openlocfilehash: 89730e591bb94022d16fe1a78a4350b38e0bf7a4
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59420888"
---
# <a name="supported-platforms"></a>サポートされているプラットフォーム

提供者: [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> この記事では、どのようなクライアントとサーバーは、SignalR でサポートされてについて説明します。 
> 
> ## <a name="questions-and-comments"></a>意見やご質問
> 
> このチュートリアルの良い点に関するフィードバックや、ページ下部にあるコメントで改善できる点をお知らせください。 チュートリアルに直接関係のない質問がある場合は、[ASP.NET SignalR フォーラム](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)または[StackOverflow.com](http://stackoverflow.com/)にて投稿してください。

SignalR は、さまざまなサーバーおよびクライアントの構成でサポートされています。 また、各トランスポート オプションでは、一連の; 独自の要件トランスポートのシステム要件が利用できない場合は、SignalR は適切に他のトランスポートへのフェールオーバーにします。 SignalR をサポートするトランスポートの詳細については、次を参照してください。[トランスポートとフォールバック](introduction-to-signalr.md#transports)します。

## <a name="server-system-requirements"></a>サーバーのシステム要件

さまざまなサーバー構成では、SignalR サーバー コンポーネントをホストすることができます。 このセクションでは、オペレーティング システム、.NET framework、インターネット インフォメーション サーバー、およびその他のコンポーネントのサポートされているバージョンについて説明します。

### <a name="supported-server-operating-systems"></a>サポートされているサーバー オペレーティング システム

SignalR のサーバー コンポーネントは、次のサーバーまたはクライアントのオペレーティング システムでホストできます。 Websocket を使用する SignalR の Windows Server 2012、Windows Server 2016 または Windows 8 が必要であるに注意してください (WebSocket を使用できます Windows Azure Web サイトでは、サイトの .NET framework のバージョンは 4.5 に設定され、サイトの Web ソケットが有効になっている限り、構成ページの場合)。

- Windows Server 2016
- Windows Server 2012
- Windows Server 2008 r2
- Windows 10
- Windows 8
- Windows 7
- Microsoft Azure

### <a name="supported-server-net-framework-version"></a>サポートされているサーバーの .NET Framework のバージョン

SignalR 2 は、.NET Framework 4.5 でのみサポートされます。 参照してください、[推奨される更新プログラム](#updates)信頼性、互換性、安定性、およびパフォーマンスを向上させる更新プログラムのセクション。

### <a name="supported-server-iis-versions"></a>サポートされているサーバーのバージョンの IIS

SignalR は、IIS でホストされている、次のバージョンがサポートされます。 クライアントのオペレーティング システムを使用する場合などの開発 (Windows 8 または Windows 7) の完全なバージョンの IIS または Cassini する必要があります使用しないこと、接続から非常に短時間に達したが 10 の同時接続、課せられる制限があるために注意してください。頻繁に再確立されると、一時的なものとは、破棄されていないすぐに使用されていないとします。 クライアント オペレーティング システムでは、IIS Express を使用する必要があります。

また、WebSocket を使用する SignalR の IIS 8 または IIS 8 の Express を使用する必要があります、サーバーに Windows 8、Windows Server 2012、またはそれ以降、する必要がありますを使用して、IIS で WebSocket を有効にする必要がありますに注意してください。 IIS で WebSocket を有効にする方法については、次を参照してください。 [IIS 8.0 WebSocket プロトコルのサポート](https://www.iis.net/learn/get-started/whats-new-in-iis-8/iis-80-websocket-protocol-support)します。

- IIS 8、または IIS 8 Express。
- IIS 7 および 7.5 です。 サポート[拡張子のない Url](https://support.microsoft.com/kb/980368)が必要です。
- IIS は、統合モードで実行する必要があります。クラシック モードがサポートされていません。 最大で 30 秒間のメッセージの待ち時間は、IIS Server-Sent イベント トランスポートを使用してクラシック モードで実行している場合に発生する可能性です。
- ホスト アプリケーションは、完全な信頼モードで実行する必要があります。

## <a name="client-system-requirements"></a>クライアントのシステム要件

SignalR は、さまざまなクライアント プラットフォームで使用できます。 このセクションでは、web ブラウザー、Windows デスクトップ アプリケーション、Silverlight アプリケーション、モバイル デバイスで SignalR を使用するためのシステム要件について説明します。

### <a name="web-browsers"></a>Web ブラウザー

SignalR は、さまざまな web ブラウザーで使用できますが、通常、最新の 2 つのバージョンのみがサポートされています。

ブラウザーで SignalR を使用するアプリケーションには、jQuery バージョン 1.6.4 またはメジャーの以降 (1.7.2、1.8.2、1.9.1 など) を使用する必要があります。

SignalR は、次のブラウザーで使用できます。

- Microsoft Internet Explorer 8、9、10、および 11 のバージョン。 最新のデスクトップ、モバイル バージョンをサポートします。
- 現在のバージョンの Mozilla Firefox: - 1 の場合、Windows と Mac の両方のバージョン。
- Google Chrome: 現在のバージョンでは、1、Windows と Mac の両方のバージョン。
- Safari: 現在のバージョンの - 1 の場合、Mac、iOS の両方のバージョン。
- Opera: 現在のバージョン - 1、Windows のみです。
- Android ブラウザー

SignalR を使用するさまざまなトランスポートでは特定のブラウザーを必要とするだけでなく、独自の要件があります。 次の構成では、次のトランスポートがサポートされています。

<a id="browser"></a>

**Web ブラウザーのトランスポートの要件**

| Transport | Internet Explorer | Chrome (Windows または iOS) | Firefox | Safari (OSX または iOS) | Android |
| --- | --- | --- | --- | --- | --- |
| WebSocket | 10+ | 現在の値-1 | 現在の値-1 | 現在の値-1 | N/A |
| Server-Sent Events | N/A | 現在の値-1 | 現在の値-1 | 現在の値-1 | N/A |
| ForeverFrame | 8+ | N/A | N/A | N/A | 4.1 |
| ロング ポーリング | 8+ | 現在の値-1 | 現在の値-1 | 現在の値-1 | 4.1 |

\*:6 + のすべての機能が必要です。

#### <a name="unsupported-browsers"></a>サポートされていないブラウザー

SignalR を while*可能性があります*を古いバージョンのブラウザーでの主要な問題なく実行に積極的に SignalR をテストしないでくださいし、一般に含まれるバグを修正しません。

### <a name="windows-desktop-and-silverlight-applications"></a>Windows デスクトップ アプリケーションと Silverlight アプリケーション

Web ブラウザーで実行されている、だけでなく、スタンドアロンの Windows クライアントまたは Silverlight アプリケーションで SignalR をホストできます。 Windows デスクトップおよび Silverlight SignalR アプリケーションでは、次のシステム要件があります。

- .NET 4 を使用してアプリケーションには、Windows XP SP3 以降がサポートされます。
- .NET Framework 4.5 を使用してアプリケーションには、Windows Vista 以降ではサポートされています。

SignalR を使用可能なトランスポートではオペレーティング システムと .NET framework の要件だけでなく、独自の要件があります。 次の構成では、次のトランスポートがサポートされています。

**Windows デスクトップおよび Silverlight のトランスポートの要件**

| Transport | .NET アプリケーション | Silverlight |
| --- | --- | --- |
| Web ソケット | Windows 8 以降、.NET 4.5 以降 | N/A |
| 永遠にフレーム | N/A | N/A |
| Server-Sent Events | .NET 4+ | 5+ |
| ロング ポーリング | .NET 4+ | 5+ |

<a id="android"></a>

### <a name="windows-store-and-windows-phone-applications"></a>Windows ストアおよび Windows Phone アプリケーション

SignalR は、Windows ストア アプリケーションと Windows Phone 8 アプリケーションで使用できます。 次の構成では、次のトランスポートがサポートされています。

**Windows ストアおよび Windows Phone のトランスポートの要件**

| Transport | Windows ストア/.NET | Windows ストア/JavaScript | Windows Phone、IE | Windows Phone/ .NET |
| --- | --- | --- | --- | --- |
| WebSocket | N/A | Win8 + | 8+ | N/A |
| 永遠にフレーム | N/A | Win8 + | 7.5+ | N/A |
| Server-Sent Events | Win8 + | N/A | N/A | 8+ |
| ロング ポーリング | Win8 + | Win8 + | 7.5+ | 8+ |

<a id="updates"></a>

## <a name="recommended-updates"></a>推奨される更新プログラム

SignalR のサーバーは、次の更新プログラムがお勧めします。

- .NET Framework 4.5 の更新プログラムが利用可能な[ここ](https://support.microsoft.com/kb/2750149)します。
- Microsoft は、ASP.NET の Qfe を定期的にリリースされます。 これらは、利用可能として適用する必要があります。
