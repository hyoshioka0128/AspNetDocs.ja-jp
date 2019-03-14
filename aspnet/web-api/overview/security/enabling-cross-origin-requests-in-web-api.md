---
uid: web-api/overview/security/enabling-cross-origin-requests-in-web-api
title: ASP.NET Web API 2 でクロスオリジン要求の有効化 |Microsoft Docs
author: MikeWasson
description: ASP.NET Web API でクロス オリジン リソース共有 (CORS) をサポートする方法を示しています。
ms.author: riande
ms.date: 01/29/2019
ms.assetid: 9b265a5a-6a70-4a82-adce-2d7c56ae8bdd
msc.legacyurl: /web-api/overview/security/enabling-cross-origin-requests-in-web-api
msc.type: authoredcontent
ms.openlocfilehash: 97a0027194b019b09e220493dcb593e682027fe3
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57046009"
---
<a name="enable-cross-origin-requests-in-aspnet-web-api-2"></a>ASP.NET Web API 2 でのクロス オリジン要求を有効にします。
====================
作成者[Mike Wasson](https://github.com/MikeWasson)

> ブラウザーのセキュリティは、Web ページが別のドメインに AJAX 要求を行うことを防止します。 この制限は*同一生成元ポリシー*と呼ばれ、悪意のあるサイトが別のサイトから機密データを読み取れないようにします。 ただし、場合がありますたい他のサイト、web API の呼び出しを使用します。
>
> [クロス オリジン リソース共有](http://www.w3.org/TR/cors/)(CORS) は、サーバーに同一生成元ポリシーの制限を緩和させる W3C 標準の１つです。 CORS を使用することによって、不明なリクエストは拒否しながら、一部のクロス オリジン要求のみを明示的に許可できるようになります。 CORS は [JSONP](http://en.wikipedia.org/wiki/JSONP) のようなかつての技術より安全でフレキシブルなものです。 このチュートリアルでは、Web API アプリケーションで CORS を有効にする方法を示します。
>
> ## <a name="software-used-in-the-tutorial"></a>このチュートリアルで使用されるソフトウェア
>
> - [Visual Studio](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)
> - Web API 2.2

## <a name="introduction"></a>はじめに

このチュートリアルでは、ASP.NET Web API における CORS のサポートについて説明します。 1 つと呼ばれる"WebService"、Web API コント ローラーをホストして、その他の呼び出された"WebClient"、web サービスを呼び出す – 2 つの ASP.NET プロジェクトの作成から始めます。 2 つのアプリケーションが別のドメインでホストされているため、WebClient から web サービスへの AJAX 要求は、クロス オリジン要求です。

![](enabling-cross-origin-requests-in-web-api/_static/image1.png)

### <a name="what-is-same-origin"></a>「同一生成元」とは

2 つの URL のスキーム、ホスト、ポートが同じである場合、その URL は同一生成元となります。 ([RFC 6454](http://tools.ietf.org/html/rfc6454))

次の 2 つの URL は生成元が同じです。

- `http://example.com/foo.html`
- `http://example.com/bar.html`

次の URL は、上の URL とは生成元が異なります。

- `http://example.net` - 異なるドメイン 
- `http://example.com:9000/foo.html` - 異なるポート
- `https://example.com/foo.html` - 異なるスキーム
- `http://www.example.com/foo.html` - 異なるサブドメイン

> [!NOTE]
> Internet Explorer では、配信元を比較するときに、ポートは考慮されません。

## <a name="create-the-webservice-project"></a>Web サービス プロジェクトを作成します。

> [!NOTE]
> このセクションでは、Web API プロジェクトを作成する方法を知ってを前提としています。 そうでない場合は、次を参照してください。 [ASP.NET Web API の概要](../getting-started-with-aspnet-web-api/tutorial-your-first-web-api.md)します。

1. Visual Studio を起動し、新しい作成**ASP.NET Web アプリケーション (.NET Framework)** プロジェクト。
2. **新しい ASP.NET Web アプリケーション**ダイアログ ボックスで、**空**プロジェクト テンプレート。 **フォルダーを追加し、参照用のコア**を選択、 **Web API**チェック ボックスをオンします。

   ![Visual Studio で新しい ASP.NET プロジェクト ダイアログ](enabling-cross-origin-requests-in-web-api/_static/new-web-app-dialog.png)

3. という名前の Web API コント ローラーを追加`TestController`を次のコード。

   [!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample1.cs)]

4. ローカル アプリケーションを実行したり、Azure にデプロイできます。 (このチュートリアルではスクリーン ショットは、アプリにデプロイされます Azure App Service Web Apps。)Web API が動作していることを確認するに移動します。`http://hostname/api/test/`ここで、*ホスト名*はアプリケーションをデプロイしたドメインです。 応答テキスト、&quot;を取得します。テスト メッセージ&quot;します。

   ![Web ブラウザーが表示されたテスト メッセージ](enabling-cross-origin-requests-in-web-api/_static/image4.png)

## <a name="create-the-webclient-project"></a>WebClient のプロジェクトを作成します。

1. 別の作成**ASP.NET Web アプリケーション (.NET Framework)** 順に選択して、 **MVC**プロジェクト テンプレート。 必要に応じて、**認証の変更** > **認証なし**します。 このチュートリアルでは、認証が不要です。

   ![Visual Studio で新しい ASP.NET プロジェクト ダイアログ ボックスで MVC テンプレート](enabling-cross-origin-requests-in-web-api/_static/new-web-app-dialog-mvc.png)

2. **ソリューション エクスプ ローラー**、ファイルを開く*Views/Home/Index.cshtml*します。 次のようにこのファイル内のコードに置き換えます。

   [!code-cshtml[Main](enabling-cross-origin-requests-in-web-api/samples/sample2.cshtml?highlight=13)]

   *ServiceUrl*変数、web サービス アプリケーションの URI を使用します。

3. WebClient のアプリをローカルで実行または別の web サイトに発行します。

表示されている HTTP メソッドを使用して web サービス アプリケーションに AJAX 要求が送信される、試してみる ボタンをクリックすると (GET、POST、または PUT) ボックスの一覧。 これにより、別のクロス オリジン要求を調べることができます。 現時点では、ボタンをクリックした場合、エラーが表示されますので、web サービス アプリには、CORS はできません。

![ブラウザーで '試して' エラー](enabling-cross-origin-requests-in-web-api/_static/image7.png)

> [!NOTE]
> などのツールで HTTP トラフィックを見る場合[Fiddler](https://www.telerik.com/fiddler)ブラウザーは、GET 要求を送信し、要求が成功するしますが、AJAX 呼び出しには、エラーが返されますことを確認します。 同一オリジン ポリシーが、ブラウザーからできないことを理解することが重要*送信*要求。 代わりに、アプリケーションが表示されるを防止、*応答*します。

![Fiddler web デバッガーが web 要求を表示](enabling-cross-origin-requests-in-web-api/_static/image8.png)

## <a name="enable-cors"></a>CORS を有効にします。

これで web サービス アプリで CORS を有効にしてみましょう。 最初に、CORS の NuGet パッケージを追加します。 Visual Studio から、**ツール**メニューの  **NuGet パッケージ マネージャー**を選択し、**パッケージ マネージャー コンソール**します。 パッケージ マネージャー コンソール ウィンドウで、次のコマンドを入力します。

[!code-powershell[Main](enabling-cross-origin-requests-in-web-api/samples/sample3.ps1)]

このコマンドは、最新のパッケージをインストールし、core Web API のライブラリを含む、すべての依存関係を更新します。 使用して、`-Version`特定のバージョンを対象とするフラグ。 CORS のパッケージには、Web API 2.0 以降が必要です。

ファイルを開く*アプリ\_Start/WebApiConfig.cs*します。 次のコードを追加、 **WebApiConfig.Register**メソッド。

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample4.cs?highlight=9)]

次に、追加、 **EnableCors**属性を`TestController`クラス。

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample5.cs?highlight=3,7)]

