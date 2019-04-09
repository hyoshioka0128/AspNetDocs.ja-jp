---
uid: web-api/overview/web-api-routing-and-actions/routing-and-action-selection
title: ルーティングと ASP.NET Web API の操作の選択 |Microsoft Docs
author: MikeWasson
description: ''
ms.author: riande
ms.date: 12/14/2018
ms.assetid: bcf2d223-cb7f-411e-be05-f43e96a14015
msc.legacyurl: /web-api/overview/web-api-routing-and-actions/routing-and-action-selection
msc.type: authoredcontent
ms.openlocfilehash: 238efd312a73e2452ca5f679f2b8f5ed1336c4dc
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59385879"
---
# <a name="routing-and-action-selection-in-aspnet-web-api"></a><span data-ttu-id="a63d7-102">ルーティングと ASP.NET Web API の操作の選択</span><span class="sxs-lookup"><span data-stu-id="a63d7-102">Routing and Action Selection in ASP.NET Web API</span></span>

<span data-ttu-id="a63d7-103">作成者[Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="a63d7-103">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

<span data-ttu-id="a63d7-104">この記事では、ASP.NET Web API が特定のコント ローラーのアクションに HTTP 要求をルーティングする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-104">This article describes how ASP.NET Web API routes an HTTP request to a particular action on a controller.</span></span>

> [!NOTE]
> <span data-ttu-id="a63d7-105">ルーティングの大まかな概要については、次を参照してください。 [ASP.NET Web API におけるルーティング](routing-in-aspnet-web-api.md)します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-105">For a high-level overview of routing, see [Routing in ASP.NET Web API](routing-in-aspnet-web-api.md).</span></span>


<span data-ttu-id="a63d7-106">この記事では、ルーティング処理の詳細を検索します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-106">This article looks at the details of the routing process.</span></span> <span data-ttu-id="a63d7-107">Web API プロジェクトを作成して、一部の要求を取得しない検索期待どおりのルーティング、うまくいけばこの記事では役立ちます。</span><span class="sxs-lookup"><span data-stu-id="a63d7-107">If you create a Web API project and find that some requests don't get routed the way you expect, hopefully this article will help.</span></span>

<span data-ttu-id="a63d7-108">ルーティングと、次の 3 つの主要なフェーズがあります。</span><span class="sxs-lookup"><span data-stu-id="a63d7-108">Routing has three main phases:</span></span>

1. <span data-ttu-id="a63d7-109">ルート テンプレートの URI と照合します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-109">Matching the URI to a route template.</span></span>
2. <span data-ttu-id="a63d7-110">コント ローラーを選択します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-110">Selecting a controller.</span></span>
3. <span data-ttu-id="a63d7-111">アクションを選択します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-111">Selecting an action.</span></span>

<span data-ttu-id="a63d7-112">プロセスの一部を独自のカスタム動作と置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="a63d7-112">You can replace some parts of the process with your own custom behaviors.</span></span> <span data-ttu-id="a63d7-113">この記事では、既定の動作を取り上げます。</span><span class="sxs-lookup"><span data-stu-id="a63d7-113">In this article, I describe the default behavior.</span></span> <span data-ttu-id="a63d7-114">最後に、私には動作をカスタマイズできる場所に注意してください。</span><span class="sxs-lookup"><span data-stu-id="a63d7-114">At the end, I note the places where you can customize the behavior.</span></span>

## <a name="route-templates"></a><span data-ttu-id="a63d7-115">ルート テンプレート</span><span class="sxs-lookup"><span data-stu-id="a63d7-115">Route Templates</span></span>

<span data-ttu-id="a63d7-116">ルート テンプレートのように URI のパスが、プレース ホルダーの値を中かっこで示されることができます。</span><span class="sxs-lookup"><span data-stu-id="a63d7-116">A route template looks similar to a URI path, but it can have placeholder values, indicated with curly braces:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample1.ps1)]

<span data-ttu-id="a63d7-117">ルートを作成するときに、プレース ホルダーの一部またはすべてに対して既定値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="a63d7-117">When you create a route, you can provide default values for some or all of the placeholders:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample2.cs)]

<span data-ttu-id="a63d7-118">URI セグメントがプレース ホルダーを照合する方法を制限する制約を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="a63d7-118">You can also provide constraints, which restrict how a URI segment can match a placeholder:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample3.js)]

<span data-ttu-id="a63d7-119">フレームワークは、テンプレートの URI パスのセグメントの一致を試みます。</span><span class="sxs-lookup"><span data-stu-id="a63d7-119">The framework tries to match the segments in the URI path to the template.</span></span> <span data-ttu-id="a63d7-120">テンプレート内のリテラルは、正確に一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a63d7-120">Literals in the template must match exactly.</span></span> <span data-ttu-id="a63d7-121">プレース ホルダーは制約を指定しない限り、任意の値と一致します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-121">A placeholder matches any value, unless you specify constraints.</span></span> <span data-ttu-id="a63d7-122">フレームワークが URI ホスト名またはクエリ パラメーターなどの他の部分と一致しません。</span><span class="sxs-lookup"><span data-stu-id="a63d7-122">The framework does not match other parts of the URI, such as the host name or the query parameters.</span></span> <span data-ttu-id="a63d7-123">フレームワークは、URI と一致するルート テーブルの最初のルートを選択します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-123">The framework selects the first route in the route table that matches the URI.</span></span>

