---
title: ASP.NET Core MVC アプリでの SQL の操作
author: rick-anderson
description: ASP.NET Core MVC アプリ内で SQL Server LocalDB または SQLite を使用する方法について説明します。
ms.author: riande
ms.date: 03/07/2017
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: 3757b972694a41cb87beb8ebee818cd498be6764
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57034679"
---
# <a name="work-with-sql-in-aspnet-core"></a>ASP.NET Core での SQL の操作

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

`MvcMovieContext` オブジェクトは、データベースへの接続と、データベース レコードへの `Movie` オブジェクトのマッピングのタスクを処理します。 データベース コンテキストは、*Startup.cs* ファイルの `ConfigureServices` メソッドで[依存性の注入](xref:fundamentals/dependency-injection)コンテナーに登録されます。

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

ASP.NET Core の[構成](xref:fundamentals/configuration/index)システムは `ConnectionString` を読み取ります。 ローカルで開発する場合は、*appsettings.json* ファイルから接続文字列を取得します。

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

<!-- Code -------------------------->
# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

ASP.NET Core の[構成](xref:fundamentals/configuration/index)システムは `ConnectionString` を読み取ります。 ローカルで開発する場合は、*appsettings.json* ファイルから接続文字列を取得します。

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---  
<!-- End of VS tabs -->

テストまたは実稼働サーバーにアプリを配置する場合は、環境変数または別の方法を使用して、実際の SQL Server に接続文字列を設定できます。 詳細については、[構成](xref:fundamentals/configuration/index)に関するページを参照してください。

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB は、プログラム開発を対象にした、SQL Server Express データベース エンジンの軽量版です。 LocalDB は要求時に開始され、ユーザー モードで実行されるため、複雑な構成はありません。 既定では、LocalDB データベースによって *C:/Users/{user}* ディレクトリに *.mdf* ファイルが作成されます。

* **[表示]** メニューの **[SQL Server オブジェクト エクスプローラー]** (SSOX) を開きます。

  ![[View] メニュー](working-with-sql/_static/ssox.png)

* `Movie` テーブルを右クリックして **[デザイナーの表示]** を選択します。

  ![Movie テーブルで開かれたコンテキスト メニュー](working-with-sql/_static/design.png)

  ![デザイナーに開かれた Movie テーブル](working-with-sql/_static/dv.png)

`ID` の横のキー アイコンに注意してください。 既定では、EF は `ID` という名前のプロパティを主キーにします。

* `Movie` テーブルを右クリックして **[データの表示]** を選択します。

  ![Movie テーブルで開かれたコンテキスト メニュー](working-with-sql/_static/ssox2.png)

  ![開いた Movie テーブルにテーブル データが表示されています](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]

---  
<!-- End of VS tabs -->

## <a name="seed-the-database"></a>データベースのシード

*Models* フォルダーに `SeedData` という名前の新しいクラスを作成します。 生成されたコードを次のコードに置き換えます。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

DB にムービーがある場合、シード初期化子が返され、ムービーは追加されません。

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>
### <a name="add-the-seed-initializer"></a>シード初期化子の追加

*Program.cs* の内容を次のコードで置き換えます。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

アプリのテスト

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* DB 内のすべてのレコードを削除します。 これはブラウザーの削除リンクで行うか、SSOX から行うことができます。
* アプリを強制的に初期化して (`Startup` クラスでメソッドを呼び出す)、シード メソッドが実行されるようにします。 強制的に初期化するには、IIS Express を停止してから再起動する必要があります。 これは次の方法のいずれかを使用して行うことができます。

  * 通知領域の IIS Express システム トレイ アイコンを右クリックし、**[終了]** または **[サイトの停止]** をタップします。

    ![IIS Express システム トレイ アイコン](working-with-sql/_static/iisExIcon.png)

    ![コンテキスト メニュー](working-with-sql/_static/stopIIS.png)

    * 非デバッグ モードで VS を実行していた場合は、F5 キーを押してデバッグ モードで実行します。
    * デバッグ モードで VS を実行していた場合は、デバッガーを停止して、F5 キーを押します。

<!-- Code -------------------------->
# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

DB 内のすべてのレコードを削除します (そのため Seed メソッドが実行されます)。 アプリを停止および起動して、データベースをシードします。

---  
<!-- End of VS tabs -->

アプリにシードされたデータが表示されます。

![ムービー データが表示された、Microsoft Edge で開かれている MVC ムービー アプリケーション](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> [前へ](adding-model.md)
> [次へ](controller-methods-views.md)  
