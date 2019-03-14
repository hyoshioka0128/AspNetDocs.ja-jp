---
title: 以下であるため、Sass、Font Awesome で ASP.NET Core
author: ardalis
description: ASP.NET Core アプリケーションで Less、Sass、および Font Awesome を使用する方法を説明します。
ms.author: tdykstra
ms.date: 10/14/2016
uid: client-side/less-sass-fa
ms.openlocfilehash: 2229c4e3b0238ff17c15e78f657b9acb10495c72
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57065559"
---
# <a name="less-sass-and-font-awesome-in-aspnet-core"></a><span data-ttu-id="6476a-103">以下であるため、Sass、Font Awesome で ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6476a-103">Less, Sass, and Font Awesome in ASP.NET Core</span></span>

<span data-ttu-id="6476a-104">作成者: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="6476a-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="6476a-105">Web アプリケーションのユーザーには、スタイルと全体的なエクスペリエンスに関して非常に高い期待を抱いています。</span><span class="sxs-lookup"><span data-stu-id="6476a-105">Users of web applications have increasingly high expectations when it comes to style and overall experience.</span></span> <span data-ttu-id="6476a-106">最新のWebアプリケーションでは、豊富なツールとフレームワークを頻繁に活用して一貫した方法で外観や操作性を定義および管理しています。</span><span class="sxs-lookup"><span data-stu-id="6476a-106">Modern web applications frequently leverage rich tools and frameworks for defining and managing their look and feel in a consistent manner.</span></span> <span data-ttu-id="6476a-107">[Bootstrap](http://getbootstrap.com/) のようなフレームワークは、Webサイトのスタイルとレイアウト オプションの共通セットの定義に非常に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="6476a-107">Frameworks like [Bootstrap](http://getbootstrap.com/) can go a long way toward defining a common set of styles and layout options for web sites.</span></span> <span data-ttu-id="6476a-108">ただし、ほとんどの重要なサイトもスタイルやカスケードスタイルシート(CSS)ファイルを効果的に定義および管理できることに加え、サイトのインターフェイスをより直観的なものにする画像以外のアイコンに簡単にアイコンできることの恩恵にあずかっています。</span><span class="sxs-lookup"><span data-stu-id="6476a-108">However, most non-trivial sites also benefit from being able to effectively define and maintain styles and cascading style sheet (CSS) files, as well as having easy access to non-image icons that help make the site's interface more intuitive.</span></span> <span data-ttu-id="6476a-109">こうした中で、[Less](http://lesscss.org/)と[Sass](http://sass-lang.com/)、そして[Font Awesome](http://fontawesome.io/)のようなライブラリをサポートする言語とツールが役に立ちます。</span><span class="sxs-lookup"><span data-stu-id="6476a-109">That's where languages and tools that support [Less](http://lesscss.org/) and [Sass](http://sass-lang.com/), and libraries like [Font Awesome](http://fontawesome.io/), come in.</span></span>

## <a name="css-preprocessor-languages"></a><span data-ttu-id="6476a-110">CSSプリプロセッサ言語</span><span class="sxs-lookup"><span data-stu-id="6476a-110">CSS preprocessor languages</span></span>

<span data-ttu-id="6476a-111">基になる言語の操作のエクスペリエンスを向上させるために、他の言語にコンパイルされる言語は、プリプロセッサと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="6476a-111">Languages that are compiled into other languages, in order to improve the experience of working with the underlying language, are referred to as preprocessors.</span></span> <span data-ttu-id="6476a-112">CSS の 2 つの一般的なプリプロセッサがあります。小さいと Sass します。</span><span class="sxs-lookup"><span data-stu-id="6476a-112">There are two popular preprocessors for CSS: Less and Sass.</span></span>  <span data-ttu-id="6476a-113">これらのプリプロセッサでは、変数と入れ子になったルールは、大規模で複雑なスタイルシートの保守容易性を向上させるためのサポートなど、CSSに機能を追加します。</span><span class="sxs-lookup"><span data-stu-id="6476a-113">These preprocessors add features to CSS, such as support for variables and nested rules, which improve the maintainability of large, complex stylesheets.</span></span> <span data-ttu-id="6476a-114">言語としてのCSSは非常に基本的なサポートも、変数と、単純なものが欠落していると、傾向が肥大化し、反復的なCSSファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="6476a-114">CSS as a language is very basic, lacking support even for something as simple as variables, and this tends to make CSS files repetitive and bloated.</span></span> <span data-ttu-id="6476a-115">プリプロセッサを使用して実際のプログラミング言語の機能を追加するのに役立つの重複を減らすのスタイルルールより効率的な整理を提供できます。</span><span class="sxs-lookup"><span data-stu-id="6476a-115">Adding real programming language features via preprocessors can help reduce duplication and provide better organization of styling rules.</span></span> <span data-ttu-id="6476a-116">Visual Studio では、両方の以下の組み込みサポートとSass、だけでなくこれらの言語を使用する場合、開発エクスペリエンスがさらに向上する拡張機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="6476a-116">Visual Studio provides built-in support for both Less and Sass, as well as extensions that can further improve the development experience when working with these languages.</span></span>

<span data-ttu-id="6476a-117">プリプロセッサが読みやすさとスタイル情報の保守容易性を向上する方法の簡単な例としては、このCSSを考慮してください。</span><span class="sxs-lookup"><span data-stu-id="6476a-117">As a quick example of how preprocessors can improve readability and maintainability of style information, consider this CSS:</span></span>

