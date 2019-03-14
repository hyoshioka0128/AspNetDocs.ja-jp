---
title: ASP.NET Core のロード/ストレス テスト
author: Jeremy-Meng
description: いくつかの注目すべきツールとロード テストとストレスの ASP.NET Core アプリをテストするための方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 01/04/2019
uid: test/loadtests
ms.openlocfilehash: d989bc841a372bed7ebf2c84c6abe1a57762ad04
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57061409"
---
# <a name="load-and-stress-testing-aspnet-core"></a>ロード テストとストレス テストの ASP.NET Core

ロード テストとストレス テストは、web アプリが高パフォーマンスであることを確認する重要な拡張性の高いです。 その目標が異なる類似のテストを多くの場合、共有もします。

**ロード テスト**:アプリは、応答の目標を引き続き満たしながら特定のシナリオのユーザーの指定した負荷を処理できるかどうかをテストします。 アプリは、通常の状況で実行されます。

**ストレス テスト**:テスト アプリの安定性では、極端な場合は、多くの場合、長期間実行されている場合:

* 高度なユーザー ロード – スパイクまたは徐々 に増やしていきます。
* コンピューティング リソースの制約。  

ストレス条件下でをアプリの障害から回復し、適切に想定される動作に戻りますか。 アプリは、ストレス条件下で*いない*通常の状況下で実行します。

## <a name="visual-studio-tools"></a>Visual Studio ツール

Visual Studio では、作成、開発、および web パフォーマンスとロード テストをデバッグすることができます。 Web ブラウザーでアクションを記録することによってテストを作成するオプションがあります。

[クイック スタート:ロード テスト プロジェクトを作成](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)作成、構成、およびロード テストを Visual Studio 2017 を使用してプロジェクトを実行する方法を示します。

詳しくは、「[その他の技術情報](#add)」をご覧ください。

Azure DevOps を使用してクラウドで実行またはオンプレミスで実行するロード テストを構成することができます。

## <a name="azure-devops"></a>Azure DevOps

使用してロード テストの実行を開始することができます、 [Azure DevOps テスト計画](/azure/devops/test/load-test/index?view=vsts)サービス。

![](./load-tests/_static/azure-devops-load-test.png)

サービスには、次の種類のテストの形式がサポートされています。

- Visual Studio テスト – Visual Studio で作成した web テストします。
- HTTP アーカイブ ベースのテスト – アーカイブ内にキャプチャされた HTTP トラフィックがテスト中に再生されます。
- [URL ベースのテスト](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts)– テスト、要求の種類、ヘッダー、およびクエリ文字列を読み込む Url を指定できます。 実行時間などのパラメーターの設定を実行するには、ロード パターンでは、ユーザーなどの数を構成できます。
- [Apache JMeter](https://jmeter.apache.org/)をテストします。

## <a name="azure-portal"></a>Azure ポータル

[Azure ポータルでは、設定して、Web アプリのロード テストを実行する](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts)Azure portal で App Service の [パフォーマンス] タブから直接します。

![](./load-tests/_static/azure-appservice-perf-test.png)

テストでは、指定した URL、または複数の Url をテストできる、Visual Studio Web テスト ファイルで手動テストを指定できます。

![](./load-tests/_static/azure-appservice-perf-test-config.png)

テストの最後に、アプリのパフォーマンス特性を表示するレポートを生成します。 統計の例は次のとおりです。

- 平均応答時間
- 最大スループット: 1 秒あたりの要求
- 失敗率

## <a name="third-party-tools"></a>サード パーティのツール

次の一覧には、さまざまな機能セットとサード パーティの web パフォーマンス ツールが含まれています。

- [Apache JMeter](https://jmeter.apache.org/) :ロード テスト ツールの完全なおすすめのスイートです。 スレッドに依存します。 には、ユーザーごとの 1 つのスレッドが必要があります。
- [ab - Apache HTTP server がベンチマーク ツール](https://httpd.apache.org/docs/2.4/programs/ab.html)
- [ガットリング](https://gatling.io/):GUI ツールとテスト レコーダーでデスクトップ ツールです。 JMeter よりパフォーマンスが向上します。
- [Locust.io](https://locust.io/) :スレッドに制限されません。

<a name="add"></a>
## <a name="additional-resources"></a>その他のリソース

[ロード テストのブログ シリーズ](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/)作成者: Charles Sterling します。 日が指定されたが、ほとんどの項目は引き続き関連します。
