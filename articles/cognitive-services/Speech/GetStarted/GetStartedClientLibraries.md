---
title: Rozpoczynanie pracy z interfejsem API rozpoznawania rozpoznawanie mowy Bing przy użyciu bibliotek klienckich | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Użyj rozpoznawanie mowy Bing bibliotek klienckich w programie Microsoft Cognitive Services do tworzenia aplikacji, które konwertują dźwięk mówiony na tekst.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 8ea0c9e7104b29456749c7f1af2a671b32a8fde2
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966843"
---
# <a name="get-started-with-bing-speech-service-client-libraries"></a>Wprowadzenie do bibliotek klienckich usługi rozpoznawanie mowy Bing Service

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Oprócz bezpośredniego żądania HTTP za pośrednictwem interfejsu API REST usługa rozpoznawanie mowy Bing udostępnia deweloperom biblioteki klienta mowy w różnych językach. Biblioteki klienta mowy:

- Obsługa bardziej zaawansowanych funkcji rozpoznawania mowy, takich jak pośrednie wyniki w czasie rzeczywistym, długi strumień audio (do 10 minut) i ciągłe rozpoznawanie.
- Podaj prosty i idiomatyczne interfejs API w języku preferencji.
- Ukryj szczegóły komunikacji niskiego poziomu.

Obecnie dostępne są następujące rozpoznawanie mowy Bing biblioteki klienckie:

- [C#Biblioteka pulpitu](GetStartedCSharpDesktop.md)
- [C#Biblioteka usług](GetStartedCSharpServiceLibrary.md)
- [Biblioteka języka JavaScript](GetStartedJSWebsockets.md)
- [Biblioteka Java dla systemu Android](GetStartedJavaAndroid.md)
- [Biblioteka języka C dla systemu iOS](Get-Started-ObjectiveC-iOS.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

- Na stronie [przykłady](../samples.md) znajdują się kompletne przykłady dotyczące korzystania z bibliotek klienckich mowy.
- Jeśli potrzebujesz biblioteki klienckiej, która nie jest jeszcze obsługiwana, możesz utworzyć własny zestaw SDK. Zaimplementuj [Protokół WebSocket mowy](../API-Reference-REST/websocketprotocol.md) na platformie i Użyj wybranego języka.

## <a name="license"></a>Licencja

Wszystkie Cognitive Services zestawy SDK i przykłady są licencjonowane z licencją MIT. Aby uzyskać więcej informacji, zobacz [licencja](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
