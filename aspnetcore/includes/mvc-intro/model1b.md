---
ms.openlocfilehash: 1c342231905775938715280681ea2b4cf6ebc9bc
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57047939"
---
`Movie` クラスに次のプロパティを追加します。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

`Movie` クラスには次が含まれます。

* データベースで主キー用に必要となる `Id` フィールド。
* `[DataType(DataType.Date)]`:[DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) 属性では、データの型 (`Date`) を指定します。 この属性を使用する場合:

  * ユーザーは日付フィールドに時刻の情報を入力する必要はありません。
  * 日付のみが表示され、時刻の情報は表示されません。

[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) は、後のチュートリアルで説明されます。