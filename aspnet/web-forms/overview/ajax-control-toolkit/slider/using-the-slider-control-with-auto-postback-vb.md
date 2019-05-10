---
uid: web-forms/overview/ajax-control-toolkit/slider/using-the-slider-control-with-auto-postback-vb
title: スライダー コントロールを使用する (VB) の自動ポストバックあり |Microsoft Docs
author: wenz
description: スライダー コントロール、AJAX Control Toolkit では、マウスを使用して制御できるグラフィカルなスライダーを提供します。 スライダー自動転記を作成することはしています.
ms.author: riande
ms.date: 06/02/2008
ms.assetid: 41d1abba-97a5-4a45-9b44-d05624c19777
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/slider/using-the-slider-control-with-auto-postback-vb
msc.type: authoredcontent
ms.openlocfilehash: c4ee6642726b4209d09907f615ee3286ca00caa3
ms.sourcegitcommit: 51b01b6ff8edde57d8243e4da28c9f1e7f1962b2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65124620"
---
# <a name="using-the-slider-control-with-auto-postback-vb"></a>スライダー コントロールを使用する (VB) の自動ポストバックあり

によって[Christian Wenz](https://github.com/wenz)

[コードのダウンロード](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/Slider1.vb.zip)または[PDF のダウンロード](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/slider1VB.pdf)

> スライダー コントロール、AJAX Control Toolkit では、マウスを使用して制御できるグラフィカルなスライダーを提供します。 ようにスライダー autopostback 1 回、値が変化することになります。

## <a name="overview"></a>概要

スライダー コントロール、AJAX Control Toolkit では、マウスを使用して制御できるグラフィカルなスライダーを提供します。 ようにスライダー autopostback 1 回、値が変化することになります。

## <a name="steps"></a>手順

両方のテキスト ボックス、スライダーを変更すると自動的にポストバックするには、するには、属性が必要な`AutoPostBack="true"`:テキスト ボックス自体には、スライダーになると、テキスト ボックス、スライダーの位置を保持します。 そのための必要なマークアップを次に示します。

[!code-aspx[Main](using-the-slider-control-with-auto-postback-vb/samples/sample1.aspx)]

`SliderExtender` ASP.NET AJAX Control toolkit コントロールが 2 つのテキスト ボックスに、スライダーの機能を割り当てます。

[!code-aspx[Main](using-the-slider-control-with-auto-postback-vb/samples/sample2.aspx)]

追加のラベル要素が、ポストバックのユーザーに通知するために後で使用されます。

[!code-aspx[Main](using-the-slider-control-with-auto-postback-vb/samples/sample3.aspx)]

最後に、 `ScriptManager` ASP.NET AJAX のコントロールが動作する Control Toolkit の必要な JavaScript を読み込みます。

[!code-aspx[Main](using-the-slider-control-with-auto-postback-vb/samples/sample4.aspx)]

これで、スライダーがポスト バック;サーバー側で、このイベントをキャッチして処理して可能性があります。

[!code-aspx[Main](using-the-slider-control-with-auto-postback-vb/samples/sample5.aspx)]

[![ポストバックをトリガーするスライダーの移動](using-the-slider-control-with-auto-postback-vb/_static/image2.png)](using-the-slider-control-with-auto-postback-vb/_static/image1.png)

ポストバックをトリガーするスライダーの移動 ([フルサイズの画像を表示する をクリックします](using-the-slider-control-with-auto-postback-vb/_static/image3.png))。

[![その後、この変更の日付は、ラベルに書き込まれます](using-the-slider-control-with-auto-postback-vb/_static/image5.png)](using-the-slider-control-with-auto-postback-vb/_static/image4.png)

その後、この変更の日付は、ラベルに書き込まれます ([フルサイズの画像を表示する をクリックします](using-the-slider-control-with-auto-postback-vb/_static/image6.png))。

> [!div class="step-by-step"]
> [前へ](databinding-the-slider-control-cs.md)
> [次へ](databinding-the-slider-control-vb.md)
