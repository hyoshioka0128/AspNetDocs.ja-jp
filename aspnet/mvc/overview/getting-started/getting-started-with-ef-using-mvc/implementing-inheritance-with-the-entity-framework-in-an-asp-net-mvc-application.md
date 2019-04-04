---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application
title: テンプレート:ASP.NET MVC 5 アプリで ef の継承を実装します。
description: このチュートリアルでは、データ モデルで継承を実装する方法を示します。
author: tdykstra
ms.author: riande
ms.date: 01/21/2019
ms.topic: tutorial
ms.assetid: 08834147-77ec-454a-bb7a-d931d2a40dab
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 3ebabd626e0b862e09f19552648406aab959f882
ms.sourcegitcommit: 289e051cc8a90e8f7127e239fda73047bde4de12
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58423313"
---
# <a name="template-implement-inheritance-with-ef-in-an-aspnet-mvc-5-app"></a>テンプレート:ASP.NET MVC 5 アプリで ef の継承を実装します。

前のチュートリアルでは、同時実行例外を処理します。 このチュートリアルでは、データ モデルで継承を実装する方法を示します。

オブジェクト指向のプログラミングで使用できます[継承](http://en.wikipedia.org/wiki/Inheritance_(object-oriented_programming))を容易にする[コードの再利用](http://en.wikipedia.org/wiki/Code_reuse)します。 このチュートリアルでは、`Instructor` と `Student` クラスを `Person` 基底クラスから派生するように変更します。この基底クラスはインストラクターと受講者の両方に共通な `LastName` などのプロパティを含んでいます。 どの Web ページも追加または変更しませんが、コードの一部を変更し、それらの変更はデータベースに自動的に反映されます。

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * 継承をデータベースにマップするについて説明します
> * Person クラスの作成
> * Instructor と Student を更新する
> * モデルにユーザーを追加します。
> * 作成し、移行の更新
> * 実装をテストする
> * Azure に配置する

## <a name="prerequisites"></a>必須コンポーネント

* [継承の実装](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="map-inheritance-to-database"></a>継承をデータベースにマップする

`Instructor`と`Student`クラス、`School`データ モデルが同一であるいくつかのプロパティがあります。

![Student_and_Instructor_classes](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

`Instructor` エンティティと `Student` エンティティで共有されるプロパティの冗長なコードを削除すると仮定します。 または、インストラクターと学生のどちらから名前を取得したかに関係なく、名前をフォーマットできるサービスを記述するとします。 作成することが、`Person`基本クラスがそれらの共有プロパティだけが含まれているようにして、`Instructor`と`Student`エンティティは、次の図に示すように、その基本クラスから継承します。

![Student_and_Instructor_classes_deriving_from_Person_class](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

データベースでこの継承構造を表すことができるいくつかの方法があります。 割り当てることも、`Person`受講者とインストラクターによる 1 つのテーブルの両方に関する情報が含まれるテーブル。 インストラクターのみに適用の一部の列 (`HireDate`)、受講者のみに (`EnrollmentDate`)、一部には、両方 (`LastName`、 `FirstName`)。 通常があります、*識別子*各行を入力するかを示す列を表します。 たとえば、識別子列にインストラクターを示す "Instructor" と受講者を示す "Student" がある場合があります。

![テーブル-hierarchy_example ごと](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image3.png)

単一のデータベース テーブルからエンティティの継承構造を生成するには、このパターンと呼ばれます *- table-per-hierarchy* (TPH) 継承します。

代わりに、継承構造と同じように見えるデータベースを作成することもできます。 など名前フィールドのみがある可能性があります、`Person`テーブルいて個別`Instructor`と`Student`日付フィールドを持つテーブル。

![Table-per-type_inheritance](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image4.png)

各エンティティ クラスと呼ばれる、データベース テーブルを作成するこのパターン*table-per-type* (TPT) 継承します。

他のオプションとして、個々のテーブルにすべての非抽象型をマップすることもできます。 継承されたプロパティを含むクラスのすべてのプロパティは、対応するテーブルの列にマップされます。 このパターンは、Table-per-Concrete Class (TPC) 継承と呼ばれます。 TPC 継承を実装する場合、 `Person`、 `Student`、および`Instructor`クラス、前述のように、`Student`と`Instructor`継承を実装する前にテーブルに同じなります。

TPC および TPH 継承パターンは、TPT パターンが複雑な結合クエリのため、TPT 継承パターンよりも、Entity Framework で高いパフォーマンスを実現一般にします。

このチュートリアルでは、TPH 継承の実装方法を示します。 TPH は、Entity Framework で既定の継承パターンの作成は行う必要があるすべてを`Person`クラス、変更、`Instructor`と`Student`クラスから派生する`Person`、新しいクラスを追加、`DbContext`を作成し、移行します。 (その他の継承パターンを実装する方法については、次を参照してください[Table-type ごと (TPT) 継承のマッピング](https://msdn.microsoft.com/data/jj591617#2.5)と[テーブル-ごとの具象クラス (TPC) 継承のマッピング](https://msdn.microsoft.com/data/jj591617#2.6)msdn。Entity Framework ドキュメントです。)

## <a name="create-the-person-class"></a>Person クラスの作成

*モデル*フォルダー作成*Person.cs*テンプレート コードを次のコードに置き換えます。

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs)]

## <a name="update-instructor-and-student"></a>Instructor と Student を更新する

今すぐ更新、 *Instructor.cs*と*Student.cs*から値を継承するように、 *Person.sc*します。

*Instructor.cs*、派生、`Instructor`クラスから、`Person`クラスし、キーと名前のフィールドを削除します。 コードは次の例のように表示されます。

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

同様に変更する*Student.cs*します。 `Student`クラスは次の例のようになります。

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs)]

## <a name="add-person-to-the-model"></a>モデルにユーザーを追加します。

*SchoolContext.cs*、追加、`DbSet`プロパティを`Person`エンティティの種類。

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs)]

