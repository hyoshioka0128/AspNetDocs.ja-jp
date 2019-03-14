---
title: ASP.NET Core のミドルウェア
author: rick-anderson
description: ASP.NET Core のミドルウェアと要求パイプラインについて説明します。
ms.author: riande
ms.custom: mvc
ms.date: 02/17/2019
uid: fundamentals/middleware/index
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="43c76-103">ASP.NET Core のミドルウェア</span><span class="sxs-lookup"><span data-stu-id="43c76-103">ASP.NET Core Middleware</span></span>

<span data-ttu-id="43c76-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="43c76-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="43c76-105">ミドルウェアとは、要求と応答を処理するために、アプリのパイプラインに組み込まれたソフトウェアです。</span><span class="sxs-lookup"><span data-stu-id="43c76-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="43c76-106">各コンポーネントで次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="43c76-106">Each component:</span></span>

* <span data-ttu-id="43c76-107">要求をパイプライン内の次のコンポーネントに渡すかどうかを選択します。</span><span class="sxs-lookup"><span data-stu-id="43c76-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="43c76-108">パイプラインの次のコンポーネントの前または後に作業を実行できます。</span><span class="sxs-lookup"><span data-stu-id="43c76-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="43c76-109">要求デリゲートは、要求パイプラインの構築に使用されます。</span><span class="sxs-lookup"><span data-stu-id="43c76-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="43c76-110">要求デリゲートは、各 HTTP 要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="43c76-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="43c76-111">要求デリゲートは、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>、<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>、<xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> の各拡張メソッドを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="43c76-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="43c76-112">個々の要求デリゲートは、匿名メソッドとしてインラインで指定するか (インライン ミドルウェアと呼ばれます)、または再利用可能なクラスで定義することができます。</span><span class="sxs-lookup"><span data-stu-id="43c76-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="43c76-113">これらの再利用可能なクラスとインラインの匿名メソッドは、"*ミドルウェア*" です。"*ミドルウェア コンポーネント*" とも呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="43c76-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="43c76-114">要求パイプライン内の各ミドルウェア コンポーネントは、パイプラインの次のコンポーネントを呼び出すか、パイプラインをショートさせます。</span><span class="sxs-lookup"><span data-stu-id="43c76-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="43c76-115">ショートサーキットしたミドルウェアは "*ターミナル ミドルウェア*" と呼ばれます。これによってさらなるミドルウェアによる要求の処理が回避されるためです。</span><span class="sxs-lookup"><span data-stu-id="43c76-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="43c76-116">「<xref:migration/http-modules>」では、ASP.NET Core と ASP.NET 4.x の要求パイプラインの違いについての説明と、他のミドルウェア サンプルが提供されています。</span><span class="sxs-lookup"><span data-stu-id="43c76-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides more middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="43c76-117">IApplicationBuilder を使用したミドルウェア パイプラインの作成</span><span class="sxs-lookup"><span data-stu-id="43c76-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="43c76-118">ASP.NET Core 要求パイプラインは、順番に呼び出される一連の要求デリゲートで構成されています。</span><span class="sxs-lookup"><span data-stu-id="43c76-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="43c76-119">次の図は、その概念を示しています。</span><span class="sxs-lookup"><span data-stu-id="43c76-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="43c76-120">実行のスレッドは黒い矢印をたどります。</span><span class="sxs-lookup"><span data-stu-id="43c76-120">The thread of execution follows the black arrows.</span></span>