<span data-ttu-id="a63d7-124">2 つの特殊なプレース ホルダーがある。"{controller}"と"{action}"。</span><span class="sxs-lookup"><span data-stu-id="a63d7-124">There are two special placeholders: "{controller}" and "{action}".</span></span>

- <span data-ttu-id="a63d7-125">"{controller}"は、コント ローラーの名前を提供します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-125">"{controller}" provides the name of the controller.</span></span>
- <span data-ttu-id="a63d7-126">"{action}"は、アクションの名前を提供します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-126">"{action}" provides the name of the action.</span></span> <span data-ttu-id="a63d7-127">Web api では、通常の規則は、"{action}"を省略する場合は。</span><span class="sxs-lookup"><span data-stu-id="a63d7-127">In Web API, the usual convention is to omit "{action}".</span></span>

### <a name="defaults"></a><span data-ttu-id="a63d7-128">既定値</span><span class="sxs-lookup"><span data-stu-id="a63d7-128">Defaults</span></span>

<span data-ttu-id="a63d7-129">既定値を指定する場合、ルートはこれらのセグメントが不足している URI に一致します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-129">If you provide defaults, the route will match a URI that is missing those segments.</span></span> <span data-ttu-id="a63d7-130">例:</span><span class="sxs-lookup"><span data-stu-id="a63d7-130">For example:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample4.cs)]

<span data-ttu-id="a63d7-131">Uri`http://localhost/api/products/all`と`http://localhost/api/products`前のルートと一致します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-131">The URIs `http://localhost/api/products/all` and `http://localhost/api/products` match the preceding route.</span></span> <span data-ttu-id="a63d7-132">後者の uri に不足している`{category}`セグメントには、既定値が割り当てられている`all`します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-132">In the latter URI, the missing `{category}` segment is assigned the default value `all`.</span></span>

### <a name="route-dictionary"></a><span data-ttu-id="a63d7-133">ルート ディクショナリ</span><span class="sxs-lookup"><span data-stu-id="a63d7-133">Route Dictionary</span></span>

<span data-ttu-id="a63d7-134">フレームワークには、URI の一致が見つかると、各プレース ホルダーの値を格納しているディクショナリを作成します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-134">If the framework finds a match for a URI, it creates a dictionary that contains the value for each placeholder.</span></span> <span data-ttu-id="a63d7-135">キーは、プレース ホルダー名は、中かっこは含まれません。</span><span class="sxs-lookup"><span data-stu-id="a63d7-135">The keys are the placeholder names, not including the curly braces.</span></span> <span data-ttu-id="a63d7-136">値は、URI のパスとは、既定値から取得されます。</span><span class="sxs-lookup"><span data-stu-id="a63d7-136">The values are taken from the URI path or from the defaults.</span></span> <span data-ttu-id="a63d7-137">ディクショナリが格納されている、 **IHttpRouteData**オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="a63d7-137">The dictionary is stored in the **IHttpRouteData** object.</span></span>

<span data-ttu-id="a63d7-138">このルートの一致フェーズ中に、特別な"{controller}"と"{action}"プレース ホルダーは、他のプレース ホルダーと同じように扱われます。</span><span class="sxs-lookup"><span data-stu-id="a63d7-138">During this route-matching phase, the special "{controller}" and "{action}" placeholders are treated just like the other placeholders.</span></span> <span data-ttu-id="a63d7-139">単にその他の値をディクショナリに格納されます。</span><span class="sxs-lookup"><span data-stu-id="a63d7-139">They are simply stored in the dictionary with the other values.</span></span>

<span data-ttu-id="a63d7-140">既定値は、特殊な値を持つことができます**RouteParameter.Optional**します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-140">A default can have the special value **RouteParameter.Optional**.</span></span> <span data-ttu-id="a63d7-141">プレース ホルダーには、この値が割り当てられている取得する場合、値はルート ディクショナリに追加されません。</span><span class="sxs-lookup"><span data-stu-id="a63d7-141">If a placeholder gets assigned this value, the value is not added to the route dictionary.</span></span> <span data-ttu-id="a63d7-142">例:</span><span class="sxs-lookup"><span data-stu-id="a63d7-142">For example:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample5.cs)]

<span data-ttu-id="a63d7-143">URI のパス「api/製品」では、ルート ディクショナリが含まれます。</span><span class="sxs-lookup"><span data-stu-id="a63d7-143">For the URI path "api/products", the route dictionary will contain:</span></span>

- <span data-ttu-id="a63d7-144">コント ローラー:"products"</span><span class="sxs-lookup"><span data-stu-id="a63d7-144">controller: "products"</span></span>
- <span data-ttu-id="a63d7-145">カテゴリ:"all"</span><span class="sxs-lookup"><span data-stu-id="a63d7-145">category: "all"</span></span>

