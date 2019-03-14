---
title: ASP.NET Core での Facebook 外部ログインのセットアップ
author: rick-anderson
description: このチュートリアルでは、既存の ASP.NET Core アプリに Facebook アカウントのユーザー認証の統合について説明します。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 12/18/2018
uid: security/authentication/facebook-logins
ms.openlocfilehash: 66f895c7c8dcc00d991c0ea57535f2ed56431a77
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57060299"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="a6cec-103">ASP.NET Core での Facebook 外部ログインのセットアップ</span><span class="sxs-lookup"><span data-stu-id="a6cec-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="a6cec-104">作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a6cec-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a6cec-105">このチュートリアルでは、サンプルの ASP.NET Core 2.0 プロジェクトが作成を使用して自分の Facebook アカウントでサインインするユーザーを有効にする方法、[前のページ](xref:security/authentication/social/index)します。</span><span class="sxs-lookup"><span data-stu-id="a6cec-105">This tutorial shows you how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 2.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="a6cec-106">まず Facebook アプリケーションの ID の作成を[公式手順](https://developers.facebook.com)します。</span><span class="sxs-lookup"><span data-stu-id="a6cec-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="a6cec-107">Facebook で、アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="a6cec-107">Create the app in Facebook</span></span>

* <span data-ttu-id="a6cec-108">移動し、 [Facebook Developers アプリ](https://developers.facebook.com/apps/)ページし、サインインします。</span><span class="sxs-lookup"><span data-stu-id="a6cec-108">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="a6cec-109">Facebook アカウントがない場合は、使用、 **Facebook にサインアップ**を作成するログイン ページのリンク。</span><span class="sxs-lookup"><span data-stu-id="a6cec-109">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>

* <span data-ttu-id="a6cec-110">タップして、**新しいアプリの追加**新しいアプリ ID を作成する右上隅にあるボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="a6cec-110">Tap the **Add a New App** button in the upper right corner to create a new App ID.</span></span>

   ![Microsoft Edge で開発者ポータルの Facebook を開く](index/_static/FBMyApps.png)

* <span data-ttu-id="a6cec-112">フォームに入力し、タップ、 **Create App ID**ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="a6cec-112">Fill out the form and tap the **Create App ID** button.</span></span>

  ![アプリ ID の新しいフォームを作成します。](index/_static/FBNewAppId.png)

* <span data-ttu-id="a6cec-114">**製品を選択**] ページで [**セットアップ**上、 **Facebook ログイン**カード。</span><span class="sxs-lookup"><span data-stu-id="a6cec-114">On the **Select a product** page, click **Set Up** on the **Facebook Login** card.</span></span>

  ![製品のセットアップ ページ](index/_static/FBProductSetup.png)

* <span data-ttu-id="a6cec-116">**クイック スタート**使用してウィザードを起動**プラットフォームを選択して**最初のページとして。</span><span class="sxs-lookup"><span data-stu-id="a6cec-116">The **Quickstart** wizard will launch with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="a6cec-117">ここでは、ウィザードをクリックしてバイパス、**設定**左側のメニューのリンク。</span><span class="sxs-lookup"><span data-stu-id="a6cec-117">Bypass the wizard for now by clicking the **Settings** link in the menu on the left:</span></span>

  ![スキップのクイック スタート](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="a6cec-119">表示されます、**クライアント OAuth 設定**ページ。</span><span class="sxs-lookup"><span data-stu-id="a6cec-119">You are presented with the **Client OAuth Settings** page:</span></span>

  ![クライアントの OAuth の設定 ページ](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="a6cec-121">開発 URI を入力と */signin-facebook*に追加されます、**有効な OAuth リダイレクト Uri**フィールド (例: `https://localhost:44320/signin-facebook`)。</span><span class="sxs-lookup"><span data-stu-id="a6cec-121">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="a6cec-122">このチュートリアルの後半で構成されている Facebook 認証の要求では自動的に処理 */signin-facebook* OAuth フローを実装するルート。</span><span class="sxs-lookup"><span data-stu-id="a6cec-122">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="a6cec-123">URI */signin-facebook* Facebook の認証プロバイダーの既定のコールバックとして設定されます。</span><span class="sxs-lookup"><span data-stu-id="a6cec-123">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="a6cec-124">既定のコールバック URI を変更するには、継承を使用して、Facebook の認証ミドルウェアを構成するときに[RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)のプロパティ、 [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)クラス。</span><span class="sxs-lookup"><span data-stu-id="a6cec-124">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="a6cec-125">クリックして**変更を保存**します。</span><span class="sxs-lookup"><span data-stu-id="a6cec-125">Click **Save Changes**.</span></span>

* <span data-ttu-id="a6cec-126">クリックして**設定** > **基本的な**左側のナビゲーション リンク。</span><span class="sxs-lookup"><span data-stu-id="a6cec-126">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="a6cec-127">このページで、メモしてをおきます、`App ID`と`App Secret`します。</span><span class="sxs-lookup"><span data-stu-id="a6cec-127">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="a6cec-128">次のセクションでは、両方に、ASP.NET Core アプリケーションを追加します。</span><span class="sxs-lookup"><span data-stu-id="a6cec-128">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="a6cec-129">サイトをデプロイするときに再アクセスする必要があります、 **Facebook ログイン**セットアップ ページと、新しいパブリック URI を登録します。</span><span class="sxs-lookup"><span data-stu-id="a6cec-129">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-facebook-app-id-and-app-secret"></a><span data-ttu-id="a6cec-130">Facebook アプリケーションの ID とアプリ シークレットを格納します。</span><span class="sxs-lookup"><span data-stu-id="a6cec-130">Store Facebook App ID and App Secret</span></span>

<span data-ttu-id="a6cec-131">Facebook などの機密設定をリンク`App ID`と`App Secret`アプリケーションの構成を使用して、 [Secret Manager](xref:security/app-secrets)します。</span><span class="sxs-lookup"><span data-stu-id="a6cec-131">Link sensitive settings like Facebook `App ID` and `App Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="a6cec-132">このチュートリアルの目的で、名前トークン`Authentication:Facebook:AppId`と`Authentication:Facebook:AppSecret`します。</span><span class="sxs-lookup"><span data-stu-id="a6cec-132">For the purposes of this tutorial, name the tokens `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`.</span></span>

<span data-ttu-id="a6cec-133">安全に保存するには、次のコマンドを実行`App ID`と`App Secret`シークレット マネージャーを使用します。</span><span class="sxs-lookup"><span data-stu-id="a6cec-133">Execute the following commands to securely store `App ID` and `App Secret` using Secret Manager:</span></span>

```console
dotnet user-secrets set Authentication:Facebook:AppId <app-id>
dotnet user-secrets set Authentication:Facebook:AppSecret <app-secret>
```

## <a name="configure-facebook-authentication"></a><span data-ttu-id="a6cec-134">Facebook 認証を構成します。</span><span class="sxs-lookup"><span data-stu-id="a6cec-134">Configure Facebook Authentication</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="a6cec-135">Facebook のサービスを追加、`ConfigureServices`メソッドで、 *Startup.cs*ファイル。</span><span class="sxs-lookup"><span data-stu-id="a6cec-135">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddDefaultIdentity<IdentityUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();

services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="a6cec-136">インストール、 [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook)パッケージ。</span><span class="sxs-lookup"><span data-stu-id="a6cec-136">Install the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) package.</span></span>

* <span data-ttu-id="a6cec-137">Visual Studio 2017 では、このパッケージをインストールするには、クリックし、プロジェクトを右クリックし**NuGet パッケージの管理**します。</span><span class="sxs-lookup"><span data-stu-id="a6cec-137">To install this package with Visual Studio 2017, right-click on the project and select **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="a6cec-138">で .NET Core CLI をインストールするには、プロジェクト ディレクトリで、次を実行します。</span><span class="sxs-lookup"><span data-stu-id="a6cec-138">To install with .NET Core CLI, execute the following in your project directory:</span></span>

   `dotnet add package Microsoft.AspNetCore.Authentication.Facebook`

<span data-ttu-id="a6cec-139">Facebook ミドルウェアを追加、`Configure`メソッド*Startup.cs*ファイル。</span><span class="sxs-lookup"><span data-stu-id="a6cec-139">Add the Facebook middleware in the `Configure` method in *Startup.cs* file:</span></span>

```csharp
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

::: moniker-end

<span data-ttu-id="a6cec-140">参照してください、 [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) Facebook 認証でサポートされる構成オプションの詳細について、API リファレンス。</span><span class="sxs-lookup"><span data-stu-id="a6cec-140">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="a6cec-141">構成オプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="a6cec-141">Configuration options can be used to:</span></span>

* <span data-ttu-id="a6cec-142">ユーザーに関するさまざまな情報を要求します。</span><span class="sxs-lookup"><span data-stu-id="a6cec-142">Request different information about the user.</span></span>
* <span data-ttu-id="a6cec-143">ログイン エクスペリエンスをカスタマイズするクエリ文字列引数を追加します。</span><span class="sxs-lookup"><span data-stu-id="a6cec-143">Add query string arguments to customize the login experience.</span></span>

## <a name="sign-in-with-facebook"></a><span data-ttu-id="a6cec-144">Facebook のサインイン</span><span class="sxs-lookup"><span data-stu-id="a6cec-144">Sign in with Facebook</span></span>

<span data-ttu-id="a6cec-145">アプリケーションを実行し、をクリックして**ログイン**します。</span><span class="sxs-lookup"><span data-stu-id="a6cec-145">Run your application and click **Log in**.</span></span> <span data-ttu-id="a6cec-146">Facebook でサインインするオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="a6cec-146">You see an option to sign in with Facebook.</span></span>

![Web アプリケーション:ユーザーが認証されていません。](index/_static/DoneFacebook.png)

<span data-ttu-id="a6cec-148">クリックすると**Facebook**認証に Facebook にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="a6cec-148">When you click on **Facebook**, you are redirected to Facebook for authentication:</span></span>

![Facebook 認証ページ](index/_static/FBLogin.png)

<span data-ttu-id="a6cec-150">Facebook の認証は、既定では、パブリック プロファイルと電子メール アドレスを要求します。</span><span class="sxs-lookup"><span data-stu-id="a6cec-150">Facebook authentication requests public profile and email address by default:</span></span>

![Facebook 認証ページの同意画面](index/_static/FBLoginDone.png)

<span data-ttu-id="a6cec-152">Facebook の資格情報を入力すると、電子メールを設定するサイトにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="a6cec-152">Once you enter your Facebook credentials you are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="a6cec-153">Facebook の資格情報を使用してログインしました。</span><span class="sxs-lookup"><span data-stu-id="a6cec-153">You are now logged in using your Facebook credentials:</span></span>

![Web アプリケーション:認証されたユーザー](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="a6cec-155">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="a6cec-155">Troubleshooting</span></span>

* <span data-ttu-id="a6cec-156">**ASP.NET Core 2.x のみ。** ユーザーが呼び出すことによって構成されていない場合`services.AddIdentity`で`ConfigureServices`、認証を試みるが*ArgumentException:'SignInScheme' オプションを指定する必要があります*します。</span><span class="sxs-lookup"><span data-stu-id="a6cec-156">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="a6cec-157">このチュートリアルで使用するプロジェクト テンプレートによりこれが行われるようになります。</span><span class="sxs-lookup"><span data-stu-id="a6cec-157">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="a6cec-158">取得する場合は、初期移行を適用することで、サイト データベースが作成されていない*要求の処理中にデータベース操作が失敗しました*エラー。</span><span class="sxs-lookup"><span data-stu-id="a6cec-158">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="a6cec-159">タップ**適用移行**データベースを作成し、エラーを引き続き更新します。</span><span class="sxs-lookup"><span data-stu-id="a6cec-159">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a6cec-160">次の手順</span><span class="sxs-lookup"><span data-stu-id="a6cec-160">Next steps</span></span>

* <span data-ttu-id="a6cec-161">追加、 [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook)高度な Facebook の認証シナリオには、プロジェクトに NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="a6cec-161">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to your project for advanced Facebook authentication scenarios.</span></span> <span data-ttu-id="a6cec-162">このパッケージは、アプリで Facebook 外部ログイン機能を統合する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="a6cec-162">This package isn't required to integrate Facebook external login functionality with your app.</span></span> 

* <span data-ttu-id="a6cec-163">この記事では、Facebook を認証する方法を示しました。</span><span class="sxs-lookup"><span data-stu-id="a6cec-163">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="a6cec-164">記載されているその他のプロバイダーで認証する同様のアプローチを利用できる、[前のページ](xref:security/authentication/social/index)します。</span><span class="sxs-lookup"><span data-stu-id="a6cec-164">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="a6cec-165">リセットする必要があります、web サイトを Azure web アプリを発行すると、 `AppSecret` Facebook 開発者ポータルでします。</span><span class="sxs-lookup"><span data-stu-id="a6cec-165">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="a6cec-166">設定、`Authentication:Facebook:AppId`と`Authentication:Facebook:AppSecret`として、Azure portal でアプリケーションの設定。</span><span class="sxs-lookup"><span data-stu-id="a6cec-166">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="a6cec-167">構成システムは、環境変数からキーの読み取りを設定します。</span><span class="sxs-lookup"><span data-stu-id="a6cec-167">The configuration system is set up to read keys from environment variables.</span></span>
