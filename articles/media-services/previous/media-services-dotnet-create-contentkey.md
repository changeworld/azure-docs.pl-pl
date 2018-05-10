---
title: Utwórz ContentKeys z platformą .NET
description: Informacje o sposobie tworzenia kluczy zawartości, które zapewniają bezpieczny dostęp do zasobów.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 225b05e5-7d30-409c-b5b7-3ef0634310c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.openlocfilehash: 53df4c4cef19f6eef99aa15bb265317aa0cd1d58
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="create-contentkeys-with-net"></a>Utwórz ContentKeys z platformą .NET
> [!div class="op_single_selector"]
> * [REST](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

Usługa Media Services umożliwia tworzenie i dostarczanie zaszyfrowanych zasoby. A **ContentKey** zapewnia bezpieczny dostęp do Twojego **zasobów**s. 

Podczas tworzenia nowego elementu zawartości (na przykład przed [przekazać pliki](media-services-dotnet-upload-files.md)), można określić następujące opcje szyfrowania: **StorageEncrypted**, **CommonEncryptionProtected**, lub **EnvelopeEncryptionProtected**. 

Zasoby można dostarczyć do klientów, można [skonfigurować trwałych był dynamicznie szyfrowany](media-services-dotnet-configure-asset-delivery-policy.md) z jednym z następujących dwie metody szyfrowania: **DynamicEnvelopeEncryption** lub  **DynamicCommonEncryption**.

Zasoby zaszyfrowanych ma ma być skojarzone z **ContentKey**s. W tym artykule opisano sposób tworzenia klucza zawartości.

> [!NOTE]
> Podczas tworzenia nowego **StorageEncrypted** zasobów przy użyciu zestawu SDK .NET usługi Media, **ContentKey** jest automatycznie tworzony i połączone z trwałym.
> 
> 

## <a name="contentkeytype"></a>ContentKeyType
Jedna z wartości, że należy ustawić podczas tworzenia zawartości klucz jest typ klucza zawartości. Wybierz jedną z następujących wartości. 

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

## <a id="envelope_contentkey"></a>Tworzenie typu koperty ContentKey
Poniższy fragment kodu tworzy klucz zawartości typu szyfrowania koperty. Następnie kojarzy klucz z określonym zasobie.

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


## <a id="common_contentkey"></a>Tworzenie wspólnego typu ContentKey
Poniższy fragment kodu tworzy zawartości klucza wspólnego typu szyfrowania. Następnie kojarzy klucz z określonym zasobie.

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

