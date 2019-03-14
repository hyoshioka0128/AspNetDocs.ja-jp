---
title: ASP.NET Core での依存関係の挿入
author: guardrex
description: ASP.NET Core で依存関係の挿入を実装する方法とそれを使う方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2019
uid: fundamentals/dependency-injection
ms.openlocfilehash: 5e1522e0819d989a7029c2928c1c33624c1774c7
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57058889"
---
# <a name="dependency-injection-in-aspnet-core"></a>ASP.NET Core での依存関係の挿入

作成者: [Steve Smith](https://ardalis.com/)、[Scott Addie](https://scottaddie.com)、[Luke Latham](https://github.com/guardrex)

ASP.NET Core では依存関係の挿入 (DI) ソフトウェア設計パターンがサポートされています。これは、クラスとその依存関係の間で[制御の反転 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) を実現するための手法です。

MVC コントローラー内部における依存関係の挿入に固有の情報については、<xref:mvc/controllers/dependency-injection> をご覧ください。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="overview-of-dependency-injection"></a>依存関係の挿入の概要

"*依存関係*" とは、他のオブジェクトが必要とする任意のオブジェクトのことです。 アプリ内の他のクラスが依存している、次の `WriteMessage` メソッドを備えた `MyDependency` クラスを調べます。

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

::: moniker range=">= aspnetcore-2.1"

クラスで `WriteMessage` メソッドを使用できるようにするために、`MyDependency` クラスのインスタンスを作成することができます。 `MyDependency` クラスは `IndexModel` クラスの依存関係です。

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

クラスで `WriteMessage` メソッドを使用できるようにするために、`MyDependency` クラスのインスタンスを作成することができます。 `MyDependency` クラスは `HomeController` クラスの依存関係です。

```csharp
public class HomeController : Controller
{
    MyDependency _dependency = new MyDependency();

    public async Task<IActionResult> Index()
    {
        await _dependency.WriteMessage(
            "HomeController.Index created this message.");

        return View();
    }
}
```

::: moniker-end

このクラスは `MyDependency` のインスタンスを作成し、これに直接依存しています。 コードの依存関係 (前の例など) には問題が含まれ、次の理由から回避する必要があります。

* `MyDependency` を別の実装で置き換えるには、クラスを変更する必要があります。
* `MyDependency` が依存関係を含んでいる場合、これらはクラスによって構成する必要があります。 複数のクラスが `MyDependency` に依存している大規模なプロジェクトでは、構成コードがアプリ全体に分散するようになります。
* このような実装では、単体テストを行うことが困難です。 アプリはモックまたはスタブの `MyDependency` クラスを使用する必要がありますが、この方法では不可能です。

依存関係の挿入は、次の方法によってこれらの問題に対応します。

* 依存関係の実装を抽象化するための、インターフェイスの使用。
* サービス コンテナー内の依存関係の登録。 ASP.NET Core には、組み込みのサービス コンテナー [IServiceProvider](/dotnet/api/system.iserviceprovider) が用意されています。 サービスはアプリの `Startup.ConfigureServices` メソッドに登録されています。
* サービスを使用するクラスのコンストラクターへの、サービスの "*挿入*"。 依存関係のインスタンスの作成、およびインスタンスが不要になったときの廃棄の役割を、フレームワークが担当します。

[サンプル アプリ](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)では、サービスがアプリに提供するメソッドが `IMyDependency` インターフェイスによって定義されます。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

このインターフェイスは、具象型 `MyDependency` によって実装されます。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

`MyDependency` はそのコンストラクター内で [ILogger&lt;TCategoryName&gt;](/dotnet/api/microsoft.extensions.logging.ilogger-1) を要求します。 依存関係の挿入をチェーン形式で使用することは、一般的ではありません。 次に、要求されたそれぞれの依存関係が、それ自身の依存関係を要求します。 コンテナーによってグラフ内の依存関係が解決され、完全に解決されたサービスが返されます。 解決する必要がある依存関係の集合的なセットは、通常、"*依存関係ツリー*"、"*依存関係グラフ*"、または "*オブジェクト グラフ*" と呼ばれます。

`IMyDependency` と `ILogger<TCategoryName>` をサービス コンテナーに登録する必要があります。 `IMyDependency` は `Startup.ConfigureServices` に登録されます。 `ILogger<TCategoryName>` はログ記録の抽象化インフラストラクチャによって登録されます。したがって、これは、フレームワークによって既定で登録される[フレームワークが提供するサービス](#framework-provided-services)です。

サンプル アプリにおいて、`IMyDependency` サービスは `MyDependency` 具象型を使用して登録されます。 登録によって、サービスの有効期間が 1 つの要求の有効期間に範囲設定されます。 [サービスの有効期間](#service-lifetimes)については、このトピックの後半で説明します。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

> [!NOTE]
> 各 `services.Add{SERVICE_NAME}` 拡張メソッドは、サービスを追加 (および場合によっては構成) します。 たとえば、`services.AddMvc()` はサービスの Razor Pages と必須の MVC を追加します。 アプリをこの規則に従わせることをお勧めします。 拡張メソッドを [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 名前空間に配置して、サービス登録のグループをカプセル化します。

サービスのコンストラクターでプリミティブ (`string` など) が必要な場合は、[構成](xref:fundamentals/configuration/index)や[オプション パターン](xref:fundamentals/configuration/options)を使ってプリミティブを挿入することができます。

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

クラスのコンストラクター経由でサービスのインスタンスが要求されます。サービスはプライベート フィールドに割り当てられて使用されます。 このフィールドは、クラス全体で必要に応じてサービスにアクセスするために使用されます。

サンプル アプリでは、`IMyDependency` インスタンスが要求され、サービスの `WriteMessage` メソッドを呼び出すために使用されます。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Controllers/MyDependencyController.cs?name=snippet1&highlight=3,5-8,13-14)]

::: moniker-end

## <a name="framework-provided-services"></a>フレームワークが提供するサービス

`Startup.ConfigureServices` メソッドでは、Entity Framework Core や ASP.NET Core MVC のようなプラットフォーム機能など、アプリが使うサービスを定義する必要があります。 最初に、`ConfigureServices` に提供される `IServiceCollection` では、次のサービスが定義されています ([ホストの構成方法](xref:fundamentals/index#host)に依存します)。

| サービスの種類 | 有効期間 |
| ------------ | -------- |
| [Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory](/dotnet/api/microsoft.aspnetcore.hosting.builder.iapplicationbuilderfactory) | 一時的 |
| [Microsoft.AspNetCore.Hosting.IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) | シングルトン |
| [Microsoft.AspNetCore.Hosting.IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) | シングルトン |
| [Microsoft.AspNetCore.Hosting.IStartup](/dotnet/api/microsoft.aspnetcore.hosting.istartup) | シングルトン |
| [Microsoft.AspNetCore.Hosting.IStartupFilter](/dotnet/api/microsoft.aspnetcore.hosting.istartupfilter) | 一時的 |
| [Microsoft.AspNetCore.Hosting.Server.IServer](/dotnet/api/microsoft.aspnetcore.hosting.server.iserver) | シングルトン |
| [Microsoft.AspNetCore.Http.IHttpContextFactory](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextfactory) | 一時的 |
| [Microsoft.Extensions.Logging.ILogger&lt;T&gt;](/dotnet/api/microsoft.extensions.logging.ilogger) | シングルトン |
| [Microsoft.Extensions.Logging.ILoggerFactory](/dotnet/api/microsoft.extensions.logging.iloggerfactory) | シングルトン |
| [Microsoft.Extensions.ObjectPool.ObjectPoolProvider](/dotnet/api/microsoft.extensions.objectpool.objectpoolprovider) | シングルトン |
| [Microsoft.Extensions.Options.IConfigureOptions&lt;T&gt;](/dotnet/api/microsoft.extensions.options.iconfigureoptions-1) | 一時的 |
| [Microsoft.Extensions.Options.IOptions&lt;T&gt;](/dotnet/api/microsoft.extensions.options.ioptions-1) | シングルトン |
| [System.Diagnostics.DiagnosticSource](/dotnet/core/api/system.diagnostics.diagnosticsource) | シングルトン |
| [System.Diagnostics.DiagnosticListener](/dotnet/core/api/system.diagnostics.diagnosticlistener) | シングルトン |

サービス (および必要であればサービスが依存するサービス) を登録するためにサービス コレクションの拡張メソッドを使用できる場合は、1 つの `Add{SERVICE_NAME}` 拡張メソッドを使用してそのサービスが必要とするすべてのサービスを登録することが規則です。 次のコードは、拡張メソッド [AddDbContext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)、[AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity)、[AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc) を使用して、コンテナーに追加のサービスを追加する方法の例です。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddMvc();
}
```

詳細については、API のドキュメントの「[ServiceCollection Class](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollection)」(ServiceCollection クラス) をご覧ください。

## <a name="service-lifetimes"></a>サービスの有効期間

登録される各サービスの適切な有効期間を選択します。 ASP.NET Core サービスは、次の有効期間で構成できます。

**一時的**

有効期間が一時的のサービスは、要求されるたびに作成されます。 この有効期間は、軽量でステートレスのサービスに最適です。

**スコープ**

有効期間がスコープのサービスは、要求ごとに 1 回作成されます。

> [!WARNING]
> ミドルウェアでスコープ サービスを使用している場合、サービスを `Invoke` または `InvokeAsync` メソッドに追加します。 コンストラクターを使用して挿入すると、サービスがシングルトンのように動作するよう強制されるので、コンストラクターを使用した挿入は行わないでください。 詳細については、「 <xref:fundamentals/middleware/index> 」を参照してください。

**シングルトン**

有効期間がシングルトンのサービスは、最初に要求されたときに作成されます (または、`ConfigureServices` が実行されて、サービス登録でインスタンスが指定された場合)。 以降の要求は、すべて同じインスタンスを使用します。 アプリをシングルトンで動作させる必要がある場合は、サービス コンテナーによるサービスの有効期間の管理を許可することをお勧めします。 クラス内のオブジェクトの有効期間を管理するために、シングルトンの設計パターンを実装してユーザー コードを提供しないでください。

> [!WARNING]
> シングルトンからスコープ サービスを解決するのは危険です。 後続の要求を処理する際に、サービスが正しくない状態になる可能性があります。

### <a name="constructor-injection-behavior"></a>コンストラクターの挿入の動作

サービスは 2 つのメカニズムによって解決できます。

* `IServiceProvider`
* [ActivatorUtilities](/dotnet/api/microsoft.extensions.dependencyinjection.activatorutilities) &ndash; 依存関係の挿入コンテナーにサービスを登録することなくオブジェクトの作成を許可します。 `ActivatorUtilities` はユーザー側の抽象化 (タグ ヘルパー、MVC コントローラー、モデル バインダーなど) で使用されます。

コンストラクターは、依存関係の挿入によって提供されない引数を受け取ることができますが、引数は既定値を割り当てる必要があります。

`IServiceProvider` または `ActivatorUtilities` によってサービスを解決する場合、コンストラクターの挿入には "*パブリック*" コンストラクターが必要です。

`ActivatorUtilities` によってサービスを解決する場合、コンストラクターの挿入に必要なことは、該当するコンストラクターが 1 つだけ存在することです。 コンストラクターのオーバーロードはサポートされていますが、依存関係の挿入によってすべての引数を設定できるオーバーロードは 1 つしか存在できません。

## <a name="entity-framework-contexts"></a>Entity Framework コンテキスト

Entity Framework コンテキストは通常、[範囲が指定された有効期間](#service-lifetimes)を利用し、サービス コンテナーに追加されます。Web アプリ データベース操作は通常、その範囲が要求に設定されるためです。 データベース コンテキストの登録時、<xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext*> オーバーロードによって有効期間が指定されなかった場合、既定の有効期間が範囲となります。 有効期間が与えられたサービスの場合、サービスより有効期間が短いデータベース コンテキストを使用できません。

## <a name="lifetime-and-registration-options"></a>有効期間と登録のオプション

有効期間と登録のオプションの違いを示すために、タスクを一意の識別子 `OperationId` を備えた操作として表す、次のインターフェイスについて考えます。 次のインターフェイスに対して操作のサービスの有効期間がどのように構成されているかに応じて、コンテナーは、クラスに要求されたときに、サービスの同じインスタンスか別のインスタンスを提供します。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

インターフェイスは `Operation` クラス内で実装されます。 指定されない場合、`Operation` コンストラクターは GUID を生成します。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

`OperationService` が登録されます。これは、その他の `Operation` 型のそれぞれに依存します。 依存関係の挿入を経由して `OperationService` が要求される場合、これは各サービスの新しいインスタンスか、依存関係のあるサービスの有効期間に基づく既存のインスタンスを受信します。

* 要求時に一時的なサービスを作成する場合、`IOperationTransient` サービスの `OperationId` と `OperationService` の `OperationId` は異なります。 `OperationService` は `IOperationTransient` クラスの新しいインスタンスを受け取ります。 新しいインスタンスは異なる `OperationId` を生成します。
* 要求ごとにスコープ サービスを作成する場合、要求内で `IOperationScoped` サービスの `OperationId` は `OperationService` のものと同じになります。 要求間で、両方のサービスは異なる `OperationId` 値を共有します。
* シングルトンとシングルトン インスタンスのサービスが 1 回作成され、すべての要求とすべてのサービス間で使用される場合、`OperationId` はすべてのサービス要求の間で一定です。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

`Startup.ConfigureServices` では、各型が名前で指定されている有効期間に従ってコンテナーに追加されます。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=12-15,18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

::: moniker-end

`IOperationSingletonInstance` サービスは、`Guid.Empty` の既知の ID を持った特定のインスタンスを使用しています。 この型が使用されるタイミングは明らかです (その GUID はすべてゼロです)。

::: moniker range=">= aspnetcore-2.1"

サンプル アプリでは、個々の要求内、および個々の要求間におけるオブジェクトの有効期間が示されます。 サンプル アプリの `IndexModel` には、各種類の `IOperation` 型と `OperationService` が必要です。 次に、ページ モデル クラスとサービスのすべての `OperationId` 値が、プロパティの割り当てを通じてページに表示されます。

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

サンプル アプリでは、個々の要求内、および個々の要求間におけるオブジェクトの有効期間が示されます。 サンプル アプリには、各種類の `IOperation` 型と `OperationService` を要求する `OperationsController` が含まれます。 `Index` アクションは、サービスの `OperationId` 値の表示のために、サービスを `ViewBag` に設定します。

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Controllers/OperationsController.cs?name=snippet1)]

::: moniker-end

2 つの要求の結果を、以下の 2 つの出力に示します。

**最初の要求:**

コントローラーの操作:

一時的: d233e165-f417-469b-a866-1cf1935d2518  
スコープ:5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9  
インスタンス:00000000-0000-0000-0000-000000000000

`OperationService` の操作:

一時的: c6b049eb-1318-4e31-90f1-eb2dd849ff64  
スコープ:5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9  
インスタンス:00000000-0000-0000-0000-000000000000

**2 番目の要求:**

コントローラーの操作:

一時的: b63bd538-0a37-4ff1-90ba-081c5138dda0  
スコープ:31e820c5-4834-4d22-83fc-a60118acb9f4  
シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9  
インスタンス:00000000-0000-0000-0000-000000000000

`OperationService` の操作:

一時的: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
スコープ:31e820c5-4834-4d22-83fc-a60118acb9f4  
シングルトン:01271bc1-9e31-48e7-8f7c-7261b040ded9  
インスタンス:00000000-0000-0000-0000-000000000000

要求内および要求間で、どの `OperationId` 値が変化しているかを確認してください。

* "*一時的な*" オブジェクトは常に異なります。 最初の要求と 2 番目の要求の一時的な `OperationId` 値は、`OperationService` 操作と要求間の両方に対して異なります。 それぞれのサービスと要求に対して、新しいインスタンスが提供されます。
* "*スコープ*" オブジェクトは、要求内では同じですが、要求間では異なります。
* "*シングルトン*" オブジェクトは、`Operation` インスタンスが `ConfigureServices` で提供されるかどうかに関係なく、すべてのオブジェクトとすべての要求について同じです。

## <a name="call-services-from-main"></a>main からサービスを呼び出す

[IServiceScopeFactory.CreateScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescopefactory.createscope) を使用して [IServiceScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescope) を作成し、アプリのスコープ内のスコープ サービスを解決します。 この方法は、起動時に初期化タスクを実行するために、スコープ サービスにアクセスするのに便利です。 次の例では、`Program.Main` で `MyScopedService` のコンテキストを取得する方法を示します。

```csharp
public static void Main(string[] args)
{
    var host = CreateWebHostBuilder(args).Build();

    using (var serviceScope = host.Services.CreateScope())
    {
        var services = serviceScope.ServiceProvider;

        try
        {
            var serviceContext = services.GetRequiredService<MyScopedService>();
            // Use the context here
        }
        catch (Exception ex)
        {
            var logger = services.GetRequiredService<ILogger<Program>>();
            logger.LogError(ex, "An error occurred.");
        }
    }

    host.Run();
}
```

## <a name="scope-validation"></a>スコープの検証

::: moniker range=">= aspnetcore-2.0"

アプリが開発環境で実行されている場合、既定のサービス プロバイダーは次を確認するためのチェックを実行します。

* スコープ サービスが、ルート サービス プロバイダーによって直接的または間接的に解決されない。
* スコープ サービスが、シングルトンに直接または間接に挿入されない。

::: moniker-end

[BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) が呼び出されると、ルート サービス プロバイダーが作成されます。 ルート サービス プロバイダーの有効期間は、プロバイダーがアプリで開始されるとアプリとサーバーの有効期間に対応し、アプリのシャットダウンには破棄されます。

スコープ サービスは、それを作成したコンテナーによって破棄されます。 ルート コンテナーにスコープ サービスが作成されると、サービスはアプリ/サーバーのシャットダウン時に、ルート コンテナーによってのみ破棄されるため、サービスの有効期間は実質的にシングルトンに昇格されます。 `BuildServiceProvider` が呼び出されると、サービス スコープの検証がこれらの状況をキャッチします。

詳細については、「 <xref:fundamentals/host/web-host#scope-validation> 」を参照してください。

## <a name="request-services"></a>要求サービス

`HttpContext` からの ASP.NET Core 要求内で使用可能なサービスは、[HttpContext.RequestServices](/dotnet/api/microsoft.aspnetcore.http.httpcontext.requestservices) コレクションを通じて公開されます。

要求サービスは、アプリの一部として構成および要求されるサービスを表します。 オブジェクトで依存関係を指定すると、これらは `ApplicationServices` ではなく `RequestServices` で検出された型で満たされます。

一般に、アプリから直接これらのプロパティを使用しないでください。 代わりに、クラスのコンストラクターを介してクラスに必要な型を要求し、フレームワークに依存関係を挿入させます。 これにより、テストしやすいクラスが生成されます。

> [!NOTE]
> コンストラクターのパラメーターとして依存関係を要求し、`RequestServices` コレクションにアクセスするようにします。

## <a name="design-services-for-dependency-injection"></a>依存関係の挿入のためのサービスの設計

ベスト プラクティスは次のとおりです。

* 各依存関係を取得するために依存関係の挿入を使用するようサービスを設計します。
* ステートフルな静的メソッドの呼び出しを回避します。
* サービス内部で依存関係のあるクラスを直接インスタンス化することを回避します。 直接のインスタンス化は、コードの固有の実装につながります。
* アプリのクラスを、小さく、十分に要素に分割された、テストしやすいものにします。

クラスに含まれる挿入される依存関係が多すぎるように見える場合、これは通常、クラスが担当する役割が多すぎて、[単一責任の原則 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) に違反していることのサインです。 責任の一部を新しいクラスに移動することにより、クラスのリファクタリングを試みます。 Razor Pages のページ モデル クラスと MVC コントローラー クラスは、UI の問題に集中する必要があることに留意します。 ビジネス ルールとデータ アクセスの実装の詳細は、これらの[個別の問題](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)に適したクラスに分離する必要があります。

### <a name="disposal-of-services"></a>サービスの破棄

コンテナーは、作成する `IDisposable` 型の `Dispose` を呼び出します。 ユーザー コードによってコンテナーに追加されたインスタンスは、自動的に破棄されません。

```csharp
// Services that implement IDisposable:
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}
public class Service3 : IDisposable {}

public interface ISomeService {}
public class SomeServiceImplementation : ISomeService, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    // The container creates the following instances and disposes them automatically:
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<ISomeService>(sp => new SomeServiceImplementation());

    // The container doesn't create the following instances, so it doesn't dispose of
    // the instances automatically:
    services.AddSingleton<Service3>(new Service3());
    services.AddSingleton(new Service3());
}
```

::: moniker range="= aspnetcore-1.0"

> [!NOTE]
> ASP.NET Core 1.0 のコンテナーは、コンテナーで作成しなかったものも含めて、"*すべての*" `IDisposable` に対して破棄を呼び出します。

::: moniker-end

## <a name="default-service-container-replacement"></a>既定のサービス コンテナーの置換

組み込みのサービス コンテナーは、フレームワークと、ほとんどのコンシューマー アプリのニーズに対応することを意図したものです。 それがサポートしない特定の機能が必要な場合は、組み込みのコンテナーを使用することをお勧めします。 組み込みのコンテナーにない、サードパーティのコンテナーでサポートされている一部の機能は次のとおりです。

* プロパティの挿入
* 名前に基づく挿入
* 子コンテナー
* 有効期間のカスタム管理
* 遅延初期化に対する `Func<T>` のサポート

アダプターをサポートするいくつかのコンテナーの一覧については、「[Dependency Injection readme.md file](https://github.com/aspnet/Extensions/tree/master/src/DependencyInjection)」 (Dependency Injection readme.md ファイル) を参照してください。

次のサンプルでは、[Autofac](https://autofac.org/) で組み込みのコンテナーが置き換えられています。

* 適切なコンテナー パッケージをインストールします。

  * [Autofac](https://www.nuget.org/packages/Autofac/)
  * [Autofac.Extensions.DependencyInjection](https://www.nuget.org/packages/Autofac.Extensions.DependencyInjection/)

* `Startup.ConfigureServices` でコンテナーを構成して、`IServiceProvider` を返します。

    ```csharp
    public IServiceProvider ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        // Add other framework services

        // Add Autofac
        var containerBuilder = new ContainerBuilder();
        containerBuilder.RegisterModule<DefaultModule>();
        containerBuilder.Populate(services);
        var container = containerBuilder.Build();
        return new AutofacServiceProvider(container);
    }
    ```

    サード パーティのコンテナーを使用するには、`Startup.ConfigureServices` で `IServiceProvider` が返される必要があります。

* `DefaultModule` で Autofac を構成します。

    ```csharp
    public class DefaultModule : Module
    {
        protected override void Load(ContainerBuilder builder)
        {
            builder.RegisterType<CharacterRepository>().As<ICharacterRepository>();
        }
    }
    ```

実行時には、Autofac を使って、型の解決と依存関係の挿入が行われます。 ASP.NET Core での Autofac の使用について詳しくは、[Autofac のドキュメント](https://docs.autofac.org/en/latest/integration/aspnetcore.html)をご覧ください。

### <a name="thread-safety"></a>スレッド セーフ

シングルトン サービスは、スレッド セーフである必要があります。 シングルトン サービスに一時的サービスへの依存関係がある場合、シングルトンによる一時的サービスの使い方によっては、一時的サービスもスレッド セーフであることが必要な場合があります。

1 つのサービスのファクトリ メソッド (例: [AddSingleton&lt;TService&gt;(IServiceCollection, Func&lt;IServiceProvider,TService&gt;)](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions.addsingleton#Microsoft_Extensions_DependencyInjection_ServiceCollectionServiceExtensions_AddSingleton__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Func_System_IServiceProvider___0__) に対する 2 番目の引数) をスレッド セーフにする必要はありません。 型 (`static`) のコンストラクターのように、1 つのスレッドによって 1 回呼び出されることが保証されます。

## <a name="recommendations"></a>推奨事項

* `async/await` および `Task` ベースのサービスの解決はサポートされていません。 C# では非同期コンストラクターがサポートされていないため、推奨パターンは、サービスを同期的に解決した後に、非同期メソッドを使用することです。

* データと構成をサービス コンテナーに直接格納しないようにします。 たとえば、通常、ユーザーのショッピング カートはサービス コンテナーに追加しません。 構成では、[オプション パターン](xref:fundamentals/configuration/options)を使う必要があります。 同様に、他のオブジェクトへのアクセスを許可するためだけに存在する "データ ホルダー" オブジェクトは避ける必要があります。 実際のアイテムを DI 経由で要求することをお勧めします。

* サービスへの静的なアクセスを回避します (たとえば、他所で使用するための [IApplicationBuilder.ApplicationServices](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.applicationservices) の静的な型指定)。

* *サービス ロケーター パターン*の使用は避けてください。 たとえば、サービス インスタンスを取得する場合、DI を使用できるときに、<xref:System.IServiceProvider.GetService*> を呼び出さないでください。 回避すべき別のサービス ロケーターのバリエーションは、実行時に依存関係を解決するファクトリを挿入することです。 この両方のプラクティスによって、複数の[制御の反転](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)方式が組み合わせられます。

* `HttpContext` への静的なアクセスを回避します (たとえば [IHttpContextAccessor.HttpContext](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor.httpcontext))。

どのような推奨事項であっても、それを無視する必要がある状況が発生する可能性があります。 例外はまれです&mdash;ほとんどがフレームワーク自体の内の特殊なケースです。

DI は静的/グローバル オブジェクト アクセス パターンの*代替機能*です。 静的オブジェクト アクセスと併用した場合、DI のメリットを実現することはできません。

## <a name="additional-resources"></a>その他の技術情報

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [依存関係の挿入による ASP.NET Core でのクリーンなコードの作成 (MSDN)](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [コンテナー管理アプリケーションの設計、前段階:コンテナーはどこに属していますか](https://blogs.msdn.microsoft.com/nblumhardt/2008/12/26/container-managed-application-design-prelude-where-does-the-container-belong/)
* [明示的な依存関係の原則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [制御の反転コンテナーと依存関係の挿入パターン (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)
* [ASP.NET Core DI 内の複数のインターフェイスにサービスを登録する方法](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)
