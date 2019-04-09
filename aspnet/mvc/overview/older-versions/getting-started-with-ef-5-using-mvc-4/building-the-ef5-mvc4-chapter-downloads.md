---
uid: mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/building-the-ef5-mvc4-chapter-downloads
title: EF 5 mvc 4 のチュートリアル」の章を構築ダウンロード |Microsoft Docs
author: Rick-Anderson
description: Contoso University のサンプルの web アプリケーションでは、Entity Framework 5 Code First と Visual Studio を使用して ASP.NET MVC 4 アプリケーションを作成する方法について説明しています.
ms.author: riande
ms.date: 07/30/2013
ms.assetid: d0a89089-eed8-4f61-a478-c5ffa30186f5
msc.legacyurl: /mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/building-the-ef5-mvc4-chapter-downloads
msc.type: authoredcontent
ms.openlocfilehash: e90eebaba3645802f318dbf449c3ec734265a092
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59381953"
---
# <a name="building-the-chapter-downloads-for-the-ef-5-mvc-4-tutorials"></a>EF 5 mvc 4 のチュートリアル」の章を構築ダウンロードします。

によって[Rick Anderson]((https://twitter.com/RickAndMSFT))

[完成したプロジェクトをダウンロードします。](http://code.msdn.microsoft.com/Getting-Started-with-dd0e2ed8)

> Contoso University のサンプルの web アプリケーションでは、Entity Framework 5 Code First と Visual Studio 2012 を使用して ASP.NET MVC 4 アプリケーションを作成する方法を示します。 チュートリアル シリーズについては、[シリーズの最初のチュートリアル](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md)をご覧ください。


## <a name="building-the-chapter-downloads"></a>章ダウンロードの構築

1. ダウンロードしてプロジェクト サンプルの zip ファイルを解凍します。 解凍したダウンロード パッケージに追加の zip ファイル、各章の完了のいずれかが表示されます。
2. 目的の zip ファイルを右クリック**プロパティ**、 をクリックし、**ブロックの解除**ボタンをクリックします。  
  
    ![](building-the-ef5-mvc4-chapter-downloads/_static/image1.png)
3. ファイルを解凍します。
4. ダブルクリックして、 *CUx.sln*ファイルを Visual Studio を起動します。
5. **ツール** メニューのをクリックして**NuGet パッケージ マネージャー**、し**パッケージ マネージャー コンソール**します。  
  
    ![](building-the-ef5-mvc4-chapter-downloads/_static/image2.png)
6. パッケージ マネージャー コンソール (PMC) をクリックして**復元**します。  
  
    ![](building-the-ef5-mvc4-chapter-downloads/_static/image3.png)
7. Visual Studio を終了します。
8. 上記の手順で終了するソリューション ファイルを開き、Visual Studio を再起動します。
9. パッケージ マネージャー コンソール (PMC) を入力、`Update-Database`コマンド。  
  
    ![](building-the-ef5-mvc4-chapter-downloads/_static/image4.png)  

    > [!NOTE]
    > : 次のエラーが発生した場合  
    >   
    >  *'データベースの更新' という用語はコマンドレット、関数、スクリプト ファイル、または操作可能なプログラムの名前として認識されません。 名前が正しく記述されていることを確認し、パスが含まれている場合はそのパスが正しいことを確認してから、再試行してください。*  
    > 終了し、Visual Studio を再起動します。

    各移行を実行し、seed メソッドが実行されます。 アプリを実行できます。

    ![](building-the-ef5-mvc4-chapter-downloads/_static/image5.png)

> [!div class="step-by-step"]
> [前へ](advanced-entity-framework-scenarios-for-an-mvc-web-application.md)
