---
title: ASP.NET Core で Razor ページに新しいファイルを追加する
author: rick-anderson
description: Entity Framework Core を使用した Razor ページへの新しいフィールドの追加方法
ms.author: riande
ms.custom: mvc
ms.date: 12/5/2018
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 98de39c63c992dce7d60563df316d848339b811a
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57050419"
---
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a>ASP.NET Core で Razor ページに新しいファイルを追加する

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[](~/includes/rp/download.md)]

このセクションでは [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations を次の目的で使用します。

* モデルに新しいフィールドを追加する。
* 新しいフィールド スキーマの変更をデータベースに移行する。

EF Code First を使用してデータベースを自動的に作成する場合、Code First では次が実行されます。

* テーブルをデータベースに追加し、データベースのスキーマが生成元のモデル クラスと同期しているかを追跡します。
* モデル クラスがデータベースと同期されていない場合、EF は例外をスローします。

同期中のスキーマ/モデルが自動的に検証されるようにすると、整合性のないデータベース/コードの問題を発見しやすくなります。

## <a name="adding-a-rating-property-to-the-movie-model"></a>ムービー モデルへの評価プロパティの追加

*Models/Movie.cs* ファイルを開き、`Rating` プロパティを追加します。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

アプリをビルドします。

*Pages/Movies/Index.cshtml* を編集し、`Rating` フィールドを追加します。

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml.?highlight=40-42,61-63)]

次のページを更新します。

* [削除] と [詳細] ページに、`Rating` フィールドを追加します。
* [Create.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) を `Rating` フィールドを使用して更新します。
* [編集] ページに、`Rating` フィールドを追加します。

DB を更新して新しいフィールドが含まれるようになるまでアプリは動作しません。 ここで実行すると、アプリによって `SqlException` がスローされます。

`SqlException: Invalid column name 'Rating'.`

このエラーが表示されるのは、更新された Movie モデル クラスがデータベースの Movie テーブルのスキーマと異なるためです。 (データベース テーブルに `Rating` 列はありません)。

このエラーを解決するための手法がいくつかあります。

1. Entity Framework に、新しいモデル クラス スキーマを使用してデータベースを自動的にドロップさせ、再作成させます。 この手法は、開発周期の早い段階で便利です。モデルとデータベース スキーマを一緒に短期間で発展させることができます。 これの欠点は、データベースで既存のデータが失われることです。 実稼働データベースでこの方法は使用しないでください。 スキーマの変更時に DB を削除し、初期化子を使用して、データベースにテスト データを自動的にシードすることは、多くの場合、アプリケーションを開発する上で有益な方法となります。

2. モデル クラスに一致するように、既存のデータベースのスキーマを明示的に変更します。 この手法の長所は、データが維持されることです。 この変更は手動で行うことも、データベース変更スクリプトを作成して行うこともできます。

3. Code First Migrations を使用して、データベース スキーマを更新します。

このチュートリアルでは、Code First Migrations を利用します。

新しい列に値を提供するように、`SeedData` クラスを更新します。 下に変更のサンプルがありますが、`new Movie` ブロックごとにこの変更を行ってください。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

[完成した SeedData.cs ファイル](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs)を参照してください。

ソリューションをビルドします。

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>評価フィールドの移行を追加する

**[ツール]** メニューで、**[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に選択します。
PMC で、次のコマンドを入力します。

```powershell
Add-Migration Rating
Update-Database
```

`Add-Migration` コマンドにより、フレームワークに次の指示があります。

* `Movie` モデルを、`Movie` DB のスキーマと比較します。
* DB スキーマを新しいモデルに移行するコードを作成します。

"Rating (評価)" という名前は任意です。移行ファイルに名前を付けるために利用されます。 移行ファイルには意味のある名前を使用すると便利です。

フレームワークは、データベースにスキーマ変更を適用するように、`Update-Database` コマンドから指示されます。

<a name="ssox"></a>

DB 内のすべてのレコードを削除すると、初期化子は DB にデータを初期投入し、`Rating` フィールドを追加します。 これはブラウザーの削除リンクで行うか、[Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX) から行うことができます。

別のオプションとしては、データベースを削除してから、移行を使用することで、データベースを再作成することもできます。 SSOX 内でデータベースを削除するには: 

* SSOX でデータベースを選択します。
* データベースを右クリックし、*[削除]* を選択します。
* **[既存の接続を閉じる]** をチェックします。
* **[OK]** を選択します。
* [PMC](xref:tutorials/razor-pages/new-field#pmc) でデータベースを更新します。

  ```powershell
  Update-Database
  ```

<!-- Code -------------------------->
# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

<!-- copy/paste this tab to the next. Not worth an include  -->

次の .NET Core CLI コマンドを実行します。

```console
dotnet ef migrations add Rating
dotnet ef database update
```

`ef migrations add` コマンドにより、フレームワークに次の指示があります。

* `Movie` モデルを、`Movie` DB のスキーマと比較します。
* DB スキーマを新しいモデルに移行するコードを作成します。

"Rating (評価)" という名前は任意です。移行ファイルに名前を付けるために利用されます。 移行ファイルには意味のある名前を使用すると便利です。

フレームワークは、データベースにスキーマ変更を適用するように、`ef database update` コマンドから指示されます。

DB 内のすべてのレコードを削除すると、初期化子は DB にデータを初期投入し、`Rating` フィールドを追加します。 これを行うには、ブラウザー内の削除リンクを使用することも、SQLite ツールを使用することもできます。

別のオプションとしては、データベースを削除してから、移行を使用することで、データベースを再作成することもできます。 データベースを削除するには、データベース ファイル (*MvcMovie.db*) を削除します。 次に、`ef database update` コマンドを実行します。 

```console
dotnet ef database update
```

> [!NOTE]
> スキーマ変更操作の多くは、EF Core の SQLite プロバイダーによってサポートされていません。 たとえば、列の追加はサポートされていますが、列の削除はサポートされていません。 移行を追加することで列を削除する場合、`ef migrations add` コマンドは成功しますが、`ef database update` コマンドは失敗します。 制限事項の一部を解決するには、手動で移行コードを記述してテーブルのリビルドを実行します。 テーブルのリビルドには、既存のテーブルの名前変更、新しいテーブルの作成、新しいテーブルへのデータのコピー、および古いテーブルの削除が必要です。 詳細については、次のリソースを参照してください。
> * [SQLite EF Core データベース プロバイダーの制限事項](/ef/core/providers/sqlite/limitations)
> * [移行コードをカスタマイズする](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [データのシード処理](/ef/core/modeling/data-seeding)

---  
<!-- End of VS tabs -->

アプリを実行し、`Rating` フィールドでムービーを作成、編集、表示できることを確認します。 データベースがシード処理されない場合は、`SeedData.Initialize` メソッドでブレークポイントを設定します。

> [!div class="step-by-step"]
> [前へ:検索の追加](xref:tutorials/razor-pages/search)
> [次へ: 検証の追加](xref:tutorials/razor-pages/validation)
