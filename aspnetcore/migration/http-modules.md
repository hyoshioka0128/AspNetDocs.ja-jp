---
title: ASP.NET Core のミドルウェアへの HTTP ハンドラーとモジュールを移行します。
author: rick-anderson
description: ''
ms.author: tdykstra
ms.date: 12/07/2016
uid: migration/http-modules
ms.openlocfilehash: 601b93fb12ab5b37b7d8ad8fd9825accc6e314cd
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57055259"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="44d16-102">ASP.NET Core のミドルウェアへの HTTP ハンドラーとモジュールを移行します。</span><span class="sxs-lookup"><span data-stu-id="44d16-102">Migrate HTTP handlers and modules to ASP.NET Core middleware</span></span>

<span data-ttu-id="44d16-103">によって[Matt Perdeck](https://www.linkedin.com/in/mattperdeck)</span><span class="sxs-lookup"><span data-stu-id="44d16-103">By [Matt Perdeck](https://www.linkedin.com/in/mattperdeck)</span></span>

<span data-ttu-id="44d16-104">この記事では、既存の ASP.NET に移行する方法を示しています。 [HTTP モジュールとハンドラー system.webserver](/iis/configuration/system.webserver/) ASP.NET core[ミドルウェア](xref:fundamentals/middleware/index)します。</span><span class="sxs-lookup"><span data-stu-id="44d16-104">This article shows how to migrate existing ASP.NET [HTTP modules and handlers from system.webserver](/iis/configuration/system.webserver/) to ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="44d16-105">モジュールとハンドラーの再考</span><span class="sxs-lookup"><span data-stu-id="44d16-105">Modules and handlers revisited</span></span>

<span data-ttu-id="44d16-106">ASP.NET Core のミドルウェアを次に進む前にまず局所的 HTTP モジュールとハンドラーの動作方法。</span><span class="sxs-lookup"><span data-stu-id="44d16-106">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![モジュールのハンドラー](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="44d16-108">**ハンドラーは次のとおりです。**</span><span class="sxs-lookup"><span data-stu-id="44d16-108">**Handlers are:**</span></span>

   * <span data-ttu-id="44d16-109">実装するクラス[IHttpHandler](/dotnet/api/system.web.ihttphandler)</span><span class="sxs-lookup"><span data-stu-id="44d16-109">Classes that implement [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span></span>

   * <span data-ttu-id="44d16-110">指定されたファイル名または拡張子で要求を処理するために使用*レポート*</span><span class="sxs-lookup"><span data-stu-id="44d16-110">Used to handle requests with a given file name or extension, such as *.report*</span></span>

   * <span data-ttu-id="44d16-111">[構成されている](/iis/configuration/system.webserver/handlers/)で*Web.config*</span><span class="sxs-lookup"><span data-stu-id="44d16-111">[Configured](/iis/configuration/system.webserver/handlers/) in *Web.config*</span></span>

<span data-ttu-id="44d16-112">**モジュールは次のとおりです。**</span><span class="sxs-lookup"><span data-stu-id="44d16-112">**Modules are:**</span></span>

   * <span data-ttu-id="44d16-113">実装するクラス[IHttpModule](/dotnet/api/system.web.ihttpmodule)</span><span class="sxs-lookup"><span data-stu-id="44d16-113">Classes that implement [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span></span>

   * <span data-ttu-id="44d16-114">すべての要求に対して呼び出されます</span><span class="sxs-lookup"><span data-stu-id="44d16-114">Invoked for every request</span></span>

   * <span data-ttu-id="44d16-115">(さらに処理の停止の要求) をショート サーキットすること</span><span class="sxs-lookup"><span data-stu-id="44d16-115">Able to short-circuit (stop further processing of a request)</span></span>

   * <span data-ttu-id="44d16-116">HTTP 応答に追加したり、独自に作成できません。</span><span class="sxs-lookup"><span data-stu-id="44d16-116">Able to add to the HTTP response, or create their own</span></span>

   * <span data-ttu-id="44d16-117">[構成されている](/iis/configuration/system.webserver/modules/)で*Web.config*</span><span class="sxs-lookup"><span data-stu-id="44d16-117">[Configured](/iis/configuration/system.webserver/modules/) in *Web.config*</span></span>

<span data-ttu-id="44d16-118">**モジュールが受信要求を処理する順序は、によって決まります。**</span><span class="sxs-lookup"><span data-stu-id="44d16-118">**The order in which modules process incoming requests is determined by:**</span></span>

   1. <span data-ttu-id="44d16-119">[アプリケーションのライフ サイクル](https://msdn.microsoft.com/library/ms227673.aspx)、ASP.NET によって発生した一連のイベントであります。[BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest)、 [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest)など。各モジュールには、1 つまたは複数のイベントのハンドラーを作成できます。</span><span class="sxs-lookup"><span data-stu-id="44d16-119">The [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx), which is a series events fired by ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Each module can create a handler for one or more events.</span></span>

   2. <span data-ttu-id="44d16-120">同一のイベントで構成されている順序*Web.config*します。</span><span class="sxs-lookup"><span data-stu-id="44d16-120">For the same event, the order in which they're configured in *Web.config*.</span></span>

<span data-ttu-id="44d16-121">ライフ サイクル イベントのハンドラーを追加するだけでなく、モジュール、 *Global.asax.cs*ファイル。</span><span class="sxs-lookup"><span data-stu-id="44d16-121">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="44d16-122">これらのハンドラーは、構成済みのモジュール内のハンドラーの後に実行します。</span><span class="sxs-lookup"><span data-stu-id="44d16-122">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="44d16-123">ハンドラーとモジュールからミドルウェアへ</span><span class="sxs-lookup"><span data-stu-id="44d16-123">From handlers and modules to middleware</span></span>

<span data-ttu-id="44d16-124">**ミドルウェアは、HTTP モジュールとハンドラーよりも簡単です。**</span><span class="sxs-lookup"><span data-stu-id="44d16-124">**Middleware are simpler than HTTP modules and handlers:**</span></span>

   * <span data-ttu-id="44d16-125">モジュール、ハンドラー、 *Global.asax.cs*、 *Web.config* (IIS の構成) を除くアプリケーションのライフ サイクルがなくなり、</span><span class="sxs-lookup"><span data-stu-id="44d16-125">Modules, handlers, *Global.asax.cs*, *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

   * <span data-ttu-id="44d16-126">ミドルウェアによって作成されたモジュールとハンドラーの両方のロール</span><span class="sxs-lookup"><span data-stu-id="44d16-126">The roles of both modules and handlers have been taken over by middleware</span></span>

   * <span data-ttu-id="44d16-127">ミドルウェアがコードを使用して構成されているのではなく*Web.config*</span><span class="sxs-lookup"><span data-stu-id="44d16-127">Middleware are configured using code rather than in *Web.config*</span></span>

   * <span data-ttu-id="44d16-128">[パイプラインを分岐](xref:fundamentals/middleware/index#use-run-and-map)要求ヘッダー、クエリ文字列などでも、URL だけでなくに基づいて、特定のミドルウェアに要求を送信することができます。</span><span class="sxs-lookup"><span data-stu-id="44d16-128">[Pipeline branching](xref:fundamentals/middleware/index#use-run-and-map) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

<span data-ttu-id="44d16-129">**ミドルウェアは、モジュールとよく似ています。**</span><span class="sxs-lookup"><span data-stu-id="44d16-129">**Middleware are very similar to modules:**</span></span>

   * <span data-ttu-id="44d16-130">基本的にすべての要求に対して呼び出されます</span><span class="sxs-lookup"><span data-stu-id="44d16-130">Invoked in principle for every request</span></span>

   * <span data-ttu-id="44d16-131">によって、要求をショート サーキットできなければ[要求を次のミドルウェアに渡されていません。](#http-modules-shortcircuiting-middleware)</span><span class="sxs-lookup"><span data-stu-id="44d16-131">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

   * <span data-ttu-id="44d16-132">独自の HTTP 応答を作成できません。</span><span class="sxs-lookup"><span data-stu-id="44d16-132">Able to create their own HTTP response</span></span>

<span data-ttu-id="44d16-133">**ミドルウェアとモジュールは、別の順序で処理されます。**</span><span class="sxs-lookup"><span data-stu-id="44d16-133">**Middleware and modules are processed in a different order:**</span></span>

   * <span data-ttu-id="44d16-134">ミドルウェアの順序が順番に挿入する要求パイプラインでは、モジュールの順序は主に基づいて順序に基づいて[アプリケーションのライフ サイクル](https://msdn.microsoft.com/library/ms227673.aspx)イベント</span><span class="sxs-lookup"><span data-stu-id="44d16-134">Order of middleware is based on the order in which they're inserted into the request pipeline, while order of modules is mainly based on [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx) events</span></span>

   * <span data-ttu-id="44d16-135">応答のミドルウェアの順序は、要求から逆モジュールの順序は要求と応答の同じ</span><span class="sxs-lookup"><span data-stu-id="44d16-135">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

   * <span data-ttu-id="44d16-136">参照してください[IApplicationBuilder を使用したミドルウェア パイプラインを作成します。](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span><span class="sxs-lookup"><span data-stu-id="44d16-136">See [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![ミドルウェア](http-modules/_static/middleware.png)

<span data-ttu-id="44d16-138">認証ミドルウェア サーキット要求上の図にする方法に注意してください。</span><span class="sxs-lookup"><span data-stu-id="44d16-138">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="44d16-139">ミドルウェアへのモジュール コードの移行</span><span class="sxs-lookup"><span data-stu-id="44d16-139">Migrating module code to middleware</span></span>

<span data-ttu-id="44d16-140">既存の HTTP モジュールは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="44d16-140">An existing HTTP module will look similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

<span data-ttu-id="44d16-141">ように、[ミドルウェア](xref:fundamentals/middleware/index) ページで、ASP.NET Core のミドルウェアが公開するクラス、`Invoke`メソッドを`HttpContext`を返すと、`Task`します。</span><span class="sxs-lookup"><span data-stu-id="44d16-141">As shown in the [Middleware](xref:fundamentals/middleware/index) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="44d16-142">新しいミドルウェアは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="44d16-142">Your new middleware will look like this:</span></span>

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

<span data-ttu-id="44d16-143">上記のミドルウェアのテンプレート セクションから引用したもの[ミドルウェアを記述](xref:fundamentals/middleware/write)します。</span><span class="sxs-lookup"><span data-stu-id="44d16-143">The preceding middleware template was taken from the section on [writing middleware](xref:fundamentals/middleware/write).</span></span>

<span data-ttu-id="44d16-144">*MyMiddlewareExtensions*ヘルパー クラスでミドルウェアを構成しやすい、`Startup`クラス。</span><span class="sxs-lookup"><span data-stu-id="44d16-144">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="44d16-145">`UseMyMiddleware`メソッドは、要求パイプラインにミドルウェア クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="44d16-145">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="44d16-146">ミドルウェアによって必要なサービスは、ミドルウェアのコンス トラクターで挿入を取得します。</span><span class="sxs-lookup"><span data-stu-id="44d16-146">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name="http-modules-shortcircuiting-middleware"></a>

<span data-ttu-id="44d16-147">モジュール例では、ユーザーが許可されていない場合、要求が終了する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="44d16-147">Your module might terminate a request, for example if the user isn't authorized:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

<span data-ttu-id="44d16-148">ミドルウェアがこれを呼び出さなかったことで処理`Invoke`パイプラインの次のミドルウェアにします。</span><span class="sxs-lookup"><span data-stu-id="44d16-148">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="44d16-149">前のミドルウェアも応答するパイプラインを遡って、その方法とが呼び出されるために、要求を終了これは完全に留意してください。</span><span class="sxs-lookup"><span data-stu-id="44d16-149">Keep in mind that this doesn't fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

<span data-ttu-id="44d16-150">新しいミドルウェアにモジュールの機能を移行する場合ために、コードをコンパイルできないことを見つけることがあります、`HttpContext`クラスは、ASP.NET Core では大幅に変更します。</span><span class="sxs-lookup"><span data-stu-id="44d16-150">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="44d16-151">[後で](#migrating-to-the-new-httpcontext)、新しい ASP.NET Core HttpContext に移行する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="44d16-151">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="44d16-152">要求パイプラインに移行するモジュールの挿入</span><span class="sxs-lookup"><span data-stu-id="44d16-152">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="44d16-153">HTTP モジュールは通常、要求パイプラインを使用する追加*Web.config*:</span><span class="sxs-lookup"><span data-stu-id="44d16-153">HTTP modules are typically added to the request pipeline using *Web.config*:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

<span data-ttu-id="44d16-154">これによって変換[新しいミドルウェアを追加する](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)で要求パイプラインに、`Startup`クラス。</span><span class="sxs-lookup"><span data-stu-id="44d16-154">Convert this by [adding your new middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

<span data-ttu-id="44d16-155">新しいミドルウェアを挿入するパイプラインで正確なスポットをモジュールとして処理するイベントによって異なります (`BeginRequest`、`EndRequest`など) とその順序でモジュールの一覧に*Web.config*します。</span><span class="sxs-lookup"><span data-stu-id="44d16-155">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config*.</span></span>

<span data-ttu-id="44d16-156">前と同じ説明するがありますはなくアプリケーションのライフ サイクル ASP.NET Core で、応答がミドルウェアによって処理される順序は、モジュールによって使用される順序によって異なります。</span><span class="sxs-lookup"><span data-stu-id="44d16-156">As previously stated, there's no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="44d16-157">これでしたかを決定順序付けより困難になります。</span><span class="sxs-lookup"><span data-stu-id="44d16-157">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="44d16-158">順序付けには、問題になると、モジュールを個別に注文する複数のミドルウェア コンポーネントに分割できます。</span><span class="sxs-lookup"><span data-stu-id="44d16-158">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="44d16-159">ミドルウェアへハンドラー コードの移行</span><span class="sxs-lookup"><span data-stu-id="44d16-159">Migrating handler code to middleware</span></span>

<span data-ttu-id="44d16-160">HTTP ハンドラーでは、このようなものはなります。</span><span class="sxs-lookup"><span data-stu-id="44d16-160">An HTTP handler looks something like this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

<span data-ttu-id="44d16-161">ASP.NET Core プロジェクトで、次のようなミドルウェアをこの変換は。</span><span class="sxs-lookup"><span data-stu-id="44d16-161">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

<span data-ttu-id="44d16-162">このミドルウェアは、モジュールに対応するミドルウェアによく似ています。</span><span class="sxs-lookup"><span data-stu-id="44d16-162">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="44d16-163">唯一の違いはここで呼び出しがない`_next.Invoke(context)`します。</span><span class="sxs-lookup"><span data-stu-id="44d16-163">The only real difference is that here there's no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="44d16-164">ハンドラーは、要求パイプラインの最後があるため次のミドルウェアを呼び出すため、理にかなっています。</span><span class="sxs-lookup"><span data-stu-id="44d16-164">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="44d16-165">要求パイプラインに移行するハンドラーの挿入</span><span class="sxs-lookup"><span data-stu-id="44d16-165">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="44d16-166">HTTP ハンドラーの構成は*Web.config*次のようになります。</span><span class="sxs-lookup"><span data-stu-id="44d16-166">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

<span data-ttu-id="44d16-167">新しいハンドラー ミドルウェアをパイプラインでは、要求に追加することで、これを変換することも、`Startup`クラス、モジュールから変換されたミドルウェアに似ています。</span><span class="sxs-lookup"><span data-stu-id="44d16-167">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="44d16-168">このアプローチの問題は、新しいハンドラー ミドルウェアにすべての要求が送信します。</span><span class="sxs-lookup"><span data-stu-id="44d16-168">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="44d16-169">ただし、特定の拡張機能とミドルウェアに要求を送信するのみ。</span><span class="sxs-lookup"><span data-stu-id="44d16-169">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="44d16-170">与えられます、同じ機能が、HTTP ハンドラーを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="44d16-170">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="44d16-171">1 つのソリューションは、特定の拡張機能の要求のパイプラインを分岐するのを使用して、`MapWhen`拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="44d16-171">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="44d16-172">同じで、これを行う`Configure`メソッドは、その他のミドルウェアを追加します。</span><span class="sxs-lookup"><span data-stu-id="44d16-172">You do this in the same `Configure` method where you add the other middleware:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

<span data-ttu-id="44d16-173">`MapWhen` これらのパラメーターを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="44d16-173">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="44d16-174">受け取るラムダ、`HttpContext`返します`true`要求する必要があります、分岐がダウンした場合。</span><span class="sxs-lookup"><span data-stu-id="44d16-174">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="44d16-175">これは、要求だけでなく、その拡張機能が、要求ヘッダー、クエリ文字列パラメーターなどを基に分岐することを意味します。</span><span class="sxs-lookup"><span data-stu-id="44d16-175">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="44d16-176">受け取るラムダ、`IApplicationBuilder`分岐のすべてのミドルウェアを追加します。</span><span class="sxs-lookup"><span data-stu-id="44d16-176">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="44d16-177">つまり、ハンドラー ミドルウェアの前に、分岐に追加のミドルウェアを追加できます。</span><span class="sxs-lookup"><span data-stu-id="44d16-177">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="44d16-178">ミドルウェアは、すべての要求に、分岐が呼び出される前に、パイプラインに追加分岐影響がないにします。</span><span class="sxs-lookup"><span data-stu-id="44d16-178">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="44d16-179">読み込みオプション パターンを使用してミドルウェア オプション</span><span class="sxs-lookup"><span data-stu-id="44d16-179">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="44d16-180">格納されている構成オプションがあるいくつかのモジュールとハンドラー *Web.config*します。ただし、ASP.NET Core で新しい構成モデルを使用の代わりに*Web.config*します。</span><span class="sxs-lookup"><span data-stu-id="44d16-180">Some modules and handlers have configuration options that are stored in *Web.config*. However, in ASP.NET Core a new configuration model is used in place of *Web.config*.</span></span>

<span data-ttu-id="44d16-181">新しい[構成システム](xref:fundamentals/configuration/index)これを解決するオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="44d16-181">The new [configuration system](xref:fundamentals/configuration/index) gives you these options to solve this:</span></span>

* <span data-ttu-id="44d16-182">ように、ミドルウェアにオプションを直接挿入、[次のセクション](#loading-middleware-options-through-direct-injection)します。</span><span class="sxs-lookup"><span data-stu-id="44d16-182">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="44d16-183">使用して、[オプション パターン](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="44d16-183">Use the [options pattern](xref:fundamentals/configuration/options):</span></span>

1. <span data-ttu-id="44d16-184">たとえば、ミドルウェアのオプションを保持するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="44d16-184">Create a class to hold your middleware options, for example:</span></span>

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. <span data-ttu-id="44d16-185">オプションの値を格納します。</span><span class="sxs-lookup"><span data-stu-id="44d16-185">Store the option values</span></span>

   <span data-ttu-id="44d16-186">構成システム オプション任意の場所を求める値を格納することができます。</span><span class="sxs-lookup"><span data-stu-id="44d16-186">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="44d16-187">ただし、使用を最もサイト*appsettings.json*ので、その方法を詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="44d16-187">However, most sites use *appsettings.json*, so we'll take that approach:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   <span data-ttu-id="44d16-188">*MyMiddlewareOptionsSection*セクション名を次に示します。</span><span class="sxs-lookup"><span data-stu-id="44d16-188">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="44d16-189">オプション クラスの名前と同じである必要はありません。</span><span class="sxs-lookup"><span data-stu-id="44d16-189">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="44d16-190">オプション クラスを使用して、オプション値を関連付ける</span><span class="sxs-lookup"><span data-stu-id="44d16-190">Associate the option values with the options class</span></span>

    <span data-ttu-id="44d16-191">オプション パターンでは、ASP.NET Core の依存関係挿入フレームワークを使用して、オプションの種類を関連付けます (など`MyMiddlewareOptions`) で、`MyMiddlewareOptions`を実際のオプションを持つオブジェクト。</span><span class="sxs-lookup"><span data-stu-id="44d16-191">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="44d16-192">更新プログラム、`Startup`クラス。</span><span class="sxs-lookup"><span data-stu-id="44d16-192">Update your `Startup` class:</span></span>

   1. <span data-ttu-id="44d16-193">使用している場合*appsettings.json*、ビルダーでは、構成に追加して、`Startup`コンス トラクター。</span><span class="sxs-lookup"><span data-stu-id="44d16-193">If you're using *appsettings.json*, add it to the configuration builder in the `Startup` constructor:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. <span data-ttu-id="44d16-194">オプションのサービスを構成します。</span><span class="sxs-lookup"><span data-stu-id="44d16-194">Configure the options service:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. <span data-ttu-id="44d16-195">オプション クラスを含む、オプションを関連付けます。</span><span class="sxs-lookup"><span data-stu-id="44d16-195">Associate your options with your options class:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. <span data-ttu-id="44d16-196">ミドルウェアのコンス トラクターにオプションを挿入します。</span><span class="sxs-lookup"><span data-stu-id="44d16-196">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="44d16-197">これは、オプションは、コント ローラーに挿入することに似ています。</span><span class="sxs-lookup"><span data-stu-id="44d16-197">This is similar to injecting options into a controller.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   <span data-ttu-id="44d16-198">[UseMiddleware](#http-modules-usemiddleware)にミドルウェアを追加する拡張メソッド、`IApplicationBuilder`依存関係の挿入を行います。</span><span class="sxs-lookup"><span data-stu-id="44d16-198">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

   <span data-ttu-id="44d16-199">これに限定されません`IOptions`オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="44d16-199">This isn't limited to `IOptions` objects.</span></span> <span data-ttu-id="44d16-200">ミドルウェアを必要とするその他のオブジェクトには、この方法を挿入できます。</span><span class="sxs-lookup"><span data-stu-id="44d16-200">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="44d16-201">直接挿入を介してミドルウェアのオプションの読み込み</span><span class="sxs-lookup"><span data-stu-id="44d16-201">Loading middleware options through direct injection</span></span>

<span data-ttu-id="44d16-202">オプション パターンでは、疎結合オプションの値と、コンシューマーが作成するという利点があります。</span><span class="sxs-lookup"><span data-stu-id="44d16-202">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="44d16-203">オプション クラスは、実際のオプション値に関連した、その他のクラスは、依存関係挿入フレームワークを通じてオプションへのアクセスを取得できます。</span><span class="sxs-lookup"><span data-stu-id="44d16-203">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="44d16-204">オプションの値を渡す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="44d16-204">There's no need to pass around options values.</span></span>

<span data-ttu-id="44d16-205">これは、分割がさまざまなオプションを 2 回、同じミドルウェアを使用する場合。</span><span class="sxs-lookup"><span data-stu-id="44d16-205">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="44d16-206">たとえば、承認ミドルウェアさまざまなロールを許可する別の分岐で使用します。</span><span class="sxs-lookup"><span data-stu-id="44d16-206">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="44d16-207">1 つのオプション クラスを使用して、2 つの異なるオプション オブジェクトを関連付けることはできません。</span><span class="sxs-lookup"><span data-stu-id="44d16-207">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="44d16-208">解決する実際のオプションの値とオプション オブジェクトを取得するには、`Startup`クラスし、ミドルウェアの各インスタンスに直接渡します。</span><span class="sxs-lookup"><span data-stu-id="44d16-208">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1. <span data-ttu-id="44d16-209">2 番目のキーを追加*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="44d16-209">Add a second key to *appsettings.json*</span></span>

   <span data-ttu-id="44d16-210">オプションの 2 番目のセットを追加する、 *appsettings.json*ファイルを一意に識別できる、新しいキーを使用します。</span><span class="sxs-lookup"><span data-stu-id="44d16-210">To add a second set of options to the *appsettings.json* file, use a new key to uniquely identify it:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. <span data-ttu-id="44d16-211">オプションの値を取得し、ミドルウェアに渡します。</span><span class="sxs-lookup"><span data-stu-id="44d16-211">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="44d16-212">`Use...`拡張メソッド (これは、パイプラインにミドルウェアを追加します) は、オプション値を渡す論理的な場所。</span><span class="sxs-lookup"><span data-stu-id="44d16-212">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. <span data-ttu-id="44d16-213">オプションのパラメーターを受け取るミドルウェアを有効にします。</span><span class="sxs-lookup"><span data-stu-id="44d16-213">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="44d16-214">オーバー ロードを提供、`Use...`拡張メソッド (オプションのパラメーターを受け取るしに渡されますを`UseMiddleware`)。</span><span class="sxs-lookup"><span data-stu-id="44d16-214">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="44d16-215">ときに`UseMiddleware`呼びますパラメーターを持つパラメーターに渡す、ミドルウェア コンス トラクター ミドルウェア オブジェクトをインスタンス化時にします。</span><span class="sxs-lookup"><span data-stu-id="44d16-215">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   <span data-ttu-id="44d16-216">これでオプションのオブジェクトの折り返し方法に注意してください、`OptionsWrapper`オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="44d16-216">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="44d16-217">これを実装して`IOptions`ミドルウェア コンス トラクターの期待どおりに、します。</span><span class="sxs-lookup"><span data-stu-id="44d16-217">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="44d16-218">新しい HttpContext への移行</span><span class="sxs-lookup"><span data-stu-id="44d16-218">Migrating to the new HttpContext</span></span>

<span data-ttu-id="44d16-219">先ほど見たを`Invoke`ミドルウェアでメソッドが型のパラメーターを受け取る`HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="44d16-219">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="44d16-220">`HttpContext` ASP.NET Core で大幅に変更されました。</span><span class="sxs-lookup"><span data-stu-id="44d16-220">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="44d16-221">このセクションの最もよく使用されるプロパティを変換する方法を示しています。 [System.Web.HttpContext](/dotnet/api/system.web.httpcontext)を新しい`Microsoft.AspNetCore.Http.HttpContext`します。</span><span class="sxs-lookup"><span data-stu-id="44d16-221">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="44d16-222">HttpContext</span><span class="sxs-lookup"><span data-stu-id="44d16-222">HttpContext</span></span>

<span data-ttu-id="44d16-223">**HttpContext.Items**に変換されます。</span><span class="sxs-lookup"><span data-stu-id="44d16-223">**HttpContext.Items** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

<span data-ttu-id="44d16-224">**一意の要求 ID (System.Web.HttpContext が認識されていません)**</span><span class="sxs-lookup"><span data-stu-id="44d16-224">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="44d16-225">うえで、一意の id の各要求。</span><span class="sxs-lookup"><span data-stu-id="44d16-225">Gives you a unique id for each request.</span></span> <span data-ttu-id="44d16-226">ログに含める非常に便利です。</span><span class="sxs-lookup"><span data-stu-id="44d16-226">Very useful to include in your logs.</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a><span data-ttu-id="44d16-227">HttpContext.Request</span><span class="sxs-lookup"><span data-stu-id="44d16-227">HttpContext.Request</span></span>

<span data-ttu-id="44d16-228">**HttpContext.Request.HttpMethod**に変換されます。</span><span class="sxs-lookup"><span data-stu-id="44d16-228">**HttpContext.Request.HttpMethod** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

<span data-ttu-id="44d16-229">**HttpContext.Request.QueryString**に変換されます。</span><span class="sxs-lookup"><span data-stu-id="44d16-229">**HttpContext.Request.QueryString** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

<span data-ttu-id="44d16-230">**HttpContext.Request.Url**と**HttpContext.Request.RawUrl**に変換します。</span><span class="sxs-lookup"><span data-stu-id="44d16-230">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

<span data-ttu-id="44d16-231">**HttpContext.Request.IsSecureConnection**に変換されます。</span><span class="sxs-lookup"><span data-stu-id="44d16-231">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

<span data-ttu-id="44d16-232">**HttpContext.Request.UserHostAddress**に変換されます。</span><span class="sxs-lookup"><span data-stu-id="44d16-232">**HttpContext.Request.UserHostAddress** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

<span data-ttu-id="44d16-233">**HttpContext.Request.Cookies**に変換されます。</span><span class="sxs-lookup"><span data-stu-id="44d16-233">**HttpContext.Request.Cookies** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

<span data-ttu-id="44d16-234">**HttpContext.Request.RequestContext.RouteData** translates to:</span><span class="sxs-lookup"><span data-stu-id="44d16-234">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

<span data-ttu-id="44d16-235">**HttpContext.Request.Headers**に変換されます。</span><span class="sxs-lookup"><span data-stu-id="44d16-235">**HttpContext.Request.Headers** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

<span data-ttu-id="44d16-236">**HttpContext.Request.UserAgent**に変換されます。</span><span class="sxs-lookup"><span data-stu-id="44d16-236">**HttpContext.Request.UserAgent** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

<span data-ttu-id="44d16-237">**HttpContext.Request.UrlReferrer**に変換されます。</span><span class="sxs-lookup"><span data-stu-id="44d16-237">**HttpContext.Request.UrlReferrer** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

<span data-ttu-id="44d16-238">**HttpContext.Request.ContentType**に変換されます。</span><span class="sxs-lookup"><span data-stu-id="44d16-238">**HttpContext.Request.ContentType** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

<span data-ttu-id="44d16-239">**HttpContext.Request.Form**に変換されます。</span><span class="sxs-lookup"><span data-stu-id="44d16-239">**HttpContext.Request.Form** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> <span data-ttu-id="44d16-240">コンテンツのサブタイプの場合にのみ、フォームの値を読み取る*x-www-form-urlencoded*または*フォーム データ*します。</span><span class="sxs-lookup"><span data-stu-id="44d16-240">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data*.</span></span>

<span data-ttu-id="44d16-241">**HttpContext.Request.InputStream**に変換されます。</span><span class="sxs-lookup"><span data-stu-id="44d16-241">**HttpContext.Request.InputStream** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> <span data-ttu-id="44d16-242">パイプラインの最後に、ハンドラーの型ミドルウェアでのみ、このコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="44d16-242">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="44d16-243">要求あたり 1 回だけ上に示す未加工の本文を読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="44d16-243">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="44d16-244">最初の読み込み後に、本文の読み取りを試みるミドルウェアでは、空の本文を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="44d16-244">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="44d16-245">これは、バッファーから行うことがあるため、前述のように、フォームの読み取りに適用されません。</span><span class="sxs-lookup"><span data-stu-id="44d16-245">This doesn't apply to reading a form as shown earlier, because that's done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="44d16-246">HttpContext.Response</span><span class="sxs-lookup"><span data-stu-id="44d16-246">HttpContext.Response</span></span>

<span data-ttu-id="44d16-247">**HttpContext.Response.Status**と**HttpContext.Response.StatusDescription**に変換します。</span><span class="sxs-lookup"><span data-stu-id="44d16-247">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

<span data-ttu-id="44d16-248">**HttpContext.Response.ContentEncoding**と**HttpContext.Response.ContentType**に変換します。</span><span class="sxs-lookup"><span data-stu-id="44d16-248">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

<span data-ttu-id="44d16-249">**HttpContext.Response.ContentType**で独自にも変換します。</span><span class="sxs-lookup"><span data-stu-id="44d16-249">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

<span data-ttu-id="44d16-250">**HttpContext.Response.Output**に変換されます。</span><span class="sxs-lookup"><span data-stu-id="44d16-250">**HttpContext.Response.Output** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

<span data-ttu-id="44d16-251">**HttpContext.Response.TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="44d16-251">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="44d16-252">については、ファイルを提供している[ここ](../fundamentals/request-features.md#middleware-and-request-features)します。</span><span class="sxs-lookup"><span data-stu-id="44d16-252">Serving up a file is discussed [here](../fundamentals/request-features.md#middleware-and-request-features).</span></span>

<span data-ttu-id="44d16-253">**HttpContext.Response.Headers**</span><span class="sxs-lookup"><span data-stu-id="44d16-253">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="44d16-254">応答ヘッダーを送信するという事実の影響は複雑になります、設定した場合に何かが応答本文に書き込まれた後、それらは送信されません。</span><span class="sxs-lookup"><span data-stu-id="44d16-254">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="44d16-255">ソリューションでは、右、応答が開始に書き込む前に呼び出されるコールバック メソッドを設定します。</span><span class="sxs-lookup"><span data-stu-id="44d16-255">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="44d16-256">先頭にこれは、最適な`Invoke`ミドルウェア内のメソッド。</span><span class="sxs-lookup"><span data-stu-id="44d16-256">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="44d16-257">このコールバック メソッド、応答ヘッダーを設定することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="44d16-257">It's this callback method that sets your response headers.</span></span>

<span data-ttu-id="44d16-258">次のコードが呼び出されるコールバック メソッドを設定`SetHeaders`:</span><span class="sxs-lookup"><span data-stu-id="44d16-258">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="44d16-259">`SetHeaders`コールバック メソッドに次のようになります。</span><span class="sxs-lookup"><span data-stu-id="44d16-259">The `SetHeaders` callback method would look like this:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

<span data-ttu-id="44d16-260">**HttpContext.Response.Cookies**</span><span class="sxs-lookup"><span data-stu-id="44d16-260">**HttpContext.Response.Cookies**</span></span>

<span data-ttu-id="44d16-261">ブラウザーに cookie の送信、 *Set-cookie*応答ヘッダー。</span><span class="sxs-lookup"><span data-stu-id="44d16-261">Cookies travel to the browser in a *Set-Cookie* response header.</span></span> <span data-ttu-id="44d16-262">結果として、cookie を送信する必要があります使用したのと同じコールバック応答ヘッダーを送信するため。</span><span class="sxs-lookup"><span data-stu-id="44d16-262">As a result, sending cookies requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="44d16-263">`SetCookies`コールバック メソッドに次のようになります。</span><span class="sxs-lookup"><span data-stu-id="44d16-263">The `SetCookies` callback method would look like the following:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a><span data-ttu-id="44d16-264">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="44d16-264">Additional resources</span></span>

* [<span data-ttu-id="44d16-265">HTTP ハンドラーと HTTP モジュールの概要</span><span class="sxs-lookup"><span data-stu-id="44d16-265">HTTP Handlers and HTTP Modules Overview</span></span>](/iis/configuration/system.webserver/)
* [<span data-ttu-id="44d16-266">構成</span><span class="sxs-lookup"><span data-stu-id="44d16-266">Configuration</span></span>](xref:fundamentals/configuration/index)
* [<span data-ttu-id="44d16-267">アプリケーションの起動</span><span class="sxs-lookup"><span data-stu-id="44d16-267">Application Startup</span></span>](xref:fundamentals/startup)
* [<span data-ttu-id="44d16-268">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="44d16-268">Middleware</span></span>](xref:fundamentals/middleware/index)
