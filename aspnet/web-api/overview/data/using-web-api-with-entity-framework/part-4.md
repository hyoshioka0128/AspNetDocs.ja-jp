---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-4
title: エンティティ関係の処理 |Microsoft Docs
author: MikeWasson
description: ''
ms.author: riande
ms.date: 06/16/2014
ms.assetid: d2f5710c-23c7-40a5-9cd9-5d0516570cba
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-4
msc.type: authoredcontent
ms.openlocfilehash: be4948e5443a5eb4e1824c63dd0c445a7ee1928e
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59384695"
---
# <a name="handling-entity-relations"></a><span data-ttu-id="0abda-102">エンティティ関係の処理</span><span class="sxs-lookup"><span data-stu-id="0abda-102">Handling Entity Relations</span></span>

<span data-ttu-id="0abda-103">作成者[Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="0abda-103">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

[<span data-ttu-id="0abda-104">完成したプロジェクトのダウンロード</span><span class="sxs-lookup"><span data-stu-id="0abda-104">Download Completed Project</span></span>](https://github.com/MikeWasson/BookService)

<span data-ttu-id="0abda-105">このセクションでは、EF が関連エンティティを読み込む方法と、モデル クラス内で循環ナビゲーション プロパティを処理する方法のいくつかの詳細について説明します。</span><span class="sxs-lookup"><span data-stu-id="0abda-105">This section describes some details of how EF loads related entities, and how to handle circular navigation properties in your model classes.</span></span> <span data-ttu-id="0abda-106">(ここでは、バック グラウンドの知識を提供し、チュートリアルを完了する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="0abda-106">(This section provides background knowledge, and is not required to complete the tutorial.</span></span> <span data-ttu-id="0abda-107">場合は、必要に応じて[パート 5](part-5.md)。)。</span><span class="sxs-lookup"><span data-stu-id="0abda-107">If you prefer, skip to [Part 5.](part-5.md).)</span></span>

## <a name="eager-loading-versus-lazy-loading"></a><span data-ttu-id="0abda-108">一括読み込みと遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="0abda-108">Eager Loading versus Lazy Loading</span></span>

<span data-ttu-id="0abda-109">リレーショナル データベースでの EF を使用する場合は、EF が関連するデータを読み込む方法を理解する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0abda-109">When using EF with a relational database, it's important to understand how EF loads related data.</span></span>

<span data-ttu-id="0abda-110">EF によって生成される SQL クエリを表示する便利です。</span><span class="sxs-lookup"><span data-stu-id="0abda-110">It's also useful to see the SQL queries that EF generates.</span></span> <span data-ttu-id="0abda-111">SQL をトレースするには、次のコードの行を追加、`BookServiceContext`コンス トラクター。</span><span class="sxs-lookup"><span data-stu-id="0abda-111">To trace the SQL, add the following line of code to the `BookServiceContext` constructor:</span></span>

[!code-csharp[Main](part-4/samples/sample1.cs)]

<span data-ttu-id="0abda-112">/Api/books に GET 要求を送信する場合は、次のような JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="0abda-112">If you send a GET request to /api/books, it returns JSON like the following:</span></span>

[!code-console[Main](part-4/samples/sample2.cmd)]

<span data-ttu-id="0abda-113">書籍が有効な著者の Id を含んでいるにもかかわらず Author プロパティが null の場合、ことを確認できます。</span><span class="sxs-lookup"><span data-stu-id="0abda-113">You can see that the Author property is null, even though the book contains a valid AuthorId.</span></span> <span data-ttu-id="0abda-114">EF が作成者の関連エンティティを読み込んでいないためにです。</span><span class="sxs-lookup"><span data-stu-id="0abda-114">That's because EF is not loading the related Author entities.</span></span> <span data-ttu-id="0abda-115">SQL クエリのトレース ログは、これを確認します。</span><span class="sxs-lookup"><span data-stu-id="0abda-115">The trace log of the SQL query confirms this:</span></span>

[!code-console[Main](part-4/samples/sample3.sql)]

<span data-ttu-id="0abda-116">SELECT ステートメントは、ブックの「テーブルから取得し作成者表を参照していますいません。</span><span class="sxs-lookup"><span data-stu-id="0abda-116">The SELECT statement takes from the Books table, and does not reference the Author table.</span></span>

<span data-ttu-id="0abda-117">リファレンスについては、メソッドでは、ここで、`BooksController`書籍の一覧を返すクラス。</span><span class="sxs-lookup"><span data-stu-id="0abda-117">For reference, here is the method in the `BooksController` class that returns the list of books.</span></span>

[!code-csharp[Main](part-4/samples/sample4.cs)]

<span data-ttu-id="0abda-118">JSON データの一部として、作成者取得する方法を見てみましょう。</span><span class="sxs-lookup"><span data-stu-id="0abda-118">Let's see how we can return the Author as part of the JSON data.</span></span> <span data-ttu-id="0abda-119">Entity Framework に関連するデータを読み込む 3 つの方法: 一括読み込み、遅延読み込み、および明示的な読み込み。</span><span class="sxs-lookup"><span data-stu-id="0abda-119">There are three ways to load related data in Entity Framework: eager loading, lazy loading, and explicit loading.</span></span> <span data-ttu-id="0abda-120">各手法では、上のトレードオフがあるため、そのしくみを理解することが重要です。</span><span class="sxs-lookup"><span data-stu-id="0abda-120">There are trade-offs with each technique, so it's important to understand how they work.</span></span>

### <a name="eager-loading"></a><span data-ttu-id="0abda-121">一括読み込み</span><span class="sxs-lookup"><span data-stu-id="0abda-121">Eager Loading</span></span>

<span data-ttu-id="0abda-122">*一括読み込み*EF は、最初のデータベース クエリの一部として関連エンティティを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="0abda-122">With *eager loading*, EF loads related entities as part of the initial database query.</span></span> <span data-ttu-id="0abda-123">一括読み込みを実行するのには、使用、 **System.Data.Entity.Include**拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="0abda-123">To perform eager loading, use the **System.Data.Entity.Include** extension method.</span></span>

[!code-csharp[Main](part-4/samples/sample5.cs)]

<span data-ttu-id="0abda-124">これは、クエリに含めるデータを作成、EF に指示します。</span><span class="sxs-lookup"><span data-stu-id="0abda-124">This tells EF to include the Author data in the query.</span></span> <span data-ttu-id="0abda-125">この変更を行うアプリを実行すると、JSON データは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="0abda-125">If you make this change and run the app, now the JSON data looks like this:</span></span>

[!code-console[Main](part-4/samples/sample6.cmd)]

<span data-ttu-id="0abda-126">トレース ログは、EF が書籍と著者のテーブルの結合を実行することを示します。</span><span class="sxs-lookup"><span data-stu-id="0abda-126">The trace log shows that EF performed a join on the Book and Author tables.</span></span>

[!code-console[Main](part-4/samples/sample7.cmd)]

### <a name="lazy-loading"></a><span data-ttu-id="0abda-127">遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="0abda-127">Lazy Loading</span></span>

<span data-ttu-id="0abda-128">遅延読み込み、EF では、そのエンティティのナビゲーション プロパティが逆参照されるときに、関連エンティティが自動的に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="0abda-128">With lazy loading, EF automatically loads a related entity when the navigation property for that entity is dereferenced.</span></span> <span data-ttu-id="0abda-129">遅延読み込みを有効にするには、仮想ナビゲーション プロパティを確認します。</span><span class="sxs-lookup"><span data-stu-id="0abda-129">To enable lazy loading, make the navigation property virtual.</span></span> <span data-ttu-id="0abda-130">たとえば、Book クラス: で</span><span class="sxs-lookup"><span data-stu-id="0abda-130">For example, in the Book class:</span></span>

[!code-csharp[Main](part-4/samples/sample8.cs?highlight=6)]

<span data-ttu-id="0abda-131">次のコードについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="0abda-131">Now consider the following code:</span></span>

[!code-csharp[Main](part-4/samples/sample9.cs)]

<span data-ttu-id="0abda-132">遅延読み込みを有効にするへのアクセス、`Author`プロパティを`books[0]`作成者のデータベースのクエリに EF を発生します。</span><span class="sxs-lookup"><span data-stu-id="0abda-132">When lazy loading is enabled, accessing the `Author` property on `books[0]` causes EF to query the database for the author.</span></span>

<span data-ttu-id="0abda-133">遅延読み込みでは、EF は、関連エンティティを取得するたびにクエリを送信するために、データベースの複数のトリップが必要です。</span><span class="sxs-lookup"><span data-stu-id="0abda-133">Lazy loading requires multiple database trips, because EF sends a query each time it retrieves a related entity.</span></span> <span data-ttu-id="0abda-134">一般に、遅延読み込みのシリアル化するオブジェクトを無効にします。</span><span class="sxs-lookup"><span data-stu-id="0abda-134">Generally, you want lazy loading disabled for objects that you serialize.</span></span> <span data-ttu-id="0abda-135">シリアライザーは、すべての関連エンティティの読み込みをトリガーすると、モデルのプロパティを読み取る必要があります。</span><span class="sxs-lookup"><span data-stu-id="0abda-135">The serializer has to read all of the properties on the model, which triggers loading the related entities.</span></span> <span data-ttu-id="0abda-136">EF は、遅延読み込みを有効になっている書籍の一覧をシリアル化時に、SQL クエリをここではたとえばです。</span><span class="sxs-lookup"><span data-stu-id="0abda-136">For example, here are the SQL queries when EF serializes the list of books with lazy loading enabled.</span></span> <span data-ttu-id="0abda-137">EF では、次の 3 つの分離したクエリを次の 3 つの作成者に確認できます。</span><span class="sxs-lookup"><span data-stu-id="0abda-137">You can see that EF makes three separate queries for the three authors.</span></span>

[!code-console[Main](part-4/samples/sample10.sql)]

<span data-ttu-id="0abda-138">遅延読み込みを使用する場合は引き続きがあります。</span><span class="sxs-lookup"><span data-stu-id="0abda-138">There are still times when you might want to use lazy loading.</span></span> <span data-ttu-id="0abda-139">一括読み込みでは、EF が非常に複雑な結合を生成する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0abda-139">Eager loading can cause EF to generate a very complex join.</span></span> <span data-ttu-id="0abda-140">または、関連エンティティは、データの小さなサブセットにする必要があり、遅延読み込みを効率的になります。</span><span class="sxs-lookup"><span data-stu-id="0abda-140">Or you might need related entities for a small subset of the data, and lazy loading would be more efficient.</span></span>

<span data-ttu-id="0abda-141">シリアル化の問題を回避する方法の 1 つでは、エンティティ オブジェクトではなく、データ転送オブジェクト (Dto) をシリアル化します。</span><span class="sxs-lookup"><span data-stu-id="0abda-141">One way to avoid serialization problems is to serialize data transfer objects (DTOs) instead of entity objects.</span></span> <span data-ttu-id="0abda-142">記事の後半では、この方法を紹介します。</span><span class="sxs-lookup"><span data-stu-id="0abda-142">I'll show this approach later in the article.</span></span>

### <a name="explicit-loading"></a><span data-ttu-id="0abda-143">明示的な読み込み</span><span class="sxs-lookup"><span data-stu-id="0abda-143">Explicit Loading</span></span>

<span data-ttu-id="0abda-144">はコードで明示的に関連するデータを取得する点を除いては、明示的読み込みは、遅延読み込みに似ていますナビゲーション プロパティにアクセスするときに自動的に発生しません。</span><span class="sxs-lookup"><span data-stu-id="0abda-144">Explicit loading is similar to lazy loading, except that you explicitly get the related data in code; it doesn't happen automatically when you access a navigation property.</span></span> <span data-ttu-id="0abda-145">明示的読み込みは、関連データを読み込むときに細かく制御できますが、余分なコードが必要です。</span><span class="sxs-lookup"><span data-stu-id="0abda-145">Explicit loading gives you more control over when to load related data, but requires extra code.</span></span> <span data-ttu-id="0abda-146">明示的な読み込みの詳細については、次を参照してください。[関連エンティティの読み込み](https://msdn.microsoft.com/data/jj574232#explicit)します。</span><span class="sxs-lookup"><span data-stu-id="0abda-146">For more information about explicit loading, see [Loading Related Entities](https://msdn.microsoft.com/data/jj574232#explicit).</span></span>

## <a name="navigation-properties-and-circular-references"></a><span data-ttu-id="0abda-147">ナビゲーション プロパティと循環参照</span><span class="sxs-lookup"><span data-stu-id="0abda-147">Navigation Properties and Circular References</span></span>

<span data-ttu-id="0abda-148">ナビゲーション プロパティを定義した書籍と著者のモデルを定義したとき、 `Book` Book の Author リレーションシップ クラスが逆方向のナビゲーション プロパティを定義するでした。</span><span class="sxs-lookup"><span data-stu-id="0abda-148">When I defined the Book and Author models, I defined a navigation property on the `Book` class for the Book-Author relationship, but I did not define a navigation property in the other direction.</span></span>

<span data-ttu-id="0abda-149">対応するナビゲーション プロパティを追加する場合の結果、`Author`クラスか?</span><span class="sxs-lookup"><span data-stu-id="0abda-149">What happens if you add the corresponding navigation property to the `Author` class?</span></span>

[!code-csharp[Main](part-4/samples/sample11.cs?highlight=7)]

<span data-ttu-id="0abda-150">残念ながら、モデルをシリアル化するときに問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="0abda-150">Unfortunately, this creates a problem when you serialize the models.</span></span> <span data-ttu-id="0abda-151">関連データを読み込む場合は、循環オブジェクト グラフを作成します。</span><span class="sxs-lookup"><span data-stu-id="0abda-151">If you load the related data, it creates a circular object graph.</span></span>

![](part-4/_static/image1.png)

<span data-ttu-id="0abda-152">JSON または XML フォーマッタは、グラフをシリアル化する際に、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="0abda-152">When the JSON or XML formatter tries to serialize the graph, it will throw an exception.</span></span> <span data-ttu-id="0abda-153">2 つのフォーマッタは、別の例外メッセージをスローします。</span><span class="sxs-lookup"><span data-stu-id="0abda-153">The two formatters throw different exception messages.</span></span> <span data-ttu-id="0abda-154">JSON フォーマッタの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="0abda-154">Here is an example for the JSON formatter:</span></span>

[!code-console[Main](part-4/samples/sample12.cmd)]

<span data-ttu-id="0abda-155">XML フォーマッタを次に示します。</span><span class="sxs-lookup"><span data-stu-id="0abda-155">Here is the XML formatter:</span></span>

[!code-xml[Main](part-4/samples/sample13.xml)]

<span data-ttu-id="0abda-156">1 つのソリューションでは、次のセクションで説明する Dto を使用します。</span><span class="sxs-lookup"><span data-stu-id="0abda-156">One solution is to use DTOs, which I describe in the next section.</span></span> <span data-ttu-id="0abda-157">また、グラフのサイクルの対応する JSON および XML フォーマッタを構成できます。</span><span class="sxs-lookup"><span data-stu-id="0abda-157">Alternatively, you can configure the JSON and XML formatters to handle graph cycles.</span></span> <span data-ttu-id="0abda-158">詳細については、次を参照してください。[循環オブジェクト参照の処理](../../formats-and-model-binding/json-and-xml-serialization.md#handling_circular_object_references)します。</span><span class="sxs-lookup"><span data-stu-id="0abda-158">For more information, see [Handling Circular Object References](../../formats-and-model-binding/json-and-xml-serialization.md#handling_circular_object_references).</span></span>

<span data-ttu-id="0abda-159">このチュートリアルで必要はありません、`Author.Book`ナビゲーション プロパティ、ため残しておくことができます。</span><span class="sxs-lookup"><span data-stu-id="0abda-159">For this tutorial, you don't need the `Author.Book` navigation property, so you can leave it out.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="0abda-160">[前へ](part-3.md)
> [次へ](part-5.md)</span><span class="sxs-lookup"><span data-stu-id="0abda-160">[Previous](part-3.md)
[Next](part-5.md)</span></span>
