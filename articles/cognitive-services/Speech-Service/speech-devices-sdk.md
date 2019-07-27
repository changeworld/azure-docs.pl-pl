---
title: Informacje o zestawie SDK urządzeń mowy — Speech Service
titleSuffix: Azure Cognitive Services
description: Wprowadzenie do zestawu Speech Devices SDK. Usługa rozpoznawania mowy współpracuje z szerokiej gamy urządzeń i źródła audio. Teraz możesz korzystać z aplikacji mowy na wyższy poziom dzięki dopasowane sprzętu i oprogramowania. Zestawu Speech Devices SDK jest biblioteką wstępnie dostosowane, który jest powiązany z systemom, mikrofon tablicy development Kit.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: f2819dd2194193ffa8171034fdbe01ac4cb26174
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558925"
---
# <a name="about-the-speech-devices-sdk"></a>Informacje o zestawie SDK urządzeń mowy

[Usługi mowy](overview.md) współpracują z szeroką gamą urządzeń i źródeł audio. Teraz możesz korzystać z aplikacji mowy na wyższy poziom dzięki dopasowane sprzętu i oprogramowania. Zestawu Speech Devices SDK jest biblioteką pretuned, który jest powiązany z systemom, mikrofon tablicy development Kit.

Zestawu Speech Devices SDK może ułatwić:

* Szybko przetestować nowe scenariusze głosu.
* Łatwiejsza integracja opartych na chmurze usług mowy z urządzeniem.
* Utwórz środowisko użytkownika wyjątkowych dla swoich klientów.

Korzysta z zestawu Speech Devices SDK [zestaw SDK rozpoznawania mowy](speech-sdk.md). Używa zestawu Speech SDK do wysyłania audio, który jest przetwarzany przez nasz algorytm zaawansowanego przetwarzania dźwięku z macierzy mikrofonu urządzenia do [usługi mowy](overview.md). Aby zapewnić bardziej precyzyjne daleko pole używa audio wielokanałowe [rozpoznawania mowy](speech-to-text.md) za pośrednictwem pomijanie szumu, echa i kształtowaniem wiązki oraz dereverberation.

Za pomocą zestawu Speech Devices SDK można także tworzyć urządzenia otoczenia, które mają własne [dostosowane programy wznawiania](speech-devices-sdk-create-kws.md) , dzięki czemu wskaźnik inicjujący interakcję użytkownika jest unikatowy dla Twojej marki.

Zestaw SDK urządzenia mowy umożliwia obsługę różnorodnych scenariuszy z obsługą głosu, takich jak [niestandardowe Asystenci wirtualne w pierwszej](https://aka.ms/bots/speech/va)kolejności, dyski, systemy zamawiania, [transkrypcja konwersacji](conversation-transcription-service.md)i inteligentne głośniki. Pozwalające reagować na użytkowników z tekstu, mowy posiadającej w domyślnej lub [niestandardowych voice](how-to-customize-voice-font.md), podaj wyniki wyszukiwania [tłumaczenie](speech-translation.md) na inne języki itd. Chętnie oglądanie, jakie zapewnia!

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
>
> [!div class="nextstepaction"]
> [Uzyskaj bezpłatnie klucz subskrypcji usługi Speech Services](get-started.md)
