---
title: 'チュートリアル: ASP.NET MVC Web アプリでの EF Core の概要'
description: これは、Contoso University のサンプル アプリケーションを一から作成する方法を説明するチュートリアル シリーズの 1 回目です。
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/06/2019
ms.topic: tutorial
uid: data/ef-mvc/intro
ms.openlocfilehash: f7b557c8e560393ae886c46fad95c48ccbcc65b4
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57043469"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a><span data-ttu-id="e3be0-103">チュートリアル: ASP.NET MVC Web アプリでの EF Core の概要</span><span class="sxs-lookup"><span data-stu-id="e3be0-103">Tutorial: Get started with EF Core in an ASP.NET MVC web app</span></span>

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="e3be0-104">Contoso University のサンプル Web アプリケーションでは、Entity Framework (EF) Core 2.0 と Visual Studio 2017 を使用して ASP.NET Core 2.2 MVC Web アプリケーションを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-104">The Contoso University sample web application demonstrates how to create ASP.NET Core 2.2 MVC web applications using Entity Framework (EF) Core 2.0 and Visual Studio 2017.</span></span>

<span data-ttu-id="e3be0-105">サンプル アプリケーションは架空の Contoso University の Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="e3be0-105">The sample application is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="e3be0-106">学生の受け付け、講座の作成、講師の割り当てなどの機能が含まれています。</span><span class="sxs-lookup"><span data-stu-id="e3be0-106">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="e3be0-107">これは、Contoso University のサンプル アプリケーションを一から作成する方法を説明するチュートリアル シリーズの 1 回目です。</span><span class="sxs-lookup"><span data-stu-id="e3be0-107">This is the first in a series of tutorials that explain how to build the Contoso University sample application from scratch.</span></span>

<span data-ttu-id="e3be0-108">EF Core 2.0 は EF の最新版ですが、EF 6.x の一部の機能にまだ対応していません。</span><span class="sxs-lookup"><span data-stu-id="e3be0-108">EF Core 2.0 is the latest version of EF but doesn't yet have all the features of EF 6.x.</span></span> <span data-ttu-id="e3be0-109">EF 6.x と EF Core のどちらを選択するかについては、「[EF Core と EF 6.x を比較する](/ef/efcore-and-ef6/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e3be0-109">For information about how to choose between EF 6.x and EF Core, see [EF Core vs. EF6.x](/ef/efcore-and-ef6/).</span></span> <span data-ttu-id="e3be0-110">EF 6.x を選択する場合、[このチュートリアル シリーズの以前のバージョン](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e3be0-110">If you choose EF 6.x, see [the previous version of this tutorial series](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).</span></span>

> [!NOTE]
> <span data-ttu-id="e3be0-111">このチュートリアルの ASP.NET Core 1.1 バージョンについては、[このチュートリアルの VS 2017 Update 2 バージョン (PDF 形式)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/efmvc1.1.pdf) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e3be0-111">For the ASP.NET Core 1.1 version of this tutorial, see the [VS 2017 Update 2 version of this tutorial in PDF format](https://webpifeed.blob.core.windows.net/webpifeed/Partners/efmvc1.1.pdf).</span></span>

<span data-ttu-id="e3be0-112">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="e3be0-112">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e3be0-113">ASP.NET Core MVC Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="e3be0-113">Create ASP.NET Core MVC web app</span></span>
> * <span data-ttu-id="e3be0-114">サイトのスタイルを設定する</span><span class="sxs-lookup"><span data-stu-id="e3be0-114">Set up the site style</span></span>
> * <span data-ttu-id="e3be0-115">EF Core の NuGet パッケージについて学習する</span><span class="sxs-lookup"><span data-stu-id="e3be0-115">Learn about EF Core NuGet packages</span></span>
> * <span data-ttu-id="e3be0-116">データ モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="e3be0-116">Create the data model</span></span>
> * <span data-ttu-id="e3be0-117">データベース コンテキストの作成</span><span class="sxs-lookup"><span data-stu-id="e3be0-117">Create the database context</span></span>
> * <span data-ttu-id="e3be0-118">SchoolContext を登録する</span><span class="sxs-lookup"><span data-stu-id="e3be0-118">Register the SchoolContext</span></span>
> * <span data-ttu-id="e3be0-119">テスト データで DB を初期化する</span><span class="sxs-lookup"><span data-stu-id="e3be0-119">Initialize DB with test data</span></span>
> * <span data-ttu-id="e3be0-120">コントローラーとビューを作成する</span><span class="sxs-lookup"><span data-stu-id="e3be0-120">Create controller and views</span></span>
> * <span data-ttu-id="e3be0-121">データベースを表示する</span><span class="sxs-lookup"><span data-stu-id="e3be0-121">View the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e3be0-122">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="e3be0-122">Prerequisites</span></span>

[!INCLUDE [](~/includes/net-core-prereqs.md)]

## <a name="troubleshooting"></a><span data-ttu-id="e3be0-123">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="e3be0-123">Troubleshooting</span></span>