![要求の到着、3 つのミドルウェアによる処理、アプリからの応答の送信を示す要求処理パターン。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="43c76-124">各デリゲートは、次のデリゲートの前と後に操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="43c76-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="43c76-125">例外処理デリゲートは、パイプラインの後のステージで発生する例外をキャッチできるように、パイプラインの早い段階で呼び出される必要があります。</span><span class="sxs-lookup"><span data-stu-id="43c76-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="43c76-126">考えられる最も簡単な ASP.NET Core アプリは、1 つの要求デリゲートを設定してすべての要求を処理するものです。</span><span class="sxs-lookup"><span data-stu-id="43c76-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="43c76-127">この場合、実際の要求パイプラインは含まれません。</span><span class="sxs-lookup"><span data-stu-id="43c76-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="43c76-128">代わりに、すべての HTTP 要求に対応して単一の匿名関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="43c76-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs?name=snippet1)]

<span data-ttu-id="43c76-129">最初の <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> デリゲートが、パイプラインを終了します。</span><span class="sxs-lookup"><span data-stu-id="43c76-129">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegate terminates the pipeline.</span></span>

<span data-ttu-id="43c76-130">複数の要求デリゲートを <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> と一緒にチェーンします。</span><span class="sxs-lookup"><span data-stu-id="43c76-130">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="43c76-131">`next` パラメーターは、パイプラインの次のデリゲートを表します </span><span class="sxs-lookup"><span data-stu-id="43c76-131">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="43c76-132">*next* パラメーターを "*呼び出さない*" ことで、パイプラインをショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="43c76-132">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="43c76-133">次の例で示すように、通常は、次のデリゲートの前と後の両方でアクションを実行できます。</span><span class="sxs-lookup"><span data-stu-id="43c76-133">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?name=snippet1)]

<span data-ttu-id="43c76-134">デリゲートが次のデリゲートに要求を渡さない場合、これは "*要求パイプラインのショートサーキット*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="43c76-134">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="43c76-135">不要な処理を回避するために、ショートさせることが望ましい場合がよくあります。</span><span class="sxs-lookup"><span data-stu-id="43c76-135">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="43c76-136">たとえば、[静的ファイル ミドルウェア](xref:fundamentals/static-files)は、静的ファイルの要求を処理して残りのパイプラインをショートサーキットすることにより、"*ターミナル ミドルウェア*" として動作させることができます。</span><span class="sxs-lookup"><span data-stu-id="43c76-136">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="43c76-137">後続の処理を終了させるミドルウェアの前にパイプラインに追加されたミドルウェアでは、その `next.Invoke` ステートメントの後も引き続きコードが処理されます。</span><span class="sxs-lookup"><span data-stu-id="43c76-137">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="43c76-138">ただし、既に送信された応答に対する書き込みの試行については、次の警告を参照してください。</span><span class="sxs-lookup"><span data-stu-id="43c76-138">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="43c76-139">応答がクライアントに送信された後に、`next.Invoke` を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="43c76-139">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="43c76-140">応答が開始した後で <xref:Microsoft.AspNetCore.Http.HttpResponse> を変更すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="43c76-140">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="43c76-141">たとえば、ヘッダーや状態コードの設定などの変更があると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="43c76-141">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="43c76-142">`next` を呼び出した後で応答本文に書き込むと、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="43c76-142">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="43c76-143">プロトコル違反が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="43c76-143">May cause a protocol violation.</span></span> <span data-ttu-id="43c76-144">たとえば、示されている `Content-Length` より多くを書き込んだ場合。</span><span class="sxs-lookup"><span data-stu-id="43c76-144">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="43c76-145">本文の形式が破損する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="43c76-145">May corrupt the body format.</span></span> <span data-ttu-id="43c76-146">たとえば、CSS ファイルに HTML フッターを書き込んだ場合。</span><span class="sxs-lookup"><span data-stu-id="43c76-146">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="43c76-147"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> は、ヘッダーが送信されたかどうかや本文が書き込まれたかどうかを示すために役立つヒントです。</span><span class="sxs-lookup"><span data-stu-id="43c76-147"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

## <a name="order"></a><span data-ttu-id="43c76-148">注文</span><span class="sxs-lookup"><span data-stu-id="43c76-148">Order</span></span>

<span data-ttu-id="43c76-149">`Startup.Configure` メソッドでミドルウェア コンポーネントを追加する順序は、要求でミドルウェア コンポーネントが呼び出される順序および応答での逆の順序を定義します。</span><span class="sxs-lookup"><span data-stu-id="43c76-149">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="43c76-150">この順序は、セキュリティ、パフォーマンス、および機能にとって重要です。</span><span class="sxs-lookup"><span data-stu-id="43c76-150">The order is critical for security, performance, and functionality.</span></span>

<span data-ttu-id="43c76-151">次の `Startup.Configure` メソッドにより、共通アプリ シナリオのためのミドルウェア コンポーネントが追加されます。</span><span class="sxs-lookup"><span data-stu-id="43c76-151">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

