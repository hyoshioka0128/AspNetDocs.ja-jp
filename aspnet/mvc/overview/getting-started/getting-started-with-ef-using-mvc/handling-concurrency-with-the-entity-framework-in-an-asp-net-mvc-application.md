---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'チュートリアル: ASP.NET MVC 5 アプリでの EF による同時実行を処理します。'
description: このチュートリアルでは、オプティミスティック同時実行制御を使用して、複数のユーザーが同時に同じエンティティを更新するときの競合を処理する方法を示します。
author: tdykstra
ms.author: riande
ms.topic: tutorial
ms.date: 01/15/2019
ms.assetid: be0c098a-1fb2-457e-b815-ddca601afc65
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 11b1bc316f730e31b4a01924765db3c982783652
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59383019"
---
# <a name="tutorial-handle-concurrency-with-ef-in-an-aspnet-mvc-5-app"></a>チュートリアル: ASP.NET MVC 5 アプリでの EF による同時実行を処理します。

前のチュートリアルでは、データを更新する方法について説明しました。 このチュートリアルでは、オプティミスティック同時実行制御を使用して、複数のユーザーが同時に同じエンティティを更新するときの競合を処理する方法を示します。 使用する web ページを変更する、`Department`エンティティ同時実行エラーを処理するようにします。 次の図は Edit ページと Delete ページのものです。コンカレンシーで競合が発生すると、メッセージが表示されます。

![Department_Edit_page_2_after_clicking_Save](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image10.png)

![Department_Edit_page_2_after_clicking_Save](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image15.png)

このチュートリアルでは、次の作業を行いました。


> [!div class="checklist"]
> * コンカレンシーの競合について学習する
> * オプティミスティック同時実行制御を追加します。
> * 部門のコント ローラーを変更します。
> * テスト同時実行の処理
> * [削除] ページを更新する


## <a name="prerequisites"></a>必須コンポーネント

