---
title: ASP.NET Core で HttpContext にアクセスする
author: coderandhiker
description: ASP.NET Core で HttpContext にアクセスする方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2018
uid: fundamentals/httpcontext
ms.openlocfilehash: 446882297524af3cbaed3ba7f941935debf5e7f4
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57026429"
---
# <a name="access-httpcontext-in-aspnet-core"></a><span data-ttu-id="4da42-103">ASP.NET Core で HttpContext にアクセスする</span><span class="sxs-lookup"><span data-stu-id="4da42-103">Access HttpContext in ASP.NET Core</span></span>

<span data-ttu-id="4da42-104">ASP.NET Core アプリでは、[IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) インターフェイスとその既定の実装である [HttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.httpcontextaccessor) を使用して、`HttpContext` にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="4da42-104">ASP.NET Core apps access the `HttpContext` through the [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) interface and its default implementation [HttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.httpcontextaccessor).</span></span> <span data-ttu-id="4da42-105">`IHttpContextAccessor` を使用する必要があるのは、サービス内の `HttpContext` にアクセスする必要がある場合のみです。</span><span class="sxs-lookup"><span data-stu-id="4da42-105">It's only necessary to use `IHttpContextAccessor` when you need access to the `HttpContext` inside a service.</span></span>

::: moniker range=">= aspnetcore-2.0"

## <a name="use-httpcontext-from-razor-pages"></a><span data-ttu-id="4da42-106">Razor Pages から HttpContext を使用する</span><span class="sxs-lookup"><span data-stu-id="4da42-106">Use HttpContext from Razor Pages</span></span>

<span data-ttu-id="4da42-107">Razor Pages の [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) では、[HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext) プロパティが公開されます。</span><span class="sxs-lookup"><span data-stu-id="4da42-107">The Razor Pages [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) exposes the [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext) property:</span></span>

```csharp
public class AboutModel : PageModel
{
    public string Message { get; set; }

    public void OnGet()
    {
        Message = HttpContext.Request.PathBase;
    }
}
```

::: moniker-end

## <a name="use-httpcontext-from-a-razor-view"></a><span data-ttu-id="4da42-108">Razor ビューから HttpContext を使用する</span><span class="sxs-lookup"><span data-stu-id="4da42-108">Use HttpContext from a Razor view</span></span>

