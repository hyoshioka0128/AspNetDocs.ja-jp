---
uid: mvc/overview/older-versions-1/contact-manager/iteration-2-make-the-application-look-nice-vb
title: '繰り返し #2 – アプリケーションの外観を良くする (VB) を作成する |Microsoft Docs'
author: microsoft
description: このイテレーションで、アプリケーションの見た目を向上させる、既定の ASP.NET MVC ビュー マスター ページを変更し、カスケード スタイル シート。
ms.author: riande
ms.date: 02/20/2009
ms.assetid: f65cb436-e493-46fd-9608-384b27385aa1
msc.legacyurl: /mvc/overview/older-versions-1/contact-manager/iteration-2-make-the-application-look-nice-vb
msc.type: authoredcontent
ms.openlocfilehash: 21f7974fe066543d6db1d17d462398a998d0171e
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59382578"
---
# <a name="iteration-2--make-the-application-look-nice-vb"></a><span data-ttu-id="9b5db-103">繰り返し #2 – アプリケーションの外観を良くする (VB) を作成します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-103">Iteration #2 – Make the application look nice (VB)</span></span>

<span data-ttu-id="9b5db-104">によって[Microsoft](https://github.com/microsoft)</span><span class="sxs-lookup"><span data-stu-id="9b5db-104">by [Microsoft](https://github.com/microsoft)</span></span>

[<span data-ttu-id="9b5db-105">コードをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="9b5db-105">Download Code</span></span>](iteration-2-make-the-application-look-nice-vb/_static/contactmanager_2_vb1.zip)

> <span data-ttu-id="9b5db-106">このイテレーションで、アプリケーションの見た目を向上させる、既定の ASP.NET MVC ビュー マスター ページを変更し、カスケード スタイル シート。</span><span class="sxs-lookup"><span data-stu-id="9b5db-106">In this iteration, we improve the appearance of the application by modifying the default ASP.NET MVC view master page and cascading style sheet.</span></span>


## <a name="building-a-contact-management-aspnet-mvc-application-vb"></a><span data-ttu-id="9b5db-107">ASP.NET MVC 連絡先管理アプリケーション (VB) の構築</span><span class="sxs-lookup"><span data-stu-id="9b5db-107">Building a Contact Management ASP.NET MVC Application (VB)</span></span>
  

<span data-ttu-id="9b5db-108">このチュートリアル シリーズでは、開始から終了に全体連絡先管理アプリケーションを構築します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-108">In this series of tutorials, we build an entire Contact Management application from start to finish.</span></span> <span data-ttu-id="9b5db-109">Contact Manager アプリケーションでは、一連の連絡先の情報-名前、電話番号と電子メール アドレス-がユーザーを格納することができます。</span><span class="sxs-lookup"><span data-stu-id="9b5db-109">The Contact Manager application enables you to store contact information – names, phone numbers and email addresses – for a list of people.</span></span>

<span data-ttu-id="9b5db-110">複数のイテレーションにおける、アプリケーションを構築します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-110">We build the application over multiple iterations.</span></span> <span data-ttu-id="9b5db-111">反復処理ごとに、アプリケーション徐々 に向上します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-111">With each iteration, we gradually improve the application.</span></span> <span data-ttu-id="9b5db-112">この複数のイテレーションのアプローチの目的は、各変更の理由を理解するためです。</span><span class="sxs-lookup"><span data-stu-id="9b5db-112">The goal of this multiple iteration approach is to enable you to understand the reason for each change.</span></span>

- <span data-ttu-id="9b5db-113">繰り返し #1 – アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-113">Iteration #1 – Create the application.</span></span> <span data-ttu-id="9b5db-114">最初のイテレーションを作成、連絡先マネージャー最も簡単な方法で考えられるします。</span><span class="sxs-lookup"><span data-stu-id="9b5db-114">In the first iteration, we create the Contact Manager in the simplest way possible.</span></span> <span data-ttu-id="9b5db-115">基本的なデータベース操作のサポートを追加します。作成、読み取り、更新、および削除 (CRUD)。</span><span class="sxs-lookup"><span data-stu-id="9b5db-115">We add support for basic database operations: Create, Read, Update, and Delete (CRUD).</span></span>

- <span data-ttu-id="9b5db-116">繰り返し #2 – アプリケーションが素敵に見えるようにする場合。</span><span class="sxs-lookup"><span data-stu-id="9b5db-116">Iteration #2 – Make the application look nice.</span></span> <span data-ttu-id="9b5db-117">このイテレーションで、アプリケーションの見た目を向上させる、既定の ASP.NET MVC ビュー マスター ページを変更し、カスケード スタイル シート。</span><span class="sxs-lookup"><span data-stu-id="9b5db-117">In this iteration, we improve the appearance of the application by modifying the default ASP.NET MVC view master page and cascading style sheet.</span></span>

- <span data-ttu-id="9b5db-118">繰り返し #3 – フォーム検証を追加します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-118">Iteration #3 – Add form validation.</span></span> <span data-ttu-id="9b5db-119">3 番目のイテレーションでは、基本的なフォーム検証を追加します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-119">In the third iteration, we add basic form validation.</span></span> <span data-ttu-id="9b5db-120">ユーザーは、必要なフォームのフィールドを完了しなくても、フォームを送信できないようにようにします。</span><span class="sxs-lookup"><span data-stu-id="9b5db-120">We prevent people from submitting a form without completing required form fields.</span></span> <span data-ttu-id="9b5db-121">また、電子メール アドレスと電話番号を検証しました。</span><span class="sxs-lookup"><span data-stu-id="9b5db-121">We also validate email addresses and phone numbers.</span></span>

- <span data-ttu-id="9b5db-122">繰り返し #4 – アプリケーションを疎結合を作成します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-122">Iteration #4 – Make the application loosely coupled.</span></span> <span data-ttu-id="9b5db-123">この 4 番目のイテレーションで、保守し、Contact Manager アプリケーションの変更を容易にできるようにするソフトウェア設計パターンをいくつかの利点を実行します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-123">In this fourth iteration, we take advantage of several software design patterns to make it easier to maintain and modify the Contact Manager application.</span></span> <span data-ttu-id="9b5db-124">たとえば、Repository パターンと依存関係の注入パターンを使用するようにアプリケーションをリファクタリングします。</span><span class="sxs-lookup"><span data-stu-id="9b5db-124">For example, we refactor our application to use the Repository pattern and the Dependency Injection pattern.</span></span>

- <span data-ttu-id="9b5db-125">繰り返し #5 – 単体テストを生成します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-125">Iteration #5 – Create unit tests.</span></span> <span data-ttu-id="9b5db-126">5 番目のイテレーションでアプリケーションと簡単に維持単体テストを追加して変更します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-126">In the fifth iteration, we make our application easier to maintain and modify by adding unit tests.</span></span> <span data-ttu-id="9b5db-127">データ モデル クラスの模擬テストを実行し、コント ローラーと検証ロジックの単体テストをビルドします。</span><span class="sxs-lookup"><span data-stu-id="9b5db-127">We mock our data model classes and build unit tests for our controllers and validation logic.</span></span>

- <span data-ttu-id="9b5db-128">繰り返し #6 – テスト駆動開発を使用します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-128">Iteration #6 – Use test-driven development.</span></span> <span data-ttu-id="9b5db-129">この 6 番目のイテレーションでは、アプリケーションに新しい機能を追加には、まず単体テストの記述、単体テストに対してコードを記述します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-129">In this sixth iteration, we add new functionality to our application by writing unit tests first and writing code against the unit tests.</span></span> <span data-ttu-id="9b5db-130">このイテレーションは、連絡先グループを追加します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-130">In this iteration, we add contact groups.</span></span>

- <span data-ttu-id="9b5db-131">繰り返し #7 – Ajax 機能を追加します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-131">Iteration #7 – Add Ajax functionality.</span></span> <span data-ttu-id="9b5db-132">7 番目のイテレーションで改良、応答性と、アプリケーションのパフォーマンスの Ajax のサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-132">In the seventh iteration, we improve the responsiveness and performance of our application by adding support for Ajax.</span></span>

## <a name="this-iteration"></a><span data-ttu-id="9b5db-133">このイテレーション</span><span class="sxs-lookup"><span data-stu-id="9b5db-133">This Iteration</span></span>

<span data-ttu-id="9b5db-134">このイテレーションの目的は、Contact Manager アプリケーションの外観を向上させることです。</span><span class="sxs-lookup"><span data-stu-id="9b5db-134">The goal of this iteration is to improve the appearance of the Contact Manager application.</span></span> <span data-ttu-id="9b5db-135">現時点では、連絡先マネージャーでは、(図 1 参照)、既定の ASP.NET MVC ビュー マスター ページとカスケード スタイル シートを使用します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-135">Currently, the Contact Manager uses the default ASP.NET MVC view master page and cascading style sheet (see Figure 1).</span></span> <span data-ttu-id="9b5db-136">不適切な場合は、これらがないが t するその他のすべての ASP.NET MVC web サイトのように、連絡先のマネージャーをしないようにします。</span><span class="sxs-lookup"><span data-stu-id="9b5db-136">These don t look bad, but I don t want the Contact Manager to look just like every other ASP.NET MVC website.</span></span> <span data-ttu-id="9b5db-137">カスタムのファイルでこれらのファイルを置換します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-137">I want to replace these files with custom files.</span></span>


[![T<span data-ttu-id="9b5db-138">彼は新しいプロジェクト] ダイアログ ボックス]</span><span class="sxs-lookup"><span data-stu-id="9b5db-138">he New Project dialog box]</span></span>(iteration-2-make-the-application-look-nice-vb/_static/image1.jpg)](iteration-2-make-the-application-look-nice-vb/_static/image1.png)

