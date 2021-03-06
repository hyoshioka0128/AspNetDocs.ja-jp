---
uid: web-forms/overview/deployment/visual-studio-web-deployment/project-properties
title: Visual Studio を使用して ASP.NET Web の展開:プロジェクト プロパティ |Microsoft Docs
author: tdykstra
description: このチュートリアル シリーズは、展開する方法を示します (発行) ASP.NET web アプリケーションを Azure App Service Web Apps、またはサード パーティのホスティング プロバイダーを使用して、.
ms.author: riande
ms.date: 02/15/2013
ms.assetid: ec1cec4c-a75f-47af-a2ba-b1e2f971d24b
msc.legacyurl: /web-forms/overview/deployment/visual-studio-web-deployment/project-properties
msc.type: authoredcontent
ms.openlocfilehash: 464146bc8af5cf978902a3e634398ed3f8d15404
ms.sourcegitcommit: 0f1119340e4464720cfd16d0ff15764746ea1fea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59400049"
---
# <a name="aspnet-web-deployment-using-visual-studio-project-properties"></a>Visual Studio を使用して ASP.NET Web の展開:プロジェクトのプロパティ

によって[Tom Dykstra](https://github.com/tdykstra)

[スタート プロジェクトをダウンロードします。](http://go.microsoft.com/fwlink/p/?LinkId=282627)

> このチュートリアル シリーズは、展開する方法を示します (発行) ASP.NET web アプリケーションを Azure App Service Web Apps、またはサード パーティのホスティング プロバイダーを Visual Studio 2012 または Visual Studio 2010 を使用しています。 系列の詳細については、次を参照してください。[シリーズの最初のチュートリアル](introduction.md)します。


## <a name="overview"></a>概要

一部の展開オプションは、プロジェクト ファイルに格納されているプロジェクトのプロパティで構成されます (、 *.csproj*または *.vbproj*ファイル)。 ほとんどの場合、これらの設定の既定値は目的の動作が使用することができます、**プロジェクト プロパティ**UI は、それらを変更する必要がある場合、これらの設定を使用する Visual Studio に組み込まれています。 このチュートリアルでの展開設定を確認する**プロジェクト プロパティ**します。 配置する空のフォルダーを原因となるプレース ホルダー ファイルを作成します。

## <a name="configure-deployment-settings-in-the-project-properties-window"></a>プロジェクトのプロパティ ウィンドウで展開の設定を構成します。

以下のチュートリアルでわかる、発行プロファイルの展開時の処理に影響する設定ほとんどにはが含まれます。 意識する必要があるいくつかの設定にある、**パッケージ化/発行**のタブ、**プロジェクト プロパティ**ウィンドウ。 これらの設定は各ビルド構成に対して指定 — 数よりも、デバッグ ビルドにリリース ビルドごとに異なる設定があることができます、します。

**ソリューション エクスプ ローラー**を右クリックし、 **ContosoUniversity**プロジェクトで、**プロパティ**、クリックして、**パッケージ化/発行 Web**タブ。

![[パッケージ/Web の発行] タブ](project-properties/_static/image1.png)

ウィンドウが表示されたら、どのビルド構成が現在アクティブなソリューションの設定を示す既定になります。 場合、**構成**ボックスは示しません**アクティブ (リリース)** を選択します**リリース**リリース ビルド構成の設定を表示するためにします。 リリース ビルドをテストおよび運用の両方の環境にデプロイします。

![リリース ビルド構成を選択します。](project-properties/_static/image2.png)

**アクティブ (リリース)** または**リリース**リリースのビルド構成を使用して展開するときに有効な値を表示、選択しました。

- **配置する項目**ボックスで、**アプリケーションの実行に必要なファイルのみ**が選択されています。 その他のオプションは**このプロジェクト内のすべてのファイル**または**このプロジェクト フォルダー内のすべてのファイル**します。 既定の選択をそのままにすることではたとえば、ソース コード ファイルを展開しないでください。 この設定は、SQL Server Compact のバイナリ ファイルを含むフォルダーがプロジェクトに含まれるがなぜ理由です。 この設定の詳細については、次を参照してください。**理由はありませんすべてのプロジェクト フォルダーにファイルをデプロイしますか?** で[ASP.NET Web アプリケーション プロジェクトの展開に関する FAQ](https://msdn.microsoft.com/library/ee942158.aspx)します。
- **デバッグ シンボルの生成を除外する**が選択されています。 このビルド構成を使用する場合にデバッグはありません。
- **SQL のパッケージ化/発行 タブで構成されているデータベースをすべて含める**が選択されています。 データベースだけでなく、ファイル、Visual Studio を展開するかどうかを指定します。 チェック ボックスのラベルのみに記載されているが、**パッケージ化/発行 SQL**  タブで、このチェック ボックスをオフにするとは無効にも発行プロファイルで構成されているデータベースの配置。 作業を後で、チェック ボックスを選択されたままにする必要がありますので。 **パッケージ化/発行 SQL**  タブはこれらのチュートリアルで使用しないメソッドを公開する従来のデータベースを使用します。
- **Web 展開パッケージの設定**セクションは 1 回のクリックを使用しているためには適用されませんこれらのチュートリアルで発行します。

変更、**構成** ボックスの一覧をデバッグすると、デバッグ ビルドの場合は、既定の設定を参照してください。 値が以外は同じですが、**生成されたデバッグ シンボルを除外する**がオフになって、デバッグ ビルドをデプロイするときにデバッグできるようにします。

## <a name="make-sure-that-the-elmah-folder-gets-deployed"></a>Elmah フォルダーがデプロイされることを確認します。

前のチュートリアルで学習したように、 [Elmah NuGet パッケージ](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx)エラー ログとレポート機能を提供します。 Contoso University アプリケーション エラーの詳細をという名前のフォルダーに格納する Elmah が構成されています*Elmah*:

![Elmah フォルダー](project-properties/_static/image3.png)

共通の要件は、展開から特定のファイルやフォルダーの除外別の例には、ユーザーがファイルをアップロードしたフォルダーがあります。 ログ ファイルをしたくないか、実稼働環境に展開する、開発環境で作成されたファイルをアップロードします。 運用環境に存在するファイルを削除する展開プロセスをしたくない運用環境に更新プログラムを展開する場合。 (設定に応じて、配置オプションを展開するときに、転送先サイトがソース サイトではなく、ファイルが存在する場合、Web 配置から削除しますが、変換先。)

このチュートリアルでは、前述のように、**配置する項目**オプション、**パッケージ化/発行 Web**に設定されているタブ**ファイルにのみこのアプリケーションを実行する必要**します。 その結果、開発で Elmah によって作成されるログ ファイルは配置されません、これは、発生します。 (プロジェクトに含まれる展開する必要がされ、その**ビルド アクション**プロパティに設定する必要があります**コンテンツ**します。 詳細については、次を参照してください。**理由はありませんすべてのプロジェクト フォルダーにファイルをデプロイしますか?** で[ASP.NET Web アプリケーション プロジェクトの展開に関する FAQ](https://msdn.microsoft.com/library/ee942158.aspx))。 ただし、Web Deploy いないフォルダーが作成されます、移行先サイトにある少なくとも 1 つのファイルをコピーする場合を除き、します。 このために追加、 *.txt*ファイルをフォルダーがコピーされるように、プレース ホルダーとして機能するフォルダー。

**ソリューション エクスプ ローラー**を右クリックし、 *Elmah*フォルダーで、**新しい項目の追加**、という名前のテキスト ファイルを作成および*Placeholder.txt*します。 これで、次のテキストを配置します。「フォルダーがデプロイされることを確認するのプレース ホルダー ファイルです。」 ファイルを保存してください。 すべてを行うために、Visual Studio でこのファイルとフォルダーを展開するかどうかを確認するためには、**ビルド アクション**プロパティの *.txt*に設定されているファイル**コンテンツ**既定。

## <a name="summary"></a>まとめ

これですべての展開のセットアップ タスクを完了しました。 次のチュートリアルでは、Contoso University サイトをテスト環境にデプロイし、し、そこでテストします。

> [!div class="step-by-step"]
> [前へ](web-config-transformations.md)
> [次へ](deploying-to-iis.md)
