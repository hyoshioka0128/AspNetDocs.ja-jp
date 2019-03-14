---
title: ASP.NET Core 2.0 への認証と Id を移行します。
author: scottaddie
description: この記事では、ASP.NET Core 2.0 に移行する ASP.NET Core 1.x の認証と Id の最も一般的な手順について説明します。
ms.author: scaddie
ms.date: 12/18/2018
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: d28b4af483c7ec9d6cff6db3e2f1693e765d4202
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57063019"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a>ASP.NET Core 2.0 への認証と Id を移行します。

によって[Scott Addie](https://github.com/scottaddie)と[Hao 力](https://github.com/HaoK)

ASP.NET Core 2.0 が認証用の新しいモデルおよび[Identity](xref:security/authentication/identity)サービスを使用して構成を簡略化します。 認証または Id を使用する ASP.NET Core 1.x アプリケーションは、以下に示すとおり、新しいモデルを使用して更新できます。

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a>認証ミドルウェアとサービス
1.x プロジェクトでは、認証はミドルウェアを介して構成されます。 サポートする各認証スキーム ミドルウェア メソッドは呼び出されます。

次の 1.x の例では、Facebook 認証を構成で Id を持つ*Startup.cs*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.UseIdentity();
    app.UseFacebookAuthentication(new FacebookOptions { 
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
} 
```

2.0 プロジェクトでサービスを使用して認証が構成されています。 各認証スキームが登録されている、`ConfigureServices`メソッドの*Startup.cs*します。 `UseIdentity`メソッドが置き換え`UseAuthentication`します。

次の 2.0 の例では、Facebook 認証を構成で Id を持つ*Startup.cs*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak Identity cookies, they're no longer part of IdentityOptions.
    services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options => 
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

`UseAuthentication`メソッドは、自動認証と、リモート認証要求の処理を担当する 1 つの認証ミドルウェア コンポーネントを追加します。 1 つの一般的なミドルウェア コンポーネントをすべての個々 のミドルウェア コンポーネントを置き換えます。

各主要な認証スキーム用の 2.0 の移行手順を次に示します。

### <a name="cookie-based-authentication"></a>Cookie ベースの認証
次の 2 つのオプションのいずれかを選択しに必要な変更を加えます*Startup.cs*:

1. Id で cookie を使用します。
    - 置換`UseIdentity`で`UseAuthentication`で、`Configure`メソッド。

        ```csharp
        app.UseAuthentication();
        ```

    - 呼び出す、`AddIdentity`メソッド、 `ConfigureServices` cookie 認証サービスを追加します。
    - 必要に応じて、呼び出し、`ConfigureApplicationCookie`または`ConfigureExternalCookie`メソッドで、 `ConfigureServices` Id cookie の設定を調整する方法。

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();
    
        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. Identity なしの cookie を使用します。
    - 置換、`UseCookieAuthentication`でメソッドを呼び出す、`Configure`メソッド`UseAuthentication`:
  
        ```csharp
        app.UseAuthentication();
        ```
 
    - 呼び出す、`AddAuthentication`と`AddCookie`メソッド、`ConfigureServices`メソッド。

        ```csharp
        // If you don't want the cookie to be automatically authenticated and assigned to HttpContext.User, 
        // remove the CookieAuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(options => 
                {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a>JWT ベアラー認証
次に変更を加えます*Startup.cs*:
- 置換、`UseJwtBearerAuthentication`でメソッドを呼び出す、`Configure`メソッド`UseAuthentication`:
 
    ```csharp
    app.UseAuthentication();
    ```

- 呼び出す、`AddJwtBearer`メソッドで、`ConfigureServices`メソッド。

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options => 
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    このコード スニペットが Id を使用しないのは、渡すことによって、既定のスキームを設定する必要があるため`JwtBearerDefaults.AuthenticationScheme`を`AddAuthentication`メソッド。

### <a name="openid-connect-oidc-authentication"></a>OpenID Connect (OIDC) 認証
次に変更を加えます*Startup.cs*:

- 置換、`UseOpenIdConnectAuthentication`でメソッドを呼び出す、`Configure`メソッド`UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- 呼び出す、`AddOpenIdConnect`メソッドで、`ConfigureServices`メソッド。

    ```csharp
    services.AddAuthentication(options => 
    {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options => 
    {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

### <a name="facebook-authentication"></a>Facebook での認証
次に変更を加えます*Startup.cs*:
- 置換、`UseFacebookAuthentication`でメソッドを呼び出す、`Configure`メソッド`UseAuthentication`:
 
    ```csharp
    app.UseAuthentication();
    ```

- 呼び出す、`AddFacebook`メソッドで、`ConfigureServices`メソッド。
    
    ```csharp
    services.AddAuthentication()
            .AddFacebook(options => 
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a>Google での認証
次に変更を加えます*Startup.cs*:
- 置換、`UseGoogleAuthentication`でメソッドを呼び出す、`Configure`メソッド`UseAuthentication`:
 
    ```csharp
    app.UseAuthentication();
    ```

- 呼び出す、`AddGoogle`メソッドで、`ConfigureServices`メソッド。

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options => 
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });    
    ```

### <a name="microsoft-account-authentication"></a>Microsoft アカウント認証
次に変更を加えます*Startup.cs*:
- 置換、`UseMicrosoftAccountAuthentication`でメソッドを呼び出す、`Configure`メソッド`UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- 呼び出す、`AddMicrosoftAccount`メソッドで、`ConfigureServices`メソッド。

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options => 
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ``` 

### <a name="twitter-authentication"></a>Twitter での認証
次に変更を加えます*Startup.cs*:
- 置換、`UseTwitterAuthentication`でメソッドを呼び出す、`Configure`メソッド`UseAuthentication`:
 
    ```csharp
    app.UseAuthentication();
    ```

- 呼び出す、`AddTwitter`メソッドで、`ConfigureServices`メソッド。

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options => 
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a>認証スキームの既定の設定
1.x で、`AutomaticAuthenticate`と`AutomaticChallenge`のプロパティ、 [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1)基底クラスが 1 つの認証スキームに設定する対象としています。 これを強制する良い方法はありませんでした。

個々 のプロパティとして 2.0 では、これら 2 つのプロパティが削除されました`AuthenticationOptions`インスタンス。 構成することができますが、`AddAuthentication`内でメソッドの呼び出し、`ConfigureServices`メソッドの*Startup.cs*:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

上記のコード スニペットでは、既定のスキームに設定されて`CookieAuthenticationDefaults.AuthenticationScheme`(「クッキー」)。

またはのオーバー ロード バージョンを使用して、`AddAuthentication`メソッドは、複数のプロパティを設定します。 次のオーバー ロードされたメソッドの例では、既定のスキームに設定されて`CookieAuthenticationDefaults.AuthenticationScheme`します。 認証スキームは、個別にまたは指定できます`[Authorize]`属性または承認ポリシー。

```csharp
services.AddAuthentication(options => 
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

次の条件のいずれかが true の場合は、2.0 で既定のスキームを定義します。
- ユーザーが自動的にサインインします。
- 使用する、`[Authorize]`スキームを指定せずに属性または承認のポリシー

このルールの例外は、`AddIdentity`メソッド。 このメソッドでは、cookie を追加して、既定の認証し、アプリケーション cookie にスキームの課題のセットの`IdentityConstants.ApplicationScheme`します。 さらに、外部の cookie を既定のサインイン方式を設定`IdentityConstants.ExternalScheme`します。

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a>HttpContext の認証拡張機能を使用します。
`IAuthenticationManager`インターフェイスは 1.x の認証システムにメイン エントリ ポイントです。 新しいセットで置き換え`HttpContext`の拡張メソッド、`Microsoft.AspNetCore.Authentication`名前空間。

たとえば、1.x プロジェクトを参照する`Authentication`プロパティ。

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

2.0 プロジェクトでは、インポート、`Microsoft.AspNetCore.Authentication`名前空間、および削除、`Authentication`プロパティの参照。

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a>Windows 認証 (HTTP.sys/IISIntegration)
Windows 認証の 2 つのバリエーションがあります。
1. ホストでは、認証されたユーザーのみで許可します。
2. により、ホストし、認証されたユーザー

上記で説明した最初のコマンドは、2.0 の変更による影響はありません。

上記で説明した 2 つ目のバリエーションは 2.0 の変更の影響を受けます。 例として、する可能性があること匿名ユーザーに、IIS でアプリまたは[HTTP.sys](xref:fundamentals/servers/httpsys)レイヤーのコント ローラー レベルで、承認ユーザー。 このシナリオで、既定のスキームを設定`IISDefaults.AuthenticationScheme`で、`Startup.ConfigureServices`メソッド。

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

それに応じてにより、作業からチャレンジを承認要求を既定のスキームを設定できません。

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a>IdentityCookieOptions インスタンス
2.0 の変更の副作用は、という名前の cookie のオプションのインスタンスではなくオプションを使用するスイッチです。 Id cookie のスキーム名をカスタマイズする機能が削除されます。

たとえば、1.x プロジェクトを使用[コンス トラクターの挿入](xref:mvc/controllers/dependency-injection#constructor-injection)を渡す、`IdentityCookieOptions`パラメーターに*AccountController.cs*します。 外部の cookie 認証スキームは、指定されたインスタンスからアクセスされます。

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

前述のコンス トラクターの挿入が 2.0 プロジェクトで不要になると、`_externalCookieScheme`フィールドを削除できます。

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

`IdentityConstants.ExternalScheme`定数を直接使用することができます。

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a>IdentityUser POCO ナビゲーション プロパティを追加します。
Entity Framework (EF) Core ナビゲーションのプロパティ ベースの`IdentityUser`POCO (Plain Old CLR Object) が削除されました。 1.x プロジェクトでは、これらのプロパティを使用する場合は、この 2.0 プロジェクトに追加手動でします。

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<IdentityUserRole<int>> Roles { get; } = new List<IdentityUserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<IdentityUserClaim<int>> Claims { get; } = new List<IdentityUserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<IdentityUserLogin<int>> Logins { get; } = new List<IdentityUserLogin<int>>();
```

外部キーの重複を防ぐためには、EF Core の移行を実行するときに、追加するには、次の`IdentityDbContext`クラス`OnModelCreating`メソッド (後に、`base.OnModelCreating();`呼び出す)。

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the ASP.NET Core Identity model and override the defaults if needed.
    // For example, you can rename the ASP.NET Core Identity table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a>GetExternalAuthenticationSchemes を置き換えます
同期メソッド`GetExternalAuthenticationSchemes`非同期バージョンを優先して削除されました。 1.x プロジェクトに次のコードがある*ManageController.cs*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

このメソッドでは、 *Login.cshtml*すぎます。

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?range=62,75-84)]

2.0 プロジェクトで使用して、`GetExternalAuthenticationSchemesAsync`メソッド。

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

*Login.cshtml*、`AuthenticationScheme`でアクセスされるプロパティ、`foreach`ループに変更`Name`:

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?range=62,75-84)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a>ManageLoginsViewModel プロパティの変更
A`ManageLoginsViewModel`でオブジェクトを使用して、`ManageLogins`のアクション*ManageController.cs*します。 1.x プロジェクトで、オブジェクトの`OtherLogins`プロパティの戻り型が`IList<AuthenticationDescription>`します。 この戻り値の型がインポートする必要があります`Microsoft.AspNetCore.Http.Authentication`:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

2.0 プロジェクトでは、戻り値の型の変更`IList<AuthenticationScheme>`します。 この新しい戻り値の型は、置き換える必要があります、`Microsoft.AspNetCore.Http.Authentication`とインポートを`Microsoft.AspNetCore.Authentication`をインポートします。

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a>その他の技術情報
追加の詳細情報とディスカッションでは、次を参照してください。、 [Auth 2.0 のディスカッション](https://github.com/aspnet/Security/issues/1338)GitHub 上の問題。
