---
uid: mvc/overview/older-versions-1/getting-started-with-mvc/getting-started-with-mvc-part8
title: モデルに列の追加 |Microsoft Docs
author: shanselman
description: これは、ASP.NET MVC の基本を紹介する初心者向けチュートリアルです。 読み取りと書き込みをデータベースから単純な web アプリケーションを作成します。
ms.author: riande
ms.date: 08/14/2010
ms.assetid: 7ae696b9-348f-4993-8ebb-a838acbe0c28
msc.legacyurl: /mvc/overview/older-versions-1/getting-started-with-mvc/getting-started-with-mvc-part8
msc.type: authoredcontent
ms.openlocfilehash: 029234cf9a28a80c487504e4e0980c214e45f53a
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59381966"
---
# <a name="adding-a-column-to-the-model"></a><span data-ttu-id="1eca2-104">モデルに列を追加する</span><span class="sxs-lookup"><span data-stu-id="1eca2-104">Adding a Column to the Model</span></span>

<span data-ttu-id="1eca2-105">[Scott Hanselman](https://github.com/shanselman)による</span><span class="sxs-lookup"><span data-stu-id="1eca2-105">by [Scott Hanselman](https://github.com/shanselman)</span></span>

> <span data-ttu-id="1eca2-106">これは、ASP.NET MVC の基本を紹介する初心者向けチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="1eca2-106">This is a beginner tutorial that introduces the basics of ASP.NET MVC.</span></span> <span data-ttu-id="1eca2-107">読み取りと書き込みをデータベースから単純な web アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="1eca2-107">You'll create a simple web application that reads and writes from a database.</span></span> <span data-ttu-id="1eca2-108">参照してください、 [ASP.NET MVC ラーニング センター](../../../index.md)チュートリアルとサンプルは、その他の ASP.NET MVC を検索します。</span><span class="sxs-lookup"><span data-stu-id="1eca2-108">Visit the [ASP.NET MVC learning center](../../../index.md) to find other ASP.NET MVC tutorials and samples.</span></span>


<span data-ttu-id="1eca2-109">このセクションではここに、データベースのスキーマ変更を加えるし、アプリケーション内で、変更を処理した方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="1eca2-109">In this section we are going to walk through how we can make changes to the schema of our database, and handle the changes within our application.</span></span>

<span data-ttu-id="1eca2-110">Movie テーブルには、「評価」の列を追加してみましょう。</span><span class="sxs-lookup"><span data-stu-id="1eca2-110">Let's add a "Rating" Column to the Movie table.</span></span> <span data-ttu-id="1eca2-111">IDE に戻るし、データベース エクスプ ローラー をクリックします。</span><span class="sxs-lookup"><span data-stu-id="1eca2-111">Go back to the IDE and click the Database Explorer.</span></span> <span data-ttu-id="1eca2-112">Movie テーブルを右クリックし、テーブル定義を開くを選択します。</span><span class="sxs-lookup"><span data-stu-id="1eca2-112">Right click the Movie table and select Open Table Definition.</span></span>

<span data-ttu-id="1eca2-113">次に示すように、「評価」列を追加します。</span><span class="sxs-lookup"><span data-stu-id="1eca2-113">Add a "Rating" column as seen below.</span></span> <span data-ttu-id="1eca2-114">これで任意の評価があるない、ため、列は null を許容できます。</span><span class="sxs-lookup"><span data-stu-id="1eca2-114">Since we don't have any Ratings now, the column can allow nulls.</span></span> <span data-ttu-id="1eca2-115">[保存] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="1eca2-115">Click Save.</span></span>

[![E<span data-ttu-id="1eca2-116">映画のテーブルの編集で]</span><span class="sxs-lookup"><span data-stu-id="1eca2-116">diting Movies Table]</span></span>(getting-started-with-mvc-part8/_static/image2.png)](getting-started-with-mvc-part8/_static/image1.png)

<span data-ttu-id="1eca2-117">次に、ソリューション エクスプ ローラーに戻り、(これ \Models フォルダーには) Movies.edmx ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="1eca2-117">Next, return to the Solution Explorer and open up the Movies.edmx file (which is in the \Models folder).</span></span> <span data-ttu-id="1eca2-118">デザイン サーフェイス (白の領域) を右クリックし、データベースからモデルを更新を選択します。</span><span class="sxs-lookup"><span data-stu-id="1eca2-118">Right click on the design surface (the white area) and select Update Model from Database.</span></span>

[![M<span data-ttu-id="1eca2-119">ovies - Microsoft Visual Web Developer 2010 Express (11)]</span><span class="sxs-lookup"><span data-stu-id="1eca2-119">ovies - Microsoft Visual Web Developer 2010 Express (11)]</span></span>(getting-started-with-mvc-part8/_static/image4.png)](getting-started-with-mvc-part8/_static/image3.png)

