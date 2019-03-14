---
title: ASP.NET Core でのログ記録
author: tdykstra
description: ASP.NET Core でのログ記録フレームワークについて説明します。 組み込みのログ プロバイダーと、サードパーティ製の一般的なプロバイダーについて説明します。
ms.author: tdykstra
ms.custom: mvc
ms.date: 01/14/2019
uid: fundamentals/logging/index
---
# <a name="logging-in-aspnet-core"></a>ASP.NET Core でのログ記録

執筆: [Steve Smith](https://ardalis.com/)、[Tom Dykstra](https://github.com/tdykstra)

ASP.NET Core では、組み込みやサード パーティ製のさまざまなログ プロバイダーと連携するログ API がサポートされています。 この記事では、組み込みプロバイダーと共にログ API を使用する方法について説明します。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="add-providers"></a>プロバイダーを追加する

ログ プロバイダーによってログが表示または格納されます。 たとえば、Console プロバイダーによってコンソール上にログが表示され、Azure Application Insights プロバイダーによってそれらが Azure Application Insights に格納されます。 複数のプロバイダーを追加することで、複数の宛先にログを送信することができます。

::: moniker range=">= aspnetcore-2.0"

プロバイダーを追加するには、*Program.cs* でプロバイダーの `Add{provider name}` 拡張メソッドを呼び出します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=17-19)]

既定のプロジェクト テンプレートでは、次のログ プロバイダーを追加する <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 拡張メソッドが呼び出されます。

* コンソール
* デバッグ
* EventSource (ASP.NET Core 2.2 以降)

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

`CreateDefaultBuilder` を使用する場合は、既定のプロバイダーを自分で選択したものと置き換えることができます。 <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> を呼び出し、目的のプロバイダーを追加します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

プロバイダーを使用するには、その NuGet パッケージをインストールし、<xref:Microsoft.Extensions.Logging.ILoggerFactory> のインスタンスに対してプロバイダーの拡張メソッドを呼び出します。

