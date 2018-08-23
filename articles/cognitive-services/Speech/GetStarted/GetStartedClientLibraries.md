---
title: Rozpoczynanie pracy z interfejsem API rozpoznawania mowy firmy Microsoft przy użyciu biblioteki klienta mowy Bing | Dokumentacja firmy Microsoft
description: Użyj biblioteki klienta usługi mowy firmy Microsoft w usługach Microsoft Cognitive Services do tworzenia aplikacji, które Konwertuj dźwięk mówiony na tekst.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: f4b6a97260c6dc176600af8844001e4de819ff7c
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2018
ms.locfileid: "42357514"
---
# <a name="get-started-with-bing-speech-service-client-libraries"></a>Rozpoczynanie pracy z bibliotekami klienckimi usługa rozpoznawania mowy Bing

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

> [!NOTE] 
W maju 2018 r. wydaliśmy się również nowe [usługa rozpoznawania mowy](/speech-service/overview.md) w publicznej wersji zapoznawczej. Firma Microsoft zachęca do [za darmo wypróbować](/speech-service/get-started.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Przykłady](../samples.md) strona zawiera pełną próbek do użycia biblioteki klienta mowy.
- Jeśli potrzebujesz biblioteki klienckiej, która nie jest jeszcze dostępna, można utworzyć własnych zestawów SDK. Implementowanie [protokołu WebSocket mowy](../API-Reference-REST/websocketprotocol.md) na platformie i Użyj preferowanego języka.

## <a name="license"></a>Licencja

Wszystkie Cognitive Services zestawy SDK i przykłady są licencjonowane z licencją MIT. Aby uzyskać więcej informacji, zobacz [licencji](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
