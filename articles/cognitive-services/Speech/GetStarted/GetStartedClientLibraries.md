---
title: Wprowadzenie do interfejsu API rozpoznawania mowy Bing za pomocą biblioteki klienta | Dokumentacja firmy Microsoft
titlesuffix: Azure Cognitive Services
description: Użyj biblioteki klienta mowy Bing w usługach Microsoft Cognitive Services do tworzenia aplikacji, które Konwertuj dźwięk mówiony na tekst.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 2682236ae9f9256b91d1259248e72139c907c8e5
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49345020"
---
# <a name="get-started-with-bing-speech-service-client-libraries"></a>Rozpoczynanie pracy z bibliotekami klienckimi usługa rozpoznawania mowy Bing

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Poza wprowadzeniem bezpośrednich żądań HTTP za pośrednictwem interfejsu API REST, usługa rozpoznawania mowy Bing oferuje deweloperom biblioteki klienta mowy w różnych językach. Biblioteki klienta mowy:

- Obsługuje bardziej zaawansowane funkcje rozpoznawanie mowy, takie jak wyników pośrednich w czasie rzeczywistym, strumienia długo audio (maksymalnie 10 minut) i ciągłe rozpoznawania.
- Zapewniają proste i idiomatyczną interfejsu API w języku preferowanym.
- Ukryj szczegóły niskiego poziomu komunikacji.

Obecnie dostępne są następujące biblioteki klienta mowy Bing:

- [Biblioteka pulpitu języka C#](GetStartedCSharpDesktop.md)
- [Biblioteka usługi C#](GetStartedCSharpServiceLibrary.md)
- [Biblioteka języka JavaScript](GetStartedJSWebsockets.md)
- [Biblioteka języka Java dla systemu Android](GetStartedJavaAndroid.md)
- [Biblioteka języka Objective-C dla systemu iOS](Get-Started-ObjectiveC-iOS.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Przykłady](../samples.md) strona zawiera pełną próbek do użycia biblioteki klienta mowy.
- Jeśli potrzebujesz biblioteki klienckiej, która nie jest jeszcze dostępna, można utworzyć własnych zestawów SDK. Implementowanie [protokołu WebSocket mowy](../API-Reference-REST/websocketprotocol.md) na platformie i Użyj preferowanego języka.

## <a name="license"></a>Licencja

Wszystkie Cognitive Services zestawy SDK i przykłady są licencjonowane z licencją MIT. Aby uzyskać więcej informacji, zobacz [licencji](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).