[!code-csharp[](index/samples/1.x/TodoApiSample//Startup.cs?name=snippet_AddConsoleAndDebug&highlight=3,5-7)]

ASP.NET Core の[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) には、`ILoggerFactory` インスタンスが用意されています。 `AddConsole` および `AddDebug` 拡張メソッドは、[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) パッケージと [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) パッケージで定義されています。 各拡張メソッドで `ILoggerFactory.AddProvider` メソッドを呼び出し、プロバイダーのインスタンスで渡します。

> [!NOTE]
> [サンプル アプリ](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/1.x)では、`Startup.Configure` メソッドにログ プロバイダーを追加しています。 前の手順で実行したコードのログ出力を取得するには、`Startup` クラス コンストラクターにログ プロバイダーを追加します。

::: moniker-end

この記事の後半では、[組み込みログ プロバイダー](#built-in-logging-providers)と[サードパーティ製ログ プロバイダー](#third-party-logging-providers)について説明します。

## <a name="create-logs"></a>ログを作成する

DI から <xref:Microsoft.Extensions.Logging.ILogger`1> オブジェクトを取得します。

::: moniker range=">= aspnetcore-2.0"

次のコントローラーの例では、`Information` と `Warning` ログを作成します。 "*カテゴリ*" は `TodoApiSample.Controllers.TodoController` (サンプル アプリの `TodoController` の完全修飾クラス名) です。

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=4,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

次の Razor Pages の例では、`Information` を "*レベル*" に、`TodoApiSample.Pages.AboutModel` を "*カテゴリ*" に設定してログを作成します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3, 7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

前の例では、`Information` と `Warning` を "*レベル*" に、`TodoController` クラスを "*カテゴリ*" に設定してログを作成しています。 

::: moniker-end

ログの "*レベル*" は、ログに記録されるイベントの重大度を示します。 ログの "*カテゴリ*" は、各ログに関連付けられている文字列です。 `ILogger<T>` インスタンスでは、カテゴリとして型 `T` の完全修飾名を持つログが作成されます。 [レベル](#log-level)と[カテゴリ](#log-category)の詳細については、この記事で後ほど説明します。 

::: moniker range=">= aspnetcore-2.0"

### <a name="create-logs-in-startup"></a>Startup でログを作成する

`Startup` クラスでログを作成するには、コンストラクター シグネチャに `ILogger` パラメーターを追加します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,19,26)]

### <a name="create-logs-in-program"></a>プログラムでログを作成する

`Program` クラスでログを作成するには、DI から `ILogger` インスタンスを取得します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a>非同期でないロガー メソッド

ログ記録は高速に実行され、非同期コードのパフォーマンス コストを下回る必要があります。 ログ記録のデータ ストアが低速の場合は、そこへ直接書き込むべきではありません。 まずログ メッセージを高速なストアに書き込んでから、後で低速なストアに移動する方法を検討してください。 たとえば、メッセージ キューにログを記録し、別のプロセスで読み取り、低速なストレージに保存します。

## <a name="configuration"></a>構成

ログ プロバイダーの構成は、1 つまたは複数の構成プロバイダーによって提供されます。

* ファイル形式 (INI、JSON、および XML)。
* コマンド ライン引数。
* 環境変数。
* メモリ内 .NET オブジェクト。
* 暗号化されていない[シークレット マネージャー](xref:security/app-secrets)の記憶域。
* [Azure Key Vault](xref:security/key-vault-configuration) などの暗号化されたユーザー ストア。
* カスタム プロバイダー (インストール済みまたは作成済み)。

たとえば、一般的に、ログの構成はアプリ設定ファイルの `Logging` セクションで指定されます。 次の例は、一般的な *appsettings.Development.json* ファイルの内容を示しています。

::: moniker range=">= aspnetcore-2.1"

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

`Logging` プロパティには `LogLevel` およびログ プロバイダーのプロパティ (Console が示されています) を含めることができます。

`Logging` の下の `LogLevel` プロパティでは、選択したカテゴリに対するログの最小の[レベル](#log-level)が指定されます。 この例では、`System` と `Microsoft` カテゴリが `Information` レベルで、その他はすべて `Debug` レベルでログに記録します。

`Logging` の下のその他のプロパティではログ プロバイダーが指定されます。 この例では、Console プロバイダーです。 プロバイダーで[ログのスコープ](#log-scopes)がサポートされている場合、`IncludeScopes` によってそれを有効にするかどうかが指定されます。 プロバイダーのプロパティ (例の `Console` など) では、`LogLevel` プロパティが指定される場合があります。 プロバイダーの下の `LogLevel` では、そのプロバイダーのログのレベルが指定されます。

`Logging.{providername}.LogLevel` でレベルが指定される場合、それによって `Logging.LogLevel` で設定されたものはすべてオーバーライドされます。

::: moniker-end

::: moniker range="< aspnetcore-2.1"

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    }
  }
}
```

`LogLevel` キーは、ログ名を表します。 `Default` キーは明示的に表示されていないログに適用されます。 値は、指定されたログに適用された[ログ レベル](#log-level)を表します。

::: moniker-end

構成プロバイダーの実装について詳しくは、<xref:fundamentals/configuration/index> をご覧ください。

## <a name="sample-logging-output"></a>サンプルのログ記録の出力

前のセクションで紹介したサンプル コードでは、コマンド ラインからアプリを実行するとコンソールにログが表示されます。 コンソールの出力例は次のとおりです。

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

前のログは、`http://localhost:5000/api/todo/0` のサンプル アプリに向けて HTTP Get 要求を作成することで生成されました。

Visual Studio でサンプル アプリを実行したときに [デバッグ] ウィンドウに表示されるログと同じログの例を、次に示します。


```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

前のセクションで紹介した `ILogger` の呼び出しで作成されるログは、"TodoApi.Controllers.TodoController" から始まります。 "Microsoft" カテゴリから始まるログは、ASP.NET Core のフレームワークのコードからのログです。 ASP.NET Core とアプリケーション コードでは、同じログ API とログ プロバイダーが使用されています。

以降、この記事では、ログ記録の詳細とオプションについて説明します。

## <a name="nuget-packages"></a>NuGet パッケージ

`ILogger` および `ILoggerFactory` インターフェイスは、[Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) 内にあり、それらの既定の実装は [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) 内にあります。

## <a name="log-category"></a>ログのカテゴリ

`ILogger` オブジェクトが作成されるときに、その "*カテゴリ*" が指定されます。 このカテゴリは、その `Ilogger` のインスタンスによって作成される各ログ メッセージと共に含められます。 カテゴリには任意の文字列を指定できますが、"TodoApi.Controllers.TodoController" などのクラス名を使用するが慣例です。

`ILogger<T>` を使用して、カテゴリとして `T` の完全修飾型名が使用される `ILogger` インスタンスを取得します。

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

カテゴリを明示的に指定するには、`ILoggerFactory.CreateLogger` を呼び出します。

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

`ILogger<T>` は、`T` の完全修飾型名を使用した `CreateLogger` の呼び出しと同じです。

## <a name="log-level"></a>ログ レベル

すべてのログで <xref:Microsoft.Extensions.Logging.LogLevel> 値が指定されます。 ログ レベルは、重大度または重要度を示します。 たとえば、メソッドが正常に終了した場合は `Information` ログを、メソッドが *404 Not Found* 状態コードを返した場合は `Warning` ログを書き込む場合があります。

`Information` および `Warning` ログを作成するコードを次に示します。

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

前のコードでは、最初のパラメーターは[ログ イベント ID](#log-event-id) です。 2 つ目のパラメーターは、他のメソッド パラメーターによって提供される引数値のプレースホルダーを含むメッセージ テンプレートです。 メソッド パラメーターについては、この記事の後半の[メッセージ テンプレートのセクション](#log-message-template)で説明します。

メソッド名にレベルを含むログ メソッド (たとえば `LogInformation` や `LogWarning`) は、[ILogger の拡張メソッド](xref:Microsoft.Extensions.Logging.LoggerExtensions)です。 これらのメソッドでは、`LogLevel` パラメーターを受け取る `Log` メソッドが呼び出されます。 これらの拡張メソッドのいずれかではなく、`Log` メソッドを直接呼び出すことができますが、構文は比較的複雑です。 詳細については、<xref:Microsoft.Extensions.Logging.ILogger> および[ロガー拡張ソース コード](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)に関するページを参照してください。

ASP.NET Core には、次のログ レベルが定義されています (重大度の低いものから高い順)。

* Trace = 0

  通常はデバッグでのみ役立つ情報の場合。 これらのメッセージには機密性の高いアプリケーション データが含まれる可能性があるため、運用環境は有効にしないことをお勧めします。 *既定で無効です。*

* Debug = 1

  開発とデバッグで役立つ可能性がある情報の場合。 例:`Entering method Configure with flag set to true.` `Debug` レベルのログは、ログのサイズが大きくなるため、トラブルシューティングの場合を除き運用環境では有効にしません。

* Information = 2

  アプリの一般的なフローを追跡する場合。 通常、これらのログには、長期的な値があります。 例 : `Request received for path /api/todo`

* Warning = 3

  アプリのフローで異常なイベントや予期しないイベントが発生した場合。 アプリの停止の原因にはならないが、調査する必要があるエラーやその他の状態がここに含まれる場合があります。 `Warning` ログ レベルが使用される一般的な場所として、例外の処理があります。 例 : `FileNotFoundException for file quotes.txt.`

* Error = 4

  処理できないエラーと例外の場合。 これらのメッセージは、アプリ全体のエラーではなく、現在のアクティビティまたは操作 (現在の HTTP 要求など) におけるエラーを示します。 ログ メッセージの例: `Cannot insert record due to duplicate key violation.`

* Critical = 5

  即時の注意が必要なエラーの場合。 例: データ損失のシナリオ、ディスク領域不足。

ログ レベルを使用して、特定のストレージ メディアまたは表示ウィンドウに書き込むログの出力量を制御します。 例:

* 運用環境では、`Information` レベルを使用してボリューム データ ストアに `Trace` を送信します。 `Critical` を使用して値のデータ ストアに `Warning` を送信します。
* 開発中は `Critical` を使用してコンソールに `Warning` を送信し、トラブルシューティングの際は `Information` を使用して `Trace` を追加します。

この記事で後述する「[ログのフィルター処理](#log-filtering)」セクションでは、プロバイダーで処理するログ レベルの制御方法について説明します。

ASP.NET Core では、フレームワーク イベントのログが書き込まれます。 この記事の前のログの例では、`Information` レベル以下のログを除外したため、`Debug` または `Trace` レベルのログは作成されませんでした。 `Debug` ログを示すために構成したサンプル アプリを実行することで生成されるコンソールのログの例を、次に示します。

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a>ログ イベント ID

各ログで "*イベント ID*" を指定できます。 サンプル アプリでは、この処理にローカルで定義された `LoggingEvents` クラスを使用します。

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/1.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

イベント ID によって一連のイベントが関連付けられます。 たとえば、ページ上に項目の一覧を表示する機能に関連するすべてのログを 1001 に設定します。

ログ プロバイダーでは、ID フィールドやログ メッセージにイベント ID が格納されたり、またはまったく格納されなかったりする場合があります。 Debug プロバイダーでイベント ID が表示されることはありません。 Console プロバイダーでは、カテゴリの後のブラケット内にイベント ID が表示されます。

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>ログ メッセージ テンプレート

各ログでメッセージ テンプレートが指定されます。 メッセージ テンプレートには、指定される引数のためのプレースホルダーを含めることができます。 プレースホルダーには、数値ではなく名前を使用します。

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

プレースホルダーの名前ではなく、プレースホルダーの順序によって、値の指定に使用されるパラメーターが決まります。 次のコードでは、パラメーター名がメッセージ テンプレート内のシーケンスの外にあることに注意してください。

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

このコードでは、シーケンス内のパラメーター値を含むログ メッセージが作成されます。

```
Parameter values: parm1, parm2
```

ログ プロバイダーが[セマンティック ログ記録 (または構造化ログ記録)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)を実装できるようにするために、ログ フレームワークはこのように動作します。 書式設定されたメッセージ テンプレートだけでなく、引数自体がログ システムに渡されます。 この情報により、ログ プロバイダーはフィールドとしてパラメーター値を格納することができます。 たとえば、つぎのようなロガー メソッドの呼び出しがあるとします。

```csharp
_logger.LogInformation("Getting item {ID} at {RequestTime}", id, DateTime.Now);
```

Azure Table Storage にログを送信する場合、各 Azure Table エンティティに `ID` および `RequestTime` プロパティを指定し、ログ データのクエリを簡略化することができます。 クエリによって指定した `RequestTime` の範囲内のすべてのログを検索できます。テキスト メッセージから時間を解析する必要はありません。

## <a name="logging-exceptions"></a>ログ記録の例外

ロガー メソッドには、次の例のように、例外で渡すことができるオーバーロードがあります。

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

プロバイダーごとに、例外情報の処理方法は異なります。 前述のコードの Debug プロバイダーの出力の例を次に示します。

```
TodoApi.Controllers.TodoController:Warning: GetById(036dd898-fb01-47e8-9a65-f92eb73cf924) NOT FOUND

System.Exception: Item not found exception.
 at TodoApi.Controllers.TodoController.GetById(String id) in C:\logging\sample\src\TodoApi\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>ログのフィルター処理

::: moniker range=">= aspnetcore-2.0"

特定のプロバイダーとカテゴリ、またはすべてのプロバイダーまたはすべてのカテゴリに最小ログ レベルを指定できます。 最小レベルを下回るログは、そのプロバイダーに渡されないので、表示または保存されません。

すべてのログを抑制するには、最小ログ レベルに `LogLevel.None` を指定します。 `LogLevel.None` の整数値は 6 であり、`LogLevel.Critical` (5) を超えます。

### <a name="create-filter-rules-in-configuration"></a>構成にフィルター規則を作成する

プロジェクト テンプレートのコードでは `CreateDefaultBuilder` が呼び出され、Console プロバイダーと Debug プロバイダーのログ記録が設定されます。 `CreateDefaultBuilder` メソッドは、次のようなコードを使用して、`Logging` セクションの構成を検索するログ記録も設定します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=16)]

次の例のように、構成データでは、プロバイダーとカテゴリごとに最小ログ レベルを指定します。

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

この JSON では、6 個のフィルター規則を作成します。1 つは Debug プロバイダー用、4 つは Console プロバイダー用、1 つはすべてのプロバイダー用です。 `ILogger` オブジェクトが作成されると、各プロバイダーに対して 1 つの規則が選択されます。

### <a name="filter-rules-in-code"></a>コードのフィルター規則

コードにフィルター規則を登録する方法を次の例に示します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

2 つ目の `AddFilter` では、プロバイダーの種類名を使用して Debug プロバイダーを指定します。 1 つ目の `AddFilter` は、プロバイダーの種類を指定していないため、すべてのプロバイダーに適用されます。

### <a name="how-filtering-rules-are-applied"></a>フィルター規則を適用する方法

前の例の構成データと `AddFilter` コードでは、次の表に示す規則を作成します。 最初の 6 つは構成例、最後の 2 つはコード例のものです。

| 数値 | プロバイダー      | 以下から始まるカテゴリ          | 最小ログ レベル |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | デバッグ         | すべてのカテゴリ                          | 情報       |
| 2      | コンソール       | Microsoft.AspNetCore.Mvc.Razor.Internal | 警告           |
| 3      | コンソール       | Microsoft.AspNetCore.Mvc.Razor.Razor    | デバッグ             |
| 4      | コンソール       | Microsoft.AspNetCore.Mvc.Razor          | Error             |
| 5      | コンソール       | すべてのカテゴリ                          | 情報       |
| 6      | すべてのプロバイダー | すべてのカテゴリ                          | デバッグ             |
| 7      | すべてのプロバイダー | システム                                  | デバッグ             |
| 8      | デバッグ         | Microsoft                               | トレース             |

`ILogger` オブジェクトを作成すると、`ILoggerFactory` オブジェクトによって、そのロガーに適用するプロバイダーごとに 1 つの規則が選択されます。 `ILogger` インスタンスによって書き込まれるすべてのメッセージは、選択した規則に基づいてフィルター処理されます。 使用できる規則から、各プロバイダーとカテゴリのペアごとに該当する最も限定的な規則が選択されます。

特定のカテゴリに `ILogger` が作成されるときに、各プロバイダーに次のアルゴリズムが使用されます。

* プロバイダーとそのエイリアスと一致するすべての規則が選択されます。 一致が見つからない場合は、空のプロバイダーですべての規則が選択されます。
* 前の手順の結果、最も長いカテゴリのプレフィックスが一致する規則が選択されます。 一致が見つからない場合は、カテゴリを指定しないすべての規則が選択されます。
* 複数の規則が選択されている場合は、**最後**の 1 つが使用されます。
* 規則が選択されていない場合は、`MinimumLevel` が使用されます。

前の規則一覧を使用して、カテゴリ "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine" に `ILogger` オブジェクトを作成するとします。

* Debug プロバイダーの場合、規則 1、6、8 が適用されます。 規則 8 が最も限定的なので、規則 8 が選択されます。
* Console プロバイダーの場合、規則 3、4、5、6 が適用されます。 規則 3 が最も限定的です。

作成される `ILogger` インスタンスでは、Debug プロバイダーに `Trace` レベル以上のログが送信されます。 `Debug` レベル以上のログが Console プロバイダーに送信されます。

### <a name="provider-aliases"></a>プロバイダーのエイリアス

各プロバイダーでは "*エイリアス*" が定義されます。これは構成で完全修飾型名の代わりに使用できます。  組み込みのプロバイダーの場合は、次のエイリアスを使用してください。

* コンソール
* デバッグ
* EventLog
* AzureAppServices
* TraceSource
* EventSource

### <a name="default-minimum-level"></a>既定の最小レベル

指定したプロバイダーとカテゴリに適用される構成またはコードの規則がない場合にのみ反映される最小レベルの設定があります。 最小レベルを設定する方法を次の例に示します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

最小レベルを明示的に設定しない場合、既定値は `Information` です。これは、`Trace` および `Debug` ログが無視されることを意味します。

### <a name="filter-functions"></a>フィルター関数

フィルター関数は、構成またはコードによって規則が割り当てられていないすべてのプロバイダーとカテゴリについて呼び出されます。 関数内のコードから、プロバイダーの種類、カテゴリ、ログ レベルにアクセスすることができます。 例:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

一部のログ プロバイダーでは、ログのレベルとカテゴリに基づいてログをストレージ メディアに書き込む場合や無視する場合を指定できます。

`AddConsole` および `AddDebug` 拡張メソッドには、フィルター条件を指定できるオーバーロードが用意されています。 次のサンプル コードを実行すると、Console プロバイダーは `Warning` レベル未満のログを無視し、Debug プロバイダーはフレームワークで作成されたログを無視します。

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_AddConsoleAndDebugWithFilter&highlight=6-7)]

