---
uid: web-forms/videos/how-do-i/how-do-i-use-the-reponsefilter-property-to-replace-html-in-an-aspnet-page
title: '[How Do i:]Reponse.Filter プロパティを使用して、ASP.NET ページの HTML を置き換える |Microsoft Docs'
author: rick-anderson
description: このビデオの Chris Pels で Reponse.Filter プロパティを使用してをインターセプトし、ページに送信される HTML を変更する方法を示しています。 まず、w サンプル ページを作成しています.
ms.author: riande
ms.date: 01/29/2009
ms.assetid: 3e5ae74a-9798-47d8-a2b3-0d8ad42dd4bc
msc.legacyurl: /web-forms/videos/how-do-i/how-do-i-use-the-reponsefilter-property-to-replace-html-in-an-aspnet-page
msc.type: video
ms.openlocfilehash: 0e8ae8b62bbb4ac1fc7e942cf3dd0438383cb510
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57065079"
---
<a name="how-do-i-use-the-reponsefilter-property-to-replace-html-in-an-aspnet-page"></a>[How Do i:]Reponse.Filter プロパティを使用して、HTML、ASP.NET ページを置換するには
====================
によって[Chris Pels](https://twitter.com/chrispels)

このビデオの Chris Pels で Reponse.Filter プロパティを使用してをインターセプトし、ページに送信される HTML を変更する方法を示しています。 最初に、サンプル ページは、いくつかの単純なテキストで作成されます。 次に、ユーザーのブラウザーに送信されている現在のストリームの代替ストリームとして使用されるカスタム Stream クラスが作成されます。 そのカスタム ストリーム クラスで、ページの内容を応答ストリームに書き込むし、変更は、ストリームから取得されます。 このカスタム Stream クラスの Write メソッドをカスタマイズして、それによってユーザーのブラウザーへの送信内容を変更する、基本の応答ストリームに HTML を置き換えます。 ページのために Response.Filter プロパティを新しいストリーム クラスが最後に、割り当てられている\_読み込みイベント、これによって、ページのコンテンツを変更するためのメカニズムを提供します。

[&#9654;(13 分) のビデオを見る](https://channel9.msdn.com/Blogs/ASP-NET-Site-Videos/how-do-i-use-the-reponsefilter-property-to-replace-html-in-an-aspnet-page)
