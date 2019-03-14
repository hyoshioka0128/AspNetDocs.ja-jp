---
title: ASP.NET Core でのデータの保護コンピューター全体のポリシーをサポートします。
author: rick-anderson
description: ASP.NET Core データ保護を使用するすべてのアプリの既定のコンピューター全体のポリシー設定のサポートについて説明します。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/configuration/machine-wide-policy
ms.openlocfilehash: 70aaca7afcd3df22cebb4466fbd9845a2277688c
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57055589"
---
# <a name="data-protection-machine-wide-policy-support-in-aspnet-core"></a><span data-ttu-id="2ed31-103">ASP.NET Core でのデータの保護コンピューター全体のポリシーをサポートします。</span><span class="sxs-lookup"><span data-stu-id="2ed31-103">Data Protection machine-wide policy support in ASP.NET Core</span></span>

<span data-ttu-id="2ed31-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2ed31-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2ed31-105">Windows で実行するときに、データ保護システムに ASP.NET Core データ保護を使用するすべてのアプリの既定のコンピューター全体のポリシー設定のサポートが制限されています。</span><span class="sxs-lookup"><span data-stu-id="2ed31-105">When running on Windows, the Data Protection system has limited support for setting a default machine-wide policy for all apps that consume ASP.NET Core Data Protection.</span></span> <span data-ttu-id="2ed31-106">基本的な考え方は、管理者が、アルゴリズムの使用など、既定の設定またはコンピューターのすべてのアプリを手動で更新する必要はありません、キーの有効期間を変更するとすることもできます。</span><span class="sxs-lookup"><span data-stu-id="2ed31-106">The general idea is that an administrator might wish to change a default setting, such as the algorithms used or key lifetime, without the need to manually update every app on the machine.</span></span>

> [!WARNING]
> <span data-ttu-id="2ed31-107">システム管理者は、既定のポリシーを設定できますが、それを適用することはできません。</span><span class="sxs-lookup"><span data-stu-id="2ed31-107">The system administrator can set default policy, but they can't enforce it.</span></span> <span data-ttu-id="2ed31-108">アプリ開発者は、独自の選択のいずれかの任意の値を常にオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="2ed31-108">The app developer can always override any value with one of their own choosing.</span></span> <span data-ttu-id="2ed31-109">既定のポリシーには、開発者が明示的な値の設定を指定していないアプリのみに影響します。</span><span class="sxs-lookup"><span data-stu-id="2ed31-109">The default policy only affects apps where the developer hasn't specified an explicit value for a setting.</span></span>

## <a name="setting-default-policy"></a><span data-ttu-id="2ed31-110">既定のポリシーを設定</span><span class="sxs-lookup"><span data-stu-id="2ed31-110">Setting default policy</span></span>

<span data-ttu-id="2ed31-111">既定のポリシーを設定するには、管理者は、次のレジストリ キーの下のシステム レジストリで既知の値を設定できます。</span><span class="sxs-lookup"><span data-stu-id="2ed31-111">To set default policy, an administrator can set known values in the system registry under the following registry key:</span></span>

<span data-ttu-id="2ed31-112">**HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection**</span><span class="sxs-lookup"><span data-stu-id="2ed31-112">**HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection**</span></span>

<span data-ttu-id="2ed31-113">64 ビットのオペレーティング システムで 32 ビット アプリの動作に影響する場合は、Wow6432Node と同等の上記のキーを構成してください。</span><span class="sxs-lookup"><span data-stu-id="2ed31-113">If you're on a 64-bit operating system and want to affect the behavior of 32-bit apps, remember to configure the Wow6432Node equivalent of the above key.</span></span>

<span data-ttu-id="2ed31-114">サポートされている値は、以下に示します。</span><span class="sxs-lookup"><span data-stu-id="2ed31-114">The supported values are shown below.</span></span>

