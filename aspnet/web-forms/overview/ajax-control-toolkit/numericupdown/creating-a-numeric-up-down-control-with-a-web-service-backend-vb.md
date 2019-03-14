---
uid: web-forms/overview/ajax-control-toolkit/numericupdown/creating-a-numeric-up-down-control-with-a-web-service-backend-vb
title: Web サービス バックエンド (VB) で、数値のアップダウン コントロールを作成する |Microsoft Docs
author: wenz
description: チェック ボックスに値を入力するだけでなく、数値を上げ下げするコントロール (Windows と他のオペレーティング システムに存在) はより多くの c を生じる可能性があります.
ms.author: riande
ms.date: 06/02/2008
ms.assetid: afa59dfa-fef1-43d3-8fdd-aea3be36ed3c
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/numericupdown/creating-a-numeric-up-down-control-with-a-web-service-backend-vb
msc.type: authoredcontent
ms.openlocfilehash: bf69b3e6932df528e8ccd2348ffa6f13132f99f8
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57029489"
---
<a name="creating-a-numeric-updown-control-with-a-web-service-backend-vb"></a>Web サービス バックエンドで数値を上げ下げするコントロールを作成する (VB)
====================
によって[Christian Wenz](https://github.com/wenz)

[コードのダウンロード](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/numericupdown1.vb.zip)または[PDF のダウンロード](http://download.microsoft.com/download/2/d/c/2dc10e34-6983-41d4-9c08-f78f5387d32b/numericupdown1VB.pdf)

> チェック ボックスに値を入力するだけでなく、数値のアップダウン コントロール (Windows と他のオペレーティング システムに存在) がより多く生じる快適です。 既定では、NumericUpDown コントロールは常に増加または値を 1 ずつ減少しますが、web サービスは柔軟性を証明します。


## <a name="overview"></a>概要

チェック ボックスに値を入力するだけでなく、数値のアップダウン コントロール (Windows と他のオペレーティング システムに存在) がより多く生じる快適です。 既定で、`NumericUpDown`柔軟性を証明する web サービスが、コントロールが常に増加または値を 1 ずつ減少します。

## <a name="steps"></a>手順

ASP.NET AJAX Control Toolkit に含まれています、`NumericUpDown`エクステンダーが自動的にテキスト ボックスに 2 つのボタンを追加します。1 つずつ減少し、その値を増やすことの 1 つです。 ただし、コントロールは、web サービスの呼び出し (またはページ メソッドの呼び出し) をもサポートします。 たびに、上または下向きの矢印ボタンがクリックすると、JavaScript コードは、web サーバーに接続して、あるメソッドを実行します。 メソッドのシグネチャは、次のいずれかを示します。

[!code-vb[Main](creating-a-numeric-up-down-control-with-a-web-service-backend-vb/samples/sample1.vb)]

`current`引数は、テキスト ボックスには、現在の値、`tag`属性は、追加のコンテキスト データのプロパティとして設定できる、`NumericUpDown`エクステンダー (ただしは必要ありません)。

このサンプルでは、数値を上げ下げするコントロールは 2 の累乗値のみを許可します。1、2、4、8、16、32、64、およびなど。 したがって、ユーザーが値を大きくときに実行されるメソッドには、古い値の倍精度浮動小数点; 必要があります。その他のメソッドは、値を 2 で除算する必要があります。 したがって、完全な web サービスを次に示します。

[!code-aspx[Main](creating-a-numeric-up-down-control-with-a-web-service-backend-vb/samples/sample2.aspx)]

最後に、新しい ASP.NET ページを作成します。 通常どおり、必要があります、`ScriptManager`コントロール、`TextBox`コントロールと`NumericUpDownExtender`コントロール。 後者の場合、web サービスの情報を提供する必要があります。

- `ServiceDownMethod` web メソッドまたはメソッドのページ、リストの名前
- `ServiceDownPath` 下向きのサービス メソッドを使用して web サービスへのパスページ メソッドを使用している場合は、省略します。
- `ServiceUpMethod` web メソッドまたはメソッドがページ上の名前
- `ServiceUpPath` サービス メソッドを使用して web サービスへのパスページ メソッドを使用している場合は、省略します。

ページの完全なマークアップを次に示します。

[!code-aspx[Main](creating-a-numeric-up-down-control-with-a-web-service-backend-vb/samples/sample3.aspx)]

ページを実行する場合は、どのようにテキスト ボックスに値常に 2 倍に上のボタンをクリックして、下のボタンをクリックするとは半分にするとに注意してください。


[![2 の累乗の数値のみが表示されます。](creating-a-numeric-up-down-control-with-a-web-service-backend-vb/_static/image2.png)](creating-a-numeric-up-down-control-with-a-web-service-backend-vb/_static/image1.png)

2 の累乗の数値のみが表示されます ([フルサイズの画像を表示する をクリックします](creating-a-numeric-up-down-control-with-a-web-service-backend-vb/_static/image3.png))。

> [!div class="step-by-step"]
> [前へ](creating-a-numeric-up-down-control-with-a-web-service-backend-cs.md)
