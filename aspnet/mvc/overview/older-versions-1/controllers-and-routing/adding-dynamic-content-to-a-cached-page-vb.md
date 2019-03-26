---
uid: mvc/overview/older-versions-1/controllers-and-routing/adding-dynamic-content-to-a-cached-page-vb
title: キャッシュされたページ (VB) への動的なコンテンツの追加 |Microsoft Docs
author: microsoft
description: 同じページで、動的およびキャッシュ済みコンテンツを混在させる方法について説明します。 キャッシュ後置換では、バナー広告 o などの動的なコンテンツを表示することができます.
ms.author: riande
ms.date: 01/27/2009
ms.assetid: 68acd884-fb57-4486-a1be-aaa93e380780
msc.legacyurl: /mvc/overview/older-versions-1/controllers-and-routing/adding-dynamic-content-to-a-cached-page-vb
msc.type: authoredcontent
ms.openlocfilehash: b361470c769b61a7b407797158eb695fe5084eda
ms.sourcegitcommit: 289e051cc8a90e8f7127e239fda73047bde4de12
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58423495"
---
<a name="adding-dynamic-content-to-a-cached-page-vb"></a>キャッシュされたページに動的コンテンツを追加する (VB)
====================
によって[Microsoft](https://github.com/microsoft)

> 同じページで、動的およびキャッシュ済みコンテンツを混在させる方法について説明します。 キャッシュ後置換では、バナー広告またはキャッシュされた出力されたページ内のニュース項目などの動的なコンテンツを表示することができます。


出力キャッシュの利用して、ASP.NET MVC アプリケーションのパフォーマンスを大幅に向上できます。 ページ、ページが要求されるたびに、再生成するのではなくページを 1 回生成し、複数のユーザー用のメモリにキャッシュすることができます。

問題があります。 場合、ページに動的なコンテンツを表示する必要がありますか。 たとえば、バナー広告をページに表示することを想像してください。 すべてのユーザーがまったく同じ提供情報を認識するようにキャッシュするバナー広告は必要ありません。 その方法も収益を上げるはありません。

さいわい、簡単なソリューションがあります。 呼ばれる ASP.NET フレームワークの機能の利用*キャッシュ後の置換*します。 キャッシュ後の置換を使用すると、メモリにキャッシュされているページで動的なコンテンツを置き換えることができます。


通常、出力する場合、このページを使用して、キャッシュ、 &lt;OutputCache&gt;属性、ページは、サーバーとクライアント (web ブラウザー) の両方でキャッシュされます。 キャッシュ後の置換を使用すると、ページがサーバー上でのみキャッシュされます。


#### <a name="using-post-cache-substitution"></a>キャッシュ後の置換を使用します。

キャッシュ後の置換を使用するには、2 つの手順が必要です。 最初に、キャッシュされたページに表示する動的なコンテンツを表す文字列を返すメソッドを定義する必要があります。 次に、ページに動的なコンテンツを挿入する HttpResponse.WriteSubstitution() メソッドを呼び出します。

たとえば、キャッシュされたページでさまざまなニュース項目をランダムに表示することに想像してください。 リスト 1 でクラスでは、3 つのニュース項目の一覧からランダムに 1 つのニュース項目を返す、RenderNews() という名前の 1 つのメソッドを公開します。

**1 – Models\News.vb を一覧表示します。**

[!code-vb[Main](adding-dynamic-content-to-a-cached-page-vb/samples/sample1.vb)]

キャッシュ後の置換を利用するには、HttpResponse.WriteSubstitution() メソッドを呼び出します。 WriteSubstitution() メソッドでコードを設定すると、動的コンテンツを含む、キャッシュされたページの領域を置換します。 WriteSubstitution() メソッドは、リスト 2 でのビューでランダムなニュース項目の表示に使用されます。

**Listing 2 – Views\Home\Index.aspx**

[!code-aspx[Main](adding-dynamic-content-to-a-cached-page-vb/samples/sample2.aspx)]

RenderNews メソッドは、WriteSubstitution() メソッドに渡されます。 RenderNews メソッドが呼び出されない点に注意してください。 代わりに、メソッドへの参照は、AddressOf 演算子を利用して WriteSubstitution() に渡されます。

インデックス ビューにキャッシュされます。 ビューは、リスト 3 のコント ローラーによって返されます。 Index() アクションがで修飾されたに注意してください、 &lt;OutputCache&gt;により、インデックス ビューは、60 秒間キャッシュする属性。

**Listing 3 – Controllers\HomeController.vb**

[!code-vb[Main](adding-dynamic-content-to-a-cached-page-vb/samples/sample3.vb)]

インデックス ビューがキャッシュされている場合でも、インデックス ページを要求するときに異なるランダムなニュースの項目が表示されます。 インデックス ページを要求するときに、ページが表示される時刻は 60 秒間の (図 1 参照) は変わりません。 時刻が変更されないという事実は、ページがキャッシュされていることを証明します。 WriteSubstitution() メソッド – ランダム ニュース項目 – が変更された各要求によって、コンテンツを挿入するただし、します。

**図 1 – キャッシュされたページで動的ニュース項目を挿入します。**

![clip_image002](adding-dynamic-content-to-a-cached-page-vb/_static/image1.jpg)

#### <a name="using-post-cache-substitution-in-helper-methods"></a>キャッシュ後の置換を使用して、ヘルパー メソッド

キャッシュ後の置換を活用する簡単な方法では、カスタム ヘルパー メソッド内で WriteSubstitution() メソッドの呼び出しをカプセル化します。 このアプローチは、リスト 4 ヘルパー メソッドに示します。

**Listing 4 – Helpers\AdHelper.vb**

[!code-vb[Main](adding-dynamic-content-to-a-cached-page-vb/samples/sample4.vb)]

4 を一覧表示するには、2 つのメソッドを公開する Visual Basic モジュールが含まれます。RenderBanner() RenderBannerInternal(). RenderBanner() メソッドでは、実際のヘルパー メソッドを表します。 このメソッドは、標準の ASP.NET MVC の HtmlHelper クラスを拡張するので、他のヘルパー メソッドと同様のビューで Html.RenderBanner() を呼び出すことができます。

RenderBanner() メソッドを WriteSubstitution() メソッド RenderBannerInternal() メソッドに渡す HttpResponse.WriteSubstitution() メソッドを呼び出します。

RenderBannerInternal() メソッドは、プライベート メソッドです。 このメソッドは、ヘルパー メソッドとして公開されません。 RenderBannerInternal() メソッドは、バナー広告の 3 つのイメージの一覧からランダムに 1 つのバナー広告イメージを返します。

変更したのインデックス ビュー リスト 5 では、RenderBanner() ヘルパー メソッドを使用する方法を示しています。 注意して、追加&lt;インポート % @ %&gt;ディレクティブは MvcApplication1.Helpers 名前空間をインポートするには、ビューの上部に含まれます。 この名前空間のインポートを怠ると、RenderBanner() メソッドは、Html プロパティのメソッドとして表示されません。

**5 – (RenderBanner() メソッド) を使用して Views\Home\Index.aspx を一覧表示します。**

[!code-aspx[Main](adding-dynamic-content-to-a-cached-page-vb/samples/sample5.aspx)]

リスト 5 で、ビューによって表示されるページを要求すると、要求ごとに異なるバナー広告が表示されます (図 2 参照)。 ページ キャッシュされますが、RenderBanner() ヘルパー メソッドによって動的にバナー広告を挿入します。

**図 2 – ランダムなバナー広告を表示するインデックス ビュー**

![clip_image004](adding-dynamic-content-to-a-cached-page-vb/_static/image2.jpg)

#### <a name="summary"></a>まとめ

このチュートリアルでは、キャッシュされたページのコンテンツを動的に更新する方法について説明します。 HttpResponse.WriteSubstitution() メソッドを使用して、キャッシュされたページに挿入されるようにする動的なコンテンツを有効にする方法を学習しました。 HTML ヘルパー メソッド内で WriteSubstitution() メソッドの呼び出しをカプセル化する方法も学習しました。

Web アプリケーションのパフォーマンスに大きな影響を及ぼすことができます: 可能な限りキャッシュを活用します。 前述のこのチュートリアルでは、ページに動的なコンテンツを表示する必要があるときにも、キャッシュの利用できます。

> [!div class="step-by-step"]
> [前へ](improving-performance-with-output-caching-vb.md)
> [次へ](creating-a-controller-vb.md)
