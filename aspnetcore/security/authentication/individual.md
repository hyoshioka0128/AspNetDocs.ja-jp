---
title: 個々 のユーザー アカウントで作成した ASP.NET Core プロジェクトに基づく記事
author: rick-anderson
description: 個々 のユーザー アカウントで作成した ASP.NET Core プロジェクトに基づくアーティクルを検出します。
ms.author: riande
ms.date: 11/30/2017
uid: security/authentication/individual
ms.openlocfilehash: c73365eafaf2c38ef02c3c83ccf5ced4264f7dc0
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57033839"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a>個々 のユーザー アカウントで作成した ASP.NET Core プロジェクトに基づく記事

ASP.NET Core Identity は、「個々 のユーザー アカウント」オプションを使用して Visual Studio でプロジェクト テンプレートに含まれます。

認証のテンプレートは .NET Core CLI を`-au Individual`:

::: moniker range=">= aspnetcore-2.1"

```console
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```console
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

参照してください[この GitHub の問題](https://github.com/aspnet/AspNetCore/issues/5833)web API 認証します。

<a name="no"></a>
## <a name="no-authentication"></a>認証なし

.NET Core CLI で認証が指定された、`-au`オプション。 Visual Studio で、**認証の変更**ダイアログは、新しい web アプリケーションで利用できます。 Visual Studio で新しい web アプリの既定値は**認証なし**します。

認証なしで作成されたプロジェクト:

* Web ページ、サインインし、サインアウトの UI が含まれていません。
* 認証コードが含まれていません。

<a name="win"></a>
## <a name="windows-authentication"></a>Windows 認証

.NET Core CLI で新しい web アプリの Windows 認証が指定されて、`-au Windows`オプション。 Visual Studio で、**認証の変更**ダイアログ ボックスには、 **Windows 認証**オプション。

使用するアプリが構成されている Windows 認証が選択されている場合、 [Windows 認証の IIS モジュール](xref:host-and-deploy/iis/modules)します。 Windows 認証は、イントラネットの web サイトを対象としています。

## <a name="additional-resources"></a>その他の技術情報

次の記事では、個々 のユーザー アカウントを使用する ASP.NET Core テンプレートで生成されたコードを使用する方法を示します。

* [SMS での 2 要素認証](xref:security/authentication/2fa)
* [ASP.NET Core でのアカウントの確認とパスワードの回復](xref:security/authentication/accconfirm)
* [承認によって保護されたユーザー データと ASP.NET Core アプリを作成します。](xref:security/authorization/secure-data)
