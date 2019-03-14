---
ms.openlocfilehash: 24726fba7f431f701b264a988a8de1b67d41d8a2
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57048719"
---
# <a name="add-search-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="66421-101">ASP.NET Core MVC アプリへの検索の追加</span><span class="sxs-lookup"><span data-stu-id="66421-101">Add search to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="66421-102">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="66421-102">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="66421-103">このセクションでは、検索機能を `Index` アクション メソッドに追加して、*ジャンル*または*名前*でムービーを検索できるようにします。</span><span class="sxs-lookup"><span data-stu-id="66421-103">In this section you add search capability to the `Index` action method that lets you search movies by *genre* or *name*.</span></span>

<span data-ttu-id="66421-104">`Index` メソッドを次のコードで更新します。</span><span class="sxs-lookup"><span data-stu-id="66421-104">Update the `Index` method with the following code:</span></span>
<!--
[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/_Layout.cshtml?highlight=7,31)]
-->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

<span data-ttu-id="66421-105">`Index` アクション メソッドの最初の行により、ムービーを選択する [LINQ](/dotnet/standard/using-linq) クエリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="66421-105">The first line of the `Index` action method creates a [LINQ](/dotnet/standard/using-linq) query to select the movies:</span></span>

```csharp
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="66421-106">このクエリはこの時点で*のみ*定義されます。データベースに対して**実行されていません**。</span><span class="sxs-lookup"><span data-stu-id="66421-106">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="66421-107">`searchString` パラメーターに文字列が含まれる場合、検索文字列の値でフィルターするようにムービー クエリが変更されます。</span><span class="sxs-lookup"><span data-stu-id="66421-107">If the `searchString` parameter contains a string, the movies query is modified to filter on the value of the search string:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

<span data-ttu-id="66421-108">上の `s => s.Title.Contains()` コードは[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)です。</span><span class="sxs-lookup"><span data-stu-id="66421-108">The `s => s.Title.Contains()` code above is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="66421-109">ラムダは、メソッド ベースの [LINQ](/dotnet/standard/using-linq) クエリで、[Where](/dotnet/api/system.linq.enumerable.where) メソッドや `Contains` (上のコードで使用されている) など、標準クエリ演算子メソッドの引数として使用されます。</span><span class="sxs-lookup"><span data-stu-id="66421-109">Lambdas are used in method-based [LINQ](/dotnet/standard/using-linq) queries as arguments to standard query operator methods such as the [Where](/dotnet/api/system.linq.enumerable.where) method or `Contains` (used in the code above).</span></span> <span data-ttu-id="66421-110">LINQ クエリは、`Where`、`Contains`、`OrderBy` などのメソッドの呼び出しで定義または変更されたときには実行されません。</span><span class="sxs-lookup"><span data-stu-id="66421-110">LINQ queries are not executed when they're defined or when they're modified by calling a method such as `Where`, `Contains`  or `OrderBy`.</span></span> <span data-ttu-id="66421-111">クエリ実行は先送りされます。</span><span class="sxs-lookup"><span data-stu-id="66421-111">Rather, query execution is deferred.</span></span>  <span data-ttu-id="66421-112">つまり、その具体値が実際に繰り返されるか、`ToListAsync` メソッドが呼び出されるまで、式の評価が延期されます。</span><span class="sxs-lookup"><span data-stu-id="66421-112">That means that the evaluation of an expression is delayed until its realized value is actually iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="66421-113">クエリの遅延実行の詳細については、「[クエリの実行](/dotnet/framework/data/adonet/ef/language-reference/query-execution)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="66421-113">For more information about deferred query execution, see [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span></span>

<span data-ttu-id="66421-114">メモ:[Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) メソッドは、上記の C# コードではなく、データベースで実行されます。</span><span class="sxs-lookup"><span data-stu-id="66421-114">Note: The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the c# code shown above.</span></span> <span data-ttu-id="66421-115">クエリの大文字と小文字の区別は、データベースや照合順序に依存します。</span><span class="sxs-lookup"><span data-stu-id="66421-115">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="66421-116">SQL Server では、[Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) は大文字と小文字の区別がない [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql) にマッピングされます。</span><span class="sxs-lookup"><span data-stu-id="66421-116">On SQL Server, [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="66421-117">SQLlite の場合、既定の照合順序で、大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="66421-117">In SQLlite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="66421-118">`/Movies/Index` に移動します。</span><span class="sxs-lookup"><span data-stu-id="66421-118">Navigate to `/Movies/Index`.</span></span> <span data-ttu-id="66421-119">`?searchString=Ghost` などのクエリ文字列を URL に追加します。</span><span class="sxs-lookup"><span data-stu-id="66421-119">Append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="66421-120">フィルターされたムービーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="66421-120">The filtered movies are displayed.</span></span>

![インデックス ビュー](~/tutorials/first-mvc-app/search/_static/ghost.png)

<span data-ttu-id="66421-122">`id` という名前のパラメーターを使用するために `Index` メソッドの署名を変更すると、`id` パラメーターは、*Startup.cs* で設定されている既定ルートの省略可能な `{id}` プレースホルダーと一致するようになります。</span><span class="sxs-lookup"><span data-stu-id="66421-122">If you change the signature of the `Index` method to have a parameter named `id`, the `id` parameter will match the optional `{id}` placeholder for the default routes set in *Startup.cs*.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]
