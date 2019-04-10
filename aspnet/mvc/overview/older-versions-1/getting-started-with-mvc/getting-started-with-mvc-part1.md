---
uid: mvc/overview/older-versions-1/getting-started-with-mvc/getting-started-with-mvc-part1
title: ASP.NET MVC の概要 |Microsoft Docs
author: shanselman
description: これは、ASP.NET MVC の基本を紹介する初心者向けチュートリアルです。 読み取りと書き込みをデータベースから単純な web アプリケーションを作成します。
ms.author: riande
ms.date: 08/14/2010
ms.assetid: bf4a1c19-0a94-4208-b268-a96ddcf26946
msc.legacyurl: /mvc/overview/older-versions-1/getting-started-with-mvc/getting-started-with-mvc-part1
msc.type: authoredcontent
ms.openlocfilehash: dcc2e703829cfa0b77575870feff451fd0738f56
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59416494"
---
# <a name="intro-to-aspnet-mvc"></a><span data-ttu-id="0ceea-104">ASP.NET MVC 入門</span><span class="sxs-lookup"><span data-stu-id="0ceea-104">Intro to ASP.NET MVC</span></span>

<span data-ttu-id="0ceea-105">[Scott Hanselman](https://github.com/shanselman)による</span><span class="sxs-lookup"><span data-stu-id="0ceea-105">by [Scott Hanselman](https://github.com/shanselman)</span></span>

> > [!NOTE]
> > <span data-ttu-id="0ceea-106">このチュートリアルでは、使用可能な場合は、更新されたバージョン[ここ](../../getting-started/introduction/getting-started.md)を使用して[Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)します。</span><span class="sxs-lookup"><span data-stu-id="0ceea-106">An updated version if this tutorial is available [here](../../getting-started/introduction/getting-started.md) using [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013).</span></span> <span data-ttu-id="0ceea-107">新しいチュートリアルでは、このチュートリアルで多くの機能強化を提供する ASP.NET MVC 5 を使用します。</span><span class="sxs-lookup"><span data-stu-id="0ceea-107">The new tutorial uses ASP.NET MVC 5, which provides many improvements over this tutorial.</span></span>
>
>
> <span data-ttu-id="0ceea-108">これは、ASP.NET MVC の基本を紹介する初心者向けチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="0ceea-108">This is a beginner tutorial that introduces the basics of ASP.NET MVC.</span></span> <span data-ttu-id="0ceea-109">読み取りと書き込みをデータベースから単純な web アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="0ceea-109">You'll create a simple web application that reads and writes from a database.</span></span> <span data-ttu-id="0ceea-110">参照してください、 [ASP.NET MVC ラーニング センター](../../../index.md)チュートリアルとサンプルは、その他の ASP.NET MVC を検索します。</span><span class="sxs-lookup"><span data-stu-id="0ceea-110">Visit the [ASP.NET MVC learning center](../../../index.md) to find other ASP.NET MVC tutorials and samples.</span></span>


<span data-ttu-id="0ceea-111">しましょう最初 ASP.NET MVC Web アプリケーションを使用して、 [Visual Web Developer 2010 Express](https://www.microsoft.com/express/Web/)します。</span><span class="sxs-lookup"><span data-stu-id="0ceea-111">Let's make our first ASP.NET MVC Web Application using [Visual Web Developer 2010 Express](https://www.microsoft.com/express/Web/).</span></span> <span data-ttu-id="0ceea-112">少しのムービー リスト アプリケーションをお知らせを作成し、ムービーの一覧を確認しましょう。</span><span class="sxs-lookup"><span data-stu-id="0ceea-112">We'll make a little Movie list application that will let us create and list movies.</span></span>

## <a name="what-youll-build"></a><span data-ttu-id="0ceea-113">構築します</span><span class="sxs-lookup"><span data-stu-id="0ceea-113">What You'll Build</span></span>

<span data-ttu-id="0ceea-114">ここでは、アプリケーションをビルドの 2 つのスクリーン ショットです。</span><span class="sxs-lookup"><span data-stu-id="0ceea-114">Here are two screenshots of the application you'll build.</span></span> <span data-ttu-id="0ceea-115">さまざまな列を持つ映画の単純なテーブルがあります。</span><span class="sxs-lookup"><span data-stu-id="0ceea-115">You will have a simple table of movies with various columns.</span></span>

[![M<span data-ttu-id="0ceea-116">リスト - ovie Windows Internet Explorer (12)]</span><span class="sxs-lookup"><span data-stu-id="0ceea-116">ovie List - Windows Internet Explorer (12)]</span></span>(getting-started-with-mvc-part1/_static/image2.png)](getting-started-with-mvc-part1/_static/image1.png)

<span data-ttu-id="0ceea-117">フォームの作成は、ムービーの一覧に追加できます必要があります。</span><span class="sxs-lookup"><span data-stu-id="0ceea-117">And you'll have a Create Form so we can add movies to the list.</span></span>

[![C<span data-ttu-id="0ceea-118">Windows Internet Explorer (2) - ムービーの作成 ()]</span><span class="sxs-lookup"><span data-stu-id="0ceea-118">reate a Movie - Windows Internet Explorer (2)]</span></span>(getting-started-with-mvc-part1/_static/image4.png)](getting-started-with-mvc-part1/_static/image3.png)

