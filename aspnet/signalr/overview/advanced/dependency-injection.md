---
uid: signalr/overview/advanced/dependency-injection
title: SignalR の依存関係の挿入 |Microsoft Docs
author: bradygaster
description: このトピックの「Visual Studio 2013 .NET 4.5 SignalR 使用されるソフトウェアのバージョンは以前のバージョンについてはこのトピック以前バージョンをバージョン 2.
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: a14121ae-02cf-4024-8af0-9dd0dc810690
msc.legacyurl: /signalr/overview/advanced/dependency-injection
msc.type: authoredcontent
ms.openlocfilehash: 52978b10b6c131ac8eff4535216cc60b43fdf3de
ms.sourcegitcommit: 51b01b6ff8edde57d8243e4da28c9f1e7f1962b2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65120104"
---
# <a name="dependency-injection-in-signalr"></a>SignalR の依存関係挿入

によって[Mike Wasson](https://github.com/MikeWasson)、 [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> ## <a name="software-versions-used-in-this-topic"></a>このトピックで使用されるソフトウェアのバージョン
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET 4.5
> - SignalR 2 のバージョン
>
>
>
> ## <a name="previous-versions-of-this-topic"></a>このトピックの以前のバージョン
>
> SignalR の以前のバージョンについては、次を参照してください。[以前のバージョンの SignalR](../older-versions/index.md)します。
>
> ## <a name="questions-and-comments"></a>意見やご質問
>
> このチュートリアルの良い点に関するフィードバックや、ページ下部にあるコメントで改善できる点をお知らせください。 チュートリアルに直接関係のない質問がある場合は、[ASP.NET SignalR フォーラム](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)または[StackOverflow.com](http://stackoverflow.com/)にて投稿してください。

依存関係の挿入は、簡単になります (モック オブジェクトを使用して) テストのいずれかのオブジェクトの依存関係を置換するか、実行時の動作を変更するオブジェクト間の依存関係をハードコーディングを削除する方法です。 このチュートリアルでは、SignalR ハブの依存関係の挿入を実行する方法を示します。 SignalR で IoC コンテナーを使用する方法も示します。 IoC コンテナーは、依存関係の挿入の一般的なフレームワークです。

## <a name="what-is-dependency-injection"></a>依存関係の挿入とは何ですか。

依存関係の挿入を理解している場合は、このセクションをスキップします。

*依存関係の注入*(DI) は、パターン オブジェクトが独自の依存関係の作成を担当します。 DI を顧客が簡単な例を次に示します。 メッセージを記録する必要があるオブジェクトがあるとします。 ログ記録のインターフェイスを定義する場合があります。

[!code-csharp[Main](dependency-injection/samples/sample1.cs)]

オブジェクトを作成できます、`ILogger`メッセージを記録します。

[!code-csharp[Main](dependency-injection/samples/sample2.cs)]

これは、動作しますが、最適なデザインではありません。 置換する場合`FileLogger`別`ILogger`の実装を変更する必要がある`SomeComponent`します。 多くの他のオブジェクトで使用されると`FileLogger`、それらのすべてを変更する必要があります。 作成する場合または`FileLogger`シングルトンもする必要があります、アプリケーション全体での変更を加えます。

「注入」するほうが効果的、`ILogger`オブジェクトに、たとえば、コンス トラクター引数を使用しています。

[!code-csharp[Main](dependency-injection/samples/sample3.cs)]

選択するため、オブジェクトはありませんので`ILogger`を使用します。 切り替えることができます`ILogger`それに依存するオブジェクトを変更することがなく実装します。

[!code-csharp[Main](dependency-injection/samples/sample4.cs)]

このパターンと呼ばれます[コンス トラクターの挿入](http://www.martinfowler.com/articles/injection.html#FormsOfDependencyInjection)します。 パターンの 1 つは、セッターの挿入、setter メソッドまたはプロパティから依存関係を設定します。

## <a name="simple-dependency-injection-in-signalr"></a>SignalR で単純な依存関係の挿入

チュートリアルのチャット アプリケーションを考えます[SignalR の概要](../getting-started/tutorial-getting-started-with-signalr.md)します。 そのアプリケーションからハブ クラスを次に示します。

[!code-csharp[Main](dependency-injection/samples/sample5.cs)]

チャット メッセージを送信する前にサーバーに格納するとします。 この機能は、抽象化するインターフェイスを定義してにインターフェイスを挿入する DI を使用する場合があります、`ChatHub`クラス。

[!code-csharp[Main](dependency-injection/samples/sample6.cs)]

唯一の問題は、SignalR アプリケーションがハブ; を直接作成していないことSignalR を作成します。 既定では、SignalR には、パラメーターなしのコンス トラクターを持つハブ クラスが必要です。 ただし、ハブのインスタンスを作成する関数を登録して、この関数を使用して、DI を実行することができます簡単にします。 関数を呼び出すことによって登録**GlobalHost.DependencyResolver.Register**します。

[!code-csharp[Main](dependency-injection/samples/sample7.cs)]

SignalR は、作成する必要があるたびに、この匿名関数が呼び出すので、`ChatHub`インスタンス。

## <a name="ioc-containers"></a>IoC コンテナー

前のコードは、単純なケースに適してします。 これを記述する必要があります。

[!code-csharp[Main](dependency-injection/samples/sample8.cs)]

多くの依存関係を持つ複雑なアプリケーションでは、多くの「接続」このコードを記述する必要があります。 このコードの依存関係が入れ子になった場合に特にハードを維持することはできます。 また、単体テストは困難です。

1 つのソリューションでは、IoC コンテナーを使用します。 IoC コンテナーは、依存関係の管理を担当するソフトウェア コンポーネントです。型、コンテナーを登録し、コンテナーを使用して、オブジェクトを作成します。 コンテナーは、依存関係を自動的に検出します。 多くの IoC コンテナーでは、オブジェクトの有効期間とスコープなどを制御することもできます。

> [!NOTE]
> "IoC"「の制御の反転」の略フレームワークからアプリケーション コードを呼び出す、一般的なパターンは。 IoC コンテナーを構築、オブジェクト、コントロールの通常のフローの「反転」をします。

## <a name="using-ioc-containers-in-signalr"></a>SignalR の IoC コンテナーの使用

チャット アプリケーションは、IoC コンテナーのメリットは単純すぎる可能性があります。 代わりを見てみましょう、 [StockTicker](http://nuget.org/packages/microsoft.aspnet.signalr.sample)サンプル。

StockTicker サンプルでは、2 つの主要なクラスを定義します。

- `StockTickerHub`:ハブ クラスはクライアント接続を管理します。
- `StockTicker`:株価を保持し、定期的に更新するシングルトン。

`StockTickerHub` 参照を保持、`StockTicker`シングルトン、中に`StockTicker`への参照を保持、 **IHubConnectionContext**の`StockTickerHub`します。 このインターフェイスを使用して通信を`StockTickerHub`インスタンス。 (詳細については、次を参照してください[ASP.NET SignalR によるサーバー ブロードキャスト](../getting-started/tutorial-server-broadcast-with-signalr.md)。)。

これらの依存関係を少し整理して、IoC コンテナーを使用できます。 まず、少し簡略化し、`StockTickerHub`と`StockTicker`クラス。 次のコードではコメント部分にしない必要があります。

パラメーターなしのコンス トラクターを削除`StockTickerHub`します。 代わりに、ハブを作成するのに DI を常に使用します。

[!code-csharp[Main](dependency-injection/samples/sample9.cs)]

StockTicker シングルトン インスタンスを削除します。 その後、StockTicker 有効期間を制御、IoC コンテナーを使用します。 パブリック コンス トラクターは、また、ください。

[!code-csharp[Main](dependency-injection/samples/sample10.cs?highlight=7)]

次に、インターフェイスを作成して、コードをリファクタリングできます`StockTicker`します。 このインターフェイスを使用して、分離、`StockTickerHub`から、`StockTicker`クラス。

Visual Studio でこの種のリファクタリングも簡単。 StockTicker.cs ファイルを開きを右クリックし、`StockTicker`クラス宣言、および選択**リファクタリング**.**インターフェイスの抽出**します。

![](dependency-injection/_static/image1.png)

**インターフェイスの抽出**ダイアログ ボックスで、をクリックして**すべて選択**します。 その他の既定値のままにします。 **[OK]** をクリックします。

![](dependency-injection/_static/image2.png)

Visual Studio は、という名前の新しいインターフェイスを作成します。 `IStockTicker`、し、変更も`StockTicker`から派生する`IStockTicker`します。

IStockTicker.cs ファイルを開き、インターフェイスを変更**パブリック**します。

[!code-csharp[Main](dependency-injection/samples/sample11.cs?highlight=1)]

`StockTickerHub`クラス、2 つのインスタンスを変更する`StockTicker`に`IStockTicker`:

[!code-csharp[Main](dependency-injection/samples/sample12.cs?highlight=4,6)]

作成、`IStockTicker`インターフェイスが厳密には必要はありませんが、DI、アプリケーションのコンポーネント間の結合の削減を支援する方法を表示したいです。

## <a name="add-the-ninject-library"></a>Ninject ライブラリを追加します。

.NET の多くのオープン ソース IoC コンテナーがあります。 このチュートリアルで使用します[Ninject](http://www.ninject.org/)します。 (その他の一般的なライブラリを含める[Castle Windsor](http://www.castleproject.org/)、 [Spring.Net](http://www.springframework.net/)、 [Autofac](https://code.google.com/p/autofac/)、 [Unity](https://github.com/unitycontainer/unity)、および[StructureMap](http://docs.structuremap.net).)

NuGet パッケージ マネージャーを使用して、インストール、 [Ninject ライブラリ](https://nuget.org/packages/Ninject/3.0.1.10)します。 Visual Studio から、**ツール**メニューの  **NuGet パッケージ マネージャー** > **パッケージ マネージャー コンソール**します。 パッケージ マネージャー コンソール ウィンドウで、次のコマンドを入力します。

[!code-powershell[Main](dependency-injection/samples/sample13.ps1)]

## <a name="replace-the-signalr-dependency-resolver"></a>SignalR の依存関係競合回避モジュールを置き換えます

SignalR 内 Ninject を使用するから派生したクラスを作成**DefaultDependencyResolver**します。

[!code-csharp[Main](dependency-injection/samples/sample14.cs)]

このクラスのオーバーライド、 **GetService**と**GetServices**メソッドの**DefaultDependencyResolver**します。 SignalR ハブのインスタンスの SignalR によって内部的に使用されるさまざまなサービスなど、実行時にさまざまなオブジェクトを作成するこれらのメソッドを呼び出します。

- **GetService**メソッドは、型の 1 つのインスタンスを作成します。 このメソッドを呼び出す、Ninject カーネルのオーバーライド**TryGet**メソッド。 そのメソッドが null を返す場合は、既定の競合回避モジュールに戻ります。
- **GetServices**メソッドは、指定した型のオブジェクトのコレクションを作成します。 既定のリゾルバーからの結果には、Ninject からの結果を連結するには、このメソッドをオーバーライドします。

## <a name="configure-ninject-bindings"></a>Ninject バインドを構成します。

宣言型バインディングに Ninject を使用します。

アプリケーションの Startup.cs クラスを開きます (ことか手動で作成したパッケージ」の手順に従って`readme.txt`プロジェクトに認証を追加することによって作成されたか)。 `Startup.Configuration`メソッド、Ninject 呼び出す Ninject コンテナーを作成、*カーネル*します。

[!code-csharp[Main](dependency-injection/samples/sample15.cs)]

このカスタム依存関係競合回避モジュールのインスタンスを作成します。

[!code-csharp[Main](dependency-injection/samples/sample16.cs)]

バインドを作成`IStockTicker`次のようにします。

[!code-csharp[Main](dependency-injection/samples/sample17.cs)]

このコードは次の 2 つことを示しています。 最初に、アプリケーションに必要なときに、 `IStockTicker`、カーネルがのインスタンスを作成する必要があります`StockTicker`します。 2 番目、`StockTicker`クラスはシングルトン オブジェクトとして作成する必要があります。 Ninject では、オブジェクトの 1 つのインスタンスを作成し、要求ごとに同じインスタンスを返します。

バインドを作成**IHubConnectionContext**次のようにします。

[!code-csharp[Main](dependency-injection/samples/sample18.cs)]

このコードを返す匿名関数の作成、 **IHubConnection**します。 **WhenInjectedInto**メソッドに指示を作成するときにのみ、この関数を使用する Ninject`IStockTicker`インスタンス。 理由は、SignalR が作成される**IHubConnectionContext**インスタンスを内部的には、SignalR での作成方法をオーバーライドする必要はないです。 この関数にのみ適用されます、`StockTicker`クラス。

依存関係の競合回避モジュールに渡す、 **MapSignalR**ハブ構成を追加して、メソッド。

[!code-csharp[Main](dependency-injection/samples/sample19.cs)]

新しいパラメーターを使用して、サンプルの Startup クラスで Startup.ConfigureSignalR メソッドを更新します。

[!code-csharp[Main](dependency-injection/samples/sample20.cs)]

SignalR がで指定された競合回避モジュールを使用して**MapSignalR**既定のリゾルバーではなく。

完全なコードを次に示します`Startup.Configuration`します。

[!code-csharp[Main](dependency-injection/samples/sample21.cs)]

Visual Studio で、StockTicker アプリケーションを実行するには、f5 キーを押します。 ブラウザーのウィンドウでに移動します。`http://localhost:*port*/SignalR.Sample/StockTicker.html`します。

![](dependency-injection/_static/image3.png)

アプリケーションには、正確に前に、のと同じ機能があります。 (詳細については、次を参照してください[ASP.NET SignalR によるサーバー ブロードキャスト](../getting-started/tutorial-server-broadcast-with-signalr.md)。)。動作を変更していません。テスト、保守、および進化を簡単にコードを単になりました。