* [非同期とストアド プロシージャ](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="concurrency-conflicts"></a>コンカレンシーの競合

あるユーザーがあるエンティティのデータを編集目的で表示したとき、別のユーザーが同じエンティティのデータを最初のユーザーの変更がデータベースに書き込まれる前に更新すると、コンカレンシーの競合が発生します。 このような競合の検出を有効にしないと、最後にデータベースを更新したユーザーが他のユーザーの変更を上書きすることになります。 多くのアプリケーションでは、このリスクが許容されています。ユーザーや更新がわずかであれば、あるいは変更が一部上書きされても大きな問題なければ、コンカレンシーのプログラミングにかかるコストが利点よりも重視されることがあります。 その場合、コンカレンシーの競合を処理するようにアプリケーションを構成する必要はありません。

### <a name="pessimistic-concurrency-locking"></a>ペシミスティック同時実行制御 (ロック)

コンカレンシーで偶発的にデータが失われる事態をアプリケーションで回避する必要があれば、その方法としてデータベース ロックがあります。 これは呼び出されます*ペシミスティック同時実行制御*します。 たとえば、データベースから行を読む前に、読み取り専用か更新アクセスでロックを要求します。 更新アクセスで行をロックすると、他のユーザーはその行を読み取り専用または更新アクセスでロックできなくなります。変更中のデータのコピーが与えられるためです。 読み取り専用で行をロックすると、他のユーザーはその行を読み取り専用でロックできますが、更新アクセスではロックできません。

ロックの利用には短所があります。 プログラムが複雑になります。 相当なデータベース管理リソースが必要になります。アプリケーションの利用者数が増えると、パフォーマンス上の問題を引き起こすことがあります。 そのような理由から、一部のデータベース管理システムはペシミスティック コンカレンシーに対応していません。 Entity Framework は、組み込みのサポートしていませんし、チュートリアルのこの方法は説明しませんが実装するためにします。

### <a name="optimistic-concurrency"></a>オプティミスティック コンカレンシー

ペシミスティック同時実行制御の代わりに、*オプティミスティック同時実行制御*します。 オプティミスティック コンカレンシーでは、コンカレンシーの競合の発生を許し、発生したら適切に対処します。 たとえば、John が部門の編集 ページで変更を実行、**予算**$350,000.00 から $0.00 に English 部署の量。

John が前に**保存**、Jane が、同じページの追加と変更を実行、 **Start Date**フィールドを 2007 年 9 月 1 日から 8/8/2013 にします。

John が**保存**最初と認識し Jane、インデックス ページに、ブラウザーが返されるときに、変更をクリックした**保存**します。 この後の動作は、コンカレンシーの競合の処理方法によって決定します。 次のようなオプションがあります。

- ユーザーが変更したプロパティを追跡記録し、それに該当する列だけをデータベースで更新できます。 例のシナリオでは、2 人のユーザーが異なるプロパティを更新したため、データは失われません。 次回の誰かが English 部署を閲覧、John のと Jane の変更が表示されます-2013 年 8 月 8 の開始日および予算が 0 ドルです。

    この更新方法では、データの損失につながる可能性がある競合の数を減らすことができますが、あるエンティティの同じプロパティに対して行われた変更が競合する場合、データの損失は避けられません。 Entity Framework がこのように動作するかどうかは、更新コードの実装方法に依存します。 これは Web アプリケーションの場合、実用的ではない場合が多いです。あるエンティティの新しい値に加え、元のプロパティ値もすべて追跡記録するため、大量のステータスを更新することになるからです。 大量のステータスを更新するとなると、サーバー リソースが必要になるか、Web ページ自体 (非表示フィールドなど) や Cookie に含める必要があるため、アプリケーションのパフォーマンスに影響が出ます。
- Jane の変更の John の変更を上書きすることができます。 次回の誰かが English 部署を閲覧、2013 年 8 月 8 が表示されますが、復元された $350,000.00 の値。 これは *Client Wins* (クライアント側に合わせる) シナリオまたは *Last in Wins* (最終書き込み者優先) シナリオと呼ばれています。 (クライアントからの値がすべて、データ ストアの値より優先されます。)このセクションの冒頭でお伝えしたように、コンカレンシー処理について何のコーディングもしない場合、これが自動的に行われます。
- Jane の変更は、データベースに更新されないようにできます。 通常、エラー メッセージが表示は、データの現在の状態を表示させます、彼女できるようにする必要がある場合は、その変更を再適用できるようにと。 これは *Store Wins* (ストア側に合わせる) シナリオと呼ばれています。 (クライアントが送信した値よりデータストアの値が優先されます。)このチュートリアルでは、Store Wins シナリオを実装します。 この手法では、変更が上書きされるとき、それが必ずユーザーに警告されます。

### <a name="detecting-concurrency-conflicts"></a>同時実行競合の検出

処理することによって競合を解決する[OptimisticConcurrencyException](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx) Entity Framework がスローする例外。 このような例外がスローされるタイミングを認識する目的で、Entity Framework は競合を検出できなければなりません。 そのため、データベースとデータ モデルを適宜構成する必要があります。 競合検出を有効にするためのオプションには次のようなものがあります。

- 行が変更されたタイミングを判断するトラッキング列をデータベース テーブルに追加します。 その列を含めるに Entity Framework を構成することができますし、 `Where` sql 句`Update`または`Delete`コマンド。

    追跡列のデータ型は、通常[rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx)します。 [Rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx)値は、行が更新されるたびにインクリメントが番号が付加されます。 `Update`または`Delete`コマンド、`Where`句にトラッキング列 (最初の行バージョン) の元の値が含まれています。 別のユーザーの値によって更新される行が変更された場合、`rowversion`列は元の値と異なるため、`Update`または`Delete`ステートメントのために更新する行を見つけることができません、`Where`句。 Entity Framework に、行が更新されていないことが見つかると、`Update`または`Delete`(つまり、影響を受けた行の数が 0 である場合) をコマンドを同時実行の競合として解釈します。
- Entity Framework 内のテーブルにすべての列の元の値を含めるを構成、`Where`の句`Update`と`Delete`コマンド。

    最初のオプションで、行が最初に読み取らため、行のすべてのものが変更された場合のように、`Where`句しない行を返すを更新する同時実行の競合として解釈し、Entity Framework。 多くの列を含むデータベース テーブルでは、このアプローチにより、非常に大きな`Where`句、および大量の状態を維持することを要求することができます。 先に述べたように、大量のステータスを保守管理することになると、アプリケーションのパフォーマンスに影響が出ます。 そのため、この手法は一般的には推奨されません。このチュートリアルでも利用しません。

    すべての非主キー プロパティの同時実行制御を追加することで追跡するエンティティをマークする必要がある同時実行するには、このアプローチを実装する場合、 [ConcurrencyCheck](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.concurrencycheckattribute.aspx)属性をします。 Entity Framework、SQL にすべての列を含めることにより`WHERE`の句`UPDATE`ステートメント。

このチュートリアルの残りの部分では追加します、 [rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx)プロパティを追跡、`Department`エンティティが、コント ローラーとビューを作成して、すべてが正常に動作することを確認します。

## <a name="add-optimistic-concurrency"></a>オプティミスティック同時実行制御を追加します。

*Models\Department.cs*、という名前の追跡プロパティを追加`RowVersion`:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs?highlight=20-22)]

