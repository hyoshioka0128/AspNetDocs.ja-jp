---
ms.openlocfilehash: e5c80c80380dadfce9cff5ec268535076258d980
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57041119"
---
<span data-ttu-id="e42d8-101">Identity scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="e42d8-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e42d8-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e42d8-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e42d8-103">**ソリューション エクスプ ローラー**、プロジェクトを右クリックして >**追加** > **スキャフォールディングされた新しい項目**します。</span><span class="sxs-lookup"><span data-stu-id="e42d8-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="e42d8-104">左側のウィンドウから、**スキャフォールディングの追加**ダイアログ ボックスで、 **Identity** > **追加**します。</span><span class="sxs-lookup"><span data-stu-id="e42d8-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **ADD**.</span></span>
* <span data-ttu-id="e42d8-105">**ADD アイデンティティ**ダイアログ ボックスで、オプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="e42d8-105">In the **ADD Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="e42d8-106">既存のレイアウト ページを選択するか、不適切なマークアップで、レイアウト ファイルが上書きされます。</span><span class="sxs-lookup"><span data-stu-id="e42d8-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="e42d8-107">たとえば`~/Pages/Shared/_Layout.cshtml`Razor ページの`~/Views/Shared/_Layout.cshtml`MVC プロジェクト</span><span class="sxs-lookup"><span data-stu-id="e42d8-107">For example `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
  * <span data-ttu-id="e42d8-108">選択、 **+** 新たに作成するボタン**データ コンテキスト クラス**します。</span><span class="sxs-lookup"><span data-stu-id="e42d8-108">Select the **+** button to create a new **Data context class**.</span></span>
* <span data-ttu-id="e42d8-109">選択**追加**します。</span><span class="sxs-lookup"><span data-stu-id="e42d8-109">Select **ADD**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="e42d8-110">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="e42d8-110">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e42d8-111">ASP.NET Core scaffolder を以前インストールしていない場合は、今すぐインストールします。</span><span class="sxs-lookup"><span data-stu-id="e42d8-111">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```cli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="e42d8-112">パッケージ参照を追加[Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)プロジェクト (\*.csproj) ファイル。</span><span class="sxs-lookup"><span data-stu-id="e42d8-112">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (\*.csproj) file.</span></span> <span data-ttu-id="e42d8-113">プロジェクト ディレクトリに、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e42d8-113">Run the following command in the project directory:</span></span>

```cli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="e42d8-114">Identity scaffolder オプションを一覧表示するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e42d8-114">Run the following command to list the Identity scaffolder options:</span></span>

```cli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="e42d8-115">プロジェクト フォルダー内には、必要なオプションで Identity scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="e42d8-115">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="e42d8-116">たとえば、既定値は、UI id とファイルの最小数を設定するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e42d8-116">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```cli
dotnet aspnet-codegenerator identity --useDefaultUI
```

-------------
