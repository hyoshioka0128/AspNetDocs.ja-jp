---
title: ASP.NET Core での Web サーバーの実装
author: guardrex
description: ASP.NET Core の Web サーバー Kestrel と HTTP.sys を検出します。 サーバーを選択する方法と、リバース プロキシ サーバーを使用するタイミングについて説明します。
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/14/2019
uid: fundamentals/servers/index
---
# <a name="web-server-implementations-in-aspnet-core"></a>ASP.NET Core での Web サーバーの実装

作成者: [Tom Dykstra](https://github.com/tdykstra)、[Steve Smith](https://ardalis.com/)、[Stephen Halter](https://twitter.com/halter73)、[Chris Ross](https://github.com/Tratcher)

ASP.NET Core アプリは、インプロセス HTTP サーバー実装を使用して実行されます。 サーバーの実装では、HTTP 要求がリッスンされ、<xref:Microsoft.AspNetCore.Http.HttpContext> に構成された[要求機能](xref:fundamentals/request-features)のセットとしてアプリに公開されます。

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

ASP.NET Core には次のものが付属しています。

* [Kestrel サーバー](xref:fundamentals/servers/kestrel)は、クロスプラットフォーム HTTP サーバーの既定の実装です。
* IIS HTTP サーバーは、IIS 用の[インプロセス サーバー](#in-process-hosting-model)です。
* [HTTP.sys サーバー](xref:fundamentals/servers/httpsys)は、[HTTP.sys カーネル ドライバーおよび HTTP サーバー API](/windows/desktop/Http/http-api-start-page) に基づく Windows 専用の HTTP サーバーです。

[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) または [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) を使用すると、アプリは次のどちらかで実行されます。

* [IIS HTTP サーバー](#iis-http-server)を使用して IIS ワーカー プロセスと同じプロセス内 ([インプロセス ホスティング モデル](#in-process-hosting-model))。 "*インプロセス*" が推奨される構成です。
* [Kestrel サーバー](#kestrel)を使用して IIS ワーカー プロセスとは異なるプロセス内 ([プロセス外ホスティング モデル](#out-of-process-hosting-model))。

[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)はネイティブの IIS モジュールであり、IIS とインプロセス IIS HTTP サーバーまたは Kestrel の間のネイティブ IIS 要求が処理されます。 詳細については、「 <xref:host-and-deploy/aspnet-core-module> 」を参照してください。

## <a name="hosting-models"></a>ホスティング モデル

### <a name="in-process-hosting-model"></a>インプロセス ホスティング モデル

インプロセス ホスティング モデルを使用する場合、ASP.NET Core アプリはその IIS ワーカー プロセスと同じプロセスで実行されます。 インプロセス ホスティングは、パフォーマンスの点でアウトプロセス ホスティングよりも優れています。要求がループバック アダプター (発信されたネットワーク トラフィックを同じコンピューターに送り返すネットワーク インターフェイス) を介してプロキシされることがないからです。 IIS では [Windows プロセス アクティブ化サービス (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) を使用してプロセス管理が処理されます。

ASP.NET Core モジュール:

* アプリの初期化を実行します。
  * [CoreCLR](/dotnet/standard/glossary#coreclr) を読み込みます。
  * `Program.Main`.
* IIS ネイティブ要求の有効期間を処理します。

.NET Framework をターゲットにする ASP.NET Core アプリではインプロセス ホスティング モデルはサポートされていません。

次の図は、IIS (ASP.NET Core モジュール) とインプロセスでホストされるアプリとの間のリレーションシップを示しています。

![ASP.NET Core モジュール](_static/ancm-inprocess.png)

Web からカーネル モードの HTTP.sys ドライバーに要求が届きます。 このドライバーによって、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) 上で IIS へのネイティブ要求がルーティングされます。 モーダルは、ネイティブ要求を受け取り、それを IIS HTTP サーバー (`IISHttpServer`) に渡します。 IIS HTTP サーバーは IIS 用のインプロセス サーバーの実装であり、要求がネイティブからマネージドに変換されます。

IIS HTTP サーバーによって要求が処理された後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。 ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。 アプリの応答は、IIS の HTTP サーバーを経由して IIS に戻されます。 IIS は、要求を開始したクライアントに応答を送信します。

インプロセス ホスティングは既存のアプリではオプトインになっていますが、[dotnet new](/dotnet/core/tools/dotnet-new) テンプレートは既定ではすべての IIS および IIS Express シナリオにおいてインプロセス ホスティング モデルに設定されています。

### <a name="out-of-process-hosting-model"></a>アウト プロセス ホスティング モデル

ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行されるため、プロセス管理はモジュールによって処理されます。 モジュールでは、最初の要求が届いたときに ASP.NET Core アプリのプロセスが開始され、プロセスがシャットダウンまたはクラッシュした場合はアプリが再起動されます。 この動作は、インプロセスで実行されるアプリであり、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理されるアプリと基本的に同じです。

次の図は、IIS (ASP.NET Core モジュール) とアウトプロセスでホストされるアプリとの間のリレーションシップを示しています。

![ASP.NET Core モジュール](_static/ancm-outofprocess.png)

要求は、Web からカーネル モードの HTTP.sys ドライバーに到着します。 ドライバーは、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) で IIS への要求をルーティングします。 モジュールでは、アプリのランダムなポート (ポート 80 または 443 ではありません) で Kestrel に要求が転送されます。

モジュールが起動時に環境変数を介してポートを指定すると、サーバーは `http://localhost:{PORT}` をリッスンするように、IIS 統合ミドルウェアによって構成されます。 追加のチェックが実行され、モジュールから発生していない要求は拒否されます。 モジュールでは HTTPS 転送がサポートされていないため、要求は HTTPS を介して IIS によって受信された場合でも、HTTP を介して転送されます。

Kestrel によってモジュールから要求が取り込まれた後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。 ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。 IIS 統合によって追加されたミドルウェアでは、カーネルへの要求の転送を考慮して、スキーム、リモート IP、およびパスベースが更新されます。 アプリの応答が IIS に戻され、IIS はその応答を、要求を開始した HTTP クライアントに返します。

IIS と ASP.NET Core モジュールの構成のガイダンスについては、次のトピックをご覧ください。

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core には、既定のクロスプラットフォーム HTTP サーバーである [Kestrel サーバー](xref:fundamentals/servers/kestrel)が付属しています。

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core には、既定のクロスプラットフォーム HTTP サーバーである [Kestrel サーバー](xref:fundamentals/servers/kestrel)が付属しています。

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

ASP.NET Core には次のものが付属しています。

* [Kestrel サーバー](xref:fundamentals/servers/kestrel)は、既定のクロスプラットフォーム HTTP サーバーです。
* [HTTP.sys サーバー](xref:fundamentals/servers/httpsys)は、[HTTP.sys カーネル ドライバーおよび HTTP サーバー API](/windows/desktop/Http/http-api-start-page) に基づく Windows 専用の HTTP サーバーです。

[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) または [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) を使用すると、アプリは [Kestrel サーバー](#kestrel)を使用して IIS ワーカー プロセスとは異なるプロセス内で実行されます ("*プロセス外*")。

ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行されるため、プロセス管理はモジュールによって処理されます。 モジュールでは、最初の要求が届いたときに ASP.NET Core アプリのプロセスが開始され、プロセスがシャットダウンまたはクラッシュした場合はアプリが再起動されます。 この動作は、インプロセスで実行されるアプリであり、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理されるアプリと基本的に同じです。

次の図は、IIS (ASP.NET Core モジュール) とアウトプロセスでホストされるアプリとの間のリレーションシップを示しています。

![ASP.NET Core モジュール](_static/ancm-outofprocess.png)

要求は、Web からカーネル モードの HTTP.sys ドライバーに到着します。 ドライバーは、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) で IIS への要求をルーティングします。 モジュールでは、アプリのランダムなポート (ポート 80 または 443 ではありません) で Kestrel に要求が転送されます。

モジュールが起動時に環境変数を介してポートを指定すると、サーバーは `http://localhost:{port}` をリッスンするように、IIS 統合ミドルウェアによって構成されます。 追加のチェックが実行され、モジュールから発生していない要求は拒否されます。 モジュールでは HTTPS 転送がサポートされていないため、要求は HTTPS を介して IIS によって受信された場合でも、HTTP を介して転送されます。

Kestrel によってモジュールから要求が取り込まれた後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。 ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。 IIS 統合によって追加されたミドルウェアでは、カーネルへの要求の転送を考慮して、スキーム、リモート IP、およびパスベースが更新されます。 アプリの応答が IIS に戻され、IIS はその応答を、要求を開始した HTTP クライアントに返します。

IIS と ASP.NET Core モジュールの構成のガイダンスについては、次のトピックをご覧ください。

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core には、既定のクロスプラットフォーム HTTP サーバーである [Kestrel サーバー](xref:fundamentals/servers/kestrel)が付属しています。

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core には、既定のクロスプラットフォーム HTTP サーバーである [Kestrel サーバー](xref:fundamentals/servers/kestrel)が付属しています。

---

::: moniker-end

## <a name="kestrel"></a>Kestrel

Kestrel は、ASP.NET Core のプロジェクト テンプレートに含まれる既定の Web サーバーです。

::: moniker range=">= aspnetcore-2.0"

Kestrel は次のように使用できます。

* これ自体で、インターネットを含むネットワークから直接要求を処理するエッジ サーバーとして。

  ![リバース プロキシ サーバーなしでインターネットと直接通信する Kestrel](kestrel/_static/kestrel-to-internet2.png)

* [インターネット インフォメーション サービス (IIS)](https://www.iis.net/)、[Nginx](http://nginx.org)、[Apache](https://httpd.apache.org/) などの*リバース プロキシ サーバー*と共に。 リバース プロキシ サーバーはインターネットから HTTP 要求を受け取り、これを Kestrel に転送します。

  ![IIS、Nginx、または Apache などのリバース プロキシ サーバーを介してインターネットと間接的に通信する Kestrel](kestrel/_static/kestrel-to-internet.png)

いずれのホスティング構成も、リバース プロキシ サーバーの有無に関わらず、ASP.NET Core 2.1 以降のアプリに対してサポートされます。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

アプリが内部ネットワークからの要求のみを受け入れる場合は、Kestrel を単独で使用することができます。

![内部ネットワークと直接通信する Kestrel](kestrel/_static/kestrel-to-internal.png)

アプリをインターネットに公開する場合は、Kestrel が[インターネット インフォメーション サービス (IIS)](https://www.iis.net/)、[Nginx](http://nginx.org)、[Apache](https://httpd.apache.org/) などの*リバース プロキシ サーバー*を使用する必要があります。 リバース プロキシ サーバーはインターネットから HTTP 要求を受け取り、これを Kestrel に転送します。

![IIS、Nginx、または Apache などのリバース プロキシ サーバーを介してインターネットと間接的に通信する Kestrel](kestrel/_static/kestrel-to-internet.png)

インターネットに直接公開される一般向けのエッジ サーバー展開でリバース プロキシを使用する最も重要な理由は、セキュリティです。 1.x バージョンの Kestrel はインターネットからの攻撃を防御する重要なセキュリティ機能を備えていません。 これには、適切なタイムアウト、要求サイズの制限、およびコンカレント接続の制限などが含まれます (ただし、これらに限定されない)。

::: moniker-end

Kestrel の構成ガイダンスおよびリバース プロキシ構成で Kestrel を使用するときの情報については、「<xref:fundamentals/servers/kestrel>」をご覧ください。

### <a name="nginx-with-kestrel"></a>Nginx と Kestrel

Kestrel のリバース プロキシ サーバーとして Linux で Nginx を使用する方法については、<xref:host-and-deploy/linux-nginx> を参照してください。

### <a name="apache-with-kestrel"></a>Apache と Kestrel

Kestrel のリバース プロキシ サーバーとして Linux で Apache を使用する方法については、<xref:host-and-deploy/linux-apache> を参照してください。

::: moniker range=">= aspnetcore-2.2"

## <a name="iis-http-server"></a>IIS HTTP サーバー

IIS HTTP サーバーは、IIS 用の[インプロセス サーバー](#in-process-hosting-model)であり、インプロセスの展開に必要です。 [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)では、IIS と IIS HTTP サーバーの間のネイティブ IIS 要求が処理されます。 詳細については、「 <xref:host-and-deploy/aspnet-core-module> 」を参照してください。

::: moniker-end

## <a name="httpsys"></a>HTTP.sys

Windows で ASP.NET Core アプリを実行する場合は、HTTP.sys を Kestrel の代わりに使用できます。 最適なパフォーマンスを得るには、通常は Kestrel をお勧めします。 HTTP.sys は、アプリがインターネットに公開されていて、必要な機能が HTTP.sys でサポートされているものの、Kestrel ではサポートされていないシナリオで使用できます。 詳細については、「 <xref:fundamentals/servers/httpsys> 」を参照してください。

![インターネットと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internet.png)

HTTP.sys は、内部ネットワークにのみ公開されるアプリにも使用できます。

![内部ネットワークと直接通信する HTTP.sys](httpsys/_static/httpsys-to-internal.png)

HTTP.sys の構成のガイダンスについては、「<xref:fundamentals/servers/httpsys>」をご覧ください。

## <a name="aspnet-core-server-infrastructure"></a>ASP.NET Core サーバー インフラストラクチャ

`Startup.Configure` メソッドで使用可能な <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> は、<xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection> 型の <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> プロパティを公開します。 Kestrel および HTTP.sys は、それぞれ単独の機能 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> のみを公開しますが、サーバーの実装が異なると追加機能が公開される場合があります。

`IServerAddressesFeature` を使用すれば、実行時にサーバー実装がバインドされたポートを見つけることができます。

## <a name="custom-servers"></a>カスタム サーバー

組み込みサーバーがアプリの要件に合わない場合は、カスタム サーバー実装を作成できます。 [Nowin](https://github.com/Bobris/Nowin) ベースの <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 実装の作成方法については、[Open Web Interface for .NET (OWIN) のガイド](xref:fundamentals/owin)を参照してください。 実装を必要とするのは、アプリで使用される機能のインターフェイスのみです。ただし、少なくとも <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> と <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> はサポートされている必要があります。

## <a name="server-startup"></a>サーバーの起動

統合開発環境 (IDE) またはエディターでアプリが開始されると、サーバーが起動されます。

* [Visual Studio](https://www.visualstudio.com/vs/) &ndash; 起動プロファイルを使用して、[IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)またはコンソールで、アプリとサーバーを開始できます。
* [Visual Studio Code](https://code.visualstudio.com/) &ndash; CoreCLR デバッガーをアクティブ化する [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode) によって、アプリとサーバーが開始されます。
* [Visual Studio for Mac](https://www.visualstudio.com/vs/mac/) &ndash; アプリとサーバーは、[Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/) によって開始されます。

コマンド プロンプトからプロジェクトのフォルダーでアプリを起動すると、[dotnet run](/dotnet/core/tools/dotnet-run) によってアプリとサーバーが起動されます (Kestrel および HTTP.sys のみ)。 この構成は、`Debug` (既定) または `Release` のどちらかに設定された `-c|--configuration` オプションによって指定されます。 起動プロファイルが *launchSettings.json* ファイルに存在する場合は、`--launch-profile <NAME>` オプションを使用して起動プロファイルを設定します (`Development`、`Production` など)。 詳しくは、「[dotnet run](/dotnet/core/tools/dotnet-run)」および「[.NET Core の配布パッケージ](/dotnet/core/build/distribution-packaging)」をご覧ください。

## <a name="http2-support"></a>HTTP/2 のサポート

[HTTP/2](https://httpwg.org/specs/rfc7540.html) は、次の展開シナリオでの ASP.NET Core でサポートされます。

::: moniker range=">= aspnetcore-2.2"

* [Kestrel](xref:fundamentals/servers/kestrel#http2-support)
  * オペレーティング システム
    * Windows Server 2016/Windows 10 以降&dagger;
    * OpenSSL 1.0.2 以降を使用した Linux (Ubuntu 16.04 以降など)
    * 将来のリリースでは HTTP/2 が macOS 上でサポートされるようになります。
  * ターゲット フレームワーク: .NET Core 2.2 以降
* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 以降
  * ターゲット フレームワーク:HTTP.sys の展開には適用できません。
* [IIS (インプロセス)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 以降、IIS 10 以降
  * ターゲット フレームワーク: .NET Core 2.2 以降
* [IIS (アウトプロセス)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 以降、IIS 10 以降
  * 一般向けのエッジ サーバーでは HTTP/2 を使用しますが、Kestrel へのリバース プロキシ接続では HTTP/1.1 を使用します。
  * ターゲット フレームワーク:IIS アウトプロセスの展開には適用できません。

&dagger;Kestrel では、Windows Server 2012 R2 および Windows 8.1 上での HTTP/2 のサポートは制限されています。 サポートが制限されている理由は、これらのオペレーティング システムで使用できる TLS 暗号のスイートのリストが制限されているためです。 TLS 接続をセキュリティで保護するためには、楕円曲線デジタル署名アルゴリズム (ECDSA) を使用して生成した証明書が必要になる場合があります。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 以降
  * ターゲット フレームワーク:HTTP.sys の展開には適用できません。
* [IIS (アウトプロセス)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 以降、IIS 10 以降
  * 一般向けのエッジ サーバーでは HTTP/2 を使用しますが、Kestrel へのリバース プロキシ接続では HTTP/1.1 を使用します。
  * ターゲット フレームワーク:IIS アウトプロセスの展開には適用できません。

::: moniker-end

HTTP/2 接続では、[アプリケーション レイヤー プロトコルのネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) および TLS 1.2 以降を使用する必要があります。 詳細については、ご利用のサーバーの展開シナリオに関連するトピックを参照してください。

## <a name="additional-resources"></a>その他の技術情報

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
