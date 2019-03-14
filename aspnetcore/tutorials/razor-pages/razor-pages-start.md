---
title: 'チュートリアル: ASP.NET Core の Razor ページの概要'
author: rick-anderson
description: このチュートリアル シリーズでは、ASP.NET Core で Razor ページを使用する方法を示します。 モデルの作成、Razor ページのコードの生成、Entity Framework Core と SQL Server を使用したデータ アクセス、検索機能の追加、入力検証の追加、および移行を使用したモデルの更新の方法について説明します。
ms.author: riande
ms.date: 12/5/2018
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 81a2a76fc1cecc78b69226fe714d7c9272b04bf7
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57046039"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a>チュートリアル: ASP.NET Core の Razor ページの概要

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

これは、シリーズの最初のチュートリアルです。 [このシリーズ](xref:tutorials/razor-pages/index)では、ASP.NET Core の Razor Pages Web アプリの構築の基礎について説明します。

[!INCLUDE[](~/includes/advancedRP.md)]

シリーズの最後には、映画のデータベースを管理できるアプリができあがります。  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

このチュートリアルでは、次の作業を行います。

> [!div class="checklist"]
> * Razor Pages Web アプリを作成する。
> * アプリを実行します。
> * プロジェクト ファイルを確認する

このチュートリアルの最後には、以降のチュートリアルでビルドする作業用 Razor ページ Web アプリができあがります。

![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-razor-pages-web-app"></a>Razor ページ Web アプリを作成する

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio の **[ファイル]** メニューから、**[新規作成]** > **[プロジェクト]** の順に選択します。

* 新しい ASP.NET Core Web アプリケーションを作成します。 プロジェクトに **RazorPagesMovie** という名前を付けます。 コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *RazorPagesMovie* という名前を付けることが重要です。

  ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2.1.png)

* ドロップダウン リストで **[ASP.NET Core 2.2]** を選択してから、**[Web アプリケーション]** を選択します。

  ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2_2.2.png)

  次のスターター プロジェクトが作成されます。

  ![ソリューション エクスプローラー](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。

* ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。

* 次のコマンドを実行します。

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * `dotnet new` コマンド: *RazorPagesMovie* フォルダーに新しい Razor Pages プロジェクトが作成されます。
  * `code` コマンド: Visual Studio Code の新しいインスタンス内で *RazorPagesMovie* フォルダーが開かれます。

  "**ビルドとデバッグに必要な資産が 'RazorPagesMovie' にありません。追加しますか?**" という内容のダイアログ ボックスが表示されたら、

* **[はい]** を選択します

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

端末から、次のコマンドを実行します。

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
cd RazorPagesMovie
```

上記のコマンドでは、[.NET Core CLI](/dotnet/core/tools/dotnet) を使用して、Razor ページ プロジェクトが作成されます。

## <a name="open-the-project"></a>プロジェクトを開く

Visual Studio から、**[ファイル]、[開く]** の順に選択し、*RazorPagesMovie.csproj* ファイルを選択します。

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>アプリを実行する

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ctrl + F5 キーを押して、デバッガーなしで実行します。

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。 アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。 これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。 localhost では、ローカル コンピューターからの Web 要求のみが処理されます。 Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* **Ctrl + F5** キーを押して、デバッガーなしで実行します。

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`http://localhost:5001` に移動します。 アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。 これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。 localhost では、ローカル コンピューターからの Web 要求のみが処理されます。
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

**[実行]、[デバッグなしで開始]** の順に選択してアプリを起動します。 Visual Studio は [Kestrel](xref:fundamentals/servers/kestrel) を開始し、ブラウザーを起動して、`http://localhost:5001` に移動します。

[!INCLUDE[](~/includes/trustCertMac.md)]

<!-- End of VS tabs -->

---

* アプリのホーム ページ上で、**[同意する]** を選択して、追跡に同意します。

  このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2.png)

  次の図では、追跡に同意した後のアプリを示しています。

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)

## <a name="examine-the-project-files"></a>プロジェクト ファイルを確認する

以降のチュートリアルで使用するメイン プロジェクト フォルダーとファイルの概要を次に示します。

### <a name="pages-folder"></a>Pages フォルダー

Razor ページとサポート ファイルが格納されます。 各 Razor ページは、次のファイルのペアとなります。

* *.cshtml* ファイル: HTML マークアップと、Razor 構文を使用した C# コードが保存されます。
* *.cshtml.cs* ファイル: ページ イベントを処理する C# コードが保存されます。

サポート ファイルには、アンダー スコアで始まる名前が付けられます。 たとえば、*_Layout.cshtml* ファイルでは、すべてのページに共通の UI 要素が構成されます。 このファイルでは、ページの上部に表示されるナビゲーション メニューと、ページの下部に表示される著作権の通知が設定されます。 詳細については、「 <xref:mvc/views/layout> 」を参照してください。


### <a name="wwwroot-folder"></a>wwwroot フォルダー

HTML ファイル、JavaScript ファイル、CSS ファイルなどの静的ファイルが格納されます。 詳細については、「 <xref:fundamentals/static-files> 」を参照してください。

### <a name="appsettingsjson"></a>appSettings.json

接続文字列などの構成データが保存されます。 詳細については、「 <xref:fundamentals/configuration/index> 」を参照してください。

### <a name="programcs"></a>Program.cs

プログラムのエントリ ポイントが保存されます。 詳細については、「 <xref:fundamentals/host/web-host> 」を参照してください。

### <a name="startupcs"></a>Startup.cs

cookie に対する同意が必要かどうかなど、アプリの動作を構成するコードが保存されます。 詳細については、「 <xref:fundamentals/startup> 」を参照してください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * Razor ページ Web アプリを作成する。
> * アプリを実行する。
> * プロジェクト ファイルを確認する。

このシリーズの次のチュートリアルに進んでください。

> [!div class="step-by-step"]
> [モデルの追加](xref:tutorials/razor-pages/model)
