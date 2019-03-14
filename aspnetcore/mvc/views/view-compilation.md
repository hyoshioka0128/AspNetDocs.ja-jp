---
title: ASP.NET Core での Razor ファイルのコンパイル
author: rick-anderson
description: ASP.NET Core アプリで Razor ファイルのコンパイルがどのように行われるかについて説明します。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/13/2019
uid: mvc/views/view-compilation
ms.openlocfilehash: 0b6173a7860f5f1d9d11219fbf3f57f76d703031
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57036799"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="73f6d-103">ASP.NET Core での Razor ファイルのコンパイル</span><span class="sxs-lookup"><span data-stu-id="73f6d-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="73f6d-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="73f6d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range="= aspnetcore-1.1"

<span data-ttu-id="73f6d-105">関連する MVC ビューが呼び出されたときに、Razor ファイルが実行時にコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="73f6d-105">A Razor file is compiled at runtime, when the associated MVC view is invoked.</span></span> <span data-ttu-id="73f6d-106">ビルド時の Razor ファイルの公開はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="73f6d-106">Build-time Razor file publishing is unsupported.</span></span> <span data-ttu-id="73f6d-107">Razor ファイルはオプションで発行時にコンパイルして、アプリ &mdash; でプリコンパイル ツールを使用して展開できます。</span><span class="sxs-lookup"><span data-stu-id="73f6d-107">Razor files can optionally be compiled at publish time and deployed with the app&mdash;using the precompilation tool.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="73f6d-108">関連する Razor ページまたは MVC ビューが呼び出されたときに、Razor ファイルが実行時にコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="73f6d-108">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="73f6d-109">ビルド時の Razor ファイルの公開はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="73f6d-109">Build-time Razor file publishing is unsupported.</span></span> <span data-ttu-id="73f6d-110">Razor ファイルはオプションで発行時にコンパイルして、アプリ &mdash; でプリコンパイル ツールを使用して展開できます。</span><span class="sxs-lookup"><span data-stu-id="73f6d-110">Razor files can optionally be compiled at publish time and deployed with the app&mdash;using the precompilation tool.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="73f6d-111">関連する Razor ページまたは MVC ビューが呼び出されたときに、Razor ファイルが実行時にコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="73f6d-111">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="73f6d-112">Razor ファイルは、[Razor SDK](xref:razor-pages/sdk) を使用してビルド時と公開時にコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="73f6d-112">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="73f6d-113">Razor ファイルは、[Razor SDK](xref:razor-pages/sdk) を使用してビルド時と公開時にコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="73f6d-113">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="73f6d-114">実行時コンパイルは、アプリケーションを構成することで任意で有効にできることがあります。</span><span class="sxs-lookup"><span data-stu-id="73f6d-114">Runtime compilation may be optionally enabled by configuring your application</span></span>

::: moniker-end

## <a name="razor-compilation"></a><span data-ttu-id="73f6d-115">Razor コンパイル</span><span class="sxs-lookup"><span data-stu-id="73f6d-115">Razor compilation</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="73f6d-116">Razor ファイルのビルドおよび発行時のコンパイルは、既定で Razor SDK によって有効になっています。</span><span class="sxs-lookup"><span data-stu-id="73f6d-116">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="73f6d-117">有効になっていると、実行時コンパイルはビルド時のコンパイルを補完し、編集された Razor ファイルを更新します。</span><span class="sxs-lookup"><span data-stu-id="73f6d-117">When enabled, runtime compilation, will complement build time compilation allowing Razor files to be updated if they are editied.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="73f6d-118">Razor ファイルのビルドおよび発行時のコンパイルは、既定で Razor SDK によって有効になっています。</span><span class="sxs-lookup"><span data-stu-id="73f6d-118">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="73f6d-119">更新後の Razor ファイルの編集は、ビルド時にサポートされています。</span><span class="sxs-lookup"><span data-stu-id="73f6d-119">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="73f6d-120">既定では、コンパイルされた *Views.dll* のみがアプリと共に展開されます。Razor ファイルのコンパイルに必要な *.cshtml* ファイルまたは参照アセンブリはアプリと共に展開されません。</span><span class="sxs-lookup"><span data-stu-id="73f6d-120">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="73f6d-121">プリコンパイル ツールは非推奨とされており、ASP.NET Core 3.0 では削除されます。</span><span class="sxs-lookup"><span data-stu-id="73f6d-121">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="73f6d-122">[Razor Sdk](xref:razor-pages/sdk) に移行することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="73f6d-122">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="73f6d-123">Razor SDK は、プロジェクト ファイルにプリコンパイル固有のプロパティが設定されていない場合のみ有効です。</span><span class="sxs-lookup"><span data-stu-id="73f6d-123">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="73f6d-124">たとえば、*.csproj* ファイルの `MvcRazorCompileOnPublish` プロパティを `true` に設定して、Razor SDK を無効にします。</span><span class="sxs-lookup"><span data-stu-id="73f6d-124">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="73f6d-125">プロジェクトのターゲットが .NET Framework である場合は、次のように [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="73f6d-125">If your project targets .NET Framework, install the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet package:</span></span>