<span data-ttu-id="4da42-109">Razor ビューでは、[RazorPage.Context](/dotnet/api/microsoft.aspnetcore.mvc.razor.razorpage.context#Microsoft_AspNetCore_Mvc_Razor_RazorPage_Context) プロパティを使用して、ビューに直接 `HttpContext` が公開されます。</span><span class="sxs-lookup"><span data-stu-id="4da42-109">Razor views expose the `HttpContext` directly via a [RazorPage.Context](/dotnet/api/microsoft.aspnetcore.mvc.razor.razorpage.context#Microsoft_AspNetCore_Mvc_Razor_RazorPage_Context) property on the view.</span></span> <span data-ttu-id="4da42-110">次の例では、Windows 認証を使用して、イントラネット アプリで現在のユーザー名を取得します。</span><span class="sxs-lookup"><span data-stu-id="4da42-110">The following example retrieves the current username in an Intranet app using Windows Authentication:</span></span>

```cshtml
@{
    var username = Context.User.Identity.Name;
}
```

## <a name="use-httpcontext-from-a-controller"></a><span data-ttu-id="4da42-111">コントローラーから HttpContext を使用する</span><span class="sxs-lookup"><span data-stu-id="4da42-111">Use HttpContext from a controller</span></span>

<span data-ttu-id="4da42-112">コントローラーでは [ControllerBase.HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.httpcontext) プロパティが公開されます。</span><span class="sxs-lookup"><span data-stu-id="4da42-112">Controllers expose the [ControllerBase.HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.httpcontext) property:</span></span>

```csharp
public class HomeController : Controller
{
    public IActionResult About()
    {
        var pathBase = HttpContext.Request.PathBase;
        // Do something with the PathBase.

        return View();
    }
}
```

## <a name="use-httpcontext-from-middleware"></a><span data-ttu-id="4da42-113">ミドルウェアから HttpContext を使用する</span><span class="sxs-lookup"><span data-stu-id="4da42-113">Use HttpContext from middleware</span></span>

<span data-ttu-id="4da42-114">カスタム ミドルウェア コンポーネントを使用する場合、`HttpContext` は `Invoke` メソッドまたは `InvokeAsync` メソッドに渡され、ミドルウェアを構成する際にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="4da42-114">When working with custom middleware components, `HttpContext` is passed into the `Invoke` or `InvokeAsync` method and can be accessed when the middleware is configured:</span></span>

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        // Middleware initialization optionally using HttpContext
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a><span data-ttu-id="4da42-115">カスタム コンポーネントから HttpContext を使用する</span><span class="sxs-lookup"><span data-stu-id="4da42-115">Use HttpContext from custom components</span></span>

<span data-ttu-id="4da42-116">`HttpContext` へのアクセスを必要とするその他のフレームワークおよびカスタム コンポーネントに対して推奨される方法は、組み込みの[依存関係の挿入](xref:fundamentals/dependency-injection)コンテナーを使用して依存関係を登録することです。</span><span class="sxs-lookup"><span data-stu-id="4da42-116">For other framework and custom components that require access to `HttpContext`, the recommended approach is to register a dependency using the built-in [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4da42-117">依存関係の挿入コンテナーは、それぞれのコンストラクター内で `IHttpContextAccessor` を依存関係として宣言するすべてのクラスに、これを提供します。</span><span class="sxs-lookup"><span data-stu-id="4da42-117">The dependency injection container supplies the `IHttpContextAccessor` to any classes that declare it as a dependency in their constructors.</span></span>

::: moniker range=">= aspnetcore-2.1"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddMvc()
         .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddMvc();
     services.AddSingleton<IHttpContextAccessor, HttpContextAccessor>();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

<span data-ttu-id="4da42-118">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="4da42-118">In the following example:</span></span>

* <span data-ttu-id="4da42-119">`UserRepository` は `IHttpContextAccessor` に対する依存関係を宣言します。</span><span class="sxs-lookup"><span data-stu-id="4da42-119">`UserRepository` declares its dependency on `IHttpContextAccessor`.</span></span>
* <span data-ttu-id="4da42-120">依存関係の挿入で依存関係のチェーンが解決され、`UserRepository` のインスタンスが作成されると、依存関係が提供されます。</span><span class="sxs-lookup"><span data-stu-id="4da42-120">The dependency is supplied when dependency injection resolves the dependency chain and creates an instance of `UserRepository`.</span></span>

```csharp
public class UserRepository : IUserRepository
{
    private readonly IHttpContextAccessor _httpContextAccessor;

    public UserRepository(IHttpContextAccessor httpContextAccessor)
    {
        _httpContextAccessor = httpContextAccessor;
    }

    public void LogCurrentUser()
    {
        var username = _httpContextAccessor.HttpContext.User.Identity.Name;
        service.LogAccessRequest(username);
    }
}
```

## <a name="httpcontext-access-from-a-background-thread"></a><span data-ttu-id="4da42-121">バックグラウンド スレッドから HttpContext にアクセスする</span><span class="sxs-lookup"><span data-stu-id="4da42-121">HttpContext access from a background thread</span></span>

<span data-ttu-id="4da42-122">`HttpContext` はスレッド セーフではありません。</span><span class="sxs-lookup"><span data-stu-id="4da42-122">`HttpContext` is not thread-safe.</span></span> <span data-ttu-id="4da42-123">要求の処理以外で `HttpContext` のプロパティを読み書きすると、結果的に `NullReferenceException` になることがあります。</span><span class="sxs-lookup"><span data-stu-id="4da42-123">Reading or writing properties of the `HttpContext` outside of processing a request can result in a `NullReferenceException`.</span></span>

> [!NOTE]
> <span data-ttu-id="4da42-124">要求の処理以外で `HttpContext` を使用すると、結果的に `NullReferenceException` になることがしばしばあります。</span><span class="sxs-lookup"><span data-stu-id="4da42-124">Using `HttpContext` outside of processing a request often results in a `NullReferenceException`.</span></span> <span data-ttu-id="4da42-125">アプリで `NullReferenceException` が散発的に生成される場合、コードの中で、バックグラウンド処理を開始する部分や要求完了後に処理を続行する部分を見直してください。</span><span class="sxs-lookup"><span data-stu-id="4da42-125">If your app generates sporadic `NullReferenceException`s , review parts of the code that start background processing, or that continue processing after a request completes.</span></span> <span data-ttu-id="4da42-126">コントローラー メソッドを `async void` として定義しているなどの間違いがないか探してください。</span><span class="sxs-lookup"><span data-stu-id="4da42-126">Look for a mistakes like defining a controller method as `async void`.</span></span>

<span data-ttu-id="4da42-127">`HttpContext` データでバックグラウンド作業を安全に実行するには:</span><span class="sxs-lookup"><span data-stu-id="4da42-127">To safely perform background work with `HttpContext` data:</span></span>

* <span data-ttu-id="4da42-128">要求処理中に必要なデータをコピーします。</span><span class="sxs-lookup"><span data-stu-id="4da42-128">Copy the required data during request processing.</span></span>
* <span data-ttu-id="4da42-129">コピーしたデータをバックグラウンド タスクに渡します。</span><span class="sxs-lookup"><span data-stu-id="4da42-129">Pass the copied data to a background task.</span></span>

<span data-ttu-id="4da42-130">安全でないコードを避けるために、バックグラウンド作業を行わないメソッドには `HttpContext` を決して渡さないでください。代わりに必要なデータを渡してください。</span><span class="sxs-lookup"><span data-stu-id="4da42-130">To avoid unsafe code, never pass the `HttpContext` into a method that does background work - pass the data you need instead.</span></span>

```csharp
public class EmailController
{
    public ActionResult SendEmail(string email)
    {
        var correlationId = HttpContext.Request.Headers["x-correlation-id"].ToString();

        // Starts sending an email, but doesn't wait for it to complete
        _ = SendEmailCore(correlationId);
        return View();
    }

    private async Task SendEmailCore(string correlationId)
    {
        // send the email
    }
}

