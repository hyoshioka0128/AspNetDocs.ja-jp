---
title: ASP.NET Core SignalR で認証と承認
author: bradygaster
description: ASP.NET Core SignalR での認証と承認を使用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/31/2019
uid: signalr/authn-and-authz
ms.openlocfilehash: 5d4574775606b4354ec099b6b32e05294d9f0e45
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57043749"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a><span data-ttu-id="a8852-103">ASP.NET Core SignalR で認証と承認</span><span class="sxs-lookup"><span data-stu-id="a8852-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="a8852-104">によって[Andrew Stanton-nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="a8852-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="a8852-105">[サンプル コードのダウンロードを表示または](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(ダウンロードする方法)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="a8852-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a><span data-ttu-id="a8852-106">SignalR ハブに接続するユーザーを認証します。</span><span class="sxs-lookup"><span data-stu-id="a8852-106">Authenticate users connecting to a SignalR hub</span></span>

<span data-ttu-id="a8852-107">SignalR で使用できる[ASP.NET Core 認証](xref:security/authentication/identity)接続ごとにユーザーを関連付ける。</span><span class="sxs-lookup"><span data-stu-id="a8852-107">SignalR can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="a8852-108">ハブの認証データをからアクセスできる、 [ `HubConnectionContext.User` ](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user)プロパティ。</span><span class="sxs-lookup"><span data-stu-id="a8852-108">In a hub, authentication data can be accessed from the [`HubConnectionContext.User`](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="a8852-109">認証で許可されるユーザーに関連付けられているすべての接続でメソッドを呼び出すハブ (を参照してください[ユーザーと SignalR でグループ管理](xref:signalr/groups)詳細については)。</span><span class="sxs-lookup"><span data-stu-id="a8852-109">Authentication allows the hub to call methods on all connections associated with a user (See [Manage users and groups in SignalR](xref:signalr/groups) for more information).</span></span> <span data-ttu-id="a8852-110">複数の接続は、1 人のユーザーを関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="a8852-110">Multiple connections may be associated with a single user.</span></span>

### <a name="cookie-authentication"></a><span data-ttu-id="a8852-111">Cookie 認証</span><span class="sxs-lookup"><span data-stu-id="a8852-111">Cookie authentication</span></span>

<span data-ttu-id="a8852-112">ブラウザー ベースのアプリでは、cookie 認証は、SignalR 接続を自動的にフローする既存のユーザーの資格情報をできます。</span><span class="sxs-lookup"><span data-stu-id="a8852-112">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="a8852-113">クライアントのブラウザーを使用する場合、追加の構成は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="a8852-113">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="a8852-114">アプリに、ユーザーがログインに SignalR 接続は、この認証を自動的に継承します。</span><span class="sxs-lookup"><span data-stu-id="a8852-114">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="a8852-115">Cookie とは、アクセス トークンを送信するブラウザー固有の方法が、ブラウザー以外のクライアントが送信できます。</span><span class="sxs-lookup"><span data-stu-id="a8852-115">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="a8852-116">使用する場合、 [.NET クライアント](xref:signalr/dotnet-client)、`Cookies`でプロパティを構成することができます、 `.WithUrl` cookie を提供するために呼び出し。</span><span class="sxs-lookup"><span data-stu-id="a8852-116">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call in order to provide a cookie.</span></span> <span data-ttu-id="a8852-117">ただし、.NET クライアントからの cookie 認証を使用するには、クッキーの認証データを交換するための API を提供するアプリが必要です。</span><span class="sxs-lookup"><span data-stu-id="a8852-117">However, using cookie authentication from the .NET Client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="a8852-118">ベアラー トークン認証</span><span class="sxs-lookup"><span data-stu-id="a8852-118">Bearer token authentication</span></span>

<span data-ttu-id="a8852-119">クライアントは、cookie を使用する代わりに、アクセス トークンを提供できます。</span><span class="sxs-lookup"><span data-stu-id="a8852-119">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="a8852-120">サーバーは、トークンを検証し、それをユーザーの識別に使用します。</span><span class="sxs-lookup"><span data-stu-id="a8852-120">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="a8852-121">接続が確立されている場合にのみ、この検証は行われます。</span><span class="sxs-lookup"><span data-stu-id="a8852-121">This validation is done only when the connection is established.</span></span> <span data-ttu-id="a8852-122">接続処理中に、トークンの失効を確認するサーバーが自動的に再検証します。</span><span class="sxs-lookup"><span data-stu-id="a8852-122">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="a8852-123">使用してベアラー トークン認証を構成サーバーで、 [JWT ベアラー ミドルウェア](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)します。</span><span class="sxs-lookup"><span data-stu-id="a8852-123">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="a8852-124">JavaScript クライアントで、トークンを提供できますを使用して、 [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication)オプション。</span><span class="sxs-lookup"><span data-stu-id="a8852-124">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=63-65)]