<span data-ttu-id="9b5db-139">**図 01**:ASP.NET MVC アプリケーションの既定の外観 ([フルサイズの画像を表示する をクリックします](iteration-2-make-the-application-look-nice-vb/_static/image2.png))。</span><span class="sxs-lookup"><span data-stu-id="9b5db-139">**Figure 01**: The default appearance of an ASP.NET MVC Application ([Click to view full-size image](iteration-2-make-the-application-look-nice-vb/_static/image2.png))</span></span>


<span data-ttu-id="9b5db-140">このイテレーションでは、アプリケーションのビジュアル デ ザインを向上する 2 つの方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-140">In this iteration, I discuss two approaches to improving the visual design of our application.</span></span> <span data-ttu-id="9b5db-141">最初に、無料の ASP.NET MVC デザイン テンプレートをダウンロードする ASP.NET MVC の設計のギャラリーの利用する方法が説明します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-141">First, I show you how to take advantage of the ASP.NET MVC Design gallery to download a free ASP.NET MVC design template.</span></span> <span data-ttu-id="9b5db-142">ASP.NET MVC デザイン ギャラリーでは、何もしないで、プロの web アプリケーションを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="9b5db-142">The ASP.NET MVC Design gallery enables you to create a professional web application without doing any work.</span></span>

