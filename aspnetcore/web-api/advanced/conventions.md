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
# <a name="use-web-api-conventions"></a><span data-ttu-id="debbb-103">Web API 規約を使用する</span><span class="sxs-lookup"><span data-stu-id="debbb-103">Use web API conventions</span></span>

<span data-ttu-id="debbb-104">作成者: [Pranav Krishnamoorthy](https://github.com/pranavkm)、[Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="debbb-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="debbb-105">ASP.NET Core 2.2 以降では、一般的な [API ドキュメント](xref:tutorials/web-api-help-pages-using-swagger)を抽出し、これを複数のアクション、コントローラー、またはアセンブリ内のすべてのコントローラーに適用する方法が含まれています。</span><span class="sxs-lookup"><span data-stu-id="debbb-105">ASP.NET Core 2.2 and later includes a way to extract common [API documentation](xref:tutorials/web-api-help-pages-using-swagger) and apply it to multiple actions, controllers, or all controllers within an assembly.</span></span> <span data-ttu-id="debbb-106">Web API 規約は、[[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) による個々のアクションの装飾の代わりになるものです。</span><span class="sxs-lookup"><span data-stu-id="debbb-106">Web API conventions are a substitute for decorating individual actions with [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute).</span></span>

<span data-ttu-id="debbb-107">規約を使用すると、次のことを実行できます。</span><span class="sxs-lookup"><span data-stu-id="debbb-107">A convention allows you to:</span></span>

* <span data-ttu-id="debbb-108">特定の型のアクションから返される最も一般的な戻り値の型と状態コードを定義する</span><span class="sxs-lookup"><span data-stu-id="debbb-108">Define the most common return types and status codes returned from a specific type of action.</span></span>
* <span data-ttu-id="debbb-109">定義済みの標準から外れるアクションを識別する</span><span class="sxs-lookup"><span data-stu-id="debbb-109">Identify actions that deviate from the defined standard.</span></span>

<span data-ttu-id="debbb-110">ASP.NET Core MVC 2.2 以降には、一連の既定の規約 `Microsoft.AspNetCore.Mvc.DefaultApiConventions` が含まれています。</span><span class="sxs-lookup"><span data-stu-id="debbb-110">ASP.NET Core MVC 2.2 and later includes a set of default conventions in `Microsoft.AspNetCore.Mvc.DefaultApiConventions`.</span></span> <span data-ttu-id="debbb-111">この規約は、ASP.NET Core **API** プロジェクト テンプレートで提供されるコントローラー (*ValuesController.cs*) に基づいています。</span><span class="sxs-lookup"><span data-stu-id="debbb-111">The conventions are based on the controller (*ValuesController.cs*) provided in the ASP.NET Core **API** project template.</span></span> <span data-ttu-id="debbb-112">アクションがテンプレートのパターンに従う場合は、既定の規約を使用すると成功します。</span><span class="sxs-lookup"><span data-stu-id="debbb-112">If your actions follow the patterns in the template, you should be successful using the default conventions.</span></span> <span data-ttu-id="debbb-113">既定の規約がニーズに合わない場合は、「[Web API 規約を作成する](#create-web-api-conventions)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="debbb-113">If the default conventions don't meet your needs, see [Create web API conventions](#create-web-api-conventions).</span></span>

<span data-ttu-id="debbb-114">実行時に、<xref:Microsoft.AspNetCore.Mvc.ApiExplorer> は規約を理解します。</span><span class="sxs-lookup"><span data-stu-id="debbb-114">At runtime, <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> understands conventions.</span></span> <span data-ttu-id="debbb-115">`ApiExplorer` は [OpenAPI](https://www.openapis.org/) (Swagger とも呼ばれている) ドキュメントのジェネレーターと通信するために MVC を抽象化したものです。</span><span class="sxs-lookup"><span data-stu-id="debbb-115">`ApiExplorer` is MVC's abstraction to communicate with [OpenAPI](https://www.openapis.org/) (also known as Swagger) document generators.</span></span> <span data-ttu-id="debbb-116">適用された規約の属性はアクションと関連付けられており、アクションの OpenAPI ドキュメントに含まれます。</span><span class="sxs-lookup"><span data-stu-id="debbb-116">Attributes from the applied convention are associated with an action and are included in the action's OpenAPI documentation.</span></span> <span data-ttu-id="debbb-117">[API アナライザー](xref:web-api/advanced/analyzers)でも、規約を理解します。</span><span class="sxs-lookup"><span data-stu-id="debbb-117">[API analyzers](xref:web-api/advanced/analyzers) also understand conventions.</span></span> <span data-ttu-id="debbb-118">従来とは異なるアクションである場合 (適用されている規約で文書化されていない状態コードを返す場合など)、警告で状態コードの文書化が促されます。</span><span class="sxs-lookup"><span data-stu-id="debbb-118">If your action is unconventional (for example, it returns a status code that isn't documented by the applied convention), a warning encourages you to document the status code.</span></span>

<span data-ttu-id="debbb-119">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="debbb-119">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="apply-web-api-conventions"></a><span data-ttu-id="debbb-120">Web API 規約を適用する</span><span class="sxs-lookup"><span data-stu-id="debbb-120">Apply web API conventions</span></span>

<span data-ttu-id="debbb-121">規約では作成は行われません。各アクションを 1 つの規約だけに関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="debbb-121">Conventions don't compose; each action may be associated with exactly one convention.</span></span> <span data-ttu-id="debbb-122">より具体的な規約の方が一般的な規約より優先されます。</span><span class="sxs-lookup"><span data-stu-id="debbb-122">More specific conventions take precedence over less specific conventions.</span></span> <span data-ttu-id="debbb-123">優先順位が同じである 2 つ以上の規約が 1 つのアクションに適用されていると、選択は不明確になります。</span><span class="sxs-lookup"><span data-stu-id="debbb-123">The selection is non-deterministic when two or more conventions of the same priority apply to an action.</span></span> <span data-ttu-id="debbb-124">次のオプションは、規約をアクションに適用するためにあります。より具体的なものから並んでいます。</span><span class="sxs-lookup"><span data-stu-id="debbb-124">The following options exist to apply a convention to an action, from the most specific to the least specific:</span></span>

1. <span data-ttu-id="debbb-125">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; 個々のアクションに適用され、適用される規約の種類と規約のメソッドが指定されます。</span><span class="sxs-lookup"><span data-stu-id="debbb-125">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; Applies to individual actions and specifies the convention type and the convention method that applies.</span></span>

    <span data-ttu-id="debbb-126">次の例では、既定の規約の種類の `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` 規約のメソッドが、`Update` アクションに適用されます。</span><span class="sxs-lookup"><span data-stu-id="debbb-126">In the following example, the default convention type's `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` convention method is applied to the `Update` action:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionMethod&highlight=3)]

    <span data-ttu-id="debbb-127">`Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` 規約のメソッドでは、次の属性がアクションに適用されます。</span><span class="sxs-lookup"><span data-stu-id="debbb-127">The `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` convention method applies the following attributes to the action:</span></span>

    ```csharp
    [ProducesDefaultResponseType]
    [ProducesResponseType(204)]
    [ProducesResponseType(404)]
    [ProducesResponseType(400)]
    ```

<span data-ttu-id="debbb-128">`[ProducesDefaultResponseType]` の詳細については、[既定の応答](https://swagger.io/docs/specification/describing-responses/#default)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="debbb-128">For more information on `[ProducesDefaultResponseType]`, see [Default Response](https://swagger.io/docs/specification/describing-responses/#default).</span></span>

1. <span data-ttu-id="debbb-129">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` (コントローラーに適用) &mdash; 指定した規約の種類がコントローラー上のすべてのアクションに適用されます。</span><span class="sxs-lookup"><span data-stu-id="debbb-129">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to a controller &mdash; Applies the specified convention type to all actions on the controller.</span></span> <span data-ttu-id="debbb-130">規約のメソッドは、規約のメソッドが適用されるアクションを決定するヒントで装飾されています。</span><span class="sxs-lookup"><span data-stu-id="debbb-130">A convention method is decorated with hints that determine the actions to which the convention method applies.</span></span> <span data-ttu-id="debbb-131">ヒントの詳細については、「[Web API 規約を作成する](#create-web-api-conventions)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="debbb-131">For more information on hints, see [Create web API conventions](#create-web-api-conventions)).</span></span>

    <span data-ttu-id="debbb-132">次の例では、一連の既定の規約が、*ContactsConventionController* のすべてのアクションに適用されます。</span><span class="sxs-lookup"><span data-stu-id="debbb-132">In the following example, the default set of conventions is applied to all actions in *ContactsConventionController*:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionTypeAttribute&highlight=2)]

1. <span data-ttu-id="debbb-133">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` (アセンブリに適用) &mdash; 指定された規約の種類が、現在のアセンブリのすべてのコントローラーに適用されます。</span><span class="sxs-lookup"><span data-stu-id="debbb-133">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to an assembly &mdash; Applies the specified convention type to all controllers in the current assembly.</span></span> <span data-ttu-id="debbb-134">*Startup.cs* ファイル内でアセンブリ レベルの属性を適用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="debbb-134">As a recommendation, apply assembly-level attributes in the *Startup.cs* file.</span></span>

    <span data-ttu-id="debbb-135">次の例では、一連の既定の規約が、アセンブリのすべてのコントローラーに適用されます。</span><span class="sxs-lookup"><span data-stu-id="debbb-135">In the following example, the default set of conventions is applied to all controllers in the assembly:</span></span>

    [!code-csharp[](conventions/sample/Startup.cs?name=snippet_ApiConventionTypeAttribute&highlight=1)]

## <a name="create-web-api-conventions"></a><span data-ttu-id="debbb-136">Web API 規約を作成する</span><span class="sxs-lookup"><span data-stu-id="debbb-136">Create web API conventions</span></span>

<span data-ttu-id="debbb-137">既定の API 規約がニーズに合わない場合は、独自の規約を作成します。</span><span class="sxs-lookup"><span data-stu-id="debbb-137">If the default API conventions don't meet your needs, create your own conventions.</span></span> <span data-ttu-id="debbb-138">規約は次のようなものです。</span><span class="sxs-lookup"><span data-stu-id="debbb-138">A convention is:</span></span>

* <span data-ttu-id="debbb-139">メソッドを含む静的な種類</span><span class="sxs-lookup"><span data-stu-id="debbb-139">A static type with methods.</span></span>
* <span data-ttu-id="debbb-140">アクションで[応答の種類](#response-types)と[名前付けの要件](#naming-requirements)を定義できる</span><span class="sxs-lookup"><span data-stu-id="debbb-140">Capable of defining [response types](#response-types) and [naming requirements](#naming-requirements) on actions.</span></span>

### <a name="response-types"></a><span data-ttu-id="debbb-141">応答の種類</span><span class="sxs-lookup"><span data-stu-id="debbb-141">Response types</span></span>

<span data-ttu-id="debbb-142">これらのメソッドには `[ProducesResponseType]` または `[ProducesDefaultResponseType]` 属性の注釈が付けられています。</span><span class="sxs-lookup"><span data-stu-id="debbb-142">These methods are annotated with `[ProducesResponseType]` or `[ProducesDefaultResponseType]` attributes.</span></span> <span data-ttu-id="debbb-143">例:</span><span class="sxs-lookup"><span data-stu-id="debbb-143">For example:</span></span>

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

<span data-ttu-id="debbb-144">より詳細なメタデータ属性が存在しない場合、この規約がアセンブリに適用されると次のことが適用されます。</span><span class="sxs-lookup"><span data-stu-id="debbb-144">If more specific metadata attributes are absent, applying this convention to an assembly enforces that:</span></span>

* <span data-ttu-id="debbb-145">規約のメソッドは、`Find` という名前のアクションに適用されます。</span><span class="sxs-lookup"><span data-stu-id="debbb-145">The convention method applies to any action named `Find`.</span></span>
* <span data-ttu-id="debbb-146">`id` という名前のパラメーターは、`Find` アクションに存在します。</span><span class="sxs-lookup"><span data-stu-id="debbb-146">A parameter named `id` is present on the `Find` action.</span></span>

### <a name="naming-requirements"></a><span data-ttu-id="debbb-147">名前付けの要件</span><span class="sxs-lookup"><span data-stu-id="debbb-147">Naming requirements</span></span>

<span data-ttu-id="debbb-148">`[ApiConventionNameMatch]` 属性と `[ApiConventionTypeMatch]` 属性は、適用されるアクションを決定する規約のメソッドに適用することができます。</span><span class="sxs-lookup"><span data-stu-id="debbb-148">The `[ApiConventionNameMatch]` and `[ApiConventionTypeMatch]` attributes can be applied to the convention method that determines the actions to which they apply.</span></span> <span data-ttu-id="debbb-149">例えば:</span><span class="sxs-lookup"><span data-stu-id="debbb-149">For example:</span></span>

```csharp
[ProducesResponseType(200)]
[ProducesResponseType(404)]
[ApiConventionNameMatch(ApiConventionNameMatchBehavior.Prefix)]
public static void Find(
    [ApiConventionNameMatch(ApiConventionNameMatchBehavior.Suffix)]
    int id)
{ }
```

<span data-ttu-id="debbb-150">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="debbb-150">In the preceding example:</span></span>

* <span data-ttu-id="debbb-151">メソッドに適用された `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` オプションは、規約が "Find" というプレフィックスがあるアクションに一致することを示します。</span><span class="sxs-lookup"><span data-stu-id="debbb-151">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` option applied to the method indicates that the convention matches any action prefixed with "Find".</span></span> <span data-ttu-id="debbb-152">`Find`、`FindPet`、および `FindById` を含む照合アクションの例。</span><span class="sxs-lookup"><span data-stu-id="debbb-152">Examples of matching actions include `Find`, `FindPet`, and `FindById`.</span></span>
* <span data-ttu-id="debbb-153">パラメーターに適用された `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` は、規約がサフィックス識別子で終わる 1 つのパラメーターが含まれるメソッドと一致することを示します。</span><span class="sxs-lookup"><span data-stu-id="debbb-153">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` applied to the parameter indicates that the convention matches methods with exactly one parameter ending in the suffix identifier.</span></span> <span data-ttu-id="debbb-154">例には、`id` や `petId` などのパラメーターが含まれます。</span><span class="sxs-lookup"><span data-stu-id="debbb-154">Examples include parameters such as `id` or `petId`.</span></span> <span data-ttu-id="debbb-155">`ApiConventionTypeMatch` も同様に型に適用して、パラメーターの型に制約を設けることができます。</span><span class="sxs-lookup"><span data-stu-id="debbb-155">`ApiConventionTypeMatch` can be similarly applied to types to constrain the parameter type.</span></span> <span data-ttu-id="debbb-156">`params[]` の引数では、明示的に一致する必要がない残りのパラメーターを示します。</span><span class="sxs-lookup"><span data-stu-id="debbb-156">A `params[]` argument indicates remaining parameters that don't need to be explicitly matched.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="debbb-157">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="debbb-157">Additional resources</span></span>

* <xref:web-api/advanced/analyzers>
* <xref:tutorials/web-api-help-pages-using-swagger>
