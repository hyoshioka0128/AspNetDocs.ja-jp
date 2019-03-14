---
ms.openlocfilehash: f323482d6f8bfaebf7bf6673d5fb91608430760a
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57044079"
---
## <a name="add-initial-migration-and-update-the-database"></a><span data-ttu-id="8a867-101">最初の移行を追加して、データベースの更新</span><span class="sxs-lookup"><span data-stu-id="8a867-101">Add initial migration and update the database</span></span>

* <span data-ttu-id="8a867-102">コマンド プロンプトを開き、プロジェクト ディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="8a867-102">Open a command prompt and navigate to the project directory.</span></span> <span data-ttu-id="8a867-103">(ディレクトリを含む、 *Startup.cs*ファイル)。</span><span class="sxs-lookup"><span data-stu-id="8a867-103">(The directory containing the *Startup.cs* file).</span></span>

* <span data-ttu-id="8a867-104">コマンド プロンプトで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="8a867-104">Run the following commands in the command prompt:</span></span>

  ```console
  dotnet restore
  dotnet ef migrations add Initial
  dotnet ef database update
  ```
  
  <span data-ttu-id="8a867-105">[.NET core](/dotnet/core/tools/index) .NET のクロスプラット フォーム対応の実装です。</span><span class="sxs-lookup"><span data-stu-id="8a867-105">[.NET Core](/dotnet/core/tools/index) is a cross-platform implementation of .NET.</span></span> <span data-ttu-id="8a867-106">これらのコマンドの実行内容を次に示します。</span><span class="sxs-lookup"><span data-stu-id="8a867-106">Here is what these commands do:</span></span>

  * <span data-ttu-id="8a867-107">[dotnet restore](/dotnet/core/tools/dotnet-restore):指定された NuGet パッケージをダウンロード、 *.csproj*ファイル。</span><span class="sxs-lookup"><span data-stu-id="8a867-107">[dotnet restore](/dotnet/core/tools/dotnet-restore): Downloads the NuGet packages specified in the *.csproj* file.</span></span>
  * <span data-ttu-id="8a867-108">`dotnet ef migrations add Initial` Entity Framework の .NET Core CLI の移行コマンドを実行し、最初の移行を作成します。</span><span class="sxs-lookup"><span data-stu-id="8a867-108">`dotnet ef migrations add Initial` Runs the Entity Framework .NET Core CLI migrations command and creates the initial migration.</span></span> <span data-ttu-id="8a867-109">パラメーターの"add"後では、移行に割り当てる名前です。</span><span class="sxs-lookup"><span data-stu-id="8a867-109">The parameter after "add" is a name that you assign to the migration.</span></span> <span data-ttu-id="8a867-110">ここで名前を付ける移行「初期」初期データベースの移行があるためです。</span><span class="sxs-lookup"><span data-stu-id="8a867-110">Here you're naming the migration "Initial" because it's the initial database migration.</span></span> <span data-ttu-id="8a867-111">この操作を作成、 */データの移行/\<日付と時刻 > _Initial.cs*ファイルを追加する、移行コマンドを含む、*ムービー*テーブルがデータベースにします。</span><span class="sxs-lookup"><span data-stu-id="8a867-111">This operation creates the *Data/Migrations/\<date-time>_Initial.cs* file containing the migration commands to add the *Movie* table to the database.</span></span>
  * <span data-ttu-id="8a867-112">`dotnet ef database update`  先ほど作成した移行では、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="8a867-112">`dotnet ef database update`  Updates the database with the migration we just created.</span></span>

<span data-ttu-id="8a867-113">データベースと接続文字列について次のチュートリアルでします。</span><span class="sxs-lookup"><span data-stu-id="8a867-113">You'll learn about the database and connection string in the next tutorial.</span></span> <span data-ttu-id="8a867-114">データ モデルの変更について説明します、[フィールドを追加する](xref:tutorials/first-mvc-app/new-field)チュートリアル。</span><span class="sxs-lookup"><span data-stu-id="8a867-114">You'll learn about data model changes in the [Add a field](xref:tutorials/first-mvc-app/new-field) tutorial.</span></span>