<span data-ttu-id="9b5db-143">Contact Manager アプリケーションの ASP.NET MVC の設計のギャラリーからテンプレートを使用しないことにしました。</span><span class="sxs-lookup"><span data-stu-id="9b5db-143">I decided to not use a template from the ASP.NET MVC Design gallery for the Contact Manager application.</span></span> <span data-ttu-id="9b5db-144">代わりに、本格的なデザイン会社によって作成されたカスタム デザインがありました。</span><span class="sxs-lookup"><span data-stu-id="9b5db-144">Instead, I had a custom design created by a professional design firm.</span></span> <span data-ttu-id="9b5db-145">このチュートリアルの 2 番目の部分で最終的な ASP.NET MVC の設計を作成する本格的なデザイン会社と一緒に作業する方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-145">In the second part of this tutorial, I explain how I worked with a professional design company to create the final ASP.NET MVC design.</span></span>

## <a name="the-aspnet-mvc-design-gallery"></a><span data-ttu-id="9b5db-146">ASP.NET MVC の設計のギャラリー</span><span class="sxs-lookup"><span data-stu-id="9b5db-146">The ASP.NET MVC Design Gallery</span></span>

<span data-ttu-id="9b5db-147">ASP.NET MVC の設計のギャラリーとは、Microsoft によって提供される無料のリソースです。</span><span class="sxs-lookup"><span data-stu-id="9b5db-147">The ASP.NET MVC Design Gallery is a free resource provided by Microsoft.</span></span> <span data-ttu-id="9b5db-148">ASP.NET MVC のギャラリーにある次のアドレス。</span><span class="sxs-lookup"><span data-stu-id="9b5db-148">The ASP.NET MVC Gallery is located at the following address:</span></span>