`AddEventLog` メソッドには、`EventLogSettings` インスタンスを受け取るオーバーロードがあります。このインスタンスの `Filter` プロパティにはフィルター関数を含めることができます。 TraceSource プロバイダーには、これらのオーバーロードのいずれも用意されていません。これは、ログ レベルと他のパラメーターが、使用される `SourceSwitch` と `TraceListener` に基づいているためです。

`ILoggerFactory` インスタンスに登録されているすべてのプロバイダーにフィルター規則を設定するには、`WithFilter` 拡張メソッドを使用します。 以下の例では、フレームワーク ログ ("Microsoft" または "System" で始まるカテゴリ) が警告に制限されますが、アプリケーション コードによって作成されたログはデバッグ レベルで記録されます。

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_FactoryFilter&highlight=6-11)]

あらゆるログの書き込みを防ぐには、最小のログ レベルとして `LogLevel.None` を指定します。 `LogLevel.None` の整数値は 6 であり、`LogLevel.Critical` (5) を超えます。

`WithFilter` 拡張メソッドは [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) NuGet パッケージで提供されます。 このメソッドは、登録されているすべてのログ プロバイダーに渡されたログ メッセージをフィルター処理する新しい `ILoggerFactory` インスタンスを返します。 これは、元の `ILoggerFactory` インスタンスを含め、他の `ILoggerFactory` インスタンスには影響がありません。

