---
title: ASP.NET Core の Razor SDK
author: Rick-Anderson
description: ASP.NET Core の Razor ページを使用して、ページのコーディングに重点を置いたシナリオをより簡略化して、MVC を使用する場合よりも生産性を高める方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/25/2018
uid: razor-pages/sdk
ms.openlocfilehash: 0e6cfeb1863ed14ffe670cf082e99f28b26718dd
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57054739"
---
# <a name="aspnet-core-razor-sdk"></a>ASP.NET Core の Razor SDK

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[](~/includes/2.1-SDK.md)]が含まれています、 `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK)。 Razor SDK とは、次のとおりです。

* ASP.NET Core MVC ベース プロジェクト用の [Razor](xref:mvc/views/razor) ファイルを含むプロジェクトのビルド、パッケージ、および発行に関わる表示と操作を標準化できます。
* Razor ファイルのコンパイルのカスタマイズを可能にする事前定義されたターゲット、プロパティ、項目のセットを含みます。

## <a name="prerequisites"></a>必須コンポーネント

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="using-the-razor-sdk"></a>Razor SDK を使用する

ほとんどの web アプリは、Razor の SDK を明示的に参照する必要はありません。

Razor SDK を使用して Razor ビューまたは Razor ページを含むクラス ライブラリを構築する方法は次のとおりです。

* `Microsoft.NET.Sdk.Razor` の代わりに `Microsoft.NET.Sdk` を使用します。

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    ...
  </Project>
  ```