[https://www.asp.net/mvc/gallery](https://www.asp.net/mvc/gallery)

<span data-ttu-id="9b5db-149">ASP.NET MVC の設計のギャラリーでは、ASP.NET MVC プロジェクトで使用するためには、具体的には作成された無料の web サイト デザインのコレクションをホストします。</span><span class="sxs-lookup"><span data-stu-id="9b5db-149">The ASP.NET MVC Design Gallery hosts a collection of free website designs that were created specifically for using in an ASP.NET MVC project.</span></span> <span data-ttu-id="9b5db-150">コミュニティのメンバーでは、設計がアップロードされます。</span><span class="sxs-lookup"><span data-stu-id="9b5db-150">Designs are uploaded by members of the community.</span></span> <span data-ttu-id="9b5db-151">ギャラリーへの訪問者は、お気に入りの設計に投票できます (図 2 参照)。</span><span class="sxs-lookup"><span data-stu-id="9b5db-151">Visitors to the Gallery can vote for their favorite designs (see Figure 2).</span></span>


[![T<span data-ttu-id="9b5db-152">彼は新しいプロジェクト] ダイアログ ボックス]</span><span class="sxs-lookup"><span data-stu-id="9b5db-152">he New Project dialog box]</span></span>(iteration-2-make-the-application-look-nice-vb/_static/image2.jpg)](iteration-2-make-the-application-look-nice-vb/_static/image3.png)

<span data-ttu-id="9b5db-153">**図 02**:ASP.NET MVC の設計のギャラリー ([フルサイズの画像を表示する をクリックします](iteration-2-make-the-application-look-nice-vb/_static/image4.png))。</span><span class="sxs-lookup"><span data-stu-id="9b5db-153">**Figure 02**: The ASP.NET MVC Design Gallery ([Click to view full-size image](iteration-2-make-the-application-look-nice-vb/_static/image4.png))</span></span>


<span data-ttu-id="9b5db-154">このチュートリアルを執筆ギャラリーで最も人気のある設計、David Hauser 年 10 月を付けた設計です。</span><span class="sxs-lookup"><span data-stu-id="9b5db-154">As I write this tutorial, the most popular design in the gallery is a design named October by David Hauser.</span></span> <span data-ttu-id="9b5db-155">次の手順を実行して、ASP.NET MVC プロジェクトのこの設計を使用できます。</span><span class="sxs-lookup"><span data-stu-id="9b5db-155">You can use this design for an ASP.NET MVC project by completing the following steps:</span></span>

1. <span data-ttu-id="9b5db-156">をクリックして、**ダウンロード**October.zip ファイルをコンピューターにダウンロードするボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="9b5db-156">Click the **Download** button to download the October.zip file to your computer.</span></span>
2. <span data-ttu-id="9b5db-157">ダウンロードした October.zip ファイルを右クリックし、をクリックして、**ブロック解除**(図 3 を参照してください) ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="9b5db-157">Right-click the downloaded October.zip file and click the **Unblock** button (see Figure 3).</span></span>
3. <span data-ttu-id="9b5db-158">10 月という名前のフォルダーにファイルを解凍します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-158">Unzip the file to a folder named October.</span></span>
4. <span data-ttu-id="9b5db-159">年 10 月のフォルダーに含まれるレイアウト フォルダーからすべてのファイルを選択で、ファイルを右クリックし、メニュー オプションを選択**コピー**します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-159">Select all of the files from the DesignTemplate folder contained in the October folder, right-click the files, and select the menu option **Copy**.</span></span>
5. <span data-ttu-id="9b5db-160">Visual Studio ソリューション エクスプ ローラー ウィンドウで ContactManager プロジェクト ノードを右クリックし、メニュー オプションを選択**貼り付け**(図 4 参照)。</span><span class="sxs-lookup"><span data-stu-id="9b5db-160">Right-click the ContactManager project node in the Visual Studio Solution Explorer window and select the menu option **Paste** (see Figure 4).</span></span>
6. <span data-ttu-id="9b5db-161">Visual Studio のメニュー オプションを選択**編集の検索し、置換、クイック置換**と置換 *[MyProjectName]* で*ContactManager* (図 5 を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="9b5db-161">Select the Visual Studio menu option **Edit, Find and Replace, Quick Replace** and replace *[MyProjectName]* with *ContactManager* (see Figure 5).</span></span>


[![T<span data-ttu-id="9b5db-162">彼は新しいプロジェクト] ダイアログ ボックス]</span><span class="sxs-lookup"><span data-stu-id="9b5db-162">he New Project dialog box]</span></span>(iteration-2-make-the-application-look-nice-vb/_static/image3.jpg)](iteration-2-make-the-application-look-nice-vb/_static/image5.png)