::: moniker-end

## <a name="system-categories-and-levels"></a>システムのカテゴリとレベル

ASP.NET Core と Entity Framework Core によって使用されるいくつかのカテゴリと、それらから想定されるログの種類に関するメモを、次に示します。

| Category                            | メモ |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | ASP.NET Core の一般的な診断。 |
| Microsoft.AspNetCore.DataProtection | どのキーが検討、検索、および使用されたか。 |
| Microsoft.AspNetCore.HostFiltering  | 許可されるホスト。 |
| Microsoft.AspNetCore.Hosting        | HTTP 要求が完了するまでにかかった時間と、それらの開始時刻。 どのホスティング スタートアップ アセンブリが読み込まれたか。 |
| Microsoft.AspNetCore.Mvc            | MVC と Razor の診断。 モデルの構築、フィルター処理の実行、ビューのコンパイル、アクションの選択。 |
| Microsoft.AspNetCore.Routing        | 一致する情報をルーティングします。 |
| Microsoft.AspNetCore.Server         | 接続の開始、停止、キープ アライブ応答。 HTTPS 証明書情報。 |
| Microsoft.AspNetCore.StaticFiles    | 提供されるファイル。 |
| Microsoft.EntityFrameworkCore       | Entity Framework Core の一般的な診断。 データベースのアクティビティと構成、変更の検出、移行。 |

