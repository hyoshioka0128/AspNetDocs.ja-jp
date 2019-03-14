---
title: ASP.NET Core でメモリ内キャッシュします。
author: rick-anderson
description: ASP.NET Core でメモリにデータをキャッシュする方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2019
uid: performance/caching/memory
ms.openlocfilehash: 9a7727ad41a05f39d74877af3c8f2e3f7a620c7d
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57050039"
---
# <a name="cache-in-memory-in-aspnet-core"></a><span data-ttu-id="07b8e-103">ASP.NET Core でメモリ内キャッシュします。</span><span class="sxs-lookup"><span data-stu-id="07b8e-103">Cache in-memory in ASP.NET Core</span></span>

<span data-ttu-id="07b8e-104">によって[Rick Anderson](https://twitter.com/RickAndMSFT)、 [John Luo](https://github.com/JunTaoLuo)、および[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="07b8e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="07b8e-105">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/memory/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="07b8e-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="caching-basics"></a><span data-ttu-id="07b8e-106">キャッシュの基礎</span><span class="sxs-lookup"><span data-stu-id="07b8e-106">Caching basics</span></span>

<span data-ttu-id="07b8e-107">キャッシュと、パフォーマンスとアプリケーションのスケーラビリティが大幅に、コンテンツの生成に必要な作業を減らすことで改善できます。</span><span class="sxs-lookup"><span data-stu-id="07b8e-107">Caching can significantly improve the performance and scalability of an app by reducing the work required to generate content.</span></span> <span data-ttu-id="07b8e-108">変更頻度の低いデータに最適なキャッシュのしくみです。</span><span class="sxs-lookup"><span data-stu-id="07b8e-108">Caching works best with data that changes infrequently.</span></span> <span data-ttu-id="07b8e-109">キャッシュで返すことができる多くのデータのコピー元のソースからよりも高速です。</span><span class="sxs-lookup"><span data-stu-id="07b8e-109">Caching makes a copy of data that can be returned much faster than from the original source.</span></span> <span data-ttu-id="07b8e-110">書き込みし、キャッシュされたデータに依存しないアプリをテストする必要があります。</span><span class="sxs-lookup"><span data-stu-id="07b8e-110">You should write and test your app to never depend on cached data.</span></span>

<span data-ttu-id="07b8e-111">ASP.NET Core には、いくつかの異なるキャッシュがサポートしています。</span><span class="sxs-lookup"><span data-stu-id="07b8e-111">ASP.NET Core supports several different caches.</span></span> <span data-ttu-id="07b8e-112">最も単純なキャッシュがに基づいて、 [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)、web サーバーのメモリに格納されているキャッシュを表します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-112">The simplest cache is based on the [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), which represents a cache stored in the memory of the web server.</span></span> <span data-ttu-id="07b8e-113">複数のサーバーのサーバー ファームで実行されるアプリでは、セッションをメモリ内キャッシュを使用する場合は固定ことを確認してください。</span><span class="sxs-lookup"><span data-stu-id="07b8e-113">Apps which run on a server farm of multiple servers should ensure that sessions are sticky when using the in-memory cache.</span></span> <span data-ttu-id="07b8e-114">スティッキー セッションでは、すべてのクライアントからの後続の要求が同じサーバーに移動することを確認します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-114">Sticky sessions ensure that subsequent requests from a client all go to the same server.</span></span> <span data-ttu-id="07b8e-115">たとえば、Azure Web アプリの使用[アプリケーション要求ルーティング処理](https://www.iis.net/learn/extensions/planning-for-arr)処理 (ARR) を同じサーバーにすべての後続の要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="07b8e-115">For example, Azure Web apps use [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) to route all subsequent requests to the same server.</span></span>

<span data-ttu-id="07b8e-116">Web ファーム内の非スティッキー セッションが必要です、[分散キャッシュ](distributed.md)キャッシュ整合性の問題を回避します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-116">Non-sticky sessions in a web farm require a [distributed cache](distributed.md) to avoid cache consistency problems.</span></span> <span data-ttu-id="07b8e-117">一部のアプリでは、分散キャッシュはメモリ内キャッシュよりも高いスケール アウトをサポートできます。</span><span class="sxs-lookup"><span data-stu-id="07b8e-117">For some apps, a distributed cache can support higher scale-out than an in-memory cache.</span></span> <span data-ttu-id="07b8e-118">分散キャッシュを使用して外部プロセスにキャッシュ メモリをオフロードします。</span><span class="sxs-lookup"><span data-stu-id="07b8e-118">Using a distributed cache offloads the cache memory to an external process.</span></span>

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="07b8e-119">`IMemoryCache`しない限り、キャッシュがメモリ負荷の下でのキャッシュ エントリを削除は、[優先順位のキャッシュ](/dotnet/api/microsoft.extensions.caching.memory.cacheitempriority)に設定されている`CacheItemPriority.NeverRemove`します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-119">The `IMemoryCache` cache will evict cache entries under memory pressure unless the [cache priority](/dotnet/api/microsoft.extensions.caching.memory.cacheitempriority) is set to `CacheItemPriority.NeverRemove`.</span></span> <span data-ttu-id="07b8e-120">設定することができます、`CacheItemPriority`キャッシュがメモリの負荷の下の項目を削除する優先順位を調整します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-120">You can set the `CacheItemPriority` to adjust the priority with which the cache evicts items under memory pressure.</span></span>

::: moniker-end

<span data-ttu-id="07b8e-121">メモリ内キャッシュは、すべてのオブジェクトを格納できます。分散キャッシュのインターフェイスに制限されます`byte[]`します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-121">The in-memory cache can store any object; the distributed cache interface is limited to `byte[]`.</span></span> <span data-ttu-id="07b8e-122">キー/値ペアとしてし、インメモリ分散キャッシュ ストア キャッシュ項目。</span><span class="sxs-lookup"><span data-stu-id="07b8e-122">The in-memory and distributed cache store cache items as key-value pairs.</span></span>

## <a name="systemruntimecachingmemorycache"></a><span data-ttu-id="07b8e-123">System.Runtime.Caching/MemoryCache</span><span class="sxs-lookup"><span data-stu-id="07b8e-123">System.Runtime.Caching/MemoryCache</span></span>

<span data-ttu-id="07b8e-124"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet パッケージ](https://www.nuget.org/packages/System.Runtime.Caching/)) で使用できます。</span><span class="sxs-lookup"><span data-stu-id="07b8e-124"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet package](https://www.nuget.org/packages/System.Runtime.Caching/)) can be used with:</span></span>

* <span data-ttu-id="07b8e-125">.NET standard 2.0 以降。</span><span class="sxs-lookup"><span data-stu-id="07b8e-125">.NET Standard 2.0 or later.</span></span>
* <span data-ttu-id="07b8e-126">すべて[.NET 実装](/dotnet/standard/net-standard#net-implementation-support).NET Standard 2.0 以降をターゲットとします。</span><span class="sxs-lookup"><span data-stu-id="07b8e-126">Any [.NET implementation](/dotnet/standard/net-standard#net-implementation-support) that targets .NET Standard 2.0 or later.</span></span> <span data-ttu-id="07b8e-127">たとえば、ASP.NET Core 2.0 以降です。</span><span class="sxs-lookup"><span data-stu-id="07b8e-127">For example, ASP.NET Core 2.0 or later.</span></span>
* <span data-ttu-id="07b8e-128">.NET framework 4.5 またはそれ以降。</span><span class="sxs-lookup"><span data-stu-id="07b8e-128">.NET Framework 4.5 or later.</span></span>

<span data-ttu-id="07b8e-129">[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` (このトピックで説明) よりもお勧め`System.Runtime.Caching` / `MemoryCache`のため、これは、ASP.NET Core に統合して強化します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-129">[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)/`IMemoryCache` (described in this topic) is recommended over `System.Runtime.Caching`/`MemoryCache` because it's better integrated into ASP.NET Core.</span></span> <span data-ttu-id="07b8e-130">たとえば、 `IMemoryCache` ASP.NET Core では、ネイティブ動作[依存関係の注入](xref:fundamentals/dependency-injection)します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-130">For example, `IMemoryCache` works natively with ASP.NET Core [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="07b8e-131">使用`System.Runtime.Caching` / `MemoryCache` ASP.NET からのコードを移植するときに互換性の仲介役としての ASP.NET core 4.x です。</span><span class="sxs-lookup"><span data-stu-id="07b8e-131">Use `System.Runtime.Caching`/`MemoryCache` as a compatibility bridge when porting code from ASP.NET 4.x to ASP.NET Core.</span></span>

## <a name="cache-guidelines"></a><span data-ttu-id="07b8e-132">キャッシュのガイドライン</span><span class="sxs-lookup"><span data-stu-id="07b8e-132">Cache guidelines</span></span>

* <span data-ttu-id="07b8e-133">データをフェッチするフォールバック オプションがコードと**いない**利用されているキャッシュされた値に依存します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-133">Code should always have a fallback option to fetch data and **not** depend on a cached value being available.</span></span>
* <span data-ttu-id="07b8e-134">キャッシュは、メモリ、不足しているリソースを使用します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-134">The cache uses a scarce resource, memory.</span></span> <span data-ttu-id="07b8e-135">キャッシュ拡張を制限するには。</span><span class="sxs-lookup"><span data-stu-id="07b8e-135">Limit cache growth:</span></span>
  * <span data-ttu-id="07b8e-136">**いない**キャッシュ キーと外部入力を使用します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-136">Do **not** use external input as cache keys.</span></span>
  * <span data-ttu-id="07b8e-137">有効期限の設定を使用すると、キャッシュ増加を制限できます。</span><span class="sxs-lookup"><span data-stu-id="07b8e-137">Use expirations to limit cache growth.</span></span>
  * [<span data-ttu-id="07b8e-138">SetSize、サイズ、および SizeLimit を使用して、キャッシュ サイズを制限するには</span><span class="sxs-lookup"><span data-stu-id="07b8e-138">Use SetSize, Size, and SizeLimit to limit cache size</span></span>](#use-setsize-size-and-sizelimit-to-limit-cache-size)

## <a name="using-imemorycache"></a><span data-ttu-id="07b8e-139">IMemoryCache を使用します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-139">Using IMemoryCache</span></span>

<span data-ttu-id="07b8e-140">メモリ内キャッシュは、*サービス*を使用して、アプリから参照される[依存関係の注入](../../fundamentals/dependency-injection.md)します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-140">In-memory caching is a *service* that's referenced from your app using [Dependency Injection](../../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="07b8e-141">呼び出す`AddMemoryCache`で`ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="07b8e-141">Call `AddMemoryCache` in `ConfigureServices`:</span></span>

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

<span data-ttu-id="07b8e-142">要求、`IMemoryCache`コンス トラクターでインスタンス。</span><span class="sxs-lookup"><span data-stu-id="07b8e-142">Request the `IMemoryCache` instance in the constructor:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="07b8e-143">`IMemoryCache` NuGet パッケージが必要です[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-143">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="07b8e-144">`IMemoryCache` NuGet パッケージが必要です[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)、用意されている、 [Microsoft.AspNetCore.All メタパッケージ](xref:fundamentals/metapackage)します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-144">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), which is available in the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0"

<span data-ttu-id="07b8e-145">`IMemoryCache` NuGet パッケージが必要です[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)、用意されている、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-145">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="07b8e-146">次のコードでは[TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__)キャッシュ内、時間をチェックします。</span><span class="sxs-lookup"><span data-stu-id="07b8e-146">The following code uses [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) to check if a time is in the cache.</span></span> <span data-ttu-id="07b8e-147">新しいエントリが作成され、キャッシュに追加、時間がキャッシュされていない場合[設定](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_)します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-147">If a time isn't cached, a new entry is created and added to the cache with [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).</span></span>

[!code-csharp [](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="07b8e-148">現在の時刻とキャッシュされた時刻が表示されます。</span><span class="sxs-lookup"><span data-stu-id="07b8e-148">The current time and the cached time are displayed:</span></span>

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

<span data-ttu-id="07b8e-149">キャッシュされた`DateTime`値は、要求がタイムアウト期間 (とメモリ不足のためのない削除) 内である間はキャッシュに残ります。</span><span class="sxs-lookup"><span data-stu-id="07b8e-149">The cached `DateTime` value remains in the cache while there are requests within the timeout period (and no eviction due to memory pressure).</span></span> <span data-ttu-id="07b8e-150">次の図は、現在の時刻と、キャッシュから取得した古い時刻を示します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-150">The following image shows the current time and an older time retrieved from the cache:</span></span>

![インデックス ビューに表示される 2 つの時刻](memory/_static/time.png)

<span data-ttu-id="07b8e-152">次のコードでは[GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__)と[GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___)データをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="07b8e-152">The following code uses [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) and [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) to cache data.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

<span data-ttu-id="07b8e-153">次のコード呼び出し[取得](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)キャッシュされる時間を取得します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-153">The following code calls [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) to fetch the cached time:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<span data-ttu-id="07b8e-154"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*> 、 <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>、および[取得](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)の拡張メソッドの一部である、 [CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions)の機能を拡張するクラスを<xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-154"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*> , <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>, and [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) are extension methods part of the [CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) class that extends the capability of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="07b8e-155">参照してください[IMemoryCache メソッド](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)と[CacheExtensions メソッド](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions)の他のキャッシュ方法の説明。</span><span class="sxs-lookup"><span data-stu-id="07b8e-155">See [IMemoryCache methods](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) and [CacheExtensions methods](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) for a description of other cache methods.</span></span>

## <a name="memorycacheentryoptions"></a><span data-ttu-id="07b8e-156">MemoryCacheEntryOptions</span><span class="sxs-lookup"><span data-stu-id="07b8e-156">MemoryCacheEntryOptions</span></span>

<span data-ttu-id="07b8e-157">次のサンプル:</span><span class="sxs-lookup"><span data-stu-id="07b8e-157">The following sample:</span></span>

- <span data-ttu-id="07b8e-158">絶対有効期限を設定します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-158">Sets the absolute expiration time.</span></span> <span data-ttu-id="07b8e-159">これは、エントリをキャッシュできる最大時間であり、項目が古くなりすぎないスライド式有効期限が継続的に更新されることを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="07b8e-159">This is the maximum time the entry can be cached and prevents the item from becoming too stale when the sliding expiration is continuously renewed.</span></span>
- <span data-ttu-id="07b8e-160">スライド式有効期限を設定します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-160">Sets a sliding expiration time.</span></span> <span data-ttu-id="07b8e-161">このキャッシュされた項目にアクセスする要求は、スライド式有効期限の時間にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="07b8e-161">Requests that access this cached item will reset the sliding expiration clock.</span></span>
- <span data-ttu-id="07b8e-162">キャッシュ優先度を設定`CacheItemPriority.NeverRemove`します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-162">Sets the cache priority to `CacheItemPriority.NeverRemove`.</span></span>
- <span data-ttu-id="07b8e-163">セットを[PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate)するエントリがキャッシュから削除された後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="07b8e-163">Sets a [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) that will be called after the entry is evicted from the cache.</span></span> <span data-ttu-id="07b8e-164">コールバックは、キャッシュから項目を削除するコードから別のスレッドで実行されます。</span><span class="sxs-lookup"><span data-stu-id="07b8e-164">The callback is run on a different thread from the code that removes the item from the cache.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

::: moniker range=">= aspnetcore-2.0"

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a><span data-ttu-id="07b8e-165">SetSize、サイズ、および SizeLimit を使用して、キャッシュ サイズを制限するには</span><span class="sxs-lookup"><span data-stu-id="07b8e-165">Use SetSize, Size, and SizeLimit to limit cache size</span></span>

<span data-ttu-id="07b8e-166">A`MemoryCache`インスタンスが必要に応じて指定し、サイズ制限が適用されます。</span><span class="sxs-lookup"><span data-stu-id="07b8e-166">A `MemoryCache` instance may optionally specify and enforce a size limit.</span></span> <span data-ttu-id="07b8e-167">メモリ サイズの制限は、キャッシュのエントリのサイズを測定するためのメカニズムがあるないために、メジャーの定義済みの単位がありません。</span><span class="sxs-lookup"><span data-stu-id="07b8e-167">The memory size limit does not have a defined unit of measure because the cache has no mechanism to measure the size of entries.</span></span> <span data-ttu-id="07b8e-168">キャッシュ メモリ サイズの制限が設定されている場合、すべてのエントリはサイズを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="07b8e-168">If the cache memory size limit is set, all entries must specify size.</span></span> <span data-ttu-id="07b8e-169">指定されたサイズは、単位、開発者が選択ができます。</span><span class="sxs-lookup"><span data-stu-id="07b8e-169">The size specified is in units the developer chooses.</span></span>

<span data-ttu-id="07b8e-170">例:</span><span class="sxs-lookup"><span data-stu-id="07b8e-170">For example:</span></span>

* <span data-ttu-id="07b8e-171">Web アプリでは、文字列をキャッシュが主に、各キャッシュ エントリのサイズが文字列の長さになります。</span><span class="sxs-lookup"><span data-stu-id="07b8e-171">If the web app was primarily caching strings, each cache entry size could be the string length.</span></span>
* <span data-ttu-id="07b8e-172">アプリは、1 とすべてのエントリのサイズを指定できます、サイズ制限はエントリの数。</span><span class="sxs-lookup"><span data-stu-id="07b8e-172">The app could specify the size of all entries as 1, and the size limit is the count of entries.</span></span>

<span data-ttu-id="07b8e-173">次のコード作成、単位のない固定サイズ[MemoryCache](/dotnet/api/microsoft.extensions.caching.memory.memorycache?view=aspnetcore-2.1)からアクセスできる[依存関係の注入](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="07b8e-173">The following code creates a unitless fixed size [MemoryCache](/dotnet/api/microsoft.extensions.caching.memory.memorycache?view=aspnetcore-2.1) accessible by [dependency injection](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

<span data-ttu-id="07b8e-174">[SizeLimit](/dotnet/api/microsoft.extensions.caching.memory.memorycacheoptions.sizelimit?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheOptions_SizeLimit)ユニットではありません。</span><span class="sxs-lookup"><span data-stu-id="07b8e-174">[SizeLimit](/dotnet/api/microsoft.extensions.caching.memory.memorycacheoptions.sizelimit?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheOptions_SizeLimit) does not have units.</span></span> <span data-ttu-id="07b8e-175">キャッシュされたエントリは、キャッシュ メモリのサイズが設定されている場合に最も適切なと見なした単位でサイズを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="07b8e-175">Cached entries must specify size in whatever units they deem most appropriate if the cache memory size has been set.</span></span> <span data-ttu-id="07b8e-176">キャッシュ インスタンスのすべてのユーザーには、同じ単位システムを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="07b8e-176">All users of a cache instance should use the same unit system.</span></span> <span data-ttu-id="07b8e-177">キャッシュ エントリ サイズの合計がで指定された値を超えた場合、エントリはキャッシュされません`SizeLimit`します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-177">An entry will not be cached if the sum of the cached entry sizes exceeds the value specified by `SizeLimit`.</span></span> <span data-ttu-id="07b8e-178">キャッシュ サイズの制限が設定されていない場合のエントリを設定するキャッシュ サイズは無視されます。</span><span class="sxs-lookup"><span data-stu-id="07b8e-178">If no cache size limit is set, the cache size set on the entry will be ignored.</span></span>

<span data-ttu-id="07b8e-179">次のコードのレジスタ`MyMemoryCache`で、[依存関係の注入](xref:fundamentals/dependency-injection)コンテナー。</span><span class="sxs-lookup"><span data-stu-id="07b8e-179">The following code registers `MyMemoryCache` with the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span>

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

<span data-ttu-id="07b8e-180">`MyMemoryCache` 独立したキャッシュはこのサイズの上限のキャッシュを意識して、キャッシュ エントリのサイズを適切に設定する方法を理解しているコンポーネントとして作成されます。</span><span class="sxs-lookup"><span data-stu-id="07b8e-180">`MyMemoryCache` is created as an independent memory cache for components that are aware of this size limited cache and know how to set cache entry size appropriately.</span></span>

<span data-ttu-id="07b8e-181">次のコードでは`MyMemoryCache`:</span><span class="sxs-lookup"><span data-stu-id="07b8e-181">The following code uses `MyMemoryCache`:</span></span>

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="07b8e-182">キャッシュ エントリのサイズを設定できます[サイズ](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size)または[SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_)拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="07b8e-182">The size of the cache entry can be set by [Size](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) or the [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) extension method:</span></span>

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

::: moniker-end

## <a name="cache-dependencies"></a><span data-ttu-id="07b8e-183">キャッシュの依存関係</span><span class="sxs-lookup"><span data-stu-id="07b8e-183">Cache dependencies</span></span>

<span data-ttu-id="07b8e-184">次の例では、依存するエントリの有効期限が切れる場合、キャッシュ エントリを期限切れする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-184">The following sample shows how to expire a cache entry if a dependent entry expires.</span></span> <span data-ttu-id="07b8e-185">A`CancellationChangeToken`キャッシュされた項目に追加されます。</span><span class="sxs-lookup"><span data-stu-id="07b8e-185">A `CancellationChangeToken` is added to the cached item.</span></span> <span data-ttu-id="07b8e-186">ときに`Cancel`で呼び出される、 `CancellationTokenSource`、両方のキャッシュ エントリが削除されます。</span><span class="sxs-lookup"><span data-stu-id="07b8e-186">When `Cancel` is called on the `CancellationTokenSource`, both cache entries are evicted.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

<span data-ttu-id="07b8e-187">使用して、`CancellationTokenSource`グループとして削除する複数のキャッシュ エントリを許可します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-187">Using a `CancellationTokenSource` allows multiple cache entries to be evicted as a group.</span></span> <span data-ttu-id="07b8e-188">`using`内で上記のコード パターンは、キャッシュ エントリの作成、`using`ブロックは、トリガーと有効期限の設定を継承します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-188">With the `using` pattern in the code above, cache entries created inside the `using` block will inherit triggers and expiration settings.</span></span>

## <a name="additional-notes"></a><span data-ttu-id="07b8e-189">補足メモ</span><span class="sxs-lookup"><span data-stu-id="07b8e-189">Additional notes</span></span>

- <span data-ttu-id="07b8e-190">コールバックを使用して、キャッシュ項目を再作成します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-190">When using a callback to repopulate a cache item:</span></span>

  - <span data-ttu-id="07b8e-191">コールバックが完了していないために、複数の要求には、キャッシュされたキーの値が空ことができます検索します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-191">Multiple requests can find the cached key value empty because the callback hasn't completed.</span></span>
  - <span data-ttu-id="07b8e-192">これは、結果、複数のスレッドが、キャッシュされた項目を再作成します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-192">This can result in several threads repopulating the cached item.</span></span>

- <span data-ttu-id="07b8e-193">1 つのキャッシュ エントリを使用して、別に作成したときに、子は親エントリの有効期限のトークンと有効期限の時間ベースの設定をコピーします。</span><span class="sxs-lookup"><span data-stu-id="07b8e-193">When one cache entry is used to create another, the child copies the parent entry's expiration tokens and time-based expiration settings.</span></span> <span data-ttu-id="07b8e-194">子は、手動で削除して期限切れまたは親エントリの更新はありません。</span><span class="sxs-lookup"><span data-stu-id="07b8e-194">The child isn't expired by manual removal or updating of the parent entry.</span></span>

- <span data-ttu-id="07b8e-195">使用[PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks)キャッシュ エントリがキャッシュから削除された後に起動されるコールバックを設定します。</span><span class="sxs-lookup"><span data-stu-id="07b8e-195">Use [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) to set the callbacks that will be fired after the cache entry is evicted from the cache.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="07b8e-196">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="07b8e-196">Additional resources</span></span>

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
