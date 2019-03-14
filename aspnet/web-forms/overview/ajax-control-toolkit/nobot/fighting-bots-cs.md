---
uid: web-forms/overview/ajax-control-toolkit/nobot/fighting-bots-cs
title: (C#) ボットと戦う |Microsoft Docs
author: wenz
description: 自動のボット雇いスパム、ユーザーが介入せずコメント フォームを送信すると、web ログやその他の web サイトを設置します。 ASP.NET AJAX の Con で NoBot コントロール.
ms.author: riande
ms.date: 06/02/2008
ms.assetid: 0a1917e0-884a-4576-8e93-9ed660faae51
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/nobot/fighting-bots-cs
msc.type: authoredcontent
ms.openlocfilehash: 52ed34e7640cd125a3b4c3b50ab760a7c1d713f1
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57035399"
---
<a name="fighting-bots-c"></a>ボットと戦う (C#)
====================
によって[Christian Wenz](https://github.com/wenz)

[コードのダウンロード](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/NoBot0.cs.zip)または[PDF のダウンロード](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/nobot0CS.pdf)

> 自動のボット雇いスパム、ユーザーが介入せずコメント フォームを送信すると、web ログやその他の web サイトを設置します。 ASP.NET AJAX Control Toolkit で NoBot コントロールは、こうしたボットの対策に役立つことができます。


## <a name="overview"></a>概要

自動のボット雇いスパム、ユーザーが介入せずコメント フォームを送信すると、web ログやその他の web サイトを設置します。 ASP.NET AJAX Control Toolkit で NoBot コントロールは、こうしたボットの対策に役立つことができます。

## <a name="steps"></a>手順

ボットを無効にする 1 つの一般的なアプローチでは、コンピューターと人間離れているかを知らせるな Captcha 完全に自動化されたパブリック チューリング テストを使用します。 チューリング テストがもともとテスト通信パートナーが、人間またはマシンかどうかを判断だれかが必要な場所。 Web、CAPTCHA は一部の歪んで表示される文字を使用してイメージの通常されています。 考え方としては OCR アルゴリズムは失敗しますが、人間だけが、イメージ上の文字を読み取ることができます。

いくつかの利点と、このアプローチに欠点があるが、これの詳細については、このチュートリアルの範囲外です。 ただしは同様のアプローチを提供する ASP.NET AJAX Control toolkit コントロール:`NoBot`します。 場所と見なされます成功最も迷惑メールの送信試行のブログなどの web サイトで非常に優れた運賃は行われず、および、CAPTCHA よりを克服する方が簡単ですが、非常に簡単に使用される、`NoBot`制御を行うことができます。

`NoBot` これらの条件の少なくとも 1 つが満たされる場合は、現在の ASP.NET web フォームのポストバックをインターセプトします。

- ブラウザーが JavaScript パズルを解くに失敗した (たとえば JavaScript が非アクティブ化時に)
- ユーザーには、高速にフォームが送信されました
- クライアントの IP アドレスは、一定期間で多くの場合、フォームを送信します。

これらの条件を確認するには、`NoBot`コントロールには、これらの属性 (すべての省略可能) が必要です。

- `ResponseMinimumDelaySeconds` 最小限のポストバック間隔 (秒)
- `CutoffWindowSeconds` 1 つの IP からのポストバックがメジャーとなる時間間隔の長さ
- `CutoffMaximumInstances` 最長 1 時間間隔 (秒)

ポストバック間で、次のマークアップ要求するには少なくとも 2 秒が経過し、5 つのみのポストバックが 30 秒の間隔内で以下。

[!code-aspx[Main](fighting-bots-cs/samples/sample1.aspx)]

通常どおりを含めるように、 `ScriptManager`  ページで、ASP.NET AJAX ライブラリが読み込まれ、Control Toolkit を使用できるように。

[!code-aspx[Main](fighting-bots-cs/samples/sample2.aspx)]

チェックの多く`NoBot`が行っている、サーバー側で発生する、これらの検証の結果を確認する必要があります。 これを行う呼び出し`NoBot`の`IsValid()`メソッド。 1 つの引数がある (として、`out`パラメーター/`ByRef`パラメーター) 型の`NoBotState`します。 チェックが失敗したときに、その文字列形式には理由が含まれますと`Valid`それ以外の場合。 次のコードによるのメッセージを出力する`NoBot`'s が発生します。

[!code-aspx[Main](fighting-bots-cs/samples/sample3.aspx)]

最後に、フォームを送信して、メッセージを出力するラベル要素が必要ですし、完了したら!

[!code-aspx[Main](fighting-bots-cs/samples/sample4.aspx)]

このスクリプトを実行し、JavaScript を非アクティブ化または最初の 2 秒内で、フォームの送信や 7 回 30 秒以内にフォームを送信、ときに、エラー メッセージが表示されます。 このコントロールを使用して、賢明なただし、そのためユーザーの 5 ~ 10% が失敗ユーザーの約 90 ~ 95% の JavaScript のアクティブ化されるため、 `NoBot`'s をテストします。


[![このエラー メッセージをボットで発生する可能性があります。](fighting-bots-cs/_static/image2.png)](fighting-bots-cs/_static/image1.png)

このエラー メッセージをボットで発生する可能性があります ([フルサイズの画像を表示する をクリックします](fighting-bots-cs/_static/image3.png))。

> [!div class="step-by-step"]
> [次へ](fighting-bots-vb.md)
