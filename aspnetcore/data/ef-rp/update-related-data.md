---
title: ASP.NET Core の Razor ページと EF Core - 関連データの更新 - 7/8
author: rick-anderson
description: このチュートリアルでは、外部キー フィールドとナビゲーション プロパティを更新することで関連データを更新します。
ms.author: riande
ms.date: 11/15/2017
uid: data/ef-rp/update-related-data
ms.openlocfilehash: 4306118240c052585a5c2eeb2053ce03534b547c
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57061219"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---update-related-data---7-of-8"></a>ASP.NET Core の Razor ページと EF Core - 関連データの更新 - 7/8

作成者: [Tom Dykstra](https://github.com/tdykstra)、[Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

このチュートリアルでは、関連データの更新を示します。 解決できない問題が発生した場合は、[完成したアプリをダウンロードまたは表示](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)してください。 [ダウンロードの方法はこちらをご覧ください。](xref:index#how-to-download-a-sample)

以下の図は、完成したページの一部を示しています。

![Course/Edit ページ](update-related-data/_static/course-edit.png)
![Instructor/Edit ページ](update-related-data/_static/instructor-edit-courses.png)

コースの Create (作成) ページと Edit (編集) ページを確認してテストします。 新しいコースを作成します。 部門は、その名前ではなく主キー (整数) で選択されます。 新しいコースを編集します。 テストが完了したら、新しいコースを削除します。

## <a name="create-a-base-class-to-share-common-code"></a>共通コードを共有する基底クラスを作成する

Courses/Create ページと Courses/Edit ページにはそれぞれ部門名のリストが必要です。 Create ページと Edit ページ用に *Pages/Courses/DepartmentNamePageModel.cshtml.cs* 基底クラスを作成します。

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

上記のコードは、部門名のリストを格納するための [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) を作成します。 `selectedDepartment` を指定すると、`SelectList` でその部門が選択されます。

Create と Edit のページ モデル クラスは、`DepartmentNamePageModel` から派生します。

## <a name="customize-the-courses-pages"></a>Courses ページをカスタマイズする

新しいコース エンティティが作成されると、既存の部門とのリレーションシップが必要になります。 コースを作成しながら部門を追加するため、Create と Edit の基底クラスには選択する部門のドロップダウン リストが含まれています。 ドロップダウン リストは、`Course.DepartmentID` 外部キー (FK) プロパティを設定します。 EF Core は `Course.DepartmentID` FK を使用して `Department` ナビゲーション プロパティを読み込みます。

![コースの作成](update-related-data/_static/ddl.png)

次のコードを使用して、Create ページ モデルを更新します。

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

上のコードでは以下の操作が行われます。

* `DepartmentNamePageModel`から派生します。
* `TryUpdateModelAsync` を使用して[過剰ポスティング](xref:data/ef-rp/crud#overposting)を防止します。
* `ViewData["DepartmentID"]` を `DepartmentNameSL` (基底クラスから) で置き換えます。

`ViewData["DepartmentID"]` は厳密に型指定された `DepartmentNameSL` に置き換えられます。 厳密に型指定されたモデルは、弱く型指定されたモデルよりも優先されます。 詳細については、「[弱い型指定のデータ (ViewData と ViewBag)](xref:mvc/views/overview#VD_VB)」を参照してください。

### <a name="update-the-courses-create-page"></a>Courses/Create ページを更新する

次のマークアップを使用して *Pages/Courses/Create.cshtml* を更新します。

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

上記のマークアップは、次の変更を加えます。

* キャプションを **DepartmentID** から **Department** (部門) に変更します。
* `"ViewBag.DepartmentID"` を `DepartmentNameSL` (基底クラスから) で置き換えます。
* [Select Department]\(部門の選択\) オプションを追加します。 この変更により、最初の部門ではなく、[Select Department]\(部門の選択\) がレンダリングされます。
* 部門が選択されていない場合に、検証メッセージを追加します。

Razor ページは[選択タグ ヘルパー](xref:mvc/views/working-with-forms#the-select-tag-helper)を使用します。

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Create ページをテストします。 Create ページには、部門 ID ではなく、部門名が表示されます。

### <a name="update-the-courses-edit-page"></a>Courses/Edit ページを更新する

次のコードを使用して、Edit ページ モデルを更新します。

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

変更は、Create ページ モデルで行われたものと似ています。 上記のコードでは、ドロップダウン リストで指定された部門を選択する `PopulateDepartmentsDropDownList` が DepartmentID で渡されます。

次のマークアップを使用して *Pages/Courses/Edit.cshtml* を更新します。

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

上記のマークアップは、次の変更を加えます。

* コース ID を表示します。 通常、エンティティの主キー (PK) は表示されません。 PK は通常、ユーザーにとっては意味がありません。 このケースでは、PK はコース番号です。
* キャプションを **DepartmentID** から **Department** (部門) に変更します。
* `"ViewBag.DepartmentID"` を `DepartmentNameSL` (基底クラスから) で置き換えます。

このページには、コース番号の隠しフィールド (`<input type="hidden">`) が含まれています。 `<label>` タグ ヘルパーと `asp-for="Course.CourseID"` を追加しても、隠しフィールドの必要性はなくなりません。 `<input type="hidden">` は、ユーザーが **[保存]** をクリックしたときに、ポストされたデータに含まれるコース番号にとって必要です。

更新されたコードをテストします。 コースを作成、編集、および削除します。

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a>AsNoTracking を Details (詳細) と Delete (削除) のページ モデルに追加する

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) は、追跡が必要ない場合に、パフォーマンスを向上させることができます。 `AsNoTracking` を Details と Delete のページ モデルに追加します。 次のコードは、更新された Delete ページ モデルを示しています。

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

*Pages/Courses/Details.cshtml.cs* ファイルで `OnGetAsync` メソッドを更新します。

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a>Delete ページと Details ページを変更する

次のマークアップを使用して、Delete Razor ページを更新します。

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

Details ページに同じ変更を行います。

### <a name="test-the-course-pages"></a>Course ページをテストする

Create、Edit、Details、Delete の各ページをテストします。

## <a name="update-the-instructor-pages"></a>Instructor ページを更新する

次のセクションでは、Instructor ページを更新します。

### <a name="add-office-location"></a>オフィスの場所を追加する

インストラクター レコードを編集するときに、インストラクターのオフィスの割り当ての更新が必要な場合があります。 `Instructor` エンティティには、`OfficeAssignment` エンティティとの一対ゼロまたは一対一のリレーションシップがあります。 インストラクター コードは次を処理する必要があります。

* ユーザーがオフィスの割り当てをクリアした場合、`OfficeAssignment` エンティティを削除する。
* ユーザーがオフィスの割り当てを入力し、それが空だった場合、新しい `OfficeAssignment` エンティティを作成する。
* ユーザーがオフィスの割り当てを変更した場合、`OfficeAssignment` エンティティを更新する。

次のコードを使用して、Instructors/Edit ページ モデルを更新します。

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

上のコードでは以下の操作が行われます。

- `OfficeAssignment` ナビゲーション プロパティの一括読み込みを使用して、現在の `Instructor` エンティティをデータベースから取得します。
- モデル バインダーからの値を使用して、取得した `Instructor` エンティティを更新します。 `TryUpdateModel` は[過剰ポスティング](xref:data/ef-rp/crud#overposting)を防止します。
- オフィスの場所が空白の場合は、`Instructor.OfficeAssignment` を null に設定します。 `Instructor.OfficeAssignment` が null の場合、`OfficeAssignment` テーブル内の関連する行が削除されます。

### <a name="update-the-instructor-edit-page"></a>Instructors/Edit ページを更新する

オフィスの場所で *Pages/Instructors/Edit.cshtml* を更新します。

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

インストラクターのオフィスの場所を変更できることを確認します。

## <a name="add-course-assignments-to-the-instructor-edit-page"></a>Instructors/Edit ページにコースの割り当てを追加する

インストラクターは、任意の数のコースを担当する場合があります。 このセクションでは、コースの割り当てを変更する機能を追加します。 次の図は、更新された Instructors/Edit ページを示しています。

![コースが表示された Instructor/Edit ページ](update-related-data/_static/instructor-edit-courses.png)

`Course` と `Instructor` は、多対多リレーションシップです。 リレーションシップの追加と削除を行うには、`CourseAssignments` 結合エンティティ セットからエンティティを追加および削除します。

チェック ボックスは、インストラクターが割り当てられるコースへの変更を有効にします。 データベース内のすべてのコースのチェック ボックスが表示されます。 インストラクターが割り当てられているコースがチェックされています。 ユーザーは、チェック ボックスをオンまたはオフにしてコースの割り当てを変更できます。 コースの数が非常に多い場合:

* 別のユーザー インターフェイスを使用して、コースを表示します。
* 結合エンティティを操作してリレーションシップを作成または削除するメソッドは変わりません。

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a>Instructors/Create ページと Instructors/Edit ページをサポートするクラスを追加する

次のコードを使用して、*SchoolViewModels/AssignedCourseData.cs* を作成します。

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

`AssignedCourseData` クラスには、インストラクターごとの割り当てられたコースのチェック ボックスを作成するデータが含まれています。

*Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* 基底クラスを作成します。

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

`InstructorCoursesPageModel` は、Edit と Create のページ モデルに使用する基底クラスです。 `PopulateAssignedCourseData` は、`AssignedCourseDataList` に設定するためのすべての `Course` エンティティを読み取ります。 コースごとに、コードは `CourseID`、タイトル、およびインストラクターがコースに割り当てられているかどうかを設定します。 効率的な参照を作成するために [HashSet](/dotnet/api/system.collections.generic.hashset-1) が使用されています。

### <a name="instructors-edit-page-model"></a>Instructors/Edit ページ モデル

次のコードを使用して、Instructors/Edit ページ モデルを更新します。

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

上記のコードでは、オフィスの割り当て変更を処理します。

Instructor Razor ビューを更新します。

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> Visual Studio にコードを貼り付けると、改行がコードを分割するように変更されます。 Ctrl キーを押しながら Z キーを 1 回押して、オート フォーマットを元に戻します。 Ctrl キーを押しながら Z キーを押すことで、改行がここに示されているように修正されます。 インデントは完璧である必要はありませんが、`@</tr><tr>`、`@:<td>`、`@:</td>`、および `@:</tr>` の行は、示されているようにそれぞれ 1 行にする必要があります。 新しいコードのブロックを選択して、Tab キーを 3 回押して、新しいコードと既存のコードを並べます。 このバグの状態を報告または確認するには、[このリンク](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html)を使用してください。

上記のコードでは、3 つの列を含む HTML テーブルを作成します。 各列には、チェック ボックスと、コース番号とタイトルを含むキャプションがあります。 チェック ボックスはすべて、同じ名前 ("selectedCourses") を持ちます。 同じ名前を使用することで、これらをグループとして扱うようにモデル バインダーに通知します。 各チェック ボックスの value 属性は `CourseID` に設定されます。 ページがポストされると、モデル バインダーは、選択されたチェック ボックスの `CourseID` 値のみで構成される配列を渡します。

チェック ボックスが最初にレンダリングされるときに、インストラクターに割り当てられているコースが checked 属性を持ちます。

アプリを実行し、更新された Instructors/Edit ページをテストします。 一部のコース割り当てを変更します。 変更が Index ページに反映されます。

メモ:インストラクター コース データを編集するためにここで採用されている方法は、コースの数が限られている場合にはうまく機能します。 非常に大きいコレクションの場合、別の UI と別の更新方法の方が有効で効率的な場合があります。

### <a name="update-the-instructors-create-page"></a>Instructors/Create ページを更新する

次のコードを使用して、Instructors/Create ページ モデルを更新します。

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

上記のコードは、*Pages/Instructors/Edit.cshtml.cs* コードに似ています。

次のマークアップを使用して、Instructors/Create Razor ページを更新します。

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

Instructors/Create ページをテストします。

## <a name="update-the-delete-page"></a>Delete ページを更新する

次のコードを使用して、Delete ページ モデルを更新します。

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

上記のコードは、次の変更を加えます。

* 一括読み込みを `CourseAssignments` ナビゲーション プロパティに使用します。 `CourseAssignments` を含める必要があります。そうしないと、インストラクターが削除されたときに削除されません。 これらを読み取らなくても済むようにするには、データベースで連鎖削除を構成します。

* 削除されるインストラクターが任意の部門の管理者として割り当てられている場合、インストラクターの割り当てをその部門から削除します。

> [!div class="step-by-step"]
> [前へ](xref:data/ef-rp/read-related-data)
> [次へ](xref:data/ef-rp/concurrency)