*オリジン*パラメーター、WebClient のアプリケーションをデプロイした URI を使用します。 これにより、WebClient から引き続き他のすべてのクロス ドメイン要求を許可中に、クロス オリジン要求ができます。 パラメーターを後で説明します**EnableCors**で詳しく説明します。

末尾にスラッシュを含めないでください、*オリジン*URL。

更新された web サービス アプリケーションを再デプロイします。 WebClient を更新する必要はありません。 今すぐ WebClient から AJAX 要求は成功する必要があります。 GET、PUT、POST メソッドはすべて許可します。

![Web ブラウザーが表示されたテストが成功したメッセージ](enabling-cross-origin-requests-in-web-api/_static/image9.png)

## <a name="how-cors-works"></a>CORS のしくみ

このセクションでは、HTTP メッセージのレベルでの CORS 要求での動作について説明します。 構成できるようにするために、CORS のしくみを理解することが重要、 **EnableCors**正しく属性し、期待どおりに動作しなかった場合のトラブルシューティングを行います。

CORS の仕様には、クロス オリジン要求を有効にするいくつかの新しい HTTP ヘッダーが導入されています。 ブラウザーでは、CORS をサポートする場合はクロス オリジン要求を自動的にこれらのヘッダーに設定します。JavaScript コードで特別な処理は必要ありません。