::: moniker range=">= aspnetcore-2.0"

1. <span data-ttu-id="43c76-152">例外/エラー処理</span><span class="sxs-lookup"><span data-stu-id="43c76-152">Exception/error handling</span></span>
1. <span data-ttu-id="43c76-153">HTTP Strict Transport Security プロトコル</span><span class="sxs-lookup"><span data-stu-id="43c76-153">HTTP Strict Transport Security Protocol</span></span>
1. <span data-ttu-id="43c76-154">HTTPS リダイレクト</span><span class="sxs-lookup"><span data-stu-id="43c76-154">HTTPS redirection</span></span>
1. <span data-ttu-id="43c76-155">静的ファイル サーバー</span><span class="sxs-lookup"><span data-stu-id="43c76-155">Static file server</span></span>
1. <span data-ttu-id="43c76-156">Cookie ポリシーの適用</span><span class="sxs-lookup"><span data-stu-id="43c76-156">Cookie policy enforcement</span></span>
1. <span data-ttu-id="43c76-157">認証</span><span class="sxs-lookup"><span data-stu-id="43c76-157">Authentication</span></span>
1. <span data-ttu-id="43c76-158">セッション</span><span class="sxs-lookup"><span data-stu-id="43c76-158">Session</span></span>
1. <span data-ttu-id="43c76-159">MVC</span><span class="sxs-lookup"><span data-stu-id="43c76-159">MVC</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    if (env.IsDevelopment())
    {
        // When the app runs in the Development environment:
        //   Use the Developer Exception Page to report app runtime errors.
        //   Use the Database Error Page to report database runtime errors.
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        // When the app doesn't run in the Development environment:
        //   Enable the Exception Handler Middleware to catch exceptions
        //     thrown in the following middlewares.
        //   Use the HTTP Strict Transport Security Protocol (HSTS)
        //     Middleware.
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    // Use HTTPS Redirection Middleware to redirect HTTP requests to HTTPS.
    app.UseHttpsRedirection();

    // Return static files and end the pipeline.
    app.UseStaticFiles();

    // Use Cookie Policy Middleware to conform to EU General Data 
    // Protection Regulation (GDPR) regulations.
    app.UseCookiePolicy();

    // Authenticate before the user accesses secure resources.
    app.UseAuthentication();

    // If the app uses session state, call Session Middleware after Cookie 
    // Policy Middleware and before MVC Middleware.
    app.UseSession();

    // Add MVC to the request pipeline.
    app.UseMvc();
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. <span data-ttu-id="43c76-160">例外/エラー処理</span><span class="sxs-lookup"><span data-stu-id="43c76-160">Exception/error handling</span></span>
1. <span data-ttu-id="43c76-161">静的ファイル</span><span class="sxs-lookup"><span data-stu-id="43c76-161">Static files</span></span>
1. <span data-ttu-id="43c76-162">認証</span><span class="sxs-lookup"><span data-stu-id="43c76-162">Authentication</span></span>
1. <span data-ttu-id="43c76-163">セッション</span><span class="sxs-lookup"><span data-stu-id="43c76-163">Session</span></span>
1. <span data-ttu-id="43c76-164">MVC</span><span class="sxs-lookup"><span data-stu-id="43c76-164">MVC</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Enable the Exception Handler Middleware to catch exceptions
    //   thrown in the following middlewares.
    app.UseExceptionHandler("/Home/Error");

    // Return static files and end the pipeline.
    app.UseStaticFiles();

    // Authenticate before you access secure resources.
    app.UseIdentity();

    // If the app uses session state, call UseSession before 
    // MVC Middleware.
    app.UseSession();

    // Add MVC to the request pipeline.
    app.UseMvcWithDefaultRoute();
}
```

::: moniker-end

<span data-ttu-id="43c76-165">前のコード例では、各ミドルウェアの拡張メソッドが <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 名前空間を通じて <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> で公開されています。</span><span class="sxs-lookup"><span data-stu-id="43c76-165">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="43c76-166">パイプラインに追加された最初のミドルウェア コンポーネントは <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> です。</span><span class="sxs-lookup"><span data-stu-id="43c76-166"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="43c76-167">そのため、例外ハンドラー ミドルウェアでは、以降の呼び出しで発生するすべての例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="43c76-167">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="43c76-168">静的ファイル ミドルウェアはパイプラインの早い段階で呼び出されるので、要求を処理し、残りのコンポーネントを通過せずにショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="43c76-168">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="43c76-169">静的ファイル ミドルウェアでは、承認チェックは提供**されません**。</span><span class="sxs-lookup"><span data-stu-id="43c76-169">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="43c76-170">*wwwroot* の下にあるものも含め、このミドルウェアによって提供されるすべてのファイルは、一般に公開されます。</span><span class="sxs-lookup"><span data-stu-id="43c76-170">Any files served by it, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="43c76-171">静的ファイルを保護する方法については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="43c76-171">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="43c76-172">要求が静的ファイル ミドルウェアによって処理されない場合、要求は認証を実行する認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) に渡されます。</span><span class="sxs-lookup"><span data-stu-id="43c76-172">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="43c76-173">認証は、認証されない要求をショートさせません。</span><span class="sxs-lookup"><span data-stu-id="43c76-173">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="43c76-174">認証ミドルウェアは要求を認証しますが、承認 (および却下) は、MVC が特定の Razor ページまたは MVC コントローラーとアクションを選んだ後でのみ行われます。</span><span class="sxs-lookup"><span data-stu-id="43c76-174">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="43c76-175">要求が静的ファイル ミドルウェアによって処理されない場合、要求は認証を実行する ID ミドルウェア (<xref:Microsoft.AspNetCore.Builder.BuilderExtensions.UseIdentity*>) に渡されます。</span><span class="sxs-lookup"><span data-stu-id="43c76-175">If the request isn't handled by Static File Middleware, it's passed on to the Identity Middleware (<xref:Microsoft.AspNetCore.Builder.BuilderExtensions.UseIdentity*>), which performs authentication.</span></span> <span data-ttu-id="43c76-176">ID は、認証されない要求をショートさせません。</span><span class="sxs-lookup"><span data-stu-id="43c76-176">Identity doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="43c76-177">ID は要求を認証しますが、承認 (および却下) は、MVC が特定のコントローラーとアクションを選んだ後でのみ行われます。</span><span class="sxs-lookup"><span data-stu-id="43c76-177">Although Identity authenticates requests, authorization (and rejection) occurs only after MVC selects a specific controller and action.</span></span>

::: moniker-end

<span data-ttu-id="43c76-178">次の例は、静的ファイルの要求が応答圧縮ミドルウェアの前に静的ファイル ミドルウェアによって処理される、ミドルウェアの順序を示します。</span><span class="sxs-lookup"><span data-stu-id="43c76-178">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="43c76-179">静的ファイルは、このミドルウェアの順序では圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="43c76-179">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="43c76-180"><xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> からの MVC 応答を圧縮することができます。</span><span class="sxs-lookup"><span data-stu-id="43c76-180">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files not compressed by Static File Middleware.
    app.UseStaticFiles();
    app.UseResponseCompression();
    app.UseMvcWithDefaultRoute();
}
```

