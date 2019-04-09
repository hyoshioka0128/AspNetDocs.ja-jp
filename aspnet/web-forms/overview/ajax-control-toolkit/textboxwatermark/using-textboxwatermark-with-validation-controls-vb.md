---
uid: web-forms/overview/ajax-control-toolkit/textboxwatermark/using-textboxwatermark-with-validation-controls-vb
title: (VB) の検証コントロールと共に TextBoxWatermark を使用する |Microsoft Docs
author: wenz
description: AJAX Control Toolkit で TextBoxWatermark コントロールでは、テキスト ボックスを拡張するので、ボックス内でテキストが表示されます。 ボックスに、ユーザーがクリックしたときに.
ms.author: riande
ms.date: 06/02/2008
ms.assetid: e6c2cb98-f745-4bc8-973a-813879c8a891
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/textboxwatermark/using-textboxwatermark-with-validation-controls-vb
msc.type: authoredcontent
ms.openlocfilehash: d83fb53ddb40a31013bc724909fa149ce2e4c713
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59387380"
---
# <a name="using-textboxwatermark-with-validation-controls-vb"></a><span data-ttu-id="fd19c-104">検証コントロールと共に TextBoxWatermark を使用する (VB)</span><span class="sxs-lookup"><span data-stu-id="fd19c-104">Using TextBoxWatermark With Validation Controls (VB)</span></span>