[!code-xml[](view-compilation/sample/DotNetFrameworkProject.csproj?name=snippet_ViewCompilationPackage)]

<span data-ttu-id="73f6d-126">プロジェクトのターゲットが .NET Core である場合、変更する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="73f6d-126">If your project targets .NET Core, no changes are necessary.</span></span>

<span data-ttu-id="73f6d-127">ASP.NET Core 2.x プロジェクト テンプレートは既定で、暗黙的に `MvcRazorCompileOnPublish` プロパティを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="73f6d-127">The ASP.NET Core 2.x project templates implicitly set the `MvcRazorCompileOnPublish` property to `true` by default.</span></span> <span data-ttu-id="73f6d-128">そのため、この要素は *.csproj* ファイルから安全に削除できます。</span><span class="sxs-lookup"><span data-stu-id="73f6d-128">Consequently, this element can be safely removed from the *.csproj* file.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="73f6d-129">プリコンパイル ツールは非推奨とされており、ASP.NET Core 3.0 では削除されます。</span><span class="sxs-lookup"><span data-stu-id="73f6d-129">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="73f6d-130">[Razor Sdk](xref:razor-pages/sdk) に移行することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="73f6d-130">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="73f6d-131">ASP.NET Core 2.0 で[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) を実行する場合、Razor ファイルのプリコンパイルを使用できません。</span><span class="sxs-lookup"><span data-stu-id="73f6d-131">Razor file precompilation is unavailable when performing a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) in ASP.NET Core 2.0.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-1.1"

