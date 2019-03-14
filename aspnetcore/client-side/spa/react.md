---
title: ASP.NET Core で React プロジェクト テンプレートを使用する
author: SteveSandersonMS
description: React と create-react-app 用の ASP.NET Core シングル ページ アプリケーション (SPA) プロジェクト テンプレートの使用を開始する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/13/2019
uid: spa/react
ms.openlocfilehash: 3b2b2e67b5d577872bafefef5624a13ca1a22449
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57026849"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a>ASP.NET Core で React プロジェクト テンプレートを使用する

更新された React プロジェクト テンプレートは、React および [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) 規則を使用してリッチなクライアント側ユーザー インターフェイス (UI) を実装する ASP.NET Core アプリを開発する場合に、便利な開始点として利用できます。

このテンプレートには、API バックエンドとして機能する ASP.NET Core プロジェクトと、UI として機能する標準の CRA React プロジェクトの両方を作成する場合と同等の機能がありますが、単一のユニットとしてビルドと発行が可能な単一のアプリ プロジェクトで両方をホストできるので便利です。

## <a name="create-a-new-app"></a>新しいアプリを作成する

ASP.NET Core 2.1 がインストールされている場合は、React プロジェクト テンプレートをインストールする必要はありません。

コマンド プロンプトで `dotnet new react` コマンドを使用して、空のディレクトリの中に新しいプロジェクトを作成します。 たとえば、次のコマンドは、*my-new-app* ディレクトリにアプリを作成し、そのディレクトリに切り替えます。

```console
dotnet new react -o my-new-app
cd my-new-app
```

Visual Studio または .NET Core CLI からアプリを実行します。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

生成された *.csproj* ファイルを開き、そこから通常の方法でアプリを実行します。

ビルド プロセスは、初回の実行で npm の依存関係を復元します。これには数分かかる可能性があります。 以降のビルドは非常に高速になります。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

値が `Development` である `ASPNETCORE_Environment` という名前の環境変数があることを確認します。 Windows では (PowerShell ではないプロンプトで) `SET ASPNETCORE_Environment=Development` を実行します。 Linux または macOS では、`export ASPNETCORE_Environment=Development` を実行します。

[dotnet build](/dotnet/core/tools/dotnet-build) を実行して、アプリが正しくビルドされていることを確認します。 ビルド プロセスは、初回の実行で npm の依存関係を復元します。これには数分かかる可能性があります。 以降のビルドは非常に高速になります。

[dotnet run](/dotnet/core/tools/dotnet-run) を実行してアプリを起動します。

---

プロジェクト テンプレートは、ASP.NET Core アプリと React アプリを作成します。 ASP.NET Core アプリは、データ アクセス、承認、およびその他のサーバー側の操作で使用することを目的としています。 *ClientApp* サブディレクトリ内の React アプリは、UI に関するすべての 操作で使用することを目的としています。

## <a name="add-pages-images-styles-modules-etc"></a>ページ、画像、スタイル、モジュールなどを追加する

*ClientApp* ディレクトリは標準の CRA React アプリです。 詳細については、公式の [CRA ドキュメント](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md)を参照してください。

このテンプレートによって作成される React アプリと CRA 自体によって作成されるアプリにはわずかな違いがありますが、アプリの機能は変わりません。 テンプレートによって作成されるアプリには、[ブートストラップ](https://getbootstrap.com/)ベースのレイアウトと基本的なルーティングの例が含まれます。

## <a name="install-npm-packages"></a>npm パッケージをインストールする

サードパーティ製の npm パッケージをインストールするには、*ClientApp* サブディレクトリでコマンド プロンプトを使用します。 例:

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a>発行と配置

開発中、アプリは、開発者に便利なように最適化されたモードで実行されます。 たとえば、JavaScript バンドルには、ソース マップが含まれます (デバッグ時に元のソース コードを確認できるようにするためです)。 アプリは、ディスク上の JavaScript、HTML および CSS ファイルの変更を監視し、これらのファイルの変更が発生した場合は、再コンパイルと再読み込みを自動的に実行します。

運用時は、パフォーマンスが最適化されたバージョンのアプリが提供されます。 これは、自動的に実行されるように構成されています。 発行すると、ビルド構成によって、クライアント側コードの縮小され、トランスパイルされたビルドが生成されます。 開発ビルドとは異なり、運用ビルドは、サーバーへの Node.js のインストールを必要としません。

標準的な [ASP.NET Core のホストと展開方法](xref:host-and-deploy/index)を使用できます。

## <a name="run-the-cra-server-independently"></a>CRA サーバーを個別に実行する

ASP.NET Core アプリが開発モードで起動された場合、プロジェクトは、CRA 開発サーバーの独自のインスタンスをバックグラウンドで開始するように構成されます。 これが便利なのは、別のサーバーを手動で実行する必要がないためです。

この既定の設定には欠点があります。 C# コードを変更し、ASP.NET Core アプリを再起動する必要がある場合、CRA サーバーが毎回再起動します。 再起動には数秒かかります。 C# コードを何度も編集するが、CRA サーバーが再起動するまで待ちたくない場合は、ASP.NET Core プロセスから独立した CRA サーバーを外部で実行します。 次の手順に従います。

1. 追加、 *.env*ファイルを*ClientApp*次の設定を持つサブディレクトリ。

    ```
    BROWSER=none
    ```
    
    これにより、web ブラウザーを開く外部から CRA サーバーを開始するときにします。

2. コマンド プロンプトで、*ClientApp* サブディレクトリに切り替え、CRA 開発サーバーを起動します。

    ```console
    cd ClientApp
    npm start
    ```

3. 独自のインスタンスを起動する代わりに外部の CRA サーバー インスタンスを使用するように ASP.NET Core アプリケーションを変更します。 *Startup* クラスで、`spa.UseReactDevelopmentServer` の呼び出しを以下に置き換えます。

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

ASP.NET Core アプリの起動時に CRA サーバーが起動されなくなります。 代わりに、手動で開始したインスタンスが使用されます。 これにより、起動と再起動を高速化できます。 React アプリが毎回リビルドされるまで待つ必要がなくなります。

> [!IMPORTANT]
> 「サーバー側レンダリング」は、このテンプレートのサポートされている機能ではありません。 このテンプレートでの目標は、「- react のアプリの作成」でパリティを満たすためにです。 そのため、シナリオと (SSR) などの「- react のアプリの作成」プロジェクトに含まれていない機能はサポートされていません、、ユーザーの練習のままです。
