---
uid: web-forms/overview/ajax-control-toolkit/animation/animating-in-response-to-user-interaction-cs
title: ユーザーの操作 (c#) への応答をアニメーション化 |Microsoft Docs
author: wenz
description: アニメーション コントロール、ASP.NET AJAX Control Toolkit ではなくコントロールだけをコントロールにアニメーションを追加するために全体のフレームワークです。 アニメーションが星できます.
ms.author: riande
ms.date: 06/02/2008
ms.assetid: ea26549d-fbbf-4973-a108-b14cd1d6de26
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/animation/animating-in-response-to-user-interaction-cs
msc.type: authoredcontent
ms.openlocfilehash: 9de99b3194a5517047e40922d89c28e341ba8e20
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57032419"
---
<a name="animating-in-response-to-user-interaction-c"></a>ユーザー操作に対してアニメーションを返す (C#)
====================
によって[Christian Wenz](https://github.com/wenz)

[コードのダウンロード](http://download.microsoft.com/download/f/9/a/f9a26acd-8df4-4484-8a18-199e4598f411/Animation6.cs.zip)または[PDF のダウンロード](http://download.microsoft.com/download/6/7/1/6718d452-ff89-4d3f-a90e-c74ec2d636a3/animation6CS.pdf)

> アニメーション コントロール、ASP.NET AJAX Control Toolkit ではなくコントロールだけをコントロールにアニメーションを追加するために全体のフレームワークです。 アニメーションは、自動的に起動できるまたはをトリガーすると、ユーザーの操作など、マウスでクリックしています。


## <a name="overview"></a>概要

アニメーション コントロール、ASP.NET AJAX Control Toolkit ではなくコントロールだけをコントロールにアニメーションを追加するために全体のフレームワークです。 アニメーションは、自動的に起動できるまたはをトリガーすると、ユーザーの操作など、マウスでクリックしています。

## <a name="steps"></a>手順

まず、含める、 `ScriptManager` ; ページで次に、ASP.NET AJAX ライブラリが読み込まれる Control Toolkit を使用すること。

[!code-aspx[Main](animating-in-response-to-user-interaction-cs/samples/sample1.aspx)]

次のようなテキストのパネルに、アニメーションが適用されます。

[!code-aspx[Main](animating-in-response-to-user-interaction-cs/samples/sample2.aspx)]

パネルの関連付けられている CSS クラス、便利な背景色を定義し、パネルの固定幅の設定も。

[!code-css[Main](animating-in-response-to-user-interaction-cs/samples/sample3.css)]

次に、追加、 `AnimationExtender` 、ページを提供する、 `ID`、`TargetControlID`属性と、変更を加える`runat="server"`:

[!code-aspx[Main](animating-in-response-to-user-interaction-cs/samples/sample4.aspx)]

内で、`<Animations>`ノードはユーザーの操作を使用してアニメーションを開始する 5 つの方法があります (不足している要素が`<OnLoad>`ページ全体が完全に読み込まれた後実行される)。

- `<OnClick>` (マウス クリック コントロール)
- `<OnHoverOut>` (マウスがコントロールを離れる)
- `<OnHoverOver>` (マウス ホバーを停止する、コントロールの上、`<OnHoverOut>`アニメーション)
- `<OnMouseOut>` (マウスがコントロールのまま)
- `<OnMouseOver>` (マウスを置くが停止しない、コントロール、`<OnMouseOut>`アニメーション)

このシナリオで`<OnClick>`使用されます。 パネルに、ユーザーがクリックすると、サイズを変更し、同時にフェードアウトします。

[!code-aspx[Main](animating-in-response-to-user-interaction-cs/samples/sample5.aspx)]


[![マウスのクリックでアニメーションを開始します。](animating-in-response-to-user-interaction-cs/_static/image2.png)](animating-in-response-to-user-interaction-cs/_static/image1.png)

マウスのクリックでアニメーションを開始 ([フルサイズの画像を表示する をクリックします](animating-in-response-to-user-interaction-cs/_static/image3.png))。

> [!div class="step-by-step"]
> [前へ](picking-one-animation-out-of-a-list-cs.md)
> [次へ](disabling-actions-during-animation-cs.md)
