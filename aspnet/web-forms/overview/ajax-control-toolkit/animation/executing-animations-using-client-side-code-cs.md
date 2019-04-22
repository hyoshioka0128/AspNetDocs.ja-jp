---
uid: web-forms/overview/ajax-control-toolkit/animation/executing-animations-using-client-side-code-cs
title: クライアント側コード (c#) を使用してアニメーションを実行する |Microsoft Docs
author: wenz
description: アニメーション コントロール、ASP.NET AJAX Control Toolkit ではなくコントロールだけをコントロールにアニメーションを追加するために全体のフレームワークです。 アニメーションの実行.
ms.author: riande
ms.date: 06/02/2008
ms.assetid: 0270e0df-6fde-4a8f-a2cb-2cacc55143f2
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/animation/executing-animations-using-client-side-code-cs
msc.type: authoredcontent
ms.openlocfilehash: 45a3d42d9e58469c789acfdc8cdaaf88b7920892
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59387094"
---
# <a name="executing-animations-using-client-side-code-c"></a>クライアント側コードを使用してアニメーションを実行する (C#)

によって[Christian Wenz](https://github.com/wenz)

[コードのダウンロード](http://download.microsoft.com/download/f/9/a/f9a26acd-8df4-4484-8a18-199e4598f411/Animation10.cs.zip)または[PDF のダウンロード](http://download.microsoft.com/download/6/7/1/6718d452-ff89-4d3f-a90e-c74ec2d636a3/animation10CS.pdf)

> アニメーション コントロール、ASP.NET AJAX Control Toolkit ではなくコントロールだけをコントロールにアニメーションを追加するために全体のフレームワークです。 カスタムのクライアント側の JavaScript コードを使用して、アニメーションの実行をトリガーも可能性があります。


## <a name="overview"></a>概要

アニメーション コントロール、ASP.NET AJAX Control Toolkit ではなくコントロールだけをコントロールにアニメーションを追加するために全体のフレームワークです。 カスタムのクライアント側の JavaScript コードを使用して、アニメーションの実行をトリガーも可能性があります。

## <a name="steps"></a>手順

まず、含める、 `ScriptManager` ; ページで次に、ASP.NET AJAX ライブラリが読み込まれる Control Toolkit を使用すること。

[!code-aspx[Main](executing-animations-using-client-side-code-cs/samples/sample1.aspx)]

次のようなテキストのパネルに、アニメーションが適用されます。

[!code-aspx[Main](executing-animations-using-client-side-code-cs/samples/sample2.aspx)]

パネルの関連付けられている CSS クラス、便利な背景色を定義し、パネルの固定幅の設定も。

[!code-css[Main](executing-animations-using-client-side-code-cs/samples/sample3.css)]

次に、追加、 `AnimationExtender` 、ページを提供する、 `ID`、`TargetControlID`属性と、変更を加える`runat="server"`:

[!code-aspx[Main](executing-animations-using-client-side-code-cs/samples/sample4.aspx)]

内で、`<Animations>`ノードを使用して`<OnClick>`パネルがクリック 1 回、ユーザーのアニメーションを実行します。 並列で実行される 2 つのアニメーションを追加します。

[!code-xml[Main](executing-animations-using-client-side-code-cs/samples/sample5.xml)]

このアニメーション (およびその他のすべてのアニメーション Control Toolkit を使用して作成) には、デモンストレーションのために、ページの実行後に、JavaScript コードを使用して実行されます。 最初にすべてのアクセスする必要があります、`AnimationExtender`コントロール。 ASP.NET AJAX ライブラリには、`$find()`このタスクの関数。

[!code-csharp[Main](executing-animations-using-client-side-code-cs/samples/sample6.cs)]

`AnimationExtender`コントロールは、XML マークアップで使用されるイベント ハンドラーと同じ名前のメソッドを含む豊富な API を公開します: `OnClick()`、`OnLoad()`など。 呼び出しなど、`OnClick()`メソッド内でアニメーションの実行、`<OnClick>`の要素、`AnimationExtender`コントロール。

[!code-javascript[Main](executing-animations-using-client-side-code-cs/samples/sample7.js)]

ここでは、ページが完全に読み込まれた後に [パネル] をクリックをエミュレートする完全なクライアント側の JavaScript コードは、注意、 `pageLoad()` 1 回、ページの ASP.NET AJAX によって呼び出される関数の名前が使用され、JavaScript ライブラリであったすべて含まれます読み込まれます。

[!code-html[Main](executing-animations-using-client-side-code-cs/samples/sample8.html)]


[![アニメーションのマウス クリックせず、すぐに実行します。](executing-animations-using-client-side-code-cs/_static/image2.png)](executing-animations-using-client-side-code-cs/_static/image1.png)

アニメーションのマウス クリックしてせず、すぐに実行 ([フルサイズの画像を表示する をクリックします](executing-animations-using-client-side-code-cs/_static/image3.png))。

> [!div class="step-by-step"]
> [前へ](modifying-animations-from-the-server-side-cs.md)
> [次へ](changing-an-animation-using-client-side-code-cs.md)
