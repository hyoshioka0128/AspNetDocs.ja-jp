---
uid: web-pages/overview/ui-layouts-and-themes/4-working-with-forms
title: ASP.NET Web Pages (Razor) サイトでの HTML フォームの操作 |Microsoft Docs
author: Rick-Anderson
description: フォームは、テキスト ボックス、チェック ボックス、ラジオ ボタン、およびプルダウン リストなどのユーザー入力コントロールを配置する HTML ドキュメントのセクションです。 フォームを使用する値を下回った場合.
ms.author: riande
ms.date: 02/10/2014
ms.assetid: f3f4b8c8-e8f6-4474-ad94-69228a6c01ee
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/4-working-with-forms
msc.type: authoredcontent
ms.openlocfilehash: c7d4802063c8610a246afe67bd15eea429f7304a
ms.sourcegitcommit: dd0dc556a3d99a31d8fdbc763e9a2e53f3441b70
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2019
ms.locfileid: "67410844"
---
# <a name="working-with-html-forms-in-aspnet-web-pages-razor-sites"></a>ASP.NET Web Pages (Razor) サイトでの HTML フォームの操作

によって[Tom FitzMacken](https://github.com/tfitzmac)

> この記事では、(テキスト ボックスとボタン) を持つ、HTML フォームを処理する方法を説明します。 ASP.NET Web Pages (Razor) の web サイトで作業している場合。
> 
> **学習内容。** 
> 
> - HTML フォームを作成する方法。
> - フォームからユーザーの入力を読み取る方法。
> - ユーザー入力を検証する方法。
> - ページが送信された後に、フォームの値を復元する方法。
> 
> これらは、プログラミングの概念は、情報の記事で導入された ASP.NET:
> 
> - `Request` オブジェクト。
> - 入力の検証。
> - HTML エンコードします。
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>このチュートリアルで使用されるソフトウェアのバージョン
> 
> 
> - ASP.NET Web Pages (Razor) 3
>   
> 
> このチュートリアルは、ASP.NET Web Pages 2 でも機能します。

## <a name="creating-a-simple-html-form"></a>単純な HTML フォームを作成します。

1. 新しい web サイトを作成します。
2. という名前の web ページを作成、ルート フォルダーで*Form.cshtml*し、次のマークアップを入力します。

    [!code-html[Main](4-working-with-forms/samples/sample1.html)]
3. お使いのブラウザーでページを起動します。 (WebMatrix で、**ファイル**] ワークスペースで、ファイルを右クリックし、[**ブラウザーで起動**)。次の 3 つの入力フィールドを持つ単純なフォームと**送信**ボタンが表示されます。

    ![次の 3 つのテキスト ボックスをフォームのスクリーン ショット。](4-working-with-forms/_static/image1.png)

    クリックした場合、この時点で、**送信**ボタンを何も起こりません。 形式を有効に活用するには、サーバーで実行されるいくつかのコードを追加する必要があります。

## <a name="reading-user-input-from-the-form"></a>フォームからユーザー入力を読み取る

フォームを処理するには、送信されたフィールドの値を読み取り、何らかの処理はコードを追加します。 この手順では、フィールドを読み取り、ページで、ユーザー入力を表示する方法を示します。 (実稼働アプリケーションで一般的に処理を行うより興味深いユーザー入力します。 行いますでデータベース操作に関する記事。)

1. 上部にある、 *Form.cshtml*ファイルに、次のコードを入力します。

    [!code-cshtml[Main](4-working-with-forms/samples/sample2.cshtml)]

    ユーザーは、まず、ページを要求するときに、空のフォームのみが表示されます。 (することになります) ユーザーが、フォームに入力し、クリックし、**送信**します。 (投稿) を送信するこのサーバーへのユーザー入力します。 既定では、要求が同じページに進みます (つまり、 *Form.cshtml*)。

    この時点で、ページを送信するときに入力した値が、フォームのすぐ上に表示されます。

    ![ページに表示される入力した値を示すスクリーン ショット。](4-working-with-forms/_static/image2.png)

    ページのコードを確認します。 最初に使用する、`IsPost`ページがポストされているかどうかを判断するメソッド&#8212;、かどうかをユーザーがクリックした、**送信**ボタンをクリックします。 場合は、投稿`IsPost`true を返します。 これは、最初の要求 (GET 要求)、またはポストバック (POST 要求) を扱うかどうかを判断する標準的な方法では、ASP.NET Web ページです。 (詳細については、GET と POST を参照してください「HTTP GET と POST と IsPost プロパティの」で[ASP.NET Web ページは、Razor 構文を使用プログラミングの概要について](https://go.microsoft.com/fwlink/?LinkId=202890#SB_HttpGetPost))。

    次に、ユーザーから入力される値を取得、`Request.Form`オブジェクト、およびを置く変数に後でします。 `Request.Form`オブジェクトには、キーによって識別される各ページに送信されたすべての値が含まれています。 キーと等価が、`name`読みたいフォーム フィールドの属性です。 たとえば、読み取り、`companyname`フィールド (テキスト ボックス) を使用する`Request.Form["companyname"]`します。

    フォームの値が格納されている、`Request.Form`文字列としてのオブジェクト。 そのため、数値または日付、またはその他の種類として値を操作するときに、文字列からその型に変換する必要があります。 例では、`AsInt`のメソッド、 `Request.Form` (を従業員数を含む) の従業員のフィールドの値を整数に変換するために使用します。
2. お使いのブラウザーでページを起動し、フォームのフィールドを入力し、をクリックして**送信**します。 ページには、入力した値が表示されます。

> [!TIP] 
> 
> <a id="SB_HTMLEncoding"></a>
> ### <a name="html-encoding-for-appearance-and-security"></a>外観とセキュリティに HTML エンコード
> 
> HTML のような文字の特殊な用途を持つ`<`、 `>`、および`&`します。 これらの特殊文字が表示されませんに期待している場合、web ページの機能と外観を欠きますができます。 たとえば、ブラウザーが解釈、`<`など HTML 要素の先頭として (ない場合に空白が続いて) を文字`<b>`または`<input ...>`します。 始まる文字列を単に破棄、ブラウザーでは、要素を認識しない場合`<`再度認識に達するまで何かです。 当然ながら、いくつかの奇妙なレンダリング ページでこのがあります。
> 
> HTML エンコードすると、ブラウザーが正しいシンボルとして解釈するコードでこれらの予約済み文字が置き換えられます。 たとえば、`<`文字で置換されます`&lt;`と`>`文字が置き換え`&gt;`。 ブラウザーでは、表示文字としてこれらの置換文字列が表示されます。
> 
> HTML エンコード文字列を表示する任意の時間を使用する (入力)、ユーザーから取得したことをお勧めすることをお勧めします。 ない場合は、ユーザーできる悪意のあるスクリプトを実行するか、別のものを web ページ、サイトのセキュリティを侵害するか、望ましくないだけであるを取得しようとします。 (これは、特に重要な場合はユーザー入力を取得、別の場所を格納し、それを後で表示&#8212;ブログ コメント、ユーザーのレビュー、またはそのようなものとしてなど)。
> 
> 自動的に ASP.NET Web Pages のこれらの問題を防ぐため HTML エンコード任意のテキスト コンテンツをコードから出力します。 たとえば、変数またはなどのコードを使用して、式のコンテンツを表示する`@MyVar`、ASP.NET Web ページが自動的に出力をエンコードします。

## <a name="validating-user-input"></a>ユーザー入力の検証

ユーザーを犯します。 フィールドに入力するよう依頼して、忘れてしまうまたは従業員の数を入力するように依頼し、代わりに名前を入力します。 フォーム データが格納された正しく処理する前に確認するには、ユーザーの入力を検証します。

この手順では、ユーザーしなかった空白のままになっていることを確認するすべての 3 つのフォーム フィールドを検証する方法を示します。 従業員数の値が数値であることも確認します。 エラーがある場合は、エラーを表示するがどのような値をユーザーに通知するメッセージが検証に合格しませんでした。

1. *Form.cshtml*ファイルでコードの最初のブロックを次のコードに置き換えます。 

    [!code-cshtml[Main](4-working-with-forms/samples/sample3.cshtml)]

    使用するユーザー入力を検証する、`Validation`ヘルパー。 呼び出すことによって必要なフィールドを登録する`Validation.RequireField`します。 呼び出すことによって他の種類の検証を登録する`Validation.Add`を検証するフィールドと実行する検証の種類を指定します。

    ページが実行される ASP.NET すべての検証にすることができます。 呼び出して結果を確認できます`Validation.IsValid`、すべてのものが渡された場合は true を返すと任意のフィールド検証に失敗した場合は false。 通常、呼び出す`Validation.IsValid`ユーザー入力にどのような処理を実行する前にします。
2. 更新プログラム、`<body>`要素に 3 つの呼び出しを追加することで、`Html.ValidationMessage`メソッドは、次のように。

    [!code-cshtml[Main](4-working-with-forms/samples/sample4.cshtml?highlight=8,13,18)]

    検証エラー メッセージを表示するには、Html を呼び出すことができます。`ValidationMessage` メッセージを使用するフィールドの名前を渡します。
3. ページを実行します。 フィールドを空白のままにし、クリックして**送信**します。 エラー メッセージが表示されます。

    ![ユーザー入力が検証に合格しなかったかどうかに表示されるエラー メッセージを示すスクリーン ショット。](4-working-with-forms/_static/image3.jpg)
4. 文字列 (たとえば、"ABC") を追加、**従業員数**フィールドし、クリックして**送信**もう一度です。 この時間のことを示すエラーが表示文字列は、適切な形式、つまり、整数ではありません。

    ![ユーザーが従業員のフィールドの文字列を入力するかどうかに表示されるエラー メッセージを示すスクリーン ショット。](4-working-with-forms/_static/image4.jpg)

ASP.NET Web ページは、自動的にユーザーがブラウザーで迅速なフィードバックを取得できるように、クライアント スクリプトを使用して検証を実行する機能など、ユーザー入力を検証するための他のオプションを提供します。 参照してください、[資料](#Additional_Resources)詳細については後でセクション。

## <a name="restoring-form-values-after-postbacks"></a>ポストバック後にフォームの値を復元します。

前のセクションで、ページをテストするときにお気付きかもしれませんが、検証エラーが発生し、(無効なデータだけでなく) を入力したすべてのものがないため、され、すべてのフィールドの値を再入力する必要がありました。 これは重要な点を示しています。 ページが最初から再作成ページを送信すると処理し、もう一度ページを表示し、します。 学習したように、送信されたときに、ページには任意の値が失われることを意味します。

修正できます簡単に、ただしします。 送信された値へのアクセスがある (で、`Request.Form`オブジェクト、ページが表示されるときに、フォーム フィールドにこれらの値を入力することができます。

1. *Form.cshtml*ファイルで、置換、`value`の属性、`<input>`要素を使用して、`value`属性。 

    [!code-cshtml[Main](4-working-with-forms/samples/sample5.cshtml?highlight=13,19,25)]

    `value`の属性、`<input>`のフィールドの値を動的に読み取る要素が設定されて、`Request.Form`オブジェクト。 内の値、最初に、ページが要求される、`Request.Form`オブジェクトがすべて空にします。 これは、により、フォームが空白ため問題ありません。
2. お使いのブラウザーでページを起動、フォーム フィールドを入力し、空白のまままたは をクリックして**送信**します。 送信された値を表示するページが表示されます。

    ![フォーム 5](4-working-with-forms/_static/image5.jpg)

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>その他のリソース

- [1,001 方法で Web ユーザーからの入力を取得するには](https://msdn.microsoft.com/library/ms971057.aspx)
- [フォームを使用して、ユーザー入力の処理](https://msdn.microsoft.com/library/ms525182(VS.90).aspx)
- [ASP.NET Web ページにおけるユーザー入力の検証](https://go.microsoft.com/fwlink/?LinkId=253002)
- [HTML フォームのオートコンプリート機能の使用](https://msdn.microsoft.com/library/ms533032(VS.85).aspx)