## <a name="skills-youll-learn"></a><span data-ttu-id="0ceea-119">学習内容</span><span class="sxs-lookup"><span data-stu-id="0ceea-119">Skills You'll Learn</span></span>

<span data-ttu-id="0ceea-120">このチュートリアルでは、Visual Studio を使用して ASP.NET MVC Web アプリケーションの構築の基礎を説明します。</span><span class="sxs-lookup"><span data-stu-id="0ceea-120">This tutorial will teach you the basics of building an ASP.NET MVC Web Application using Visual Studio.</span></span> <span data-ttu-id="0ceea-121">について説明します。</span><span class="sxs-lookup"><span data-stu-id="0ceea-121">You'll learn:</span></span>

- <span data-ttu-id="0ceea-122">新しい ASP.NET MVC プロジェクトを作成する方法</span><span class="sxs-lookup"><span data-stu-id="0ceea-122">How to create a new ASP.NET MVC Project</span></span>
- <span data-ttu-id="0ceea-123">SQL Server で新しいデータベースを作成する方法</span><span class="sxs-lookup"><span data-stu-id="0ceea-123">How to create a new Database with SQL Server</span></span>
- <span data-ttu-id="0ceea-124">ASP.NET MVC のコント ローラーとビューを作成する方法</span><span class="sxs-lookup"><span data-stu-id="0ceea-124">How to create ASP.NET MVC Controllers and Views</span></span>
- <span data-ttu-id="0ceea-125">データ取得して表示する方法</span><span class="sxs-lookup"><span data-stu-id="0ceea-125">How to retrieve and display data</span></span>
- <span data-ttu-id="0ceea-126">データを編集し、データの検証を有効にする方法</span><span class="sxs-lookup"><span data-stu-id="0ceea-126">How to edit data and enable data validation</span></span>
- <span data-ttu-id="0ceea-127">データベース スキーマを更新する方法</span><span class="sxs-lookup"><span data-stu-id="0ceea-127">How to update the database schema</span></span>

## <a name="get-started"></a><span data-ttu-id="0ceea-128">開始するには</span><span class="sxs-lookup"><span data-stu-id="0ceea-128">Get Started</span></span>

<span data-ttu-id="0ceea-129">スタート画面から Visual Web Developer 2010 Express (名前を付けます"VWD"ようになりました) と新しいプロジェクトを選択を実行して開始します。</span><span class="sxs-lookup"><span data-stu-id="0ceea-129">Start by running Visual Web Developer 2010 Express (I'll call it "VWD" from now on) and select New Project from the Start Screen.</span></span>

<span data-ttu-id="0ceea-130">Visual Web Developer は、IDE、または統合開発環境です。</span><span class="sxs-lookup"><span data-stu-id="0ceea-130">Visual Web Developer is an IDE, or Integrated Developer Environment.</span></span> <span data-ttu-id="0ceea-131">Microsoft Word を使用してドキュメントを記述するのにのと同じようにアプリケーションを作成、IDE を使用します。</span><span class="sxs-lookup"><span data-stu-id="0ceea-131">Just like you use Microsoft Word to write documents, you'll use an IDE to create applications.</span></span> <span data-ttu-id="0ceea-132">およびファイルの選択にも使用してもメニューを使用できるさまざまなオプションを示す上部のツールバーが |新しいプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="0ceea-132">There's a toolbar along the top showing various options available to you, as well as the menu you could also have used to Select File | New project.</span></span>

[![M<span data-ttu-id="0ceea-133">icrosoft Visual Web Developer 2010 Express]</span><span class="sxs-lookup"><span data-stu-id="0ceea-133">icrosoft Visual Web Developer 2010 Express]</span></span>(getting-started-with-mvc-part1/_static/image6.png)](getting-started-with-mvc-part1/_static/image5.png)

## <a name="creating-your-first-application"></a><span data-ttu-id="0ceea-134">最初のアプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="0ceea-134">Creating Your First Application</span></span>

<span data-ttu-id="0ceea-135">Visual Basic または Visual C# を使用してアプリケーションを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="0ceea-135">You can create applications using Visual Basic or Visual C#.</span></span> <span data-ttu-id="0ceea-136">ここで、選択 Visual C#、左側の「ASP.NET MVC 2 Web アプリケーション」を選択し、</span><span class="sxs-lookup"><span data-stu-id="0ceea-136">For now, Select Visual C# on the left, then pick "ASP.NET MVC 2 Web Application."</span></span> <span data-ttu-id="0ceea-137">プロジェクトに"Movies"という名前し、[ok] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="0ceea-137">Name your project "Movies" and click OK.</span></span>

[![N<span data-ttu-id="0ceea-138">新しいプロジェクト]</span><span class="sxs-lookup"><span data-stu-id="0ceea-138">ew Project]</span></span>(getting-started-with-mvc-part1/_static/image8.png)](getting-started-with-mvc-part1/_static/image7.png)

