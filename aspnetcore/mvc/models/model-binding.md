---
title: ASP.NET Core でのモデル バインド
author: tdykstra
description: ASP.NET Core MVC のモデル バインドでは、HTTP 要求からアクション メソッドのパラメーターにデータをマップする方法について説明します。
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: tdykstra
ms.date: 11/13/2018
uid: mvc/models/model-binding
ms.openlocfilehash: 1dc9b41328ed78440622acc1865b6f088d394403
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57037049"
---
# <a name="model-binding-in-aspnet-core"></a>ASP.NET Core でのモデル バインド

作成者: [Rachel Appel](https://github.com/rachelappel)

## <a name="introduction-to-model-binding"></a>モデル バインドの概要

ASP.NET Core MVC のモデル バインドでは、HTTP 要求からアクション メソッドのパラメーターにデータをマップします。 パラメーターは、文字列、整数、浮動小数点などの単純型である場合や、複合型である場合があります。 これは MVC の優れた機能です。データのサイズや複雑さに関係なく、多くの場合、受信データは対応するものに繰り返しマップされるためです。 MVC では、開発者が各アプリで同じコードの若干異なるバージョンを再書き込みしなくてすむように、バインドを抽象化することでこの問題を解決します。 独自のテキストを書き込んでコンバーター コードを入力するのは面倒でエラーが発生しやすくなります。

## <a name="how-model-binding-works"></a>モデル バインドのしくみ

MVC は HTTP 要求を受信すると、それをコントローラーの特定のアクション メソッドにルーティングします。 ルート データの内容に基づいて実行するアクション メソッドを決定し、次に HTTP 要求からそのアクション メソッドのパラメーターに値をバインドします。 たとえば、次のような URL があるとします。

`http://contoso.com/movies/edit/2`

ルート テンプレートは `{controller=Home}/{action=Index}/{id?}` のようになるため、`movies/edit/2` は `Movies` コントローラーと、その `Edit` アクション メソッドにルーティングします。 また、`id` という省略可能なパラメーターも受け入れます。 アクション メソッドのコードは次のようになります。

```csharp
public IActionResult Edit(int? id)
   ```

メモ:URL ルート内の文字列は大文字小文字が区別されません。

MVC は、名前を使用して要求データをアクション パラメーターにバインドしようとします。 MVC は、パラメーター名とパブリックに設定可能なプロパティの名前を使用して、各パラメーターの値を探します。 上記の例では、アクション パラメーターのみに `id` という名前が付いています。したがって、MVC はルート値の同じ名前を使用して値にバインドします。 ルート値に加え、MVC は要求のさまざまな部分のデータをバインドします。これはセット順に行われます。 以下に、モデル バインドでの検索順にデータ ソースをリストします。

1. `Form values`:これらは、POST メソッドを使用して HTTP 要求に追加するフォームの値です。 (jQuery POST 要求を含む)。

2. `Route values`:によって提供されるルート値のセット[ルーティング](xref:fundamentals/routing)

3. `Query strings`:URI のクエリ文字列の一部です。

<!-- DocFX BUG
The link works but generates an error when building with DocFX
@fundamentals/routing
[Routing](xref:fundamentals/routing)
-->

メモ:フォームの値、ルート データ、およびクエリ文字列はすべて名前と値のペアとして格納します。

モデル バインドでは `id` という名前のキーが求められましたが、フォーム値に `id` という名前のものがないため、ルート値に移動してそのキーを探しました。 この例では、一致するものがあります。 バインドが行われ、値は整数の 2 に変換されます。 Edit(string id) を使用する同じ要求では、文字列 "2" に変換されます。

ここまでは、例で単純型を使用しています。 MVC では、単純型は任意の .NET プリミティブ型、または文字列型コンバーターを使用する型となります。 アクション メソッドのパラメーターが、プロパティとして単純型と複合型の両方を含む、`Movie` 型などのクラスだった場合でも、MVC のモデル バインドでは適切に処理されます。 リフレクションと再帰を使用して、複合型のプロパティをトラバースして一致するものを探します。 モデル バインドでは *parameter_name.property_name* というパターンを探して、値をプロパティにバインドします。 このフォームの一致する値が見つからない場合は、プロパティ名のみを使用してバインドを試みます。 `Collection` 型などの型の場合、モデル バインドでは *parameter_name[index]* または *[index]* と一致するものを探します。 モデル バインドでは、キーが単純型である限り、同様に `Dictionary` 型を処理し、*parameter_name[key]* または *[key]* のみを求めます。 サポートされているキーは、同じモデル型に対して HTML ヘルパーとタグ ヘルパーで生成されたフィールド名と一致します。 これにより、値のラウンド トリップが有効になり、フォーム フィールドは、利便性のためにユーザーの入力が設定されたままとなります (作成または編集してバインドされたデータが検証に合格しなかった場合など)。

モデル バインドを可能にするには、クラスにバインドする既定のパブリック コンストラクターと、書き込み可能なパブリック プロパティが必要です。 モデル バインドが行われると、クラスは既定のパブリック コンストラクターを使用してインスタンス化され、その後、プロパティを設定できます。

パラメーターがバインドされると、モデル バインドではその名前を使用する値の検索を停止し、次のパラメーターのバインドに移ります。 それ以外の場合、既定のモデル バインドの動作では、パラメーターがその型に応じて既定値に設定されます。

* `T[]`:型の配列を除く`byte[]`、バインドの型のパラメーターを設定する`T[]`に`Array.Empty<T>()`します。 `byte[]` 型の配列は `null` に設定されます。

* 参照型:バインドは、プロパティを設定せず、既定のコンス トラクターとクラスのインスタンスを作成します。 ただし、このモデル バインドでは `string` パラメーターが `null` に設定されます。

* Null 許容型:Null 許容型に設定されて`null`します。 上記の例では、`int?` 型であるため、モデル バインドで `id` が `null` に設定されます。

* 値の型。型の null 非許容の値の型`T`に設定されている`default(T)`します。 たとえば、モデル バインドではパラメーター `int id` が 0 に設定されます。 既定値に依存するのではなく、モデルの検証または null 許容型を使用することを検討してください。

バインドが失敗した場合、MVC はエラーをスローしません。 ユーザー入力を受け入れるすべてのアクションで `ModelState.IsValid` プロパティを確認する必要があります。

メモ:コント ローラー内の各エントリ`ModelState`プロパティは、`ModelStateEntry`を含む、`Errors`プロパティ。 ユーザーが自分でこのコレクションに対してクエリを実行する必要はほとんどありません。 代わりに、`ModelState.IsValid` を使用してください。

また、モデル バインドを実行する際に、MVC で考慮する必要がある特別なデータ型がいくつかあります。

* `IFormFile`, `IEnumerable<IFormFile>`:HTTP 要求の一部である 1 つまたは複数のアップロードされたファイル。

* `CancellationToken`:非同期コント ローラーでアクティビティをキャンセルするために使用します。

これらの型をクラス型のプロパティまたはアクション パラメーターにバインドすることができます。

モデル バインドが完了したら、[検証](validation.md)が行われます。 既定のモデル バインドは、大部分の開発シナリオで適切に動作します。 また、拡張可能であるため、必要に応じて、組み込み動作をカスタマイズすることもできます。

## <a name="customize-model-binding-behavior-with-attributes"></a>属性を使用してモデル バインドの動作をカスタマイズする

MVC にはいくつかの属性が含まれています。これらを使用して、その既定のモデル バインドの動作を別のソースに指示することができます。 たとえば、プロパティでバインドが必要かどうかや、`[BindRequired]` または `[BindNever]` 属性を使用してまったくバインドしないようにするかを指定できます。 あるいは、既定のデータ ソースをオーバーライドし、モデル バインダーのデータ ソースを指定することもできます。 モデル バインド属性のリストを以下に示します。

* `[BindRequired]`:この属性は、バインドできない場合に、モデル状態エラーを追加します。

* `[BindNever]`:このパラメーターにバインドしないにモデル バインダーに指示します。

* `[FromHeader]`, `[FromQuery]`, `[FromRoute]`, `[FromForm]`:適用する正確なバインディング ソースを指定するのにには、これらを使用します。

* `[FromServices]`:この属性を使用して[依存関係の注入](../../fundamentals/dependency-injection.md)サービスからパラメーターをバインドします。

* `[FromBody]`:要求本文からデータをバインドするのにには、構成済みのフォーマッタを使用します。 フォーマッタは、要求のコンテンツの種類に基づいて選択されます。

* `[ModelBinder]`:既定のモデル バインダー、バインディング ソースと名前をオーバーライドするために使用します。

属性は、モデル バインドの既定の動作をオーバーライドする必要がある場合にとても便利なツールです。

## <a name="customize-model-binding-and-validation-globally"></a>モデル バインドと検証をグローバルにカスタマイズする

モデル バインドおよび検証システムの動作は、次の内容を記述した [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) によって駆動されます。

* モデルをバインドする方法。
* 型とそのプロパティに対して行われる検証の方法。

システムの動作の特性をグローバルに構成するには、[MvcOptions.ModelMetadataDetailsProviders](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions.modelmetadatadetailsproviders#Microsoft_AspNetCore_Mvc_MvcOptions_ModelMetadataDetailsProviders) に詳細プロバイダーを追加します。 MVC には組み込みの詳細プロバイダーがいくつか用意されています。これらにより、特定の型に対してモデル バインドまたは検証を無効にするなどの動作を構成することができます。

特定の型のすべてのモデルに対してモデル バインドを無効にするには、`Startup.ConfigureServices` 内に [ExcludeBindingMetadataProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.excludebindingmetadataprovider) を追加します。 たとえば、`System.Version` 型のすべてのモデルに対してモデル バインドを無効にするには、次のようにします。

```csharp
services.AddMvc().AddMvcOptions(options =>
    options.ModelMetadataDetailsProviders.Add(
        new ExcludeBindingMetadataProvider(typeof(System.Version))));
```

特定の型のプロパティに対して検証を無効にするには、`Startup.ConfigureServices` 内に [SuppressChildValidationMetadataProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.suppresschildvalidationmetadataprovider) を追加します。 たとえば、`System.Guid` 型のプロパティに対して検証を無効にするには、次のようにします。

```csharp
services.AddMvc().AddMvcOptions(options =>
    options.ModelMetadataDetailsProviders.Add(
        new SuppressChildValidationMetadataProvider(typeof(System.Guid))));
```

## <a name="bind-formatted-data-from-the-request-body"></a>書式付きデータを要求本文からバインドする

要求データは、JSON、XML およびその他の多くの書式を含む、さまざまな書式で指定できます。 [FromBody] 属性を使用して、要求本文のデータにパラメーターをバインドするよう指定すると、MVC では構成済みのフォーマッタ セットを使用して、そのコンテンツの種類に基づいて要求データを処理します。 既定では、MVC には JSON データを処理するための `JsonInputFormatter` クラスが含まれますが、XML やその他のカスタム書式を処理するためにフォーマッタを追加することができます。

> [!NOTE]
> `[FromBody]` で修飾されたアクションごとに 1 つのパラメーターのみを指定できます。 ASP.NET Core MVC ランタイムは、フォーマッタへの要求ストリームを読み取る責任を委任します。 パラメーターで要求ストリームが読み取られると、通常は他の `[FromBody]` パラメーターをバインドするために要求ストリームを再度読み取ることはできません。

> [!NOTE]
> `JsonInputFormatter` は既定のフォーマッタであり、[Json.NET](https://www.newtonsoft.com/json) に基づいています。

ASP.NET Core では、[Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html) ヘッダーとパラメーターの型に基づいて入力フォーマッタが選択されます。ただし、他に特に適用された属性がある場合を除きます。 XML または別の形式を使用する場合、*Startup.cs* ファイルでそれを構成する必要がありますが、最初に NuGet を使用して `Microsoft.AspNetCore.Mvc.Formatters.Xml` への参照を取得する必要がある場合があります。 スタートアップ コードは次のようになります。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddXmlSerializerFormatters();
   }
```

*Startup.cs* ファイル内のコードには、`services` 引数を持つ `ConfigureServices` メソッドが含まれます。これを使用することで、ASP.NET Core アプリ用のサービスを構築することができます。 サンプルでは、MVC がこのアプリに提供するサービスとして XML フォーマッタを追加します。 `AddMvc` メソッドに渡された `options` 引数を使用することで、アプリの起動時に MVC からフィルター、フォーマッタ、およびその他のシステム オプションを追加して管理することができます。 その後、`Consumes` 属性をコントローラー クラスまたはアクション メソッドに適用し、必要な書式を操作します。

### <a name="custom-model-binding"></a>カスタム モデル バインド

独自のカスタム モデル バインダーを書き込んで、モデル バインドを拡張することができます。 詳細については、「[custom model binding](../advanced/custom-model-binding.md)」 (カスタム モデル バインド) を参照してください。