### <a name="use-run-and-map"></a><span data-ttu-id="43c76-181">Use、Run、および Map</span><span class="sxs-lookup"><span data-stu-id="43c76-181">Use, Run, and Map</span></span>

<span data-ttu-id="43c76-182">HTTP パイプラインを構成するには、`Use`、`Run`、`Map` を使います。</span><span class="sxs-lookup"><span data-stu-id="43c76-182">Configure the HTTP pipeline using `Use`, `Run`, and `Map`.</span></span> <span data-ttu-id="43c76-183">`Use` メソッドは、パイプラインをショートさせることができます (つまり `next` 要求デリゲートを呼び出さない場合)。</span><span class="sxs-lookup"><span data-stu-id="43c76-183">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="43c76-184">`Run` は規則であり、一部のミドルウェア コンポーネントは、パイプラインの最後に実行される `Run[Middleware]` メソッドを公開することがあります。</span><span class="sxs-lookup"><span data-stu-id="43c76-184">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="43c76-185"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 拡張メソッドは、パイプラインを分岐する規則として使われます。</span><span class="sxs-lookup"><span data-stu-id="43c76-185"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="43c76-186">`Map*` は、指定された要求パスの一致に基づいて、要求パイプラインを分岐します。</span><span class="sxs-lookup"><span data-stu-id="43c76-186">`Map*` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="43c76-187">要求パスが指定されたパスで開始する場合、分岐が実行されます。</span><span class="sxs-lookup"><span data-stu-id="43c76-187">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs?name=snippet1)]

