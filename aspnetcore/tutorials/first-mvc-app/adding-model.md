---
title: ASP.NET Core MVC アプリへのモデルの追加
author: rick-anderson
description: 単純な ASP.NET Core アプリケーションにモデルを追加します。
ms.author: riande
ms.date: 02/25/2019
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: ccdb7b920517c94b9154fe73b4ef1633f4ad0157
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57054359"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="b356e-103">ASP.NET Core MVC アプリへのモデルの追加</span><span class="sxs-lookup"><span data-stu-id="b356e-103">Add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="b356e-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="b356e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="b356e-105">このセクションでは、データベースのムービーを管理するクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="b356e-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="b356e-106">これらのクラスは、**M**VC アプリの "**モ**デル" 部分です。</span><span class="sxs-lookup"><span data-stu-id="b356e-106">These classes will be the "**M**odel" part of the **M**VC app.</span></span>

<span data-ttu-id="b356e-107">[Entity Framework Core](/ef/core) (EF Core) でこれらのクラスを使用して、データベースを操作します。</span><span class="sxs-lookup"><span data-stu-id="b356e-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="b356e-108">EF Core は、記述する必要があるデータ アクセス コードを簡略化するオブジェクト リレーショナル マッピング (ORM) フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="b356e-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="b356e-109">作成するモデル クラスは、EF Core に対する依存関係がないために、POCO クラス (**P**lain-**O**ld **C**LR **O**bjects から) と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="b356e-109">The model classes you create are known as POCO classes (from **P**lain **O**ld **C**LR **O**bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="b356e-110">これらは単に、データベースに格納されるデータのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="b356e-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="b356e-111">このチュートリアルでは、まずモデル クラスを記述し、EF コアによってデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b356e-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span> <span data-ttu-id="b356e-112">ここで取り上げていない別の方法は、既存のデータベースからモデル クラスを生成する方法です。</span><span class="sxs-lookup"><span data-stu-id="b356e-112">An alternate approach not covered here is to generate model classes from an existing database.</span></span> <span data-ttu-id="b356e-113">その方法については、[ASP.NET Core の既存のデータベース](/ef/core/get-started/aspnetcore/existing-db)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b356e-113">For information about that approach, see [ASP.NET Core - Existing Database](/ef/core/get-started/aspnetcore/existing-db).</span></span>

## <a name="add-a-data-model-class"></a><span data-ttu-id="b356e-114">データ モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="b356e-114">Add a data model class</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b356e-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b356e-115">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b356e-116">*Models* フォルダーを右クリックし、**[追加]** > **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b356e-116">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="b356e-117">クラスに **Movie** と名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="b356e-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

<!-- Code -------------------------->
# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="b356e-118">Visual Studio Code/Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b356e-118">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b356e-119">*Movie.cs* という名前の *Models* フォルダーにクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="b356e-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---  
<!-- End of VS tabs -->

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="b356e-120">ムービー モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="b356e-120">Scaffold the movie model</span></span>

<span data-ttu-id="b356e-121">このセクションでは、ムービー モデルがスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="b356e-121">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="b356e-122">つまり、スキャフォールディング ツールにより、ムービー モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="b356e-122">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b356e-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b356e-123">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b356e-124">*ソリューション エクスプローラー*で、**Controllers** フォルダーを右クリックし、**[追加]、[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b356e-124">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![前述の手順を参照](adding-model/_static/add_controller21.png)

<span data-ttu-id="b356e-126">**[スキャフォールディングを追加]** ダイアログで、**[Entity Framework を使用したビューがある MVC コントローラー]、[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b356e-126">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![[スキャフォールディングを追加] ダイアログ](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="b356e-128">**[コントローラーの追加]** ダイアログ ボックスを完了します。</span><span class="sxs-lookup"><span data-stu-id="b356e-128">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="b356e-129">**モデル クラス:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="b356e-129">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="b356e-130">**データ コンテキスト クラス:** **+** アイコンを選択して、既定の **MvcMovie.Models.MvcMovieContext** を追加します。</span><span class="sxs-lookup"><span data-stu-id="b356e-130">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![[データの追加] コンテキスト](adding-model/_static/dc.png)

* <span data-ttu-id="b356e-132">**ビュー:** 各オプションの既定値をオンにします。</span><span class="sxs-lookup"><span data-stu-id="b356e-132">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="b356e-133">**コントローラー名:** 既定の *MoviesController* のままにします。</span><span class="sxs-lookup"><span data-stu-id="b356e-133">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="b356e-134">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b356e-134">Select **Add**</span></span>

![[コントローラーの追加] ダイアログ](adding-model/_static/add_controller2.png)

<span data-ttu-id="b356e-136">Visual Studio では、次が作成されます。</span><span class="sxs-lookup"><span data-stu-id="b356e-136">Visual Studio creates:</span></span>

* <span data-ttu-id="b356e-137">Entity Framework Core の[データベース コンテキスト クラス](xref:data/ef-mvc/intro#create-the-database-context)(*Data/MvcMovieContext.cs*)</span><span class="sxs-lookup"><span data-stu-id="b356e-137">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="b356e-138">ムービー コントローラー (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="b356e-138">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="b356e-139">作成、削除、詳細、編集、およびインデックス ページ用の Razor ビュー ファイル (<em>Views/Movies/&ast;.cshtml</em>)</span><span class="sxs-lookup"><span data-stu-id="b356e-139">Razor view files for Create, Delete, Details, Edit, and Index pages (<em>Views/Movies/&ast;.cshtml</em>)</span></span>

<span data-ttu-id="b356e-140">データベース コンテキストと [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (作成、読み取り、更新、および削除) アクション メソッドとビューの自動作成は、*スキャフォールディング*と言います。</span><span class="sxs-lookup"><span data-stu-id="b356e-140">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b356e-141">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b356e-141">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="b356e-142">プロジェクト ディレクトリ (*Program.cs*、*Startup.cs*、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="b356e-142">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="b356e-143">スキャフォールディング ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="b356e-143">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="b356e-144">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b356e-144">Run the following command:</span></span>

  ```console
     dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b356e-145">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b356e-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b356e-146">プロジェクト ディレクトリ (*Program.cs*、*Startup.cs*、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="b356e-146">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="b356e-147">スキャフォールディング ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="b356e-147">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="b356e-148">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b356e-148">Run the following command:</span></span>

  ```console
     dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="b356e-149">アプリを実行し、**[MVC Movie]\(MVC ムービー\)** リンクをクリックすると、次のようなエラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b356e-149">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

``` error
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

<span data-ttu-id="b356e-150">データベースを作成する必要があり、それには EF Core [移行](xref:data/ef-mvc/migrations)機能を使用します。</span><span class="sxs-lookup"><span data-stu-id="b356e-150">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="b356e-151">移行では、データ モデルに一致するデータベースを作成し、データ モデルの変更時にデータベース スキーマを更新することができます。</span><span class="sxs-lookup"><span data-stu-id="b356e-151">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="b356e-152">最初の移行</span><span class="sxs-lookup"><span data-stu-id="b356e-152">Initial migration</span></span>

<span data-ttu-id="b356e-153">このセクションでは、次のタスクを完了しました。</span><span class="sxs-lookup"><span data-stu-id="b356e-153">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="b356e-154">初期移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="b356e-154">Add an initial migration.</span></span>
* <span data-ttu-id="b356e-155">初期移行でデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="b356e-155">Update the database with the initial migration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b356e-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b356e-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b356e-157">**[ツール]** メニューで、**[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** (PMC) の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b356e-157">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC メニュー](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="b356e-159">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="b356e-159">In the PMC, enter the following commands:</span></span>

   ```console
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="b356e-160">`Add-Migration` コマンドによって最初のデータベース スキーマを作成するコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="b356e-160">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="b356e-161">データベース スキーマは、`MvcMovieContext` クラスで指定されたモデルに基づきます (*Data/MvcMovieContext.cs* ファイル内)。</span><span class="sxs-lookup"><span data-stu-id="b356e-161">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="b356e-162">`Initial` 引数は、移行の名前です。</span><span class="sxs-lookup"><span data-stu-id="b356e-162">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="b356e-163">任意の名前を使用できますが、慣例により、移行を説明する名前が使用されます。</span><span class="sxs-lookup"><span data-stu-id="b356e-163">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="b356e-164">詳細については、「 <xref:data/ef-mvc/migrations> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b356e-164">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="b356e-165">`Update-Database` コマンドは、データベースを作成する、*Migrations/{time-stamp}_InitialCreate.cs* ファイルの `Up` メソッドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b356e-165">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="b356e-166">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b356e-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="b356e-167">`ef migrations add InitialCreate` コマンドによって最初のデータベース スキーマを作成するコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="b356e-167">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="b356e-168">データベース スキーマは、`MvcMovieContext` クラスで指定されたモデルに基づきます (*Data/MvcMovieContext.cs* ファイル内)。</span><span class="sxs-lookup"><span data-stu-id="b356e-168">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="b356e-169">`InitialCreate` 引数は、移行の名前です。</span><span class="sxs-lookup"><span data-stu-id="b356e-169">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="b356e-170">任意の名前を使用できますが、慣例により、移行を説明する名前が選択されます。</span><span class="sxs-lookup"><span data-stu-id="b356e-170">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="b356e-171">依存関係挿入に登録されるコンテキストを調べる</span><span class="sxs-lookup"><span data-stu-id="b356e-171">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="b356e-172">ASP.NET Core には、[依存関係挿入 (DI)](xref:fundamentals/dependency-injection) が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="b356e-172">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b356e-173">サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に DI に登録されます。</span><span class="sxs-lookup"><span data-stu-id="b356e-173">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="b356e-174">これらのサービスを必要とするコンポーネント (Razor ページなど) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="b356e-174">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="b356e-175">DB コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。</span><span class="sxs-lookup"><span data-stu-id="b356e-175">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b356e-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b356e-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b356e-177">スキャフォールディング ツールが自動的に DB コンテキストを作成し、それを DI コンテナーに登録します。</span><span class="sxs-lookup"><span data-stu-id="b356e-177">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="b356e-178">次の `Startup.ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="b356e-178">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="b356e-179">強調表示された行は、スキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="b356e-179">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="b356e-180">`MvcMovieContext` では、`Movie` モデルのために EF Core 機能 (作成、読み取り、更新、削除など) が調整されます。</span><span class="sxs-lookup"><span data-stu-id="b356e-180">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="b356e-181">データ コンテキスト (`MvcMovieContext`) は [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生されます。</span><span class="sxs-lookup"><span data-stu-id="b356e-181">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="b356e-182">データ コンテキストによって、データ モデルに含めるエンティティが指定されます: </span><span class="sxs-lookup"><span data-stu-id="b356e-182">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

<span data-ttu-id="b356e-183">上記のコードによって、エンティティ セットの [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b356e-183">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="b356e-184">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="b356e-184">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="b356e-185">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="b356e-185">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="b356e-186">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="b356e-186">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="b356e-187">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)が *appsettings.json* ファイルから接続文字列を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="b356e-187">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="b356e-188">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b356e-188">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="b356e-189">DB コンテキストを作成し、それを DI コンテナーに登録しました。</span><span class="sxs-lookup"><span data-stu-id="b356e-189">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="b356e-190">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="b356e-190">Test the app</span></span>

* <span data-ttu-id="b356e-191">アプリを実行し、ブラウザーで URL に `/Movies` を追加します ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="b356e-191">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="b356e-192">次のようなデータベースの例外が表示された場合: </span><span class="sxs-lookup"><span data-stu-id="b356e-192">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="b356e-193">[移行手順](#pmc)を失敗しました。</span><span class="sxs-lookup"><span data-stu-id="b356e-193">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="b356e-194">**[作成]** リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="b356e-194">Test the **Create** link.</span></span>

  > [!NOTE]
  > <span data-ttu-id="b356e-195">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="b356e-195">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="b356e-196">小数点にコンマ (",") を使う英語以外のロケール、および英語 (米国) 以外の日付形式で、[jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b356e-196">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="b356e-197">グローバル化の手順については、[この GitHub の記事](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b356e-197">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="b356e-198">**[編集]**、**[詳細]**、および **[削除]** の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="b356e-198">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="b356e-199">次の `Startup` クラスを調べます。</span><span class="sxs-lookup"><span data-stu-id="b356e-199">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="b356e-200">上のコードで強調表示されている部分は、[依存性の注入](xref:fundamentals/dependency-injection)コンテナーに追加されているムービー データベース コンテキストを示します。</span><span class="sxs-lookup"><span data-stu-id="b356e-200">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="b356e-201">`services.AddDbContext<MvcMovieContext>(options =>` では、使用するデータベースと接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="b356e-201">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="b356e-202">`=>` は[ラムダ演算子](/dotnet/articles/csharp/language-reference/operators/lambda-operator)です。</span><span class="sxs-lookup"><span data-stu-id="b356e-202">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="b356e-203">*Controllers/MoviesController.cs* ファイルを開いて、コンストラクターを調べます。</span><span class="sxs-lookup"><span data-stu-id="b356e-203">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="b356e-204">コンストラクターでは、[依存性の注入](xref:fundamentals/dependency-injection)を使ってデータベース コンテキスト (`MvcMovieContext `) がコントローラーに挿入されています。</span><span class="sxs-lookup"><span data-stu-id="b356e-204">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext `) into the controller.</span></span> <span data-ttu-id="b356e-205">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="b356e-205">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="b356e-206">厳密に型指定されたモデルと @model キーワード</span><span class="sxs-lookup"><span data-stu-id="b356e-206">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="b356e-207">コントローラーで `ViewData` ディクショナリを使ってビューにデータまたはオブジェクトを渡す方法を前に示しました。</span><span class="sxs-lookup"><span data-stu-id="b356e-207">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="b356e-208">`ViewData` ディクショナリは動的オブジェクトであり、ビューに情報を渡すための便利な遅延バインディングの方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="b356e-208">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="b356e-209">MVC にも、厳密に型指定されたモデル オブジェクトをビューに渡す機能があります。</span><span class="sxs-lookup"><span data-stu-id="b356e-209">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="b356e-210">この厳密に型指定された方法を使うと、コンパイル時のコードのチェックが向上します。</span><span class="sxs-lookup"><span data-stu-id="b356e-210">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="b356e-211">スキャフォールディング メカニズムは、メソッドとビューを作成するときに、`MoviesController` クラスとビューでこの方法 (つまり、厳密に型指定されたモデルを渡すこと) を使いました。</span><span class="sxs-lookup"><span data-stu-id="b356e-211">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="b356e-212">*Controllers/MoviesController.cs* ファイルで生成された `Details` メソッドを調べてください。</span><span class="sxs-lookup"><span data-stu-id="b356e-212">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="b356e-213">通常、`id` パラメーターはルート データとして渡されます。</span><span class="sxs-lookup"><span data-stu-id="b356e-213">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="b356e-214">たとえば、`https://localhost:5001/movies/details/1` は次のように設定します。</span><span class="sxs-lookup"><span data-stu-id="b356e-214">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="b356e-215">コントローラーを `movies` コントローラーに (最初の URL セグメント)。</span><span class="sxs-lookup"><span data-stu-id="b356e-215">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="b356e-216">アクションを `details` に (2 番目の URL セグメント)。</span><span class="sxs-lookup"><span data-stu-id="b356e-216">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="b356e-217">ID を 1 に (最後の URL セグメント)。</span><span class="sxs-lookup"><span data-stu-id="b356e-217">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="b356e-218">次のようにクエリ文字列で `id` を渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="b356e-218">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="b356e-219">ID 値が指定されない場合、`id` パラメーターは [null 許容型](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) として定義されます。</span><span class="sxs-lookup"><span data-stu-id="b356e-219">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="b356e-220">ルート データまたはクエリ文字列の値と一致するムービー エンティティを選択するため、[ラムダ式](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)が `FirstOrDefaultAsync` に渡されます。</span><span class="sxs-lookup"><span data-stu-id="b356e-220">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="b356e-221">ムービーが見つかった場合、`Movie` モデルのインスタンスが `Details` ビューに渡されます。</span><span class="sxs-lookup"><span data-stu-id="b356e-221">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="b356e-222">*Views/Movies/Details.cshtml* ファイルの内容を確認してください。</span><span class="sxs-lookup"><span data-stu-id="b356e-222">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="b356e-223">ビュー ファイルの先頭に `@model` ステートメントを含めることで、ビューが期待するオブジェクトの型を指定することができます。</span><span class="sxs-lookup"><span data-stu-id="b356e-223">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="b356e-224">ムービー コントローラーを作成したとき、*Details.cshtml* ファイルの先頭に次の `@model` ステートメントが自動的に追加されています。</span><span class="sxs-lookup"><span data-stu-id="b356e-224">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```HTML
@model MvcMovie.Models.Movie
   ```

<span data-ttu-id="b356e-225">この `@model` ディレクティブにより、厳密に型指定された `Model` オブジェクトを使って、コントローラーがビューに渡したムービーにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="b356e-225">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="b356e-226">たとえば、*Details.cshtml* ビューでは、コードで厳密に型指定された `Model` オブジェクトを使って、`DisplayNameFor` および `DisplayFor` HTML ヘルパーに各ムービー フィールドを渡しています。</span><span class="sxs-lookup"><span data-stu-id="b356e-226">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="b356e-227">`Create` および `Edit` のメソッドとビューも、`Movie` モデル オブジェクトを渡します。</span><span class="sxs-lookup"><span data-stu-id="b356e-227">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="b356e-228">Movies コントローラーの *Index.cshtml* ビューと `Index` メソッドを確認してください。</span><span class="sxs-lookup"><span data-stu-id="b356e-228">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="b356e-229">コードで `View` メソッドを呼び出すときの `List` オブジェクトの作成方法に注意してください。</span><span class="sxs-lookup"><span data-stu-id="b356e-229">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="b356e-230">コードでは、この `Movies` リストを `Index` アクション メソッドからビューに渡しています。</span><span class="sxs-lookup"><span data-stu-id="b356e-230">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="b356e-231">ムービー コントローラーを作成したとき、スキャフォールディングによって *Index.cshtml* ファイルの先頭に `@model` ステートメントが自動的に追加されています。</span><span class="sxs-lookup"><span data-stu-id="b356e-231">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="b356e-232">`@model` ディレクティブにより、厳密に型指定された `Model` オブジェクトを使って、コントローラーがビューに渡したムービーのリストにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="b356e-232">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="b356e-233">たとえば、*Index.cshtml* ビューのコードでは、`foreach` ステートメントを使って厳密に型指定された `Model` オブジェクトのムービーをループ処理しています。</span><span class="sxs-lookup"><span data-stu-id="b356e-233">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="b356e-234">`Model` オブジェクトは厳密に型指定されているので (`IEnumerable<Movie>` オブジェクトとして)、ループ内の各項目は `Movie` として型指定されます。</span><span class="sxs-lookup"><span data-stu-id="b356e-234">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="b356e-235">それ以外の利点としては、コンパイル時にコードのチェックが行われます。</span><span class="sxs-lookup"><span data-stu-id="b356e-235">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b356e-236">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="b356e-236">Additional resources</span></span>

* [<span data-ttu-id="b356e-237">タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="b356e-237">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="b356e-238">グローバライズとローカライズ</span><span class="sxs-lookup"><span data-stu-id="b356e-238">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="b356e-239">[前のチュートリアル ビューの追加](adding-view.md)
> [次のチュートリアル SQL の使用](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="b356e-239">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>  
