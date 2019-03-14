---
title: ASP.NET Core SignalR の Java クライアント
author: mikaelm12
description: ASP.NET Core SignalR の Java クライアントを使用する方法について説明します。
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/07/2018
uid: signalr/java-client
ms.openlocfilehash: d0eff38c1f622b896ed1dc3002238aec7b6bfd38
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57030069"
---
# <a name="aspnet-core-signalr-java-client"></a><span data-ttu-id="11de7-103">ASP.NET Core SignalR の Java クライアント</span><span class="sxs-lookup"><span data-stu-id="11de7-103">ASP.NET Core SignalR Java client</span></span>

<span data-ttu-id="11de7-104">によって[Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="11de7-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="11de7-105">Java クライアントでは、Java のコードは、Android アプリを含む、ASP.NET Core SignalR サーバーに接続します。</span><span class="sxs-lookup"><span data-stu-id="11de7-105">The Java client enables connecting to an ASP.NET Core SignalR server from Java code, including Android apps.</span></span> <span data-ttu-id="11de7-106">ように、 [JavaScript クライアント](xref:signalr/javascript-client)と[.NET クライアント](xref:signalr/dotnet-client)、Java クライアントでは、リアルタイムでハブにメッセージを送受信することができます。</span><span class="sxs-lookup"><span data-stu-id="11de7-106">Like the [JavaScript client](xref:signalr/javascript-client) and the [.NET client](xref:signalr/dotnet-client), the Java client enables you to receive and send messages to a hub in real time.</span></span> <span data-ttu-id="11de7-107">Java クライアントとは、ASP.NET Core 2.2 で使用可能な以降です。</span><span class="sxs-lookup"><span data-stu-id="11de7-107">The Java client is available in ASP.NET Core 2.2 and later.</span></span>

<span data-ttu-id="11de7-108">この記事で参照されているサンプルの Java コンソール アプリでは、SignalR の Java クライアントを使用します。</span><span class="sxs-lookup"><span data-stu-id="11de7-108">The sample Java console app referenced in this article uses the SignalR Java client.</span></span>

<span data-ttu-id="11de7-109">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/java-client/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="11de7-109">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/java-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-java-client-package"></a><span data-ttu-id="11de7-110">SignalR の Java クライアント パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="11de7-110">Install the SignalR Java client package</span></span>

<span data-ttu-id="11de7-111">*Signalr 1.0.0* JAR ファイルは、SignalR ハブに接続するクライアントを使用できます。</span><span class="sxs-lookup"><span data-stu-id="11de7-111">The *signalr-1.0.0* JAR file allows clients to connect to SignalR hubs.</span></span> <span data-ttu-id="11de7-112">JAR ファイルの最新のバージョン番号を検索するには、次を参照してください。、 [Maven 検索結果](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr)します。</span><span class="sxs-lookup"><span data-stu-id="11de7-112">To find the latest JAR file version number, see the [Maven search results](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span></span>

<span data-ttu-id="11de7-113">Gradle を使用する場合に、次の行を追加、`dependencies`のセクション、 *build.gradle*ファイル。</span><span class="sxs-lookup"><span data-stu-id="11de7-113">If using Gradle, add the following line to the `dependencies` section of your *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

<span data-ttu-id="11de7-114">内の次の行を追加して Maven を使用した場合、`<dependencies>`の要素、 *pom.xml*ファイル。</span><span class="sxs-lookup"><span data-stu-id="11de7-114">If using Maven, add the following lines inside the `<dependencies>` element of your *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a><span data-ttu-id="11de7-115">ハブへの接続します。</span><span class="sxs-lookup"><span data-stu-id="11de7-115">Connect to a hub</span></span>

<span data-ttu-id="11de7-116">確立するために、 `HubConnection`、`HubConnectionBuilder`使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="11de7-116">To establish a `HubConnection`, the `HubConnectionBuilder` should be used.</span></span> <span data-ttu-id="11de7-117">接続の作成中には、ハブの URL とログ レベルを構成できます。</span><span class="sxs-lookup"><span data-stu-id="11de7-117">The hub URL and log level can be configured while building a connection.</span></span> <span data-ttu-id="11de7-118">いずれかを呼び出すことによって、必要なオプションを構成、`HubConnectionBuilder`前にメソッド`build`します。</span><span class="sxs-lookup"><span data-stu-id="11de7-118">Configure any required options by calling any of the `HubConnectionBuilder` methods before `build`.</span></span> <span data-ttu-id="11de7-119">接続を開始`start`します。</span><span class="sxs-lookup"><span data-stu-id="11de7-119">Start the connection with `start`.</span></span>

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="11de7-120">クライアントからのハブ メソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="11de7-120">Call hub methods from client</span></span>

<span data-ttu-id="11de7-121">呼び出し`send`ハブ メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="11de7-121">A call to `send` invokes a hub method.</span></span> <span data-ttu-id="11de7-122">ハブ メソッドの名前およびハブ メソッドで定義されている引数を渡す`send`します。</span><span class="sxs-lookup"><span data-stu-id="11de7-122">Pass the hub method name and any arguments defined in the hub method to `send`.</span></span>

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="11de7-123">ハブからのクライアント メソッドを呼び出す</span><span class="sxs-lookup"><span data-stu-id="11de7-123">Call client methods from hub</span></span>

<span data-ttu-id="11de7-124">使用`hubConnection.on`ハブで呼び出すことができるクライアントでメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="11de7-124">Use `hubConnection.on` to define methods on the client that the hub can call.</span></span> <span data-ttu-id="11de7-125">作成した後は、接続を開始する前に、メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="11de7-125">Define the methods after building but before starting the connection.</span></span>

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a><span data-ttu-id="11de7-126">ログ記録を追加します。</span><span class="sxs-lookup"><span data-stu-id="11de7-126">Add logging</span></span>

<span data-ttu-id="11de7-127">SignalR の Java クライアントを使用して、 [SLF4J](https://www.slf4j.org/)のログ記録ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="11de7-127">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="11de7-128">ライブラリのユーザーは、特定のログ出力の依存関係に導入することで独自の特定のログ記録の実装を選択できるようにする高度なログ記録 API になります。</span><span class="sxs-lookup"><span data-stu-id="11de7-128">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="11de7-129">次のコード スニペットは、使用する方法を示します`java.util.logging`SignalR Java クライアントを使用します。</span><span class="sxs-lookup"><span data-stu-id="11de7-129">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="11de7-130">依存関係でのログ記録を構成しない場合、SLF4J は、次の警告メッセージが既定の操作なしロガーを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="11de7-130">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="11de7-131">これは無視しても。</span><span class="sxs-lookup"><span data-stu-id="11de7-131">This can safely be ignored.</span></span>

## <a name="android-development-notes"></a><span data-ttu-id="11de7-132">Android 開発メモ</span><span class="sxs-lookup"><span data-stu-id="11de7-132">Android development notes</span></span>

<span data-ttu-id="11de7-133">SignalR クライアントの機能の Android SDK の互換性、に関して、ターゲットの Android SDK のバージョンを指定するときに、次のもの考慮してください。</span><span class="sxs-lookup"><span data-stu-id="11de7-133">With regards to Android SDK compatibility for the SignalR client features, consider the following items when specifying your target Android SDK version:</span></span>

* <span data-ttu-id="11de7-134">SignalR の Java クライアントは、Android API レベルの 16 以降に実行されます。</span><span class="sxs-lookup"><span data-stu-id="11de7-134">The SignalR Java Client will run on Android API Level 16 and later.</span></span>
* <span data-ttu-id="11de7-135">Android API レベル 20 以降が必要になります Azure SignalR サービス経由で接続するため、 [Azure SignalR サービス](/azure/azure-signalr/signalr-overview)TLS 1.2 を必要とし、SHA 1 ベースの暗号スイートをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="11de7-135">Connecting through the Azure SignalR Service will require Android API Level 20 and later because the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) requires TLS 1.2 and doesn't support SHA-1-based cipher suites.</span></span> <span data-ttu-id="11de7-136">Android[暗号スイートの SHA 256 (以降) のサポートを追加](https://developer.android.com/reference/javax/net/ssl/SSLSocket)API レベル 20 でします。</span><span class="sxs-lookup"><span data-stu-id="11de7-136">Android [added support for SHA-256 (and above) cipher suites](https://developer.android.com/reference/javax/net/ssl/SSLSocket) in API Level 20.</span></span>

## <a name="configure-bearer-token-authentication"></a><span data-ttu-id="11de7-137">ベアラー トークン認証を構成します。</span><span class="sxs-lookup"><span data-stu-id="11de7-137">Configure bearer token authentication</span></span>

<span data-ttu-id="11de7-138">「アクセス トークン ファクトリを」を提供することで、認証に使用するベアラー トークンを構成する、SignalR の Java クライアントで、 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)します。</span><span class="sxs-lookup"><span data-stu-id="11de7-138">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an "access token factory" to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="11de7-139">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を提供する、 [RxJava](https://github.com/ReactiveX/RxJava) [単一<String>](http://reactivex.io/documentation/single.html)します。</span><span class="sxs-lookup"><span data-stu-id="11de7-139">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single<String>](http://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="11de7-140">呼び出して[Single.defer](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)クライアントのアクセス トークンを生成するロジックを記述することができます。</span><span class="sxs-lookup"><span data-stu-id="11de7-140">With a call to [Single.defer](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a><span data-ttu-id="11de7-141">既知の制限事項</span><span class="sxs-lookup"><span data-stu-id="11de7-141">Known limitations</span></span>

* <span data-ttu-id="11de7-142">JSON プロトコルのみがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="11de7-142">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="11de7-143">Websocket トランスポートのみがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="11de7-143">Only the WebSockets transport is supported.</span></span>
* <span data-ttu-id="11de7-144">ストリーミングはまだサポートされません。</span><span class="sxs-lookup"><span data-stu-id="11de7-144">Streaming isn't supported yet.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="11de7-145">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="11de7-145">Additional resources</span></span>

* [<span data-ttu-id="11de7-146">Java API リファレンス</span><span class="sxs-lookup"><span data-stu-id="11de7-146">Java API reference</span></span>](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
