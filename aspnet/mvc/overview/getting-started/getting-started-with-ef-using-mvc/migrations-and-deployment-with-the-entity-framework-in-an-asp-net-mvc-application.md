---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'チュートリアル: ASP.NET MVC アプリで EF 移行を利用し、Azure にデプロイ'
author: tdykstra
description: このチュートリアルでは、Code First migrations を有効にし、アプリケーションを Azure でクラウドにデプロイします。
ms.author: riande
ms.date: 01/16/2019
ms.topic: tutorial
ms.assetid: d4dfc435-bda6-4621-9762-9ba270f8de4e
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 1f25a9afdf379d725496bd88f6ac192ab19930ca
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59384514"
---
# <a name="tutorial-use-ef-migrations-in-an-aspnet-mvc-app-and-deploy-to-azure"></a>チュートリアル: ASP.NET MVC アプリで EF 移行を利用し、Azure にデプロイ

これまでに、Contoso University のサンプル web アプリケーションがされてローカルで実行して IIS Express で、開発用コンピューター。 で実際のアプリケーションをインターネット経由で使用するには、他のユーザーに使用できるようにするには、web ホスティング プロバイダーにデプロイがあります。 このチュートリアルでは、Code First migrations を有効にし、アプリケーションを Azure でクラウドにデプロイします。

- Code First Migrations を有効にします。 移行機能を使用すると、データ モデルを変更し、変更を削除して、データベースを再作成しなくても、データベース スキーマを更新することで運用環境にデプロイできます。
- Azure にデプロイします。 この手順は省略可能です。残りのチュートリアルを続行するには、プロジェクトをデプロイすることなしです。

ソース管理と展開については、継続的インテグレーション プロセスを使用するが、このチュートリアルではこれらのトピックについては説明しないことをお勧めします。 詳細については、次を参照してください。、[ソース管理](xref:aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control)と[継続的インテグレーション](xref:aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery)の章[Azure と実際のクラウド アプリの構築](xref:aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/introduction)します。

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * Code First migrations を有効にします。
> * (省略可能) Azure でアプリをデプロイします。

## <a name="prerequisites"></a>必須コンポーネント