* 通常、パッケージ参照を`Microsoft.AspNetCore.Mvc`ビルドし、Razor ページと Razor ビュー コンパイルに必要な追加の依存関係を受信するが必要です。 少なくとも、プロジェクトへのパッケージ参照を追加する必要があります。

  * `Microsoft.AspNetCore.Razor.Design` 
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
    
  `Microsoft.AspNetCore.Razor.Design`パッケージがプロジェクトに、Razor コンパイル タスクとターゲットを提供します。

  前述のパッケージは、`Microsoft.AspNetCore.Mvc` に組み込まれています。 次のマークアップは、Razor ファイルの ASP.NET Core Razor ページ アプリをビルドする Razor SDK を使用するプロジェクト ファイルを示します。
    
  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> `Microsoft.AspNetCore.Razor.Design`と`Microsoft.AspNetCore.Mvc.Razor.Extensions`でパッケージが含まれている、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)します。 ただし、バージョンのない`Microsoft.AspNetCore.App`パッケージ参照は、の最新バージョンが含まれていないアプリへのメタパッケージ`Microsoft.AspNetCore.Razor.Design`します。 プロジェクトは、一貫したバージョンを参照する必要があります`Microsoft.AspNetCore.Razor.Design`(または`Microsoft.AspNetCore.Mvc`) Razor の最新のビルド時の修正プログラムが含まれるようにします。 詳細については、次を参照してください。[この GitHub の問題](https://github.com/aspnet/Razor/issues/2553)します。

::: moniker-end

### <a name="properties"></a>プロパティ

Razor の SDK の動作は、プロジェクトをビルドする際に次のプロパティにより制御されます。

* `RazorCompileOnBuild` &ndash; ときに`true`、コンパイルし、プロジェクトのビルドの一部として Razor アセンブリを生成します。 既定値は `true` です。
* `RazorCompileOnPublish` &ndash; ときに`true`、コンパイルし、プロジェクトの発行の一部として Razor アセンブリを生成します。 既定値は `true` です。

プロパティと、次の表の項目は、入力と剃刀 SDK への出力の構成に使用されます。

| 項目 | 説明 |
| ----- | ----------- |
| `RazorGenerate` | コードの生成対象に入力する項目要素 (*.cshtml* ファイル) です。 |
| `RazorCompile` | 項目要素 (*.cs*ファイル)、Razor コンパイルのターゲットに入力します。 Razor アセンブリに追加でコンパイルするファイルを指定するには、この ItemGroup を使用します。 |
| `RazorTargetAssemblyAttribute` | Razor アセンブリ用の属性をコード生成するために使用する項目要素です。 例:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | 項目の要素が生成された Razor アセンブリに埋め込みリソースとして追加します。 |

| プロパティ | 説明 |
| -------- | ----------- |
| `RazorTargetName` | Razor によって生成されたアセンブリの (拡張子なしの) ファイル名です。 | 
| `RazorOutputPath` | Razor の出力ディレクトリです。 |
| `RazorCompileToolset` | Razor アセンブリをビルドするために使用するツールセットを決定するために使用します。 有効な値は `Implicit`、`RazorSDK`、`PrecompilationTool` です。 |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | 既定値は `true` です。 ときに`true`が含まれています*web.config*、 *.json*、および *.cshtml*プロジェクト内のコンテンツとしてファイル。 使用して参照されている場合`Microsoft.NET.Sdk.Web`、ファイル*wwwroot*し、構成ファイルも含まれています。 |
| `EnableDefaultRazorGenerateItems` | `true` の場合、`RazorGenerate` 項目の `Content` 項目の *.cshtml* ファイルが含まれます。 |
| `GenerateRazorTargetAssemblyInfo` | ときに`true`、生成、 *.cs*ファイルで指定された属性を格納している`RazorAssemblyAttribute`コンパイル出力ファイルが含まれています。 |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | `true` の場合、`RazorAssemblyAttribute` にアセンブリ属性の既定のセットが追加されます。 |
| `CopyRazorGenerateFilesToPublishDirectory` | ときに`true`、コピー`RazorGenerate`項目 (*.cshtml*) ファイルを発行ディレクトリ。 通常、Razor ファイルは必要ありません発行されたアプリの場合、それらのビルド時または発行時のコンパイルに参加します。 既定値は `false` です。 |
| `CopyRefAssembliesToPublishDirectory` | `true` の場合、発行ディレクトリに参照アセンブリ項目がコピーされます。 通常、参照アセンブリは必要ありません発行されたアプリのビルド時または発行時に Razor コンパイルが発生した場合。 設定`true`発行されたアプリケーションがランタイムのコンパイルを必要とする場合。 などの値を設定`true`アプリを変更する場合 *.cshtml*実行時にファイルや埋め込みのビューを使用します。 既定値は `false` です。 |
| `IncludeRazorContentInPack` | ときに`true`、すべての Razor コンテンツ アイテム (*.cshtml*ファイル)、生成された NuGet パッケージに含める対象としてマークされました。 既定値は `false` です。 |
| `EmbedRazorGenerateSources` | `true` の場合、生成された Razor アセンブリに、埋め込みファイルとして RazorGenerate (*.cshtml*) 項目が追加されます。 既定値は `false` です。 |
| `UseRazorBuildServer` | `true` の場合、コードの生成作業をオフロードするために、永続的なビルド サーバーが使用されます。 既定値は、`UseSharedCompilation` の値です。 |

プロパティの詳細については、「[MSBuild プロパティ](/visualstudio/msbuild/msbuild-properties)」を参照してください。

### <a name="targets"></a>ターゲット

Razor SDK では、次の 2 つの主要なターゲットが定義されています。

* `RazorGenerate` &ndash; コード生成 *.cs*ファイルから`RazorGenerate`項目要素。 このターゲットの前後に実行する追加のターゲットを指定するには、`RazorGenerateDependsOn` プロパティを使用します。
* `RazorCompile` &ndash; 生成されたコンパイル *.cs* Razor アセンブリへのファイルします。 このターゲットの前後に実行する追加のターゲットを指定するには、`RazorCompileDependsOn` を使用します。

### <a name="runtime-compilation-of-razor-views"></a>Razor ビューの実行時のコンパイル

* 既定では、Razor SDK は、実行時のコンパイルを実行するために必要な参照アセンブリを公開しません。 この結果、アプリケーション モデルが実行時のコンパイルに依存している場合には、コンパイルが失敗します。たとえば、アプリが公開後に埋め込まれたビューを使用したり、ビューを変更したりする場合などです。 `CopyRefAssembliesToPublishDirectory` を `true` に設定して、参照アセンブリの公開を続行します。

* Web アプリの場合、アプリが対象とすることを確認して、 `Microsoft.NET.Sdk.Web` SDK。
