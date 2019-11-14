---
title: Informacje o zestawie SDK urządzeń mowy — Speech Service
titleSuffix: Azure Cognitive Services
description: Wprowadzenie do zestawu Speech Devices SDK. Usługa rozpoznawania mowy współpracuje z szerokiej gamy urządzeń i źródła audio. Zestawu Speech Devices SDK jest biblioteką wstępnie dostosowane, który jest powiązany z systemom, mikrofon tablicy development Kit.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 31d1a60cc6c1316dcfccb4f9ba2571708fddd991
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072375"
---
# <a name="about-the-speech-devices-sdk"></a>Informacje o zestawie SDK urządzeń mowy

[Usługi mowy](overview.md) współpracują z szeroką gamą urządzeń i źródeł audio. Teraz możesz korzystać z aplikacji mowy na wyższy poziom dzięki dopasowane sprzętu i oprogramowania. Zestawu Speech Devices SDK jest biblioteką pretuned, który jest powiązany z systemom, mikrofon tablicy development Kit.

Zestawu Speech Devices SDK może ułatwić:

- Szybko przetestować nowe scenariusze głosu.
- Łatwiejsza integracja opartych na chmurze usług mowy z urządzeniem.
- Utwórz środowisko użytkownika wyjątkowych dla swoich klientów.

Korzysta z zestawu Speech Devices SDK [zestaw SDK rozpoznawania mowy](speech-sdk.md). Używa zestawu Speech SDK do wysyłania audio, który jest przetwarzany przez nasz algorytm zaawansowanego przetwarzania dźwięku z macierzy mikrofonu urządzenia do [usługi mowy](overview.md). Aby zapewnić bardziej precyzyjne daleko pole używa audio wielokanałowe [rozpoznawania mowy](speech-to-text.md) za pośrednictwem pomijanie szumu, echa i kształtowaniem wiązki oraz dereverberation.

Za pomocą zestawu Speech Devices SDK można także tworzyć urządzenia otoczenia, które mają własne [niestandardowe słowo kluczowe](speech-devices-sdk-create-kws.md) , dzięki czemu wskaźnik inicjujący interakcję użytkownika jest unikatowy dla Twojej marki.

Zestaw SDK urządzeń mowy ułatwia różne scenariusze z obsługą głosu, takie jak [asystentów głosowych](https://aka.ms/bots/speech/va), systemów do określania kolejności, [transkrypcji konwersacji](conversation-transcription-service.md)i inteligentnych głośników. Pozwalające reagować na użytkowników z tekstu, mowy posiadającej w domyślnej lub [niestandardowych voice](how-to-customize-voice-font.md), podaj wyniki wyszukiwania [tłumaczenie](speech-translation.md) na inne języki itd. Chętnie oglądanie, jakie zapewnia!

## <a name="get-the-speech-devices-sdk"></a>Pobieranie zestawu Speech Devices SDK

### <a name="android"></a>Android

W przypadku urządzeń z systemem Android pobierana jest Najnowsza wersja [zestawu SDK urządzeń z systemem Android](https://aka.ms/sdsdk-download-android).

### <a name="windows"></a>Windows

W przypadku systemu Windows Przykładowa aplikacja jest dostarczana jako aplikacja Java dla wielu platform. Pobierz najnowszą wersję [zestawu SDK urządzeń mowy środowiska JRE](https://aka.ms/sdsdk-download-JRE).
Aplikacja została skompilowana przy użyciu pakietu zestawu Speech SDK, a środowisko IDE środowiska Java (v4) w systemie 64-bitowym. Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

### <a name="linux"></a>Linux

W przypadku systemu Linux Przykładowa aplikacja jest udostępniana jako aplikacja w języku Java dla wielu platform. Pobierz najnowszą wersję [zestawu SDK urządzeń mowy środowiska JRE](https://aka.ms/sdsdk-download-JRE).
Aplikacja została skompilowana przy użyciu pakietu zestawu Speech SDK, a środowisko IDE języka Java (v4) w systemie 64-bitowym Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9). Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wybieranie urządzenia mowy](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Uzyskaj bezpłatnie klucz subskrypcji usługi mowy](get-started.md)