<span data-ttu-id="73f6d-132">`MvcRazorCompileOnPublish` プロパティを `true` に設定して、[Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="73f6d-132">Set the `MvcRazorCompileOnPublish` property to `true`, and install the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet package.</span></span> <span data-ttu-id="73f6d-133">次の *.csproj* サンプルでは、これらの設定が強調表示されています。</span><span class="sxs-lookup"><span data-stu-id="73f6d-133">The following *.csproj* sample highlights these settings:</span></span>

[!code-xml[](view-compilation/sample/MvcRazorCompileOnPublish.csproj?highlight=4,10)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="73f6d-134">[.NET Core CLI publish コマンド](/dotnet/core/tools/dotnet-publish)を使用して、[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)用にアプリを準備します。</span><span class="sxs-lookup"><span data-stu-id="73f6d-134">Prepare the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd) with the [.NET Core CLI publish command](/dotnet/core/tools/dotnet-publish).</span></span> <span data-ttu-id="73f6d-135">たとえば、プロジェクト ルートで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="73f6d-135">For example, execute the following command at the project root:</span></span>

```console
dotnet publish -c Release
```

<span data-ttu-id="73f6d-136">コンパイル済みの Razor ファイルを含む、*<project_name>.PrecompiledViews.dll* ファイルは、プリコンパイルが正常に行われたときに生成されます。</span><span class="sxs-lookup"><span data-stu-id="73f6d-136">A *<project_name>.PrecompiledViews.dll* file, containing the compiled Razor files, is produced when precompilation succeeds.</span></span> <span data-ttu-id="73f6d-137">たとえば、次のスクリーンショットは、*WebApplication1.PrecompiledViews.dll* 内の *Index.cshtml* のコンテンツを示しています。</span><span class="sxs-lookup"><span data-stu-id="73f6d-137">For example, the screenshot below depicts the contents of *Index.cshtml* within *WebApplication1.PrecompiledViews.dll*:</span></span>

![DLL 内の Razor ビュー](view-compilation/_static/razor-views-in-dll.png)

::: moniker-end

## <a name="runtime-compilation"></a><span data-ttu-id="73f6d-139">実行時のコンパイル</span><span class="sxs-lookup"><span data-stu-id="73f6d-139">Runtime compilation</span></span>

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="73f6d-140">ビルド時のコンパイルは Razor ファイルの実行時コンパイルによって補完されます。</span><span class="sxs-lookup"><span data-stu-id="73f6d-140">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="73f6d-141">ASP.NET Core MVC は、*.cshtml* ファイルの内容が変更されたとき、Razor ファイルを再コンパイルします。</span><span class="sxs-lookup"><span data-stu-id="73f6d-141">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="73f6d-142">ビルド時のコンパイルは Razor ファイルの実行時コンパイルによって補完されます。</span><span class="sxs-lookup"><span data-stu-id="73f6d-142">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="73f6d-143"><xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> では、ディスク上で Razor ファイル (Razor ビューと Razor Pages) が変更された場合に、ファイルが再コンパイルおよび更新されるかどうかを判断する値が取得または設定されます。</span><span class="sxs-lookup"><span data-stu-id="73f6d-143">The <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> gets or sets a value that determines if Razor files (Razor views and Razor Pages) are recompiled and updated if files change on disk.</span></span>

<span data-ttu-id="73f6d-144">次の場合、規定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="73f6d-144">The default value is `true` for:</span></span>

* <span data-ttu-id="73f6d-145">アプリの互換性バージョンが <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> 以前に設定されている場合</span><span class="sxs-lookup"><span data-stu-id="73f6d-145">If the app's compatibility version is set to <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> or earlier</span></span>
* <span data-ttu-id="73f6d-146">アプリの互換性バージョンが <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> 以降に設定され、アプリが開発環境 <xref:Microsoft.AspNetCore.Hosting.HostingEnvironmentExtensions.IsDevelopment*> にない場合。</span><span class="sxs-lookup"><span data-stu-id="73f6d-146">If the app's compatibility version is set to set to <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> or later and the app is in the Development environment <xref:Microsoft.AspNetCore.Hosting.HostingEnvironmentExtensions.IsDevelopment*>.</span></span> <span data-ttu-id="73f6d-147">言い換えると、<xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> が明示的に設定されていない限り、Razor ファイルは非開発環境ではコンパイルされません。</span><span class="sxs-lookup"><span data-stu-id="73f6d-147">In other words, Razor files would not recompile in non-Development environment unless <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> is explicitly set.</span></span>

<span data-ttu-id="73f6d-148">アプリの互換バージョンの設定に関するガイダンスと例については、<xref:mvc/compatibility-version> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="73f6d-148">For guidance and examples of setting the app's compatibility version, see <xref:mvc/compatibility-version>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="73f6d-149">実行時コンパイルは `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` パッケージを使用して有効になっています。</span><span class="sxs-lookup"><span data-stu-id="73f6d-149">Runtime compilation is enabled using the `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` package.</span></span> <span data-ttu-id="73f6d-150">実行時コンパイルを有効にするには、アプリで </span><span class="sxs-lookup"><span data-stu-id="73f6d-150">To enable runtime compilation, apps must</span></span>

* <span data-ttu-id="73f6d-151">[Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet パッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="73f6d-151">install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
* <span data-ttu-id="73f6d-152">アプリケーションの `ConfigureServices` を更新し、`AddMvcRazorRuntimeCompilation` の呼び出しを含めます。</span><span class="sxs-lookup"><span data-stu-id="73f6d-152">Update the application's `ConfigureServices` to include a call to `AddMvcRazorRuntimeCompilation`:</span></span>

```csharp
services
    .AddMvc()
    .AddMvcRazorRuntimeCompilation()
```

<span data-ttu-id="73f6d-153">展開時に実行時コンパイルを動作させるには、アプリでそのプロジェクト ファイルをさらに変更し、`PreserveCompilationReferences` を `true` に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="73f6d-153">For runtime compilation to work when deployed, apps must additionally modify their project files to set the `PreserveCompilationReferences` to `true`.</span></span>
[!code-xml[](view-compilation/sample/RuntimeCompilation.csproj?highlight=3)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="73f6d-154">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="73f6d-154">Additional resources</span></span>

::: moniker range="= aspnetcore-1.1"

* <xref:mvc/views/overview>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
