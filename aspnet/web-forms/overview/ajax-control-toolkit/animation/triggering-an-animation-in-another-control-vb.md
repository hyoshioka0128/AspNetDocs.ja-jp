---
uid: web-forms/overview/ajax-control-toolkit/animation/triggering-an-animation-in-another-control-vb
title: アニメーションをトリガーするもう 1 つコントロール (VB) |Microsoft Docs
author: wenz
description: アニメーション コントロール、ASP.NET AJAX Control Toolkit ではなくコントロールだけをコントロールにアニメーションを追加するために全体のフレームワークです。 一般に、起動する.
ms.author: riande
ms.date: 06/02/2008
ms.assetid: 25ebaf1f-5a9f-423d-98c7-1d694e93664f
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/animation/triggering-an-animation-in-another-control-vb
msc.type: authoredcontent
ms.openlocfilehash: b390ebfaec695a1c2565a91acaa38dbcc10a8959
ms.sourcegitcommit: 289e051cc8a90e8f7127e239fda73047bde4de12
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58425796"
---
<a name="triggering-an-animation-in-another-control-vb"></a>別のコントロールでアニメーションをトリガーする (VB)
====================
によって[Christian Wenz](https://github.com/wenz)

[コードのダウンロード](http://download.microsoft.com/download/f/9/a/f9a26acd-8df4-4484-8a18-199e4598f411/Animation8.vb.zip)または[PDF のダウンロード](http://download.microsoft.com/download/6/7/1/6718d452-ff89-4d3f-a90e-c74ec2d636a3/animation8VB.pdf)

> アニメーション コントロール、ASP.NET AJAX Control Toolkit ではなくコントロールだけをコントロールにアニメーションを追加するために全体のフレームワークです。 一般に、アニメーションの起動は、同じコントロールでのユーザー操作によってトリガーされます。 ただしも別のコントロールを 1 つのコントロールと、アニメーションと対話することです。


## <a name="overview"></a>概要

アニメーション コントロール、ASP.NET AJAX Control Toolkit ではなくコントロールだけをコントロールにアニメーションを追加するために全体のフレームワークです。 一般に、アニメーションの起動は、同じコントロールでのユーザー操作によってトリガーされます。 ただしも別のコントロールを 1 つのコントロールと、アニメーションと対話することです。

## <a name="steps"></a>手順

まず、含める、 `ScriptManager` ; ページで次に、ASP.NET AJAX ライブラリが読み込まれる Control Toolkit を使用すること。

[!code-aspx[Main](triggering-an-animation-in-another-control-vb/samples/sample1.aspx)]

次のようなテキストのパネルに、アニメーションが適用されます。

[!code-aspx[Main](triggering-an-animation-in-another-control-vb/samples/sample2.aspx)]

パネルの関連付けられている CSS クラス、便利な背景色を定義し、パネルの固定幅の設定も。

[!code-css[Main](triggering-an-animation-in-another-control-vb/samples/sample3.css)]

パネルをアニメーション化を開始するには、HTML ボタンが使用されます。 なお`<input type="button" />`をお勧め`<asp:Button />`ためたくないポストバック、ユーザーがそのボタンをクリックするとにします。

[!code-aspx[Main](triggering-an-animation-in-another-control-vb/samples/sample4.aspx)]

次に、追加、 `AnimationExtender` 、ページを提供する、 `ID`、`TargetControlID`属性と、変更を加える`runat="server"`。 設定することが重要`TargetControlID`ボタン (アニメーションをトリガーする要素) の ID に、パネル (アニメーション化されている要素) の ID にありません

[!code-aspx[Main](triggering-an-animation-in-another-control-vb/samples/sample5.aspx)]

内で、`<Animations>`ノードで、通常どおりの場所のアニメーション。 パネルを変更するには、するには、ボタンの設定、`AnimationTarget`内のすべてのアニメーション要素の属性`AnimationExtender`します。 値は、`AnimationTarget`パネルの ID をもちろんです。 そうすること、アニメーションにトリガーを起動するボタンではなく、パネルで発生します。 ここでは、`AnimationExtender`このシナリオのマークアップ。

[!code-aspx[Main](triggering-an-animation-in-another-control-vb/samples/sample6.aspx)]

個々 のアニメーションを表示する特別な順序に注意してください。 まず、アニメーションの実行後に、ボタンが非アクティブ化を取得します。 あるためありません`AnimationTarget`属性、`<EnableAction>`要素をこのアニメーションは、元のコントロールに適用される: ボタンをクリックします。 次の 2 つのアニメーションのステップを並列で実行されます (`<Parallel>`要素)。 両方が、`AnimationTarget`属性に設定`"Panel1"`、そのため、パネル、[not] ボタンをアニメーション化します。


[![パネル アニメーションを開始するボタンにマウスのクリック](triggering-an-animation-in-another-control-vb/_static/image2.png)](triggering-an-animation-in-another-control-vb/_static/image1.png)

ボタンをマウスのクリックは、パネルのアニメーションを開始 ([フルサイズの画像を表示する をクリックします](triggering-an-animation-in-another-control-vb/_static/image3.png))。

> [!div class="step-by-step"]
> [前へ](disabling-actions-during-animation-vb.md)
> [次へ](modifying-animations-from-the-server-side-vb.md)
