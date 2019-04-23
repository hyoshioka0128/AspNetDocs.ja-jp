---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-7
title: ビュー (UI) を作成する |Microsoft Docs
author: MikeWasson
description: ''
ms.author: riande
ms.date: 06/16/2014
ms.assetid: b2445062-a1fe-4133-8994-f510280f6d9a
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-7
msc.type: authoredcontent
ms.openlocfilehash: 62c4523c2c6fb399cfbc3716309a1379996d601c
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59408239"
---
# <a name="create-the-view-ui"></a>ビューの作成 (UI)

作成者[Mike Wasson](https://github.com/MikeWasson)

[完成したプロジェクトのダウンロード](https://github.com/MikeWasson/BookService)

このセクションでは、アプリでは、HTML を定義し、HTML とビュー モデル間のデータ バインディングを追加する開始されます。

Views/Home/Index.cshtml ファイルを開きます。 次のように、そのファイルの内容全体を置き換えます。

[!code-cshtml[Main](part-7/samples/sample1.cshtml)]

ほとんどの`div`要素はあります[ブートス トラップ](http://getbootstrap.com/)のスタイルを設定します。 重要な要素は、のあるもの`data-bind`属性。 この属性は、HTML をビュー モデルにリンクします。

例:

[!code-html[Main](part-7/samples/sample2.html)]

この例で、 &quot; `text` &quot;バインド エラーの原因、`<p>`要素の値を表示する、`error`ビュー モデルからのプロパティ。 いることを思い出してください`error`として宣言されている、 `ko.observable`:

[!code-javascript[Main](part-7/samples/sample3.js)]

新しい値が割り当てられたときに`error`、Knockout でのテキストを更新する、`<p>`要素。

`foreach`バインド通知の内容をループする Knockout、`books`配列。 Knockout、配列内の各項目を新規作成&lt;li&gt;要素。 バインドのコンテキスト内で、`foreach`配列項目のプロパティを参照してください。 例えば:

[!code-html[Main](part-7/samples/sample4.html)]

ここで、`text`バインドはの各 book の Author プロパティを読み取ります。

これでアプリケーションを実行する場合次のようになります。

![](part-7/_static/image1.png)

書籍の一覧は、ページが読み込まれた後、非同期的に読み込みます。 今すぐ、&quot;詳細&quot;リンクは機能しません。 次のセクションで、この機能を追加します。

> [!div class="step-by-step"]
> [前へ](part-6.md)
> [次へ](part-8.md)
