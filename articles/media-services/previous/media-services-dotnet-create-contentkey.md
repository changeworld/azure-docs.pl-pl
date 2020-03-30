---
title: Tworzenie kluczy zawartości za pomocą platformy .NET
description: W tym artykule pokazano, jak utworzyć klucze zawartości, które zapewniają bezpieczny dostęp do zasobów.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 225b05e5-7d30-409c-b5b7-3ef0634310c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: aebd6dee9314d6e5641988767c024790b6b721f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251156"
---
# <a name="create-contentkeys-with-net"></a>Tworzenie kluczy zawartości za pomocą platformy .NET 
> [!div class="op_single_selector"]
> * [Reszta](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

Usługi Media Services umożliwiają tworzenie i dostarczanie zaszyfrowanych zasobów. **ContentKey** zapewnia bezpieczny dostęp do **zasobów**s. 

Podczas tworzenia nowego zasobu (na przykład przed [przekazaniem plików)](media-services-dotnet-upload-files.md)można określić następujące opcje szyfrowania: **StorageEncrypted**, **CommonEncryptionProtected**lub **EnvelopeEncryptionProtected**. 

Podczas dostarczania zasobów do klientów można [skonfigurować dynamiczne szyfrowanie zasobów za](media-services-dotnet-configure-asset-delivery-policy.md) pomocą jednego z następujących dwóch szyfrowania: **DynamicEnvelopeEncryption** lub **DynamicCommonEncryption**.

Zaszyfrowane zasoby muszą być skojarzone z **ContentKey**s. W tym artykule opisano sposób tworzenia klucza zawartości.

> [!NOTE]
> Podczas tworzenia nowego **zasobu kryptanego storageencrypted** przy użyciu programu Media Services .NET SDK, **ContentKey** jest automatycznie tworzony i połączony z zasobem.
> 
> 

## <a name="contentkeytype"></a>Typ klucza zawartości
Jedną z wartości, które należy ustawić podczas tworzenia klucza zawartości jest typ klucza zawartości. Wybierz jedną z następujących wartości. 

```csharp
    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for configuration encryption.
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }
```

## <a name="create-envelope-type-contentkey"></a><a id="envelope_contentkey"></a>Tworzenie typu koperty ContentKey
Poniższy fragment kodu tworzy klucz zawartości typu szyfrowania koperty. Następnie kojarzy klucz z określonym zasobem.

```csharp
    static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
    {
        // Create envelope encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.EnvelopeEncryption);

        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int size)
    {
        byte[] randomBytes = new byte[size];
        using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
        {
            rng.GetBytes(randomBytes);
        }

        return randomBytes;
    }

call

    IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);
```


## <a name="create-common-type-contentkey"></a><a id="common_contentkey"></a>Tworzenie wspólnego typu ContentKey
Poniższy fragment kodu tworzy klucz zawartości wspólnego typu szyfrowania. Następnie kojarzy klucz z określonym zasobem.

```csharp
    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.CommonEncryption);

        // Associate the key with the asset.
        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int length)
    {
        var returnValue = new byte[length];

        using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
        {
            rng.GetBytes(returnValue);
        }

        return returnValue;
    }
call

    IContentKey key = CreateCommonTypeContentKey(encryptedsset); 
```

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

