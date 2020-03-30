---
title: Przekazywanie tokenów uwierzytelniania do usługi Azure Media Services | Dokumenty firmy Microsoft
description: Dowiedz się, jak wysyłać tokeny uwierzytelniania od klienta do usługi dostarczania kluczy usługi Azure Media Services
services: media-services
keywords: ochrona zawartości, DRM, uwierzytelnianie tokenów
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 15d4cbc372f5d5ec0d323170189329152ed436e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73684943"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Dowiedz się, jak klienci przekazują tokeny do usługi dostarczania kluczy usługi Azure Media Services
Klienci często pytają, jak gracz może przekazać tokeny do usługi dostarczania kluczy usługi Azure Media Services w celu weryfikacji, aby odtwarzacz mógł uzyskać klucz. Usługi Media Services obsługują formaty prostego tokenu sieci web (SWT) i tokenu JSON Web Token (JWT). Uwierzytelnianie tokenu jest stosowane do dowolnego typu klucza, niezależnie od tego, czy w systemie jest używane typowe szyfrowanie, czy szyfrowanie koperty Advanced Encryption Standard (AES).

 W zależności od gracza i platformy, na którą kierujesz reklamy, możesz przekazać go graczowi w następujący sposób:

- Za pośrednictwem nagłówka autoryzacji HTTP.
    > [!NOTE]
    > Prefiks "Na okaziciela" jest oczekiwany zgodnie ze specyfikacją OAuth 2.0. Przykładowy odtwarzacz z konfiguracją tokenu jest hostowany na [stronie demonstracyjnej](https://ampdemo.azureedge.net/)programu Azure Media Player . Aby ustawić źródło wideo, wybierz pozycję **AES (Token JWT)** lub **AES (SWT Token).** Token jest przekazywany za pośrednictwem nagłówka Autoryzacja.

- Poprzez dodanie parametru zapytania adresu URL z "token=tokenvalue".  
    > [!NOTE]
    > Prefiks "Na okaziciela" nie jest oczekiwany. Ponieważ token jest wysyłany za pośrednictwem adresu URL, należy opancerzyć ciąg tokenu. Oto przykładowy kod języka C#, który pokazuje, jak to zrobić:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Za pośrednictwem pola Niestandardowe dane.
Ta opcja jest używana tylko do pozyskiwania licencji PlayReady, za pośrednictwem pola CustomData wyzwania nabycia licencji PlayReady. W takim przypadku token musi znajdować się wewnątrz dokumentu XML, jak opisano w tym miejscu:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Umieść token uwierzytelniania w tokenie elementu.

- Za pośrednictwem alternatywnej listy odtwarzania HTTP Live Streaming (HLS). Jeśli chcesz skonfigurować uwierzytelnianie tokenu dla odtwarzania AES + HLS w systemie iOS/Safari, nie ma sposobu, w jaki można bezpośrednio wysłać token. Aby uzyskać więcej informacji na temat naprzemienności listy odtwarzania w celu włączenia tego [scenariusza,](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)zobacz ten wpis w blogu .

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
