---
title: ASP.NET Core MVC の概要
author: ardalis
description: ASP.NET Core MVC が、モデル ビュー コントローラー デザイン パターンを使用して、Web アプリと API をビルドするための豊富なフレームワークであることについて説明します。
ms.author: riande
ms.date: 01/08/2018
uid: mvc/overview
ms.openlocfilehash: 205948cb45709b4eb6014aaf4960bf193a20dc30
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57046049"
---
# <a name="overview-of-aspnet-core-mvc"></a><span data-ttu-id="a9bed-103">ASP.NET Core MVC の概要</span><span class="sxs-lookup"><span data-stu-id="a9bed-103">Overview of ASP.NET Core MVC</span></span>

<span data-ttu-id="a9bed-104">作成者: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a9bed-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a9bed-105">ASP.NET Core MVC は、モデル ビュー コントローラー デザイン パターンを使用して、Web アプリと API をビルドするための豊富なフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="a9bed-105">ASP.NET Core MVC is a rich framework for building web apps and APIs using the Model-View-Controller design pattern.</span></span>

## <a name="what-is-the-mvc-pattern"></a><span data-ttu-id="a9bed-106">MVC パターンとは何ですか?</span><span class="sxs-lookup"><span data-stu-id="a9bed-106">What is the MVC pattern?</span></span>

<span data-ttu-id="a9bed-107">モデル ビュー コントローラー (MVC) アーキテクチャ パターンでは、アプリケーションを 3 つの主要なコンポーネントのグループに分けます:モデル、ビュー、コントローラーです。</span><span class="sxs-lookup"><span data-stu-id="a9bed-107">The Model-View-Controller (MVC) architectural pattern separates an application into three main groups of components: Models, Views, and Controllers.</span></span> <span data-ttu-id="a9bed-108">このパターンは、[関心の分離](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)を実現するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-108">This pattern helps to achieve [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span> <span data-ttu-id="a9bed-109">このパターンを使用すると、ユーザーの要求が、ユーザーの操作を実行したり、クエリの結果を取得したりするためにモデルを操作する役割がある、コントローラーにルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-109">Using this pattern, user requests are routed to a Controller which is responsible for working with the Model to perform user actions and/or retrieve results of queries.</span></span> <span data-ttu-id="a9bed-110">コントローラーでは、ユーザーに表示するビューを選び、必要なモデル データと共に提供します。</span><span class="sxs-lookup"><span data-stu-id="a9bed-110">The Controller chooses the View to display to the user, and provides it with any Model data it requires.</span></span>

<span data-ttu-id="a9bed-111">次の図は、3 つの主要なコンポーネントと、どのコンポーネントがどのコンポーネントを参照するかを示しています。</span><span class="sxs-lookup"><span data-stu-id="a9bed-111">The following diagram shows the three main components and which ones reference the others:</span></span>

![MVC パターン](overview/_static/mvc.png)

<span data-ttu-id="a9bed-113">この責任の概念は、複雑さの観点からアプリケーションを計るために役立ちます。それは、単一のジョブを持つもの (モデル、ビュー、コントローラー) の方がコーディング、デバッグ、およびテストしやすいためです。</span><span class="sxs-lookup"><span data-stu-id="a9bed-113">This delineation of responsibilities helps you scale the application in terms of complexity because it's easier to code, debug, and test something (model, view, or controller) that has a single job.</span></span> <span data-ttu-id="a9bed-114">これらの 3 つの領域の 2 つ以上に依存関係が広がるコードを更新、テスト、デバッグする方が難しいです。</span><span class="sxs-lookup"><span data-stu-id="a9bed-114">It's more difficult to update, test, and debug code that has dependencies spread across two or more of these three areas.</span></span> <span data-ttu-id="a9bed-115">たとえば、ユーザー インターフェイス ロジックは、ビジネス ロジックよりも頻繁に変更される傾向があります。</span><span class="sxs-lookup"><span data-stu-id="a9bed-115">For example, user interface logic tends to change more frequently than business logic.</span></span> <span data-ttu-id="a9bed-116">プレゼンテーション コードとビジネス ロジックが 1 つのオブジェクトに結合されている場合は、ユーザー インターフェイスを変更するたびに、ビジネス ロジックを含むオブジェクトは変更されます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-116">If presentation code and business logic are combined in a single object, an object containing business logic must be modified every time the user interface is changed.</span></span> <span data-ttu-id="a9bed-117">これにより、エラーが発生することが多く、最小限のユーザー インターフェイスの変更後に毎回ビジネス ロジックのテストを再実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a9bed-117">This often introduces errors and requires the retesting of business logic after every minimal user interface change.</span></span>