<span data-ttu-id="a8852-125">.NET クライアントである類似した[AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication)トークンを構成するために使用できるプロパティ。</span><span class="sxs-lookup"><span data-stu-id="a8852-125">In the .NET client, there is a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="a8852-126">指定したアクセス トークンの関数は、前に呼び出されます**すべて**SignalR による HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="a8852-126">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="a8852-127">(ための接続中に、有効期限が切れる可能性があります) は、接続を維持するために、トークンを更新する必要があるかどうか、この関数内から行うし、更新されたトークンが返されます。</span><span class="sxs-lookup"><span data-stu-id="a8852-127">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="a8852-128">標準の web Api では、ベアラー トークンは、HTTP ヘッダーで送信されます。</span><span class="sxs-lookup"><span data-stu-id="a8852-128">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="a8852-129">ただし、SignalR では、一部のトランスポートを使用する場合は、ブラウザーでこれらのヘッダーを設定することはありません。</span><span class="sxs-lookup"><span data-stu-id="a8852-129">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="a8852-130">WebSockets および Server-Sent イベントを使用する場合は、トークンが、クエリ文字列パラメーターとして送信されます。</span><span class="sxs-lookup"><span data-stu-id="a8852-130">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> <span data-ttu-id="a8852-131">サーバーでこれをサポートするためには、追加の構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="a8852-131">In order to support this on the server, additional configuration is required:</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

### <a name="cookies-vs-bearer-tokens"></a><span data-ttu-id="a8852-132">ベアラー トークンと cookie</span><span class="sxs-lookup"><span data-stu-id="a8852-132">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="a8852-133">Cookie はブラウザーに固有であるため、その他のクライアントから送信ベアラー トークンを送信すると比較して複雑さを追加します。</span><span class="sxs-lookup"><span data-stu-id="a8852-133">Because cookies are specific to browsers, sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="a8852-134">このため、cookie 認証は、アプリはのみ、ブラウザー クライアントからユーザーを認証する必要がない限りをお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="a8852-134">For this reason, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="a8852-135">ベアラー トークン認証は、クライアントのブラウザー以外のクライアントを使用する場合に推奨されるアプローチです。</span><span class="sxs-lookup"><span data-stu-id="a8852-135">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="a8852-136">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="a8852-136">Windows authentication</span></span>

<span data-ttu-id="a8852-137">場合[Windows 認証](xref:security/authentication/windowsauth)が構成されているアプリに、SignalR は、その id を使用して、ハブをセキュリティで保護します。</span><span class="sxs-lookup"><span data-stu-id="a8852-137">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="a8852-138">ただし、個々 のユーザーにメッセージを送信するには、カスタムのユーザーの ID プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a8852-138">However, in order to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="a8852-139">Windows 認証システムは、SignalR を使用してユーザー名を確認する"Name Identifier"要求を提供しないためにです。</span><span class="sxs-lookup"><span data-stu-id="a8852-139">This is because the Windows authentication system doesn't provide the "Name Identifier" claim that SignalR uses to determine the user name.</span></span>

<span data-ttu-id="a8852-140">実装する新しいクラスを追加`IUserIdProvider`し、識別子として使用するユーザーから、要求の 1 つを取得します。</span><span class="sxs-lookup"><span data-stu-id="a8852-140">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="a8852-141">たとえば、"Name"要求を使用する (これは、フォーム内の Windows ユーザー名`[Domain]\[Username]`)、次のクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="a8852-141">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="a8852-142">なく`ClaimTypes.Name`、任意の値を使用することができます、 `User` (など、Windows SID 識別子など)。</span><span class="sxs-lookup"><span data-stu-id="a8852-142">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, etc.).</span></span>

