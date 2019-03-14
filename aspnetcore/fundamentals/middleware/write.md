---
title: カスタム ASP.NET Core ミドルウェアを記述する
author: rick-anderson
description: カスタム ASP.NET Core ミドルウェアを記述する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 02/14/2019
uid: fundamentals/middleware/write
ms.openlocfilehash: 2c5577394a10370d92c8a83f9d806b63f3245c8b
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57046239"
---
# <a name="write-custom-aspnet-core-middleware"></a>カスタム ASP.NET Core ミドルウェアを記述する

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://ardalis.com/)

ミドルウェアとは、要求と応答を処理するために、アプリのパイプラインに組み込まれたソフトウェアです。 ASP.NET Core からは、組み込みミドルウェア コンポーネントが豊富に提供されますが、カスタム ミドルウェアを記述したほうが良い場合もあります。

## <a name="middleware-class"></a>ミドルウェア クラス

ミドルウェアは一般に、クラスにカプセル化され、拡張メソッドを使用して公開されます。 クエリ文字列から現在の要求のカルチャを設定する次のようなミドルウェアを考慮します。

[!code-csharp[](index/snapshot/Culture/StartupCulture.cs?name=snippet1)]

上のサンプル コードを使って、ミドルウェア コンポーネントの作成方法を示します。 ASP.NET Core に組み込まれているローカライズのサポートについては、「<xref:fundamentals/localization>」を参照してください。

カルチャを渡すことによって、ミドルウェアをテストできます。 たとえば、`http://localhost:7997/?culture=no` のようにします。

次のコードは、ミドルウェアのデリゲートをクラスに移動します。

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddleware.cs)]

::: moniker range="< aspnetcore-2.0"

ミドルウェア `Task` メソッドの名前は `Invoke` である必要があります。 ASP.NET Core 2.0 以降では、名前は `Invoke` でも `InvokeAsync` でも構いません。

::: moniker-end

## <a name="middleware-extension-method"></a>ミドルウェア拡張メソッド

次の拡張メソッドは、<xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> を介してミドルウェアを公開します。

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddlewareExtensions.cs)]

次のコードは、`Startup.Configure` からミドルウェアを呼び出します。

[!code-csharp[](index/snapshot/Culture/Startup.cs?name=snippet1&highlight=5)]

ミドルウェアは、コンストラクターで依存関係を公開することによって、[明示的な依存関係の原則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)に従う必要があります。 ミドルウェアは、"*アプリケーションの有効期間*" ごとに 1 回構築されます。 要求内でミドルウェアとサービスを共有する必要がある場合は、「[要求ごとの依存関係](#per-request-dependencies)」を参照してください。

ミドルウェア コンポーネントは、コンストラクター パラメーターにより、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) から依存関係を解決できます。 [UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) は、追加パラメーターを直接受け入れることもできます。

## <a name="per-request-dependencies"></a>要求ごとの依存関係

ミドルウェアは要求ごとではなくアプリの起動時に構築されるため、ミドルウェアのコンストラクターによって使われる "*スコープ*" 有効期間のサービスは、各要求の間に、依存関係によって挿入される他の種類と共有されません。 ミドルウェアとその他の種類の間で "*スコープ*" サービスを共有する必要がある場合は、これらのサービスを `Invoke` メソッドのシグネチャに追加します。 `Invoke` メソッドは、DI によって設定される追加のパラメーターを受け取ることができます。

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="additional-resources"></a>その他の技術情報

* <xref:fundamentals/middleware/index>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