<span data-ttu-id="a63d7-146">「Api/製品/toys/123」、ただし、ルート ディクショナリが含まれます。</span><span class="sxs-lookup"><span data-stu-id="a63d7-146">For "api/products/toys/123", however, the route dictionary will contain:</span></span>

- <span data-ttu-id="a63d7-147">コント ローラー:"products"</span><span class="sxs-lookup"><span data-stu-id="a63d7-147">controller: "products"</span></span>
- <span data-ttu-id="a63d7-148">category: "toys"</span><span class="sxs-lookup"><span data-stu-id="a63d7-148">category: "toys"</span></span>
- <span data-ttu-id="a63d7-149">Id:"123"</span><span class="sxs-lookup"><span data-stu-id="a63d7-149">id: "123"</span></span>

<span data-ttu-id="a63d7-150">既定値は、ルート テンプレートで任意の場所に表示されていない値を含めることができますも。</span><span class="sxs-lookup"><span data-stu-id="a63d7-150">The defaults can also include a value that does not appear anywhere in the route template.</span></span> <span data-ttu-id="a63d7-151">ルートが一致すると、その値がディクショナリに格納します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-151">If the route matches, that value is stored in the dictionary.</span></span> <span data-ttu-id="a63d7-152">例:</span><span class="sxs-lookup"><span data-stu-id="a63d7-152">For example:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample6.cs)]

<span data-ttu-id="a63d7-153">URI のパスが「api/ルート/8」の場合は、2 つの値がディクショナリに含まれます。</span><span class="sxs-lookup"><span data-stu-id="a63d7-153">If the URI path is "api/root/8", the dictionary will contain two values:</span></span>

- <span data-ttu-id="a63d7-154">コント ローラー:"customers"</span><span class="sxs-lookup"><span data-stu-id="a63d7-154">controller: "customers"</span></span>
- <span data-ttu-id="a63d7-155">Id:"8"</span><span class="sxs-lookup"><span data-stu-id="a63d7-155">id: "8"</span></span>

## <a name="selecting-a-controller"></a><span data-ttu-id="a63d7-156">コント ローラーを選択します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-156">Selecting a Controller</span></span>

<span data-ttu-id="a63d7-157">コント ローラーの選択がによって処理される、 **IHttpControllerSelector.SelectController**メソッド。</span><span class="sxs-lookup"><span data-stu-id="a63d7-157">Controller selection is handled by the **IHttpControllerSelector.SelectController** method.</span></span> <span data-ttu-id="a63d7-158">このメソッドは、 **HttpRequestMessage**インスタンスを返す、 **HttpControllerDescriptor**します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-158">This method takes an **HttpRequestMessage** instance and returns an **HttpControllerDescriptor**.</span></span> <span data-ttu-id="a63d7-159">によって既定の実装が提供される、 **DefaultHttpControllerSelector**クラス。</span><span class="sxs-lookup"><span data-stu-id="a63d7-159">The default implementation is provided by the **DefaultHttpControllerSelector** class.</span></span> <span data-ttu-id="a63d7-160">このクラスは、単純なアルゴリズムを使用します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-160">This class uses a straightforward algorithm:</span></span>

1. <span data-ttu-id="a63d7-161">キー"controller"のルート ディクショナリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a63d7-161">Look in the route dictionary for the key "controller".</span></span>
2. <span data-ttu-id="a63d7-162">このキーの値を取得し、コント ローラーの種類の名前を取得するには、"Controller"という文字列を追加します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-162">Take the value for this key and append the string "Controller" to get the controller type name.</span></span>
3. <span data-ttu-id="a63d7-163">この型の名前と Web API コント ローラーを探します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-163">Look for a Web API controller with this type name.</span></span>

<span data-ttu-id="a63d7-164">たとえば、ルート ディクショナリに"controller"キーと値のペアが含まれている場合 =「製品」、コント ローラーの種類が"ProductsController"します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-164">For example, if the route dictionary contains the key-value pair "controller" = "products", then the controller type is "ProductsController".</span></span> <span data-ttu-id="a63d7-165">一致する型がない、または複数の一致がある場合、フレームワークは、クライアントにエラーを返します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-165">If there is no matching type, or multiple matches, the framework returns an error to the client.</span></span>

<span data-ttu-id="a63d7-166">手順 3. の**DefaultHttpControllerSelector**を使用して、 **IHttpControllerTypeResolver**インターフェイスが Web API コント ローラーの種類の一覧を取得します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-166">For step 3, **DefaultHttpControllerSelector** uses the **IHttpControllerTypeResolver** interface to get the list of Web API controller types.</span></span> <span data-ttu-id="a63d7-167">既定の実装**IHttpControllerTypeResolver** (a) 実装するすべてのパブリック クラスを返します**IHttpController**、(b) はない抽象化、および (c)"Controller"で終わる名前があります。</span><span class="sxs-lookup"><span data-stu-id="a63d7-167">The default implementation of **IHttpControllerTypeResolver** returns all public classes that (a) implement **IHttpController**, (b) are not abstract, and (c) have a name that ends in "Controller".</span></span>

