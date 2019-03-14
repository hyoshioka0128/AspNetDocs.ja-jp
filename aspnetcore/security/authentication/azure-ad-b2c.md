---
title: ASP.NET Core での Azure Active Directory B2C でのクラウド認証
author: camsoper
description: ASP.NET Core での Azure Active Directory B2C の認証を設定する方法を説明します。
ms.date: 01/25/2018
ms.custom: mvc
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: 2c544475ccd3eb76f2737fec1cf269ac86add372
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57040629"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a><span data-ttu-id="cbd88-103">ASP.NET Core での Azure Active Directory B2C でのクラウド認証</span><span class="sxs-lookup"><span data-stu-id="cbd88-103">Cloud authentication with Azure Active Directory B2C in ASP.NET Core</span></span>

<span data-ttu-id="cbd88-104">作成者: [Cam Soper](https://twitter.com/camsoper)</span><span class="sxs-lookup"><span data-stu-id="cbd88-104">By [Cam Soper](https://twitter.com/camsoper)</span></span>

<span data-ttu-id="cbd88-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) は、web およびモバイル アプリのクラウド id 管理ソリューションです。</span><span class="sxs-lookup"><span data-stu-id="cbd88-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) is a cloud identity management solution for web and mobile apps.</span></span> <span data-ttu-id="cbd88-106">サービスは、クラウドとオンプレミスでホストされているアプリの認証を提供します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-106">The service provides authentication for apps hosted in the cloud and on-premises.</span></span> <span data-ttu-id="cbd88-107">認証の種類は、個々 のアカウントに、ソーシャル ネットワーク アカウントを含めるし、エンタープライズ アカウントをフェデレーションします。</span><span class="sxs-lookup"><span data-stu-id="cbd88-107">Authentication types include individual accounts, social network accounts, and federated enterprise accounts.</span></span> <span data-ttu-id="cbd88-108">また、Azure AD B2C では、最小構成での多要素認証を提供できます。</span><span class="sxs-lookup"><span data-stu-id="cbd88-108">Additionally, Azure AD B2C can provide multi-factor authentication with minimal configuration.</span></span>

