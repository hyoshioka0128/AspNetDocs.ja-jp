---
title: Web API 規約を使用する
author: pranavkm
description: ASP.NET Core の Web API 規約について説明します。
monikerRange: '>= aspnetcore-2.2'
ms.author: scaddie
ms.custom: mvc
ms.date: 12/13/2018
uid: web-api/advanced/conventions
ms.openlocfilehash: 5ae96b213a19464045e1d0b1a76f8eb81089dc5b
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57029939"
---
# <a name="use-web-api-conventions"></a>Web API 規約を使用する

作成者: [Pranav Krishnamoorthy](https://github.com/pranavkm)、[Scott Addie](https://github.com/scottaddie)

ASP.NET Core 2.2 以降では、一般的な [API ドキュメント](xref:tutorials/web-api-help-pages-using-swagger)を抽出し、これを複数のアクション、コントローラー、またはアセンブリ内のすべてのコントローラーに適用する方法が含まれています。 Web API 規約は、[[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) による個々のアクションの装飾の代わりになるものです。

規約を使用すると、次のことを実行できます。

* 特定の型のアクションから返される最も一般的な戻り値の型と状態コードを定義する
* 定義済みの標準から外れるアクションを識別する

ASP.NET Core MVC 2.2 以降には、一連の既定の規約 `Microsoft.AspNetCore.Mvc.DefaultApiConventions` が含まれています。 この規約は、ASP.NET Core **API** プロジェクト テンプレートで提供されるコントローラー (*ValuesController.cs*) に基づいています。 アクションがテンプレートのパターンに従う場合は、既定の規約を使用すると成功します。 既定の規約がニーズに合わない場合は、「[Web API 規約を作成する](#create-web-api-conventions)」を参照してください。

実行時に、<xref:Microsoft.AspNetCore.Mvc.ApiExplorer> は規約を理解します。 `ApiExplorer` は [OpenAPI](https://www.openapis.org/) (Swagger とも呼ばれている) ドキュメントのジェネレーターと通信するために MVC を抽象化したものです。 適用された規約の属性はアクションと関連付けられており、アクションの OpenAPI ドキュメントに含まれます。 [API アナライザー](xref:web-api/advanced/analyzers)でも、規約を理解します。 従来とは異なるアクションである場合 (適用されている規約で文書化されていない状態コードを返す場合など)、警告で状態コードの文書化が促されます。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="apply-web-api-conventions"></a>Web API 規約を適用する

規約では作成は行われません。各アクションを 1 つの規約だけに関連付けることができます。 より具体的な規約の方が一般的な規約より優先されます。 優先順位が同じである 2 つ以上の規約が 1 つのアクションに適用されていると、選択は不明確になります。 次のオプションは、規約をアクションに適用するためにあります。より具体的なものから並んでいます。

1. `Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; 個々のアクションに適用され、適用される規約の種類と規約のメソッドが指定されます。

    次の例では、既定の規約の種類の `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` 規約のメソッドが、`Update` アクションに適用されます。

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionMethod&highlight=3)]

    `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` 規約のメソッドでは、次の属性がアクションに適用されます。

    ```csharp
    [ProducesDefaultResponseType]
    [ProducesResponseType(204)]
    [ProducesResponseType(404)]
    [ProducesResponseType(400)]
    ```

`[ProducesDefaultResponseType]` の詳細については、[既定の応答](https://swagger.io/docs/specification/describing-responses/#default)に関する記事をご覧ください。

1. `Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` (コントローラーに適用) &mdash; 指定した規約の種類がコントローラー上のすべてのアクションに適用されます。 規約のメソッドは、規約のメソッドが適用されるアクションを決定するヒントで装飾されています。 ヒントの詳細については、「[Web API 規約を作成する](#create-web-api-conventions)」を参照してください。

    次の例では、一連の既定の規約が、*ContactsConventionController* のすべてのアクションに適用されます。

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionTypeAttribute&highlight=2)]

1. `Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` (アセンブリに適用) &mdash; 指定された規約の種類が、現在のアセンブリのすべてのコントローラーに適用されます。 *Startup.cs* ファイル内でアセンブリ レベルの属性を適用することをお勧めします。

    次の例では、一連の既定の規約が、アセンブリのすべてのコントローラーに適用されます。

    [!code-csharp[](conventions/sample/Startup.cs?name=snippet_ApiConventionTypeAttribute&highlight=1)]

## <a name="create-web-api-conventions"></a>Web API 規約を作成する

既定の API 規約がニーズに合わない場合は、独自の規約を作成します。 規約は次のようなものです。

* メソッドを含む静的な種類
* アクションで[応答の種類](#response-types)と[名前付けの要件](#naming-requirements)を定義できる

### <a name="response-types"></a>応答の種類

これらのメソッドには `[ProducesResponseType]` または `[ProducesDefaultResponseType]` 属性の注釈が付けられています。 例:

```csharp
public static class MyAppConventions
{
    [ProducesResponseType(200)]
    [ProducesResponseType(404)]
    public static void Find(int id)
    {
    }
}
```

より詳細なメタデータ属性が存在しない場合、この規約がアセンブリに適用されると次のことが適用されます。

* 規約のメソッドは、`Find` という名前のアクションに適用されます。
* `id` という名前のパラメーターは、`Find` アクションに存在します。

### <a name="naming-requirements"></a>名前付けの要件

`[ApiConventionNameMatch]` 属性と `[ApiConventionTypeMatch]` 属性は、適用されるアクションを決定する規約のメソッドに適用することができます。 例えば:

```csharp
[ProducesResponseType(200)]
[ProducesResponseType(404)]
[ApiConventionNameMatch(ApiConventionNameMatchBehavior.Prefix)]
public static void Find(
    [ApiConventionNameMatch(ApiConventionNameMatchBehavior.Suffix)]
    int id)
{ }
```

前の例の場合:

* メソッドに適用された `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` オプションは、規約が "Find" というプレフィックスがあるアクションに一致することを示します。 `Find`、`FindPet`、および `FindById` を含む照合アクションの例。
* パラメーターに適用された `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` は、規約がサフィックス識別子で終わる 1 つのパラメーターが含まれるメソッドと一致することを示します。 例には、`id` や `petId` などのパラメーターが含まれます。 `ApiConventionTypeMatch` も同様に型に適用して、パラメーターの型に制約を設けることができます。 `params[]` の引数では、明示的に一致する必要がない残りのパラメーターを示します。

## <a name="additional-resources"></a>その他の技術情報

* <xref:web-api/advanced/analyzers>
* <xref:tutorials/web-api-help-pages-using-swagger>
