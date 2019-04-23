---
uid: web-api/overview/getting-started-with-aspnet-web-api/action-results
title: 操作の結果が Web API 2 - ASP.NET 4.x
author: MikeWasson
description: ASP.NET Web API に ASP.NET の HTTP 応答メッセージにコント ローラー アクションからの戻り値を変換する方法について説明します。 4.x です。
ms.author: riande
ms.date: 02/03/2014
ms.custom: seoapril2019
ms.assetid: 2fc4797c-38ef-4cc7-926c-ca431c4739e8
msc.legacyurl: /web-api/overview/getting-started-with-aspnet-web-api/action-results
msc.type: authoredcontent
ms.openlocfilehash: 87f71938a5c5f38d3a456ba9339540f67e236e1a
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59400894"
---
# <a name="action-results-in-web-api-2"></a><span data-ttu-id="334c0-103">Web API 2 のアクションの結果</span><span class="sxs-lookup"><span data-stu-id="334c0-103">Action Results in Web API 2</span></span>

<span data-ttu-id="334c0-104">作成者[Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="334c0-104">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

<span data-ttu-id="334c0-105">このトピックでは、ASP.NET Web API に HTTP 応答メッセージにコント ローラー アクションからの戻り値を変換する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="334c0-105">This topic describes how ASP.NET Web API converts the return value from a controller action into an HTTP response message.</span></span>

<span data-ttu-id="334c0-106">Web API コント ローラーのアクションは、次のいずれかを返すことができます。</span><span class="sxs-lookup"><span data-stu-id="334c0-106">A Web API controller action can return any of the following:</span></span>

1. <span data-ttu-id="334c0-107">void</span><span class="sxs-lookup"><span data-stu-id="334c0-107">void</span></span>
2. <span data-ttu-id="334c0-108">**HttpResponseMessage**</span><span class="sxs-lookup"><span data-stu-id="334c0-108">**HttpResponseMessage**</span></span>
3. <span data-ttu-id="334c0-109">**IHttpActionResult**</span><span class="sxs-lookup"><span data-stu-id="334c0-109">**IHttpActionResult**</span></span>
4. <span data-ttu-id="334c0-110">その他の種類</span><span class="sxs-lookup"><span data-stu-id="334c0-110">Some other type</span></span>

<span data-ttu-id="334c0-111">これらのいずれかによって返される、Web API は、別のメカニズムを使用して HTTP 応答を作成します。</span><span class="sxs-lookup"><span data-stu-id="334c0-111">Depending on which of these is returned, Web API uses a different mechanism to create the HTTP response.</span></span>

| <span data-ttu-id="334c0-112">戻り値の型</span><span class="sxs-lookup"><span data-stu-id="334c0-112">Return type</span></span> | <span data-ttu-id="334c0-113">Web API が応答を作成する方法</span><span class="sxs-lookup"><span data-stu-id="334c0-113">How Web API creates the response</span></span> |
| --- | --- |
| <span data-ttu-id="334c0-114">void</span><span class="sxs-lookup"><span data-stu-id="334c0-114">void</span></span> | <span data-ttu-id="334c0-115">空の 204 (No Content) を返す</span><span class="sxs-lookup"><span data-stu-id="334c0-115">Return empty 204 (No Content)</span></span> |
| <span data-ttu-id="334c0-116">**HttpResponseMessage**</span><span class="sxs-lookup"><span data-stu-id="334c0-116">**HttpResponseMessage**</span></span> | <span data-ttu-id="334c0-117">HTTP 応答メッセージに直接変換します。</span><span class="sxs-lookup"><span data-stu-id="334c0-117">Convert directly to an HTTP response message.</span></span> |
| <span data-ttu-id="334c0-118">**IHttpActionResult**</span><span class="sxs-lookup"><span data-stu-id="334c0-118">**IHttpActionResult**</span></span> | <span data-ttu-id="334c0-119">呼び出す**ExecuteAsync**を作成する、 **HttpResponseMessage**、HTTP 応答メッセージに変換します。</span><span class="sxs-lookup"><span data-stu-id="334c0-119">Call **ExecuteAsync** to create an **HttpResponseMessage**, then convert to an HTTP response message.</span></span> |
| <span data-ttu-id="334c0-120">その他の種類</span><span class="sxs-lookup"><span data-stu-id="334c0-120">Other type</span></span> | <span data-ttu-id="334c0-121">応答の本体にシリアル化された戻り値を書き込む200 (OK) を返します。</span><span class="sxs-lookup"><span data-stu-id="334c0-121">Write the serialized return value into the response body; return 200 (OK).</span></span> |

<span data-ttu-id="334c0-122">このトピックの残りの部分では、各オプションの詳細について説明します。</span><span class="sxs-lookup"><span data-stu-id="334c0-122">The rest of this topic describes each option in more detail.</span></span>

## <a name="void"></a><span data-ttu-id="334c0-123">void</span><span class="sxs-lookup"><span data-stu-id="334c0-123">void</span></span>

<span data-ttu-id="334c0-124">戻り値の型がある場合`void`、Web API は、単に、空の HTTP 応答状態コード 204 (No Content) を返します。</span><span class="sxs-lookup"><span data-stu-id="334c0-124">If the return type is `void`, Web API simply returns an empty HTTP response with status code 204 (No Content).</span></span>

<span data-ttu-id="334c0-125">例のコント ローラー:</span><span class="sxs-lookup"><span data-stu-id="334c0-125">Example controller:</span></span>

[!code-csharp[Main](action-results/samples/sample1.cs)]

<span data-ttu-id="334c0-126">HTTP 応答:</span><span class="sxs-lookup"><span data-stu-id="334c0-126">HTTP response:</span></span>

[!code-console[Main](action-results/samples/sample2.cmd)]

## <a name="httpresponsemessage"></a><span data-ttu-id="334c0-127">HttpResponseMessage</span><span class="sxs-lookup"><span data-stu-id="334c0-127">HttpResponseMessage</span></span>

<span data-ttu-id="334c0-128">アクションが返された場合、 [HttpResponseMessage](https://msdn.microsoft.com/library/system.net.http.httpresponsemessage.aspx)、Web API からのプロパティを使用して、HTTP 応答メッセージに直接戻り値が変換、 **HttpResponseMessage**を設定するオブジェクト、応答です。</span><span class="sxs-lookup"><span data-stu-id="334c0-128">If the action returns an [HttpResponseMessage](https://msdn.microsoft.com/library/system.net.http.httpresponsemessage.aspx), Web API converts the return value directly into an HTTP response message, using the properties of the **HttpResponseMessage** object to populate the response.</span></span>

<span data-ttu-id="334c0-129">このオプションでは、多数の応答メッセージを制御できます。</span><span class="sxs-lookup"><span data-stu-id="334c0-129">This option gives you a lot of control over the response message.</span></span> <span data-ttu-id="334c0-130">たとえば、次のコント ローラー アクションは、Cache-control ヘッダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="334c0-130">For example, the following controller action sets the Cache-Control header.</span></span>

[!code-csharp[Main](action-results/samples/sample3.cs)]

<span data-ttu-id="334c0-131">応答 :</span><span class="sxs-lookup"><span data-stu-id="334c0-131">Response:</span></span>

[!code-console[Main](action-results/samples/sample4.cmd?highlight=2)]

<span data-ttu-id="334c0-132">ドメイン モデルを渡す場合、 **CreateResponse**メソッドは、Web API では、[メディア フォーマッタ](../formats-and-model-binding/media-formatters.md)応答本文にシリアル化されたモデルを記述します。</span><span class="sxs-lookup"><span data-stu-id="334c0-132">If you pass a domain model to the **CreateResponse** method, Web API uses a [media formatter](../formats-and-model-binding/media-formatters.md) to write the serialized model into the response body.</span></span>

[!code-csharp[Main](action-results/samples/sample5.cs)]

<span data-ttu-id="334c0-133">Web API では、要求の Accept ヘッダーを使用して、フォーマッタを選択します。</span><span class="sxs-lookup"><span data-stu-id="334c0-133">Web API uses the Accept header in the request to choose the formatter.</span></span> <span data-ttu-id="334c0-134">詳細については、次を参照してください。[コンテンツ ネゴシエーション](../formats-and-model-binding/content-negotiation.md)します。</span><span class="sxs-lookup"><span data-stu-id="334c0-134">For more information, see [Content Negotiation](../formats-and-model-binding/content-negotiation.md).</span></span>

## <a name="ihttpactionresult"></a><span data-ttu-id="334c0-135">IHttpActionResult</span><span class="sxs-lookup"><span data-stu-id="334c0-135">IHttpActionResult</span></span>

<span data-ttu-id="334c0-136">**IHttpActionResult**インターフェイスが Web API 2 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="334c0-136">The **IHttpActionResult** interface was introduced in Web API 2.</span></span> <span data-ttu-id="334c0-137">基本的には、定義、 **HttpResponseMessage**ファクトリ。</span><span class="sxs-lookup"><span data-stu-id="334c0-137">Essentially, it defines an **HttpResponseMessage** factory.</span></span> <span data-ttu-id="334c0-138">使用しての利点は次のとおり、 **IHttpActionResult**インターフェイス。</span><span class="sxs-lookup"><span data-stu-id="334c0-138">Here are some advantages of using the **IHttpActionResult** interface:</span></span>

- <span data-ttu-id="334c0-139">簡略化[単体テスト](../testing-and-debugging/unit-testing-controllers-in-web-api.md)コント ローラー。</span><span class="sxs-lookup"><span data-stu-id="334c0-139">Simplifies [unit testing](../testing-and-debugging/unit-testing-controllers-in-web-api.md) your controllers.</span></span>
- <span data-ttu-id="334c0-140">別のクラスの HTTP 応答を作成するための一般的なロジックを移動します。</span><span class="sxs-lookup"><span data-stu-id="334c0-140">Moves common logic for creating HTTP responses into separate classes.</span></span>
- <span data-ttu-id="334c0-141">わかりやすく、コント ローラー アクションの目的は、応答を構築する低レベルの詳細を非表示にしています。</span><span class="sxs-lookup"><span data-stu-id="334c0-141">Makes the intent of the controller action clearer, by hiding the low-level details of constructing the response.</span></span>

<span data-ttu-id="334c0-142">**IHttpActionResult**含まれる 1 つのメソッドは**ExecuteAsync**、非同期的に作成し、 **HttpResponseMessage**インスタンス。</span><span class="sxs-lookup"><span data-stu-id="334c0-142">**IHttpActionResult** contains a single method, **ExecuteAsync**, which asynchronously creates an **HttpResponseMessage** instance.</span></span>

[!code-csharp[Main](action-results/samples/sample6.cs)]

<span data-ttu-id="334c0-143">コント ローラーのアクションが返された場合、 **IHttpActionResult**、Web API 呼び出し、 **ExecuteAsync**を作成する方法、 **HttpResponseMessage**します。</span><span class="sxs-lookup"><span data-stu-id="334c0-143">If a controller action returns an **IHttpActionResult**, Web API calls the **ExecuteAsync** method to create an **HttpResponseMessage**.</span></span> <span data-ttu-id="334c0-144">変換し、 **HttpResponseMessage**を HTTP 応答メッセージにします。</span><span class="sxs-lookup"><span data-stu-id="334c0-144">Then it converts the **HttpResponseMessage** into an HTTP response message.</span></span>

<span data-ttu-id="334c0-145">単純な実装を次に示します**IHttpActionResult**プレーン テキストの応答を作成します。</span><span class="sxs-lookup"><span data-stu-id="334c0-145">Here is a simple implementation of **IHttpActionResult** that creates a plain text response:</span></span>

[!code-csharp[Main](action-results/samples/sample7.cs)]

<span data-ttu-id="334c0-146">コント ローラー アクションの例:</span><span class="sxs-lookup"><span data-stu-id="334c0-146">Example controller action:</span></span>

[!code-csharp[Main](action-results/samples/sample8.cs)]

<span data-ttu-id="334c0-147">応答 :</span><span class="sxs-lookup"><span data-stu-id="334c0-147">Response:</span></span>

[!code-console[Main](action-results/samples/sample9.cmd)]

<span data-ttu-id="334c0-148">多くの場合、使用する、 **IHttpActionResult**実装で定義されている、 **[System.Web.Http.Results](https://msdn.microsoft.com/library/system.web.http.results.aspx)** 名前空間。</span><span class="sxs-lookup"><span data-stu-id="334c0-148">More often, you will use the **IHttpActionResult** implementations defined in the **[System.Web.Http.Results](https://msdn.microsoft.com/library/system.web.http.results.aspx)** namespace.</span></span> <span data-ttu-id="334c0-149">**ApiController**クラスは、これらの組み込みのアクション結果を返すヘルパー メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="334c0-149">The **ApiController** class defines helper methods that return these built-in action results.</span></span>

<span data-ttu-id="334c0-150">次の例では、要求が既存の製品 ID と一致しない場合、コント ローラーを呼び出す[ApiController.NotFound](https://msdn.microsoft.com/library/system.web.http.apicontroller.notfound.aspx) 404 (Not Found) 応答を作成します。</span><span class="sxs-lookup"><span data-stu-id="334c0-150">In the following example, if the request does not match an existing product ID, the controller calls [ApiController.NotFound](https://msdn.microsoft.com/library/system.web.http.apicontroller.notfound.aspx) to create a 404 (Not Found) response.</span></span> <span data-ttu-id="334c0-151">それ以外の場合、コント ローラーを呼び出す[ApiController.OK](https://msdn.microsoft.com/library/dn314591.aspx)、200 (OK) 応答を作成する、製品が含まれています。</span><span class="sxs-lookup"><span data-stu-id="334c0-151">Otherwise, the controller calls [ApiController.OK](https://msdn.microsoft.com/library/dn314591.aspx), which creates a 200 (OK) response that contains the product.</span></span>

[!code-csharp[Main](action-results/samples/sample10.cs)]

## <a name="other-return-types"></a><span data-ttu-id="334c0-152">その他の戻り値の型</span><span class="sxs-lookup"><span data-stu-id="334c0-152">Other Return Types</span></span>

<span data-ttu-id="334c0-153">その他のすべての戻り値型の Web API を使用して、[メディア フォーマッタ](../formats-and-model-binding/media-formatters.md)戻り値をシリアル化します。</span><span class="sxs-lookup"><span data-stu-id="334c0-153">For all other return types, Web API uses a [media formatter](../formats-and-model-binding/media-formatters.md) to serialize the return value.</span></span> <span data-ttu-id="334c0-154">Web API は、応答本文にシリアル化された値を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="334c0-154">Web API writes the serialized value into the response body.</span></span> <span data-ttu-id="334c0-155">応答ステータス コードは、200 (OK) です。</span><span class="sxs-lookup"><span data-stu-id="334c0-155">The response status code is 200 (OK).</span></span>

[!code-csharp[Main](action-results/samples/sample11.cs)]

<span data-ttu-id="334c0-156">この方法の欠点は、404 など、エラー コードを直接返すことはできません。</span><span class="sxs-lookup"><span data-stu-id="334c0-156">A disadvantage of this approach is that you cannot directly return an error code, such as 404.</span></span> <span data-ttu-id="334c0-157">ただし、スロー、 **HttpResponseException**エラー コード。</span><span class="sxs-lookup"><span data-stu-id="334c0-157">However, you can throw an **HttpResponseException** for error codes.</span></span> <span data-ttu-id="334c0-158">詳細については、次を参照してください。 [ASP.NET Web API での例外処理](../error-handling/exception-handling.md)します。</span><span class="sxs-lookup"><span data-stu-id="334c0-158">For more information, see [Exception Handling in ASP.NET Web API](../error-handling/exception-handling.md).</span></span>

<span data-ttu-id="334c0-159">Web API では、要求の Accept ヘッダーを使用して、フォーマッタを選択します。</span><span class="sxs-lookup"><span data-stu-id="334c0-159">Web API uses the Accept header in the request to choose the formatter.</span></span> <span data-ttu-id="334c0-160">詳細については、次を参照してください。[コンテンツ ネゴシエーション](../formats-and-model-binding/content-negotiation.md)します。</span><span class="sxs-lookup"><span data-stu-id="334c0-160">For more information, see [Content Negotiation](../formats-and-model-binding/content-negotiation.md).</span></span>

<span data-ttu-id="334c0-161">要求の例</span><span class="sxs-lookup"><span data-stu-id="334c0-161">Example request</span></span>

[!code-console[Main](action-results/samples/sample12.cmd)]

<span data-ttu-id="334c0-162">応答の例:</span><span class="sxs-lookup"><span data-stu-id="334c0-162">Example response:</span></span>

[!code-console[Main](action-results/samples/sample13.cmd)]
