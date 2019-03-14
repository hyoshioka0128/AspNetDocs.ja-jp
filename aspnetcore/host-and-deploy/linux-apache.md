---
title: Apache 搭載の Linux で ASP.NET Core をホストする
description: CentOS 上にリバース プロキシ サーバーとして Apache をセットアップし、Kestrel 上で実行されている ASP.NET Core Web アプリに HTTP トラフィックを転送する方法について説明します。
author: spboyer
ms.author: spboyer
ms.custom: mvc
ms.date: 02/13/2019
uid: host-and-deploy/linux-apache
ms.openlocfilehash: 0dec9c657134bba3224a1fbb69aaefaaba753404
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57026489"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a>Apache 搭載の Linux で ASP.NET Core をホストする

作成者: [Shayne Boyer](https://github.com/spboyer)

このガイドでは、[CentOS 7](https://www.centos.org/) 上にリバース プロキシ サーバーとして [Apache](https://httpd.apache.org/) をセットアップし、[Kestrel](xref:fundamentals/servers/kestrel) サーバー上で実行されている ASP.NET Core Web アプリに HTTP トラフィックをリダイレクトする方法について説明します。 [mod_proxy 拡張機能](http://httpd.apache.org/docs/2.4/mod/mod_proxy.html)および関連するモジュールは、サーバーのリバース プロキシを作成します。

## <a name="prerequisites"></a>必須コンポーネント

* CentOS 7 を実行しているサーバーと、sudo 特権を持つ標準ユーザー アカウント。
* サーバーへの .NET Core ランタイムのインストール。
   1. [.NET の「All Downloads」 (すべてのダウンロード) ページ](https://www.microsoft.com/net/download/all)に移動します。
   1. プレビューではない最新のランタイムを、**Runtime** (ランタイム) の一覧から選択します。
   1. CentOS/Oracle の手順を選択し、それに従います。
* 既存の ASP.NET Core アプリ。

## <a name="publish-and-copy-over-the-app"></a>アプリを介して発行およびコピーする

[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)用にアプリを構成します。

開発環境から [dotnet publish](/dotnet/core/tools/dotnet-publish) を実行し、サーバー上で実行できるディレクトリ (たとえば、*bin/Release/&lt;target_framework_moniker&gt;/publish*) にアプリをパッケージします。

```console
dotnet publish --configuration Release
```

サーバーで .NET Core ランタイムを管理しない場合、アプリは[独立した展開](/dotnet/core/deploying/#self-contained-deployments-scd)として発行することもできます。

組織のワークフローに統合されているツール (SCP や SFTP など) を使用して、サーバーに ASP.NET Core アプリをコピーします。 Web アプリは一般的に *var* ディレクトリの下に配置されます (たとえば、*var/www/helloapp*)。

> [!NOTE]
> 運用展開シナリオの場合、継続的インテグレーション ワークフローが、アプリの発行処理とサーバーへの資産のコピーを行います。

## <a name="configure-a-proxy-server"></a>プロキシ サーバーを構成する

リバース プロキシは、動的 Web アプリを提供するための一般的な仕組みです。 リバース プロキシは HTTP 要求を終了させ、ASP.NET アプリに転送します。

プロキシ サーバーは、クライアント要求を処理せずに他のサーバーに転送するサーバーです。 リバース プロキシは、一般的に任意のクライアントに代わって固定の送信先に転送します。 このガイドでは、Kestrel が ASP.NET Core アプリを提供しているものと同じサーバー上で実行されるリバース プロキシとして Apache を構成します。

要求はリバース プロキシによって転送されます。そのため、[Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) パッケージの [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) を使用します。 リダイレクト URI とその他のセキュリティ ポリシーを正しく機能させるために、このミドルウェアは、`X-Forwarded-Proto` ヘッダーを利用して、`Request.Scheme` を更新します。

認証、リンクの生成、リダイレクト、および地理的位置情報など、スキームに依存するすべてのコンポーネントは、Forwarded Headers Middleware の呼び出し後に配置する必要があります。 一般的な規則として、Forwarded Headers Middleware は、診断およびエラー処理ミドルウェアを除くその他のミドルウェアより前に実行される必要があります。 この順序により、転送されるヘッダー情報に依存するミドルウェアが処理にヘッダー値を使用できます。

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> や同様の認証スキーム ミドルウェアを呼び出す前に、`Startup.Configure` の <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> メソッドを呼び出します。 ミドルウェアを構成して、`X-Forwarded-For` および `X-Forwarded-Proto` ヘッダーを転送します。

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Builder.BuilderExtensions.UseIdentity*> や <xref:Microsoft.AspNetCore.Builder.FacebookAppBuilderExtensions.UseFacebookAuthentication*>、または同様の認証スキーム ミドルウェアを呼び出す前に、`Startup.Configure` の <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> メソッドを呼び出します。 ミドルウェアを構成して、`X-Forwarded-For` および `X-Forwarded-Proto` ヘッダーを転送します。

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseIdentity();
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

::: moniker-end

ミドルウェアに対して <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> が指定されていない場合、転送される既定のヘッダーは `None` です。

localhost (127.0.0.1, [::1]) で実行されるプロキシのみが既定で信頼されます。 組織内のその他の信頼されているプロキシまたはネットワークによってインターネットと Web サーバーの間の要求が処理される場合は、それらを、<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> を使用して <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> または <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> のリストに追加します。 次の例では、IP アドレス 10.0.0.100 にある信頼されているプロキシ サーバーが `Startup.ConfigureServices` 内の Forwarded Headers Middleware `KnownProxies` に追加されます。

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

詳細については、「 <xref:host-and-deploy/proxy-load-balancer> 」を参照してください。

### <a name="install-apache"></a>Apache をインストールする

CentOS パッケージを最新の安定したバージョンに更新します。

```bash
sudo yum update -y
```

1 つの `yum` コマンドで、CentOS に Apache Web サーバーをインストールします。

```bash
sudo yum -y install httpd mod_ssl
```

コマンド実行後の出力例:

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> この例では、CentOS 7 のバージョンが 64 ビットなので、出力は httpd.86_64 を反映しています。 Apache がインストールされている場所を確認するには、コマンド プロンプトから `whereis httpd` を実行します。

### <a name="configure-apache"></a>Apache を構成する

Apache の構成ファイルは、`/etc/httpd/conf.d/` ディレクトリ内にあります。 `/etc/httpd/conf.modules.d/` 内のモジュール構成ファイルに加え、拡張子が *.conf* のファイルがアルファベット順で処理されます。このディレクトリには、モジュールの読み込みに必要な構成ファイルが含まれています。

アプリ用に *helloapp.conf* という名前の構成ファイルを作成します。

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
    ErrorLog ${APACHE_LOG_DIR}helloapp-error.log
    CustomLog ${APACHE_LOG_DIR}helloapp-access.log common
</VirtualHost>
```

`VirtualHost` ブロックは、サーバー上の 1 つまたは複数のファイルに複数回出現することができます。 上記の構成ファイルでは、Apache はポート 80 でパブリック トラフィックを受け入れます。 ドメイン `www.example.com` が提供されており、別名 `*.example.com` は同じ Web サイトに解決されます。 詳しくは、「[名前ベースのバーチャル ホスト](https://httpd.apache.org/docs/current/vhosts/name-based.html)」をご覧ください。 要求は、ルートにおいて、127.0.0.1 にあるサーバーのポート 5000 にプロキシされます。 双方向通信の場合は、`ProxyPass` と `ProxyPassReverse` が必要です。 Kestrel の IP/ポートを変更するには、[Kestrel のエンドポイントの構成](xref:fundamentals/servers/kestrel#endpoint-configuration)に関するセクションを参照してください。

> [!WARNING]
> **VirtualHost** ブロックで適切な [ServerName ディレクティブ](https://httpd.apache.org/docs/current/mod/core.html#servername)を指定しないと、アプリにセキュリティ上の脆弱性が生じます。 親ドメイン全体を制御する場合、サブドメイン ワイルドカード バインド (たとえば、`*.example.com`) にこのセキュリティ リスクはありません (脆弱である `*.com` とは対照的)。 詳細については、[rfc7230 セクション-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) を参照してください。

`ErrorLog` および `CustomLog` ディレクティブを使って、`VirtualHost` ごとにログを構成できます。 `ErrorLog` は、サーバーがエラーをログに記録する場所です。`CustomLog` には、ログ ファイルのファイル名と形式を設定します。 この例では、要求の情報がログに記録される場所です。 1 つの要求につき 1 行が記録されます。

ファイルを保存し、構成をテストします。 すべてに合格すると、応答は `Syntax [OK]` になります。

```bash
sudo service httpd configtest
```

Apache を再起動します。

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a>アプリを監視する

これで、`http://localhost:80` に対して行われた要求を Kestrel で実行されている ASP.NET Core アプリ (`http://127.0.0.1:5000`) に転送するように Apache が設定されました。 ただし、Apache は Kestrel プロセスを管理するようには設定されていません。 *systemd* を使って、基礎 Web アプリを起動して監視するサービス ファイルを作成します。 *systemd* は init システムであり、プロセスを起動、停止、管理するためのさまざまな高性能機能を提供します。

### <a name="create-the-service-file"></a>サービス ファイルを作成する

次のように、サービス定義ファイルを作成します。

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

アプリのサービス ファイルの例を次に示します。

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

ユーザー *apache* が構成で使用されない場合、ユーザーを先に作成し、そのユーザーにファイルの適切な所有権を与える必要があります。

アプリが最初の割り込み信号を受信してからシャットダウンするのを待機する期間を構成するには、`TimeoutStopSec` を使用します。 この期間内にアプリがシャットダウンしない場合は、SIGKILL を発行してアプリを終了します。 タイムアウトを無効にするには、値として、単位なしの秒数 (`150` など)、期間の値 (`2min 30s` など)、または `infinity` を指定します。 `TimeoutStopSec` は、既定ではマネージャー構成ファイル (*systemd-system.conf*、*system.conf.d*、*systemd-user.conf*、*user.conf.d*) 内の `DefaultTimeoutStopSec` の値に設定されます。 ほとんどのディストリビューションにおいて、タイムアウトの既定値は 90 秒となります。

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

構成プロバイダーが環境変数を読み取れるようにするために、一部の値 (たとえば SQL の接続文字列) をエスケープする必要があります。 次のコマンドを使用して、構成ファイルで使用するために適切にエスケープされた値を生成します。

```console
systemd-escape "<value-to-escape>"
```

ファイルを保存し、サービスを有効にします。

```bash
sudo systemctl enable kestrel-helloapp.service
```

サービスを起動し、動作を確認します。

```bash
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

● kestrel-helloapp.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

リバース プロキシが構成され、Kestrel は *systemd* 経由で管理されます。これで Web アプリは完全に構成され、`http://localhost` でローカル コンピューター上のブラウザーからアクセスできます。 応答ヘッダーを調べると、**Server** ヘッダーでは ASP.NET Core アプリが Kestrel によって提供されていることが示されています。

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>ログを表示する

Kestrel を使う Web アプリは *systemd* を使って管理されるため、イベントとプロセスは中央のジャーナルに記録されます。 ただし、このジャーナルには、*systemd* によって管理されるすべてのサービスとプロセスのエントリが含まれます。 `kestrel-helloapp.service` 固有の項目を表示するには、次のコマンドを使用します。

```bash
sudo journalctl -fu kestrel-helloapp.service
```

時間フィルタリングの場合は、コマンドで時間のオプションを指定します。 たとえば、現在の日付でフィルター処理するには `--since today` を使い、前の 1 時間のエントリを参照するには `--until 1 hour ago` を使います。 詳しくは、[journalctl の man ページ](https://www.unix.com/man-page/centos/1/journalctl/)をご覧ください。

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a>データの保護

[ASP.NET Core データ保護スタック](xref:security/data-protection/introduction)は、認証ミドルウェア (Cookie ミドルウェアなど) やクロスサイト リクエスト フォージェリ (CSRF) 保護を含む、いくつかの ASP.NET Core [ ミドルウェア](xref:fundamentals/middleware/index)で使用されます。 データ保護 API がユーザーのコードから呼び出されない場合でも、永続的な暗号化[キー ストア](xref:security/data-protection/implementation/key-management)を作成するようにデータ保護を構成する必要があります。 データ保護を構成しない場合、既定でキーはメモリ内に保持され、アプリが再起動すると破棄されます。

キーリングがメモリに格納されている場合、アプリを再起動すると次のことが行われます。

* すべての cookie ベースの認証トークンは無効になります。
* ユーザーは、次回の要求時に再度サインインする必要があります。
* キーリングで保護されているデータは、いずれも復号化できなくなります。 これには、[CSRF トークン](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)と [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata) が含まれます。

キー リングを永続化して暗号化するようにデータ保護を構成する場合は、次を参照してください。

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a>アプリをセキュリティで保護する

### <a name="configure-firewall"></a>ファイアウォールを構成する

*Firewalld* は、ネットワーク ゾーンをサポートするファイアウォールを管理するための動的デーモンです。 ポートとパケットのフィルター処理は、iptables によって引き続き管理できます。 *Firewalld* は既定でインストールされます。 `yum` を使ってパッケージをインストールしたり、インストールされていることを確認したりできます。

```bash
sudo yum install firewalld -y
```

アプリに必要なポートのみを開くには、`firewalld` を使います。 この場合、ポート 80 と 443 が使用されています。 次のコマンドは、ポート 80 と 443 が永続的に開かれるように設定します。

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

ファイアウォールの設定を再度読み込みます。 既定のゾーンで使用できるサービスとポートを確認します。 オプションは `firewall-cmd -h` を調べることで使用できます。

```bash
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="https-configuration"></a>HTTPS の構成

HTTPS 用に Apache を構成するには、*mod_ssl* モジュールを使います。 *httpd* モジュールがインストールされていると、*mod_ssl* モジュールもインストールされています。 インストールされていない場合は、`yum` を使って構成に追加します。

```bash
sudo yum install mod_ssl
```

HTTPS を強制するには、`mod_rewrite` モジュールをインストールして URL の書き換えを有効にします。

```bash
sudo yum install mod_rewrite
```

*helloapp.conf* ファイルを変更して、URL の書き換えを有効にし、ポート 443 での通信をセキュリティで保護します。

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> この例では、ローカルで生成された証明書を使います。 **SSLCertificateFile** は、ドメイン名のプライマリ証明書ファイルです。 **SSLCertificateKeyFile** は、CSR の作成時に生成されるキー ファイルです。 **SSLCertificateChainFile** は、証明機関から提供された中間証明書ファイル (存在する場合) です。

ファイルを保存し、構成をテストします。

```bash
sudo service httpd configtest
```

Apache を再起動します。

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a>Apache に関するその他の推奨事項

### <a name="additional-headers"></a>その他のヘッダー

悪意のある攻撃から防御するために、変更または追加する必要があるヘッダーがいくつかあります。 必ず `mod_headers` モジュールをインストールします。

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a>Apache をクリックジャッキング攻撃から保護する

[クリックジャッキング](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger)は "*UI 着せ替え攻撃*" とも呼ばれ、Web サイトの訪問者を騙して現在訪れているものとは異なるページのリンクやボタンをクリックさせる悪意のある攻撃です。 サイトをセキュリティで保護するには、`X-FRAME-OPTIONS` を使います。

クリックジャッキング攻撃を軽減するには、次の手順に従います。

1. *httpd.conf* ファイルを編集します。

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   行 `Header append X-FRAME-OPTIONS "SAMEORIGIN"` を追加します。
1. ファイルを保存します。
1. Apache を再起動します。

#### <a name="mime-type-sniffing"></a>MIME タイプ スニッフィング

`X-Content-Type-Options` ヘッダーは、Internet Explorer を "*MIME スニッフィング*" から防ぎます (ファイルの内容からファイルの `Content-Type` を判断します)。 サーバーが `nosniff` オプションを指定して `Content-Type` ヘッダーを `text/html` に設定すると、Internet Explorer はファイルの内容に関係なく `text/html` として内容をレンダリングします。

*httpd.conf* ファイルを編集します。

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

行 `Header set X-Content-Type-Options "nosniff"` を追加します。 ファイルを保存します。 Apache を再起動します。

### <a name="load-balancing"></a>負荷分散

この例では、同じインスタンス コンピューターに CentOS 7 と Kestrel をインストールし、Apache をセットアップおよび構成する方法を示します。 単一障害点を持たないように、*mod_proxy_balancer* を使い、**VirtualHost** を変更することで、Apache プロキシ サーバーの背後で複数インスタンスの Web アプを管理できるようにします。

```bash
sudo yum install mod_proxy_balancer
```

次に示す構成ファイルでは、ポート 5001 上で実行するように `helloapp` の追加インスタンスを設定しています。 *Proxy* セクションは、2 メンバーのバランサー構成を使って *byrequests* を負荷分散するように設定されています。

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a>速度の制限

*httpd* モジュールに含まれる *mod_ratelimit* を使って、クライアントの帯域幅を制限できます。

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

次のファイルの例では、ルートの場所の下での帯域幅を 600 KB/秒に制限しています。

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a>要求ヘッダー フィールドが長すぎます

アプリにプロキシ サーバーの既定の設定によって許可されている (通常、8,190 バイト) よりも長い要求ヘッダー フィールドが必要な場合、[LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) ディレクティブの値を調整します。 適用する値は、シナリオによって異なります。 詳細については、ご利用のサーバーのドキュメントを参照してください。

> [!WARNING]
> 必要な場合を除いて、`LimitRequestFieldSize` の既定値を増やさないでください。 値を増やすと、悪意のあるユーザーによるバッファー オーバーラン (オーバーフロー) とサービス拒否 (DoS) の攻撃のリスクが増加します。

## <a name="additional-resources"></a>その他の技術情報

* [Linux における .NET Core の前提条件](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
