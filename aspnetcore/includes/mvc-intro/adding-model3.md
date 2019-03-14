---
ms.openlocfilehash: 3940675548ad7496aab9c720ee0b7fd512bfe029
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57043459"
---

## <a name="test-the-app"></a>アプリのテスト

* アプリを実行し、**[Mvc ムービー]** リンクをタップします。
* **[新規作成]** リンクをタップし、ムービーを作成します。

  ![ジャンル、価格、リリース日、タイトルのフィールドを持つビューを作成します。](~/tutorials/first-mvc-app/adding-model/_static/movies.png)

* `Price` フィールドに小数点またはコンマを入力することはできない場合があります。 小数点にコンマ (",") を使い、英語 (米国) 以外の日付形式を使う英語以外のロケールの [jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する手順を行う必要があります。 詳しくは、[https://github.com/aspnet/Docs/issues/4076](https://github.com/aspnet/Docs/issues/4076) と「[その他の技術情報](#additional-resources)」をご覧ください。 ここでは、単に 10 のような整数を入力します。

<a name="displayformatdatelocal"></a>

* いくつかのロケールでは、日付形式を指定する必要があります。 次の強調表示されたコードを参照してください。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDateFormat.cs?name=snippet_1&highlight=2,10)]

`DataAnnotations` についてはチュートリアルで後ほど説明します。

**[作成]** をタップすると、ムービー情報がデータベースに保存されているサーバーにフォームが送信されます。 アプリは、*/Movies* URL にリダイレクトし、新しく作成したムービー情報が表示されます。

![新しく作成したムービーの一覧を表示したムービー ビュー](~/tutorials/first-mvc-app/adding-model/_static/h.png)

いくつかのムービー エントリを作成します。 **[編集]**、**[詳細]**、および **[削除]** リンクを試してください。すべて機能します。
