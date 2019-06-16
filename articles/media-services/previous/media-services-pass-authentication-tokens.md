---
title: Przekazywanie tokenów uwierzytelniania usługi Azure Media Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wysyłać tokeny uwierzytelniania od klienta do usługi dostarczania kluczy usługi Azure Media Services
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
ms.date: 03/19/2019
ms.author: dwgeo
ms.openlocfilehash: 71925a1ee67956df45901950b2a59fa4c1b458a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61463229"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Dowiedz się, jak klienci przekazywanie tokenów usługi dostarczania kluczy usługi Azure Media Services
Klienci często pytają, jak Odtwarzacz można przekazać tokenów do usługi dostarczania kluczy usługi Azure Media Services w celu weryfikacji, gracz może uzyskać klucz. Usługa Media Services obsługuje prosty token sieci web (SWT) i formatuje tokenu Web JSON (JWT). Token uwierzytelniania są stosowane do dowolnego typu klucza, niezależnie od tego, czy używać wspólnych lub szyfrowania Advanced Encryption Standard (AES) koperty w systemie.

 W zależności od platformy docelowej i odtwarzacza można przekazać token za pomocą odtwarzacza w następujący sposób:

- Za pomocą nagłówka autoryzacji HTTP.
    > [!NOTE]
    > Prefiks "Bearer" oczekuje na specyfikacji protokołu OAuth 2.0. Odtwarzacz próbki, przy użyciu tokenu konfiguracji znajduje się na usługi Azure Media Player [strona pokazu](https://ampdemo.azureedge.net/). Aby ustawić źródło wideo, wybierz **AES (tokenu JWT)** lub **AES (SWT tokenu)** . Token jest przekazywany za pośrednictwem nagłówka autoryzacji.

- Przez dodanie ich do adresu URL zapytania parametr "token = tokenvalue."  
    > [!NOTE]
    > Prefiks "Bearer" nie jest oczekiwany. Token jest wysyłany przy użyciu adresu URL, dlatego musisz bilecie ciąg tokenu. Oto C# przykładowego kodu, który pokazuje, jak to zrobić:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Za pomocą pola CustomData.
Ta opcja służy do tylko nabycie licencji PlayReady przy użyciu pola CustomData żądania pozyskiwania licencji PlayReady. W tym przypadku token musi znajdować się w dokumencie XML zgodnie z opisem w tym miejscu:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Umieść swój token uwierzytelniania w elemencie tokenu.

- Za pomocą alternatywnej listy odtwarzania HTTP Live Streaming (HLS). Jeśli musisz skonfigurować uwierzytelnianie przy użyciu tokenów AES + HLS odtwarzania na iOS/Safari, nie można wysyłać bezpośrednio w tokenie. Aby uzyskać więcej informacji na temat sposobu alternatywny listy odtwarzania, aby umożliwić wykonanie tego scenariusza, zobacz ten [wpis w blogu](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Kolejne kroki

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]