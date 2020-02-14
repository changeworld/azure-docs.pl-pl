---
title: Informacje o zestawie SDK urządzeń mowy — Speech Service
titleSuffix: Azure Cognitive Services
description: Wprowadzenie do zestawu Speech Devices SDK. Usługa mowy współpracuje z wieloma urządzeniami i źródłami audio. Zestawu Speech Devices SDK jest biblioteką wstępnie dostosowane, który jest powiązany z systemom, mikrofon tablicy development Kit.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: erhopf
ms.openlocfilehash: 854e7beb2afd8ae838455f77ff448f13d8b3fbea
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188977"
---
# <a name="about-the-speech-devices-sdk"></a>Informacje o zestawie Speech Devices SDK

[Usługa mowy](overview.md) współpracuje z wieloma urządzeniami i źródłami audio. Teraz możesz korzystać z aplikacji mowy na wyższy poziom dzięki dopasowane sprzętu i oprogramowania. Zestawu Speech Devices SDK jest biblioteką pretuned, który jest powiązany z systemom, mikrofon tablicy development Kit.

Zestawu Speech Devices SDK może ułatwić:

- Szybko przetestować nowe scenariusze głosu.
- Łatwo zintegrować usługę rozpoznawania mowy opartej na chmurze urządzenia.
- Utwórz środowisko użytkownika wyjątkowych dla swoich klientów.

Zestaw SDK urządzeń mowy korzysta z [zestawu Speech SDK](speech-sdk.md). Korzystając z naszych zaawansowanych algorytmów przetwarzania dźwięku z tablicą mikrofonu urządzenia, można wysłać dźwięk do [usługi mowy](overview.md). Zapewnia dokładne [rozpoznawanie mowy](speech-to-text.md) z odłożeniami przy użyciu funkcji pomijania szumów, anulowania echa, Beamforming i dereverberation.

Za pomocą zestawu Speech Devices SDK można także tworzyć urządzenia otoczenia z własnymi własnymi [słowami kluczowymi](speech-devices-sdk-create-kws.md). Słowo kluczowe Custom zawiera wskaźnik uruchamiający interakcję użytkownika, która jest unikatowa dla Twojej marki.

Zestaw SDK urządzeń mowy umożliwia korzystanie z różnych scenariuszy z obsługą głosu, takich jak [asystentów głosowych](https://aka.ms/bots/speech/va), systemów porządkujących się na stacjach, [transkrypcji konwersacji](conversation-transcription-service.md)i inteligentnych głośników. Możesz odpowiedzieć użytkownikom z tekstem, mówić z powrotem do nich przy użyciu domyślnego lub [niestandardowego głosu](how-to-customize-voice-font.md), podać wyniki wyszukiwania, [przetłumaczać](speech-translation.md) je na inne języki i nie tylko. Chętnie oglądanie, jakie zapewnia!

## <a name="get-the-speech-devices-sdk"></a>Pobieranie zestawu Speech Devices SDK

### <a name="android"></a>Android

W przypadku systemu Android urządzenia pobierają najnowszą wersję [zestawu SDK urządzeń z systemem Android Speech](https://aka.ms/sdsdk-download-android).

### <a name="windows"></a>System Windows

W przypadku systemu Windows Przykładowa aplikacja jest udostępniana jako aplikacja Java dla wielu platform. Pobierz najnowszą wersję [zestawu SDK urządzeń mowy środowiska JRE](https://aka.ms/sdsdk-download-JRE).
Aplikacja została skompilowana przy użyciu pakietu zestawu Speech SDK, a środowisko IDE środowiska Java (v4) w systemie 64-bitowym. Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

### <a name="linux"></a>Linux

W przypadku systemu Linux Przykładowa aplikacja jest udostępniana jako aplikacja w języku Java dla wielu platform. Pobierz najnowszą wersję [zestawu SDK urządzeń mowy środowiska JRE](https://aka.ms/sdsdk-download-JRE).
Aplikacja została skompilowana przy użyciu pakietu zestawu Speech SDK, a środowisko IDE języka Java (v4) w systemie 64-bitowym Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9). Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

Dodatkowe pliki binarne są udostępniane do obsługi nadchodzących urządzeń, [zestawu DDK dla systemu roobo v2](https://aka.ms/sdsdk-download-roobov2)i [zestawu DDK Urbetter](https://aka.ms/sdsdk-download-urbetter).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wybieranie urządzenia mowy](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Uzyskaj bezpłatnie klucz subskrypcji usługi mowy](get-started.md)
