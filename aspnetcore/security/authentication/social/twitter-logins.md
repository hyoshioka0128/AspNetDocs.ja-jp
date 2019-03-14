---
title: ASP.NET Core での twitter 外部ログインのセットアップ
author: rick-anderson
description: このチュートリアルでは、既存の ASP.NET Core アプリに Twitter アカウントのユーザー認証の統合について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
uid: security/authentication/twitter-logins
ms.openlocfilehash: 49db8b921fde169380ca284f46e535786b2b8a30
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57055949"
---
# <a name="twitter-external-login-setup-with-aspnet-core"></a><span data-ttu-id="15a79-103">ASP.NET Core での twitter 外部ログインのセットアップ</span><span class="sxs-lookup"><span data-stu-id="15a79-103">Twitter external login setup with ASP.NET Core</span></span>

<span data-ttu-id="15a79-104">作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="15a79-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="15a79-105">このチュートリアルでは、ユーザーに有効にする方法[、Twitter アカウントでサインイン](https://dev.twitter.com/web/sign-in/desktop-browser)で作成されたサンプルの ASP.NET Core 2.0 プロジェクトを使用して、[前のページ](xref:security/authentication/social/index)します。</span><span class="sxs-lookup"><span data-stu-id="15a79-105">This tutorial shows you how to enable your users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 2.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="15a79-106">Twitter でアプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="15a79-106">Create the app in Twitter</span></span>

