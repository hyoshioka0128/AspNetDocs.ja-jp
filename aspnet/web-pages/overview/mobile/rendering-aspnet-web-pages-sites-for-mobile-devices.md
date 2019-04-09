---
uid: web-pages/overview/mobile/rendering-aspnet-web-pages-sites-for-mobile-devices
title: モバイル デバイスのページ (Razor) サイトを ASP.NET Web の表示 |Microsoft Docs
author: Rick-Anderson
description: この記事では、モバイル デバイスで適切にレンダリングする ASP.NET Web Pages (Razor) サイトでページを作成する方法について説明します。 学習内容。する方法.
ms.author: riande
ms.date: 02/17/2014
ms.assetid: f15ab392-c05e-4269-83bf-7c6d2b8c8ec8
msc.legacyurl: /web-pages/overview/mobile/rendering-aspnet-web-pages-sites-for-mobile-devices
msc.type: authoredcontent
ms.openlocfilehash: dbcd25331387f8606343e551302bc3ed1f9b2c25
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59379509"
---
# <a name="rendering-aspnet-web-pages-razor-sites-for-mobile-devices"></a><span data-ttu-id="8620e-104">モバイル デバイス用の ASP.NET Web Pages (Razor) サイトの表示</span><span class="sxs-lookup"><span data-stu-id="8620e-104">Rendering ASP.NET Web Pages (Razor) Sites for Mobile Devices</span></span>