## <a name="action-selection"></a><span data-ttu-id="a63d7-168">アクションの選択</span><span class="sxs-lookup"><span data-stu-id="a63d7-168">Action Selection</span></span>

<span data-ttu-id="a63d7-169">コント ローラーを選択すると、フレームワークが呼び出すことによってアクションを選択、 **IHttpActionSelector.SelectAction**メソッド。</span><span class="sxs-lookup"><span data-stu-id="a63d7-169">After selecting the controller, the framework selects the action by calling the **IHttpActionSelector.SelectAction** method.</span></span> <span data-ttu-id="a63d7-170">このメソッドは、 **HttpControllerContext**を返します、 **HttpActionDescriptor**します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-170">This method takes an **HttpControllerContext** and returns an **HttpActionDescriptor**.</span></span>

<span data-ttu-id="a63d7-171">によって既定の実装が提供される、 **ApiControllerActionSelector**クラス。</span><span class="sxs-lookup"><span data-stu-id="a63d7-171">The default implementation is provided by the **ApiControllerActionSelector** class.</span></span> <span data-ttu-id="a63d7-172">アクションを選択するには、次の場所になります。</span><span class="sxs-lookup"><span data-stu-id="a63d7-172">To select an action, it looks at the following:</span></span>

- <span data-ttu-id="a63d7-173">要求の HTTP メソッド。</span><span class="sxs-lookup"><span data-stu-id="a63d7-173">The HTTP method of the request.</span></span>
- <span data-ttu-id="a63d7-174">存在する場合はルート テンプレートで"{action}"プレース ホルダーです。</span><span class="sxs-lookup"><span data-stu-id="a63d7-174">The "{action}" placeholder in the route template, if present.</span></span>
- <span data-ttu-id="a63d7-175">コント ローラーのアクションのパラメーター。</span><span class="sxs-lookup"><span data-stu-id="a63d7-175">The parameters of the actions on the controller.</span></span>

<span data-ttu-id="a63d7-176">選択アルゴリズムを見る前に、コント ローラー アクションのいくつかの点を理解する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a63d7-176">Before looking at the selection algorithm, we need to understand some things about controller actions.</span></span>