[タイムスタンプ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute.aspx)属性は、この列に含まれることを指定します、`Where`の句`Update`と`Delete`データベースに送信されたコマンド。 属性がという名前[タイムスタンプ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute.aspx)以前のバージョンの SQL Server、SQL を使用するため、[タイムスタンプ](https://msdn.microsoft.com/library/ms182776(v=SQL.90).aspx)データ型、SQL に[rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx)に置き換えられます。 用の .Net 型[rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx)バイト配列です。

Fluent API を使用する場合は、使用、 [IsConcurrencyToken](https://msdn.microsoft.com/library/gg679501(v=VS.103).aspx)次の例に示すように、トラッキング プロパティを指定します。

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

プロパティを追加し、データベース モデルを変更したので、別の移行を行う必要があります。 パッケージ マネージャー コンソール (PMC) で、次のコマンドを入力します。

[!code-console[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cmd)]

## <a name="modify-department-controller"></a>部門のコント ローラーを変更します。

*Controllers\DepartmentController.cs*、追加、`using`ステートメント。

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs)]

*DepartmentController.cs*ファイルで、"LastName"の 4 つすべての出現箇所を"FullName"に変更すると、部門管理者のドロップダウン リストには、インストラクターの完全名ではなく姓だけが含まれます。

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs?highlight=1)]

既存のコードを置き換える、 `HttpPost` `Edit`メソッドを次のコード。

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs)]

`FindAsync` が null を返した場合、部署は別のユーザーが削除しています。 コードでは、ポストされたフォーム値を使用して、[編集] ページは、エラー メッセージと共に再表示できるように、部署エンティティを作成します。 あるいは、部署フィールドを再表示せず、エラー メッセージのみを表示するのであれば、部署エンティティを再作成する必要はないでしょう。

ビュー、元の格納`RowVersion`で非表示のフィールドとメソッドの値が受信、`rowVersion`パラメーター。 `SaveChanges` を呼び出す前に、エンティティの `OriginalValues` コレクションにその元の `RowVersion` プロパティ値を置く必要があります。 SQL を作成と、Entity Framework`UPDATE`コマンド、コマンドが含まれる、`WHERE`元を含む行を検索する句`RowVersion`値。