```css
.header {
    color: black;
    font-weight: bold;
    font-size: 18px;
    font-family: Helvetica, Arial, sans-serif;
}

.small-header {
    color: black;
    font-weight: bold;
    font-size: 14px;
    font-family: Helvetica, Arial, sans-serif;
}
```

<span data-ttu-id="6476a-118">以下を使用して、この書き直すことができます、重複をすべて取り除くを使用して、 *mixin* (という名前は"mix"することができます、1つのクラスまたは別のルールセットからのプロパティ)。</span><span class="sxs-lookup"><span data-stu-id="6476a-118">Using Less, this can be rewritten to eliminate all of the duplication, using a *mixin* (so named because it allows you to "mix in" properties from one class or rule-set into another):</span></span>

```less
.header {
    color: black;
    font-weight: bold;
    font-size: 18px;
    font-family: Helvetica, Arial, sans-serif;
}

.small-header {
    .header;
    font-size: 14px;
}
```

## <a name="less"></a><span data-ttu-id="6476a-119">Less</span><span class="sxs-lookup"><span data-stu-id="6476a-119">Less</span></span>

<span data-ttu-id="6476a-120">Less CSS プリプロセッサでは、Node.js を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="6476a-120">The Less CSS preprocessor runs using Node.js.</span></span> <span data-ttu-id="6476a-121">以下をインストールするには、コマンド プロンプトからノード パッケージ マネージャー (npm) を使用 (-g 意味「グローバル」)。</span><span class="sxs-lookup"><span data-stu-id="6476a-121">To install Less, use Node Package Manager (npm) from a command prompt (-g means "global"):</span></span>

```console
npm install -g less
```

<span data-ttu-id="6476a-122">Visual Studioを使用している場合より少ないリソースをプロジェクトに以下の1つまたは複数のファイルを追加し、コンパイル時にそれを処理するGulp(またはGrunt)を構成で始めることができます。</span><span class="sxs-lookup"><span data-stu-id="6476a-122">If you're using Visual Studio, you can get started with Less by adding one or more Less files to your project, and then configuring Gulp (or Grunt) to process them at compile-time.</span></span> <span data-ttu-id="6476a-123">追加、*スタイル*フォルダーをプロジェクトに追加し、という名前のファイルより新しい*main.less*このフォルダーにします。</span><span class="sxs-lookup"><span data-stu-id="6476a-123">Add a *Styles* folder to your project, and then add a new Less file named *main.less* to this folder.</span></span>

![Less ファイルを追加します。](less-sass-fa/_static/add-less-file.png)

<span data-ttu-id="6476a-125">追加されると、フォルダー構造は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="6476a-125">Once added, your folder structure should look something like this:</span></span>

![フォルダー構造](less-sass-fa/_static/folder-structure.png)

<span data-ttu-id="6476a-127">今すぐCSSにコンパイルされ、Gulpでwwwrootフォルダーに展開されているファイルをいくつかの基本的なスタイルを追加できます。</span><span class="sxs-lookup"><span data-stu-id="6476a-127">Now you can add some basic styling to the file, which will be compiled into CSS and deployed to the wwwroot folder by Gulp.</span></span>

<span data-ttu-id="6476a-128">変更*main.less*に含める次の内容は、1つの基本色から単純なカラーパレットを作成します。</span><span class="sxs-lookup"><span data-stu-id="6476a-128">Modify *main.less* to include the following content, which creates a simple color palette from a single base color.</span></span>

```less
@base: #663333;
@background: spin(@base, 180);
@lighter: lighten(spin(@base, 5), 10%);
@lighter2: lighten(spin(@base, 10), 20%);
@darker: darken(spin(@base, -5), 10%);
@darker2: darken(spin(@base, -10), 20%);

body {
    background-color:@background;
}
.baseColor  {color:@base}
.bgLight    {color:@lighter}
.bgLight2   {color:@lighter2}
.bgDark     {color:@darker}
.bgDark2    {color:@darker2}
```

<span data-ttu-id="6476a-129">`@base` およびその他、@-prefixed項目は、変数。</span><span class="sxs-lookup"><span data-stu-id="6476a-129">`@base` and the other @-prefixed items are variables.</span></span> <span data-ttu-id="6476a-130">それぞれは、色を表します。</span><span class="sxs-lookup"><span data-stu-id="6476a-130">Each of them represents a color.</span></span> <span data-ttu-id="6476a-131">除く`@base`色の関数の使用を設定すると、:明るく、暗くする、および回転します。</span><span class="sxs-lookup"><span data-stu-id="6476a-131">Except for `@base`, they're set using color functions: lighten, darken, and spin.</span></span> <span data-ttu-id="6476a-132">明るくして、画面の非常に期待されるものです操作を行います。スピンは、(周囲の色は、ホイール)度の数によって、色の色合いを調整します。</span><span class="sxs-lookup"><span data-stu-id="6476a-132">Lighten and darken do pretty much what you would expect; spin adjusts the hue of a color by a number of degrees (around the color wheel).</span></span> <span data-ttu-id="6476a-133">小さいプロセッサには使用されていない変数を無視するためにどこかに使用する必要はこれらの変数の動作を示すためには、します。</span><span class="sxs-lookup"><span data-stu-id="6476a-133">The Less processor is smart enough to ignore variables that aren't used, so to demonstrate how these variables work, we need to use them somewhere.</span></span> <span data-ttu-id="6476a-134">クラスは、`.baseColor`などが生成されたCSSファイルで変数のそれぞれの計算値をデモンストレーションします。</span><span class="sxs-lookup"><span data-stu-id="6476a-134">The classes `.baseColor`, etc. will demonstrate the calculated values of each of the variables in the CSS file that's produced.</span></span>

