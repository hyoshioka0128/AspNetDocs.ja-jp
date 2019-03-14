---
title: ASP.NET Core のイメージ タグ ヘルパー
author: pkellner
description: イメージ タグ ヘルパーを使用する方法を示します。
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: mvc/views/tag-helpers/builtin-th/image-tag-helper
ms.openlocfilehash: 5eb74a6698911a1c594d11573192cb1b9ed53b49
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57030109"
---
# <a name="image-tag-helper-in-aspnet-core"></a>ASP.NET Core のイメージ タグ ヘルパー

著者: [Peter Kellner](http://peterkellner.net)

イメージ タグ ヘルパーは `<img>` タグを強化し、静的イメージ ファイルにキャッシュ バスティング動作を提供します。

キャッシュ バスティング文字列は、アセットの URL に追加される、静的なイメージ ファイルのハッシュを表す一意の値です。 一意の文字列は、クライアントのキャッシュからではなく、ホスト Web サーバーからイメージの再読み込みを行うよう、クライアント (および一部のプロキシ) に要求します。

イメージ ソース (`src`) がホスト Web サーバー上の静的ファイルの場合:

* 一意のキャッシュ バスティング文字列は、クエリ パラメーターとしてイメージ ソースに追加されます。
* ホスト Web サーバーのファイルが変更された場合、更新された要求パラメーターを含む一意の要求 URL が確実に生成されます。

タグ ヘルパーの概要については、「<xref:mvc/views/tag-helpers/intro>」をご覧ください。

## <a name="image-tag-helper-attributes"></a>イメージ タグ ヘルパーの属性

### <a name="src"></a>src

イメージ タグ ヘルパーをアクティブにするには、`<img>` 要素に `src` 属性が必要です。

イメージ ソース (`src`) は、サーバー上の物理静的ファイルをポイントしている必要があります。 `src` がリモート URI の場合は、キャッシュ バスティング クエリ文字列パラメーターは生成されません。

### <a name="asp-append-version"></a>asp-append-version

`asp-append-version` 属性の値が `true` で、`src` 属性も指定されている場合、イメージ タグ ヘルパーが呼び出されます。

イメージ タグ ヘルパーを使用する例を次に示します。

```cshtml
<img src="~/images/asplogo.png" asp-append-version="true" />
```

静的ファイルがディレクトリ */wwwroot/images/* に存在する場合、生成された HTML は次のようになります (ハッシュは異なります)。

```html
<img src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM" />
```

パラメーター `v` に割り当てられた値は、ディスク上の *asplogo.png* ファイルのハッシュ値です。 Web サーバーが静的ファイルに対する読み取りアクセスを取得できない場合、`v` パラメーターは表示されるマークアップの `src` 属性に追加されません。

## <a name="hash-caching-behavior"></a>ハッシュ キャッシュの動作

イメージ タグ ヘルパーはローカル Web サーバーでキャッシュ プロバイダーを使用して、特定のファイルの計算された `Sha512` ハッシュを格納します。 ファイルが複数回要求された場合、ハッシュは再計算されません。 キャッシュは、ファイルの `Sha512` ハッシュが計算されたときにファイルにアタッチされるファイル ウォッチャーによって無効になります。 ディスク上のファイルが変更されると、新しいハッシュが計算されてキャッシュされます。

## <a name="additional-resources"></a>その他の技術情報

* <xref:performance/caching/memory>
