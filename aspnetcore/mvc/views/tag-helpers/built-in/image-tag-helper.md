---
title: ASP.NET Core のイメージ タグ ヘルパー
author: pkellner
description: イメージ タグ ヘルパーを使用する方法を示します。
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: mvc/views/tag-helpers/builtin-th/image-tag-helper
ms.openlocfilehash: 5eb74a6698911a1c594d11573192cb1b9ed53b49
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57030109"
---
# <a name="image-tag-helper-in-aspnet-core"></a><span data-ttu-id="26a02-103">ASP.NET Core のイメージ タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="26a02-103">Image Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="26a02-104">著者: [Peter Kellner](http://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="26a02-104">By [Peter Kellner](http://peterkellner.net)</span></span>

<span data-ttu-id="26a02-105">イメージ タグ ヘルパーは `<img>` タグを強化し、静的イメージ ファイルにキャッシュ バスティング動作を提供します。</span><span class="sxs-lookup"><span data-stu-id="26a02-105">The Image Tag Helper enhances the `<img>` tag to provide cache-busting behavior for static image files.</span></span>

<span data-ttu-id="26a02-106">キャッシュ バスティング文字列は、アセットの URL に追加される、静的なイメージ ファイルのハッシュを表す一意の値です。</span><span class="sxs-lookup"><span data-stu-id="26a02-106">A cache-busting string is a unique value representing the hash of the static image file appended to the asset's URL.</span></span> <span data-ttu-id="26a02-107">一意の文字列は、クライアントのキャッシュからではなく、ホスト Web サーバーからイメージの再読み込みを行うよう、クライアント (および一部のプロキシ) に要求します。</span><span class="sxs-lookup"><span data-stu-id="26a02-107">The unique string prompts clients (and some proxies) to reload the image from the host web server and not from the client's cache.</span></span>

<span data-ttu-id="26a02-108">イメージ ソース (`src`) がホスト Web サーバー上の静的ファイルの場合:</span><span class="sxs-lookup"><span data-stu-id="26a02-108">If the image source (`src`) is a static file on the host web server:</span></span>

* <span data-ttu-id="26a02-109">一意のキャッシュ バスティング文字列は、クエリ パラメーターとしてイメージ ソースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="26a02-109">A unique cache-busting string is appended as a query parameter to the image source.</span></span>
* <span data-ttu-id="26a02-110">ホスト Web サーバーのファイルが変更された場合、更新された要求パラメーターを含む一意の要求 URL が確実に生成されます。</span><span class="sxs-lookup"><span data-stu-id="26a02-110">If the file on the host web server changes, a unique request URL is generated that includes the updated request parameter.</span></span>

<span data-ttu-id="26a02-111">タグ ヘルパーの概要については、「<xref:mvc/views/tag-helpers/intro>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="26a02-111">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

## <a name="image-tag-helper-attributes"></a><span data-ttu-id="26a02-112">イメージ タグ ヘルパーの属性</span><span class="sxs-lookup"><span data-stu-id="26a02-112">Image Tag Helper Attributes</span></span>

### <a name="src"></a><span data-ttu-id="26a02-113">src</span><span class="sxs-lookup"><span data-stu-id="26a02-113">src</span></span>

<span data-ttu-id="26a02-114">イメージ タグ ヘルパーをアクティブにするには、`<img>` 要素に `src` 属性が必要です。</span><span class="sxs-lookup"><span data-stu-id="26a02-114">To activate the Image Tag Helper, the `src` attribute is required on the `<img>` element.</span></span>

<span data-ttu-id="26a02-115">イメージ ソース (`src`) は、サーバー上の物理静的ファイルをポイントしている必要があります。</span><span class="sxs-lookup"><span data-stu-id="26a02-115">The image source (`src`) must point to a physical static file on the server.</span></span> <span data-ttu-id="26a02-116">`src` がリモート URI の場合は、キャッシュ バスティング クエリ文字列パラメーターは生成されません。</span><span class="sxs-lookup"><span data-stu-id="26a02-116">If the `src` is a remote URI, the cache-busting query string parameter isn't generated.</span></span>

### <a name="asp-append-version"></a><span data-ttu-id="26a02-117">asp-append-version</span><span class="sxs-lookup"><span data-stu-id="26a02-117">asp-append-version</span></span>

<span data-ttu-id="26a02-118">`asp-append-version` 属性の値が `true` で、`src` 属性も指定されている場合、イメージ タグ ヘルパーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="26a02-118">When `asp-append-version` is specified with a `true` value along with a `src` attribute, the Image Tag Helper is invoked.</span></span>

<span data-ttu-id="26a02-119">イメージ タグ ヘルパーを使用する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="26a02-119">The following example uses an Image Tag Helper:</span></span>

```cshtml
<img src="~/images/asplogo.png" asp-append-version="true" />
```

<span data-ttu-id="26a02-120">静的ファイルがディレクトリ */wwwroot/images/* に存在する場合、生成された HTML は次のようになります (ハッシュは異なります)。</span><span class="sxs-lookup"><span data-stu-id="26a02-120">If the static file exists in the directory */wwwroot/images/*, the generated HTML is similar to the following (the hash will be different):</span></span>

```html
<img src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM" />
```

<span data-ttu-id="26a02-121">パラメーター `v` に割り当てられた値は、ディスク上の *asplogo.png* ファイルのハッシュ値です。</span><span class="sxs-lookup"><span data-stu-id="26a02-121">The value assigned to the parameter `v` is the hash value of the *asplogo.png* file on disk.</span></span> <span data-ttu-id="26a02-122">Web サーバーが静的ファイルに対する読み取りアクセスを取得できない場合、`v` パラメーターは表示されるマークアップの `src` 属性に追加されません。</span><span class="sxs-lookup"><span data-stu-id="26a02-122">If the web server is unable to obtain read access to the static file, no `v` parameter is added to the `src` attribute in the rendered markup.</span></span>

## <a name="hash-caching-behavior"></a><span data-ttu-id="26a02-123">ハッシュ キャッシュの動作</span><span class="sxs-lookup"><span data-stu-id="26a02-123">Hash caching behavior</span></span>

<span data-ttu-id="26a02-124">イメージ タグ ヘルパーはローカル Web サーバーでキャッシュ プロバイダーを使用して、特定のファイルの計算された `Sha512` ハッシュを格納します。</span><span class="sxs-lookup"><span data-stu-id="26a02-124">The Image Tag Helper uses the cache provider on the local web server to store the calculated `Sha512` hash of a given file.</span></span> <span data-ttu-id="26a02-125">ファイルが複数回要求された場合、ハッシュは再計算されません。</span><span class="sxs-lookup"><span data-stu-id="26a02-125">If the file is requested multiple times, the hash isn't recalculated.</span></span> <span data-ttu-id="26a02-126">キャッシュは、ファイルの `Sha512` ハッシュが計算されたときにファイルにアタッチされるファイル ウォッチャーによって無効になります。</span><span class="sxs-lookup"><span data-stu-id="26a02-126">The cache is invalidated by a file watcher that's attached to the file when the file's `Sha512` hash is calculated.</span></span> <span data-ttu-id="26a02-127">ディスク上のファイルが変更されると、新しいハッシュが計算されてキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="26a02-127">When the file changes on disk, a new hash is calculated and cached.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="26a02-128">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="26a02-128">Additional resources</span></span>

* <xref:performance/caching/memory>
