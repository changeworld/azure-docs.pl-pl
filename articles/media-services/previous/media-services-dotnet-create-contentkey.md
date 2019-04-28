---
title: Tworzenie kluczy zawartości przy użyciu platformy .NET
description: Dowiedz się, jak utworzyć kluczy zawartości, które zapewniają bezpieczny dostęp do zasobów.
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
ms.openlocfilehash: ab26be3b9ac5d209cfe8117bdf9e87e0c7e74188
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465579"
---
# <a name="create-contentkeys-with-net"></a>Tworzenie kluczy zawartości przy użyciu platformy .NET 
> [!div class="op_single_selector"]
> * [REST](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

Usługa Media Services umożliwia tworzenie i dostarczanie zaszyfrowanych zasoby. A **ContentKey** zapewnia bezpieczny dostęp do Twojego **zasobów**s. 

Po utworzeniu nowego elementu zawartości (na przykład, zanim [przekazywanie plików](media-services-dotnet-upload-files.md)), można określić następujące opcje szyfrowania: **StorageEncrypted**, **CommonEncryptionProtected**, lub **EnvelopeEncryptionProtected**. 

Gdy zasoby można dostarczać klientom, możesz ją [konfigurowanie dla zasobów, aby był dynamicznie szyfrowany](media-services-dotnet-configure-asset-delivery-policy.md) przy użyciu jednego z następujących dwóch metody szyfrowania: **DynamicEnvelopeEncryption** lub **DynamicCommonEncryption**.

Zaszyfrowane obiekty muszą być skojarzone z **ContentKey**s. W tym artykule opisano sposób tworzenia klucza zawartości.

> [!NOTE]
> Podczas tworzenia nowego **StorageEncrypted** zasobów przy użyciu Media Services .NET SDK **ContentKey** jest automatycznie tworzony i połączone z elementem zawartości.
> 
> 

## <a name="contentkeytype"></a>ContentKeyType
Jedna z wartości, należy ustawić podczas tworzenia zawartości klucza jest typ klucza zawartości. Wybierz jedną z następujących wartości. 

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

## <a id="envelope_contentkey"></a>Utwórz typ schematu envelope ContentKey
Poniższy fragment kodu tworzy klucz zawartości typu schematu envelope szyfrowania. Następnie kojarzy klucz z określonym zasobie.

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


## <a id="common_contentkey"></a>Utwórz wspólny typ ContentKey
Poniższy fragment kodu tworzy zawartości klucz wspólny typ szyfrowania. Następnie kojarzy klucz z określonym zasobie.

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