<span data-ttu-id="43c76-188">次の表は、前のコードを使用した `http://localhost:1234` からの要求および応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="43c76-188">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="43c76-189">要求</span><span class="sxs-lookup"><span data-stu-id="43c76-189">Request</span></span>             | <span data-ttu-id="43c76-190">応答</span><span class="sxs-lookup"><span data-stu-id="43c76-190">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="43c76-191">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="43c76-191">localhost:1234</span></span>      | <span data-ttu-id="43c76-192">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="43c76-192">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="43c76-193">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="43c76-193">localhost:1234/map1</span></span> | <span data-ttu-id="43c76-194">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="43c76-194">Map Test 1</span></span>                   |
| <span data-ttu-id="43c76-195">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="43c76-195">localhost:1234/map2</span></span> | <span data-ttu-id="43c76-196">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="43c76-196">Map Test 2</span></span>                   |
| <span data-ttu-id="43c76-197">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="43c76-197">localhost:1234/map3</span></span> | <span data-ttu-id="43c76-198">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="43c76-198">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="43c76-199">`Map` を使用すると、一致したパス セグメントが `HttpRequest.Path` から削除され、要求ごとに `HttpRequest.PathBase` に付加されます。</span><span class="sxs-lookup"><span data-stu-id="43c76-199">When `Map` is used, the matched path segment(s) are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="43c76-200">[MapWhen](/dotnet/api/microsoft.aspnetcore.builder.mapwhenextensions) は、指定された述語の結果に基づいて、要求パイプラインを分岐します。</span><span class="sxs-lookup"><span data-stu-id="43c76-200">[MapWhen](/dotnet/api/microsoft.aspnetcore.builder.mapwhenextensions) branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="43c76-201">`Func<HttpContext, bool>` という型の任意の述語を使って、要求をパイプラインの新しい分岐にマップできます。</span><span class="sxs-lookup"><span data-stu-id="43c76-201">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="43c76-202">次の例では、クエリ文字列変数 `branch` の存在を検出するために術後が使用されます。</span><span class="sxs-lookup"><span data-stu-id="43c76-202">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?name=snippet1)]

<span data-ttu-id="43c76-203">次の表は、前のコードを使用した `http://localhost:1234` からの要求および応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="43c76-203">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="43c76-204">要求</span><span class="sxs-lookup"><span data-stu-id="43c76-204">Request</span></span>                       | <span data-ttu-id="43c76-205">応答</span><span class="sxs-lookup"><span data-stu-id="43c76-205">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="43c76-206">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="43c76-206">localhost:1234</span></span>                | <span data-ttu-id="43c76-207">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="43c76-207">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="43c76-208">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="43c76-208">localhost:1234/?branch=master</span></span> | <span data-ttu-id="43c76-209">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="43c76-209">Branch used = master</span></span>         |