<span data-ttu-id="9b5db-163">**図 03**:Web からダウンロードしたファイルのブロックを解除 ([フルサイズの画像を表示する をクリックします](iteration-2-make-the-application-look-nice-vb/_static/image6.png))。</span><span class="sxs-lookup"><span data-stu-id="9b5db-163">**Figure 03**: Unblocking a file downloaded from the web ([Click to view full-size image](iteration-2-make-the-application-look-nice-vb/_static/image6.png))</span></span>


[![T<span data-ttu-id="9b5db-164">彼は新しいプロジェクト] ダイアログ ボックス]</span><span class="sxs-lookup"><span data-stu-id="9b5db-164">he New Project dialog box]</span></span>(iteration-2-make-the-application-look-nice-vb/_static/image4.jpg)](iteration-2-make-the-application-look-nice-vb/_static/image7.png)

<span data-ttu-id="9b5db-165">**図 04**:ソリューション エクスプ ローラーでファイルを上書きする ([フルサイズの画像を表示する をクリックします](iteration-2-make-the-application-look-nice-vb/_static/image8.png))。</span><span class="sxs-lookup"><span data-stu-id="9b5db-165">**Figure 04**: Overwriting files in the Solution Explorer ([Click to view full-size image](iteration-2-make-the-application-look-nice-vb/_static/image8.png))</span></span>


[![T<span data-ttu-id="9b5db-166">彼は新しいプロジェクト] ダイアログ ボックス]</span><span class="sxs-lookup"><span data-stu-id="9b5db-166">he New Project dialog box]</span></span>(iteration-2-make-the-application-look-nice-vb/_static/image5.jpg)](iteration-2-make-the-application-look-nice-vb/_static/image9.png)

<span data-ttu-id="9b5db-167">**図 05**:[プロジェクト名] に置き換えて ContactManager ([フルサイズの画像を表示する をクリックします](iteration-2-make-the-application-look-nice-vb/_static/image10.png))。</span><span class="sxs-lookup"><span data-stu-id="9b5db-167">**Figure 05**: Replacing [ProjectName] with ContactManager ([Click to view full-size image](iteration-2-make-the-application-look-nice-vb/_static/image10.png))</span></span>


<span data-ttu-id="9b5db-168">次の手順を完了すると、web アプリケーションは、新しいデザインを使用します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-168">After you complete these steps, your web application will use the new design.</span></span> <span data-ttu-id="9b5db-169">図 6 ページは、年 10 月の設計と Contact Manager アプリケーションの外観を示しています。</span><span class="sxs-lookup"><span data-stu-id="9b5db-169">The page in Figure 6 illustrates the appearance of the Contact Manager application with the October design.</span></span>


[![T<span data-ttu-id="9b5db-170">彼は新しいプロジェクト] ダイアログ ボックス]</span><span class="sxs-lookup"><span data-stu-id="9b5db-170">he New Project dialog box]</span></span>(iteration-2-make-the-application-look-nice-vb/_static/image6.jpg)](iteration-2-make-the-application-look-nice-vb/_static/image11.png)

<span data-ttu-id="9b5db-171">**図 06**:ContactManager 年 10 月のテンプレートを使用して ([フルサイズの画像を表示する をクリックします](iteration-2-make-the-application-look-nice-vb/_static/image12.png))。</span><span class="sxs-lookup"><span data-stu-id="9b5db-171">**Figure 06**: ContactManager with the October template ([Click to view full-size image](iteration-2-make-the-application-look-nice-vb/_static/image12.png))</span></span>


## <a name="creating-a-custom-aspnet-mvc-design"></a><span data-ttu-id="9b5db-172">カスタム ASP.NET MVC の設計を作成します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-172">Creating a Custom ASP.NET MVC Design</span></span>

<span data-ttu-id="9b5db-173">ASP.NET MVC デザイン ギャラリーでは、さまざまなデザイン スタイルの適切な選択範囲には。</span><span class="sxs-lookup"><span data-stu-id="9b5db-173">The ASP.NET MVC Design Gallery has a good selection of different design styles.</span></span> <span data-ttu-id="9b5db-174">ギャラリーには、ASP.NET MVC アプリケーションの外観をカスタマイズする最も簡単に提供します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-174">The Gallery provides you with a painless way to customize the appearance of your ASP.NET MVC applications.</span></span> <span data-ttu-id="9b5db-175">また、もちろん、ギャラリーには、完全に無料になるという大きな利点。</span><span class="sxs-lookup"><span data-stu-id="9b5db-175">And, of course, the Gallery has the big advantage of being completely free.</span></span>

