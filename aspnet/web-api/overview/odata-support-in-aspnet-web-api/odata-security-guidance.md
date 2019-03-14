---
uid: web-api/overview/odata-support-in-aspnet-web-api/odata-security-guidance
title: ASP.NET Web API 2 のセキュリティ ガイダンス OData |Microsoft Docs
author: MikeWasson
description: ''
ms.author: riande
ms.date: 02/06/2013
ms.assetid: b91e6424-1544-4747-bd0b-d1f8418c9653
msc.legacyurl: /web-api/overview/odata-support-in-aspnet-web-api/odata-security-guidance
msc.type: authoredcontent
ms.openlocfilehash: 4ba53e15dab83368097a58ba4d0d2e46d113d1d2
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57065249"
---
<a name="security-guidance-for-aspnet-web-api-2-odata"></a><span data-ttu-id="cd3de-102">ASP.NET Web API 2 のセキュリティ ガイダンス OData</span><span class="sxs-lookup"><span data-stu-id="cd3de-102">Security Guidance for ASP.NET Web API 2 OData</span></span>
====================
<span data-ttu-id="cd3de-103">作成者[Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="cd3de-103">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

<span data-ttu-id="cd3de-104">このトピックでは、OData でのデータセットを公開するときに考慮すべきセキュリティの問題について説明します。</span><span class="sxs-lookup"><span data-stu-id="cd3de-104">This topic describes some of the security issues that you should consider when exposing a dataset through OData.</span></span>

## <a name="edm-security"></a><span data-ttu-id="cd3de-105">EDM のセキュリティ</span><span class="sxs-lookup"><span data-stu-id="cd3de-105">EDM Security</span></span>

<span data-ttu-id="cd3de-106">クエリのセマンティクスは、entity data model (EDM) でないモデルの基になる型に基づきます。</span><span class="sxs-lookup"><span data-stu-id="cd3de-106">The query semantics are based on the entity data model (EDM), not the underlying model types.</span></span> <span data-ttu-id="cd3de-107">プロパティを除外するには、EDM からして、クエリには表示されません。</span><span class="sxs-lookup"><span data-stu-id="cd3de-107">You can exclude a property from the EDM and it will not be visible to the query.</span></span> <span data-ttu-id="cd3de-108">たとえば、給与プロパティを持つ従業員の種類が、モデルに含まれています。</span><span class="sxs-lookup"><span data-stu-id="cd3de-108">For example, suppose your model includes an Employee type with a Salary property.</span></span> <span data-ttu-id="cd3de-109">クライアントから非表示にする、EDM からこのプロパティを除外する場合があります。</span><span class="sxs-lookup"><span data-stu-id="cd3de-109">You might want to exclude this property from the EDM to hide it from clients.</span></span>

<span data-ttu-id="cd3de-110">除外する 2 つの方法、EDM からプロパティ。</span><span class="sxs-lookup"><span data-stu-id="cd3de-110">There are two ways to exlude a property from the EDM.</span></span> <span data-ttu-id="cd3de-111">設定することができます、 **[IgnoreDataMember]** モデル クラスのプロパティの属性。</span><span class="sxs-lookup"><span data-stu-id="cd3de-111">You can set the **[IgnoreDataMember]** attribute on the property in the model class:</span></span>

[!code-csharp[Main](odata-security-guidance/samples/sample1.cs)]

<span data-ttu-id="cd3de-112">EDM からプログラムで、プロパティも削除できます。</span><span class="sxs-lookup"><span data-stu-id="cd3de-112">You can also remove the property from the EDM programmatically:</span></span>

[!code-csharp[Main](odata-security-guidance/samples/sample2.cs)]

## <a name="query-security"></a><span data-ttu-id="cd3de-113">クエリのセキュリティ</span><span class="sxs-lookup"><span data-stu-id="cd3de-113">Query Security</span></span>

<span data-ttu-id="cd3de-114">悪意のあるまたは単純なクライアントを実行する非常に長い時間がかかるクエリを作成することがあります。</span><span class="sxs-lookup"><span data-stu-id="cd3de-114">A malicious or naive client may be able to construct a query that takes a very long time to execute.</span></span> <span data-ttu-id="cd3de-115">最悪の場合に、サービスへのアクセスをこれが中断されることができます。</span><span class="sxs-lookup"><span data-stu-id="cd3de-115">In the worst case this can disrupt access to your service.</span></span>

<span data-ttu-id="cd3de-116">**[Queryable]** 属性は、解析、検証、およびクエリを適用するアクション フィルター。</span><span class="sxs-lookup"><span data-stu-id="cd3de-116">The **[Queryable]** attribute is an action filter that parses, validates, and applies the query.</span></span> <span data-ttu-id="cd3de-117">フィルターは、クエリ オプションを LINQ 式に変換します。</span><span class="sxs-lookup"><span data-stu-id="cd3de-117">The filter converts the query options into a LINQ expression.</span></span> <span data-ttu-id="cd3de-118">OData コント ローラーを返す場合、 **IQueryable**の種類、 **IQueryable** LINQ プロバイダーは、クエリに LINQ 式を変換します。</span><span class="sxs-lookup"><span data-stu-id="cd3de-118">When the OData controller returns an **IQueryable** type, the **IQueryable** LINQ provider converts the LINQ expression into a query.</span></span> <span data-ttu-id="cd3de-119">そのため、パフォーマンスは、LINQ プロバイダーを使用して、データセットやデータベース スキーマの特定の特性を依存します。</span><span class="sxs-lookup"><span data-stu-id="cd3de-119">Therefore, performance depends on the LINQ provider that is used, and also on the particular characteristics of your dataset or database schema.</span></span>

<span data-ttu-id="cd3de-120">ASP.NET Web API での OData クエリ オプションの使用に関する詳細については、次を参照してください。 [OData クエリ オプションをサポートしている](supporting-odata-query-options.md)します。</span><span class="sxs-lookup"><span data-stu-id="cd3de-120">For more information about using OData query options in ASP.NET Web API, see [Supporting OData Query Options](supporting-odata-query-options.md).</span></span>

<span data-ttu-id="cd3de-121">(たとえば、エンタープライズ環境) で、すべてのクライアントが信頼されていることがわかっている場合、または、データセットが小さい場合は、クエリのパフォーマンスが問題になりません。</span><span class="sxs-lookup"><span data-stu-id="cd3de-121">If you know that all clients are trusted (for example, in an enterprise environment), or if your dataset is small, query performance might not be an issue.</span></span> <span data-ttu-id="cd3de-122">それ以外の場合、次の推奨事項を考慮する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cd3de-122">Otherwise, you should consider the following recommendations.</span></span>

- <span data-ttu-id="cd3de-123">さまざまなクエリを使用してサービスをテストし、DB をプロファイルします。</span><span class="sxs-lookup"><span data-stu-id="cd3de-123">Test your service with various queries and profile the DB.</span></span>
- <span data-ttu-id="cd3de-124">1 つのクエリで大規模なデータ セットを返すことを回避するために、サーバー ドリブン ページングを有効にします。</span><span class="sxs-lookup"><span data-stu-id="cd3de-124">Enable server-driven paging, to avoid returning a large data set in one query.</span></span> <span data-ttu-id="cd3de-125">詳細については、次を参照してください。 [Server-Driven ページング](supporting-odata-query-options.md#server-paging)します。</span><span class="sxs-lookup"><span data-stu-id="cd3de-125">For more information, see [Server-Driven Paging](supporting-odata-query-options.md#server-paging).</span></span> 

    [!code-csharp[Main](odata-security-guidance/samples/sample3.cs)]
- <span data-ttu-id="cd3de-126">$Filter、$orderby が必要ですか。</span><span class="sxs-lookup"><span data-stu-id="cd3de-126">Do you need $filter and $orderby?</span></span> <span data-ttu-id="cd3de-127">一部のアプリケーションは、ページング、$top、$skip を使用してクライアントを許可するが、その他のクエリ オプションを無効にする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cd3de-127">Some applications might allow client paging, using $top and $skip, but disable the other query options.</span></span> 

    [!code-csharp[Main](odata-security-guidance/samples/sample4.cs)]
- <span data-ttu-id="cd3de-128">クラスター化インデックスのプロパティに $orderby を制限することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="cd3de-128">Consider restricting $orderby to properties in a clustered index.</span></span> <span data-ttu-id="cd3de-129">クラスター化インデックスがなくても大量のデータの並べ替えは、低速です。</span><span class="sxs-lookup"><span data-stu-id="cd3de-129">Sorting large data without a clustered index is slow.</span></span> 

    [!code-csharp[Main](odata-security-guidance/samples/sample5.cs)]
- <span data-ttu-id="cd3de-130">ノードの最大数:**MaxNodeCount**プロパティ **[Queryable]** $filter 構文ツリー内で許可されているノードの最大数を設定します。</span><span class="sxs-lookup"><span data-stu-id="cd3de-130">Maximum node count: The **MaxNodeCount** property on **[Queryable]** sets the maximum number nodes allowed in the $filter syntax tree.</span></span> <span data-ttu-id="cd3de-131">既定値は 100 が、コンパイルに時間がかかることができるノードの数が多いため、低い値を設定する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cd3de-131">The default value is 100, but you may want to set a lower value, because a large number of nodes can be slow to compile.</span></span> <span data-ttu-id="cd3de-132">これは LINQ to Objects (つまり、中間の LINQ プロバイダーを使用せずに、メモリ内コレクションに対して LINQ クエリ) を使用している場合に特に当てはまります。</span><span class="sxs-lookup"><span data-stu-id="cd3de-132">This is particularly true if you are using LINQ to Objects (i.e., LINQ queries on a collection in memory, without the use of an intermediate LINQ provider).</span></span> 

    [!code-csharp[Main](odata-security-guidance/samples/sample6.cs)]
- <span data-ttu-id="cd3de-133">Any() および all() の関数で無効にすると遅くなることが検討します。</span><span class="sxs-lookup"><span data-stu-id="cd3de-133">Consider disabling the any() and all() functions, as these can be slow.</span></span> 

    [!code-csharp[Main](odata-security-guidance/samples/sample7.cs)]
- <span data-ttu-id="cd3de-134">任意の文字列プロパティには、大きな文字列が含まれている場合&#8212;製品の説明、ブログ エントリなど、&#8212;文字列関数を無効にしてみます。</span><span class="sxs-lookup"><span data-stu-id="cd3de-134">If any string properties contain large strings&#8212;for example, a product description or a blog entry&#8212;consider disabling the string functions.</span></span> 

    [!code-csharp[Main](odata-security-guidance/samples/sample8.cs)]
- <span data-ttu-id="cd3de-135">ナビゲーション プロパティのフィルター処理を禁止することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="cd3de-135">Consider disallowing filtering on navigation properties.</span></span> <span data-ttu-id="cd3de-136">ナビゲーション プロパティのフィルター処理、結合、遅く、データベース スキーマに応じて、可能性のある可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cd3de-136">Filtering on navigation properties can result in a join, which might be slow, depending on your database schema.</span></span> <span data-ttu-id="cd3de-137">次のコードでは、ナビゲーション プロパティのフィルター処理しないようにクエリ検証コントロールを示します。</span><span class="sxs-lookup"><span data-stu-id="cd3de-137">The following code shows a query validator that prevents filtering on navigation properties.</span></span> <span data-ttu-id="cd3de-138">クエリ検証コントロールの詳細については、次を参照してください。[クエリ検証](supporting-odata-query-options.md#query-validation)です。</span><span class="sxs-lookup"><span data-stu-id="cd3de-138">For more information about query validators, see [Query Validation](supporting-odata-query-options.md#query-validation).</span></span> 

    [!code-csharp[Main](odata-security-guidance/samples/sample9.cs)]
- <span data-ttu-id="cd3de-139">データベース用にカスタマイズされている検証コントロールを記述することで $filter のクエリを制限することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="cd3de-139">Consider restricting $filter queries by writing a validator that is customized for your database.</span></span> <span data-ttu-id="cd3de-140">たとえば、これら 2 つのクエリがあるとします。</span><span class="sxs-lookup"><span data-stu-id="cd3de-140">For example, consider these two queries:</span></span> 

  - <span data-ttu-id="cd3de-141">すべての映画"A"で始まる最後の名前を持つ actors の使用。</span><span class="sxs-lookup"><span data-stu-id="cd3de-141">All movies with actors whose last name starts with ‘A'.</span></span>
  - <span data-ttu-id="cd3de-142">1994 年にリリースされたすべての映画します。</span><span class="sxs-lookup"><span data-stu-id="cd3de-142">All movies released in 1994.</span></span>

    <span data-ttu-id="cd3de-143">映画がアクターによってインデックスが作成しない限り、最初のクエリは、ムービーのリスト全体をスキャンする DB エンジンを必要があります。</span><span class="sxs-lookup"><span data-stu-id="cd3de-143">Unless movies are indexed by actors, the first query might require the DB engine to scan the entire list of movies.</span></span> <span data-ttu-id="cd3de-144">2 番目のクエリもかまいませんが、リリース年ごとと仮定して映画のインデックスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="cd3de-144">Whereas the second query might be acceptable, assuming movies are indexed by release year.</span></span>

    <span data-ttu-id="cd3de-145">次のコードでは、"ReleaseYear"と"Title"プロパティがないその他のプロパティのフィルター処理できるようにする検証コントロールを示します。</span><span class="sxs-lookup"><span data-stu-id="cd3de-145">The following code shows a validator that allows filtering on the "ReleaseYear" and "Title" properties but no other properties.</span></span>

    [!code-csharp[Main](odata-security-guidance/samples/sample10.cs)]
- <span data-ttu-id="cd3de-146">一般に、必要などの $filter 機能を検討してください。</span><span class="sxs-lookup"><span data-stu-id="cd3de-146">In general, consider which $filter functions you need.</span></span> <span data-ttu-id="cd3de-147">クライアントは $filter の完全な表現力を必要としない場合は、関数を使用できますを制限できます。</span><span class="sxs-lookup"><span data-stu-id="cd3de-147">If your clients do not need the full expressiveness of $filter, you can limit the allowed functions.</span></span>