クロス オリジン要求の例を次に示します。 "Origin"ヘッダーは、要求を行っているサイトのドメインを示します。

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample6.cmd?highlight=5)]

サーバーは、要求を許可している場合は、アクセス制御の許可-オリジン ヘッダーを設定します。 このヘッダーの値は配信元のヘッダーと一致するか、ワイルドカード値は、"\*"、任意のオリジンを許可することを意味します。

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample7.cmd?highlight=5)]

応答に、アクセス制御の許可-オリジン ヘッダーが含まれていない場合、AJAX 要求は失敗します。 具体的には、ブラウザーには、要求が許可されていません。 サーバーに正常な応答が返される場合でも、ブラウザーは行いません応答クライアント アプリケーションで使用できます。

**プレフライト要求**

一部の CORS 要求では、ブラウザーは、リソースの実際の要求を送信する前に「プレフライト要求を」と呼ばれる追加の要求を送信します。

次の条件に該当する場合、ブラウザーでプレフライト要求をスキップできます。

- 要求メソッドが GET、HEAD、または POST、*と*
- アプリケーションが承諾、Accept-language、Content-language 以外のすべての要求ヘッダーを設定していないコンテンツの種類、または最後のイベント ID、*と*
- Content-type ヘッダー (場合設定) は、次の 1 つです。

    - application/x-www-form-urlencoded
    - マルチパート/フォーム データ
    - テキスト/プレーン

アプリケーションで呼び出すことによって設定されたヘッダーを要求ヘッダーについて規則が適用される**setRequestHeader**上、 **XMLHttpRequest**オブジェクト。 (CORS の仕様は、これら「作成者要求ヘッダー」を呼び出します)。このルールは、ヘッダーには適用されません、*ブラウザー*ユーザー エージェント、ホスト、またはコンテンツの長さなど、設定できます。

プレフライト要求の例を次に示します。

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample8.cmd?highlight=4-5)]

事前要求は HTTP OPTIONS メソッドを使用します。 2 つの特殊なヘッダーが含まれています。

- アクセス制御の要求メソッド:実際の要求に使用される HTTP メソッド。
- アクセス制御の要求ヘッダー。要求ヘッダーの一覧を*アプリケーション*実際の要求に設定します。 (ここでも、これは含まれません、ブラウザーを設定するヘッダー。)

サーバーが要求を許可すると仮定すると、例応答を次に示します。

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample9.cmd?highlight=6-7)]

応答には、許可されているメソッドを一覧表示するアクセスの制御-許可する-メソッド ヘッダーと、必要に応じて、アクセスの制御-許可する-ヘッダー ヘッダー、許可されたヘッダーの一覧を表示するが含まれます。 プレフライト要求が成功すると、ブラウザーは、前述のように、実際の要求を送信します。

