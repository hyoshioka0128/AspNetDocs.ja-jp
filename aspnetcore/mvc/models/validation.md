---
title: ASP.NET Core MVC でのモデルの検証
author: tdykstra
description: ASP.NET Core MVC でのモデルの検証について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 01/14/2019
uid: mvc/models/validation
ms.openlocfilehash: ca7ee54b8e6b6ae5091b0cb133e448ad9c04da8f
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57049069"
---
# <a name="model-validation-in-aspnet-core-mvc"></a>ASP.NET Core MVC でのモデルの検証

作成者: [Rachel Appel](https://github.com/rachelappel)

## <a name="introduction-to-model-validation"></a>モデル検証の概要

アプリでは、データベースにデータを格納する前に、データを検証する必要があります。 データにセキュリティ上の脅威がないかどうかを確認し、種類とサイズが適切に設定されていることを検証しなければなりません。また、ご自身のルールに準拠している必要もあります。 検証を実装するのは冗長で面倒な場合がありますが、必要不可欠です。 MVC では、検証はクライアントとサーバーの両方で発生します。

さいわい、.NET では検証が検証属性に抽象化されています。 これらの属性には検証コードが含まれているため、開発者が記述しなければならないコードの量は少なくて済みます。

ASP.NET Core 2.2 以降の ASP.NET Core ランタイムでは、特定のモデル グラフが検証を必要としていないことを判断できる場合、検証がショートサーキット (スキップ) されます。 関連付けられた検証を持つことができない、または持っていないモデルを検証する場合、検証のスキップによりパフォーマンスを大幅に向上させることができます。 スキップされた検証には、プリミティブ型のコレクション (`byte[]`、`string[]`、`Dictionary<string, string>` など) や、検証コントロールのない複雑なオブジェクト グラフなどのオブジェクトが含まれます。

[GitHub のサンプルを表示またはダウンロードしてください](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/validation/sample)。

## <a name="validation-attributes"></a>検証属性

検証属性はモデルの検証を構成する方法であり、概念的にはデータベース テーブルでのフィールドの検証に似ています。 これには、データ型の割り当てや必須フィールドなどの制約が含まれます。 その他の種類の検証としては、データへのパターンの適用があります。クレジット カード、電話番号、メール アドレスなどのビジネス ルールを強制するためのものです。 検証属性を使うと、これらの要件の適用がはるかに単純で簡単になります。

検証属性は、次のようにプロパティ レベルで指定されます。 

```csharp
[Required]
public string MyProperty { get; set; }
```

次に示すのは、映画やテレビ番組に関する情報を格納するアプリの注釈付き `Movie` モデルです。 ほとんどのプロパティは必須であり、一部の文字列プロパティには長さの要件があります。 さらに、`Price` プロパティには 0 から $999.99 までの数値範囲制限が、カスタム検証属性と共に適用されています。

[!code-csharp[](validation/sample/Movie.cs?range=6-29)]

モデルに目を通すだけでこのアプリのデータについてのルールがわかり、コードの保守が簡単になります。 いくつかの一般的な組み込み検証属性を次に示します。

* `[CreditCard]`:プロパティがクレジット カード形式であることを検証します。

* `[Compare]`:モデルの 2 つのプロパティが一致することを検証します。

* `[EmailAddress]`:プロパティが電子メール形式であることを検証します。

* `[Phone]`:プロパティが電話番号形式であることを検証します。

* `[Range]`:プロパティの値が特定の範囲内であることを検証します。

* `[RegularExpression]`:データが指定した正規表現と一致することを検証します。

* `[Required]`:プロパティを必須にします。

* `[StringLength]`:文字列プロパティが指定した最大長以下であることを検証します。

* `[Url]`:プロパティが URL 形式であることを検証します。

MVC は、検証のために `ValidationAttribute` から派生した任意の属性をサポートします。 [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) 名前空間には多くの便利な検証属性が含まれています。

組み込みの属性で提供されるものより多くの機能が必要になる場合があります。 そのようなときは、`ValidationAttribute` から派生するか、モデルを変更して `IValidatableObject` を実装することにより、カスタム検証属性を作成できます。

## <a name="notes-on-the-use-of-the-required-attribute"></a>Required 属性の使用に関する注意事項

null 非許容の[値の型](/dotnet/csharp/language-reference/keywords/value-types) (`decimal`、`int`、`float`、`DateTime` など) は本質的に必須であり、`Required` 属性を必要としません。 アプリは、`Required` とマークされている null 非許容型に対して、サーバー側の検証チェックを実行しません。

MVC モデル バインドは、検証および検証属性には関わりがありませんが、null 非許容型に対して欠落値または空白文字を含むフォーム フィールドの送信を却下します。 ターゲット プロパティに `BindRequired` 属性がない場合、モデル バインドは null 非許容型の欠落データを無視し、そのフォーム フィールドは受信フォーム データからなくなります。

[BindRequired 属性](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.bindrequiredattribute) (「<xref:mvc/models/model-binding#customize-model-binding-behavior-with-attributes>」も参照) は、フォーム データが完全であることを保証するのに便利です。 プロパティに適用すると、モデル バインド システムはそのプロパティの値を必須にします。 型に適用すると、モデル バインド システムはその型のすべてのプロパティに対して値を必須にします。

[Nullable\<T> 型](/dotnet/csharp/programming-guide/nullable-types/) (例: `decimal?`、`System.Nullable<decimal>`) を使い、それを `Required` とマークすると、プロパティが標準の null 許容型 (例: `string`) の場合と同様に、サーバー側の検証チェックが実行されます。

クライアント側検証では、`Required` とマークされているモデル プロパティに対応するフォーム フィールドの値、および `Required` とマークされていない null 非許容型のプロパティの値を必須にします。 `Required` を使って、クライアント側検証のエラー メッセージを制御できます。

::: moniker range=">= aspnetcore-2.1"

## <a name="top-level-node-validation"></a>最上位ノードの検証

最上位ノードには次が含まれています。

* アクションのパラメーター
* コントローラーのプロパティ
* ページ ハンドラーのパラメーター
* ページ モデルのプロパティ

モデルのパラメーター検証に加え、モデルが関連付けられた最上位ノードが検証されます。 サンプル アプリからの次の例では、`VerifyPhone` メソッドでは <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> を使用し、フォームの Phone フィールドのユーザー データを検証します。

[!code-csharp[](validation/sample/UsersController.cs?name=snippet_VerifyPhone)]

最上位ノードでは、検証属性と共に <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> を使用できます。 サンプル アプリからの次の例では、`CheckAge` メソッドによって、フォームの送信時、クエリ文字列から `age` パラメーターを関連付ける必要があることが指定されます。

[!code-csharp[](validation/sample/UsersController.cs?name=snippet_CheckAge)]

[年齢確認] ページ (*CheckAge.cshtml*) には 2 つのフォームがあります。 最初のフォームでは、`Age` の値 `99` がクエリ文字列 `https://localhost:5001/Users/CheckAge?Age=99` として送信されます。

クエリ文字列の正しく書式設定された `age` パラメーターが送信されると、フォームの有効性が確認されます。

[年齢確認] ページの 2 番目のフォームでは、要求本文で `Age` 値が送信され、検証は不合格となります。 `age` パラメーターはクエリ文字列で渡される必要があるため、バインドが失敗します。

検証は既定で有効になっており、<xref:Microsoft.AspNetCore.Mvc.MvcOptions> の <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> プロパティによって制御されます。 最上位ノードの検証を無効にするには、MVC オプション (`Startup.ConfigureServices`) で `AllowValidatingTopLevelNodes` を `false` に設定します。

[!code-csharp[](validation/sample_snapshot/Startup.cs?name=snippet_AddMvc&highlight=4)]

::: moniker-end

## <a name="model-state"></a>モデルの状態

モデルの状態は、送信された HTML フォーム値での検証エラーを表します。

MVC は、エラー数の最大数 (既定値は 200) に達するまで、フィールドの検証を続けます。 この数は、`Startup.ConfigureServices` の次のコードを使用して構成します。

[!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors)]

## <a name="handle-model-state-errors"></a>モデルの状態エラーの処理

モデルの検証は、コントローラー アクションの実行前に発生します。 このアクションは、`ModelState.IsValid` を検査し、適切な対処を行います。 多くの場合において適切な対応は、エラー応答を返すことであり、モデルの検証が失敗した理由を詳しく示すのが理想的です。

::: moniker range=">= aspnetcore-2.1"

Web API コントローラーで `[ApiController]` 属性を使用して `ModelState.IsValid` が `false` と評価されると、問題の詳細を含む自動的な HTTP 400 応答が返されます。 詳細については、「[自動的な HTTP 400 応答](xref:web-api/index#automatic-http-400-responses)」を参照してください。

::: moniker-end

一部のアプリでは、モデル検証エラーを処理するとき、標準的な規則に従います。その場合、そのようなポリシーを実装する場所としてフィルターが適していることがあります。 モデルの状態が有効なとき、および無効なときにアクションがどのように動作するかテストする必要があります。

## <a name="manual-validation"></a>手動検証

モデル バインドと検証が完了した後、その一部を繰り返すことが必要になる場合があります。 たとえば、整数が想定されているフィールドにユーザーがテキストを入力したかもしれない場合や、モデルのプロパティの値を計算する必要がある場合などです。

検証の手動実行が必要になることがあります。 その場合は、次に示すように `TryValidateModel` メソッドを呼び出します。

[!code-csharp[](validation/sample/MoviesController.cs?name=snippet_TryValidateModel)]

## <a name="custom-validation"></a>カスタム検証

検証属性の機能は、ほとんどの検証のニーズに対応します。 しかし、中にはご自身のビジネスに特化した検証ルールもあります。 そのルールは、必須フィールドや値範囲の確認などの一般的なデータ検証方法ではない場合があります。 そのような場合は、カスタム検証属性が優れた解決策です。 MVC では独自のカスタム検証属性を簡単に作成できます。 `ValidationAttribute` から継承し、`IsValid` メソッドをオーバーライドするだけです。 `IsValid` メソッドは 2 つのパラメーターを受け取ります。1 番目は *value* という名前のオブジェクトで、2 番目は *validationContext* という名前の `ValidationContext` オブジェクトです。 *Value* は、カスタム検証メソッドで検証するフィールドの実際の値を示します。

次のサンプルのビジネス ルールでは、ユーザーは 1960 年より後にリリースされた映画のジャンルを *Classic* に設定できないことになっています。 `[ClassicMovie]` 属性は最初にジャンルをチェックし、それが Classic である場合、次にリリース日をチェックして、それが 1960 年以降であるかを確認します。 1960 年より後にリリースされている場合、検証は失敗します。 この属性には、データの検証に使うことができる、年を表す整数パラメーターを指定します。 次のように、属性のコンストラクターでパラメーターの値をキャプチャすることができます。

[!code-csharp[](validation/sample/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

上の `movie` 変数は、フォーム送信からのデータを検証のために格納している `Movie` オブジェクトを表します。 この例では、検証コードはルールに従って `ClassicMovieAttribute` クラスの `IsValid` メソッドで日付とジャンルをチェックします。 検証に成功すると、`IsValid` によって `ValidationResult.Success` コードが返されます。 検証に失敗すると、`ValidationResult` が次のエラー メッセージとともに返されます。

[!code-csharp[](validation/sample/ClassicMovieAttribute.cs?name=snippet_GetErrorMessage)]

ユーザーが `Genre` フィールドを変更してフォームを送信すると、`ClassicMovieAttribute` の `IsValid` メソッドは映画が Classic かどうかを検証します。 他の組み込み属性と同じように、`ClassicMovieAttribute` を `ReleaseDate` などのプロパティに適用し、検証が確実に行われるようにします (前のコード例を参照)。 この例は `Movie` 型でのみ機能するので、次の段落で示すように、`IValidatableObject` を使うのがさらによい方法です。

または、`IValidatableObject` インターフェイスに `Validate` メソッドを実装することで、これと同じコードをモデルに配置することもできます。 カスタム検証属性は個別のプロパティを検証するために使えます。一方、`IValidatableObject` の実装はクラス レベルの検証を実装するために使えます。

[!code-csharp[](validation/sample/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="client-side-validation"></a>クライアント側の検証

クライアント側の検証は、ユーザーにとって非常に便利です。 他の方法を使用した場合に必要な、サーバーへのラウンド トリップを待つ時間が節約できます。 ビジネス的に表現すると、1 回だけ見ればわずかな時間でも、それが毎日何百回も積み重なると、膨大な時間、費用、フラストレーションになります。 簡単かつ迅速な検証は、ユーザーの作業をいっそう効率的にし、入力と出力の品質が向上します。

クライアント側検証が動作するには、ここで示すように、適切な JavaScript スクリプト参照をビューに設定する必要があります。

[!code-cshtml[](validation/sample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/sample/Views/Shared/_ValidationScriptsPartial.cshtml)]

[jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) スクリプトは、人気のある [jQuery Validate](https://jqueryvalidation.org/) プラグインを基に作成された Microsoft のカスタム フロントエンド ライブラリです。 jQuery Unobtrusive Validation を使わないと、同じ検証ロジックを 2 か所でコーディングする必要があります。1 つはモデル プロパティでのサーバー側検証属性で、もう 1 つはクライアント側スクリプトです (jQuery Validate の [`validate()`](https://jqueryvalidation.org/validate/) メソッドの例を見ると、これがどれほど複雑になるかがわかります)。 代わりに、MVC の[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)および [HTML ヘルパー](xref:mvc/views/overview)では、モデル プロパティの検証属性と型メタデータを使って、検証の必要なフォーム要素に HTML 5 の [data- 属性](http://w3c.github.io/html/dom.html#embedding-custom-non-visible-data-with-the-data-attributes)をレンダリングできます。 MVC は、組み込み属性とカスタム属性の両方に対して、`data-` 属性を生成します。 その後、jQuery Unobtrusive Validation は `data-` 属性を解析し、ロジックを jQuery Validate に渡して、クライアントにサーバー側検証ロジックを実質的に "コピー" します。 次に示すように、関連するタグ ヘルパーを使って、クライアントで検証エラーを表示できます。

[!code-cshtml[](validation/sample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

上記のタグ ヘルパーは、以下の HTML をレンダリングします。 HTML 出力の `data-` 属性が、`ReleaseDate` プロパティの検証属性に対応していることに注意してください。 下の `data-val-required` 属性には、ユーザーがリリース日フィールドを入力していない場合に表示するエラー メッセージが含まれています。 jQuery Unobtrusive Validation はこの値を jQuery Validate の [`required()`](https://jqueryvalidation.org/required-method/) メソッドに渡し、このメソッドは付随する **\<span>** 要素にそのメッセージを表示します。

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="" />
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

クライアント側検証は、フォームが有効になるまで送信を許可しません。 送信ボタンをクリックすると、フォームの送信またはエラー メッセージの表示を行う JavaScript が実行されます。

MVC はプロパティの .NET データ型に基づいて型属性の値を特定しますが、場合によってはこの値は `[DataType]` 属性を使ってオーバーライドされています。 基本 `[DataType]` 属性では、実際のサーバー側検証は行われません。 ブラウザーでは独自のエラー メッセージが選択され、自由にエラーが表示されますが、jQuery Validation Unobtrusive パッケージではメッセージをオーバーライドし、他と整合性のあるメッセージを表示できます。 これが最もよくわかるのは、ユーザーが `[EmailAddress]` などの `[DataType]` サブクラスを適用した場合です。

### <a name="add-validation-to-dynamic-forms"></a>動的なフォームに検証を追加する

jQuery Unobtrusive Validation はページが初めて読み込まれるときに検証ロジックとパラメーターを jQuery Validate に渡すので、動的に生成されるフォームでは、検証は自動的には行われません。 代わりに、作成直後に動的フォームを解析するよう、jQuery Unobtrusive Validation に指示する必要があります。 たとえば、次のコードは、AJAX によって追加されるフォームでクライアント側検証を設定する方法を示しています。

```js
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

`$.validator.unobtrusive.parse()` メソッドには、その引数の 1 つで jQuery セレクターを指定します。 このメソッドは、そのセレクター内のフォームの `data-` 属性を解析するよう jQuery Unobtrusive Validation に指示します。 その後、これらの属性の値が jQuery Validate プラグインに渡され、必要なクライアント側検証ルールがフォームで示されます。

### <a name="add-validation-to-dynamic-controls"></a>動的なコントロールに検証を追加する

`<input/>` や `<select/>` などの個別のコントロールが動的に生成されるときに、フォームの検証ルールを更新することもできます。 これらの要素のセレクターを `parse()` メソッドに直接渡すことはできません。格納しているフォームが既に解析されていて更新されないためです。 代わりに、まず既存の検証データを削除した後、次に示すようにフォーム全体を再解析します。

```js
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="iclientmodelvalidator"></a>IClientModelValidator

カスタム属性のクライアント側ロジックを作成することができ、[jquery 検証](http://jqueryvalidation.org/documentation/)へのアダプターを作成する [Unobtrusive Validation](http://bradwilson.typepad.com/blog/2010/10/mvc3-unobtrusive-validation.html) はそれを検証の一部としてクライアントで自動的に実行します。 最初に、次に示すように `IClientModelValidator` インターフェイスを実装することで、追加される data- 属性を制御します。

[!code-csharp[](validation/sample/ClassicMovieAttribute.cs?name=snippet_AddValidation)]

このインターフェイスを実装する属性は、生成されるフィールドに HTML 属性を追加できます。 `ReleaseDate` 要素の出力を調べると、前の例と似た HTML であることがわかりますが、ここでは、`IClientModelValidator` の `AddValidation` メソッドで定義された `data-val-classicmovie` 属性があります。

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="" />
```

Unobtrusive Validation は、`data-` 属性のデータを使ってエラー メッセージを表示します。 ただし、jQuery が規則やメッセージを認識するのは、jQuery の `validator` オブジェクトにそれらが追加されてからです。 次の例でこれを示します。ここでは、カスタムの `classicmovie` クライアント検証メソッドが、jQuery `validator` オブジェクトに追加されます。 `unobtrusive.adapters.add` メソッドの説明については、[ASP.NET MVC での控え目なクライアント検証](http://bradwilson.typepad.com/blog/2010/10/mvc3-unobtrusive-validation.html)に関するページをご覧ください。

[!code-javascript[](validation/sample/Views/Movies/Create.cshtml?name=snippet_UnobtrusiveValidation)]

上のコードでは、`classicmovie` メソッドが映画のリリース日のクライアント側検証を実行しています。 メソッドが `false` を返す場合、エラー メッセージが表示されます。

## <a name="remote-validation"></a>リモート検証

リモート検証は、クライアント上のデータをサーバー上のデータに対して検証する必要があるときに使う優れた機能です。 たとえば、アプリでメール アドレスやユーザー名が既に使われているかどうかを検証する必要があり、そのために大量のデータをクエリする必要があるような場合です。 1 つまたは少数のフィールドを検証するために大きなデータ セットをダウンロードするのでは、消費するリソースが多すぎます。 機密情報が漏えいする可能性もあります。 代わりの方法として、ラウンド トリップ要求を行って、フィールドを検証します。

リモート検証は、2 ステップのプロセスで実装できます。 最初に、`[Remote]` 属性でモデルに注釈を付ける必要があります。 `[Remote]` 属性には複数のオーバーロードが指定できるので、それを使って、クライアント側の JavaScript が適切なコードを呼び出すようにします。 次の例では、`Users` コントローラーの `VerifyEmail` アクション メソッドを指し示しています。

[!code-csharp[](validation/sample/User.cs?name=snippet_UserEmailProperty)]

2 番目のステップでは、`[Remote]` 属性で定義されている対応するアクション メソッドに、検証コードを配置します。 Jquery Validate の[リモート](https://jqueryvalidation.org/remote-method/) メソッドに関するドキュメントによれば、サーバーの応答は次のいずれかの JSON 文字列である必要があります。

* 有効な要素の場合は `"true"`。
* 無効な要素の場合は `"false"`、`undefined`、または `null` (既定のエラー メッセージを使用)。

サーバーの応答が文字列 (例: `"That name is already taken, try peter123 instead"`) である場合、文字列は既定の文字列の代わりにカスタム エラー メッセージとして表示されます。

次に示すように、`VerifyEmail` メソッドの定義はこれらの規則に従います。 このメソッドは、メール アドレスが使われている場合は検証エラー メッセージを返し、メール アドレスが空いている場合は `true` を返します。また、結果を `JsonResult` オブジェクトにラップします。 クライアント側では、返された値を使って、続行するか、必要な場合はエラーを表示できます。

[!code-csharp[](validation/sample/UsersController.cs?name=snippet_VerifyEmail)]

ユーザーがメール アドレスを入力すると、ビューの JavaScript はリモート呼び出しを行って、そのメール アドレスが使われているかどうかを確認し、使われている場合はエラー メッセージを表示します。 使われていない場合は、ユーザーは普通にフォームを送信できます。

`[Remote]` 属性の `AdditionalFields` プロパティは、フィールドの組み合わせをサーバー上のデータに対して検証するときに役立ちます。 たとえば、上の `User` モデルに `FirstName` および `LastName` という名前の 2 つの追加プロパティがある場合、その名前のペアを使う既存ユーザーがいないことを確認したい場合があるかもしれません。 次のコードで示すように、新しいプロパティを定義します。

[!code-csharp[](validation/sample/User.cs?name=snippet_UserNameProperties)]

`AdditionalFields` を文字列 `"FirstName"` および `"LastName"` に明示的に設定することもできますが、[`nameof`](/dotnet/csharp/language-reference/keywords/nameof) 演算子をこのように使うと、後のリファクタリングが容易になります。 検証を実行するアクション メソッドには、`FirstName` の値と `LastName` の値のそれぞれに対応する 2 つの引数を指定する必要があります。

[!code-csharp[](validation/sample/UsersController.cs?name=snippet_VerifyName)]

ここで、ユーザーが姓と名を入力すると、JavaScript は以下のことを行います。

* リモート呼び出しを行って、その名前のペアが使われているかどうかを確認します。
* ペアが使われている場合は、エラー メッセージが表示されます。
* 使われていない場合は、ユーザーはフォームを送信できます。

複数の追加フィールドを `[Remote]` 属性で検証する必要がある場合は、コンマ区切りのリストとして提供します。 たとえば、`MiddleName` プロパティをモデルに追加するには、`[Remote]` 属性を次のコードのように設定します。

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

他の属性引数と同じように、`AdditionalFields` も定数式である必要があります。 したがって、[補間文字列](/dotnet/csharp/language-reference/keywords/interpolated-strings)を使ったり、<xref:System.String.Join*> を呼び出して `AdditionalFields` を初期化したりすることはできません。 `[Remote]` 属性に新しいフィールドを追加するごとに、対応するコントローラー アクション メソッドに別の引数を追加する必要があります。
