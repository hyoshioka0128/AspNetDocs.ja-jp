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
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a>ASP.NET Core SignalR で認証と承認

によって[Andrew Stanton-nurse](https://twitter.com/anurse)

[サンプル コードのダウンロードを表示または](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(ダウンロードする方法)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a>SignalR ハブに接続するユーザーを認証します。

SignalR で使用できる[ASP.NET Core 認証](xref:security/authentication/identity)接続ごとにユーザーを関連付ける。 ハブの認証データをからアクセスできる、 [ `HubConnectionContext.User` ](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user)プロパティ。 認証で許可されるユーザーに関連付けられているすべての接続でメソッドを呼び出すハブ (を参照してください[ユーザーと SignalR でグループ管理](xref:signalr/groups)詳細については)。 複数の接続は、1 人のユーザーを関連付けることができます。

### <a name="cookie-authentication"></a>Cookie 認証

ブラウザー ベースのアプリでは、cookie 認証は、SignalR 接続を自動的にフローする既存のユーザーの資格情報をできます。 クライアントのブラウザーを使用する場合、追加の構成は必要ありません。 アプリに、ユーザーがログインに SignalR 接続は、この認証を自動的に継承します。

Cookie とは、アクセス トークンを送信するブラウザー固有の方法が、ブラウザー以外のクライアントが送信できます。 使用する場合、 [.NET クライアント](xref:signalr/dotnet-client)、`Cookies`でプロパティを構成することができます、 `.WithUrl` cookie を提供するために呼び出し。 ただし、.NET クライアントからの cookie 認証を使用するには、クッキーの認証データを交換するための API を提供するアプリが必要です。

### <a name="bearer-token-authentication"></a>ベアラー トークン認証

クライアントは、cookie を使用する代わりに、アクセス トークンを提供できます。 サーバーは、トークンを検証し、それをユーザーの識別に使用します。 接続が確立されている場合にのみ、この検証は行われます。 接続処理中に、トークンの失効を確認するサーバーが自動的に再検証します。

使用してベアラー トークン認証を構成サーバーで、 [JWT ベアラー ミドルウェア](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)します。

JavaScript クライアントで、トークンを提供できますを使用して、 [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication)オプション。

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=63-65)]

.NET クライアントである類似した[AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication)トークンを構成するために使用できるプロパティ。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> 指定したアクセス トークンの関数は、前に呼び出されます**すべて**SignalR による HTTP 要求。 (ための接続中に、有効期限が切れる可能性があります) は、接続を維持するために、トークンを更新する必要があるかどうか、この関数内から行うし、更新されたトークンが返されます。

標準の web Api では、ベアラー トークンは、HTTP ヘッダーで送信されます。 ただし、SignalR では、一部のトランスポートを使用する場合は、ブラウザーでこれらのヘッダーを設定することはありません。 WebSockets および Server-Sent イベントを使用する場合は、トークンが、クエリ文字列パラメーターとして送信されます。 サーバーでこれをサポートするためには、追加の構成が必要です。

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

### <a name="cookies-vs-bearer-tokens"></a>ベアラー トークンと cookie 

Cookie はブラウザーに固有であるため、その他のクライアントから送信ベアラー トークンを送信すると比較して複雑さを追加します。 このため、cookie 認証は、アプリはのみ、ブラウザー クライアントからユーザーを認証する必要がない限りをお勧めしません。 ベアラー トークン認証は、クライアントのブラウザー以外のクライアントを使用する場合に推奨されるアプローチです。

### <a name="windows-authentication"></a>Windows 認証

場合[Windows 認証](xref:security/authentication/windowsauth)が構成されているアプリに、SignalR は、その id を使用して、ハブをセキュリティで保護します。 ただし、個々 のユーザーにメッセージを送信するには、カスタムのユーザーの ID プロバイダーを追加する必要があります。 Windows 認証システムは、SignalR を使用してユーザー名を確認する"Name Identifier"要求を提供しないためにです。

実装する新しいクラスを追加`IUserIdProvider`し、識別子として使用するユーザーから、要求の 1 つを取得します。 たとえば、"Name"要求を使用する (これは、フォーム内の Windows ユーザー名`[Domain]\[Username]`)、次のクラスを作成します。

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

なく`ClaimTypes.Name`、任意の値を使用することができます、 `User` (など、Windows SID 識別子など)。

> [!NOTE]
> 選択した値は、システム内のすべてのユーザーの間で一意でなければなりません。 それ以外の場合、1 人のユーザー宛てのメッセージは、別のユーザーに陥る可能性があります。

このコンポーネントの登録、`Startup.ConfigureServices`メソッド。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

設定して、.NET クライアントで Windows 認証を有効にする必要があります、 [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials)プロパティ。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

Windows 認証は、Microsoft Internet Explorer または Microsoft Edge を使用する場合にのみ、ブラウザー クライアントでサポートされます。

### <a name="use-claims-to-customize-identity-handling"></a>使用してクレーム id の処理をカスタマイズするには

ユーザーを認証するアプリでは、ユーザー クレームから SignalR ユーザー Id を派生できます。 SignalR がユーザー Id を作成する方法を指定するには、実装`IUserIdProvider`および実装を登録します。

サンプル コードでは、ユーザーの電子メール アドレスを識別するプロパティとして選択する要求を使用する方法を示します。 

> [!NOTE]
> 選択した値は、システム内のすべてのユーザーの間で一意でなければなりません。 それ以外の場合、1 人のユーザー宛てのメッセージは、別のユーザーに陥る可能性があります。

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

アカウントの登録の種類で要求を追加します`ClaimsTypes.Email`ASP.NET identity のデータベースにします。

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

このコンポーネントの登録、`Startup.ConfigureServices`します。

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a>アクセスのハブおよびハブ メソッドのユーザーを認証します。

既定では、認証されていないユーザーによってハブのすべてのメソッドを呼び出すことができます。 認証を必要とするためには、適用、 [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute)ハブに属性します。

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

コンス トラクターの引数とのプロパティを使用することができます、`[Authorize]`特定に一致する唯一のユーザーへのアクセス制限を属性[承認ポリシー](xref:security/authorization/policies)します。 たとえばと呼ばれるカスタム承認ポリシーがある場合`MyAuthorizationPolicy`そのポリシーに一致するユーザーのみが、次のコードを使用してハブにアクセスできることを確認できます。

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub: Hub
{
}
```

個々 のハブ メソッドを持つことができます、`[Authorize]`属性も適用されます。 現在のユーザーが、メソッドに適用されるポリシーに一致しない場合は、呼び出し元にエラーが返されます。

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

## <a name="additional-resources"></a>その他の技術情報

* [ASP.NET Core でベアラー トークンの認証](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