**<span data-ttu-id="a63d7-177">コント ローラーのメソッドは、「アクション」と見なされますか。</span><span class="sxs-lookup"><span data-stu-id="a63d7-177">Which methods on the controller are considered "actions"?</span></span>** <span data-ttu-id="a63d7-178">アクションを選択すると、フレームワークのみパブリック インスタンス メソッドを調べ、コント ローラー。</span><span class="sxs-lookup"><span data-stu-id="a63d7-178">When selecting an action, the framework only looks at public instance methods on the controller.</span></span> <span data-ttu-id="a63d7-179">また、バインドファイル[「特別な名前」](https://msdn.microsoft.com/library/system.reflection.methodbase.isspecialname)メソッド (コンス トラクター、イベント、演算子のオーバー ロードおよびなど) とから継承されたメソッド、 **ApiController**クラス。</span><span class="sxs-lookup"><span data-stu-id="a63d7-179">Also, it excludes ["special name"](https://msdn.microsoft.com/library/system.reflection.methodbase.isspecialname) methods (constructors, events, operator overloads, and so forth), and methods inherited from the **ApiController** class.</span></span>

**<span data-ttu-id="a63d7-180">HTTP メソッド。</span><span class="sxs-lookup"><span data-stu-id="a63d7-180">HTTP Methods.</span></span>** <span data-ttu-id="a63d7-181">フレームワークには、次のように、要求の HTTP メソッドに一致するアクションのみ選択されます。</span><span class="sxs-lookup"><span data-stu-id="a63d7-181">The framework only chooses actions that match the HTTP method of the request, determined as follows:</span></span>

1. <span data-ttu-id="a63d7-182">属性では、HTTP メソッドを指定できます。**AcceptVerbs**、 **HttpDelete**、 **HttpGet**、 **HttpHead**、 **HttpOptions**、 **HttpPatch**、 **HttpPost**、または**HttpPut**します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-182">You can specify the HTTP method with an attribute: **AcceptVerbs**, **HttpDelete**, **HttpGet**, **HttpHead**, **HttpOptions**, **HttpPatch**, **HttpPost**, or **HttpPut**.</span></span>
2. <span data-ttu-id="a63d7-183">それ以外の場合、コント ローラー メソッドの名前は、"Get"、"Post"、"Put"、"Delete"、"Head"、「オプション」または「パッチ」で始まる場合、規則によって、アクションがサポートする HTTP メソッド。</span><span class="sxs-lookup"><span data-stu-id="a63d7-183">Otherwise, if the name of the controller method starts with "Get", "Post", "Put", "Delete", "Head", "Options", or "Patch", then by convention the action supports that HTTP method.</span></span>
3. <span data-ttu-id="a63d7-184">上記のいずれの場合、メソッドは POST をサポートします。</span><span class="sxs-lookup"><span data-stu-id="a63d7-184">If none of the above, the method supports POST.</span></span>

**<span data-ttu-id="a63d7-185">パラメーター バインディング。</span><span class="sxs-lookup"><span data-stu-id="a63d7-185">Parameter Bindings.</span></span>** <span data-ttu-id="a63d7-186">パラメーターのバインドでは、Web API がパラメーターの値を作成する方法です。</span><span class="sxs-lookup"><span data-stu-id="a63d7-186">A parameter binding is how Web API creates a value for a parameter.</span></span> <span data-ttu-id="a63d7-187">パラメーター バインディングの既定のルールを次に示します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-187">Here is the default rule for parameter binding:</span></span>

- <span data-ttu-id="a63d7-188">単純型は、URI から取得されます。</span><span class="sxs-lookup"><span data-stu-id="a63d7-188">Simple types are taken from the URI.</span></span>
- <span data-ttu-id="a63d7-189">複合型は、要求本文から取得されます。</span><span class="sxs-lookup"><span data-stu-id="a63d7-189">Complex types are taken from the request body.</span></span>

<span data-ttu-id="a63d7-190">すべての単純型が含まれます、 [.NET Framework のプリミティブ型](https://msdn.microsoft.com/library/system.type.isprimitive)、plus **DateTime**、 **Decimal**、 **Guid**、**文字列**、および**TimeSpan**します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-190">Simple types include all of the [.NET Framework primitive types](https://msdn.microsoft.com/library/system.type.isprimitive), plus **DateTime**, **Decimal**, **Guid**, **String**, and **TimeSpan**.</span></span> <span data-ttu-id="a63d7-191">アクションごとに最大で 1 つのパラメーターは、要求本文を読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="a63d7-191">For each action, at most one parameter can read the request body.</span></span>

> [!NOTE]
> <span data-ttu-id="a63d7-192">既定のバインディング規則を上書きすることになります。</span><span class="sxs-lookup"><span data-stu-id="a63d7-192">It is possible to override the default binding rules.</span></span> <span data-ttu-id="a63d7-193">参照してください[内部的には、WebAPI パラメーター バインド](https://blogs.msdn.com/b/jmstall/archive/2012/05/11/webapi-parameter-binding-under-the-hood.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-193">See [WebAPI Parameter binding under the hood](https://blogs.msdn.com/b/jmstall/archive/2012/05/11/webapi-parameter-binding-under-the-hood.aspx).</span></span>


<span data-ttu-id="a63d7-194">を踏まえて、アクションの選択アルゴリズムを示します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-194">With that background, here is the action selection algorithm.</span></span>

1. <span data-ttu-id="a63d7-195">HTTP 要求メソッドに一致するコント ローラーのすべてのアクションの一覧を作成します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-195">Create a list of all actions on the controller that match the HTTP request method.</span></span>
2. <span data-ttu-id="a63d7-196">ルート ディクショナリに、"action"エントリがある場合は、名前がこの値と一致しないアクションを削除します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-196">If the route dictionary has an "action" entry, remove actions whose name does not match this value.</span></span>
3. <span data-ttu-id="a63d7-197">次のように、URI にアクション パラメーターを照合しようとしてください。</span><span class="sxs-lookup"><span data-stu-id="a63d7-197">Try to match action parameters to the URI, as follows:</span></span> 

    1. <span data-ttu-id="a63d7-198">アクションごとに、単純型は、バインディングが、URI からパラメーターを取得するパラメーターの一覧を取得します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-198">For each action, get a list of the parameters that are a simple type, where the binding gets the parameter from the URI.</span></span> <span data-ttu-id="a63d7-199">省略可能なパラメーターを除外します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-199">Exclude optional parameters.</span></span>
    2. <span data-ttu-id="a63d7-200">この一覧から、ルート ディクショナリで、または URI クエリ文字列では、各パラメーター名と一致するを検索してください。</span><span class="sxs-lookup"><span data-stu-id="a63d7-200">From this list, try to find a match for each parameter name, either in the route dictionary or in the URI query string.</span></span> <span data-ttu-id="a63d7-201">一致は、大文字と小文字を区別しない、パラメーターの順序に依存しません。</span><span class="sxs-lookup"><span data-stu-id="a63d7-201">Matches are case insensitive and do not depend on the parameter order.</span></span>
    3. <span data-ttu-id="a63d7-202">リスト内のすべてのパラメーターが URI に一致するものがアクションを選択します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-202">Select an action where every parameter in the list has a match in the URI.</span></span>
    4. <span data-ttu-id="a63d7-203">その 1 つのアクションよりがこれらの条件を満たしている場合は、1 つと、ほとんどのパラメーターに一致するを選択します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-203">If more that one action meets these criteria, pick the one with the most parameter matches.</span></span>
4. <span data-ttu-id="a63d7-204">アクションは、無視、 **[NonAction]** 属性。</span><span class="sxs-lookup"><span data-stu-id="a63d7-204">Ignore actions with the **[NonAction]** attribute.</span></span>

<span data-ttu-id="a63d7-205">ステップ 3 は、おそらく最もわかりにくいです。</span><span class="sxs-lookup"><span data-stu-id="a63d7-205">Step #3 is probably the most confusing.</span></span> <span data-ttu-id="a63d7-206">基本的な考え方は、パラメーターは、URI から、要求本文またはカスタム バインドからはその値を取得できます。</span><span class="sxs-lookup"><span data-stu-id="a63d7-206">The basic idea is that a parameter can get its value either from the URI, from the request body, or from a custom binding.</span></span> <span data-ttu-id="a63d7-207">URI から取得したパラメーターの場合、URI に実際には、パス (ルート ディクショナリ) 経由で、またはクエリ文字列にそのパラメーターの値が含まれていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-207">For parameters that come from the URI, we want to ensure that the URI actually contains a value for that parameter, either in the path (via the route dictionary) or in the query string.</span></span>

<span data-ttu-id="a63d7-208">たとえば、次のアクションがあるとします。</span><span class="sxs-lookup"><span data-stu-id="a63d7-208">For example, consider the following action:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample7.cs)]

<span data-ttu-id="a63d7-209">*Id*パラメーターが URI にバインドします。</span><span class="sxs-lookup"><span data-stu-id="a63d7-209">The *id* parameter binds to the URI.</span></span> <span data-ttu-id="a63d7-210">したがって、このアクションでは、"id", ルート ディクショナリまたはクエリ文字列内のいずれかの値を含む URI が一致のみできます。</span><span class="sxs-lookup"><span data-stu-id="a63d7-210">Therefore, this action can only match a URI that contains a value for "id", either in the route dictionary or in the query string.</span></span>

<span data-ttu-id="a63d7-211">省略可能なために、省略可能なパラメーターは、例外は。</span><span class="sxs-lookup"><span data-stu-id="a63d7-211">Optional parameters are an exception, because they are optional.</span></span> <span data-ttu-id="a63d7-212">省略可能なパラメーターでは、[ok] 場合は、バインドは、URI から値を取得することはできません。</span><span class="sxs-lookup"><span data-stu-id="a63d7-212">For an optional parameter, it's OK if the binding can't get the value from the URI.</span></span>

<span data-ttu-id="a63d7-213">複合型では、例外を別の理由。</span><span class="sxs-lookup"><span data-stu-id="a63d7-213">Complex types are an exception for a different reason.</span></span> <span data-ttu-id="a63d7-214">複合型は、カスタム バインディングを介して URI にのみバインドできます。</span><span class="sxs-lookup"><span data-stu-id="a63d7-214">A complex type can only bind to the URI through a custom binding.</span></span> <span data-ttu-id="a63d7-215">その場合は、フレームワークことはできません、あらかじめ調べておくパラメーターは、特定の URI にバインドされているかどうか。</span><span class="sxs-lookup"><span data-stu-id="a63d7-215">But in that case, the framework cannot know in advance whether the parameter would bind to a particular URI.</span></span> <span data-ttu-id="a63d7-216">確認するには、バインディングを呼び出し、必要があります。</span><span class="sxs-lookup"><span data-stu-id="a63d7-216">To find out, it would need to invoke the binding.</span></span> <span data-ttu-id="a63d7-217">選択アルゴリズムの目的では、すべてのバインドを呼び出す前に、静的な説明からアクションを選択します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-217">The goal of the selection algorithm is to select an action from the static description, before invoking any bindings.</span></span> <span data-ttu-id="a63d7-218">そのため、複合型は、一致のアルゴリズムから除外されます。</span><span class="sxs-lookup"><span data-stu-id="a63d7-218">Therefore, complex types are excluded from the matching algorithm.</span></span>

<span data-ttu-id="a63d7-219">アクションを選択すると、すべてのパラメーター バインドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="a63d7-219">After the action is selected, all parameter bindings are invoked.</span></span>

<span data-ttu-id="a63d7-220">概要:</span><span class="sxs-lookup"><span data-stu-id="a63d7-220">Summary:</span></span>

- <span data-ttu-id="a63d7-221">アクションは、要求の HTTP メソッドと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a63d7-221">The action must match the HTTP method of the request.</span></span>
- <span data-ttu-id="a63d7-222">アクション名は、存在する場合、ルート ディクショナリで"action"エントリを一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a63d7-222">The action name must match the "action" entry in the route dictionary, if present.</span></span>
- <span data-ttu-id="a63d7-223">アクションのすべてのパラメーターの場合は、パラメーターは、URI から取得し、パラメーター名いる必要がありますルート ディクショナリまたは URI クエリ文字列。</span><span class="sxs-lookup"><span data-stu-id="a63d7-223">For every parameter of the action, if the parameter is taken from the URI, then the parameter name must be found either in the route dictionary or in the URI query string.</span></span> <span data-ttu-id="a63d7-224">(省略可能なパラメーターと複雑な種類のパラメーターは除外されます。)</span><span class="sxs-lookup"><span data-stu-id="a63d7-224">(Optional parameters and parameters with complex types are excluded.)</span></span>
- <span data-ttu-id="a63d7-225">パラメーターの数、最も一致するようにしてみてください。</span><span class="sxs-lookup"><span data-stu-id="a63d7-225">Try to match the most number of parameters.</span></span> <span data-ttu-id="a63d7-226">最も一致するパラメーターなしのメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="a63d7-226">The best match might be a method with no parameters.</span></span>

## <a name="extended-example"></a><span data-ttu-id="a63d7-227">詳細な例</span><span class="sxs-lookup"><span data-stu-id="a63d7-227">Extended Example</span></span>

<span data-ttu-id="a63d7-228">ルート:</span><span class="sxs-lookup"><span data-stu-id="a63d7-228">Routes:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample8.cs)]