- [接続復元性とコマンド傍受](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="enable-code-first-migrations"></a>Code First migrations を有効にします。

新しいアプリケーションを開発して、データ モデルが頻繁に変更される場合、モデルが変更されるたびに、モデルはデータベースと同期されなくなります。 自動的に削除し、データ モデルを変更するたびにデータベースを再作成するには、Entity Framework が構成されました。 ときに追加、削除、またはエンティティ クラスを変更または変更、`DbContext`クラスを次に、アプリケーションを実行したときに自動的に、既存のデータベースを削除します。、、モデルと一致し、テスト データのシードを設定するか新しいを作成します。

このメソッドは、実稼働環境にアプリケーションを展開するまで、データベースとデータ モデルの同期の維持がうまく機能します。 アプリケーションは、実稼働環境で実行中は、通常、保持しておくし、新しい列の追加などの変更を加えるたびにデータを全部失ってしたくないされるデータの格納には。 [Code First Migrations](https://msdn.microsoft.com/data/jj591621)機能は Code First を削除し、データベースを再作成ではなく、データベース スキーマの更新を有効にしてこの問題を解決します。 このチュートリアルでは、アプリケーションをデプロイしを準備する移行を実現します。

1. 前のコメント アウトまたは削除してセットアップする初期化子を無効にする、`contexts`アプリケーションの Web.config ファイルに追加する要素。

    [!code-xml[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.xml?highlight=2,6)]
2. アプリケーションでも*Web.config*ファイルで、接続文字列でデータベースの名前を ContosoUniversity2 に変更します。

    [!code-xml[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.xml?highlight=2)]

    この変更は、最初の移行は、新しいデータベースを作成できるように、プロジェクトを設定します。 これは必要ありませんが、後述することがお勧めします。
3. **[ツール]** メニューで、**[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** の順に選択します。

1. `PM>`プロンプトは、次のコマンドを入力します。

    ```text
    enable-migrations
    add-migration InitialCreate
    ```

    `enable-migrations`コマンドは、作成、*移行*ContosoUniversity プロジェクト、およびそのフォルダーはそのフォルダーに格納、 *Configuration.cs*移行を構成する編集可能なファイル。

    (データベース名を変更することを指示する上記の手順が実行されなかった場合、移行は既存のデータベースを見つけるし、自動的に実行、`add-migration`コマンド。 では、データベースを展開する前に、移行コードのテストを実行しないことだけを意味です。 実行すると、以降、`update-database`コマンドは、データベースが既に存在するために何も実行されません)。

    開く、 *ContosoUniversity\Migrations\Configuration.cs*ファイル。 初期化子クラスを前述のような`Configuration`クラスが含まれています、`Seed`メソッド。

    [!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs)]

    目的、[シード](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx)メソッドは、挿入または Code First を作成またはデータベースを更新した後、テスト データを更新できるようにします。 メソッドは、データベースが作成されると、そのデータ モデルを変更した後、データベース スキーマが更新されるたびに呼び出されます。

### <a name="set-up-the-seed-method"></a>Seed メソッドを設定します。

初期化子クラスを使用して削除し、データ モデルの変更はすべてのデータベースを再作成すると、ときに`Seed`モデル変更のたびに、データベースが削除されるため、テスト データを挿入するメソッドをおよびすべてのテスト データが失われます。 テスト データを保持するデータベースの変更後の Code First Migrations でにテスト データを含むため、[シード](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx)メソッドは通常必要ありません。 実際には、したくない、`Seed`場合、使用する移行、データベースを運用環境にデプロイするために、テスト データを挿入するメソッドを`Seed`メソッドは、実稼働環境で実行されます。 その場合は、`Seed`実稼働環境で、データベースに必要なデータのみを挿入するメソッド。 などの実際の部署名を含めるデータベースをする可能性があります、`Department`テーブルの場合、アプリケーションが運用環境で使用可能になります。

このチュートリアルで使用する移行展開についてが、`Seed`メソッドが大量のデータを手動で挿入することがなくアプリケーションの機能の動作を確認しやすくためにテスト データをも挿入されます。

1. 内容を置き換える、 *Configuration.cs*ファイルを次のコードは、新しいデータベースにテスト データを読み込みます。

    [!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs)]

    [シード](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx)メソッドは、入力パラメーターとして、データベース コンテキストのオブジェクトとメソッドのコードがそのオブジェクトを使用して、データベースに新しいエンティティを追加します。 エンティティ型ごとに、コードは、新しいエンティティのコレクションを作成、適切なに追加します[DbSet](https://msdn.microsoft.com/library/system.data.entity.dbset(v=vs.103).aspx)プロパティ、および、データベースへの変更を保存します。 呼び出す必要はありません、 [SaveChanges](https://msdn.microsoft.com/library/system.data.entity.dbcontext.savechanges(v=VS.103).aspx)メソッドのエンティティの各グループの後に、ここでは、として行われますが、これを行うコードは、データベースに書き込んでいるときに例外が発生した場合は、問題の原因を見つけることができます。

    一部のデータを挿入するステートメントを使用して、 [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) "upsert"操作を実行するメソッド。 `Seed`メソッドの実行を実行するたびに、`update-database`データベースを作成する最初の移行後に追加しようとしている行があるなる既にため、データを挿入できませんだけを各移行後に通常コマンドします。 "Upsert"操作が既に存在する、行を挿入しようとする場合に発生するとエラーを防ぐこと***オーバーライド***アプリケーションのテスト中に行ったデータに変更します。 いくつかのテーブルでのテスト データたくないことが起こる: 場合によってはテスト中にデータを変更すると、変更するデータベースの更新後に残します。 条件付きの insert 操作を実行する場合: 存在しない場合にのみ行を挿入します。 Seed メソッドでは、両方のアプローチを使用します。

    渡される最初のパラメーター、 [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx)メソッドを使用して、行が既に存在するかを確認するプロパティを指定します。 テスト受講者データを提供するには`LastName`各姓の一覧では一意であるために、この目的のプロパティを使用できます。

    [!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs)]

    このコードでは、姓と名が一意であることを前提としています。 重複する最後の名前を持つ学生を手動で追加するには、次回の移行を実行する次の例外が発生します。

    **シーケンスには、1 つ以上の要素が含まれています。**

    "Alexander Carson"という名前の 2 つの学生などの冗長なデータを処理する方法については、次を参照してください。[シード処理中、およびデバッグ Entity Framework (EF) Db](https://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx) Rick Anderson のブログ。 詳細については、`AddOrUpdate`メソッドを参照してください[EF 4.3 AddOrUpdate メソッドを使用して対処](http://thedatafarm.com/blog/data-access/take-care-with-ef-4-3-addorupdate-method/)Julie Lerman のブログ。

    作成するコード`Enrollment`エンティティがあることを前提、`ID`内のエンティティの値、`students`コレクション、コレクションを作成するコードでそのプロパティを設定していません。

    [!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs?highlight=2)]

    使用することができます、`ID`プロパティのため、`ID`を呼び出すときに値が設定`SaveChanges`の`students`コレクション。 EF は、エンティティをデータベースに挿入および更新時に自動的に主キーの値を取得、`ID`メモリ内のエンティティのプロパティ。

    各を追加するコード`Enrollment`エンティティを`Enrollments`エンティティ セットを使用していない、`AddOrUpdate`メソッド。 エンティティが既に存在し、存在しない場合は、エンティティを挿入かどうかを確認します。 このアプローチでは、アプリケーション UI を使用して、登録の成績に行った変更を保持します。 各メンバーがループ処理、 `Enrollment`[一覧](https://msdn.microsoft.com/library/6sh2ey19.aspx)とデータベースの登録が見つからない場合、データベースに登録を追加します。 最初に、データベースを更新するデータベースは空になりますので、各登録が追加されます。

    [!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs)]

2. プロジェクトをビルドします。

### <a name="execute-the-first-migration"></a>最初の移行を実行します。

実行すると、`add-migration`コマンド、移行は、最初からデータベースを作成するコードを生成します。 このコードもに、*移行*という名前のファイルのフォルダー *&lt;タイムスタンプ&gt;\_InitialCreate.cs*します。 `Up`のメソッド、`InitialCreate`クラスは、データ モデルのエンティティ セットに対応するデータベース テーブルを作成し、`Down`メソッドがそれらを削除します。

[!code-csharp[Main](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]

移行は、`Up` メソッドを呼び出して、移行のためのデータ モデルの変更を実装します。 更新をロールバックするためのコマンドを入力すると、移行が `Down` メソッドを呼び出します。

これは、入力したときに作成された最初の移行、`add-migration InitialCreate`コマンド。 パラメーター (`InitialCreate`例では) ファイルの使用は、単語または語句の移行に実行する事柄をまとめたものを選択する通常; 名前を指定し、必要な値を指定できます。 たとえば、後で移行を名前可能性があります&quot;AddDepartmentTable&quot;します。

データベースが既に存在するときに、初期移行を作成した場合、データベースの作成コードが生成されますが、データベースは既にデータと一致しているため、作成コードを実行する必要はありません。 データベースがまだ存在しない別の環境にアプリを展開する場合、データベースを作成するために、このコードが実行されるため、最初にテストを行うことをお勧めします。 理由で、前の接続文字列でデータベースの名前を変更した&mdash;移行は、新しいものをゼロから作成できるようにします。

1. **パッケージ マネージャー コンソール**ウィンドウで、次のコマンドを入力します。

    `update-database`

    `update-database`コマンドを実行、`Up`データベースし、作成するメソッドの実行、`Seed`メソッドは、データベースを設定します。 同じプロセスは自動的に実行実稼働環境で、アプリケーションの配置後ように、次のセクションが表示されます。
2. 使用**サーバー エクスプ ローラー**を最初のチュートリアルで行ったように、データベースを検査し、動作を確認することすべて引き続き同じ以前と同様、アプリケーションを実行します。

## <a name="deploy-to-azure"></a>Azure に配置する

これまでに、アプリケーションを実行したローカル IIS Express で開発用コンピューターにします。 他のユーザーに、インターネット経由で使用できるように、web ホスティング プロバイダーにデプロイがあります。 チュートリアルのこのセクションでは、Azure にデプロイします。 このセクションは省略可能です。これを省略して次のチュートリアルを続行または好みのさまざまなホスティング プロバイダーのこのセクションの手順を調整できます。

### <a name="use-code-first-migrations-to-deploy-the-database"></a>Code First migrations を使用して、データベースをデプロイするには

データベースを展開するには、Code First Migrations を使用します。 Visual Studio から展開の設定を構成するために使用する発行プロファイルを作成するときに、というラベルの付いたチェック ボックスをオンします**Update Database**します。 この設定により、自動的にアプリケーションを構成する展開プロセス*Web.config* Code First を使用するように、移行先サーバー上のファイル、`MigrateDatabaseToLatestVersion`初期化子クラス。

Visual Studio は何も、データベースと展開プロセス中に、移行先サーバーにプロジェクトをコピー中にします。 デプロイされたアプリケーションを実行して、デプロイ後に初めてデータベースにアクセスする、Code First によって確認されます、データベースがデータ モデルと一致するかどうか。 一致しない場合は、Code First が自動的に作成、データベース (まだ存在しない) 場合または (データベースが存在しますが、モデルと一致しない) 場合は、最新バージョンにデータベース スキーマを更新します。 アプリケーションは、移行を実装している場合`Seed`メソッド、メソッドの実行後、データベースが作成されるか、スキーマを更新します。

使用して移行`Seed`メソッドは、テスト データを挿入します。 運用環境にデプロイする、変更する必要があります、`Seed`メソッドを実稼働データベースに挿入するデータの挿入のみであるためです。 たとえば、現在のデータ モデルで開発用データベースに実際のコースが架空の受講者がある可能性があります。 記述することができます、`Seed`メソッドは負荷が開発では、両方とも、実稼働環境に展開する前に、架空の受講者をし、コメントをします。 したり、作成することができます、`Seed`のみのコースを読み込むし、アプリケーションの UI を使用して手動でテスト データベースで架空の受講者を入力します。

### <a name="get-an-azure-account"></a>Azure アカウントを取得します。

Azure アカウントが必要があります。 場合は、1 つ必要は既にありませんが、Visual Studio サブスクリプションが、[サブスクリプションの特典をアクティブ化](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/
)します。 それ以外の場合、ほんの数分で無料試用版アカウントを作成できます。 詳細については、[Azure 無料試用版](https://azure.microsoft.com/free/)を参照してください。

### <a name="create-a-web-site-and-a-sql-database-in-azure"></a>Azure での web サイトと SQL database を作成します。

Azure での web アプリは、Azure の他のクライアントと共有されている仮想マシン (Vm) で実行されることを意味する共有ホスティング環境で実行されます。 共有ホスティング環境は、クラウドで開始する低コスト方法です。 後で、web トラフィックが増加すると、アプリケーションは専用 Vm 上で実行して、ニーズを満たすためにスケールできます。 Azure App Service の価格オプションの詳細については、読み取る[App Service の価格](https://azure.microsoft.com/pricing/details/app-service/)します。

Azure SQL database にデータベースをデプロイします。 SQL database とは、SQL Server テクノロジに基づいて構築されたクラウド ベースのリレーショナル データベース サービスです。 ツールおよび SQL Server で動作するアプリケーションは、SQL database でも動作します。

1. [Azure 管理ポータル](https://portal.azure.com)、選択**リソースの作成**左にあるタブをクリックして**すべて表示**上、**新規**ウィンドウ (または*ブレード*) を使用可能なすべてのリソースを参照してください。 選択**Web アプリ + SQL**で、 **Web**のセクション、**すべて**ブレード。 最後に、選択**作成**です。

    ![Azure portal でリソースを作成します。](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/create-azure-resource.png)

   新たに作成するフォーム**新しい Web アプリ + SQL**リソースが表示されます。

2. 文字列を入力して、**アプリ名**ボックス、アプリケーションの一意の URL として使用します。 完全な URL が入力した内容のここでさらに Azure App Services の既定のドメインで構成されます (. azurewebsites.net)。 場合、**アプリ名**を既に取得して、ウィザードに通知する赤い*アプリ名をご利用いただけません*メッセージ。 場合、**アプリ名**は緑色のチェック マークを表示、使用可能な。

3. **サブスクリプション**ボックスで、選択する Azure サブスクリプション、 **App Service**を配置します。

4. **リソース グループ**テキスト ボックスに、リソース グループを選択するか、新しく作成します。 この設定では、web サイトが実行されるデータ センターを指定します。 リソース グループの詳細については、次を参照してください。[リソース グループ](/azure/azure-resource-manager/resource-group-overview#resource-groups)します。

5. 新規作成**App Service プラン**をクリックして、 *App Service のセクション*、**新規作成**、入力と**App Service プラン**(と同じ名前を指定できますApp Service の場合)、**場所**、および**価格レベル**(無料のオプションがある)。

6. をクリックして**SQL Database**を選び、**新しいデータベースを作成**または既存のデータベースを選択します。

7. **名前**ボックスに、データベースの名前を入力します。
8. をクリックして、**ターゲット サーバー**ボックスを選び**サーバーの新規作成**です。 または、以前にサーバーを作成する場合は、使用可能なサーバーの一覧からそのサーバーを選択できます。
9. 選択**価格レベル**セクションで、選択*Free*します。 その他のリソースが必要な場合、データベースをいつでもまで拡張できます。 Azure SQL の料金の詳細については、次を参照してください。 [Azure SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/managed/)します。
10. 変更[照合](/sql/relational-databases/collations/collation-and-unicode-support)に応じて。
11. 管理者が入力**SQL 管理ユーザー名**と**SQL 管理パスワード**します。

    - 選択した場合**新しい SQL Database server**、新しい名前とデータベースにアクセスするときに後で使用するパスワードを定義します。
    - 以前に作成したサーバーを選択した場合は、そのサーバーの資格情報を入力します。

12. Application Insights を使用した App Service にテレメトリの収集を有効にすることができます。 ほとんどの構成では、Application Insights は、重要なイベント、例外、依存関係、要求、およびトレース情報を収集します。 Application Insights の詳細については、次を参照してください。 [Azure Monitor](https://azure.microsoft.com/services/monitor/)します。
13. をクリックして**作成**下部を完了することを示します。

    管理ポータルのダッシュ ボード ページに戻り、**通知**ページの上部にある領域は、サイトが作成されていることを示しています。 (通常、1 分未満)、しばらくしてから、デプロイが成功したことを示す通知があります。 新しい App Service は、左側にあるナビゲーション バーで、 **App Services**セクションと、新しい SQL データベースで表示されます、 **SQL データベース**セクション。

### <a name="deploy-the-app-to-azure"></a>Azure にアプリを配置する

1. Visual Studio でのプロジェクトを右クリックして**ソリューション エクスプ ローラー**選択と**発行**コンテキスト メニュー。

2. **発行先を選択**ページで、選択**App Service**し**既存の**を選び、**発行**。

    ![発行先 ページを選択します。](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/publish-select-existing-azure-app-service.png)

3. Visual Studio で、Azure サブスクリプションを追加していない以前の場合は、画面に、手順を実行します。 次の手順が、Azure サブスクリプションに接続する Visual Studio のリストを有効にする**App Services** web サイトが含まれます。

4. **App Service** ] ページで、[、**サブスクリプション**に App Service を追加します。 **ビュー**、**リソース グループ**します。 App Service を追加したリソース グループを展開し、アプリ サービスを選択します。 選択**OK**アプリを発行します。

5. **出力**ウィンドウが実行されたどのようなデプロイ操作を示しています。 され、展開が正常に完了を報告します。

6. デプロイが成功、既定のブラウザーは自動的にデプロイされた web サイトの URL を開きます。

    ![Students_index_page_with_paging](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/cloud-app-browser.png)

    クラウドでアプリが実行されているようになりました。

この時点で、 *SchoolContext*選択したので、Azure SQL database でデータベースが作成されている**実行 Code First Migrations (アプリの起動時に実行)** します。 *Web.config*デプロイされた web サイトのファイルが変更されているように、 [MigrateDatabaseToLatestVersion](https://msdn.microsoft.com/library/hh829476(v=vs.103).aspx)初期化子は初めて、コードの読み取りまたは (これが発生したデータベースでのデータの書き込みを実行します。選択した場合、**学生** タブ)。

![Web.config file excerpt](https://asp.net/media/4367421/mig.png)

展開プロセスは、新しい接続文字列を作成することも *(SchoolContext\_DatabasePublish*)、データベース スキーマを更新して、データベースをシード処理を使用する Code First Migrations に対する。

![Web.config ファイル内の接続文字列](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image26.png)

デプロイされたバージョンので、自分のコンピューター上の Web.config ファイルを検索する*ContosoUniversity\obj\Release\Package\PackageTmp\Web.config*します。デプロイされているアクセスできる*Web.config* FTP を使用してファイル自体。 手順については、次を参照してください。 [Visual Studio を使用して ASP.NET Web 展開。コードの更新を展開する](xref:web-forms/overview/deployment/visual-studio-web-deployment/deploying-a-code-update)します。 始まる手順"、FTP ツールを使用するには、するには、次の 3 つが必要です: FTP の URL、ユーザー名、およびパスワード。"。

> [!NOTE]
> Web アプリの URL を見つけた他のユーザー データを変更できるように、セキュリティを実装しません。 Web サイトをセキュリティで保護する方法の詳細については、次を参照してください。[メンバーシップ、OAuth、および SQL database で安全な ASP.NET MVC アプリを Azure にデプロイ](/aspnet/core/security/authorization/secure-data)します。 他のユーザーが Azure 管理ポータルを使用してサービスを停止して、サイトの使用禁止または**サーバー エクスプ ローラー** Visual Studio でします。

![アプリ サービスのメニュー項目を停止します。](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application/_static/server-explorer-stop-app-service.png)

## <a name="advanced-migrations-scenarios"></a>高度な移行シナリオ

このチュートリアルで示すように自動的に移行を実行してデータベースを展開する複数のサーバーで実行されている web サイトにデプロイする場合は、移行を同時に実行しようとしています。 複数のサーバーを取得できます。 2 台のサーバーでは、同じ移行を実行しようとして、1 つは成功し、(2 回の操作を実行できませんを想定) が失敗、その他の移行はアトミックで。 このシナリオでは、これらの問題を回避する場合の移行を手動で呼び出すしてのみ 1 回行われるように、独自のコードを設定します。 詳細については、次を参照してください。[実行とコードからの移行のスクリプト](http://romiller.com/2012/02/09/running-scripting-migrations-from-code/)Rowan Miller のブログと[Migrate.exe](/ef/ef6/modeling/code-first/migrations/migrate-exe) (コマンドラインからの移行を実行して) 用です。

その他の移行シナリオについては、次を参照してください。[移行スクリーン キャスト シリーズ](https://blogs.msdn.com/b/adonet/archive/2014/03/12/migrations-screencast-series.aspx)します。

## <a name="code-first-initializers"></a>コードの最初の初期化子

展開セクションで説明しました、 [MigrateDatabaseToLatestVersion](https://msdn.microsoft.com/library/hh829476(v=vs.103).aspx)初期化子が使用されています。 最初のコードなどの他の初期化子を提供[CreateDatabaseIfNotExists](https://msdn.microsoft.com/library/gg679221(v=vs.103).aspx) (既定)、 [DropCreateDatabaseIfModelChanges](https://msdn.microsoft.com/library/gg679604(v=VS.103).aspx) (これは、以前に使用した) と[DropCreateDatabaseAlways](https://msdn.microsoft.com/library/gg679506(v=VS.103).aspx)します。 `DropCreateAlways`初期化子は、単体テストの条件の設定に役立ちます。 独自の初期化子を記述することもでき、明示的に、アプリケーションから読み取るか、データベースに書き込むまで待機するたくない場合、初期化子を呼び出すことができます。

初期化子の詳細については、次を参照してください。 [Entity Framework Code First でデータベース初期化子を理解する](http://www.codeguru.com/csharp/article.php/c19999/Understanding-Database-Initializers-in-Entity-Framework-Code-First.htm)と書籍の第 6 章[Entity Framework のプログラミング。コード ファースト](http://shop.oreilly.com/product/0636920022220.do)を Julie Lerman が Rowan Miller が。

## <a name="get-the-code"></a>コードを取得する

[完成したプロジェクトをダウンロードします。](https://webpifeed.blob.core.windows.net/webpifeed/Partners/ASP.NET%20MVC%20Application%20Using%20Entity%20Framework%20Code%20First.zip)

## <a name="additional-resources"></a>その他の技術情報

その他の Entity Framework リソースへのリンクが記載[ASP.NET データ アクセス - 推奨リソース](xref:whitepapers/aspnet-data-access-content-map)します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * Code First migrations を有効になっています。
> * (省略可能) Azure でアプリをデプロイ

ASP.NET MVC アプリケーションのより複雑なデータ モデルを作成する方法については、次の記事に進んでください。
> [!div class="nextstepaction"]
> [複雑なデータ モデルを作成します。](creating-a-more-complex-data-model-for-an-asp-net-mvc-application.md)