<span data-ttu-id="fd19c-105">によって[Christian Wenz](https://github.com/wenz)</span><span class="sxs-lookup"><span data-stu-id="fd19c-105">by [Christian Wenz](https://github.com/wenz)</span></span>

<span data-ttu-id="fd19c-106">[コードのダウンロード](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/TextBoxWatermark2.vb.zip)または[PDF のダウンロード](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/textboxwatermark2VB.pdf)</span><span class="sxs-lookup"><span data-stu-id="fd19c-106">[Download Code](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/TextBoxWatermark2.vb.zip) or [Download PDF](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/textboxwatermark2VB.pdf)</span></span>

> <span data-ttu-id="fd19c-107">AJAX Control Toolkit で TextBoxWatermark コントロールでは、テキスト ボックスを拡張するので、ボックス内でテキストが表示されます。</span><span class="sxs-lookup"><span data-stu-id="fd19c-107">The TextBoxWatermark control in the AJAX Control Toolkit extends a text box so that a text is displayed within the box.</span></span> <span data-ttu-id="fd19c-108">ボックスに、ユーザーがクリックするは空になります。</span><span class="sxs-lookup"><span data-stu-id="fd19c-108">When a user clicks into the box, it is emptied.</span></span> <span data-ttu-id="fd19c-109">ユーザーがテキストを入力しなくても、ボックスのまま、指定済みのテキストが表示されます。</span><span class="sxs-lookup"><span data-stu-id="fd19c-109">If the user leaves the box without entering text, the prefilled text reappears.</span></span> <span data-ttu-id="fd19c-110">これは、同じ ページで、ASP.NET 検証コントロールと衝突する可能性がありますが、これらの問題を克服することがあります。</span><span class="sxs-lookup"><span data-stu-id="fd19c-110">This may collide with ASP.NET Validation Controls on the same page, but these issues may be overcome.</span></span>


## <a name="overview"></a><span data-ttu-id="fd19c-111">概要</span><span class="sxs-lookup"><span data-stu-id="fd19c-111">Overview</span></span>

<span data-ttu-id="fd19c-112">`TextBoxWatermark`ボックス内でテキストが表示されるように、AJAX Control Toolkit のコントロールがテキスト ボックスを拡張します。</span><span class="sxs-lookup"><span data-stu-id="fd19c-112">The `TextBoxWatermark` control in the AJAX Control Toolkit extends a text box so that a text is displayed within the box.</span></span> <span data-ttu-id="fd19c-113">ボックスに、ユーザーがクリックするは空になります。</span><span class="sxs-lookup"><span data-stu-id="fd19c-113">When a user clicks into the box, it is emptied.</span></span> <span data-ttu-id="fd19c-114">ユーザーがテキストを入力しなくても、ボックスのまま、指定済みのテキストが表示されます。</span><span class="sxs-lookup"><span data-stu-id="fd19c-114">If the user leaves the box without entering text, the prefilled text reappears.</span></span> <span data-ttu-id="fd19c-115">これは、同じ ページで、ASP.NET 検証コントロールと衝突する可能性がありますが、これらの問題を克服することがあります。</span><span class="sxs-lookup"><span data-stu-id="fd19c-115">This may collide with ASP.NET Validation Controls on the same page, but these issues may be overcome.</span></span>

## <a name="steps"></a><span data-ttu-id="fd19c-116">手順</span><span class="sxs-lookup"><span data-stu-id="fd19c-116">Steps</span></span>

<span data-ttu-id="fd19c-117">サンプルの基本的なセットアップは、次:`TextBox`を使用してコントロールを透かし、`TextBoxWatermarkExtender`コントロール。</span><span class="sxs-lookup"><span data-stu-id="fd19c-117">The basic setup of the sample is the following: a `TextBox` control is watermarked using a `TextBoxWatermarkExtender` control.</span></span> <span data-ttu-id="fd19c-118">ボタンは、ポストバックをトリガーし、後で、ページの検証コントロールのトリガーに使用されます。</span><span class="sxs-lookup"><span data-stu-id="fd19c-118">A button triggers a postback and will later be used to trigger the validation controls on the page.</span></span> <span data-ttu-id="fd19c-119">また、`ScriptManager`コントロールが ASP.NET AJAX を初期化するために必要です。</span><span class="sxs-lookup"><span data-stu-id="fd19c-119">Also, a `ScriptManager` control is required to initialize ASP.NET AJAX:</span></span>

[!code-aspx[Main](using-textboxwatermark-with-validation-controls-vb/samples/sample1.aspx)]

<span data-ttu-id="fd19c-120">ここで追加、`RequiredFieldValidator`かどうかにテキストが、フィールド、フォームが送信されたときにチェックするコントロール。</span><span class="sxs-lookup"><span data-stu-id="fd19c-120">Now add a `RequiredFieldValidator` control that checks whether there is text in the field when the form is submitted.</span></span> <span data-ttu-id="fd19c-121">`InitialValue`検証コントロールのプロパティで使用されているのと同じ値に設定する必要があります、`TextBoxWatermarkExtender`コントロール。フォームが送信されたときに、変更されていないテキスト ボックスの値はその中の基準値に。</span><span class="sxs-lookup"><span data-stu-id="fd19c-121">The `InitialValue` property of the validator must be set to the same value that is used in the `TextBoxWatermarkExtender` control: When the form is submitted, the value of an unchanged textbox is the watermark value within it:</span></span>

[!code-aspx[Main](using-textboxwatermark-with-validation-controls-vb/samples/sample2.aspx)]

<span data-ttu-id="fd19c-122">ただしこのアプローチの 1 つの問題があります。場合は、クライアントは、JavaScript を無効化、テキスト フィールドがないあらかじめ入力されています、透かしテキストをそのため、`RequiredFieldValidator`エラー メッセージは発生しません。</span><span class="sxs-lookup"><span data-stu-id="fd19c-122">However there is one problem with this approach: If the client disables JavaScript, the text field is not prefilled with the watermark text, therefore the `RequiredFieldValidator` does not trigger an error message.</span></span> <span data-ttu-id="fd19c-123">そのため、2 つ目`RequiredFieldValidator`コントロールが必要ですが、空のテキスト ボックスを確認します (省略すると、`InitialValue`属性)。</span><span class="sxs-lookup"><span data-stu-id="fd19c-123">Therefore, a second `RequiredFieldValidator` control is required which checks for an empty text box (omitting the `InitialValue` attribute).</span></span>

[!code-aspx[Main](using-textboxwatermark-with-validation-controls-vb/samples/sample3.aspx)]

<span data-ttu-id="fd19c-124">両方の検証コントロールを使用しているため`Display` = `"Dynamic"`、エンドユーザーが発生した、この 2 つの検証コントロールの外観から区別できません。 代わりに、ように見えますが、1 つだけです。</span><span class="sxs-lookup"><span data-stu-id="fd19c-124">Since both validators use `Display`=`"Dynamic"`, the end user cannot distinguish from the visual appearance which of the two validators was fired; instead, it looks like there was only one of them.</span></span>

<span data-ttu-id="fd19c-125">最後に、検証コントロールには、エラー メッセージ発行いない場合、フィールド内のテキストを出力するいくつかのサーバー側コードを追加します。</span><span class="sxs-lookup"><span data-stu-id="fd19c-125">Finally, add some server-side code to output the text in the field if no validator issued an error message:</span></span>

[!code-aspx[Main](using-textboxwatermark-with-validation-controls-vb/samples/sample4.aspx)]


[![T<span data-ttu-id="fd19c-126">彼の検証ツールでは、フィールドにテキストがないことという苦情]</span><span class="sxs-lookup"><span data-stu-id="fd19c-126">he validator complains that there is no text in the field]</span></span>(using-textboxwatermark-with-validation-controls-vb/_static/image2.png)](using-textboxwatermark-with-validation-controls-vb/_static/image1.png)

<span data-ttu-id="fd19c-127">検証コントロール文句を言う フィールドにテキストがないこと ([フルサイズの画像を表示する をクリックします](using-textboxwatermark-with-validation-controls-vb/_static/image3.png))。</span><span class="sxs-lookup"><span data-stu-id="fd19c-127">The validator complains that there is no text in the field ([Click to view full-size image](using-textboxwatermark-with-validation-controls-vb/_static/image3.png))</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="fd19c-128">前へ</span><span class="sxs-lookup"><span data-stu-id="fd19c-128">Previous</span></span>](using-textboxwatermark-in-a-formview-vb.md)
