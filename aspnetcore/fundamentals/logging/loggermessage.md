---
title: ASP.NET Core での LoggerMessage による高パフォーマンスのログ記録
author: guardrex
description: 高パフォーマンスのログ記録シナリオにおいて、必要とするオブジェクト割り当ての数が少ない、キャッシュ可能なデリゲートを LoggerMessage を使用して作成する方法について説明します。
ms.author: riande
ms.date: 11/03/2017
uid: fundamentals/logging/loggermessage
ms.openlocfilehash: a0080a20fed2d8fc295e55822c11d5731c6910ca
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57048619"
---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a><span data-ttu-id="b4138-103">ASP.NET Core での LoggerMessage による高パフォーマンスのログ記録</span><span class="sxs-lookup"><span data-stu-id="b4138-103">High-performance logging with LoggerMessage in ASP.NET Core</span></span>

<span data-ttu-id="b4138-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b4138-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b4138-105">[LoggerMessage](/dotnet/api/microsoft.extensions.logging.loggermessage) 機能では、`LogInformation`、`LogDebug`、`LogError` のような[ロガー拡張メソッド](/dotnet/api/Microsoft.Extensions.Logging.LoggerExtensions)と比較して、必要なオブジェクト割り当ての数が少なくコンピューティング オーバーヘッドが小さいキャッシュ可能なデリゲートを作成します。</span><span class="sxs-lookup"><span data-stu-id="b4138-105">[LoggerMessage](/dotnet/api/microsoft.extensions.logging.loggermessage) features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](/dotnet/api/Microsoft.Extensions.Logging.LoggerExtensions), such as `LogInformation`, `LogDebug`, and `LogError`.</span></span> <span data-ttu-id="b4138-106">高パフォーマンスのログ記録シナリオの場合は、`LoggerMessage` パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="b4138-106">For high-performance logging scenarios, use the `LoggerMessage` pattern.</span></span>

<span data-ttu-id="b4138-107">`LoggerMessage` には、ロガー拡張メソッドに比べて次のようなパフォーマンス上の利点があります。</span><span class="sxs-lookup"><span data-stu-id="b4138-107">`LoggerMessage` provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="b4138-108">ロガー拡張メソッドでは、`int` などの値の型を `object` に "ボックス化" (変換) する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b4138-108">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="b4138-109">`LoggerMessage` パターンでは、静的な `Action` フィールドと、厳密に型指定されたパラメーターを持つ拡張メソッドを使用してボックス化を回避します。</span><span class="sxs-lookup"><span data-stu-id="b4138-109">The `LoggerMessage` pattern avoids boxing by using static `Action` fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="b4138-110">ロガー拡張メソッドでは、ログ メッセージが書き込まれるたびにメッセージ テンプレート (名前付きの書式文字列) を解析する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b4138-110">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="b4138-111">`LoggerMessage` では、メッセージを定義するときに、一度テンプレートを解析する必要があるだけです。</span><span class="sxs-lookup"><span data-stu-id="b4138-111">`LoggerMessage` only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="b4138-112">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/sample/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="b4138-112">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/sample/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b4138-113">サンプル アプリでは、基本的な見積もり追跡システムで `LoggerMessage` 機能を実演します。</span><span class="sxs-lookup"><span data-stu-id="b4138-113">The sample app demonstrates `LoggerMessage` features with a basic quote tracking system.</span></span> <span data-ttu-id="b4138-114">アプリでは、メモリ内のデータベースを使用して見積もりの追加および削除を行います。</span><span class="sxs-lookup"><span data-stu-id="b4138-114">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="b4138-115">これらの操作が実行されると、`LoggerMessage` パターンによってログ メッセージが生成されます。</span><span class="sxs-lookup"><span data-stu-id="b4138-115">As these operations occur, log messages are generated using the `LoggerMessage` pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="b4138-116">LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="b4138-116">LoggerMessage.Define</span></span>

