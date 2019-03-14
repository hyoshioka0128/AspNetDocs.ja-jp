---
title: ASP.NET Core の概要
author: rick-anderson
description: ASP.NET Core を使用して単純な Hello World アプリを作成し、実行する簡単なチュートリアルです。
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2019
uid: getting-started
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="162fa-103">チュートリアル: ASP.NET Core の概要</span><span class="sxs-lookup"><span data-stu-id="162fa-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="162fa-104">このチュートリアルでは、.NET Core コマンド ライン インターフェイスを使用して ASP.NET Core Web アプリを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="162fa-104">This tutorial shows how to use the .NET Core command-line interface to create an ASP.NET Core web app.</span></span>

<span data-ttu-id="162fa-105">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="162fa-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="162fa-106">Web アプリ プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="162fa-106">Create a web app project.</span></span>
> * <span data-ttu-id="162fa-107">ローカル HTTPS を有効にする。</span><span class="sxs-lookup"><span data-stu-id="162fa-107">Enable local HTTPS.</span></span>
> * <span data-ttu-id="162fa-108">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="162fa-108">Run the app.</span></span>
> * <span data-ttu-id="162fa-109">Razor ページを編集する。</span><span class="sxs-lookup"><span data-stu-id="162fa-109">Edit a Razor page.</span></span>

<span data-ttu-id="162fa-110">最後に、作業用の Web アプリがご利用のローカル コンピューター上で実行されるようにします。</span><span class="sxs-lookup"><span data-stu-id="162fa-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Web アプリのホーム ページ](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="162fa-112">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="162fa-112">Prerequisites</span></span>

* [<span data-ttu-id="162fa-113">.NET Core 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="162fa-113">.NET Core 2.2 SDK</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web-app-project"></a><span data-ttu-id="162fa-114">Web アプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="162fa-114">Create a web app project</span></span>

<span data-ttu-id="162fa-115">コマンド シェルを開き、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="162fa-115">Open a command shell, and enter the following command:</span></span>

```console
dotnet new webapp -o aspnetcoreapp
```

## <a name="enable-local-https"></a><span data-ttu-id="162fa-116">ローカル HTTPS を有効にする</span><span class="sxs-lookup"><span data-stu-id="162fa-116">Enable local HTTPS</span></span>

<span data-ttu-id="162fa-117">HTTPS 開発証明書を信頼します。</span><span class="sxs-lookup"><span data-stu-id="162fa-117">Trust the HTTPS development certificate:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="162fa-118">Windows</span><span class="sxs-lookup"><span data-stu-id="162fa-118">Windows</span></span>](#tab/windows)

```console
dotnet dev-certs https --trust
```

<span data-ttu-id="162fa-119">上記のコマンドでは、次のダイアログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="162fa-119">The preceding command displays the following dialog:</span></span>

![セキュリティ警告のダイアログ](~/getting-started/_static/cert.png)

<span data-ttu-id="162fa-121">開発証明書を信頼することに同意する場合は、**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="162fa-121">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="162fa-122">macOS</span><span class="sxs-lookup"><span data-stu-id="162fa-122">macOS</span></span>](#tab/macos)

```console
dotnet dev-certs https --trust
```

<span data-ttu-id="162fa-123">上記のコマンドでは、次のメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="162fa-123">The preceding command displays the following message:</span></span>

<span data-ttu-id="162fa-124">*HTTPS 開発証明書の信頼が要求されました。証明書がまだ信頼されていない場合は、次のコマンドを実行します。* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`。</span><span class="sxs-lookup"><span data-stu-id="162fa-124">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`.</span></span>
 
<span data-ttu-id="162fa-125">このコマンド コマンドでは、システム キーチェーン上に証明書をインストールするためにご自分のパスワードを入力するよう求められる場合があります。</span><span class="sxs-lookup"><span data-stu-id="162fa-125">This command command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="162fa-126">開発証明書を信頼することに同意する場合は、パスワードを入力します。</span><span class="sxs-lookup"><span data-stu-id="162fa-126">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="162fa-127">Linux</span><span class="sxs-lookup"><span data-stu-id="162fa-127">Linux</span></span>](#tab/linux)

<span data-ttu-id="162fa-128">HTTPS 開発証明書を信頼する方法については、Linux ディストリビューションのドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="162fa-128">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="162fa-129">詳細については、[ASP.NET Core HTTPS 開発証明書の信頼](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)に関する記事をご覧ください</span><span class="sxs-lookup"><span data-stu-id="162fa-129">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="162fa-130">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="162fa-130">Run the app</span></span>

<span data-ttu-id="162fa-131">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="162fa-131">Run the following commands:</span></span>

```console
cd aspnetcoreapp
dotnet run
```

<span data-ttu-id="162fa-132">コマンド シェルでアプリが開始したことが示されたら、[https://localhost:5001](https://localhost:5001) を参照します。</span><span class="sxs-lookup"><span data-stu-id="162fa-132">After the command shell indicates that the app has started, browse to [https://localhost:5001](https://localhost:5001).</span></span> <span data-ttu-id="162fa-133">**[Accept]\(承認\)** をクリックして、プライバシーとクッキーのポリシーを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="162fa-133">Click **Accept** to accept the privacy and cookie policy.</span></span> <span data-ttu-id="162fa-134">このアプリで個人情報が保持されることはありません。</span><span class="sxs-lookup"><span data-stu-id="162fa-134">This app doesn't keep personal information.</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="162fa-135">Razor ページを編集する</span><span class="sxs-lookup"><span data-stu-id="162fa-135">Edit a Razor page</span></span>

<span data-ttu-id="162fa-136">*Pages/Index.cshtml* を開き、次の強調表示されたマークアップでページを変更します。</span><span class="sxs-lookup"><span data-stu-id="162fa-136">Open *Pages/Index.cshtml* and modify the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="162fa-137">[https://localhost:5001](https://localhost:5001) を参照して、変更が表示されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="162fa-137">Browse to [https://localhost:5001](https://localhost:5001), and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="162fa-138">次の手順</span><span class="sxs-lookup"><span data-stu-id="162fa-138">Next steps</span></span>

<span data-ttu-id="162fa-139">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="162fa-139">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="162fa-140">Web アプリ プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="162fa-140">Create a web app project.</span></span>
> * <span data-ttu-id="162fa-141">ローカル HTTPS を有効にする。</span><span class="sxs-lookup"><span data-stu-id="162fa-141">Enable local HTTPS.</span></span>
> * <span data-ttu-id="162fa-142">プロジェクトを実行します。</span><span class="sxs-lookup"><span data-stu-id="162fa-142">Run the project.</span></span>
> * <span data-ttu-id="162fa-143">変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="162fa-143">Make a change.</span></span>

<span data-ttu-id="162fa-144">ASP.NET Core の詳細については、次の概要の推奨のラーニング パスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="162fa-144">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
