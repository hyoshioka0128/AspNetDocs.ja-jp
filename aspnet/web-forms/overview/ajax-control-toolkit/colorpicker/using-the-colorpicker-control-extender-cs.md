---
uid: web-forms/overview/ajax-control-toolkit/colorpicker/using-the-colorpicker-control-extender-cs
title: ColorPicker コントロール エクステンダー (c#) を使用して |Microsoft Docs
author: microsoft
description: ColorPicker では、popup コントロールの UI を使用したクライアント側の色を選択機能を提供する ASP.NET AJAX エクステンダーです。 任意の ASP.NET にアタッチできます.
ms.author: riande
ms.date: 05/12/2009
ms.assetid: 0d86a1e7-a910-4ab2-b85c-7a9ea6906c39
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/colorpicker/using-the-colorpicker-control-extender-cs
msc.type: authoredcontent
ms.openlocfilehash: 58b8d581ed426227ed77435e22c84e9ea5d62ebe
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57040049"
---
<a name="using-the-colorpicker-control-extender-c"></a><span data-ttu-id="53d01-104">ColorPicker コントロール エクステンダー (c#) を使用します。</span><span class="sxs-lookup"><span data-stu-id="53d01-104">Using the ColorPicker Control Extender (C#)</span></span>
====================
<span data-ttu-id="53d01-105">によって[Microsoft](https://github.com/microsoft)</span><span class="sxs-lookup"><span data-stu-id="53d01-105">by [Microsoft](https://github.com/microsoft)</span></span>

> <span data-ttu-id="53d01-106">ColorPicker では、popup コントロールの UI を使用したクライアント側の色を選択機能を提供する ASP.NET AJAX エクステンダーです。</span><span class="sxs-lookup"><span data-stu-id="53d01-106">ColorPicker is an ASP.NET AJAX extender that provides client-side color-picking functionality with UI in a popup control.</span></span> <span data-ttu-id="53d01-107">ASP.NET の TextBox コントロールにアタッチできます。</span><span class="sxs-lookup"><span data-stu-id="53d01-107">It can be attached to any ASP.NET TextBox control.</span></span> <span data-ttu-id="53d01-108">です。</span><span class="sxs-lookup"><span data-stu-id="53d01-108">It.</span></span>


<span data-ttu-id="53d01-109">このチュートリアルの目的では、AJAX Control Toolkit ColorPicker コントロール エクステンダーを使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="53d01-109">The goal of this tutorial is to explain how you can use the AJAX Control Toolkit ColorPicker control extender.</span></span> <span data-ttu-id="53d01-110">ColorPicker コントロール エクステンダーでは、色を選択することができます、ポップアップ ダイアログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="53d01-110">The ColorPicker control extender displays a popup dialog that enables you to select a color.</span></span> <span data-ttu-id="53d01-111">ColorPicker は、ユーザーが色を選択するための直感的なユーザー インターフェイスを提供したい場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="53d01-111">The ColorPicker is useful whenever you want to provide an intuitive user interface for a user to pick a color.</span></span>

## <a name="extending-a-textbox-control-with-the-colorpicker-control-extender"></a><span data-ttu-id="53d01-112">ColorPicker コントロール エクステンダーを TextBox コントロールを拡張します。</span><span class="sxs-lookup"><span data-stu-id="53d01-112">Extending a TextBox Control with the ColorPicker Control Extender</span></span>

<span data-ttu-id="53d01-113">たとえば、ユーザーがカスタマイズされたビジネス カードを作成できるようにする web サイトを作成することに想像してください。</span><span class="sxs-lookup"><span data-stu-id="53d01-113">Imagine, for example, that you want to create a website that enables visitors to create customized business cards.</span></span> <span data-ttu-id="53d01-114">訪問者は、名刺のテキストを入力し、色を選択できます。</span><span class="sxs-lookup"><span data-stu-id="53d01-114">Visitors can enter the text for a business card and pick the color.</span></span> <span data-ttu-id="53d01-115">リスト 1 での ASP.NET ページには、txtCardText txtCardColor という 2 つのテキスト ボックス コントロールが含まれています。</span><span class="sxs-lookup"><span data-stu-id="53d01-115">The ASP.NET page in Listing 1 contains two TextBox controls named txtCardText and txtCardColor.</span></span> <span data-ttu-id="53d01-116">フォームを送信すると、選択した値が表示されます (図 1 参照)。</span><span class="sxs-lookup"><span data-stu-id="53d01-116">When you submit the form, the selected values are displayed (see Figure 1).</span></span>


<span data-ttu-id="53d01-117">[![ビジネスのカードを作成するための簡単なフォーム](using-the-colorpicker-control-extender-cs/_static/image1.jpg)](using-the-colorpicker-control-extender-cs/_static/image1.png)</span><span class="sxs-lookup"><span data-stu-id="53d01-117">[![Simple form for creating a business card](using-the-colorpicker-control-extender-cs/_static/image1.jpg)](using-the-colorpicker-control-extender-cs/_static/image1.png)</span></span>

<span data-ttu-id="53d01-118">**図 01**:ビジネスのカードを作成するための簡単なフォーム ([フルサイズの画像を表示する をクリックします](using-the-colorpicker-control-extender-cs/_static/image2.png))。</span><span class="sxs-lookup"><span data-stu-id="53d01-118">**Figure 01**: Simple form for creating a business card ([Click to view full-size image](using-the-colorpicker-control-extender-cs/_static/image2.png))</span></span>


<span data-ttu-id="53d01-119">**1 - CreateCard.aspx を一覧表示します。**</span><span class="sxs-lookup"><span data-stu-id="53d01-119">**Listing 1 - CreateCard.aspx**</span></span>

[!code-aspx[Main](using-the-colorpicker-control-extender-cs/samples/sample1.aspx)]

<span data-ttu-id="53d01-120">リスト 1 の動作がフォームでは、優れたユーザー エクスペリエンスは提供されません。</span><span class="sxs-lookup"><span data-stu-id="53d01-120">The form in Listing 1 works, but it does not provide a great user experience.</span></span> <span data-ttu-id="53d01-121">ユーザーは、テキスト ボックスに色を入力します。</span><span class="sxs-lookup"><span data-stu-id="53d01-121">The user has to type a color into the textbox.</span></span> <span data-ttu-id="53d01-122">場合は、ユーザーが特殊な色のなど pea 緑 - は、ユーザーの適切な網掛けだけが手助けがなくて HTML カラー コードを算出する必要があります。</span><span class="sxs-lookup"><span data-stu-id="53d01-122">If the user wants a specialized color - for example, just the right shade of pea green - then the user must figure out the HTML color code without any help.</span></span>

<span data-ttu-id="53d01-123">ColorPicker コントロール エクステンダーを使用して、優れたユーザー エクスペリエンスを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="53d01-123">You can use the ColorPicker control extender to create a better user experience.</span></span> <span data-ttu-id="53d01-124">TextBox コントロールにフォーカスを移動すると、ColorPicker に色のダイアログが表示されます (図 2 参照)。</span><span class="sxs-lookup"><span data-stu-id="53d01-124">The ColorPicker displays a color dialog when you move focus to a TextBox control (see Figure 2).</span></span>


<span data-ttu-id="53d01-125">[![ColorPicker コントロール エクステンダー](using-the-colorpicker-control-extender-cs/_static/image2.jpg)](using-the-colorpicker-control-extender-cs/_static/image3.png)</span><span class="sxs-lookup"><span data-stu-id="53d01-125">[![The ColorPicker Control Extender](using-the-colorpicker-control-extender-cs/_static/image2.jpg)](using-the-colorpicker-control-extender-cs/_static/image3.png)</span></span>

<span data-ttu-id="53d01-126">**図 02**:ColorPicker コントロール エクステンダー ([フルサイズの画像を表示する をクリックします](using-the-colorpicker-control-extender-cs/_static/image4.png))。</span><span class="sxs-lookup"><span data-stu-id="53d01-126">**Figure 02**: The ColorPicker Control Extender ([Click to view full-size image](using-the-colorpicker-control-extender-cs/_static/image4.png))</span></span>


<span data-ttu-id="53d01-127">ColorPicker コントロール エクステンダーを使用して、リスト 1 でフォームを 2 つの手順を完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="53d01-127">You need to complete two steps to use the ColorPicker control extender with the form in Listing 1:</span></span>

1. <span data-ttu-id="53d01-128">ScriptManager コントロールをページに追加します。</span><span class="sxs-lookup"><span data-stu-id="53d01-128">Add a ScriptManager control to the page</span></span>
2. <span data-ttu-id="53d01-129">ColorPicker コントロール エクステンダーをページに追加します。</span><span class="sxs-lookup"><span data-stu-id="53d01-129">Add the ColorPicker control extender to the page</span></span>

<span data-ttu-id="53d01-130">ColorPicker を使用する前に、ページに、ScriptManager を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="53d01-130">Before you can use the ColorPicker, you must add a ScriptManager to your page.</span></span> <span data-ttu-id="53d01-131">Scriptmanager コントロールを追加する点としては、開始のサーバー側のすぐ下&lt;フォーム&gt;タグ。</span><span class="sxs-lookup"><span data-stu-id="53d01-131">A good place to add the ScriptManager is right below the opening server-side &lt;form&gt; tag.</span></span> <span data-ttu-id="53d01-132">([AJAX Extensions] タブでは、scriptmanager コントロールがあります)、ツールボックスから、ページ上に、scriptmanager コントロールをドラッグできます。</span><span class="sxs-lookup"><span data-stu-id="53d01-132">You can drag the ScriptManager onto the page from the toolbox (the ScriptManager is located under the AJAX Extensions tab).</span></span> <span data-ttu-id="53d01-133">または、開始のサーバー側のフォーム タグの下に、ソース ビューに、次のタグを入力できます。</span><span class="sxs-lookup"><span data-stu-id="53d01-133">Alternatively, you can type the following tag into Source View beneath the opening server-side form tag:</span></span>

<span data-ttu-id="53d01-134">&lt;asp:ScriptManager ID="ScriptManager1" runat="server" /&gt;</span><span class="sxs-lookup"><span data-stu-id="53d01-134">&lt;asp:ScriptManager ID="ScriptManager1" runat="server" /&gt;</span></span>

<span data-ttu-id="53d01-135">ColorPicker コントロール エクステンダーをページに追加する最も簡単な方法は、デザイン ビューでです。</span><span class="sxs-lookup"><span data-stu-id="53d01-135">The easiest way to add the ColorPicker control extender to the page is in Design View.</span></span> <span data-ttu-id="53d01-136">TxtCardColor テキスト ボックスの上にマウスを移動する場合は、スマート タスク オプションは、可能で表示されます。 エクステンダーを追加する (図 3 を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="53d01-136">If you hover your mouse over the txtCardColor TextBox, a smart task option appears the enables you to add an extender (see Figure 3).</span></span> <span data-ttu-id="53d01-137">このオプションを選択する場合、Extender ウィザードでは、(図 4 参照) が表示されます。</span><span class="sxs-lookup"><span data-stu-id="53d01-137">If you pick this option, the Extender Wizard appears (see Figure 4).</span></span>


<span data-ttu-id="53d01-138">[![Extender の追加](using-the-colorpicker-control-extender-cs/_static/image3.jpg)](using-the-colorpicker-control-extender-cs/_static/image5.png)</span><span class="sxs-lookup"><span data-stu-id="53d01-138">[![Adding an extender](using-the-colorpicker-control-extender-cs/_static/image3.jpg)](using-the-colorpicker-control-extender-cs/_static/image5.png)</span></span>

<span data-ttu-id="53d01-139">**図 03**:Extender の追加 ([フルサイズの画像を表示する をクリックします](using-the-colorpicker-control-extender-cs/_static/image6.png))。</span><span class="sxs-lookup"><span data-stu-id="53d01-139">**Figure 03**: Adding an extender ([Click to view full-size image](using-the-colorpicker-control-extender-cs/_static/image6.png))</span></span>


<span data-ttu-id="53d01-140">[![エクステンダーのウィザードを使用してコントロール エクステンダーを選択します。](using-the-colorpicker-control-extender-cs/_static/image4.jpg)](using-the-colorpicker-control-extender-cs/_static/image7.png)</span><span class="sxs-lookup"><span data-stu-id="53d01-140">[![Selecting a control extender with the Extender Wizard](using-the-colorpicker-control-extender-cs/_static/image4.jpg)](using-the-colorpicker-control-extender-cs/_static/image7.png)</span></span>

<span data-ttu-id="53d01-141">**図 04**:エクステンダーのウィザードを使用してコントロール エクステンダーを選択すると ([フルサイズの画像を表示する をクリックします](using-the-colorpicker-control-extender-cs/_static/image8.png))。</span><span class="sxs-lookup"><span data-stu-id="53d01-141">**Figure 04**: Selecting a control extender with the Extender Wizard ([Click to view full-size image](using-the-colorpicker-control-extender-cs/_static/image8.png))</span></span>


<span data-ttu-id="53d01-142">ColorPicker エクステンダー txtCardColor テキスト ボックスを拡張する ColorPicker エクステンダーを選択することができます。</span><span class="sxs-lookup"><span data-stu-id="53d01-142">You can pick the ColorPicker extender to extend the txtCardColor TextBox with the ColorPicker extender.</span></span> <span data-ttu-id="53d01-143">ダイアログ ボックスを閉じるには、[ok] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="53d01-143">Click OK to close the dialog.</span></span>

<span data-ttu-id="53d01-144">これらの変更を行った後、ページのソースは、リスト 2 のようになります。</span><span class="sxs-lookup"><span data-stu-id="53d01-144">After you make these changes, the source for the page looks like Listing 2.</span></span>

<span data-ttu-id="53d01-145">2 - (ColorPicker) を含む CreateCard.aspx を一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="53d01-145">Listing 2 - CreateCard.aspx (with ColorPicker)</span></span>

[!code-aspx[Main](using-the-colorpicker-control-extender-cs/samples/sample2.aspx)]

<span data-ttu-id="53d01-146">ページに今すぐ txtCardColor TextBox コントロールのすぐ下に表示される ColorPickerExtender コントロールが含まれていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="53d01-146">Notice that the page now contains a ColorPickerExtender control that appears directly below the txtCardColor TextBox control.</span></span> <span data-ttu-id="53d01-147">ColorPickerExtender コントロールは、カラー ピッカー ダイアログを表示するように、txtCardColor コントロールを拡張します。</span><span class="sxs-lookup"><span data-stu-id="53d01-147">The ColorPickerExtender control extends the txtCardColor control so that it displays a color picker dialog.</span></span>

## <a name="using-a-button-to-launch-the-color-picker-dialog"></a><span data-ttu-id="53d01-148">カラー ピッカー ダイアログ ボックスを起動するのにボタンの使用</span><span class="sxs-lookup"><span data-stu-id="53d01-148">Using a Button to Launch the Color Picker Dialog</span></span>

<span data-ttu-id="53d01-149">ColorPicker エクステンダーには、次のプロパティがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="53d01-149">The ColorPicker extender supports the following properties:</span></span>

- <span data-ttu-id="53d01-150">PopupButtonId - カラー ピッカー ダイアログを表示すると、ページ上のボタンの ID。</span><span class="sxs-lookup"><span data-stu-id="53d01-150">PopupButtonId - The ID of a button on the page that causes the color picker dialog to appear.</span></span>
- <span data-ttu-id="53d01-151">PopupPosition - カラー ピッカー ダイアログ ボックスのターゲット コントロールに対する相対的な位置。</span><span class="sxs-lookup"><span data-stu-id="53d01-151">PopupPosition - The position, relative to the target control, of the color picker dialog.</span></span> <span data-ttu-id="53d01-152">使用可能な値は、Absolute、Center、斜め、BottomRight、左、右、右、左 (既定では斜め) です。</span><span class="sxs-lookup"><span data-stu-id="53d01-152">Possible values are Absolute, Center, BottomLeft, BottomRight, TopLeft, TopRight, Right, and Left (the default is BottomLeft).</span></span>
- <span data-ttu-id="53d01-153">SampleControlId - 選択した色を表示するコントロールの ID。</span><span class="sxs-lookup"><span data-stu-id="53d01-153">SampleControlId - The ID of a control that displays the selected color.</span></span>
- <span data-ttu-id="53d01-154">SelectedColor - 初期の色、ColorPicker で選択されています。</span><span class="sxs-lookup"><span data-stu-id="53d01-154">SelectedColor - The initial color selected by the ColorPicker.</span></span>

<span data-ttu-id="53d01-155">これらのプロパティを使用して、カラー ピッカー ダイアログ ボックスを表示する方法と、選択した色の表示方法をカスタマイズすることができます。</span><span class="sxs-lookup"><span data-stu-id="53d01-155">You can use these properties to customize how the color picker dialog is displayed and how the selected color is displayed.</span></span> <span data-ttu-id="53d01-156">リスト 3 のページでは、これらのプロパティのいくつかの使用方法を示します。</span><span class="sxs-lookup"><span data-stu-id="53d01-156">The page in Listing 3 illustrates how you can use several of these properties.</span></span>

<span data-ttu-id="53d01-157">**3 - CreateCardButton.aspx を一覧表示します。**</span><span class="sxs-lookup"><span data-stu-id="53d01-157">**Listing 3 - CreateCardButton.aspx**</span></span>

[!code-aspx[Main](using-the-colorpicker-control-extender-cs/samples/sample3.aspx)]

<span data-ttu-id="53d01-158">リスト 3 のページには、色選択にはが含まれています (図 5 を参照してください) ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="53d01-158">The page in Listing 3 includes a Pick Color button (see Figure 5).</span></span> <span data-ttu-id="53d01-159">このボタンをクリックすると、テキスト ボックス上にカラー ピッカー ダイアログ ボックスが表示されます。</span><span class="sxs-lookup"><span data-stu-id="53d01-159">When you click this button, the color picker dialog appears above the TextBox.</span></span> <span data-ttu-id="53d01-160">ダイアログ ボックスから色を選択する場合は、lblSample ラベル コントロールの背景色として、選択した色が表示されます。</span><span class="sxs-lookup"><span data-stu-id="53d01-160">If you select a color from the dialog then the selected color appears as the background color of the lblSample Label control.</span></span>

<span data-ttu-id="53d01-161">ColorPicker PopupButtonID プロパティは、色の選択 ボタンを関連付ける ColorPicker エクステンダーに使用されます。</span><span class="sxs-lookup"><span data-stu-id="53d01-161">The ColorPicker PopupButtonID property is used to associate the Pick Color button with the ColorPicker extender.</span></span> <span data-ttu-id="53d01-162">PopupButtonID プロパティの値を指定するときに、カラー ピッカー ダイアログ不要になった、ターゲット コントロールにフォーカスがある場合が表示されます。</span><span class="sxs-lookup"><span data-stu-id="53d01-162">When you supply a value for the PopupButtonID property, the color picker dialog no longer appears when the target control has focus.</span></span> <span data-ttu-id="53d01-163">ダイアログ ボックスを表示するボタンをクリックする必要があります。</span><span class="sxs-lookup"><span data-stu-id="53d01-163">You must click the button to display the dialog.</span></span>

<span data-ttu-id="53d01-164">SampleControlID プロパティは、関連付ける、ColorPicker で選択した色を表示するコントロールに使用されます。</span><span class="sxs-lookup"><span data-stu-id="53d01-164">The SampleControlID property is used to associate a control that displays the selected color with the ColorPicker.</span></span> <span data-ttu-id="53d01-165">ColorPicker は、現在選択されている色にこのコントロールの背景色を変更します。</span><span class="sxs-lookup"><span data-stu-id="53d01-165">The ColorPicker changes the background color of this control to the currently selected color.</span></span>


<span data-ttu-id="53d01-166">[![ボタンのカラー ピッカー ダイアログを表示します。](using-the-colorpicker-control-extender-cs/_static/image5.jpg)](using-the-colorpicker-control-extender-cs/_static/image9.png)</span><span class="sxs-lookup"><span data-stu-id="53d01-166">[![Displaying the color picker dialog with a button](using-the-colorpicker-control-extender-cs/_static/image5.jpg)](using-the-colorpicker-control-extender-cs/_static/image9.png)</span></span>

<span data-ttu-id="53d01-167">**図 05**:ボタンのカラー ピッカー ダイアログを表示する ([フルサイズの画像を表示する をクリックします](using-the-colorpicker-control-extender-cs/_static/image10.png))。</span><span class="sxs-lookup"><span data-stu-id="53d01-167">**Figure 05**: Displaying the color picker dialog with a button ([Click to view full-size image](using-the-colorpicker-control-extender-cs/_static/image10.png))</span></span>


## <a name="summary"></a><span data-ttu-id="53d01-168">まとめ</span><span class="sxs-lookup"><span data-stu-id="53d01-168">Summary</span></span>

<span data-ttu-id="53d01-169">このチュートリアルでは、ColorPicker コントロール エクステンダーを使用して、ポップアップ カラー ピッカー ダイアログを表示する方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="53d01-169">In this tutorial, you learned how to use the ColorPicker control extender to display a popup color picker dialog.</span></span> <span data-ttu-id="53d01-170">最初に、TextBox コントロールにフォーカスが移動すると、ダイアログ ボックスを表示する方法について確認しました。</span><span class="sxs-lookup"><span data-stu-id="53d01-170">First, we examined how you can display the dialog when focus is moved to a TextBox control.</span></span> <span data-ttu-id="53d01-171">次に、ボタンがクリックされたときに、カラー ピッカー ダイアログ ボックスを表示するボタンを作成する方法を学習しました。</span><span class="sxs-lookup"><span data-stu-id="53d01-171">Next, you learned how to create a button that displays the color picker dialog when the button is clicked.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="53d01-172">次へ</span><span class="sxs-lookup"><span data-stu-id="53d01-172">Next</span></span>](using-the-colorpicker-control-extender-vb.md)
