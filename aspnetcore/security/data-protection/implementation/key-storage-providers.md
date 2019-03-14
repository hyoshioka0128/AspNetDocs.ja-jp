---
title: ASP.NET Core でのキー記憶域プロバイダー
author: rick-anderson
description: キー記憶域プロバイダーでは、ASP.NET Core とキー記憶域の場所を構成する方法について説明します。
ms.author: riande
ms.date: 12/19/2018
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: d6dabc9e4581e0891d1dd14f73e086d50b45bba4
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57054449"
---
# <a name="key-storage-providers-in-aspnet-core"></a><span data-ttu-id="ed648-103">ASP.NET Core でのキー記憶域プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ed648-103">Key storage providers in ASP.NET Core</span></span>

<span data-ttu-id="ed648-104">データ保護システム[検出メカニズムを使用して、既定で](xref:security/data-protection/configuration/default-settings)暗号化キーを保持するかを判断します。</span><span class="sxs-lookup"><span data-stu-id="ed648-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine where cryptographic keys should be persisted.</span></span> <span data-ttu-id="ed648-105">開発者は、既定の検出メカニズムをオーバーライドし、場所を手動で指定できます。</span><span class="sxs-lookup"><span data-stu-id="ed648-105">The developer can override the default discovery mechanism and manually specify the location.</span></span>

> [!WARNING]
> <span data-ttu-id="ed648-106">キーの永続性の明示的な場所を指定する場合データ保護システムの登録を解除 rest のメカニズムで既定のキーの暗号化キーは保存時暗号化されなくようにします。</span><span class="sxs-lookup"><span data-stu-id="ed648-106">If you specify an explicit key persistence location, the data protection system deregisters the default key encryption at rest mechanism, so keys are no longer encrypted at rest.</span></span> <span data-ttu-id="ed648-107">お勧めするさらに[明示的なキーの暗号化メカニズムを指定](xref:security/data-protection/implementation/key-encryption-at-rest)運用環境のデプロイ。</span><span class="sxs-lookup"><span data-stu-id="ed648-107">It's recommended that you additionally [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span>

## <a name="file-system"></a><span data-ttu-id="ed648-108">ファイル システム</span><span class="sxs-lookup"><span data-stu-id="ed648-108">File system</span></span>

<span data-ttu-id="ed648-109">ファイル システム ベースのキー リポジトリを構成するには、呼び出し、 [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)次に示すように構成ルーチン。</span><span class="sxs-lookup"><span data-stu-id="ed648-109">To configure a file system-based key repository, call the [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) configuration routine as shown below.</span></span> <span data-ttu-id="ed648-110">提供、 [DirectoryInfo](/dotnet/api/system.io.directoryinfo)キーを格納するリポジトリをポイントします。</span><span class="sxs-lookup"><span data-stu-id="ed648-110">Provide a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) pointing to the repository where keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