<span data-ttu-id="1eca2-120">これにより、「の更新ウィザード」が起動します。</span><span class="sxs-lookup"><span data-stu-id="1eca2-120">This will launch the "Update Wizard".</span></span> <span data-ttu-id="1eca2-121">内に、[更新] タブをクリックし、[完了] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="1eca2-121">Click the Refresh tab within it and click Finish.</span></span> <span data-ttu-id="1eca2-122">Movie モデル クラスは、新しい列と共に更新されます。</span><span class="sxs-lookup"><span data-stu-id="1eca2-122">Our Movie model class will then be updated with the new column.</span></span>

![(2) の更新ウィザード](getting-started-with-mvc-part8/_static/image5.png)

<span data-ttu-id="1eca2-124">[完了] をクリックすると、新しい [評価] 列が、モデル内のムービー エンティティに追加されていますがわかります。</span><span class="sxs-lookup"><span data-stu-id="1eca2-124">After clicking Finish, you can see the new Rating Column has been added to the Movie Entity in our model.</span></span>

[![M<span data-ttu-id="1eca2-125">ovie エンティティ]</span><span class="sxs-lookup"><span data-stu-id="1eca2-125">ovie Entity]</span></span>(getting-started-with-mvc-part8/_static/image7.png)](getting-started-with-mvc-part8/_static/image6.png)

<span data-ttu-id="1eca2-126">データベース モデルの列が追加されましたが、それに関するビューがわからない。</span><span class="sxs-lookup"><span data-stu-id="1eca2-126">We've added a column in the database model, but the Views don't know about it.</span></span>

## <a name="update-views-with-model-changes"></a><span data-ttu-id="1eca2-127">モデルの変更と更新の表示</span><span class="sxs-lookup"><span data-stu-id="1eca2-127">Update Views with Model Changes</span></span>

<span data-ttu-id="1eca2-128">新しい [評価] 列を反映するようにテンプレートの表示、更新、いくつかの方法はあります。</span><span class="sxs-lookup"><span data-stu-id="1eca2-128">There are a few ways we could update our view templates to reflect the new Rating column.</span></span> <span data-ttu-id="1eca2-129">ビューの追加 ダイアログを使用してそれらを生成することによってこれらのビューを作成した後はそれらを削除でしたし、もう一度再作成します。</span><span class="sxs-lookup"><span data-stu-id="1eca2-129">Since we created these Views by generating them via the Add View dialog, we could delete them and recreate them again.</span></span> <span data-ttu-id="1eca2-130">ただし、通常ユーザーが既に変更を加えたビュー テンプレートに最初のスキャフォールディングの生成から、追加または作成時に、ID フィールドのときと同様、手動でフィールドを削除します。</span><span class="sxs-lookup"><span data-stu-id="1eca2-130">However, typically people will have already made modifications to their View templates from the initial scaffolded generation and will want to add or delete fields manually, just as we did with the ID field for Create.</span></span>

<span data-ttu-id="1eca2-131">\Views\Movies\Index.aspx テンプレートを開き、追加、 &lt;th&gt;評価&lt;/th&gt; Movie テーブルの先頭にします。</span><span class="sxs-lookup"><span data-stu-id="1eca2-131">Open up the \Views\Movies\Index.aspx template and add a &lt;th&gt;Rating&lt;/th&gt; to the head of the Movie table.</span></span> <span data-ttu-id="1eca2-132">ジャンルの後に、私は追加されます。</span><span class="sxs-lookup"><span data-stu-id="1eca2-132">I added mine after Genre.</span></span> <span data-ttu-id="1eca2-133">次で同じ列の位置が下位には、当社の新しい評価の出力に行を追加します。</span><span class="sxs-lookup"><span data-stu-id="1eca2-133">Then, in the same column position but lower down, add a line to output our new Rating.</span></span>

[!code-aspx[Main](getting-started-with-mvc-part8/samples/sample1.aspx)]

<span data-ttu-id="1eca2-134">最終的な Index.aspx テンプレートは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="1eca2-134">Our final Index.aspx template will look like this:</span></span>

[!code-aspx[Main](getting-started-with-mvc-part8/samples/sample2.aspx)]

<span data-ttu-id="1eca2-135">みましょうし \Views\Movies\Create.aspx テンプレートを開き、新しい評価プロパティのラベルとテキスト ボックスを追加します。</span><span class="sxs-lookup"><span data-stu-id="1eca2-135">Let's then open up the \Views\Movies\Create.aspx template and add a Label and Textbox for our new Rating property:</span></span>

[!code-aspx[Main](getting-started-with-mvc-part8/samples/sample3.aspx)]

<span data-ttu-id="1eca2-136">最終的な Create.aspx テンプレートは次のようと、ブラウザーのタイトルとセカンダリを変更してみましょう&lt;h2&gt;タイトルを「を作成、映画」ここで私たちのようなものです。</span><span class="sxs-lookup"><span data-stu-id="1eca2-136">Our final Create.aspx template will look like this, and let's change our browser's title and secondary &lt;h2&gt; title to something like "Create a Movie" while we're in here!</span></span>

