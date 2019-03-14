---
title: ASP.NET Core の Razor 構文リファレンス
author: rick-anderson
description: Web ページにサーバー ベースのコードを埋め込むための Razor マークアップの構文について説明します。
ms.author: riande
ms.date: 10/26/2018
uid: mvc/views/razor
ms.openlocfilehash: 8e9ec3c5040e5a24cd5f773b1232897338741c0c
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57052499"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a>ASP.NET Core の Razor 構文リファレンス

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Luke Latham](https://github.com/guardrex)、[Taylor Mullen](https://twitter.com/ntaylormullen)、[Dan Vicarel](https://github.com/Rabadash8820)

Razor は、Web ページにサーバー ベースのコードを埋め込むためのマークアップ構文です。 Razor 構文は、Razor マークアップ、C#、HTML で構成されます。 通常、Razor を含むファイルのファイル拡張子は *.cshtml* です。

## <a name="rendering-html"></a>HTML のレンダリング

Razor の既定の言語は HTML です。 Razor マークアップからの HTML のレンダリングは、HTML ファイルからの HTML のレンダリングと同じです。 *.cshtml* Razor ファイル内の HTML マークアップは、サーバーによって変更されずにレンダリングされます。

## <a name="razor-syntax"></a>Razor の構文

Razor は C# をサポートし、`@` 記号を使って HTML から C# に移行します。 Razor は C# の式を評価し、それらを HTML の出力でレンダリングします。

`@` 記号の後に [Razor の予約済みキーワード](#razor-reserved-keywords)が続いている場合は、Razor 固有のマークアップに移行します。 それ以外の場合は、普通の C# に移行します。

Razor マークアップで `@` 記号をエスケープするには、`@` 記号をもう 1 つ使います。

```cshtml
<p>@@Username</p>
```

HTML では、コードは 1 つの `@` 記号でレンダリングされます。

```html
<p>@Username</p>
```

メール アドレスを含む HTML の属性とコンテンツは、`@` 記号を遷移文字として扱いません。 次の例のメール アドレスは、Razor の解析ではそのまま残ります。

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a>暗黙的な Razor 式

Razor の暗黙的な式は、`@` で始まって C# のコードが続きます。

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

C# の `await` キーワードを除き、暗黙的な式にスペースを含めることはできません。 C# のステートメントに明確な終わりがある場合は、スペースを混在させることができます。

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

暗黙的な式では、山かっこ (`<>`) の内側の文字は HTML タグとして解釈されるため、C# ジェネリックを含めることは**できません**。 次のコードは有効では**ありません**。

```cshtml
<p>@GenericMethod<int>()</p>
```

上記のコードでは、次のいずれかのようなコンパイラ エラーが生成されます。

 * The "int" element wasn't closed. All elements must be either self-closing or have a matching end tag. ("int" 要素が閉じられませんでした。すべての要素は、自己終了するか、対応する終了タグが存在する必要があります。)
 *  メソッド グループ 'GenericMethod' を非デリゲート型 'object' に変換することはできません。 このメソッドを呼び出しますか? 
 
ジェネリック メソッドの呼び出しは、[明示的な Razor 式](#explicit-razor-expressions)または [Razor コード ブロック](#razor-code-blocks)にラップする必要があります。

## <a name="explicit-razor-expressions"></a>明示的な Razor 式

明示的な Razor 式は、`@` 記号とバランスの取れたかっこ記号で構成されます。 1 週間前の時刻を表示するには、次の Razor マークアップを使います。

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

`@()` のかっこ内の内容が評価されて、出力にレンダリングされます。

前のセクションで説明した暗黙的な式は、一般に、スペースを含むことはできません。 次のコードでは、現在時刻から 1 週間は減算されません。

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

このコードでは、次のような HTML がレンダリングされます。

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

明示的な式を使うと、テキストと式の結果を連結できます。

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

明示的な式を使わないと、`<p>Age@joe.Age</p>` はメール アドレスとして扱われ、`<p>Age@joe.Age</p>` がレンダリングされます。 明示的な式として記述すると、`<p>Age33</p>` がレンダリングされます。

明示的な式を使うと、ジェネリック メソッドから *.cshtml* ファイルに出力できます。 次のマークアップは、C# ジェネリックの山かっこによって発生した前述のエラーを修正する方法について示します。 コードは明示的な式として書き込まれます。

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a>式のエンコード

文字列として評価される C# の式は、HTML でエンコードされます。 `IHtmlContent` として評価される C# の式は、`IHtmlContent.WriteTo` によって直接レンダリングされます。 `IHtmlContent` として評価されない C# の式は、`ToString` によって文字列に変換され、レンダリングされる前にエンコードされます。

```cshtml
@("<span>Hello World</span>")
```

このコードでは、次のような HTML がレンダリングされます。

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

HTML はブラウザーで次のように表示されます。

```
<span>Hello World</span>
```

`HtmlHelper.Raw` の出力はエンコードされませんが、HTML マークアップとしてレンダリングされません。

> [!WARNING]
> サニタイズされていないユーザー入力で `HtmlHelper.Raw` を使うと、セキュリティ上のリスクがあります。 ユーザー入力には、悪意のある JavaScript または他の攻撃が含まれる可能性があります。 ユーザー入力をサニタイズすることは困難です。 ユーザー入力では `HtmlHelper.Raw` を使わないでください。

```cshtml
@Html.Raw("<span>Hello World</span>")
```

このコードでは、次のような HTML がレンダリングされます。

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a>Razor コード ブロック

Razor コード ブロックは、`@` で始まり、`{}` で囲まれています。 式とは異なり、コード ブロック内の C# コードはレンダリングされません。 ビュー内のコード ブロックと式は同じスコープを共有し、次の順序で定義されます。

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

このコードでは、次のような HTML がレンダリングされます。

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

### <a name="implicit-transitions"></a>暗黙の移行

コード ブロック内の既定の言語は C# ですが、Razor ページは HTML に移行して戻ることがあります。

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a>明示的に区切られた遷移

HTML をレンダリングする必要のあるコード ブロックのサブセクションを定義するには、レンダリングする文字を Razor の **\<text>** タグで囲みます。

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

HTML タグによって囲まれていない HTML をレンダリングするには、この方法を使います。 HTML タグまたは Razor タグがない場合、Razor ラインタイム エラーが発生します。

**\<text>** タグは、内容をレンダリングするときに空白文字を制御するのに便利です。

* **\<text>** タグの間の内容だけがレンダリングされます。 
* **\<text>** タグの前後にある空白文字は HTML の出力には表示されません。

### <a name="explicit-line-transition-with-"></a>@: による明示的な行の遷移

残りの行全体をコード ブロック内に HTML としてレンダリングするには、`@:` 構文を使います。

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

コードに `@:` がないと、Razor ランタイム エラーが生成されます。

警告 :Razor ファイルに余分な `@` 文字があると、ブロックの後続のステートメントでコンパイラ エラーが発生することがあります。 これらのコンパイラ エラーは、実際のエラーは報告されたエラーより前で発生しているため、理解するのが難しい場合があります。 このエラーは、複数の暗黙的/明示的な式を 1 つのコード ブロックに結合した後で発生することがよくあります。

## <a name="control-structures"></a>制御構造

制御構造は、コード ブロックの拡張機能です。 コード ブロックのすべての側面 (マークアップへの遷移、インライン C# ) が、次の構造にも適用されます。

### <a name="conditionals-if-else-if-else-and-switch"></a>条件付き @if、else if、else、@switch

`@if` は、いつコードを実行するかを制御します。

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

`else` および `else if` には、`@` 記号は必要ありません。

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

次のマークアップでは、switch ステートメントの使い方を示します。

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a>@for、@foreach、@while、@do while のループ

ループ制御ステートメントを使って、テンプレート化された HTML をレンダリングできます。 人の一覧をレンダリングするには:

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

以下のループ ステートメントがサポートされています。

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a>複合 @using

C# では、オブジェクトを確実に破棄するために `using` オブジェクトが使われています。 Razor では、同じメカニズムが、追加コンテンツを含む HTML ヘルパーを作成するために使われています。 次のコードの HTML ヘルパーは、`@using` ステートメントを含むフォーム タグをレンダリングします。


```cshtml
@using (Html.BeginForm())
{
    <div>
        email:
        <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

スコープ レベルのアクションは、[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)を使って実行できます。

### <a name="try-catch-finally"></a>@try、catch、finally

例外処理は C# に似ています。

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a>@lock

Razor には、重要なセクションを lock ステートメントで保護する機能があります。

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>コメント

Razor は、C# と HTML のコメントをサポートしています。

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

このコードでは、次のような HTML がレンダリングされます。

```html
<!-- HTML comment -->
```

Razor のコメントは、Web ページがレンダリングされる前に、サーバーによって削除されます。 Razor では、`@*  *@` を使ってコメントを区切ります。 次のコードはコメント化されているため、サーバーはどのマークアップもレンダリングしません。

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a>ディレクティブ

Razor のディレクティブは、`@` 記号の後の予約キーワードによる暗黙的な式で表されます。 通常、ディレクティブは、ビューの解析方法を変更したり、異なる機能を有効にしたりします。

Razor がビューのコードを生成する方法を理解すると、ディレクティブの動作を理解しやすくなります。

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

上のコードでは、次のようなクラスが生成されます。

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

後の「[ビューに対して生成された Razor C# クラスを調べる](#inspect-the-razor-c-class-generated-for-a-view)」セクションでは、この生成されたクラスを表示する方法について説明します。

<a name="using"></a>
### <a name="using"></a>@using

`@using` ディレクティブは、生成されるビューに C# の `using` ディレクティブを追加します。

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

### <a name="model"></a>@model

`@model` ディレクティブは、ビューに渡されるモデルの型を指定します。

```cshtml
@model TypeNameOfModel
```

個々のユーザー アカウントで作成される ASP.NET Core MVC アプリの *Views/Account/Login.cshtml* ビューには、次のモデル宣言が含まれます。

```cshtml
@model LoginViewModel
```

生成されるクラスは、`RazorPage<dynamic>` を継承します。

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Razor では、ビューに渡されるモデルにアクセスするための `Model` プロパティが公開されています。

```cshtml
<div>The Login Email: @Model.Email</div>
```

`@model` ディレクティブは、このプロパティの型を指定します。 ディレクティブでは、ビューが派生する生成されたクラスの `T` を `RazorPage<T>` で指定します。 `@model` ディレクティブが指定されていない場合、`Model` プロパティは `dynamic` 型になります。 モデルの値は、コントローラーからビューに渡されます。 詳細については、「[厳密に型指定されたモデルと &commat;model キーワード](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword)」を参照してください。

### <a name="inherits"></a>@inherits

`@inherits` ディレクティブは、ビューが継承するクラスの完全な制御を提供します。

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

次のコードは、カスタム Razor ページ型です。

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

`CustomText` がビューに表示されます。

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

このコードでは、次のような HTML がレンダリングされます。

```html
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

 `@model` と `@inherits` は同じビューで使うことができます。 `@inherits` は、ビューがインポートする *_ViewImports.cshtml* ファイルで指定できます。

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

次のコードは、厳密に型指定されたビューの例です。

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

モデルに rick@contoso.com が渡された場合、ビューは次の HTML マークアップを生成します。

```html
<div>The Login Email: rick@contoso.com</div>
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

### <a name="inject"></a>@inject

`@inject` ディレクティブを使うと、Razor ページで[サービス コンテナー](xref:fundamentals/dependency-injection)からビューにサービスを挿入できます。 詳しくは、「[ビューへの依存関係の挿入](xref:mvc/views/dependency-injection)」をご覧ください。

### <a name="functions"></a>@functions

`@functions` ディレクティブを使うと、Razor ページで C# コード ブロックをビューに追加できます。

```cshtml
@functions { // C# Code }
```

次に例を示します。

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

このコードは、次の HTML マークアップを生成します。

```html
<div>From method: Hello</div>
```

次のコードは、生成された Razor C# クラスです。

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

### <a name="section"></a>@section

`@section` ディレクティブを[レイアウト](xref:mvc/views/layout)と組み合わせて使うと、HTML ページのさまざまな部分のコンテンツをビューでレンダリングできます。 詳しくは、「[セクション](xref:mvc/views/layout#layout-sections-label)」をご覧ください。

## <a name="templated-razor-delegates"></a>テンプレート化された Razor デリゲート

Razor テンプレートを使用すると、次の形式で UI スニペットを定義できます。

```cshtml
@<tag>...</tag>
```

次の例では、テンプレート化された Razor デリゲートを <xref:System.Func`2> として指定する方法を示します。 デリゲートによってカプセル化されるメソッドのパラメーターに対しては、[dynamic 型](/dotnet/csharp/programming-guide/types/using-type-dynamic)を指定します。 デリゲートの戻り値としては、[object 型](/dotnet/csharp/language-reference/keywords/object)を指定します。 テンプレートは、`Name` プロパティを持つ `Pet` の <xref:System.Collections.Generic.List`1> で使用されます。

```csharp
public class Pet
{
    public string Name { get; set; }
}
```

```cshtml
@{
    Func<dynamic, object> petTemplate = @<p>You have a pet named <strong>@item.Name</strong>.</p>;

    var pets = new List<Pet>
    {
        new Pet { Name = "Rin Tin Tin" },
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "K-9" }
    };
}
```

テンプレートは、`foreach` ステートメントによって提供される `pets` で表示されます。

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

表示される出力:

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

メソッドへの引数としてインライン Razor テンプレートを指定することもできます。 次の例では、`Repeat` メソッドは Razor テンプレートを受け取ります。 メソッドは、テンプレートを使用して、リストから提供される項目の繰り返しで HTML コンテンツを生成します。

```cshtml
@using Microsoft.AspNetCore.Html

@functions {
    public static IHtmlContent Repeat(IEnumerable<dynamic> items, int times, 
        Func<dynamic, IHtmlContent> template)
    {
        var html = new HtmlContentBuilder();

        foreach (var item in items)
        {
            for (var i = 0; i < times; i++)
            {
                html.AppendHtml(template(item));
            }
        }

        return html;
    }
}
```

前の例のペットのリストを使用して、次のように `Repeat` メソッドを呼び出します。

* <xref:System.Collections.Generic.List`1> の `Pet`。
* 各ペットを繰り返す回数。
* 順不同のリストのリスト項目に対して使用するインライン テンプレート。

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

表示される出力:

```html
<ul>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>K-9</li>
    <li>K-9</li>
    <li>K-9</li>
</ul>
```

## <a name="tag-helpers"></a>タグ ヘルパー

[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)に関する 3 つのディレクティブがあります。

| ディレクティブ | 関数 |
| --------- | -------- |
| [&commat;addTagHelper](xref:mvc/views/tag-helpers/intro#add-helper-label) | ビューでタグ ヘルパーを使えるようにします。 |
| [&commat;removeTagHelper](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | 前に追加したタグ ヘルパーをビューから削除します。 |
| [&commat;tagHelperPrefix](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | タグ プレフィックスを指定して、タグ ヘルパーのサポートを有効にしたり、タグ ヘルパーの使用を明示的にしたりします。 |

## <a name="razor-reserved-keywords"></a>Razor の予約済みキーワード

### <a name="razor-keywords"></a>Razor のキーワード

* page (ASP.NET Core 2.0 以降が必要)
* namespace
* 関数
* 継承
* モデル
* section
* helper (現在は ASP.NET Core ではサポートされていません)

Razor のキーワードは、`@(Razor Keyword)` でエスケープします (例: `@(functions)`)。

### <a name="c-razor-keywords"></a>C# Razor のキーワード

* case
* do
* default
* for
* foreach
* if
* else
* lock
* switch
* try
* catch
* finally
* 使用
* while

C# Razor のキーワードは、`@(@C# Razor Keyword)` で二重にエスケープする必要があります (例: `@(@case)`)。 最初の `@` は、Razor パーサーをエスケープします。 2 番目の `@` は、C# パーサーをエスケープします。

### <a name="reserved-keywords-not-used-by-razor"></a>Razor で使われない予約済みキーワード

* class

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a>ビューに対して生成された Razor C# クラスを調べる

::: moniker range=">= aspnetcore-2.1"

.NET Core SDK 2.1 以降、[Razor SDK](xref:razor-pages/sdk) は Razor ファイルのコンパイルを処理します。 プロジェクトを作成する際に、Razor SDK はプロジェクト ルートに *obj/<build_configuration>/<target_framework_moniker>/Razor* ディレクトリを生成します。 *Razor* ディレクトリ内のディレクトリ構造は、プロジェクトのディレクトリ構造をミラー化します。

.NET Core 2.1 をターゲットとする ASP.NET Core 2.1 Razor Pages プロジェクト内の次のディレクトリ構造を考えてみましょう。

* **Areas/**
  * **Admin/**
    * **Pages/**
      * *Index.cshtml*
      * *Index.cshtml.cs*
* **Pages/**
  * **Shared/**
    * *_Layout.cshtml*
  * *_ViewImports.cshtml*
  * *_ViewStart.cshtml*
  * *Index.cshtml*
  * *Index.cshtml.cs*

*Debug* 構成でプロジェクトを作成すると、次の *obj* ディレクトリが生成されます。

* **obj/**
  * **Debug/**
    * **netcoreapp2.1/**
      * **Razor/**
        * **Areas/**
          * **Admin/**
            * **Pages/**
              * *Index.g.cshtml.cs*
        * **Pages/**
          * **Shared/**
            * *_Layout.g.cshtml.cs*
          * *_ViewImports.g.cshtml.cs*
          * *_ViewStart.g.cshtml.cs*
          * *Index.g.cshtml.cs*

*Pages/Index.cshtml* に対して生成されたクラスを表示するには、*obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs* を開きます。

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

次のクラスを ASP.NET Core MVC プロジェクトに追加します。

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

`Startup.ConfigureServices` で、MVC によって追加された `RazorTemplateEngine` を `CustomTemplateEngine` クラスでオーバーライドします。

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

`CustomTemplateEngine` の `return csharpDocument;` ステートメントにブレークポイントを設定します。 プログラムの実行がブレークポイントで停止したら、`generatedCode` の値を表示します。

![generatedCode のテキスト ビジュアライザーの表示](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a>ビューの参照と大文字/小文字の区別

Razor ビュー エンジンによるビューの参照では、大文字と小文字が区別されます。 ただし、実際の参照は、基になるファイル システムによって決定されます。

* ファイル ベースのソース:
  * 大文字と小文字が区別されないファイル システムを使っているオペレーティング システム (Windows など) では、物理的なファイル プロバイダーの参照は大文字と小文字を区別しません。 たとえば、`return View("Test")` は、*/Views/Home/Test.cshtml*、*/Views/home/test.cshtml*、その他のすべての大文字と小文字のバリエーションと一致します。
  * 大文字と小文字が区別されるファイル システム (たとえば、Linux、OSX、および `EmbeddedFileProvider`) では、参照は大文字と小文字を区別します。 たとえば、`return View("Test")` は */Views/Home/Test.cshtml* だけと一致します。
* プリコンパイル済みのビュー:ASP.NET Core 2.0 以降では、プリコンパイル済みのビューの参照は、すべてのオペレーティング システムで大文字と小文字を区別しません。 動作は、Windows での物理ファイル プロバイダーの動作と同じです。 2 つのプリコンパイル済みビューの相違点が大文字と小文字の使い分けだけの場合、参照の結果はどちらになるかわかりません。

開発者には、ファイル名とディレクトリ名の大文字/小文字の使い分けを、次のものと一致させることをお勧めします。

* 領域、コントローラー、アクションの名前。
* Razor ページ。

大文字と小文字の使い分けを一致させると、展開は基になっているファイル システムに関係なくビューを検索できます。
