---
title: Razor Components の概要
author: guardrex
description: ASP.NET Core アプリ内に .NET を使った対話型のクライアント側 Web UI を構築する方法である、ASP.NET Core Razor Components について調べます。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2019
uid: razor-components/index
---
# <a name="introduction-to-razor-components"></a><span data-ttu-id="79b6d-103">Razor Components の概要</span><span class="sxs-lookup"><span data-stu-id="79b6d-103">Introduction to Razor Components</span></span>

<span data-ttu-id="79b6d-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="79b6d-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="79b6d-105">*Razor Components* は、.NET を使った対話型のクライアント側 Web UI を構築するための方法です。</span><span class="sxs-lookup"><span data-stu-id="79b6d-105">*Razor Components* is a way to build interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="79b6d-106">JavaScript の代わりに C# を使って、多機能な対話型 UI を構築します。</span><span class="sxs-lookup"><span data-stu-id="79b6d-106">Build rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="79b6d-107">すべて .NET で記述された、サーバー側とクライアント側アプリのロジックを共有します。</span><span class="sxs-lookup"><span data-stu-id="79b6d-107">Share server-side and client-side app logic all written with .NET.</span></span>
* <span data-ttu-id="79b6d-108">モバイル ブラウザーを含めた広範なブラウザーのサポートのために、HTML および CSS として UI をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="79b6d-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="79b6d-109">Razor Components は、ほとんどのアプリで必要となる次のコア機能をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="79b6d-109">Razor Components supports core facilities required by most apps, including:</span></span>

* <span data-ttu-id="79b6d-110">パラメーター</span><span class="sxs-lookup"><span data-stu-id="79b6d-110">Parameters</span></span>
* <span data-ttu-id="79b6d-111">イベント処理</span><span class="sxs-lookup"><span data-stu-id="79b6d-111">Event handling</span></span>
* <span data-ttu-id="79b6d-112">データ バインディング</span><span class="sxs-lookup"><span data-stu-id="79b6d-112">Data binding</span></span>
* <span data-ttu-id="79b6d-113">ルーティング</span><span class="sxs-lookup"><span data-stu-id="79b6d-113">Routing</span></span>
* <span data-ttu-id="79b6d-114">依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="79b6d-114">Dependency injection</span></span>
* <span data-ttu-id="79b6d-115">レイアウト</span><span class="sxs-lookup"><span data-stu-id="79b6d-115">Layouts</span></span>
* <span data-ttu-id="79b6d-116">テンプレート</span><span class="sxs-lookup"><span data-stu-id="79b6d-116">Templating</span></span>
* <span data-ttu-id="79b6d-117">カスケード値</span><span class="sxs-lookup"><span data-stu-id="79b6d-117">Cascading values</span></span>

<span data-ttu-id="79b6d-118">Razor Components では、UI の更新を適用する方法からコンポーネントのレンダリング ロジックが分離されます。</span><span class="sxs-lookup"><span data-stu-id="79b6d-118">Razor Components decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="79b6d-119">.NET Core 3.0 の ASP.NET Core Razor Component には、ASP.NET Core アプリでサーバー上の Razor Components をホストするためのサポートが追加されています。</span><span class="sxs-lookup"><span data-stu-id="79b6d-119">ASP.NET Core Razor Components in .NET Core 3.0 adds support for hosting Razor Components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="79b6d-120">UI の更新は SignalR 接続を介して処理されます。</span><span class="sxs-lookup"><span data-stu-id="79b6d-120">UI updates are handled over a SignalR connection.</span></span>

<span data-ttu-id="79b6d-121">ランタイムは:</span><span class="sxs-lookup"><span data-stu-id="79b6d-121">The runtime:</span></span>

