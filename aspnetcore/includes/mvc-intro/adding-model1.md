---
ms.openlocfilehash: 72e33ea44976963193d2560427fc418875be450e
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57038869"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a>ASP.NET Core MVC アプリへのモデルの追加

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Tom Dykstra](https://github.com/tdykstra)

このセクションでは、データベースのムービーを管理するクラスをいくつか追加します。 これらのクラスは、**M**VC アプリの "**モ**デル" 部分です。

[Entity Framework Core](/ef/core) (EF Core) でこれらのクラスを使用して、データベースを操作します。 EF Core は、記述する必要があるデータ アクセス コードを簡略化するオブジェクト リレーショナル マッピング (ORM) フレームワークです。 [EF Core では多くのデータベース エンジンがサポートされます](/ef/core/providers/)。

作成するモデル クラスは、EF Core に対する依存関係がないために、POCO ("単純な従来の CLR オブジェクト") クラスと呼ばれます。 これらは単に、データベースに格納されるデータのプロパティを定義します。

このチュートリアルでは、まず、モデル クラスを記述します。データベースは EF コアによって作成されます。 ここで取り上げていない別の方法では、既存のデータベースからモデル クラスを生成します。 その方法については、[ASP.NET Core の既存のデータベース](/ef/core/get-started/aspnetcore/existing-db)に関するページを参照してください。

## <a name="add-a-data-model-class"></a>データ モデル クラスの追加