### <a name="get-started"></a><span data-ttu-id="6476a-135">作業開始</span><span class="sxs-lookup"><span data-stu-id="6476a-135">Get started</span></span>

<span data-ttu-id="6476a-136">作成、**npm 構成ファイル**(*package.json*) のプロジェクトフォルダーを参照するように編集および`gulp`と`gulp-less`:</span><span class="sxs-lookup"><span data-stu-id="6476a-136">Create an **npm Configuration File** (*package.json*) in your project folder and edit it to reference `gulp` and `gulp-less`:</span></span>

```json
{
  "version": "1.0.0",
  "name": "asp.net",
  "private": true,
  "devDependencies": {
    "gulp": "3.9.1",
    "gulp-less": "3.3.0"
  }
}
```

<span data-ttu-id="6476a-137">またはVisual Studioで、プロジェクトのフォルダーで、コマンドプロンプトでいずれかの依存関係をインストール**ソリューションエクスプローラー**(**の依存関係 > npm > パッケージを復元**)。</span><span class="sxs-lookup"><span data-stu-id="6476a-137">Install the dependencies either at a command prompt in your project folder, or in Visual Studio **Solution Explorer** (**Dependencies > npm > Restore packages**).</span></span>

```console
npm install
```

![VS パッケージを復元します。](less-sass-fa/_static/restore-packages.png)

<span data-ttu-id="6476a-139">プロジェクトフォルダーに作成、**構成ファイルのGulp** (*gulpfile.js*) 自動プロセスを定義します。</span><span class="sxs-lookup"><span data-stu-id="6476a-139">In the project folder, create a **Gulp Configuration File** (*gulpfile.js*) to define the automated process.</span></span>  <span data-ttu-id="6476a-140">表す未満、ファイルサービスおよび以下を実行するタスクの上部にある変数を追加します。</span><span class="sxs-lookup"><span data-stu-id="6476a-140">Add a variable at the top of the file to represent Less, and a task to run Less:</span></span>

```javascript
var gulp = require("gulp"),
  fs = require("fs"),
  less = require("gulp-less");

gulp.task("less", function () {
  return gulp.src('Styles/main.less')
    .pipe(less())
    .pipe(gulp.dest('wwwroot/css'));
});
```

<span data-ttu-id="6476a-141">開く、**タスクランナーエクスプローラー** (**ビュー > その他の Windows > タスク ランナー エクスプ ローラー**)。</span><span class="sxs-lookup"><span data-stu-id="6476a-141">Open the **Task Runner Explorer** (**View > Other Windows > Task Runner Explorer**).</span></span> <span data-ttu-id="6476a-142">、タスク間では、新しいタスクがという名前を表示する必要があります`less`です。</span><span class="sxs-lookup"><span data-stu-id="6476a-142">Among the tasks, you should see a new task named `less`.</span></span> <span data-ttu-id="6476a-143">ウィンドウを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6476a-143">You might have to refresh the window.</span></span>

<span data-ttu-id="6476a-144">実行、`less`タスクは、ここに表示される内容のような出力を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6476a-144">Run the `less` task, and you see output similar to what is shown here:</span></span>

![以下のタスク ランナー](less-sass-fa/_static/less-task-runner.png)

<span data-ttu-id="6476a-146">*Wwwroot/css*フォルダーにはこれで、新しいファイルが含まれています*main.css*:</span><span class="sxs-lookup"><span data-stu-id="6476a-146">The *wwwroot/css* folder now contains a new file, *main.css*:</span></span>

![メインの css を作成](less-sass-fa/_static/main-css-created.png)

<span data-ttu-id="6476a-148">開いている*main.css*を参照してください、次のようなものとします。</span><span class="sxs-lookup"><span data-stu-id="6476a-148">Open *main.css* and you see something like the following:</span></span>

```css
body {
    background-color: #336666;
}
.baseColor {
    color: #663333;
}
.bgLight {
    color: #884a44;
}
.bgLight2 {
    color: #aa6355;
}
.bgDark {
    color: #442225;
}
.bgDark2 {
    color: #221114;
}
```

<span data-ttu-id="6476a-149">単純なHTMLページを追加、*wwwroot*フォルダー、および参照*main.css*アクションのカラーパレットを表示します。</span><span class="sxs-lookup"><span data-stu-id="6476a-149">Add a simple HTML page to the *wwwroot* folder, and reference *main.css* to see the color palette in action.</span></span>

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <link href="css/main.css" rel="stylesheet" />
    <title></title>
</head>
<body>
    <div>
        <div class="baseColor">BaseColor</div>
        <div class="bgLight">Light</div>
        <div class="bgLight2">Light2</div>
        <div class="bgDark">Dark</div>
        <div class="bgDark2">Dark2</div>
    </div>
