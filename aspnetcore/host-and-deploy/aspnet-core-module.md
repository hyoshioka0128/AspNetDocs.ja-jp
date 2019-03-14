---
title: ASP.NET Core モジュール
author: guardrex
description: ASP.NET Core アプリをホストするための ASP.NET Core モジュールを構成する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 02/26/2019
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 302cfb00127c223aeb5e51e4d0a9ef3cb69b10eb
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57029889"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="27d56-103">ASP.NET Core モジュール</span><span class="sxs-lookup"><span data-stu-id="27d56-103">ASP.NET Core Module</span></span>

<span data-ttu-id="27d56-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Rick Strahl](https://github.com/RickStrahl)、[Chris Ross](https://github.com/Tratcher)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Sourabh Shirhatti](https://twitter.com/sshirhatti)、[Justin Kotalik](https://github.com/jkotalik)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="27d56-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="27d56-105">ASP.NET Core モジュールはネイティブな IIS モジュールであり、次のいずれかを目的として、IIS パイプラインにプラグインされます。</span><span class="sxs-lookup"><span data-stu-id="27d56-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="27d56-106">IIS ワーカー プロセス (`w3wp.exe`) の内部で ASP.NET Core アプリをホストします。これは、[インプロセス ホスティング モデル](#in-process-hosting-model)と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="27d56-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="27d56-107">[Kestrel サーバー](xref:fundamentals/servers/kestrel)を実行しているバックエンドの ASP.NET Core アプリに Web 要求を転送します。これは、[アウト プロセス ホスティング モデル](#out-of-process-hosting-model)と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="27d56-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="27d56-108">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="27d56-108">Supported Windows versions:</span></span>

* <span data-ttu-id="27d56-109">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="27d56-109">Windows 7 or later</span></span>
* <span data-ttu-id="27d56-110">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="27d56-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="27d56-111">インプロセス ホスティングの場合、モジュールでは IIS HTTP サーバー (`IISHttpServer`) と呼ばれる IIS 用のインプロセス サーバー実装が使用されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="27d56-112">アウト プロセスでホストする場合、モジュールは Kestrel に対してのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="27d56-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="27d56-113">モジュールは [HTTP.sys](xref:fundamentals/servers/httpsys) と互換性はありません。</span><span class="sxs-lookup"><span data-stu-id="27d56-113">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="27d56-114">ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="27d56-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="27d56-115">インプロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="27d56-115">In-process hosting model</span></span>

<span data-ttu-id="27d56-116">アプリに対してインプロセス ホスティングを構成するには、そのアプリのプロジェクト ファイルに `<AspNetCoreHostingModel>` プロパティを追加して、値を `InProcess` に設定します。(アウト プロセス ホスティングは `OutOfProcess` を使用して設定します)。</span><span class="sxs-lookup"><span data-stu-id="27d56-116">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="27d56-117">.NET Framework をターゲットにする ASP.NET Core アプリではインプロセス ホスティング モデルはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="27d56-117">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="27d56-118">`<AspNetCoreHostingModel>` プロパティがファイルに存在しない場合、既定値は `OutOfProcess` です。</span><span class="sxs-lookup"><span data-stu-id="27d56-118">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="27d56-119">インプロセスでホストする場合は、次の特性が適用されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-119">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="27d56-120">[Kestrel](xref:fundamentals/servers/kestrel) サーバーの代わりに、IIS HTTP サーバー (`IISHttpServer`) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-120">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="27d56-121">インプロセスの場合、[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) により <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> が呼び出され、次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-121">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="27d56-122">`IISHttpServer` を登録します。</span><span class="sxs-lookup"><span data-stu-id="27d56-122">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="27d56-123">ASP.NET Core モジュールの背後で実行するときにサーバーがリッスンする、ポートとベース パスを構成します。</span><span class="sxs-lookup"><span data-stu-id="27d56-123">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="27d56-124">スタートアップ エラーをキャプチャするホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="27d56-124">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="27d56-125">[requestTimeout 属性](#attributes-of-the-aspnetcore-element) は、インプロセス ホスティングには適用されません。</span><span class="sxs-lookup"><span data-stu-id="27d56-125">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="27d56-126">アプリ プールをアプリ間で共有することはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="27d56-126">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="27d56-127">アプリごとに 1 つのアプリ プールを使用します。</span><span class="sxs-lookup"><span data-stu-id="27d56-127">Use one app pool per app.</span></span>

* <span data-ttu-id="27d56-128">[Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)を使用したとき、または [app_offline.htm ファイルを手動で配置内に置いた](xref:host-and-deploy/iis/index#locked-deployment-files)ときには、開いている接続があると、アプリがすぐにシャットダウンされない場合があります。</span><span class="sxs-lookup"><span data-stu-id="27d56-128">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="27d56-129">たとえば、WebSocket 接続では、アプリのシャットダウンが遅れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="27d56-129">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="27d56-130">アプリとインストールされているランタイム (x64 または x86) のアーキテクチャ (ビット) は、アプリ プールのアーキテクチャと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="27d56-130">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="27d56-131">([CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) ではなく) `WebHostBuilder` を使用してアプリのホストを手動で設定するときに、アプリがこれまで Kestrel サーバー上で直接実行されていた場合 (セルフホステッド)、`UseKestrel` を呼び出してから `UseIISIntegration` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="27d56-131">If setting up the app's host manually with `WebHostBuilder` (not using [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)) and the app is ever run directly on the Kestrel server (self-hosted), call `UseKestrel` before calling `UseIISIntegration`.</span></span> <span data-ttu-id="27d56-132">順序を逆にすると、ホストは開始に失敗します。</span><span class="sxs-lookup"><span data-stu-id="27d56-132">If the order is reversed, the host fails to start.</span></span>

* <span data-ttu-id="27d56-133">クライアントの切断が検出されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-133">Client disconnects are detected.</span></span> <span data-ttu-id="27d56-134">クライアントが切断されると、[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) キャンセル トークンが取り消されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-134">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="27d56-135">ASP.NET Core 2.2.1 以前の場合、<xref:System.IO.Directory.GetCurrentDirectory*> は、アプリのディレクトリではなく、IIS によって開始されたプロセスのワーカー ディレクトリを返します (たとえば、*w3wp.exe* に対して *C:\Windows\System32\inetsrv*)。</span><span class="sxs-lookup"><span data-stu-id="27d56-135">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="27d56-136">アプリの現在のディレクトリを設定するサンプル コードについては、「[CurrentDirectoryHelpers クラス](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="27d56-136">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="27d56-137">
  `SetCurrentDirectory\` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="27d56-137">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="27d56-138"><xref:System.IO.Directory.GetCurrentDirectory*> の後続の呼び出しによって、アプリのディレクトリが指定されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-138">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>
  
* <span data-ttu-id="27d56-139">インプロセス ホスティングの場合、ユーザーを初期化するために内部で <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> が呼び出されることはありません。</span><span class="sxs-lookup"><span data-stu-id="27d56-139">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="27d56-140">そのため、認証のたびに要求を変換するための <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装は既定で有効になっていません。</span><span class="sxs-lookup"><span data-stu-id="27d56-140">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="27d56-141"><xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装で要求を返還するとき、<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> を呼び出し、認証サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="27d56-141">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }
  
  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="27d56-142">アウト プロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="27d56-142">Out-of-process hosting model</span></span>

<span data-ttu-id="27d56-143">アウト プロセス ホスティング用のアプリを構成するには、プロジェクト ファイルで次の方法のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="27d56-143">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="27d56-144">`<AspNetCoreHostingModel>` プロパティは指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="27d56-144">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="27d56-145">`<AspNetCoreHostingModel>` プロパティがファイルに存在しない場合、既定値は `OutOfProcess` です。</span><span class="sxs-lookup"><span data-stu-id="27d56-145">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="27d56-146">`<AspNetCoreHostingModel>` プロパティの値を `OutOfProcess` に設定します (インプロセス ホスティングは `InProcess` で設定されます)。</span><span class="sxs-lookup"><span data-stu-id="27d56-146">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="27d56-147">IIS HTTP サーバー (`IISHttpServer`) の代わりに、[Kestrel](xref:fundamentals/servers/kestrel) サーバーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-147">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="27d56-148">アウトプロセスの場合、[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) により <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> が呼び出され、次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-148">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="27d56-149">ASP.NET Core モジュールの背後で実行するときにサーバーがリッスンする、ポートとベース パスを構成します。</span><span class="sxs-lookup"><span data-stu-id="27d56-149">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="27d56-150">スタートアップ エラーをキャプチャするホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="27d56-150">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="27d56-151">ホスティング モデルの変更</span><span class="sxs-lookup"><span data-stu-id="27d56-151">Hosting model changes</span></span>

<span data-ttu-id="27d56-152">`hostingModel` 設定が *web.config* ファイル内で変更された場合 (「[web.config での構成](#configuration-with-webconfig)」セクションを参照)、モジュールによって IIS 用のワーカー プロセスがリサイクルされます。</span><span class="sxs-lookup"><span data-stu-id="27d56-152">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="27d56-153">IIS Express の場合、モジュールによってワーカー プロセスのリサイクルは行われませんが、代わりに、現在の IIS Express プロセスの正常なシャットダウンがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="27d56-153">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="27d56-154">アプリに対して次の要求が出されると、IIS Express の新しいプロセスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-154">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="27d56-155">プロセス名</span><span class="sxs-lookup"><span data-stu-id="27d56-155">Process name</span></span>

<span data-ttu-id="27d56-156">`Process.GetCurrentProcess().ProcessName` から、`w3wp`/`iisexpress` (インプロセス) または `dotnet` (アウト プロセス) がレポートされます。</span><span class="sxs-lookup"><span data-stu-id="27d56-156">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="27d56-157">ASP.NET Core モジュールは、ネイティブな IIS モジュールであり、バックエンドの ASP.NET Core アプリに Web 要求を転送するために IIS パイプラインにプラグインされます。</span><span class="sxs-lookup"><span data-stu-id="27d56-157">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="27d56-158">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="27d56-158">Supported Windows versions:</span></span>

* <span data-ttu-id="27d56-159">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="27d56-159">Windows 7 or later</span></span>
* <span data-ttu-id="27d56-160">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="27d56-160">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="27d56-161">モジュールは、Kestrel に対してのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="27d56-161">The module only works with Kestrel.</span></span> <span data-ttu-id="27d56-162">モジュールは [HTTP.sys](xref:fundamentals/servers/httpsys) と互換性はありません。</span><span class="sxs-lookup"><span data-stu-id="27d56-162">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="27d56-163">ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行されるため、モジュールもプロセス管理を処理します。</span><span class="sxs-lookup"><span data-stu-id="27d56-163">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="27d56-164">モジュールは、最初の要求を受信したときに ASP.NET Core アプリのプロセスを開始し、プロセスがクラッシュした場合はアプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="27d56-164">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="27d56-165">この動作は、IIS のインプロセスで実行され、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理される ASP.NET 4.x アプリと基本的に同じです。</span><span class="sxs-lookup"><span data-stu-id="27d56-165">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="27d56-166">次の図は、IIS (ASP.NET Core モジュール) とアプリの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="27d56-166">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core モジュール](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="27d56-168">要求は、Web からカーネル モードの HTTP.sys ドライバーに到着します。</span><span class="sxs-lookup"><span data-stu-id="27d56-168">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="27d56-169">ドライバーは、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) で IIS への要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="27d56-169">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="27d56-170">モジュールでは、アプリのランダムなポート (ポート 80 または 443 ではありません) で Kestrel に要求が転送されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-170">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="27d56-171">モジュールが起動時に環境変数を介してポートを指定すると、サーバーは `http://localhost:{port}` をリッスンするように、IIS 統合ミドルウェアによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-171">The module specifies the port via an environment variable at startup, and the IIS Integration Middleware configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="27d56-172">追加のチェックが実行され、モジュールから発生していない要求は拒否されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-172">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="27d56-173">モジュールでは HTTPS 転送がサポートされていないため、要求は HTTPS を介して IIS によって受信された場合でも、HTTP を介して転送されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-173">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="27d56-174">Kestrel によってモジュールから要求が取り込まれた後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="27d56-174">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="27d56-175">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="27d56-175">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="27d56-176">IIS 統合によって追加されたミドルウェアでは、カーネルへの要求の転送を考慮して、スキーム、リモート IP、およびパスベースが更新されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-176">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="27d56-177">アプリの応答が IIS に戻され、IIS はその応答を、要求を開始した HTTP クライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="27d56-177">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

::: moniker-end

<span data-ttu-id="27d56-178">Windows 認証などの多くのネイティブなモジュールは、アクティブなままです。</span><span class="sxs-lookup"><span data-stu-id="27d56-178">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="27d56-179">ASP.NET Core モジュールと共にアクティブな IIS モジュールの詳細については、「<xref:host-and-deploy/iis/modules>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="27d56-179">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="27d56-180">ASP.NET Core モジュールでは次のことも行えます。</span><span class="sxs-lookup"><span data-stu-id="27d56-180">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="27d56-181">ワーカー プロセスの環境変数を設定する</span><span class="sxs-lookup"><span data-stu-id="27d56-181">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="27d56-182">起動に関する問題をトラブルシューティングするために、Stdout 出力をファイル ストレージに記録する</span><span class="sxs-lookup"><span data-stu-id="27d56-182">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="27d56-183">Windows 認証トークンを転送する</span><span class="sxs-lookup"><span data-stu-id="27d56-183">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="27d56-184">ASP.NET Core モジュールをインストールして使用する方法</span><span class="sxs-lookup"><span data-stu-id="27d56-184">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="27d56-185">ASP.NET Core モジュールをインストールして使用する方法の詳細については、「<xref:host-and-deploy/iis/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="27d56-185">For instructions on how to install and use the ASP.NET Core Module, see <xref:host-and-deploy/iis/index>.</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="27d56-186">web.config での構成</span><span class="sxs-lookup"><span data-stu-id="27d56-186">Configuration with web.config</span></span>

<span data-ttu-id="27d56-187">ASP.NET Core モジュールは、サイトの *web.config* ファイルの `system.webServer` ノードの `aspNetCore` セクションを使って構成します。</span><span class="sxs-lookup"><span data-stu-id="27d56-187">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="27d56-188">次に示す *web.config* ファイルは、[フレームワークに依存する展開](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)用に発行されたもので、サイトの要求を処理するように ASP.NET Core モジュールを構成します。</span><span class="sxs-lookup"><span data-stu-id="27d56-188">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet" 
                  arguments=".\MyApp.dll" 
                  stdoutLogEnabled="false" 
                  stdoutLogFile=".\logs\stdout" 
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet" 
                arguments=".\MyApp.dll" 
                stdoutLogEnabled="false" 
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

::: moniker-end

<span data-ttu-id="27d56-189">次の *web.config* は、[自己完結型の展開](/dotnet/articles/core/deploying/#self-contained-deployments-scd)用に発行されたものです。</span><span class="sxs-lookup"><span data-stu-id="27d56-189">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe" 
                  stdoutLogEnabled="false" 
                  stdoutLogFile=".\logs\stdout" 
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="27d56-190"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> プロパティは、`false` に設定されます。これは、[\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 要素内で指定された設定が、アプリのサブディレクトリにあるアプリによって継承されないことを示します。</span><span class="sxs-lookup"><span data-stu-id="27d56-190">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe" 
                stdoutLogEnabled="false" 
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

::: moniker-end

<span data-ttu-id="27d56-191">アプリが [Azure App Service](https://azure.microsoft.com/services/app-service/) に対して展開されると、`stdoutLogFile` パスは `\\?\%home%\LogFiles\stdout` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-191">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="27d56-192">パスは stdout ログを *LogFiles* フォルダーに保存します。これは、サービスによって自動的に作成される場所です。</span><span class="sxs-lookup"><span data-stu-id="27d56-192">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="27d56-193">IIS サブアプリケーション構成について詳しくは、「<xref:host-and-deploy/iis/index#sub-applications>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="27d56-193">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="27d56-194">aspNetCore 要素の属性</span><span class="sxs-lookup"><span data-stu-id="27d56-194">Attributes of the aspNetCore element</span></span>

::: moniker range=">= aspnetcore-2.2"

| <span data-ttu-id="27d56-195">属性</span><span class="sxs-lookup"><span data-stu-id="27d56-195">Attribute</span></span> | <span data-ttu-id="27d56-196">説明</span><span class="sxs-lookup"><span data-stu-id="27d56-196">Description</span></span> | <span data-ttu-id="27d56-197">既定値</span><span class="sxs-lookup"><span data-stu-id="27d56-197">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="27d56-198">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="27d56-198">Optional string attribute.</span></span></p><p><span data-ttu-id="27d56-199">**processPath** において指定されている実行可能ファイルへの引数です。</span><span class="sxs-lookup"><span data-stu-id="27d56-199">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="27d56-200">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="27d56-200">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="27d56-201">true の場合、**502.5 - 処理エラー** ページは抑制され、*web.config* で構成されている 502 状態コード ページが優先されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-201">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="27d56-202">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="27d56-202">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="27d56-203">true の場合、トークンは、%ASPNETCORE_PORT% でリッスンしている子プロセスに、要求ごとの 'MS-ASPNETCORE-WINAUTHTOKEN' ヘッダーとして転送されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-203">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="27d56-204">要求ごとのこのトークンで CloseHandle を呼び出すのは、そのプロセスの役割です。</span><span class="sxs-lookup"><span data-stu-id="27d56-204">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="27d56-205">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="27d56-205">Optional string attribute.</span></span></p><p><span data-ttu-id="27d56-206">ホスティング モデルをインプロセス (`InProcess`) またはアウト プロセス (`OutOfProcess`) として指定します。</span><span class="sxs-lookup"><span data-stu-id="27d56-206">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `OutOfProcess` |
| `processesPerApplication` | <p><span data-ttu-id="27d56-207">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="27d56-207">Optional integer attribute.</span></span></p><p><span data-ttu-id="27d56-208">アプリごとにスピンアップすることができる **processPath** 設定内で指定したプロセスのインスタンス数が指定されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-208">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="27d56-209">&dagger;インプロセス ホスティングの場合、値は `1` に制限されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-209">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="27d56-210">設定 `processesPerApplication` は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="27d56-210">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="27d56-211">この属性は将来のリリースで削除されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-211">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="27d56-212">既定値: `1`</span><span class="sxs-lookup"><span data-stu-id="27d56-212">Default: `1`</span></span><br><span data-ttu-id="27d56-213">最小値: `1`</span><span class="sxs-lookup"><span data-stu-id="27d56-213">Min: `1`</span></span><br><span data-ttu-id="27d56-214">最大値: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="27d56-214">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="27d56-215">必須の文字列属性です。</span><span class="sxs-lookup"><span data-stu-id="27d56-215">Required string attribute.</span></span></p><p><span data-ttu-id="27d56-216">HTTP 要求をリッスンするプロセスを起動する実行可能ファイルへのパスです。</span><span class="sxs-lookup"><span data-stu-id="27d56-216">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="27d56-217">相対パスがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="27d56-217">Relative paths are supported.</span></span> <span data-ttu-id="27d56-218">パスが `.` で始まる場合、パスはサイトのルートを基準とする相対パスであると見なされます。</span><span class="sxs-lookup"><span data-stu-id="27d56-218">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="27d56-219">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="27d56-219">Optional integer attribute.</span></span></p><p><span data-ttu-id="27d56-220">**processPath** で指定されているプロセスが 1 分間にクラッシュできる回数を指定します。</span><span class="sxs-lookup"><span data-stu-id="27d56-220">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="27d56-221">この制限を超えた場合、モジュールは、1 分間の残りの間、プロセスの起動を停止します。</span><span class="sxs-lookup"><span data-stu-id="27d56-221">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="27d56-222">インプロセス ホスティングではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="27d56-222">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="27d56-223">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="27d56-223">Default: `10`</span></span><br><span data-ttu-id="27d56-224">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="27d56-224">Min: `0`</span></span><br><span data-ttu-id="27d56-225">最大値: `100`</span><span class="sxs-lookup"><span data-stu-id="27d56-225">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="27d56-226">省略可能な期間属性。</span><span class="sxs-lookup"><span data-stu-id="27d56-226">Optional timespan attribute.</span></span></p><p><span data-ttu-id="27d56-227">%ASPNETCORE_PORT% でリッスンしているプロセスからの応答を ASP.NET Core モジュールが待機する期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="27d56-227">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="27d56-228">ASP.NET Core 2.1 以降のリリースに付属する ASP.NET Core モジュールのバージョンでは、`requestTimeout` は時間、分、および秒単位で指定します。</span><span class="sxs-lookup"><span data-stu-id="27d56-228">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="27d56-229">インプロセス ホスティングには適用されません。</span><span class="sxs-lookup"><span data-stu-id="27d56-229">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="27d56-230">インプロセス ホスティングの場合、アプリによって要求が処理されるまでモジュールは待機します。</span><span class="sxs-lookup"><span data-stu-id="27d56-230">For in-process hosting, the module waits for the app to process the request.</span></span></p> | <span data-ttu-id="27d56-231">既定値: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="27d56-231">Default: `00:02:00`</span></span><br><span data-ttu-id="27d56-232">最小値: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="27d56-232">Min: `00:00:00`</span></span><br><span data-ttu-id="27d56-233">最大値: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="27d56-233">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="27d56-234">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="27d56-234">Optional integer attribute.</span></span></p><p><span data-ttu-id="27d56-235">*app_offline.htm* ファイルが検出されたときに、モジュールが実行可能ファイルの正常なシャットダウンを待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="27d56-235">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="27d56-236">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="27d56-236">Default: `10`</span></span><br><span data-ttu-id="27d56-237">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="27d56-237">Min: `0`</span></span><br><span data-ttu-id="27d56-238">最大値: `600`</span><span class="sxs-lookup"><span data-stu-id="27d56-238">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="27d56-239">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="27d56-239">Optional integer attribute.</span></span></p><p><span data-ttu-id="27d56-240">ポートでリッスンするプロセスを実行可能ファイルが開始するのをモジュールが待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="27d56-240">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="27d56-241">この制限時間を超えた場合、モジュールはプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="27d56-241">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="27d56-242">最後のローリング分においてアプリが **rapidFailsPerMinute** 回だけ開始を失敗しているのでないかぎり、モジュールは、新しい要求を受け取るとプロセスの再起動を試み、それ以降に受信した要求に対してプロセスの再起動を試み続けます。</span><span class="sxs-lookup"><span data-stu-id="27d56-242">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="27d56-243">値 0 (ゼロ) は無限のタイムアウトと見なされ**ません**。</span><span class="sxs-lookup"><span data-stu-id="27d56-243">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="27d56-244">既定値: `120`</span><span class="sxs-lookup"><span data-stu-id="27d56-244">Default: `120`</span></span><br><span data-ttu-id="27d56-245">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="27d56-245">Min: `0`</span></span><br><span data-ttu-id="27d56-246">最大値: `3600`</span><span class="sxs-lookup"><span data-stu-id="27d56-246">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="27d56-247">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="27d56-247">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="27d56-248">true の場合、**processPath** で指定されているプロセスの **stdout** と **stderr** は、**stdoutLogFile** で指定されているファイルにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="27d56-248">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="27d56-249">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="27d56-249">Optional string attribute.</span></span></p><p><span data-ttu-id="27d56-250">**processPath** で指定されているプロセスからの **stdout** と **stderr** がログに記録される相対ファイル パスまたは絶対ファイル パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="27d56-250">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="27d56-251">相対パスの基準はサイトのルートです。</span><span class="sxs-lookup"><span data-stu-id="27d56-251">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="27d56-252">`.` で始まっているパスはすべてサイト ルートに対する相対パスであり、他のすべてのパスは絶対パスとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="27d56-252">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="27d56-253">パスで指定されたフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-253">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="27d56-254">アンダースコアの区切り記号を使って、タイムスタンプ、プロセス ID、およびファイル拡張子 (*.log*) が、**stdoutLogFile** パスの最後のセグメントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-254">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="27d56-255">たとえば、値として `.\logs\stdout` を指定し、2018 年 2 月 5 日の 19:41:32 にプロセス ID 1934 で保存すると、stdout ログは *logs* フォルダーに *stdout_20180205194132_1934.log* として保存されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-255">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="27d56-256">属性</span><span class="sxs-lookup"><span data-stu-id="27d56-256">Attribute</span></span> | <span data-ttu-id="27d56-257">説明</span><span class="sxs-lookup"><span data-stu-id="27d56-257">Description</span></span> | <span data-ttu-id="27d56-258">既定値</span><span class="sxs-lookup"><span data-stu-id="27d56-258">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="27d56-259">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="27d56-259">Optional string attribute.</span></span></p><p><span data-ttu-id="27d56-260">**processPath** において指定されている実行可能ファイルへの引数です。</span><span class="sxs-lookup"><span data-stu-id="27d56-260">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="27d56-261">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="27d56-261">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="27d56-262">true の場合、**502.5 - 処理エラー** ページは抑制され、*web.config* で構成されている 502 状態コード ページが優先されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-262">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="27d56-263">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="27d56-263">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="27d56-264">true の場合、トークンは、%ASPNETCORE_PORT% でリッスンしている子プロセスに、要求ごとの 'MS-ASPNETCORE-WINAUTHTOKEN' ヘッダーとして転送されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-264">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="27d56-265">要求ごとのこのトークンで CloseHandle を呼び出すのは、そのプロセスの役割です。</span><span class="sxs-lookup"><span data-stu-id="27d56-265">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="27d56-266">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="27d56-266">Optional integer attribute.</span></span></p><p><span data-ttu-id="27d56-267">アプリごとにスピンアップすることができる **processPath** 設定内で指定したプロセスのインスタンス数が指定されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-267">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="27d56-268">設定 `processesPerApplication` は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="27d56-268">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="27d56-269">この属性は将来のリリースで削除されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-269">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="27d56-270">既定値: `1`</span><span class="sxs-lookup"><span data-stu-id="27d56-270">Default: `1`</span></span><br><span data-ttu-id="27d56-271">最小値: `1`</span><span class="sxs-lookup"><span data-stu-id="27d56-271">Min: `1`</span></span><br><span data-ttu-id="27d56-272">最大値: `100`</span><span class="sxs-lookup"><span data-stu-id="27d56-272">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="27d56-273">必須の文字列属性です。</span><span class="sxs-lookup"><span data-stu-id="27d56-273">Required string attribute.</span></span></p><p><span data-ttu-id="27d56-274">HTTP 要求をリッスンするプロセスを起動する実行可能ファイルへのパスです。</span><span class="sxs-lookup"><span data-stu-id="27d56-274">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="27d56-275">相対パスがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="27d56-275">Relative paths are supported.</span></span> <span data-ttu-id="27d56-276">パスが `.` で始まる場合、パスはサイトのルートを基準とする相対パスであると見なされます。</span><span class="sxs-lookup"><span data-stu-id="27d56-276">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="27d56-277">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="27d56-277">Optional integer attribute.</span></span></p><p><span data-ttu-id="27d56-278">**processPath** で指定されているプロセスが 1 分間にクラッシュできる回数を指定します。</span><span class="sxs-lookup"><span data-stu-id="27d56-278">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="27d56-279">この制限を超えた場合、モジュールは、1 分間の残りの間、プロセスの起動を停止します。</span><span class="sxs-lookup"><span data-stu-id="27d56-279">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="27d56-280">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="27d56-280">Default: `10`</span></span><br><span data-ttu-id="27d56-281">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="27d56-281">Min: `0`</span></span><br><span data-ttu-id="27d56-282">最大値: `100`</span><span class="sxs-lookup"><span data-stu-id="27d56-282">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="27d56-283">省略可能な期間属性。</span><span class="sxs-lookup"><span data-stu-id="27d56-283">Optional timespan attribute.</span></span></p><p><span data-ttu-id="27d56-284">%ASPNETCORE_PORT% でリッスンしているプロセスからの応答を ASP.NET Core モジュールが待機する期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="27d56-284">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="27d56-285">ASP.NET Core 2.1 以降のリリースに付属する ASP.NET Core モジュールのバージョンでは、`requestTimeout` は時間、分、および秒単位で指定します。</span><span class="sxs-lookup"><span data-stu-id="27d56-285">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="27d56-286">既定値: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="27d56-286">Default: `00:02:00`</span></span><br><span data-ttu-id="27d56-287">最小値: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="27d56-287">Min: `00:00:00`</span></span><br><span data-ttu-id="27d56-288">最大値: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="27d56-288">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="27d56-289">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="27d56-289">Optional integer attribute.</span></span></p><p><span data-ttu-id="27d56-290">*app_offline.htm* ファイルが検出されたときに、モジュールが実行可能ファイルの正常なシャットダウンを待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="27d56-290">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="27d56-291">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="27d56-291">Default: `10`</span></span><br><span data-ttu-id="27d56-292">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="27d56-292">Min: `0`</span></span><br><span data-ttu-id="27d56-293">最大値: `600`</span><span class="sxs-lookup"><span data-stu-id="27d56-293">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="27d56-294">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="27d56-294">Optional integer attribute.</span></span></p><p><span data-ttu-id="27d56-295">ポートでリッスンするプロセスを実行可能ファイルが開始するのをモジュールが待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="27d56-295">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="27d56-296">この制限時間を超えた場合、モジュールはプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="27d56-296">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="27d56-297">最後のローリング分においてアプリが **rapidFailsPerMinute** 回だけ開始を失敗しているのでないかぎり、モジュールは、新しい要求を受け取るとプロセスの再起動を試み、それ以降に受信した要求に対してプロセスの再起動を試み続けます。</span><span class="sxs-lookup"><span data-stu-id="27d56-297">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="27d56-298">値 0 (ゼロ) は無限のタイムアウトと見なされ**ません**。</span><span class="sxs-lookup"><span data-stu-id="27d56-298">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="27d56-299">既定値: `120`</span><span class="sxs-lookup"><span data-stu-id="27d56-299">Default: `120`</span></span><br><span data-ttu-id="27d56-300">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="27d56-300">Min: `0`</span></span><br><span data-ttu-id="27d56-301">最大値: `3600`</span><span class="sxs-lookup"><span data-stu-id="27d56-301">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="27d56-302">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="27d56-302">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="27d56-303">true の場合、**processPath** で指定されているプロセスの **stdout** と **stderr** は、**stdoutLogFile** で指定されているファイルにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="27d56-303">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="27d56-304">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="27d56-304">Optional string attribute.</span></span></p><p><span data-ttu-id="27d56-305">**processPath** で指定されているプロセスからの **stdout** と **stderr** がログに記録される相対ファイル パスまたは絶対ファイル パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="27d56-305">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="27d56-306">相対パスの基準はサイトのルートです。</span><span class="sxs-lookup"><span data-stu-id="27d56-306">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="27d56-307">`.` で始まっているパスはすべてサイト ルートに対する相対パスであり、他のすべてのパスは絶対パスとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="27d56-307">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="27d56-308">モジュールがログ ファイルを作成するためには、パスで指定されているすべてのフォルダーが存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="27d56-308">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="27d56-309">アンダースコアの区切り記号を使って、タイムスタンプ、プロセス ID、およびファイル拡張子 (*.log*) が、**stdoutLogFile** パスの最後のセグメントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-309">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="27d56-310">たとえば、値として `.\logs\stdout` を指定し、2018 年 2 月 5 日の 19:41:32 にプロセス ID 1934 で保存すると、stdout ログは *logs* フォルダーに *stdout_20180205194132_1934.log* として保存されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-310">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

| <span data-ttu-id="27d56-311">属性</span><span class="sxs-lookup"><span data-stu-id="27d56-311">Attribute</span></span> | <span data-ttu-id="27d56-312">説明</span><span class="sxs-lookup"><span data-stu-id="27d56-312">Description</span></span> | <span data-ttu-id="27d56-313">既定値</span><span class="sxs-lookup"><span data-stu-id="27d56-313">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="27d56-314">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="27d56-314">Optional string attribute.</span></span></p><p><span data-ttu-id="27d56-315">**processPath** において指定されている実行可能ファイルへの引数です。</span><span class="sxs-lookup"><span data-stu-id="27d56-315">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="27d56-316">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="27d56-316">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="27d56-317">true の場合、**502.5 - 処理エラー** ページは抑制され、*web.config* で構成されている 502 状態コード ページが優先されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-317">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="27d56-318">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="27d56-318">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="27d56-319">true の場合、トークンは、%ASPNETCORE_PORT% でリッスンしている子プロセスに、要求ごとの 'MS-ASPNETCORE-WINAUTHTOKEN' ヘッダーとして転送されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-319">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="27d56-320">要求ごとのこのトークンで CloseHandle を呼び出すのは、そのプロセスの役割です。</span><span class="sxs-lookup"><span data-stu-id="27d56-320">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="27d56-321">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="27d56-321">Optional integer attribute.</span></span></p><p><span data-ttu-id="27d56-322">アプリごとにスピンアップすることができる **processPath** 設定内で指定したプロセスのインスタンス数が指定されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-322">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="27d56-323">設定 `processesPerApplication` は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="27d56-323">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="27d56-324">この属性は将来のリリースで削除されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-324">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="27d56-325">既定値: `1`</span><span class="sxs-lookup"><span data-stu-id="27d56-325">Default: `1`</span></span><br><span data-ttu-id="27d56-326">最小値: `1`</span><span class="sxs-lookup"><span data-stu-id="27d56-326">Min: `1`</span></span><br><span data-ttu-id="27d56-327">最大値: `100`</span><span class="sxs-lookup"><span data-stu-id="27d56-327">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="27d56-328">必須の文字列属性です。</span><span class="sxs-lookup"><span data-stu-id="27d56-328">Required string attribute.</span></span></p><p><span data-ttu-id="27d56-329">HTTP 要求をリッスンするプロセスを起動する実行可能ファイルへのパスです。</span><span class="sxs-lookup"><span data-stu-id="27d56-329">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="27d56-330">相対パスがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="27d56-330">Relative paths are supported.</span></span> <span data-ttu-id="27d56-331">パスが `.` で始まる場合、パスはサイトのルートを基準とする相対パスであると見なされます。</span><span class="sxs-lookup"><span data-stu-id="27d56-331">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="27d56-332">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="27d56-332">Optional integer attribute.</span></span></p><p><span data-ttu-id="27d56-333">**processPath** で指定されているプロセスが 1 分間にクラッシュできる回数を指定します。</span><span class="sxs-lookup"><span data-stu-id="27d56-333">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="27d56-334">この制限を超えた場合、モジュールは、1 分間の残りの間、プロセスの起動を停止します。</span><span class="sxs-lookup"><span data-stu-id="27d56-334">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="27d56-335">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="27d56-335">Default: `10`</span></span><br><span data-ttu-id="27d56-336">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="27d56-336">Min: `0`</span></span><br><span data-ttu-id="27d56-337">最大値: `100`</span><span class="sxs-lookup"><span data-stu-id="27d56-337">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="27d56-338">省略可能な期間属性。</span><span class="sxs-lookup"><span data-stu-id="27d56-338">Optional timespan attribute.</span></span></p><p><span data-ttu-id="27d56-339">%ASPNETCORE_PORT% でリッスンしているプロセスからの応答を ASP.NET Core モジュールが待機する期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="27d56-339">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="27d56-340">ASP.NET Core 2.0 以前のリリースに付属する ASP.NET Core モジュールのバージョンでは、`requestTimeout` は整数でのみ指定する必要があります。そうしないと、既定値の 2 分に設定されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-340">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.0 or earlier, the `requestTimeout` must be specified in whole minutes only, otherwise it defaults to 2 minutes.</span></span></p> | <span data-ttu-id="27d56-341">既定値: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="27d56-341">Default: `00:02:00`</span></span><br><span data-ttu-id="27d56-342">最小値: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="27d56-342">Min: `00:00:00`</span></span><br><span data-ttu-id="27d56-343">最大値: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="27d56-343">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="27d56-344">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="27d56-344">Optional integer attribute.</span></span></p><p><span data-ttu-id="27d56-345">*app_offline.htm* ファイルが検出されたときに、モジュールが実行可能ファイルの正常なシャットダウンを待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="27d56-345">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="27d56-346">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="27d56-346">Default: `10`</span></span><br><span data-ttu-id="27d56-347">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="27d56-347">Min: `0`</span></span><br><span data-ttu-id="27d56-348">最大値: `600`</span><span class="sxs-lookup"><span data-stu-id="27d56-348">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="27d56-349">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="27d56-349">Optional integer attribute.</span></span></p><p><span data-ttu-id="27d56-350">ポートでリッスンするプロセスを実行可能ファイルが開始するのをモジュールが待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="27d56-350">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="27d56-351">この制限時間を超えた場合、モジュールはプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="27d56-351">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="27d56-352">最後のローリング分においてアプリが **rapidFailsPerMinute** 回だけ開始を失敗しているのでないかぎり、モジュールは、新しい要求を受け取るとプロセスの再起動を試み、それ以降に受信した要求に対してプロセスの再起動を試み続けます。</span><span class="sxs-lookup"><span data-stu-id="27d56-352">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p> | <span data-ttu-id="27d56-353">既定値: `120`</span><span class="sxs-lookup"><span data-stu-id="27d56-353">Default: `120`</span></span><br><span data-ttu-id="27d56-354">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="27d56-354">Min: `0`</span></span><br><span data-ttu-id="27d56-355">最大値: `3600`</span><span class="sxs-lookup"><span data-stu-id="27d56-355">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="27d56-356">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="27d56-356">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="27d56-357">true の場合、**processPath** で指定されているプロセスの **stdout** と **stderr** は、**stdoutLogFile** で指定されているファイルにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="27d56-357">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="27d56-358">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="27d56-358">Optional string attribute.</span></span></p><p><span data-ttu-id="27d56-359">**processPath** で指定されているプロセスからの **stdout** と **stderr** がログに記録される相対ファイル パスまたは絶対ファイル パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="27d56-359">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="27d56-360">相対パスの基準はサイトのルートです。</span><span class="sxs-lookup"><span data-stu-id="27d56-360">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="27d56-361">`.` で始まっているパスはすべてサイト ルートに対する相対パスであり、他のすべてのパスは絶対パスとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="27d56-361">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="27d56-362">モジュールがログ ファイルを作成するためには、パスで指定されているすべてのフォルダーが存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="27d56-362">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="27d56-363">アンダースコアの区切り記号を使って、タイムスタンプ、プロセス ID、およびファイル拡張子 (*.log*) が、**stdoutLogFile** パスの最後のセグメントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-363">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="27d56-364">たとえば、値として `.\logs\stdout` を指定し、2018 年 2 月 5 日の 19:41:32 にプロセス ID 1934 で保存すると、stdout ログは *logs* フォルダーに *stdout_20180205194132_1934.log* として保存されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-364">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

### <a name="setting-environment-variables"></a><span data-ttu-id="27d56-365">環境変数の設定</span><span class="sxs-lookup"><span data-stu-id="27d56-365">Setting environment variables</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="27d56-366">`processPath` 属性で、プロセスに対する環境変数を指定できます。</span><span class="sxs-lookup"><span data-stu-id="27d56-366">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="27d56-367">`<environmentVariables>` コレクション要素の `<environmentVariable>` 子要素で、環境変数を指定します。</span><span class="sxs-lookup"><span data-stu-id="27d56-367">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="27d56-368">このセクションで設定された環境変数は、システム環境変数より優先されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-368">Environment variables set in this section take precedence over system environment variables.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="27d56-369">`processPath` 属性で、プロセスに対する環境変数を指定できます。</span><span class="sxs-lookup"><span data-stu-id="27d56-369">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="27d56-370">`<environmentVariables>` コレクション要素の `<environmentVariable>` 子要素で、環境変数を指定します。</span><span class="sxs-lookup"><span data-stu-id="27d56-370">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="27d56-371">このセクションで設定した環境変数は、同じ名前を使って設定したシステム環境変数と競合します。</span><span class="sxs-lookup"><span data-stu-id="27d56-371">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="27d56-372">*web.config* ファイルと Windows のシステム レベルの両方で 1 つの環境変数が設定されている場合、*web.config* ファイルからの値がシステム環境変数の値に追加されるため (例: `ASPNETCORE_ENVIRONMENT: Development;Development`)、アプリが起動できなくなります。</span><span class="sxs-lookup"><span data-stu-id="27d56-372">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

::: moniker-end

<span data-ttu-id="27d56-373">次の例では、2 つの環境変数を設定しています。</span><span class="sxs-lookup"><span data-stu-id="27d56-373">The following example sets two environment variables.</span></span> <span data-ttu-id="27d56-374">`ASPNETCORE_ENVIRONMENT` は、`Development` に対するアプリの環境を構成します。</span><span class="sxs-lookup"><span data-stu-id="27d56-374">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="27d56-375">開発者は、アプリの例外をデバッグするときに[開発者例外ページ](xref:fundamentals/error-handling)を強制的に読み込むため、*web.config* ファイルでこの値を一時的に設定できます。</span><span class="sxs-lookup"><span data-stu-id="27d56-375">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="27d56-376">`CONFIG_DIR` はユーザー定義の環境変数の例です。開発者はここに、アプリの構成ファイルを読み込むためのパスを形成するために起動時に値を読み取るコードを記述してあります。</span><span class="sxs-lookup"><span data-stu-id="27d56-376">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="27d56-377">*web.config* 内で環境を直接設定する代わりに、発行プロファイル (*.pubxml*) またはプロジェクト ファイルに `<EnvironmentName>` プロパティを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="27d56-377">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="27d56-378">この方法では、プロジェクトが発行されるときに *web.config* に環境が設定されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-378">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

::: moniker-end

> [!WARNING]
> <span data-ttu-id="27d56-379">インターネットなどの信頼されていないネットワークにアクセスできないステージング サーバーやテスト サーバーでは、`ASPNETCORE_ENVIRONMENT` 環境変数を `Development` に設定するだけです。</span><span class="sxs-lookup"><span data-stu-id="27d56-379">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="appofflinehtm"></a><span data-ttu-id="27d56-380">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="27d56-380">app_offline.htm</span></span>

<span data-ttu-id="27d56-381">*app_offline.htm* という名前のファイルがアプリのルート ディレクトリで検出された場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、受信要求の処理を停止することを試みます。</span><span class="sxs-lookup"><span data-stu-id="27d56-381">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="27d56-382">`shutdownTimeLimit` で定義されている秒数が経過してもまだアプリが実行している場合、ASP.NET Core モジュールは実行中のプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="27d56-382">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="27d56-383">*app_offline.htm* ファイルが存在している間、ASP.NET Core モジュールは、*app_offline.htm* ファイルの内容を返送することで、要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="27d56-383">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="27d56-384">*app_offline.htm* ファイルが削除されると、次の要求によってアプリが起動されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-384">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="27d56-385">アウト プロセス ホスティング モデルを使用するときは、開いている接続があると、アプリがすぐにシャットダウンされない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="27d56-385">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="27d56-386">たとえば、WebSocket 接続では、アプリのシャットダウンが遅れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="27d56-386">For example, a websocket connection may delay app shut down.</span></span>

::: moniker-end

## <a name="start-up-error-page"></a><span data-ttu-id="27d56-387">起動エラー ページ</span><span class="sxs-lookup"><span data-stu-id="27d56-387">Start-up error page</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="27d56-388">インプロセス ホスティングでもアウト プロセス ホスティングでも、アプリの起動に失敗すると、カスタム エラー ページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-388">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="27d56-389">ASP.NET Core モジュールが、インプロセスまたはアウト プロセスのどちらかの要求ハンドラーの検索に失敗した場合、*500.0 - インプロセス/アウト プロセス ハンドラーの読み込みエラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-389">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="27d56-390">インプロセス ホスティングで、ASP.NET Core モジュールによるアプリの起動が失敗すると、*500.30 - 開始エラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-390">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="27d56-391">アウト プロセス ホスティングで、ASP.NET Core モジュールがバックエンド プロセスの起動に失敗した場合、またはバックエンド プロセスは開始しても構成されているポートでのリッスンに失敗した場合は、*502.5 処理エラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-391">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="27d56-392">このページを抑制して、既定の IIS 5xx 状態コード ページに戻すには、`disableStartUpErrorPage` 属性を使います。</span><span class="sxs-lookup"><span data-stu-id="27d56-392">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="27d56-393">カスタム エラー メッセージの構成方法について詳しくは、「[HTTP エラー \<httpErrors](/iis/configuration/system.webServer/httpErrors/)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="27d56-393">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="27d56-394">ASP.NET Core モジュールが、バックエンド プロセスの起動に失敗した場合、またはバックエンド プロセスは開始しても構成されているポートでのリッスンに失敗した場合は、*502.5 処理エラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-394">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="27d56-395">このページを抑制して、既定の IIS 502 状態コード ページに戻すには、`disableStartUpErrorPage` 属性を使います。</span><span class="sxs-lookup"><span data-stu-id="27d56-395">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="27d56-396">カスタム エラー メッセージの構成方法について詳しくは、「[HTTP エラー \<httpErrors](/iis/configuration/system.webServer/httpErrors/)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="27d56-396">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![502.5 処理エラーの状態コード ページ](aspnet-core-module/_static/ANCM-502_5.png)

::: moniker-end

## <a name="log-creation-and-redirection"></a><span data-ttu-id="27d56-398">ログの作成とリダイレクト</span><span class="sxs-lookup"><span data-stu-id="27d56-398">Log creation and redirection</span></span>

<span data-ttu-id="27d56-399">`aspNetCore` 要素の `stdoutLogEnabled` 属性および `stdoutLogFile` 属性が設定されている場合は、stdout および stderr コンソール出力が ASP.NET Core モジュールによってディスクにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="27d56-399">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="27d56-400">`stdoutLogFile` パスのフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-400">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="27d56-401">アプリ プールは、ログが書き込まれる場所への書き込みアクセス権を持っている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\<app_pool_name>` を使います)。</span><span class="sxs-lookup"><span data-stu-id="27d56-401">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="27d56-402">プロセスのリサイクル/再起動が発生しない場合、ログは循環されません。</span><span class="sxs-lookup"><span data-stu-id="27d56-402">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="27d56-403">ログが使用するディスク領域を制限するのは、ホストの役割です。</span><span class="sxs-lookup"><span data-stu-id="27d56-403">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="27d56-404">stdout ログの使用は、アプリ起動時の問題をトラブルシューティングする場合にのみ推奨されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-404">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="27d56-405">一般的なアプリ ログの目的には、stdout ログを使わないでください。</span><span class="sxs-lookup"><span data-stu-id="27d56-405">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="27d56-406">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="27d56-406">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="27d56-407">詳しくは、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="27d56-407">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="27d56-408">ログ ファイルの作成時には、タイムスタンプとファイルの拡張子が自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-408">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="27d56-409">ログ ファイル名は、タイムスタンプ、プロセス ID、およびファイル拡張子 (*.log*) を `stdoutLogFile` パスの最後のセグメント (通常は *stdout*) にアンダースコアで区切って追加することで構成されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-409">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="27d56-410">`stdoutLogFile` パスが *stdout* で終わっている場合、PID が 1934 で 2018 年 2 月 5 日の 19:42:32 に作成されたアプリのログのファイル名は、*stdout_20180205194132_1934.log* になります。</span><span class="sxs-lookup"><span data-stu-id="27d56-410">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="27d56-411">`stdoutLogEnabled` が false の場合は、アプリの起動時に発生するエラーがキャプチャされ、30 KB までイベント ログに出力されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-411">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="27d56-412">起動後、すべての追加のログが破棄されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-412">After startup, all additional logs are discarded.</span></span>

::: moniker-end

<span data-ttu-id="27d56-413">次の `aspNetCore` 要素の例では、Azure App Service でホストされているアプリの stdout ログを構成しています。</span><span class="sxs-lookup"><span data-stu-id="27d56-413">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="27d56-414">ローカル ログには、ローカル パスまたはネットワーク共有パスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="27d56-414">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="27d56-415">AppPool のユーザー ID に、指定されたパスへの書き込みアクセス許可があることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="27d56-415">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout">
</aspNetCore>
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="27d56-416">強化された診断ログ</span><span class="sxs-lookup"><span data-stu-id="27d56-416">Enhanced diagnostic logs</span></span>

<span data-ttu-id="27d56-417">ASP.NET Core モジュールは、強化された診断ログを提供するよう構成できます。</span><span class="sxs-lookup"><span data-stu-id="27d56-417">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="27d56-418">*web.config* で、`<aspNetCore>` 要素に `<handlerSettings>` 要素を追加します。`debugLevel` を `TRACE` に設定すると、診断情報が再現性の高いものになります。</span><span class="sxs-lookup"><span data-stu-id="27d56-418">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="debugFile" value="aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="27d56-419">デバッグ レベル (`debugLevel`) 値には、レベルと場所の両方を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="27d56-419">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="27d56-420">レベルは次のとおりです (情報量が少ないものから多いものへの順)。</span><span class="sxs-lookup"><span data-stu-id="27d56-420">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="27d56-421">ERROR</span><span class="sxs-lookup"><span data-stu-id="27d56-421">ERROR</span></span>
* <span data-ttu-id="27d56-422">WARNING</span><span class="sxs-lookup"><span data-stu-id="27d56-422">WARNING</span></span>
* <span data-ttu-id="27d56-423">INFO</span><span class="sxs-lookup"><span data-stu-id="27d56-423">INFO</span></span>
* <span data-ttu-id="27d56-424">TRACE</span><span class="sxs-lookup"><span data-stu-id="27d56-424">TRACE</span></span>

<span data-ttu-id="27d56-425">場所は次のとおりです (複数の場所を指定できます)。</span><span class="sxs-lookup"><span data-stu-id="27d56-425">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="27d56-426">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="27d56-426">CONSOLE</span></span>
* <span data-ttu-id="27d56-427">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="27d56-427">EVENTLOG</span></span>
* <span data-ttu-id="27d56-428">ファイル</span><span class="sxs-lookup"><span data-stu-id="27d56-428">FILE</span></span>

<span data-ttu-id="27d56-429">ハンドラー設定は、次の環境変数を使用して指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="27d56-429">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="27d56-430">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; デバッグ ログ ファイルへのパス </span><span class="sxs-lookup"><span data-stu-id="27d56-430">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="27d56-431">(既定: *aspnetcore debug.log*)。</span><span class="sxs-lookup"><span data-stu-id="27d56-431">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="27d56-432">`ASPNETCORE_MODULE_DEBUG` &ndash; デバッグ レベルの設定。</span><span class="sxs-lookup"><span data-stu-id="27d56-432">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="27d56-433">配置内でデバッグ ログを、問題のトラブルシューティングに必要な時間よりも長く有効のままに**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="27d56-433">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="27d56-434">ログのサイズは制限されていません。</span><span class="sxs-lookup"><span data-stu-id="27d56-434">The size of the log isn't limited.</span></span> <span data-ttu-id="27d56-435">デバッグ ログを有効のままにすると、使用可能なディスク領域が使い果たされ、サーバーまたはアプリ サービスがクラッシュする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="27d56-435">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

::: moniker-end

<span data-ttu-id="27d56-436">*web.config* ファイルでの `aspNetCore` 要素の例については、「[web.config での構成](#configuration-with-webconfig)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="27d56-436">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="27d56-437">プロキシの構成で HTTP プロトコルとペアリング トークンを使用する</span><span class="sxs-lookup"><span data-stu-id="27d56-437">Proxy configuration uses HTTP protocol and a pairing token</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="27d56-438">*アウト プロセス ホスティングにのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="27d56-438">*Only applies to out-of-process hosting.*</span></span>

::: moniker-end

<span data-ttu-id="27d56-439">ASP.NET Core モジュールと Kestrel の間に作成されるプロキシは、HTTP プロトコルを使います。</span><span class="sxs-lookup"><span data-stu-id="27d56-439">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="27d56-440">HTTP を使うと、パフォーマンスが最適化されます。この場合、モジュールと Kestrel の間のトラフィックは、ネットワーク インターフェイスから切り離されたループバック アドレスで発生します。</span><span class="sxs-lookup"><span data-stu-id="27d56-440">Using HTTP is a performance optimization, where the traffic between the module and Kestrel takes place on a loopback address off of the network interface.</span></span> <span data-ttu-id="27d56-441">モジュールと Kestrel の間のトラフィックが、サーバーから離れた場所で傍受される危険はありません。</span><span class="sxs-lookup"><span data-stu-id="27d56-441">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="27d56-442">ペアリング トークンを使用すると、Kestrel によって受信される要求が IIS によってプロキシされたものであり、他のソースからのものでないことを保証できます。</span><span class="sxs-lookup"><span data-stu-id="27d56-442">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="27d56-443">ペアリング トークンは、モジュールによって作成されて、環境変数 (`ASPNETCORE_TOKEN`) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-443">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="27d56-444">ペアリング トークンはまた、プロキシされたすべての要求のヘッダー (`MS-ASPNETCORE-TOKEN`) にも設定されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-444">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="27d56-445">IIS ミドルウェアは、受信した各要求をチェックし、ペアリング トークン ヘッダーの値が環境変数の値と一致することを確認します。</span><span class="sxs-lookup"><span data-stu-id="27d56-445">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="27d56-446">トークンの値が一致しない場合、要求はログに記録され、拒否されます。</span><span class="sxs-lookup"><span data-stu-id="27d56-446">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="27d56-447">ペアリング トークン環境変数およびモジュールと Kestrel の間のトラフィックには、サーバーから離れた場所からアクセスすることはできません。</span><span class="sxs-lookup"><span data-stu-id="27d56-447">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="27d56-448">ペアリング トークンの値がわからなければ、攻撃者は IIS ミドルウェアのチェックをバイパスする要求を送信できません。</span><span class="sxs-lookup"><span data-stu-id="27d56-448">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="27d56-449">IIS 共有構成での ASP.NET Core モジュール</span><span class="sxs-lookup"><span data-stu-id="27d56-449">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="27d56-450">ASP.NET Core モジュールのインストーラーは、**TrustedInstaller** アカウントのアクセス許可を使って実行します。</span><span class="sxs-lookup"><span data-stu-id="27d56-450">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="27d56-451">ローカル システム アカウントには、IIS 共有構成によって使われる共有パスに対する変更アクセス許可がないため、インストーラーが共有上の *applicationHost.config* ファイル内のモジュール設定を構成しようとすると、アクセス拒否エラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="27d56-451">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="27d56-452">IIS がインストールされている同じコンピューターで IIS 共有抗生を使用するとき、`OPT_NO_SHARED_CONFIG_CHECK` パラメーターを `1` に設定して ASP.NET Core Hosting Bundle インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="27d56-452">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="27d56-453">共有構成のパスが IIS をインストールしている同じコンピューター上にないときは、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="27d56-453">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="27d56-454">IIS 共有構成を無効にします。</span><span class="sxs-lookup"><span data-stu-id="27d56-454">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="27d56-455">インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="27d56-455">Run the installer.</span></span>
1. <span data-ttu-id="27d56-456">更新された *applicationHost.config* ファイルを共有にエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="27d56-456">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="27d56-457">IIS 共有構成を再び有効にします。</span><span class="sxs-lookup"><span data-stu-id="27d56-457">Re-enable the IIS Shared Configuration.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="27d56-458">IIS 共有構成を使うときは、次の手順で行います。</span><span class="sxs-lookup"><span data-stu-id="27d56-458">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="27d56-459">IIS 共有構成を無効にします。</span><span class="sxs-lookup"><span data-stu-id="27d56-459">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="27d56-460">インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="27d56-460">Run the installer.</span></span>
1. <span data-ttu-id="27d56-461">更新された *applicationHost.config* ファイルを共有にエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="27d56-461">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="27d56-462">IIS 共有構成を再び有効にします。</span><span class="sxs-lookup"><span data-stu-id="27d56-462">Re-enable the IIS Shared Configuration.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="application-initialization"></a><span data-ttu-id="27d56-463">Application Initialization</span><span class="sxs-lookup"><span data-stu-id="27d56-463">Application Initialization</span></span>

<span data-ttu-id="27d56-464">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) は、アプリ プールが開始するときまたはリサイクルされるときに、アプリに HTTP 要求を送信する IIS 機能です。</span><span class="sxs-lookup"><span data-stu-id="27d56-464">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="27d56-465">要求によってアプリの起動がトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="27d56-465">The request triggers the app to start.</span></span> <span data-ttu-id="27d56-466">Application Initialization は、ASP.NET Core モジュール バージョン 2 を備えた[インプロセス ホスティング モデル](xref:fundamentals/servers/index#in-process-hosting-model)、[アウトプロセス ホスティング モデル](xref:fundamentals/servers/index#out-of-process-hosting-model)の両方で使えます。</span><span class="sxs-lookup"><span data-stu-id="27d56-466">Application Initialization can be used by both the [in-process hosting model](xref:fundamentals/servers/index#in-process-hosting-model) and [out-of-process hosting model](xref:fundamentals/servers/index#out-of-process-hosting-model) with the ASP.NET Core Module version 2.</span></span>

<span data-ttu-id="27d56-467">Application Initialization を有効にするには:</span><span class="sxs-lookup"><span data-stu-id="27d56-467">To enable Application Initialization:</span></span>

1. <span data-ttu-id="27d56-468">IIS Application Initialization 役割の機能が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="27d56-468">Confirm that the IIS Application Initialization role feature in enabled:</span></span>
   * <span data-ttu-id="27d56-469">Windows 7 以降の場合:**[コントロール パネル]** > **[プログラム]** > **[プログラムと機能]** > **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="27d56-469">On Windows 7 or later: Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span> <span data-ttu-id="27d56-470">**[インターネット インフォメーション サービス]** > **[World Wide Web サービス]** > **[アプリケーション開発機能]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="27d56-470">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="27d56-471">**[Application Initialization]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="27d56-471">Select the check box for **Application Initialization**.</span></span>
   * <span data-ttu-id="27d56-472">Windows Server 2008 R2 以降の場合は、**[役割と機能の追加ウィザード]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="27d56-472">On Windows Server 2008 R2 or later, open the **Add Roles and Features Wizard**.</span></span> <span data-ttu-id="27d56-473">**[役割サービスの選択]** パネルに移動したら、**[アプリケーション開発]** ノードを開いて、**[Application Initialization]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="27d56-473">When you reach the **Select role services** panel, open the **Application Development** node and select the **Application Initialization** check box.</span></span>
1. <span data-ttu-id="27d56-474">IIS マネージャーで、**[接続]** パネルの **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="27d56-474">In IIS Manager, select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="27d56-475">一覧からそのアプリのアプリ プールを選択します。</span><span class="sxs-lookup"><span data-stu-id="27d56-475">Select the app's app pool in the list.</span></span>
1. <span data-ttu-id="27d56-476">**[アクション]** パネルの **[アプリケーション プールの編集]** で **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="27d56-476">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="27d56-477">**[開始モード]** を **[常時実行]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="27d56-477">Set **Start Mode** to **AlwaysRunning**.</span></span>
1. <span data-ttu-id="27d56-478">**[接続]** パネルの **[サイト]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="27d56-478">Open the **Sites** node in the **Connections** panel.</span></span>
1. <span data-ttu-id="27d56-479">アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="27d56-479">Select the app.</span></span>
1. <span data-ttu-id="27d56-480">**[アクション]** パネルの **[Web サイトの管理]** の下で **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="27d56-480">Select **Advanced Settings** under **Manage Website** in the **Actions** panel.</span></span>
1. <span data-ttu-id="27d56-481">**[有効化されたプリロード]** を **True** に設定します。</span><span class="sxs-lookup"><span data-stu-id="27d56-481">Set **Preload Enabled** to **True**.</span></span>

<span data-ttu-id="27d56-482">詳細については、「[IIS 8.0 Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="27d56-482">For more information, see [IIS 8.0 Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization).</span></span>

<span data-ttu-id="27d56-483">[アウトプロセス ホスティング モデル](xref:fundamentals/servers/index#out-of-process-hosting-model)を使うアプリでは、外部サービスを使い、アプリに定期的に ping を送信して、それを実行させ続ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="27d56-483">Apps that use the [out-of-process hosting model](xref:fundamentals/servers/index#out-of-process-hosting-model) must use an external service to periodically ping the app in order to keep it running.</span></span>

::: moniker-end

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="27d56-484">モジュールのバージョンとホスティング バンドル インストーラーのログ</span><span class="sxs-lookup"><span data-stu-id="27d56-484">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="27d56-485">インストールされている ASP.NET Core モジュールのバージョンを確認するには:</span><span class="sxs-lookup"><span data-stu-id="27d56-485">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="27d56-486">ホスティング システム上で、*%windir%\System32\inetsrv* に移動します。</span><span class="sxs-lookup"><span data-stu-id="27d56-486">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="27d56-487">*aspnetcore.dll* ファイルを探します。</span><span class="sxs-lookup"><span data-stu-id="27d56-487">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="27d56-488">ファイルを右クリックし、コンテキスト メニューの **[プロパティ]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="27d56-488">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="27d56-489">**[詳細]** タブを選びます。**[ファイル バージョン]** と **[製品バージョン]** が、インストールされているモジュールのバージョンを表します。</span><span class="sxs-lookup"><span data-stu-id="27d56-489">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="27d56-490">モジュールのホスティング バンドル インストーラーのログは、*C:\\Users\\%UserName%\\AppData\\Local\\Temp* にあります。ファイルの名前は、*dd_DotNetCoreWinSvrHosting__\<タイムスタンプ>_000_AspNetCoreModule_x64.log* です。</span><span class="sxs-lookup"><span data-stu-id="27d56-490">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="27d56-491">モジュール、スキーマ、構成ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="27d56-491">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="27d56-492">Module</span><span class="sxs-lookup"><span data-stu-id="27d56-492">Module</span></span>

<span data-ttu-id="27d56-493">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="27d56-493">**IIS (x86/amd64):**</span></span>

   * <span data-ttu-id="27d56-494">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="27d56-494">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

   * <span data-ttu-id="27d56-495">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="27d56-495">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="27d56-496">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="27d56-496">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

   * <span data-ttu-id="27d56-497">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="27d56-497">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

<span data-ttu-id="27d56-498">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="27d56-498">**IIS Express (x86/amd64):**</span></span>

   * <span data-ttu-id="27d56-499">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="27d56-499">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

   * <span data-ttu-id="27d56-500">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="27d56-500">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="27d56-501">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="27d56-501">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

   * <span data-ttu-id="27d56-502">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="27d56-502">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

### <a name="schema"></a><span data-ttu-id="27d56-503">Schema</span><span class="sxs-lookup"><span data-stu-id="27d56-503">Schema</span></span>

<span data-ttu-id="27d56-504">**IIS**</span><span class="sxs-lookup"><span data-stu-id="27d56-504">**IIS**</span></span>

   * <span data-ttu-id="27d56-505">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="27d56-505">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="27d56-506">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="27d56-506">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end
<span data-ttu-id="27d56-507">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="27d56-507">**IIS Express**</span></span>

   * <span data-ttu-id="27d56-508">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="27d56-508">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="27d56-509">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="27d56-509">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="27d56-510">構成</span><span class="sxs-lookup"><span data-stu-id="27d56-510">Configuration</span></span>

<span data-ttu-id="27d56-511">**IIS**</span><span class="sxs-lookup"><span data-stu-id="27d56-511">**IIS**</span></span>

   * <span data-ttu-id="27d56-512">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="27d56-512">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="27d56-513">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="27d56-513">**IIS Express**</span></span>

   * <span data-ttu-id="27d56-514">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="27d56-514">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>
   
   * <span data-ttu-id="27d56-515">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="27d56-515">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="27d56-516">ファイルは、*applicationHost.config* ファイルで *aspnetcore* を検索することにより見つかります。</span><span class="sxs-lookup"><span data-stu-id="27d56-516">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="27d56-517">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="27d56-517">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* [<span data-ttu-id="27d56-518">ASP.NET Core モジュールの GitHub リポジトリ (参照元)</span><span class="sxs-lookup"><span data-stu-id="27d56-518">ASP.NET Core Module GitHub repository (reference source)</span></span>](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