行に影響がない場合、`UPDATE`コマンド (行には、元のあるありません`RowVersion`値)、Entity Framework がスローされます、`DbUpdateConcurrencyException`例外、およびコードでは、`catch`ブロックを取得、影響を受ける`Department`例外からのエンティティオブジェクト。

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs)]

このオブジェクトでユーザーが入力した新しい値にはその`Entity`プロパティは、呼び出すことによって、データベースから読み取られた値を取得できます、`GetDatabaseValues`メソッド。

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]

`GetDatabaseValues`人には、データベースから行が削除しない場合は null を返します。 それ以外の場合、返されたオブジェクトにキャストする必要がある、`Department`クラスにアクセスするために、`Department`プロパティ。 (既に削除対象としてチェックするため、`databaseEntry`部門が後に削除された場合にのみ、null になります`FindAsync`を実行する前に`SaveChanges`を実行します)。

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs)]

次に、コードの編集 ページで、ユーザーが入力した異なるデータベース値を持つ各列のカスタム エラー メッセージを追加します。

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs)]

長いエラー メッセージは、変更点と、それについての対処方法について説明します。

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.cs)]

コードの最後に、設定、`RowVersion`の値、`Department`データベースからオブジェクトを新しい値を取得します。 Edit ページが再表示されるとき、この新しい `RowVersion` 値が非表示フィールドに保存されます。今度ユーザーが **[保存]** をクリックすると、Edit ページの再表示後に発生したコンカレンシー エラーのみが取得されます。

*Views\Department\Edit.cshtml*、保存する非表示フィールドを追加、`RowVersion`用の隠しフィールドのすぐ後、プロパティの値、`DepartmentID`プロパティ。

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample12.cshtml?highlight=18)]

## <a name="test-concurrency-handling"></a>テスト同時実行の処理

サイトを実行し、をクリックして**部門**します。

右クリックして、**編集**ハイパーリンクをクリックし、English 部署**新しいタブで開く**順にクリックして、**編集**English 部署のハイパーリンクです。 2 つのタブには、同じ情報が表示されます。

最初のブラウザー タブでフィールドを変更し、**[保存]** をクリックします。

値が変更された Index ページがブラウザーに表示されます。

2 番目のブラウザー タブでフィールドを変更し、クリックして**保存**します。 エラー メッセージが表示されます。

![Department_Edit_page_2_after_clicking_Save](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image10.png)

**[保存]** をもう一度クリックします。 2 番目のブラウザー タブで入力した値は、最初のブラウザーで変更されたデータの元の値と共に保存されます。 Index ページが表示されると、保存した値を確認できます。

## <a name="update-the-delete-page"></a>Delete ページを更新する

Delete ページの場合、Entity Framework は、同様の方法で部署を編集している他のユーザーが起こしたコンカレンシーの競合を検出します。 ときに、 `HttpGet` `Delete`メソッドが確定ビューが表示されます、ビュー、元に含まれる`RowVersion`非表示フィールドの値。 値が使用できる、そのこと、 `HttpPost` `Delete`ユーザーが、削除するときに呼び出されるメソッド。 Entity Framework で SQL を作成するときに`DELETE`コマンドが含まれています、`WHERE`句と元`RowVersion`値。 行が 0 で、コマンドの結果に (つまり、削除の確認ページが表示された後、行が変更された) が影響を受ける場合は、同時実行例外がスローされます、および`HttpGet Delete`エラー フラグを設定するメソッドが呼び出された`true`再表示するには、エラー メッセージの確認 ページ。 別のエラー メッセージが表示される場合は、行が別のユーザーによって削除されたため、0 行が影響を受けたこともできます。

*DepartmentController.cs*、置換、 `HttpGet` `Delete`メソッドを次のコード。

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample13.cs)]

このメソッドは、コンカレンシー エラー後にページが再表示されたかどうかを示すオプション パラメーターを受け取ります。 このフラグが場合`true`、ビューを使用して、エラー メッセージを送信、`ViewBag`プロパティ。

