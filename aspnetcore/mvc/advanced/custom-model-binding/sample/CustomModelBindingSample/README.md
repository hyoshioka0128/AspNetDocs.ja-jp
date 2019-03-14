---
ms.openlocfilehash: 71a40fcab8f1d1005cbe9327d01a42484765a421
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57059639"
---
# <a name="custom-model-binding-demo"></a>カスタム モデル バインド デモ

アプリを実行し、base64 でエンコードされた文字列を `ImageController` エンドポイント (`/api/image/`) に POST することによって、`ByteArrayModelBinder` をテストします。 要求の本文でファイルとファイル名のプロパティをフォーム データとして指定します ([Postman](https://www.getpostman.com/) または同様のツールを使用します)。 [この文字列のサンプル](Base64String.txt)を使用することができます。 結果は、指定したファイル名で *wwwroot/images/upload* フォルダーに保存されます。

カスタム バインドの例をテストするには、次のエンドポイントを試してください。

* /api/authors/1
* /api/authors/2 (NOT FOUND)
* /api/boundauthors/1
* /api/boundauthors/2 (NOT FOUND)
* /api/boundauthors/get/1
* /api/boundauthors/get/2 (NO CONTENT) &ndash; このアクションは null をチェックせず、*404 Not Found* を返します。
