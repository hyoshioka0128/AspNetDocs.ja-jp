---
ms.openlocfilehash: 282871e5db197dfb4226cc02918f2d6ba1135c04
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57045599"
---
# <a name="aspnet-core-middleware-extensibility-sample"></a><span data-ttu-id="9526e-101">ASP.NET Core のミドルウェアの拡張性の例</span><span class="sxs-lookup"><span data-stu-id="9526e-101">ASP.NET Core Middleware Extensibility Sample</span></span>

<span data-ttu-id="9526e-102">このサンプルでは、サード パーティ製の依存関係挿入コンテナーである [Simple Injector](https://simpleinjector.org) を使用した [IMiddleware](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware) と [IMiddlewareFactory](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) の用途を説明します。</span><span class="sxs-lookup"><span data-stu-id="9526e-102">This sample illustrates the use of [IMiddleware](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware) and [IMiddlewareFactory](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) with a 3rd party dependency injection container, [Simple Injector](https://simpleinjector.org).</span></span> <span data-ttu-id="9526e-103">このサンプルでは、「[Middleware activation with a third-party container in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/extensibility-third-party-container)」 (ASP.NET Core でのサードパーティ コンテナーを使用したミドルウェアのアクティブ化) に書かれている機能について説明します。</span><span class="sxs-lookup"><span data-stu-id="9526e-103">This sample demonstrates the features described in [Middleware activation with a third-party container in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/extensibility-third-party-container).</span></span>
