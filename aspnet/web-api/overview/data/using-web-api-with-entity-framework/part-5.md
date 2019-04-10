---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-5
title: データ転送オブジェクト (Dto) の作成 |Microsoft Docs
author: MikeWasson
description: ''
ms.author: riande
ms.date: 06/16/2014
ms.assetid: 0fd07176-b74b-48f0-9fac-0f02e3ffa213
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-5
msc.type: authoredcontent
ms.openlocfilehash: 1af29955e8040c34840d4c77fc2006f59d2324dd
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59395278"
---
# <a name="create-data-transfer-objects-dtos"></a><span data-ttu-id="aa6f9-102">データ転送オブジェクト (DTO) の作成</span><span class="sxs-lookup"><span data-stu-id="aa6f9-102">Create Data Transfer Objects (DTOs)</span></span>

<span data-ttu-id="aa6f9-103">作成者[Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="aa6f9-103">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

[<span data-ttu-id="aa6f9-104">完成したプロジェクトをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="aa6f9-104">Download Completed Project</span></span>](https://github.com/MikeWasson/BookService)

<span data-ttu-id="aa6f9-105">権利、web API、クライアントにデータベースのエンティティを公開します。</span><span class="sxs-lookup"><span data-stu-id="aa6f9-105">Right now, our web API exposes the database entities to the client.</span></span> <span data-ttu-id="aa6f9-106">クライアントは、データベース テーブルに直接マップされるデータを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="aa6f9-106">The client receives data that maps directly to your database tables.</span></span> <span data-ttu-id="aa6f9-107">ただし、ない常にお勧めします。</span><span class="sxs-lookup"><span data-stu-id="aa6f9-107">However, that's not always a good idea.</span></span> <span data-ttu-id="aa6f9-108">クライアントに送信するデータの形状を変更することがあります。</span><span class="sxs-lookup"><span data-stu-id="aa6f9-108">Sometimes you want to change the shape of the data that you send to client.</span></span> <span data-ttu-id="aa6f9-109">たとえば、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="aa6f9-109">For example, you might want to:</span></span>

- <span data-ttu-id="aa6f9-110">(前のセクションを参照してください)、循環参照を削除します。</span><span class="sxs-lookup"><span data-stu-id="aa6f9-110">Remove circular references (see previous section).</span></span>
- <span data-ttu-id="aa6f9-111">表示するのには、クライアントは想定されていない特定のプロパティを非表示にします。</span><span class="sxs-lookup"><span data-stu-id="aa6f9-111">Hide particular properties that clients are not supposed to view.</span></span>
- <span data-ttu-id="aa6f9-112">ペイロードのサイズを軽減するために、いくつかのプロパティを省略します。</span><span class="sxs-lookup"><span data-stu-id="aa6f9-112">Omit some properties in order to reduce payload size.</span></span>
- <span data-ttu-id="aa6f9-113">クライアントの方が便利にする、入れ子になったオブジェクトを含むオブジェクト グラフを平坦化します。</span><span class="sxs-lookup"><span data-stu-id="aa6f9-113">Flatten object graphs that contain nested objects, to make them more convenient for clients.</span></span>
- <span data-ttu-id="aa6f9-114">「オーバーポスティング」脆弱性を回避します。</span><span class="sxs-lookup"><span data-stu-id="aa6f9-114">Avoid "over-posting" vulnerabilities.</span></span> <span data-ttu-id="aa6f9-115">(を参照してください[モデルの検証](../../formats-and-model-binding/model-validation-in-aspnet-web-api.md)オーバーポスティング攻撃の詳細についてはします)。</span><span class="sxs-lookup"><span data-stu-id="aa6f9-115">(See [Model Validation](../../formats-and-model-binding/model-validation-in-aspnet-web-api.md) for a discussion of over-posting.)</span></span>
- <span data-ttu-id="aa6f9-116">データベース層から、サービス層を分離します。</span><span class="sxs-lookup"><span data-stu-id="aa6f9-116">Decouple your service layer from your database layer.</span></span>

<span data-ttu-id="aa6f9-117">定義することがこれを実現する、*データ転送オブジェクト*(DTO)。</span><span class="sxs-lookup"><span data-stu-id="aa6f9-117">To accomplish this, you can define a *data transfer object* (DTO).</span></span> <span data-ttu-id="aa6f9-118">DTO は、ネットワーク経由でデータを送信する方法を定義するオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="aa6f9-118">A DTO is an object that defines how the data will be sent over the network.</span></span> <span data-ttu-id="aa6f9-119">書籍エンティティとそのしくみを見てみましょう。</span><span class="sxs-lookup"><span data-stu-id="aa6f9-119">Let's see how that works with the Book entity.</span></span> <span data-ttu-id="aa6f9-120">Models フォルダーでは、2 つの DTO クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="aa6f9-120">In the Models folder, add two DTO classes:</span></span>

[!code-csharp[Main](part-5/samples/sample1.cs)]

<span data-ttu-id="aa6f9-121">`BookDetailDTO`クラスには、すべての点を除いて、書籍モデルのプロパティが含まれています`AuthorName`作成者の名前を保持する文字列です。</span><span class="sxs-lookup"><span data-stu-id="aa6f9-121">The `BookDetailDTO` class includes all of the properties from the Book model, except that `AuthorName` is a string that will hold the author name.</span></span> <span data-ttu-id="aa6f9-122">`BookDTO`クラスにはからプロパティのサブセットが含まれています`BookDetailDTO`します。</span><span class="sxs-lookup"><span data-stu-id="aa6f9-122">The `BookDTO` class contains a subset of properties from `BookDetailDTO`.</span></span>

<span data-ttu-id="aa6f9-123">2 つの GET メソッドを次に、置換、 `BooksController` Dto を返すバージョンのクラス。</span><span class="sxs-lookup"><span data-stu-id="aa6f9-123">Next, replace the two GET methods in the `BooksController` class, with versions that return DTOs.</span></span> <span data-ttu-id="aa6f9-124">LINQ を使用します**選択**書籍エンティティから Dto に変換するステートメント。</span><span class="sxs-lookup"><span data-stu-id="aa6f9-124">We'll use the LINQ **Select** statement to convert from Book entities into DTOs.</span></span>

[!code-csharp[Main](part-5/samples/sample2.cs)]

<span data-ttu-id="aa6f9-125">ここで、新しいによって生成された SQL は、`GetBooks`メソッド。</span><span class="sxs-lookup"><span data-stu-id="aa6f9-125">Here is the SQL generated by the new `GetBooks` method.</span></span> <span data-ttu-id="aa6f9-126">EF が、LINQ を変換することを確認できます**選択**SQL SELECT ステートメントにします。</span><span class="sxs-lookup"><span data-stu-id="aa6f9-126">You can see that EF translates the LINQ **Select** into a SQL SELECT statement.</span></span>

[!code-sql[Main](part-5/samples/sample3.sql)]

<span data-ttu-id="aa6f9-127">最後に、変更、 `PostBook` DTO を返すメソッド。</span><span class="sxs-lookup"><span data-stu-id="aa6f9-127">Finally, modify the `PostBook` method to return a DTO.</span></span>

[!code-csharp[Main](part-5/samples/sample4.cs)]

> [!NOTE]
> <span data-ttu-id="aa6f9-128">このチュートリアルで変換しています Dto を手動でコード。</span><span class="sxs-lookup"><span data-stu-id="aa6f9-128">In this tutorial, we're converting to DTOs manually in code.</span></span> <span data-ttu-id="aa6f9-129">ようなライブラリを使用することも[AutoMapper](http://automapper.org/)変換を自動的に処理します。</span><span class="sxs-lookup"><span data-stu-id="aa6f9-129">Another option is to use a library like [AutoMapper](http://automapper.org/) that handles the conversion automatically.</span></span>
> 
> [!div class="step-by-step"]
> <span data-ttu-id="aa6f9-130">[前へ](part-4.md)
> [次へ](part-6.md)</span><span class="sxs-lookup"><span data-stu-id="aa6f9-130">[Previous](part-4.md)
[Next](part-6.md)</span></span>