<span data-ttu-id="a63d7-229">コントローラー:</span><span class="sxs-lookup"><span data-stu-id="a63d7-229">Controller:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample9.cs)]

<span data-ttu-id="a63d7-230">HTTP 要求:</span><span class="sxs-lookup"><span data-stu-id="a63d7-230">HTTP request:</span></span>

[!code-console[Main](routing-and-action-selection/samples/sample10.cmd)]

### <a name="route-matching"></a><span data-ttu-id="a63d7-231">ルートの照合</span><span class="sxs-lookup"><span data-stu-id="a63d7-231">Route Matching</span></span>

<span data-ttu-id="a63d7-232">URI では、"DefaultApi"という名前のルートと一致します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-232">The URI matches the route named "DefaultApi".</span></span> <span data-ttu-id="a63d7-233">ルート ディクショナリには、次のエントリが含まれています。</span><span class="sxs-lookup"><span data-stu-id="a63d7-233">The route dictionary contains the following entries:</span></span>

- <span data-ttu-id="a63d7-234">コント ローラー:"products"</span><span class="sxs-lookup"><span data-stu-id="a63d7-234">controller: "products"</span></span>
- <span data-ttu-id="a63d7-235">Id:"1"</span><span class="sxs-lookup"><span data-stu-id="a63d7-235">id: "1"</span></span>

