---
ms.openlocfilehash: 934db70fe49ba9a5330b25596dc694e0ac50c04e
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57037899"
---
<span data-ttu-id="63bfb-101">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="63bfb-101">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="63bfb-102">このセクションでは、データベースのムービーを管理するクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="63bfb-102">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="63bfb-103">データベースで使用できるよう、これらのクラスは [Entity Framework Core](/ef/core) (EF Core) を使用します。</span><span class="sxs-lookup"><span data-stu-id="63bfb-103">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="63bfb-104">EF Core は、記述するデータ アクセス コードを簡略化するオブジェクト リレーショナル マッピング (ORM) フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="63bfb-104">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="63bfb-105">作成するモデル クラスは、EF Core に対する依存関係がないために、POCO クラス (plain-old CLR オブジェクト、つまり単純な従来の CLR) と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="63bfb-105">The model classes you create are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="63bfb-106">これらは、データベースに格納されるデータのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="63bfb-106">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="63bfb-107">このチュートリアルでは、まずモデル クラスを記述し、EF コアによってデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="63bfb-107">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span> <span data-ttu-id="63bfb-108">ここで取り上げていない別の方法は、[既存のデータベースからモデル クラスを生成する](/ef/core/get-started/aspnetcore/existing-db)方法です。</span><span class="sxs-lookup"><span data-stu-id="63bfb-108">An alternate approach not covered here is to [generate model classes from an existing database](/ef/core/get-started/aspnetcore/existing-db).</span></span>

<span data-ttu-id="63bfb-109">サンプルを[表示またはダウンロード](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie)します。</span><span class="sxs-lookup"><span data-stu-id="63bfb-109">[View or download](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie) sample.</span></span>
