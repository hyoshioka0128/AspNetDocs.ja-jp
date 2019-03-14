---
uid: web-forms/videos/how-do-i/how-do-i-export-data-to-a-comma-delimited-csv-file-for-an-application-like-excel
title: '[How Do i:]Excel のようなアプリケーションのデータをコンマ区切り (CSV) ファイルにエクスポート |Microsoft Docs'
author: rick-anderson
description: このビデオでは、Chris Pels は、データベースまたはその他のソースからデータを取得し、アプリケーション、li で使用できるコンマ区切りファイルにエクスポートする方法を説明しています.
ms.author: riande
ms.date: 01/22/2009
ms.assetid: c9df86ad-aec2-43d5-bb8a-413ebb666673
msc.legacyurl: /web-forms/videos/how-do-i/how-do-i-export-data-to-a-comma-delimited-csv-file-for-an-application-like-excel
msc.type: video
ms.openlocfilehash: 401df30b8f35355ecca5226883bb16b46bf88507
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57064209"
---
<a name="how-do-i-export-data-to-a-comma-delimited-csv-file-for-an-application-like-excel"></a><span data-ttu-id="a8dc7-103">[How Do i:]Excel のようなアプリケーションのデータをコンマ区切り (CSV) ファイルにエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="a8dc7-103">[How Do I:] Export Data to a Comma Delimited (CSV) File for an Application Like Excel</span></span>
====================
<span data-ttu-id="a8dc7-104">によって[Chris Pels](https://twitter.com/chrispels)</span><span class="sxs-lookup"><span data-stu-id="a8dc7-104">by [Chris Pels](https://twitter.com/chrispels)</span></span>

<span data-ttu-id="a8dc7-105">このビデオでは、Chris Pels は、データベースまたはその他のソースからデータを取得し、Excel などのアプリケーションで使用できるコンマ区切りファイルにエクスポートする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="a8dc7-105">In this video Chris Pels shows how to take data from a database or other source and export it to a comma delimited file that can be used in an application like Excel.</span></span> <span data-ttu-id="a8dc7-106">最初に、一連のデータは、DataTable オブジェクトとして作成されます。</span><span class="sxs-lookup"><span data-stu-id="a8dc7-106">First, a set of data is created as a DataTable object.</span></span> <span data-ttu-id="a8dc7-107">次に、現在の web ページ要求に対する応答がオフになってされ、ヘッダーとコンテンツの種類は、csv ファイルに構成されます。</span><span class="sxs-lookup"><span data-stu-id="a8dc7-107">Next, the Response for the current web page request is cleared and the header and content type are configured to be a csv file.</span></span> <span data-ttu-id="a8dc7-108">実際のデータは、最初にデータ値の後に、csv ファイルの列ヘッダーを記述することで、応答ストリームに追加されます。</span><span class="sxs-lookup"><span data-stu-id="a8dc7-108">Then the actual data is added to the response stream by first writing the column headers for the csv file followed by the data values.</span></span> <span data-ttu-id="a8dc7-109">このアプローチは、ユーザーは、Excel などのプログラムでローカルで操作できるように、データのエクスポートを必要とするときに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="a8dc7-109">This approach can be useful when users require an export of data so it can be manipulated locally in a program like Excel.</span></span>

[<span data-ttu-id="a8dc7-110">&#9654;(19 分) のビデオを見る</span><span class="sxs-lookup"><span data-stu-id="a8dc7-110">&#9654; Watch video (19 minutes)</span></span>](https://channel9.msdn.com/Blogs/ASP-NET-Site-Videos/how-do-i-export-data-to-a-comma-delimited-csv-file-for-an-application-like-excel)