プレフライト OPTIONS 要求でエンドポイントをテストによく使用されるツール (たとえば、 [Fiddler](https://www.telerik.com/fiddler)と[Postman](https://www.getpostman.com/)) 既定では、必要なオプションのヘッダーを送信しません。 いることを確認、`Access-Control-Request-Method`と`Access-Control-Request-Headers`ヘッダーが要求と共に送信されると、オプションのヘッダーが IIS を使用してアプリにアクセスします。

ASP.NET アプリを受信し、オプションの要求を処理できるように IIS を構成するには、アプリの次の構成を追加*web.config*ファイル、`<system.webServer><handlers>`セクション。

```xml
<system.webServer>
  <handlers>
    <remove name="ExtensionlessUrlHandler-Integrated-4.0" />
    <remove name="OPTIONSVerbHandler" />
    <add name="ExtensionlessUrlHandler-Integrated-4.0" path="*." verb="*" type="System.Web.Handlers.TransferRequestHandler" preCondition="integratedMode,runtimeVersionv4.0" />
  </handlers>
</system.webServer>
```

削除`OPTIONSVerbHandler`IIS が OPTIONS 要求を処理するを防ぎます。 置換`ExtensionlessUrlHandler-Integrated-4.0`の既定のモジュールの登録は拡張子のない Url に GET、HEAD、POST、およびデバッグの要求のみを許可するため、アプリに到達するオプションの要求を許可します。

## <a name="scope-rules-for-enablecors"></a>EnableCors のスコープ規則

アクション、コント ローラーごと、またはすべての Web API コント ローラーをグローバルにあたり、アプリケーションで CORS を有効にできます。

**アクションごと**

1 つのアクションで CORS を有効にするには設定、 **EnableCors**アクション メソッドの属性。 次の例での CORS、`GetItem`メソッドのみです。

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample10.cs)]

**コント ローラーごと**

設定した場合**EnableCors**コント ローラー クラスで、コント ローラーのすべてのアクションに適用されます。 アクションに対して CORS を無効にする追加の**DisableCors**属性をアクションにします。 次の例では、CORS を有効を除くすべてのメソッドに対して`PutItem`します。

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample11.cs)]

**グローバルに**

アプリケーション内のすべての Web API コント ローラーで CORS を有効にするのには、渡す、 **EnableCorsAttribute**インスタンスを**EnableCors**メソッド。

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample12.cs)]

1 つ以上のスコープで、属性を設定した場合の優先順位には。

1. アクション
2. コントローラー
3. Global

## <a name="set-the-allowed-origins"></a>許可されるオリジンを設定します。

*オリジン*のパラメーター、 **EnableCors**属性は、リソースのアクセスを許可するオリジンを指定します。 値が、許可されたオリジンのコンマ区切り一覧です。

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample13.cs)]

ワイルドカード値を使用することもできます。"\*"任意のオリジンからの要求を許可します。

任意のオリジンからの要求を許可する前に慎重に検討してください。 あらゆる web サイトが web API への AJAX 呼び出しを実行できることを意味します。

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample14.cs)]

## <a name="set-the-allowed-http-methods"></a>許可される HTTP メソッドを設定します。

*メソッド*のパラメーター、 **EnableCors**属性は、どの HTTP メソッドは、リソースへのアクセス許可を指定します。 すべてのメソッドを許可するワイルドカード値を使用して、"\*"。 次の例では、GET と POST の要求のみを許可します。

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample15.cs)]

## <a name="set-the-allowed-request-headers"></a>許可されている要求ヘッダーを設定します。

この記事では、プレフライト要求がアプリケーションで設定される HTTP ヘッダーの一覧を表示するアクセス制御の要求ヘッダー ヘッダーを含めることがあります以前方法を説明 (いわゆる"author 要求ヘッダー")。 *ヘッダー*のパラメーター、 **EnableCors**属性を指定する作成者の要求ヘッダーが許可されます。 すべてのヘッダーは、次のように設定します。*ヘッダー*に"\*"。 ホワイト リストの特定のヘッダーを次のように設定します。*ヘッダー*が許可されるヘッダーのコンマ区切りのリストに。

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample16.cs)]

ただし、ブラウザーでは、このアクセス制御の要求ヘッダーを設定する方法で完全に一貫性がありません。 たとえば、Chrome では、"origin"現在含まれています。 FireFox では、スクリプトでアプリケーションを設定している場合でも、「受け入れ」などの標準ヘッダーは含まれません。

