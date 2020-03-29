---
title: SDK urządzeń mowy — usługa mowy
titleSuffix: Azure Cognitive Services
description: Wprowadzenie do sdk urządzeń mowy. Usługa Mowy współpracuje z szeroką gamą urządzeń i źródeł dźwięku. Zestaw SDK urządzeń mowy to wstępnie dostrojona biblioteka połączona z specjalnie zaprojektowanymi zestawami programistycznymi macierzy mikrofonowej.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: add0d27ae6a612fed0320b5329d19236b7bbbd01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370648"
---
# <a name="what-is-the-speech-devices-sdk"></a>Co to jest SDK urządzeń mowy?

[Usługa Mowy](overview.md) współpracuje z szeroką gamą urządzeń i źródeł dźwięku. Teraz możesz przejść do następnego poziomu aplikacji mowy z dopasowanym sprzętem i oprogramowaniem. Zestaw SDK urządzeń mowy to wstępnie dostrojona biblioteka połączona z specjalnie zaprojektowanymi zestawami programistycznymi macierzy mikrofonowej.

SDK urządzeń mowy może ci pomóc:

- Szybko przetestuj nowe scenariusze głosowe.
- Łatwiej zintegrować chmurową usługę mowy z urządzeniem.
- Stwórz wyjątkowe środowisko użytkownika dla swoich klientów.

SDK urządzeń mowy zużywa [SDK mowy](speech-sdk.md). Korzystanie z naszych zaawansowanych algorytmów przetwarzania dźwięku z macierzą mikrofonów urządzenia do wysyłania dźwięku do [usługi Mowy.](overview.md) Zapewnia dokładne [rozpoznawanie mowy](speech-to-text.md) dalekiego pola poprzez tłumienie szumów, eliminację echa, kształtowanie wiązki i dereverberation.

Za pomocą SDK urządzeń mowy można również tworzyć urządzenia otoczenia, które mają własne [niestandardowe słowo kluczowe.](speech-devices-sdk-create-kws.md) Niestandardowe słowo kluczowe zapewnia wskazówkę, która rozpoczyna interakcję z użytkownikiem, która jest unikatowa dla Twojej marki.

SDK urządzeń mowy umożliwia różne scenariusze z obsługą głosową, takie jak [asystenty głosowe,](https://aka.ms/bots/speech/va)systemy zamawiania drive-thru, [transkrypcja konwersacji](conversation-transcription-service.md)i inteligentne głośniki. Możesz odpowiadać użytkownikom za pomocą tekstu, mówić do nich głosem domyślnym lub [niestandardowym,](how-to-customize-voice-font.md)dostarczać wyniki wyszukiwania, [tłumaczyć](speech-translation.md) na inne języki i nie tylko. Czekamy na to, co budujesz!

## <a name="get-the-speech-devices-sdk"></a>Pobieranie zestawu Speech Devices SDK

### <a name="android"></a>Android

W przypadku systemu Android urządzenia pobierają najnowszą wersję [SDK urządzeń mowy systemu Android](https://aka.ms/sdsdk-download-android).

### <a name="windows"></a>Windows

W systemie Windows przykładowa aplikacja jest dostarczana jako wieloplatformowa aplikacja Java. Pobierz najnowszą wersję [sdk urządzeń mowy ŚRODOWISKA JRE](https://aka.ms/sdsdk-download-JRE).
Aplikacja jest zbudowana z pakietu Speech SDK i Eclipse Java IDE (v4) w 64-bitowym systemie Windows. Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

### <a name="linux"></a>Linux

W systemie Linux przykładowa aplikacja jest dostarczana jako wieloplatformowa aplikacja Java. Pobierz najnowszą wersję [sdk urządzeń mowy ŚRODOWISKA JRE](https://aka.ms/sdsdk-download-JRE).
Aplikacja jest zbudowana z pakietu Speech SDK i Eclipse Java IDE (v4) na 64-bitowym systemie Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8). Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

Dodatkowe pliki binarne są dostarczane do obsługi nadchodzących urządzeń, [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2), [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter)i [GGEC Speaker](https://aka.ms/sdsdk-download-speaker).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wybieranie urządzenia mowy](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Uzyskaj bezpłatny klucz subskrypcji usługi mowy](get-started.md)
