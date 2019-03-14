---
title: ASP.NET Core の区分
author: rick-anderson
description: 区分は ASP.NET MVC の機能であり、関連する機能を別の名前空間 (ルーティングの場合) およびフォルダー構造 (ビューの場合) としてグループにまとめるために使用する方法を説明します。
ms.author: riande
ms.date: 02/14/2019
uid: mvc/controllers/areas
ms.openlocfilehash: c21eed04ea68512515da262b6b6895dc1a821039
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57061709"
---
# <a name="areas-in-aspnet-core"></a>ASP.NET Core の区分

作成者: [Dhananjay Kumar](https://twitter.com/debug_mode) および [Rick Anderson](https://twitter.com/RickAndMSFT)

区分は ASP.NET の機能であり、関連する機能を別の名前空間 (ルーティングの場合) およびフォルダー構造 (ビューの場合) としてグループにまとめるために使用されます。 ルーティングを行うために、区分を使用して、別のルート パラメーター `area` を `controller` と `action` または Razor Page `page` に追加して階層を作成します。

区分は、ASP.NET Core Web アプリをより小さな機能グループにパーティション分割する方法であり、分割した各グループにそれぞれの Razor Pages、コントローラー、ビュー、モデルが与えられます。 区分は、実質的にはアプリ内の構造体となります。 ASP.NET Core Web プロジェクトでは、ページ、モデル、コントローラー、ビューなどの論理コンポーネントが別々のフォルダーに保存されます。 ASP.NET Core ランタイムでは、名前付け規則を使用し、これらのコンポーネント間のリレーションシップを作成します。 大きなアプリでは、アプリを機能の個別の高レベル区分に分割すると便利な場合があります。 チェックアウト、請求、検索などの複数のビジネス ユニットがある eコマース アプリの場合です。 これらのユニットにはそれぞれ、ビュー、コントローラー、Razor Pages、モデルを含める独自の論理区分があります。

次のような場合は、プロジェクトで区分を使用することを検討してください。

* 論理的に大まかに区切れる複数の機能コンポーネントでアプリが構成されている。
* 各機能区分を個別に使用できるようにアプリを分割したい。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。 ダウンロード サンプルからは、区分をテストするための基本的なアプリが与えられます。

## <a name="areas-for-controllers-with-views"></a>ビューを伴うコントローラーの区分

区分、コントローラー、ビューを使用する一般的な ASP.NET Core Web アプリに含まれる内容:

* [区分フォルダーの構造](#area-folder-structure)。
* コントローラーと区分を関連付ける目的で [&lbrack;Area&rbrack;](#attribute) 属性で装飾されたコントローラー: [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]
* [スタートアップに追加された区分ルート](#add-area-route): [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

## <a name="area-folder-structure"></a>区分フォルダーの構造
あるアプリに *Products* と *Services* という 2 つの論理グループが与えられているとします。 区分を利用すると、フォルダーの構造は次のようになります。

* Project name
  * Areas
    * Products
      * Controllers
        * HomeController.cs
        * ManageController.cs
      * Views
        * Home
          * Index.cshtml
        * Manage
          * Index.cshtml
          * About.cshtml
    * Services
      * Controllers
        * HomeController.cs
      * Views
        * Home
          * Index.cshtml

区分を使用するとき、前述のレイアウトが一般的ですが、このフォルダー構造を使用するにはビュー ファイルのみが求められます。 ビューの検出では、一致する区分ビュー ファイルを次の順序で検索します。

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
   ```

*Controllers* や *Models* など、ビュー以外のフォルダーの場所は問題では**ありません**。 たとえば、*Controllers* や *Models* のフォルダーは不要です。 *Controllers* と *Models* の内容はコードであり、.dll にコンパイルされます。 *Views* の内容は、ビューに対する要求が行われるまでコンパイルされません。

<!-- TODO review:
The content of the *Views* isn't compiled until a request to that view has been made.

What about precompiled views? 
 -->
<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a>コントローラーを区分に関連付ける

区分コントローラーは [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) 属性で指名されます。

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a>区分ルートを追加する

区分ルートでは通常、属性ルーティングではなく、従来のルーティングが使用されます。 規則ルーティングは順序に依存します。 一般に、区分のあるルートは区分を持たないルートより具体的なので、区分のあるルートはルート テーブルの前の方に配置する必要があります。

URL スペースがすべての区分で統一されている場合、ルート テンプレートでトークンとして `{area:...}` を使用できます。

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

上記のコードでは、ルートは 1 つの区分に一致しなければならないという制約が `exists` によって適用されます。 `{area:...}` の使用は、ルーティングを区分に追加するメカニズムとして最も単純です。

次のコードでは、<xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> を使用し、名前の付いた区分ルートが 2 つ作成されます。

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

ASP.NET Core 2.2 で `MapAreaRoute` を使用するときは、[この GitHub 問題](https://github.com/aspnet/AspNetCore/issues/7772)を確認してください。

詳細については、[区分のルーティング](xref:mvc/controllers/routing#areas)に関するページを参照してください。

### <a name="link-generation-with-areas"></a>区分を含むリンクの生成

[サンプル ダウンロード](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)に含まれる次のコードでは、区分が指定された上でリンクが生成されます。

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

上記のコードで生成されたリンクは、アプリ内のあらゆる場所で有効となります。

サンプル ダウンロードには、[部分ビュー](xref:mvc/views/partial)が含まれます。部分ビューには、上記のリンクと区分が指定されていない同じリンクが含まれます。 部分ビューは[レイアウト ファイル]()で参照されます。そのため、生成されたリンクがアプリのすべてのページに表示されます。 区分が指定されずに生成されたリンクは、同じ区分やコントローラーのページから参照されるときにのみ有効です。

区分またはコントローラーが指定されていないとき、ルーティングは*アンビエント*値に依存します。 現在の要求の現在のルート値は、リンク生成の場合、アンビエント値として見なされます。 サンプル アプリでは多くの場合、アンビエント値を使用すると、間違ったリンクが生成されます。

詳細については、「[コントローラー アクションへのルーティング](xref:mvc/controllers/routing)」を参照してください。

### <a name="shared-layout-for-areas-using-the-viewstartcshtml-file"></a>_ViewStart.cshtml ファイルを使用した区分の共有レイアウト

アプリ全体で共通レイアウトを共有するには、アプリケーションのルート フォルダーに *_ViewStart.cshtml* を移動します。

<!-- This section will be completed after https://github.com/aspnet/Docs/pull/10978 is merged.
<a name="arp"></a>

## Areas for Razor Pages
-->
<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a>ビューが保存されている既定の区分フォルダーを変更する

次のコードでは、既定の区分フォルダーが `"Areas"` から `"MyAreas"` に変更されます。

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<!-- TODO review - can we delete this. Areas doesn't change publishing - right? -->
### <a name="publishing-areas"></a>区分の発行

`<Project Sdk="Microsoft.NET.Sdk.Web">` が *.csproj* ファイルに含まれている場合、すべての `*.cshtml` および `wwwroot/**` ファイルが発行され、出力されます。
