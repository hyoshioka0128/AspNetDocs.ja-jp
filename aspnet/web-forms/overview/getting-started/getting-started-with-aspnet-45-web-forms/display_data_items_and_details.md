---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
title: 項目し、詳細のデータの表示 |Microsoft Docs
author: Erikre
description: このチュートリアル シリーズが ASP.NET 4.7 および Microsoft Visual Studio 2017 に ASP.NET Web フォーム アプリケーションの構築の基礎を表示します。
ms.author: riande
ms.date: 1/04/2019
ms.assetid: 64a491a8-0ed6-4c2f-9c1c-412962eb6006
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
msc.type: authoredcontent
ms.openlocfilehash: acc2f8e78375ef0455d467e2af750ecbee623224
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57044639"
---
<a name="display-data-items-and-details"></a>データ アイテムの表示と詳細
====================
によって[Erik Reitan](https://github.com/Erikre)

> このチュートリアル シリーズでは、ASP.NET 4.7 および Microsoft Visual Studio 2017 に ASP.NET Web フォーム アプリケーションの構築の基礎を説明します。

このチュートリアルでは、データ項目と ASP.NET Web フォームと Entity Framework Code First でのデータ項目の詳細を表示する方法を学習します。 このチュートリアルでは、Wingtip 玩具店のチュートリアル シリーズの一部として、前の「UI とナビゲーション」チュートリアルに基づいています。 このチュートリアルを完了した後に製品が表示されます、 *ProductsList.aspx*ページと、製品の詳細について、 *ProductDetails.aspx*ページ。

## <a name="youll-learn-how-to"></a>以下の方法について説明します。

- データベースから製品を表示するデータ コントロールを追加します。
- データ コントロールを選択したデータに接続します。
- データベースから製品の詳細を表示するデータ コントロールを追加します。
- クエリ文字列から値を取得し、その値を使用して、データベースから取得したデータを制限するには

### <a name="features-introduced-in-this-tutorial"></a>このチュートリアルで導入された機能:

- モデル バインド
- 値プロバイダー

## <a name="add-a-data-control"></a>データ コントロールを追加します。

いくつかの異なるオプションを使用すると、サーバー コントロールにデータをバインドします。 最も一般的なのとおりです。

 * データ ソース コントロールの追加
 * コードを手動で追加します。
 * モデル バインドを使用します。

### <a name="use-a-data-source-control-to-bind-data"></a>データ ソース コントロールを使用してデータをバインドするには

データ ソース コントロールの追加データを表示するコントロールにデータ ソース コントロールをリンクすることができます。 この方法により、ここでは、代わりにできますプログラムでは、サーバー側コントロールをデータ ソースに接続します。

### <a name="code-by-hand-to-bind-data"></a>データをバインドするには、手動でコード

手動でコーディングが含まれます。

1. 値の読み取り
2. かどうかは null をチェックします。
3. 適切な型に変換します。
4. 変換成功の確認
5. クエリで値を使用します。 

このアプローチにより、データ アクセス ロジックを完全に制御できます。

### <a name="use-model-binding-to-bind-data"></a>モデル バインドを使用してデータをバインドするには

モデル バインドでは、はるかに少ないコードで結果をバインドすることができ、使用すると、アプリケーション全体で機能を再利用できます。 豊富なデータ バインディング フレームワークを提供しながらコード中心のデータ アクセス ロジックと作業が簡略化します。

## <a name="display-products"></a>製品を表示します。

このチュートリアルでは、データをバインドするのにモデル バインドを使用します。 モデル バインドを使用してデータを選択するデータ コントロールを構成するには、コントロールを設定する`SelectMethod`プロパティ ページのコードでメソッドの名前にします。 データ コントロールでは、ページのライフ サイクルの適切なタイミングでメソッドを呼び出すし、自動的に返されるデータをバインドします。 明示的に呼び出す必要はありません、`DataBind`メソッド。

1. **ソリューション エクスプ ローラー**オープン*ProductList.aspx*します。
2. このマークアップで既存のマークアップに置き換えます。   

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample1.aspx)]

このコードを使用して、 **ListView**という名前のコントロール`productList`商品を表示します。

[!code-aspx-csharp[Main](display_data_items_and_details/samples/sample2.aspx)]

テンプレートとスタイルを使用して定義する方法、 **ListView**コントロールにデータが表示されます。 任意の繰り返し構造内のデータと便利です。 ただしこの**ListView**の例には、データベースのデータだけが表示されます、編集、挿入、およびデータを削除して、並べ替えやデータをページにユーザーを有効にするコードがないこともできます。

設定して、`ItemType`プロパティ、 **ListView**制御、データ バインディング式`Item`が使用可能なコントロールが厳密に型指定されました。 指定するなど、IntelliSense 項目オブジェクトの詳細を選択するには前のチュートリアルで前述の`ProductName`:

![データを表示する項目と IntelliSense の詳細](display_data_items_and_details/_static/image1.png)

モデル バインドを使用して指定することもいる、`SelectMethod`値。 この値 (`GetProducts`) に対応するメソッドのコードを追加します次の手順で製品を表示するには、遅れています。

### <a name="add-code-to-display-products"></a>製品を表示するコードを追加します。

この手順では、データを読み込むコードを追加します、 **ListView**コントロールに、データベースから製品データ。 コードでは、すべての製品と個々 のカテゴリの製品を表示をサポートします。

1. **ソリューション エクスプ ローラー**を右クリックして*ProductList.aspx*選び**コードの表示**します。
2. 既存のコードを置き換える、 *ProductList.aspx.cs*このファイル。   

    [!code-csharp[Main](display_data_items_and_details/samples/sample3.cs)]

