---
uid: whitepapers/ms03-32-issue
title: IE のセキュリティ更新プログラムを適用した後に 'サーバー アプリケーションが使用できない' エラーの修正 |Microsoft Docs
author: rick-anderson
description: このホワイト ペーパーでは、Wi で実行されている ASP.NET 1.0 アプリケーションに影響する Internet Explorer の ms 03 32 のセキュリティ更新プログラムの問題を修正する修正プログラムについて説明しています.
ms.author: riande
ms.date: 02/10/2010
ms.assetid: 1365eebb-bdf7-4a05-8d18-7f200531be55
msc.legacyurl: /whitepapers/ms03-32-issue
msc.type: content
ms.openlocfilehash: 9041f8d15a449a517594f8051c3d9f0ceb18a8a3
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57038549"
---
<a name="fix-for-server-application-unavailable-error-after-applying-security-update-for-ie"></a><span data-ttu-id="38671-103">IE のセキュリティ更新を適用した後の 'サーバー アプリケーションは使用できません ' エラーの修正</span><span class="sxs-lookup"><span data-stu-id="38671-103">Fix for 'Server Application Unavailable' Error after Applying Security Update for IE</span></span>
====================
> <span data-ttu-id="38671-104">このホワイト ペーパーでは、Windows XP Professional で実行されている ASP.NET 1.0 アプリケーションに影響する Internet Explorer の ms 03 32 のセキュリティ更新プログラムの問題を修正する修正プログラムについて説明します。</span><span class="sxs-lookup"><span data-stu-id="38671-104">This paper describes the patch that fixes an issue with the MS03-32 Security Update for Internet Explorer that affects ASP.NET 1.0 applications running on Windows XP Professional.</span></span>
> 
> <span data-ttu-id="38671-105">ASP.NET 1.0 と Windows XP Professional に適用されます。</span><span class="sxs-lookup"><span data-stu-id="38671-105">Applies to ASP.NET 1.0 and Windows XP Professional.</span></span>


<span data-ttu-id="38671-106">Microsoft では、Internet Explorer のセキュリティ更新プログラムの ms 03 32 セキュリティ更新プログラムで Windows XP で実行されている ASP.NET 1.0 問題点を確認します。</span><span class="sxs-lookup"><span data-stu-id="38671-106">Microsoft identified an issue with the MS03-32 Security Update for Internet Explorer security patch and ASP.NET 1.0 running on Windows XP.</span></span> <span data-ttu-id="38671-107">この修正プログラムは、手動、または、Windows Update サイトから最新の重要な更新プログラムを入手してインストールできます。</span><span class="sxs-lookup"><span data-stu-id="38671-107">This patch can be installed manually or by obtaining recent critical updates from the Windows Update site.</span></span>

<span data-ttu-id="38671-108">この問題の現象は、Windows XP コンピューターで、修正プログラムをインストールした後、ローカルの IIS 5.1 web サーバーで実行されている ASP.NET アプリケーションへのすべての要求の「サーバー アプリケーションは使用できません」というエラー メッセージで結果することです。</span><span class="sxs-lookup"><span data-stu-id="38671-108">The symptom of this issue is that after installing the patch on a Windows XP machine, all requests to ASP.NET applications running on the local IIS 5.1 web server result in an error message saying "Server Application Unavailable".</span></span> <span data-ttu-id="38671-109">リモート web サーバーへの要求は、影響を受けません。</span><span class="sxs-lookup"><span data-stu-id="38671-109">Requests to remote web servers are unaffected.</span></span>

<span data-ttu-id="38671-110">この問題には、Windows XP での ASP.NET 1.0 を実行しているインストールのみに影響します。</span><span class="sxs-lookup"><span data-stu-id="38671-110">This issue only impacts installations running ASP.NET 1.0 on Windows XP.</span></span> <span data-ttu-id="38671-111">Windows 2000 または Windows Server 2003 を実行しているマシンには影響しません。</span><span class="sxs-lookup"><span data-stu-id="38671-111">It does not impact machines running Windows 2000 or Windows Server 2003.</span></span> <span data-ttu-id="38671-112">ASP.NET 1.1 がインストールされていると、Windows XP を実行しているマシンも影響しません。</span><span class="sxs-lookup"><span data-stu-id="38671-112">It also does not impact machines running Windows XP with ASP.NET 1.1 installed.</span></span>

