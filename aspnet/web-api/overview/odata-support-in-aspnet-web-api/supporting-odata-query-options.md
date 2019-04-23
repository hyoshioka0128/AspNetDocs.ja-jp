---
uid: web-api/overview/odata-support-in-aspnet-web-api/supporting-odata-query-options
title: ASP.NET Web API 2 - ASP.NET での OData クエリ オプションをサポートしている 4.x
author: MikeWasson
description: 概要のコード例と ASP.NET Web API 2 で ASP.NET のサポートの OData クエリ オプションでは、4.x です。
ms.author: riande
ms.date: 02/04/2013
ms.custom: seoapril2019
ms.assetid: 50e6e62b-e72e-4a29-8293-4b67377bd21f
msc.legacyurl: /web-api/overview/odata-support-in-aspnet-web-api/supporting-odata-query-options
msc.type: authoredcontent
ms.openlocfilehash: 428e4942e42436585049c1e84cd7b07a4a79c0d1
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59411567"
---
# <a name="supporting-odata-query-options-in-aspnet-web-api-2"></a><span data-ttu-id="185d0-103">ASP.NET web API 2 OData クエリ オプションをサポート</span><span class="sxs-lookup"><span data-stu-id="185d0-103">Supporting OData Query Options in ASP.NET Web API 2</span></span>