<span data-ttu-id="e3be0-124">解決できない問題に遭遇した場合、通常、[完成済みのプロジェクト](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)と自分のコードを比較することで解決策がわかります。</span><span class="sxs-lookup"><span data-stu-id="e3be0-124">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final).</span></span> <span data-ttu-id="e3be0-125">一般的なエラーとその解決方法の一覧については、[チュートリアル シリーズの後半に登場するトラブルシューティング セクション](advanced.md#common-errors)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="e3be0-125">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="e3be0-126">そこで必要な答えが見つからない場合、StackOverflow.com で [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) または [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) に関する質問を投稿できます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-126">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="e3be0-127">これは 10 回のチュートリアルからなるシリーズであり、いずれの回も前のチュートリアルを基盤にしています。</span><span class="sxs-lookup"><span data-stu-id="e3be0-127">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="e3be0-128">チュートリアルが完了したら、毎回、プロジェクトのコピーを保存するようお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e3be0-128">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="e3be0-129">問題に遭遇したとき、前のチュートリアルから始めることができます。シリーズ全体の始めまで戻る必要がありません。</span><span class="sxs-lookup"><span data-stu-id="e3be0-129">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="e3be0-130">Contoso University Web アプリ</span><span class="sxs-lookup"><span data-stu-id="e3be0-130">Contoso University web app</span></span>

<span data-ttu-id="e3be0-131">一連のチュートリアルで作成するアプリケーションは、簡単な大学向け Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="e3be0-131">The application you'll be building in these tutorials is a simple university web site.</span></span>

<span data-ttu-id="e3be0-132">ユーザーは学生、講座、講師の情報を見たり、更新したりできます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-132">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="e3be0-133">次のような画面をこれから作成します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-133">Here are a few of the screens you'll create.</span></span>

![Students インデックス ページ](intro/_static/students-index.png)

![Students 編集ページ](intro/_static/student-edit.png)

<span data-ttu-id="e3be0-136">このサイトの UI スタイルは、組み込みテンプレートで生成されるスタイルに近いものになっています。それにより、このチュートリアルでは主に、Entity Framework の使い方を取り上げることができます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-136">The UI style of this site has been kept close to what's generated by the built-in templates, so that the tutorial can focus mainly on how to use the Entity Framework.</span></span>

## <a name="create-aspnet-core-mvc-web-app"></a><span data-ttu-id="e3be0-137">ASP.NET Core MVC Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="e3be0-137">Create ASP.NET Core MVC web app</span></span>

<span data-ttu-id="e3be0-138">Visual Studio を開き、新しい ASP.NET Core C# Web プロジェクトを作成します。プロジェクトの名前は "ContosoUniversity" です。</span><span class="sxs-lookup"><span data-stu-id="e3be0-138">Open Visual Studio and create a new ASP.NET Core C# web project named "ContosoUniversity".</span></span>

* <span data-ttu-id="e3be0-139">**[ファイル]** メニューで **[新規作成]、[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-139">From the **File** menu, select **New > Project**.</span></span>

* <span data-ttu-id="e3be0-140">左側のウィンドウで、**[インストール済み]、[Visual C#]、[Web]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-140">From the left pane, select **Installed > Visual C# > Web**.</span></span>

* <span data-ttu-id="e3be0-141">**[ASP.NET Core Web アプリケーション]** プロジェクト テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-141">Select the **ASP.NET Core Web Application** project template.</span></span>

* <span data-ttu-id="e3be0-142">名前に「**ContosoUniversity**」と入力し、**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e3be0-142">Enter **ContosoUniversity** as the name and click **OK**.</span></span>

  ![[新しいプロジェクト] ダイアログ](intro/_static/new-project2.png)

* <span data-ttu-id="e3be0-144">**[新しい ASP.NET Core Web アプリケーション (.NET Core)]** ダイアログが表示されるのを待ちます</span><span class="sxs-lookup"><span data-stu-id="e3be0-144">Wait for the **New ASP.NET Core Web Application (.NET Core)** dialog to appear</span></span>

  ![[新しい ASP.NET Core プロジェクト] ダイアログ](intro/_static/new-aspnet2.png)

* <span data-ttu-id="e3be0-146">**[ASP.NET Core 2.2]** と **[Web アプリケーション (モデル ビュー コントローラー)]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-146">Select **ASP.NET Core 2.2** and the **Web Application (Model-View-Controller)** template.</span></span>

  <span data-ttu-id="e3be0-147">**注:** このチュートリアルでは、ASP.NET Core 2.2 と EF Core 2.0 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="e3be0-147">**Note:** This tutorial requires ASP.NET Core 2.2 and EF Core 2.0 or later.</span></span>

* <span data-ttu-id="e3be0-148">**[認証]** に **[認証なし]** が設定されていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="e3be0-148">Make sure **Authentication** is set to **No Authentication**.</span></span>

* <span data-ttu-id="e3be0-149">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e3be0-149">Click **OK**</span></span>

## <a name="set-up-the-site-style"></a><span data-ttu-id="e3be0-150">サイトのスタイルを設定する</span><span class="sxs-lookup"><span data-stu-id="e3be0-150">Set up the site style</span></span>

<span data-ttu-id="e3be0-151">簡単な変更をいくつか行い、サイトのメニュー、レイアウト、ホーム ページを決めます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-151">A few simple changes will set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="e3be0-152">*Views/Shared/_Layout.cshtml* を開き、次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-152">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="e3be0-153">"ContosoUniversity" をすべて "Contoso University" に変更します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-153">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="e3be0-154">これは 3 回出てきます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-154">There are three occurrences.</span></span>

* <span data-ttu-id="e3be0-155">メニュー エントリとして「**About**」、「**Students**」、「**Courses**」、「**Instructors**」、「**Departments**」を追加し、「**Privacy**」メニュー エントリを削除します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-155">Add menu entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="e3be0-156">変更が強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-156">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,32-36,51)]

<span data-ttu-id="e3be0-157">*Views/Home/Index.cshtml* で、ファイルの中身を次のコードに変更し、ASP.NET と MVC に関するテキストをこのアプリケーションに関するテキストに変更します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-157">In *Views/Home/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this application:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

<span data-ttu-id="e3be0-158">CTRL を押しながら F5 を押してプロジェクトを実行するか、メニューで **[デバッグ]、[デバッグなしで開始]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-158">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="e3be0-159">一連のチュートリアルで作成するページのホーム ページとタブが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-159">You see the home page with tabs for the pages you'll create in these tutorials.</span></span>

![Contoso University のホーム ページ](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a><span data-ttu-id="e3be0-161">EF Core の NuGet パッケージについて</span><span class="sxs-lookup"><span data-stu-id="e3be0-161">About EF Core NuGet packages</span></span>

<span data-ttu-id="e3be0-162">プロジェクトに EF Core サポートを追加するには、対象とするデータベース プロバイダーをインストールします。</span><span class="sxs-lookup"><span data-stu-id="e3be0-162">To add EF Core support to a project, install the database provider that you want to target.</span></span> <span data-ttu-id="e3be0-163">このチュートリアルでは SQL Server を使用します。プロバイダー パッケージは [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) です。</span><span class="sxs-lookup"><span data-stu-id="e3be0-163">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span> <span data-ttu-id="e3be0-164">このパッケージは [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれているので、アプリに `Microsoft.AspNetCore.App` パッケージのパッケージ参照がある場合、パッケージを参照する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e3be0-164">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to reference the package if your app has a package reference for the `Microsoft.AspNetCore.App` package.</span></span>

<span data-ttu-id="e3be0-165">このパッケージとその依存関係 (`Microsoft.EntityFrameworkCore` と `Microsoft.EntityFrameworkCore.Relational`) により、EF のランタイム サポートが与えられます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-165">This package and its dependencies (`Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`) provide runtime support for EF.</span></span> <span data-ttu-id="e3be0-166">後の[移行](migrations.md)チュートリアルでツール パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-166">You'll add a tooling package later, in the [Migrations](migrations.md) tutorial.</span></span>

<span data-ttu-id="e3be0-167">Entity Framework Core で利用できるその他のデータベース プロバイダーに関しては、「[データベース プロバイダー](/ef/core/providers/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e3be0-167">For information about other database providers that are available for Entity Framework Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="e3be0-168">データ モデルの作成</span><span class="sxs-lookup"><span data-stu-id="e3be0-168">Create the data model</span></span>

<span data-ttu-id="e3be0-169">次に、Contoso University アプリケーションのエンティティ クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-169">Next you'll create entity classes for the Contoso University application.</span></span> <span data-ttu-id="e3be0-170">次の 3 つのエンティティから始めます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-170">You'll start with the following three entities.</span></span>

![講座、登録、学生からなるデータ モデルの図](intro/_static/data-model-diagram.png)

<span data-ttu-id="e3be0-172">`Student` エンティティと `Enrollment` エンティティの間に一対多の関係があり、`Course` エンティティと `Enrollment` エンティティの間に一対多の関係があります。</span><span class="sxs-lookup"><span data-stu-id="e3be0-172">There's a one-to-many relationship between `Student` and `Enrollment` entities, and there's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="e3be0-173">言い換えると、1 人の学生をさまざまな講座に登録し、1 つの講座にたくさんの学生を登録できます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-173">In other words, a student can be enrolled in any number of courses, and a course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="e3be0-174">次のセクションでは、エンティティごとにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-174">In the following sections you'll create a class for each one of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="e3be0-175">Student エンティティ</span><span class="sxs-lookup"><span data-stu-id="e3be0-175">The Student entity</span></span>

![Student エンティティの図](intro/_static/student-entity.png)

<span data-ttu-id="e3be0-177">*[Models]* フォルダーで、*Student.cs* という名前のクラス ファイルを作成し、テンプレート コードを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-177">In the *Models* folder, create a class file named *Student.cs* and replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="e3be0-178">`ID` プロパティは、このクラスに相当するデータベース テーブルの主キー列になります。</span><span class="sxs-lookup"><span data-stu-id="e3be0-178">The `ID` property will become the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="e3be0-179">既定では、Entity Framework は、`ID` または `classnameID` という名前のプロパティを主キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-179">By default, the Entity Framework interprets a property that's named `ID` or `classnameID` as the primary key.</span></span>

<span data-ttu-id="e3be0-180">`Enrollments` プロパティは[ナビゲーション プロパティ](/ef/core/modeling/relationships)です。</span><span class="sxs-lookup"><span data-stu-id="e3be0-180">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="e3be0-181">ナビゲーション プロパティには、このエンティティに関連する他のエンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-181">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="e3be0-182">この例では、`Student entity` の `Enrollments` プロパティで、その `Student` エンティティに関連するすべての `Enrollment` エンティティが保持されます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-182">In this case, the `Enrollments` property of a `Student entity` will hold all of the `Enrollment` entities that are related to that `Student` entity.</span></span> <span data-ttu-id="e3be0-183">つまり、データベースの Student 行にある 2 つの Enrollment 行が関連している場合 (外部キー列 StudentID にその学生の主キー値が含まれる行)、その `Student` エンティティの `Enrollments` ナビゲーション プロパティにその 2 つの `Enrollment` エンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-183">In other words, if a given Student row in the database has two related Enrollment rows (rows that contain that student's primary key value in their StudentID foreign key column), that `Student` entity's `Enrollments` navigation property will contain those two `Enrollment` entities.</span></span>

<span data-ttu-id="e3be0-184">ナビゲーション プロパティに複数のエンティティが含まれる場合 (多対多または一対多の関係で)、その型はリストにする必要があります。`ICollection<T>` のように、エンティティを追加、削除、更新できるリストです。</span><span class="sxs-lookup"><span data-stu-id="e3be0-184">If a navigation property can hold multiple entities (as in many-to-many or one-to-many relationships), its type must be a list in which entries can be added, deleted, and updated, such as `ICollection<T>`.</span></span> <span data-ttu-id="e3be0-185">`ICollection<T>`、または `List<T>` や `HashSet<T>` などの型を指定することができます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-185">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="e3be0-186">`ICollection<T>` を指定した場合、EF では既定で `HashSet<T>` コレクションが作成されます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-186">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="e3be0-187">Enrollment エンティティ</span><span class="sxs-lookup"><span data-stu-id="e3be0-187">The Enrollment entity</span></span>

![Enrollment エンティティの図](intro/_static/enrollment-entity.png)

<span data-ttu-id="e3be0-189">*[Models]* フォルダーで、*Enrollment.cs* を作成し、既存のコードを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-189">In the *Models* folder, create *Enrollment.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="e3be0-190">`EnrollmentID` プロパティは主キーになります。このエンティティは、`Student` エンティティと同様に、`ID` ではなく `classnameID` パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-190">The `EnrollmentID` property will be the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself as you saw in the `Student` entity.</span></span> <span data-ttu-id="e3be0-191">通常、パターンを 1 つ選択し、データ モデル全体でそれを使用します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-191">Ordinarily you would choose one pattern and use it throughout your data model.</span></span> <span data-ttu-id="e3be0-192">ここのバリエーションから、いずれのパターンも利用できることがわかります。</span><span class="sxs-lookup"><span data-stu-id="e3be0-192">Here, the variation illustrates that you can use either pattern.</span></span> <span data-ttu-id="e3be0-193">[後のチュートリアル](inheritance.md)では、クラス名なしの ID を利用し、データ モデルに継承を簡単に実装する方法を学習します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-193">In a [later tutorial](inheritance.md), you'll see how using ID without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="e3be0-194">`Grade` プロパティは `enum` です。</span><span class="sxs-lookup"><span data-stu-id="e3be0-194">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="e3be0-195">`Grade` の型宣言の後の疑問符は、`Grade` プロパティが null 許容であることを示します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-195">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="e3be0-196">null という成績は、0 の成績とは異なります。null は成績がわからないことか、まだ評価されていないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-196">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="e3be0-197">`StudentID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Student` です。</span><span class="sxs-lookup"><span data-stu-id="e3be0-197">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="e3be0-198">`Enrollment` エンティティは 1 つの `Student` エンティティに関連付けられており、1 つの `Student` エンティティだけを保持できます (先に見た、複数の `Enrollment` エンティティを保持できる `Student.Enrollments` ナビゲーション プロパティとは異なります)。</span><span class="sxs-lookup"><span data-stu-id="e3be0-198">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity (unlike the `Student.Enrollments` navigation property you saw earlier, which can hold multiple `Enrollment` entities).</span></span>

<span data-ttu-id="e3be0-199">`CourseID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Course` です。</span><span class="sxs-lookup"><span data-stu-id="e3be0-199">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="e3be0-200">`Enrollment` エンティティは 1 つの `Course` エンティティに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-200">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="e3be0-201">Entity Framework は `<navigation property name><primary key property name>` という名前が付いている場合、プロパティを外部キー プロパティとして解釈します。たとえば、`Student` ナビゲーション プロパティの `StudentID` です。`Student` エンティティの主キーが `ID` であるためです。</span><span class="sxs-lookup"><span data-stu-id="e3be0-201">Entity Framework interprets a property as a foreign key property if it's named `<navigation property name><primary key property name>` (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="e3be0-202">外部キーにも `<primary key property name>` という単純な名前を付けることができます。たとえば、`CourseID` です。`Course` エンティティの主キーが `CourseID` であるためです。</span><span class="sxs-lookup"><span data-stu-id="e3be0-202">Foreign key properties can also be named simply `<primary key property name>` (for example, `CourseID` since the `Course` entity's primary key is `CourseID`).</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="e3be0-203">Course エンティティ</span><span class="sxs-lookup"><span data-stu-id="e3be0-203">The Course entity</span></span>

![Course エンティティの図](intro/_static/course-entity.png)

<span data-ttu-id="e3be0-205">*[Models]* フォルダーで、*Course.cs* を作成し、既存のコードを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-205">In the *Models* folder, create *Course.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="e3be0-206">`Enrollments` プロパティはナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="e3be0-206">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="e3be0-207">1 つの `Course` エンティティにたくさんの `Enrollment` エンティティを関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-207">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="e3be0-208">`DatabaseGenerated` 属性については、このシリーズの[後のチュートリアル](complex-data-model.md)で詳しく学習します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-208">We'll say more about the `DatabaseGenerated` attribute in a [later tutorial](complex-data-model.md) in this series.</span></span> <span data-ttu-id="e3be0-209">基本的に、この属性によって、講座の主キーをデータベースに生成させず、自分で入力できるようになります。</span><span class="sxs-lookup"><span data-stu-id="e3be0-209">Basically, this attribute lets you enter the primary key for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="e3be0-210">データベース コンテキストの作成</span><span class="sxs-lookup"><span data-stu-id="e3be0-210">Create the database context</span></span>

<span data-ttu-id="e3be0-211">所与のデータ モデルの Entity Framework 機能を調整するメイン クラスは、データベース コンテキスト クラスです。</span><span class="sxs-lookup"><span data-stu-id="e3be0-211">The main class that coordinates Entity Framework functionality for a given data model is the database context class.</span></span> <span data-ttu-id="e3be0-212">このクラスは、`Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-212">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="e3be0-213">自分のコードでは、データ モデルに含めるエンティティを自分で指定します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-213">In your code you specify which entities are included in the data model.</span></span> <span data-ttu-id="e3be0-214">Entity Framework の特定の動作をカスタマイズすることもできます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-214">You can also customize certain Entity Framework behavior.</span></span> <span data-ttu-id="e3be0-215">このプロジェクトでは、クラスに `SchoolContext` という名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="e3be0-215">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="e3be0-216">プロジェクト フォルダーで、*Data* という名前のフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-216">In the project folder, create a folder named *Data*.</span></span>

<span data-ttu-id="e3be0-217">*[Data]* フォルダーで、*SchoolContext.cs* という名前の新しいクラス ファイルを作成し、テンプレート コードを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-217">In the *Data* folder create a new class file named *SchoolContext.cs*, and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="e3be0-218">このコードによって、エンティティ セットごとに `DbSet` プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-218">This code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="e3be0-219">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応し、エンティティはテーブルの行に対応します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-219">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e3be0-220">`DbSet<Enrollment>` ステートメントと `DbSet<Course>` ステートメントは省略しても同じ動作をします。</span><span class="sxs-lookup"><span data-stu-id="e3be0-220">You could've omitted the `DbSet<Enrollment>` and `DbSet<Course>` statements and it would work the same.</span></span> <span data-ttu-id="e3be0-221">Entity Framework にはそれらが暗黙的に含まれることがあります。`Student` エンティティが `Enrollment` エンティティを参照し、`Enrollment` エンティティが `Course` エンティティを参照するためです。</span><span class="sxs-lookup"><span data-stu-id="e3be0-221">The Entity Framework would include them implicitly because the `Student` entity references the `Enrollment` entity and the `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="e3be0-222">データベースが作成されると、EF によって、`DbSet` プロパティと同じ名前を持つテーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-222">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="e3be0-223">一般的にコレクションのプロパティ名は複数形 (Student ではなく、Students) ですが、テーブル名を複数にするかどうかについては、開発者の間で意見が分かれています。</span><span class="sxs-lookup"><span data-stu-id="e3be0-223">Property names for collections are typically plural (Students rather than Student), but developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="e3be0-224">このチュートリアル シリーズでは、DbContext に単数のテーブル名を指定して既定の動作をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="e3be0-224">For these tutorials you'll override the default behavior by specifying singular table names in the DbContext.</span></span> <span data-ttu-id="e3be0-225">そのために、最後の DbSet プロパティの後に、次の強調表示されているコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-225">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a><span data-ttu-id="e3be0-226">SchoolContext を登録する</span><span class="sxs-lookup"><span data-stu-id="e3be0-226">Register the SchoolContext</span></span>

<span data-ttu-id="e3be0-227">ASP.NET Core は既定で[依存関係の挿入](../../fundamentals/dependency-injection.md)を実装します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-227">ASP.NET Core implements [dependency injection](../../fundamentals/dependency-injection.md) by default.</span></span> <span data-ttu-id="e3be0-228">サービス (EF データベース コンテキストなど) は、アプリケーションの起動時に依存関係の挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-228">Services (such as the EF database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e3be0-229">これらのサービス (MVC コント ローラーなど) を必要とするコンポーネントには、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-229">Components that require these services (such as MVC controllers) are provided these services via constructor parameters.</span></span> <span data-ttu-id="e3be0-230">このチュートリアルの後半で、コンテキスト インスタンスを取得するコントローラー コンストラクター コードが登場します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-230">You'll see the controller constructor code that gets a context instance later in this tutorial.</span></span>

<span data-ttu-id="e3be0-231">`SchoolContext` をサービスとして登録するには、*Startup.cs* を開き、強調表示されている行を `ConfigureServices` メソッドに追加します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-231">To register `SchoolContext` as a service, open *Startup.cs*, and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=3-4)]

<span data-ttu-id="e3be0-232">`DbContextOptionsBuilder` オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-232">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="e3be0-233">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)が *appsettings.json* ファイルから接続文字列を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="e3be0-233">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="e3be0-234">名前空間の `ContosoUniversity.Data` と `Microsoft.EntityFrameworkCore` に対して `using` ステートメントを追加し、プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="e3be0-234">Add `using` statements for `ContosoUniversity.Data` and `Microsoft.EntityFrameworkCore` namespaces, and then build the project.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

<span data-ttu-id="e3be0-235">*appsettings.json* ファイルを開き、次のサンプルのように接続文字列を追加します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-235">Open the *appsettings.json* file and add a connection string as shown in the following example.</span></span>

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a><span data-ttu-id="e3be0-236">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="e3be0-236">SQL Server Express LocalDB</span></span>

<span data-ttu-id="e3be0-237">この接続文字列によって SQL Server LocalDB データベースが指定されます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-237">The connection string specifies a SQL Server LocalDB database.</span></span> <span data-ttu-id="e3be0-238">LocalDB は SQL Server Express データベース エンジンの軽量版であり、実稼働ではなく、アプリケーションの開発を意図して設計されています。</span><span class="sxs-lookup"><span data-stu-id="e3be0-238">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for application development, not production use.</span></span> <span data-ttu-id="e3be0-239">LocalDB は要求時に開始され、ユーザー モードで実行されるため、複雑な構成はありません。</span><span class="sxs-lookup"><span data-stu-id="e3be0-239">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="e3be0-240">既定では、LocalDB は `C:/Users/<user>` ディレクトリに *.mdf* データベース ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-240">By default, LocalDB creates *.mdf* database files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="e3be0-241">テスト データで DB を初期化する</span><span class="sxs-lookup"><span data-stu-id="e3be0-241">Initialize DB with test data</span></span>

<span data-ttu-id="e3be0-242">Entity Framework によって空のデータベースが自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-242">The Entity Framework will create an empty database for you.</span></span> <span data-ttu-id="e3be0-243">このセクションでは、テスト データを入力する目的で、データベースの作成後に呼び出されるメソッドを記述します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-243">In this section, you write a method that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="e3be0-244">ここでは、データベースを自動的に作成する `EnsureCreated` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-244">Here you'll use the `EnsureCreated` method to automatically create the database.</span></span> <span data-ttu-id="e3be0-245">[後のチュートリアル](migrations.md)では、モデル変更の処理方法について学習します。データベースを削除し、再作成するのではなく、Code First Migrations を利用してデータベース スキーマを変更します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-245">In a [later tutorial](migrations.md) you'll see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="e3be0-246">*[データ]* フォルダーで *DbInitializer.cs* という名前の新しいクラス ファイルを作成し、テンプレート コードを次のコードに変更します。このコードにより、必要なときにデータベースが作成され、新しいデータベースにテスト データが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-246">In the *Data* folder, create a new class file named *DbInitializer.cs* and replace the template code with the following code, which causes a database to be created when needed and loads test data into the new database.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="e3be0-247">このコードはデータベースに学生が存在するかどうかを確認し、存在しない場合、そのデータベースは新しく、テスト データを入力する必要があると見なします。</span><span class="sxs-lookup"><span data-stu-id="e3be0-247">The code checks if there are any students in the database, and if not, it assumes the database is new and needs to be seeded with test data.</span></span> <span data-ttu-id="e3be0-248">`List<T>` コレクションではなく配列にテスト データを読み込み、パフォーマンスを最適化します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-248">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="e3be0-249">*Program.cs* で、アプリケーションの起動時に次を実行するように `Main` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-249">In *Program.cs*, modify the `Main` method to do the following on application startup:</span></span>

* <span data-ttu-id="e3be0-250">依存関係挿入コンテナーからデータベース コンテキスト インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-250">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="e3be0-251">seed メソッドを呼び出し、コンテキストを渡します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-251">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="e3be0-252">seed メソッドが完了したら、コンテキストを破棄します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-252">Dispose the context when the seed method is done.</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

<span data-ttu-id="e3be0-253">`using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-253">Add `using` statements:</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Usings)]

<span data-ttu-id="e3be0-254">以前のチュートリアルでは、*Startup.cs* で `Configure` メソッドと同様のコードを確認できるかもしれません。</span><span class="sxs-lookup"><span data-stu-id="e3be0-254">In older tutorials, you may see similar code in the `Configure` method in *Startup.cs*.</span></span> <span data-ttu-id="e3be0-255">要求パイプラインを設定する目的でのみ `Configure` メソッドを利用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e3be0-255">We recommend that you use the `Configure` method only to set up the request pipeline.</span></span> <span data-ttu-id="e3be0-256">アプリケーションの起動コードは、`Main` メソッドに属します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-256">Application startup code belongs in the `Main` method.</span></span>

<span data-ttu-id="e3be0-257">これからアプリケーションを初めて実行します。データベースが作成され、テスト データが入力されます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-257">Now the first time you run the application, the database will be created and seeded with test data.</span></span> <span data-ttu-id="e3be0-258">データ モデルを変更するたびに、データベースを削除し、seed メソッドを更新し、新しいデータベースで同様にやり直すことができます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-258">Whenever you change your data model, you can delete the database, update your seed method, and start afresh with a new database the same way.</span></span> <span data-ttu-id="e3be0-259">後のチュートリアルでは、データ モデルが変わったとき、データベースを削除して作り直すのではなく、修正する方法について学習します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-259">In later tutorials, you'll see how to modify the database when the data model changes, without deleting and re-creating it.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="e3be0-260">コントローラーとビューを作成する</span><span class="sxs-lookup"><span data-stu-id="e3be0-260">Create controller and views</span></span>

<span data-ttu-id="e3be0-261">次に、Visual Studio のスキャフォールディング エンジンを利用し、MVC のコントローラーとビューを追加します。このコントローラーとビューでは、EF を利用してクエリを実行し、データを保存します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-261">Next, you'll use the scaffolding engine in Visual Studio to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="e3be0-262">CRUD アクションのメソッドとビューの自動作成は、スキャフォールディングと言います。</span><span class="sxs-lookup"><span data-stu-id="e3be0-262">The automatic creation of CRUD action methods and views is known as scaffolding.</span></span> <span data-ttu-id="e3be0-263">一般的には生成されたコードは修正しないのに対し、スキャフォールディングされたコードを開始点として独自の要件に合うように変更できるという点で、スキャフォールディングはコード生成と異なります。</span><span class="sxs-lookup"><span data-stu-id="e3be0-263">Scaffolding differs from code generation in that the scaffolded code is a starting point that you can modify to suit your own requirements, whereas you typically don't modify generated code.</span></span> <span data-ttu-id="e3be0-264">生成されたコードをカスタマイズする必要があるとき、部分クラスを利用するか、状況が変わったときにコードを再生成します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-264">When you need to customize generated code, you use partial classes or you regenerate the code when things change.</span></span>

* <span data-ttu-id="e3be0-265">**ソリューション エクスプローラー**の **Controllers** フォルダーを右クリックし、**[追加]、[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-265">Right-click the **Controllers** folder in **Solution Explorer** and select **Add > New Scaffolded Item**.</span></span>

<span data-ttu-id="e3be0-266">**[MVC 依存関係の追加]** ダイアログ ボックスが表示された場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="e3be0-266">If the **Add MVC Dependencies** dialog appears:</span></span>

* <span data-ttu-id="e3be0-267">[Visual Studio を最新バージョンに更新します](https://www.visualstudio.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)。</span><span class="sxs-lookup"><span data-stu-id="e3be0-267">[Update Visual Studio to the latest version](https://www.visualstudio.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017).</span></span> <span data-ttu-id="e3be0-268">15.5 より前のバージョンの Visual Studio の場合はこのダイアログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-268">Visual Studio versions prior to 15.5 show this dialog.</span></span>
* <span data-ttu-id="e3be0-269">更新できない場合は、**[追加]** を選択してから、もう一度コントローラーの追加手順に従ってください。</span><span class="sxs-lookup"><span data-stu-id="e3be0-269">If you can't update, select **ADD**, and then follow the add controller steps again.</span></span>

* <span data-ttu-id="e3be0-270">**[スキャフォールディングを追加]** ダイアログ ボックスで:</span><span class="sxs-lookup"><span data-stu-id="e3be0-270">In the **Add Scaffold** dialog box:</span></span>

  * <span data-ttu-id="e3be0-271">**[Entity Framework を使用したビューがある MVC コントローラー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-271">Select **MVC controller with views, using Entity Framework**.</span></span>

  * <span data-ttu-id="e3be0-272">**[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e3be0-272">Click **Add**.</span></span> <span data-ttu-id="e3be0-273">**[Add MVC Controller with views, using Entity Framework]\(Entity Framework を使用したビューがある MVC コントローラーを追加する\)** ダイアログ ボックスが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-273">The **Add MVC Controller with views, using Entity Framework** dialog box appears.</span></span>

    ![Student のスキャフォールディング](intro/_static/scaffold-student2.png)

  * <span data-ttu-id="e3be0-275">**[モデル クラス]** で **[Student]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-275">In **Model class** select **Student**.</span></span>

  * <span data-ttu-id="e3be0-276">**[データ コンテキスト クラス]** で **[SchoolContext]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-276">In **Data context class** select **SchoolContext**.</span></span>

  * <span data-ttu-id="e3be0-277">名前は **StudentsController** をそのまま選択します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-277">Accept the default **StudentsController** as the name.</span></span>

  * <span data-ttu-id="e3be0-278">**[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e3be0-278">Click **Add**.</span></span>

  <span data-ttu-id="e3be0-279">**[追加]** をクリックすると、Visual Studio スキャフォールディング エンジンは *StudentsController.cs* ファイルと、コントローラーと連動する一連のビュー (*.cshtml* ファイル) を作成します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-279">When you click **Add**, the Visual Studio scaffolding engine creates a *StudentsController.cs* file and a set of views (*.cshtml* files) that work with the controller.</span></span>

<span data-ttu-id="e3be0-280">(スキャフォールディング エンジンは、このチュートリアルで先に行ったように手動で最初に作成しない場合、データベース コンテキストを自動作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-280">(The scaffolding engine can also create the database context for you if you don't create it manually first as you did earlier for this tutorial.</span></span> <span data-ttu-id="e3be0-281">**[コントローラーの追加]** ボックスで新しいコンテキスト クラスを指定できます。**[データ コンテキスト クラス]** の右にあるプラス記号をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e3be0-281">You can specify a new context class in the **Add Controller** box by clicking the plus sign to the right of **Data context class**.</span></span>  <span data-ttu-id="e3be0-282">Visual Studio はコントローラーやビューと共に `DbContext` クラスを作成します。)</span><span class="sxs-lookup"><span data-stu-id="e3be0-282">Visual Studio will then create your `DbContext` class as well as the controller and views.)</span></span>

<span data-ttu-id="e3be0-283">コントローラーがコンストラクター パラメーターとして `SchoolContext` を受け取ることがわかります。</span><span class="sxs-lookup"><span data-stu-id="e3be0-283">You'll notice that the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="e3be0-284">ASP.NET Core 依存関係挿入では、`SchoolContext` のインスタンスがコントローラーに渡されます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-284">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="e3be0-285">それは先に *Startup.cs* ファイルで構成しました。</span><span class="sxs-lookup"><span data-stu-id="e3be0-285">You configured that in the *Startup.cs* file earlier.</span></span>

<span data-ttu-id="e3be0-286">コントローラーには `Index` アクション メソッドが含まれます。これはデータベースにあるすべての学生を表示します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-286">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="e3be0-287">このメソッドはデータベース コンテキスト インスタンスの `Students` プロパティを読み取り、Students エンティティ セットから学生の一覧を取得します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-287">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="e3be0-288">チュートリアルの後半で、このコードの非同期プログラミング要素について学習します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-288">You'll learn about the asynchronous programming elements in this code later in the tutorial.</span></span>

<span data-ttu-id="e3be0-289">*Views/Students/Index.cshtml* ビューには、この一覧で表形式で表示されます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-289">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="e3be0-290">CTRL を押しながら F5 を押してプロジェクトを実行するか、メニューで **[デバッグ]、[デバッグなしで開始]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-290">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="e3be0-291">[Students] タブをクリックすると、`DbInitializer.Initialize` メソッドによって挿入されたテスト データが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-291">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="e3be0-292">ブラウザーのウィンドウ幅によって決まることですが、`Student` タブ リンクはページの一番上に表示されるか、右上隅のナビゲーション アイコンをクリックしないと表示されません。</span><span class="sxs-lookup"><span data-stu-id="e3be0-292">Depending on how narrow your browser window is, you'll see the `Student` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Contoso University のホーム ページ (ウィンドウ幅が狭いとき)](intro/_static/home-page-narrow.png)

![Students インデックス ページ](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="e3be0-295">データベースを表示する</span><span class="sxs-lookup"><span data-stu-id="e3be0-295">View the database</span></span>

<span data-ttu-id="e3be0-296">アプリケーションを起動する葉、`DbInitializer.Initialize` メソッドが `EnsureCreated` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-296">When you started the application, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="e3be0-297">EF はデータベースがないことを認識し、作成します。`Initialize` メソッド コードの残りの部分により、データベースにデータが入力されます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-297">EF saw that there was no database and so it created one, then the remainder of the `Initialize` method code populated the database with data.</span></span> <span data-ttu-id="e3be0-298">**SQL Server Object Explorer** (SSOX) を利用し、Visual Studio でデータベースを表示できます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-298">You can use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio.</span></span>

<span data-ttu-id="e3be0-299">ブラウザーを閉じます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-299">Close the browser.</span></span>

<span data-ttu-id="e3be0-300">SSOX ウィンドウがまだ開いていない場合、Visual Studio の **[表示]** メニューから選択します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-300">If the SSOX window isn't already open, select it from the **View** menu in Visual Studio.</span></span>

<span data-ttu-id="e3be0-301">SSOX で **(localdb)\MSSQLLocalDB > Databases** をクリックし、*appsettings.json* ファイルの接続文字列にあるデータベース名のエントリをクリックします。</span><span class="sxs-lookup"><span data-stu-id="e3be0-301">In SSOX, click **(localdb)\MSSQLLocalDB > Databases**, and then click the entry for the database name that's in the connection string in your *appsettings.json* file.</span></span>

<span data-ttu-id="e3be0-302">**[テーブル]** ノードを展開し、データベースのテーブルを表示します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-302">Expand the **Tables** node to see the tables in your database.</span></span>

![SSOX のテーブル](intro/_static/ssox-tables.png)

<span data-ttu-id="e3be0-304">**[Student]** テーブルを右クリックし、**[データの表示]** をクリックすると、作成された列とテーブルに挿入された行が表示されます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-304">Right-click the **Student** table and click **View Data** to see the columns that were created and the rows that were inserted into the table.</span></span>

![SSOX の Student テーブル](intro/_static/ssox-student-table.png)

<span data-ttu-id="e3be0-306"><em>.mdf</em> データベース ファイルと <em>.ldf</em> データベース ファイルは <em>C:\Users\\<yourusername></em> フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="e3be0-306">The <em>.mdf</em> and <em>.ldf</em> database files are in the <em>C:\Users\\<yourusername></em> folder.</span></span>

<span data-ttu-id="e3be0-307">アプリの起動時に実行される初期化子メソッドで `EnsureCreated` を呼び出すため、`Student` クラスを変更し、データベースを削除し、アプリケーションを再実行できます。変更に合わせてデータベースが自動的に再作成されます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-307">Because you're calling `EnsureCreated` in the initializer method that runs on app start, you could now make a change to the `Student` class, delete the database, run the application again, and the database would automatically be re-created to match your change.</span></span> <span data-ttu-id="e3be0-308">たとえば、`Student` クラスに `EmailAddress` プロパティを追加する場合、再作成されたテーブルに新しい `EmailAddress` 列が表示されます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-308">For example, if you add an `EmailAddress` property to the `Student` class, you'll see a new `EmailAddress` column in the re-created table.</span></span>

## <a name="conventions"></a><span data-ttu-id="e3be0-309">規約</span><span class="sxs-lookup"><span data-stu-id="e3be0-309">Conventions</span></span>

<span data-ttu-id="e3be0-310">規約を利用することや Entity Framework が想定を行うことにより、Entity Framework が完全なデータベースを自動作成するために記述しなければならないコードの量が最小限に抑えられます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-310">The amount of code you had to write in order for the Entity Framework to be able to create a complete database for you is minimal because of the use of conventions, or assumptions that the Entity Framework makes.</span></span>

* <span data-ttu-id="e3be0-311">`DbSet` プロパティの名前がテーブル名として使用されます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-311">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="e3be0-312">`DbSet` プロパティによって参照されないエンティティについては、エンティティ クラス名がテーブル名として使用されます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-312">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>

* <span data-ttu-id="e3be0-313">列名には、エンティティ プロパティ名が使用されます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-313">Entity property names are used for column names.</span></span>

* <span data-ttu-id="e3be0-314">ID または classnameID という名前が付けられているエンティティ プロパティは主キーのプロパティとして認識されます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-314">Entity properties that are named ID or classnameID are recognized as primary key properties.</span></span>

* <span data-ttu-id="e3be0-315">*<navigation property name><primary key property name>* という名前が付いている場合、プロパティは外部キー プロパティとして解釈されます。たとえば、`Student` ナビゲーション プロパティの `StudentID` です。`Student` エンティティの主キーが `ID` であるためです。</span><span class="sxs-lookup"><span data-stu-id="e3be0-315">A property is interpreted as a foreign key property if it's named *<navigation property name><primary key property name>* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="e3be0-316">外部キーにも *<primary key property name>* という単純な名前を付けることができます。たとえば、`EnrollmentID` です。`Enrollment` エンティティの主キーが `EnrollmentID` であるためです。</span><span class="sxs-lookup"><span data-stu-id="e3be0-316">Foreign key properties can also be named simply *<primary key property name>* (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="e3be0-317">従来の動作をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-317">Conventional behavior can be overridden.</span></span> <span data-ttu-id="e3be0-318">たとえば、このチュートリアルで先に見たように、テーブル名を明示的に指定できます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-318">For example, you can explicitly specify table names, as you saw earlier in this tutorial.</span></span> <span data-ttu-id="e3be0-319">また、列名を設定し、任意のプロパティを主キーまたは外部キーとして設定できます。これについては、このシリーズの[後のチュートリアル](complex-data-model.md)で学習します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-319">And you can set column names and set any property as primary key or foreign key, as you'll see in a [later tutorial](complex-data-model.md) in this series.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="e3be0-320">非同期コード</span><span class="sxs-lookup"><span data-stu-id="e3be0-320">Asynchronous code</span></span>

<span data-ttu-id="e3be0-321">ASP.NET Core と EF Core では、非同期プログラミングが既定のモードです。</span><span class="sxs-lookup"><span data-stu-id="e3be0-321">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="e3be0-322">Web サーバーでは、利用できるスレッド数に限りがあります。負荷が高い状況では、利用できるスレッドが全部使われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e3be0-322">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="e3be0-323">その場合、スレッドが解放されるまでサーバーは新しい要求を処理できません。</span><span class="sxs-lookup"><span data-stu-id="e3be0-323">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="e3be0-324">同期コードの場合、たくさんのスレッドが関連付けられていても、I/O の完了を待っているため、実際には何の作業も行っていないということがあります。</span><span class="sxs-lookup"><span data-stu-id="e3be0-324">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="e3be0-325">非同期コードの場合、あるプロセスが I/O の完了を待っているとき、そのスレッドは解放され、サーバーによって他の要求の処理に利用できます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-325">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="e3be0-326">結果として、非同期コードの場合、サーバー リソースをより効率的に利用できます。サーバーは、より多くのトラフィックを遅延なく処理できます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-326">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="e3be0-327">非同期コードは実行時に少量のオーバーヘッドを発生させるが、トラフィックが少ない場合、パフォーマンスに与える影響は無視して構わない程度です。トラフィックが多い場合、相当なパフォーマンス改善が見込まれます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-327">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="e3be0-328">次のコードでは、キーワード `async`、戻り値 `Task<T>`、キーワード `await`、メソッド `ToListAsync` によりコードの実行が非同期になります。</span><span class="sxs-lookup"><span data-stu-id="e3be0-328">In the following code, the `async` keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="e3be0-329">キーワード `async` は、メソッド本文の一部にコールバックを生成し、返された `Task<IActionResult>` オブジェクトを自動作成するようにコンパイラに伝えます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-329">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>

* <span data-ttu-id="e3be0-330">戻り値の型 `Task<IActionResult>` は、進行中の作業と型 `IActionResult` の結果を表します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-330">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>

* <span data-ttu-id="e3be0-331">キーワード `await` により、コンパイラはメソッドを 2 つに分割します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-331">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="e3be0-332">最初の部分は、非同期で開始される操作で終わります。</span><span class="sxs-lookup"><span data-stu-id="e3be0-332">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="e3be0-333">2 つ目の部分は、操作の完了時に呼び出されるコールバック メソッドに入ります。</span><span class="sxs-lookup"><span data-stu-id="e3be0-333">The second part is put into a callback method that's called when the operation completes.</span></span>

* <span data-ttu-id="e3be0-334">`ToListAsync` は、`ToList` 拡張メソッドの非同期バージョンです。</span><span class="sxs-lookup"><span data-stu-id="e3be0-334">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="e3be0-335">Entity Framework を利用する非同期コードの記述で注意すべき点:</span><span class="sxs-lookup"><span data-stu-id="e3be0-335">Some things to be aware of when you are writing asynchronous code that uses the Entity Framework:</span></span>

* <span data-ttu-id="e3be0-336">クエリやコマンドをデータベースに送信するステートメントのみが非同期で実行されます。</span><span class="sxs-lookup"><span data-stu-id="e3be0-336">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="e3be0-337">たとえば、`ToListAsync`、`SingleOrDefaultAsync`、`SaveChangesAsync` などです。</span><span class="sxs-lookup"><span data-stu-id="e3be0-337">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="e3be0-338">`var students = context.Students.Where(s => s.LastName == "Davolio")` など、`IQueryable` を変更するだけのステートメントは含まれません。</span><span class="sxs-lookup"><span data-stu-id="e3be0-338">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>

* <span data-ttu-id="e3be0-339">EF コンテキストはスレッド セーフではありません。複数の操作を並列実行しないでください。</span><span class="sxs-lookup"><span data-stu-id="e3be0-339">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="e3be0-340">非同期 EF メソッドを呼び出すとき、`await` キーワードを常に使用します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-340">When you call any async EF method, always use the `await` keyword.</span></span>

* <span data-ttu-id="e3be0-341">非同期コードのパフォーマンス上の利点を最大限に活用する場合、(ページングなどのために) ライブラリ パッケージを利用しているのであれば、それがクエリをデータベースに送信させる Entity Framework メソッドを呼び出す場合、非同期を利用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e3be0-341">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="e3be0-342">.NET の非同期プログラミングについては、「[非同期の概要](/dotnet/articles/standard/async)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e3be0-342">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="e3be0-343">コードを取得する</span><span class="sxs-lookup"><span data-stu-id="e3be0-343">Get the code</span></span>

[<span data-ttu-id="e3be0-344">完成したアプリケーションをダウンロードまたは表示する。</span><span class="sxs-lookup"><span data-stu-id="e3be0-344">Download or view the completed application.</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="e3be0-345">次の手順</span><span class="sxs-lookup"><span data-stu-id="e3be0-345">Next steps</span></span>

<span data-ttu-id="e3be0-346">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="e3be0-346">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e3be0-347">ASP.NET Core MVC Web アプリを作成した</span><span class="sxs-lookup"><span data-stu-id="e3be0-347">Created ASP.NET Core MVC web app</span></span>
> * <span data-ttu-id="e3be0-348">サイトのスタイルを設定する</span><span class="sxs-lookup"><span data-stu-id="e3be0-348">Set up the site style</span></span>
> * <span data-ttu-id="e3be0-349">EF Core の NuGet パッケージについて学習した</span><span class="sxs-lookup"><span data-stu-id="e3be0-349">Learned about EF Core NuGet packages</span></span>
> * <span data-ttu-id="e3be0-350">データ モデルを作成した</span><span class="sxs-lookup"><span data-stu-id="e3be0-350">Created the data model</span></span>
> * <span data-ttu-id="e3be0-351">データベース コンテキストを作成した</span><span class="sxs-lookup"><span data-stu-id="e3be0-351">Created the database context</span></span>
> * <span data-ttu-id="e3be0-352">SchoolContext を登録した</span><span class="sxs-lookup"><span data-stu-id="e3be0-352">Registered the SchoolContext</span></span>
> * <span data-ttu-id="e3be0-353">テスト データで DB を初期化した</span><span class="sxs-lookup"><span data-stu-id="e3be0-353">Initialized DB with test data</span></span>
> * <span data-ttu-id="e3be0-354">コントローラーとビューを作成した</span><span class="sxs-lookup"><span data-stu-id="e3be0-354">Created controller and views</span></span>
> * <span data-ttu-id="e3be0-355">データベースを表示した</span><span class="sxs-lookup"><span data-stu-id="e3be0-355">Viewed the database</span></span>

<span data-ttu-id="e3be0-356">次のチュートリアルでは、基本的な CRUD (作成、読み取り、更新、削除) 操作を実行する方法について学習します。</span><span class="sxs-lookup"><span data-stu-id="e3be0-356">In the following tutorial, you'll learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

<span data-ttu-id="e3be0-357">基本的な CRUD (作成、読み取り、更新、削除) 操作の実行方法を学習するには、次の記事に進んでください。</span><span class="sxs-lookup"><span data-stu-id="e3be0-357">Advance to the next article to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="e3be0-358">基本 CRUD 機能を実装する</span><span class="sxs-lookup"><span data-stu-id="e3be0-358">Implement basic CRUD functionality</span></span>](crud.md)
