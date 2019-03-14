---
uid: web-forms/overview/ajax-control-toolkit/accordion/databinding-to-an-accordion-cs
title: (C#) アコーディオンにデータ バインドする |Microsoft Docs
author: wenz
description: AJAX Control Toolkit で、アコーディオン コントロールは、複数のペインを示し、うち 1 つを同時に表示できます。 パネルには、w を宣言は、通常は.
ms.author: riande
ms.date: 06/02/2008
ms.assetid: 9c8f0054-e319-46f8-80c0-35b606d2fbd4
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/accordion/databinding-to-an-accordion-cs
msc.type: authoredcontent
ms.openlocfilehash: 930392bd33cfeb7dec52a6084a5d401a6134a7ef
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57024639"
---
<a name="databinding-to-an-accordion-c"></a>アコーディオンにデータバインドする (C#)
====================
によって[Christian Wenz](https://github.com/wenz)

[コードのダウンロード](http://download.microsoft.com/download/5/6/d/56d50cef-2011-4c8f-9891-7edc6dc57df9/Accordion1.cs.zip)または[PDF のダウンロード](http://download.microsoft.com/download/6/7/1/6718d452-ff89-4d3f-a90e-c74ec2d636a3/accordion1CS.pdf)

> AJAX Control Toolkit で、アコーディオン コントロールは、複数のペインを示し、うち 1 つを同時に表示できます。 パネルは通常、ページ自体内で宣言が、データ ソースへのバインドは柔軟性を提供します。


## <a name="overview"></a>概要

AJAX Control Toolkit で、アコーディオン コントロールは、複数のペインを示し、うち 1 つを同時に表示できます。 パネルは通常、ページ自体内で宣言が、データ ソースへのバインドは柔軟性を提供します。

## <a name="steps"></a>手順

まず、データ ソースが必要です。 このサンプルでは、AdventureWorks データベースと Microsoft SQL Server 2005 Express Edition を使用します。 データベース (express edition を含む)、Visual Studio のインストールのオプションの一部であるし、下の個別のダウンロードとしても利用[ https://go.microsoft.com/fwlink/?LinkId=64064](https://go.microsoft.com/fwlink/?LinkId=64064)します。 AdventureWorks データベースが SQL Server 2005 サンプルとサンプル データベースの一部 (ダウンロード[ https://www.microsoft.com/downloads/details.aspx?FamilyID=e719ecf7-9f46-4312-af89-6ad8702e4e6e&amp; DisplayLang = en](https://www.microsoft.com/downloads/details.aspx?FamilyID=e719ecf7-9f46-4312-af89-6ad8702e4e6e&amp;DisplayLang=en))。 データベースをセットアップする最も簡単な方法は、Microsoft SQL Server Management Studio Express を使用する、([https://www.microsoft.com/downloads/details.aspx?FamilyID=c243a5ae-4bd1-4e3d-94b8-5a0f62bf7796&amp; DisplayLang = en](https://www.microsoft.com/downloads/details.aspx?FamilyID=c243a5ae-4bd1-4e3d-94b8-5a0f62bf7796&amp;DisplayLang=en)) とアタッチ、`AdventureWorks.mdf`データベース ファイル。

このサンプルでは、SQL Server 2005 Express Edition のインスタンスが呼び出されること前提としています`SQLEXPRESS`は web サーバーと同じコンピューター上に存在して、既定の設定にもなります。 場合は、セットアップが異なる場合は、データベースの接続情報を調整する必要があります。

ASP.NET AJAX Control Toolkit の機能をアクティブ化するために、`ScriptManager`コントロールは、ページのどこでも配置する必要があります (ただし内、`<form>`要素)。

[!code-aspx[Main](databinding-to-an-accordion-cs/samples/sample1.aspx)]

次に、ページにデータ ソースを追加します。 少量のデータを使用するためにはのみ、AdventureWorks データベースの Vendor テーブルの最初の 5 つのエントリを選択します。 データ ソースを作成するには、Visual Studio アシスタントを使用する場合は、現在のバージョンのバグは、テーブル名のプレフィックスにしないことに注意してください。 (`Vendor`) と`Purchasing`します。 次のマークアップは、正しい構文を示しています。

[!code-aspx[Main](databinding-to-an-accordion-cs/samples/sample2.aspx)]

データ ソースの名前 (ID) に注意してください。 この非常に id で使用する必要があります、`DataSourceID`アコーディオン コントロールのプロパティ。

[!code-aspx[Main](databinding-to-an-accordion-cs/samples/sample3.aspx)]

アコーディオン コントロール内では、さまざまなパーツのヘッダーを含む、コントロールのテンプレートを行うことができます (`<HeaderTemplate>`) とコンテンツ (`<ContentTemplate>`)。 これらの要素内でデータからデータを出力だけを使用して、ソース、`DataBinder.Eval()`メソッド。

[!code-aspx[Main](databinding-to-an-accordion-cs/samples/sample4.aspx)]

ページが読み込まれるときに、このサーバー側コードとアコーディオンにデータ ソースをバインドする必要があります。

[!code-aspx[Main](databinding-to-an-accordion-cs/samples/sample5.aspx)]

このサンプルを完了するまでにはアコーディオン コントロールで参照されている 2 つの CSS クラスを定義する必要があります (のプロパティで`HeaderCssClass`と`ContentCssClass`)。 次のマークアップを格納、`<head>`ページのセクション。

[!code-css[Main](databinding-to-an-accordion-cs/samples/sample6.css)]


[![アコーディオンにデータがデータ ソースから直接取得されます。](databinding-to-an-accordion-cs/_static/image2.png)](databinding-to-an-accordion-cs/_static/image1.png)

アコーディオンにデータがデータ ソースから直接取得されます ([フルサイズの画像を表示する をクリックします](databinding-to-an-accordion-cs/_static/image3.png))。

> [!div class="step-by-step"]
> [次へ](dynamically-adding-an-accordion-pane-cs.md)
