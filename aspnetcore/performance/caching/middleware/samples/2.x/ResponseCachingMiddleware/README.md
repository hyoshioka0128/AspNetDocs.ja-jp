---
ms.openlocfilehash: 93bda587eebc438e5da36b07cb7e4a37df8a91eb
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57062719"
---
# <a name="aspnet-core-response-caching-sample"></a><span data-ttu-id="7483d-101">ASP.NET Core の応答キャッシュのサンプル</span><span class="sxs-lookup"><span data-stu-id="7483d-101">ASP.NET Core Response Caching Sample</span></span>

<span data-ttu-id="7483d-102">このサンプルは、ASP.NET Core の使用方法を示します[応答キャッシュ ミドルウェア](https://docs.microsoft.com/aspnet/core/performance/caching/middleware)します。</span><span class="sxs-lookup"><span data-stu-id="7483d-102">This sample illustrates the usage of ASP.NET Core [Response Caching Middleware](https://docs.microsoft.com/aspnet/core/performance/caching/middleware).</span></span>

<span data-ttu-id="7483d-103">そのインデックス ページのアプリの応答を含む、`Cache-Control`ヘッダー キャッシュの動作を構成します。</span><span class="sxs-lookup"><span data-stu-id="7483d-103">The app responds with its Index page, including a `Cache-Control` header to configure caching behavior.</span></span> <span data-ttu-id="7483d-104">また、アプリ、設定、`Vary`場合にのみ応答を処理するためにキャッシュを構成するヘッダー、`Accept-Encoding`を指定する元の要求からの後続の要求のヘッダーと一致します。</span><span class="sxs-lookup"><span data-stu-id="7483d-104">The app also sets the `Vary` header to configure the cache to serve the response only if the `Accept-Encoding` header of subsequent requests matches that from the original request.</span></span>

<span data-ttu-id="7483d-105">サンプルを実行するときに、インデックス ページは保存され、最大で 10 秒間キャッシュにキャッシュから提供されます。</span><span class="sxs-lookup"><span data-stu-id="7483d-105">When running the sample, the Index page is served from cache when stored and cached for up to 10 seconds.</span></span>