<span data-ttu-id="b4138-117">[Define(LogLevel, EventId, String)](/dotnet/api/microsoft.extensions.logging.loggermessage.define) では、メッセージをログ記録するための `Action` デリゲートが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b4138-117">[Define(LogLevel, EventId, String)](/dotnet/api/microsoft.extensions.logging.loggermessage.define) creates an `Action` delegate for logging a message.</span></span> <span data-ttu-id="b4138-118">`Define` オーバー ロードでは、名前付きの書式文字列 (テンプレート) に対して最大 6 個の型パラメーターを渡すことを許可します。</span><span class="sxs-lookup"><span data-stu-id="b4138-118">`Define` overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="b4138-119">`Define` メソッドに指定された文字列はテンプレートであり、補間文字列ではありません。</span><span class="sxs-lookup"><span data-stu-id="b4138-119">The string provided to the `Define` method is a template and not an interpolated string.</span></span> <span data-ttu-id="b4138-120">プレースホルダーは、型が指定された順に入力されます。</span><span class="sxs-lookup"><span data-stu-id="b4138-120">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="b4138-121">テンプレート内のプレースホルダー名はわかりやすく、テンプレート間で一貫している必要があります。</span><span class="sxs-lookup"><span data-stu-id="b4138-121">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="b4138-122">それらは構造化されたログ データ内でプロパティ名としての役割を果たします。</span><span class="sxs-lookup"><span data-stu-id="b4138-122">They serve as property names within structured log data.</span></span> <span data-ttu-id="b4138-123">プレースホルダー名には [Pascal 形式](/dotnet/standard/design-guidelines/capitalization-conventions)を推奨します。</span><span class="sxs-lookup"><span data-stu-id="b4138-123">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="b4138-124">たとえば、`{Count}`、`{FirstName}` のようになります。</span><span class="sxs-lookup"><span data-stu-id="b4138-124">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="b4138-125">各ログ メッセージは、`LoggerMessage.Define` によって作成された静的フィールドに保持される `Action` です。</span><span class="sxs-lookup"><span data-stu-id="b4138-125">Each log message is an `Action` held in a static field created by `LoggerMessage.Define`.</span></span> <span data-ttu-id="b4138-126">たとえば、サンプル アプリでは、インデックス ページに対する GET 要求に関するログ メッセージを記述するフィールドを作成します (*Internal/LoggerExtensions.cs*)。</span><span class="sxs-lookup"><span data-stu-id="b4138-126">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="b4138-127">`Action` の場合は、次を指定します。</span><span class="sxs-lookup"><span data-stu-id="b4138-127">For the `Action`, specify:</span></span>

* <span data-ttu-id="b4138-128">ログ レベル。</span><span class="sxs-lookup"><span data-stu-id="b4138-128">The log level.</span></span>
* <span data-ttu-id="b4138-129">静的な拡張メソッドの名前を持つ一意のイベント識別子です ([EventId](/dotnet/api/microsoft.extensions.logging.eventid))。</span><span class="sxs-lookup"><span data-stu-id="b4138-129">A unique event identifier ([EventId](/dotnet/api/microsoft.extensions.logging.eventid)) with the name of the static extension method.</span></span>
* <span data-ttu-id="b4138-130">メッセージ テンプレート (名前付き書式文字列)。</span><span class="sxs-lookup"><span data-stu-id="b4138-130">The message template (named format string).</span></span> 