| <span data-ttu-id="2ed31-115">[値]</span><span class="sxs-lookup"><span data-stu-id="2ed31-115">Value</span></span>              | <span data-ttu-id="2ed31-116">型</span><span class="sxs-lookup"><span data-stu-id="2ed31-116">Type</span></span>   | <span data-ttu-id="2ed31-117">説明</span><span class="sxs-lookup"><span data-stu-id="2ed31-117">Description</span></span> |
| ------------------ | :----: | ----------- |
| <span data-ttu-id="2ed31-118">EncryptionType</span><span class="sxs-lookup"><span data-stu-id="2ed31-118">EncryptionType</span></span>     | <span data-ttu-id="2ed31-119">string</span><span class="sxs-lookup"><span data-stu-id="2ed31-119">string</span></span> | <span data-ttu-id="2ed31-120">データ保護にどのアルゴリズムを使用する必要がありますを指定します。</span><span class="sxs-lookup"><span data-stu-id="2ed31-120">Specifies which algorithms should be used for data protection.</span></span> <span data-ttu-id="2ed31-121">値は、CNG CBC、CNG の GCM、または管理されている必要があり、詳細については、以下が説明します。</span><span class="sxs-lookup"><span data-stu-id="2ed31-121">The value must be CNG-CBC, CNG-GCM, or Managed and is described in more detail below.</span></span> |
| <span data-ttu-id="2ed31-122">DefaultKeyLifetime</span><span class="sxs-lookup"><span data-stu-id="2ed31-122">DefaultKeyLifetime</span></span> | <span data-ttu-id="2ed31-123">DWORD</span><span class="sxs-lookup"><span data-stu-id="2ed31-123">DWORD</span></span>  | <span data-ttu-id="2ed31-124">新しく生成されたキーの有効期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="2ed31-124">Specifies the lifetime for newly-generated keys.</span></span> <span data-ttu-id="2ed31-125">値を選択しを日数で指定する必要があります > 7 を = です。</span><span class="sxs-lookup"><span data-stu-id="2ed31-125">The value is specified in days and must be >= 7.</span></span> |
| <span data-ttu-id="2ed31-126">KeyEscrowSinks</span><span class="sxs-lookup"><span data-stu-id="2ed31-126">KeyEscrowSinks</span></span>     | <span data-ttu-id="2ed31-127">string</span><span class="sxs-lookup"><span data-stu-id="2ed31-127">string</span></span> | <span data-ttu-id="2ed31-128">キー エスクローの使用される型を指定します。</span><span class="sxs-lookup"><span data-stu-id="2ed31-128">Specifies the types that are used for key escrow.</span></span> <span data-ttu-id="2ed31-129">値は、キー エスクロー シンク、リスト内の各要素が実装する型のアセンブリ修飾名をセミコロンで区切られたリスト[IKeyEscrowSink](/dotnet/api/microsoft.aspnetcore.dataprotection.keymanagement.ikeyescrowsink)します。</span><span class="sxs-lookup"><span data-stu-id="2ed31-129">The value is a semicolon-delimited list of key escrow sinks, where each element in the list is the assembly-qualified name of a type that implements [IKeyEscrowSink](/dotnet/api/microsoft.aspnetcore.dataprotection.keymanagement.ikeyescrowsink).</span></span> |

## <a name="encryption-types"></a><span data-ttu-id="2ed31-130">暗号化の種類</span><span class="sxs-lookup"><span data-stu-id="2ed31-130">Encryption types</span></span>

<span data-ttu-id="2ed31-131">システムが Windows CNG が提供するサービスで、信頼性の機密性と HMAC の CBC モードの対称ブロック暗号を使用するよう構成 EncryptionType が CNG CBC の場合は、(を参照してください[カスタム Windows CNG アルゴリズムを指定する](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms)の参照してください。)</span><span class="sxs-lookup"><span data-stu-id="2ed31-131">If EncryptionType is CNG-CBC, the system is configured to use a CBC-mode symmetric block cipher for confidentiality and HMAC for authenticity with services provided by Windows CNG (see [Specifying custom Windows CNG algorithms](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) for more details).</span></span> <span data-ttu-id="2ed31-132">次の追加の値がサポートされている、CngCbcAuthenticatedEncryptionSettings 型のプロパティにそれぞれ対応します。</span><span class="sxs-lookup"><span data-stu-id="2ed31-132">The following additional values are supported, each of which corresponds to a property on the CngCbcAuthenticatedEncryptionSettings type.</span></span>

