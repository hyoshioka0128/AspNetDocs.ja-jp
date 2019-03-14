---
title: Razor Components のホストと展開
author: guardrex
description: ASP.NET Core、Content Delivery Networks (CDN)、ファイル サーバー、GitHub ページを使用して、Razor Components と Blazor アプリをホストし展開する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: host-and-deploy/razor-components/index
---
# <a name="host-and-deploy-razor-components"></a>Razor Components のホストと展開

作成者: [Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com)、[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

## <a name="publish-the-app"></a>アプリの発行

アプリは [dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドによって、リリース構成での展開のために発行されます。 `dotnet publish` コマンドの実行は、IDE により組み込みの発行機能を使用して自動的に行われることがあります。そのため、ご使用の展開ツールによっては、このコマンドを手動でコマンド プロンプトから実行する必要がない場合があります。

```console
dotnet publish -c Release
```

`dotnet publish` により、プロジェクトの依存関係の[復元](/dotnet/core/tools/dotnet-restore)がトリガーされ、展開に使用されるアセットの作成前にプロジェクトが[ビルド](/dotnet/core/tools/dotnet-build)されます。 ビルド プロセスの一環として、アプリのダウンロード サイズを縮小し読み込み時間を短縮するため、未使用のメソッドおよびアセンブリが削除されます。 */bin/Release/&lt;&gt;/publish* フォルダーに、展開が作成されます。

*publish* フォルダー内のアセットは、Web サーバーに展開されます。 展開のプロセスが手動であるか自動であるかは、ご使用の展開ツールによって異なります。

## <a name="host-configuration-values"></a>ホストの構成値

[サーバー側のホスティング モデル](xref:razor-components/hosting-models#server-side-hosting-model)を使用する Razor Components アプリでは、[Web ホスト構成値](xref:fundamentals/host/web-host#host-configuration-values)を受け入れることができます。

[クライアント側のホスティング モデル](xref:razor-components/hosting-models#client-side-hosting-model) を使用する Blazor アプリでは、開発環境での実行時に以下のホスト構成値をコマンドライン引数として受け入れることができます。

### <a name="content-root"></a>コンテンツ ルート

`--contentroot` 引数では、アプリのコンテンツ ファイルを含むディレクトリへの絶対パスが設定されます。

* コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。 アプリのディレクトリから、次のコマンドを実行します。

  ```console
  dotnet run --contentroot=/<content-root>
  ```

* **IIS Express** プロファイル内のアプリの *launchSettings.json* ファイルに、エントリを追加します。 アプリを Visual Studio デバッガーを使用して実行する場合、およびコマンド プロンプトから `dotnet run` を使用して実行する場合、この設定がピックアップされます。

  ```json
  "commandLineArgs": "--contentroot=/<content-root>"
  ```

* Visual Studio の **[プロパティ]** > **[デバッグ]** > **[アプリケーションの引数]** で、引数を指定します。 Visual Studio のプロパティ ページで引数を設定すると、その引数が *launchSettings.json* ファイルに追加されます。

  ```console
  --contentroot=/<content-root>
  ```

### <a name="path-base"></a>パス ベース

`--pathbase` 引数により、ルート以外の仮想パスでローカルで実行されているアプリに対して、アプリのベース パスを設定することができます (ステージングと運用環境の場合、`<base>` タグ `href` は `/` 以外のパスに設定されます)。 詳細については、「[App base path](#app-base-path)」(アプリのベース パス) セクションを参照してください。

> [!IMPORTANT]
> `<base>` タグの `href` に指定するパスとは異なり、`--pathbase` 引数値を渡すときはスラッシュ (`/`) を末尾に含めないでください。 `<base>` タグでアプリのベース パスが `<base href="/CoolApp/" />` と指定されている場合 (末尾にスラッシュあり)、コマンドライン引数値としては `--pathbase=/CoolApp` を渡してください (末尾にスラッシュなし)。

* コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。 アプリのディレクトリから、次のコマンドを実行します。

  ```console
  dotnet run --pathbase=/<virtual-path>
  ```

* **IIS Express** プロファイル内のアプリの *launchSettings.json* ファイルに、エントリを追加します。 アプリを Visual Studio デバッガーを使用して実行する場合、およびコマンド プロンプトから `dotnet run` を使用して実行する場合、この設定がピックアップされます。

  ```json
  "commandLineArgs": "--pathbase=/<virtual-path>"
  ```

* Visual Studio の **[プロパティ]** > **[デバッグ]** > **[アプリケーションの引数]** で、引数を指定します。 Visual Studio のプロパティ ページで引数を設定すると、その引数が *launchSettings.json* ファイルに追加されます。

  ```console
  --pathbase=/<virtual-path>
  ```

### <a name="urls"></a>URL

`--urls` 引数では、要求をリッスンするポートとプロトコルを使用して、IP アドレスまたはホスト アドレスが示されます。

* コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。 アプリのディレクトリから、次のコマンドを実行します。

  ```console
  dotnet run --urls=http://127.0.0.1:0
  ```

* **IIS Express** プロファイル内のアプリの *launchSettings.json* ファイルに、エントリを追加します。 アプリを Visual Studio デバッガーを使用して実行する場合、およびコマンド プロンプトから `dotnet run` を使用して実行する場合、この設定がピックアップされます。

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* Visual Studio の **[プロパティ]** > **[デバッグ]** > **[アプリケーションの引数]** で、引数を指定します。 Visual Studio のプロパティ ページで引数を設定すると、その引数が *launchSettings.json* ファイルに追加されます。

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="deploy-a-client-side-blazor-app"></a>クライアント側 Blazor アプリの展開

[クライアント側のホスティング モデルは次のとおりです](xref:razor-components/hosting-models#client-side-hosting-model)。

* Blazor アプリ、その依存関係、.NET ランタイムがブラウザーにダウンロードされます。
* アプリがブラウザー UI スレッド上で直接実行されます。 次の方法のいずれかがサポートされています。
  * Blazor アプリは、ASP.NET Core アプリによって提供されます。 詳細については、「[ASP.NET Core を使用してホストされているクライアント側 Blazor 展開](#client-side-blazor-hosted-deployment-with-aspnet-core)」セクションで説明します。
  * Blazor アプリは、Blazor アプリの提供に .NET が使用されていない静的ホスティング Web サーバーまたはサービス上に配置されます。 詳細については、「[クライアント側 Blazor スタンドアロン展開](#client-side-blazor-standalone-deployment)」セクションで説明します。
  
### <a name="configure-the-linker"></a>リンカーを構成する

Blazor では、出力アセンブリから不要な中間言語 (IL) を削除するために、IL リンク設定が各ビルド上で実行されます。 ビルド上のアセンブリのリンク設定を制御することができます。 詳細については、「 <xref:host-and-deploy/razor-components/configure-linker> 」を参照してください。

### <a name="rewrite-urls-for-correct-routing"></a>正しいルーティングのために URL を書き換える

クライアント側のアプリ内のページ コンポーネントに対するルーティング要求は、サーバー側のホストされているアプリに対するルーティング要求のようにシンプルなものではありません。 次の 2 つのページがあるクライアント側のアプリについて考えてみます。

* **_Main.cshtml_** &ndash; アプリのルートで読み込まれ、About ページ (`href="About"`) へのリンクが含まれています。
* **_About.cshtml_** &ndash; About ページ。

アプリの既定のドキュメントがブラウザーのアドレス バー (例: `https://www.contoso.com/`) を使用して要求された場合:

1. ブラウザーにより要求が送信されます。
1. 既定のページ (通常は *index.html*) が返されます。
1. *index.html* によりアプリがブートストラップされます。
1. Blazor のルーターによって読み込まれ、Razor Main ページ (*Main.cshtml*) が表示されます。

Main ページで About ページへのリンクを選択すると、About ページが読み込まれます。 Blazor のルーターにより、ブラウザーからのインターネット上での `www.contoso.com` に対する `About` の要求が停止され、About ページ自体が提供されるため、クライアント上での About ページへのリンクの選択が機能します。 *クライアント側のアプリ内*の内部ページの要求もすべて、同じように動作します。要求によって、サーバーにホストされているインターネット上のリソースに対するブラウザーベースの要求がトリガーされることはありません。 要求は、ルーターによって内部的に処理されます。

ブラウザーのアドレス バーを使用して `www.contoso.com/About` の要求が行われた場合、その要求は失敗します。 アプリのインターネット ホスト上にそのようなリソースは存在しないため、*404 見つかりません*という応答が返されます。

ブラウザーではクライアント側ページの要求がインターネットベースのホストに対して行われるため、Web サーバーとホスティング サービスでは、サーバー上に物理的に存在しないリソースに対する *index.html* ページへのすべての要求を、書き換える必要があります。 *index.html* が返されると、アプリのクライアント側のルーターがそれを受け取り、正しいリソースで応答します。

### <a name="app-base-path"></a>アプリのベース パス

アプリのベース パスは、サーバー上の仮想アプリ ルート パスです。 例えば、`/CoolApp/` の仮想フォルダー内の Contoso サーバー上に存在するアプリの場合、そのアプリは `https://www.contoso.com/CoolApp` で到達可能であり、仮想ベース パスは `/CoolApp/` となります。 アプリのベース パスを `CoolApp/` に設定することで、アプリのサーバー上の仮想位置がわかります。 アプリでは、アプリのベース パスを使用して、ルート ディレクトリに存在しないコンポーネントからのアプリのルートへの相対 URL を構築することができます。 これにより、ディレクトリ構造の別のレベルに存在するコンポーネントでは、アプリ内のさまざまな場所にある他のリソースに対するリンクを構築することができます。 リンクの `href` ターゲットがアプリのベース パス URI 空間内の場合に、そのハイパーリンクのクリックを阻止するためにも使用できます。つまり、Blazor のルーターにより内部ナビゲーションが処理されます。

多くのホスティング シナリオでは、サーバーのアプリへの仮想パスは、アプリのルートです。 このような場合、アプリのベース パスは最初にスラッシュ (`<base href="/" />`) が付きます。これは、アプリの既定の構成です。 GitHub ページと IIS 仮想ディレクトリまたはサブアプリケーションなど、その他のホスティング シナリオの場合、サーバーのアプリへの仮想パスを、アプリのベース パスとして設定する必要があります。 アプリのベース パスを設定するには、`<head>`タグ要素内で見つかった *index.html* 内の `<base>` タグを追加または更新します。 `href` 属性値として `<virtual-path>/` (末尾にスラッシュが必要) を設定します。ここで、`<virtual-path>/` は、アプリに対するサーバー上の完全仮想アプリ ルート パスです。 前の例では、仮想パスには `CoolApp/`: `<base href="CoolApp/" />` が設定されていました。

ルート以外の仮想パスが構成されているアプリの場合 (例: `<base href="CoolApp/" />`)、そのアプリは*ローカルで実行*されていると自身のリソースを見つけることができません。 ローカルでの開発およびテスト中は、実行時の `<base>` タグの `href` 値と一致する*パス ベース*引数を指定することで、この問題を克服することができます。

アプリをローカルで実行しているときにパス ベース引数をルート パス (`/`) と共に渡すには、アプリのディレクトリから次のコマンドを実行します。

```console
dotnet run --pathbase=/CoolApp
```

アプリは `http://localhost:port/CoolApp` でローカルで応答します。

詳細については、「ホストの構成値」の「[パス ベース](#path-base)」セクションを参照してください。

> [!IMPORTANT]
> アプリが[クライアント側のホスティング モデル](xref:razor-components/hosting-models#client-side-hosting-model) (**Blazor** プロジェクト テンプレートに基づく) を使用し、ASP.NET Core アプリ内で IIS サブアプリケーションとしてホストされている場合、継承された ASP.NET Core モジュール ハンドラーを無効とするか、*web.config*ファイル内のルート (親) アプリの `<handlers>` セクションがサブアプリに継承されていないことを確認することが必要となります。
>
> アプリの発行された *web.config* ファイル内のハンドラーを、`<handlers>` セクションをファイルに追加することで削除します。
>
> ```xml
> <handlers>
>   <remove name="aspNetCore" />
> </handlers>
> ```
>
> または、`inheritInChildApplications` が `false` に設定された `<location>` 要素を使用して、ルート (親) アプリの `<system.webServer>` セクションの継承を無効にします。
>
> ```xml
> <?xml version="1.0" encoding="utf-8"?>
> <configuration>
>  <location path="." inheritInChildApplications="false">
>    <system.webServer>
>      <handlers>
>        <add name="aspNetCore" ... />
>      </handlers>
>      <aspNetCore ... />
>    </system.webServer>
>  </location>
> </configuration>
> ```
>
> ハンドラーの削除または継承の無効化は、このセクションで説明したアプリのベース パスの構成に加えて実行されます。 IIS でサブアプリを構成するときに、アプリの *index.html* ファイル内のアプリのベース パスを、使用している IIS エイリアスに設定します。

### <a name="client-side-blazor-hosted-deployment-with-aspnet-core"></a>ASP.NET Core を使用してホストされているクライアント側 Blazor 展開

*ホストされている展開*により、クライアント側の Blazor アプリが、サーバー上で実行されている [ASP.NET Core アプリ](xref:index)からブラウザーに提供されます。

Blazor アプリは、発行された出力に ASP.NET Core アプリと共に含まれているため、2 つのアプリを一緒に展開することができます。 ASP.NET Core アプリをホストできる Web サーバーが必要です。 ホストされている展開の場合、Visual Studio には **Blazor (ASP.NET Core でホストされる)** プロジェクト テンプレートが含まれています ([dotnet new](/dotnet/core/tools/dotnet-new) コマンドを使用する場合は `blazorhosted` テンプレート)。

ASP.NET Core アプリでのホストと展開の詳細については、「<xref:host-and-deploy/index>」を参照してください。

Azure App Service の展開については、次のトピックを参照してください。

<xref:tutorials/publish-to-azure-webapp-using-vs>  
Visual Studio を使用して Azure App Service に ASP.NET Core アプリを発行する方法を説明します。

### <a name="client-side-blazor-standalone-deployment"></a>クライアント側 Blazor スタンドアロン展開

*スタンドアロン展開*により、クライアント側 Blazor アプリが、クライアントによって直接要求される静的ファイルのセットとして提供されます。 Web サーバーは、Blazor アプリの提供には使用されません。

#### <a name="client-side-blazor-standalone-hosting-with-iis"></a>IIS を使用したクライアント側 Blazor スタンドアロン ホスティング

IIS は、Blazor アプリ対応の静的ファイル サーバーです。 Blazor をホストするよう IIS を構成する方法については、「[Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)」 (IIS で静的 Web サイトを構築する) を参照してください。

発行されたアセットは、*\\bin\\Release\\&lt;ターゲットフレームワーク&gt;\\publish* フォルダーに作成されます。 *publish*フォルダーのコンテンツを、Web サーバーまたはホスティング サービス上でホストします。

**web.config**

Blazor プロジェクトが発行されると、*web.config* ファイルが以下の IIS 構成で作成されます。

* 各ファイル拡張子に対して設定される MIME の種類は次のとおりです。
  * *\*.dll*: `application/octet-stream`
  * *\*.json*: `application/json`
  * *\*.wasm*: `application/wasm`
  * *\*.woff*: `application/font-woff`
  * *\*.woff2*: `application/font-woff`
* 次の MIME の種類に対しては、HTTP 圧縮が有効にされます。
  * `application/octet-stream`
  * `application/wasm`
* URL Rewrite Module のルールが確立されます。
  * アプリの静的なアセットが存在するサブディレクトリ (*&lt;アセンブリ名&gt;\\dist\\&lt;要求されたパス&gt;*) が提供されます。
  * ファイル以外のアセットの要求が、アプリの静的アセット フォルダー内の既定のドキュメント (*&lt;アセンブリ名&gt;\\dist\\index.html*) にリダイレクトされるように、SPA フォールバック ルーティングが作成されます

**URL Rewrite Module をインストールする**

[URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) は、URL の書き換えに必要となります。 このモジュールは既定ではインストールされていません。また、Web サーバー (IIS) の役割サービス機能としてインストールすることはできません。 モジュールは、IIS Web サイトからダウンロードする必要があります。 Web Platform Installer を使用してモジュールをインストールします。

1. ローカルで、[URL Rewrite Module のダウンロード ページ](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)に移動します。 英語版については、**WebPI** を選択して WebPI インストーラーをダウンロードします。 その他の言語版については、サーバーの適切なアーキテクチャ (x86/x64) を選択して、インストーラーをダウンロードします。
1. インストーラーをサーバーにコピーします。 インストーラーを実行します。 **[インストール]** ボタンを選択して、ライセンス条項に同意します。 インストールが完了した後、サーバーの再起動は必要はありません。

**Web サイトを構成する**

Web サイトの**物理パス**をアプリのフォルダーに設定します。 フォルダーには次のものが含まれます。

* *web.config* ファイル。IIS ではこのファイルを使用して、必要なリダイレクト ルールやファイルのコンテンツの種類など、Web サイトの構成が行われます。
* アプリの静的なアセット フォルダー。

**トラブルシューティング**

Web サイトの構成にアクセスしようとしたときに、*500 内部サーバー エラー*という応答が返され、IIS マネージャーによりエラーがスローされた場合、URL Rewrite Module がインストールされていることを確認してください。 モジュールがインストールされていない場合、IIS では *web.config* ファイルを解析できません。 これは、IIS マネージャーによる Web サイトの構成の読み込み、そして Web サイトによる Blazor の静的ファイルの提供を阻止するためのものです。

IIS への展開に関するトラブルシューティングの詳細については、「<xref:host-and-deploy/iis/troubleshoot>」を参照してください。

#### <a name="client-side-blazor-standalone-hosting-with-nginx"></a>Nginx を使用したクライアント側 Blazor スタンドアロン ホスティング

以下に示す *nginx.conf* ファイルは、Nginx が対応するファイルをディスク上で見つけられないときは *Index.html* ファイルを送信するよう Nginx を構成する方法を示すために、簡素化したものです。

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /Index.html =404;
        }
    }
}
```

運用環境での Nginx Web サーバーの構成に関する詳細については、「[Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)」 (NGINX Plus と NGINX 構成ファイルの作成) を参照してください。

#### <a name="client-side-blazor-standalone-hosting-with-nginx-in-docker"></a>Docker で Nginx を使用したクライアント側 Blazor スタンドアロン ホスティング

Nginx を使用して Docker で Blazor をホストするには、Alpine ベースの Nginx イメージを使用するように Dockerfile をセットアップします。 *nginx.config* ファイルをコンテナーにコピーするように、Dockerfile を更新します。

次の例に示すように、1 つの行を Dockerfile に追加します。

```
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

#### <a name="client-side-blazor-standalone-hosting-with-github-pages"></a>GitHub ページを使用したクライアント側 Blazor スタンドアロン ホスティング

URL の書き換えを処理するために、*404.html* ファイルを、要求を *index.html* ページにリダイレクトするスクリプトと共に追加します。 コミュニティが提供する実装例については、「[Single Page Apps for GitHub Pages](http://spa-github-pages.rafrex.com/)」(GitHub ページ用の単一ページ アプリ) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)) を参照してください。 コミュニティのアプローチの使用例については、[GitHub 上の blazor-demo/blazor-demo.github.io に関するページ](https://github.com/blazor-demo/blazor-demo.github.io) ([ライブ サイト](https://blazor-demo.github.io/)) を参照してください。

組織のサイトではなくプロジェクトのサイトを使用している場合、*index.html*内の `<base>` タグを追加または更新します。 `href` 属性値として `<repository-name>/` を設定します。ここで、`<repository-name>/` は GitHub のリポジトリ名です。

## <a name="deploy-a-server-side-razor-components-app"></a>サーバー側 Razor Components アプリの展開

[サーバー側のホスティング モデル](xref:razor-components/hosting-models#server-side-hosting-model)では、Razor Components はサーバー上で ASP.NET Core アプリ内から実行されます。 UI の更新、イベント処理、JavaScript の呼び出しは、SignalR 接続経由で処理されます。

アプリは、発行された出力に ASP.NET Core アプリと共に含まれているため、2 つのアプリを一緒に展開することができます。 ASP.NET Core アプリをホストできる Web サーバーが必要です。 サーバー側の展開の場合、Visual Studio には **Blazor (ASP.NET Core 内でサーバー側)** プロジェクト テンプレートが含まれています ([dotnet new](/dotnet/core/tools/dotnet-new) コマンドを使用する場合は `blazorserver` テンプレート)。

<!--

**INSERT: Concerns are the same as publishing an ASP.NET Core SignalR app**

**INSERT: Content on the Azure SignalR Service**

**INSERT: Manually turn on WebSockets support**

-->

ASP.NET Core アプリが発行されると、ASP.NET Core アプリと Razor Components アプリを一緒に展開することができるように、発行された出力内に Razor コンポーネント アプリが含まれます。 ASP.NET Core アプリでのホストと展開の詳細については、「<xref:host-and-deploy/index>」を参照してください。

Azure App Service の展開については、次のトピックを参照してください。

<xref:tutorials/publish-to-azure-webapp-using-vs>  
Visual Studio を使用して Azure App Service に ASP.NET Core アプリを発行する方法を説明します。
