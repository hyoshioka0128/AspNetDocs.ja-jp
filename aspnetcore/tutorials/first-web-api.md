---
title: 'チュートリアル: ASP.NET Core MVC で Web API を作成する'
author: rick-anderson
description: ASP.NET Core MVC で Web API をビルドする
ms.author: riande
ms.custom: mvc
ms.date: 02/4/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 686397cd25248ce7b37e505c7129a3b56d4ada1b
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57045779"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core-mvc"></a>チュートリアル: ASP.NET Core MVC で Web API を作成する

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) と [Mike Wasson](https://github.com/mikewasson)

このチュートリアルでは、ASP.NET Core で Web API をビルドする方法の基本について説明します。

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
> * Web API プロジェクトを作成する。
> * モデル クラスを追加する。
> * データベース コンテキストを作成する。
> * データベース コンテキストを登録する。
> * コントローラーを追加する。
> * CRUD メソッドを追加する。
> * ルーティングと URL パスを構成する。
> * 戻り値を指定する。
> * Postman で Web API を呼び出す。
> * jQuery で Web API を呼び出す。

最後に、リレーショナル データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。

## <a name="overview"></a>概要

このチュートリアルでは、次の API を作成します。

|API | 説明 | 要求本文 | 応答本文 |
|--- | ---- | ---- | ---- |
|GET /api/todo | すべての To Do アイテムを取得します。 | なし | To Do アイテムの配列|
|GET /api/todo/{id} | ID でアイテムを取得します。 | なし | To Do アイテム|
|POST /api/todo | 新しいアイテムを追加します。 | To Do アイテム | To Do アイテム |
|PUT /api/todo/{id} | 既存のアイテムを更新します。&nbsp; | To Do アイテム | なし |
|DELETE /api/todo/{id} &nbsp; &nbsp; | アイテムを削除します &nbsp; &nbsp; | なし | なし|

次の図は、アプリのデザインを示しています。

![クライアントは左側のボックスで表され、要求を送信し、アプリケーション (右側に描画されたボックス) から応答を受信します。 アプリケーション ボックス内の 3 つのボックスは、コントローラー、モデル、およびデータ アクセス レイヤーを表しています。 要求はアプリケーションのコントローラーに送られ、コントローラーとデータ アクセス レイヤー間で読み取り/書き込み操作が行われます。 モデルはシリアル化され、応答でクライアントに返されます。](first-web-api/_static/architecture.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-web-project"></a>Web プロジェクトを作成する

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **[ファイル]** メニューで、**[新規作成]**、 > **[プロジェクト]** の順に作成します。
* **[ASP.NET Core Web アプリケーション]** テンプレートを選択します。 プロジェクトに「*TodoApi*」という名前を付け、**[OK]** をクリックます。
* **[New ASP.NET Core Web Application - TodoApi]\(新しい ASP.NET Core Web アプリケーション - TodoApi\)** ダイアログで、ASP.NET Core のバージョンを選択します。 **API** テンプレートを選択し、**[OK]** をクリックします。 **[Enable Docker Support]\(Docker サポートを有効にする\)** は**選択しないで**ください。

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。
* ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。
* 次のコマンドを実行します。

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  これらのコマンドでは、新しい Web API プロジェクトが作成され、新しいプロジェクト フォルダー内に Visual Studio Code の新しいインスタンスが開かれます。

* ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、**[はい]** を選択します。

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* **[ファイル]**、**[新しいソリューション]** の順に選択します。

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* **[.NET Core アプリ]**、 > **[ASP.NET Core Web API]**、 > **[次へ]** の順に選択します。

  ![macOS の [新しいプロジェクト] ダイアログ](first-web-api-mac/_static/1.png)
  
* **[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログ ボックスで、既定の**ターゲット フレームワーク** **.NET Core 2.2* を受け入れます。

* **[プロジェクト名]** に「*TodoApi*」と入力し、**[作成]** を選択します。

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a>API のテスト

プロジェクト テンプレートによって `values` API が作成されます。 ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Ctrl キーを押しながら F5 キーを押して、アプリを実行します。 Visual Studio でブラウザーが起動し、`https://localhost:<port>/api/values` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。

IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、**[はい]** を選択します。 次に表示される **[セキュリティ警告]** ダイアログ ボックスで、**[はい]** を選択します。

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ctrl キーを押しながら F5 キーを押して、アプリを実行します。 ブラウザーで、次の URL に移動します: [https://localhost:5001/api/values](https://localhost:5001/api/values)。

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

**[実行]**、**[デバッグありで開始]** の順に選択してアプリを起動します。 Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。 HTTP 404 (Not Found) エラーが返されます。 URL に `/api/values` を追加します (URL を `https://localhost:<port>/api/values` に変更します)。

---

次の JSON が返されます。

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a>モデル クラスの追加

*モデル*は、アプリが管理するデータを表すクラスのセットです。 このアプリのモデルは、単一の `TodoItem` クラスです。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **ソリューション エクスプローラー**で、プロジェクトを右クリックします。 **[追加]** > **[新しいフォルダー]** の順に選択します。 フォルダーに *Models* という名前を付けます。

* *Models* フォルダーを右クリックし、**[追加]** > **[クラス]** の順にクリックします。 クラスに「*TodoItem*」という名前を付け、**[追加]** を選択します。

* テンプレート コードを次のコードに置き換えます。

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Models* という名前のフォルダーを追加します。

* 次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* プロジェクトを右クリックします。 **[追加]** > **[新しいフォルダー]** の順に選択します。 フォルダーに *Models* という名前を付けます。

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* *Models* フォルダーを右クリックし、**[追加]**、**[新しいファイル]**、**[全般]**、**[空のクラス]** の順に選択します。

* クラスに「*TodoItem*」という名前を付け、**[新規]** をクリックします。

* テンプレート コードを次のコードに置き換えます。

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。

モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。

## <a name="add-a-database-context"></a>データベース コンテキストの追加

*データベース コンテキスト*は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。 このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* *Models* フォルダーを右クリックし、**[追加]** > **[クラス]** の順にクリックします。 クラスに「*TodoContext*」という名前を付け、**[追加]** をクリックします。

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* *Models* フォルダーに `TodoContext` クラスを追加します。

---

* テンプレート コードを次のコードに置き換えます。

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>データベース コンテキストの登録

ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI) コンテナー](xref:fundamentals/dependency-injection)に登録する必要があります。 コンテナーは、コントローラーにサービスを提供します。

次の強調表示されているコードを使用して、*Startup.cs* を更新します。

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

上のコードでは以下の操作が行われます。

* 不要な `using` 宣言を削除します。
* DI コンテナーにデータベース コンテキストを追加します。
* データベース コンテキストがメモリ内データベースを使用することを指定します。

## <a name="add-a-controller"></a>コントローラーの追加

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* *Controllers* フォルダーを右クリックします。
* **[追加]** > **[新しい項目]** の順に選択します。
* **[新しい項目の追加]** ダイアログで、**[API コントローラー クラス]** テンプレートを選択します。
* クラスに「*TodoController*」という名前を付け、**[追加]** を選択します。

  ![[新しい項目の追加] ダイアログ。検索ボックスに「controller」と入力されています。Web API コントローラーが選択されています。](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* *Controllers* フォルダーで、「`TodoController`」という名前のクラスを作成します。

---

* テンプレート コードを次のコードに置き換えます。

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

上のコードでは以下の操作が行われます。

* メソッドを使用せず、API コントローラー クラスを定義します。
* クラスを [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性で修飾します。 この属性は、コントローラーが Web API 要求に応答することを示します。 属性によって有効化される特定の動作については、「[ApiController 属性を使用した注釈](xref:web-api/index#annotation-with-apicontroller-attribute)」を参照してください。
* DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。 データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。
* 追加データベースが空の場合、`Item1` という名前のアイテムをデータベースにします。 このコードはコンストラクター内にあるので、新しい HTTP 要求が行われるたびに実行されます。 すべてのアイテムを削除した場合、コンストラクターは、次回に API メソッドが呼び出されたときに `Item1` をもう一度作成します。 そのため、削除が実際には機能していても、機能しなかったように見える場合があります。

## <a name="add-get-methods"></a>Get メソッドの追加

To Do アイテムを取得する API を指定するには、`TodoController` クラスに次のメソッドを追加します。

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

これらのメソッドは、次の 2 つの GET エンドポイントを実装します。

* `GET /api/todo`
* `GET /api/todo/{id}`

ブラウザーからこれらの 2 つのエンドポイントを呼び出すことによって、アプリをテストします。 例:

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

`GetTodoItems` への呼び出しによって、次の HTTP 応答が生成されます。

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a>ルーティングと URL パス

[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性は、HTTP GET 要求に応答するメソッドを表します。 各メソッドの URL パスは次のように構成されます。

* コントローラーの `Route` 属性でテンプレート文字列を使用します。

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* `[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。 このサンプルでは、コントローラー クラス名は **Todo**Controller なので、コントローラー名は "todo" です。 ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。
* `[HttpGet]` 属性にルート テンプレート (例: `[HttpGet("products")]`) がある場合は、それをパスに追加します。 このサンプルではテンプレートを使用しません。 詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。

次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。 `GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>戻り値

`GetTodoItems` メソッドと `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T > 型](xref:web-api/action-return-types#actionresultt-type)です。 ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。 この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。 ハンドルされない例外は 5xx エラーに変換されます。

`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。 たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。

* 要求された ID と一致するアイテムがない場合、メソッドは 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) エラー コードを返します。
* それ以外の場合、メソッドは JSON 応答本文で 200 を返します。 戻り値が `item` の場合、HTTP 200 応答が返されます。

## <a name="test-the-gettodoitems-method"></a>GetTodoItems メソッドのテスト

このチュートリアルでは、Postman を使用して Web API をテストします。

* [Postman](https://www.getpostman.com/apps) をインストールします。
* Web アプリを起動します。
* Postman を起動します。
* **SSL 証明書の検証**を無効にします。
  
  * **[ファイル] > [設定]** (\* *[全般]* タブ) で、 **SSL 証明書の検証** を無効にします。
    > [!WARNING]
    > コントローラーをテストした後、SSL 証明書の検証を再度有効にします。

* 新しい要求を作成します。
  * HTTP メソッドを **GET** に設定します。
  * 要求 URL を `https://localhost:<port>/api/todo` に設定します。 たとえば、`https://localhost:5001/api/todo` のようにします。
* Postman で **[Two pane view]** を設定します。
* **[Send]** を選択します。

![Postman での Get 要求](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a>Create メソッドの追加

次の `PostTodoItem` メソッドを追加します。

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

上記のコードは、[[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性で示されるように、HTTP POST メソッドです。 このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。

`CreatedAtAction` メソッド:

* 成功すると、HTTP 201 状態コードが返されます。 HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。
* `Location` ヘッダーを応答に追加します。 `Location` ヘッダーでは、新しく作成された To Do アイテムの URI を指定します。 詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。
* `GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。 C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a>PostTodoItem メソッドのテスト

* プロジェクトをビルドします。
* Postman で、HTTP メソッド名を `POST` に設定します。
* **[Body]** タブを選択します。
* **[raw]** ラジオ ボタンを選択します。
* 型を **[JSON (application/json)]** に設定します。
* 要求本文に、To Do アイテムの JSON を入力します。

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* **[Send]** を選択します。

  ![Postman での Create 要求](first-web-api/_static/create.png)

  405 (Method Not Allowed) エラーが発生した場合は、`PostTodoItem` メソッドの追加後にプロジェクトをコンパイルしていないことが原因である可能性があります。

### <a name="test-the-location-header-uri"></a>場所ヘッダー URI のテスト

* **[Response]** ウィンドウで、**[Headers]** タブを選択します。
* **[Location]** ヘッダー値をコピーします。

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/pmc2.png)

* メソッドを GET に設定します。
* URI を貼り付けます (たとえば、`https://localhost:5001/api/Todo/2`)。
* **[Send]** を選択します。

## <a name="add-a-puttodoitem-method"></a>PutTodoItem メソッドの追加

次の `PutTodoItem` メソッドを追加します。

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。 応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。 HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。 部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。

`PutTodoItem` を呼び出してエラーが表示された場合、`GET` を呼び出し、データベースに項目があることを確認してください。

### <a name="test-the-puttodoitem-method"></a>PutTodoItem メソッドのテスト

このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。 PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。 GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが存在していることを確認します。

ID = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

次の図は、Postman の更新を示しています。

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a>DeleteTodoItem メソッドの追加

次の `DeleteTodoItem` メソッドを追加します。

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

`DeleteTodoItem` の応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。

### <a name="test-the-deletetodoitem-method"></a>DeleteTodoItem メソッドのテスト

Postman を使用して、To Do アイテムを削除します。

* メソッドを `DELETE` に設定します。
* 削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/todo/1`) を設定します。
* **[Send]** を選択します。

サンプル アプリではすべてのアイテムを削除できますが、最後のアイテムが削除されると、次回 API が呼び出されたときに、モデル クラス コンストラクターによって新しいアイテムが作成されます。

## <a name="call-the-api-with-jquery"></a>jQuery での API の呼び出し

このセクションでは、jQuery を使用して Web API を呼び出す、HTML ページが追加されます。 jQuery で要求を開始し、API の応答からの詳細を含むページを更新します。

[静的ファイルを提供し](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)、[既定のファイル マッピングを有効にする](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)ようにアプリを構成します。

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

::: moniker range=">= aspnetcore-2.2"
プロジェクト ディレクトリで *wwwroot* フォルダーを作成します。
::: moniker-end

*index.html* という名前の HTML ファイルを *wwwroot* ディレクトリに追加します。 その内容を次のマークアップに置き換えます。

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

*site.js* という名前の JavaScript ファイルを *wwwroot* ディレクトリに追加します。 その内容を次のコードに置き換えます。

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

ローカルで HTML ページをテストするために、ASP.NET Core プロジェクトの起動設定への変更が要求される場合があります。

* *Properties\launchSettings.json* を開きます。
* アプリが強制的に *index.html*&mdash;プロジェクトの既定ファイルで開くようにするには、`launchUrl` プロパティを削除します。

jQuery を取得するには、いくつかの方法があります。 前のスニペットでは、ライブラリは CDN から読み込まれます。

このサンプルでは、API のすべての CRUD メソッドを呼び出します。 次に、API の呼び出しについて説明します。

### <a name="get-a-list-of-to-do-items"></a>To Do アイテムのリストの取得

jQuery [ajax](https://api.jquery.com/jquery.ajax/) 関数は、`GET` 要求を API に送信します。この API は、To Do アイテムの配列を表す JSON を返します。 要求が成功した場合、`success` コールバック関数が呼び出されます。 コールバックでは、DOM は To Do 情報で更新されます。

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a>To Do アイテムの追加

[ajax](https://api.jquery.com/jquery.ajax/) 関数は、要求本文内で To Do アイテムと共に `POST` 要求を送信します。 `accepts` オプションと `contentType` オプションは `application/json` に設定されて、送受信されるメディアの種類を指定します。 To Do アイテムは、[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) を使用して JSON に変換されます。 API で正常な状況コードが返された場合、`getData` 関数が呼び出され、HTML テーブルを更新します。

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a>To Do アイテムの更新

To Do アイテムの更新は、追加操作に似ています。 アイテムの一意の識別子を追加するように `url` が変更され、`type` は `PUT` となります。

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a>To Do アイテムの削除

To Do アイテムを削除するには、`DELETE` への AJAX 呼び出しで `type` を設定して、URL でアイテムの一意の識別子を指定します。

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

## <a name="additional-resources"></a>その他の技術情報

[このチュートリアルのサンプル コードを表示またはダウンロード](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)します。 [ダウンロード方法](xref:index#how-to-download-a-sample)に関するページを参照してください。

詳細については、次のリソースを参照してください。

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/index>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の作業を行う方法を学びました。

> [!div class="checklist"]
> * Web API プロジェクトを作成する。
> * モデル クラスを追加する。
> * データベース コンテキストを作成する。
> * データベース コンテキストを登録する。
> * コントローラーを追加する。
> * CRUD メソッドを追加する。
> * ルーティングと URL パスを構成する。
> * 戻り値を指定する。
> * Postman で Web API を呼び出す。
> * jQuery で Web API を呼び出す。

API ヘルプ ページを生成する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>