## <a name="log-scopes"></a>ログのスコープ

 "*スコープ*" では、論理操作のセットをグループ化できます。 このグループ化を使用して、セットの一部として作成される各ログに同じデータをアタッチすることができます。 たとえば、トランザクション処理の一部として作成されるすべてのログに、トランザクション ID を含めることができます。

スコープは <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> メソッドから返される `IDisposable` の種類であり、破棄されるまで継続します。 ロガーの呼び出しを `using` ブロックでラップすることによって、スコープを使用します。

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

次のコードでは、Console プロバイダーのスコープを有効にしています。

::: moniker range="> aspnetcore-2.0"

*Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> スコープベースのログ記録を有効にするには、`IncludeScopes` コンソールのロガー オプションを構成する必要があります。
>
> 構成について詳しくは、「[構成](#configuration)」セクションをご覧ください。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

*Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> スコープベースのログ記録を有効にするには、`IncludeScopes` コンソールのロガー オプションを構成する必要があります。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

*Startup.cs*:

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

各ログ メッセージには、スコープ内の情報が含まれます。

```
info: TodoApi.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>組み込みのログ プロバイダー

ASP.NET Core には次のプロバイダーが付属しています。

* [コンソール](#console-provider)
* [デバッグ](#debug-provider)
* [EventSource](#eventsource-provider)
* [EventLog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)

[Azure でのログ記録](#logging-in-azure)用のオプションについては、この記事で後ほど説明します。

stdout ログについては、<xref:host-and-deploy/iis/troubleshoot#aspnet-core-module-stdout-log> と <xref:host-and-deploy/azure-apps/troubleshoot#aspnet-core-module-stdout-log> をご覧ください。

### <a name="console-provider"></a>Console プロバイダー

[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) プロバイダー パッケージは、ログ出力をコンソールに送信します。 

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddConsole();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddConsole();
```

[AddConsole オーバーロード](xref:Microsoft.Extensions.Logging.ConsoleLoggerExtensions)を使用すると、最小ログ レベル、フィルター関数、スコープがサポートされているかどうかを示すブール値を渡すことができます。 もう 1 つの選択肢として、`IConfiguration` オブジェクトを渡す方法があります。この場合、スコープのサポートとログ レベルを指定できます。

Console プロバイダーはパフォーマンスに大きな影響を及ぼすため、一般的に運用環境での使用には適していません。

Visual Studio で新しいプロジェクトを作成する場合、`AddConsole` メソッドは次のようになります。

```csharp
loggerFactory.AddConsole(Configuration.GetSection("Logging"));
```

このコードは、*appSettings.json* ファイルの `Logging` セクションを参照しています。

[!code-json[](index/samples/1.x/TodoApiSample//appsettings.json)]

この設定ではフレームワークのログを警告に制限していますが、「[ログのフィルター処理](#log-filtering)」セクションで説明したように、アプリではデバッグ レベルでログに記録することができます。 詳細については、[構成](xref:fundamentals/configuration/index)に関するページを参照してください。

::: moniker-end

コンソールのログ出力を確認するには、プロジェクト フォルダーでコマンド プロンプトを開き、次のコマンドを実行します。

```console
dotnet run
```

### <a name="debug-provider"></a>Debug プロバイダー

[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) プロバイダー パッケージは、[System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) クラス (`Debug.WriteLine` メソッドの呼び出し) を使用してログの出力を書き込みます。

Linux では、このプロバイダーから */var/log/message* にログが書き込まれます。

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddDebug();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddDebug();
```

[AddDebug オーバーロード](xref:Microsoft.Extensions.Logging.DebugLoggerFactoryExtensions)を使用すると、最小ログ レベルまたはフィルター関数を渡すことができます。

::: moniker-end

### <a name="eventsource-provider"></a>EventSource プロバイダー

ASP.NET Core 1.1.0 以降をターゲットとするアプリの場合、[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) プロバイダー パッケージは、イベントのトレースを実装できます。 Windows では、[ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803) を使用します。 プロバイダーはクロスプラットフォームですが、Linux または macOS 用のイベント コレクションはまだ存在せず、ツールは表示されません。

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddEventSourceLogger();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddEventSourceLogger();
```

::: moniker-end

ログの収集と表示には、[PerfView utility](https://github.com/Microsoft/perfview) を使用することをお勧めします。 ETW ログを表示できる他のツールはありますが、ASP.NET から出力される ETW イベントを操作する場合、PerfView は最適なエクスペリエンスを提供します。

このプロバイダーでログに記録されるイベントを収集するように PerfView を構成するには、**[追加プロバイダー]** の一覧に文字列 `*Microsoft-Extensions-Logging` を追加します  (文字列の先頭に忘れずにアスタリスクを付けてください)。

![Perfview の追加プロバイダー](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Windows EventLog プロバイダー

[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) プロバイダー パッケージは、ログ出力を Windows イベント ログに送信します。

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddEventLog();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddEventLog();
```

[AddEventLog オーバーロード](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)を使用すると、`EventLogSettings` または最小ログ レベルを渡すことができます。

::: moniker-end

### <a name="tracesource-provider"></a>TraceSource プロバイダー

[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) プロバイダー パッケージでは、<xref:System.Diagnostics.TraceSource> ライブラリとプロバイダーが使用されます。

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddTraceSource(sourceSwitchName);
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddTraceSource(sourceSwitchName);
```

::: moniker-end

[AddTraceSource オーバーロード](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions)を使用すると、ソース スイッチとトレース リスナーを渡すことができます。

このプロバイダーを使用するには、アプリを (.NET Core ではなく) .NET Framework 上で実行する必要があります。 このプロバイダーでは、サンプル アプリで使用されている <xref:System.Diagnostics.TextWriterTraceListener> など、さまざまな[リスナー](/dotnet/framework/debug-trace-profile/trace-listeners)にメッセージをルーティングさせることができます。

::: moniker range="< aspnetcore-2.0"

次の例では、`Warning` 以上のメッセージのログをコンソール ウィンドウに出力する `TraceSource` プロバイダーを構成します。

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_TraceSource&highlight=9-12)]

::: moniker-end

## <a name="logging-in-azure"></a>Azure でのログ記録

Azure でのログ記録については、次のセクションを参照してください。

* [Azure App Service プロバイダー](#azure-app-service-provider)
* [Azure ログのストリーミング](#azure-log-streaming)

::: moniker range=">= aspnetcore-1.1"

* [Azure Application Insights のトレース ログ](#azure-application-insights-trace-logging)

::: moniker-end

### <a name="azure-app-service-provider"></a>Azure App Service プロバイダー

[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) プロバイダー パッケージは、Azure App Service アプリのファイル システムのテキスト ファイルと、Azure Storage アカウントの [BLOB ストレージ](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)にログを書き込みます。 プロバイダー パッケージは、.NET Core 1.1 以降を対象とするアプリで使用可能です。

::: moniker range=">= aspnetcore-2.0"

.NET Core を対象とする場合は、次の点に注意してください。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* プロバイダー パッケージは、ASP.NET Core の [Microsoft.AspNetCore.All メタパッケージ](xref:fundamentals/metapackage)に含まれます。

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* プロバイダー パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)には含まれません。 プロバイダーを使用するには、パッケージをインストールします。

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

* <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> を明示的に呼び出さないでください。 アプリを Azure App Service にデプロイすると、プロバイダーはアプリで自動的に使用できるようになります。

.NET Framework をターゲットとする場合、または `Microsoft.AspNetCore.App` を参照している場合は、プロバイダー パッケージをプロジェクトに追加します。 <xref:Microsoft.Extensions.Logging.ILoggerFactory> インスタンス上で `AddAzureWebAppDiagnostics` を呼び出します。

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker-end

::: moniker range="= aspnetcore-1.1"

```csharp
loggerFactory.AddAzureWebAppDiagnostics();
```

::: moniker-end

::: moniker range=">= aspnetcore-1.1"

<xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> のオーバーロードによって <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings> を渡すことができます。 設定オブジェクトで既定の設定をオーバーライドできます。たとえば、ログの出力テンプレート、BLOB 名、ファイル サイズの制限などです。 ("*出力テンプレート*" は、`ILogger` メソッドの呼び出しと共に指定されるものに加えて、すべてのログに適用されるメッセージ テンプレートです。)

アプリケーションを App Service アプリにデプロイすると、Azure portal の **[App Service]** ページの [[診断ログ]](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) セクションで指定された設定が適用されます。 これらの設定が更新されると、アプリの再起動や再デプロイを必要とせずに、変更がすぐに有効になります。

![Azure のログ設定](index/_static/azure-logging-settings.png)

ログ ファイルの既定の場所は、*D:\\home\\LogFiles\\Application* です。既定のファイル名は *diagnostics-yyyymmdd.txt* です。 既定のファイル サイズ制限は 10 MB です。保持されるファイルの既定の最大数は 2 です。 既定の BLOB 名は *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt* です。 既定の動作について詳しくは、<xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings> をご覧ください。

このプロバイダーは、プロジェクトが Azure 環境で実行される場合にのみ機能します。 プロジェクトをローカルで実行しても、効果はありません&mdash;BLOB のローカル ファイルまたはローカル開発ストレージへの書き込みは行われません。

::: moniker-end

### <a name="azure-log-streaming"></a>Azure ログのストリーミング

Azure ログのストリーミングを使用すると、以下からリアル タイムでログ アクティビティを確認できます。

* アプリ サーバー
* Web サーバー
* 失敗した要求のトレース

Azure ログのストリーミングを構成するには

* アプリのポータル ページから **[診断ログ]** ページに移動します。
* **[アプリケーション ログ (ファイル システム)]** を **[オン]** に設定します。

![Azure Portal の [診断ログ] ページ](index/_static/azure-diagnostic-logs.png)

**[ログ ストリーミング]** ページに移動して、アプリのメッセージを確認します。 これらはアプリによって、`ILogger` インターフェイスを介してログに記録されます。

![Azure Portal のアプリケーション ログのストリーミング](index/_static/azure-log-streaming.png)

::: moniker range=">= aspnetcore-1.1"

### <a name="azure-application-insights-trace-logging"></a>Azure Application Insights のトレース ログ

Application Insights SDK では、ASP.NET Core のログ インフラストラクチャによって生成されるログを収集およびレポートすることができます。 詳細については、次のリソースを参照してください。

* [Application Insights の概要](/azure/application-insights/app-insights-overview)
* [ASP.NET Core 用 Application Insights](/azure/application-insights/app-insights-asp-net-core)
* [Application Insights のログ アダプター](https://github.com/Microsoft/ApplicationInsights-dotnet-logging/blob/develop/README.md)。

::: moniker-end

## <a name="third-party-logging-providers"></a>サードパーティ製のログ プロバイダー

ASP.NET Core で使用できるサードパーティ製のログ記録フレームワークをいくつか紹介します。

* [elmah.io](https://elmah.io/) ([GitHub リポジトリ](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](http://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub リポジトリ](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](http://jsnlog.com/) ([GitHub リポジトリ](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/) ([GitHub リポジトリ](https://github.com/catalingavan/KissLog-net))
* [Loggr](http://loggr.net/) ([GitHub リポジトリ](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](http://nlog-project.org/) ([GitHub リポジトリ](https://github.com/NLog/NLog.Extensions.Logging))
* [Sentry](https://sentry.io/welcome/) ([GitHub リポジトリ](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([GitHub リポジトリ](https://github.com/serilog/serilog-extensions-logging))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github リポジトリ](https://github.com/googleapis/google-cloud-dotnet))

一部のサードパーティ製フレームワークは、[セマンティック ログ記録 (構造化ログ記録とも呼ばれます)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging) を実行できます。

サード パーティ製フレームワークを使用することは、組み込みのプロバイダーのいずれかを使用することと似ています。

1. プロジェクトに NuGet パッケージを追加します。
1. `ILoggerFactory` を呼び出します。

詳細については、各プロバイダーのドキュメントをご覧ください。 サード パーティ製のログ プロバイダーは、Microsoft ではサポートされていません。

## <a name="additional-resources"></a>その他の技術情報

* <xref:fundamentals/logging/loggermessage>