| <span data-ttu-id="2ed31-133">[値]</span><span class="sxs-lookup"><span data-stu-id="2ed31-133">Value</span></span>                       | <span data-ttu-id="2ed31-134">型</span><span class="sxs-lookup"><span data-stu-id="2ed31-134">Type</span></span>   | <span data-ttu-id="2ed31-135">説明</span><span class="sxs-lookup"><span data-stu-id="2ed31-135">Description</span></span> |
| --------------------------- | :----: | ----------- |
| <span data-ttu-id="2ed31-136">EncryptionAlgorithm</span><span class="sxs-lookup"><span data-stu-id="2ed31-136">EncryptionAlgorithm</span></span>         | <span data-ttu-id="2ed31-137">string</span><span class="sxs-lookup"><span data-stu-id="2ed31-137">string</span></span> | <span data-ttu-id="2ed31-138">CNG で認識される対称ブロック暗号アルゴリズムの名前。</span><span class="sxs-lookup"><span data-stu-id="2ed31-138">The name of a symmetric block cipher algorithm understood by CNG.</span></span> <span data-ttu-id="2ed31-139">このアルゴリズムは、CBC モードで開きます。</span><span class="sxs-lookup"><span data-stu-id="2ed31-139">This algorithm is opened in CBC mode.</span></span> |
| <span data-ttu-id="2ed31-140">EncryptionAlgorithmProvider</span><span class="sxs-lookup"><span data-stu-id="2ed31-140">EncryptionAlgorithmProvider</span></span> | <span data-ttu-id="2ed31-141">string</span><span class="sxs-lookup"><span data-stu-id="2ed31-141">string</span></span> | <span data-ttu-id="2ed31-142">EncryptionAlgorithm アルゴリズムを作成できる CNG プロバイダーの実装の名前。</span><span class="sxs-lookup"><span data-stu-id="2ed31-142">The name of the CNG provider implementation that can produce the algorithm EncryptionAlgorithm.</span></span> |
| <span data-ttu-id="2ed31-143">EncryptionAlgorithmKeySize</span><span class="sxs-lookup"><span data-stu-id="2ed31-143">EncryptionAlgorithmKeySize</span></span>  | <span data-ttu-id="2ed31-144">DWORD</span><span class="sxs-lookup"><span data-stu-id="2ed31-144">DWORD</span></span>  | <span data-ttu-id="2ed31-145">(Bits) の対称ブロック暗号アルゴリズムを派生させるキーの長さ。</span><span class="sxs-lookup"><span data-stu-id="2ed31-145">The length (in bits) of the key to derive for the symmetric block cipher algorithm.</span></span> |
| <span data-ttu-id="2ed31-146">HashAlgorithm</span><span class="sxs-lookup"><span data-stu-id="2ed31-146">HashAlgorithm</span></span>               | <span data-ttu-id="2ed31-147">string</span><span class="sxs-lookup"><span data-stu-id="2ed31-147">string</span></span> | <span data-ttu-id="2ed31-148">CNG で認識されるハッシュ アルゴリズムの名前。</span><span class="sxs-lookup"><span data-stu-id="2ed31-148">The name of a hash algorithm understood by CNG.</span></span> <span data-ttu-id="2ed31-149">このアルゴリズムは、HMAC のモードで開きます。</span><span class="sxs-lookup"><span data-stu-id="2ed31-149">This algorithm is opened in HMAC mode.</span></span> |
| <span data-ttu-id="2ed31-150">HashAlgorithmProvider</span><span class="sxs-lookup"><span data-stu-id="2ed31-150">HashAlgorithmProvider</span></span>       | <span data-ttu-id="2ed31-151">string</span><span class="sxs-lookup"><span data-stu-id="2ed31-151">string</span></span> | <span data-ttu-id="2ed31-152">アルゴリズムの HashAlgorithm を作成できる CNG プロバイダーの実装の名前。</span><span class="sxs-lookup"><span data-stu-id="2ed31-152">The name of the CNG provider implementation that can produce the algorithm HashAlgorithm.</span></span> |

