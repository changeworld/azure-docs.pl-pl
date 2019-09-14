---
title: Usługa rozpoznawanie mowy Bing firmy Microsoft | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Użyj Speech API Microsoft, aby dodać do aplikacji akcje oparte na mowie, w tym interakcje w czasie rzeczywistym z użytkownikami.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ff9de2557583eecd5ddb2acd97b445a93abc5fb6
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966644"
---
# <a name="what-is-bing-speech"></a>Co to jest rozpoznawanie mowy Bing?

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Oparta na chmurze interfejs API rozpoznawania mowy Bing firmy Microsoft oferuje deweloperom prosty sposób tworzenia zaawansowanych funkcji z obsługą mowy w aplikacjach, takich jak kontrolka poleceń głosowych, okno dialogowe użytkownika przy użyciu zwykłej konwersacji mowy oraz transkrypcja mowy i Funkcja dyktowania. Speech API firmy Microsoft obsługuje zamianę *mowy na tekst* i *Zamiana tekstu na mowę* konwersji.

- **Zamiana mowy na tekst** Interfejs API konwertuje ludzkie mowę na tekst, który może być używany jako dane wejściowe lub polecenia sterujące aplikacji.
- **Zamiana tekstu na mowę** Interfejs API konwertuje tekst na strumienie audio, które mogą być odtwarzane użytkownikowi aplikacji.

## <a name="speech-to-text-speech-recognition"></a>Zamiana mowy na tekst (rozpoznawanie mowy)

Interfejs API rozpoznawania mowy firmy Microsoft *przekształca* strumienie audio do tekstu, który aplikacja może wyświetlać użytkownikowi lub działa jako dane wejściowe polecenia. Udostępnia dwa sposoby, aby deweloperzy mogli dodawać mowę do swoich aplikacji: Interfejsy API REST **lub** biblioteki klienckie oparte na protokole WebSocket.

- [Interfejsy API REST](GetStarted/GetStartedREST.md): Deweloperzy mogą używać wywołań HTTP z aplikacji do usługi w celu rozpoznawania mowy.
- [Biblioteki klienckie](GetStarted/GetStartedClientLibraries.md): Aby uzyskać zaawansowane funkcje, deweloperzy mogą pobrać biblioteki klienckie Microsoft Speech i połączyć się ze swoimi aplikacjami.  Biblioteki klienckie są dostępne na różnych platformach (Windows, Android, iOS) przy użyciu różnych języków (C#, Java, JavaScript, ObjectiveC). W przeciwieństwie do interfejsów API REST, biblioteki klienta wykorzystują protokół oparty na protokole WebSocket.

| Przypadki zastosowań | [Interfejsy API REST](GetStarted/GetStartedREST.md) | [Biblioteki klienckie](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Konwertuj krótki dźwięk mówiony, na przykład polecenia (długość audio < 15 s) bez wyników pośrednich | Tak | Tak |
| Konwertuj długi dźwięk (> 15 s) | Nie | Tak |
| Strumieniowe przesyłanie dźwięku z pożądanymi pośrednimi wynikami | Nie | Tak |
| Zapoznaj się z tekstem konwertowanym z dźwięku przy użyciu LUIS | Nie | Tak |

W zależności od tego, co deweloperzy wybierają (interfejsy API REST lub biblioteki klienckie), usługa Microsoft Speech Service obsługuje następujące czynności:

- Zaawansowane technologie rozpoznawania mowy firmy Microsoft, które są używane przez Cortana, Dyktowanie pakietu Office oraz inne produkty firmy Microsoft.
- W czasie rzeczywistym rozpoznawania ciągłe. Interfejs API rozpoznawania mowy umożliwia użytkownikom transkrypcja dźwięk do tekstu w czasie rzeczywistym i obsługuje pobieranie pośrednich wyników wyrazów, które zostały rozpoznane do tej pory. Usługa mowy obsługuje również wykrywanie końców mowy. Ponadto użytkownicy mogą wybierać dodatkowe możliwości formatowania, takie jak wielkie litery i interpunkcja, maskowanie wulgarności i Normalizacja tekstu.
- Obsługuje zoptymalizowane wyniki rozpoznawania mowy dla scenariuszy *interaktywnych*, *konwersacji*i *dyktowania* . W przypadku scenariuszy użytkowników, które wymagają niestandardowych modeli językowych i modeli akustycznych, [Custom Speech Service](../custom-speech-service/cognitive-services-custom-speech-home.md) umożliwia tworzenie modeli mowy, które są dostosowane do aplikacji i użytkowników.
- Obsługa wielu języków mówionych w wielu dialektach. Aby uzyskać pełną listę obsługiwanych języków w każdym trybie rozpoznawania, zobacz [Języki rozpoznawania](api-reference-rest/supportedlanguages.md).
- Integracja z funkcją interpretacji języka. Oprócz konwersji wejściowego dźwięku na tekst, *Zamiana mowy na tekst* zapewnia aplikacjom dodatkową możliwość zrozumienia, co oznacza tekst. Używa [Language Understanding Intelligent Service (Luis)](../LUIS/what-is-luis.md) w celu wyodrębnienia intencji i jednostek z rozpoznanego tekstu.

### <a name="next-steps"></a>Następne kroki

- Rozpocznij korzystanie z usługi rozpoznawania mowy firmy Microsoft z [interfejsami API REST](GetStarted/GetStartedREST.md) lub [bibliotekami klienckimi](GetStarted/GetStartedClientLibraries.md).
- Zapoznaj się z [przykładowymi aplikacjami](samples.md) w preferowanym języku programowania.
- Przejdź do sekcji Dokumentacja, aby znaleźć szczegóły [protokołu Microsoft Speech](API-Reference-REST/websocketprotocol.md) i dokumentacja interfejsu API.

## <a name="text-to-speech-speech-synthesis"></a>Zamiana tekstu na mowę (synteza mowy)

*Zamiana tekstu na mowę* Interfejsy API używają funkcji REST do konwertowania tekstu strukturalnego na strumień audio. Interfejsy API zapewniają szybką konwersję tekstu na mowę na różne głosy i języki. Ponadto użytkownicy mogą również zmieniać cechy audio, takie jak wymowa, głośność, gęstość itd. Używanie tagów SSML.

### <a name="next-steps"></a>Następne kroki

- Zacznij korzystać z usługi Microsoft Text to Speech Service: [Odwołanie interfejs API zamiany tekstu na mowę](api-reference-rest/bingvoiceoutput.md). Aby uzyskać pełną listę języków i głosów obsługiwanych przez zamiana tekstu na mowę, zobacz [obsługiwane ustawienia regionalne i czcionki głosowe](api-reference-rest/bingvoiceoutput.md#SupLocales).
