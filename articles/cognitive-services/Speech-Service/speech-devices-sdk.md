---
title: Zestaw Speech Devices SDK — usługi mowy — informacje
titleSuffix: Azure Cognitive Services
description: Wprowadzenie do zestawu Speech Devices SDK. Usługi mowy współpracować z szerokiej gamy urządzeń i źródła audio. Teraz możesz korzystać z aplikacji mowy na wyższy poziom dzięki dopasowane sprzętu i oprogramowania. Zestawu Speech Devices SDK jest biblioteką wstępnie dostosowane, który jest powiązany z systemom, mikrofon tablicy development Kit.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 341f35d04c7f3f591198acbbf05ac32afe84e95c
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718542"
---
# <a name="about-the-speech-devices-sdk"></a>Zestaw Speech Devices SDK — informacje

[Usług przetwarzania mowy](overview.md) działają z szeroką gamę urządzeń i źródła audio. Teraz możesz korzystać z aplikacji mowy na wyższy poziom dzięki dopasowane sprzętu i oprogramowania. Zestawu Speech Devices SDK jest biblioteką pretuned, który jest powiązany z systemom, mikrofon tablicy development Kit.

Zestawu Speech Devices SDK może ułatwić:

* Szybko przetestować nowe scenariusze głosu.
* Łatwo Zintegruj usług rozpoznawania mowy opartej na chmurze urządzenia.
* Utwórz środowisko użytkownika wyjątkowych dla swoich klientów.

Korzysta z zestawu Speech Devices SDK [zestaw SDK rozpoznawania mowy](speech-sdk.md). Zestaw SDK rozpoznawania mowy używa do wysyłania dźwięk, który jest przetwarzany przez naszych algorytm zaawansowane przetwarzanie audio z tablicy mikrofonu urządzenia do [usług przetwarzania mowy](overview.md). Aby zapewnić bardziej precyzyjne daleko pole używa audio wielokanałowe [rozpoznawania mowy](speech-to-text.md) za pośrednictwem pomijanie szumu, echa i kształtowaniem wiązki oraz dereverberation.

Umożliwia także zestawu Speech Devices SDK do tworzenia otoczenia urządzeń, które mają własne [dostosowane słowo aktywujące](speech-devices-sdk-create-kws.md) tak wskaźnika, który inicjuje interakcji z użytkownikiem jest unikatowy dla Twojej marki.

Zestawu Speech Devices SDK ułatwia różnych scenariuszy z obsługą głosu, takich jak [niestandardowe głosu pierwszego wirtualnego asystentów](https://aka.ms/bots/speech/va), dysk — do porządkowania systemów, [transkrypcji konwersacji](conversation-transcription-service.md)i inteligentnych Prelegenci. Pozwalające reagować na użytkowników z tekstu, mowy posiadającej w domyślnej lub [niestandardowych voice](how-to-customize-voice-font.md), podaj wyniki wyszukiwania [tłumaczenie](speech-translation.md) na inne języki itd. Chętnie oglądanie, jakie zapewnia!

## <a name="get-the-speech-devices-sdk"></a>Pobieranie zestawu Speech Devices SDK

### <a name="android"></a>Android

Pobierania najnowszej wersji dla systemu Android urządzenia [Android zestawu Speech Devices SDK](https://aka.ms/sdsdk-download-android).

### <a name="windows"></a>Windows

Dla Windows Przykładowa aplikacja jest dostarczany jako aplikacji w języku Java dla wielu platform. Pobierz najnowszą wersję [zestawu Speech Devices SDK dla środowiska JRE](https://aka.ms/sdsdk-download-JRE).
Aplikacja jest tworzone za pomocą pakietu SDK rozpoznawania mowy i środowisko Eclipse IDE Java (v4) na Windows 64-bitowych. Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

### <a name="linux"></a>Linux

Dla systemu Linux Przykładowa aplikacja jest dostarczany jako aplikacji w języku Java dla wielu platform. Pobierz najnowszą wersję [zestawu Speech Devices SDK dla środowiska JRE](https://aka.ms/sdsdk-download-JRE).
Aplikacja jest skompilowana przy użyciu pakietu SDK rozpoznawania mowy i środowiska Eclipse IDE Java (v4) w systemie 64-bitowych Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9). Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wybierz swoje urządzenie mowy](get-speech-devices-sdk.md)
>
> [!div class="nextstepaction"]
> [Bezpłatnie Uzyskaj klucz subskrypcji usług przetwarzania mowy](get-started.md)