<span data-ttu-id="2ed31-153">EncryptionType が CNG GCM の場合は、Windows CNG が提供するサービスに、機密性、完全性を Galois/カウンター モードの対称ブロック暗号を使用するシステムの構成 (を参照してください[カスタム Windows CNG アルゴリズムを指定する](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms)ご覧ください)。</span><span class="sxs-lookup"><span data-stu-id="2ed31-153">If EncryptionType is CNG-GCM, the system is configured to use a Galois/Counter Mode symmetric block cipher for confidentiality and authenticity with services provided by Windows CNG (see [Specifying custom Windows CNG algorithms](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) for more details).</span></span> <span data-ttu-id="2ed31-154">次の追加の値がサポートされている、CngGcmAuthenticatedEncryptionSettings 型のプロパティにそれぞれ対応します。</span><span class="sxs-lookup"><span data-stu-id="2ed31-154">The following additional values are supported, each of which corresponds to a property on the CngGcmAuthenticatedEncryptionSettings type.</span></span>

| <span data-ttu-id="2ed31-155">[値]</span><span class="sxs-lookup"><span data-stu-id="2ed31-155">Value</span></span>                       | <span data-ttu-id="2ed31-156">型</span><span class="sxs-lookup"><span data-stu-id="2ed31-156">Type</span></span>   | <span data-ttu-id="2ed31-157">説明</span><span class="sxs-lookup"><span data-stu-id="2ed31-157">Description</span></span> |
| --------------------------- | :----: | ----------- |
| <span data-ttu-id="2ed31-158">EncryptionAlgorithm</span><span class="sxs-lookup"><span data-stu-id="2ed31-158">EncryptionAlgorithm</span></span>         | <span data-ttu-id="2ed31-159">string</span><span class="sxs-lookup"><span data-stu-id="2ed31-159">string</span></span> | <span data-ttu-id="2ed31-160">CNG で認識される対称ブロック暗号アルゴリズムの名前。</span><span class="sxs-lookup"><span data-stu-id="2ed31-160">The name of a symmetric block cipher algorithm understood by CNG.</span></span> <span data-ttu-id="2ed31-161">このアルゴリズムが Galois/カウンター モードで開かれます。</span><span class="sxs-lookup"><span data-stu-id="2ed31-161">This algorithm is opened in Galois/Counter Mode.</span></span> |
| <span data-ttu-id="2ed31-162">EncryptionAlgorithmProvider</span><span class="sxs-lookup"><span data-stu-id="2ed31-162">EncryptionAlgorithmProvider</span></span> | <span data-ttu-id="2ed31-163">string</span><span class="sxs-lookup"><span data-stu-id="2ed31-163">string</span></span> | <span data-ttu-id="2ed31-164">EncryptionAlgorithm アルゴリズムを作成できる CNG プロバイダーの実装の名前。</span><span class="sxs-lookup"><span data-stu-id="2ed31-164">The name of the CNG provider implementation that can produce the algorithm EncryptionAlgorithm.</span></span> |
| <span data-ttu-id="2ed31-165">EncryptionAlgorithmKeySize</span><span class="sxs-lookup"><span data-stu-id="2ed31-165">EncryptionAlgorithmKeySize</span></span>  | <span data-ttu-id="2ed31-166">DWORD</span><span class="sxs-lookup"><span data-stu-id="2ed31-166">DWORD</span></span>  | <span data-ttu-id="2ed31-167">(Bits) の対称ブロック暗号アルゴリズムを派生させるキーの長さ。</span><span class="sxs-lookup"><span data-stu-id="2ed31-167">The length (in bits) of the key to derive for the symmetric block cipher algorithm.</span></span> |

