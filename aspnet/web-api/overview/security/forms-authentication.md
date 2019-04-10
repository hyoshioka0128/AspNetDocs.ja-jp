---
uid: web-api/overview/security/forms-authentication
title: ASP.NET Web API でフォーム認証 |Microsoft Docs
author: MikeWasson
description: ASP.NET Web API でフォーム認証の使用について説明します。
ms.author: riande
ms.date: 12/12/2012
ms.assetid: 9f06c1f2-ffaa-4831-94a0-2e4a3befdf07
msc.legacyurl: /web-api/overview/security/forms-authentication
msc.type: authoredcontent
ms.openlocfilehash: 147bfab76e48497f35a72b28cd935f40ec4193bf
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59410080"
---
# <a name="forms-authentication-in-aspnet-web-api"></a><span data-ttu-id="11fad-103">ASP.NET Web API でフォーム認証</span><span class="sxs-lookup"><span data-stu-id="11fad-103">Forms Authentication in ASP.NET Web API</span></span>

<span data-ttu-id="11fad-104">作成者[Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="11fad-104">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

<span data-ttu-id="11fad-105">フォーム認証では、HTML フォームを使用して、ユーザーの資格情報をサーバーに送信します。</span><span class="sxs-lookup"><span data-stu-id="11fad-105">Forms authentication uses an HTML form to send the user's credentials to the server.</span></span> <span data-ttu-id="11fad-106">インターネット標準ではありません。</span><span class="sxs-lookup"><span data-stu-id="11fad-106">It is not an Internet standard.</span></span> <span data-ttu-id="11fad-107">ユーザーが HTML フォームを操作できるように、フォーム認証は web、web アプリケーションから呼び出される API の適切なのみ。</span><span class="sxs-lookup"><span data-stu-id="11fad-107">Forms authentication is only appropriate for web APIs that are called from a web application, so that the user can interact with the HTML form.</span></span>

| <span data-ttu-id="11fad-108">長所</span><span class="sxs-lookup"><span data-stu-id="11fad-108">Advantages</span></span> | <span data-ttu-id="11fad-109">短所</span><span class="sxs-lookup"><span data-stu-id="11fad-109">Disadvantages</span></span> |
| --- | --- |
| <span data-ttu-id="11fad-110">実装が容易。ASP.NET に組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="11fad-110">- Easy to implement: Built into ASP.NET.</span></span> <span data-ttu-id="11fad-111">-ASP.NET メンバーシップ プロバイダーは、簡単にユーザー アカウントの管理を使用します。</span><span class="sxs-lookup"><span data-stu-id="11fad-111">- Uses ASP.NET membership provider, which makes it easy to manage user accounts.</span></span> | <span data-ttu-id="11fad-112">-非標準の HTTP 認証機構です。標準の Authorization ヘッダーではなく、HTTP クッキーを使用します。</span><span class="sxs-lookup"><span data-stu-id="11fad-112">- Not a standard HTTP authentication mechanism; uses HTTP cookies instead of the standard Authorization header.</span></span> <span data-ttu-id="11fad-113">-クライアントのブラウザーが必要です。</span><span class="sxs-lookup"><span data-stu-id="11fad-113">- Requires a browser client.</span></span> <span data-ttu-id="11fad-114">-資格情報は、プレーン テキストとして送信されます。</span><span class="sxs-lookup"><span data-stu-id="11fad-114">- Credentials are sent as plaintext.</span></span> <span data-ttu-id="11fad-115">クロスサイト リクエスト フォージェリ (CSRF); に対して脆弱CSRF 対策メジャーが必要です。</span><span class="sxs-lookup"><span data-stu-id="11fad-115">- Vulnerable to cross-site request forgery (CSRF); requires anti-CSRF measures.</span></span> <span data-ttu-id="11fad-116">-Nonbrowser クライアントから使用するは困難です。</span><span class="sxs-lookup"><span data-stu-id="11fad-116">- Difficult to use from nonbrowser clients.</span></span> <span data-ttu-id="11fad-117">ログインには、ブラウザーが必要です。</span><span class="sxs-lookup"><span data-stu-id="11fad-117">Login requires a browser.</span></span> <span data-ttu-id="11fad-118">のユーザー資格情報は、要求で送信されます。</span><span class="sxs-lookup"><span data-stu-id="11fad-118">- User credentials are sent in the request.</span></span> <span data-ttu-id="11fad-119">-一部のユーザーは、cookie を無効にします。</span><span class="sxs-lookup"><span data-stu-id="11fad-119">- Some users disable cookies.</span></span> |

<span data-ttu-id="11fad-120">について簡単に、ASP.NET フォーム認証は、次のように動作します。</span><span class="sxs-lookup"><span data-stu-id="11fad-120">Briefly, forms authentication in ASP.NET works like this:</span></span>

1. <span data-ttu-id="11fad-121">クライアントは、認証が必要なリソースを要求します。</span><span class="sxs-lookup"><span data-stu-id="11fad-121">The client requests a resource that requires authentication.</span></span>
2. <span data-ttu-id="11fad-122">ユーザーが認証されていない場合、サーバーは HTTP 302 (検出) を返し、ログイン ページにリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="11fad-122">If the user is not authenticated, the server returns HTTP 302 (Found) and redirects to a login page.</span></span>
3. <span data-ttu-id="11fad-123">ユーザーは資格情報を入力し、フォームを送信します。</span><span class="sxs-lookup"><span data-stu-id="11fad-123">The user enters credentials and submits the form.</span></span>
4. <span data-ttu-id="11fad-124">サーバーは、元の URI にリダイレクトするもう 1 つの HTTP 302 を返します。</span><span class="sxs-lookup"><span data-stu-id="11fad-124">The server returns another HTTP 302 that redirects back to the original URI.</span></span> <span data-ttu-id="11fad-125">この応答には、認証 cookie が含まれています。</span><span class="sxs-lookup"><span data-stu-id="11fad-125">This response includes an authentication cookie.</span></span>
5. <span data-ttu-id="11fad-126">クライアントは、もう一度リソースを要求します。</span><span class="sxs-lookup"><span data-stu-id="11fad-126">The client requests the resource again.</span></span> <span data-ttu-id="11fad-127">要求には、サーバー要求を許可するために、認証 cookie が含まれます。</span><span class="sxs-lookup"><span data-stu-id="11fad-127">The request includes the authentication cookie, so the server grants the request.</span></span>

![](forms-authentication/_static/image1.png)

<span data-ttu-id="11fad-128">詳細については、次を参照してください。 [、フォーム認証の概要。](../../../web-forms/overview/older-versions-security/introduction/an-overview-of-forms-authentication-cs.md)</span><span class="sxs-lookup"><span data-stu-id="11fad-128">For more information, see [An Overview of Forms Authentication.](../../../web-forms/overview/older-versions-security/introduction/an-overview-of-forms-authentication-cs.md)</span></span>

## <a name="using-forms-authentication-with-web-api"></a><span data-ttu-id="11fad-129">フォーム認証を使用して Web API</span><span class="sxs-lookup"><span data-stu-id="11fad-129">Using Forms Authentication with Web API</span></span>

<span data-ttu-id="11fad-130">フォーム認証を使用するアプリケーションを作成するには、MVC 4 プロジェクト ウィザードで、「インターネット アプリケーション」テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="11fad-130">To create an application that uses forms authentication, select the "Internet Application" template in the MVC 4 project wizard.</span></span> <span data-ttu-id="11fad-131">このテンプレートは、アカウント管理のための MVC コント ローラーを作成します。</span><span class="sxs-lookup"><span data-stu-id="11fad-131">This template creates MVC controllers for account management.</span></span> <span data-ttu-id="11fad-132">ASP.NET の Fall 2012 更新プログラムで使用できる「シングル ページ アプリケーション」テンプレートを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="11fad-132">You can also use the "Single Page Application" template, available in the ASP.NET Fall 2012 Update.</span></span>

<span data-ttu-id="11fad-133">使用してアクセスを制限する、web API コント ローラーで、`[Authorize]`属性」の説明に従って[[Authorize] 属性を使用して](authentication-and-authorization-in-aspnet-web-api.md#auth3)します。</span><span class="sxs-lookup"><span data-stu-id="11fad-133">In your web API controllers, you can restrict access by using the `[Authorize]` attribute, as described in [Using the [Authorize] Attribute](authentication-and-authorization-in-aspnet-web-api.md#auth3).</span></span>

<span data-ttu-id="11fad-134">フォーム認証では、セッションの cookie を使用して、要求を認証します。</span><span class="sxs-lookup"><span data-stu-id="11fad-134">Forms-authentication uses a session cookie to authenticate requests.</span></span> <span data-ttu-id="11fad-135">ブラウザーは、web サイトに自動的にすべての関連クッキーを送信します。</span><span class="sxs-lookup"><span data-stu-id="11fad-135">Browsers automatically send all relevant cookies to the destination web site.</span></span> <span data-ttu-id="11fad-136">この機能により、フォーム認証のクロスサイト リクエスト フォージェリ (CSRF) 攻撃を参照する可能性のある脆弱性のある[防止クロスサイト リクエスト フォージェリ (CSRF) 攻撃](preventing-cross-site-request-forgery-csrf-attacks.md)します。</span><span class="sxs-lookup"><span data-stu-id="11fad-136">This feature makes forms authentication potentially vulnerable to cross-site request forgery (CSRF) attacks See [Preventing Cross-Site Request Forgery (CSRF) Attacks](preventing-cross-site-request-forgery-csrf-attacks.md).</span></span>

<span data-ttu-id="11fad-137">フォーム認証では、ユーザーの資格情報は暗号化されません。</span><span class="sxs-lookup"><span data-stu-id="11fad-137">Forms authentication does not encrypt the user's credentials.</span></span> <span data-ttu-id="11fad-138">そのため、フォーム認証と SSL を使用しない場合は、安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="11fad-138">Therefore, forms authentication is not secure unless used with SSL.</span></span> <span data-ttu-id="11fad-139">参照してください[Web API の SSL の使用](working-with-ssl-in-web-api.md)します。</span><span class="sxs-lookup"><span data-stu-id="11fad-139">See [Working with SSL in Web API](working-with-ssl-in-web-api.md).</span></span>