コードに置き換えます、 `HttpPost` `Delete`メソッド (名前付き`DeleteConfirmed`) を次のコード。

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample14.cs)]

置き換えたスキャフォールディングされたコードで、このメソッドがレコード ID を 1 つだけ受け取りました。

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample15.cs)]

このパラメーターを変更して、`Department`モデル バインダーによって作成されたエンティティ インスタンス。 これによりへのアクセス、`RowVersion`レコード キーに加え、プロパティの値。

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample16.cs)]

また、アクション メソッドの名前を `DeleteConfirmed` から `Delete` に変更しています。 という名前のスキャフォールディングされたコード、 `HttpPost` `Delete`メソッド`DeleteConfirmed`提供する、`HttpPost`メソッドに一意のシグネチャ。 (CLR にオーバー ロードされたメソッドが、さまざまなメソッド パラメーターが必要です)。シグネチャが一意ではこれでは、MVC 規則に従うし、に同じ名前を使用、`HttpPost`と`HttpGet`メソッドを削除します。

コンカレンシー エラーがキャッチされた場合、このコードは削除確認ページを再表示し、コンカレンシー エラー メッセージを表示するかどうかを示すフラグを提供します。

*Views\Department\Delete.cshtml*、スキャフォールディングされたコードをエラー メッセージ フィールドと DepartmentID および RowVersion プロパティの非表示フィールドを追加します。 次のコードに置き換えます。 変更が強調表示されます。

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample17.cshtml?highlight=9-10,21,52-54)]

このコードは、間にエラー メッセージを追加、`h2`と`h3`見出し。

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample18.cshtml)]

置き換える`LastName`で`FullName`で、`Administrator`フィールド。

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample19.cshtml)]

最後に、非表示フィールドを追加、`DepartmentID`と`RowVersion`プロパティの後、`Html.BeginForm`ステートメント。

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample20.cshtml)]

Departments Index ページを実行します。 右クリックして、**削除**ハイパーリンクをクリックし、English 部署**新しいタブで開く**最初のタブをクリックして、**編集**English 部署のハイパーリンクです。

最初のウィンドウで、値のいずれかを変更し、をクリックして**保存**します。

インデックス ページが変更を確認します。

2 番目のタブで **[削除]** をクリックします。

コンカレンシー エラー メッセージが表示されます。Department 値がデータベースの現在の内容で更新されています。

![Department_Delete_confirmation_page_with_concurrency_error](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image15.png)

**[削除]** をもう一度クリックすると、Index ページにリダイレクトされます。Index ページには、部署が削除されていることが表示されます。

## <a name="get-the-code"></a>コードを取得する

[完成したプロジェクトをダウンロードします。](https://webpifeed.blob.core.windows.net/webpifeed/Partners/ASP.NET%20MVC%20Application%20Using%20Entity%20Framework%20Code%20First.zip)

## <a name="additional-resources"></a>その他の技術情報

その他の Entity Framework リソースへのリンクが記載されて、 [ASP.NET データ アクセス - 推奨リソース](../../../../whitepapers/aspnet-data-access-content-map.md)します。

同時実行のさまざまなシナリオを処理する他の方法については、次を参照してください。[オプティミスティック同時実行パターン](https://msdn.microsoft.com/data/jj592904)と[プロパティの値を操作](https://msdn.microsoft.com/data/jj592677)msdn です。 次のチュートリアル用の table-per-hierarchy 継承を実装する方法を示しています、`Instructor`と`Student`エンティティ。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * コンカレンシーの競合について学習した
> * 追加したオプティミスティック同時実行制御
> * 変更後の Department コント ローラー
> * テスト同時実行処理
> * Delete ページを更新した

データ モデルで継承を実装する方法については、次の記事に進んでください。
> [!div class="nextstepaction"]
> [データ モデルで継承を実装します。](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application.md)
