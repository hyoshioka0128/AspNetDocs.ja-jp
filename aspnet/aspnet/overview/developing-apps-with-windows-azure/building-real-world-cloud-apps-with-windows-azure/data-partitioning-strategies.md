---
uid: aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-partitioning-strategies
title: データのパーティション分割戦略 (Azure で現実世界のクラウド アプリの構築) |Microsoft Docs
author: MikeWasson
description: Azure 電子書籍で構築実世界クラウド アプリは、Scott Guthrie が開発したプレゼンテーションに基づいています。 13 のパターンとプラクティスを彼がについて説明しています.
ms.author: riande
ms.date: 06/12/2014
ms.assetid: 513837a7-cfea-4568-a4e9-1f5901245d24
msc.legacyurl: /aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-partitioning-strategies
msc.type: authoredcontent
ms.openlocfilehash: 1050018794526e12aad43cd473665de5ff575d7d
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59403559"
---
# <a name="data-partitioning-strategies-building-real-world-cloud-apps-with-azure"></a>データのパーティション分割戦略 (Azure で現実世界のクラウド アプリの構築)

によって[Mike Wasson](https://github.com/MikeWasson)、 [Rick Anderson]((https://twitter.com/RickAndMSFT))、 [Tom Dykstra](https://github.com/tdykstra)

[ダウンロードその修正プロジェクト](http://code.msdn.microsoft.com/Fix-It-app-for-Building-cdd80df4)または[電子書籍をダウンロード](http://blogs.msdn.com/b/microsoft_press/archive/2014/07/23/free-ebook-building-cloud-apps-with-microsoft-azure.aspx)

> **構築現実世界の Cloud Apps with Azure**電子書籍は Scott Guthrie が開発したプレゼンテーションに基づきます。 13 のパターンについて説明しするのに役立つプラクティスは、クラウドの web アプリの開発が成功します。 系列の詳細については、次を参照してください。[第 1 章](introduction.md)します。


前の追加と web サーバーを削除して、クラウド アプリケーションの web 層をスケールするがいかに簡単かを説明しました。 すべて発生する場合、同じデータ ストア、バック エンド、フロント エンドからアプリケーションのボトルネックに移動し、データ層はスケールにします。 この章で利用する方法、データ層は拡張性の高い複数のリレーショナル データベースにデータを分割することで、または他のデータ ストレージ オプションとリレーショナル データベースの記憶域を組み合わせることによってに注目します。

最適なパーティション分割構成の設定前に説明したのと同じ理由の前面を元に戻す: アプリが稼働した後、データ記憶域の戦略を変更することは困難です。 思われる場合ハード前もってさまざまな方法、「Twitter 少し」アプリがクラッシュまたは、アプリのデータとデータ アクセス コードを再編成するときに、長い間ダウンの必要があります。

## <a name="the-three-vs-of-data-storage"></a>データ ストレージの 3 つの Vs

パーティション分割戦略とべきことが必要かどうかを判断するために、データに関する 3 つの質問を考慮してください。

- ボリューム: データの量はするが最終的には格納か。 いくつかのギガバイトですか。 いくつかの数百ギガバイトですか。 テラバイトでしょうか。 ペタバイト単位ですか。
- – 速度、データが増加するレートとは何ですか。 されていない大量のデータを生成している内部アプリですか。 お客様は、画像やビデオに、アップロードは外部アプリですか。
- さまざまな – データは、の型を格納しますか。 リレーショナル、画像、キーと値のペア、ソーシャル グラフでしょうか。

ボリューム、速度、または各種の多いしようとしている場合は、パーティション分割構成の種類は、スケーリングするアプリを効率的かつ効果的に拡張、およびボトルネックを実行しないことを確認すると有効にする最適なを慎重に検討する必要があります。

基本的には、パーティション分割に 3 つの方法があります。

- 垂直的パーティション分割
- 水平的パーティション分割
- ハイブリッド パーティション分割

## <a name="vertical-partitioning"></a>垂直的パーティション分割

列でテーブルを分割するなど、垂直 portioning: 列の 1 つのセットが 1 つのデータ ストアに移動し、別の列のセットが別のデータ ストアに移動します。

たとえば、マイ アプリは、イメージを含む、ユーザーに関するデータを格納します。

![データ テーブル](data-partitioning-strategies/_static/image1.png)

そのデータをテーブルとして表すデータの種類を確認すると、左側の 3 つの列がある効率的に格納できるリレーショナル データベースで、右側に 2 つの列は基本的にバイト配列の文字列データを c を参照できます。イメージ ファイルから ome します。 記憶域イメージ ファイル、リレーショナル データベース内のデータを行うことが、ファイル システムにデータを保存するので、多くの人はいます。 必要な量のデータを格納できるファイル システムを持たないまたは独立したバックアップを管理およびシステムを復元するようにする場合があります。 このアプローチは、オンプレミス データベースと少量のクラウド データベースでのデータもは機能します。 オンプレミス環境ですべての DBA に任せて、簡単に場合があります。

クラウド データベースではストレージは比較的低コストで、大量のイメージの位置を行える効率的に制限を超えるデータベースのサイズになることです。 これらの問題は、データのテーブルの各列に対して最も適切なデータ ストアを選択します。 つまり、データの垂直方向にパーティション分割によって対処できます。 この例では最適と可能性があります、リレーショナル データベースと Blob storage 内のイメージに文字列データを格納することです。

![垂直方向にパーティション分割されたデータ テーブル](data-partitioning-strategies/_static/image2.png)

ファイル サーバーの設定や、バックアップと復元のリレーショナル データベースの外部に格納されているデータの管理について心配する必要がないため現実的では、オンプレミス環境よりもクラウドでは、データベースではなく Blob ストレージにイメージを格納する: すべてによって、Blob ストレージ サービスが自動的に処理をされます。

これは、パーティション分割アプローチ Fix It アプリで実装しましたと見て、コードでは、そのため、 [Blob ストレージの章](unstructured-blob-storage.md)します。 このパーティション構成、および 3 のメガバイト数の平均のイメージのサイズと仮定すると、Fix It アプリがのみなければに 150 ギガバイト単位のデータベースの最大サイズに達するまで、約 40,000 個のタスクを格納できるようにします。 データベース、イメージを削除すた後には、10 回として多くのタスク; を保存できます。水平方向のパーティション分割構成の実装について考える前に大幅に長くを移動できます。 や支出が非常に低コストの Blob ストレージへの記憶域のニーズの大半のので拡張より緩やかに変化ように、アプリがスケール。

## <a name="horizontal-partitioning-sharding"></a>水平方向のパーティション分割 (シャーディング)

行ごとにテーブルを分割するなど、水平 portioning: 行の 1 つのセットが 1 つのデータ ストアに移動し、別の行セットが別のデータ ストアに移動します。

データの同じセットを指定するには、別の方法も異なるデータベースに異なる範囲の顧客名を格納するあります。

![水平方向にパーティション分割されたデータ テーブル](data-partitioning-strategies/_static/image3.png)

非常にホット スポットを回避するために、データを均等に分散するかどうかを確認する分割スキームに注意します。 多くの人がある一般的な文字で始まる最後の名前、姓の最初の文字を使用してこの簡単な例は、その要件を満たしていません。 テーブル サイズの制限はほとんどが小さいままは中に、一部のデータベースが非常に大きなは取得するため、ご想像よりも早くヒットは。

水平的パーティション分割のデメリットは、ことをすべてのデータの間でクエリを行うには難しいかもしれません。 この例では、クエリはすべて、アプリケーションによって保存されたデータの取得を最大 26 個の異なるデータベースから描画するために必要があります。

## <a name="hybrid-partitioning"></a>ハイブリッド パーティション分割

垂直および水平方向のパーティション分割を組み合わせることができます。 たとえば、サンプル データで、イメージ Blob storage に保存し、文字列データを水平方向にパーティション分割する可能性があります。

![データ テーブルのハイブリッド パーティション分割](data-partitioning-strategies/_static/image4.png)

## <a name="partitioning-a-production-application"></a>実稼働アプリケーションのパーティション分割

パーティション分割構成を行う方法をわかりやすくが概念的には、任意のパーティション分割スキームを選択し、コードの複雑さが増加に対処する必要のある多くの新しい問題が導入されています。 Blob ストレージにイメージを移動する場合は皆さんは、ストレージ サービスがダウンした場合ですか。 Blob のセキュリティの処理方法 同期データベースと blob ストレージを取得するとどうなりますか。 シャーディングの場合はの処理方法のすべてのデータベース間でクエリを実行するでしょうか。

問題は、運用環境に進む前に、それらを計画している限り、管理しやすいです。 多くの人がせずに後で保持していた場合します。 Customer Advisory Team (CAT) チームがお客様のアプリが非常に大きな方法で始まってから 1 か月に 1 回の電話をかけるパニックを取得する平均して、この計画はありませんでした。 言うようなもの。"ヘルプ。 すべてのものを 1 つのデータ ストアに配置するそして 45 日以内の領域が不足する!" 多数のデータ ストアにアクセスする方法に組み込まれているビジネス ロジックがあり、アプリを使用しているユーザーがある場合は、移行するときに、1 日がダウンするよい機会はありません。 そこら顧客パーティションを支援する取り組みをデータでダウンタイムなくその場でとなります。 それが非常に魅力的な非常に恐ろしいとものではない場合に使用することを回避できます! これについて事前に考えると、アプリに統合することが容易、ずっと後で、アプリが大きくなる場合。

## <a name="summary"></a>まとめ

効果的なパーティション分割スキームには、ボトルネックがないクラウド内のデータのペタバイトにスケールするクラウド アプリが有効にすることができます。 オンプレミスのデータ センターでアプリを実行していた場合、膨大なマシンや広範なインフラストラクチャの前もって支払う必要はありません。 クラウドで、必要なし、を使用している場合に限り使用しているときにのみ支払うこといるだけ、容量を追加できます。

[[次へ] の章](unstructured-blob-storage.md)Fix It アプリがイメージを Blob storage に格納することで垂直的パーティション分割を実装する方法を見てみましょう。

## <a name="resources"></a>リソース

パーティション分割の方法の詳細については、次のリソースを参照してください。

ドキュメント:

- [Windows Azure クラウド サービスで大規模なサービスを設計するためのベスト プラクティス](https://msdn.microsoft.com/library/windowsazure/jj717232.aspx)します。 Mark Simms、Michael Thomassy、ホワイト ペーパー。
- [Microsoft Patterns and Practices - クラウドの設計パターン](https://msdn.microsoft.com/library/dn568099.aspx)します。 データのパーティション分割ガイダンスについては、シャーディング パターンを参照してください。

ビデオ:

- [フェール セーフ:スケーラブルで回復力のあるクラウド サービスの構築](https://channel9.msdn.com/Series/FailSafe)します。 Ulrich Homann、Marc Mercuri、Mark Simms、9 部構成シリーズ。 Microsoft Customer ・ Advisory Team (CAT) の実際の顧客使用経験描画ストーリーと非常にアクセス可能な興味深い方法で高度な概念とアーキテクチャの原則を説明します。 エピソード 7 でのパーティション分割のディスカッションを参照してください。
- [大規模なビルド。Windows Azure のお客様 - パート I から学んだ教訓](https://channel9.msdn.com/Events/Build/2012/3-029)します。Mark Simms 説明パーティション分割スキーム、シャーディング戦略では、シャーディングの場合と SQL Database Federation、19:49 以降に実装する方法。 フェール セーフ シリーズが操作方法の詳細になると似ています。

サンプル コード: 

- [クラウド サービス Windows Azure の基礎](https://code.msdn.microsoft.com/Cloud-Service-Fundamentals-4ca72649)します。 シャード化されたデータベースを含むサンプル アプリケーション。 実装されているシャーディング スキームについては、次を参照してください。 [DAL – シャーディングの RDBMS](https://blogs.msdn.com/b/windowsazure/archive/2013/09/05/dal-sharding-of-rdbms.aspx) 、Windows Azure ブログ。

> [!div class="step-by-step"]
> [前へ](data-storage-options.md)
> [次へ](unstructured-blob-storage.md)