<span data-ttu-id="2ed31-168">EncryptionType が管理されている場合の信頼性の機密性および KeyedHashAlgorithm マネージ SymmetricAlgorithm を使用するシステムの構成 (を参照してください[を指定するカスタム アルゴリズムのマネージ](xref:security/data-protection/configuration/overview#specifying-custom-managed-algorithms)の詳細)。</span><span class="sxs-lookup"><span data-stu-id="2ed31-168">If EncryptionType is Managed, the system is configured to use a managed SymmetricAlgorithm for confidentiality and KeyedHashAlgorithm for authenticity (see [Specifying custom managed algorithms](xref:security/data-protection/configuration/overview#specifying-custom-managed-algorithms) for more details).</span></span> <span data-ttu-id="2ed31-169">次の追加の値がサポートされている、ManagedAuthenticatedEncryptionSettings 型のプロパティにそれぞれ対応します。</span><span class="sxs-lookup"><span data-stu-id="2ed31-169">The following additional values are supported, each of which corresponds to a property on the ManagedAuthenticatedEncryptionSettings type.</span></span>

| <span data-ttu-id="2ed31-170">[値]</span><span class="sxs-lookup"><span data-stu-id="2ed31-170">Value</span></span>                      | <span data-ttu-id="2ed31-171">型</span><span class="sxs-lookup"><span data-stu-id="2ed31-171">Type</span></span>   | <span data-ttu-id="2ed31-172">説明</span><span class="sxs-lookup"><span data-stu-id="2ed31-172">Description</span></span> |
| -------------------------- | :----: | ----------- |
| <span data-ttu-id="2ed31-173">EncryptionAlgorithmType</span><span class="sxs-lookup"><span data-stu-id="2ed31-173">EncryptionAlgorithmType</span></span>    | <span data-ttu-id="2ed31-174">string</span><span class="sxs-lookup"><span data-stu-id="2ed31-174">string</span></span> | <span data-ttu-id="2ed31-175">SymmetricAlgorithm を実装する型のアセンブリ修飾名。</span><span class="sxs-lookup"><span data-stu-id="2ed31-175">The assembly-qualified name of a type that implements SymmetricAlgorithm.</span></span> |
| <span data-ttu-id="2ed31-176">EncryptionAlgorithmKeySize</span><span class="sxs-lookup"><span data-stu-id="2ed31-176">EncryptionAlgorithmKeySize</span></span> | <span data-ttu-id="2ed31-177">DWORD</span><span class="sxs-lookup"><span data-stu-id="2ed31-177">DWORD</span></span>  | <span data-ttu-id="2ed31-178">対称暗号化アルゴリズムを派生させるキーの (bits) の長さ。</span><span class="sxs-lookup"><span data-stu-id="2ed31-178">The length (in bits) of the key to derive for the symmetric encryption algorithm.</span></span> |
| <span data-ttu-id="2ed31-179">ValidationAlgorithmType</span><span class="sxs-lookup"><span data-stu-id="2ed31-179">ValidationAlgorithmType</span></span>    | <span data-ttu-id="2ed31-180">string</span><span class="sxs-lookup"><span data-stu-id="2ed31-180">string</span></span> | <span data-ttu-id="2ed31-181">KeyedHashAlgorithm を実装する型のアセンブリ修飾名。</span><span class="sxs-lookup"><span data-stu-id="2ed31-181">The assembly-qualified name of a type that implements KeyedHashAlgorithm.</span></span> |

<span data-ttu-id="2ed31-182">EncryptionType がその他の値以外は、null または空の場合、データ保護システムは起動時に例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="2ed31-182">If EncryptionType has any other value other than null or empty, the Data Protection system throws an exception at startup.</span></span>

> [!WARNING]
> <span data-ttu-id="2ed31-183">型名 (EncryptionAlgorithmType、ValidationAlgorithmType、KeyEscrowSinks) は、既定のポリシー設定を構成するときに、型が、アプリに対して使用できる場合があります。</span><span class="sxs-lookup"><span data-stu-id="2ed31-183">When configuring a default policy setting that involves type names (EncryptionAlgorithmType, ValidationAlgorithmType, KeyEscrowSinks), the types must be available to the app.</span></span> <span data-ttu-id="2ed31-184">意味デスクトップ CLR で実行されるアプリでは、これらの型を含むアセンブリがグローバル アセンブリ キャッシュ (GAC) に存在します。</span><span class="sxs-lookup"><span data-stu-id="2ed31-184">This means that for apps running on Desktop CLR, the assemblies that contain these types should be present in the Global Assembly Cache (GAC).</span></span> <span data-ttu-id="2ed31-185">ASP.NET Core アプリの .NET Core で実行されている場合は、これらの型が含まれているパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2ed31-185">For ASP.NET Core apps running on .NET Core, the packages that contain these types should be installed.</span></span>