> [!NOTE]
> <span data-ttu-id="a9bed-118">ビューとコントローラーは両方ともモデルに依存します。</span><span class="sxs-lookup"><span data-stu-id="a9bed-118">Both the view and the controller depend on the model.</span></span> <span data-ttu-id="a9bed-119">ただし、モデルはビューにもコントローラーにも依存しません。</span><span class="sxs-lookup"><span data-stu-id="a9bed-119">However, the model depends on neither the view nor the controller.</span></span> <span data-ttu-id="a9bed-120">これは、分離の主な利点の 1 つです。</span><span class="sxs-lookup"><span data-stu-id="a9bed-120">This is one of the key benefits of the separation.</span></span> <span data-ttu-id="a9bed-121">この分離によって、モデルをビルドし、視覚表示の独立をテストすることができます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-121">This separation allows the model to be built and tested independent of the visual presentation.</span></span>

### <a name="model-responsibilities"></a><span data-ttu-id="a9bed-122">モデルの責任</span><span class="sxs-lookup"><span data-stu-id="a9bed-122">Model Responsibilities</span></span>

<span data-ttu-id="a9bed-123">MVC アプリケーションのモデルは、アプリケーションの状態、およびアプリケーションによって実行されるビジネス ロジックや操作を表します。</span><span class="sxs-lookup"><span data-stu-id="a9bed-123">The Model in an MVC application represents the state of the application and any business logic or operations that should be performed by it.</span></span> <span data-ttu-id="a9bed-124">ビジネス ロジックは、アプリケーションの状態を保持するための実装ロジックと共に、モデルにカプセル化される必要があります。</span><span class="sxs-lookup"><span data-stu-id="a9bed-124">Business logic should be encapsulated in the model, along with any implementation logic for persisting the state of the application.</span></span> <span data-ttu-id="a9bed-125">通常、厳密に型指定されたビューでは、そのビューに表示するデータを含めるようにデザインされた ViewModel 型を使用します。</span><span class="sxs-lookup"><span data-stu-id="a9bed-125">Strongly-typed views typically use ViewModel types designed to contain the data to display on that view.</span></span> <span data-ttu-id="a9bed-126">コントローラーは、モデルからこれらの ViewModel インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="a9bed-126">The controller creates and populates these ViewModel instances from the model.</span></span>

### <a name="view-responsibilities"></a><span data-ttu-id="a9bed-127">ビューの責任</span><span class="sxs-lookup"><span data-stu-id="a9bed-127">View Responsibilities</span></span>