Table-per-Hierarchy 継承を構成するために Entity Framework に必要なのことはこれですべてです。 わかる、データベースが更新されたとき、`Person`テーブルの代わりに、`Student`と`Instructor`テーブル。

## <a name="create-and-update-migrations"></a>作成し、移行の更新

パッケージ マネージャー コンソール (PMC) では、次のコマンドを入力します。

`Add-Migration Inheritance`

実行、 `Update-Database` PMC でコマンド。 移行が処理する方法を認識しない既存のデータがあるので、コマンドはこの時点で失敗します。 次のようなエラー メッセージを取得します。

> *オブジェクトを削除できませんでした ' dbo します。Instructor' FOREIGN KEY 制約によって参照されているためです。*


開いている*移行\&lt; タイムスタンプ&gt;\_Inheritance.cs*と置換、`Up`メソッドを次のコード。

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs)]

このコードは、次のデータベースの更新タスクを処理します。

- 外部キー制約と Student テーブルをポイントするインデックスを削除します。
- Instructor テーブルの名前の Person に変更し、Student データを格納するために必要な変更を加えます。

    - 受講者の null 許容 EnrollmentDate を追加します。
    - 行が、受講者かインストラクターかを示すために識別子列を追加します。
    - 受講者行には雇用日がないので HireDate を nul 許容にします。
    - 受講者をポイントする外部キーの更新に使用する一時的なフィールドを追加します。 Person テーブルに受講者をコピーする場合は、新しい主キー値が表示されます。
- Student テーブルから Person テーブルにデータをコピーします。 これにより、受講者に新しい主キー値が割り当てられます。
- 受講者をポイントする外部キー値を修正します。
- 今は Person テーブルをポイントしている外部キー制約とインデックスを再作成します 

(主キーの型として整数の代わりに GUID を使用した場合は、受講者の主キー値を変更する必要はなく、これらの手順のいくつかを省略できます)。

実行、`update-database`コマンドを再実行します。

(実稼働システムでは対応に変更を加えるダウン メソッドを使用してデータベースの以前のバージョンに戻ることがある発生した場合。 このチュートリアルではありませんする方法を使用してダウンします。)

