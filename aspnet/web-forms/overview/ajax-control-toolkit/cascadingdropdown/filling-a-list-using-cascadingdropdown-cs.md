---
uid: web-forms/overview/ajax-control-toolkit/cascadingdropdown/filling-a-list-using-cascadingdropdown-cs
title: CascadingDropDown (c#) を使用するリスト |Microsoft Docs
author: wenz
description: CascadingDropDown コントロール、AJAX Control Toolkit では、anoth 内の値が 1 つの DropDownList の読み込みの変更に関連付けられているように DropDownList コントロールを拡張しています.
ms.author: riande
ms.date: 06/02/2008
ms.assetid: f949aafa-fe57-43b0-b722-f0dd33a900be
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/cascadingdropdown/filling-a-list-using-cascadingdropdown-cs
msc.type: authoredcontent
ms.openlocfilehash: a9a3bf12b721c8f5eec21f3090142e40e74b0b9c
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59395645"
---
# <a name="filling-a-list-using-cascadingdropdown-c"></a>CascadingDropDown を使用して一覧に入力する (C#)

によって[Christian Wenz](https://github.com/wenz)

[コードのダウンロード](http://download.microsoft.com/download/9/0/7/907760b1-2c60-4f81-aeb6-ca416a573b0d/cascadingdropdown0.cs.zip)または[PDF のダウンロード](http://download.microsoft.com/download/2/d/c/2dc10e34-6983-41d4-9c08-f78f5387d32b/cascadingdropdown0CS.pdf)

> CascadingDropDown コントロール、AJAX Control Toolkit では、もう 1 つの DropDownList の値が 1 つの DropDownList の読み込みの変更に関連付けられているように、DropDownList コントロールを拡張します。 (たとえば、1 つのリストは、私たちの状態の一覧を提供します。 し、[次へ] の一覧は、その状態の主な都市で埋められます)。解決するために最初のチャレンジでは、実際にこのコントロールを使用して、ドロップダウン リストを入力します。


## <a name="overview"></a>概要

CascadingDropDown コントロール、AJAX Control Toolkit では、もう 1 つの DropDownList の値が 1 つの DropDownList の読み込みの変更に関連付けられているように、DropDownList コントロールを拡張します。 (たとえば、1 つのリストは、私たちの状態の一覧を提供します。 し、[次へ] の一覧は、その状態の主な都市で埋められます)。解決するために最初のチャレンジでは、実際にこのコントロールを使用して、ドロップダウン リストを入力します。

## <a name="steps"></a>手順

ASP.NET AJAX Control Toolkit の機能をアクティブ化するために、`ScriptManager`コントロールは、ページのどこでも配置する必要があります (ただし内、`<form>`要素)。

[!code-aspx[Main](filling-a-list-using-cascadingdropdown-cs/samples/sample1.aspx)]

次に、DropDownList コントロールが必要です。

[!code-aspx[Main](filling-a-list-using-cascadingdropdown-cs/samples/sample2.aspx)]

このリストの CascadingDropDown エクステンダーが追加されます。 これは、一覧に表示されるエントリの一覧が返されますし、web サービスに非同期要求を送信します。 これを機能させるには、CascadingDropDown の次の属性を設定する必要があります。

- `ServicePath`:一覧のエントリを提供する web サービスの URL
- `ServiceMethod`:一覧のエントリを提供する web メソッド
- `TargetControlID`:ドロップダウン リストの ID
- `Category`:Web メソッドが呼び出されたときに送信されるカテゴリ情報
- `PromptText`:サーバーからリスト データを非同期的に読み込むときに表示されるテキスト

次のマークアップに示します、`CascadingDropDown`要素。 C# および VB の唯一の違いは、関連付けられている web サービスの名前を示します。

[!code-aspx[Main](filling-a-list-using-cascadingdropdown-cs/samples/sample3.aspx)]

JavaScript コード、`CascadingDropDown`エクステンダーは、次のシグネチャを持つ web サービス メソッドを呼び出します。

[!code-csharp[Main](filling-a-list-using-cascadingdropdown-cs/samples/sample4.cs)]

重要な側面は、メソッドが型の配列を取得する必要がある`CascadingDropDownNameValue`(ASP.NET AJAX Control Toolkit によって定義される)。 `CascadingDropDownNameValue`リスト エントリのテキストとし、その値を指定する必要があります、最初のコンス トラクターと同様`<option value="VALUE">NAME</option>`HTML で行います。 サンプル データの一部を次に示します。

[!code-aspx[Main](filling-a-list-using-cascadingdropdown-cs/samples/sample5.aspx)]

ブラウザーでページの読み込みと、次の 3 つの仕入先を格納するリストがトリガーされます。


[![リストが自動的に入力します。](filling-a-list-using-cascadingdropdown-cs/_static/image2.png)](filling-a-list-using-cascadingdropdown-cs/_static/image1.png)

リストが自動的に入力されます ([フルサイズの画像を表示する をクリックします](filling-a-list-using-cascadingdropdown-cs/_static/image3.png))。

> [!div class="step-by-step"]
> [次へ](using-cascadingdropdown-with-a-database-cs.md)