<span data-ttu-id="9b5db-176">ただし、web サイトの完全に独自のデザインを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9b5db-176">However, you might need to create a completely unique design for your website.</span></span> <span data-ttu-id="9b5db-177">その場合は、理にかなって web サイトのデザイン会社を使用します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-177">In that case, it makes sense to work with a website design company.</span></span> <span data-ttu-id="9b5db-178">Contact Manager アプリケーションを設計するためには、この手法を採用することにしました。</span><span class="sxs-lookup"><span data-stu-id="9b5db-178">I decided to take this approach for the design for the Contact Manager application.</span></span>

<span data-ttu-id="9b5db-179">イテレーション 1 Contact Manager を zip 形式し、デザイン会社にプロジェクトを送信します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-179">I zipped up the Contact Manager from Iteration #1 and sent the project to the design company.</span></span> <span data-ttu-id="9b5db-180">Visual Studio (残念なことに!) を所有せずが問題を表示しませんでした。</span><span class="sxs-lookup"><span data-stu-id="9b5db-180">They did not own Visual Studio (shame on them!), but that didn't present a problem.</span></span> <span data-ttu-id="9b5db-181">Microsoft Visual Web Developer を無料でダウンロードすることができました、 [ https://www.asp.net ](https://www.asp.net) web サイトを Visual Web Developer で Contact Manager アプリケーションを開きます。</span><span class="sxs-lookup"><span data-stu-id="9b5db-181">They were able to download Microsoft Visual Web Developer for free from the [https://www.asp.net](https://www.asp.net) website and open the Contact Manager application in Visual Web Developer.</span></span> <span data-ttu-id="9b5db-182">いくつかの日には、図 7 の設計を生成されると必要があります。</span><span class="sxs-lookup"><span data-stu-id="9b5db-182">In a couple of days, they had produced the design in Figure 7.</span></span>


[![T<span data-ttu-id="9b5db-183">彼は新しいプロジェクト] ダイアログ ボックス]</span><span class="sxs-lookup"><span data-stu-id="9b5db-183">he New Project dialog box]</span></span>(iteration-2-make-the-application-look-nice-vb/_static/image7.jpg)](iteration-2-make-the-application-look-nice-vb/_static/image13.png)

<span data-ttu-id="9b5db-184">**図 07**:ASP.NET MVC の Contact Manager デザイン ([フルサイズの画像を表示する をクリックします](iteration-2-make-the-application-look-nice-vb/_static/image14.png))。</span><span class="sxs-lookup"><span data-stu-id="9b5db-184">**Figure 07**: The ASP.NET MVC Contact Manager Design ([Click to view full-size image](iteration-2-make-the-application-look-nice-vb/_static/image14.png))</span></span>


<span data-ttu-id="9b5db-185">2 つの主なファイルの新しいデザインを行いました。 新しいカスケード スタイル シート ファイルと新しいビュー マスター ページ ファイルです。</span><span class="sxs-lookup"><span data-stu-id="9b5db-185">The new design consisted of two main files: a new cascading style sheet file and a new view master page file.</span></span> <span data-ttu-id="9b5db-186">ビュー マスター ページには、レイアウトとビューの ASP.NET MVC アプリケーションで共有のコンテンツが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9b5db-186">A view master page contains the layout and shared content for views in an ASP.NET MVC application.</span></span> <span data-ttu-id="9b5db-187">たとえば、ビュー マスター ページにヘッダー、ナビゲーション タブ、およびフッターに表示される図 7 されます。</span><span class="sxs-lookup"><span data-stu-id="9b5db-187">For example, the view master page includes the header, navigation tabs, and footer that appear in Figure 7.</span></span> <span data-ttu-id="9b5db-188">Views \shared フォルダー Site.Master 既存ビュー マスター ページが上書きされましたデザイン社では、新しい Site.Master ファイルには</span><span class="sxs-lookup"><span data-stu-id="9b5db-188">I overwrote the existing Site.Master view master page in the Views\Shared folder with the new Site.Master file from the design company,</span></span>

<span data-ttu-id="9b5db-189">デザイン会社では、新しいカスケード スタイル シートとイメージのセットも作成されます。</span><span class="sxs-lookup"><span data-stu-id="9b5db-189">The design company also created a new cascading style sheet and set of images.</span></span> <span data-ttu-id="9b5db-190">コンテンツのフォルダーにこれらの新しいファイルを配置し、既存の Site.css ファイルを上書きします。</span><span class="sxs-lookup"><span data-stu-id="9b5db-190">I placed these new files in the Content folder and overwrote the existing Site.css file.</span></span> <span data-ttu-id="9b5db-191">コンテンツのフォルダーには、すべての静的コンテンツを配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9b5db-191">You should place all static content in the Content folder.</span></span>

