---
title: Visual Studio for ASP.NET Core の開発時 IIS サポート
author: shirhatti
description: ASP.NET Core アプリが Windows Server の IIS の背後で実行されている場合に、そのデバッグのサポートを検出します。
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2018
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: 44570bb28451ce4c5fde12ec77e3856fb5bd3062
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57055249"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="0654b-103">Visual Studio for ASP.NET Core の開発時 IIS サポート</span><span class="sxs-lookup"><span data-stu-id="0654b-103">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="0654b-104">著者: [Sourabh Shirhatti](https://twitter.com/sshirhatti)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0654b-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0654b-105">この記事では、 Windows Server の IIS の背後で実行されている ASP.NET Core アプリをデバッグするための、[Visual Studio](https://www.visualstudio.com/vs/) のサポートについて説明します。</span><span class="sxs-lookup"><span data-stu-id="0654b-105">This article describes [Visual Studio](https://www.visualstudio.com/vs/) support for debugging ASP.NET Core apps running behind IIS on Windows Server.</span></span> <span data-ttu-id="0654b-106">このトピックでは、手順に従ってこの機能を有効にし、プロジェクトを設定します。</span><span class="sxs-lookup"><span data-stu-id="0654b-106">This topic walks through enabling this feature and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0654b-107">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="0654b-107">Prerequisites</span></span>

* <span data-ttu-id="0654b-108">[Visual Studio (Windows 版)](https://www.microsoft.com/net/download/windows)。</span><span class="sxs-lookup"><span data-stu-id="0654b-108">[Visual Studio for Windows](https://www.microsoft.com/net/download/windows)</span></span>
* <span data-ttu-id="0654b-109">**ASP.NET および Web の開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="0654b-109">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="0654b-110">**.NET Core クロスプラットフォームの開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="0654b-110">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="0654b-111">X.509 セキュリティ証明書</span><span class="sxs-lookup"><span data-stu-id="0654b-111">X.509 security certificate</span></span>

## <a name="enable-iis"></a><span data-ttu-id="0654b-112">IIS を有効にする</span><span class="sxs-lookup"><span data-stu-id="0654b-112">Enable IIS</span></span>

1. <span data-ttu-id="0654b-113">**[コントロール パネル]** > **[プログラム]** > **[プログラムと機能]** > **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="0654b-113">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="0654b-114">**[インターネット インフォメーション サービス]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="0654b-114">Select the **Internet Information Services** check box.</span></span>

![[インターネット インフォメーション サービス] チェック ボックスが黒い四角 (チェックマークではない) で選択され、一部の IIS 機能が有効であることが表示されている [Windows の機能]](development-time-iis-support/_static/enable_iis.png)

<span data-ttu-id="0654b-116">IIS のインストールには、システムの再起動が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="0654b-116">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="0654b-117">IIS の構成</span><span class="sxs-lookup"><span data-stu-id="0654b-117">Configure IIS</span></span>

<span data-ttu-id="0654b-118">IIS には、次のように構成された Web サイトが含まれている必要があります。</span><span class="sxs-lookup"><span data-stu-id="0654b-118">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="0654b-119">アプリの起動プロファイルの URL ホスト名と一致するホスト名。</span><span class="sxs-lookup"><span data-stu-id="0654b-119">A host name that matches the app's launch profile URL host name.</span></span>
* <span data-ttu-id="0654b-120">割り当てられた証明書でのポート 443 のバインド。</span><span class="sxs-lookup"><span data-stu-id="0654b-120">Binding for port 443 with an assigned certificate.</span></span>

<span data-ttu-id="0654b-121">たとえば、追加された Web サイトの **[ホスト名]** は "localhost" に設定されます (このトピックの後半では、起動プロファイルも "localhost" を使用します)。</span><span class="sxs-lookup"><span data-stu-id="0654b-121">For example, the **Host name** for an added website is set to "localhost" (the launch profile will also use "localhost" later in this topic).</span></span> <span data-ttu-id="0654b-122">ポートは "443" (HTTPS) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="0654b-122">The port is set to "443" (HTTPS).</span></span> <span data-ttu-id="0654b-123">**IIS Express Development Certificate** が Web サイトに割り当てられていますが、すべての有効な証明書を使用できます。</span><span class="sxs-lookup"><span data-stu-id="0654b-123">The **IIS Express Development Certificate** is assigned to the website, but any valid certificate works:</span></span>

![証明書が割り当てられた、ポート 443 での localhost に対するバインド セットを表示する、IIS の [Web サイトの追加] ウィンドウ](development-time-iis-support/_static/add-website-window.png)

<span data-ttu-id="0654b-125">ホスト名がアプリの起動プロファイルの URL ホスト名と一致する **[既定の Web サイト]** が、IIS のインストールに既に含まれている場合は、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="0654b-125">If the IIS installation already has a **Default Web Site** with a host name that matches the app's launch profile URL host name:</span></span>

* <span data-ttu-id="0654b-126">ポート 443 (HTTPS) に対するポートのバインドを追加する。</span><span class="sxs-lookup"><span data-stu-id="0654b-126">Add a port binding for port 443 (HTTPS).</span></span>
* <span data-ttu-id="0654b-127">Web サイトに有効な証明書を割り当てる。</span><span class="sxs-lookup"><span data-stu-id="0654b-127">Assign a valid certificate to the website.</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="0654b-128">Visual Studio の開発時 IIS サポートを有効にする</span><span class="sxs-lookup"><span data-stu-id="0654b-128">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="0654b-129">Visual Studio インストーラーを起動します。</span><span class="sxs-lookup"><span data-stu-id="0654b-129">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="0654b-130">**[開発時 IIS サポート]** コンポーネントを選択します。</span><span class="sxs-lookup"><span data-stu-id="0654b-130">Select the **Development time IIS support** component.</span></span> <span data-ttu-id="0654b-131">**[ASP.NET と Web 開発]** ワークロードの **[概要]** パネルに、このコンポーネントがオプションとして表示されます。</span><span class="sxs-lookup"><span data-stu-id="0654b-131">The component is listed as optional in the **Summary** panel for the **ASP.NET and web development** workload.</span></span> <span data-ttu-id="0654b-132">このコンポーネントにより、IIS を使用した ASP.NET Core アプリの実行に必要なネイティブの IIS モジュールである [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="0654b-132">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

![Visual Studio の機能を変更する: [ワークロード] タブが選択されます。](development-time-iis-support/_static/development_time_support.png)

## <a name="configure-the-project"></a><span data-ttu-id="0654b-136">プロジェクトを構成する</span><span class="sxs-lookup"><span data-stu-id="0654b-136">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="0654b-137">HTTPS リダイレクト</span><span class="sxs-lookup"><span data-stu-id="0654b-137">HTTPS redirection</span></span>

<span data-ttu-id="0654b-138">新しいプロジェクトの場合、**[新しい ASP.NET Core Web アプリケーション]** ウィンドウで、**[Configure for HTTPS]\(HTTPS 用に構成する\)** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="0654b-138">For a new project, select the check box to **Configure for HTTPS** in the **New ASP.NET Core Web Application** window:</span></span>

![HTTPS 用に構成するチェック ボックスが選択された新しい ASP.NET Core Web アプリケーション ウィンドウ](development-time-iis-support/_static/new-app.png)

<span data-ttu-id="0654b-140">既存のプロジェクトで、[UseHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpspolicybuilderextensions.usehttpsredirection) 拡張メソッドを呼び出すことで、`Startup.Configure` の HTTPS リダイレクト ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="0654b-140">In an existing project, use HTTPS Redirection Middleware in `Startup.Configure` by calling the [UseHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpspolicybuilderextensions.usehttpsredirection) extension method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();

    app.UseMvc();
}
```

### <a name="iis-launch-profile"></a><span data-ttu-id="0654b-141">IIS 起動プロファイル</span><span class="sxs-lookup"><span data-stu-id="0654b-141">IIS launch profile</span></span>

<span data-ttu-id="0654b-142">新しい起動プロファイルを作成して、開発時 IIS サポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="0654b-142">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="0654b-143">**[プロファイル]** で、**[新規]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="0654b-143">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="0654b-144">ポップアップ ウィンドウで、プロファイルに "IIS" という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="0654b-144">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="0654b-145">**[OK]** をクリックして、プロファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="0654b-145">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="0654b-146">**[起動]** の設定で、一覧から **[IIS]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0654b-146">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="0654b-147">**[ブラウザーの起動]** チェック ボックスをオンにして、エンドポイント URL を指定します。</span><span class="sxs-lookup"><span data-stu-id="0654b-147">Select the check box for **Launch browser** and provide the endpoint URL.</span></span> <span data-ttu-id="0654b-148">HTTPS プロトコルを使用します。</span><span class="sxs-lookup"><span data-stu-id="0654b-148">Use the HTTPS protocol.</span></span> <span data-ttu-id="0654b-149">この例では `https://localhost/WebApplication1` を使用します。</span><span class="sxs-lookup"><span data-stu-id="0654b-149">This example uses `https://localhost/WebApplication1`.</span></span>
1. <span data-ttu-id="0654b-150">**[環境変数]** セクションで、**[追加]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="0654b-150">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="0654b-151">キーを `ASPNETCORE_ENVIRONMENT`、値を `Development` として環境変数を指定します。</span><span class="sxs-lookup"><span data-stu-id="0654b-151">Provide an environment variable with a key of `ASPNETCORE_ENVIRONMENT` and a value of `Development`.</span></span>
1. <span data-ttu-id="0654b-152">**[Web サーバー設定]** で **[アプリ URL]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="0654b-152">In the **Web Server Settings** area, set the **App URL**.</span></span> <span data-ttu-id="0654b-153">この例では `https://localhost/WebApplication1` を使用します。</span><span class="sxs-lookup"><span data-stu-id="0654b-153">This example uses `https://localhost/WebApplication1`.</span></span>
1. <span data-ttu-id="0654b-154">プロファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="0654b-154">Save the profile.</span></span>

![[デバッグ] タブが選択された、プロジェクト プロパティのウィンドウ。](development-time-iis-support/_static/project_properties.png)

<span data-ttu-id="0654b-159">または、起動プロファイルをアプリの [launchSettings.json](http://json.schemastore.org/launchsettings) ファイルに手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="0654b-159">Alternatively, manually add a launch profile to the [launchSettings.json](http://json.schemastore.org/launchsettings) file in the app:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

## <a name="run-the-project"></a><span data-ttu-id="0654b-160">プロジェクトを実行する</span><span class="sxs-lookup"><span data-stu-id="0654b-160">Run the project</span></span>

<span data-ttu-id="0654b-161">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="0654b-161">In Visual Studio:</span></span>

* <span data-ttu-id="0654b-162">ビルド構成のドロップダウン リストが **[デバッグ]** に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0654b-162">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="0654b-163">[実行] ボタンを **[IIS]** プロファイルに設定し、ボタンを選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="0654b-163">Set the Run button to the **IIS** profile and select the button to start the app.</span></span>

![VS ツールバーの [実行] ボタンは [IIS] プロファイルに設定され、ビルド構成のドロップダウン リストは [リリース] に構成されます。](development-time-iis-support/_static/toolbar.png)

<span data-ttu-id="0654b-165">管理者として実行していない場合、Visual Studio によって再起動を求められる場合があります。</span><span class="sxs-lookup"><span data-stu-id="0654b-165">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="0654b-166">その場合は、Visual Studio を再起動します。</span><span class="sxs-lookup"><span data-stu-id="0654b-166">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="0654b-167">信頼されていない開発証明書を使用すると、信頼されていない証明書に対する例外を作成するように、ブラウザーによって求められる場合があります。</span><span class="sxs-lookup"><span data-stu-id="0654b-167">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="0654b-168">[マイ コードのみ](/visualstudio/debugger/just-my-code)とコンパイラの最適化を使用してリリースのビルド構成をデバッグすると、エクスペリエンスの品質が低下します。</span><span class="sxs-lookup"><span data-stu-id="0654b-168">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="0654b-169">たとえば、ブレーク ポイントがヒットしません。</span><span class="sxs-lookup"><span data-stu-id="0654b-169">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0654b-170">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="0654b-170">Additional resources</span></span>

* [<span data-ttu-id="0654b-171">IIS を使用した Windows での ASP.NET Core のホスト</span><span class="sxs-lookup"><span data-stu-id="0654b-171">Host ASP.NET Core on Windows with IIS</span></span>](xref:host-and-deploy/iis/index)
* [<span data-ttu-id="0654b-172">ASP.NET Core モジュールの概要</span><span class="sxs-lookup"><span data-stu-id="0654b-172">Introduction to ASP.NET Core Module</span></span>](xref:host-and-deploy/aspnet-core-module)
* [<span data-ttu-id="0654b-173">ASP.NET Core モジュール構成リファレンス</span><span class="sxs-lookup"><span data-stu-id="0654b-173">ASP.NET Core Module configuration reference</span></span>](xref:host-and-deploy/aspnet-core-module)
* [<span data-ttu-id="0654b-174">HTTPS の適用</span><span class="sxs-lookup"><span data-stu-id="0654b-174">Enforce HTTPS</span></span>](xref:security/enforcing-ssl)
