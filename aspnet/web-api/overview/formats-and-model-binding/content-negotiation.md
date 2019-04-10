---
uid: web-api/overview/formats-and-model-binding/content-negotiation
title: コンテンツ ネゴシエーションでは、ASP.NET Web API - ASP.NET 4.x
author: MikeWasson
description: ASP.NET Web API が ASP.NET の HTTP コンテンツ ネゴシエーションを実装する方法について説明します。 4.x です。
ms.author: riande
ms.date: 05/20/2012
ms.custom: seoapril2019
ms.assetid: 0dd51b30-bf5a-419f-a1b7-2817ccca3c7d
msc.legacyurl: /web-api/overview/formats-and-model-binding/content-negotiation
msc.type: authoredcontent
ms.openlocfilehash: cb6668ff6de276d3778ce11f27ce597d8bf1f9c7
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59380161"
---
# <a name="content-negotiation-in-aspnet-web-api"></a><span data-ttu-id="f0beb-103">ASP.NET Web API でコンテンツ ネゴシエーション</span><span class="sxs-lookup"><span data-stu-id="f0beb-103">Content Negotiation in ASP.NET Web API</span></span>

<span data-ttu-id="f0beb-104">作成者[Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="f0beb-104">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

<span data-ttu-id="f0beb-105">この記事では、ASP.NET Web API での ASP.NET コンテンツ ネゴシエーションの実装方法について説明します 4.x です。</span><span class="sxs-lookup"><span data-stu-id="f0beb-105">This article describes how ASP.NET Web API implements content negotiation for ASP.NET 4.x.</span></span>

<span data-ttu-id="f0beb-106">HTTP の仕様 (RFC 2616) は、「複数の表現が使用できる場合に、指定された応答の最適な表現を選択した場合のプロセスです。」とコンテンツ ネゴシエーションを定義します。</span><span class="sxs-lookup"><span data-stu-id="f0beb-106">The HTTP specification (RFC 2616) defines content negotiation as "the process of selecting the best representation for a given response when there are multiple representations available."</span></span> <span data-ttu-id="f0beb-107">Http コンテンツ ネゴシエーションを主要なメカニズムは、これらの要求ヘッダーです。</span><span class="sxs-lookup"><span data-stu-id="f0beb-107">The primary mechanism for content negotiation in HTTP are these request headers:</span></span>

- <span data-ttu-id="f0beb-108">**使用できます。** どのメディアの種類が"application/json に"など、応答として受け入れ可能な"application/xml、"またはカスタムのメディアの種類など、 &quot;application/vnd.example+xml&quot;</span><span class="sxs-lookup"><span data-stu-id="f0beb-108">**Accept:** Which media types are acceptable for the response, such as "application/json," "application/xml," or a custom media type such as &quot;application/vnd.example+xml&quot;</span></span>
- <span data-ttu-id="f0beb-109">**受け入れる-文字セット:** 文字セットは許容できますが、utf-8 または ISO 8859-1 などです。</span><span class="sxs-lookup"><span data-stu-id="f0beb-109">**Accept-Charset:** Which character sets are acceptable, such as UTF-8 or ISO 8859-1.</span></span>
- <span data-ttu-id="f0beb-110">**Accept-encoding:** コンテンツのエンコード方式は、gzip など、許容されます。</span><span class="sxs-lookup"><span data-stu-id="f0beb-110">**Accept-Encoding:** Which content encodings are acceptable, such as gzip.</span></span>
- <span data-ttu-id="f0beb-111">**Accept Language:**、使用する自然言語など"英語-米国"。</span><span class="sxs-lookup"><span data-stu-id="f0beb-111">**Accept-Language:** The preferred natural language, such as "en-us".</span></span>

<span data-ttu-id="f0beb-112">サーバーは、HTTP 要求の他の部分でも確認できます。</span><span class="sxs-lookup"><span data-stu-id="f0beb-112">The server can also look at other portions of the HTTP request.</span></span> <span data-ttu-id="f0beb-113">たとえば、要求に X-要求-でヘッダーが含まれている場合、AJAX 要求を示すサーバー可能性があります既定値を JSON に Accept ヘッダーがない場合。</span><span class="sxs-lookup"><span data-stu-id="f0beb-113">For example, if the request contains an X-Requested-With header, indicating an AJAX request, the server might default to JSON if there is no Accept header.</span></span>

<span data-ttu-id="f0beb-114">この記事では、Web API で Accept、Accept-charset ヘッダーを使用する方法を紹介します。</span><span class="sxs-lookup"><span data-stu-id="f0beb-114">In this article, we'll look at how Web API uses the Accept and Accept-Charset headers.</span></span> <span data-ttu-id="f0beb-115">(この時点ではありません Accept-encoding、Accept-language の組み込みサポート。)</span><span class="sxs-lookup"><span data-stu-id="f0beb-115">(At this time, there is no built-in support for Accept-Encoding or Accept-Language.)</span></span>

## <a name="serialization"></a><span data-ttu-id="f0beb-116">シリアル化</span><span class="sxs-lookup"><span data-stu-id="f0beb-116">Serialization</span></span>

<span data-ttu-id="f0beb-117">Web API コント ローラーには、CLR 型としてのリソースが返された場合、パイプラインは、戻り値をシリアル化し、HTTP 応答の本文に書き込みます。</span><span class="sxs-lookup"><span data-stu-id="f0beb-117">If a Web API controller returns a resource as CLR type, the pipeline serializes the return value and writes it into the HTTP response body.</span></span>

<span data-ttu-id="f0beb-118">たとえば、次のコント ローラー アクションがあるとします。</span><span class="sxs-lookup"><span data-stu-id="f0beb-118">For example, consider the following controller action:</span></span>

[!code-csharp[Main](content-negotiation/samples/sample1.cs)]

<span data-ttu-id="f0beb-119">クライアントでは、この HTTP 要求を送信可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f0beb-119">A client might send this HTTP request:</span></span>

[!code-console[Main](content-negotiation/samples/sample2.cmd)]

<span data-ttu-id="f0beb-120">応答では、サーバーが送信可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f0beb-120">In response, the server might send:</span></span>

[!code-console[Main](content-negotiation/samples/sample3.cmd)]

<span data-ttu-id="f0beb-121">JSON、Javascript、またはその「何も」この例では、クライアントが要求した (\*/\*)。</span><span class="sxs-lookup"><span data-stu-id="f0beb-121">In this example, the client requested either JSON, Javascript, or "anything" (\*/\*).</span></span> <span data-ttu-id="f0beb-122">サーバー応答の JSON 表現しました、`Product`オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="f0beb-122">The server responded with a JSON representation of the `Product` object.</span></span> <span data-ttu-id="f0beb-123">通知に応答の Content-type ヘッダーが設定されている&quot;、application/json&quot;します。</span><span class="sxs-lookup"><span data-stu-id="f0beb-123">Notice that the Content-Type header in the response is set to &quot;application/json&quot;.</span></span>

<span data-ttu-id="f0beb-124">コント ローラーを返すことも、 **HttpResponseMessage**オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="f0beb-124">A controller can also return an **HttpResponseMessage** object.</span></span> <span data-ttu-id="f0beb-125">応答本文の CLR オブジェクトを指定するには、呼び出し、 **CreateResponse**拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="f0beb-125">To specify a CLR object for the response body, call the **CreateResponse** extension method:</span></span>

[!code-csharp[Main](content-negotiation/samples/sample4.cs)]

<span data-ttu-id="f0beb-126">このオプションでは、応答の詳細についてより詳細に制御をできます。</span><span class="sxs-lookup"><span data-stu-id="f0beb-126">This option gives you more control over the details of the response.</span></span> <span data-ttu-id="f0beb-127">ステータス コードを設定し、HTTP のヘッダーを追加するなどできます。</span><span class="sxs-lookup"><span data-stu-id="f0beb-127">You can set the status code, add HTTP headers, and so forth.</span></span>

<span data-ttu-id="f0beb-128">リソースをシリアル化するオブジェクトが呼び出されると、*メディア フォーマッタ*します。</span><span class="sxs-lookup"><span data-stu-id="f0beb-128">The object that serializes the resource is called a *media formatter*.</span></span> <span data-ttu-id="f0beb-129">派生してメディア フォーマッタ、 **MediaTypeFormatter**クラス。</span><span class="sxs-lookup"><span data-stu-id="f0beb-129">Media formatters derive from the **MediaTypeFormatter** class.</span></span> <span data-ttu-id="f0beb-130">Web API は、XML および JSON 用メディア フォーマッタを提供し、その他のメディアの種類をサポートするために、カスタム フォーマッタを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="f0beb-130">Web API provides media formatters for XML and JSON, and you can create custom formatters to support other media types.</span></span> <span data-ttu-id="f0beb-131">カスタム フォーマッタを作成する方法の詳細については、次を参照してください。[メディア フォーマッタ](media-formatters.md)します。</span><span class="sxs-lookup"><span data-stu-id="f0beb-131">For information about writing a custom formatter, see [Media Formatters](media-formatters.md).</span></span>

## <a name="how-content-negotiation-works"></a><span data-ttu-id="f0beb-132">コンテンツ ネゴシエーションのしくみ</span><span class="sxs-lookup"><span data-stu-id="f0beb-132">How Content Negotiation Works</span></span>

<span data-ttu-id="f0beb-133">最初に、パイプラインの取得、 **IContentNegotiator**からサービス、 **HttpConfiguration**オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="f0beb-133">First, the pipeline gets the **IContentNegotiator** service from the **HttpConfiguration** object.</span></span> <span data-ttu-id="f0beb-134">メディア フォーマッタの一覧を取得また、 **HttpConfiguration.Formatters**コレクション。</span><span class="sxs-lookup"><span data-stu-id="f0beb-134">It also gets the list of media formatters from the **HttpConfiguration.Formatters** collection.</span></span>

<span data-ttu-id="f0beb-135">次に、パイプラインを呼び出す**IContentNegotiator.Negotiate**で渡し。</span><span class="sxs-lookup"><span data-stu-id="f0beb-135">Next, the pipeline calls **IContentNegotiator.Negotiate**, passing in:</span></span>

- <span data-ttu-id="f0beb-136">シリアル化するオブジェクトの種類</span><span class="sxs-lookup"><span data-stu-id="f0beb-136">The type of object to serialize</span></span>
- <span data-ttu-id="f0beb-137">メディア フォーマッタのコレクション</span><span class="sxs-lookup"><span data-stu-id="f0beb-137">The collection of media formatters</span></span>
- <span data-ttu-id="f0beb-138">HTTP 要求</span><span class="sxs-lookup"><span data-stu-id="f0beb-138">The HTTP request</span></span>

<span data-ttu-id="f0beb-139">**ネゴシエート**メソッドは 2 つの情報を返します。</span><span class="sxs-lookup"><span data-stu-id="f0beb-139">The **Negotiate** method returns two pieces of information:</span></span>

- <span data-ttu-id="f0beb-140">どのフォーマッタを使用するには</span><span class="sxs-lookup"><span data-stu-id="f0beb-140">Which formatter to use</span></span>
- <span data-ttu-id="f0beb-141">応答のメディアの種類</span><span class="sxs-lookup"><span data-stu-id="f0beb-141">The media type for the response</span></span>

<span data-ttu-id="f0beb-142">フォーマッタが見つからない場合、**ネゴシエート**メソッドを返します。 **null**、し、クライアントは、406 (Not Acceptable) の HTTP エラーを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="f0beb-142">If no formatter is found, the **Negotiate** method returns **null**, and the client receives HTTP error 406 (Not Acceptable).</span></span>

<span data-ttu-id="f0beb-143">次のコードは、どのコント ローラーを直接呼び出すできますコンテンツ ネゴシエーションを示しています。</span><span class="sxs-lookup"><span data-stu-id="f0beb-143">The following code shows how a controller can directly invoke content negotiation:</span></span>

[!code-csharp[Main](content-negotiation/samples/sample5.cs)]

<span data-ttu-id="f0beb-144">このコードは、パイプラインを自動的には、何をします。</span><span class="sxs-lookup"><span data-stu-id="f0beb-144">This code is equivalent to the what the pipeline does automatically.</span></span>

## <a name="default-content-negotiator"></a><span data-ttu-id="f0beb-145">既定のコンテンツ ネゴシエーター</span><span class="sxs-lookup"><span data-stu-id="f0beb-145">Default Content Negotiator</span></span>

<span data-ttu-id="f0beb-146">**DefaultContentNegotiator**クラスの既定の実装を提供する**IContentNegotiator**します。</span><span class="sxs-lookup"><span data-stu-id="f0beb-146">The **DefaultContentNegotiator** class provides the default implementation of **IContentNegotiator**.</span></span> <span data-ttu-id="f0beb-147">いくつかの条件を使用して、フォーマッタを選択します。</span><span class="sxs-lookup"><span data-stu-id="f0beb-147">It uses several criteria to select a formatter.</span></span>

<span data-ttu-id="f0beb-148">最初に、フォーマッタは、型をシリアル化できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="f0beb-148">First, the formatter must be able to serialize the type.</span></span> <span data-ttu-id="f0beb-149">これは、呼び出すことによって確認が**MediaTypeFormatter.CanWriteType**します。</span><span class="sxs-lookup"><span data-stu-id="f0beb-149">This is verified by calling **MediaTypeFormatter.CanWriteType**.</span></span>

<span data-ttu-id="f0beb-150">次に、コンテンツ ネゴシエーターを使用して、各フォーマッタを調べ、HTTP 要求が一致する度合いを評価します。</span><span class="sxs-lookup"><span data-stu-id="f0beb-150">Next, the content negotiator looks at each formatter and evaluates how well it matches the HTTP request.</span></span> <span data-ttu-id="f0beb-151">一致を評価するには、コンテンツ ネゴシエーターを次に 2 つのことで、フォーマッタで</span><span class="sxs-lookup"><span data-stu-id="f0beb-151">To evaluate the match, the content negotiator looks at two things on the formatter:</span></span>

- <span data-ttu-id="f0beb-152">**SupportedMediaTypes**コレクションで、サポートされているメディアの種類の一覧が含まれています。</span><span class="sxs-lookup"><span data-stu-id="f0beb-152">The **SupportedMediaTypes** collection, which contains a list of supported media types.</span></span> <span data-ttu-id="f0beb-153">コンテンツ ネゴシエーターは、要求の Accept ヘッダーに対しては、この一覧の一致を試みます。</span><span class="sxs-lookup"><span data-stu-id="f0beb-153">The content negotiator tries to match this list against the request Accept header.</span></span> <span data-ttu-id="f0beb-154">Accept ヘッダーが範囲を含めることができますに注意してください。</span><span class="sxs-lookup"><span data-stu-id="f0beb-154">Note that the Accept header can include ranges.</span></span> <span data-ttu-id="f0beb-155">たとえば、"text/plain"にはテキストと一致する/\*または\* /\*します。</span><span class="sxs-lookup"><span data-stu-id="f0beb-155">For example, "text/plain" is a match for text/\* or \*/\*.</span></span>
- <span data-ttu-id="f0beb-156">**MediaTypeMappings**の一覧を含むコレクションを**MediaTypeMapping**オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="f0beb-156">The **MediaTypeMappings** collection, which contains a list of **MediaTypeMapping** objects.</span></span> <span data-ttu-id="f0beb-157">**MediaTypeMapping**クラスには、メディアの種類が HTTP 要求を一致するように、一般的な方法が用意されています。</span><span class="sxs-lookup"><span data-stu-id="f0beb-157">The **MediaTypeMapping** class provides a generic way to match HTTP requests with media types.</span></span> <span data-ttu-id="f0beb-158">たとえば、カスタム HTTP ヘッダーを特定のメディアの種類にマップことでした。</span><span class="sxs-lookup"><span data-stu-id="f0beb-158">For example, it could map a custom HTTP header to a particular media type.</span></span>

<span data-ttu-id="f0beb-159">複数ある場合は一致すると、最高の品質ファクター wins と一致します。</span><span class="sxs-lookup"><span data-stu-id="f0beb-159">If there are multiple matches, the match with the highest quality factor wins.</span></span> <span data-ttu-id="f0beb-160">例えば:</span><span class="sxs-lookup"><span data-stu-id="f0beb-160">For example:</span></span>

[!code-console[Main](content-negotiation/samples/sample6.cmd)]

<span data-ttu-id="f0beb-161">この例では、application/json は、application/xml が優先されるために暗黙の品質ファクター 1.0 が。</span><span class="sxs-lookup"><span data-stu-id="f0beb-161">In this example, application/json has an implied quality factor of 1.0, so it is preferred over application/xml.</span></span>

<span data-ttu-id="f0beb-162">一致が見つからない場合、コンテンツ ネゴシエーターは存在する場合、要求本文のメディアの種類に一致するように試みます。</span><span class="sxs-lookup"><span data-stu-id="f0beb-162">If no matches are found, the content negotiator tries to match on the media type of the request body, if any.</span></span> <span data-ttu-id="f0beb-163">たとえば、要求に JSON データが含まれている場合、コンテンツ ネゴシエーターは、JSON フォーマッタを探します。</span><span class="sxs-lookup"><span data-stu-id="f0beb-163">For example, if the request contains JSON data, the content negotiator looks for a JSON formatter.</span></span>

<span data-ttu-id="f0beb-164">引き続き、一致がない、コンテンツ ネゴシエーターは単に型をシリアル化できる最初のフォーマッタを取得します。</span><span class="sxs-lookup"><span data-stu-id="f0beb-164">If there are still no matches, the content negotiator simply picks the first formatter that can serialize the type.</span></span>

## <a name="selecting-a-character-encoding"></a><span data-ttu-id="f0beb-165">文字エンコーディングを選択します。</span><span class="sxs-lookup"><span data-stu-id="f0beb-165">Selecting a Character Encoding</span></span>

<span data-ttu-id="f0beb-166">コンテンツ ネゴシエーターが調べることで最適な文字エンコーディングを選択、フォーマッタを選択した後、 **SupportedEncodings**プロパティをフォーマッタおよび (存在する場合)、要求の Accept-charset ヘッダーに対して照合します。</span><span class="sxs-lookup"><span data-stu-id="f0beb-166">After a formatter is selected, the content negotiator chooses the best character encoding by looking at the **SupportedEncodings** property on the formatter, and matching it against the Accept-Charset header in the request (if any).</span></span>
