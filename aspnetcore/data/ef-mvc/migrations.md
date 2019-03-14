---
title: 'チュートリアル: 移行機能の使用 - ASP.NET MVC と EF Core'
description: このチュートリアルでは、ASP.NET Core MVC アプリケーションでデータ モデルの変更を管理するための EF Core の移行機能の使用を開始します。
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/04/2019
ms.topic: tutorial
uid: data/ef-mvc/migrations
ms.openlocfilehash: ac924e7d6bee2f02ab11281a5c27f2c94a7183b3
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57026909"
---
# <a name="tutorial-using-the-migrations-feature---aspnet-mvc-with-ef-core"></a><span data-ttu-id="e47dc-103">チュートリアル: 移行機能の使用 - ASP.NET MVC と EF Core</span><span class="sxs-lookup"><span data-stu-id="e47dc-103">Tutorial: Using the migrations feature - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="e47dc-104">このチュートリアルでは、データ モデルの変更を管理するための EF Core の移行機能の使用を開始します。</span><span class="sxs-lookup"><span data-stu-id="e47dc-104">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="e47dc-105">チュートリアルの後半では、データ モデルを変更するときに移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="e47dc-105">In later tutorials, you'll add more migrations as you change the data model.</span></span>

<span data-ttu-id="e47dc-106">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="e47dc-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e47dc-107">移行について学習する</span><span class="sxs-lookup"><span data-stu-id="e47dc-107">Learn about migrations</span></span>
> * <span data-ttu-id="e47dc-108">NuGet 移行パッケージについて学習する</span><span class="sxs-lookup"><span data-stu-id="e47dc-108">Learn about NuGet migration packages</span></span>
> * <span data-ttu-id="e47dc-109">接続文字列を変更する</span><span class="sxs-lookup"><span data-stu-id="e47dc-109">Change the connection string</span></span>
> * <span data-ttu-id="e47dc-110">初期移行を作成する</span><span class="sxs-lookup"><span data-stu-id="e47dc-110">Create an initial migration</span></span>
> * <span data-ttu-id="e47dc-111">Up および Down メソッドを確認する</span><span class="sxs-lookup"><span data-stu-id="e47dc-111">Examine Up and Down methods</span></span>
> * <span data-ttu-id="e47dc-112">データ モデルのスナップショットについて学習する</span><span class="sxs-lookup"><span data-stu-id="e47dc-112">Learn about the data model snapshot</span></span>
> * <span data-ttu-id="e47dc-113">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="e47dc-113">Apply the migration</span></span>


## <a name="prerequisites"></a><span data-ttu-id="e47dc-114">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="e47dc-114">Prerequisites</span></span>

* [<span data-ttu-id="e47dc-115">EF Core を使った並べ替え、フィルター処理、ページングを ASP.NET Core MVC に追加する</span><span class="sxs-lookup"><span data-stu-id="e47dc-115">Add sorting, filtering, and paging with EF Core in an ASP.NET Core MVC app</span></span>](sort-filter-page.md)

## <a name="about-migrations"></a><span data-ttu-id="e47dc-116">移行について</span><span class="sxs-lookup"><span data-stu-id="e47dc-116">About migrations</span></span>

<span data-ttu-id="e47dc-117">新しいアプリケーションを開発して、データ モデルが頻繁に変更される場合、モデルが変更されるたびに、モデルはデータベースと同期されなくなります。</span><span class="sxs-lookup"><span data-stu-id="e47dc-117">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="e47dc-118">データベースが存在しない場合にデータベースを作成するように Entity Framework を構成して、これらのチュートリアルを開始しました。</span><span class="sxs-lookup"><span data-stu-id="e47dc-118">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="e47dc-119">そのため、データ モデルを変更する (エンティティ クラスを追加、削除、または変更するか、DbContext クラスを変更する) たびに、データベースを削除することができ、EF でモデルに一致する新しいデータベースを作成し、テスト データを使ってシードを設定します。</span><span class="sxs-lookup"><span data-stu-id="e47dc-119">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="e47dc-120">このメソッドは、実稼働環境にアプリケーションを展開するまで、データベースとデータ モデルの同期の維持がうまく機能します。</span><span class="sxs-lookup"><span data-stu-id="e47dc-120">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="e47dc-121">実稼働環境でアプリケーションを実行している場合、通常、保持する必要があるデータが保存され、新しい列の追加などの変更を加えるたびにすべてが失われないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e47dc-121">When the application is running in production it's usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="e47dc-122">EF Core の移行機能は、新しいデータベースを作成する代わりに、EF でデータベース スキーマを更新できるようにすることで、この問題を解決します。</span><span class="sxs-lookup"><span data-stu-id="e47dc-122">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