<span data-ttu-id="38671-113">注意してくださいこの問題**ない**ASP.NET を使用したセキュリティ バグです。</span><span class="sxs-lookup"><span data-stu-id="38671-113">Please note that this issue **is not** a security bug with ASP.NET.</span></span> <span data-ttu-id="38671-114">これは、**しない**開くか、ASP.NET アプリケーションまたはサーバーに対する悪意のある攻撃を許可します。</span><span class="sxs-lookup"><span data-stu-id="38671-114">It **does not** open up or allow any malicious attacks against an ASP.NET application or server.</span></span> <span data-ttu-id="38671-115">代わりに、パッチ自体による機能のバグ純粋になります。</span><span class="sxs-lookup"><span data-stu-id="38671-115">Instead, it is purely a functional bug caused by the patch itself.</span></span>

<span data-ttu-id="38671-116">取り組んでいますハードこの問題の永続的なソリューションです。</span><span class="sxs-lookup"><span data-stu-id="38671-116">We are working hard on a permanent solution for this issue.</span></span> <span data-ttu-id="38671-117">それまでは、問題の回避策として、次のバッチ ファイルを実行できます。</span><span class="sxs-lookup"><span data-stu-id="38671-117">In the meantime, you can execute the following batch file as a workaround for the issue.</span></span> <span data-ttu-id="38671-118">バッチ ファイルは、次を行います。</span><span class="sxs-lookup"><span data-stu-id="38671-118">The batch file does the following:</span></span>

1. <span data-ttu-id="38671-119">IIS および ASP.NET 状態サービスを停止します</span><span class="sxs-lookup"><span data-stu-id="38671-119">Stops the IIS and ASP.NET state services</span></span>
2. <span data-ttu-id="38671-120">削除し、既知の一時的なパスワードを使用して、ASPNET アカウントを再作成されます。</span><span class="sxs-lookup"><span data-stu-id="38671-120">Deletes and recreates the ASPNET account with a known temporary password</span></span>
3. <span data-ttu-id="38671-121">Windows を使用して`runas`ASPNET ユーザー プロファイルを作成する実行可能ファイルを起動するコマンド</span><span class="sxs-lookup"><span data-stu-id="38671-121">Uses the Windows `runas` command to launch an executable that creates an ASPNET user profile</span></span>
4. <span data-ttu-id="38671-122">ASP.NET を再登録します。</span><span class="sxs-lookup"><span data-stu-id="38671-122">Re-registers ASP.NET.</span></span> <span data-ttu-id="38671-123">これは、アカウントの新しいランダムなパスワードを作成しの既定の ASP.NET へのアクセス制御の設定を適用</span><span class="sxs-lookup"><span data-stu-id="38671-123">This creates a new random password for the account and applies default ASP.NET access control settings for it</span></span>
5. <span data-ttu-id="38671-124">IIS サービスを再起動します。</span><span class="sxs-lookup"><span data-stu-id="38671-124">Restarts the IIS service</span></span>

<span data-ttu-id="38671-125">バッチ ファイルにはハードコードされた一時パスワードが含まれています"<strong>1pass\@word</strong>"、runas コマンド、バッチ ファイルの実行時に入力するように求めできます。</span><span class="sxs-lookup"><span data-stu-id="38671-125">The batch file contains a hardcoded temporary password of "<strong>1pass\@word</strong>" which you will be prompted to enter for the runas command when the batch file is run.</span></span> <span data-ttu-id="38671-126">Runas コマンドが完了した後、ASPNET アカウントのパスワードは強力なランダムな値で再作成します。</span><span class="sxs-lookup"><span data-stu-id="38671-126">After the runas command completes, the ASPNET account password is recreated with a strong random value.</span></span> <span data-ttu-id="38671-127">ハードコーディングされたパスワードが環境内でパスワードの複雑さの要件を満たしていない場合、バッチ ファイルが失敗する可能性がありますに注意してください。</span><span class="sxs-lookup"><span data-stu-id="38671-127">Note that the batch file may fail if the hardcoded password does not meet the password complexity requirements in your environment.</span></span> <span data-ttu-id="38671-128">ケースがある場合は、環境に適したが別の値に変更できます。</span><span class="sxs-lookup"><span data-stu-id="38671-128">If that's the case, you can change it to another value that is appropriate for your environment.</span></span>

<span data-ttu-id="38671-129">*> [!IMPORTANT]* カスタムのアクセス制御の設定や、ASPNET アカウントのデータベース アカウントのアクセス許可を追加した場合は、このバッチ ファイルが完了したら、再作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="38671-129">*> [!IMPORTANT]* If you have added custom access control settings or database account permissions for the ASPNET account, they will need to be recreated after this batch file completes.</span></span> <span data-ttu-id="38671-130">アカウントを再作成するときに新しいセキュリティ識別子 (SID) を取得するためです。</span><span class="sxs-lookup"><span data-stu-id="38671-130">This is because when the account is recreated, it will get a new security identifier (SID).</span></span>

