---
ms.openlocfilehash: 72e33ea44976963193d2560427fc418875be450e
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57038869"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="452ad-101">ASP.NET Core MVC アプリへのモデルの追加</span><span class="sxs-lookup"><span data-stu-id="452ad-101">Add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="452ad-102">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="452ad-102">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="452ad-103">このセクションでは、データベースのムービーを管理するクラスをいくつか追加します。</span><span class="sxs-lookup"><span data-stu-id="452ad-103">In this section, you'll add some classes for managing movies in a database.</span></span> <span data-ttu-id="452ad-104">これらのクラスは、**M**VC アプリの "**モ**デル" 部分です。</span><span class="sxs-lookup"><span data-stu-id="452ad-104">These classes will be the "**M**odel" part of the **M**VC app.</span></span>

<span data-ttu-id="452ad-105">[Entity Framework Core](/ef/core) (EF Core) でこれらのクラスを使用して、データベースを操作します。</span><span class="sxs-lookup"><span data-stu-id="452ad-105">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="452ad-106">EF Core は、記述する必要があるデータ アクセス コードを簡略化するオブジェクト リレーショナル マッピング (ORM) フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="452ad-106">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span> <span data-ttu-id="452ad-107">[EF Core では多くのデータベース エンジンがサポートされます](/ef/core/providers/)。</span><span class="sxs-lookup"><span data-stu-id="452ad-107">[EF Core supports many database engines](/ef/core/providers/).</span></span>

<span data-ttu-id="452ad-108">作成するモデル クラスは、EF Core に対する依存関係がないために、POCO ("単純な従来の CLR オブジェクト") クラスと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="452ad-108">The model classes you'll create are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="452ad-109">これらは単に、データベースに格納されるデータのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="452ad-109">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="452ad-110">このチュートリアルでは、まず、モデル クラスを記述します。データベースは EF コアによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="452ad-110">In this tutorial you'll write the model classes first, and EF Core will create the database.</span></span> <span data-ttu-id="452ad-111">ここで取り上げていない別の方法では、既存のデータベースからモデル クラスを生成します。</span><span class="sxs-lookup"><span data-stu-id="452ad-111">An alternate approach not covered here is to generate model classes from an already-existing database.</span></span> <span data-ttu-id="452ad-112">その方法については、[ASP.NET Core の既存のデータベース](/ef/core/get-started/aspnetcore/existing-db)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="452ad-112">For information about that approach, see [ASP.NET Core - Existing Database](/ef/core/get-started/aspnetcore/existing-db).</span></span>

## <a name="add-a-data-model-class"></a><span data-ttu-id="452ad-113">データ モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="452ad-113">Add a data model class</span></span>
