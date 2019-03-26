---
uid: web-forms/overview/ajax-control-toolkit/animation/triggering-an-animation-in-another-control-cs
title: アニメーションをトリガーする、別のコントロール (c#) |Microsoft Docs
author: wenz
description: アニメーション コントロール、ASP.NET AJAX Control Toolkit ではなくコントロールだけをコントロールにアニメーションを追加するために全体のフレームワークです。 一般に、起動する.
ms.author: riande
ms.date: 06/02/2008
ms.assetid: e5d99c2b-d8ee-413c-80d5-c120cffb0a4c
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/animation/triggering-an-animation-in-another-control-cs
msc.type: authoredcontent
ms.openlocfilehash: 6216f24e497936245280f337477b287ff2afb080
ms.sourcegitcommit: 289e051cc8a90e8f7127e239fda73047bde4de12
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58421389"
---
<a name="triggering-an-animation-in-another-control-c"></a><span data-ttu-id="65e45-104">別のコントロールでアニメーションをトリガーする (C#)</span><span class="sxs-lookup"><span data-stu-id="65e45-104">Triggering an Animation in another Control (C#)</span></span>
====================
<span data-ttu-id="65e45-105">によって[Christian Wenz](https://github.com/wenz)</span><span class="sxs-lookup"><span data-stu-id="65e45-105">by [Christian Wenz](https://github.com/wenz)</span></span>

<span data-ttu-id="65e45-106">[コードのダウンロード](http://download.microsoft.com/download/f/9/a/f9a26acd-8df4-4484-8a18-199e4598f411/Animation8.cs.zip)または[PDF のダウンロード](http://download.microsoft.com/download/6/7/1/6718d452-ff89-4d3f-a90e-c74ec2d636a3/animation8CS.pdf)</span><span class="sxs-lookup"><span data-stu-id="65e45-106">[Download Code](http://download.microsoft.com/download/f/9/a/f9a26acd-8df4-4484-8a18-199e4598f411/Animation8.cs.zip) or [Download PDF](http://download.microsoft.com/download/6/7/1/6718d452-ff89-4d3f-a90e-c74ec2d636a3/animation8CS.pdf)</span></span>

> <span data-ttu-id="65e45-107">アニメーション コントロール、ASP.NET AJAX Control Toolkit ではなくコントロールだけをコントロールにアニメーションを追加するために全体のフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="65e45-107">The Animation control in the ASP.NET AJAX Control Toolkit is not just a control but a whole framework to add animations to a control.</span></span> <span data-ttu-id="65e45-108">一般に、アニメーションの起動は、同じコントロールでのユーザー操作によってトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="65e45-108">Generally, launching an animation is triggered by user interaction with the same control.</span></span> <span data-ttu-id="65e45-109">ただしも別のコントロールを 1 つのコントロールと、アニメーションと対話することです。</span><span class="sxs-lookup"><span data-stu-id="65e45-109">It is however also possible to interact with one control and then animation another control.</span></span>


## <a name="overview"></a><span data-ttu-id="65e45-110">概要</span><span class="sxs-lookup"><span data-stu-id="65e45-110">Overview</span></span>

<span data-ttu-id="65e45-111">アニメーション コントロール、ASP.NET AJAX Control Toolkit ではなくコントロールだけをコントロールにアニメーションを追加するために全体のフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="65e45-111">The Animation control in the ASP.NET AJAX Control Toolkit is not just a control but a whole framework to add animations to a control.</span></span> <span data-ttu-id="65e45-112">一般に、アニメーションの起動は、同じコントロールでのユーザー操作によってトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="65e45-112">Generally, launching an animation is triggered by user interaction with the same control.</span></span> <span data-ttu-id="65e45-113">ただしも別のコントロールを 1 つのコントロールと、アニメーションと対話することです。</span><span class="sxs-lookup"><span data-stu-id="65e45-113">It is however also possible to interact with one control and then animation another control.</span></span>

## <a name="steps"></a><span data-ttu-id="65e45-114">手順</span><span class="sxs-lookup"><span data-stu-id="65e45-114">Steps</span></span>

<span data-ttu-id="65e45-115">まず、含める、 `ScriptManager` ; ページで次に、ASP.NET AJAX ライブラリが読み込まれる Control Toolkit を使用すること。</span><span class="sxs-lookup"><span data-stu-id="65e45-115">First of all, include the `ScriptManager` in the page; then, the ASP.NET AJAX library is loaded, making it possible to use the Control Toolkit:</span></span>

[!code-aspx[Main](triggering-an-animation-in-another-control-cs/samples/sample1.aspx)]

<span data-ttu-id="65e45-116">次のようなテキストのパネルに、アニメーションが適用されます。</span><span class="sxs-lookup"><span data-stu-id="65e45-116">The animation will be applied to a panel of text which looks like this:</span></span>

[!code-aspx[Main](triggering-an-animation-in-another-control-cs/samples/sample2.aspx)]

<span data-ttu-id="65e45-117">パネルの関連付けられている CSS クラス、便利な背景色を定義し、パネルの固定幅の設定も。</span><span class="sxs-lookup"><span data-stu-id="65e45-117">In the associated CSS class for the panel, define a nice background color and also set a fixed width for the panel:</span></span>

[!code-css[Main](triggering-an-animation-in-another-control-cs/samples/sample3.css)]

<span data-ttu-id="65e45-118">パネルをアニメーション化を開始するには、HTML ボタンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="65e45-118">In order to start animating the panel, an HTML button is used.</span></span> <span data-ttu-id="65e45-119">なお`<input type="button" />`をお勧め`<asp:Button />`ためたくないポストバック、ユーザーがそのボタンをクリックするとにします。</span><span class="sxs-lookup"><span data-stu-id="65e45-119">Note that `<input type="button" />` is favoured over `<asp:Button />` since we do not want a postback when the user clicks on that button.</span></span>

[!code-aspx[Main](triggering-an-animation-in-another-control-cs/samples/sample4.aspx)]

<span data-ttu-id="65e45-120">次に、追加、 `AnimationExtender` 、ページを提供する、 `ID`、`TargetControlID`属性と、変更を加える`runat="server"`。</span><span class="sxs-lookup"><span data-stu-id="65e45-120">Then, add the `AnimationExtender` to the page, providing an `ID`, the `TargetControlID` attribute and the obligatory `runat="server"`.</span></span> <span data-ttu-id="65e45-121">設定することが重要`TargetControlID`ボタン (アニメーションをトリガーする要素) の ID に、パネル (アニメーション化されている要素) の ID にありません</span><span class="sxs-lookup"><span data-stu-id="65e45-121">It is important to set `TargetControlID` to the ID of the button (the element triggering the animation), not to the ID of the panel (the element being animated)</span></span>

[!code-aspx[Main](triggering-an-animation-in-another-control-cs/samples/sample5.aspx)]

<span data-ttu-id="65e45-122">内で、`<Animations>`ノードで、通常どおりの場所のアニメーション。</span><span class="sxs-lookup"><span data-stu-id="65e45-122">Within the `<Animations>` node, place animations as usual.</span></span> <span data-ttu-id="65e45-123">パネルを変更するには、するには、ボタンの設定、`AnimationTarget`内のすべてのアニメーション要素の属性`AnimationExtender`します。</span><span class="sxs-lookup"><span data-stu-id="65e45-123">In order to make them change the panel, not the button, set the `AnimationTarget` attribute for every animation element within `AnimationExtender`.</span></span> <span data-ttu-id="65e45-124">値は、`AnimationTarget`パネルの ID をもちろんです。</span><span class="sxs-lookup"><span data-stu-id="65e45-124">The value for `AnimationTarget` is the ID of the panel, of course.</span></span> <span data-ttu-id="65e45-125">そうすること、アニメーションにトリガーを起動するボタンではなく、パネルで発生します。</span><span class="sxs-lookup"><span data-stu-id="65e45-125">That way, the animations happen with the panel, not with the triggering button.</span></span> <span data-ttu-id="65e45-126">ここでは、`AnimationExtender`このシナリオのマークアップ。</span><span class="sxs-lookup"><span data-stu-id="65e45-126">Here is the `AnimationExtender` markup for this scenario:</span></span>

[!code-aspx[Main](triggering-an-animation-in-another-control-cs/samples/sample6.aspx)]

<span data-ttu-id="65e45-127">個々 のアニメーションを表示する特別な順序に注意してください。</span><span class="sxs-lookup"><span data-stu-id="65e45-127">Note the special order in which the individual animations appear.</span></span> <span data-ttu-id="65e45-128">まず、アニメーションの実行後に、ボタンが非アクティブ化を取得します。</span><span class="sxs-lookup"><span data-stu-id="65e45-128">First of all, the button gets deactivated once the animation runs.</span></span> <span data-ttu-id="65e45-129">あるためありません`AnimationTarget`属性、`<EnableAction>`要素をこのアニメーションは、元のコントロールに適用される: ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="65e45-129">Since there is no `AnimationTarget` attribute in the `<EnableAction>` element, this animation is applied to the originating control: the button.</span></span> <span data-ttu-id="65e45-130">次の 2 つのアニメーションのステップを並列で実行されます (`<Parallel>`要素)。</span><span class="sxs-lookup"><span data-stu-id="65e45-130">The next two animation steps shall be carried out in parallel (`<Parallel>` element).</span></span> <span data-ttu-id="65e45-131">両方が、`AnimationTarget`属性に設定`"Panel1"`、そのため、パネル、[not] ボタンをアニメーション化します。</span><span class="sxs-lookup"><span data-stu-id="65e45-131">Both have their `AnimationTarget` attributes set to `"Panel1"`, thus animating the panel, not the button.</span></span>


<span data-ttu-id="65e45-132">[![パネル アニメーションを開始するボタンにマウスのクリック](triggering-an-animation-in-another-control-cs/_static/image2.png)](triggering-an-animation-in-another-control-cs/_static/image1.png)</span><span class="sxs-lookup"><span data-stu-id="65e45-132">[![A mouse click on the button starts the panel animation](triggering-an-animation-in-another-control-cs/_static/image2.png)](triggering-an-animation-in-another-control-cs/_static/image1.png)</span></span>

<span data-ttu-id="65e45-133">ボタンをマウスのクリックは、パネルのアニメーションを開始 ([フルサイズの画像を表示する をクリックします](triggering-an-animation-in-another-control-cs/_static/image3.png))。</span><span class="sxs-lookup"><span data-stu-id="65e45-133">A mouse click on the button starts the panel animation ([Click to view full-size image](triggering-an-animation-in-another-control-cs/_static/image3.png))</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="65e45-134">[前へ](disabling-actions-during-animation-cs.md)
> [次へ](modifying-animations-from-the-server-side-cs.md)</span><span class="sxs-lookup"><span data-stu-id="65e45-134">[Previous](disabling-actions-during-animation-cs.md)
[Next](modifying-animations-from-the-server-side-cs.md)</span></span>
