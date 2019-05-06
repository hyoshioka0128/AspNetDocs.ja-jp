---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application
title: 'チュートリアル: MVC 5 Web アプリの高度な EF のシナリオについて説明します'
description: このチュートリアルでは、Entity Framework Code First を使用する ASP.NET web アプリケーションの開発の基本機能を越えて移動するときの注意すべきに役立ついくつかのトピックについて紹介します。
author: tdykstra
ms.author: riande
ms.date: 01/22/2019
ms.topic: tutorial
ms.assetid: f35a9b0c-49ef-4cde-b06d-19d1543feb0b
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application
msc.type: authoredcontent
ms.openlocfilehash: d7cc83a5b78a60f575f5c3065079679189296a0c
ms.sourcegitcommit: 289e051cc8a90e8f7127e239fda73047bde4de12
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58425276"
---
# <a name="tutorial-learn-about-advanced-ef-scenarios-for-an-mvc-5-web-app"></a>チュートリアル: MVC 5 Web アプリの高度な EF のシナリオについて説明します

前のチュートリアルでは、table-per-hierarchy 継承を実装しました。 このチュートリアルでは、Entity Framework Code First を使用する ASP.NET web アプリケーションの開発の基本機能を越えて移動するときの注意すべきに役立ついくつかのトピックについて紹介します。 最初のいくつかのセクションは、コードについて説明した手順を持ち、詳細については、リソースへのリンクの後に簡単な紹介をいくつかのトピックを紹介する Visual Studio を使用して、次のセクションのタスクを完了します。

これらのトピックのほとんどは、既に作成したページを操作します。 生 SQL を使用して一括更新プログラムを実行するには、データベース内のすべてのコースの単位数を更新する新しいページを作成します。

![Update_Course_Credits_initial_page](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image1.png)

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * 生 SQL クエリを実行する
> * 追跡なしのクエリを実行します。
> * SQL を調べるクエリ データベースに送信されます

についても説明します。

> [!div class="checklist"]
> * 抽象化レイヤーを作成します。
> * プロキシ クラス
> * 変更の自動検出
> * 自動検証
> * Entity Framework Power Tools
> * Entity Framework のソース コード

## <a name="prerequisite"></a>必須コンポーネント

* [継承の実装](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="perform-raw-sql-queries"></a>生 SQL クエリを実行する

Entity Framework Code First API をデータベースに直接 SQL コマンドを渡すことができるようにするメソッドが含まれています。 次のオプションがあります。

- 使用して、 [DbSet.SqlQuery](https://msdn.microsoft.com/library/system.data.entity.dbset.sqlquery.aspx)エンティティ型を返すクエリ メソッド。 によって予期される型で返されたオブジェクトが必要があります、`DbSet`オブジェクト、およびそれらが自動的に追跡データベース コンテキストによって追跡をオフにする場合を除き、します。 (に関する次のセクションを参照してください、 [AsNoTracking](https://msdn.microsoft.com/library/system.data.entity.dbextensions.asnotracking.aspx)メソッドです)。
- 使用して、 [Database.SqlQuery](https://msdn.microsoft.com/library/system.data.entity.database.sqlquery.aspx)エンティティではない型を返すクエリ メソッド。 このメソッドを使用してエンティティ型を取得する場合でも、返されるデータはデータベース コンテキストによって追跡されません。
- 使用して、 [Database.ExecuteSqlCommand](https://msdn.microsoft.com/library/gg679456.aspx)非クエリ コマンド。

Entity Framework を使用する利点の 1 つは、データを格納する特定のメソッドにコードを過度に接近させなくてもよい点です。 SQL クエリとコマンドが生成されるため、自分でこれらを記述する必要がなくなります。 手動で作成した特定の SQL クエリを実行する必要がある場合に例外的なシナリオがあるし、これらのメソッドにより、このような例外を処理することが可能です。

Web アプリケーションで SQL コマンドを実行する場合は常に、SQL インジェクション攻撃から自身のサイトを保護する対策を講じる必要があります。 これを行う 1 つの方法として、パラメーター化されたクエリを使用して、Web ページによって送信された文字列が SQL コマンドとして解釈できないことを確認します。 このチュートリアルでは、ユーザー入力をクエリに統合するときに、パラメーター化されたクエリを使用します。

### <a name="calling-a-query-that-returns-entities"></a>クエリを呼び出すには、エンティティが返されます。

[DbSet&lt;TEntity&gt; ](https://msdn.microsoft.com/library/gg696460.aspx)クラス型のエンティティを返すクエリの実行に使用できるメソッドを提供`TEntity`します。 しくみを確認するコードを変更します、`Details`のメソッド、`Department`コント ローラー。

*DepartmentController.cs*の`Details`メソッド、置換、`db.Departments.FindAsync`メソッドの呼び出しで、`db.Departments.SqlQuery`メソッドの呼び出し、次の強調表示されたコードに示すように。

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample1.cs?highlight=8-14)]

新しいコードが正しく動作することを確認するには、**[Departments]\(部門\)** タブを選択し、いずれかの部門の **[Details]\(詳細\)** を選択します。 期待どおりにすべてのデータ表示を確認します。

### <a name="calling-a-query-that-returns-other-types-of-objects"></a>クエリを呼び出すには、その他の種類のオブジェクトが返されます。

以前に、登録日ごとの学生数を示す About ページ用に、学生の統計グリッドを作成しました。 コードでは、これを*HomeController.cs* LINQ を使用します。

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample2.cs)]

LINQ を使用するのではなく、SQL で直接このデータを取得するコードを記述するとします。 エンティティ オブジェクト以外のものを返すクエリを実行する必要があることには、ことを意味する必要があるを使用して、 [Database.SqlQuery](https://msdn.microsoft.com/library/system.data.entity.database.sqlquery(v=VS.103).aspx)メソッド。

*HomeController.cs*での LINQ ステートメントを置き換えます、`About`メソッドを次の強調表示されたコードに示すように、SQL ステートメント。

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample3.cs?highlight=3-18)]

