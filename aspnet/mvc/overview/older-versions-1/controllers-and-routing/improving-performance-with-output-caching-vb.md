---
uid: mvc/overview/older-versions-1/controllers-and-routing/improving-performance-with-output-caching-vb
title: キャッシュ (VB) を出力によるパフォーマンスの向上 |Microsoft Docs
author: microsoft
description: このチュートリアルでは、出力キャッシュを活用して、ASP.NET MVC web アプリケーションのパフォーマンスを大幅に向上する方法を説明します。 あなたが。。。
ms.author: riande
ms.date: 01/27/2009
ms.assetid: 0e7b4d85-2c46-4eaf-b6a8-6cd566a67334
msc.legacyurl: /mvc/overview/older-versions-1/controllers-and-routing/improving-performance-with-output-caching-vb
msc.type: authoredcontent
ms.openlocfilehash: 9402d7e053ef11eeefa92d112b05ec255d5ec6f7
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57033669"
---
<a name="improving-performance-with-output-caching-vb"></a>出力キャッシュでパフォーマンスを改善する (VB)
====================
によって[Microsoft](https://github.com/microsoft)

> このチュートリアルでは、出力キャッシュを活用して、ASP.NET MVC web アプリケーションのパフォーマンスを大幅に向上する方法を説明します。 同じコンテンツは、新しいユーザーがアクションを呼び出すたびに作成する必要はありません、コント ローラー アクションから返される結果をキャッシュする方法について説明します。


このチュートリアルの目的では、出力キャッシュを活用して、ASP.NET MVC アプリケーションのパフォーマンスを大幅に向上する方法について説明します。 出力キャッシュでは、コント ローラーのアクションによって返されるコンテンツをキャッシュすることができます。 これにより、同じコンテンツは同じコント ローラー アクションが呼び出されるたびに生成する必要ありません。

たとえば、ASP.NET MVC アプリケーションがインデックスをという名前のビューでデータベースのレコードの一覧を表示することに想像してください。 通常、ユーザーは、インデックス ビューを返すコント ローラー アクションを呼び出すたびにデータベースのレコード セットする必要がありますデータベースから取得、データベース クエリを実行しています。

場合を出力キャッシュを利用する一方では、すべてのユーザーが同じコント ローラー アクションを呼び出すたびに、データベース クエリの実行を回避できます。 ビューは、コント ローラー アクションから再生成されることがなくキャッシュから取得できます。 サーバー上で動作する冗長な実行を回避するキャッシュ有効。

#### <a name="enabling-output-caching"></a>出力キャッシュを有効にします。

追加して、出力キャッシュを有効にすると、 &lt;OutputCache&gt;属性の個々 のコント ローラー アクションまたはコント ローラー全体クラスのいずれかにします。 たとえば、リスト 1 で、コント ローラーは、Index() という名前のアクションを公開します。 Index() アクションの出力は、10 秒間キャッシュされます。

**Listing 1 – Controllers\HomeController.vb**

[!code-vb[Main](improving-performance-with-output-caching-vb/samples/sample1.vb)]


ASP.NET MVC のベータ バージョンでは、出力キャッシュは機能しませんように URL [ http://www.MySite.com/](http://www.mysite.com/)します。 代わりに、ような URL を入力する必要があります[ http://www.MySite.com/Home/Index](http://www.mysite.com/Home/Index)します。


リストの 1 の Index() アクションの出力は 10 秒間キャッシュされます。 場合は、大幅に長くキャッシュ期間を指定できます。 たとえば、1 日のコント ローラー アクションの出力をキャッシュする場合できます指定する必要が 86400 秒のキャッシュ有効期間 (60 秒\*60 分\*24 時間)。

指定した時間にわたって保証がそのコンテンツはキャッシュされません。 メモリ リソースが不足になると、キャッシュは削除のコンテンツを自動的に開始します。

リスト 1 で、Home コント ローラーは、リスト 2 で、インデックス ビューを返します。 このビューに関する特別なものがあります。 インデックス ビューには、現在の時刻だけが表示されます (図 1 参照)。

**Listing 2 – Views\Home\Index.aspx**

[!code-aspx[Main](improving-performance-with-output-caching-vb/samples/sample2.aspx)]

**図 1 – キャッシュのインデックス ビュー**

![clip_image002](improving-performance-with-output-caching-vb/_static/image1.jpg)

お使いのブラウザーのアドレス バーに URL/Home/インデックスを入力し、時間インデックス ビューに表示し、繰り返し、お使いのブラウザーの更新/再読み込み ボタンを押すを複数回 Index() アクションを起動する場合は、10 秒間は変更されません。 ビューがキャッシュされるため、同じ時間が表示されます。

アプリケーションにアクセスするすべてのユーザーに対して同じビューがキャッシュされていることを理解しておく必要があります。 Index() アクションを起動するすべてのユーザーにより、インデックス ビューの場合は、同じキャッシュされたバージョンが表示されます。 つまり、インデックス ビューを提供する web サーバーを実行する必要がある作業量が大幅に減ります。

リスト 2 で、ビューは、非常に単純な処理を実行するのには発生します。 ビューには、現在の時刻のみが表示されます。 ただし、同様の一連のデータベース レコードを表示するビューを簡単にキャッシュできます。 その場合は、データベース レコードのセットでは、ビューを返すコント ローラー アクションが呼び出されるたびに、データベースから取得する必要はありません。 キャッシュと、web サーバーとデータベース サーバーの両方を実行する必要がある作業の量を減らすことができます。

ページを使用しない&lt;% @ OutputCache %&gt; MVC ビューでディレクティブ。 このディレクティブは、Web フォームの世界から漏れると、ASP.NET MVC アプリケーションでは使用しない必要があります。 

#### <a name="where-content-is-cached"></a>コンテンツがキャッシュされています。

既定で使用すると、 &lt;OutputCache&gt;属性、コンテンツが 3 つの場所にキャッシュされている: web サーバー、すべてのプロキシ サーバーと web ブラウザー。 Location プロパティを変更することによってコンテンツがキャッシュされているに正確に制御することができます、 &lt;OutputCache&gt;属性。

Location プロパティは、次の値のいずれかに設定できます。

> ·任意
> 
> ·クライアント
> 
> ·ダウン ストリーム
> 
> ·サーバー
> 
> ·[なし]
> 
> ·ServerAndClient


既定では、Location プロパティ値を持ちます。 ただし、キャッシュ、ブラウザーでのみ、またはサーバー上でのみにすることがありますもあります。 たとえば、ユーザーごとに合わせてカスタマイズされた情報をキャッシュする場合は必要がありますいないサーバー上の情報をキャッシュしました。 別のユーザーに異なる情報を表示する場合は、クライアント上にのみ情報をキャッシュする必要があります。

たとえば、リスト 3 のコント ローラーを現在のユーザー名を返す GetName() という名前のアクションを公開します。 ジャック、web サイトにログインし、GetName() アクションを呼び出す場合、アクションの文字列を返します"やあ Jack"。 その後、Jill は、web サイトにログインし、GetName() アクションを呼び出します場合、して彼女も表示されます"やあ Jack"の文字列。 文字列は、回線のモジュラー ジャックが最初にコント ローラー アクションを起動した後、すべてのユーザーの web サーバーでキャッシュされます。

**Listing 3 – Controllers\BadUserController.vb**

[!code-vb[Main](improving-performance-with-output-caching-vb/samples/sample3.vb)]

ほとんどの場合、リスト 3 のコント ローラーでは、使用する方法は機能しません。 Jill に"やあ Jack"のメッセージを表示しません。

サーバー キャッシュのパーソナライズされたコンテンツをキャッシュしない必要があります。 ただし、パフォーマンスを向上させるために、ブラウザーのキャッシュでパーソナライズされたコンテンツをキャッシュする場合があります。 ブラウザーでコンテンツをキャッシュすると、ユーザーが同じコント ローラー アクションを複数回起動、コンテンツは、サーバーではなく、ブラウザーのキャッシュから取得できます。

リスト 4 変更後のコント ローラーは、GetName() アクションの出力をキャッシュします。 ただし、ブラウザーでのみ、サーバーではなく、コンテンツがキャッシュされます。 これにより、複数のユーザーが GetName() メソッドを呼び出すときに、各ユーザーは、自分のユーザー名と他のユーザーのユーザー名を取得します。

**Listing 4 – Controllers\UserController.vb**

[!code-vb[Main](improving-performance-with-output-caching-vb/samples/sample4.vb)]

なお、 &lt;OutputCache&gt;属性リスト 4 OutputCacheLocation.Client 値に設定された場所プロパティが含まれています。 &lt;OutputCache&gt;属性には、NoStore プロパティも含まれています。 NoStore プロパティは、キャッシュされたコンテンツの永続的なコピーを保存しないようにすることのプロキシ サーバーとブラウザーに通知するために使用されます。

#### <a name="varying-the-output-cache"></a>出力キャッシュを変更

状況によっては、まったく同じコンテンツの別のキャッシュされたバージョンを必要があります。 たとえば、マスター/詳細ページを作成することに想像してください。 マスター ページには、ムービーのタイトルの一覧が表示されます。 タイトルをクリックすると、選択したムービーの詳細を取得します。

詳細ページをキャッシュする場合、同じムービーの詳細が表示されますどの映画をクリックします。 最初のユーザーが選択した最初のムービーが将来のすべてのユーザーに表示されます。

この問題を解決するには、VaryByParam プロパティを活用して、 &lt;OutputCache&gt;属性。 このプロパティでは、フォーム パラメーターとまったく同じコンテンツの別のキャッシュされたバージョンを作成できます。 または、クエリ文字列パラメーターによって異なります。

たとえば、リスト 5 コント ローラーは、Master() Details() という 2 つのアクションを公開します。 Master() アクションはムービーのタイトルの一覧を返し、Details() アクションは、選択したムービーの詳細を返します。

**Listing 5 – Controllers\MoviesController.vb**

[!code-vb[Main](improving-performance-with-output-caching-vb/samples/sample5.vb)]

Master() アクションには、値を持つ VaryByParam プロパティが含まれています。"none"です。 アクションが呼び出された Master() マスターの場合は、同じキャッシュされたバージョンの表示とが返されます。 パラメーターは、フォームのパラメーターやクエリ文字列には、(図 2 参照) が無視されます。

**図 2 –/Movies/Master ビュー**

![clip_image004](improving-performance-with-output-caching-vb/_static/image2.jpg)

**図 3 –/ムービー/詳細ビュー**

![clip_image006](improving-performance-with-output-caching-vb/_static/image3.jpg)

Details() アクションには、"Id"値を持つ VaryByParam プロパティが含まれています。 Id パラメーターのさまざまな値は、コント ローラー アクションに渡される、別のキャッシュされたバージョンの詳細ビューが生成されます。

以上のキャッシュで VaryByParam プロパティの結果を使用することを理解して重要でない less をお勧めします。 Id パラメーターの各バージョンでは別のキャッシュされたバージョンの詳細ビューが作成されます。

VaryByParam プロパティは、次の値に設定できます。

> \* = フォームまたはクエリ文字列パラメーターが変化するたびに、別のキャッシュされたバージョンを作成します。
> 
> none = しない別のキャッシュされたバージョンを作成します。
> 
> パラメーターのリストをセミコロン、リスト内のフォームまたはクエリ文字列パラメーターのいずれかによって異なりますたびに異なるキャッシュされたバージョンの作成を =


#### <a name="creating-a-cache-profile"></a>キャッシュ プロファイルを作成します。

プロパティを変更して出力キャッシュのプロパティを構成する代わりに、 &lt;OutputCache&gt;属性に、web の構成 (web.config) ファイルでキャッシュ プロファイルを作成することができます。 Web 構成ファイルでキャッシュ プロファイルを作成するには、重要な利点がいくつか用意されています。

最初に、出力キャッシュを web 構成ファイルに構成すると、コント ローラー アクションが 1 つの場所にコンテンツをキャッシュする方法を制御できます。 1 つのキャッシュ プロファイルを作成し、いくつかのコント ローラーまたはコント ローラー アクションにプロファイルを適用することができます。

次に、アプリケーションを再コンパイルしなくても、web 構成ファイルを変更できます。 実稼働環境に既にデプロイされているアプリケーションのキャッシュを無効にする必要がある場合、web 構成ファイルで定義されているキャッシュ プロファイルだけ変更できます。 Web 構成ファイルへの変更が自動的に検出され、適用します。

たとえば、&lt;キャッシュ&gt;リスト 6 web 構成セクションが Cache1Hour をという名前のキャッシュ プロファイルを定義します。 &lt;キャッシュ&gt;セクションで囲む必要があります、 &lt;system.web&gt; web 構成ファイルのセクション。

**6 – web.config のキャッシュ セクションの一覧を表示します。**

[!code-xml[Main](improving-performance-with-output-caching-vb/samples/sample6.xml)]

リスト 7 でコント ローラーはコント ローラー アクションとに Cache1Hour プロファイルを適用する方法を示しています、 &lt;OutputCache&gt;属性。

**7 – Controllers\ProfileController.vb を一覧表示します。**

[!code-vb[Main](improving-performance-with-output-caching-vb/samples/sample7.vb)]

リスト 7 でコント ローラーによって公開される Index() アクションを呼び出す場合と同じ時間が 1 時間返されます。

#### <a name="summary"></a>まとめ

出力キャッシュと、ASP.NET MVC アプリケーションのパフォーマンスを大幅に向上させるの非常に簡単なメソッドを提供します。 このチュートリアルで使用する方法を学習しました、 &lt;OutputCache&gt;属性をコント ローラー アクションの出力をキャッシュします。 プロパティを変更する方法も学習しました、 &lt;OutputCache&gt;コンテンツがキャッシュを取得する方法を変更する期間と VaryByParam プロパティなどの属性。 最後に、web 構成ファイルでキャッシュ プロファイルを定義する方法を学習しました。

> [!div class="step-by-step"]
> [前へ](understanding-action-filters-vb.md)
> [次へ](adding-dynamic-content-to-a-cached-page-vb.md)
