---
uid: web-forms/overview/ajax-control-toolkit/dynamicpopulate/dynamically-populating-a-control-using-javascript-code-vb
title: JavaScript コード (VB) を使用してコントロールを動的に作成する |Microsoft Docs
author: wenz
description: ASP.NET AJAX Control Toolkit で DynamicPopulate コントロールは、web サービス (またはページ メソッド) を呼び出すし、t のターゲット コントロールに、結果の値を入力しています.
ms.author: riande
ms.date: 06/02/2008
ms.assetid: 90582e54-3e90-432a-9da5-689fb39ed56b
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/dynamicpopulate/dynamically-populating-a-control-using-javascript-code-vb
msc.type: authoredcontent
ms.openlocfilehash: 668815d58f2dc9a67cce441dfa267fa043a35091
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59387205"
---
# <a name="dynamically-populating-a-control-using-javascript-code-vb"></a>JavaScript コードを使用してコントロールに動的に入力する (VB)

によって[Christian Wenz](https://github.com/wenz)

[コードのダウンロード](http://download.microsoft.com/download/d/8/f/d8f2f6f9-1b7c-46ad-9252-e1fc81bdea3e/dynamicpopulate1.vb.zip)または[PDF のダウンロード](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/dynamicpopulate1VB.pdf)

> ASP.NET AJAX Control Toolkit で DynamicPopulate コントロールは、web サービス (またはページ メソッド) を呼び出すし、ページで、ページを更新せず、ターゲット コントロールに、結果の値を設定します。 カスタムのクライアント側の JavaScript コードを使用して、カタログの作成をトリガーすることもできます。


## <a name="overview"></a>概要

`DynamicPopulate` ASP.NET AJAX Control Toolkit のコントロールが web サービス (またはページ メソッド) を呼び出し、ターゲット コントロール ページで、ページ更新せずに、結果の値を入力します。 カスタムのクライアント側の JavaScript コードを使用して、カタログの作成をトリガーすることもできます。

## <a name="steps"></a>手順

まず、ASP.NET Web サービスによって呼び出されるメソッドを実装する必要があります、`DynamicPopulateExtender`コントロール。 Web サービス メソッドを実装する`getDate()`と呼ばれる、文字列型の 1 つの引数が予想される`contextKey`、ので、`DynamicPopulate`コントロールが web サービスの呼び出しごとに 2 つのコンテキスト情報を送信します。 コードを次に示します (ファイル`DynamicPopulate.vb.asmx`) 3 つの形式のいずれかで現在の日付を取得します。

[!code-aspx[Main](dynamically-populating-a-control-using-javascript-code-vb/samples/sample1.aspx)]

次の手順では、新しい ASP.NET サイトを作成し、ASP.NET AJAX ScriptManager コントロールに起動します。

[!code-aspx[Main](dynamically-populating-a-control-using-javascript-code-vb/samples/sample2.aspx)]

ラベル コントロールを追加し、(たとえば、同じ名前の HTML コントロールを使用して、または`<asp:Label />`web コントロール)、web サービス呼び出しの結果は後で説明します。

[!code-aspx[Main](dynamically-populating-a-control-using-javascript-code-vb/samples/sample3.aspx)]

次に、含める、`DynamicPopulateExtender`制御し、対象のコントロールがカスタム JavaScript を使用して後で、この実行は作成をトリガーするコントロールの名前ではなく、web サービス情報を提供します。

[!code-aspx[Main](dynamically-populating-a-control-using-javascript-code-vb/samples/sample4.aspx)]

JavaScript の部分するようになりました。 `$find()` 、ASP.NET AJAX ライブラリで定義されている、関数など、ASP.NET AJAX Control Toolkit のサーバー側のオブジェクトへの参照を返します`DynamicPopulateExtender`します。 現在のファイルで`$find("dpe")`ものへの参照を返します`DynamicPopulateExtender`ページ内のコントロール。 呼び出されるメソッドを公開`populate()`動的作成プロセスをトリガーします。 `populate()`メソッドが 1 つの引数が必要です。 コンテキスト キーへの引数として機能、 `getDate()` web メソッド。 たとえば、`$find("dpe").populate("format1")`月-日-年の形式で現在の日付のラベルに表示します。

サンプルをさらに柔軟性にするには、するには、ユーザーがいくつかの日付形式の間は選択ようになりました。 それぞれのラジオ ボタンが表示されます。 1 回オプション ボタンでユーザーが、JavaScript コード動的に設定を選択した日付の形式でラベル。 これらのオプション ボタンを次に示します。

[!code-aspx[Main](dynamically-populating-a-control-using-javascript-code-vb/samples/sample5.aspx)]

ラジオ ボタン、JavaScript の式のコンテキスト内でなお`this.value`はまったく同じ情報を使用する場合は、現在のボタンの値を示します、`getDate()`メソッドを使用できます。


[![ボタンをクリックして指定された形式で、サーバーから日付を取得します。](dynamically-populating-a-control-using-javascript-code-vb/_static/image2.png)](dynamically-populating-a-control-using-javascript-code-vb/_static/image1.png)

ボタンをクリックしますが、指定された形式でサーバーから日付を取得 ([フルサイズの画像を表示する をクリックします](dynamically-populating-a-control-using-javascript-code-vb/_static/image3.png))。

> [!div class="step-by-step"]
> [前へ](dynamically-populating-a-control-vb.md)
> [次へ](using-dynamicpopulate-with-a-user-control-and-javascript-vb.md)
