---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-7
title: ビュー (UI) を作成する |Microsoft Docs
author: MikeWasson
description: ''
ms.author: riande
ms.date: 06/16/2014
ms.assetid: b2445062-a1fe-4133-8994-f510280f6d9a
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-7
msc.type: authoredcontent
ms.openlocfilehash: aa0ea68dd83a294e6d6c343887738c60eef595bf
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57034839"
---
<a name="create-the-view-ui"></a><span data-ttu-id="2c6dc-102">ビューの作成 (UI)</span><span class="sxs-lookup"><span data-stu-id="2c6dc-102">Create the View (UI)</span></span>
====================
<span data-ttu-id="2c6dc-103">作成者[Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="2c6dc-103">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

[<span data-ttu-id="2c6dc-104">完成したプロジェクトのダウンロード</span><span class="sxs-lookup"><span data-stu-id="2c6dc-104">Download Completed Project</span></span>](https://github.com/MikeWasson/BookService)

<span data-ttu-id="2c6dc-105">このセクションでは、アプリでは、HTML を定義し、HTML とビュー モデル間のデータ バインディングを追加する開始されます。</span><span class="sxs-lookup"><span data-stu-id="2c6dc-105">In this section, you will start to define the HTML for the app, and add data binding between the HTML and the view model.</span></span>

<span data-ttu-id="2c6dc-106">Views/Home/Index.cshtml ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="2c6dc-106">Open the file Views/Home/Index.cshtml.</span></span> <span data-ttu-id="2c6dc-107">次のように、そのファイルの内容全体を置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2c6dc-107">Replace the entire contents of that file with the following.</span></span>

[!code-cshtml[Main](part-7/samples/sample1.cshtml)]

<span data-ttu-id="2c6dc-108">ほとんどの`div`要素はあります[ブートス トラップ](http://getbootstrap.com/)のスタイルを設定します。</span><span class="sxs-lookup"><span data-stu-id="2c6dc-108">Most of the `div` elements are there for [Bootstrap](http://getbootstrap.com/) styling.</span></span> <span data-ttu-id="2c6dc-109">重要な要素は、のあるもの`data-bind`属性。</span><span class="sxs-lookup"><span data-stu-id="2c6dc-109">The important elements are the ones with `data-bind` attributes.</span></span> <span data-ttu-id="2c6dc-110">この属性は、HTML をビュー モデルにリンクします。</span><span class="sxs-lookup"><span data-stu-id="2c6dc-110">This attribute links the HTML to the view model.</span></span>

<span data-ttu-id="2c6dc-111">例:</span><span class="sxs-lookup"><span data-stu-id="2c6dc-111">For example:</span></span>

[!code-html[Main](part-7/samples/sample2.html)]

<span data-ttu-id="2c6dc-112">この例で、 &quot; `text` &quot;バインド エラーの原因、`<p>`要素の値を表示する、`error`ビュー モデルからのプロパティ。</span><span class="sxs-lookup"><span data-stu-id="2c6dc-112">In this example, the &quot;`text`&quot; binding causes the `<p>` element to show the value of the `error` property from the view model.</span></span> <span data-ttu-id="2c6dc-113">いることを思い出してください`error`として宣言されている、 `ko.observable`:</span><span class="sxs-lookup"><span data-stu-id="2c6dc-113">Recall that `error` was declared as a `ko.observable`:</span></span>

[!code-javascript[Main](part-7/samples/sample3.js)]

<span data-ttu-id="2c6dc-114">新しい値が割り当てられたときに`error`、Knockout でのテキストを更新する、`<p>`要素。</span><span class="sxs-lookup"><span data-stu-id="2c6dc-114">Whenever a new value is assigned to `error`, Knockout updates the text in the `<p>` element.</span></span>

<span data-ttu-id="2c6dc-115">`foreach`バインド通知の内容をループする Knockout、`books`配列。</span><span class="sxs-lookup"><span data-stu-id="2c6dc-115">The `foreach` binding tells Knockout to loop through the contents of the `books` array.</span></span> <span data-ttu-id="2c6dc-116">Knockout、配列内の各項目を新規作成&lt;li&gt;要素。</span><span class="sxs-lookup"><span data-stu-id="2c6dc-116">For each item in the array, Knockout creates a new &lt;li&gt; element.</span></span> <span data-ttu-id="2c6dc-117">バインドのコンテキスト内で、`foreach`配列項目のプロパティを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2c6dc-117">Bindings inside the context of the `foreach` refer to properties on the array item.</span></span> <span data-ttu-id="2c6dc-118">例えば:</span><span class="sxs-lookup"><span data-stu-id="2c6dc-118">For example:</span></span>

[!code-html[Main](part-7/samples/sample4.html)]

<span data-ttu-id="2c6dc-119">ここで、`text`バインドはの各 book の Author プロパティを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="2c6dc-119">Here the `text` binding reads the Author property of each book.</span></span>

<span data-ttu-id="2c6dc-120">これでアプリケーションを実行する場合次のようになります。</span><span class="sxs-lookup"><span data-stu-id="2c6dc-120">If you run the application now, it should look like this:</span></span>

![](part-7/_static/image1.png)

<span data-ttu-id="2c6dc-121">書籍の一覧は、ページが読み込まれた後、非同期的に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="2c6dc-121">The list of books loads asynchronously, after the page loads.</span></span> <span data-ttu-id="2c6dc-122">今すぐ、&quot;詳細&quot;リンクは機能しません。</span><span class="sxs-lookup"><span data-stu-id="2c6dc-122">Right now, the &quot;Details&quot; links are not functional.</span></span> <span data-ttu-id="2c6dc-123">次のセクションで、この機能を追加します。</span><span class="sxs-lookup"><span data-stu-id="2c6dc-123">We'll add this functionality in the next section.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="2c6dc-124">[前へ](part-6.md)
> [次へ](part-8.md)</span><span class="sxs-lookup"><span data-stu-id="2c6dc-124">[Previous](part-6.md)
[Next](part-8.md)</span></span>
