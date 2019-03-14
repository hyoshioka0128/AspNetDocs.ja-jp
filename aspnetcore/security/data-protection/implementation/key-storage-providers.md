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
# <a name="key-storage-providers-in-aspnet-core"></a>ASP.NET Core でのキー記憶域プロバイダー

データ保護システム[検出メカニズムを使用して、既定で](xref:security/data-protection/configuration/default-settings)暗号化キーを保持するかを判断します。 開発者は、既定の検出メカニズムをオーバーライドし、場所を手動で指定できます。

> [!WARNING]
> キーの永続性の明示的な場所を指定する場合データ保護システムの登録を解除 rest のメカニズムで既定のキーの暗号化キーは保存時暗号化されなくようにします。 お勧めするさらに[明示的なキーの暗号化メカニズムを指定](xref:security/data-protection/implementation/key-encryption-at-rest)運用環境のデプロイ。

## <a name="file-system"></a>ファイル システム

ファイル システム ベースのキー リポジトリを構成するには、呼び出し、 [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)次に示すように構成ルーチン。 提供、 [DirectoryInfo](/dotnet/api/system.io.directoryinfo)キーを格納するリポジトリをポイントします。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

`DirectoryInfo`ローカル コンピューター上のディレクトリを指すことができます、またはネットワーク共有上のフォルダーを指すことができます。 ローカル コンピューター上のディレクトリを指している場合 (および、シナリオでは、ローカル コンピューター上のアプリのみが使用して、このリポジトリへのアクセスを必要と)、使用を検討して[Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest)保存時のキーを暗号化する (on Windows)。 それ以外の場合、使用を検討して、 [X.509 証明書](xref:security/data-protection/implementation/key-encryption-at-rest)保存時のキーを暗号化します。

## <a name="azure-and-redis"></a>Azure と Redis

::: moniker range=">= aspnetcore-2.2"

[Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/)と[Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) Azure Storage または Redis のデータ保護キーを格納するパッケージを使用します。キャッシュします。 Web アプリの複数のインスタンスでは、キーを共有することができます。 アプリでは、複数のサーバー認証 cookie または CSRF protection を共有できます。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/)と[Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) Azure Storage または Redis cache でデータ保護キーを格納するパッケージを使用します。 Web アプリの複数のインスタンスでは、キーを共有することができます。 アプリでは、複数のサーバー認証 cookie または CSRF protection を共有できます。

::: moniker-end

で、Azure Blob 記憶域プロバイダーを構成するには、のいずれかを呼び出して、 [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)オーバー ロードします。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

::: moniker range=">= aspnetcore-2.2"

で Redis を構成するには、のいずれかを呼び出して、 [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis)オーバー ロードします。

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

で Redis を構成するには、のいずれかを呼び出して、 [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis)オーバー ロードします。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

詳細については、次のトピックを参照してください。

* [StackExchange.Redis ConnectionMultiplexer](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [Azure Redis Cache](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [aspnet/DataProtection サンプル](https://github.com/aspnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a>レジストリ

**Windows の展開にのみ適用されます。**

場合によって、アプリでは、ファイル システムへの書き込みアクセスがあります。 アプリが仮想サービス アカウントとして実行されているシナリオについて考えてみます (など*w3wp.exe*のアプリケーション プール id)。 このような場合は、管理者は、サービス アカウント id でアクセスできるレジストリ キーをプロビジョニングできます。 呼び出す、 [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry)次に示すように、拡張メソッド。 提供、 [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey)暗号化キーを格納する場所を指しています。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> 使用することをお勧めします。 [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest)保存時のキーを暗号化します。

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a>Entity Framework Core

[Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/)パッケージには、Entity Framework Core を使用してデータベースにデータ保護キーを格納するためのメカニズムが用意されています。 `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet パッケージする必要がありますファイルに追加する、プロジェクトはの一部、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)します。

このパッケージによって、web アプリの複数のインスタンス間でキーを共有できます。

EF Core プロバイダーを構成するには、呼び出し、 [ `PersistKeysToDbContext<TContext>` ](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext)メソッド。

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-15)]

ジェネリック パラメーター、`TContext`から継承する必要があります[DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)と[IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

作成、`DataProtectionKeys`テーブル。 

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

次のコマンドの実行、**パッケージ マネージャー コンソール**(PMC) ウィンドウ。

```PowerShell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

コマンド シェルで次のコマンドを実行します。

```console
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

`MyKeysContext` `DbContext`上記のコード サンプルで定義されています。 使用している場合、`DbContext`別の名前で置き換えて、`DbContext`の名前`MyKeysContext`します。

`DataProtectionKeys`クラス/エンティティは、次の表に示す構造を採用します。

| プロパティ/フィールド | CLR 型 | SQL 型              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | `int`、PK、null でないです。   |
| `FriendlyName` | `string` | `nvarchar(MAX)`、null |
| `Xml`          | `string` | `nvarchar(MAX)`、null |

::: moniker-end

## <a name="custom-key-repository"></a>カスタム キー リポジトリ

組み込みのメカニズムがない適切な場合、開発者がカスタムを提供することで独自のキーの永続化メカニズムを指定できます[IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository)します。