<span data-ttu-id="9b5db-192">新しいデザイン用連絡先マネージャーに編集および連絡先を削除するためのイメージが含まれていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-192">Notice that the new design for the Contact Manager includes images for editing and deleting contacts.</span></span> <span data-ttu-id="9b5db-193">連絡先の HTML の表では、各連絡先の横にある、編集、削除のイメージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="9b5db-193">An Edit and Delete image appear next to each contact in the HTML table of contacts.</span></span>

<span data-ttu-id="9b5db-194">最初に、これらのリンク、HTML でレンダリングされました。このような ActionLink() ヘルパー:</span><span class="sxs-lookup"><span data-stu-id="9b5db-194">Originally, these links that were rendered with the HTML.ActionLink() helper like this:</span></span>

[!code-aspx[Main](iteration-2-make-the-application-look-nice-vb/samples/sample1.aspx)]

<span data-ttu-id="9b5db-195">Html.ActionLink() メソッドは、イメージ (HTML メソッドには、セキュリティ上の理由のリンクのテキストがエンコードされます) をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="9b5db-195">The Html.ActionLink() method does not support images (the method HTML encodes the link text for security reasons).</span></span> <span data-ttu-id="9b5db-196">そのため、このような Url.Action() への呼び出しに Html.ActionLink() への呼び出しは置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="9b5db-196">Therefore, I replaced the calls to Html.ActionLink() with calls to Url.Action() like this:</span></span>

[!code-aspx[Main](iteration-2-make-the-application-look-nice-vb/samples/sample2.aspx)]

<span data-ttu-id="9b5db-197">Html.ActionLink() メソッドは、全体の HTML ハイパーリンクを表示します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-197">The Html.ActionLink() method renders an entire HTML hyperlink.</span></span> <span data-ttu-id="9b5db-198">Url.Action() メソッドなしの URL だけをレンダリング一方で、 &lt;、&gt;タグ。</span><span class="sxs-lookup"><span data-stu-id="9b5db-198">The Url.Action() method, on the other hand, renders just the URL without the &lt;a&gt; tag.</span></span>

<span data-ttu-id="9b5db-199">さらに、新しいデザインが選択および選択されていない [両方] タブが含まれることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="9b5db-199">Notice, furthermore, that the new design includes both selected and unselected tabs.</span></span> <span data-ttu-id="9b5db-200">たとえば、図 8 に、**新しい連絡先の作成**タブが選択されていると、**個人用の連絡先** タブが選択されていません。</span><span class="sxs-lookup"><span data-stu-id="9b5db-200">For example, in Figure 8, the **Create New Contact** tab is selected and the **My Contacts** tab is not selected.</span></span>


[![T<span data-ttu-id="9b5db-201">彼は新しいプロジェクト] ダイアログ ボックス]</span><span class="sxs-lookup"><span data-stu-id="9b5db-201">he New Project dialog box]</span></span>(iteration-2-make-the-application-look-nice-vb/_static/image8.jpg)](iteration-2-make-the-application-look-nice-vb/_static/image15.png)

<span data-ttu-id="9b5db-202">**図 08**:選択し、タブを選択解除 ([フルサイズの画像を表示する をクリックします](iteration-2-make-the-application-look-nice-vb/_static/image16.png))。</span><span class="sxs-lookup"><span data-stu-id="9b5db-202">**Figure 08**: Selected and unselected tabs([Click to view full-size image](iteration-2-make-the-application-look-nice-vb/_static/image16.png))</span></span>


<span data-ttu-id="9b5db-203">選択および選択されていない [両方] タブの表示をサポートするには、MenuItemHelper という名前のカスタム HTML ヘルパーを作成しました。</span><span class="sxs-lookup"><span data-stu-id="9b5db-203">To support rendering both selected and unselected tabs, I created a custom HTML helper named the MenuItemHelper.</span></span> <span data-ttu-id="9b5db-204">このヘルパー メソッドをレンダリングするか、 &lt;li&gt;タグまたは&lt;li クラス =「選択」&gt;タグ ヘルパーに渡されるコント ローラーとアクション名に、現在のコント ローラーとアクションが対応するかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="9b5db-204">This helper method renders either a &lt;li&gt; tag or a &lt;li class="selected"&gt; tag depending on whether the current controller and action corresponds to the controller and action name passed to the helper.</span></span> <span data-ttu-id="9b5db-205">リスト 1 で、MenuItemHelper のコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9b5db-205">The code for the MenuItemHelper is contained in Listing 1.</span></span>