> [!NOTE]
> <span data-ttu-id="a8852-143">選択した値は、システム内のすべてのユーザーの間で一意でなければなりません。</span><span class="sxs-lookup"><span data-stu-id="a8852-143">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="a8852-144">それ以外の場合、1 人のユーザー宛てのメッセージは、別のユーザーに陥る可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a8852-144">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="a8852-145">このコンポーネントの登録、`Startup.ConfigureServices`メソッド。</span><span class="sxs-lookup"><span data-stu-id="a8852-145">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="a8852-146">設定して、.NET クライアントで Windows 認証を有効にする必要があります、 [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials)プロパティ。</span><span class="sxs-lookup"><span data-stu-id="a8852-146">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="a8852-147">Windows 認証は、Microsoft Internet Explorer または Microsoft Edge を使用する場合にのみ、ブラウザー クライアントでサポートされます。</span><span class="sxs-lookup"><span data-stu-id="a8852-147">Windows Authentication is only supported by the browser client when using Microsoft Internet Explorer or Microsoft Edge.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="a8852-148">使用してクレーム id の処理をカスタマイズするには</span><span class="sxs-lookup"><span data-stu-id="a8852-148">Use claims to customize identity handling</span></span>

<span data-ttu-id="a8852-149">ユーザーを認証するアプリでは、ユーザー クレームから SignalR ユーザー Id を派生できます。</span><span class="sxs-lookup"><span data-stu-id="a8852-149">An app that authenticates users can derive SignalR user IDs from user claims.</span></span> <span data-ttu-id="a8852-150">SignalR がユーザー Id を作成する方法を指定するには、実装`IUserIdProvider`および実装を登録します。</span><span class="sxs-lookup"><span data-stu-id="a8852-150">To specify how SignalR creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="a8852-151">サンプル コードでは、ユーザーの電子メール アドレスを識別するプロパティとして選択する要求を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="a8852-151">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="a8852-152">選択した値は、システム内のすべてのユーザーの間で一意でなければなりません。</span><span class="sxs-lookup"><span data-stu-id="a8852-152">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="a8852-153">それ以外の場合、1 人のユーザー宛てのメッセージは、別のユーザーに陥る可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a8852-153">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="a8852-154">アカウントの登録の種類で要求を追加します`ClaimsTypes.Email`ASP.NET identity のデータベースにします。</span><span class="sxs-lookup"><span data-stu-id="a8852-154">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="a8852-155">このコンポーネントの登録、`Startup.ConfigureServices`します。</span><span class="sxs-lookup"><span data-stu-id="a8852-155">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="a8852-156">アクセスのハブおよびハブ メソッドのユーザーを認証します。</span><span class="sxs-lookup"><span data-stu-id="a8852-156">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="a8852-157">既定では、認証されていないユーザーによってハブのすべてのメソッドを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="a8852-157">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="a8852-158">認証を必要とするためには、適用、 [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute)ハブに属性します。</span><span class="sxs-lookup"><span data-stu-id="a8852-158">In order to require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="a8852-159">コンス トラクターの引数とのプロパティを使用することができます、`[Authorize]`特定に一致する唯一のユーザーへのアクセス制限を属性[承認ポリシー](xref:security/authorization/policies)します。</span><span class="sxs-lookup"><span data-stu-id="a8852-159">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="a8852-160">たとえばと呼ばれるカスタム承認ポリシーがある場合`MyAuthorizationPolicy`そのポリシーに一致するユーザーのみが、次のコードを使用してハブにアクセスできることを確認できます。</span><span class="sxs-lookup"><span data-stu-id="a8852-160">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub: Hub
{
}
```

<span data-ttu-id="a8852-161">個々 のハブ メソッドを持つことができます、`[Authorize]`属性も適用されます。</span><span class="sxs-lookup"><span data-stu-id="a8852-161">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="a8852-162">現在のユーザーが、メソッドに適用されるポリシーに一致しない場合は、呼び出し元にエラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="a8852-162">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class ChatHub: Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="a8852-163">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="a8852-163">Additional resources</span></span>

* [<span data-ttu-id="a8852-164">ASP.NET Core でベアラー トークンの認証</span><span class="sxs-lookup"><span data-stu-id="a8852-164">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
