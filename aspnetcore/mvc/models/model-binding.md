---
title: ASP.NET Core でのモデル バインド
author: tdykstra
description: ASP.NET Core MVC のモデル バインドでは、HTTP 要求からアクション メソッドのパラメーターにデータをマップする方法について説明します。
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: tdykstra
ms.date: 11/13/2018
uid: mvc/models/model-binding
ms.openlocfilehash: 1dc9b41328ed78440622acc1865b6f088d394403
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57037049"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="85987-103">ASP.NET Core でのモデル バインド</span><span class="sxs-lookup"><span data-stu-id="85987-103">Model Binding in ASP.NET Core</span></span>

<span data-ttu-id="85987-104">作成者: [Rachel Appel](https://github.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="85987-104">By [Rachel Appel](https://github.com/rachelappel)</span></span>

## <a name="introduction-to-model-binding"></a><span data-ttu-id="85987-105">モデル バインドの概要</span><span class="sxs-lookup"><span data-stu-id="85987-105">Introduction to model binding</span></span>

<span data-ttu-id="85987-106">ASP.NET Core MVC のモデル バインドでは、HTTP 要求からアクション メソッドのパラメーターにデータをマップします。</span><span class="sxs-lookup"><span data-stu-id="85987-106">Model binding in ASP.NET Core MVC maps data from HTTP requests to action method parameters.</span></span> <span data-ttu-id="85987-107">パラメーターは、文字列、整数、浮動小数点などの単純型である場合や、複合型である場合があります。</span><span class="sxs-lookup"><span data-stu-id="85987-107">The parameters may be simple types such as strings, integers, or floats, or they may be complex types.</span></span> <span data-ttu-id="85987-108">これは MVC の優れた機能です。データのサイズや複雑さに関係なく、多くの場合、受信データは対応するものに繰り返しマップされるためです。</span><span class="sxs-lookup"><span data-stu-id="85987-108">This is a great feature of MVC because mapping incoming data to a counterpart is an often repeated scenario, regardless of size or complexity of the data.</span></span> <span data-ttu-id="85987-109">MVC では、開発者が各アプリで同じコードの若干異なるバージョンを再書き込みしなくてすむように、バインドを抽象化することでこの問題を解決します。</span><span class="sxs-lookup"><span data-stu-id="85987-109">MVC solves this problem by abstracting binding away so developers don't have to keep rewriting a slightly different version of that same code in every app.</span></span> <span data-ttu-id="85987-110">独自のテキストを書き込んでコンバーター コードを入力するのは面倒でエラーが発生しやすくなります。</span><span class="sxs-lookup"><span data-stu-id="85987-110">Writing your own text to type converter code is tedious, and error prone.</span></span>

## <a name="how-model-binding-works"></a><span data-ttu-id="85987-111">モデル バインドのしくみ</span><span class="sxs-lookup"><span data-stu-id="85987-111">How model binding works</span></span>

<span data-ttu-id="85987-112">MVC は HTTP 要求を受信すると、それをコントローラーの特定のアクション メソッドにルーティングします。</span><span class="sxs-lookup"><span data-stu-id="85987-112">When MVC receives an HTTP request, it routes it to a specific action method of a controller.</span></span> <span data-ttu-id="85987-113">ルート データの内容に基づいて実行するアクション メソッドを決定し、次に HTTP 要求からそのアクション メソッドのパラメーターに値をバインドします。</span><span class="sxs-lookup"><span data-stu-id="85987-113">It determines which action method to run based on what is in the route data, then it binds values from the HTTP request to that action method's parameters.</span></span> <span data-ttu-id="85987-114">たとえば、次のような URL があるとします。</span><span class="sxs-lookup"><span data-stu-id="85987-114">For example, consider the following URL:</span></span>

`http://contoso.com/movies/edit/2`

<span data-ttu-id="85987-115">ルート テンプレートは `{controller=Home}/{action=Index}/{id?}` のようになるため、`movies/edit/2` は `Movies` コントローラーと、その `Edit` アクション メソッドにルーティングします。</span><span class="sxs-lookup"><span data-stu-id="85987-115">Since the route template looks like this, `{controller=Home}/{action=Index}/{id?}`, `movies/edit/2` routes to the `Movies` controller, and its `Edit` action method.</span></span> <span data-ttu-id="85987-116">また、`id` という省略可能なパラメーターも受け入れます。</span><span class="sxs-lookup"><span data-stu-id="85987-116">It also accepts an optional parameter called `id`.</span></span> <span data-ttu-id="85987-117">アクション メソッドのコードは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="85987-117">The code for the action method should look something like this:</span></span>

```csharp
public IActionResult Edit(int? id)
   ```

<span data-ttu-id="85987-118">メモ:URL ルート内の文字列は大文字小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="85987-118">Note: The strings in the URL route are not case sensitive.</span></span>

<span data-ttu-id="85987-119">MVC は、名前を使用して要求データをアクション パラメーターにバインドしようとします。</span><span class="sxs-lookup"><span data-stu-id="85987-119">MVC will try to bind request data to the action parameters by name.</span></span> <span data-ttu-id="85987-120">MVC は、パラメーター名とパブリックに設定可能なプロパティの名前を使用して、各パラメーターの値を探します。</span><span class="sxs-lookup"><span data-stu-id="85987-120">MVC will look for values for each parameter using the parameter name and the names of its public settable properties.</span></span> <span data-ttu-id="85987-121">上記の例では、アクション パラメーターのみに `id` という名前が付いています。したがって、MVC はルート値の同じ名前を使用して値にバインドします。</span><span class="sxs-lookup"><span data-stu-id="85987-121">In the above example, the only action parameter is named `id`, which MVC binds to the value with the same name in the route values.</span></span> <span data-ttu-id="85987-122">ルート値に加え、MVC は要求のさまざまな部分のデータをバインドします。これはセット順に行われます。</span><span class="sxs-lookup"><span data-stu-id="85987-122">In addition to route values MVC will bind data from various parts of the request and it does so in a set order.</span></span> <span data-ttu-id="85987-123">以下に、モデル バインドでの検索順にデータ ソースをリストします。</span><span class="sxs-lookup"><span data-stu-id="85987-123">Below is a list of the data sources in the order that model binding looks through them:</span></span>

1. <span data-ttu-id="85987-124">`Form values`:これらは、POST メソッドを使用して HTTP 要求に追加するフォームの値です。</span><span class="sxs-lookup"><span data-stu-id="85987-124">`Form values`: These are form values that go in the HTTP request using the POST method.</span></span> <span data-ttu-id="85987-125">(jQuery POST 要求を含む)。</span><span class="sxs-lookup"><span data-stu-id="85987-125">(including jQuery POST requests).</span></span>

2. <span data-ttu-id="85987-126">`Route values`:によって提供されるルート値のセット[ルーティング](xref:fundamentals/routing)</span><span class="sxs-lookup"><span data-stu-id="85987-126">`Route values`: The set of route values provided by [Routing](xref:fundamentals/routing)</span></span>

3. <span data-ttu-id="85987-127">`Query strings`:URI のクエリ文字列の一部です。</span><span class="sxs-lookup"><span data-stu-id="85987-127">`Query strings`: The query string part of the URI.</span></span>

<!-- DocFX BUG
The link works but generates an error when building with DocFX
@fundamentals/routing
[Routing](xref:fundamentals/routing)
-->

<span data-ttu-id="85987-128">メモ:フォームの値、ルート データ、およびクエリ文字列はすべて名前と値のペアとして格納します。</span><span class="sxs-lookup"><span data-stu-id="85987-128">Note: Form values, route data, and query strings are all stored as name-value pairs.</span></span>

<span data-ttu-id="85987-129">モデル バインドでは `id` という名前のキーが求められましたが、フォーム値に `id` という名前のものがないため、ルート値に移動してそのキーを探しました。</span><span class="sxs-lookup"><span data-stu-id="85987-129">Since model binding asked for a key named `id` and there's nothing named `id` in the form values, it moved on to the route values looking for that key.</span></span> <span data-ttu-id="85987-130">この例では、一致するものがあります。</span><span class="sxs-lookup"><span data-stu-id="85987-130">In our example, it's a match.</span></span> <span data-ttu-id="85987-131">バインドが行われ、値は整数の 2 に変換されます。</span><span class="sxs-lookup"><span data-stu-id="85987-131">Binding happens, and the value is converted to the integer 2.</span></span> <span data-ttu-id="85987-132">Edit(string id) を使用する同じ要求では、文字列 "2" に変換されます。</span><span class="sxs-lookup"><span data-stu-id="85987-132">The same request using Edit(string id) would convert to the string "2".</span></span>

<span data-ttu-id="85987-133">ここまでは、例で単純型を使用しています。</span><span class="sxs-lookup"><span data-stu-id="85987-133">So far the example uses simple types.</span></span> <span data-ttu-id="85987-134">MVC では、単純型は任意の .NET プリミティブ型、または文字列型コンバーターを使用する型となります。</span><span class="sxs-lookup"><span data-stu-id="85987-134">In MVC simple types are any .NET primitive type or type with a string type converter.</span></span> <span data-ttu-id="85987-135">アクション メソッドのパラメーターが、プロパティとして単純型と複合型の両方を含む、`Movie` 型などのクラスだった場合でも、MVC のモデル バインドでは適切に処理されます。</span><span class="sxs-lookup"><span data-stu-id="85987-135">If the action method's parameter were a class such as the `Movie` type, which contains both simple and complex types as properties, MVC's model binding will still handle it nicely.</span></span> <span data-ttu-id="85987-136">リフレクションと再帰を使用して、複合型のプロパティをトラバースして一致するものを探します。</span><span class="sxs-lookup"><span data-stu-id="85987-136">It uses reflection and recursion to traverse the properties of complex types looking for matches.</span></span> <span data-ttu-id="85987-137">モデル バインドでは *parameter_name.property_name* というパターンを探して、値をプロパティにバインドします。</span><span class="sxs-lookup"><span data-stu-id="85987-137">Model binding looks for the pattern *parameter_name.property_name* to bind values to properties.</span></span> <span data-ttu-id="85987-138">このフォームの一致する値が見つからない場合は、プロパティ名のみを使用してバインドを試みます。</span><span class="sxs-lookup"><span data-stu-id="85987-138">If it doesn't find matching values of this form, it will attempt to bind using just the property name.</span></span> <span data-ttu-id="85987-139">`Collection` 型などの型の場合、モデル バインドでは *parameter_name[index]* または *[index]* と一致するものを探します。</span><span class="sxs-lookup"><span data-stu-id="85987-139">For those types such as `Collection` types, model binding looks for matches to *parameter_name[index]* or just *[index]*.</span></span> <span data-ttu-id="85987-140">モデル バインドでは、キーが単純型である限り、同様に `Dictionary` 型を処理し、*parameter_name[key]* または *[key]* のみを求めます。</span><span class="sxs-lookup"><span data-stu-id="85987-140">Model binding treats  `Dictionary` types similarly, asking for *parameter_name[key]* or just *[key]*, as long as the keys are simple types.</span></span> <span data-ttu-id="85987-141">サポートされているキーは、同じモデル型に対して HTML ヘルパーとタグ ヘルパーで生成されたフィールド名と一致します。</span><span class="sxs-lookup"><span data-stu-id="85987-141">Keys that are supported match the field names HTML and tag helpers generated for the same model type.</span></span> <span data-ttu-id="85987-142">これにより、値のラウンド トリップが有効になり、フォーム フィールドは、利便性のためにユーザーの入力が設定されたままとなります (作成または編集してバインドされたデータが検証に合格しなかった場合など)。</span><span class="sxs-lookup"><span data-stu-id="85987-142">This enables round-tripping values so that the form fields remain filled with the user's input for their convenience, for example, when bound data from a create or edit didn't pass validation.</span></span>

<span data-ttu-id="85987-143">モデル バインドを可能にするには、クラスにバインドする既定のパブリック コンストラクターと、書き込み可能なパブリック プロパティが必要です。</span><span class="sxs-lookup"><span data-stu-id="85987-143">To make model binding possible, the class must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="85987-144">モデル バインドが行われると、クラスは既定のパブリック コンストラクターを使用してインスタンス化され、その後、プロパティを設定できます。</span><span class="sxs-lookup"><span data-stu-id="85987-144">When model binding occurs, the class is instantiated using the public default constructor, then the properties can be set.</span></span>

<span data-ttu-id="85987-145">パラメーターがバインドされると、モデル バインドではその名前を使用する値の検索を停止し、次のパラメーターのバインドに移ります。</span><span class="sxs-lookup"><span data-stu-id="85987-145">When a parameter is bound, model binding stops looking for values with that name and it moves on to bind the next parameter.</span></span> <span data-ttu-id="85987-146">それ以外の場合、既定のモデル バインドの動作では、パラメーターがその型に応じて既定値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="85987-146">Otherwise, the default model binding behavior sets parameters to their default values depending on their type:</span></span>

* <span data-ttu-id="85987-147">`T[]`:型の配列を除く`byte[]`、バインドの型のパラメーターを設定する`T[]`に`Array.Empty<T>()`します。</span><span class="sxs-lookup"><span data-stu-id="85987-147">`T[]`: With the exception of arrays of type `byte[]`, binding sets parameters of type `T[]` to `Array.Empty<T>()`.</span></span> <span data-ttu-id="85987-148">`byte[]` 型の配列は `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="85987-148">Arrays of type `byte[]` are set to `null`.</span></span>

* <span data-ttu-id="85987-149">参照型:バインドは、プロパティを設定せず、既定のコンス トラクターとクラスのインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="85987-149">Reference Types: Binding creates an instance of a class with the default constructor without setting properties.</span></span> <span data-ttu-id="85987-150">ただし、このモデル バインドでは `string` パラメーターが `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="85987-150">However, model binding sets `string` parameters to `null`.</span></span>

* <span data-ttu-id="85987-151">Null 許容型:Null 許容型に設定されて`null`します。</span><span class="sxs-lookup"><span data-stu-id="85987-151">Nullable Types: Nullable types are set to `null`.</span></span> <span data-ttu-id="85987-152">上記の例では、`int?` 型であるため、モデル バインドで `id` が `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="85987-152">In the above example, model binding sets `id` to `null` since it's of type `int?`.</span></span>

* <span data-ttu-id="85987-153">値の型。型の null 非許容の値の型`T`に設定されている`default(T)`します。</span><span class="sxs-lookup"><span data-stu-id="85987-153">Value Types: Non-nullable value types of type `T` are set to `default(T)`.</span></span> <span data-ttu-id="85987-154">たとえば、モデル バインドではパラメーター `int id` が 0 に設定されます。</span><span class="sxs-lookup"><span data-stu-id="85987-154">For example, model binding will set a parameter `int id` to 0.</span></span> <span data-ttu-id="85987-155">既定値に依存するのではなく、モデルの検証または null 許容型を使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="85987-155">Consider using model validation or nullable types rather than relying on default values.</span></span>

<span data-ttu-id="85987-156">バインドが失敗した場合、MVC はエラーをスローしません。</span><span class="sxs-lookup"><span data-stu-id="85987-156">If binding fails, MVC doesn't throw an error.</span></span> <span data-ttu-id="85987-157">ユーザー入力を受け入れるすべてのアクションで `ModelState.IsValid` プロパティを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="85987-157">Every action which accepts user input should check the `ModelState.IsValid` property.</span></span>

<span data-ttu-id="85987-158">メモ:コント ローラー内の各エントリ`ModelState`プロパティは、`ModelStateEntry`を含む、`Errors`プロパティ。</span><span class="sxs-lookup"><span data-stu-id="85987-158">Note: Each entry in the controller's `ModelState` property is a `ModelStateEntry` containing an `Errors` property.</span></span> <span data-ttu-id="85987-159">ユーザーが自分でこのコレクションに対してクエリを実行する必要はほとんどありません。</span><span class="sxs-lookup"><span data-stu-id="85987-159">It's rarely necessary to query this collection yourself.</span></span> <span data-ttu-id="85987-160">代わりに、`ModelState.IsValid` を使用してください。</span><span class="sxs-lookup"><span data-stu-id="85987-160">Use `ModelState.IsValid` instead.</span></span>

<span data-ttu-id="85987-161">また、モデル バインドを実行する際に、MVC で考慮する必要がある特別なデータ型がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="85987-161">Additionally, there are some special data types that MVC must consider when performing model binding:</span></span>

* <span data-ttu-id="85987-162">`IFormFile`, `IEnumerable<IFormFile>`:HTTP 要求の一部である 1 つまたは複数のアップロードされたファイル。</span><span class="sxs-lookup"><span data-stu-id="85987-162">`IFormFile`, `IEnumerable<IFormFile>`: One or more uploaded files that are part of the HTTP request.</span></span>

* <span data-ttu-id="85987-163">`CancellationToken`:非同期コント ローラーでアクティビティをキャンセルするために使用します。</span><span class="sxs-lookup"><span data-stu-id="85987-163">`CancellationToken`: Used to cancel activity in asynchronous controllers.</span></span>

<span data-ttu-id="85987-164">これらの型をクラス型のプロパティまたはアクション パラメーターにバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="85987-164">These types can be bound to action parameters or to properties on a class type.</span></span>

<span data-ttu-id="85987-165">モデル バインドが完了したら、[検証](validation.md)が行われます。</span><span class="sxs-lookup"><span data-stu-id="85987-165">Once model binding is complete, [Validation](validation.md) occurs.</span></span> <span data-ttu-id="85987-166">既定のモデル バインドは、大部分の開発シナリオで適切に動作します。</span><span class="sxs-lookup"><span data-stu-id="85987-166">Default model binding works great for the vast majority of development scenarios.</span></span> <span data-ttu-id="85987-167">また、拡張可能であるため、必要に応じて、組み込み動作をカスタマイズすることもできます。</span><span class="sxs-lookup"><span data-stu-id="85987-167">It's also extensible so if you have unique needs you can customize the built-in behavior.</span></span>

## <a name="customize-model-binding-behavior-with-attributes"></a><span data-ttu-id="85987-168">属性を使用してモデル バインドの動作をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="85987-168">Customize model binding behavior with attributes</span></span>

<span data-ttu-id="85987-169">MVC にはいくつかの属性が含まれています。これらを使用して、その既定のモデル バインドの動作を別のソースに指示することができます。</span><span class="sxs-lookup"><span data-stu-id="85987-169">MVC contains several attributes that you can use to direct its default model binding behavior to a different source.</span></span> <span data-ttu-id="85987-170">たとえば、プロパティでバインドが必要かどうかや、`[BindRequired]` または `[BindNever]` 属性を使用してまったくバインドしないようにするかを指定できます。</span><span class="sxs-lookup"><span data-stu-id="85987-170">For example, you can specify whether binding is required for a property, or if it should never happen at all by using the `[BindRequired]` or `[BindNever]` attributes.</span></span> <span data-ttu-id="85987-171">あるいは、既定のデータ ソースをオーバーライドし、モデル バインダーのデータ ソースを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="85987-171">Alternatively, you can override the default data source, and specify the model binder's data source.</span></span> <span data-ttu-id="85987-172">モデル バインド属性のリストを以下に示します。</span><span class="sxs-lookup"><span data-stu-id="85987-172">Below is a list of model binding attributes:</span></span>

* <span data-ttu-id="85987-173">`[BindRequired]`:この属性は、バインドできない場合に、モデル状態エラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="85987-173">`[BindRequired]`: This attribute adds a model state error if binding cannot occur.</span></span>

* <span data-ttu-id="85987-174">`[BindNever]`:このパラメーターにバインドしないにモデル バインダーに指示します。</span><span class="sxs-lookup"><span data-stu-id="85987-174">`[BindNever]`: Tells the model binder to never bind to this parameter.</span></span>

* <span data-ttu-id="85987-175">`[FromHeader]`, `[FromQuery]`, `[FromRoute]`, `[FromForm]`:適用する正確なバインディング ソースを指定するのにには、これらを使用します。</span><span class="sxs-lookup"><span data-stu-id="85987-175">`[FromHeader]`, `[FromQuery]`, `[FromRoute]`, `[FromForm]`: Use these to specify the exact binding source you want to apply.</span></span>

* <span data-ttu-id="85987-176">`[FromServices]`:この属性を使用して[依存関係の注入](../../fundamentals/dependency-injection.md)サービスからパラメーターをバインドします。</span><span class="sxs-lookup"><span data-stu-id="85987-176">`[FromServices]`: This attribute uses [dependency injection](../../fundamentals/dependency-injection.md) to bind parameters from services.</span></span>

* <span data-ttu-id="85987-177">`[FromBody]`:要求本文からデータをバインドするのにには、構成済みのフォーマッタを使用します。</span><span class="sxs-lookup"><span data-stu-id="85987-177">`[FromBody]`: Use the configured formatters to bind data from the request body.</span></span> <span data-ttu-id="85987-178">フォーマッタは、要求のコンテンツの種類に基づいて選択されます。</span><span class="sxs-lookup"><span data-stu-id="85987-178">The formatter is selected based on content type of the request.</span></span>

* <span data-ttu-id="85987-179">`[ModelBinder]`:既定のモデル バインダー、バインディング ソースと名前をオーバーライドするために使用します。</span><span class="sxs-lookup"><span data-stu-id="85987-179">`[ModelBinder]`: Used to override the default model binder, binding source and name.</span></span>

<span data-ttu-id="85987-180">属性は、モデル バインドの既定の動作をオーバーライドする必要がある場合にとても便利なツールです。</span><span class="sxs-lookup"><span data-stu-id="85987-180">Attributes are very helpful tools when you need to override the default behavior of model binding.</span></span>

## <a name="customize-model-binding-and-validation-globally"></a><span data-ttu-id="85987-181">モデル バインドと検証をグローバルにカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="85987-181">Customize model binding and validation globally</span></span>

<span data-ttu-id="85987-182">モデル バインドおよび検証システムの動作は、次の内容を記述した [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) によって駆動されます。</span><span class="sxs-lookup"><span data-stu-id="85987-182">The model binding and validation system's behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) that describes:</span></span>

* <span data-ttu-id="85987-183">モデルをバインドする方法。</span><span class="sxs-lookup"><span data-stu-id="85987-183">How a model is to be bound.</span></span>
* <span data-ttu-id="85987-184">型とそのプロパティに対して行われる検証の方法。</span><span class="sxs-lookup"><span data-stu-id="85987-184">How validation occurs on the type and its properties.</span></span>

<span data-ttu-id="85987-185">システムの動作の特性をグローバルに構成するには、[MvcOptions.ModelMetadataDetailsProviders](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions.modelmetadatadetailsproviders#Microsoft_AspNetCore_Mvc_MvcOptions_ModelMetadataDetailsProviders) に詳細プロバイダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="85987-185">Aspects of the system's behavior can be configured globally by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions.modelmetadatadetailsproviders#Microsoft_AspNetCore_Mvc_MvcOptions_ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="85987-186">MVC には組み込みの詳細プロバイダーがいくつか用意されています。これらにより、特定の型に対してモデル バインドまたは検証を無効にするなどの動作を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="85987-186">MVC has a few built-in details providers that allow configuring behavior such as disabling model binding or validation for certain types.</span></span>

<span data-ttu-id="85987-187">特定の型のすべてのモデルに対してモデル バインドを無効にするには、`Startup.ConfigureServices` 内に [ExcludeBindingMetadataProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.excludebindingmetadataprovider) を追加します。</span><span class="sxs-lookup"><span data-stu-id="85987-187">To disable model binding on all models of a certain type, add an [ExcludeBindingMetadataProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.excludebindingmetadataprovider) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="85987-188">たとえば、`System.Version` 型のすべてのモデルに対してモデル バインドを無効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="85987-188">For example, to disable model binding on all models of type `System.Version`:</span></span>

```csharp
services.AddMvc().AddMvcOptions(options =>
    options.ModelMetadataDetailsProviders.Add(
        new ExcludeBindingMetadataProvider(typeof(System.Version))));
```

<span data-ttu-id="85987-189">特定の型のプロパティに対して検証を無効にするには、`Startup.ConfigureServices` 内に [SuppressChildValidationMetadataProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.suppresschildvalidationmetadataprovider) を追加します。</span><span class="sxs-lookup"><span data-stu-id="85987-189">To disable validation on properties of a certain type, add a [SuppressChildValidationMetadataProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.suppresschildvalidationmetadataprovider) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="85987-190">たとえば、`System.Guid` 型のプロパティに対して検証を無効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="85987-190">For example, to disable validation on properties of type `System.Guid`:</span></span>

```csharp
services.AddMvc().AddMvcOptions(options =>
    options.ModelMetadataDetailsProviders.Add(
        new SuppressChildValidationMetadataProvider(typeof(System.Guid))));
```

## <a name="bind-formatted-data-from-the-request-body"></a><span data-ttu-id="85987-191">書式付きデータを要求本文からバインドする</span><span class="sxs-lookup"><span data-stu-id="85987-191">Bind formatted data from the request body</span></span>

<span data-ttu-id="85987-192">要求データは、JSON、XML およびその他の多くの書式を含む、さまざまな書式で指定できます。</span><span class="sxs-lookup"><span data-stu-id="85987-192">Request data can come in a variety of formats including JSON, XML and many others.</span></span> <span data-ttu-id="85987-193">[FromBody] 属性を使用して、要求本文のデータにパラメーターをバインドするよう指定すると、MVC では構成済みのフォーマッタ セットを使用して、そのコンテンツの種類に基づいて要求データを処理します。</span><span class="sxs-lookup"><span data-stu-id="85987-193">When you use the [FromBody] attribute to indicate that you want to bind a parameter to data in the request body, MVC uses a configured set of formatters to handle the request data based on its content type.</span></span> <span data-ttu-id="85987-194">既定では、MVC には JSON データを処理するための `JsonInputFormatter` クラスが含まれますが、XML やその他のカスタム書式を処理するためにフォーマッタを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="85987-194">By default MVC includes a `JsonInputFormatter` class for handling JSON data, but you can add additional formatters for handling XML and other custom formats.</span></span>

> [!NOTE]
> <span data-ttu-id="85987-195">`[FromBody]` で修飾されたアクションごとに 1 つのパラメーターのみを指定できます。</span><span class="sxs-lookup"><span data-stu-id="85987-195">There can be at most one parameter per action decorated with `[FromBody]`.</span></span> <span data-ttu-id="85987-196">ASP.NET Core MVC ランタイムは、フォーマッタへの要求ストリームを読み取る責任を委任します。</span><span class="sxs-lookup"><span data-stu-id="85987-196">The ASP.NET Core MVC run-time delegates the responsibility of reading the request stream to the formatter.</span></span> <span data-ttu-id="85987-197">パラメーターで要求ストリームが読み取られると、通常は他の `[FromBody]` パラメーターをバインドするために要求ストリームを再度読み取ることはできません。</span><span class="sxs-lookup"><span data-stu-id="85987-197">Once the request stream is read for a parameter, it's generally not possible to read the request stream again for binding other `[FromBody]` parameters.</span></span>

> [!NOTE]
> <span data-ttu-id="85987-198">`JsonInputFormatter` は既定のフォーマッタであり、[Json.NET](https://www.newtonsoft.com/json) に基づいています。</span><span class="sxs-lookup"><span data-stu-id="85987-198">The `JsonInputFormatter` is the default formatter and is based on [Json.NET](https://www.newtonsoft.com/json).</span></span>

<span data-ttu-id="85987-199">ASP.NET Core では、[Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html) ヘッダーとパラメーターの型に基づいて入力フォーマッタが選択されます。ただし、他に特に適用された属性がある場合を除きます。</span><span class="sxs-lookup"><span data-stu-id="85987-199">ASP.NET Core selects input formatters based on the [Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html) header and the type of the parameter, unless there's an attribute applied to it specifying otherwise.</span></span> <span data-ttu-id="85987-200">XML または別の形式を使用する場合、*Startup.cs* ファイルでそれを構成する必要がありますが、最初に NuGet を使用して `Microsoft.AspNetCore.Mvc.Formatters.Xml` への参照を取得する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="85987-200">If you'd like to use XML or another format you must configure it in the *Startup.cs* file, but you may first have to obtain a reference to `Microsoft.AspNetCore.Mvc.Formatters.Xml` using NuGet.</span></span> <span data-ttu-id="85987-201">スタートアップ コードは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="85987-201">Your startup code should look something like this:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddXmlSerializerFormatters();
   }
```

<span data-ttu-id="85987-202">*Startup.cs* ファイル内のコードには、`services` 引数を持つ `ConfigureServices` メソッドが含まれます。これを使用することで、ASP.NET Core アプリ用のサービスを構築することができます。</span><span class="sxs-lookup"><span data-stu-id="85987-202">Code in the *Startup.cs* file contains a `ConfigureServices` method with a `services` argument you can use to build up services for your ASP.NET Core app.</span></span> <span data-ttu-id="85987-203">サンプルでは、MVC がこのアプリに提供するサービスとして XML フォーマッタを追加します。</span><span class="sxs-lookup"><span data-stu-id="85987-203">In the sample, we are adding an XML formatter as a service that MVC will provide for this app.</span></span> <span data-ttu-id="85987-204">`AddMvc` メソッドに渡された `options` 引数を使用することで、アプリの起動時に MVC からフィルター、フォーマッタ、およびその他のシステム オプションを追加して管理することができます。</span><span class="sxs-lookup"><span data-stu-id="85987-204">The `options` argument passed into the `AddMvc` method allows you to add and manage filters, formatters, and other system options from MVC upon app startup.</span></span> <span data-ttu-id="85987-205">その後、`Consumes` 属性をコントローラー クラスまたはアクション メソッドに適用し、必要な書式を操作します。</span><span class="sxs-lookup"><span data-stu-id="85987-205">Then apply the `Consumes` attribute to controller classes or action methods to work with the format you want.</span></span>

### <a name="custom-model-binding"></a><span data-ttu-id="85987-206">カスタム モデル バインド</span><span class="sxs-lookup"><span data-stu-id="85987-206">Custom Model Binding</span></span>

<span data-ttu-id="85987-207">独自のカスタム モデル バインダーを書き込んで、モデル バインドを拡張することができます。</span><span class="sxs-lookup"><span data-stu-id="85987-207">You can extend model binding by writing your own custom model binders.</span></span> <span data-ttu-id="85987-208">詳細については、「[custom model binding](../advanced/custom-model-binding.md)」 (カスタム モデル バインド) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="85987-208">Learn more about [custom model binding](../advanced/custom-model-binding.md).</span></span>
