---
title: Wprowadzenie do interfejsu API rozpoznawania mowy Bing za pomocą biblioteki klienta | Dokumentacja firmy Microsoft
titlesuffix: Azure Cognitive Services
description: Użyj biblioteki klienta mowy Bing w usługach Microsoft Cognitive Services do tworzenia aplikacji, które Konwertuj dźwięk mówiony na tekst.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 89eb18a2b4af76f6489442dc66ab12d0840e92c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60515231"
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

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Przykłady](../samples.md) strona zawiera pełną próbek do użycia biblioteki klienta mowy.
- Jeśli potrzebujesz biblioteki klienckiej, która nie jest jeszcze dostępna, można utworzyć własnych zestawów SDK. Implementowanie [protokołu WebSocket mowy](../API-Reference-REST/websocketprotocol.md) na platformie i Użyj preferowanego języka.

## <a name="license"></a>Licencja

Wszystkie Cognitive Services zestawy SDK i przykłady są licencjonowane z licencją MIT. Aby uzyskać więcej informacji, zobacz [licencji](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