</body>
</html>
```

<span data-ttu-id="6476a-150">ご覧180度ですぐにご利用いただけます`@base`生成に使用される`@background`の色の両カラーホイールが発生しました`@base`:</span><span class="sxs-lookup"><span data-stu-id="6476a-150">You can see that the 180 degree spin on `@base` used to produce `@background` resulted in the color wheel opposing color of `@base`:</span></span>

![小さいテストの例](less-sass-fa/_static/less-test-screenshot.png)

<span data-ttu-id="6476a-152">Lessも入れ子になったメディア クエリと入れ子になったルールは、サポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="6476a-152">Less also provides support for nested rules, as well as nested media queries.</span></span> <span data-ttu-id="6476a-153">たとえば、CSS規則の詳細なメニューが生じるような入れ子になった階層を定義するには、このような。</span><span class="sxs-lookup"><span data-stu-id="6476a-153">For example, defining nested hierarchies like menus can result in verbose CSS rules like these:</span></span>

```css
nav {
    height: 40px;
    width: 100%;
}
nav li {
    height: 38px;
    width: 100px;
}
nav li a:link {
    color: #000;
    text-decoration: none;
}
nav li a:visited {
    text-decoration: none;
    color: #CC3333;
}
nav li a:hover {
    text-decoration: underline;
    font-weight: bold;
}
nav li a:active {
    text-decoration: underline;
}
```

<span data-ttu-id="6476a-154">理想的にはすべての関連するスタイルルールは、一緒にCSSファイル内では実際に何も規則とおそらくブロックコメントを除く、この規則を適用します。</span><span class="sxs-lookup"><span data-stu-id="6476a-154">Ideally all of the related style rules will be placed together within the CSS file, but in practice there's nothing enforcing this rule except convention and perhaps block comments.</span></span>

<span data-ttu-id="6476a-155">以下を使用してこれらの同じ規則を定義すると、ようになります。</span><span class="sxs-lookup"><span data-stu-id="6476a-155">Defining these same rules using Less looks like this:</span></span>

```less
nav {
    height: 40px;
    width: 100%;
    li {
        height: 38px;
        width: 100px;
        a {
            color: #000;
            &:link { text-decoration:none}
            &:visited { color: #CC3333; text-decoration:none}
            &:hover { text-decoration:underline; font-weight:bold}
            &:active {text-decoration:underline}
        }
    }
}
```

<span data-ttu-id="6476a-156">この場合は、すべての下位の要素に注意して`nav`そのスコープ内に含まれます。</span><span class="sxs-lookup"><span data-stu-id="6476a-156">Note that in this case, all of the subordinate elements of `nav` are contained within its scope.</span></span> <span data-ttu-id="6476a-157">親要素の任意の繰り返しが不要になった(`nav`、 `li`、 `a`)、合計行の数が同様に下がっています(いくつかのことでは2番目の例では、同じ行に値を作成する場合の結果)。</span><span class="sxs-lookup"><span data-stu-id="6476a-157">There's no longer any repetition of parent elements (`nav`, `li`, `a`), and the total line count has dropped as well (though some of that's a result of putting values on the same lines in the second example).</span></span> <span data-ttu-id="6476a-158">非常に役に立ちます、組織的、ここではすべて、明示的に範囲指定されたスコープ内で指定したUI要素の規則の表示をoffに設定、ファイルの残りの部分から中かっこでします。</span><span class="sxs-lookup"><span data-stu-id="6476a-158">It can be very helpful, organizationally, to see all of the rules for a given UI element within an explicitly bounded scope, in this case set off from the rest of the file by curly braces.</span></span>

<span data-ttu-id="6476a-159">`&`構文では、以下のセレクターの機能(&)、現在のセレクターの親を表すです。</span><span class="sxs-lookup"><span data-stu-id="6476a-159">The `&` syntax is a Less selector feature, with & representing the current selector parent.</span></span> <span data-ttu-id="6476a-160">内で、{...}</span><span class="sxs-lookup"><span data-stu-id="6476a-160">So, within the a {...}</span></span> <span data-ttu-id="6476a-161">ブロック、`&`を表す、`a`タグ、つまり`&:link`と等価`a:link`。</span><span class="sxs-lookup"><span data-stu-id="6476a-161">block, `&` represents an `a` tag, and thus `&:link` is equivalent to `a:link`.</span></span>

<span data-ttu-id="6476a-162">繰り返しとCSSの複雑さには、メディアクエリ、応答性の高いデザインを作成するのには、非常に便利ですが大きく貢献もできます。</span><span class="sxs-lookup"><span data-stu-id="6476a-162">Media queries, extremely useful in creating responsive designs, can also contribute heavily to repetition and complexity in CSS.</span></span> <span data-ttu-id="6476a-163">小さいによりメディア クエリ クラス内で入れ子になるので、クラス定義全体を異なるの内で繰り返す必要はありません最上位`@media`要素。</span><span class="sxs-lookup"><span data-stu-id="6476a-163">Less allows media queries to be nested within classes, so that the entire class definition doesn't need to be repeated within different top-level `@media` elements.</span></span> <span data-ttu-id="6476a-164">たとえば、応答性の高いメニューのCSSを次に示します。</span><span class="sxs-lookup"><span data-stu-id="6476a-164">For example, here is CSS for a responsive menu:</span></span>

```css
.navigation {
    margin-top: 30%;
    width: 100%;
}
@media screen and (min-width: 40em) {
    .navigation {
        margin: 0;
    }
}
@media screen and (min-width: 62em) {
    .navigation {
        width: 960px;
        margin: 0;
    }
}
```

<span data-ttu-id="6476a-165">これは、としてでより定義することができます。</span><span class="sxs-lookup"><span data-stu-id="6476a-165">This can be better defined in Less as:</span></span>

```less
.navigation {
    margin-top: 30%;
    width: 100%;
    @media screen and (min-width: 40em) {
        margin: 0;
    }
    @media screen and (min-width: 62em) {
        width: 960px;
        margin: 0;
    }
}
```

<span data-ttu-id="6476a-166">既に見た未満のもう 1 つの機能は、事前定義済み変数から構築されているスタイル属性をできるように、数値演算のサポートです。</span><span class="sxs-lookup"><span data-stu-id="6476a-166">Another feature of Less that we have already seen is its support for mathematical operations, allowing style attributes to be constructed from pre-defined variables.</span></span> <span data-ttu-id="6476a-167">これにより、ベースの変数を変更してすべての依存値が自動的に変更するため、はるかに簡単で、関連するスタイルを更新しています。</span><span class="sxs-lookup"><span data-stu-id="6476a-167">This makes updating related styles much easier, since the base variable can be modified and all dependent values change automatically.</span></span>

<span data-ttu-id="6476a-168">特に大規模なサイト用の CSS ファイル (およびメディア クエリを使用している場合に特に) を得ることが非常に大きく時間の経過と共にそれらの操作を扱いにくくなるためです。</span><span class="sxs-lookup"><span data-stu-id="6476a-168">CSS files, especially for large sites (and especially if media queries are being used), tend to get quite large over time, making working with them unwieldy.</span></span> <span data-ttu-id="6476a-169">Less ファイルは、個別を使用して、プル`@import`ディレクティブ。</span><span class="sxs-lookup"><span data-stu-id="6476a-169">Less files can be defined separately, then pulled together using `@import` directives.</span></span> <span data-ttu-id="6476a-170">小さいこともできますファイルをインポートする各 CSS 同様に、必要な場合。</span><span class="sxs-lookup"><span data-stu-id="6476a-170">Less can also be used to import individual CSS files, as well, if desired.</span></span>

<span data-ttu-id="6476a-171">*Mixin*パラメーターを受け入れることができ、特定 mixin が有効になる場合を定義する宣言型の方法を提供する mixin 警備員の形式で条件付きロジックをサポートしている小さい。</span><span class="sxs-lookup"><span data-stu-id="6476a-171">*Mixins* can accept parameters, and Less supports conditional logic in the form of mixin guards, which provide a declarative way to define when certain mixins take effect.</span></span> <span data-ttu-id="6476a-172">Mixin 警備員の一般的な用途は、光に基づく色を調整する、または元の色の濃いは。</span><span class="sxs-lookup"><span data-stu-id="6476a-172">A common use for mixin guards is to adjust colors based on how light or dark the source color is.</span></span> <span data-ttu-id="6476a-173">色のパラメーターを受け入れる mixin を指定するには、その色に基づいて mixin を変更する mixin guard が使用できます。</span><span class="sxs-lookup"><span data-stu-id="6476a-173">Given a mixin that accepts a parameter for color, a mixin guard can be used to modify the mixin based on that color:</span></span>

```less
.box (@color) when (lightness(@color) >= 50%) {
    background-color: #000;
}
.box (@color) when (lightness(@color) < 50%) {
    background-color: #FFF;
}
.box (@color) {
    color: @color;
}

.feature {
    .box (@base);
}
```

<span data-ttu-id="6476a-174">指定された、現在`@base`の値`#663333`、次のCSSをこの少ないスクリプトが生成されます。</span><span class="sxs-lookup"><span data-stu-id="6476a-174">Given our current `@base` value of `#663333`, this Less script will produce the following CSS:</span></span>

```css
.feature {
    background-color: #FFF;
    color: #663333;
}
```

<span data-ttu-id="6476a-175">小さい数、追加機能を提供しますが、これが把握できるようにこれの電源の言語を前処理します。</span><span class="sxs-lookup"><span data-stu-id="6476a-175">Less provides a number of additional features, but this should give you some idea of the power of this preprocessing language.</span></span>

## <a name="sass"></a><span data-ttu-id="6476a-176">Sass</span><span class="sxs-lookup"><span data-stu-id="6476a-176">Sass</span></span>

<span data-ttu-id="6476a-177">Sass は、構文は若干異なりますが、同じ機能の多くのサポートを提供する、以下のと似ています。</span><span class="sxs-lookup"><span data-stu-id="6476a-177">Sass is similar to Less, providing support for many of the same features, but with slightly different syntax.</span></span> <span data-ttu-id="6476a-178">JavaScript ではなく、Ruby を使用して構築されて、別のセットアップ要件。</span><span class="sxs-lookup"><span data-stu-id="6476a-178">It's built using Ruby, rather than JavaScript, and so has different setup requirements.</span></span> <span data-ttu-id="6476a-179">Sass の元の言語では、中かっこやセミコロンを使用しませんでしたが、代わりに空白とインデントを使用してスコープを定義します。</span><span class="sxs-lookup"><span data-stu-id="6476a-179">The original Sass language didn't use curly braces or semicolons, but instead defined scope using white space and indentation.</span></span> <span data-ttu-id="6476a-180">Sass のバージョン 3 では、新しい構文が導入された、 **SCSS** ("Sassy CSS")。</span><span class="sxs-lookup"><span data-stu-id="6476a-180">In version 3 of Sass, a new syntax was introduced, **SCSS** ("Sassy CSS").</span></span> <span data-ttu-id="6476a-181">インデント レベルと、空白を無視し、セミコロンと中かっこを代わりに使用できるで、SCSS は CSS に似ています。</span><span class="sxs-lookup"><span data-stu-id="6476a-181">SCSS is similar to CSS in that it ignores indentation levels and whitespace, and instead uses semicolons and curly braces.</span></span>

<span data-ttu-id="6476a-182">通常Sassをインストールするには、まずRuby(プレインストール macos)をインストールして実行するとします。</span><span class="sxs-lookup"><span data-stu-id="6476a-182">To install Sass, typically you would first install Ruby (pre-installed on macOS), and then run:</span></span>

```console
gem install sass
```

<span data-ttu-id="6476a-183">ただし、Visual Studioを実行している場合開始できますSassとほぼ同じ方法未満の場合と同様です。</span><span class="sxs-lookup"><span data-stu-id="6476a-183">However, if you're running Visual Studio, you can get started with Sass in much the same way as you would with Less.</span></span> <span data-ttu-id="6476a-184">開いている*package.json* "gulp sass"パッケージを追加および`devDependencies`:</span><span class="sxs-lookup"><span data-stu-id="6476a-184">Open *package.json* and add the "gulp-sass" package to `devDependencies`:</span></span>

```json
"devDependencies": {
  "gulp": "3.9.1",
  "gulp-less": "3.3.0",
  "gulp-sass": "3.1.0"
}
```

<span data-ttu-id="6476a-185">次に、変更*gulpfile.js* sass変数とSassファイルをコンパイルし、結果wwwrootフォルダーに配置するタスクを追加します。</span><span class="sxs-lookup"><span data-stu-id="6476a-185">Next, modify *gulpfile.js* to add a sass variable and a task to compile your Sass files and place the results in the wwwroot folder:</span></span>

```javascript
var gulp = require("gulp"),
  fs = require("fs"),
  less = require("gulp-less"),
  sass = require("gulp-sass");

// other content removed

gulp.task("sass", function () {
  return gulp.src('Styles/main2.scss')
    .pipe(sass())
    .pipe(gulp.dest('wwwroot/css'));
});
```

<span data-ttu-id="6476a-186">Sassファイルを追加するようになりました*main2.scss*を*スタイル*プロジェクトのルートにフォルダー。</span><span class="sxs-lookup"><span data-stu-id="6476a-186">Now you can add the Sass file *main2.scss* to the *Styles* folder in the root of the project:</span></span>

![scss ファイルを追加します。](less-sass-fa/_static/add-scss-file.png)

<span data-ttu-id="6476a-188">開いている*main2.scss*以下を追加します。</span><span class="sxs-lookup"><span data-stu-id="6476a-188">Open *main2.scss* and add the following:</span></span>

```sass
$base: #CC0000;
body {
    background-color: $base;
}
```

<span data-ttu-id="6476a-189">すべてのファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="6476a-189">Save all of your files.</span></span> <span data-ttu-id="6476a-190">今すぐ更新**Task Runner Explorer**、表示、`sass`タスク。</span><span class="sxs-lookup"><span data-stu-id="6476a-190">Now when you refresh **Task Runner Explorer**, you see a `sass` task.</span></span> <span data-ttu-id="6476a-191">実行、およびファイルの場所、 *wwwroot/css*フォルダー。</span><span class="sxs-lookup"><span data-stu-id="6476a-191">Run it, and look in the */wwwroot/css* folder.</span></span> <span data-ttu-id="6476a-192">ここでは、 *main2.css*次の内容をファイル。</span><span class="sxs-lookup"><span data-stu-id="6476a-192">There's now a *main2.css* file, with these contents:</span></span>

```css
body {
    background-color: #CC0000;
}
```

<span data-ttu-id="6476a-193">Sass に似ているLessが、同様のメリットを提供することが、同じ入れ子がサポートしています。</span><span class="sxs-lookup"><span data-stu-id="6476a-193">Sass supports nesting in much the same was that Less does, providing similar benefits.</span></span> <span data-ttu-id="6476a-194">ファイルが関数によって分割されることができを使用して含まれている、`@import`ディレクティブ。</span><span class="sxs-lookup"><span data-stu-id="6476a-194">Files can be split up by function and included using the `@import` directive:</span></span>

```sass
@import 'anotherfile';
```

<span data-ttu-id="6476a-195">Sassサポートmixins同様を使用して、`@mixin`に定義するキーワードおよび`@include`からこの例のように、それらを含める[sass lang.com](http://sass-lang.com):</span><span class="sxs-lookup"><span data-stu-id="6476a-195">Sass supports mixins as well, using the `@mixin` keyword to define them and `@include` to include them, as in this example from [sass-lang.com](http://sass-lang.com):</span></span>

```sass
@mixin border-radius($radius) {
    -webkit-border-radius: $radius;
    -moz-border-radius: $radius;
    -ms-border-radius: $radius;
    border-radius: $radius;
}

.box { @include border-radius(10px); }
```

<span data-ttu-id="6476a-196">Mixins、に加えてSassもサポートしています、継承の概念を拡張する別の1つのクラスを許可します。</span><span class="sxs-lookup"><span data-stu-id="6476a-196">In addition to mixins, Sass also supports the concept of inheritance, allowing one class to extend another.</span></span> <span data-ttu-id="6476a-197">概念的には、混合が少ないCSSコードに似ています。</span><span class="sxs-lookup"><span data-stu-id="6476a-197">It's conceptually similar to a mixin, but results in less CSS code.</span></span> <span data-ttu-id="6476a-198">使用する方法は、その、`@extend`キーワード。</span><span class="sxs-lookup"><span data-stu-id="6476a-198">It's accomplished using the `@extend` keyword.</span></span> <span data-ttu-id="6476a-199">Mixinsを試すには、次を追加、*main2.scss*ファイル。</span><span class="sxs-lookup"><span data-stu-id="6476a-199">To try out mixins, add the following to your *main2.scss* file:</span></span>

```sass
@mixin alert {
    border: 1px solid black;
    padding: 5px;
    color: #333333;
}

.success {
    @include alert;
    border-color: green;
}

.error {
    @include alert;
    color: red;
    border-color: red;
    font-weight:bold;
}
```

<span data-ttu-id="6476a-200">出力を確認*main2.css*実行した後、`sass`でタスク**Task Runner Explorer**:</span><span class="sxs-lookup"><span data-stu-id="6476a-200">Examine the output in *main2.css* after running the `sass` task in **Task Runner Explorer**:</span></span>

```css
.success {
    border: 1px solid black;
    padding: 5px;
    color: #333333;
    border-color: green;
 }

.error {
    border: 1px solid black;
    padding: 5px;
    color: #333333;
    color: red;
    border-color: red;
    font-weight: bold;
}
```

<span data-ttu-id="6476a-201">すべてのアラートmixinの一般的なプロパティが各クラスで繰り返されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="6476a-201">Notice that all of the common properties of the alert mixin are repeated in each class.</span></span> <span data-ttu-id="6476a-202">Mixinでした、適切なジョブが、開発時に重複を排除することができ、多数の重複していること、必要なCSSファイルで、潜在的なパフォーマンスの問題よりも大きくなります結果として得られるでCSSをまだ作成しています。</span><span class="sxs-lookup"><span data-stu-id="6476a-202">The mixin did a good job of helping eliminate duplication at development time, but it's still creating CSS with a lot of duplication in it, resulting in larger than necessary CSS files - a potential performance issue.</span></span>

<span data-ttu-id="6476a-203">アラートmixinに置き換える、`.alert`クラス、および変更`@include`に`@extend`(拡張を記述する`.alert`ではなく、 `alert`)。</span><span class="sxs-lookup"><span data-stu-id="6476a-203">Now replace the alert mixin with a `.alert` class, and change `@include` to `@extend` (remembering to extend `.alert`, not `alert`):</span></span>

```sass
.alert {
    border: 1px solid black;
    padding: 5px;
    color: #333333;
}

.success {
    @extend .alert;
    border-color: green;
}

.error {
    @extend .alert;
    color: red;
    border-color: red;
    font-weight:bold;
}
```

<span data-ttu-id="6476a-204">Sassをもう一度実行し、結果として得られるCSSを確認します。</span><span class="sxs-lookup"><span data-stu-id="6476a-204">Run Sass once more, and examine the resulting CSS:</span></span>

```css
.alert, .success, .error {
    border: 1px solid black;
    padding: 5px;
    color: #333333;
}

.success {
    border-color: green;
}

.error {
    color: red;
    border-color: red;
    font-weight: bold;
}
```

<span data-ttu-id="6476a-205">プロパティが、必要に応じて何度もとしてのみ定義されているようになりましたし、CSSが生成される向上します。</span><span class="sxs-lookup"><span data-stu-id="6476a-205">Now the properties are defined only as many times as needed, and better CSS is generated.</span></span>

<span data-ttu-id="6476a-206">Sassには、関数と、以下のような条件付きロジック操作も含まれています。</span><span class="sxs-lookup"><span data-stu-id="6476a-206">Sass also includes functions and conditional logic operations, similar to Less.</span></span> <span data-ttu-id="6476a-207">実際には、2つの言語の機能は、非常に似ています。</span><span class="sxs-lookup"><span data-stu-id="6476a-207">In fact, the two languages' capabilities are very similar.</span></span>

## <a name="less-or-sass"></a><span data-ttu-id="6476a-208">LessかSass?</span><span class="sxs-lookup"><span data-stu-id="6476a-208">Less or Sass?</span></span>

<span data-ttu-id="6476a-209">まだやSassの使用が少なく、通常方が優れているかどうかについての合意がない(または元のSassまたはSass内で新しいSCSS構文を使用するかどうかも)。</span><span class="sxs-lookup"><span data-stu-id="6476a-209">There's still no consensus as to whether it's generally better to use Less or Sass (or even whether to prefer the original Sass or the newer SCSS syntax within Sass).</span></span> <span data-ttu-id="6476a-210">最も重要な意思決定が、おそらく**これらのツールのいずれかを使用して**だけ手動コーディングのCSSファイルではなく、します。</span><span class="sxs-lookup"><span data-stu-id="6476a-210">Probably the most important decision is to **use one of these tools**, as opposed to just hand-coding your CSS files.</span></span> <span data-ttu-id="6476a-211">作成した後も、両方の以下の意思決定し、Sassは適切な選択肢です。</span><span class="sxs-lookup"><span data-stu-id="6476a-211">Once you've made that decision, both Less and Sass are good choices.</span></span>

## <a name="font-awesome"></a><span data-ttu-id="6476a-212">Font Awsom</span><span class="sxs-lookup"><span data-stu-id="6476a-212">Font Awesome</span></span>

<span data-ttu-id="6476a-213">CSS プリプロセッサに加えて最新のwebアプリケーションのスタイル処理の重要なリソースは、フォントすばらしいです。</span><span class="sxs-lookup"><span data-stu-id="6476a-213">In addition to CSS preprocessors, another great resource for styling modern web applications is Font Awesome.</span></span> <span data-ttu-id="6476a-214">フォントAwesomeは、webアプリケーションで自由に使用できる 500を超えるスケーラブルベクターアイコンを提供するツールキットです。</span><span class="sxs-lookup"><span data-stu-id="6476a-214">Font Awesome is a toolkit that provides over 500 scalable vector icons that can be freely used in your web applications.</span></span> <span data-ttu-id="6476a-215">ブートストラップを使用すること、もともとられていますが依存関係またはそのフレームワークにJavaScriptライブラリにします。</span><span class="sxs-lookup"><span data-stu-id="6476a-215">It was originally designed to work with Bootstrap, but it has no dependency on that framework or on any JavaScript libraries.</span></span>

<span data-ttu-id="6476a-216">フォントサービスを開始する最も簡単な方法では、パブリックのコンテンツ配信ネットワーク(CDN)の場所を使用してへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="6476a-216">The easiest way to get started with Font Awesome is to add a reference to it, using its public content delivery network (CDN) location:</span></span>

```html
<link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/font-awesome/4.3.0/css/font-awesome.min.css">
```

<span data-ttu-id="6476a-217">追加することも、Visual Studioプロジェクトに「依存関係」に追加することによってで*bower.json*:</span><span class="sxs-lookup"><span data-stu-id="6476a-217">You can also add it to your Visual Studio project by adding it to the "dependencies" in *bower.json*:</span></span>

```json
{
  "name": "ASP.NET",
  "private": true,
  "dependencies": {
    "bootstrap": "3.0.0",
    "jquery": "1.10.2",
    "jquery-validation": "1.11.1",
    "jquery-validation-unobtrusive": "3.2.2",
    "hammer.js": "2.0.4",
    "bootstrap-touch-carousel": "0.8.0",
    "Font-Awesome": "4.3.0"
  }
}
```

<span data-ttu-id="6476a-218">Font Awesomeの参照をページにある場合は後に、追加できますアイコン アプリケーション Font Awesomeクラス、通常の付いた「fa-」で、インライン HTML 要素に適用することで (など`<span>`または`<i>`)。</span><span class="sxs-lookup"><span data-stu-id="6476a-218">Once you have a reference to Font Awesome on a page, you can add icons to your application by applying Font Awesome classes, typically prefixed with "fa-", to your inline HTML elements (such as `<span>` or `<i>`).</span></span>  <span data-ttu-id="6476a-219">たとえば、単純なリストをこのようなコードを使用してメニュー アイコンを追加できます。</span><span class="sxs-lookup"><span data-stu-id="6476a-219">For example, you can add icons to simple lists and menus using code like this:</span></span>

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title></title>
    <link href="lib/font-awesome/css/font-awesome.css" rel="stylesheet" />
</head>
<body>
    <ul class="fa-ul">
        <li><i class="fa fa-li fa-home"></i> Home</li>
        <li><i class="fa fa-li fa-cog"></i> Settings</li>
    </ul>
</body>
</html>
```

<span data-ttu-id="6476a-220">ブラウザーでは、次が生成されます - 各項目の横にあるアイコンに注意してください。</span><span class="sxs-lookup"><span data-stu-id="6476a-220">This produces the following in the browser - note the icon beside each item:</span></span>

![リストのアイコン](less-sass-fa/_static/list-icons-screenshot.png)

<span data-ttu-id="6476a-222">ここで、可能なアイコンの完全な一覧を表示することができます。</span><span class="sxs-lookup"><span data-stu-id="6476a-222">You can view a complete list of the available icons here:</span></span>

http://fontawesome.io/icons/

## <a name="summary"></a><span data-ttu-id="6476a-223">まとめ</span><span class="sxs-lookup"><span data-stu-id="6476a-223">Summary</span></span>

<span data-ttu-id="6476a-224">最新のwebアプリケーションは、ますますは、クリーン、直感的で、およびさまざまなデバイスから簡単に使用される応答性の高い、滑らかな設計を要求します。</span><span class="sxs-lookup"><span data-stu-id="6476a-224">Modern web applications increasingly demand responsive, fluid designs that are clean, intuitive, and easy to use from a variety of devices.</span></span> <span data-ttu-id="6476a-225">これらの目標を達成するために必要なCSSスタイルシートの複雑さを管理すると、小さいプリプロセッサlikeを使用してまたはSass最適な方法はします。</span><span class="sxs-lookup"><span data-stu-id="6476a-225">Managing the complexity of the CSS stylesheets required to achieve these goals is best done using a preprocessor like Less or Sass.</span></span> <span data-ttu-id="6476a-226">さらに、ツールキットフォント素晴らしいと同様には、テキストのナビゲーションメニューによく知られているアイコンを迅速に提供し、ボタン、全体的なユーザーの向上が、アプリケーションのエクスペリエンスします。</span><span class="sxs-lookup"><span data-stu-id="6476a-226">In addition, toolkits like Font Awesome quickly provide well-known icons to textual navigation menus and buttons, improving the overall user experience of your application.</span></span>