<span data-ttu-id="185d0-104">作成者[Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="185d0-104">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

<span data-ttu-id="185d0-105">コード例では、この概要では、ASP.NET Web API 2 では、サポートの OData クエリ オプションを紹介 for ASP.NET 4.x です。</span><span class="sxs-lookup"><span data-stu-id="185d0-105">This overview with code examples demonstrates the supporting OData Query Options in ASP.NET Web API 2 for ASP.NET 4.x.</span></span> 

<span data-ttu-id="185d0-106">OData では、OData のクエリを変更するために使用するパラメーターを定義します。</span><span class="sxs-lookup"><span data-stu-id="185d0-106">OData defines parameters that can be used to modify an OData query.</span></span> <span data-ttu-id="185d0-107">クライアントは、要求 URI のクエリ文字列で、これらのパラメーターを送信します。</span><span class="sxs-lookup"><span data-stu-id="185d0-107">The client sends these parameters in the query string of the request URI.</span></span> <span data-ttu-id="185d0-108">たとえば、クライアントに結果を並べ替えるには、$orderby パラメーターを使用してください。</span><span class="sxs-lookup"><span data-stu-id="185d0-108">For example, to sort the results, a client uses the $orderby parameter:</span></span>

`http://localhost/Products?$orderby=Name`

<span data-ttu-id="185d0-109">OData の仕様は、これらのパラメーターを呼び出す*クエリ オプション*します。</span><span class="sxs-lookup"><span data-stu-id="185d0-109">The OData specification calls these parameters *query options*.</span></span> <span data-ttu-id="185d0-110">プロジェクトで任意の Web API コント ローラーの OData クエリ オプションを有効にできます&#8212;コント ローラーは、OData エンドポイントである必要はありません。</span><span class="sxs-lookup"><span data-stu-id="185d0-110">You can enable OData query options for any Web API controller in your project &#8212; the controller does not need to be an OData endpoint.</span></span> <span data-ttu-id="185d0-111">これにより、フィルター処理およびすべての Web API アプリケーションに並べ替えなどの機能を追加する便利な方法です。</span><span class="sxs-lookup"><span data-stu-id="185d0-111">This gives you a convenient way to add features such as filtering and sorting to any Web API application.</span></span>

<span data-ttu-id="185d0-112">トピックを参照してください、クエリ オプションを有効にする前に[OData セキュリティ ガイダンス](odata-security-guidance.md)します。</span><span class="sxs-lookup"><span data-stu-id="185d0-112">Before enabling query options, please read the topic [OData Security Guidance](odata-security-guidance.md).</span></span>

- [<span data-ttu-id="185d0-113">OData クエリ オプションを有効にします。</span><span class="sxs-lookup"><span data-stu-id="185d0-113">Enabling OData Query Options</span></span>](#enable)
- [<span data-ttu-id="185d0-114">クエリの例</span><span class="sxs-lookup"><span data-stu-id="185d0-114">Example Queries</span></span>](#examples)
- [<span data-ttu-id="185d0-115">サーバー ドリブン ページング</span><span class="sxs-lookup"><span data-stu-id="185d0-115">Server-Driven Paging</span></span>](#server-paging)
- [<span data-ttu-id="185d0-116">クエリ オプションを制限します。</span><span class="sxs-lookup"><span data-stu-id="185d0-116">Limiting the Query Options</span></span>](#limiting_query_options)
- [<span data-ttu-id="185d0-117">クエリ オプションを直接呼び出す</span><span class="sxs-lookup"><span data-stu-id="185d0-117">Invoking Query Options Directly</span></span>](#ODataQueryOptions)
- [<span data-ttu-id="185d0-118">クエリの検証</span><span class="sxs-lookup"><span data-stu-id="185d0-118">Query Validation</span></span>](#query-validation)

<a id="enable"></a>
## <a name="enabling-odata-query-options"></a><span data-ttu-id="185d0-119">OData クエリ オプションを有効にします。</span><span class="sxs-lookup"><span data-stu-id="185d0-119">Enabling OData Query Options</span></span>

<span data-ttu-id="185d0-120">Web API には、次の OData クエリ オプションがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="185d0-120">Web API supports the following OData query options:</span></span>

| <span data-ttu-id="185d0-121">オプション</span><span class="sxs-lookup"><span data-stu-id="185d0-121">Option</span></span> | <span data-ttu-id="185d0-122">説明</span><span class="sxs-lookup"><span data-stu-id="185d0-122">Description</span></span> |
| --- | --- |
| <span data-ttu-id="185d0-123">$ を展開します。</span><span class="sxs-lookup"><span data-stu-id="185d0-123">$expand</span></span> | <span data-ttu-id="185d0-124">インラインの関連エンティティを展開します。</span><span class="sxs-lookup"><span data-stu-id="185d0-124">Expands related entities inline.</span></span> |
| <span data-ttu-id="185d0-125">$filter</span><span class="sxs-lookup"><span data-stu-id="185d0-125">$filter</span></span> | <span data-ttu-id="185d0-126">ブール条件に基づいて、結果をフィルター処理します。</span><span class="sxs-lookup"><span data-stu-id="185d0-126">Filters the results, based on a Boolean condition.</span></span> |
| <span data-ttu-id="185d0-127">$inlinecount</span><span class="sxs-lookup"><span data-stu-id="185d0-127">$inlinecount</span></span> | <span data-ttu-id="185d0-128">一致するエンティティの合計数を応答に含めるようにサーバーに指示します。</span><span class="sxs-lookup"><span data-stu-id="185d0-128">Tells the server to include the total count of matching entities in the response.</span></span> <span data-ttu-id="185d0-129">(サーバー側のページングに役立ちます。)</span><span class="sxs-lookup"><span data-stu-id="185d0-129">(Useful for server-side paging.)</span></span> |
| <span data-ttu-id="185d0-130">$orderby</span><span class="sxs-lookup"><span data-stu-id="185d0-130">$orderby</span></span> | <span data-ttu-id="185d0-131">結果を並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="185d0-131">Sorts the results.</span></span> |
| <span data-ttu-id="185d0-132">$select</span><span class="sxs-lookup"><span data-stu-id="185d0-132">$select</span></span> | <span data-ttu-id="185d0-133">応答に含めるプロパティを選択します。</span><span class="sxs-lookup"><span data-stu-id="185d0-133">Selects which properties to include in the response.</span></span> |
| <span data-ttu-id="185d0-134">$skip</span><span class="sxs-lookup"><span data-stu-id="185d0-134">$skip</span></span> | <span data-ttu-id="185d0-135">最初の n 個の結果をスキップします。</span><span class="sxs-lookup"><span data-stu-id="185d0-135">Skips the first n results.</span></span> |
| <span data-ttu-id="185d0-136">$top</span><span class="sxs-lookup"><span data-stu-id="185d0-136">$top</span></span> | <span data-ttu-id="185d0-137">最初の n のみに結果を返します。</span><span class="sxs-lookup"><span data-stu-id="185d0-137">Returns only the first n the results.</span></span> |

<span data-ttu-id="185d0-138">OData クエリ オプションを使用するには、必要があります有効に明示的にします。</span><span class="sxs-lookup"><span data-stu-id="185d0-138">To use OData query options, you must enable them explicitly.</span></span> <span data-ttu-id="185d0-139">特定のコント ローラーまたは特定のアクションのために有効または、アプリケーション全体でグローバルに有効にできます。</span><span class="sxs-lookup"><span data-stu-id="185d0-139">You can enable them globally for the entire application, or enable them for specific controllers or specific actions.</span></span>

<span data-ttu-id="185d0-140">OData クエリ オプションをグローバルに有効にするを呼び出す**EnableQuerySupport**上、 **HttpConfiguration**起動時クラス。</span><span class="sxs-lookup"><span data-stu-id="185d0-140">To enable OData query options globally, call **EnableQuerySupport** on the **HttpConfiguration** class at startup:</span></span>

[!code-csharp[Main](supporting-odata-query-options/samples/sample1.cs)]

<span data-ttu-id="185d0-141">**EnableQuerySupport**メソッドを返すコント ローラーのアクションに対してグローバルにクエリ オプションを使用できます、 **IQueryable**型。</span><span class="sxs-lookup"><span data-stu-id="185d0-141">The **EnableQuerySupport** method enables query options globally for any controller action that returns an **IQueryable** type.</span></span> <span data-ttu-id="185d0-142">クエリ オプションをアプリケーション全体に対して有効にしない場合は、ことができますの特定のコント ローラーのアクションを追加して、 **[Queryable]** 属性をアクション メソッド。</span><span class="sxs-lookup"><span data-stu-id="185d0-142">If you don't want query options enabled for the entire application, you can enable them for specific controller actions by adding the **[Queryable]** attribute to the action method.</span></span>

[!code-csharp[Main](supporting-odata-query-options/samples/sample2.cs)]

<a id="examples"></a>
## <a name="example-queries"></a><span data-ttu-id="185d0-143">クエリの例</span><span class="sxs-lookup"><span data-stu-id="185d0-143">Example Queries</span></span>

<span data-ttu-id="185d0-144">このセクションでは、OData クエリ オプションを使用してクエリの種類が表示されます。</span><span class="sxs-lookup"><span data-stu-id="185d0-144">This section shows the types of queries that are possible using the OData query options.</span></span> <span data-ttu-id="185d0-145">特定の詳細については、クエリ オプションでは、OData ドキュメントを参照してください[www.odata.org](http://www.odata.org/)します。</span><span class="sxs-lookup"><span data-stu-id="185d0-145">For specific details about the query options, refer to the OData documentation at [www.odata.org](http://www.odata.org/).</span></span>

<span data-ttu-id="185d0-146">$ について順に展開し、$select を参照してください[$ の $select を使用して、展開と ASP.NET Web API odata $value](using-select-expand-and-value.md)。</span><span class="sxs-lookup"><span data-stu-id="185d0-146">For information about $expand and $select, see [Using $select, $expand, and $value in ASP.NET Web API OData](using-select-expand-and-value.md).</span></span>

<span data-ttu-id="185d0-147">**クライアント ドリブン ページング**</span><span class="sxs-lookup"><span data-stu-id="185d0-147">**Client-Driven Paging**</span></span>

<span data-ttu-id="185d0-148">大きなエンティティ セットの場合は、クライアントは、結果の数を制限する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="185d0-148">For large entity sets, the client might want to limit the number of results.</span></span> <span data-ttu-id="185d0-149">たとえば、クライアントは、結果の次のページを取得する [次へ] のリンク時に 10 個のエントリを表示する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="185d0-149">For example, a client might show 10 entries at a time, with "next" links to get the next page of results.</span></span> <span data-ttu-id="185d0-150">これを行うには、クライアントは、$top、$skip オプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="185d0-150">To do this, the client uses the $top and $skip options.</span></span>

`http://localhost/Products?$top=10&$skip=20`

<span data-ttu-id="185d0-151">$Top オプションは、返される項目の最大数と、$skip オプションは、スキップするエントリの数を示します。</span><span class="sxs-lookup"><span data-stu-id="185d0-151">The $top option gives the maximum number of entries to return, and the $skip option gives the number of entries to skip.</span></span> <span data-ttu-id="185d0-152">前の例では、21 ~ 30 のエントリをフェッチします。</span><span class="sxs-lookup"><span data-stu-id="185d0-152">The previous example fetches entries 21 through 30.</span></span>

<span data-ttu-id="185d0-153">**フィルター処理**</span><span class="sxs-lookup"><span data-stu-id="185d0-153">**Filtering**</span></span>

<span data-ttu-id="185d0-154">$Filter オプションは、ブール式を適用することで、結果をフィルター処理クライアントを使用できます。</span><span class="sxs-lookup"><span data-stu-id="185d0-154">The $filter option lets a client filter the results by applying a Boolean expression.</span></span> <span data-ttu-id="185d0-155">フィルター式は非常に強力です。論理演算子および算術演算子、文字列関数、および日付関数が含まれます。</span><span class="sxs-lookup"><span data-stu-id="185d0-155">The filter expressions are quite powerful; they include logical and arithmetic operators, string functions, and date functions.</span></span>

| <span data-ttu-id="185d0-156">"Toys"に等しいすべての製品のカテゴリを返します。</span><span class="sxs-lookup"><span data-stu-id="185d0-156">Return all products with category equal to "Toys".</span></span> | <span data-ttu-id="185d0-157">`http://localhost/Products?$filter=Category` eq 'Toys'</span><span class="sxs-lookup"><span data-stu-id="185d0-157">`http://localhost/Products?$filter=Category` eq 'Toys'</span></span> |
| --- | --- |
| <span data-ttu-id="185d0-158">すべての製品の価格が 10 よりも小さいかを返します。</span><span class="sxs-lookup"><span data-stu-id="185d0-158">Return all products with price less than 10.</span></span> | <span data-ttu-id="185d0-159">`http://localhost/Products?$filter=Price` lt 10</span><span class="sxs-lookup"><span data-stu-id="185d0-159">`http://localhost/Products?$filter=Price` lt 10</span></span> |
| <span data-ttu-id="185d0-160">論理演算子 : すべての製品を返す価格、> = 5 と価格 < 15 を = です。</span><span class="sxs-lookup"><span data-stu-id="185d0-160">Logical operators: Return all products where price >= 5 and price <= 15.</span></span> | <span data-ttu-id="185d0-161">`http://localhost/Products?$filter=Price` ge 5 と価格 le 15</span><span class="sxs-lookup"><span data-stu-id="185d0-161">`http://localhost/Products?$filter=Price` ge 5 and Price le 15</span></span> |
| <span data-ttu-id="185d0-162">文字列関数。すべての製品"zz"の名前を返します。</span><span class="sxs-lookup"><span data-stu-id="185d0-162">String functions: Return all products with "zz" in the name.</span></span> | `http://localhost/Products?$filter=substringof('zz',Name)` |
| <span data-ttu-id="185d0-163">日付関数:2005 より後に、すべての製品の ReleaseDate を返します。</span><span class="sxs-lookup"><span data-stu-id="185d0-163">Date functions: Return all products with ReleaseDate after 2005.</span></span> | <span data-ttu-id="185d0-164">`http://localhost/Products?$filter=year(ReleaseDate)` gt 2005</span><span class="sxs-lookup"><span data-stu-id="185d0-164">`http://localhost/Products?$filter=year(ReleaseDate)` gt 2005</span></span> |

<span data-ttu-id="185d0-165">**並べ替え**</span><span class="sxs-lookup"><span data-stu-id="185d0-165">**Sorting**</span></span>

<span data-ttu-id="185d0-166">結果を並べ替えるには、$orderby フィルターを使用します。</span><span class="sxs-lookup"><span data-stu-id="185d0-166">To sort the results, use the $orderby filter.</span></span>

| <span data-ttu-id="185d0-167">価格で並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="185d0-167">Sort by price.</span></span> | `http://localhost/Products?$orderby=Price` |
| --- | --- |
| <span data-ttu-id="185d0-168">降順 (最低に最高) の価格で並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="185d0-168">Sort by price in descending order (highest to lowest).</span></span> | `http://localhost/Products?$orderby=Price desc` |
| <span data-ttu-id="185d0-169">カテゴリ別に並べ替えるし、価格のカテゴリ内の降順で並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="185d0-169">Sort by category, then sort by price in descending order within categories.</span></span> | `http://localhost/odata/Products?$orderby=Category,Price desc` |

<a id="server-paging"></a>
## <a name="server-driven-paging"></a><span data-ttu-id="185d0-170">サーバー ドリブン ページング</span><span class="sxs-lookup"><span data-stu-id="185d0-170">Server-Driven Paging</span></span>

<span data-ttu-id="185d0-171">送信しない場合は、データベースには、何百万もレコードにはが含まれています、すべて 1 つのペイロードにします。</span><span class="sxs-lookup"><span data-stu-id="185d0-171">If your database contains millions of records, you don't want to send them all in one payload.</span></span> <span data-ttu-id="185d0-172">これを防ぐためには、サーバーは、1 つの応答で送信するエントリの数を制限できます。</span><span class="sxs-lookup"><span data-stu-id="185d0-172">To prevent this, the server can limit the number of entries that it sends in a single response.</span></span> <span data-ttu-id="185d0-173">サーバーのページングを有効にするには設定、 **PageSize**プロパティ、 **Queryable**属性。</span><span class="sxs-lookup"><span data-stu-id="185d0-173">To enable server paging, set the **PageSize** property in the **Queryable** attribute.</span></span> <span data-ttu-id="185d0-174">値は、返される項目の最大数です。</span><span class="sxs-lookup"><span data-stu-id="185d0-174">The value is the maximum number of entries to return.</span></span>

[!code-csharp[Main](supporting-odata-query-options/samples/sample3.cs)]

<span data-ttu-id="185d0-175">コント ローラーには、OData 形式が返された場合、応答本文にはデータの次のページへのリンクが含まれます。</span><span class="sxs-lookup"><span data-stu-id="185d0-175">If your controller returns OData format, the response body will contain a link to the next page of data:</span></span>

[!code-json[Main](supporting-odata-query-options/samples/sample4.json?highlight=8)]

<span data-ttu-id="185d0-176">クライアントは、次のページをフェッチするのにこのリンクを使用できます。</span><span class="sxs-lookup"><span data-stu-id="185d0-176">The client can use this link to fetch the next page.</span></span> <span data-ttu-id="185d0-177">結果セット内のエントリの合計数については、クライアント設定できます $inlinecount クエリ オプション値を持つ"allpages"。</span><span class="sxs-lookup"><span data-stu-id="185d0-177">To learn the total number of entries in the result set, the client can set the $inlinecount query option with the value "allpages".</span></span>

`http://localhost/Products?$inlinecount=allpages`

<span data-ttu-id="185d0-178">値"allpages"応答の合計数を追加するには、サーバーに指示します。</span><span class="sxs-lookup"><span data-stu-id="185d0-178">The value "allpages" tells the server to include the total count in the response:</span></span>

[!code-json[Main](supporting-odata-query-options/samples/sample5.json?highlight=3)]

> [!NOTE]
> <span data-ttu-id="185d0-179">次のページへのリンクとインライン カウントの OData 形式が必要です。</span><span class="sxs-lookup"><span data-stu-id="185d0-179">Next-page links and inline count both require OData format.</span></span> <span data-ttu-id="185d0-180">理由は、OData のリンクとカウントを保持するために、応答本文で特別なフィールドを定義します。</span><span class="sxs-lookup"><span data-stu-id="185d0-180">The reason is that OData defines special fields in the response body to hold the link and count.</span></span>


<span data-ttu-id="185d0-181">クエリの結果をラップすることで、次のページのリンクとインライン カウントをサポートするためにできますが、非 OData 形式の場合、 **PageResult&lt;T&gt;** オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="185d0-181">For non-OData formats, it is still possible to support next-page links and inline count, by wrapping the query results in a **PageResult&lt;T&gt;** object.</span></span> <span data-ttu-id="185d0-182">ただし、少しコードが必要です。</span><span class="sxs-lookup"><span data-stu-id="185d0-182">However, it requires a bit more code.</span></span> <span data-ttu-id="185d0-183">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="185d0-183">Here is an example:</span></span>

[!code-csharp[Main](supporting-odata-query-options/samples/sample6.cs)]

<span data-ttu-id="185d0-184">JSON 応答例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="185d0-184">Here is an example JSON response:</span></span>

[!code-json[Main](supporting-odata-query-options/samples/sample7.json)]

<a id="limiting_query_options"></a>
## <a name="limiting-the-query-options"></a><span data-ttu-id="185d0-185">クエリ オプションを制限します。</span><span class="sxs-lookup"><span data-stu-id="185d0-185">Limiting the Query Options</span></span>

<span data-ttu-id="185d0-186">クエリ オプションは、クライアントに多数のサーバーで実行されるクエリに制御を提供します。</span><span class="sxs-lookup"><span data-stu-id="185d0-186">The query options give the client a lot of control over the query that is run on the server.</span></span> <span data-ttu-id="185d0-187">場合によっては、セキュリティやパフォーマンス上の理由から使用可能なオプションを制限する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="185d0-187">In some cases, you might want to limit the available options for security or performance reasons.</span></span> <span data-ttu-id="185d0-188">**[Queryable]** 属性が一部の組み込みプロパティでこのします。</span><span class="sxs-lookup"><span data-stu-id="185d0-188">The **[Queryable]** attribute has some built in properties for this.</span></span> <span data-ttu-id="185d0-189">以下に例をいくつか示します。</span><span class="sxs-lookup"><span data-stu-id="185d0-189">Here are some examples.</span></span>

<span data-ttu-id="185d0-190">ページングと何をサポートするために、$skip、$top、のみを許可します。</span><span class="sxs-lookup"><span data-stu-id="185d0-190">Allow only $skip and $top, to support paging and nothing else:</span></span>

[!code-csharp[Main](supporting-odata-query-options/samples/sample8.cs)]

<span data-ttu-id="185d0-191">特定のプロパティによってのみ、データベースでインデックス化されていないプロパティで並べ替えを防ぐために順序付けを許可します。</span><span class="sxs-lookup"><span data-stu-id="185d0-191">Allow ordering only by certain properties, to prevent sorting on properties that are not indexed in the database:</span></span>

[!code-csharp[Main](supporting-odata-query-options/samples/sample9.cs)]

<span data-ttu-id="185d0-192">"Eq"論理関数ですがないその他の論理関数を許可します。</span><span class="sxs-lookup"><span data-stu-id="185d0-192">Allow the "eq" logical function but no other logical functions:</span></span>

[!code-csharp[Main](supporting-odata-query-options/samples/sample10.cs)]

<span data-ttu-id="185d0-193">すべての算術演算子をことはできません。</span><span class="sxs-lookup"><span data-stu-id="185d0-193">Do not allow any arithmetic operators:</span></span>

[!code-csharp[Main](supporting-odata-query-options/samples/sample11.cs)]

<span data-ttu-id="185d0-194">オプションをグローバルに制限するには作成することにより、 **QueryableAttribute**インスタンスとに渡して、 **EnableQuerySupport**関数。</span><span class="sxs-lookup"><span data-stu-id="185d0-194">You can restrict options globally by constructing a **QueryableAttribute** instance and passing it to the **EnableQuerySupport** function:</span></span>

[!code-csharp[Main](supporting-odata-query-options/samples/sample12.cs)]

<a id="ODataQueryOptions"></a>
## <a name="invoking-query-options-directly"></a><span data-ttu-id="185d0-195">クエリ オプションを直接呼び出す</span><span class="sxs-lookup"><span data-stu-id="185d0-195">Invoking Query Options Directly</span></span>

<span data-ttu-id="185d0-196">使用する代わりに、 **[Queryable]** 属性をコント ローラーで直接クエリ オプションを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="185d0-196">Instead of using the **[Queryable]** attribute, you can invoke the query options directly in your controller.</span></span> <span data-ttu-id="185d0-197">これを行うには、追加、 **ODataQueryOptions**コント ローラー メソッドのパラメーター。</span><span class="sxs-lookup"><span data-stu-id="185d0-197">To do so, add an **ODataQueryOptions** parameter to the controller method.</span></span> <span data-ttu-id="185d0-198">この場合、必要はありません、 **[Queryable]** 属性。</span><span class="sxs-lookup"><span data-stu-id="185d0-198">In this case, you don't need the **[Queryable]** attribute.</span></span>

[!code-csharp[Main](supporting-odata-query-options/samples/sample13.cs)]

<span data-ttu-id="185d0-199">Web API の作成、 **ODataQueryOptions** URI からクエリ文字列。</span><span class="sxs-lookup"><span data-stu-id="185d0-199">Web API populates the **ODataQueryOptions** from the URI query string.</span></span> <span data-ttu-id="185d0-200">クエリを適用するには、渡す、 **IQueryable**を**ApplyTo**メソッド。</span><span class="sxs-lookup"><span data-stu-id="185d0-200">To apply the query, pass an **IQueryable** to the **ApplyTo** method.</span></span> <span data-ttu-id="185d0-201">メソッドを返します別**IQueryable**します。</span><span class="sxs-lookup"><span data-stu-id="185d0-201">The method returns another **IQueryable**.</span></span>

<span data-ttu-id="185d0-202">高度なシナリオではない場合、 **IQueryable**クエリ プロバイダーを調べることができます、 **ODataQueryOptions**し、別のフォームへのクエリ オプションを変換します。</span><span class="sxs-lookup"><span data-stu-id="185d0-202">For advanced scenarios, if you do not have an **IQueryable** query provider, you can examine the **ODataQueryOptions** and translate the query options into another form.</span></span> <span data-ttu-id="185d0-203">(たとえば、RaghuRam Nadiminti のブログの投稿を参照してください[変換の OData クエリの HQL へ](https://blogs.msdn.com/b/webdev/archive/2013/02/25/translating-odata-queries-to-hql.aspx)も含まれていますが、[サンプル](http://aspnet.codeplex.com/SourceControl/changeset/view/75a56ec99968#Samples/WebApi/NHibernateQueryableSample/Readme.txt))。</span><span class="sxs-lookup"><span data-stu-id="185d0-203">(For example, see RaghuRam Nadiminti's blog post [Translating OData queries to HQL](https://blogs.msdn.com/b/webdev/archive/2013/02/25/translating-odata-queries-to-hql.aspx), which also includes a [sample](http://aspnet.codeplex.com/SourceControl/changeset/view/75a56ec99968#Samples/WebApi/NHibernateQueryableSample/Readme.txt).)</span></span>

<a id="query-validation"></a>
## <a name="query-validation"></a><span data-ttu-id="185d0-204">クエリの検証</span><span class="sxs-lookup"><span data-stu-id="185d0-204">Query Validation</span></span>

<span data-ttu-id="185d0-205">**[Queryable]** 属性は、実行する前に、クエリを検証します。</span><span class="sxs-lookup"><span data-stu-id="185d0-205">The **[Queryable]** attribute validates the query before executing it.</span></span> <span data-ttu-id="185d0-206">検証手順がで実行される、 **QueryableAttribute.ValidateQuery**メソッド。</span><span class="sxs-lookup"><span data-stu-id="185d0-206">The validation step is performed in the **QueryableAttribute.ValidateQuery** method.</span></span> <span data-ttu-id="185d0-207">検証プロセスをカスタマイズすることもできます。</span><span class="sxs-lookup"><span data-stu-id="185d0-207">You can also customize the validation process.</span></span>

<span data-ttu-id="185d0-208">参照してください[OData セキュリティ ガイダンス](odata-security-guidance.md)します。</span><span class="sxs-lookup"><span data-stu-id="185d0-208">Also see [OData Security Guidance](odata-security-guidance.md).</span></span>

<span data-ttu-id="185d0-209">最初に、オーバーライドされている検証コントロールのいずれかのクラスが定義されている、 **Web.Http.OData.Query.Validators**名前空間。</span><span class="sxs-lookup"><span data-stu-id="185d0-209">First, override one of the validator classes that is defined in the **Web.Http.OData.Query.Validators** namespace.</span></span> <span data-ttu-id="185d0-210">たとえば、次の検証コントロール クラスには、$orderby オプションの 'desc' オプションが無効にします。</span><span class="sxs-lookup"><span data-stu-id="185d0-210">For example, the following validator class disables the 'desc' option for the $orderby option.</span></span>

[!code-csharp[Main](supporting-odata-query-options/samples/sample14.cs)]

<span data-ttu-id="185d0-211">サブクラス、 **[Queryable]** をオーバーライドする属性、 **ValidateQuery**メソッド。</span><span class="sxs-lookup"><span data-stu-id="185d0-211">Subclass the **[Queryable]** attribute to override the **ValidateQuery** method.</span></span>

[!code-csharp[Main](supporting-odata-query-options/samples/sample15.cs)]

<span data-ttu-id="185d0-212">カスタム属性を設定するかグローバルにまたはコント ローラーごと。</span><span class="sxs-lookup"><span data-stu-id="185d0-212">Then set your custom attribute either globally or per-controller:</span></span>

[!code-csharp[Main](supporting-odata-query-options/samples/sample16.cs)]

<span data-ttu-id="185d0-213">使用する場合**ODataQueryOptions**を直接検証コントロールをオプションに設定します。</span><span class="sxs-lookup"><span data-stu-id="185d0-213">If you are using **ODataQueryOptions** directly, set the validator on the options:</span></span>

[!code-csharp[Main](supporting-odata-query-options/samples/sample17.cs)]
