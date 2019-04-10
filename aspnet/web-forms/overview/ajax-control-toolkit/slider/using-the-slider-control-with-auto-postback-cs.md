---
uid: web-forms/overview/ajax-control-toolkit/slider/using-the-slider-control-with-auto-postback-cs
title: スライダー コントロールを使用すると自動ポストバック (c#) |Microsoft Docs
author: wenz
description: スライダー コントロール、AJAX Control Toolkit では、マウスを使用して制御できるグラフィカルなスライダーを提供します。 スライダー自動転記を作成することはしています.
ms.author: riande
ms.date: 06/02/2008
ms.assetid: 4d85e9fb-91e6-41f2-9c13-754549b19c27
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/slider/using-the-slider-control-with-auto-postback-cs
msc.type: authoredcontent
ms.openlocfilehash: a5b858a05470caa244902afbb404adbb2e4761b7
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59402727"
---
# <a name="using-the-slider-control-with-auto-postback-c"></a>自動ポストバック (c#) でスライダー コントロールの使用

によって[Christian Wenz](https://github.com/wenz)

[コードのダウンロード](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/Slider1.cs.zip)または[PDF のダウンロード](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/slider1CS.pdf)

> スライダー コントロール、AJAX Control Toolkit では、マウスを使用して制御できるグラフィカルなスライダーを提供します。 ようにスライダー autopostback 1 回、値が変化することになります。


## <a name="overview"></a>概要

スライダー コントロール、AJAX Control Toolkit では、マウスを使用して制御できるグラフィカルなスライダーを提供します。 ようにスライダー autopostback 1 回、値が変化することになります。

## <a name="steps"></a>手順

両方のテキスト ボックス、スライダーを変更すると自動的にポストバックするには、するには、属性が必要な`AutoPostBack="true"`:テキスト ボックス自体には、スライダーになると、テキスト ボックス、スライダーの位置を保持します。 そのための必要なマークアップを次に示します。

[!code-aspx[Main](using-the-slider-control-with-auto-postback-cs/samples/sample1.aspx)]

`SliderExtender` ASP.NET AJAX Control toolkit コントロールが 2 つのテキスト ボックスに、スライダーの機能を割り当てます。

[!code-aspx[Main](using-the-slider-control-with-auto-postback-cs/samples/sample2.aspx)]

追加のラベル要素が、ポストバックのユーザーに通知するために後で使用されます。

[!code-aspx[Main](using-the-slider-control-with-auto-postback-cs/samples/sample3.aspx)]

最後に、 `ScriptManager` ASP.NET AJAX のコントロールが動作する Control Toolkit の必要な JavaScript を読み込みます。

[!code-aspx[Main](using-the-slider-control-with-auto-postback-cs/samples/sample4.aspx)]

これで、スライダーがポスト バック;サーバー側で、このイベントをキャッチして処理して可能性があります。

[!code-aspx[Main](using-the-slider-control-with-auto-postback-cs/samples/sample5.aspx)]


[![Moving スライダーでは、ポストバックをトリガー](using-the-slider-control-with-auto-postback-cs/_static/image2.png)](using-the-slider-control-with-auto-postback-cs/_static/image1.png)

ポストバックをトリガーするスライダーの移動 ([フルサイズの画像を表示する をクリックします](using-the-slider-control-with-auto-postback-cs/_static/image3.png))。


[![Afterwards、ラベルに、この変更の日付が書き込まれる](using-the-slider-control-with-auto-postback-cs/_static/image5.png)](using-the-slider-control-with-auto-postback-cs/_static/image4.png)

その後、この変更の日付は、ラベルに書き込まれます ([フルサイズの画像を表示する をクリックします](using-the-slider-control-with-auto-postback-cs/_static/image6.png))。

> [!div class="step-by-step"]
> [次へ](databinding-the-slider-control-cs.md)