<span data-ttu-id="ed648-111">`DirectoryInfo`ローカル コンピューター上のディレクトリを指すことができます、またはネットワーク共有上のフォルダーを指すことができます。</span><span class="sxs-lookup"><span data-stu-id="ed648-111">The `DirectoryInfo` can point to a directory on the local machine, or it can point to a folder on a network share.</span></span> <span data-ttu-id="ed648-112">ローカル コンピューター上のディレクトリを指している場合 (および、シナリオでは、ローカル コンピューター上のアプリのみが使用して、このリポジトリへのアクセスを必要と)、使用を検討して[Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest)保存時のキーを暗号化する (on Windows)。</span><span class="sxs-lookup"><span data-stu-id="ed648-112">If pointing to a directory on the local machine (and the scenario is that only apps on the local machine require access to use this repository), consider using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (on Windows) to encrypt the keys at rest.</span></span> <span data-ttu-id="ed648-113">それ以外の場合、使用を検討して、 [X.509 証明書](xref:security/data-protection/implementation/key-encryption-at-rest)保存時のキーを暗号化します。</span><span class="sxs-lookup"><span data-stu-id="ed648-113">Otherwise, consider using an [X.509 certificate](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt keys at rest.</span></span>

## <a name="azure-and-redis"></a><span data-ttu-id="ed648-114">Azure と Redis</span><span class="sxs-lookup"><span data-stu-id="ed648-114">Azure and Redis</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="ed648-115">[Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/)と[Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) Azure Storage または Redis のデータ保護キーを格納するパッケージを使用します。キャッシュします。</span><span class="sxs-lookup"><span data-stu-id="ed648-115">The [Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) and [Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) packages allow storing data protection keys in Azure Storage or a Redis cache.</span></span> <span data-ttu-id="ed648-116">Web アプリの複数のインスタンスでは、キーを共有することができます。</span><span class="sxs-lookup"><span data-stu-id="ed648-116">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="ed648-117">アプリでは、複数のサーバー認証 cookie または CSRF protection を共有できます。</span><span class="sxs-lookup"><span data-stu-id="ed648-117">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="ed648-118">[Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/)と[Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) Azure Storage または Redis cache でデータ保護キーを格納するパッケージを使用します。</span><span class="sxs-lookup"><span data-stu-id="ed648-118">The [Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) and [Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) packages allow storing data protection keys in Azure Storage or a Redis cache.</span></span> <span data-ttu-id="ed648-119">Web アプリの複数のインスタンスでは、キーを共有することができます。</span><span class="sxs-lookup"><span data-stu-id="ed648-119">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="ed648-120">アプリでは、複数のサーバー認証 cookie または CSRF protection を共有できます。</span><span class="sxs-lookup"><span data-stu-id="ed648-120">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

<span data-ttu-id="ed648-121">で、Azure Blob 記憶域プロバイダーを構成するには、のいずれかを呼び出して、 [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)オーバー ロードします。</span><span class="sxs-lookup"><span data-stu-id="ed648-121">To configure the Azure Blob Storage provider, call one of the [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="ed648-122">で Redis を構成するには、のいずれかを呼び出して、 [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis)オーバー ロードします。</span><span class="sxs-lookup"><span data-stu-id="ed648-122">To configure on Redis, call one of the [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToStackExchangeRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="ed648-123">で Redis を構成するには、のいずれかを呼び出して、 [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis)オーバー ロードします。</span><span class="sxs-lookup"><span data-stu-id="ed648-123">To configure on Redis, call one of the [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

<span data-ttu-id="ed648-124">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ed648-124">For more information, see the following topics:</span></span>

* [<span data-ttu-id="ed648-125">StackExchange.Redis ConnectionMultiplexer</span><span class="sxs-lookup"><span data-stu-id="ed648-125">StackExchange.Redis ConnectionMultiplexer</span></span>](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [<span data-ttu-id="ed648-126">Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="ed648-126">Azure Redis Cache</span></span>](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [<span data-ttu-id="ed648-127">aspnet/DataProtection サンプル</span><span class="sxs-lookup"><span data-stu-id="ed648-127">aspnet/DataProtection samples</span></span>](https://github.com/aspnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a><span data-ttu-id="ed648-128">レジストリ</span><span class="sxs-lookup"><span data-stu-id="ed648-128">Registry</span></span>

<span data-ttu-id="ed648-129">**Windows の展開にのみ適用されます。**</span><span class="sxs-lookup"><span data-stu-id="ed648-129">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="ed648-130">場合によって、アプリでは、ファイル システムへの書き込みアクセスがあります。</span><span class="sxs-lookup"><span data-stu-id="ed648-130">Sometimes the app might not have write access to the file system.</span></span> <span data-ttu-id="ed648-131">アプリが仮想サービス アカウントとして実行されているシナリオについて考えてみます (など*w3wp.exe*のアプリケーション プール id)。</span><span class="sxs-lookup"><span data-stu-id="ed648-131">Consider a scenario where an app is running as a virtual service account (such as *w3wp.exe*'s app pool identity).</span></span> <span data-ttu-id="ed648-132">このような場合は、管理者は、サービス アカウント id でアクセスできるレジストリ キーをプロビジョニングできます。</span><span class="sxs-lookup"><span data-stu-id="ed648-132">In these cases, the administrator can provision a registry key that's accessible by the service account identity.</span></span> <span data-ttu-id="ed648-133">呼び出す、 [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry)次に示すように、拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="ed648-133">Call the [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) extension method as shown below.</span></span> <span data-ttu-id="ed648-134">提供、 [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey)暗号化キーを格納する場所を指しています。</span><span class="sxs-lookup"><span data-stu-id="ed648-134">Provide a [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) pointing to the location where cryptographic keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> <span data-ttu-id="ed648-135">使用することをお勧めします。 [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest)保存時のキーを暗号化します。</span><span class="sxs-lookup"><span data-stu-id="ed648-135">We recommend using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt the keys at rest.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a><span data-ttu-id="ed648-136">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="ed648-136">Entity Framework Core</span></span>

<span data-ttu-id="ed648-137">[Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/)パッケージには、Entity Framework Core を使用してデータベースにデータ保護キーを格納するためのメカニズムが用意されています。</span><span class="sxs-lookup"><span data-stu-id="ed648-137">The [Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) package provides a mechanism for storing data protection keys to a database using Entity Framework Core.</span></span> <span data-ttu-id="ed648-138">`Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet パッケージする必要がありますファイルに追加する、プロジェクトはの一部、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)します。</span><span class="sxs-lookup"><span data-stu-id="ed648-138">The `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet package must be added to the project file, it's not part of the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="ed648-139">このパッケージによって、web アプリの複数のインスタンス間でキーを共有できます。</span><span class="sxs-lookup"><span data-stu-id="ed648-139">With this package, keys can be shared across multiple instances of a web app.</span></span>

<span data-ttu-id="ed648-140">EF Core プロバイダーを構成するには、呼び出し、 [ `PersistKeysToDbContext<TContext>` ](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext)メソッド。</span><span class="sxs-lookup"><span data-stu-id="ed648-140">To configure the EF Core provider, call the [`PersistKeysToDbContext<TContext>`](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) method:</span></span>

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-15)]

<span data-ttu-id="ed648-141">ジェネリック パラメーター、`TContext`から継承する必要があります[DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)と[IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span><span class="sxs-lookup"><span data-stu-id="ed648-141">The generic parameter, `TContext`, must inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span></span>

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

<span data-ttu-id="ed648-142">作成、`DataProtectionKeys`テーブル。</span><span class="sxs-lookup"><span data-stu-id="ed648-142">Create the `DataProtectionKeys` table.</span></span> 

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ed648-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ed648-143">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ed648-144">次のコマンドの実行、**パッケージ マネージャー コンソール**(PMC) ウィンドウ。</span><span class="sxs-lookup"><span data-stu-id="ed648-144">Execute the following commands in the **Package Manager Console** (PMC) window:</span></span>

```PowerShell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="ed648-145">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ed648-145">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="ed648-146">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ed648-146">Execute the following commands in a command shell:</span></span>

```console
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

<span data-ttu-id="ed648-147">`MyKeysContext` `DbContext`上記のコード サンプルで定義されています。</span><span class="sxs-lookup"><span data-stu-id="ed648-147">`MyKeysContext` is the `DbContext` defined in the preceding code sample.</span></span> <span data-ttu-id="ed648-148">使用している場合、`DbContext`別の名前で置き換えて、`DbContext`の名前`MyKeysContext`します。</span><span class="sxs-lookup"><span data-stu-id="ed648-148">If you're using a `DbContext` with a different name, substitute your `DbContext` name for `MyKeysContext`.</span></span>

<span data-ttu-id="ed648-149">`DataProtectionKeys`クラス/エンティティは、次の表に示す構造を採用します。</span><span class="sxs-lookup"><span data-stu-id="ed648-149">The `DataProtectionKeys` class/entity adopts the structure shown in the following table.</span></span>

| <span data-ttu-id="ed648-150">プロパティ/フィールド</span><span class="sxs-lookup"><span data-stu-id="ed648-150">Property/Field</span></span> | <span data-ttu-id="ed648-151">CLR 型</span><span class="sxs-lookup"><span data-stu-id="ed648-151">CLR Type</span></span> | <span data-ttu-id="ed648-152">SQL 型</span><span class="sxs-lookup"><span data-stu-id="ed648-152">SQL Type</span></span>              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | <span data-ttu-id="ed648-153">`int`、PK、null でないです。</span><span class="sxs-lookup"><span data-stu-id="ed648-153">`int`, PK, not null</span></span>   |
| `FriendlyName` | `string` | <span data-ttu-id="ed648-154">`nvarchar(MAX)`、null</span><span class="sxs-lookup"><span data-stu-id="ed648-154">`nvarchar(MAX)`, null</span></span> |
| `Xml`          | `string` | <span data-ttu-id="ed648-155">`nvarchar(MAX)`、null</span><span class="sxs-lookup"><span data-stu-id="ed648-155">`nvarchar(MAX)`, null</span></span> |

::: moniker-end

## <a name="custom-key-repository"></a><span data-ttu-id="ed648-156">カスタム キー リポジトリ</span><span class="sxs-lookup"><span data-stu-id="ed648-156">Custom key repository</span></span>

<span data-ttu-id="ed648-157">組み込みのメカニズムがない適切な場合、開発者がカスタムを提供することで独自のキーの永続化メカニズムを指定できます[IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository)します。</span><span class="sxs-lookup"><span data-stu-id="ed648-157">If the in-box mechanisms aren't appropriate, the developer can specify their own key persistence mechanism by providing a custom [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span></span>