<span data-ttu-id="38671-131">*> [!IMPORTANT]* ASPNET アカウント以外のカスタム アカウントを使用して、ASP.NET ワーカー プロセスを実行する場合、このバッチ ファイルいない実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="38671-131">*> [!IMPORTANT]* If you are running the ASP.NET worker process with a custom account other than the ASPNET account, then you should not run this batch file.</span></span> <span data-ttu-id="38671-132">代わりに、対話形式でログインまたは runas コマンドを使用して、そのアカウントのユーザー プロファイルを作成し、そのアカウントを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="38671-132">Instead, you should log in interactively or use the runas command with that account which will create a user profile for that account.</span></span>

<span data-ttu-id="38671-133">バッチ ファイルは、次の自己解凍形式のアーカイブに含まれます。</span><span class="sxs-lookup"><span data-stu-id="38671-133">The batch file is included in the self-extracting archive below.</span></span> <span data-ttu-id="38671-134">使用方法。</span><span class="sxs-lookup"><span data-stu-id="38671-134">To use it:</span></span>

1. <span data-ttu-id="38671-135">管理者特権では、アカウントとして実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="38671-135">You must be running as an account with Administrator privileges</span></span>
2. [<span data-ttu-id="38671-136">ダウンロードして、自己解凍実行可能ファイルを開きます</span><span class="sxs-lookup"><span data-stu-id="38671-136">Download and open the self-extracting executable file</span></span>](ms03-32-issue/_static/fixup1.exe)
3. <span data-ttu-id="38671-137">C:\ に内容を抽出します。</span><span class="sxs-lookup"><span data-stu-id="38671-137">Extract the contents to c:\\</span></span>
4. <span data-ttu-id="38671-138">...、スタート メニューから実行を選択し、入力 `cmd.exe`</span><span class="sxs-lookup"><span data-stu-id="38671-138">Select Run... from the start menu, and enter `cmd.exe`</span></span>
5. <span data-ttu-id="38671-139">開いているコマンド ウィンドウで、入力`c:\fixup.cmd`します。</span><span class="sxs-lookup"><span data-stu-id="38671-139">In the open command windows, type `c:\fixup.cmd`.</span></span>
6. <span data-ttu-id="38671-140">入力を求められたら入力<strong>1pass\@word</strong>パスワードとして。</span><span class="sxs-lookup"><span data-stu-id="38671-140">When prompted, enter <strong>1pass\@word</strong> as the password.</span></span>
7. <span data-ttu-id="38671-141">以前のカスタム アクセス制御の設定または ASPNET アカウントのデータベース アカウントのアクセス許可がある場合は、これらの設定を今すぐ再適用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="38671-141">If you have previously custom access control settings or database account permissions for the ASPNET account, you'll need to re-apply these settings now.</span></span>

<span data-ttu-id="38671-142">これが原因となった不便多く申し訳ありません。</span><span class="sxs-lookup"><span data-stu-id="38671-142">Many apologies for the inconvenience that this has caused.</span></span> <span data-ttu-id="38671-143">利用可能になった追加情報を掲載します。</span><span class="sxs-lookup"><span data-stu-id="38671-143">We'll post additional information as it becomes available.</span></span>

<span data-ttu-id="38671-144">次の表は、プラットフォームとこの問題による影響を受けるバージョンについて説明します。</span><span class="sxs-lookup"><span data-stu-id="38671-144">The matrix below details platforms and versions impacted by this issue.</span></span>