> [!TIP]
> <span data-ttu-id="cbd88-109">Azure Active Directory (Azure AD) と Azure AD B2C は個別の製品を提供します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-109">Azure Active Directory (Azure AD) and Azure AD B2C are separate product offerings.</span></span> <span data-ttu-id="cbd88-110">Azure AD テナントは、組織を表し、Azure AD B2C テナントは証明書利用者アプリケーションで使用される id のコレクションを表します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-110">An Azure AD tenant represents an organization, while an Azure AD B2C tenant represents a collection of identities to be used with relying party applications.</span></span> <span data-ttu-id="cbd88-111">詳細についてを参照してください[Azure AD B2C:。よく寄せられる質問 (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs)します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-111">To learn more, see [Azure AD B2C: Frequently asked questions (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span></span>

<span data-ttu-id="cbd88-112">このチュートリアルで学習する方法。</span><span class="sxs-lookup"><span data-stu-id="cbd88-112">In this tutorial, learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cbd88-113">Azure Active Directory B2C テナントを作成します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-113">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="cbd88-114">Azure AD B2C にアプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-114">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="cbd88-115">Visual Studio を使用して、ASP.NET Core web アプリの認証に Azure AD B2C テナントを使用するように構成を作成するには</span><span class="sxs-lookup"><span data-stu-id="cbd88-115">Use Visual Studio to create an ASP.NET Core web app configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="cbd88-116">Azure AD B2C テナントの動作を制御するポリシーを構成します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-116">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cbd88-117">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="cbd88-117">Prerequisites</span></span>

<span data-ttu-id="cbd88-118">次に、このチュートリアルでは必須です。</span><span class="sxs-lookup"><span data-stu-id="cbd88-118">The following are required for this walkthrough:</span></span>

* [<span data-ttu-id="cbd88-119">Microsoft Azure サブスクリプション</span><span class="sxs-lookup"><span data-stu-id="cbd88-119">Microsoft Azure subscription</span></span>](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* <span data-ttu-id="cbd88-120">[Visual Studio 2017](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) (任意のエディション)</span><span class="sxs-lookup"><span data-stu-id="cbd88-120">[Visual Studio 2017](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) (any edition)</span></span>

## <a name="create-the-azure-active-directory-b2c-tenant"></a><span data-ttu-id="cbd88-121">Azure Active Directory B2C テナントを作成します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-121">Create the Azure Active Directory B2C tenant</span></span>

<span data-ttu-id="cbd88-122">Azure Active Directory B2C テナントの作成[ドキュメント」の説明に従って](/azure/active-directory-b2c/active-directory-b2c-get-started)します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-122">Create an Azure Active Directory B2C tenant [as described in the documentation](/azure/active-directory-b2c/active-directory-b2c-get-started).</span></span> <span data-ttu-id="cbd88-123">入力を求められたら、テナントを Azure サブスクリプションに関連付ける省略可能なこのチュートリアルでは。</span><span class="sxs-lookup"><span data-stu-id="cbd88-123">When prompted, associating the tenant with an Azure subscription is optional for this tutorial.</span></span>

## <a name="register-the-app-in-azure-ad-b2c"></a><span data-ttu-id="cbd88-124">Azure AD B2C でアプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-124">Register the app in Azure AD B2C</span></span>

<span data-ttu-id="cbd88-125">新しく作成された Azure AD B2C テナントで登録を使用して、アプリ[、ドキュメントの手順](/azure/active-directory-b2c/active-directory-b2c-app-registration#register-a-web-app)下、 **web アプリを登録**セクション。</span><span class="sxs-lookup"><span data-stu-id="cbd88-125">In the newly created Azure AD B2C tenant, register your app using [the steps in the documentation](/azure/active-directory-b2c/active-directory-b2c-app-registration#register-a-web-app) under the **Register a web app** section.</span></span> <span data-ttu-id="cbd88-126">停止時刻、 **web アプリ クライアント シークレットの作成**セクション。</span><span class="sxs-lookup"><span data-stu-id="cbd88-126">Stop at the **Create a web app client secret** section.</span></span> <span data-ttu-id="cbd88-127">このチュートリアルでは、クライアント シークレットが必要はありません。</span><span class="sxs-lookup"><span data-stu-id="cbd88-127">A client secret isn't required for this tutorial.</span></span> 

<span data-ttu-id="cbd88-128">次の値を使用します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-128">Use the following values:</span></span>

| <span data-ttu-id="cbd88-129">設定</span><span class="sxs-lookup"><span data-stu-id="cbd88-129">Setting</span></span>                       | <span data-ttu-id="cbd88-130">[値]</span><span class="sxs-lookup"><span data-stu-id="cbd88-130">Value</span></span>                     | <span data-ttu-id="cbd88-131">メモ</span><span class="sxs-lookup"><span data-stu-id="cbd88-131">Notes</span></span>                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="cbd88-132">**Name**</span><span class="sxs-lookup"><span data-stu-id="cbd88-132">**Name**</span></span>                      | <span data-ttu-id="cbd88-133">*&lt;アプリ名&gt;*</span><span class="sxs-lookup"><span data-stu-id="cbd88-133">*&lt;app name&gt;*</span></span>        | <span data-ttu-id="cbd88-134">入力、**名前**コンシューマーに、アプリについて説明しているアプリ。</span><span class="sxs-lookup"><span data-stu-id="cbd88-134">Enter a **Name** for the app that describes your app to consumers.</span></span>                                                                                                                                 |
| <span data-ttu-id="cbd88-135">**Web アプリを含める/web API**</span><span class="sxs-lookup"><span data-stu-id="cbd88-135">**Include web app / web API**</span></span> | <span data-ttu-id="cbd88-136">[はい]</span><span class="sxs-lookup"><span data-stu-id="cbd88-136">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="cbd88-137">**暗黙的なフローを許可します。**</span><span class="sxs-lookup"><span data-stu-id="cbd88-137">**Allow implicit flow**</span></span>       | <span data-ttu-id="cbd88-138">[はい]</span><span class="sxs-lookup"><span data-stu-id="cbd88-138">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="cbd88-139">**応答 URL**</span><span class="sxs-lookup"><span data-stu-id="cbd88-139">**Reply URL**</span></span>                 | `https://localhost:44300/signin-oidc` | <span data-ttu-id="cbd88-140">応答 Url とは、Azure AD B2C が、アプリが要求したトークンを返すエンドポイントです。</span><span class="sxs-lookup"><span data-stu-id="cbd88-140">Reply URLs are endpoints where Azure AD B2C returns any tokens that your app requests.</span></span> <span data-ttu-id="cbd88-141">Visual Studio では、使用する応答 URL を提供します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-141">Visual Studio provides the Reply URL to use.</span></span> <span data-ttu-id="cbd88-142">ここでは、次のように入力します。`https://localhost:44300/signin-oidc`フォームを完了します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-142">For now, enter `https://localhost:44300/signin-oidc` to complete the form.</span></span> |
| <span data-ttu-id="cbd88-143">**アプリ ID URI**</span><span class="sxs-lookup"><span data-stu-id="cbd88-143">**App ID URI**</span></span>                | <span data-ttu-id="cbd88-144">空白のままに</span><span class="sxs-lookup"><span data-stu-id="cbd88-144">Leave blank</span></span>               | <span data-ttu-id="cbd88-145">このチュートリアルでは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="cbd88-145">Not required for this tutorial.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="cbd88-146">**ネイティブ クライアントを含める**</span><span class="sxs-lookup"><span data-stu-id="cbd88-146">**Include native client**</span></span>     | <span data-ttu-id="cbd88-147">いいえ</span><span class="sxs-lookup"><span data-stu-id="cbd88-147">No</span></span>                        |                                                                                                                                                                                                    |

> [!WARNING]
> <span data-ttu-id="cbd88-148">かどうかは、を認識する localhost 以外の応答 URL では、設定、[応答 URL の一覧で許可されている内容に対する制約](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-web-app-or-api-reply-url)します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-148">If setting up a non-localhost Reply URL, be aware of the [constraints on what is allowed in the Reply URL list](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-web-app-or-api-reply-url).</span></span> 

<span data-ttu-id="cbd88-149">アプリを登録すると、テナント内のアプリの一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="cbd88-149">After the app is registered, the list of apps in the tenant is displayed.</span></span> <span data-ttu-id="cbd88-150">登録されたアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-150">Select the app that was just registered.</span></span> <span data-ttu-id="cbd88-151">選択、**コピー**アイコンの右側に、**アプリケーション ID**フィールドをクリップボードにコピーします。</span><span class="sxs-lookup"><span data-stu-id="cbd88-151">Select the **Copy** icon to the right of the **Application ID** field to copy it to the clipboard.</span></span>

<span data-ttu-id="cbd88-152">Nothing の詳細はこの時点で Azure AD B2C テナントで構成できますが、ブラウザー ウィンドウを開いたままにしておきます。</span><span class="sxs-lookup"><span data-stu-id="cbd88-152">Nothing more can be configured in the Azure AD B2C tenant at this time, but leave the browser window open.</span></span> <span data-ttu-id="cbd88-153">ASP.NET Core アプリを作成した後の構成の詳細は。</span><span class="sxs-lookup"><span data-stu-id="cbd88-153">There is more configuration after the ASP.NET Core app is created.</span></span>

## <a name="create-an-aspnet-core-app-in-visual-studio-2017"></a><span data-ttu-id="cbd88-154">Visual Studio 2017 での ASP.NET Core アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-154">Create an ASP.NET Core app in Visual Studio 2017</span></span>

<span data-ttu-id="cbd88-155">認証に Azure AD B2C テナントを使用する Visual Studio Web アプリケーション テンプレートを構成できます。</span><span class="sxs-lookup"><span data-stu-id="cbd88-155">The Visual Studio Web Application template can be configured to use the Azure AD B2C tenant for authentication.</span></span>

<span data-ttu-id="cbd88-156">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="cbd88-156">In Visual Studio:</span></span>

1. <span data-ttu-id="cbd88-157">新しい ASP.NET Core Web アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-157">Create a new ASP.NET Core Web Application.</span></span> 
2. <span data-ttu-id="cbd88-158">選択**Web アプリケーション**テンプレートの一覧から。</span><span class="sxs-lookup"><span data-stu-id="cbd88-158">Select **Web Application** from the list of templates.</span></span>
3. <span data-ttu-id="cbd88-159">選択、**認証の変更**ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="cbd88-159">Select the **Change Authentication** button.</span></span>
    
    ![変更の認証 ボタン](./azure-ad-b2c/_static/changeauth.png)

4. <span data-ttu-id="cbd88-161">**認証の変更**ダイアログ ボックスで、**個々 のユーザー アカウント**、し、 **、クラウド内の既存のユーザー ストアへの接続**ドロップダウン リストにします。</span><span class="sxs-lookup"><span data-stu-id="cbd88-161">In the **Change Authentication** dialog, select **Individual User Accounts**, and then select **Connect to an existing user store in the cloud** in the dropdown.</span></span> 
    
    ![認証の変更ダイアログ](./azure-ad-b2c/_static/changeauthdialog.png)

5. <span data-ttu-id="cbd88-163">次の値をフォームに入力します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-163">Complete the form with the following values:</span></span>
    
    | <span data-ttu-id="cbd88-164">設定</span><span class="sxs-lookup"><span data-stu-id="cbd88-164">Setting</span></span>                       | <span data-ttu-id="cbd88-165">[値]</span><span class="sxs-lookup"><span data-stu-id="cbd88-165">Value</span></span>                                                 |
    |-------------------------------|-------------------------------------------------------|
    | <span data-ttu-id="cbd88-166">**ドメイン名**</span><span class="sxs-lookup"><span data-stu-id="cbd88-166">**Domain Name**</span></span>               | <span data-ttu-id="cbd88-167">*&lt;B2C テナントのドメイン名&gt;*</span><span class="sxs-lookup"><span data-stu-id="cbd88-167">*&lt;the domain name of your B2C tenant&gt;*</span></span>          |
    | <span data-ttu-id="cbd88-168">**アプリケーション ID**</span><span class="sxs-lookup"><span data-stu-id="cbd88-168">**Application ID**</span></span>            | <span data-ttu-id="cbd88-169">*&lt;クリップボードからのアプリケーション ID を貼り付けます&gt;*</span><span class="sxs-lookup"><span data-stu-id="cbd88-169">*&lt;paste the Application ID from the clipboard&gt;*</span></span> |
    | <span data-ttu-id="cbd88-170">**コールバック パス**</span><span class="sxs-lookup"><span data-stu-id="cbd88-170">**Callback Path**</span></span>             | <span data-ttu-id="cbd88-171">*&lt;既定値を使用します。&gt;*</span><span class="sxs-lookup"><span data-stu-id="cbd88-171">*&lt;use the default value&gt;*</span></span>                       |
    | <span data-ttu-id="cbd88-172">**サインアップまたはサインイン ポリシー**</span><span class="sxs-lookup"><span data-stu-id="cbd88-172">**Sign-up or sign-in policy**</span></span> | `B2C_1_SiUpIn`                                        |
    | <span data-ttu-id="cbd88-173">**パスワードのリセット ポリシー**</span><span class="sxs-lookup"><span data-stu-id="cbd88-173">**Reset password policy**</span></span>     | `B2C_1_SSPR`                                          |
    | <span data-ttu-id="cbd88-174">**プロファイルのポリシーを編集します。**</span><span class="sxs-lookup"><span data-stu-id="cbd88-174">**Edit profile policy**</span></span>       | <span data-ttu-id="cbd88-175">*&lt;空白のままに&gt;*</span><span class="sxs-lookup"><span data-stu-id="cbd88-175">*&lt;leave blank&gt;*</span></span>                                 |
    
    <span data-ttu-id="cbd88-176">選択、**コピー**リンクの横に**応答 URI**応答 URI をクリップボードにコピーします。</span><span class="sxs-lookup"><span data-stu-id="cbd88-176">Select the **Copy** link next to **Reply URI** to copy the Reply URI to the clipboard.</span></span> <span data-ttu-id="cbd88-177">選択**OK**を閉じる、**認証の変更**ダイアログ。</span><span class="sxs-lookup"><span data-stu-id="cbd88-177">Select **OK** to close the **Change Authentication** dialog.</span></span> <span data-ttu-id="cbd88-178">選択**OK** web アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-178">Select **OK** to create the web app.</span></span>

## <a name="finish-the-b2c-app-registration"></a><span data-ttu-id="cbd88-179">B2C アプリの登録を完了します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-179">Finish the B2C app registration</span></span>

<span data-ttu-id="cbd88-180">まだ開いて B2C アプリのプロパティを備えたブラウザー ウィンドウに戻ります。</span><span class="sxs-lookup"><span data-stu-id="cbd88-180">Return to the browser window with the B2C app properties still open.</span></span> <span data-ttu-id="cbd88-181">一時的な変更**応答 URL** Visual Studio から以前の値にコピーを指定します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-181">Change the temporary **Reply URL** specified earlier to the value copied from Visual Studio.</span></span> <span data-ttu-id="cbd88-182">選択**保存**ウィンドウの上部にあります。</span><span class="sxs-lookup"><span data-stu-id="cbd88-182">Select **Save** at the top of the window.</span></span>

> [!TIP]
> <span data-ttu-id="cbd88-183">[デバッグ] タブからの HTTPS アドレスを使用して、web プロジェクトのプロパティと追加の応答 URL をコピーしなかった場合、 **CallbackPath**値から*appsettings.json*します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-183">If you didn't copy the Reply URL, use the HTTPS address from the Debug tab in the web project properties, and append the **CallbackPath** value from *appsettings.json*.</span></span>

## <a name="configure-policies"></a><span data-ttu-id="cbd88-184">ポリシーを構成します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-184">Configure policies</span></span>

<span data-ttu-id="cbd88-185">手順を使用して、Azure AD B2C のドキュメントを[サインアップまたはサインイン ポリシーを作成する](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-sign-up-or-sign-in-policy)、し[パスワードのリセット ポリシーを作成](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-password-reset-policy)。</span><span class="sxs-lookup"><span data-stu-id="cbd88-185">Use the steps in the Azure AD B2C documentation to [create a sign-up or sign-in policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-sign-up-or-sign-in-policy), and then [create a password reset policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-password-reset-policy).</span></span> <span data-ttu-id="cbd88-186">ドキュメントの例の値を使用して、 **Id プロバイダー**、**サインアップ属性**、および**アプリケーション要求**します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-186">Use the example values provided in the documentation for **Identity providers**, **Sign-up attributes**, and **Application claims**.</span></span> <span data-ttu-id="cbd88-187">使用して、**を今すぐ実行**ドキュメント」の説明に従って、ポリシーをテストするボタンは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="cbd88-187">Using the **Run now** button to test the policies as described in the documentation is optional.</span></span>

> [!WARNING]
> <span data-ttu-id="cbd88-188">ポリシー名は、のドキュメントで説明したとおりでこれらのポリシーが使用されていたことを確認、**認証の変更**Visual Studio でダイアログ。</span><span class="sxs-lookup"><span data-stu-id="cbd88-188">Ensure the policy names are exactly as described in the documentation, as those policies were used in the **Change Authentication** dialog in Visual Studio.</span></span> <span data-ttu-id="cbd88-189">ポリシー名を検証できます*appsettings.json*します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-189">The policy names can be verified in *appsettings.json*.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="cbd88-190">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="cbd88-190">Run the app</span></span>

<span data-ttu-id="cbd88-191">Visual Studio で、キーを押して**F5**アプリをビルドして実行します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-191">In Visual Studio, press **F5** to build and run the app.</span></span> <span data-ttu-id="cbd88-192">Web アプリが起動後、選択**Accept** (要求) の場合、cookie の使用に同意し、選択**サインイン**します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-192">After the web app launches, select **Accept** to accept the use of cookies (if prompted), and then select **Sign in**.</span></span>

![アプリにサインインします。](./azure-ad-b2c/_static/signin.png)

<span data-ttu-id="cbd88-194">ブラウザーは、Azure AD B2C テナントにリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="cbd88-194">The browser redirects to the Azure AD B2C tenant.</span></span> <span data-ttu-id="cbd88-195">(この場合、ポリシーをテストする 1 つが作成された) 既存のアカウントでサインインまたは選択**今すぐサインアップ**新しいアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-195">Sign in with an existing account (if one was created testing the policies) or select **Sign up now** to create a new account.</span></span> <span data-ttu-id="cbd88-196">**パスワードを忘れた場合でしょうか。** 忘れたパスワードをリセットするリンクを使用します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-196">The **Forgot your password?** link is used to reset a forgotten password.</span></span>

![Azure AD B2C のログイン](./azure-ad-b2c/_static/b2csts.png)

<span data-ttu-id="cbd88-198">正常にサインインした後、ブラウザーに web アプリにリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="cbd88-198">After successfully signing in, the browser redirects to the web app.</span></span>

![成功](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a><span data-ttu-id="cbd88-200">次の手順</span><span class="sxs-lookup"><span data-stu-id="cbd88-200">Next steps</span></span>

<span data-ttu-id="cbd88-201">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="cbd88-201">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cbd88-202">Azure Active Directory B2C テナントを作成します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-202">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="cbd88-203">Azure AD B2C にアプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-203">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="cbd88-204">Visual Studio を使用して、認証に Azure AD B2C テナントを使用するように構成の ASP.NET Core Web アプリケーションを作成するには</span><span class="sxs-lookup"><span data-stu-id="cbd88-204">Use Visual Studio to create an ASP.NET Core Web Application configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="cbd88-205">Azure AD B2C テナントの動作を制御するポリシーを構成します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-205">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

<span data-ttu-id="cbd88-206">認証に Azure AD B2C を使用する ASP.NET Core アプリが構成されたので、 [Authorize 属性](xref:security/authorization/simple)アプリをセキュリティで保護するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="cbd88-206">Now that the ASP.NET Core app is configured to use Azure AD B2C for authentication, the [Authorize attribute](xref:security/authorization/simple) can be used to secure your app.</span></span> <span data-ttu-id="cbd88-207">学習することにより、アプリの開発を続行するには。</span><span class="sxs-lookup"><span data-stu-id="cbd88-207">Continue developing your app by learning to:</span></span>

* <span data-ttu-id="cbd88-208">[Azure AD B2C ユーザー インターフェイスをカスタマイズ](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization)します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-208">[Customize the Azure AD B2C user interface](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span></span>
* <span data-ttu-id="cbd88-209">[パスワードの複雑さの要件を構成する](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-209">[Configure password complexity requirements](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span></span>
* <span data-ttu-id="cbd88-210">[多要素認証を有効にする](/azure/active-directory-b2c/active-directory-b2c-reference-mfa)します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-210">[Enable multi-factor authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span></span>
* <span data-ttu-id="cbd88-211">など、追加の id プロバイダーを構成[Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app)、 [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app)、 [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app)、 [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app)、 [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)、およびその他。</span><span class="sxs-lookup"><span data-stu-id="cbd88-211">Configure additional identity providers, such as [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), and others.</span></span>
* <span data-ttu-id="cbd88-212">[Azure AD Graph API を使用して、](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) Azure AD B2C テナントからグループのメンバーシップなどの追加のユーザー情報を取得します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-212">[Use the Azure AD Graph API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) to retrieve additional user information, such as group membership, from the Azure AD B2C tenant.</span></span>
* <span data-ttu-id="cbd88-213">[セキュリティで保護された ASP.NET Core web API が Azure AD B2C を使用して](xref:security/authentication/azure-ad-b2c-webapi)します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-213">[Secure an ASP.NET Core web API using Azure AD B2C](xref:security/authentication/azure-ad-b2c-webapi).</span></span>
* <span data-ttu-id="cbd88-214">[Azure AD B2C を使用して .NET web アプリから .NET web API を呼び出す](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet)します。</span><span class="sxs-lookup"><span data-stu-id="cbd88-214">[Call a .NET web API from a .NET web app using Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet).</span></span>