<span data-ttu-id="a63d7-236">クエリ文字列パラメーター、"version"と「詳細」を含まないルート ディクショナリが、これらのアクションの選択中にも考慮されます。</span><span class="sxs-lookup"><span data-stu-id="a63d7-236">The route dictionary does not contain the query string parameters, "version" and "details", but these will still be considered during action selection.</span></span>

### <a name="controller-selection"></a><span data-ttu-id="a63d7-237">コント ローラーの選択</span><span class="sxs-lookup"><span data-stu-id="a63d7-237">Controller Selection</span></span>

<span data-ttu-id="a63d7-238">コント ローラーの種類は、ルート ディクショナリの"controller"エントリから`ProductsController`します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-238">From the "controller" entry in the route dictionary, the controller type is `ProductsController`.</span></span>

### <a name="action-selection"></a><span data-ttu-id="a63d7-239">アクションの選択</span><span class="sxs-lookup"><span data-stu-id="a63d7-239">Action Selection</span></span>

<span data-ttu-id="a63d7-240">HTTP 要求は、GET 要求です。</span><span class="sxs-lookup"><span data-stu-id="a63d7-240">The HTTP request is a GET request.</span></span> <span data-ttu-id="a63d7-241">GET をサポートするコント ローラー アクションが`GetAll`、 `GetById`、および`FindProductsByName`します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-241">The controller actions that support GET are `GetAll`, `GetById`, and `FindProductsByName`.</span></span> <span data-ttu-id="a63d7-242">ルート ディクショナリは、"action"のエントリを含まないため、操作名と一致する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="a63d7-242">The route dictionary does not contain an entry for "action", so we don't need to match the action name.</span></span>

<span data-ttu-id="a63d7-243">次に、私たち見てみてください、アクションのパラメーターの名前に一致するようにのみ、GET アクション。</span><span class="sxs-lookup"><span data-stu-id="a63d7-243">Next, we try to match parameter names for the actions, looking only at the GET actions.</span></span>

| <span data-ttu-id="a63d7-244">アクション</span><span class="sxs-lookup"><span data-stu-id="a63d7-244">Action</span></span> | <span data-ttu-id="a63d7-245">一致するパラメーター</span><span class="sxs-lookup"><span data-stu-id="a63d7-245">Parameters to Match</span></span> |
| --- | --- |
| `GetAll` | <span data-ttu-id="a63d7-246">none</span><span class="sxs-lookup"><span data-stu-id="a63d7-246">none</span></span> |
| `GetById` | <span data-ttu-id="a63d7-247">"id"</span><span class="sxs-lookup"><span data-stu-id="a63d7-247">"id"</span></span> |
| `FindProductsByName` | <span data-ttu-id="a63d7-248">"name"</span><span class="sxs-lookup"><span data-stu-id="a63d7-248">"name"</span></span> |

<span data-ttu-id="a63d7-249">注意、*バージョン*パラメーターの`GetById`とは見なされません、オプションのパラメーターがあるためです。</span><span class="sxs-lookup"><span data-stu-id="a63d7-249">Notice that the *version* parameter of `GetById` is not considered, because it is an optional parameter.</span></span>