[About] ページを実行します。 先ほどと同じデータを表示することを確認します。

### <a name="calling-an-update-query"></a>更新クエリを呼び出す

Contoso University の管理者は、すべてのコースの単位数を変更するなど、データベースで一括変更を実行することができるようにするとします。 大学に多くのコースがある場合は、それらすべてをエンティティとして取得し、それらを個別に変更するのは非効率的です。 このセクションでは、ユーザーは、すべてのコースの単位数を変更する係数を指定できる web ページを実装し、SQL を実行することによって、変更を行います`UPDATE`ステートメント。 

*CourseController.cs*、追加`UpdateCourseCredits`メソッド`HttpGet`と`HttpPost`:

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample4.cs)]

コント ローラーを処理すると、`HttpGet`要求、何も返されないで、`ViewBag.RowsAffected`変数、および、ビューは空のテキスト ボックスと送信ボタンが表示されます。

ときに、 **Update**ボタンがクリックされた、`HttpPost`メソッドが呼び出されると、および`multiplier`テキスト ボックスに入力した値を持ちます。 コードのコースを更新し、ビューに影響を受けた行の数を返します SQL を実行し、`ViewBag.RowsAffected`変数。 ビューは、その変数に値を取得、ときに、テキスト ボックスではなく更新された行の数を表示し、送信ボタン。

*CourseController.cs*のいずれかを右クリックし、`UpdateCourseCredits`メソッド、およびクリック**ビューの追加**します。 **ビューの追加**ダイアログが表示されます。 既定値と選択のままに**追加**します。

*Views\Course\UpdateCourseCredits.cshtml*、テンプレート コードを次のコードに置き換えます。

[!code-cshtml[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample5.cshtml)]

**[Courses]\(コース\)** タブを選択してから、ブラウザーのアドレス バーで URL の末尾に "/UpdateCourseCredits" を追加して (例: `http://localhost:50205/Course/UpdateCourseCredits`)、`UpdateCourseCredits` メソッドを実行します。 テキスト ボックスに数値を入力します。

![Update_Course_Credits_initial_page_with_2_entered](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image1.png)

**[更新]** をクリックします。 影響を受ける行の数を表示します。

**[リストに戻る]** をクリックして、単位数が変更されたコースの一覧を表示します。