[!code-aspx[Main](getting-started-with-mvc-part8/samples/sample4.aspx)]

<span data-ttu-id="1eca2-137">アプリを実行し、[作成] ページに追加されているデータベースの新しいフィールドをしたようになりました。</span><span class="sxs-lookup"><span data-stu-id="1eca2-137">Run your app and now you've got a new field in the database that's been added to the Create page.</span></span> <span data-ttu-id="1eca2-138">-新しいムービーを追加するには、この時点で評価 - と作成 をクリックします。</span><span class="sxs-lookup"><span data-stu-id="1eca2-138">Add a new Movie - this time with a Rating - and click Create.</span></span>

[![C<span data-ttu-id="1eca2-139">Windows Internet Explorer - ムービーの作成 ()]</span><span class="sxs-lookup"><span data-stu-id="1eca2-139">reate a Movie - Windows Internet Explorer]</span></span>(getting-started-with-mvc-part8/_static/image9.png)](getting-started-with-mvc-part8/_static/image8.png)

<span data-ttu-id="1eca2-140">作成 をクリックすると後、は、インデックス ページに送信している新しいムービーが記載されている場合、データベース内の新しい評価列</span><span class="sxs-lookup"><span data-stu-id="1eca2-140">After you click Create, you're sent to the Index page where you new Movie is listed with the new Rating Column in the database</span></span>

[![M<span data-ttu-id="1eca2-141">リスト - ovie Windows Internet Explorer (12)]</span><span class="sxs-lookup"><span data-stu-id="1eca2-141">ovie List - Windows Internet Explorer (12)]</span></span>(getting-started-with-mvc-part8/_static/image11.png)](getting-started-with-mvc-part8/_static/image10.png)

<span data-ttu-id="1eca2-142">この基本的なチュートリアルでは、コント ローラー、ビューと関連付けると、ハード コーディングされたデータの受け渡しを行う作業を開始する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1eca2-142">This basic tutorial got you started making Controllers, associating them with Views and passing around hard-coded data.</span></span> <span data-ttu-id="1eca2-143">作成しデータベースを設計して一部のデータの配置でします。</span><span class="sxs-lookup"><span data-stu-id="1eca2-143">Then we created and designed a Database and put some data it in.</span></span> <span data-ttu-id="1eca2-144">データベースからデータを取得し、HTML テーブルに表示されることです。</span><span class="sxs-lookup"><span data-stu-id="1eca2-144">We retrieved the data from the database and displayed it in an HTML table.</span></span> <span data-ttu-id="1eca2-145">ユーザー データを追加、データベース自体から Web アプリケーション内で使用できるフォームを作成するを追加します。</span><span class="sxs-lookup"><span data-stu-id="1eca2-145">Then we added a Create form that let the user add data to the database themselves from within the Web Application.</span></span> <span data-ttu-id="1eca2-146">私たちは、検証を追加し、JavaScript を使用して、クライアント側で検証が行われたします。</span><span class="sxs-lookup"><span data-stu-id="1eca2-146">We added validation, then made the validation use JavaScript on the client-side.</span></span> <span data-ttu-id="1eca2-147">最後に、私たちに、データの新しい列を含めるデータベースを変更し、作成し、この新しいデータを表示する、2 つのページを更新します。</span><span class="sxs-lookup"><span data-stu-id="1eca2-147">Finally, we changed the database to include a new column of data, then updated our two pages to create and display this new data.</span></span>

<span data-ttu-id="1eca2-148">中間レベルのチュートリアル移動するようになりました"[MVC Music Store](../../older-versions/mvc-music-store/mvc-music-store-part-1.md)"と、多くのビデオおよびリソース[ https://asp.net/mvc ](https://asp.net/mvc) ASP.NET MVC についてさらに学習します。</span><span class="sxs-lookup"><span data-stu-id="1eca2-148">I now encourage you to move on to our intermediate-level tutorial "[MVC Music Store](../../older-versions/mvc-music-store/mvc-music-store-part-1.md)" as well as the many videos and resources at [https://asp.net/mvc](https://asp.net/mvc) to learn even more about ASP.NET MVC!</span></span>

<span data-ttu-id="1eca2-149">それではお楽しみください。</span><span class="sxs-lookup"><span data-stu-id="1eca2-149">Enjoy!</span></span>

- <span data-ttu-id="1eca2-150">Scott Hanselman - [ http://hanselman.com ](http://hanselman.com)と[ @shanselman ](http://twitter.com/shanselman) twitter。</span><span class="sxs-lookup"><span data-stu-id="1eca2-150">Scott Hanselman - [http://hanselman.com](http://hanselman.com) and [@shanselman](http://twitter.com/shanselman) on Twitter.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="1eca2-151">前へ</span><span class="sxs-lookup"><span data-stu-id="1eca2-151">Previous</span></span>](getting-started-with-mvc-part7.md)