<span data-ttu-id="a63d7-250">`GetAll`メソッドに普通に一致します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-250">The `GetAll` method matches trivially.</span></span> <span data-ttu-id="a63d7-251">`GetById`メソッドも一致すると、ルート ディクショナリには、"id"が含まれています。</span><span class="sxs-lookup"><span data-stu-id="a63d7-251">The `GetById` method also matches, because the route dictionary contains "id".</span></span> <span data-ttu-id="a63d7-252">`FindProductsByName`メソッドと一致しません。</span><span class="sxs-lookup"><span data-stu-id="a63d7-252">The `FindProductsByName` method does not match.</span></span>

<span data-ttu-id="a63d7-253">`GetById`のパラメーターなしではなく、1 つのパラメーターに一致するため、メソッドが wins`GetAll`します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-253">The `GetById` method wins, because it matches one parameter, versus no parameters for `GetAll`.</span></span> <span data-ttu-id="a63d7-254">メソッドは、次のパラメーター値で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="a63d7-254">The method is invoked with the following parameter values:</span></span>

- <span data-ttu-id="a63d7-255">*id* = 1</span><span class="sxs-lookup"><span data-stu-id="a63d7-255">*id* = 1</span></span>
- <span data-ttu-id="a63d7-256">*バージョン*1.5 を =</span><span class="sxs-lookup"><span data-stu-id="a63d7-256">*version* = 1.5</span></span>

<span data-ttu-id="a63d7-257">*バージョン*使用されませんでした選択アルゴリズムでパラメーターの値は、URI クエリ文字列から取得します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-257">Notice that even though *version* was not used in the selection algorithm, the value of the parameter comes from the URI query string.</span></span>

## <a name="extension-points"></a><span data-ttu-id="a63d7-258">拡張ポイント</span><span class="sxs-lookup"><span data-stu-id="a63d7-258">Extension Points</span></span>

<span data-ttu-id="a63d7-259">Web API は、ルーティングのプロセスの一部の拡張ポイントを提供します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-259">Web API provides extension points for some parts of the routing process.</span></span>

| <span data-ttu-id="a63d7-260">Interface</span><span class="sxs-lookup"><span data-stu-id="a63d7-260">Interface</span></span> | <span data-ttu-id="a63d7-261">説明</span><span class="sxs-lookup"><span data-stu-id="a63d7-261">Description</span></span> |
| --- | --- |
| **<span data-ttu-id="a63d7-262">IHttpControllerSelector</span><span class="sxs-lookup"><span data-stu-id="a63d7-262">IHttpControllerSelector</span></span>** | <span data-ttu-id="a63d7-263">コント ローラーを選択します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-263">Selects the controller.</span></span> |
| **<span data-ttu-id="a63d7-264">IHttpControllerTypeResolver</span><span class="sxs-lookup"><span data-stu-id="a63d7-264">IHttpControllerTypeResolver</span></span>** | <span data-ttu-id="a63d7-265">コント ローラーの種類の一覧を取得します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-265">Gets the list of controller types.</span></span> <span data-ttu-id="a63d7-266">**DefaultHttpControllerSelector**はこの一覧から、コント ローラーの種類を選択します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-266">The **DefaultHttpControllerSelector** chooses the controller type from this list.</span></span> |
| **<span data-ttu-id="a63d7-267">IAssembliesResolver</span><span class="sxs-lookup"><span data-stu-id="a63d7-267">IAssembliesResolver</span></span>** | <span data-ttu-id="a63d7-268">プロジェクト アセンブリの一覧を取得します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-268">Gets the list of project assemblies.</span></span> <span data-ttu-id="a63d7-269">**IHttpControllerTypeResolver**インターフェイスでは、このリストを使用して、コント ローラーの種類を検索します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-269">The **IHttpControllerTypeResolver** interface uses this list to find the controller types.</span></span> |
| **<span data-ttu-id="a63d7-270">IHttpControllerActivator</span><span class="sxs-lookup"><span data-stu-id="a63d7-270">IHttpControllerActivator</span></span>** | <span data-ttu-id="a63d7-271">コント ローラーの新しいインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-271">Creates new controller instances.</span></span> |
| **<span data-ttu-id="a63d7-272">IHttpActionSelector</span><span class="sxs-lookup"><span data-stu-id="a63d7-272">IHttpActionSelector</span></span>** | <span data-ttu-id="a63d7-273">アクションを選択します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-273">Selects the action.</span></span> |
| **<span data-ttu-id="a63d7-274">IHttpActionInvoker</span><span class="sxs-lookup"><span data-stu-id="a63d7-274">IHttpActionInvoker</span></span>** | <span data-ttu-id="a63d7-275">アクションを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a63d7-275">Invokes the action.</span></span> |

<span data-ttu-id="a63d7-276">これらのインターフェイスのいずれかの独自の実装を提供するには、使用、**サービス**コレクションに、 **HttpConfiguration**オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="a63d7-276">To provide your own implementation for any of these interfaces, use the **Services** collection on the **HttpConfiguration** object:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample11.cs)]