<span data-ttu-id="0ceea-139">右側にあるすべてのファイルとフォルダーを示す、アプリケーションで、ソリューション エクスプ ローラーです。</span><span class="sxs-lookup"><span data-stu-id="0ceea-139">On the right-hand side is the Solution Explorer showing all the files and folders in your application.</span></span> <span data-ttu-id="0ceea-140">中央の大きなウィンドウは、コードを編集し、ほとんどの時間を費やす場所には。</span><span class="sxs-lookup"><span data-stu-id="0ceea-140">The big window in the middle is where you edit your code and spend most of your time.</span></span> <span data-ttu-id="0ceea-141">Visual Studio は、何もせず、実用的なアプリケーションが現在あるため、作成した ASP.NET MVC プロジェクトの既定のテンプレートを使用!</span><span class="sxs-lookup"><span data-stu-id="0ceea-141">Visual Studio used a default template for the ASP.NET MVC project you just created, so you have a working application right now without doing anything!</span></span> <span data-ttu-id="0ceea-142">これは、単純な"Hello World!</span><span class="sxs-lookup"><span data-stu-id="0ceea-142">This is a simple "Hello World!</span></span> <span data-ttu-id="0ceea-143">プロジェクト、およびそのは、アプリケーションを起動することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="0ceea-143">project, and it is a good place to start for our application.</span></span>

[![M<span data-ttu-id="0ceea-144">icrosoft Visual Web Developer 2010 Express]</span><span class="sxs-lookup"><span data-stu-id="0ceea-144">icrosoft Visual Web Developer 2010 Express]</span></span>(getting-started-with-mvc-part1/_static/image10.png)](getting-started-with-mvc-part1/_static/image9.png)

<span data-ttu-id="0ceea-145">ツールバーに「再生」ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="0ceea-145">Select the "play" button to the toolbar.</span></span>

![デバッグの開始](getting-started-with-mvc-part1/_static/image11.png)

<span data-ttu-id="0ceea-147">プログラムをコンパイルし、web ブラウザーでアプリケーションを起動する右側に緑色の矢印になります。</span><span class="sxs-lookup"><span data-stu-id="0ceea-147">It's a green arrow pointing to the right that will compile your program and start your application in a web browser.</span></span>

*<span data-ttu-id="0ceea-148">注: 代わりに、キーボードの f5 キーを押して、デバッグを選択または&gt;[デバッグ] メニューからデバッグを開始します。</span><span class="sxs-lookup"><span data-stu-id="0ceea-148">NOTE: You can instead press F5 on your keyboard, or select Debug-&gt;Start Debugging from the "Debug" menu.</span></span>*

<span data-ttu-id="0ceea-149">これは、結果、Visual Web Developer を開発 web サーバーを起動し、(はありません構成や手動の手順がこれを有効にするために必要)、web アプリケーションを実行します。</span><span class="sxs-lookup"><span data-stu-id="0ceea-149">This will cause Visual Web Developer to start a development web-server and run our web application (there are no configuration or manual steps required to enable this).</span></span> <span data-ttu-id="0ceea-150">ブラウザーを起動し、アプリケーションのホーム ページを参照するように構成には、します。</span><span class="sxs-lookup"><span data-stu-id="0ceea-150">It will then launch a browser and configure it to browse the application's home-page.</span></span> <span data-ttu-id="0ceea-151">以下の"localhost"の example.com のようなものであり、ブラウザーのアドレス バーに表示されるに注意してください。</span><span class="sxs-lookup"><span data-stu-id="0ceea-151">Notice below that the address bar of the browser says "localhost", and not something like example.com.</span></span> <span data-ttu-id="0ceea-152">Localhost は常に、ここで作成したアプリケーションを実行している独自のローカル コンピューターをポイントするためです。</span><span class="sxs-lookup"><span data-stu-id="0ceea-152">That's because localhost always points to your own local computer - which in this case is running the application we just built.</span></span>

[![H<span data-ttu-id="0ceea-153">ome ページ]</span><span class="sxs-lookup"><span data-stu-id="0ceea-153">ome Page]</span></span>(getting-started-with-mvc-part1/_static/image13.png)](getting-started-with-mvc-part1/_static/image12.png)

<span data-ttu-id="0ceea-154">この既定のテンプレートはすぐする 2 つのページにアクセスして、基本的なログイン ページを示します。</span><span class="sxs-lookup"><span data-stu-id="0ceea-154">Out of the box this default template gives you two pages to visit and a basic login page.</span></span> <span data-ttu-id="0ceea-155">このアプリケーションの動作を変更して、プロセスで ASP.NET MVC についてもう少し説明します。</span><span class="sxs-lookup"><span data-stu-id="0ceea-155">Let's change how this application works and learn a little bit about ASP.NET MVC in the process.</span></span> <span data-ttu-id="0ceea-156">ブラウザーを閉じて、いくつかのコードを変更することができます。</span><span class="sxs-lookup"><span data-stu-id="0ceea-156">Close your browser and lets change some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="0ceea-157">次へ</span><span class="sxs-lookup"><span data-stu-id="0ceea-157">Next</span></span>](getting-started-with-mvc-part2.md)
