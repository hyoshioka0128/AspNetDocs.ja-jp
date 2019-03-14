---
title: ASP.NET Core のタグ ヘルパー コンポーネント
author: scottaddie
description: タグ ヘルパー コンポーネントについてと、ASP.NET Core でのその使用方法について説明します。
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 09/18/2018
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: 3d21e12650d844f05bdfdf5b3451ab6219e3c3b7
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57040029"
---
# <a name="tag-helper-components-in-aspnet-core"></a><span data-ttu-id="98c42-103">ASP.NET Core のタグ ヘルパー コンポーネント</span><span class="sxs-lookup"><span data-stu-id="98c42-103">Tag Helper Components in ASP.NET Core</span></span>

<span data-ttu-id="98c42-104">作成者: [Scott Addie](https://twitter.com/Scott_Addie)、[Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)</span><span class="sxs-lookup"><span data-stu-id="98c42-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)</span></span>

<span data-ttu-id="98c42-105">タグ ヘルパー コンポーネントは、サーバー側のコードから HTML 要素を、条件に応じて変更または追加できるタグ ヘルパーです。</span><span class="sxs-lookup"><span data-stu-id="98c42-105">A Tag Helper Component is a Tag Helper that allows you to conditionally modify or add HTML elements from server-side code.</span></span> <span data-ttu-id="98c42-106">この機能は、ASP.NET Core 2.0 以降で使用できます。</span><span class="sxs-lookup"><span data-stu-id="98c42-106">This feature is available in ASP.NET Core 2.0 or later.</span></span>

<span data-ttu-id="98c42-107">ASP.NET Core には、組み込みのタグ ヘルパー コンポーネントが 2 つ (`head` と `body`) 含まれています。</span><span class="sxs-lookup"><span data-stu-id="98c42-107">ASP.NET Core includes two built-in Tag Helper Components: `head` and `body`.</span></span> <span data-ttu-id="98c42-108">これらは <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> 名前空間に配置され、MVC と Razor Pages の両方で使用できます。</span><span class="sxs-lookup"><span data-stu-id="98c42-108">They're located in the <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> namespace and can be used in both MVC and Razor Pages.</span></span> <span data-ttu-id="98c42-109">タグ ヘルパー コンポーネントには、*_ViewImports.cshtml* でのアプリへの登録は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="98c42-109">Tag Helper Components don't require registration with the app in *_ViewImports.cshtml*.</span></span>

<span data-ttu-id="98c42-110">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="98c42-110">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="use-cases"></a><span data-ttu-id="98c42-111">ユース ケース</span><span class="sxs-lookup"><span data-stu-id="98c42-111">Use cases</span></span>

<span data-ttu-id="98c42-112">タグ ヘルパー コンポーネントの 2 つの一般的なユース ケースは、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="98c42-112">Two common use cases of Tag Helper Components include:</span></span>

1. [<span data-ttu-id="98c42-113">`<link>` を `<head>` に挿入する</span><span class="sxs-lookup"><span data-stu-id="98c42-113">Injecting a `<link>` into the `<head>`.</span></span>](#inject-into-html-head-element)
1. [<span data-ttu-id="98c42-114">`<script>` を `<body>` に挿入する</span><span class="sxs-lookup"><span data-stu-id="98c42-114">Injecting a `<script>` into the `<body>`.</span></span>](#inject-into-html-body-element)

<span data-ttu-id="98c42-115">次のセクションでは、これらのユース ケースについて説明します。</span><span class="sxs-lookup"><span data-stu-id="98c42-115">The following sections describe these use cases.</span></span>

### <a name="inject-into-html-head-element"></a><span data-ttu-id="98c42-116">HTML の head 要素の挿入</span><span class="sxs-lookup"><span data-stu-id="98c42-116">Inject into HTML head element</span></span>

<span data-ttu-id="98c42-117">HTML `<head>` 要素内で、CSS ファイルは HTML `<link>` 要素でよくインポートされます。</span><span class="sxs-lookup"><span data-stu-id="98c42-117">Inside the HTML `<head>` element, CSS files are commonly imported with the HTML `<link>` element.</span></span> <span data-ttu-id="98c42-118">次のコードでは、`head` タグ ヘルパー コンポーネントを使用して `<link>` 要素が `<head>` 要素に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="98c42-118">The following code injects a `<link>` element into the `<head>` element using the `head` Tag Helper Component:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

<span data-ttu-id="98c42-119">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="98c42-119">In the preceding code:</span></span>

* <span data-ttu-id="98c42-120">`AddressStyleTagHelperComponent` は、<xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent> を実装します。</span><span class="sxs-lookup"><span data-stu-id="98c42-120">`AddressStyleTagHelperComponent` implements <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>.</span></span> <span data-ttu-id="98c42-121">抽象化では次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="98c42-121">The abstraction:</span></span>
  * <span data-ttu-id="98c42-122"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext> を使ってクラスの初期化を許可。</span><span class="sxs-lookup"><span data-stu-id="98c42-122">Allows initialization of the class with a <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.</span></span>
  * <span data-ttu-id="98c42-123">タグ ヘルパー コンポーネントの使用を有効にして、HTML 要素を追加または変更。</span><span class="sxs-lookup"><span data-stu-id="98c42-123">Enables the use of Tag Helper Components to add or modify HTML elements.</span></span>
* <span data-ttu-id="98c42-124"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> プロパティでは、コンポーネントがレンダリングされる順序を定義します。</span><span class="sxs-lookup"><span data-stu-id="98c42-124">The <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> property defines the order in which the Components are rendered.</span></span> <span data-ttu-id="98c42-125">アプリでタグ ヘルパー コンポーネントが複数使用されている場合、`Order` が必要です。</span><span class="sxs-lookup"><span data-stu-id="98c42-125">`Order` is necessary when there are multiple usages of Tag Helper Components in an app.</span></span>
* <span data-ttu-id="98c42-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> では、実行コンテキストの <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> プロパティ値が `head` と比較されます。</span><span class="sxs-lookup"><span data-stu-id="98c42-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> compares the execution context's <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> property value to `head`.</span></span> <span data-ttu-id="98c42-127">比較評価の結果が true の場合は、`_style` フィールドのコンテンツが HTML `<head>` 要素に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="98c42-127">If the comparison evaluates to true, the content of the `_style` field is injected into the HTML `<head>` element.</span></span>

### <a name="inject-into-html-body-element"></a><span data-ttu-id="98c42-128">HTML の body 要素に挿入</span><span class="sxs-lookup"><span data-stu-id="98c42-128">Inject into HTML body element</span></span>

<span data-ttu-id="98c42-129">`body` タグ ヘルパー コンポーネントによって、`<script>` 要素を `<body>` 要素に挿入できます。</span><span class="sxs-lookup"><span data-stu-id="98c42-129">The `body` Tag Helper Component can inject a `<script>` element into the `<body>` element.</span></span> <span data-ttu-id="98c42-130">次のコードはこの技法を示しています。</span><span class="sxs-lookup"><span data-stu-id="98c42-130">The following code demonstrates this technique:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

<span data-ttu-id="98c42-131">個別の HTML ファイルを使用して、`<script>` 要素を格納します。</span><span class="sxs-lookup"><span data-stu-id="98c42-131">A separate HTML file is used to store the `<script>` element.</span></span> <span data-ttu-id="98c42-132">HTML ファイルを使用すると、コードはより整理され、よりメンテナンスしやすくなります。</span><span class="sxs-lookup"><span data-stu-id="98c42-132">The HTML file makes the code cleaner and more maintainable.</span></span> <span data-ttu-id="98c42-133">上のコードでは、*TagHelpers/Templates/AddressToolTipScript.html* のコンテンツを読み取り、タグ ヘルパーの出力でそれを追加します。</span><span class="sxs-lookup"><span data-stu-id="98c42-133">The preceding code reads the contents of *TagHelpers/Templates/AddressToolTipScript.html* and appends it with the Tag Helper output.</span></span> <span data-ttu-id="98c42-134">*AddressToolTipScript.html* ファイルには、次のマークアップが含まれます。</span><span class="sxs-lookup"><span data-stu-id="98c42-134">The *AddressToolTipScript.html* file includes the following markup:</span></span>

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

<span data-ttu-id="98c42-135">上のコードでは、[ブートストラップ ヒント ウィジェット](https://getbootstrap.com/docs/3.3/javascript/#tooltips)を `printable` 属性を含む任意の `<address>` 要素にバインドします。</span><span class="sxs-lookup"><span data-stu-id="98c42-135">The preceding code binds a [Bootstrap tooltip widget](https://getbootstrap.com/docs/3.3/javascript/#tooltips) to any `<address>` element that includes a `printable` attribute.</span></span> <span data-ttu-id="98c42-136">要素の上にマウス ポインターが移動したときに、効果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="98c42-136">The effect is visible when a mouse pointer hovers over the element.</span></span>

## <a name="register-a-component"></a><span data-ttu-id="98c42-137">コンポーネントの登録</span><span class="sxs-lookup"><span data-stu-id="98c42-137">Register a Component</span></span>

<span data-ttu-id="98c42-138">タグ ヘルパー コンポーネントは、アプリのタグ ヘルパー コンポーネント コレクションに追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="98c42-138">A Tag Helper Component must be added to the app's Tag Helper Components collection.</span></span> <span data-ttu-id="98c42-139">コレクションに追加するには、次の 3 つの方法があります。</span><span class="sxs-lookup"><span data-stu-id="98c42-139">There are three ways to add to the collection:</span></span>

1. [<span data-ttu-id="98c42-140">サービス コンテナーによる登録</span><span class="sxs-lookup"><span data-stu-id="98c42-140">Registration via services container</span></span>](#registration-via-services-container)
1. [<span data-ttu-id="98c42-141">Razor ファイルによる登録</span><span class="sxs-lookup"><span data-stu-id="98c42-141">Registration via Razor file</span></span>](#registration-via-razor-file)
1. [<span data-ttu-id="98c42-142">ページ モデルまたはコントローラーによる登録</span><span class="sxs-lookup"><span data-stu-id="98c42-142">Registration via Page Model or controller</span></span>](#registration-via-page-model-or-controller)

### <a name="registration-via-services-container"></a><span data-ttu-id="98c42-143">サービス コンテナーによる登録</span><span class="sxs-lookup"><span data-stu-id="98c42-143">Registration via services container</span></span>

<span data-ttu-id="98c42-144">タグ ヘルパー コンポーネント クラスが <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager> で管理されていない場合、[依存関係挿入 (DI)](xref:fundamentals/dependency-injection) システムで登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="98c42-144">If the Tag Helper Component class isn't managed with <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, it must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) system.</span></span> <span data-ttu-id="98c42-145">次の `Startup.ConfigureServices` コードでは、[一時的な有効期間](xref:fundamentals/dependency-injection#lifetime-and-registration-options)で `AddressStyleTagHelperComponent` クラスと `AddressScriptTagHelperComponent` クラスを登録します。</span><span class="sxs-lookup"><span data-stu-id="98c42-145">The following `Startup.ConfigureServices` code registers the `AddressStyleTagHelperComponent` and `AddressScriptTagHelperComponent` classes with a [transient lifetime](xref:fundamentals/dependency-injection#lifetime-and-registration-options):</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-razor-file"></a><span data-ttu-id="98c42-146">Razor ファイルによる登録</span><span class="sxs-lookup"><span data-stu-id="98c42-146">Registration via Razor file</span></span>

<span data-ttu-id="98c42-147">タグ ヘルパー コンポーネントが DI で登録されていない場合は、Razor Pages ページまたは MVC ビューから登録できます。</span><span class="sxs-lookup"><span data-stu-id="98c42-147">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page or an MVC view.</span></span> <span data-ttu-id="98c42-148">この技法は、Razor ファイルから挿入されたマークアップとコンポーネントの実行する順番を制御するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="98c42-148">This technique is used for controlling the injected markup and the component execution order from a Razor file.</span></span>

<span data-ttu-id="98c42-149">`ITagHelperComponentManager` を使用して、タグ ヘルパー コンポーネントを追加したり、アプリから削除したりします。</span><span class="sxs-lookup"><span data-stu-id="98c42-149">`ITagHelperComponentManager` is used to add Tag Helper Components or remove them from the app.</span></span> <span data-ttu-id="98c42-150">次のコードでは、`AddressTagHelperComponent` を使ってこの技法を示します。</span><span class="sxs-lookup"><span data-stu-id="98c42-150">The following code demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

<span data-ttu-id="98c42-151">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="98c42-151">In the preceding code:</span></span>

* <span data-ttu-id="98c42-152">`@inject` ディレクティブでは、`ITagHelperComponentManager` のインスタンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="98c42-152">The `@inject` directive provides an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="98c42-153">インスタンスは、Razor ファイルでダウンストリームのアクセスを行うために、`manager` という名前の変数に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="98c42-153">The instance is assigned to a variable named `manager` for access downstream in the Razor file.</span></span>
* <span data-ttu-id="98c42-154">`AddressTagHelperComponent` のインスタンスは、アプリのタグ ヘルパー コンポーネント コレクションに追加されます。</span><span class="sxs-lookup"><span data-stu-id="98c42-154">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

<span data-ttu-id="98c42-155">`AddressTagHelperComponent` は、`markup` と `order` パラメーターを受け入れるコンストラクターに反映するために変更されます。</span><span class="sxs-lookup"><span data-stu-id="98c42-155">`AddressTagHelperComponent` is modified to accommodate a constructor that accepts the `markup` and `order` parameters:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

<span data-ttu-id="98c42-156">指定された `markup` パラメーターは、次のように `ProcessAsync` で使用されます。</span><span class="sxs-lookup"><span data-stu-id="98c42-156">The provided `markup` parameter is used in `ProcessAsync` as follows:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a><span data-ttu-id="98c42-157">ページ モデルまたはコントローラーによる登録</span><span class="sxs-lookup"><span data-stu-id="98c42-157">Registration via Page Model or controller</span></span>

<span data-ttu-id="98c42-158">タグ ヘルパー コンポーネントが DI で登録されていない場合は、Razor Pages ページ モデルまたは MVC コントローラーから登録できます。</span><span class="sxs-lookup"><span data-stu-id="98c42-158">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page model or an MVC controller.</span></span> <span data-ttu-id="98c42-159">この技法は、Razor ファイルから C# ロジックを分離するのに便利です。</span><span class="sxs-lookup"><span data-stu-id="98c42-159">This technique is useful for separating C# logic from Razor files.</span></span>

<span data-ttu-id="98c42-160">コンストラクター挿入を使用して、`ITagHelperComponentManager` のインスタンスにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="98c42-160">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="98c42-161">タグ ヘルパー コンポーネントは、インスタンスのタグ ヘルパー コンポーネント コレクションに追加されます。</span><span class="sxs-lookup"><span data-stu-id="98c42-161">The Tag Helper Component is added to the instance's Tag Helper Components collection.</span></span> <span data-ttu-id="98c42-162">次の Razor Pages ページ モデルでは、`AddressTagHelperComponent` を使ったこの技法を示します。</span><span class="sxs-lookup"><span data-stu-id="98c42-162">The following Razor Pages page model demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

<span data-ttu-id="98c42-163">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="98c42-163">In the preceding code:</span></span>

* <span data-ttu-id="98c42-164">コンストラクター挿入を使用して、`ITagHelperComponentManager` のインスタンスにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="98c42-164">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span>
* <span data-ttu-id="98c42-165">`AddressTagHelperComponent` のインスタンスは、アプリのタグ ヘルパー コンポーネント コレクションに追加されます。</span><span class="sxs-lookup"><span data-stu-id="98c42-165">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

## <a name="create-a-component"></a><span data-ttu-id="98c42-166">コンポーネントの作成</span><span class="sxs-lookup"><span data-stu-id="98c42-166">Create a Component</span></span>

<span data-ttu-id="98c42-167">カスタムのタグ ヘルパー コンポーネントを作成するには</span><span class="sxs-lookup"><span data-stu-id="98c42-167">To create a custom Tag Helper Component:</span></span>

* <span data-ttu-id="98c42-168"><xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper> から派生するパブリック クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="98c42-168">Create a public class deriving from <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.</span></span>
* <span data-ttu-id="98c42-169">[[HtmlTargetElement]](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) 属性をクラスに適用します。</span><span class="sxs-lookup"><span data-stu-id="98c42-169">Apply an [[HtmlTargetElement]](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) attribute to the class.</span></span> <span data-ttu-id="98c42-170">ターゲット HTML 要素の名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="98c42-170">Specify the name of the target HTML element.</span></span>
* <span data-ttu-id="98c42-171">*省略可能*:適用する[[EditorBrowsable(EditorBrowsableState.Never)]](xref:System.ComponentModel.EditorBrowsableAttribute)属性を IntelliSense で、型の表示を抑制するクラス。</span><span class="sxs-lookup"><span data-stu-id="98c42-171">*Optional*: Apply an [[EditorBrowsable(EditorBrowsableState.Never)]](xref:System.ComponentModel.EditorBrowsableAttribute) attribute to the class to suppress the type's display in IntelliSense.</span></span>

<span data-ttu-id="98c42-172">次のコードでは、`<address>` HTML 要素をターゲットとするカスタムのタグ ヘルパー コンポーネントが作成されます。</span><span class="sxs-lookup"><span data-stu-id="98c42-172">The following code creates a custom Tag Helper Component that targets the `<address>` HTML element:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

<span data-ttu-id="98c42-173">カスタムの `address` タグ ヘルパー コンポーネントを使用して、次のように HTML マークアップを挿入します。</span><span class="sxs-lookup"><span data-stu-id="98c42-173">Use the custom `address` Tag Helper Component to inject HTML markup as follows:</span></span>

```csharp
public class AddressTagHelperComponent : TagHelperComponent
{
    private readonly string _printableButton =
        "<button type='button' class='btn btn-info' onclick=\"window.open("
        "'https://binged.it/2AXRRYw')\">" +
        "<span class='glyphicon glyphicon-road' aria-hidden='true'></span>" +
        "</button>";

    public override int Order => 3;

    public override async Task ProcessAsync(TagHelperContext context,
                                            TagHelperOutput output)
    {
        if (string.Equals(context.TagName, "address",
                StringComparison.OrdinalIgnoreCase) &&
            output.Attributes.ContainsName("printable"))
        {
            var content = await output.GetChildContentAsync();
            output.Content.SetHtmlContent(
                $"<div>{content.GetContent()}</div>{_printableButton}");
        }
    }
}
```

<span data-ttu-id="98c42-174">上の `ProcessAsync` メソッドでは、<xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> に提供された HTML が一致する `<address>` 要素に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="98c42-174">The preceding `ProcessAsync` method injects the HTML provided to <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> into the matching `<address>` element.</span></span> <span data-ttu-id="98c42-175">挿入は次の場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="98c42-175">The injection occurs when:</span></span>

* <span data-ttu-id="98c42-176">実行コンテキストの `TagName` プロパティ値が `address` と等しい場合。</span><span class="sxs-lookup"><span data-stu-id="98c42-176">The execution context's `TagName` property value equals `address`.</span></span>
* <span data-ttu-id="98c42-177">対応する `<address>` 要素に `printable` 属性がある場合。</span><span class="sxs-lookup"><span data-stu-id="98c42-177">The corresponding `<address>` element has a `printable` attribute.</span></span>

<span data-ttu-id="98c42-178">たとえば、次の `<address>` 要素を処理しているときに、`if` ステートメントの評価の結果が true になります。</span><span class="sxs-lookup"><span data-stu-id="98c42-178">For example, the `if` statement evaluates to true when processing the following `<address>` element:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a><span data-ttu-id="98c42-179">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="98c42-179">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>
