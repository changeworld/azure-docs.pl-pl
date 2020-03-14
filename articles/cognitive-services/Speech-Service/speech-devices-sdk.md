---
title: Zestaw SDK urządzeń mowy — usługa mowy
titleSuffix: Azure Cognitive Services
description: Wprowadzenie do zestawu Speech Devices SDK. Usługa mowy współpracuje z wieloma urządzeniami i źródłami audio. Zestawu Speech Devices SDK jest biblioteką wstępnie dostosowane, który jest powiązany z systemom, mikrofon tablicy development Kit.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: add0d27ae6a612fed0320b5329d19236b7bbbd01
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370648"
---
# <a name="what-is-the-speech-devices-sdk"></a>Co to jest zestaw SDK urządzeń mowy?

[Usługa mowy](overview.md) współpracuje z wieloma urządzeniami i źródłami audio. Teraz możesz korzystać z aplikacji mowy na wyższy poziom dzięki dopasowane sprzętu i oprogramowania. Zestawu Speech Devices SDK jest biblioteką wstępnie dostosowane, który jest powiązany z systemom, mikrofon tablicy development Kit.

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
Aplikacja została skompilowana przy użyciu pakietu SDK mowy, a środowisko IDE języka Java (v4) w systemie 64-bitowym Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8). Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

Dodatkowe pliki binarne są udostępniane do obsługi nadchodzących urządzeń, [roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2), [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter)i [GGEC](https://aka.ms/sdsdk-download-speaker).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wybieranie urządzenia mowy](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Uzyskaj bezpłatnie klucz subskrypcji usługi mowy](get-started.md)