<span data-ttu-id="b4138-131">サンプル アプリのインデックス ページに対する要求では、次の設定を行います。</span><span class="sxs-lookup"><span data-stu-id="b4138-131">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="b4138-132">ログ レベルを `Information` に設定します。</span><span class="sxs-lookup"><span data-stu-id="b4138-132">Log level to `Information`.</span></span>
* <span data-ttu-id="b4138-133">イベント ID を `IndexPageRequested` メソッドの名前を含む `1` に設定します。</span><span class="sxs-lookup"><span data-stu-id="b4138-133">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="b4138-134">メッセージ テンプレート (名前付き書式文字列) を文字列に設定します。</span><span class="sxs-lookup"><span data-stu-id="b4138-134">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="b4138-135">構造化されたログ ストアは、イベント ID によって指定されているとき、ログ記録を強化するために、イベント名を使用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="b4138-135">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="b4138-136">たとえば、[Serilog](https://github.com/serilog/serilog-extensions-logging) はイベント名を使用します。</span><span class="sxs-lookup"><span data-stu-id="b4138-136">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="b4138-137">`Action` は厳密に型指定された拡張メソッドによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b4138-137">The `Action` is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="b4138-138">`IndexPageRequested` メソッドは、サンプル アプリにおけるインデックス ページの GET 要求に関するメッセージを記録します。</span><span class="sxs-lookup"><span data-stu-id="b4138-138">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="b4138-139">`IndexPageRequested` は、*Pages/Index.cshtml.cs* に含まれる `OnGetAsync` メソッドのロガー上で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b4138-139">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="b4138-140">アプリのコンソール出力を検査する:</span><span class="sxs-lookup"><span data-stu-id="b4138-140">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="b4138-141">ログ メッセージにパラメーターを渡すには、静的フィールドを作成するときに最大 6 個の型を定義します。</span><span class="sxs-lookup"><span data-stu-id="b4138-141">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="b4138-142">サンプル アプリでは、`Action` フィールドに対して `string` 型を定義して見積もりを追加すると、文字列が記録されます。</span><span class="sxs-lookup"><span data-stu-id="b4138-142">The sample app logs a string when adding a quote by defining a `string` type for the `Action` field:</span></span>

[!code-csharp[](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="b4138-143">デリゲートのログ メッセージ テンプレートは、そのプレースホルダー値を指定された型で受け取ります。</span><span class="sxs-lookup"><span data-stu-id="b4138-143">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="b4138-144">サンプル アプリでは、見積もりを追加するためのデリゲートを定義します。ここで見積もりパラメーターは `string` となります。</span><span class="sxs-lookup"><span data-stu-id="b4138-144">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="b4138-145">見積もりを追加するための静的な拡張メソッドである `QuoteAdded` は、見積もり引数の値を受け取って、`Action` デリゲートに渡します。</span><span class="sxs-lookup"><span data-stu-id="b4138-145">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the `Action` delegate:</span></span>

[!code-csharp[](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="b4138-146">インデックス ページのページ モデル (*Pages/Index.cshtml.cs*) では、メッセージを記録するために `QuoteAdded` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b4138-146">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/sample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="b4138-147">アプリのコンソール出力を検査する:</span><span class="sxs-lookup"><span data-stu-id="b4138-147">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="b4138-148">サンプル アプリでは、見積もり削除用の `try`&ndash;`catch` パターンを実装します。</span><span class="sxs-lookup"><span data-stu-id="b4138-148">The sample app implements a `try`&ndash;`catch` pattern for quote deletion.</span></span> <span data-ttu-id="b4138-149">削除操作に成功した場合、情報メッセージが記録されます。</span><span class="sxs-lookup"><span data-stu-id="b4138-149">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="b4138-150">例外がスローされた場合は、削除操作に対してエラー メッセージが記録されます。</span><span class="sxs-lookup"><span data-stu-id="b4138-150">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="b4138-151">削除操作が失敗した場合のログ メッセージには、例外スタック トレースが含まれます (*Internal/LoggerExtensions.cs*)。</span><span class="sxs-lookup"><span data-stu-id="b4138-151">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="b4138-152">`QuoteDeleteFailed` 内のデリゲートに例外が渡される方法に注目してください。</span><span class="sxs-lookup"><span data-stu-id="b4138-152">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="b4138-153">インデックス ページのページ モデルでは、見積もりの削除に成功すると、ロガー上で `QuoteDeleted` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b4138-153">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="b4138-154">削除対象の見積もりが見つからない場合は、`ArgumentNullException` がスローされます。</span><span class="sxs-lookup"><span data-stu-id="b4138-154">When a quote isn't found for deletion, an `ArgumentNullException` is thrown.</span></span> <span data-ttu-id="b4138-155">例外は `try`&ndash;`catch` ステートメントによってトラップされ、`catch` ブロック (*Pages/Index.cshtml.cs*) 内のロガーで `QuoteDeleteFailed` を呼び出すことにより記録されます。</span><span class="sxs-lookup"><span data-stu-id="b4138-155">The exception is trapped by the `try`&ndash;`catch` statement and logged by calling the `QuoteDeleteFailed` method on the logger in the `catch` block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/sample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

<span data-ttu-id="b4138-156">見積もりが正常に削除されたら、アプリのコンソール出力を検査します。</span><span class="sxs-lookup"><span data-stu-id="b4138-156">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="b4138-157">見積もりの削除が失敗したら、アプリのコンソール出力を検査します。</span><span class="sxs-lookup"><span data-stu-id="b4138-157">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="b4138-158">例外はログ メッセージに含められます。</span><span class="sxs-lookup"><span data-stu-id="b4138-158">Note that the exception is included in the log message:</span></span>

```console
fail: LoggerMessageSample.Pages.IndexModel[5]
      => RequestId:0HL90M6E7PHK5:00000010 RequestPath:/ => /Index
      Quote delete failed (Id = 999)
System.ArgumentNullException: Value cannot be null.
Parameter name: entity
   at Microsoft.EntityFrameworkCore.Utilities.Check.NotNull[T](T value, String parameterName)
   at Microsoft.EntityFrameworkCore.DbContext.Remove[TEntity](TEntity entity)
   at Microsoft.EntityFrameworkCore.Internal.InternalDbSet`1.Remove(TEntity entity)
   at LoggerMessageSample.Pages.IndexModel.<OnPostDeleteQuoteAsync>d__14.MoveNext() in 
      <PATH>\sample\Pages\Index.cshtml.cs:line 87
```

## <a name="loggermessagedefinescope"></a><span data-ttu-id="b4138-159">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="b4138-159">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="b4138-160">[DefineScope(String)](/dotnet/api/microsoft.extensions.logging.loggermessage.definescope) は、[ログ スコープ](xref:fundamentals/logging/index#log-scopes)を定義するための `Func` デリゲートを作成します。</span><span class="sxs-lookup"><span data-stu-id="b4138-160">[DefineScope(String)](/dotnet/api/microsoft.extensions.logging.loggermessage.definescope) creates a `Func` delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="b4138-161">`DefineScope` オーバー ロードでは、名前付きの書式文字列 (テンプレート) に対して最大 3 個の型パラメーターを渡すことを許可します。</span><span class="sxs-lookup"><span data-stu-id="b4138-161">`DefineScope` overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="b4138-162">`Define` メソッドの場合と同様に、`DefineScope` メソッドに指定された文字列はテンプレートであり、補間文字列ではありません。</span><span class="sxs-lookup"><span data-stu-id="b4138-162">As is the case with the `Define` method, the string provided to the `DefineScope` method is a template and not an interpolated string.</span></span> <span data-ttu-id="b4138-163">プレースホルダーは、型が指定された順に入力されます。</span><span class="sxs-lookup"><span data-stu-id="b4138-163">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="b4138-164">テンプレート内のプレースホルダー名はわかりやすく、テンプレート間で一貫している必要があります。</span><span class="sxs-lookup"><span data-stu-id="b4138-164">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="b4138-165">それらは構造化されたログ データ内でプロパティ名としての役割を果たします。</span><span class="sxs-lookup"><span data-stu-id="b4138-165">They serve as property names within structured log data.</span></span> <span data-ttu-id="b4138-166">プレースホルダー名には [Pascal 形式](/dotnet/standard/design-guidelines/capitalization-conventions)を推奨します。</span><span class="sxs-lookup"><span data-stu-id="b4138-166">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="b4138-167">たとえば、`{Count}`、`{FirstName}` のようになります。</span><span class="sxs-lookup"><span data-stu-id="b4138-167">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="b4138-168">[DefineScope(String)](/dotnet/api/microsoft.extensions.logging.loggermessage.definescope) メソッドを使用して、一連のログ メッセージに適用する[ログ スコープ](xref:fundamentals/logging/index#log-scopes)を定義します。</span><span class="sxs-lookup"><span data-stu-id="b4138-168">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the [DefineScope(String)](/dotnet/api/microsoft.extensions.logging.loggermessage.definescope) method.</span></span>

<span data-ttu-id="b4138-169">サンプル アプリには、データベース内のすべての見積もりを削除するための **[すべてクリア]** ボタンがあります。</span><span class="sxs-lookup"><span data-stu-id="b4138-169">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="b4138-170">見積もりを削除するには、一度に 1 つ削除します。</span><span class="sxs-lookup"><span data-stu-id="b4138-170">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="b4138-171">見積もりを削除するたびに、ロガーで `QuoteDeleted` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b4138-171">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="b4138-172">ログ スコープは、これらのログ メッセージに追加されます。</span><span class="sxs-lookup"><span data-stu-id="b4138-172">A log scope is added to these log messages.</span></span>

<span data-ttu-id="b4138-173">*appsettings.json* のコンソール ロガーのセクションで、`IncludeScopes` を有効にします。</span><span class="sxs-lookup"><span data-stu-id="b4138-173">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/sample/appsettings.json?highlight=3-5)]

<span data-ttu-id="b4138-174">ログ スコープを作成するには、スコープに対して `Func` デリゲートを保持するフィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="b4138-174">To create a log scope, add a field to hold a `Func` delegate for the scope.</span></span> <span data-ttu-id="b4138-175">サンプル アプリでは、`_allQuotesDeletedScope` と呼ばれるフィールドを作成します (*Internal/LoggerExtensions.cs*)。</span><span class="sxs-lookup"><span data-stu-id="b4138-175">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="b4138-176">`DefineScope` を使用してデリゲートを作成します。</span><span class="sxs-lookup"><span data-stu-id="b4138-176">Use `DefineScope` to create the delegate.</span></span> <span data-ttu-id="b4138-177">デリゲートを呼び出すとき、テンプレート引数として使用するように最大 3 つの型を指定することができます。</span><span class="sxs-lookup"><span data-stu-id="b4138-177">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="b4138-178">サンプル アプリでは、削除された見積もりの数 (`int` 型) を含むメッセージ テンプレートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="b4138-178">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="b4138-179">ログ メッセージ用の静的な拡張メソッドを指定します。</span><span class="sxs-lookup"><span data-stu-id="b4138-179">Provide a static extension method for the log message.</span></span> <span data-ttu-id="b4138-180">メッセージ テンプレートに表示される名前付きプロパティの任意の型パラメーターを含めます。</span><span class="sxs-lookup"><span data-stu-id="b4138-180">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="b4138-181">サンプル アプリでは、削除する見積もりの `count` を取得し、`_allQuotesDeletedScope` を返します。</span><span class="sxs-lookup"><span data-stu-id="b4138-181">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="b4138-182">スコープは、`using` ブロックでログ拡張呼び出しをラップします。</span><span class="sxs-lookup"><span data-stu-id="b4138-182">The scope wraps the logging extension calls in a `using` block:</span></span>

[!code-csharp[](loggermessage/sample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="b4138-183">アプリのコンソール出力のログ メッセージを検査します。</span><span class="sxs-lookup"><span data-stu-id="b4138-183">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="b4138-184">次の結果には、削除された 3 つの見積もりに加えて、ログ スコープ メッセージが表示されています。</span><span class="sxs-lookup"><span data-stu-id="b4138-184">The following result shows three quotes deleted with the log scope message included:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

## <a name="additional-resources"></a><span data-ttu-id="b4138-185">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="b4138-185">Additional resources</span></span>

* [<span data-ttu-id="b4138-186">ログ</span><span class="sxs-lookup"><span data-stu-id="b4138-186">Logging</span></span>](xref:fundamentals/logging/index)
