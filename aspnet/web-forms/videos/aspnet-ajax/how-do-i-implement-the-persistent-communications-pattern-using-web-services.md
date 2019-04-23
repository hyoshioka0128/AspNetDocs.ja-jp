---
uid: web-forms/videos/aspnet-ajax/how-do-i-implement-the-persistent-communications-pattern-using-web-services
title: '[How Do i:]Web サービスを使用して、永続的な通信パターンを実装するか。 | Microsoft Docs'
author: JoeStagner
description: 従来の Web サイトでは、進行中の通信は維持されません、ブラウザーとサーバーが、機能を実行するユーザーへの応答でのみ通信しています.
ms.author: riande
ms.date: 08/22/2007
ms.assetid: 424c06cd-6d61-43cd-a1f2-d1a6b62e47b1
msc.legacyurl: /web-forms/videos/aspnet-ajax/how-do-i-implement-the-persistent-communications-pattern-using-web-services
msc.type: video
ms.openlocfilehash: de2eb281cd4bab46635af480ac2e8f07f60f1591
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59408109"
---
# <a name="how-do-i-implement-the-persistent-communications-pattern-using-web-services"></a><span data-ttu-id="7f743-104">[How Do i:]Web サービスを使用して、永続的な通信パターンを実装するか。</span><span class="sxs-lookup"><span data-stu-id="7f743-104">[How Do I:] Implement the Persistent Communications Pattern using Web Services?</span></span>

<span data-ttu-id="7f743-105">によって[Joe Stagner](https://github.com/JoeStagner)</span><span class="sxs-lookup"><span data-stu-id="7f743-105">by [Joe Stagner](https://github.com/JoeStagner)</span></span>

<span data-ttu-id="7f743-106">従来の Web サイトで、ブラウザーとサーバーに、進行中の通信は維持されませんが、アクションを実行するユーザーへの応答でのみ通信。</span><span class="sxs-lookup"><span data-stu-id="7f743-106">In a traditional Web site the browser and the server do not maintain an ongoing communication, but communicate only in response to the user performing an action.</span></span> <span data-ttu-id="7f743-107">アプリケーション コンテナーをページになる最新の Web サイトでは、ブラウザーとページの更新プログラムがユーザーに操作を実行することがなく発生することができるように、進行中の通信を維持するために、サーバーにとって得策ですができます。</span><span class="sxs-lookup"><span data-stu-id="7f743-107">In a modern Web site where the page becomes an application container, it can be advantageous for the browser and the server to maintain an ongoing communication so that page updates can occur without the user performing an action.</span></span> <span data-ttu-id="7f743-108">これは、AJAX の永続的な通信パターンと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="7f743-108">This is known as the Persistent Communications Pattern for AJAX.</span></span> <span data-ttu-id="7f743-109">ASP.NET AJAX では、永続的な通信パターンを実装する Web 開発者向けの 2 つの主な方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="7f743-109">ASP.NET AJAX provides two main ways for Web developers to implement the Persistent Communications Pattern.</span></span> <span data-ttu-id="7f743-110">以前のビデオでは、実装の基礎として、ASP.NET AJAX UpdatePanel を使用する方法を説明しました。</span><span class="sxs-lookup"><span data-stu-id="7f743-110">In an earlier video we saw how to use the ASP.NET AJAX UpdatePanel as the basis of the implementation.</span></span> <span data-ttu-id="7f743-111">このビデオでは、ASP.NET AJAX UpdatePanel の必要性が削除され、Web サービスに JavaScrpt 呼び出しを使用して、同じパターンを実装する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="7f743-111">In this video we learn how to implement the same pattern using a JavaScrpt call to a Web service, which removes the need for an ASP.NET AJAX UpdatePanel.</span></span>

[<span data-ttu-id="7f743-112">&#9654;ビデオ (16 分)</span><span class="sxs-lookup"><span data-stu-id="7f743-112">&#9654; Watch video (16 minutes)</span></span>](https://channel9.msdn.com/Blogs/ASP-NET-Site-Videos/how-do-i-implement-the-persistent-communications-pattern-using-web-services)

> [!div class="step-by-step"]
> <span data-ttu-id="7f743-113">[前へ](how-do-i-localize-an-aspnet-ajax-application.md)
> [次へ](how-do-i-trigger-an-updatepanel-refresh-from-a-dropdownlist-control.md)</span><span class="sxs-lookup"><span data-stu-id="7f743-113">[Previous](how-do-i-localize-an-aspnet-ajax-application.md)
[Next](how-do-i-trigger-an-updatepanel-refresh-from-a-dropdownlist-control.md)</span></span>
