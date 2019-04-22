---
uid: mvc/overview/older-versions-1/controllers-and-routing/creating-a-route-constraint-cs
title: ルート制約を作成 (c#) |Microsoft Docs
author: StephenWalther
description: このチュートリアルでは、Stephen Walther は、正規表現のルート制約を作成して、ブラウザーが一致するルートを要求する方法を制御する方法について説明します。
ms.author: riande
ms.date: 02/16/2009
ms.assetid: 0bfd06b1-12d3-4fbb-9779-a82e5eb7fe7d
msc.legacyurl: /mvc/overview/older-versions-1/controllers-and-routing/creating-a-route-constraint-cs
msc.type: authoredcontent
ms.openlocfilehash: 42c0ce5e158e2fe9387ac218ac0762b6362094f9
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59389577"
---
# <a name="creating-a-route-constraint-c"></a>ルート制約を作成する (C#)

によって[Stephen Walther](https://github.com/StephenWalther)

> このチュートリアルでは、Stephen Walther は、正規表現のルート制約を作成して、ブラウザーが一致するルートを要求する方法を制御する方法について説明します。


ルート制約を使用すると、特定のルートに一致するブラウザーの要求を制限できます。 正規表現を使用して、ルート制約を指定することができます。

たとえば、ルート、Global.asax ファイルでリスト 1 で定義したとします。

**1 - Global.asax.cs の一覧を表示します。**

[!code-csharp[Main](creating-a-route-constraint-cs/samples/sample1.cs)]

1 を一覧表示するには、製品をという名前のルートが含まれています。 製品のルートを使用して、リスト 2 に含まれる「productcontroller」ブラウザー要求をマップすることができます。

**Listing 2 - Controllers\ProductController.cs**

[!code-csharp[Main](creating-a-route-constraint-cs/samples/sample2.cs)]

製品のコント ローラーによって公開される Details() アクションが productId という 1 つのパラメーターを受け入れることに注意してください。 このパラメーターは、整数パラメーターです。

リスト 1 で定義されているルートは、次の Url のいずれかに一致します。

- /製品/23
- /製品/7

残念ながら、ルートには、次の Url は一致も。

- /製品/とおりです。
- /製品/apple

Details() アクションには、整数パラメーターが必要ですが、ため、整数値以外のものを含む要求を行うと、エラーが発生します。 たとえば、URL/Product/apple をお使いのブラウザーに入力した場合は、図 1 エラー ページを表示がされます。


[![[新しいプロジェクト] ダイアログ ボックス](creating-a-route-constraint-cs/_static/image1.jpg)](creating-a-route-constraint-cs/_static/image1.png)

**図 01**:展開のページが表示 ([フルサイズの画像を表示する をクリックします](creating-a-route-constraint-cs/_static/image2.png))。


本当にする内容は、のみ一致する適切な整数 productId を含む Url です。 ルートに一致する Url を制限するのにルートを定義するときに制約を使用できます。 リスト 3 で修正された製品のルートには、整数にのみ一致する正規表現の制約が含まれています。

**3 - Global.asax.cs の一覧を表示します。**

[!code-csharp[Main](creating-a-route-constraint-cs/samples/sample3.cs)]

正規表現 \d+ では、1 つまたは複数の整数と一致します。 この制約により、次の Url と一致する製品ルート。

- /製品/3
- /製品/8999

次の Url ではないです。

- /製品/apple
- /製品

- 別のルートでこれらのブラウザー要求を処理します。 または、一致のルートがない場合、*リソースが見つかりませんでした*エラーが返されます。

> [!div class="step-by-step"]
> [前へ](creating-custom-routes-cs.md)
> [次へ](creating-a-custom-route-constraint-cs.md)
