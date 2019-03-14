---
uid: web-forms/overview/older-versions-getting-started/deployment-to-a-hosting-provider/deployment-to-a-hosting-provider-deploying-a-database-update-9-of-12
title: SQL Server compact の Visual Studio または Visual Web Developer を使用して ASP.NET Web アプリケーションの展開。12 の 9 - データベース更新の展開 |Microsoft Docs
author: tdykstra
description: このチュートリアル シリーズには、展開する方法を示します (発行) ASP.NET web アプリケーション プロジェクトを Visual Stu を使用して、SQL Server Compact データベースが含まれています.
ms.author: riande
ms.date: 11/17/2011
ms.assetid: a8d776af-4735-4612-87f6-9f326587f2d3
msc.legacyurl: /web-forms/overview/older-versions-getting-started/deployment-to-a-hosting-provider/deployment-to-a-hosting-provider-deploying-a-database-update-9-of-12
msc.type: authoredcontent
ms.openlocfilehash: b15d27a07207110187b897624814125c9e030493
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57041309"
---
<a name="deploying-an-aspnet-web-application-with-sql-server-compact-using-visual-studio-or-visual-web-developer-deploying-a-database-update---9-of-12"></a><span data-ttu-id="d3bc5-103">SQL Server compact の Visual Studio または Visual Web Developer を使用して ASP.NET Web アプリケーションの展開。12 の 9 - データベース更新の展開</span><span class="sxs-lookup"><span data-stu-id="d3bc5-103">Deploying an ASP.NET Web Application with SQL Server Compact using Visual Studio or Visual Web Developer: Deploying a Database Update - 9 of 12</span></span>
====================
<span data-ttu-id="d3bc5-104">によって[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="d3bc5-104">by [Tom Dykstra](https://github.com/tdykstra)</span></span>

[<span data-ttu-id="d3bc5-105">スタート プロジェクトをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-105">Download Starter Project</span></span>](http://code.msdn.microsoft.com/Deploying-an-ASPNET-Web-4e31366b)

> <span data-ttu-id="d3bc5-106">この一連のチュートリアルは、展開する方法を示します (発行) ASP.NET web アプリケーション プロジェクトを Visual Studio 2012 RC または Visual Studio Express 2012 RC を for Web を使用して、SQL Server Compact データベースが含まれています。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-106">This series of tutorials shows you how to deploy (publish) an ASP.NET web application project that includes a SQL Server Compact database by using Visual Studio 2012 RC or Visual Studio Express 2012 RC for Web.</span></span> <span data-ttu-id="d3bc5-107">Web の発行の更新をインストールする場合は、Visual Studio 2010 を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-107">You can also use Visual Studio 2010 if you install the Web Publish Update.</span></span> <span data-ttu-id="d3bc5-108">シリーズの概要については、次を参照してください。[シリーズの最初のチュートリアル](deployment-to-a-hosting-provider-introduction-1-of-12.md)します。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-108">For an introduction to the series, see [the first tutorial in the series](deployment-to-a-hosting-provider-introduction-1-of-12.md).</span></span>
> 
> <span data-ttu-id="d3bc5-109">Visual Studio 2012 RC のリリース後に導入された展開機能を示しています、SQL Server Compact 以外の SQL Server のエディションをデプロイする方法を示しています、および Azure App Service Web Apps にデプロイする方法を示していますチュートリアルでは、次を参照してください。 [ASP.NET Web 配置。Visual Studio を使用して](../../deployment/visual-studio-web-deployment/introduction.md)します。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-109">For a tutorial that shows deployment features introduced after the RC release of Visual Studio 2012, shows how to deploy SQL Server editions other than SQL Server Compact, and shows how to deploy to Azure App Service Web Apps, see [ASP.NET Web Deployment using Visual Studio](../../deployment/visual-studio-web-deployment/introduction.md).</span></span>


## <a name="overview"></a><span data-ttu-id="d3bc5-110">概要</span><span class="sxs-lookup"><span data-stu-id="d3bc5-110">Overview</span></span>

<span data-ttu-id="d3bc5-111">このチュートリアルで行ったデータベースの変更と関連するコードの変更、Visual Studio での変更をテストし、テストと運用環境の両方の環境に更新を展開します。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-111">In this tutorial, you make a database change and related code changes, test the changes in Visual Studio, then deploy the update to both the test and production environments.</span></span>

<span data-ttu-id="d3bc5-112">リマインダー:エラー メッセージを表示するか、チュートリアルを読み進めるしたとおりに機能しない場合に、チェックすることを確認して、[トラブルシューティング ページ](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12.md)します。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-112">Reminder: If you get an error message or something doesn't work as you go through the tutorial, be sure to check the [troubleshooting page](deployment-to-a-hosting-provider-creating-and-installing-deployment-packages-12-of-12.md).</span></span>

## <a name="adding-a-new-column-to-a-table"></a><span data-ttu-id="d3bc5-113">テーブルに新しい列の追加</span><span class="sxs-lookup"><span data-stu-id="d3bc5-113">Adding a New Column to a Table</span></span>

<span data-ttu-id="d3bc5-114">このセクションに生年月日の列を追加、`Person`の基本クラス、`Student`と`Instructor`エンティティ。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-114">In this section, you add a birth date column to the `Person` base class for the `Student` and `Instructor` entities.</span></span> <span data-ttu-id="d3bc5-115">新しい列を表示するように、インストラクター データを表示するページを更新します。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-115">Then you update the page that displays instructor data so that it displays the new column.</span></span>

<span data-ttu-id="d3bc5-116">*ContosoUniversity.DAL*プロジェクトを開き、 *Person.cs*の末尾に次のプロパティを追加し、`Person`クラス (あります終わり波かっこの後に 2 つ)。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-116">In the *ContosoUniversity.DAL* project, open *Person.cs* and add the following property at the end of the `Person` class (there should be two closing curly braces following it):</span></span>

[!code-csharp[Main](deployment-to-a-hosting-provider-deploying-a-database-update-9-of-12/samples/sample1.cs)]

<span data-ttu-id="d3bc5-117">次に、新しい列の値を提供するように、Seed メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-117">Next, update the Seed method so that it provides a value for the new column.</span></span> <span data-ttu-id="d3bc5-118">開いている*migrations \configuration.cs*と置換を開始するコード ブロック`var instructors = new List<Instructor>`誕生日に関する情報を含む次のコード ブロックで。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-118">Open *Migrations\Configuration.cs* and replace the code block that begins `var instructors = new List<Instructor>` with the following code block which includes birth date information:</span></span>

[!code-csharp[Main](deployment-to-a-hosting-provider-deploying-a-database-update-9-of-12/samples/sample2.cs)]

<span data-ttu-id="d3bc5-119">ContosoUniversity プロジェクトで開きます*Instructors.aspx*し、誕生日を表示する新しいテンプレート フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-119">In the ContosoUniversity project, open *Instructors.aspx* and add a new template field to display the birth date.</span></span> <span data-ttu-id="d3bc5-120">雇用日とオフィスの割り当てのためのものの間に追加します。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-120">Add it between the ones for hire date and office assignment:</span></span>

[!code-aspx[Main](deployment-to-a-hosting-provider-deploying-a-database-update-9-of-12/samples/sample3.aspx)]

<span data-ttu-id="d3bc5-121">(コードのインデントとれていない場合は、することができますキーを押して CTRL + K し CTRL-D ファイルを自動的に再フォーマットします。)</span><span class="sxs-lookup"><span data-stu-id="d3bc5-121">(If code indentation gets out of sync, you can press CTRL-K and then CTRL-D to automatically reformat the file.)</span></span>

<span data-ttu-id="d3bc5-122">ソリューションをビルドしを開き、**パッケージ マネージャー コンソール**ウィンドウ。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-122">Build the solution, and then open the **Package Manager Console** window.</span></span> <span data-ttu-id="d3bc5-123">として ContosoUniversity.DAL がまだ選択されていることを確認、**既定のプロジェクト**します。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-123">Make sure that ContosoUniversity.DAL is still selected as the **Default project**.</span></span>

<span data-ttu-id="d3bc5-124">**パッケージ マネージャー コンソール**ウィンドウで、 **ContosoUniversity.DAL**として、**既定のプロジェクト**、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-124">In the **Package Manager Console** window, select **ContosoUniversity.DAL** as the **Default project**, and then enter the following command:</span></span>

[!code-powershell[Main](deployment-to-a-hosting-provider-deploying-a-database-update-9-of-12/samples/sample4.ps1)]

<span data-ttu-id="d3bc5-125">このコマンドが完了したら、Visual Studio は、新しいを定義するクラス ファイルを開きます`DbMIgration`クラス、および、`Up`メソッド、新しい列を作成するコードを表示できます。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-125">When this command finishes, Visual Studio opens the class file that defines the new `DbMIgration` class, and in the `Up` method you can see the code that creates the new column.</span></span>

![AddBirthDate_migration_code](deployment-to-a-hosting-provider-deploying-a-database-update-9-of-12/_static/image1.png)

<span data-ttu-id="d3bc5-127">ソリューションをビルドおよびでは、次のコマンドを入力し、**パッケージ マネージャー コンソール**ウィンドウ (ContosoUniversity.DAL プロジェクトが選択されていることを確認してください)。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-127">Build the solution, and then enter the following command in the **Package Manager Console** window (make sure the ContosoUniversity.DAL project is still selected):</span></span>

[!code-powershell[Main](deployment-to-a-hosting-provider-deploying-a-database-update-9-of-12/samples/sample5.ps1)]

<span data-ttu-id="d3bc5-128">コマンドが完了したら、アプリケーションを実行し、Instructors ページを選択します。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-128">When the command finishes, run the application and select the Instructors page.</span></span> <span data-ttu-id="d3bc5-129">ページが読み込まれると、新しいがあるを参照してください。 生年月日フィールド。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-129">When the page loads, you see that it has the new birth date field.</span></span>

<span data-ttu-id="d3bc5-130">[![Instructors_page_with_birth_date](deployment-to-a-hosting-provider-deploying-a-database-update-9-of-12/_static/image3.png)](deployment-to-a-hosting-provider-deploying-a-database-update-9-of-12/_static/image2.png)</span><span class="sxs-lookup"><span data-stu-id="d3bc5-130">[![Instructors_page_with_birth_date](deployment-to-a-hosting-provider-deploying-a-database-update-9-of-12/_static/image3.png)](deployment-to-a-hosting-provider-deploying-a-database-update-9-of-12/_static/image2.png)</span></span>

## <a name="deploying-the-database-update-to-the-test-environment"></a><span data-ttu-id="d3bc5-131">データベースの更新をテスト環境に展開します。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-131">Deploying the Database Update to the Test Environment</span></span>

<span data-ttu-id="d3bc5-132">**ソリューション エクスプ ローラー** ContosoUniversity プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-132">In **Solution Explorer** select the ContosoUniversity project.</span></span>

<span data-ttu-id="d3bc5-133">**Web の 1 クリックして発行**ツールバーで、**テスト**発行プロファイルをクリックして**Web の発行**します。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-133">In the **Web One Click Publish** toolbar, select the **Test** publish profile, and then click **Publish Web**.</span></span> <span data-ttu-id="d3bc5-134">(ツールバーが無効になっている場合で ContosoUniversity プロジェクトを選択します**ソリューション エクスプ ローラー**。)。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-134">(If the toolbar is disabled, select the ContosoUniversity project in **Solution Explorer**.)</span></span>

<span data-ttu-id="d3bc5-135">Visual Studio が更新されたアプリケーションを配置し、ホーム ページにブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-135">Visual Studio deploys the updated application, and the browser opens to the home page.</span></span> <span data-ttu-id="d3bc5-136">更新プログラムが正常にデプロイされたことを確認するには、Instructors ページを実行します。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-136">Run the Instructors page to verify that the update was successfully deployed.</span></span> <span data-ttu-id="d3bc5-137">Code First アプリケーションは、このページのデータベースにアクセスする際に、データベース スキーマを更新して実行、`Seed`メソッド。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-137">When the application tries to access the database for this page, Code First updates the database schema and runs the `Seed` method.</span></span> <span data-ttu-id="d3bc5-138">予期される確認ページが表示されたら、**生年月日**で日付を含む列。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-138">When the page displays, you see the expected **Birth Date** column with dates in it.</span></span>

<span data-ttu-id="d3bc5-139">[![Instructors_page_with_birth_date_Test](deployment-to-a-hosting-provider-deploying-a-database-update-9-of-12/_static/image5.png)](deployment-to-a-hosting-provider-deploying-a-database-update-9-of-12/_static/image4.png)</span><span class="sxs-lookup"><span data-stu-id="d3bc5-139">[![Instructors_page_with_birth_date_Test](deployment-to-a-hosting-provider-deploying-a-database-update-9-of-12/_static/image5.png)](deployment-to-a-hosting-provider-deploying-a-database-update-9-of-12/_static/image4.png)</span></span>

## <a name="deploying-the-database-update-to-the-production-environment"></a><span data-ttu-id="d3bc5-140">運用環境にデータベースの更新を展開します。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-140">Deploying the Database Update to the Production Environment</span></span>

<span data-ttu-id="d3bc5-141">運用環境に展開できます。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-141">You can now deploy to production.</span></span> <span data-ttu-id="d3bc5-142">唯一の違いは、使用する*アプリ\_offline.htm*ユーザー サイトへのアクセスと変更をデプロイするときに、データベースが更新できないようにします。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-142">The only difference is that you'll use *app\_offline.htm* to prevent users from accessing the site and thus updating the database while you're deploying changes.</span></span> <span data-ttu-id="d3bc5-143">運用環境のデプロイでは、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-143">For production deployment perform the following steps:</span></span>

- <span data-ttu-id="d3bc5-144">アップロード、*アプリ\_offline.htm*ファイルを実稼働サイト。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-144">Upload the *app\_offline.htm* file to the production site.</span></span>
- <span data-ttu-id="d3bc5-145">Visual Studio での実稼働プロファイルを選択、 **Web の 1 クリックして発行**ツールバーとクリック**Web の発行**します。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-145">In Visual Studio, choose the Production profile in the **Web One Click Publish** toolbar and click **Publish Web**.</span></span>
- <span data-ttu-id="d3bc5-146">削除、*アプリ\_offline.htm*運用サイトからのファイル。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-146">Delete the *app\_offline.htm* file from the production site.</span></span>

> [!NOTE]
> <span data-ttu-id="d3bc5-147">アプリケーションを運用環境で使用中には、バックアップ計画を実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-147">While your application is in use in the production environment you should be implementing a backup plan.</span></span> <span data-ttu-id="d3bc5-148">つまり、する必要がありますが定期的にコピーして、*の学校 Prod.sdf*と*aspnet Prod.sdf*運用環境からファイルをセキュリティで保護された記憶域の場所、サイトし、このようないくつかの世代を保存する必要がありますバックアップします。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-148">That is, you should be periodically copying the *School-Prod.sdf* and *aspnet-Prod.sdf* files from the production site to a secure storage location, and you should be keeping several generations of such backups.</span></span> <span data-ttu-id="d3bc5-149">データベースを更新するときに、変更の直前のバックアップ コピーをする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-149">When you update the database, you should make a backup copy from immediately before the change.</span></span> <span data-ttu-id="d3bc5-150">次に、設定を間違えたして運用環境に配置した後に検出されるまでそのしない場合が破損する前に、の状態にデータベースを復旧できます。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-150">Then, if you make a mistake and don't discover it until after you have deployed it to production, you will still be able to recover the database to the state it was in before it became corrupted.</span></span>


<span data-ttu-id="d3bc5-151">Visual Studio がブラウザーで開いたら、ホーム ページの URL、*アプリ\_offline.htm*ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-151">When Visual Studio opens the home page URL in the browser, the *app\_offline.htm* page is displayed.</span></span> <span data-ttu-id="d3bc5-152">削除した後、*アプリ\_offline.htm*ファイル、更新プログラムが正常にデプロイされたことを確認するには、もう一度、ホーム ページを参照することができます。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-152">After you delete the *app\_offline.htm* file, you can browse to your home page again to verify that the update was successfully deployed.</span></span>

<span data-ttu-id="d3bc5-153">[![Instructors_page_with_birth_date_Prod](deployment-to-a-hosting-provider-deploying-a-database-update-9-of-12/_static/image7.png)](deployment-to-a-hosting-provider-deploying-a-database-update-9-of-12/_static/image6.png)</span><span class="sxs-lookup"><span data-stu-id="d3bc5-153">[![Instructors_page_with_birth_date_Prod](deployment-to-a-hosting-provider-deploying-a-database-update-9-of-12/_static/image7.png)](deployment-to-a-hosting-provider-deploying-a-database-update-9-of-12/_static/image6.png)</span></span>

<span data-ttu-id="d3bc5-154">これで、テストと運用環境の両方にデータベースの変更を含むアプリケーションの更新プログラムをデプロイしました。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-154">You've now deployed an application update that included a database change to both test and production.</span></span> <span data-ttu-id="d3bc5-155">次のチュートリアルでは、SQL Server Compact から SQL Server Express と SQL Server にデータベースを移行する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="d3bc5-155">The next tutorial shows you how to migrate your database from SQL Server Compact to SQL Server Express and SQL Server.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="d3bc5-156">[前へ](deployment-to-a-hosting-provider-deploying-a-code-only-update-8-of-12.md)
> [次へ](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12.md)</span><span class="sxs-lookup"><span data-stu-id="d3bc5-156">[Previous](deployment-to-a-hosting-provider-deploying-a-code-only-update-8-of-12.md)
[Next](deployment-to-a-hosting-provider-migrating-to-sql-server-10-of-12.md)</span></span>