<span data-ttu-id="8620e-105">によって[Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="8620e-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="8620e-106">この記事では、モバイル デバイスで適切にレンダリングする ASP.NET Web Pages (Razor) サイトでページを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="8620e-106">This article describes how to create pages in an ASP.NET Web Pages (Razor) site that will render appropriately on mobile devices.</span></span>
> 
> <span data-ttu-id="8620e-107">学習内容。</span><span class="sxs-lookup"><span data-stu-id="8620e-107">What you'll learn:</span></span>
> 
> - <span data-ttu-id="8620e-108">名前付け規則を使用して、ページが特にモバイル デバイス向けに設計されているを指定する方法。</span><span class="sxs-lookup"><span data-stu-id="8620e-108">How to use a naming convention to specify that a page is designed specifically for mobile devices.</span></span>
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="8620e-109">このチュートリアルで使用されるソフトウェアのバージョン</span><span class="sxs-lookup"><span data-stu-id="8620e-109">Software versions used in the tutorial</span></span>
> 
> 
> - <span data-ttu-id="8620e-110">ASP.NET Web Pages (Razor) 3</span><span class="sxs-lookup"><span data-stu-id="8620e-110">ASP.NET Web Pages (Razor) 3</span></span>
>   
> 
> <span data-ttu-id="8620e-111">このチュートリアルは、ASP.NET Web Pages 2 でも機能します。</span><span class="sxs-lookup"><span data-stu-id="8620e-111">This tutorial also works with ASP.NET Web Pages 2.</span></span>


<span data-ttu-id="8620e-112">ASP.NET Web ページを使用して、モバイル デバイスまたはその他のデバイスでコンテンツの描画のカスタムの表示を作成できます。</span><span class="sxs-lookup"><span data-stu-id="8620e-112">ASP.NET Web Pages lets you create custom displays for rendering content on mobile or other devices.</span></span>

<span data-ttu-id="8620e-113">ASP.NET Web Pages サイトでデバイスに固有のページを作成する最も簡単な方法では、このようなファイルの名前付けパターンを使用しています。*FileName.Mobile.cshtml*します。</span><span class="sxs-lookup"><span data-stu-id="8620e-113">The simplest way to create device-specific page in an ASP.NET Web Pages site is by using a file-naming pattern like this: *FileName.Mobile.cshtml*.</span></span> <span data-ttu-id="8620e-114">ページの 2 つのバージョンを作成することができます (たとえば、1 つという名前*MyFile.cshtml*という名前の 1 つ*MyFile.Mobile.cshtml*)。</span><span class="sxs-lookup"><span data-stu-id="8620e-114">You can create two versions of a page (for example, one named *MyFile.cshtml* and one named *MyFile.Mobile.cshtml*).</span></span> <span data-ttu-id="8620e-115">実行時に、モバイル デバイスを要求したとき*MyFile.cshtml*、ASP.NET からコンテンツをレンダリングする*MyFile.Mobile.cshtml*します。</span><span class="sxs-lookup"><span data-stu-id="8620e-115">At run time, when a mobile device requests *MyFile.cshtml*, ASP.NET renders the content from *MyFile.Mobile.cshtml*.</span></span> <span data-ttu-id="8620e-116">それ以外の場合、 *MyFile.cshtml*を表示します。</span><span class="sxs-lookup"><span data-stu-id="8620e-116">Otherwise, *MyFile.cshtml* is rendered.</span></span>

<span data-ttu-id="8620e-117">次の例では、モバイル デバイスのコンテンツ ページを追加して、モバイルのレンダリングを有効にする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="8620e-117">The following example shows how to enable mobile rendering by adding a content page for mobile devices.</span></span> <span data-ttu-id="8620e-118">*Page1.cshtml*コンテンツにはナビゲーション サイド バーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8620e-118">*Page1.cshtml* contains content plus a navigation sidebar.</span></span> <span data-ttu-id="8620e-119">*Page1.Mobile.cshtml* 、同じコンテンツが含まれていますが、サイドバーが省略されます。</span><span class="sxs-lookup"><span data-stu-id="8620e-119">*Page1.Mobile.cshtml* contains the same content, but omits the sidebar.</span></span>

1. <span data-ttu-id="8620e-120">という名前のファイルを作成する ASP.NET Web Pages サイトで*Page1.cshtml*現在のコンテンツを次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="8620e-120">In an ASP.NET Web Pages site, create a file named *Page1.cshtml* and replace the current content with following markup.</span></span>

    [!code-html[Main](rendering-aspnet-web-pages-sites-for-mobile-devices/samples/sample1.html)]
2. <span data-ttu-id="8620e-121">という名前のファイルを作成する*Page1.Mobile.cshtml*既存のコンテンツを次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="8620e-121">Create a file named *Page1.Mobile.cshtml* and replace the existing content with the following markup.</span></span> <span data-ttu-id="8620e-122">モバイル バージョンのページが小さい画面できれいに表示のナビゲーション セクションを省略することに注意してください。</span><span class="sxs-lookup"><span data-stu-id="8620e-122">Notice that the mobile version of the page omits the navigation section for better rendering on a smaller screen.</span></span>

    [!code-html[Main](rendering-aspnet-web-pages-sites-for-mobile-devices/samples/sample2.html)]
3. <span data-ttu-id="8620e-123">デスクトップ ブラウザーを実行しを参照*Page1.cshtml*します。</span><span class="sxs-lookup"><span data-stu-id="8620e-123">Run a desktop browser and browse to *Page1.cshtml*.</span></span> ![mobilesites 1](rendering-aspnet-web-pages-sites-for-mobile-devices/_static/image1.png)
4. <span data-ttu-id="8620e-125">モバイル ブラウザー (または、モバイル デバイス エミュレーター) を実行しを参照*Page1.cshtml*します。</span><span class="sxs-lookup"><span data-stu-id="8620e-125">Run a mobile browser (or a mobile device emulator) and browse to *Page1.cshtml*.</span></span> <span data-ttu-id="8620e-126">(通知を組み込んでいない *.mobile します。*</span><span class="sxs-lookup"><span data-stu-id="8620e-126">(Notice that you do not include *.mobile.*</span></span> <span data-ttu-id="8620e-127">URL の一部として、します。)要求されている場合でも*Page1.cshtml*、ASP.NET のレンダリング*Page1.Mobile.cshtml*します。</span><span class="sxs-lookup"><span data-stu-id="8620e-127">as part of the URL.) Even though the request is to *Page1.cshtml*, ASP.NET renders *Page1.Mobile.cshtml*.</span></span>

    ![mobilesites 2](rendering-aspnet-web-pages-sites-for-mobile-devices/_static/image2.png)

> [!NOTE]
> <span data-ttu-id="8620e-129">モバイル ページをテストするには、デスクトップ コンピューターで実行されているモバイル デバイス シミュレーターを使用できます。</span><span class="sxs-lookup"><span data-stu-id="8620e-129">To test mobile pages, you can use a mobile device simulator that runs on a desktop computer.</span></span> <span data-ttu-id="8620e-130">このツールを使用して、モバイル デバイスでになりますが、web ページをテストできます (つまり、通常ははるかに小さいで領域を表示)。</span><span class="sxs-lookup"><span data-stu-id="8620e-130">This tool lets you test web pages as they would look on mobile devices (that is, typically with a much smaller display area).</span></span> <span data-ttu-id="8620e-131">シミュレーターの 1 つの例は、[ユーザー エージェント切り替えアドオン](http://addons.mozilla.org/firefox/addon/user-agent-switcher/)、Mozilla firefox に対応することができます Firefox のデスクトップ バージョンからのさまざまなモバイル ブラウザーをエミュレートします。</span><span class="sxs-lookup"><span data-stu-id="8620e-131">One example of a simulator is the [User Agent Switcher add-on](http://addons.mozilla.org/firefox/addon/user-agent-switcher/) for Mozilla Firefox, which lets you emulate various mobile browsers from a desktop version of Firefox.</span></span>


<a id="Additional_Resources"></a>
## <a name="additional-resources"></a><span data-ttu-id="8620e-132">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="8620e-132">Additional Resources</span></span>


[<span data-ttu-id="8620e-133">Windows Phone エミュレーター</span><span class="sxs-lookup"><span data-stu-id="8620e-133">Windows Phone Emulator</span></span>](https://msdn.microsoft.com/library/ff402563(v=VS.92).aspx)