このコードを示しています、`GetProducts`メソッドを**ListView**コントロールの`ItemType`内のプロパティ参照、 *ProductList.aspx*ページ。 コードの設定を特定のデータベース カテゴリに結果を制限するため、`categoryId`に渡されるクエリ文字列値から、 *ProductList.aspx*ときにページ、 *ProductList.aspx*ページは、移動します。 `QueryStringAttribute`クラス、`System.Web.ModelBinding`名前空間を使用して、クエリ文字列変数の値を取得`id`します。 これに指示するクエリ文字列から値をバインドしようとするモデル バインド、`categoryId`実行時にパラメーター。

クエリの結果に一致するデータベース内のこれらの製品に制限されています ページにクエリ文字列として有効なカテゴリが渡される、`categoryId`値。 たとえば場合、 *ProductsList.aspx*はページの URL:


[!code-console[Main](display_data_items_and_details/samples/sample4.cmd)]

ページには、製品のみが表示されます。 場所、 `categoryId` equals`1`します。

クエリ文字列が含まれていないときに場合、すべての製品が表示されます、 *ProductList.aspx*ページが呼び出されます。

これらのメソッドの値のソースとして参照されます*値プロバイダー* (など*QueryString*)、パラメーターの属性を使用する値プロバイダーを示すとして参照されます*値プロバイダー属性*(など`id`)。 ASP.NET には、クエリ文字列、cookie、フォーム値、コントロール、ビュー ステート、セッション状態、およびプロファイルのプロパティなどの Web フォーム アプリケーションでの値プロバイダーとすべてのユーザー入力の一般的なソースの対応する属性が含まれています。 カスタム値プロバイダーを記述することもできます。

### <a name="run-the-application"></a>アプリケーションの実行

すべての製品またはカテゴリの製品を表示するアプリケーションを実行します。

1. キーを押して**F5** Visual Studio でアプリケーションを実行するときにします。  
   ブラウザーが開きを示しています、 *Default.aspx*ページ。

2. 選択**自動車**製品カテゴリのナビゲーション メニュー。  
   *ProductList.aspx*ページのみ表示**自動車**カテゴリの製品です。 このチュートリアルの後半では、製品の詳細を表示します。  

    ![データを表示する項目と詳細 - 自動車](display_data_items_and_details/_static/image2.png)

3. 選択**製品**上部にあるナビゲーション メニュー。  
   もう一度、 *ProductList.aspx*ページが表示されますが、今度は製品の全リストが表示されます。   

    ![データを表示する項目と製品の詳細](display_data_items_and_details/_static/image3.png)

4. ブラウザーを閉じて、Visual Studio に戻ります。

### <a name="add-a-data-control-to-display-product-details"></a>製品の詳細を表示するデータ コントロールを追加します。

内のマークアップを次に、変更を*ProductDetails.aspx*ページが特定の製品情報を表示する前のチュートリアルで追加しました。

1. **ソリューション エクスプ ローラー**オープン*ProductDetails.aspx*します。

2. このマークアップで既存のマークアップに置き換えます。

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample5.aspx)] 

    このコードを使用して、 **FormView**特定の製品の詳細を表示するコントロール。 このマークアップは、データを表示するために使用するメソッドと同様のメソッドを使用して、 *ProductList.aspx*ページ。 **FormView**コントロールを使用すると、データ ソースから一度に 1 つのレコードを表示します。 使用すると、 **FormView**コントロール、データ バインドされた値を表示および編集テンプレートを作成します。 これらのテンプレートは、バインド式のコントロールを含めるし、フォームの外観と機能を定義する書式設定します。

前のマークアップをデータベースに接続するには、コードを追加する必要があります。

1. **ソリューション エクスプ ローラー**、右クリック*ProductDetails.aspx*  をクリックし、**コードの表示**します。  
   *ProductDetails.aspx.cs*ファイルが表示されます。

2. このコードでは、既存のコードを置き換えます。   

    [!code-csharp[Main](display_data_items_and_details/samples/sample6.cs)]

このコードをチェックする"`productID`"クエリ文字列値。 有効なクエリ文字列値が見つかった場合は、一致する製品が表示されます。 クエリ文字列が見つからない、またはその値が無効です、製品は表示されません。

### <a name="run-the-application"></a>アプリケーションの実行

プロダクト ID に基づいて、表示される個別の製品を表示するアプリケーションを実行するようになりました

1. キーを押して**F5** Visual Studio でアプリケーションを実行するときにします。  
   ブラウザーが開きを示しています、 *Default.aspx*ページ。

2. 選択**ボート**カテゴリのナビゲーション メニュー。  
   *ProductList.aspx*ページが表示されます。

3. 選択**用紙ボート**製品一覧から。
   *ProductDetails.aspx*ページが表示されます。

    ![データを表示する項目と製品の詳細](display_data_items_and_details/_static/image4.png)
    
4. ブラウザーを閉じます。


## <a name="additional-resources"></a>その他の技術情報

[取得して、モデル バインディングと web フォームでデータの表示](../../presenting-and-managing-data/model-binding/retrieving-data.md)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、マークアップと製品と製品の詳細を表示するコードを追加しました。 厳密に型指定されたデータ コントロール、モデル バインド、および値プロバイダーについて説明しました。 次のチュートリアルでは、Wingtip Toys のサンプル アプリケーションをショッピング カートを追加します。 

> [!div class="step-by-step"]
> [前へ](ui_and_navigation.md)
> [次へ](shopping-cart.md)
