---
ms.openlocfilehash: d7ef9b11af8ee11e4ec84404f8cdeb0b89384a3c
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57130451"
---
## <a name="forward-request-information-with-a-proxy-or-load-balancer"></a><span data-ttu-id="812bc-101">転送プロキシ情報を要求またはロード バランサー</span><span class="sxs-lookup"><span data-stu-id="812bc-101">Forward request information with a proxy or load balancer</span></span>

<span data-ttu-id="812bc-102">プロキシ サーバーまたはロード バランサーの背後にあるアプリを展開する場合、要求ヘッダーでアプリを元の要求情報の一部を転送する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="812bc-102">If the app is deployed behind a proxy server or load balancer, some of the original request information might be forwarded to the app in request headers.</span></span> <span data-ttu-id="812bc-103">この情報には、セキュリティで保護された要求スキームには、通常が含まれます (`https`)、ホスト、およびクライアントの IP アドレス。</span><span class="sxs-lookup"><span data-stu-id="812bc-103">This information usually includes the secure request scheme (`https`), host, and client IP address.</span></span> <span data-ttu-id="812bc-104">アプリでは、探索し、元の要求情報を使用して、これらの要求ヘッダーを自動的に読み取りますはありません。</span><span class="sxs-lookup"><span data-stu-id="812bc-104">Apps don't automatically read these request headers to discover and use the original request information.</span></span>

<span data-ttu-id="812bc-105">スキームは、外部プロバイダーを使用した認証フローに影響するリンクの生成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="812bc-105">The scheme is used in link generation that affects the authentication flow with external providers.</span></span> <span data-ttu-id="812bc-106">セキュリティで保護されたスキームが失われる (`https`) が正しくない安全でないリダイレクト Url を生成するアプリで発生します。</span><span class="sxs-lookup"><span data-stu-id="812bc-106">Losing the secure scheme (`https`) results in the app generating incorrect insecure redirect URLs.</span></span>

<span data-ttu-id="812bc-107">元の要求情報を要求の処理用のアプリに使用できるようにするのにには、Forwarded Headers Middleware を使用します。</span><span class="sxs-lookup"><span data-stu-id="812bc-107">Use Forwarded Headers Middleware to make the original request information available to the app for request processing.</span></span>

<span data-ttu-id="812bc-108">詳細については、「 <xref:host-and-deploy/proxy-load-balancer> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="812bc-108">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>