* <span data-ttu-id="79b6d-122">ブラウザーからサーバーへの UI イベントの送信を処理します。</span><span class="sxs-lookup"><span data-stu-id="79b6d-122">Handles sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="79b6d-123">サーバーから送信された UI の更新を、コンポーネントの実行後にブラウザーに適用します。</span><span class="sxs-lookup"><span data-stu-id="79b6d-123">Applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="79b6d-124">ブラウザーと通信するために Razor Components で使われる接続は、JavaScript 相互運用の呼び出しを処理するためにも使われます。</span><span class="sxs-lookup"><span data-stu-id="79b6d-124">The connection used by Razor Components to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![Razor Components では、サーバー上で .NET コードが実行され、SignalR 接続を介してクライアント上のドキュメント オブジェクト モデルとのやりとりが行われます。](index/_static/aspnet-core-razor-components.png)

<span data-ttu-id="79b6d-126">詳細については、「 <xref:razor-components/hosting-models#server-side-hosting-model> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79b6d-126">For more information, see <xref:razor-components/hosting-models#server-side-hosting-model>.</span></span>

<span data-ttu-id="79b6d-127">*Blazor* は、Razor Components の実験的なクライアント側のホスティング モデルです。</span><span class="sxs-lookup"><span data-stu-id="79b6d-127">*Blazor* is the experimental client-side hosting model of Razor Components.</span></span> <span data-ttu-id="79b6d-128">Blazor は、オープンな Web 標準を使ってブラウザー内の .NET 上で実行され、プラグインもコード トランスパイルも必要ありません。</span><span class="sxs-lookup"><span data-stu-id="79b6d-128">Blazor runs on .NET in the browser using open web standards without plugins or code transpilation.</span></span> <span data-ttu-id="79b6d-129">詳細については、「 <xref:razor-components/hosting-models#client-side-hosting-model> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79b6d-129">For more information, see <xref:razor-components/hosting-models#client-side-hosting-model>.</span></span>

## <a name="components"></a><span data-ttu-id="79b6d-130">コンポーネント</span><span class="sxs-lookup"><span data-stu-id="79b6d-130">Components</span></span>

<span data-ttu-id="79b6d-131">*Razor Component* は、ページ、ダイアログ、データ入力フォームなど、UI の一部です。</span><span class="sxs-lookup"><span data-stu-id="79b6d-131">A *Razor Component* is a piece of UI, such as a page, dialog, or data entry form.</span></span> <span data-ttu-id="79b6d-132">コンポーネントではユーザー イベントが処理され、柔軟な UI のレンダリング ロジックが定義されます。</span><span class="sxs-lookup"><span data-stu-id="79b6d-132">Components handle user events and define flexible UI rendering logic.</span></span> <span data-ttu-id="79b6d-133">コンポーネントは、入れ子にしたり再利用したりできます。</span><span class="sxs-lookup"><span data-stu-id="79b6d-133">Components can be nested and reused.</span></span>

<span data-ttu-id="79b6d-134">コンポーネントは .NET アセンブリに組み込まれた .NET クラスであり、NuGet パッケージとして共有したり配布したりできます。</span><span class="sxs-lookup"><span data-stu-id="79b6d-134">Components are .NET classes built into .NET assemblies that can be shared and distributed as NuGet packages.</span></span> <span data-ttu-id="79b6d-135">このクラスは、Razor マークアップ ページの形式 (*.cshtml*) で記述することも、C# クラス (*.cs*) として記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="79b6d-135">The class can either be written in the form of a Razor markup page (*.cshtml*) or as a C# class (*.cs*).</span></span>

