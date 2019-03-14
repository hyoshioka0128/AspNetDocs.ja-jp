---
uid: mvc/overview/views/dynamic-v-strongly-typed-views
title: 動的に型指定されたビューと ビューを厳密に型指定 |Microsoft Docs
author: Rick-Anderson
description: ''
ms.author: riande
ms.date: 01/27/2011
ms.assetid: 0cbd88da-0da6-4605-b222-2835c6478304
msc.legacyurl: /mvc/overview/views/dynamic-v-strongly-typed-views
msc.type: authoredcontent
ms.openlocfilehash: bd00fccc44019b2e401247de1532d2abcb5dd396
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57057019"
---
<a name="dynamic-v-strongly-typed-views"></a><span data-ttu-id="894ca-103">動的に型指定されたビューと</span><span class="sxs-lookup"><span data-stu-id="894ca-103">Dynamic v.</span></span> <span data-ttu-id="894ca-104">厳密に型指定されたビュー</span><span class="sxs-lookup"><span data-stu-id="894ca-104">Strongly Typed Views</span></span>
====================
<span data-ttu-id="894ca-105">によって[Rick Anderson]((https://twitter.com/RickAndMSFT))</span><span class="sxs-lookup"><span data-stu-id="894ca-105">by [Rick Anderson]((https://twitter.com/RickAndMSFT))</span></span>

<span data-ttu-id="894ca-106">ASP.NET MVC 3 でビュー コント ローラーから情報を渡すための 3 つの方法はあります。</span><span class="sxs-lookup"><span data-stu-id="894ca-106">There are three ways to pass information from a controller to a view in ASP.NET MVC 3:</span></span>

1. <span data-ttu-id="894ca-107">厳密に型指定されたモデル オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="894ca-107">As a strongly typed model object.</span></span>
2. <span data-ttu-id="894ca-108">動的な型として (を使用して@model動的)</span><span class="sxs-lookup"><span data-stu-id="894ca-108">As a dynamic type (using @model dynamic)</span></span>
3. <span data-ttu-id="894ca-109">ViewBag を使用します。</span><span class="sxs-lookup"><span data-stu-id="894ca-109">Using the ViewBag</span></span>

<span data-ttu-id="894ca-110">動的かつ厳密に型指定されたビューを比較して単純な MVC 3 上位ブログ アプリケーション作成しました。</span><span class="sxs-lookup"><span data-stu-id="894ca-110">I've written a simple MVC 3 Top Blog application to compare and contrast dynamic and strongly typed views.</span></span> <span data-ttu-id="894ca-111">ブログの単純なリストから始まって、コント ローラー。</span><span class="sxs-lookup"><span data-stu-id="894ca-111">The controller starts out with a simple list of blogs:</span></span>

[!code-csharp[Main](dynamic-v-strongly-typed-views/samples/sample1.cs)]

<span data-ttu-id="894ca-112">IndexNotStonglyTyped() メソッド内を右クリックし、Razor ビューを追加します。</span><span class="sxs-lookup"><span data-stu-id="894ca-112">Right click in the IndexNotStonglyTyped() method and add a Razor view.</span></span>

<span data-ttu-id="894ca-113">[![8475.NotStronglyTypedView[1]](dynamic-v-strongly-typed-views/_static/image2.png)](dynamic-v-strongly-typed-views/_static/image1.png)</span><span class="sxs-lookup"><span data-stu-id="894ca-113">[![8475.NotStronglyTypedView[1]](dynamic-v-strongly-typed-views/_static/image2.png)](dynamic-v-strongly-typed-views/_static/image1.png)</span></span>

<span data-ttu-id="894ca-114">確認、**厳密に型指定されたビューを作成**ボックスがオンになっていません。</span><span class="sxs-lookup"><span data-stu-id="894ca-114">Make sure the **Create a strongly-typed view** box is not checked.</span></span> <span data-ttu-id="894ca-115">結果のビューには、ほとんどが含まれていません。</span><span class="sxs-lookup"><span data-stu-id="894ca-115">The resulting view doesn't contain much:</span></span>

[!code-cshtml[Main](dynamic-v-strongly-typed-views/samples/sample2.cshtml)]

[!code-cshtml[Main](dynamic-v-strongly-typed-views/samples/sample3.cshtml)]

<span data-ttu-id="894ca-116">動的なと厳密に型指定されたビューではなくを使用するため、intellisense が役立ちます。</span><span class="sxs-lookup"><span data-stu-id="894ca-116">Because we're using a dynamic and not a strongly typed view, intellisense doesn't help us.</span></span> <span data-ttu-id="894ca-117">完成したコードは、以下に示します。</span><span class="sxs-lookup"><span data-stu-id="894ca-117">The completed code is shown below:</span></span>

[!code-cshtml[Main](dynamic-v-strongly-typed-views/samples/sample4.cshtml)]

<span data-ttu-id="894ca-118">[![6646.NotStronglyTypedView_5F00_IE[1]](dynamic-v-strongly-typed-views/_static/image4.png)](dynamic-v-strongly-typed-views/_static/image3.png)</span><span class="sxs-lookup"><span data-stu-id="894ca-118">[![6646.NotStronglyTypedView_5F00_IE[1]](dynamic-v-strongly-typed-views/_static/image4.png)](dynamic-v-strongly-typed-views/_static/image3.png)</span></span>

<span data-ttu-id="894ca-119">厳密に型指定されたビューを追加しましょう。</span><span class="sxs-lookup"><span data-stu-id="894ca-119">Now we'll add a strongly typed view.</span></span> <span data-ttu-id="894ca-120">コント ローラーには、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="894ca-120">Add the following code to the controller:</span></span>

[!code-csharp[Main](dynamic-v-strongly-typed-views/samples/sample5.cs)]


<span data-ttu-id="894ca-121">正確に、同じ戻り View(topBlogs); に注目してください。非厳密に型指定されたビューとして呼び出します。</span><span class="sxs-lookup"><span data-stu-id="894ca-121">Notice it's exactly the same return View(topBlogs); call as the non-strongly typed view.</span></span> <span data-ttu-id="894ca-122">内側を右クリックして*StonglyTypedIndex()* 選択**ビューの追加**します。</span><span class="sxs-lookup"><span data-stu-id="894ca-122">Right click inside of *StonglyTypedIndex()* and select **Add View**.</span></span> <span data-ttu-id="894ca-123">この時間を選択、**ブログ**モデル クラスと選択**一覧**スキャフォールディング テンプレートとして。</span><span class="sxs-lookup"><span data-stu-id="894ca-123">This time select the **Blog** Model class and select **List** as the Scaffold template.</span></span>

<span data-ttu-id="894ca-124">[![5658.StrongView[1]](dynamic-v-strongly-typed-views/_static/image6.png)](dynamic-v-strongly-typed-views/_static/image5.png)</span><span class="sxs-lookup"><span data-stu-id="894ca-124">[![5658.StrongView[1]](dynamic-v-strongly-typed-views/_static/image6.png)](dynamic-v-strongly-typed-views/_static/image5.png)</span></span>

<span data-ttu-id="894ca-125">新しいビュー テンプレートの内部では、intellisense サポートを取得します。</span><span class="sxs-lookup"><span data-stu-id="894ca-125">Inside the new view template we get intellisense support.</span></span>

<span data-ttu-id="894ca-126">[![7002.intellesince[1]](dynamic-v-strongly-typed-views/_static/image8.png)](dynamic-v-strongly-typed-views/_static/image7.png)</span><span class="sxs-lookup"><span data-stu-id="894ca-126">[![7002.intellesince[1]](dynamic-v-strongly-typed-views/_static/image8.png)](dynamic-v-strongly-typed-views/_static/image7.png)</span></span>

<span data-ttu-id="894ca-127">C# プロジェクトをダウンロードできます[ここ](https://blogs.msdn.com/cfs-file.ashx/__key/CommunityServer-Blogs-Components-WeblogFiles/00-00-01-11-73-SSMS/1817.Mvc3ViewDemo.zip)します。</span><span class="sxs-lookup"><span data-stu-id="894ca-127">The c# project can be downloaded [here](https://blogs.msdn.com/cfs-file.ashx/__key/CommunityServer-Blogs-Components-WeblogFiles/00-00-01-11-73-SSMS/1817.Mvc3ViewDemo.zip).</span></span>