設定した場合*ヘッダー*以外の値を"\*"を含める必要がある、少なくとも"accept"、「コンテンツの種類」と"origin"、およびサポートするカスタム ヘッダー。

## <a name="set-the-allowed-response-headers"></a>許可される応答ヘッダーを設定します。

既定では、ブラウザーは公開されませんすべてのアプリケーションに応答ヘッダー。 既定で使用できる応答ヘッダーは次のとおりです。

- キャッシュ制御
- コンテンツの言語
- Content-Type
- 有効期限が切れます
- Last-Modified
- プラグマ

CORS の仕様を呼び出す[単純な応答ヘッダー](https://dvcs.w3.org/hg/cors/raw-file/tip/Overview.html#simple-response-header)します。 で他のヘッダーをアプリケーションに使用できるようにするには設定、 *exposedHeaders*パラメーターの**EnableCors**します。

次の例で、コント ローラーの`Get`メソッドが 'X カスタム ヘッダー' という名前のカスタム ヘッダーを設定します。 既定では、ブラウザーでは、クロス オリジン要求では、このヘッダーは公開しません。 ヘッダーを使用できるようにするに 'X カスタム ヘッダー' を含める*exposedHeaders*します。

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample17.cs)]

## <a name="pass-credentials-in-cross-origin-requests"></a>クロス オリジン要求で資格情報を渡す

資格情報では、CORS 要求で特別な処理が必要です。 既定では、ブラウザーは、クロス オリジン要求と共に資格情報を送信しません。 資格情報には、cookie として HTTP 認証方式がなどがあります。 クロス オリジン要求に資格情報を送信するクライアントを設定する必要があります**XMLHttpRequest.withCredentials**を true にします。

使用して**XMLHttpRequest**直接。

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample18.cs)]

Jquery では。

[!code-javascript[Main](enabling-cross-origin-requests-in-web-api/samples/sample19.js)]

さらに、サーバーは、資格情報を許可する必要があります。 Web API でクロス オリジンの資格情報をできるように、設定、 **SupportsCredentials**プロパティを true に、 **EnableCors**属性。

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample20.cs)]

このプロパティが true の場合、HTTP 応答には、アクセス コントロール-許可する-資格情報のヘッダーが含まれます。 このヘッダーは、サーバーは、クロス オリジン要求の資格情報をブラウザーに指示します。

ブラウザーが資格情報を送信、応答が有効なアクセス制御を許可する-資格情報のヘッダーに含まれない場合は、ブラウザーは、アプリケーションへの応答を公開しないと、AJAX 要求は失敗します。

設定に関する注意**SupportsCredentials**を true に別のドメインに web サイトは、ユーザーを認識することがなく、ユーザーの代わりに、Web API へのログイン ユーザーの資格情報を送信することができますが行われるためです。 CORS の仕様もその設定を示す*オリジン*に&quot; \* &quot;有効でない場合**SupportsCredentials**が true。

## <a name="custom-cors-policy-providers"></a>カスタムの CORS ポリシー プロバイダー

**EnableCors**実装の属性、 **ICorsPolicyProvider**インターフェイス。 派生したクラスを作成して、独自の実装を行うことができます**属性**実装と**ICorsProlicyProvider**します。

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample21.cs)]

任意の場所にする場合は、属性を適用するようになりました**EnableCors**します。

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample22.cs)]

たとえば、カスタム CORS ポリシー プロバイダーは、構成ファイルから設定を読み取る可能性があります。

属性を使用する代わりに、登録することができます、 **ICorsPolicyProviderFactory**オブジェクトを作成する**ICorsPolicyProvider**オブジェクト。

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample23.cs)]

設定する、 **ICorsPolicyProviderFactory**を呼び出し、 **SetCorsPolicyProviderFactory**起動時に次のように、拡張メソッド。

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample24.cs)]

## <a name="browser-support"></a>ブラウザーのサポート

Web API CORS パッケージは、サーバー側テクノロジです。 ユーザーのブラウザーが CORS をサポートするためにも必要です。 幸いにも、すべての主要なブラウザーの現在のバージョンを含める[cors サポート](http://caniuse.com/cors)します。
