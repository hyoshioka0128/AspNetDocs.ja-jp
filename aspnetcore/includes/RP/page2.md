---
ms.openlocfilehash: 7d84624165abd0cf61d3b94f82f3d25f78dbc630
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57051019"
---
<span data-ttu-id="46c25-101">`<form method="post">` 要素は[フォーム タグ ヘルパー](xref:mvc/views/working-with-forms#the-form-tag-helper)です。</span><span class="sxs-lookup"><span data-stu-id="46c25-101">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="46c25-102">フォーム タグ ヘルパーには自動的に[偽造防止トークン](xref:security/anti-request-forgery)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="46c25-102">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="46c25-103">スキャフォールディング エンジンは、次のような、(ID を除く) モデルの各フィールドの Razor マークアップを作成します。</span><span class="sxs-lookup"><span data-stu-id="46c25-103">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="46c25-104">[検証タグ ヘルパー](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` と ` <span asp-validation-for`) には検証エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="46c25-104">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and ` <span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="46c25-105">検証については、後で詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="46c25-105">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="46c25-106">[ラベル タグ ヘルパー](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) は、`Title` プロパティのラベル キャプションと `for` 属性を生成します。</span><span class="sxs-lookup"><span data-stu-id="46c25-106">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="46c25-107">[入力タグ ヘルパー](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control" />`) は [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 属性を使用し、クライアント側で jQuery 検証に必要な HTML 属性を生成します。</span><span class="sxs-lookup"><span data-stu-id="46c25-107">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control" />`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>