> [!NOTE]
> データとスキーマの変更を移行するときにその他のエラーが発生することになります。 移行エラーが発生する場合は解決できない場合、接続文字列を変更することで、チュートリアルを続けることができます、 *Web.config*ファイルまたはでデータベースを削除します。 データベースの名前を変更する最も簡単なアプローチとしては、 *Web.config*ファイル。 たとえば、次の例に示すようを ContosoUniversity2 にデータベース名を変更します。
>
> [!code-xml[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.xml?highlight=2)]
>
> 新しいデータベースを移行するデータがないと、`update-database`コマンドがエラーなしで完了する可能性が高くなります。 データベースを削除する方法の詳細については、[Visual Studio 2012 からデータベースを削除する方法](http://romiller.com/2013/05/17/how-to-drop-a-database-from-visual-studio-2012/)を参照してください。 チュートリアルを続行するにはこの方法を実行する場合は、このチュートリアルの最後に、展開の手順をスキップまたは新しいサイトとデータベースを展開します。 既にするデプロイされたしたのと同じサイトに更新プログラムを展開する場合の移行を自動的に実行時に EF は、同じエラーがありますを取得します。 移行エラーのトラブルシューティングを行う場合は、最適なリソースは、Entity Framework のフォーラムまたは StackOverflow.com のいずれか。

## <a name="test-the-implementation"></a>実装をテストする

サイトを実行し、さまざまなページをお試しください。 すべてが前と同じように動作します。

**サーバー エクスプ ローラー**展開**データ Connections\SchoolContext**し**テーブル**、ことを確認して、**学生**と**インストラクター**テーブルに置換された、 **Person**テーブル。 展開、 **Person**テーブルとするすべてに使用されていた列があるを参照してください、**学生**と**インストラクター**テーブル。

Person テーブルを右クリックし、**[テーブル データの表示]** をクリックして識別子列を表示します。

次の図は、新しい School データベースの構造を示しています。

![School_database_diagram](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image7.png)

## <a name="deploy-to-azure"></a>Azure に配置する

このセクションでは、省略可能なが完了する必要があります**アプリを Azure にデプロイ**セクション[パート 3、並べ替え、フィルター処理、およびページング](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application.md)このチュートリアル シリーズの。 ローカル プロジェクトでデータベースを削除することによって解決された移行エラーがある場合は、この手順では; をスキップします。または、新しいサイトとデータベースを作成し、新しい環境にデプロイします。

1. Visual Studio でのプロジェクトを右クリックして**ソリューション エクスプ ローラー**選択と**発行**コンテキスト メニュー。

2. **[発行]** をクリックします。

    Web アプリは、既定のブラウザーで開きます。

3. アプリケーションをテストして確認することが動作します。

    初めてページを実行するデータベースにアクセスする、Entity Framework で実行されるすべての移行`Up`データベースの現在のデータ モデルで最新の状態に必要なメソッドです。

## <a name="get-the-code"></a>コードを取得する

[完成したプロジェクトのダウンロード](https://webpifeed.blob.core.windows.net/webpifeed/Partners/ASP.NET%20MVC%20Application%20Using%20Entity%20Framework%20Code%20First.zip)

## <a name="additional-resources"></a>その他の技術情報

その他の Entity Framework リソースへのリンクが記載されて、 [ASP.NET データ アクセス - 推奨リソース](../../../../whitepapers/aspnet-data-access-content-map.md)します。

これと他の継承構造の詳細については、[TPT 継承パターン](https://msdn.microsoft.com/data/jj618293)と[TPH 継承パターン](https://msdn.microsoft.com/data/jj618292)msdn を参照してください。 次のチュートリアルでは、比較的高度なさまざまな Entity Framework のシナリオを処理する方法を説明します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * 継承をデータベースにマップするについて説明しました。
> * Person クラスを作成した
> * Instructor と Student を更新した
> * モデルに追加されたユーザー
> * 作成され、移行の更新
> * 実装をテストした
> * Azure にデプロイ

Entity Framework Code First を使用する ASP.NET web アプリケーションの開発の基本機能を越えて移動するときの注意すべきに役立つトピックの詳細については、次の記事に進んでください。
> [!div class="nextstepaction"]
> [高度な Entity Framework シナリオ](advanced-entity-framework-scenarios-for-an-mvc-web-application.md)