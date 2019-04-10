---
uid: web-pages/overview/performance-and-traffic/14-analyzing-traffic
title: ASP.NET Web Pages (Razor) サイトの訪問者の情報 (分析) の追跡 |Microsoft Docs
author: Rick-Anderson
description: Web サイトから取得した後、web サイト トラフィックを分析する可能性があります。
ms.author: riande
ms.date: 02/17/2014
ms.assetid: 360bc6e1-84c5-4b8e-a84c-ea48ab807aa4
msc.legacyurl: /web-pages/overview/performance-and-traffic/14-analyzing-traffic
msc.type: authoredcontent
ms.openlocfilehash: a99ed5cc8875ef9f39234e3f394b46b5782d0bc1
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59390221"
---
# <a name="tracking-visitor-information-analytics-for-an-aspnet-web-pages-razor-site"></a><span data-ttu-id="b866a-103">ASP.NET Web Pages (Razor) サイトの訪問者情報 (分析) の追跡</span><span class="sxs-lookup"><span data-stu-id="b866a-103">Tracking Visitor Information (Analytics) for an ASP.NET Web Pages (Razor) Site</span></span>

<span data-ttu-id="b866a-104">によって[Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="b866a-104">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="b866a-105">この記事では、ヘルパーを使用して ASP.NET Web Pages (Razor) の web サイトのページに web サイト分析を追加する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="b866a-105">This article describes how to use a helper to add website analytics to pages in an ASP.NET Web Pages (Razor) website.</span></span>
> 
> <span data-ttu-id="b866a-106">学習内容。</span><span class="sxs-lookup"><span data-stu-id="b866a-106">What you'll learn:</span></span>
> 
> - <span data-ttu-id="b866a-107">分析プロバイダーに、web サイト トラフィックに関する情報を送信する方法。</span><span class="sxs-lookup"><span data-stu-id="b866a-107">How to send information about your website traffic to an analytics provider.</span></span>
> 
> <span data-ttu-id="b866a-108">この記事で導入された機能のプログラミング、ASP.NET を次に示します。</span><span class="sxs-lookup"><span data-stu-id="b866a-108">These are the ASP.NET programming features introduced in the article:</span></span>
> 
> - <span data-ttu-id="b866a-109">`Analytics`ヘルパー。</span><span class="sxs-lookup"><span data-stu-id="b866a-109">The `Analytics` helper.</span></span>
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="b866a-110">このチュートリアルで使用されるソフトウェアのバージョン</span><span class="sxs-lookup"><span data-stu-id="b866a-110">Software versions used in the tutorial</span></span>
> 
> 
> - <span data-ttu-id="b866a-111">ASP.NET Web Pages (Razor) 2</span><span class="sxs-lookup"><span data-stu-id="b866a-111">ASP.NET Web Pages (Razor) 2</span></span>
> - <span data-ttu-id="b866a-112">ASP.NET Web Helpers Library (NuGet パッケージ)</span><span class="sxs-lookup"><span data-stu-id="b866a-112">ASP.NET Web Helpers Library (NuGet package)</span></span>


<span data-ttu-id="b866a-113">Analytics は、ユーザーが、サイトを使用する方法を理解できるように、web サイト上のトラフィックを測定するテクノロジの一般的な用語です。</span><span class="sxs-lookup"><span data-stu-id="b866a-113">Analytics is a general term for technology that measures traffic on your website so you can understand how people use the site.</span></span> <span data-ttu-id="b866a-114">多くの分析サービスは、使用可能な Google、Yahoo、StatCounter、およびその他のユーザーからサービスを含みます。</span><span class="sxs-lookup"><span data-stu-id="b866a-114">Many analytics services are available, including services from Google, Yahoo, StatCounter, and others.</span></span>

<span data-ttu-id="b866a-115">追跡することへのサインアップ、アカウント分析プロバイダーは、サイトを登録することはように分析が必要。プロバイダーでは、ID またはアカウントのコードの追跡が含まれる JavaScript コードのスニペットを送信します。</span><span class="sxs-lookup"><span data-stu-id="b866a-115">The way analytics works is that you sign up for an account with the analytics provider, where you register the site that you want to track. The provider sends you a snippet of JavaScript code that includes an ID or tracking code for your account.</span></span> <span data-ttu-id="b866a-116">JavaScript のスニペットを追跡しサイト上の web ページに追加します。(通常、スニペットを追加する analytics フッターまたはレイアウト ページまたはサイト内の各ページに表示されるその他の HTML マークアップ。)ユーザーは、これらの JavaScript スニペットのいずれかを含むページを要求するときに、スニペットは分析プロバイダーは、ページのさまざまな詳細を記録するに、現在のページに関する情報を送信します。</span><span class="sxs-lookup"><span data-stu-id="b866a-116">You add the JavaScript snippet to the web pages on the site that you want to track. (You typically add the analytics snippet to a footer or layout page or other HTML markup that appears on every page in your site.) When users request a page that contains one of these JavaScript snippets, the snippet sends information about the current page to the analytics provider, who records various details about the page.</span></span>

<span data-ttu-id="b866a-117">サイトの統計情報を確認する場合は、分析プロバイダーの web サイトにログインします。</span><span class="sxs-lookup"><span data-stu-id="b866a-117">When you want to have a look at your site statistics, you log into the analytics provider's website.</span></span> <span data-ttu-id="b866a-118">ように、サイトに関する、あらゆる種類のレポートを表示できます。</span><span class="sxs-lookup"><span data-stu-id="b866a-118">You can then view all sorts of reports about your site, like:</span></span>

- <span data-ttu-id="b866a-119">個々 のページのページ ビューの数。</span><span class="sxs-lookup"><span data-stu-id="b866a-119">The number of page views for individual pages.</span></span> <span data-ttu-id="b866a-120">これは、(ほぼ) 多くの人たちが、サイトにアクセスして、サイトでは、どのページが最も人気のあります。</span><span class="sxs-lookup"><span data-stu-id="b866a-120">This tells you (roughly) how many people are visiting the site, and which pages on your site are the most popular.</span></span>
- <span data-ttu-id="b866a-121">どのくらいの期間のユーザーは、特定のページに費やしています。</span><span class="sxs-lookup"><span data-stu-id="b866a-121">How long people spend on specific pages.</span></span> <span data-ttu-id="b866a-122">ホーム ページがユーザーの関心を維持するかどうかなどが確認できます。</span><span class="sxs-lookup"><span data-stu-id="b866a-122">This can tell you things like whether your home page is keeping people's interest.</span></span>
- <span data-ttu-id="b866a-123">どのようなサイトのユーザーは、サイトを訪問する前ででした。</span><span class="sxs-lookup"><span data-stu-id="b866a-123">What sites people were on before they visited your site.</span></span> <span data-ttu-id="b866a-124">これにより、検索、およびなどからのリンクから、トラフィックが送信されるかどうかを理解することができます。</span><span class="sxs-lookup"><span data-stu-id="b866a-124">This helps you understand whether your traffic is coming from links, from searches, and so on.</span></span>
- <span data-ttu-id="b866a-125">ユーザーが、サイトを訪問するときと期間します。</span><span class="sxs-lookup"><span data-stu-id="b866a-125">When people visit your site and how long they stay.</span></span>
- <span data-ttu-id="b866a-126">どのような国の利用者はからです。</span><span class="sxs-lookup"><span data-stu-id="b866a-126">What countries your visitors are from.</span></span>
- <span data-ttu-id="b866a-127">どのようなブラウザーとオペレーティング システム、訪問者が使用しています。</span><span class="sxs-lookup"><span data-stu-id="b866a-127">What browsers and operating systems your visitors are using.</span></span>

    ![Ch14traffic 1](14-analyzing-traffic/_static/image1.jpg)

## <a name="using-a-helper-to-add-analytics-to-a-page"></a><span data-ttu-id="b866a-129">ヘルパーを使用して分析ページを追加するには</span><span class="sxs-lookup"><span data-stu-id="b866a-129">Using a Helper to Add Analytics to a Page</span></span>

<span data-ttu-id="b866a-130">ASP.NET Web ページには、いくつかの analytics ヘルパーが含まれています (`Analytics.GetGoogleHtml`、 `Analytics.GetYahooHtml`、および`Analytics.GetStatCounterHtml`) を簡単に分析するための JavaScript スニペットを管理します。</span><span class="sxs-lookup"><span data-stu-id="b866a-130">ASP.NET Web Pages includes several analytics helpers (`Analytics.GetGoogleHtml`, `Analytics.GetYahooHtml`, and `Analytics.GetStatCounterHtml`) that make it easy to manage the JavaScript snippets used for analytics.</span></span> <span data-ttu-id="b866a-131">方法と場所を把握するのではなく、JavaScript コードを配置する行う必要があるすべてがページに、ヘルパーを追加します。</span><span class="sxs-lookup"><span data-stu-id="b866a-131">Instead of figuring out how and where to put the JavaScript code, all you have to do is add the helper to a page.</span></span> <span data-ttu-id="b866a-132">唯一の情報を提供する必要があるは、アカウント名、ID、または追跡コードです。</span><span class="sxs-lookup"><span data-stu-id="b866a-132">The only information you need to provide is your account name, ID, or tracking code.</span></span> <span data-ttu-id="b866a-133">(StatCounter、するも指定する必要あります、いくつか追加の値。)</span><span class="sxs-lookup"><span data-stu-id="b866a-133">(For StatCounter, you also have to provide a few additional values.)</span></span>

<span data-ttu-id="b866a-134">この手順では、使用するレイアウト ページを作成します、`GetGoogleHtml`ヘルパー。</span><span class="sxs-lookup"><span data-stu-id="b866a-134">In this procedure, you'll create a layout page that uses the `GetGoogleHtml` helper.</span></span> <span data-ttu-id="b866a-135">その他の分析プロバイダーのいずれかのアカウントを既にがある場合は、代わりにそのアカウントを使用し、必要に応じて、若干の調整を行います。</span><span class="sxs-lookup"><span data-stu-id="b866a-135">If you already have an account with one of the other analytics providers, you can use that account instead and make slight adjustments as needed.</span></span>

> [!NOTE]
> <span data-ttu-id="b866a-136">Analytics アカウントを作成するときにをトラッキングするサイトの URL を登録します。</span><span class="sxs-lookup"><span data-stu-id="b866a-136">When you create an analytics account, you register the URL of the site that you want to be tracking.</span></span> <span data-ttu-id="b866a-137">(トラフィックのみユーザーは)、実際のトラフィックが追跡するされない場合は、ローカル コンピューター上のすべてをテストするため、サイトの統計情報を記録および表示することはできません。</span><span class="sxs-lookup"><span data-stu-id="b866a-137">If you're testing everything on your local computer, you won't be tracking actual traffic (the only traffic is you), so you won't be able to record and view site statistics.</span></span> <span data-ttu-id="b866a-138">この手順は、ページを analytics ヘルパーを追加する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="b866a-138">But this procedure shows how you add an analytics helper to a page.</span></span> <span data-ttu-id="b866a-139">サイトを発行するときに、ライブ サイトは、分析プロバイダーに情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="b866a-139">When you publish your site, the live site will send information to your analytics provider.</span></span>


1. <span data-ttu-id="b866a-140">」の説明に従って、web サイトに、ASP.NET Web Helpers Library を追加[ASP.NET Web ページ サイトでインストールするヘルパー](https://go.microsoft.com/fwlink/?LinkId=252372)、既に追加していない場合。</span><span class="sxs-lookup"><span data-stu-id="b866a-140">Add the ASP.NET Web Helpers Library to your website as described in [Installing Helpers in an ASP.NET Web Pages Site](https://go.microsoft.com/fwlink/?LinkId=252372), if you haven't already added it.</span></span>
2. <span data-ttu-id="b866a-141">Google アナリティクスでアカウントを作成し、アカウント名を記録します。</span><span class="sxs-lookup"><span data-stu-id="b866a-141">Create an account with Google Analytics and record the account name.</span></span>
3. <span data-ttu-id="b866a-142">という名前のレイアウト ページを作成する*Analytics.cshtml*し、次のマークアップを追加します。</span><span class="sxs-lookup"><span data-stu-id="b866a-142">Create a layout page named *Analytics.cshtml* and add the following markup:</span></span>

    [!code-cshtml[Main](14-analyzing-traffic/samples/sample1.cshtml)]

    > [!NOTE]
    > <span data-ttu-id="b866a-143">呼び出しを配置する必要があります、 `Analytics` 、web ページの本文でヘルパー (の前に、`</body>`タグ)。</span><span class="sxs-lookup"><span data-stu-id="b866a-143">You must place the call to the `Analytics` helper in the body of your web page (before the `</body>` tag).</span></span> <span data-ttu-id="b866a-144">それ以外の場合、ブラウザーでは、スクリプトは実行されません。</span><span class="sxs-lookup"><span data-stu-id="b866a-144">Otherwise, the browser will not run the script.</span></span>

    <span data-ttu-id="b866a-145">別の分析プロバイダーを使用している場合は、代わりに使用のヘルパーを次のいずれか。</span><span class="sxs-lookup"><span data-stu-id="b866a-145">If you're using a different analytics provider, use one of the following helpers instead:</span></span>

    - <span data-ttu-id="b866a-146">(Yahoo)</span><span class="sxs-lookup"><span data-stu-id="b866a-146">(Yahoo)</span></span> `@Analytics.GetYahooHtml("myaccount")`
    - <span data-ttu-id="b866a-147">(StatCounter)</span><span class="sxs-lookup"><span data-stu-id="b866a-147">(StatCounter)</span></span> `@Analytics.GetStatCounterHtml("project", "security")`
4. <span data-ttu-id="b866a-148">置換`myaccount`アカウント、ID、または手順 1. で作成した追跡コードの名前に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b866a-148">Replace `myaccount` with the name of the account, ID, or tracking code that you created in step 1.</span></span>
5. <span data-ttu-id="b866a-149">ブラウザーでページを実行します。</span><span class="sxs-lookup"><span data-stu-id="b866a-149">Run the page in the browser.</span></span> <span data-ttu-id="b866a-150">(内でページが選択されていることを確認、**ファイル**ワークスペースを実行する前にします)。</span><span class="sxs-lookup"><span data-stu-id="b866a-150">(Make sure the page is selected in the **Files** workspace before you run it.)</span></span>
6. <span data-ttu-id="b866a-151">ブラウザーでページ ソースを表示します。</span><span class="sxs-lookup"><span data-stu-id="b866a-151">In the browser, view the page source.</span></span> <span data-ttu-id="b866a-152">レンダリングされた分析コードを表示することができます。</span><span class="sxs-lookup"><span data-stu-id="b866a-152">You'll be able to see the rendered analytics code:</span></span>

    [!code-html[Main](14-analyzing-traffic/samples/sample2.html)]
7. <span data-ttu-id="b866a-153">Google アナリティクスのサイトにログオンし、サイトの統計を確認します。</span><span class="sxs-lookup"><span data-stu-id="b866a-153">Log onto the Google Analytics site and examine the statistics for your site.</span></span> <span data-ttu-id="b866a-154">ライブ サイトでページを実行している場合、ページへのアクセス ログに記録するエントリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b866a-154">If you're running the page on a live site, you see an entry that logs the visit to your page.</span></span>

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a><span data-ttu-id="b866a-155">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="b866a-155">Additional Resources</span></span>

- [<span data-ttu-id="b866a-156">Google Analytics サイト</span><span class="sxs-lookup"><span data-stu-id="b866a-156">Google Analytics site</span></span>](https://www.google.com/analytics/)
- [<span data-ttu-id="b866a-157">Yahoo!</span><span class="sxs-lookup"><span data-stu-id="b866a-157">Yahoo!</span></span> <span data-ttu-id="b866a-158">サイトを Web Analytics</span><span class="sxs-lookup"><span data-stu-id="b866a-158">Web Analytics site</span></span>](http://help.yahoo.com/l/us/yahoo/ywa/)
- [<span data-ttu-id="b866a-159">StatCounter サイト</span><span class="sxs-lookup"><span data-stu-id="b866a-159">StatCounter site</span></span>](http://statcounter.com/)