| <span data-ttu-id="38671-145">.NET Framework</span><span class="sxs-lookup"><span data-stu-id="38671-145">.NET Framework</span></span> | <span data-ttu-id="38671-146">プラットフォーム</span><span class="sxs-lookup"><span data-stu-id="38671-146">Platform</span></span> | <span data-ttu-id="38671-147">影響を受ける</span><span class="sxs-lookup"><span data-stu-id="38671-147">Affected</span></span> |
| --- | --- | --- |
| <span data-ttu-id="38671-148">バージョン 1.0</span><span class="sxs-lookup"><span data-stu-id="38671-148">Version 1.0</span></span> | <span data-ttu-id="38671-149">Windows 2000 Professional</span><span class="sxs-lookup"><span data-stu-id="38671-149">Windows 2000 Professional</span></span> | <span data-ttu-id="38671-150">いいえ</span><span class="sxs-lookup"><span data-stu-id="38671-150">No</span></span> |
| <span data-ttu-id="38671-151">バージョン 1.0</span><span class="sxs-lookup"><span data-stu-id="38671-151">Version 1.0</span></span> | <span data-ttu-id="38671-152">Windows 2000 Server</span><span class="sxs-lookup"><span data-stu-id="38671-152">Windows 2000 Server</span></span> | <span data-ttu-id="38671-153">いいえ</span><span class="sxs-lookup"><span data-stu-id="38671-153">No</span></span> |
| <span data-ttu-id="38671-154">バージョン 1.0</span><span class="sxs-lookup"><span data-stu-id="38671-154">Version 1.0</span></span> | <span data-ttu-id="38671-155">Windows XP Professional</span><span class="sxs-lookup"><span data-stu-id="38671-155">Windows XP Professional</span></span> | <span data-ttu-id="38671-156">[はい]</span><span class="sxs-lookup"><span data-stu-id="38671-156">Yes</span></span> |
| <span data-ttu-id="38671-157">バージョン 1.0</span><span class="sxs-lookup"><span data-stu-id="38671-157">Version 1.0</span></span> | <span data-ttu-id="38671-158">Windows Server 2003</span><span class="sxs-lookup"><span data-stu-id="38671-158">Windows Server 2003</span></span> | <span data-ttu-id="38671-159">いいえ</span><span class="sxs-lookup"><span data-stu-id="38671-159">No</span></span> |
| <span data-ttu-id="38671-160">バージョン 1.0</span><span class="sxs-lookup"><span data-stu-id="38671-160">Version 1.0</span></span> | <span data-ttu-id="38671-161">Cassini と Windows XP ホーム</span><span class="sxs-lookup"><span data-stu-id="38671-161">Windows XP Home with Cassini</span></span> | <span data-ttu-id="38671-162">いいえ</span><span class="sxs-lookup"><span data-stu-id="38671-162">No</span></span> |
| <span data-ttu-id="38671-163">バージョン 1.1</span><span class="sxs-lookup"><span data-stu-id="38671-163">Version 1.1</span></span> | <span data-ttu-id="38671-164">Windows 2000 Professional</span><span class="sxs-lookup"><span data-stu-id="38671-164">Windows 2000 Professional</span></span> | <span data-ttu-id="38671-165">いいえ</span><span class="sxs-lookup"><span data-stu-id="38671-165">No</span></span> |
| <span data-ttu-id="38671-166">バージョン 1.1</span><span class="sxs-lookup"><span data-stu-id="38671-166">Version 1.1</span></span> | <span data-ttu-id="38671-167">Windows 2000 Server</span><span class="sxs-lookup"><span data-stu-id="38671-167">Windows 2000 Server</span></span> | <span data-ttu-id="38671-168">いいえ</span><span class="sxs-lookup"><span data-stu-id="38671-168">No</span></span> |
| <span data-ttu-id="38671-169">バージョン 1.1</span><span class="sxs-lookup"><span data-stu-id="38671-169">Version 1.1</span></span> | <span data-ttu-id="38671-170">Windows XP Professional</span><span class="sxs-lookup"><span data-stu-id="38671-170">Windows XP Professional</span></span> | <span data-ttu-id="38671-171">いいえ</span><span class="sxs-lookup"><span data-stu-id="38671-171">No</span></span> |
| <span data-ttu-id="38671-172">バージョン 1.1</span><span class="sxs-lookup"><span data-stu-id="38671-172">Version 1.1</span></span> | <span data-ttu-id="38671-173">Windows Server 2003</span><span class="sxs-lookup"><span data-stu-id="38671-173">Windows Server 2003</span></span> | <span data-ttu-id="38671-174">いいえ</span><span class="sxs-lookup"><span data-stu-id="38671-174">No</span></span> |
| <span data-ttu-id="38671-175">バージョン 1.1</span><span class="sxs-lookup"><span data-stu-id="38671-175">Version 1.1</span></span> | <span data-ttu-id="38671-176">Cassini と Windows XP ホーム</span><span class="sxs-lookup"><span data-stu-id="38671-176">Windows XP Home with Cassini</span></span> | <span data-ttu-id="38671-177">いいえ</span><span class="sxs-lookup"><span data-stu-id="38671-177">No</span></span> |

<span data-ttu-id="38671-178">よろしくお願いいたします</span><span class="sxs-lookup"><span data-stu-id="38671-178">Thanks,</span></span>   
 <span data-ttu-id="38671-179">ASP.NET チーム</span><span class="sxs-lookup"><span data-stu-id="38671-179">The ASP.NET Team</span></span>