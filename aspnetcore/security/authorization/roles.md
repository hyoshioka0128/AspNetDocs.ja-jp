---
title: ASP.NET Core でのロールベースの承認
author: rick-anderson
description: Authorize 属性にロールを渡すことによって、ASP.NET Core のコント ローラーとアクションのアクセスを制限する方法について説明します。
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/roles
ms.openlocfilehash: c38e7144166ce7741eee6e3acb4d1c952ad4f024
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57054379"
---
# <a name="role-based-authorization-in-aspnet-core"></a>ASP.NET Core でのロールベースの承認

<a name="security-authorization-role-based"></a>

Id が作成されたときに、1 つまたは複数のロールに属する可能性があります。 たとえば、Tracy は、Scott は、ユーザー ロールにのみ属することがあります、管理者およびユーザー ロールに属して可能性があります。 これらのロールを作成および管理する方法は、承認プロセスのバッキング ストアに依存します。 ロールは、開発者に公開、 [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole)メソッドを[ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal)クラス。

## <a name="adding-role-checks"></a>追加の役割の確認

ロール ベースの承認チェックは、宣言型&mdash;開発者それらを埋め込みます、コント ローラーまたはコント ローラー内のアクションに対して、コード内で要求されたリソースにアクセスするのメンバーである必要があります、現在のユーザー ロールを指定します。

次のコードがすべてのアクションへのアクセスを制限するなど、`AdministrationController`のメンバーであるユーザーに、`Administrator`ロール。

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

複数のロールは、コンマ区切りリストとして指定できます。

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

このコント ローラーがメンバーであるユーザーがアクセスのみができるは`HRManager`ロールまたは`Finance`ロール。

アクセスのユーザーは、指定したすべてのロールのメンバーである必要がありますし、複数の属性を適用する場合次の例では、ユーザーが両方のメンバーでなければならないことが必要です、`PowerUser`と`ControlPanelUser`ロール。

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

さらに、アクション レベルで追加のロールの承認属性を適用することでアクセスを制限できます。

```csharp
[Authorize(Roles = "Administrator, PowerUser")]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [Authorize(Roles = "Administrator")]
    public ActionResult ShutDown()
    {
    }
}
```

前のコード スニペットのメンバーで、`Administrator`ロールまたは`PowerUser`コント ローラーにアクセスできるロールと`SetTime`アクションがのメンバーのみ、`Administrator`ロールがアクセスできる、`ShutDown`アクション。

コント ローラーのロックダウンも、個々 のアクションへの匿名、認証されていないアクセスを許可できます。

```csharp
[Authorize]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [AllowAnonymous]
    public ActionResult Login()
    {
    }
}
```

::: moniker range=">= aspnetcore-2.0"

Razor ページの場合、`AuthorizeAttribute`いずれかで適用できます。

* 使用して、[規則](xref:razor-pages/razor-pages-conventions#page-model-action-conventions)、または
* 適用、`AuthorizeAttribute`を`PageModel`インスタンス。

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public class UpdateModel : PageModel
{
    public ActionResult OnPost()
    {
    }
}
```

> [!IMPORTANT]
> などの属性をフィルター処理`AuthorizeAttribute`PageModel にのみ適用できる、特定のページ ハンドラー メソッドには適用できません。
::: moniker-end


<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a>ポリシー ベースの役割の確認

承認サービスの構成の一部として、開発者が起動時にポリシーを登録、新しいポリシーの構文を使用しても、役割の要件を表現できます。 これに通常発生`ConfigureServices()`で、 *Startup.cs*ファイル。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```

使用してポリシーが適用される、`Policy`プロパティを`AuthorizeAttribute`属性。

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

要件で許可されている複数のロールを指定するかどうかは、それらを指定するにはパラメーターとして、`RequireRole`メソッド。

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

この例に属しているユーザーの承認、 `Administrator`、`PowerUser`または`BackupAdministrator`ロール。

### <a name="add-role-services-to-identity"></a>役割サービスの Id を追加します。

追加[AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1)役割サービスを追加します。

[!code-csharp[](roles/samples/Startup.cs?name=snippet&highlight=7)]
