---
ms.openlocfilehash: 71a40fcab8f1d1005cbe9327d01a42484765a421
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57059639"
---
# <a name="custom-model-binding-demo"></a><span data-ttu-id="58481-101">カスタム モデル バインド デモ</span><span class="sxs-lookup"><span data-stu-id="58481-101">Custom Model Binding Demo</span></span>

<span data-ttu-id="58481-102">アプリを実行し、base64 でエンコードされた文字列を `ImageController` エンドポイント (`/api/image/`) に POST することによって、`ByteArrayModelBinder` をテストします。</span><span class="sxs-lookup"><span data-stu-id="58481-102">Test `ByteArrayModelBinder` by running the app and POSTing a base64-encoded string to the `ImageController` endpoint (`/api/image/`).</span></span> <span data-ttu-id="58481-103">要求の本文でファイルとファイル名のプロパティをフォーム データとして指定します ([Postman](https://www.getpostman.com/) または同様のツールを使用します)。</span><span class="sxs-lookup"><span data-stu-id="58481-103">Specify the file and filename properties in the request body as form-data (using [Postman](https://www.getpostman.com/) or a similar tool).</span></span> <span data-ttu-id="58481-104">[この文字列のサンプル](Base64String.txt)を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="58481-104">You can use [this sample string](Base64String.txt).</span></span> <span data-ttu-id="58481-105">結果は、指定したファイル名で *wwwroot/images/upload* フォルダーに保存されます。</span><span class="sxs-lookup"><span data-stu-id="58481-105">The result is saved in the *wwwroot/images/upload* folder with the filename specified.</span></span>

<span data-ttu-id="58481-106">カスタム バインドの例をテストするには、次のエンドポイントを試してください。</span><span class="sxs-lookup"><span data-stu-id="58481-106">To test the custom binding example, try the following endpoints:</span></span>

* <span data-ttu-id="58481-107">/api/authors/1</span><span class="sxs-lookup"><span data-stu-id="58481-107">/api/authors/1</span></span>
* <span data-ttu-id="58481-108">/api/authors/2 (NOT FOUND)</span><span class="sxs-lookup"><span data-stu-id="58481-108">/api/authors/2 (NOT FOUND)</span></span>
* <span data-ttu-id="58481-109">/api/boundauthors/1</span><span class="sxs-lookup"><span data-stu-id="58481-109">/api/boundauthors/1</span></span>
* <span data-ttu-id="58481-110">/api/boundauthors/2 (NOT FOUND)</span><span class="sxs-lookup"><span data-stu-id="58481-110">/api/boundauthors/2 (NOT FOUND)</span></span>
* <span data-ttu-id="58481-111">/api/boundauthors/get/1</span><span class="sxs-lookup"><span data-stu-id="58481-111">/api/boundauthors/get/1</span></span>
* <span data-ttu-id="58481-112">/api/boundauthors/get/2 (NO CONTENT) &ndash; このアクションは null をチェックせず、*404 Not Found* を返します。</span><span class="sxs-lookup"><span data-stu-id="58481-112">/api/boundauthors/get/2 (NO CONTENT) &ndash; This action doesn't check for null and returns a *404 Not Found*.</span></span>
