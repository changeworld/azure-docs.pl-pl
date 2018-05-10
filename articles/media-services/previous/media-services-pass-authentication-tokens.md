---
title: Przekazywanie tokenów uwierzytelniania do usługi Azure Media Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wysyłać tokeny uwierzytelniania z klienta do usługi dostarczania klucza usługi Azure Media Services
services: media-services
keywords: token uwierzytelniania w usłudze Content protection DRM,
documentationcenter: ''
author: dbgeorge
manager: jasonsue
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: dwgeo
ms.openlocfilehash: b6aca2928465b73e35ac15f01bb776b1f69add0b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Dowiedz się, jak klienci przekazywania tokenów do usługi dostarczania klucza usługi Azure Media Services
Klienci często dotyczy jak odtwarzacza można przekazać tokenów do usługi Azure Media Services klucza dostawy do weryfikacji, odtwarzacza można uzyskać klucz. Usługa Media Services obsługuje simple web token (SWT) i formatuje tokenu Web JSON (JWT). Token uwierzytelniania jest stosowane do dowolnego typu klucza, niezależnie od tego, czy używać wspólnego szyfrowania lub szyfrowania koperty Advanced Encryption (Standard AES) w systemie.

 W zależności od player i platformy, które są przeznaczone można przekazać tokenu do odtwarzacza w następujący sposób:

- Za pomocą nagłówek autoryzacji HTTP.
    > [!NOTE]
    > Prefiks "Bearer" oczekuje na specyfikacji protokołu OAuth 2.0. Odtwarzacz przykładowy token konfiguracji znajduje się na Azure Media Player [strony pokaz](http://ampdemo.azureedge.net/). Aby ustawić źródło wideo, wybierz **AES (JWT Token)** lub **AES (SWT Token)**. Token jest przekazywany za pośrednictwem nagłówek autoryzacji.

- Poprzez dodanie adresu URL parametr z zapytania "tokenu = tokenvalue."  
    > [!NOTE]
    > Prefiks "Bearer" nie jest oczekiwany. Ponieważ token jest wysyłany przy użyciu adresu URL, należy do ochrony ciąg tokena. Oto kod przykładowy C#, który pokazuje, jak to zrobić:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Za pomocą pola CustomData.
Ta opcja jest używana tylko nabycia licencji PlayReady przy użyciu pola CustomData żądania pozyskiwania licencji PlayReady. W takim przypadku token musi znajdować się w dokumencie XML zgodnie z opisem w tym miejscu:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Umieść token uwierzytelniania w elemencie tokenu.

- Za pomocą listy HTTP Live Streaming (HLS). Jeśli trzeba skonfigurować token uwierzytelniania w przypadku AES + HLS odtwarzania na iOS/Safari, nie istnieje sposób można wysyłać bezpośrednio w tokenie. Aby uzyskać więcej informacji na temat sposobu alternatywny listy odtwarzania tego scenariusza, zobacz [wpis w blogu](http://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Kolejne kroki

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]