<span data-ttu-id="43c76-210">`Map` は入れ子をサポートします。次にその例を示します。</span><span class="sxs-lookup"><span data-stu-id="43c76-210">`Map` supports nesting, for example:</span></span>

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
   ```

<span data-ttu-id="43c76-211">`Map` では、次のように一度に複数のセグメントを照合できます。</span><span class="sxs-lookup"><span data-stu-id="43c76-211">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?name=snippet1&highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="43c76-212">組み込みミドルウェア</span><span class="sxs-lookup"><span data-stu-id="43c76-212">Built-in middleware</span></span>

<span data-ttu-id="43c76-213">ASP.NET Core には、次のミドルウェア コンポーネントが付属しています。</span><span class="sxs-lookup"><span data-stu-id="43c76-213">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="43c76-214">"*順番*" 列には、要求を処理するパイプライン内のミドルウェアの配置と、ミドルウェアが要求処理を終了する条件に関するメモが記載されています。</span><span class="sxs-lookup"><span data-stu-id="43c76-214">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="43c76-215">ミドルウェアが要求を処理するパイプラインをショートサーキットし、下流のさらなるミドルウェアによる要求の処理を回避する場合、これは "*ターミナル ミドルウェア*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="43c76-215">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="43c76-216">ショートサーキットについて詳しくは、「[IApplicationBuilder を使用したミドルウェア パイプラインの作成](#create-a-middleware-pipeline-with-iapplicationbuilder)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="43c76-216">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="43c76-217">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="43c76-217">Middleware</span></span> | <span data-ttu-id="43c76-218">説明</span><span class="sxs-lookup"><span data-stu-id="43c76-218">Description</span></span> | <span data-ttu-id="43c76-219">注文</span><span class="sxs-lookup"><span data-stu-id="43c76-219">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="43c76-220">認証</span><span class="sxs-lookup"><span data-stu-id="43c76-220">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="43c76-221">認証のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="43c76-221">Provides authentication support.</span></span> | <span data-ttu-id="43c76-222">`HttpContext.User` が必要な場所の前。</span><span class="sxs-lookup"><span data-stu-id="43c76-222">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="43c76-223">OAuth コールバックの終端。</span><span class="sxs-lookup"><span data-stu-id="43c76-223">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="43c76-224">Cookie のポリシー</span><span class="sxs-lookup"><span data-stu-id="43c76-224">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="43c76-225">個人情報の保存に関してユーザーからの同意を追跡し、`secure` や `SameSite` など、Cookie フィールドの最小要件を適用します。</span><span class="sxs-lookup"><span data-stu-id="43c76-225">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="43c76-226">Cookie を発行するミドルウェアの前。</span><span class="sxs-lookup"><span data-stu-id="43c76-226">Before middleware that issues cookies.</span></span> <span data-ttu-id="43c76-227">次に例を示します。 認証、セッション、MVC (TempData)</span><span class="sxs-lookup"><span data-stu-id="43c76-227">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="43c76-228">CORS</span><span class="sxs-lookup"><span data-stu-id="43c76-228">CORS</span></span>](xref:security/cors) | <span data-ttu-id="43c76-229">クロス オリジン リソース共有を構成します。</span><span class="sxs-lookup"><span data-stu-id="43c76-229">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="43c76-230">CORS を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="43c76-230">Before components that use CORS.</span></span> |
| [<span data-ttu-id="43c76-231">診断</span><span class="sxs-lookup"><span data-stu-id="43c76-231">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="43c76-232">診断を構成します。</span><span class="sxs-lookup"><span data-stu-id="43c76-232">Configures diagnostics.</span></span> | <span data-ttu-id="43c76-233">エラーを生成するコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="43c76-233">Before components that generate errors.</span></span> |
| [<span data-ttu-id="43c76-234">転送されるヘッダー</span><span class="sxs-lookup"><span data-stu-id="43c76-234">Forwarded Headers</span></span>](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions) | <span data-ttu-id="43c76-235">プロキシされたヘッダーを現在の要求に転送します。</span><span class="sxs-lookup"><span data-stu-id="43c76-235">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="43c76-236">更新されたフィールドを使用するコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="43c76-236">Before components that consume the updated fields.</span></span> <span data-ttu-id="43c76-237">例: スキーム、ホスト、クライアント IP、メソッド。</span><span class="sxs-lookup"><span data-stu-id="43c76-237">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="43c76-238">正常性チェック</span><span class="sxs-lookup"><span data-stu-id="43c76-238">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="43c76-239">ASP.NET Core アプリとその依存関係の正常性を、データベースの可用性などで確認します。</span><span class="sxs-lookup"><span data-stu-id="43c76-239">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="43c76-240">要求が正常性チェックのエンドポイントと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="43c76-240">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="43c76-241">HTTP メソッドのオーバーライド</span><span class="sxs-lookup"><span data-stu-id="43c76-241">HTTP Method Override</span></span>](/dotnet/api/microsoft.aspnetcore.builder.httpmethodoverrideextensions) | <span data-ttu-id="43c76-242">メソッドをオーバーライドする受信 POST 要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="43c76-242">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="43c76-243">更新されたメソッドを使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="43c76-243">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="43c76-244">HTTPS リダイレクト</span><span class="sxs-lookup"><span data-stu-id="43c76-244">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="43c76-245">すべての HTTP 要求を HTTPS (ASP.NET Core 2.1 以降) にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="43c76-245">Redirect all HTTP requests to HTTPS (ASP.NET Core 2.1 or later).</span></span> | <span data-ttu-id="43c76-246">URL を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="43c76-246">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="43c76-247">HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="43c76-247">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="43c76-248">特殊な応答ヘッダーを追加するセキュリティ拡張機能のミドルウェア (ASP.NET Core 2.1 以降)。</span><span class="sxs-lookup"><span data-stu-id="43c76-248">Security enhancement middleware that adds a special response header (ASP.NET Core 2.1 or later).</span></span> | <span data-ttu-id="43c76-249">応答が送信される前と要求を変更するコンポーネントの後。</span><span class="sxs-lookup"><span data-stu-id="43c76-249">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="43c76-250">次に例を示します。 転送されるヘッダー、URL リライト。</span><span class="sxs-lookup"><span data-stu-id="43c76-250">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="43c76-251">MVC</span><span class="sxs-lookup"><span data-stu-id="43c76-251">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="43c76-252">MVC/Razor Pages (ASP.NET Core 2.0 以降) で要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="43c76-252">Processes requests with MVC/Razor Pages (ASP.NET Core 2.0 or later).</span></span> | <span data-ttu-id="43c76-253">要求がルートと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="43c76-253">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="43c76-254">OWIN</span><span class="sxs-lookup"><span data-stu-id="43c76-254">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="43c76-255">OWIN ベースのアプリ、サーバー、およびミドルウェアと相互運用します。</span><span class="sxs-lookup"><span data-stu-id="43c76-255">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="43c76-256">OWIN ミドルウェアが要求を完全に処理した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="43c76-256">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="43c76-257">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="43c76-257">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="43c76-258">応答のキャッシュのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="43c76-258">Provides support for caching responses.</span></span> | <span data-ttu-id="43c76-259">キャッシュが必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="43c76-259">Before components that require caching.</span></span> |
| [<span data-ttu-id="43c76-260">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="43c76-260">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="43c76-261">応答の圧縮のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="43c76-261">Provides support for compressing responses.</span></span> | <span data-ttu-id="43c76-262">圧縮が必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="43c76-262">Before components that require compression.</span></span> |
| [<span data-ttu-id="43c76-263">要求のローカライズ</span><span class="sxs-lookup"><span data-stu-id="43c76-263">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="43c76-264">ローカライズのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="43c76-264">Provides localization support.</span></span> | <span data-ttu-id="43c76-265">ローカリゼーションが重要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="43c76-265">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="43c76-266">ルーティング</span><span class="sxs-lookup"><span data-stu-id="43c76-266">Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="43c76-267">要求のルートを定義および制約します。</span><span class="sxs-lookup"><span data-stu-id="43c76-267">Defines and constrains request routes.</span></span> | <span data-ttu-id="43c76-268">一致するルートの終端。</span><span class="sxs-lookup"><span data-stu-id="43c76-268">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="43c76-269">セッション</span><span class="sxs-lookup"><span data-stu-id="43c76-269">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="43c76-270">ユーザー セッションの管理のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="43c76-270">Provides support for managing user sessions.</span></span> | <span data-ttu-id="43c76-271">セッションが必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="43c76-271">Before components that require Session.</span></span> |
| [<span data-ttu-id="43c76-272">静的ファイル</span><span class="sxs-lookup"><span data-stu-id="43c76-272">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="43c76-273">静的ファイルとディレクトリ参照に対応するサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="43c76-273">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="43c76-274">要求がファイルと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="43c76-274">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="43c76-275">URL リライト</span><span class="sxs-lookup"><span data-stu-id="43c76-275">URL Rewriting</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="43c76-276">URL の書き換えと要求のリダイレクトのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="43c76-276">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="43c76-277">URL を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="43c76-277">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="43c76-278">WebSocket</span><span class="sxs-lookup"><span data-stu-id="43c76-278">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="43c76-279">WebSocket プロトコルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="43c76-279">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="43c76-280">WebSocket 要求を受け入れる必要があるコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="43c76-280">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="43c76-281">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="43c76-281">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