生 SQL クエリの詳細については、[生 SQL クエリ](https://msdn.microsoft.com/data/jj592907)msdn を参照してください。

## <a name="no-tracking-queries"></a>追跡なしのクエリ

データベース コンテキストは、テーブルの行を取得してそれらを表すエンティティ オブジェクトを作成するとき、既定では、メモリ内のエンティティがデータベース内のデータと同期されているかどうかを追跡します。 メモリ内のデータはキャッシュとして機能し、エンティティを更新するときに使われます。 Web アプリケーションでは、一般にコンテキスト インスタンスの存続期間は短く (要求ごとに新しいインスタンスが作成されて破棄されます)、通常、エンティティを読み取るコンテキストはエンティティが再び使われる前に破棄されるので、多くの場合、このようなキャッシュは必要ありません。

使用してメモリ内のエンティティ オブジェクトの追跡を無効にすることができます、 [AsNoTracking](https://msdn.microsoft.com/library/gg679352(v=vs.103).aspx)メソッド。 追跡を無効にした方がよい一般的なシナリオを以下に示します。

- クエリでは、このような膨大な量の追跡をオフにするとパフォーマンスを向上させる著しくをデータを取得します。
- それを更新するには、エンティティをアタッチしたいが、異なる目的で以前に同じエンティティを取得します。 エンティティはデータベース コンテキストによって既に追跡されているため、変更するエンティティをアタッチできません。 この状況に対処する 1 つの方法は使用する、`AsNoTracking`オプションは、以前のクエリを使用します。

使用する方法を示す例については、 [AsNoTracking](https://msdn.microsoft.com/library/gg679352(v=vs.103).aspx)メソッドを参照してください[このチュートリアルの以前のバージョン](../../older-versions/getting-started-with-ef-5-using-mvc-4/advanced-entity-framework-scenarios-for-an-mvc-web-application.md)します。 チュートリアルのこのバージョンは、必要がないために、編集の方法でモデル バインダーに作成されたエンティティでの Modified フラグを設定しない`AsNoTracking`します。

## <a name="examine-sql-sent-to-database"></a>データベースに送信される SQL を確認します。

データベースに送信される実際の SQL クエリを確認できると役立つ場合があります。 前のチュートリアルでは、インターセプターのコードで方法を説明しましたこれでインターセプターのコードを記述せずに行う方法をいくつかを確認します。 これを試すには、単純なクエリを見てをこのような一括読み込み、フィルター処理、および並べ替えオプションを追加するときにどう見ています。

*コント ローラー/CourseController*、置換、`Index`メソッドを一時的に一括読み込みを停止するには、次のコード。

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample6.cs)]

今すぐにブレークポイントを設定、`return`ステートメント (その行にカーソルを f9 キーを押します)。 キーを押して**F5**プロジェクトをデバッグ モードで実行して、コースのインデックス ページを選択します。 コードでは、ブレークポイントに達すると、確認、`sql`変数。 SQL Server に送信されるクエリが表示されます。 単純な`Select`ステートメント。

[!code-json[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample7.json)]

クエリでは、虫眼鏡をクリックして、**テキスト ビジュアライザー**します。

![](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image10.png)

今すぐできるように、ユーザーは、特定の部署のフィルター処理できます Courses/index ページにドロップダウン リストを追加します。 コース タイトルで並べ替えられるし、一括読み込みを指定します、`Department`ナビゲーション プロパティ。

*CourseController.cs*、置換、`Index`メソッドを次のコード。

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample8.cs)]

ブレークポイントの復元、`return`ステートメント。

メソッドのドロップダウン リストの選択した値を受け取る、`SelectedDepartment`パラメーター。 何も選択されている場合、このパラメーターは null になります。

A`SelectList`ドロップダウン リストのすべての部門を含むコレクションが、ビューに渡されます。 渡されるパラメーター、`SelectList`コンストラクターは、値フィールドの名前、テキスト フィールド名、および選択した項目を指定します。

`Get`のメソッド、`Course`リポジトリ、コードは、フィルター式、並べ替え順序、およびの一括読み込みを指定、`Department`ナビゲーション プロパティ。 フィルター式は常に返します`true`かどうかは、ドロップダウン リストで何も選択が (つまり、`SelectedDepartment`が null)。

*Views\Course\Index.cshtml*、開始する直前に`table`タグは、ドロップダウン リストと送信ボタンを作成する次のコードを追加します。

[!code-cshtml[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample9.cshtml)]

まだブレークポイント設定、コースのインデックス ページを実行します。 最初に、コードが、ブレークポイントをヒット回を続け、ブラウザーでページが表示されます。 ドロップダウン リストから、部門を選択し、クリックして**フィルター**します。

この時間、最初のブレークポイントは、ドロップダウン リストの部門のクエリになります。 スキップして、表示、`query`変数次に、コード、ブレークポイントに到達内容を表示するには、`Course`クエリのようになりますようになりました。 次のようなものが表示されます。