**<span data-ttu-id="9b5db-206">1 – Helpers\MenuItemHelper.vb を一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-206">Listing 1 – Helpers\MenuItemHelper.vb</span></span>**

[!code-vb[Main](iteration-2-make-the-application-look-nice-vb/samples/sample3.vb)]

<span data-ttu-id="9b5db-207">MenuItemHelper TagBuilder クラスで、内部的にを使用してビルド、 &lt;li&gt; HTML タグ。</span><span class="sxs-lookup"><span data-stu-id="9b5db-207">The MenuItemHelper uses the TagBuilder class internally to build the &lt;li&gt; HTML tag.</span></span> <span data-ttu-id="9b5db-208">TagBuilder クラスは、新しい HTML タグを構築する必要がある場合に使用できる非常に便利なユーティリティ クラスです。</span><span class="sxs-lookup"><span data-stu-id="9b5db-208">The TagBuilder class is a very useful utility class that you can use whenever you need to build up a new HTML tag.</span></span> <span data-ttu-id="9b5db-209">属性の追加、CSS クラスの追加、Id を生成するおよび s タグを変更するためのメソッドを含む内部 HTML。</span><span class="sxs-lookup"><span data-stu-id="9b5db-209">It includes methods for adding attributes, adding CSS classes, generating Ids, and modifying the tag s inner HTML.</span></span>

## <a name="summary"></a><span data-ttu-id="9b5db-210">まとめ</span><span class="sxs-lookup"><span data-stu-id="9b5db-210">Summary</span></span>

<span data-ttu-id="9b5db-211">このイテレーションでは、ASP.NET MVC アプリケーションのビジュアル デ ザインを改善しました。</span><span class="sxs-lookup"><span data-stu-id="9b5db-211">In this iteration, we improved the visual design of our ASP.NET MVC application.</span></span> <span data-ttu-id="9b5db-212">最初に、ASP.NET MVC のデザインを紹介しました。</span><span class="sxs-lookup"><span data-stu-id="9b5db-212">First, you were introduced to the ASP.NET MVC Design Gallery.</span></span> <span data-ttu-id="9b5db-213">ASP.NET MVC アプリケーションで使用できる ASP.NET MVC デザイン ギャラリーから無料のデザイン テンプレートをダウンロードする方法を学習しました。</span><span class="sxs-lookup"><span data-stu-id="9b5db-213">You learned how to download free design templates from the ASP.NET MVC Design Gallery that you can use in your ASP.NET MVC applications.</span></span>

<span data-ttu-id="9b5db-214">次に、既定のカスケード スタイル シート ファイルおよびマスター ビュー ページ ファイルを変更してカスタム デザインを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-214">Next, we discussed how you can create a custom design by modifying the default cascading style sheet file and master view page file.</span></span> <span data-ttu-id="9b5db-215">新しい設計をサポートするためには、Contact Manager アプリケーションをわずかな変更を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="9b5db-215">In order to support the new design, we had to make some minor changes to our Contact Manager application.</span></span> <span data-ttu-id="9b5db-216">たとえば、MenuItemHelper 選択および選択されていない タブを表示するという名前の新しい HTML ヘルパーを追加します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-216">For example, we added a new HTML helper named the MenuItemHelper that displays selected and unselected tabs.</span></span>

<span data-ttu-id="9b5db-217">次のイテレーションで検証の非常に重要なサブジェクトに取り組みます。</span><span class="sxs-lookup"><span data-stu-id="9b5db-217">In the next iteration, we tackle the very important subject of validation.</span></span> <span data-ttu-id="9b5db-218">ユーザーがまず個人 s などの必要な値を指定せずに新しい連絡先を作成して、姓、名ことはできませんようににそのアプリケーションに検証コードを追加します。</span><span class="sxs-lookup"><span data-stu-id="9b5db-218">We add validation code to our application so that a user cannot create a new contact without supplying required values such as a person s first and last name.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="9b5db-219">[前へ](iteration-1-create-the-application-vb.md)
> [次へ](iteration-3-add-form-validation-vb.md)</span><span class="sxs-lookup"><span data-stu-id="9b5db-219">[Previous](iteration-1-create-the-application-vb.md)
[Next](iteration-3-add-form-validation-vb.md)</span></span>