<span data-ttu-id="a9bed-128">ビューは、ユーザー インターフェイスを介してコンテンツを表示する役割があります。</span><span class="sxs-lookup"><span data-stu-id="a9bed-128">Views are responsible for presenting content through the user interface.</span></span> <span data-ttu-id="a9bed-129">ビューでは [Razor ビュー エンジン](#razor-view-engine) を使用して、HTML マークアップに .NET コードを埋め込みます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-129">They use the [Razor view engine](#razor-view-engine) to embed .NET code in HTML markup.</span></span> <span data-ttu-id="a9bed-130">ビュー内のロジックは最小限にする必要があり、そこに含まれるロジックはすべて、コンテンツの表示に関連する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a9bed-130">There should be minimal logic within views, and any logic in them should relate to presenting content.</span></span> <span data-ttu-id="a9bed-131">複雑なモデルからデータを表示するために、ビュー ファイルで多くのロジックを実行する必要がある場合、ビューを簡略化するために、[ビューのコンポーネント](views/view-components.md)、ViewModel、テンプレートの表示を使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="a9bed-131">If you find the need to perform a great deal of logic in view files in order to display data from a complex model, consider using a [View Component](views/view-components.md), ViewModel, or view template to simplify the view.</span></span>

### <a name="controller-responsibilities"></a><span data-ttu-id="a9bed-132">コントローラーの責任</span><span class="sxs-lookup"><span data-stu-id="a9bed-132">Controller Responsibilities</span></span>

<span data-ttu-id="a9bed-133">コントローラーは、ユーザーの操作を処理し、モデルを操作し、最終的にレンダリングするビューを選択するコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="a9bed-133">Controllers are the components that handle user interaction, work with the model, and ultimately select a view to render.</span></span> <span data-ttu-id="a9bed-134">MVC アプリケーションでは、ビューは情報のみを表示し、コントローラーがユーザーの入力と操作を処理して応答します。</span><span class="sxs-lookup"><span data-stu-id="a9bed-134">In an MVC application, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="a9bed-135">MVC パターンでは、コントローラーは最初のエントリ ポイントであり、処理するモデルの型およびレンダリングするビューを選択する役割があります (そのため、このような名前で呼ばれており、アプリが指定した要求に応答する方法を制御します)。</span><span class="sxs-lookup"><span data-stu-id="a9bed-135">In the MVC pattern, the controller is the initial entry point, and is responsible for selecting which model types to work with and which view to render (hence its name - it controls how the app responds to a given request).</span></span>

> [!NOTE]
> <span data-ttu-id="a9bed-136">コントローラーは責任が非常に多いので、過度に複雑にしないでください。</span><span class="sxs-lookup"><span data-stu-id="a9bed-136">Controllers shouldn't be overly complicated by too many responsibilities.</span></span> <span data-ttu-id="a9bed-137">コントローラー ロジックが過度に複雑にならないように、ビジネス ロジックをコントローラーから排除し、ドメイン モデルに含めます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-137">To keep controller logic from becoming overly complex, push business logic out of the controller and into the domain model.</span></span>

>[!TIP]
> <span data-ttu-id="a9bed-138">コントローラーのアクションで、頻繁に同じ種類のアクションを実行することがわかった場合、これらの一般的なアクションを[フィルター](#filters)に移動させます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-138">If you find that your controller actions frequently perform the same kinds of actions, move these common actions into [filters](#filters).</span></span>

## <a name="what-is-aspnet-core-mvc"></a><span data-ttu-id="a9bed-139">ASP.NET Core MVC とは何ですか?</span><span class="sxs-lookup"><span data-stu-id="a9bed-139">What is ASP.NET Core MVC</span></span>

<span data-ttu-id="a9bed-140">ASP.NET Core MVC フレームワークは、ASP.NET Core と共に使用するために最適化された、ライトウェイトかつオープン ソースのテストしやすいプレゼンテーション フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="a9bed-140">The ASP.NET Core MVC framework is a lightweight, open source, highly testable presentation framework optimized for use with ASP.NET Core.</span></span>

<span data-ttu-id="a9bed-141">ASP.NET Core MVC では、明確な関心の分離を可能にする動的な Web サイトをビルドするためのパターン ベースの方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="a9bed-141">ASP.NET Core MVC provides a patterns-based way to build dynamic websites that enables a clean separation of concerns.</span></span> <span data-ttu-id="a9bed-142">ここでは、マークアップのフル コントロールが提供され、TDD 向けの開発をサポートし、最新の Web 標準を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-142">It gives you full control over markup, supports TDD-friendly development and uses the latest web standards.</span></span>

## <a name="features"></a><span data-ttu-id="a9bed-143">機能</span><span class="sxs-lookup"><span data-stu-id="a9bed-143">Features</span></span>

<span data-ttu-id="a9bed-144">ASP.NET Core MVC には、以下が含まれます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-144">ASP.NET Core MVC includes the following:</span></span>

* [<span data-ttu-id="a9bed-145">ルーティング</span><span class="sxs-lookup"><span data-stu-id="a9bed-145">Routing</span></span>](#routing)
* [<span data-ttu-id="a9bed-146">モデル バインド</span><span class="sxs-lookup"><span data-stu-id="a9bed-146">Model binding</span></span>](#model-binding)
* [<span data-ttu-id="a9bed-147">モデル検証</span><span class="sxs-lookup"><span data-stu-id="a9bed-147">Model validation</span></span>](#model-validation)
* [<span data-ttu-id="a9bed-148">依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="a9bed-148">Dependency injection</span></span>](../fundamentals/dependency-injection.md)
* [<span data-ttu-id="a9bed-149">Filters</span><span class="sxs-lookup"><span data-stu-id="a9bed-149">Filters</span></span>](#filters)
* [<span data-ttu-id="a9bed-150">領域</span><span class="sxs-lookup"><span data-stu-id="a9bed-150">Areas</span></span>](#areas)
* [<span data-ttu-id="a9bed-151">Web API</span><span class="sxs-lookup"><span data-stu-id="a9bed-151">Web APIs</span></span>](#web-apis)
* [<span data-ttu-id="a9bed-152">テストの容易性</span><span class="sxs-lookup"><span data-stu-id="a9bed-152">Testability</span></span>](#testability)
* [<span data-ttu-id="a9bed-153">Razor ビュー エンジン</span><span class="sxs-lookup"><span data-stu-id="a9bed-153">Razor view engine</span></span>](#razor-view-engine)
* [<span data-ttu-id="a9bed-154">厳密に型指定されたビュー</span><span class="sxs-lookup"><span data-stu-id="a9bed-154">Strongly typed views</span></span>](#strongly-typed-views)
* [<span data-ttu-id="a9bed-155">タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="a9bed-155">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="a9bed-156">ビュー コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a9bed-156">View Components</span></span>](#view-components)

### <a name="routing"></a><span data-ttu-id="a9bed-157">ルーティング</span><span class="sxs-lookup"><span data-stu-id="a9bed-157">Routing</span></span>

<span data-ttu-id="a9bed-158">ASP.NET Core MVC は、[ASP.NET Core のルーティング](../fundamentals/routing.md)の上にビルドされます。このルーティングは強力な URL マッピング コンポーネントで、わかりやすく検索可能な URL のアプリケーションをビルドできます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-158">ASP.NET Core MVC is built on top of [ASP.NET Core's routing](../fundamentals/routing.md), a powerful URL-mapping component that lets you build applications that have comprehensible and searchable URLs.</span></span> <span data-ttu-id="a9bed-159">これにより、Web サーバー上のファイルを整理する方法に関係なく、検索エンジンの最適化 (SEO) およびリンクの作成に適しているアプリケーションの URL の名前付けパターンを定義できます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-159">This enables you to define your application's URL naming patterns that work well for search engine optimization (SEO) and for link generation, without regard for how the files on your web server are organized.</span></span> <span data-ttu-id="a9bed-160">ルート値制約、既定値、省略可能な値をサポートする、便利なルート テンプレートの構文を使用して、ルートを定義できます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-160">You can define your routes using a convenient route template syntax that supports route value constraints, defaults and optional values.</span></span>

<span data-ttu-id="a9bed-161">*規約に基づくルーティング*では、アプリケーションが受け入れる URL 形式、およびそれらの各形式を指定したコントローラー上の特定のアクション メソッドにマップする方法をグローバルに定義することができます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-161">*Convention-based routing* enables you to globally define the URL formats that your application accepts and how each of those formats maps to a specific action method on given controller.</span></span> <span data-ttu-id="a9bed-162">受信要求を受け取ると、ルーティング エンジンは URL を解析し、定義された URL 形式のいずれかに合わせて、関連するコントローラーのアクション メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a9bed-162">When an incoming request is received, the routing engine parses the URL and matches it to one of the defined URL formats, and then calls the associated controller's action method.</span></span>

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="a9bed-163">*属性のルーティング*では、アプリケーションのルートを定義する属性でコントローラーとアクションを装飾して、ルーティング情報を指定できます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-163">*Attribute routing* enables you to specify routing information by decorating your controllers and actions with attributes that define your application's routes.</span></span> <span data-ttu-id="a9bed-164">つまり、ルート定義は、関連付けられているコントローラーとアクションの横に配置されるということです。</span><span class="sxs-lookup"><span data-stu-id="a9bed-164">This means that your route definitions are placed next to the controller and action with which they're associated.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
  [HttpGet("{id}")]
  public IActionResult GetProduct(int id)
  {
    ...
  }
}
```

### <a name="model-binding"></a><span data-ttu-id="a9bed-165">モデル バインド</span><span class="sxs-lookup"><span data-stu-id="a9bed-165">Model binding</span></span>

<span data-ttu-id="a9bed-166">ASP.NET Core MVC の[モデル バインド](models/model-binding.md)は、クライアント要求データ (フォームの値、ルート データ、クエリ文字列のパラメーター、HTTP ヘッダー) をコントローラーが処理できるオブジェクトに変換します。</span><span class="sxs-lookup"><span data-stu-id="a9bed-166">ASP.NET Core MVC [model binding](models/model-binding.md) converts client request data  (form values, route data, query string parameters, HTTP headers) into objects that the controller can handle.</span></span> <span data-ttu-id="a9bed-167">その結果、コントローラー ロジックは、受信要求データを判断する作業を行う必要はありません。そのアクション メソッドに対するパラメーターとしてデータを持つだけです。</span><span class="sxs-lookup"><span data-stu-id="a9bed-167">As a result, your controller logic doesn't have to do the work of figuring out the incoming request data; it simply has the data as parameters to its action methods.</span></span>

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
   ```

### <a name="model-validation"></a><span data-ttu-id="a9bed-168">モデルの検証</span><span class="sxs-lookup"><span data-stu-id="a9bed-168">Model validation</span></span>

<span data-ttu-id="a9bed-169">ASP.NET Core MVC では、データ注釈の検証属性でモデル オブジェクトを装飾することで、[検証](models/validation.md)をサポートします。</span><span class="sxs-lookup"><span data-stu-id="a9bed-169">ASP.NET Core MVC supports [validation](models/validation.md) by decorating your model object with data annotation validation attributes.</span></span> <span data-ttu-id="a9bed-170">検証属性は、値をサーバーにポストする前に、クライアント側でチェックされ、コントローラー アクションが呼び出される前に、サーバー側でチェックされます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-170">The validation attributes are checked on the client side before values are posted to the server, as well as on the server before the controller action is called.</span></span>

```csharp
using System.ComponentModel.DataAnnotations;
public class LoginViewModel
{
    [Required]
    [EmailAddress]
    public string Email { get; set; }

    [Required]
    [DataType(DataType.Password)]
    public string Password { get; set; }

    [Display(Name = "Remember me?")]
    public bool RememberMe { get; set; }
}
```

<span data-ttu-id="a9bed-171">コントローラー アクション:</span><span class="sxs-lookup"><span data-stu-id="a9bed-171">A controller action:</span></span>

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null)
{
    if (ModelState.IsValid)
    {
      // work with the model
    }
    // At this point, something failed, redisplay form
    return View(model);
}
```

<span data-ttu-id="a9bed-172">フレームワークでは、クライアントとサーバーの両方で要求データの検証を処理します。</span><span class="sxs-lookup"><span data-stu-id="a9bed-172">The framework handles validating request data both on the client and on the server.</span></span> <span data-ttu-id="a9bed-173">モデルの型に指定された検証ロジックは、控えめな注釈としてレンダリングされたビューに追加され、[jQuery Validation](https://jqueryvalidation.org/) を使ってブラウザーに適用されます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-173">Validation logic specified on model types is added to the rendered views as unobtrusive annotations and is enforced in the browser with [jQuery Validation](https://jqueryvalidation.org/).</span></span>

### <a name="dependency-injection"></a><span data-ttu-id="a9bed-174">依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="a9bed-174">Dependency injection</span></span>

<span data-ttu-id="a9bed-175">ASP.NET Core には、[依存関係の挿入 (DI)](../fundamentals/dependency-injection.md) の組み込みのサポートがあります。</span><span class="sxs-lookup"><span data-stu-id="a9bed-175">ASP.NET Core has built-in support for [dependency injection (DI)](../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="a9bed-176">ASP.NET Core MVC では、[コントローラー](controllers/dependency-injection.md)は、[明示的な依存関係の原則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)に従うことを許可して、コンストラクターを介して必要なサービスを要求できます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-176">In ASP.NET Core MVC, [controllers](controllers/dependency-injection.md) can request needed services through their constructors, allowing them to follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

<span data-ttu-id="a9bed-177">また、自分のアプリで、`@inject` ディレクティブを使用して、[ビュー ファイルに依存関係の挿入](views/dependency-injection.md)を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-177">Your app can also use [dependency injection in view files](views/dependency-injection.md), using the `@inject` directive:</span></span>

```cshtml
@inject SomeService ServiceName
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ServiceName.GetTitle</title>
</head>
<body>
    <h1>@ServiceName.GetTitle</h1>
</body>
</html>
```

### <a name="filters"></a><span data-ttu-id="a9bed-178">フィルター</span><span class="sxs-lookup"><span data-stu-id="a9bed-178">Filters</span></span>

<span data-ttu-id="a9bed-179">[フィルター](controllers/filters.md)は、開発者が例外処理や承認など、横断的関心事をカプセル化するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-179">[Filters](controllers/filters.md) help developers encapsulate cross-cutting concerns, like exception handling or authorization.</span></span> <span data-ttu-id="a9bed-180">フィルターでは、アクション メソッドの前処理と後処理ロジックを実行できるようにします。また、指定した要求の実行パイプライン内のある時点で実行するように構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-180">Filters enable running custom pre- and post-processing logic for action methods, and can be configured to run at certain points within the execution pipeline for a given request.</span></span> <span data-ttu-id="a9bed-181">フィルターは、属性としてコントローラーまたはアクションに適用できます (または、グローバルに実行できます)。</span><span class="sxs-lookup"><span data-stu-id="a9bed-181">Filters can be applied to controllers or actions as attributes (or can be run globally).</span></span> <span data-ttu-id="a9bed-182">いくつかのフィルター (`Authorize` など) は、フレームワークに含まれます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-182">Several filters (such as `Authorize`) are included in the framework.</span></span> <span data-ttu-id="a9bed-183">`[Authorize]` は MVC 承認フィルターの作成のために使用される属性です.</span><span class="sxs-lookup"><span data-stu-id="a9bed-183">`[Authorize]` is the attribute that is used to create MVC authorization filters.</span></span>

```csharp
[Authorize]
   public class AccountController : Controller
   {
```

### <a name="areas"></a><span data-ttu-id="a9bed-184">Areas</span><span class="sxs-lookup"><span data-stu-id="a9bed-184">Areas</span></span>

<span data-ttu-id="a9bed-185">[区分](controllers/areas.md)は、大きな ASP.NET Core MVC Web アプリを小さな機能グループに分割するための方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="a9bed-185">[Areas](controllers/areas.md) provide a way to partition a large ASP.NET Core MVC Web app into smaller functional groupings.</span></span> <span data-ttu-id="a9bed-186">区分は、アプリケーション内の MVC 構造体となります。</span><span class="sxs-lookup"><span data-stu-id="a9bed-186">An area is an MVC structure inside an application.</span></span> <span data-ttu-id="a9bed-187">MVC プロジェクトでは、モデル、コント ローラー、ビューなどの論理コンポーネントが異なるフォルダーに保持され、MVC では名前付け規則を使用して、これらのコンポーネントの関係を作成します。</span><span class="sxs-lookup"><span data-stu-id="a9bed-187">In an MVC project, logical components like Model, Controller, and View are kept in different folders, and MVC uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="a9bed-188">大きなアプリでは、アプリを機能の個別の高レベル区分に分割すると便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="a9bed-188">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="a9bed-189">たとえば、チェックアウト、請求、検索などの複数のビジネス ユニットを持つ e コマース アプリの場合です。これらのユニットにはそれぞれ独自の論理コンポーネント ビュー、コントローラー、およびモデルがあります。</span><span class="sxs-lookup"><span data-stu-id="a9bed-189">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search etc. Each of these units have their own logical component views, controllers, and models.</span></span>

### <a name="web-apis"></a><span data-ttu-id="a9bed-190">Web API</span><span class="sxs-lookup"><span data-stu-id="a9bed-190">Web APIs</span></span>

<span data-ttu-id="a9bed-191">Web サイトのビルドに最適なプラットフォームというだけでなく、ASP.NET Core MVC には Web API のビルドに適したサポートがあります。</span><span class="sxs-lookup"><span data-stu-id="a9bed-191">In addition to being a great platform for building web sites, ASP.NET Core MVC has great support for building Web APIs.</span></span> <span data-ttu-id="a9bed-192">ブラウザーやモバイル デバイスなど、さまざまなクライアントに提供されるサービスをビルドできます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-192">You can build services that reach a broad range of clients including browsers and mobile devices.</span></span>

<span data-ttu-id="a9bed-193">フレームワークには、JSON または XML として [書式のデータ](xref:web-api/advanced/formatting)の組み込みサポートと共に、HTTP コンテンツ ネゴシエーションのサポートが含まれます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-193">The framework includes support for HTTP content-negotiation with built-in support to [format data](xref:web-api/advanced/formatting) as JSON or XML.</span></span> <span data-ttu-id="a9bed-194">[カスタム フォーマッタ](xref:web-api/advanced/custom-formatters)を作成して、独自の書式のサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="a9bed-194">Write [custom formatters](xref:web-api/advanced/custom-formatters) to add support for your own formats.</span></span>

<span data-ttu-id="a9bed-195">リンクの生成を使って、ハイパーメディアのサポートを有効にします。</span><span class="sxs-lookup"><span data-stu-id="a9bed-195">Use link generation to enable support for hypermedia.</span></span> <span data-ttu-id="a9bed-196">自分の Web API を複数の Web アプリケーションで共有できるように、[クロス オリジン リソース共有 (CORS)](http://www.w3.org/TR/cors/) のサポートを簡単に有効にできます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-196">Easily enable support for [cross-origin resource sharing (CORS)](http://www.w3.org/TR/cors/) so that your Web APIs can be shared across multiple Web applications.</span></span>

### <a name="testability"></a><span data-ttu-id="a9bed-197">テストの容易性</span><span class="sxs-lookup"><span data-stu-id="a9bed-197">Testability</span></span>

<span data-ttu-id="a9bed-198">フレームワークでインターフェイスと依存関係の挿入を使用して、単体テストに適するようにします。また、フレームワークには、[統合テスト](xref:test/integration-tests)もすばやく簡単にする機能 (Entity Framework の TestHost と InMemory プロバイダーなど) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-198">The framework's use of interfaces and dependency injection make it well-suited to unit testing, and the framework includes features (like a TestHost and InMemory provider for Entity Framework) that make [integration tests](xref:test/integration-tests) quick and easy as well.</span></span> <span data-ttu-id="a9bed-199">詳細については、[コントローラー ロジックのテスト方法](controllers/testing.md)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a9bed-199">Learn more about [how to test controller logic](controllers/testing.md).</span></span>

### <a name="razor-view-engine"></a><span data-ttu-id="a9bed-200">Razor ビュー エンジン</span><span class="sxs-lookup"><span data-stu-id="a9bed-200">Razor view engine</span></span>

<span data-ttu-id="a9bed-201">[ASP.NET Core MVC ビュー](views/overview.md)では、[Razor ビュー エンジン](views/razor.md)を使用してビューをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="a9bed-201">[ASP.NET Core MVC views](views/overview.md) use the [Razor view engine](views/razor.md) to render views.</span></span> <span data-ttu-id="a9bed-202">Razor は、埋め込みの C# コードを使用してビューを定義するためのコンパクトで豊富な表現かつ流動的なテンプレートのマークアップ言語です。</span><span class="sxs-lookup"><span data-stu-id="a9bed-202">Razor is a compact, expressive and fluid template markup language for defining views using embedded C# code.</span></span> <span data-ttu-id="a9bed-203">Razor は、サーバーに Web コンテンツを動的に作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-203">Razor is used to dynamically generate web content on the server.</span></span> <span data-ttu-id="a9bed-204">サーバー コードを、クライアント側のコンテンツとコードにクリーンに混在させることができます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-204">You can cleanly mix server code with client side content and code.</span></span>

```text
<ul>
  @for (int i = 0; i < 5; i++) {
    <li>List item @i</li>
  }
</ul>
```

<span data-ttu-id="a9bed-205">Razor ビュー エンジンを使用して、[レイアウト](views/layout.md)、[部分ビュー](views/partial.md)、置き換え可能なセクションを定義できます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-205">Using the Razor view engine you can define [layouts](views/layout.md), [partial views](views/partial.md) and replaceable sections.</span></span>

### <a name="strongly-typed-views"></a><span data-ttu-id="a9bed-206">厳密に型指定されたビュー</span><span class="sxs-lookup"><span data-stu-id="a9bed-206">Strongly typed views</span></span>

<span data-ttu-id="a9bed-207">MVC の Razor ビューは、モデルを基にして厳密に型指定できます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-207">Razor views in MVC can be strongly typed based on your model.</span></span> <span data-ttu-id="a9bed-208">コントローラーは、ビューの型チェックと IntelliSense サポートを有効にして、厳密に型指定されたモデルをビューに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-208">Controllers can pass a strongly typed model to views enabling your views to have type checking and IntelliSense support.</span></span>

<span data-ttu-id="a9bed-209">たとえば、次のビューでは、モデルの型 `IEnumerable<Product>` をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="a9bed-209">For example, the following view renders a model of type `IEnumerable<Product>`:</span></span>

```cshtml
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a><span data-ttu-id="a9bed-210">タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="a9bed-210">Tag Helpers</span></span>

<span data-ttu-id="a9bed-211">[タグ ヘルパー](views/tag-helpers/intro.md)を使うと、Razor ファイルでの HTML 要素の作成とレンダリングに、サーバー側コードを組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-211">[Tag Helpers](views/tag-helpers/intro.md) enable server side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="a9bed-212">タグ ヘルパーを使って、カスタム タグ (例: `<environment>`) を定義したり、既存のタグ (例: `<label>`) の動作を変更したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-212">You can use tag helpers to define custom tags (for example, `<environment>`) or to modify the behavior of existing tags (for example, `<label>`).</span></span> <span data-ttu-id="a9bed-213">タグ ヘルパーは、要素名とその属性に基づいて特定の要素をバインドします。</span><span class="sxs-lookup"><span data-stu-id="a9bed-213">Tag Helpers bind to specific elements based on the element name and its attributes.</span></span> <span data-ttu-id="a9bed-214">タグ ヘルパーでは、HTML の編集操作を保持しながら、サーバー側のレンダリングの利点を提供します。</span><span class="sxs-lookup"><span data-stu-id="a9bed-214">They provide the benefits of server-side rendering while still preserving an HTML editing experience.</span></span>

<span data-ttu-id="a9bed-215">フォームやリンクの作成、資産の読み込みなど、一般的なタスクに対する組み込みのタグ ヘルパーは数多く存在します。パブリック GitHub リポジトリで NuGet パッケージとして使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-215">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="a9bed-216">タグ ヘルパーは C# で作成され、要素名、属性名、または親タグに基づく HTML 要素をターゲットとします。</span><span class="sxs-lookup"><span data-stu-id="a9bed-216">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="a9bed-217">たとえば、組み込みの LinkTagHelper を使用して、`AccountsController` の `Login` へのリンクを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-217">For example, the built-in LinkTagHelper can be used to create a link to the `Login` action of the `AccountsController`:</span></span>

```cshtml
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

<span data-ttu-id="a9bed-218">`EnvironmentTagHelper` を使用して、開発、ステージング、運用など、ランタイム環境に基づいて、ビュー (未加工、縮小など) にさまざまなスクリプトを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-218">The `EnvironmentTagHelper` can be used to include different scripts in your views (for example, raw or minified) based on the runtime environment, such as Development, Staging, or Production:</span></span>

```cshtml
<environment names="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
</environment>
<environment names="Staging,Production">
    <script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-2.1.4.min.js"
            asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
            asp-fallback-test="window.jQuery">
    </script>
</environment>
```

<span data-ttu-id="a9bed-219">タグ ヘルパーでは、HTML に適した開発機能、および HTML および Razor マークアップを作成するための豊富な IntelliSense 環境を提供します。</span><span class="sxs-lookup"><span data-stu-id="a9bed-219">Tag Helpers provide an HTML-friendly development experience and a rich IntelliSense environment for creating HTML and Razor markup.</span></span> <span data-ttu-id="a9bed-220">組み込みのタグ ヘルパーのほとんどは、既存の HTML 要素をターゲットとし、要素に対してサーバー側の属性を提供します。</span><span class="sxs-lookup"><span data-stu-id="a9bed-220">Most of the built-in Tag Helpers target existing HTML elements and provide server-side attributes for the element.</span></span>

### <a name="view-components"></a><span data-ttu-id="a9bed-221">ビュー コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a9bed-221">View Components</span></span>

<span data-ttu-id="a9bed-222">[ビュー コンポーネント](views/view-components.md)を使用すると、レンダリング ロジックをパッケージ化し、アプリケーション全体で再利用することができます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-222">[View Components](views/view-components.md) allow you to package rendering logic and reuse it throughout the application.</span></span> <span data-ttu-id="a9bed-223">これは[部分ビュー](views/partial.md)と似ていますが、関連するロジックを含みます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-223">They're similar to [partial views](views/partial.md), but with associated logic.</span></span>

## <a name="compatibility-version"></a><span data-ttu-id="a9bed-224">互換バージョン</span><span class="sxs-lookup"><span data-stu-id="a9bed-224">Compatibility version</span></span>

<span data-ttu-id="a9bed-225"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> メソッドを使用すると、ASP.NET Core MVC 2.1 以降に導入されている、互換性に影響する重大な変更をオプトインまたはオプトアウトすることができます。</span><span class="sxs-lookup"><span data-stu-id="a9bed-225">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span>

<span data-ttu-id="a9bed-226">詳細については、「 <xref:mvc/compatibility-version> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a9bed-226">For more information, see <xref:mvc/compatibility-version>.</span></span>
