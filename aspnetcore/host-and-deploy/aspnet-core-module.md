---
title: ASP.NET Core モジュール
author: guardrex
description: ASP.NET Core アプリをホストするための ASP.NET Core モジュールを構成する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 02/26/2019
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 302cfb00127c223aeb5e51e4d0a9ef3cb69b10eb
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57029889"
---
# <a name="aspnet-core-module"></a>ASP.NET Core モジュール

作成者: [Tom Dykstra](https://github.com/tdykstra)、[Rick Strahl](https://github.com/RickStrahl)、[Chris Ross](https://github.com/Tratcher)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Sourabh Shirhatti](https://twitter.com/sshirhatti)、[Justin Kotalik](https://github.com/jkotalik)、[Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-2.2"

ASP.NET Core モジュールはネイティブな IIS モジュールであり、次のいずれかを目的として、IIS パイプラインにプラグインされます。

* IIS ワーカー プロセス (`w3wp.exe`) の内部で ASP.NET Core アプリをホストします。これは、[インプロセス ホスティング モデル](#in-process-hosting-model)と呼ばれています。
* [Kestrel サーバー](xref:fundamentals/servers/kestrel)を実行しているバックエンドの ASP.NET Core アプリに Web 要求を転送します。これは、[アウト プロセス ホスティング モデル](#out-of-process-hosting-model)と呼ばれています。

サポートされている Windows バージョン:

* Windows 7 以降
* Windows Server 2008 R2 以降

インプロセス ホスティングの場合、モジュールでは IIS HTTP サーバー (`IISHttpServer`) と呼ばれる IIS 用のインプロセス サーバー実装が使用されます。

アウト プロセスでホストする場合、モジュールは Kestrel に対してのみ機能します。 モジュールは [HTTP.sys](xref:fundamentals/servers/httpsys) と互換性はありません。

## <a name="hosting-models"></a>ホスティング モデル

### <a name="in-process-hosting-model"></a>インプロセス ホスティング モデル

アプリに対してインプロセス ホスティングを構成するには、そのアプリのプロジェクト ファイルに `<AspNetCoreHostingModel>` プロパティを追加して、値を `InProcess` に設定します。(アウト プロセス ホスティングは `OutOfProcess` を使用して設定します)。

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

.NET Framework をターゲットにする ASP.NET Core アプリではインプロセス ホスティング モデルはサポートされていません。

`<AspNetCoreHostingModel>` プロパティがファイルに存在しない場合、既定値は `OutOfProcess` です。

インプロセスでホストする場合は、次の特性が適用されます。

* [Kestrel](xref:fundamentals/servers/kestrel) サーバーの代わりに、IIS HTTP サーバー (`IISHttpServer`) が使用されます。 インプロセスの場合、[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) により <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> が呼び出され、次が実行されます。

  * `IISHttpServer` を登録します。
  * ASP.NET Core モジュールの背後で実行するときにサーバーがリッスンする、ポートとベース パスを構成します。
  * スタートアップ エラーをキャプチャするホストを構成します。

* [requestTimeout 属性](#attributes-of-the-aspnetcore-element) は、インプロセス ホスティングには適用されません。

* アプリ プールをアプリ間で共有することはサポートされていません。 アプリごとに 1 つのアプリ プールを使用します。

* [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)を使用したとき、または [app_offline.htm ファイルを手動で配置内に置いた](xref:host-and-deploy/iis/index#locked-deployment-files)ときには、開いている接続があると、アプリがすぐにシャットダウンされない場合があります。 たとえば、WebSocket 接続では、アプリのシャットダウンが遅れる可能性があります。

* アプリとインストールされているランタイム (x64 または x86) のアーキテクチャ (ビット) は、アプリ プールのアーキテクチャと一致する必要があります。

* ([CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) ではなく) `WebHostBuilder` を使用してアプリのホストを手動で設定するときに、アプリがこれまで Kestrel サーバー上で直接実行されていた場合 (セルフホステッド)、`UseKestrel` を呼び出してから `UseIISIntegration` を呼び出します。 順序を逆にすると、ホストは開始に失敗します。

* クライアントの切断が検出されます。 クライアントが切断されると、[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) キャンセル トークンが取り消されます。

* ASP.NET Core 2.2.1 以前の場合、<xref:System.IO.Directory.GetCurrentDirectory*> は、アプリのディレクトリではなく、IIS によって開始されたプロセスのワーカー ディレクトリを返します (たとえば、*w3wp.exe* に対して *C:\Windows\System32\inetsrv*)。

  アプリの現在のディレクトリを設定するサンプル コードについては、「[CurrentDirectoryHelpers クラス](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs)」を参照してください。 
  `SetCurrentDirectory` メソッドを呼び出します。 <xref:System.IO.Directory.GetCurrentDirectory*> の後続の呼び出しによって、アプリのディレクトリが指定されます。
  
* インプロセス ホスティングの場合、ユーザーを初期化するために内部で <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> が呼び出されることはありません。 そのため、認証のたびに要求を変換するための <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装は既定で有効になっていません。 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装で要求を返還するとき、<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> を呼び出し、認証サービスを追加します。

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }
  
  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a>アウト プロセス ホスティング モデル

アウト プロセス ホスティング用のアプリを構成するには、プロジェクト ファイルで次の方法のいずれかを使用します。

* `<AspNetCoreHostingModel>` プロパティは指定しないでください。 `<AspNetCoreHostingModel>` プロパティがファイルに存在しない場合、既定値は `OutOfProcess` です。
* `<AspNetCoreHostingModel>` プロパティの値を `OutOfProcess` に設定します (インプロセス ホスティングは `InProcess` で設定されます)。

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

IIS HTTP サーバー (`IISHttpServer`) の代わりに、[Kestrel](xref:fundamentals/servers/kestrel) サーバーが使用されます。

アウトプロセスの場合、[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) により <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> が呼び出され、次が実行されます。

* ASP.NET Core モジュールの背後で実行するときにサーバーがリッスンする、ポートとベース パスを構成します。
* スタートアップ エラーをキャプチャするホストを構成します。

### <a name="hosting-model-changes"></a>ホスティング モデルの変更

`hostingModel` 設定が *web.config* ファイル内で変更された場合 (「[web.config での構成](#configuration-with-webconfig)」セクションを参照)、モジュールによって IIS 用のワーカー プロセスがリサイクルされます。

IIS Express の場合、モジュールによってワーカー プロセスのリサイクルは行われませんが、代わりに、現在の IIS Express プロセスの正常なシャットダウンがトリガーされます。 アプリに対して次の要求が出されると、IIS Express の新しいプロセスが生成されます。

### <a name="process-name"></a>プロセス名

`Process.GetCurrentProcess().ProcessName` から、`w3wp`/`iisexpress` (インプロセス) または `dotnet` (アウト プロセス) がレポートされます。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

ASP.NET Core モジュールは、ネイティブな IIS モジュールであり、バックエンドの ASP.NET Core アプリに Web 要求を転送するために IIS パイプラインにプラグインされます。

サポートされている Windows バージョン:

* Windows 7 以降
* Windows Server 2008 R2 以降

モジュールは、Kestrel に対してのみ機能します。 モジュールは [HTTP.sys](xref:fundamentals/servers/httpsys) と互換性はありません。

ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行されるため、モジュールもプロセス管理を処理します。 モジュールは、最初の要求を受信したときに ASP.NET Core アプリのプロセスを開始し、プロセスがクラッシュした場合はアプリを再起動します。 この動作は、IIS のインプロセスで実行され、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理される ASP.NET 4.x アプリと基本的に同じです。

次の図は、IIS (ASP.NET Core モジュール) とアプリの間のリレーションシップを示しています。

![ASP.NET Core モジュール](aspnet-core-module/_static/ancm-outofprocess.png)

要求は、Web からカーネル モードの HTTP.sys ドライバーに到着します。 ドライバーは、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) で IIS への要求をルーティングします。 モジュールでは、アプリのランダムなポート (ポート 80 または 443 ではありません) で Kestrel に要求が転送されます。

モジュールが起動時に環境変数を介してポートを指定すると、サーバーは `http://localhost:{port}` をリッスンするように、IIS 統合ミドルウェアによって構成されます。 追加のチェックが実行され、モジュールから発生していない要求は拒否されます。 モジュールでは HTTPS 転送がサポートされていないため、要求は HTTPS を介して IIS によって受信された場合でも、HTTP を介して転送されます。

Kestrel によってモジュールから要求が取り込まれた後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。 ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。 IIS 統合によって追加されたミドルウェアでは、カーネルへの要求の転送を考慮して、スキーム、リモート IP、およびパスベースが更新されます。 アプリの応答が IIS に戻され、IIS はその応答を、要求を開始した HTTP クライアントに返します。

::: moniker-end

Windows 認証などの多くのネイティブなモジュールは、アクティブなままです。 ASP.NET Core モジュールと共にアクティブな IIS モジュールの詳細については、「<xref:host-and-deploy/iis/modules>」を参照してください。

ASP.NET Core モジュールでは次のことも行えます。

* ワーカー プロセスの環境変数を設定する
* 起動に関する問題をトラブルシューティングするために、Stdout 出力をファイル ストレージに記録する
* Windows 認証トークンを転送する

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>ASP.NET Core モジュールをインストールして使用する方法

ASP.NET Core モジュールをインストールして使用する方法の詳細については、「<xref:host-and-deploy/iis/index>」を参照してください。

## <a name="configuration-with-webconfig"></a>web.config での構成

ASP.NET Core モジュールは、サイトの *web.config* ファイルの `system.webServer` ノードの `aspNetCore` セクションを使って構成します。

次に示す *web.config* ファイルは、[フレームワークに依存する展開](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)用に発行されたもので、サイトの要求を処理するように ASP.NET Core モジュールを構成します。

::: moniker range=">= aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet" 
                  arguments=".\MyApp.dll" 
                  stdoutLogEnabled="false" 
                  stdoutLogFile=".\logs\stdout" 
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet" 
                arguments=".\MyApp.dll" 
                stdoutLogEnabled="false" 
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

::: moniker-end

次の *web.config* は、[自己完結型の展開](/dotnet/articles/core/deploying/#self-contained-deployments-scd)用に発行されたものです。

::: moniker range=">= aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe" 
                  stdoutLogEnabled="false" 
                  stdoutLogFile=".\logs\stdout" 
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

<xref:System.Configuration.SectionInformation.InheritInChildApplications*> プロパティは、`false` に設定されます。これは、[\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 要素内で指定された設定が、アプリのサブディレクトリにあるアプリによって継承されないことを示します。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe" 
                stdoutLogEnabled="false" 
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

::: moniker-end

アプリが [Azure App Service](https://azure.microsoft.com/services/app-service/) に対して展開されると、`stdoutLogFile` パスは `\\?\%home%\LogFiles\stdout` に設定されます。 パスは stdout ログを *LogFiles* フォルダーに保存します。これは、サービスによって自動的に作成される場所です。

IIS サブアプリケーション構成について詳しくは、「<xref:host-and-deploy/iis/index#sub-applications>」をご覧ください。

### <a name="attributes-of-the-aspnetcore-element"></a>aspNetCore 要素の属性

::: moniker range=">= aspnetcore-2.2"

| 属性 | 説明 | 既定値 |
| --------- | ----------- | :-----: |
| `arguments` | <p>省略可能な文字列属性。</p><p>**processPath** において指定されている実行可能ファイルへの引数です。</p> | |
| `disableStartUpErrorPage` | <p>省略可能な Boolean 属性です。</p><p>true の場合、**502.5 - 処理エラー** ページは抑制され、*web.config* で構成されている 502 状態コード ページが優先されます。</p> | `false` |
| `forwardWindowsAuthToken` | <p>省略可能な Boolean 属性です。</p><p>true の場合、トークンは、%ASPNETCORE_PORT% でリッスンしている子プロセスに、要求ごとの 'MS-ASPNETCORE-WINAUTHTOKEN' ヘッダーとして転送されます。 要求ごとのこのトークンで CloseHandle を呼び出すのは、そのプロセスの役割です。</p> | `true` |
| `hostingModel` | <p>省略可能な文字列属性。</p><p>ホスティング モデルをインプロセス (`InProcess`) またはアウト プロセス (`OutOfProcess`) として指定します。</p> | `OutOfProcess` |
| `processesPerApplication` | <p>省略可能な整数属性</p><p>アプリごとにスピンアップすることができる **processPath** 設定内で指定したプロセスのインスタンス数が指定されます。</p><p>&dagger;インプロセス ホスティングの場合、値は `1` に制限されます。</p><p>設定 `processesPerApplication` は推奨されません。 この属性は将来のリリースで削除されます。</p> | 既定値: `1`<br>最小値: `1`<br>最大値: `100`&dagger; |
| `processPath` | <p>必須の文字列属性です。</p><p>HTTP 要求をリッスンするプロセスを起動する実行可能ファイルへのパスです。 相対パスがサポートされています。 パスが `.` で始まる場合、パスはサイトのルートを基準とする相対パスであると見なされます。</p> | |
| `rapidFailsPerMinute` | <p>省略可能な整数属性</p><p>**processPath** で指定されているプロセスが 1 分間にクラッシュできる回数を指定します。 この制限を超えた場合、モジュールは、1 分間の残りの間、プロセスの起動を停止します。</p><p>インプロセス ホスティングではサポートされていません。</p> | 既定値: `10`<br>最小値: `0`<br>最大値: `100` |
| `requestTimeout` | <p>省略可能な期間属性。</p><p>%ASPNETCORE_PORT% でリッスンしているプロセスからの応答を ASP.NET Core モジュールが待機する期間を指定します。</p><p>ASP.NET Core 2.1 以降のリリースに付属する ASP.NET Core モジュールのバージョンでは、`requestTimeout` は時間、分、および秒単位で指定します。</p><p>インプロセス ホスティングには適用されません。 インプロセス ホスティングの場合、アプリによって要求が処理されるまでモジュールは待機します。</p> | 既定値: `00:02:00`<br>最小値: `00:00:00`<br>最大値: `360:00:00` |
| `shutdownTimeLimit` | <p>省略可能な整数属性</p><p>*app_offline.htm* ファイルが検出されたときに、モジュールが実行可能ファイルの正常なシャットダウンを待機する秒単位の期間です。</p> | 既定値: `10`<br>最小値: `0`<br>最大値: `600` |
| `startupTimeLimit` | <p>省略可能な整数属性</p><p>ポートでリッスンするプロセスを実行可能ファイルが開始するのをモジュールが待機する秒単位の期間です。 この制限時間を超えた場合、モジュールはプロセスを強制終了します。 最後のローリング分においてアプリが **rapidFailsPerMinute** 回だけ開始を失敗しているのでないかぎり、モジュールは、新しい要求を受け取るとプロセスの再起動を試み、それ以降に受信した要求に対してプロセスの再起動を試み続けます。</p><p>値 0 (ゼロ) は無限のタイムアウトと見なされ**ません**。</p> | 既定値: `120`<br>最小値: `0`<br>最大値: `3600` |
| `stdoutLogEnabled` | <p>省略可能な Boolean 属性です。</p><p>true の場合、**processPath** で指定されているプロセスの **stdout** と **stderr** は、**stdoutLogFile** で指定されているファイルにリダイレクトされます。</p> | `false` |
| `stdoutLogFile` | <p>省略可能な文字列属性。</p><p>**processPath** で指定されているプロセスからの **stdout** と **stderr** がログに記録される相対ファイル パスまたは絶対ファイル パスを指定します。 相対パスの基準はサイトのルートです。 `.` で始まっているパスはすべてサイト ルートに対する相対パスであり、他のすべてのパスは絶対パスとして扱われます。 パスで指定されたフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。 アンダースコアの区切り記号を使って、タイムスタンプ、プロセス ID、およびファイル拡張子 (*.log*) が、**stdoutLogFile** パスの最後のセグメントに追加されます。 たとえば、値として `.\logs\stdout` を指定し、2018 年 2 月 5 日の 19:41:32 にプロセス ID 1934 で保存すると、stdout ログは *logs* フォルダーに *stdout_20180205194132_1934.log* として保存されます。</p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| 属性 | 説明 | 既定値 |
| --------- | ----------- | :-----: |
| `arguments` | <p>省略可能な文字列属性。</p><p>**processPath** において指定されている実行可能ファイルへの引数です。</p>| |
| `disableStartUpErrorPage` | <p>省略可能な Boolean 属性です。</p><p>true の場合、**502.5 - 処理エラー** ページは抑制され、*web.config* で構成されている 502 状態コード ページが優先されます。</p> | `false` |
| `forwardWindowsAuthToken` | <p>省略可能な Boolean 属性です。</p><p>true の場合、トークンは、%ASPNETCORE_PORT% でリッスンしている子プロセスに、要求ごとの 'MS-ASPNETCORE-WINAUTHTOKEN' ヘッダーとして転送されます。 要求ごとのこのトークンで CloseHandle を呼び出すのは、そのプロセスの役割です。</p> | `true` |
| `processesPerApplication` | <p>省略可能な整数属性</p><p>アプリごとにスピンアップすることができる **processPath** 設定内で指定したプロセスのインスタンス数が指定されます。</p><p>設定 `processesPerApplication` は推奨されません。 この属性は将来のリリースで削除されます。</p> | 既定値: `1`<br>最小値: `1`<br>最大値: `100` |
| `processPath` | <p>必須の文字列属性です。</p><p>HTTP 要求をリッスンするプロセスを起動する実行可能ファイルへのパスです。 相対パスがサポートされています。 パスが `.` で始まる場合、パスはサイトのルートを基準とする相対パスであると見なされます。</p> | |
| `rapidFailsPerMinute` | <p>省略可能な整数属性</p><p>**processPath** で指定されているプロセスが 1 分間にクラッシュできる回数を指定します。 この制限を超えた場合、モジュールは、1 分間の残りの間、プロセスの起動を停止します。</p> | 既定値: `10`<br>最小値: `0`<br>最大値: `100` |
| `requestTimeout` | <p>省略可能な期間属性。</p><p>%ASPNETCORE_PORT% でリッスンしているプロセスからの応答を ASP.NET Core モジュールが待機する期間を指定します。</p><p>ASP.NET Core 2.1 以降のリリースに付属する ASP.NET Core モジュールのバージョンでは、`requestTimeout` は時間、分、および秒単位で指定します。</p> | 既定値: `00:02:00`<br>最小値: `00:00:00`<br>最大値: `360:00:00` |
| `shutdownTimeLimit` | <p>省略可能な整数属性</p><p>*app_offline.htm* ファイルが検出されたときに、モジュールが実行可能ファイルの正常なシャットダウンを待機する秒単位の期間です。</p> | 既定値: `10`<br>最小値: `0`<br>最大値: `600` |
| `startupTimeLimit` | <p>省略可能な整数属性</p><p>ポートでリッスンするプロセスを実行可能ファイルが開始するのをモジュールが待機する秒単位の期間です。 この制限時間を超えた場合、モジュールはプロセスを強制終了します。 最後のローリング分においてアプリが **rapidFailsPerMinute** 回だけ開始を失敗しているのでないかぎり、モジュールは、新しい要求を受け取るとプロセスの再起動を試み、それ以降に受信した要求に対してプロセスの再起動を試み続けます。</p><p>値 0 (ゼロ) は無限のタイムアウトと見なされ**ません**。</p> | 既定値: `120`<br>最小値: `0`<br>最大値: `3600` |
| `stdoutLogEnabled` | <p>省略可能な Boolean 属性です。</p><p>true の場合、**processPath** で指定されているプロセスの **stdout** と **stderr** は、**stdoutLogFile** で指定されているファイルにリダイレクトされます。</p> | `false` |
| `stdoutLogFile` | <p>省略可能な文字列属性。</p><p>**processPath** で指定されているプロセスからの **stdout** と **stderr** がログに記録される相対ファイル パスまたは絶対ファイル パスを指定します。 相対パスの基準はサイトのルートです。 `.` で始まっているパスはすべてサイト ルートに対する相対パスであり、他のすべてのパスは絶対パスとして扱われます。 モジュールがログ ファイルを作成するためには、パスで指定されているすべてのフォルダーが存在する必要があります。 アンダースコアの区切り記号を使って、タイムスタンプ、プロセス ID、およびファイル拡張子 (*.log*) が、**stdoutLogFile** パスの最後のセグメントに追加されます。 たとえば、値として `.\logs\stdout` を指定し、2018 年 2 月 5 日の 19:41:32 にプロセス ID 1934 で保存すると、stdout ログは *logs* フォルダーに *stdout_20180205194132_1934.log* として保存されます。</p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

| 属性 | 説明 | 既定値 |
| --------- | ----------- | :-----: |
| `arguments` | <p>省略可能な文字列属性。</p><p>**processPath** において指定されている実行可能ファイルへの引数です。</p>| |
| `disableStartUpErrorPage` | <p>省略可能な Boolean 属性です。</p><p>true の場合、**502.5 - 処理エラー** ページは抑制され、*web.config* で構成されている 502 状態コード ページが優先されます。</p> | `false` |
| `forwardWindowsAuthToken` | <p>省略可能な Boolean 属性です。</p><p>true の場合、トークンは、%ASPNETCORE_PORT% でリッスンしている子プロセスに、要求ごとの 'MS-ASPNETCORE-WINAUTHTOKEN' ヘッダーとして転送されます。 要求ごとのこのトークンで CloseHandle を呼び出すのは、そのプロセスの役割です。</p> | `true` |
| `processesPerApplication` | <p>省略可能な整数属性</p><p>アプリごとにスピンアップすることができる **processPath** 設定内で指定したプロセスのインスタンス数が指定されます。</p><p>設定 `processesPerApplication` は推奨されません。 この属性は将来のリリースで削除されます。</p> | 既定値: `1`<br>最小値: `1`<br>最大値: `100` |
| `processPath` | <p>必須の文字列属性です。</p><p>HTTP 要求をリッスンするプロセスを起動する実行可能ファイルへのパスです。 相対パスがサポートされています。 パスが `.` で始まる場合、パスはサイトのルートを基準とする相対パスであると見なされます。</p> | |
| `rapidFailsPerMinute` | <p>省略可能な整数属性</p><p>**processPath** で指定されているプロセスが 1 分間にクラッシュできる回数を指定します。 この制限を超えた場合、モジュールは、1 分間の残りの間、プロセスの起動を停止します。</p> | 既定値: `10`<br>最小値: `0`<br>最大値: `100` |
| `requestTimeout` | <p>省略可能な期間属性。</p><p>%ASPNETCORE_PORT% でリッスンしているプロセスからの応答を ASP.NET Core モジュールが待機する期間を指定します。</p><p>ASP.NET Core 2.0 以前のリリースに付属する ASP.NET Core モジュールのバージョンでは、`requestTimeout` は整数でのみ指定する必要があります。そうしないと、既定値の 2 分に設定されます。</p> | 既定値: `00:02:00`<br>最小値: `00:00:00`<br>最大値: `360:00:00` |
| `shutdownTimeLimit` | <p>省略可能な整数属性</p><p>*app_offline.htm* ファイルが検出されたときに、モジュールが実行可能ファイルの正常なシャットダウンを待機する秒単位の期間です。</p> | 既定値: `10`<br>最小値: `0`<br>最大値: `600` |
| `startupTimeLimit` | <p>省略可能な整数属性</p><p>ポートでリッスンするプロセスを実行可能ファイルが開始するのをモジュールが待機する秒単位の期間です。 この制限時間を超えた場合、モジュールはプロセスを強制終了します。 最後のローリング分においてアプリが **rapidFailsPerMinute** 回だけ開始を失敗しているのでないかぎり、モジュールは、新しい要求を受け取るとプロセスの再起動を試み、それ以降に受信した要求に対してプロセスの再起動を試み続けます。</p> | 既定値: `120`<br>最小値: `0`<br>最大値: `3600` |
| `stdoutLogEnabled` | <p>省略可能な Boolean 属性です。</p><p>true の場合、**processPath** で指定されているプロセスの **stdout** と **stderr** は、**stdoutLogFile** で指定されているファイルにリダイレクトされます。</p> | `false` |
| `stdoutLogFile` | <p>省略可能な文字列属性。</p><p>**processPath** で指定されているプロセスからの **stdout** と **stderr** がログに記録される相対ファイル パスまたは絶対ファイル パスを指定します。 相対パスの基準はサイトのルートです。 `.` で始まっているパスはすべてサイト ルートに対する相対パスであり、他のすべてのパスは絶対パスとして扱われます。 モジュールがログ ファイルを作成するためには、パスで指定されているすべてのフォルダーが存在する必要があります。 アンダースコアの区切り記号を使って、タイムスタンプ、プロセス ID、およびファイル拡張子 (*.log*) が、**stdoutLogFile** パスの最後のセグメントに追加されます。 たとえば、値として `.\logs\stdout` を指定し、2018 年 2 月 5 日の 19:41:32 にプロセス ID 1934 で保存すると、stdout ログは *logs* フォルダーに *stdout_20180205194132_1934.log* として保存されます。</p> | `aspnetcore-stdout` |

::: moniker-end

### <a name="setting-environment-variables"></a>環境変数の設定

::: moniker range=">= aspnetcore-3.0"

`processPath` 属性で、プロセスに対する環境変数を指定できます。 `<environmentVariables>` コレクション要素の `<environmentVariable>` 子要素で、環境変数を指定します。 このセクションで設定された環境変数は、システム環境変数より優先されます。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

`processPath` 属性で、プロセスに対する環境変数を指定できます。 `<environmentVariables>` コレクション要素の `<environmentVariable>` 子要素で、環境変数を指定します。

> [!WARNING]
> このセクションで設定した環境変数は、同じ名前を使って設定したシステム環境変数と競合します。 *web.config* ファイルと Windows のシステム レベルの両方で 1 つの環境変数が設定されている場合、*web.config* ファイルからの値がシステム環境変数の値に追加されるため (例: `ASPNETCORE_ENVIRONMENT: Development;Development`)、アプリが起動できなくなります。

::: moniker-end

次の例では、2 つの環境変数を設定しています。 `ASPNETCORE_ENVIRONMENT` は、`Development` に対するアプリの環境を構成します。 開発者は、アプリの例外をデバッグするときに[開発者例外ページ](xref:fundamentals/error-handling)を強制的に読み込むため、*web.config* ファイルでこの値を一時的に設定できます。 `CONFIG_DIR` はユーザー定義の環境変数の例です。開発者はここに、アプリの構成ファイルを読み込むためのパスを形成するために起動時に値を読み取るコードを記述してあります。

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> *web.config* 内で環境を直接設定する代わりに、発行プロファイル (*.pubxml*) またはプロジェクト ファイルに `<EnvironmentName>` プロパティを含めることができます。 この方法では、プロジェクトが発行されるときに *web.config* に環境が設定されます。
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

::: moniker-end

> [!WARNING]
> インターネットなどの信頼されていないネットワークにアクセスできないステージング サーバーやテスト サーバーでは、`ASPNETCORE_ENVIRONMENT` 環境変数を `Development` に設定するだけです。

## <a name="appofflinehtm"></a>app_offline.htm

*app_offline.htm* という名前のファイルがアプリのルート ディレクトリで検出された場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、受信要求の処理を停止することを試みます。 `shutdownTimeLimit` で定義されている秒数が経過してもまだアプリが実行している場合、ASP.NET Core モジュールは実行中のプロセスを強制終了します。

*app_offline.htm* ファイルが存在している間、ASP.NET Core モジュールは、*app_offline.htm* ファイルの内容を返送することで、要求に応答します。 *app_offline.htm* ファイルが削除されると、次の要求によってアプリが起動されます。

::: moniker range=">= aspnetcore-2.2"

アウト プロセス ホスティング モデルを使用するときは、開いている接続があると、アプリがすぐにシャットダウンされない可能性があります。 たとえば、WebSocket 接続では、アプリのシャットダウンが遅れる可能性があります。

::: moniker-end

## <a name="start-up-error-page"></a>起動エラー ページ

::: moniker range=">= aspnetcore-2.2"

インプロセス ホスティングでもアウト プロセス ホスティングでも、アプリの起動に失敗すると、カスタム エラー ページが生成されます。

ASP.NET Core モジュールが、インプロセスまたはアウト プロセスのどちらかの要求ハンドラーの検索に失敗した場合、*500.0 - インプロセス/アウト プロセス ハンドラーの読み込みエラー*状態コード ページが表示されます。

インプロセス ホスティングで、ASP.NET Core モジュールによるアプリの起動が失敗すると、*500.30 - 開始エラー*状態コード ページが表示されます。

アウト プロセス ホスティングで、ASP.NET Core モジュールがバックエンド プロセスの起動に失敗した場合、またはバックエンド プロセスは開始しても構成されているポートでのリッスンに失敗した場合は、*502.5 処理エラー*状態コード ページが表示されます。

このページを抑制して、既定の IIS 5xx 状態コード ページに戻すには、`disableStartUpErrorPage` 属性を使います。 カスタム エラー メッセージの構成方法について詳しくは、「[HTTP エラー \<httpErrors](/iis/configuration/system.webServer/httpErrors/)」をご覧ください。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

ASP.NET Core モジュールが、バックエンド プロセスの起動に失敗した場合、またはバックエンド プロセスは開始しても構成されているポートでのリッスンに失敗した場合は、*502.5 処理エラー*状態コード ページが表示されます。 このページを抑制して、既定の IIS 502 状態コード ページに戻すには、`disableStartUpErrorPage` 属性を使います。 カスタム エラー メッセージの構成方法について詳しくは、「[HTTP エラー \<httpErrors](/iis/configuration/system.webServer/httpErrors/)」をご覧ください。

![502.5 処理エラーの状態コード ページ](aspnet-core-module/_static/ANCM-502_5.png)

::: moniker-end

## <a name="log-creation-and-redirection"></a>ログの作成とリダイレクト

`aspNetCore` 要素の `stdoutLogEnabled` 属性および `stdoutLogFile` 属性が設定されている場合は、stdout および stderr コンソール出力が ASP.NET Core モジュールによってディスクにリダイレクトされます。 `stdoutLogFile` パスのフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。 アプリ プールは、ログが書き込まれる場所への書き込みアクセス権を持っている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\<app_pool_name>` を使います)。

プロセスのリサイクル/再起動が発生しない場合、ログは循環されません。 ログが使用するディスク領域を制限するのは、ホストの役割です。

stdout ログの使用は、アプリ起動時の問題をトラブルシューティングする場合にのみ推奨されます。 一般的なアプリ ログの目的には、stdout ログを使わないでください。 ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。 詳しくは、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」をご覧ください。

ログ ファイルの作成時には、タイムスタンプとファイルの拡張子が自動的に追加されます。 ログ ファイル名は、タイムスタンプ、プロセス ID、およびファイル拡張子 (*.log*) を `stdoutLogFile` パスの最後のセグメント (通常は *stdout*) にアンダースコアで区切って追加することで構成されます。 `stdoutLogFile` パスが *stdout* で終わっている場合、PID が 1934 で 2018 年 2 月 5 日の 19:42:32 に作成されたアプリのログのファイル名は、*stdout_20180205194132_1934.log* になります。

::: moniker range=">= aspnetcore-2.2"

`stdoutLogEnabled` が false の場合は、アプリの起動時に発生するエラーがキャプチャされ、30 KB までイベント ログに出力されます。 起動後、すべての追加のログが破棄されます。

::: moniker-end

次の `aspNetCore` 要素の例では、Azure App Service でホストされているアプリの stdout ログを構成しています。 ローカル ログには、ローカル パスまたはネットワーク共有パスを使用できます。 AppPool のユーザー ID に、指定されたパスへの書き込みアクセス許可があることを確認してください。

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout">
</aspNetCore>
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="enhanced-diagnostic-logs"></a>強化された診断ログ

ASP.NET Core モジュールは、強化された診断ログを提供するよう構成できます。 *web.config* で、`<aspNetCore>` 要素に `<handlerSettings>` 要素を追加します。`debugLevel` を `TRACE` に設定すると、診断情報が再現性の高いものになります。

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="debugFile" value="aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

デバッグ レベル (`debugLevel`) 値には、レベルと場所の両方を含めることができます。

レベルは次のとおりです (情報量が少ないものから多いものへの順)。

* ERROR
* WARNING
* INFO
* TRACE

場所は次のとおりです (複数の場所を指定できます)。

* CONSOLE
* EVENTLOG
* ファイル

ハンドラー設定は、次の環境変数を使用して指定することもできます。

* `ASPNETCORE_MODULE_DEBUG_FILE` &ndash; デバッグ ログ ファイルへのパス  (既定: *aspnetcore debug.log*)。
* `ASPNETCORE_MODULE_DEBUG` &ndash; デバッグ レベルの設定。

> [!WARNING]
> 配置内でデバッグ ログを、問題のトラブルシューティングに必要な時間よりも長く有効のままに**しないでください**。 ログのサイズは制限されていません。 デバッグ ログを有効のままにすると、使用可能なディスク領域が使い果たされ、サーバーまたはアプリ サービスがクラッシュする可能性があります。

::: moniker-end

*web.config* ファイルでの `aspNetCore` 要素の例については、「[web.config での構成](#configuration-with-webconfig)」をご覧ください。

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>プロキシの構成で HTTP プロトコルとペアリング トークンを使用する

::: moniker range=">= aspnetcore-2.2"

*アウト プロセス ホスティングにのみ適用されます。*

::: moniker-end

ASP.NET Core モジュールと Kestrel の間に作成されるプロキシは、HTTP プロトコルを使います。 HTTP を使うと、パフォーマンスが最適化されます。この場合、モジュールと Kestrel の間のトラフィックは、ネットワーク インターフェイスから切り離されたループバック アドレスで発生します。 モジュールと Kestrel の間のトラフィックが、サーバーから離れた場所で傍受される危険はありません。

ペアリング トークンを使用すると、Kestrel によって受信される要求が IIS によってプロキシされたものであり、他のソースからのものでないことを保証できます。 ペアリング トークンは、モジュールによって作成されて、環境変数 (`ASPNETCORE_TOKEN`) に設定されます。 ペアリング トークンはまた、プロキシされたすべての要求のヘッダー (`MS-ASPNETCORE-TOKEN`) にも設定されます。 IIS ミドルウェアは、受信した各要求をチェックし、ペアリング トークン ヘッダーの値が環境変数の値と一致することを確認します。 トークンの値が一致しない場合、要求はログに記録され、拒否されます。 ペアリング トークン環境変数およびモジュールと Kestrel の間のトラフィックには、サーバーから離れた場所からアクセスすることはできません。 ペアリング トークンの値がわからなければ、攻撃者は IIS ミドルウェアのチェックをバイパスする要求を送信できません。

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>IIS 共有構成での ASP.NET Core モジュール

ASP.NET Core モジュールのインストーラーは、**TrustedInstaller** アカウントのアクセス許可を使って実行します。 ローカル システム アカウントには、IIS 共有構成によって使われる共有パスに対する変更アクセス許可がないため、インストーラーが共有上の *applicationHost.config* ファイル内のモジュール設定を構成しようとすると、アクセス拒否エラーがスローされます。

::: moniker range=">= aspnetcore-2.2"

IIS がインストールされている同じコンピューターで IIS 共有抗生を使用するとき、`OPT_NO_SHARED_CONFIG_CHECK` パラメーターを `1` に設定して ASP.NET Core Hosting Bundle インストーラーを実行します。

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

共有構成のパスが IIS をインストールしている同じコンピューター上にないときは、次の手順を実行します。

1. IIS 共有構成を無効にします。
1. インストーラーを実行します。
1. 更新された *applicationHost.config* ファイルを共有にエクスポートします。
1. IIS 共有構成を再び有効にします。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

IIS 共有構成を使うときは、次の手順で行います。

1. IIS 共有構成を無効にします。
1. インストーラーを実行します。
1. 更新された *applicationHost.config* ファイルを共有にエクスポートします。
1. IIS 共有構成を再び有効にします。

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="application-initialization"></a>Application Initialization

[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) は、アプリ プールが開始するときまたはリサイクルされるときに、アプリに HTTP 要求を送信する IIS 機能です。 要求によってアプリの起動がトリガーされます。 Application Initialization は、ASP.NET Core モジュール バージョン 2 を備えた[インプロセス ホスティング モデル](xref:fundamentals/servers/index#in-process-hosting-model)、[アウトプロセス ホスティング モデル](xref:fundamentals/servers/index#out-of-process-hosting-model)の両方で使えます。

Application Initialization を有効にするには:

1. IIS Application Initialization 役割の機能が有効になっていることを確認します。
   * Windows 7 以降の場合:**[コントロール パネル]** > **[プログラム]** > **[プログラムと機能]** > **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。 **[インターネット インフォメーション サービス]** > **[World Wide Web サービス]** > **[アプリケーション開発機能]** を開きます。 **[Application Initialization]** のチェック ボックスをオンにします。
   * Windows Server 2008 R2 以降の場合は、**[役割と機能の追加ウィザード]** を開きます。 **[役割サービスの選択]** パネルに移動したら、**[アプリケーション開発]** ノードを開いて、**[Application Initialization]** チェック ボックスをオンにします。
1. IIS マネージャーで、**[接続]** パネルの **[アプリケーション プール]** を選択します。
1. 一覧からそのアプリのアプリ プールを選択します。
1. **[アクション]** パネルの **[アプリケーション プールの編集]** で **[詳細設定]** を選択します。
1. **[開始モード]** を **[常時実行]** に設定します。
1. **[接続]** パネルの **[サイト]** ノードを開きます。
1. アプリを選択します。
1. **[アクション]** パネルの **[Web サイトの管理]** の下で **[詳細設定]** を選択します。
1. **[有効化されたプリロード]** を **True** に設定します。

詳細については、「[IIS 8.0 Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)」をご覧ください。

[アウトプロセス ホスティング モデル](xref:fundamentals/servers/index#out-of-process-hosting-model)を使うアプリでは、外部サービスを使い、アプリに定期的に ping を送信して、それを実行させ続ける必要があります。

::: moniker-end

## <a name="module-version-and-hosting-bundle-installer-logs"></a>モジュールのバージョンとホスティング バンドル インストーラーのログ

インストールされている ASP.NET Core モジュールのバージョンを確認するには:

1. ホスティング システム上で、*%windir%\System32\inetsrv* に移動します。
1. *aspnetcore.dll* ファイルを探します。
1. ファイルを右クリックし、コンテキスト メニューの **[プロパティ]** を選びます。
1. **[詳細]** タブを選びます。**[ファイル バージョン]** と **[製品バージョン]** が、インストールされているモジュールのバージョンを表します。

モジュールのホスティング バンドル インストーラーのログは、*C:\\Users\\%UserName%\\AppData\\Local\\Temp* にあります。ファイルの名前は、*dd_DotNetCoreWinSvrHosting__\<タイムスタンプ>_000_AspNetCoreModule_x64.log* です。

## <a name="module-schema-and-configuration-file-locations"></a>モジュール、スキーマ、構成ファイルの場所

### <a name="module"></a>Module

**IIS (x86/amd64):**

   * %windir%\System32\inetsrv\aspnetcore.dll

   * %windir%\SysWOW64\inetsrv\aspnetcore.dll

::: moniker range=">= aspnetcore-2.2"

   * %ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

   * %ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

::: moniker-end

**IIS Express (x86/amd64):**

   * %ProgramFiles%\IIS Express\aspnetcore.dll

   * %ProgramFiles(x86)%\IIS Express\aspnetcore.dll

::: moniker range=">= aspnetcore-2.2"

   * %ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

   * %ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

::: moniker-end

### <a name="schema"></a>Schema

**IIS**

   * %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

::: moniker range=">= aspnetcore-2.2"

   * %windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml

::: moniker-end
**IIS Express**

   * %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

::: moniker range=">= aspnetcore-2.2"

   * %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml

::: moniker-end

### <a name="configuration"></a>構成

**IIS**

   * %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

   * Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config
   
   * *iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config

ファイルは、*applicationHost.config* ファイルで *aspnetcore* を検索することにより見つかります。

## <a name="additional-resources"></a>その他の技術情報

* <xref:host-and-deploy/iis/index>
* [ASP.NET Core モジュールの GitHub リポジトリ (参照元)](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
