---
uid: mvc/overview/older-versions-1/models-data/validating-with-a-service-layer-vb
title: サービス層 (VB) の検証 |Microsoft Docs
author: StephenWalther
description: コント ローラー アクションから、別のサービス層には、検証ロジックを移動する方法について説明します。 このチュートリアルでは、Stephen Walther がについて説明する方法をしています.
ms.author: riande
ms.date: 03/02/2009
ms.assetid: 344bb38e-4965-4c47-bda1-f6d29ae5b83a
msc.legacyurl: /mvc/overview/older-versions-1/models-data/validating-with-a-service-layer-vb
msc.type: authoredcontent
ms.openlocfilehash: fc819494ef58824d485144396e3a995d906c8b42
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59398710"
---
# <a name="validating-with-a-service-layer-vb"></a>サービス層の検証 (VB)

によって[Stephen Walther](https://github.com/StephenWalther)

> コント ローラー アクションから、別のサービス層には、検証ロジックを移動する方法について説明します。 このチュートリアルでは、Stephen Walther は、コント ローラー層から、サービス層を分離することで、懸念事項のシャープな分離を維持する方法について説明します。


このチュートリアルの目的では、ASP.NET MVC アプリケーションで検証を実行する 1 つの方法について説明します。 このチュートリアルでは、コント ローラーから、別のサービス層には、検証ロジックを移動する方法について説明します。

## <a name="separating-concerns"></a>懸念事項の分離

ASP.NET MVC アプリケーションをビルドすると、コント ローラー アクションの内部データベース ロジックを置かないでください。 データベースとコント ローラー ロジックの混合によってアプリケーションの時間の経過と共に管理が困難です。 すべてのデータベース ロジックを別のリポジトリ層に配置することをお勧めします。

たとえば、リスト 1 には、ProductRepository をという名前の単純なリポジトリが含まれています。 製品のリポジトリには、すべてのアプリケーションのデータ アクセス コードが含まれています。 一覧には、製品のリポジトリを実装する IProductRepository インターフェイスも含まれています。

**1 - Models\ProductRepository.vb を一覧表示します。**

[!code-vb[Main](validating-with-a-service-layer-vb/samples/sample1.vb)]

リスト 2 でコント ローラーは、その Index() と Create() の両方のアクションのリポジトリ層を使用します。 このコント ローラーに任意のデータベース ロジックが含まれていないことに注意してください。 リポジトリ層を作成するには、懸念事項の明確な分離を維持することができます。 コント ローラーは、アプリケーションのフロー制御ロジックとリポジトリがデータ アクセス ロジックを担当します。

**Listing 2 - Controllers\ProductController.vb**

[!code-vb[Main](validating-with-a-service-layer-vb/samples/sample2.vb)]

## <a name="creating-a-service-layer"></a>サービス層の作成

そのため、アプリケーションのフロー制御ロジックはコント ローラーが属しているし、データ アクセス ロジックをリポジトリに属しています。 その場合は、コピー先の場所は、検証ロジックでしょうか。 1 つのオプションは、検証ロジックを配置する、*サービス層*します。

サービス層は、コント ローラーおよびリポジトリ層間の通信を仲介する ASP.NET MVC アプリケーションで追加のレイヤーです。 サービス層には、ビジネス ロジックが含まれています。 具体的には、検証ロジックが含まれています。

たとえば、リスト 3 の製品のサービス層では、CreateProduct() メソッドがあります。 CreateProduct() メソッドは、製品を製品リポジトリに渡す前に、新しい製品を検証する ValidateProduct() メソッドを呼び出します。

**3 - Models\ProductService.vb を一覧表示します。**

[!code-vb[Main](validating-with-a-service-layer-vb/samples/sample3.vb)]

製品のコント ローラーはリポジトリ層ではなく、サービス層を使用するリスト 4 に更新されました。 コント ローラーのレイヤーは、サービス層を説明します。 サービス層がリポジトリ層に説明します。 各レイヤーでは、個別の責任を持ちます。

**Listing 4 - Controllers\ProductController.vb**

[!code-vb[Main](validating-with-a-service-layer-vb/samples/sample4.vb)]

製品のコント ローラー コンス トラクターで、製品のサービスが作成されたことに注意してください。 製品サービスの作成時に、モデル状態ディクショナリがサービスに渡されます。 製品のサービスでは、モデルの状態を使用して、コント ローラーに検証エラー メッセージを渡します。

## <a name="decoupling-the-service-layer"></a>サービス層を分離

コント ローラーと 1 つの点では、サービス層を分離することができませんでした。 コント ローラーとサービス レイヤーは、モデルの状態を通信します。 つまり、サービス層、ASP.NET MVC framework の特定の機能に依存しています。

可能な限り、コント ローラー レイヤーからサービス層を分離します。 理論上は、任意の種類のアプリケーションと ASP.NET MVC アプリケーションだけでなく、サービス層を使用することができなければなりません。 たとえば、今後、する可能性が、WPF アプリケーションのフロント エンドを構築します。 ASP.NET MVC 依存関係を削除する方法モデルの状態は、サービス層から見つかったする必要があります。

リスト 5 では、サービス層を不要になったモデルの状態を使用するように更新されましたが。 代わりに、IValidationDictionary インターフェイスを実装する任意のクラスを使用します。

**5 - Models\ProductService.vb (分離) を一覧表示します。**

[!code-vb[Main](validating-with-a-service-layer-vb/samples/sample5.vb)]

IValidationDictionary インターフェイスは、リスト 6 で定義されます。 このシンプルなインターフェイスは、1 つのメソッドと 1 つのプロパティを持ちます。

**6 - Models\IValidationDictionary.cs を一覧表示します。**

[!code-vb[Main](validating-with-a-service-layer-vb/samples/sample6.vb)]

リスト 7、ModelStateWrapper クラスという名前でクラスでは、IValidationDictionary インターフェイスを実装します。 モデル状態ディクショナリをコンス トラクターに渡すことによって、ModelStateWrapper クラスをインスタンス化できます。

**Listing 7 - Models\ModelStateWrapper.vb**

[!code-vb[Main](validating-with-a-service-layer-vb/samples/sample7.vb)]

最後に、一覧の 8 に更新されたコント ローラーは、そのコンス トラクターで、サービス層を作成するときに、ModelStateWrapper を使用します。

**Listing 8 - Controllers\ProductController.vb**

[!code-vb[Main](validating-with-a-service-layer-vb/samples/sample8.vb)]

IValidationDictionary を使用してインターフェイスと ModelStateWrapper クラスにより、コント ローラーの層から、サービス層を完全に分離できます。 サービス層がモデル状態に依存するではなくなりました。 サービス層に IValidationDictionary インターフェイスを実装する任意のクラスを渡すことができます。 たとえば、WPF アプリケーションは、単純なコレクション クラスを使用して IValidationDictionary インターフェイスを実装する場合があります。

## <a name="summary"></a>まとめ

このチュートリアルの目的は、ASP.NET MVC アプリケーションで検証を実行するための 1 つのアプローチについて説明することでした。 このチュートリアルでは、すべてのコント ローラーから、別のサービス層に、検証ロジックを移動する方法について説明しました。 ModelStateWrapper クラスを作成して、コント ローラーの層から、サービス層を分離する方法も学習しました。

> [!div class="step-by-step"]
> [前へ](validating-with-the-idataerrorinfo-interface-vb.md)
> [次へ](validation-with-the-data-annotation-validators-vb.md)