[!code-sql[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample10.sql)]

クエリを参照してください、`JOIN`読み込みクエリ`Department`データと共に、`Course`データ、および含まれている、`WHERE`句。

削除、`var sql = courses.ToString()`行。

## <a name="create-an-abstraction-layer"></a>抽象化レイヤーを作成する

多くの開発者は、Entity Framework で動作するコードをラップするラッパーとして、Repository パターンと Unit of Work パターンを実装するためのコードを記述します。 これらのパターンは、アプリケーションのデータ アクセス層とビジネス ロジック層の間に抽象化レイヤーを作成するためのものです。 これらのパターンを実装すると、データ ストアの変更からアプリケーションを隔離でき、自動化された単体テストやテスト駆動開発 (TDD) を円滑化できます。 ただし、これらのパターンを実装する追加コードの記述は常にいくつかの理由、EF を使用するアプリケーションに最適な選択肢。

- EF コンテキスト クラス自体が、コードをデータ ストア固有のコードから隔離します。
- EF コンテキスト クラスは、EF を使用して行っているデータベースの更新の unit-of-work クラスとして動作できます。
- Entity Framework 6 で導入された機能を簡単にリポジトリのコードを記述することがなく、TDD を実装します。

リポジトリと unit of work パターンを実装する方法の詳細については、[このチュートリアル シリーズの Entity Framework 5 バージョン](../../older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application.md)を参照してください。 Entity Framework 6 で TDD を実装する方法については、次のリソースを参照してください。