## <a name="about-nuget-migration-packages"></a><span data-ttu-id="e47dc-123">NuGet 移行パッケージについて</span><span class="sxs-lookup"><span data-stu-id="e47dc-123">About NuGet migration packages</span></span>

<span data-ttu-id="e47dc-124">移行を使用するには、**パッケージ マネージャー コンソール** (PMC) またはコマンド ライン インターフェイス (CLI) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="e47dc-124">To work with migrations, you can use the **Package Manager Console** (PMC) or the command-line interface (CLI).</span></span>  <span data-ttu-id="e47dc-125">このチュートリアルでは、CLI コマンドを使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="e47dc-125">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="e47dc-126">PMC については、[このチュートリアルの最後](#pmc)に説明します。</span><span class="sxs-lookup"><span data-stu-id="e47dc-126">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

<span data-ttu-id="e47dc-127">コマンド ライン インターフェイス (CLI) の EF ツールが [Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet) で提供されています。</span><span class="sxs-lookup"><span data-stu-id="e47dc-127">The EF tools for the command-line interface (CLI) are provided in [Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet).</span></span> <span data-ttu-id="e47dc-128">このパッケージをインストールするには、以下のように、*.csproj* ファイルの `DotNetCliToolReference` コレクションにパッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="e47dc-128">To install this package, add it to the `DotNetCliToolReference` collection in the *.csproj* file, as shown.</span></span> <span data-ttu-id="e47dc-129">\**注:\*\*\*.csproj* ファイルを編集して、このパッケージをインストールする必要があります。`install-package` コマンドやパッケージ マネージャーの GUI を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="e47dc-129">**Note:** You have to install this package by editing the *.csproj* file; you can't use the `install-package` command or the package manager GUI.</span></span> <span data-ttu-id="e47dc-130">*.csproj* ファイルを編集するには、**[ソリューション エクスプローラー]** でプロジェクト名を右クリックし、**[ContosoUniversity.csproj の編集]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e47dc-130">You can edit the *.csproj* file by right-clicking the project name in **Solution Explorer** and selecting **Edit ContosoUniversity.csproj**.</span></span>

[!code-xml[](intro/samples/cu/ContosoUniversity.csproj?range=12-15&highlight=2)]

<span data-ttu-id="e47dc-131">(この例のバージョン番号は、チュートリアルが記述された時点で最新でした)。</span><span class="sxs-lookup"><span data-stu-id="e47dc-131">(The version numbers in this example were current when the tutorial was written.)</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="e47dc-132">接続文字列を変更する</span><span class="sxs-lookup"><span data-stu-id="e47dc-132">Change the connection string</span></span>

<span data-ttu-id="e47dc-133">*appsettings.json* ファイルで、接続文字列のデータベース名を ContosoUniversity2 に変更するか、使用しているコンピューターではまだ使用していない別の名前に変更します。</span><span class="sxs-lookup"><span data-stu-id="e47dc-133">In the *appsettings.json* file, change the name of the database in the connection string to ContosoUniversity2 or some other name that you haven't used on the computer you're using.</span></span>

[!code-json[](intro/samples/cu/appsettings2.json?range=1-4)]

<span data-ttu-id="e47dc-134">この変更では、最初の移行で新しいデータベースが作成されるように、プロジェクトを設定します。</span><span class="sxs-lookup"><span data-stu-id="e47dc-134">This change sets up the project so that the first migration will create a new database.</span></span> <span data-ttu-id="e47dc-135">これは移行の作業を開始するために必要ではありませんが、後でこの設定をお勧めする理由がわかります。</span><span class="sxs-lookup"><span data-stu-id="e47dc-135">This isn't required to get started with migrations, but you'll see later why it's a good idea.</span></span>

> [!NOTE]
> <span data-ttu-id="e47dc-136">データベース名を変更する代わりに、データベースを削除することもできます。</span><span class="sxs-lookup"><span data-stu-id="e47dc-136">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="e47dc-137">**SQL Server オブジェクト エクスプローラー** (SSOX) または `database drop` CLI コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="e47dc-137">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
> ```console
> dotnet ef database drop
> ```
> <span data-ttu-id="e47dc-138">次のセクションでは、CLI コマンドを実行する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="e47dc-138">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="e47dc-139">初期移行を作成する</span><span class="sxs-lookup"><span data-stu-id="e47dc-139">Create an initial migration</span></span>

<span data-ttu-id="e47dc-140">変更を保存し、プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="e47dc-140">Save your changes and build the project.</span></span> <span data-ttu-id="e47dc-141">次に、コマンド ウィンドウを開き、プロジェクト フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="e47dc-141">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="e47dc-142">この操作を行う簡単な方法を次に示します。</span><span class="sxs-lookup"><span data-stu-id="e47dc-142">Here's a quick way to do that:</span></span>

* <span data-ttu-id="e47dc-143">**[ソリューション エクスプローラー]** で、プロジェクトを右クリックし、コンテキスト メニューの **[エクスプローラーでフォルダーを開く]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="e47dc-143">In **Solution Explorer**, right-click the project and choose **Open Folder in File Explorer** from the context menu.</span></span>

  ![[エクスプローラーで開く] メニュー項目](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="e47dc-145">アドレス バーに「cmd」と入力して、Enter キーを押します。</span><span class="sxs-lookup"><span data-stu-id="e47dc-145">Enter "cmd" in the address bar and press Enter.</span></span>

  ![コマンド ウィンドウを開く](migrations/_static/open-command-window.png)

<span data-ttu-id="e47dc-147">コマンド ウィンドウで次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="e47dc-147">Enter the following command in the command window:</span></span>

```console
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="e47dc-148">コマンド ウィンドウに次のような出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="e47dc-148">You see output like the following in the command window:</span></span>

```console
info: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[0]
      User profile is available. Using 'C:\Users\username\AppData\Local\ASP.NET\DataProtection-Keys' as key repository and Windows DPAPI to encrypt keys at rest.
info: Microsoft.EntityFrameworkCore.Infrastructure[100403]
      Entity Framework Core 2.0.0-rtm-26452 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

> [!NOTE]
> <span data-ttu-id="e47dc-149">"*"dotnet-ef" コマンドに一致する実行可能ファイルが見つかりませんでした*" というエラー メッセージが表示された場合、トラブルシューティングのヘルプについては、[このブログ記事](http://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e47dc-149">If you see an error message *No executable found matching command "dotnet-ef"*, see [this blog post](http://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) for help troubleshooting.</span></span>

<span data-ttu-id="e47dc-150">"*別のプロセスによって使用されているため、ContosoUniversity.dll ファイルにアクセスできません。*" というエラー メッセージが表示された場合は、Windows のシステム トレイの IIS Express アイコンを見つけて右クリックし、**[ContosoUniversity]、[サイトの停止]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="e47dc-150">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-up-and-down-methods"></a><span data-ttu-id="e47dc-151">Up および Down メソッドを確認する</span><span class="sxs-lookup"><span data-stu-id="e47dc-151">Examine Up and Down methods</span></span>

<span data-ttu-id="e47dc-152">`migrations add` コマンドを実行したときに、EF では最初からデータベースを作成するコードが生成されています。</span><span class="sxs-lookup"><span data-stu-id="e47dc-152">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="e47dc-153">このコードは、*\<タイムスタンプ>_InitialCreate.cs* という名前のファイルにある *[Migrations]* フォルダー内にあります。</span><span class="sxs-lookup"><span data-stu-id="e47dc-153">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="e47dc-154">`InitialCreate` クラスの `Up` メソッドでは、データ モデルのエンティティ セットに対応するデータベース テーブルを作成し、`Down` メソッドでは、次の例に示すようにそれらを削除します。</span><span class="sxs-lookup"><span data-stu-id="e47dc-154">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="e47dc-155">移行は、`Up` メソッドを呼び出して、移行のためのデータ モデルの変更を実装します。</span><span class="sxs-lookup"><span data-stu-id="e47dc-155">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="e47dc-156">更新をロールバックするためのコマンドを入力すると、移行が `Down` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e47dc-156">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="e47dc-157">このコードは、`migrations add InitialCreate` コマンドを入力したときに作成された初期移行向けのものです。</span><span class="sxs-lookup"><span data-stu-id="e47dc-157">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="e47dc-158">移行の name パラメーター (この例では "InitialCreate") は、ファイル名に使用され、どのような名前でも付けることができます。</span><span class="sxs-lookup"><span data-stu-id="e47dc-158">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="e47dc-159">移行で行われている処理をまとめた単語または語句を選択することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e47dc-159">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="e47dc-160">たとえば、後で移行に "AddDepartmentTable" という名前を付ける可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e47dc-160">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="e47dc-161">データベースが既に存在するときに、初期移行を作成した場合、データベースの作成コードが生成されますが、データベースは既にデータと一致しているため、作成コードを実行する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e47dc-161">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="e47dc-162">データベースがまだ存在しない別の環境にアプリを展開する場合、データベースを作成するために、このコードが実行されるため、最初にテストを行うことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e47dc-162">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="e47dc-163">これが、移行で新しいデータベースを最初から作成できるように、前の接続文字列でデータベースの名前を変更した理由です。</span><span class="sxs-lookup"><span data-stu-id="e47dc-163">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="e47dc-164">データ モデルのスナップショット</span><span class="sxs-lookup"><span data-stu-id="e47dc-164">The data model snapshot</span></span>

<span data-ttu-id="e47dc-165">移行は、現在のデータベース スキーマの*スナップショット*を *Migrations/SchoolContextModelSnapshot.cs* 内に作成します。</span><span class="sxs-lookup"><span data-stu-id="e47dc-165">Migrations creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="e47dc-166">移行を追加するときに、EF は、スナップショット ファイルとデータ モデルを比較することによって変更内容を判断します。</span><span class="sxs-lookup"><span data-stu-id="e47dc-166">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="e47dc-167">移行を削除するには、[dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="e47dc-167">When deleting a migration, use the [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command.</span></span> <span data-ttu-id="e47dc-168">`dotnet ef migrations remove` によって移行が削除され、スナップショットが正しくリセットされたことが確認されます。</span><span class="sxs-lookup"><span data-stu-id="e47dc-168">`dotnet ef migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span>

<span data-ttu-id="e47dc-169">スナップショット ファイルの使用方法の詳細については、[チーム環境での EF Core 移行](/ef/core/managing-schemas/migrations/teams)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e47dc-169">See [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) for more information about how the snapshot file is used.</span></span>

## <a name="apply-the-migration"></a><span data-ttu-id="e47dc-170">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="e47dc-170">Apply the migration</span></span>

<span data-ttu-id="e47dc-171">コマンド ウィンドウで、次のコマンドを入力してデータベースとデータベース内のテーブルを作成します。</span><span class="sxs-lookup"><span data-stu-id="e47dc-171">In the command window, enter the following command to create the database and tables in it.</span></span>

```console
dotnet ef database update
```

<span data-ttu-id="e47dc-172">コマンドからの出力は、データベースを設定する SQL コマンドのログを表示する以外は、`migrations add` コマンドと同様です。</span><span class="sxs-lookup"><span data-stu-id="e47dc-172">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="e47dc-173">次のサンプル出力では、ログのほとんどは省略されています。</span><span class="sxs-lookup"><span data-stu-id="e47dc-173">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="e47dc-174">ログ メッセージの詳細レベルを表示しない場合は、*appsettings.Development.json* ファイルでログ レベルを変更できます。</span><span class="sxs-lookup"><span data-stu-id="e47dc-174">If you prefer not to see this level of detail in log messages, you can change the log level in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="e47dc-175">詳細については、「 <xref:fundamentals/logging/index> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e47dc-175">For more information, see <xref:fundamentals/logging/index>.</span></span>

```text
info: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[0]
      User profile is available. Using 'C:\Users\username\AppData\Local\ASP.NET\DataProtection-Keys' as key repository and Windows DPAPI to encrypt keys at rest.
info: Microsoft.EntityFrameworkCore.Infrastructure[100403]
      Entity Framework Core 2.0.0-rtm-26452 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (467ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (20ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20170816151242_InitialCreate', N'2.0.0-rtm-26452');
Done.
```

<span data-ttu-id="e47dc-176">**SQL Server オブジェクト エクスプローラー**を使用して、最初のチュートリアルで行ったように、データベースを調べます。</span><span class="sxs-lookup"><span data-stu-id="e47dc-176">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="e47dc-177">データベースに適用されている移行を記録する \_\_EFMigrationsHistory テーブルに追加があることがわかります。</span><span class="sxs-lookup"><span data-stu-id="e47dc-177">You'll notice the addition of an \_\_EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="e47dc-178">テーブルのデータを表示すると、最初の移行の 1 行が表示されます。</span><span class="sxs-lookup"><span data-stu-id="e47dc-178">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="e47dc-179">(前の CLI の出力例の最後のログは、この行を作成する INSERT ステートメントを示しています)。</span><span class="sxs-lookup"><span data-stu-id="e47dc-179">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="e47dc-180">アプリケーションを実行して、すべてが以前と同じように動作することを確認します。</span><span class="sxs-lookup"><span data-stu-id="e47dc-180">Run the application to verify that everything still works the same as before.</span></span>

![Students インデックス ページ](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a><span data-ttu-id="e47dc-182">CLI と PMC を比較する</span><span class="sxs-lookup"><span data-stu-id="e47dc-182">Compare CLI and PMC</span></span>

<span data-ttu-id="e47dc-183">移行を管理するための EF ツールは、.NET Core CLI コマンドから、または Visual Studio **パッケージ マネージャー コンソール** (PMC) ウィンドウの PowerShell コマンドレットから利用できます。</span><span class="sxs-lookup"><span data-stu-id="e47dc-183">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="e47dc-184">このチュートリアルでは、CLI の使用方法を示しますが、好みに応じて PMC を使用できます。</span><span class="sxs-lookup"><span data-stu-id="e47dc-184">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="e47dc-185">PMC コマンドの EF コマンドは、[Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) パッケージ内にあります。</span><span class="sxs-lookup"><span data-stu-id="e47dc-185">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="e47dc-186">このパッケージは [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれているので、アプリに `Microsoft.AspNetCore.App` のパッケージ参照がある場合、パッケージ参照を追加する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e47dc-186">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to add a package reference if your app has a package reference for `Microsoft.AspNetCore.App`.</span></span>

<span data-ttu-id="e47dc-187">**重要:** これは、*.csproj* ファイルを編集して CLI 用にインストールするものと同じパッケージではありません。</span><span class="sxs-lookup"><span data-stu-id="e47dc-187">**Important:** This isn't the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="e47dc-188">`Tools.DotNet` で終わる CLI パッケージ名とは異なり、このパッケージの名前は `Tools` で終わります。</span><span class="sxs-lookup"><span data-stu-id="e47dc-188">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="e47dc-189">CLI コマンドの詳細については、「[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e47dc-189">For more information about the CLI commands, see [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="e47dc-190">PMC コマンドの詳細については、「[パッケージ マネージャー コンソール (Visual Studio)](/ef/core/miscellaneous/cli/powershell)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e47dc-190">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="e47dc-191">コードを取得する</span><span class="sxs-lookup"><span data-stu-id="e47dc-191">Get the code</span></span>

[<span data-ttu-id="e47dc-192">完成したアプリケーションをダウンロードまたは表示する。</span><span class="sxs-lookup"><span data-stu-id="e47dc-192">Download or view the completed application.</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-step"></a><span data-ttu-id="e47dc-193">次の手順</span><span class="sxs-lookup"><span data-stu-id="e47dc-193">Next step</span></span>

<span data-ttu-id="e47dc-194">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="e47dc-194">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e47dc-195">移行について学習した</span><span class="sxs-lookup"><span data-stu-id="e47dc-195">Learned about migrations</span></span>
> * <span data-ttu-id="e47dc-196">NuGet 移行パッケージについて学習した</span><span class="sxs-lookup"><span data-stu-id="e47dc-196">Learned about NuGet migration packages</span></span>
> * <span data-ttu-id="e47dc-197">接続文字列を変更した</span><span class="sxs-lookup"><span data-stu-id="e47dc-197">Changed the connection string</span></span>
> * <span data-ttu-id="e47dc-198">初期移行を作成した</span><span class="sxs-lookup"><span data-stu-id="e47dc-198">Created an initial migration</span></span>
> * <span data-ttu-id="e47dc-199">Up および Down メソッドを確認した</span><span class="sxs-lookup"><span data-stu-id="e47dc-199">Examined Up and Down methods</span></span>
> * <span data-ttu-id="e47dc-200">データ モデルのスナップショットについて学習した</span><span class="sxs-lookup"><span data-stu-id="e47dc-200">Learned about the data model snapshot</span></span>
> * <span data-ttu-id="e47dc-201">移行を適用した</span><span class="sxs-lookup"><span data-stu-id="e47dc-201">Applied the migration</span></span>

<span data-ttu-id="e47dc-202">データ モデルの展開に関するより高度なトピックを確認するには、次の記事に進んでください。</span><span class="sxs-lookup"><span data-stu-id="e47dc-202">Advance to the next article to begin looking at more advanced topics about expanding the data model.</span></span> <span data-ttu-id="e47dc-203">その途中で、追加の移行を作成して適用することになります。</span><span class="sxs-lookup"><span data-stu-id="e47dc-203">Along the way you'll create and apply additional migrations.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="e47dc-204">追加の移行を作成して適用する</span><span class="sxs-lookup"><span data-stu-id="e47dc-204">Create and apply additional migrations</span></span>](complex-data-model.md)
