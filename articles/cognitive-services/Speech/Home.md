---
title: Usługa Microsoft mowy | Dokumentacja firmy Microsoft
description: Interfejs API mowy Microsoft umożliwia dodawanie działaniami mowy do aplikacji, w tym w czasie rzeczywistym interakcji z użytkownikami.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: c041132e992f07e94e4b6669ec7ce174f7c2d0dd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347421"
---
# <a name="microsoft-speech-api-overview"></a>Przegląd interfejsu API mowy firmy Microsoft

Oparte na chmurze mowy interfejsu API usługi Microsoft udostępnia deweloperom łatwe tworzenie zaawansowanych funkcji mowy w swoich aplikacjach formantu poleceń głosowych, okno dialogowe użytkownika przy użyciu konwersacji mowy fizyczną i przekształcania mowy i dyktowania. Interfejs API mowy Microsoft obsługuje zarówno *mowy na tekst* i *tekst na mowę* konwersji.

- **Mowy na tekst** interfejsu API konwertuje ludzkiej mowy na tekst, który może służyć jako wejście lub polecenia do kontroli aplikacji.
- **Tekst na mowę** interfejsu API konwertuje tekst do strumieni audio, które mogą być odtwarzane użytkownika aplikacji.

## <a name="speech-to-text-speech-recognition"></a>Mowy na tekst (rozpoznawanie mowy)

Rozpoznawanie mowy Microsoft interfejsu API *transcribes* strumieni audio w aplikacji można wyświetlić dla użytkownika lub obsługiwanie jako tekst polecenia danych wejściowych. Zapewnia dwa sposoby dla deweloperów dodać swoje aplikacje mowy: interfejsów API REST **lub** bibliotek klienckich opartych na protokołu Websocket.

- [Interfejsów API REST](GetStarted/GetStartedREST.md): deweloperzy mogą używać połączenia HTTP z ich aplikacji z usługą rozpoznawania mowy.
- [Biblioteki klienta](GetStarted/GetStartedClientLibraries.md): dla zaawansowanych funkcji, deweloperzy można pobrać bibliotek klienta Microsoft Speech i link do ich aplikacji.  Biblioteki klienta są dostępne na różnych platformach (z systemem Windows, Android, iOS) przy użyciu różnych języków (C#, Java, JavaScript, ObjectiveC). W przeciwieństwie do interfejsów API REST bibliotek klienckich korzystać z protokołu na podstawie obiektu Websocket.

| Przypadki zastosowań | [Interfejsy API REST](GetStarted/GetStartedREST.md) | [Biblioteki klienta](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Konwertuj krótko rozmowy audio, na przykład poleceń (audio długość < 15 s) bez wyników pośrednich | Yes | Yes |
| Konwertuj audio długa (> 15 s) | Nie | Yes |
| Strumień audio z wyników pośrednich potrzeby | Nie | Yes |
| Zrozumienie tekst przekonwertowany z przy użyciu LUIS audio | Nie | Yes |

Deweloperzy niezależnie od tego podejścia wybierz (interfejsy API REST lub bibliotek klienckich), usługa mowy Microsoft obsługuje następujące:

- Zaawansowane mowy rozpoznawania technologii firmy Microsoft, które są używane przez Cortana, dyktowania Office Translator pakietu Office i innych produktów firmy Microsoft.
- Rozpoznawanie ciągłej w czasie rzeczywistym. Rozpoznawanie mowy interfejsu API umożliwia użytkownikom wykonać transkrypcji audio na tekst w czasie rzeczywistym i obsługuje do odbierania wyników pośrednich wyrazy, które zostały uznane wykonanej do tej pory. Usługa mowy obsługuje również wykrywania zakończenia z mowy. Ponadto użytkownicy mogą wybrać dodatkowe możliwości formatowania, takich jak wielkość liter i znaków interpunkcyjnych, niestosownych wyrażeń maskowania i normalizacji tekstu.
- Obsługuje zoptymalizowanych pod kątem wyników rozpoznawania mowy *interakcyjne*, *konwersacji*, i *dyktowania* scenariuszy. Dla scenariuszy użytkownika, które wymagają modeli dostosowane języka i modele akustycznego [niestandardowej usługi rozpoznawania mowy](../custom-speech-service/cognitive-services-custom-speech-home.md) służy do tworzenia modeli mowy dostosowane do użytkowników i aplikacji.
- Obsługa wielu języków w wielu dialekty. Aby uzyskać pełną listę obsługiwanych języków w każdym trybie rozpoznawania zobacz [języków rozpoznawania](api-reference-rest/supportedlanguages.md).
- Integracja z opis języka. Oprócz konwertowanie wejście audio na tekst, *mowy na tekst* zapewnia dodatkowe możliwości, aby zrozumieć, co oznacza tekst aplikacji. Używa [Service(LUIS) inteligentnego opis języka](../LUIS/Home.md) do wyodrębniania rozpoznany intencje i jednostek.

### <a name="next-steps"></a>Kolejne kroki

- Wprowadzenie do używania usługi rozpoznawania mowy firmy Microsoft z [interfejsów API REST](GetStarted/GetStartedREST.md) lub [bibliotek klienckich](GetStarted/GetStartedClientLibraries.md).
- Zapoznaj się z [przykładowe aplikacje](samples.md) w preferowanym języku programowania.
- Przejdź do sekcji odwołania, aby znaleźć [protokołu mowy Microsoft](API-Reference-REST/websocketprotocol.md) szczegóły i odwołania do interfejsu API.

## <a name="text-to-speech-speech-synthesis"></a>Tekst na mowę (mowy syntezy)

*Tekst na mowę* interfejsów API umożliwiają REST przekonwertować tekst strukturalnych strumieniem audio. Interfejsy API zapewniają szybki tekst na mowę konwersji w różnych językach i głosów. Ponadto użytkownicy mają również możliwość zmiany cechy audio, takie jak wymowa, woluminu, wysokość itp. przy użyciu SSML tagów.

### <a name="next-steps"></a>Kolejne kroki

- Wprowadzenie do używania usługi tekst na mowę firmy Microsoft: [tekst na mowę dokumentacja interfejsu API](api-reference-rest/bingvoiceoutput.md). Aby uzyskać pełną listę języków i głosy są obsługiwane przez tekst na mowę, zobacz [obsługiwanych ustawień regionalnych i czcionek głosu](api-reference-rest/bingvoiceoutput.md#SupLocales).
