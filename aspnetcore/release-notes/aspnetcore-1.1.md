---
title: ASP.NET Core 1.1 の新機能
author: rick-anderson
description: ASP.NET Core 1.1 の新機能について説明します。
ms.author: riande
ms.date: 12/18/2018
uid: aspnetcore-1.1
ms.openlocfilehash: a21b2b82bfefb5b24898cf58ef97284a143e2e4a
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57060609"
---
# <a name="whats-new-in-aspnet-core-11"></a><span data-ttu-id="fe87e-103">ASP.NET Core 1.1 の新機能</span><span class="sxs-lookup"><span data-stu-id="fe87e-103">What's new in ASP.NET Core 1.1</span></span>

<span data-ttu-id="fe87e-104">ASP.NET Core 1.1 には次の新機能があります。</span><span class="sxs-lookup"><span data-stu-id="fe87e-104">ASP.NET Core 1.1 includes the following new features:</span></span>

- [<span data-ttu-id="fe87e-105">URL リライト ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="fe87e-105">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting)
- [<span data-ttu-id="fe87e-106">応答キャッシュ ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="fe87e-106">Response Caching Middleware</span></span>](xref:performance/caching/middleware)
- [<span data-ttu-id="fe87e-107">タグ ヘルパーとしてのビュー コンポーネント</span><span class="sxs-lookup"><span data-stu-id="fe87e-107">View Components as Tag Helpers</span></span>](xref:mvc/views/view-components#invoking-a-view-component-as-a-tag-helper)
- [<span data-ttu-id="fe87e-108">MVC フィルターとしてのミドルウェア</span><span class="sxs-lookup"><span data-stu-id="fe87e-108">Middleware as MVC filters</span></span>](xref:mvc/controllers/filters#using-middleware-in-the-filter-pipeline)
- [<span data-ttu-id="fe87e-109">Cookie ベースの TempData プロバイダー</span><span class="sxs-lookup"><span data-stu-id="fe87e-109">Cookie-based TempData provider</span></span>](xref:fundamentals/app-state#tempdata)
- [<span data-ttu-id="fe87e-110">Azure App Service ログ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="fe87e-110">Azure App Service logging provider</span></span>](xref:fundamentals/logging/index#azure-app-service-provider)
- [<span data-ttu-id="fe87e-111">Azure Key Vault 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="fe87e-111">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration)
- [<span data-ttu-id="fe87e-112">Azure と Redis のストレージ データ保護キー リポジトリ</span><span class="sxs-lookup"><span data-stu-id="fe87e-112">Azure and Redis Storage Data Protection Key Repositories</span></span>](xref:security/data-protection/implementation/key-storage-providers#azure-and-redis)
- <span data-ttu-id="fe87e-113">Windows 用 WebListener サーバー</span><span class="sxs-lookup"><span data-stu-id="fe87e-113">WebListener Server for Windows</span></span>
- [<span data-ttu-id="fe87e-114">WebSocket のサポート</span><span class="sxs-lookup"><span data-stu-id="fe87e-114">WebSockets support</span></span>](xref:fundamentals/websockets)

## <a name="choosing-between-versions-10-and-11-of-aspnet-core"></a><span data-ttu-id="fe87e-115">ASP.NET Core のバージョン 1.0 と 1.1 の選択</span><span class="sxs-lookup"><span data-stu-id="fe87e-115">Choosing between versions 1.0 and 1.1 of ASP.NET Core</span></span>

<span data-ttu-id="fe87e-116">ASP.NET Core 1.1 には 1.0 より多くの機能があります。</span><span class="sxs-lookup"><span data-stu-id="fe87e-116">ASP.NET Core 1.1 has more features than 1.0.</span></span> <span data-ttu-id="fe87e-117">一般に、最新バージョンを使うことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="fe87e-117">In general, we recommend you use the latest version.</span></span>

## <a name="additional-information"></a><span data-ttu-id="fe87e-118">追加情報</span><span class="sxs-lookup"><span data-stu-id="fe87e-118">Additional Information</span></span>

- [<span data-ttu-id="fe87e-119">ASP.NET Core 1.1.0 リリース ノート</span><span class="sxs-lookup"><span data-stu-id="fe87e-119">ASP.NET Core 1.1.0 Release Notes</span></span>](https://github.com/aspnet/Home/releases/tag/1.1.0)
- <span data-ttu-id="fe87e-120">ASP.NET Core 開発チームの進捗状況や計画とつながるには、週次の [ASP.NET Community Standup](https://live.asp.net/) にアクセスしてください。</span><span class="sxs-lookup"><span data-stu-id="fe87e-120">To connect with the ASP.NET Core development team's progress and plans, tune in to the [ASP.NET Community Standup](https://live.asp.net/).</span></span>