<span data-ttu-id="79b6d-136">[Razor](xref:mvc/views/razor) とは、HTML マークアップを C# コードに結合するための構文です。</span><span class="sxs-lookup"><span data-stu-id="79b6d-136">[Razor](xref:mvc/views/razor) is a syntax for combining HTML markup with C# code.</span></span> <span data-ttu-id="79b6d-137">Razor は開発者の生産性向上を目的に設計されています。これにより開発者は [IntelliSense](/visualstudio/ide/using-intellisense) サポートを使用して同じファイル内でマークアップと C# を切り替えることができます。</span><span class="sxs-lookup"><span data-stu-id="79b6d-137">Razor is designed for developer productivity, allowing the developer to switch between markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="79b6d-138">Razor Pages と MVC ビューでも Razor が使われます。</span><span class="sxs-lookup"><span data-stu-id="79b6d-138">Razor Pages and MVC views also use Razor.</span></span> <span data-ttu-id="79b6d-139">要求/応答モデルを中心に構築される Razor Pages や MVC ビューとは異なり、コンポーネントは特に UI コンポジションを処理するために使われます。</span><span class="sxs-lookup"><span data-stu-id="79b6d-139">Unlike Razor Pages and MVC views, which are built around a request/response model, components are used specifically for handling UI composition.</span></span> <span data-ttu-id="79b6d-140">Razor Components は特にクライアント側の UI ロジックとコンポジションのために使えます。</span><span class="sxs-lookup"><span data-stu-id="79b6d-140">Razor Components can be used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="79b6d-141">次のマークアップは、Razor ファイル (*DialogComponent.cshtml*) に含まれるカスタム ダイアログ コンポーネントの例です。</span><span class="sxs-lookup"><span data-stu-id="79b6d-141">The following markup is an example of a custom dialog component in a Razor file (*DialogComponent.cshtml*):</span></span>

```cshtml
<div>
    <h2>@Title</h2>
    @BodyContent
    <button onclick=@OnOK>OK</button>
</div>

@functions {
    public string Title { get; set; }
    public RenderFragment BodyContent { get; set; }
    public Action OnOK { get; set; }
}
```

<span data-ttu-id="79b6d-142">このコンポーネントをアプリ内の他の場所で使用する場合、IntelliSense の構文およびパラメーター補完により開発を迅速化できます。</span><span class="sxs-lookup"><span data-stu-id="79b6d-142">When this component is used elsewhere in the app, IntelliSense speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="79b6d-143">コンポーネントは、"*レンダリング ツリー*" と呼ばれるブラウザー DOM のメモリ内表現としてレンダリングされ、柔軟かつ効率的な方法で UI を更新するために使えるようになります。</span><span class="sxs-lookup"><span data-stu-id="79b6d-143">Components render into an in-memory representation of the browser DOM called a *render tree* that can then be used to update the UI in a flexible and efficient way.</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="79b6d-144">JavaScript 相互運用</span><span class="sxs-lookup"><span data-stu-id="79b6d-144">JavaScript interop</span></span>

<span data-ttu-id="79b6d-145">サード パーティ製の JavaScript ライブラリおよびブラウザーの API を必要とするアプリのために、コンポーネントは JavaScript と相互運用します。</span><span class="sxs-lookup"><span data-stu-id="79b6d-145">For apps that require third-party JavaScript libraries and browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="79b6d-146">コンポーネントでは、JavaScript で使用できるライブラリまたは API はいずれも使用することができます。</span><span class="sxs-lookup"><span data-stu-id="79b6d-146">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="79b6d-147">C# コードによる JavaScript コードの呼び出し、および JavaScript コードによる C# コードの呼び出しを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="79b6d-147">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="79b6d-148">詳細については、[JavaScript 相互運用](xref:razor-components/javascript-interop)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79b6d-148">For more information, see [JavaScript interop](xref:razor-components/javascript-interop).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="79b6d-149">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="79b6d-149">Additional resources</span></span>

* <xref:spa/blazor/index>
* [<span data-ttu-id="79b6d-150">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="79b6d-150">WebAssembly</span></span>](http://webassembly.org/)
* [<span data-ttu-id="79b6d-151">C# のガイド</span><span class="sxs-lookup"><span data-stu-id="79b6d-151">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="79b6d-152">HTML</span><span class="sxs-lookup"><span data-stu-id="79b6d-152">HTML</span></span>](https://www.w3.org/html/)
