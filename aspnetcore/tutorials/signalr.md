---
title: ASP.NET Core SignalR の概要
author: bradygaster
description: このチュートリアルでは、ASP.NET Core SignalR を使用するチャット アプリを作成します。
ms.author: bradyg
ms.custom: mvc
ms.date: 11/30/2018
uid: tutorials/signalr
ms.openlocfilehash: 53ec924c2d7b4fac227be0c0bf24d93476528167
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57029029"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a>チュートリアル: ASP.NET Core SignalR の概要

このチュートリアルでは、SignalR を使用してリアルタイム アプリをビルドするための基礎について説明します。 以下の方法について説明します。

> [!div class="checklist"]
> * Web プロジェクトを作成します。
> * SignalR クライアント ライブラリを追加する。
> * SignalR ハブを作成する。
> * SignalR を使用するようにプロジェクトを構成する。
> * 任意のクライアントから、接続されているすべてのクライアントにメッセージを送信するコードを追加します。

最後には、次のように動作するチャット アプリが作成されます。

![SignalR のサンプル アプリ](signalr/_static/signalr-get-started-finished.png)

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/signalr/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

[!INCLUDE [|Prerequisites](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-web-project"></a>Web プロジェクトを作成する

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio/)

* メニューから、**[ファイル]、[新規プロジェクト]** の順に選択します。

* **[新しいプロジェクト]** ダイアログで、**[インストール]、[Visual C#]、[Web]、[ASP.NET Core Web アプリケーション]** の順に選択します。 プロジェクトに "*SignalRChat*" という名前を付けます。

  ![Visual Studio の [新しいプロジェクト] ダイアログ ボックス](signalr/_static/signalr-new-project-dialog.png)

* **[Web アプリケーション]** を選択して、Razor Pages を使用するプロジェクトを作成します。

* **.NET Core** のターゲット フレームワークを選択し、**ASP.NET Core 2.2** を選択して、**[OK]** をクリックします。

  ![Visual Studio の [新しいプロジェクト] ダイアログ ボックス](signalr/_static/signalr-new-project-choose-type.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* 新しいプロジェクト フォルダーを作成するフォルダーで[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。

* 次のコマンドを実行します。

   ```console
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* メニューから、**[ファイル]、[新しいソリューション]** の順に選択します。

* **[.NET Core]、[アプリ]、[ASP.NET Core Web アプリ]** の順に選択します (**[ASP.NET Core Web アプリ (MVC)]** を選択しないでください)。

* **[次へ]** を選択します。

* プロジェクトに *SignalRChat* という名前を付けて、**[作成]** を選択します。

---

## <a name="add-the-signalr-client-library"></a>SignalR クライアント ライブラリを追加する

SignalR サーバー ライブラリは、`Microsoft.AspNetCore.App` メタパッケージに含まれています。 JavaScript クライアント ライブラリはプロジェクトに自動的に含まれません。 このチュートリアルでは、ライブラリ マネージャー (LibMan) を使用して *unpkg* からクライアント ライブラリを取得します。 unpkg は、npm (Node.js パッケージ マネージャー) で見つかるものすべてを配信できるコンテンツ配信ネットワーク (CDN) です。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio/)

* **ソリューション エクスプローラー**で、プロジェクトを右クリックし、**[追加]** > **[クライアント側のライブラリ]** を選択します。

* **[Add Client-Side Library]\(クライアント側のライブラリの追加\)** ダイアログで、**[プロバイダー]** に対して **[unpkg]** を選択します。 

* **[ライブラリ]** に対して「`@aspnet/signalr@1`」を入力し、プレビューでない最新のバージョンを選択します。

  ![クライアント側のライブラリの追加ダイアログ - ライブラリの選択](signalr/_static/libman1.png)

* **[Choose specific files]\(特定のファイルの選択\)** を選択して *dist/browser* フォルダーを展開し、*signalr.js* と *signalr.min.js* を選択します。

* **[ターゲットの場所]** を *wwwroot/lib/signalr/* に設定して、**[インストール]** を選択します。

  ![クライアント側のライブラリの追加ダイアログ - ファイルと保存先の選択](signalr/_static/libman2.png)

  LibMan によって *wwwroot/lib/signalr* フォルダーが作成され、そこに選択したファイルがコピーされます。

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* 統合端末で、次のコマンドを実行して LibMan をインストールします。

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* 次のコマンドを実行して、LibMan を使用して SignalR クライアント ライブラリを取得します。 出力が表示されるまでに数秒待機する必要がある場合があります。

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  パラメーターによって次のオプションが指定されます。
  * unpkg プロバイダーを使用する。
  * ファイルを *wwwroot/lib/signalr* にコピーする。
  * 指定したファイルのみをコピーする。

  出力は次の例のようになります。

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* **[端末]** で、次のコマンドを実行して LibMan をインストールします。

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* プロジェクト フォルダー (ファイル *SignalRChat.csproj* を含んでいるフォルダー) に移動します。

* 次のコマンドを実行して、LibMan を使用して SignalR クライアント ライブラリを取得します。

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  パラメーターによって次のオプションが指定されます。
  * unpkg プロバイダーを使用する。
  * ファイルを *wwwroot/lib/signalr* にコピーする。
  * 指定したファイルのみをコピーする。

  出力は次の例のようになります。

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a>SignalR ハブを作成する

*ハブ*はクライアント サーバー通信を処理するハイレベル パイプラインとして機能するクラスです。

* SignalRChat プロジェクト フォルダー内で、*Hubs* フォルダーを作成します。

* *Hubs* フォルダー内で、次のコードを使用して *ChatHub.cs* ファイルを作成します。

  [!code-csharp[Startup](signalr/sample/Hubs/ChatHub.cs)]

  `ChatHub` クラスは、SignalR `Hub` クラスを継承します。 `Hub` クラスでは、接続、グループ、およびメッセージングが管理されます。

  `SendMessage` メソッドは、メッセージをすべてのクライアントに送信するために、接続されたクライアントによって呼び出される場合があります。 このメソッドを呼び出す JavaScript クライアント コードは、チュートリアルの後半で示されます。 最大のスケーラビリティを実現するために、SignalR コードは非同期になっています。

## <a name="configure-signalr"></a>SignalR を構成する

SignalR 要求が SignalR に渡されるように SignalR サーバーを構成する必要があります。

* 次の強調表示されたコードを *Startup.cs* ファイルに追加します。

  [!code-csharp[Startup](signalr/sample/Startup.cs?highlight=7,33,52-55)]

  これらの変更によって、SignalR が ASP.NET Core 依存関係挿入システムとミドルウェア パイプラインに追加されます。

## <a name="add-signalr-client-code"></a>SignalR クライアント コードを追加する

* *Pages\Index.cshtml* のコンテンツを次のコードに変更します。

  [!code-cshtml[Index](signalr/sample/Pages/Index.cshtml)]

  上のコードでは以下の操作が行われます。

  * 名前およびメッセージ テキスト用のテキスト ボックスと送信ボタンを作成します。
  * SignalR ハブから受信したメッセージを表示するために、`id="messagesList"` を使用してリストを作成します。
  * SignalR へのスクリプト参照と、次の手順で作成する *chat.js* アプリケーション コードを含めます。

* *wwwroot/js* フォルダー内に、次のコードを使用して *chat.js* ファイルを作成します。

  [!code-javascript[Index](signalr/sample/wwwroot/js/chat.js)]

  上のコードでは以下の操作が行われます。

  * 接続を作成して開始します。
  * ハブにメッセージを送信するハンドラーを送信ボタンに追加します。
  * ハブからメッセージを受信してからそれをリストに追加するハンドラーを接続オブジェクトに追加します。

## <a name="run-the-app"></a>アプリを実行する

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Ctrl + F5** キーを押して、デバッグを行わずにアプリを実行します。

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 統合端末で、次のコマンドを実行します。

  ```console
  dotnet run -p SignalRChat.csproj
  ```
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* メニューから、**[実行]、[デバッグなしで開始]** の順に選択します。

---

* アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。

* いずれかのブラウザーを選択し、名前とメッセージを入力し、**[Send Message]\(メッセージの送信\)** ボタンを選択します。

  次の瞬間、両方のページに名前とメッセージが表示されます。

  ![SignalR のサンプル アプリ](signalr/_static/signalr-get-started-finished.png)

> [!TIP]
> アプリが動作しない場合は、ご利用のブラウザーの開発者ツール (F12) を開き、コンソールに移動します。 HTML および JavaScript コードに関連するエラーが発生している場合があります。 たとえば、*signalr.js* を指示されたフォルダーとは別のフォルダーに配置したとします。 その場合、そのファイルへの参照は機能せず、コンソールに 404 エラーが表示されます。
> ![エラー "signalr.js が見つかりませんでした"](signalr/_static/f12-console.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の作業を行う方法を学びました。

> [!div class="checklist"]
> * Web アプリ プロジェクトを作成する。
> * SignalR クライアント ライブラリを追加する。
> * SignalR ハブを作成する。
> * SignalR を使用するようにプロジェクトを構成する。
> * ハブを使用して任意のクライアントから接続済みのすべてのクライアントにメッセージを送信する、コードを追加する。

SignalR の詳細については、次の概要を参照してください。

> [!div class="nextstepaction"]
> [ASP.NET Core SignalR の概要](xref:signalr/introduction)
