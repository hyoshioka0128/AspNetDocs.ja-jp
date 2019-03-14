---
uid: web-forms/overview/ajax-control-toolkit/animation/dynamically-controlling-updatepanel-animations-vb
title: UpdatePanel アニメーション (VB) を動的に制御する |Microsoft Docs
author: wenz
description: アニメーション コントロール、ASP.NET AJAX Control Toolkit ではなくコントロールだけをコントロールにアニメーションを追加するために全体のフレームワークです。 内容として、.
ms.author: riande
ms.date: 06/02/2008
ms.assetid: bea66072-59b6-42b4-98fa-211812f5925f
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/animation/dynamically-controlling-updatepanel-animations-vb
msc.type: authoredcontent
ms.openlocfilehash: 2a4c01ffdee20f2c7970d999b34bf1374088d4c5
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57053669"
---
<a name="dynamically-controlling-updatepanel-animations-vb"></a><span data-ttu-id="485aa-104">UpdatePanel アニメーションを動的に制御する (VB)</span><span class="sxs-lookup"><span data-stu-id="485aa-104">Dynamically Controlling UpdatePanel Animations (VB)</span></span>
====================
<span data-ttu-id="485aa-105">によって[Christian Wenz](https://github.com/wenz)</span><span class="sxs-lookup"><span data-stu-id="485aa-105">by [Christian Wenz](https://github.com/wenz)</span></span>

<span data-ttu-id="485aa-106">[コードのダウンロード](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/UpdatePanelAnimation2.vb.zip)または[PDF のダウンロード](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/updatepanelanimation2VB.pdf)</span><span class="sxs-lookup"><span data-stu-id="485aa-106">[Download Code](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/UpdatePanelAnimation2.vb.zip) or [Download PDF](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/updatepanelanimation2VB.pdf)</span></span>

> <span data-ttu-id="485aa-107">アニメーション コントロール、ASP.NET AJAX Control Toolkit ではなくコントロールだけをコントロールにアニメーションを追加するために全体のフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="485aa-107">The Animation control in the ASP.NET AJAX Control Toolkit is not just a control but a whole framework to add animations to a control.</span></span> <span data-ttu-id="485aa-108">UpdatePanel の内容は、の特別なエクステンダーには、アニメーション フレームワークに大きく依存しているが存在します。UpdatePanelAnimation します。</span><span class="sxs-lookup"><span data-stu-id="485aa-108">For the contents of an UpdatePanel, a special extender exists that relies heavily on the animation framework: UpdatePanelAnimation.</span></span> <span data-ttu-id="485aa-109">これは UpdatePanel トリガーと共にも使用できます。</span><span class="sxs-lookup"><span data-stu-id="485aa-109">It can also work together with UpdatePanel triggers.</span></span>


## <a name="overview"></a><span data-ttu-id="485aa-110">概要</span><span class="sxs-lookup"><span data-stu-id="485aa-110">Overview</span></span>

<span data-ttu-id="485aa-111">アニメーション コントロール、ASP.NET AJAX Control Toolkit ではなくコントロールだけをコントロールにアニメーションを追加するために全体のフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="485aa-111">The Animation control in the ASP.NET AJAX Control Toolkit is not just a control but a whole framework to add animations to a control.</span></span> <span data-ttu-id="485aa-112">内容として、 `UpdatePanel`、アニメーション フレームワークに大きく依存している特別なエクステンダーが存在します:`UpdatePanelAnimation`します。</span><span class="sxs-lookup"><span data-stu-id="485aa-112">For the contents of an `UpdatePanel`, a special extender exists that relies heavily on the animation framework: `UpdatePanelAnimation`.</span></span> <span data-ttu-id="485aa-113">でも機能と共に`UpdatePanel`トリガーします。</span><span class="sxs-lookup"><span data-stu-id="485aa-113">It can also work together with `UpdatePanel` triggers.</span></span>

## <a name="steps"></a><span data-ttu-id="485aa-114">手順</span><span class="sxs-lookup"><span data-stu-id="485aa-114">Steps</span></span>

<span data-ttu-id="485aa-115">最初の手順は、通常どおりに含める、 `ScriptManager`  ページで、ASP.NET AJAX ライブラリが読み込まれ、Control Toolkit を使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="485aa-115">The first step is as usual to include the `ScriptManager` in the page so that the ASP.NET AJAX library is loaded and the Control Toolkit can be used:</span></span>


[!code-aspx[Main](dynamically-controlling-updatepanel-animations-vb/samples/sample1.aspx)]

<span data-ttu-id="485aa-116">このシナリオでは、アニメーションは、現在の時刻の表示に適用されます。</span><span class="sxs-lookup"><span data-stu-id="485aa-116">The animation in this scenario will be applied to a display of the current time.</span></span> <span data-ttu-id="485aa-117">この情報を使用して、ラベルに書き込まれることができます、`Page_Load()`メソッド、または次のインライン コードの使用 (わかりやすくする)。</span><span class="sxs-lookup"><span data-stu-id="485aa-117">This information can be written into a label using the `Page_Load()` method, or (for the sake of simplicity) the following inline code is used:</span></span>


[!code-aspx[Main](dynamically-controlling-updatepanel-animations-vb/samples/sample2.aspx)]

<span data-ttu-id="485aa-118">また、時間の更新をトリガーするボタンが作成されます。</span><span class="sxs-lookup"><span data-stu-id="485aa-118">Also, a button to trigger updating the time is created:</span></span>


[!code-aspx[Main](dynamically-controlling-updatepanel-animations-vb/samples/sample3.aspx)]

<span data-ttu-id="485aa-119">このコードに配置し、`<ContentTemplate>`のセクション、`UpdatePanel`要素。</span><span class="sxs-lookup"><span data-stu-id="485aa-119">This code is then put into the `<ContentTemplate>` section of an `UpdatePanel` element.</span></span> <span data-ttu-id="485aa-120">パネルの`UpdateMode`に属性を設定する必要があります`"Conditional"`、トリガーのみがパネルの内容を更新します。</span><span class="sxs-lookup"><span data-stu-id="485aa-120">The panel's `UpdateMode` attribute must be set to `"Conditional"`, since only triggers may update the panel's contents.</span></span> <span data-ttu-id="485aa-121">`<Triggers>`のセクション、 `UpdatePanel`、非同期ポストバックのトリガーが作成されに関連付けられている、`Click`ボタンのイベント。</span><span class="sxs-lookup"><span data-stu-id="485aa-121">In the `<Triggers>` section of the `UpdatePanel`, an asynchronous postback trigger is created and tied to the `Click` event of the button.</span></span> <span data-ttu-id="485aa-122">したがって、ユーザーが、ボタンをクリックした場合、`UpdatePanel`が更新されます。</span><span class="sxs-lookup"><span data-stu-id="485aa-122">Thus, if the user clicks on the button, the `UpdatePanel` is refreshed.</span></span> <span data-ttu-id="485aa-123">次のマークアップに示します、`UpdatePanel`コントロール。</span><span class="sxs-lookup"><span data-stu-id="485aa-123">Here is the markup for the `UpdatePanel` control:</span></span>


[!code-aspx[Main](dynamically-controlling-updatepanel-animations-vb/samples/sample4.aspx)]

<span data-ttu-id="485aa-124">最後に、`UpdatePanelAnimationExtender`構成する必要があります。設定、`TargetControlID`パネルの id 属性し、エクステンダー内でアニメーションを定義します。</span><span class="sxs-lookup"><span data-stu-id="485aa-124">Finally, the `UpdatePanelAnimationExtender` must be configured: Set the `TargetControlID` attribute to the ID of the panel, and define an animation within the extender.</span></span> <span data-ttu-id="485aa-125">フェードはある意味、更新された時間に優れた visual 重点を置いたを作成します。</span><span class="sxs-lookup"><span data-stu-id="485aa-125">Fading in makes sense, which creates a nice visual emphasis on the updated time.</span></span> <span data-ttu-id="485aa-126">エクステンダーのマークアップには可能性がありますし、このようになります。</span><span class="sxs-lookup"><span data-stu-id="485aa-126">Your extender markup may then look like this:</span></span>


[!code-aspx[Main](dynamically-controlling-updatepanel-animations-vb/samples/sample5.aspx)]

<span data-ttu-id="485aa-127">ブラウザーでファイルを実行します。</span><span class="sxs-lookup"><span data-stu-id="485aa-127">Run the file in the browser.</span></span> <span data-ttu-id="485aa-128">ボタンをクリックしたときに、常に 1 秒の間フェードのパネルで、現在の時刻が表示されます。</span><span class="sxs-lookup"><span data-stu-id="485aa-128">Whenever you click on the button, the current time is shown in the panel, always fading in for the duration of one second.</span></span>


<span data-ttu-id="485aa-129">[![現在の時刻がフェードインします。](dynamically-controlling-updatepanel-animations-vb/_static/image2.png)](dynamically-controlling-updatepanel-animations-vb/_static/image1.png)</span><span class="sxs-lookup"><span data-stu-id="485aa-129">[![The current time is fading in](dynamically-controlling-updatepanel-animations-vb/_static/image2.png)](dynamically-controlling-updatepanel-animations-vb/_static/image1.png)</span></span>

<span data-ttu-id="485aa-130">現在の時刻がフェードイン ([フルサイズの画像を表示する をクリックします](dynamically-controlling-updatepanel-animations-vb/_static/image3.png))。</span><span class="sxs-lookup"><span data-stu-id="485aa-130">The current time is fading in ([Click to view full-size image](dynamically-controlling-updatepanel-animations-vb/_static/image3.png))</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="485aa-131">前へ</span><span class="sxs-lookup"><span data-stu-id="485aa-131">Previous</span></span>](animating-an-updatepanel-control-vb.md)