* <span data-ttu-id="15a79-107">移動します[ https://apps.twitter.com/ ](https://apps.twitter.com/)してサインインします。</span><span class="sxs-lookup"><span data-stu-id="15a79-107">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="15a79-108">Twitter アカウントがない場合は、使用、 **[今すぐサインアップ](https://twitter.com/signup)** リンクを作成します。</span><span class="sxs-lookup"><span data-stu-id="15a79-108">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span> <span data-ttu-id="15a79-109">サインインした後、**アプリケーション管理**ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="15a79-109">After signing in, the **Application Management** page is shown:</span></span>

  ![Microsoft Edge で開いている twitter アプリケーションの管理](index/_static/TwitterAppManage.png)

* <span data-ttu-id="15a79-111">タップ**Create New App**アプリケーション記入と**名前**、**説明**とパブリック**web サイト**URI (このことは一時的なものまで、ドメイン名を登録)。</span><span class="sxs-lookup"><span data-stu-id="15a79-111">Tap **Create New App** and fill out the application **Name**, **Description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

  ![アプリケーション ページを作成します。](index/_static/TwitterCreate.png)

* <span data-ttu-id="15a79-113">開発 URI を入力と`/signin-twitter`に追加されます、**有効な OAuth リダイレクト Uri**フィールド (例: `https://localhost:44320/signin-twitter`)。</span><span class="sxs-lookup"><span data-stu-id="15a79-113">Enter your development URI with `/signin-twitter` appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-twitter`).</span></span> <span data-ttu-id="15a79-114">このチュートリアルの後半で構成されている Twitter 認証の構成は自動的に要求を処理`/signin-twitter`OAuth フローを実装するルート。</span><span class="sxs-lookup"><span data-stu-id="15a79-114">The Twitter authentication scheme configured later in this tutorial will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="15a79-115">URI セグメント`/signin-twitter`Twitter の認証プロバイダーの既定のコールバックとして設定されます。</span><span class="sxs-lookup"><span data-stu-id="15a79-115">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="15a79-116">既定のコールバック URI を変更するには、継承を使用して、Twitter 認証ミドルウェアを構成するときに[RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)のプロパティ、 [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)クラス。</span><span class="sxs-lookup"><span data-stu-id="15a79-116">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="15a79-117">フォームの残りの部分を入力し、タップ**Twitter アプリケーションを作成**です。</span><span class="sxs-lookup"><span data-stu-id="15a79-117">Fill out the rest of the form and tap **Create your Twitter application**.</span></span> <span data-ttu-id="15a79-118">新しいアプリケーションの詳細が表示されます。</span><span class="sxs-lookup"><span data-stu-id="15a79-118">New application details are displayed:</span></span>

  ![[アプリケーション] ページの [詳細] タブ](index/_static/TwitterAppDetails.png)

* <span data-ttu-id="15a79-120">サイトをデプロイするときに、再アクセスする必要があります、**アプリケーション管理**ページし、新しいパブリック URI を登録します。</span><span class="sxs-lookup"><span data-stu-id="15a79-120">When deploying the site you'll need to revisit the **Application Management** page and register a new public URI.</span></span>

## <a name="storing-twitter-consumerkey-and-consumersecret"></a><span data-ttu-id="15a79-121">Twitter ConsumerKey ConsumerSecret を格納します。</span><span class="sxs-lookup"><span data-stu-id="15a79-121">Storing Twitter ConsumerKey and ConsumerSecret</span></span>

<span data-ttu-id="15a79-122">Twitter などの機密設定をリンク`Consumer Key`と`Consumer Secret`アプリケーションの構成を使用して、 [Secret Manager](xref:security/app-secrets)します。</span><span class="sxs-lookup"><span data-stu-id="15a79-122">Link sensitive settings like Twitter `Consumer Key` and `Consumer Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="15a79-123">このチュートリアルの目的で、名前トークン`Authentication:Twitter:ConsumerKey`と`Authentication:Twitter:ConsumerSecret`します。</span><span class="sxs-lookup"><span data-stu-id="15a79-123">For the purposes of this tutorial, name the tokens `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`.</span></span>

<span data-ttu-id="15a79-124">これらのトークンで確認できます、 **Keys and Access Tokens**新しい Twitter アプリケーションを作成した後タブ。</span><span class="sxs-lookup"><span data-stu-id="15a79-124">These tokens can be found on the **Keys and Access Tokens** tab after creating your new Twitter application:</span></span>

![キーとアクセス トークン タブ](index/_static/TwitterKeys.png)

## <a name="configure-twitter-authentication"></a><span data-ttu-id="15a79-126">Twitter 認証を構成します。</span><span class="sxs-lookup"><span data-stu-id="15a79-126">Configure Twitter Authentication</span></span>

<span data-ttu-id="15a79-127">このチュートリアルで使用するプロジェクト テンプレートにより[Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter)パッケージが既にインストールされています。</span><span class="sxs-lookup"><span data-stu-id="15a79-127">The project template used in this tutorial ensures that [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter) package is already installed.</span></span>

* <span data-ttu-id="15a79-128">Visual Studio 2017 では、このパッケージをインストールするには、クリックし、プロジェクトを右クリックし**NuGet パッケージの管理**します。</span><span class="sxs-lookup"><span data-stu-id="15a79-128">To install this package with Visual Studio 2017, right-click on the project and select **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="15a79-129">で .NET Core CLI をインストールするには、プロジェクト ディレクトリで、次を実行します。</span><span class="sxs-lookup"><span data-stu-id="15a79-129">To install with .NET Core CLI, execute the following in your project directory:</span></span>

  `dotnet add package Microsoft.AspNetCore.Authentication.Twitter`

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="15a79-130">Twitter サービスを追加、`ConfigureServices`メソッド*Startup.cs*ファイル。</span><span class="sxs-lookup"><span data-stu-id="15a79-130">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

```csharp
services.AddDefaultIdentity<IdentityUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();

services.AddAuthentication().AddTwitter(twitterOptions =>
{
    twitterOptions.ConsumerKey = Configuration["Authentication:Twitter:ConsumerKey"];
    twitterOptions.ConsumerSecret = Configuration["Authentication:Twitter:ConsumerSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="15a79-131">Twitter ミドルウェア内の追加、`Configure`メソッド*Startup.cs*ファイル。</span><span class="sxs-lookup"><span data-stu-id="15a79-131">Add the Twitter middleware in the `Configure` method in *Startup.cs* file:</span></span>

```csharp
app.UseTwitterAuthentication(new TwitterOptions()
{
    ConsumerKey = Configuration["Authentication:Twitter:ConsumerKey"],
    ConsumerSecret = Configuration["Authentication:Twitter:ConsumerSecret"]
});
```

::: moniker-end

<span data-ttu-id="15a79-132">参照してください、 [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) Twitter 認証でサポートされる構成オプションの詳細について、API リファレンス。</span><span class="sxs-lookup"><span data-stu-id="15a79-132">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="15a79-133">これは、ユーザーに関するさまざまな情報を要求を使用できます。</span><span class="sxs-lookup"><span data-stu-id="15a79-133">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="15a79-134">Sign in with Twitter</span><span class="sxs-lookup"><span data-stu-id="15a79-134">Sign in with Twitter</span></span>

<span data-ttu-id="15a79-135">アプリケーションを実行し、をクリックして**ログイン**します。</span><span class="sxs-lookup"><span data-stu-id="15a79-135">Run your application and click **Log in**.</span></span> <span data-ttu-id="15a79-136">Twitter でサインインするためのオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="15a79-136">An option to sign in with Twitter appears:</span></span>

![Web アプリケーション:ユーザーが認証されていません。](index/_static/DoneTwitter.png)

<span data-ttu-id="15a79-138">クリックすると**Twitter**認証の Twitter にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="15a79-138">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

![Twitter 認証ページ](index/_static/TwitterLogin.png)

<span data-ttu-id="15a79-140">Twitter の資格情報を入力した後、電子メールを設定する web サイトにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="15a79-140">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="15a79-141">これで、Twitter の資格情報を使用してをログインしています。</span><span class="sxs-lookup"><span data-stu-id="15a79-141">You are now logged in using your Twitter credentials:</span></span>

![Web アプリケーション:認証されたユーザー](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="15a79-143">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="15a79-143">Troubleshooting</span></span>

* <span data-ttu-id="15a79-144">**ASP.NET Core 2.x のみ。** ユーザーが呼び出すことによって構成されていない場合`services.AddIdentity`で`ConfigureServices`、認証を試みるが*ArgumentException:'SignInScheme' オプションを指定する必要があります*します。</span><span class="sxs-lookup"><span data-stu-id="15a79-144">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="15a79-145">このチュートリアルで使用するプロジェクト テンプレートによりこれが行われるようになります。</span><span class="sxs-lookup"><span data-stu-id="15a79-145">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="15a79-146">最初の移行を適用することで、サイト データベースが作成されていない場合になります*要求の処理中にデータベース操作が失敗しました*エラー。</span><span class="sxs-lookup"><span data-stu-id="15a79-146">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="15a79-147">タップ**適用移行**データベースを作成し、エラーを引き続き更新します。</span><span class="sxs-lookup"><span data-stu-id="15a79-147">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="15a79-148">次の手順</span><span class="sxs-lookup"><span data-stu-id="15a79-148">Next steps</span></span>

* <span data-ttu-id="15a79-149">この記事では、Twitter に認証する方法を示しました。</span><span class="sxs-lookup"><span data-stu-id="15a79-149">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="15a79-150">記載されているその他のプロバイダーで認証する同様のアプローチを利用できる、[前のページ](xref:security/authentication/social/index)します。</span><span class="sxs-lookup"><span data-stu-id="15a79-150">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="15a79-151">リセットする必要があります、web サイトを Azure web アプリを発行すると、 `ConsumerSecret` Twitter の開発者ポータルでします。</span><span class="sxs-lookup"><span data-stu-id="15a79-151">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="15a79-152">設定、`Authentication:Twitter:ConsumerKey`と`Authentication:Twitter:ConsumerSecret`として、Azure portal でアプリケーションの設定。</span><span class="sxs-lookup"><span data-stu-id="15a79-152">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="15a79-153">構成システムは、環境変数からキーの読み取りを設定します。</span><span class="sxs-lookup"><span data-stu-id="15a79-153">The configuration system is set up to read keys from environment variables.</span></span>