- [EF6 がより簡単にモックの作成の DbSets を使用](http://thedatafarm.com/data-access/how-ef6-enables-mocking-dbsets-more-easily/)
- [モック作成フレームワークとテスト](https://msdn.microsoft.com/data/dn314429)
- [独自のテスト代替によるテスト](https://msdn.microsoft.com/data/dn314431)

<a id="proxies"></a>

## <a name="proxy-classes"></a>プロキシ クラス

Entity Framework では、(たとえば、クエリを実行するときに) エンティティ インスタンスを作成するときは、動的に生成されたエンティティのプロキシとして機能する派生型のインスタンスとしてそのを多くの場合、作成します。 たとえば、次の 2 つのデバッガー イメージを参照してください。 最初のイメージで、`student`変数は、予想される`Student`エンティティをインスタンス化した直後に入力します。 2 番目のイメージを student エンティティをデータベースから読み取る EF を使用した後、プロキシ クラスを参照します。

![プロキシ クラスの前に](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image12.png)

![プロキシ クラスの後](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image13.png)

このプロキシ クラスは、プロパティにアクセスするときにアクションを自動的に実行するためのフックを挿入するエンティティの一部の仮想プロパティをオーバーライドします。 このメカニズムが使用する 1 つの関数では、遅延読み込みです。

ほとんどの場合、このプロキシの使用を意識する必要はありませんが、例外があります。

- 一部のシナリオでは、Entity Framework がプロキシのインスタンスを作成するを防ぐためにする場合があります。 たとえば、エンティティをシリアル化しているときに一般的にする POCO クラス、プロキシ クラスではありません。 シリアル化の問題を回避する方法の 1 つは、ように、エンティティ オブジェクトではなく、データ転送オブジェクト (Dto) のシリアル化には、 [Entity Framework で Web API を使用して](../../../../web-api/overview/data/using-web-api-with-entity-framework/part-1.md)チュートリアル。 別の方法が、[プロキシの作成を無効にする](https://msdn.microsoft.com/data/jj592886.aspx)します。
- インスタンス化すると、エンティティ クラスを使用して、`new`オペレーターは、プロキシ インスタンスを取得しません。 これは、遅延読み込みと自動変更の追跡などの機能を取得しないことを意味します。 これは通常では;一般的に不要な lazy loading、データベースにない新しいエンティティを作成するためと、変更の追跡としてエンティティを明示的にマークしている場合は通常不要`Added`します。 ただし、遅延読み込みする必要は、変更の追跡が必要な場合は、インスタンスを作成できます新しいエンティティを使用してプロキシを持つ、[作成](https://msdn.microsoft.com/library/gg679504.aspx)のメソッド、`DbSet`クラス。
- プロキシ型から実際のエンティティ型を取得する可能性があります。 使用することができます、 [GetObjectType](https://msdn.microsoft.com/library/system.data.objects.objectcontext.getobjecttype.aspx)のメソッド、`ObjectContext`プロキシ型のインスタンスの実際のエンティティ型を取得するクラス。

詳細については、[Proxies の操作](https://msdn.microsoft.com/data/JJ592886.aspx)msdn を参照してください。

## <a name="automatic-change-detection"></a>変更の自動検出

Entity Framework では、エンティティの現在の値と元の値を比較して、エンティティがどのように変更されたか (およびそれによって、どの更新プログラムをデータベースに送信する必要があるか) を判断します。 元の値は、エンティティが照会されるかアタッチされるときに格納されます。 変更の自動検出を行うメソッドには、次のようなものがあります。

- `DbSet.Find`
- `DbSet.Local`
- `DbSet.Remove`
- `DbSet.Add`
- `DbSet.Attach`
- `DbContext.SaveChanges`
- `DbContext.GetValidationErrors`
- `DbContext.Entry`
- `DbChangeTracker.Entries`

多数のエンティティを管理するいると、ループ内で何度もがこれらのメソッドのいずれかの呼び出すと、一時的に変更の自動検出を使用して無効にすることで大幅なパフォーマンス向上を取得可能性があります、 [AutoDetectChangesEnabled](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbcontextconfiguration.autodetectchangesenabled.aspx)プロパティ。 詳細については、[変更を自動的に検出](https://msdn.microsoft.com/data/jj556205)msdn を参照してください。

## <a name="automatic-validation"></a>自動検証

呼び出すと、`SaveChanges`メソッド、既定では、Entity Framework データを検証、変更されたすべてのエンティティのすべてのプロパティで、データベースを更新する前にします。 多数のエンティティを更新したら、既に検証したデータは、この作業は必要ありませんし保存するプロセスを行うことができます、変更は一時的に検証を無効にすることで時間が。 使用して行うことができます、 [ValidateOnSaveEnabled](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbcontextconfiguration.validateonsaveenabled.aspx)プロパティ。 詳細については、[検証](https://msdn.microsoft.com/data/gg193959)msdn を参照してください。

## <a name="entity-framework-power-tools"></a>Entity Framework Power Tools

[Entity Framework Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition)データ モデルのダイアグラムの作成に使用された Visual Studio アドインは、このチュートリアルで示します。 ツールでは、Code First とデータベースを使用できるように、既存のデータベース内のテーブルに基づくエンティティ クラスを生成するなどの他の関数も実行できます。 ツールをインストールすると、コンテキスト メニューに追加のオプションが表示されます。 たとえば、右クリックすると、コンテキスト クラスを**ソリューション エクスプ ローラー**、表示と**Entity Framework**オプション。 これにより、図を生成します。 Code First を使用する場合、図では、データ モデルを変更することはできませんを理解しやすいように内点を移動することができます。

![EF のダイアグラム](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image15.png)

## <a name="entity-framework-source-code"></a>Entity Framework のソース コード

Entity Framework 6 のソース コードは[GitHub](https://github.com/aspnet/EntityFramework6)します。 バグを探し、EF のソース コードを独自の機能強化を投稿することができます。

ソース コードはオープンですが、Entity Framework は、Microsoft 製品がサポートされます。 Microsoft Entity Framework チームは、各リリースの品質を保証するため、受け入れる投稿を管理し、すべてのコード変更をテストしています。

## <a name="acknowledgments"></a>謝辞

- Tom Dykstra は、このチュートリアルの元のバージョンを記述した、EF 5 更新プログラムを共同執筆し、EF 6 の更新プログラムを記述します。 Tom は、Microsoft Web プラットフォームとツール コンテンツ チームのシニア プログラミング ライターです。
- [Rick Anderson](https://blogs.msdn.com/b/rickandy/) (twitter [ @RickAndMSFT ](http://twitter.com/RickAndMSFT)) EF 5 と MVC 4 のチュートリアルを更新する作業の大半を EF 6 の更新プログラムを共同で作成します。 Rick は、Azure と MVC に注目して Microsoft のシニア プログラミング ライターです。
- [Rowan Miller](http://www.romiller.com)と Entity Framework チームの他のメンバーがコード レビューを支援し、EF 5 と 6 の EF のチュートリアルを更新していた私たちの中に発生した移行に関する多くの問題のデバッグに協力します。

## <a name="troubleshoot-common-errors"></a>一般的なエラーのトラブルシューティング

### <a name="cannot-createshadow-copy"></a>コピーを作成/シャドウことはできません。

エラー メッセージ:

> コピーを作成/シャドウできません '&lt;filename&gt;' そのファイルが既に存在します。

ソリューション

数秒待ってからページを更新します。

### <a name="update-database-not-recognized"></a>データベースの更新認識されません。

エラー メッセージ (から、 `Update-Database` PMC でコマンド)。

> 'データベースの更新' という用語はコマンドレット、関数、スクリプト ファイル、または操作可能なプログラムの名前として認識されません。 名前のスペルを確認するか、パスが正しいことを確認し、もう一度お試しパスが含まれている場合。

ソリューション

Visual Studio を終了します。 プロジェクトを再度開いてもう一度やり直してください。

### <a name="validation-failed"></a>検証に失敗しました

エラー メッセージ (から、 `Update-Database` PMC でコマンド)。

> 1 つまたは複数のエンティティの検証に失敗しました。 詳細については、'EntityValidationErrors' プロパティを参照してください。

ソリューション

この問題の原因の 1 つは、検証エラー時に、`Seed`メソッドを実行します。 参照してください[シード処理中、およびデバッグ Entity Framework (EF) Db](https://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx)デバッグに関するヒント、`Seed`メソッド。

### <a name="http-50019-error"></a>HTTP 500.19 エラー

エラー メッセージ:

> HTTP エラー 500.19 - 内部サーバー エラー、要求されたページは、ページの関連する構成データが無効であるために、アクセスできません。

ソリューション

このエラーを取得する方法の 1 つは、同じポート番号を使用してそれらの各ソリューションの複数のコピーです。 この問題の解決には、Visual Studio のすべてのインスタンスを終了し、プロジェクトでの作業を再起動する通常します。 うまく行かない場合は、ポート番号を変更してみてください。 プロジェクト ファイルを右クリックし、し、[プロパティ] をクリックします。 選択、 **Web**タブをされているポート番号を変更し、**プロジェクト Url**テキスト ボックス。

### <a name="error-locating-sql-server-instance"></a>SQL Server インスタンスの位置を特定しているときのエラー

エラー メッセージ:

> SQL Server への接続を確立しているときに、ネットワーク関連またはインスタンス固有のエラーが発生しました。 サーバーが見つからないかアクセスできません。 インスタンス名が正しいこと、および SQL Server がリモート接続を許可するように構成されていることを確認してください。 (プロバイダー:SQL ネットワーク インターフェイス、エラー:26 - 指定されたサーバーまたはインスタンスの位置を特定しているときにエラーが発生しました)

ソリューション

接続文字列を確認します。 データベースを手動で削除した場合は、構築文字列でデータベースの名前を変更します。

## <a name="get-the-code"></a>コードを取得する

[完成したプロジェクトのダウンロード](https://webpifeed.blob.core.windows.net/webpifeed/Partners/ASP.NET%20MVC%20Application%20Using%20Entity%20Framework%20Code%20First.zip)

## <a name="additional-resources"></a>その他の技術情報

 Entity Framework を使用してデータを操作する方法の詳細については、、 [msdn ドキュメント ページを EF](https://msdn.microsoft.com/data/ee712907)と[ASP.NET データ アクセス - 推奨リソース](../../../../whitepapers/aspnet-data-access-content-map.md)を参照してください。

これを構築した後、web アプリケーションをデプロイする方法の詳細については、次を参照してください。 [ASP.NET Web 展開 - 推奨リソース](../../../../whitepapers/aspnet-web-deployment-content-map.md)、MSDN ライブラリ。

については、認証および承認など、MVC に関連するその他のトピックを参照してください、 [ASP.NET MVC - 推奨リソース](../recommended-resources-for-mvc.md)します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * 生 SQL クエリを実行した
> * 追跡なしのクエリの実行
> * データベースに送信する SQL クエリの検査

詳細についても学習しました。

> [!div class="checklist"]
> * 抽象化レイヤーを作成します。
> * プロキシ クラス
> * 変更の自動検出
> * 自動検証
> * Entity Framework Power Tools
> * Entity Framework のソース コード

これは、この一連の ASP.NET MVC アプリケーションで Entity Framework の使用に関するチュートリアルを完了します。 EF Database First について学習する場合は、DB の最初のチュートリアル シリーズを参照してください。
> [!div class="nextstepaction"]
> [Entity Framework データベース ファースト](../database-first-development/setting-up-database.md)