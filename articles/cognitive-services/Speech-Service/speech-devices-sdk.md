---
title: Informacje o zestawie SDK urządzeń mowy — Speech Service
titleSuffix: Azure Cognitive Services
description: Rozpocznij pracę z zestawem SDK urządzeń mowy. Usługa mowy współpracuje z wieloma urządzeniami i źródłami audio. Teraz możesz robić aplikacje mowy na następnym poziomie przy użyciu dopasowanego sprzętu i oprogramowania. Zestaw SDK urządzeń rozpoznawania mowy jest wstępnie dostrojoną biblioteką, która jest sparowana z utworzonymi przez siebie pakietami deweloperskimi.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 243072477c7d249d7066a7a448061c51a0bd2f34
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468720"
---
# <a name="about-the-speech-devices-sdk"></a>Informacje o zestawie SDK urządzeń mowy

[Usługi mowy](overview.md) współpracują z szeroką gamą urządzeń i źródeł audio. Teraz możesz robić aplikacje mowy na następnym poziomie przy użyciu dopasowanego sprzętu i oprogramowania. Zestaw SDK urządzeń rozpoznawania mowy jest wstępnie dostroją biblioteką, która jest sparowana z pakietami deweloperskimi opartymi na przeznaczeniu.

Zestaw SDK urządzeń mowy może pomóc:

* Szybkie testowanie nowych scenariuszy głosowych.
* Łatwiejsza integracja opartych na chmurze usług mowy z urządzeniem.
* Utwórz wyjątkowe środowisko użytkownika dla klientów.

Zestaw SDK urządzeń mowy korzysta z [zestawu Speech SDK](speech-sdk.md). Używa zestawu Speech SDK do wysyłania audio, który jest przetwarzany przez nasz algorytm zaawansowanego przetwarzania dźwięku z macierzy mikrofonu urządzenia do [usługi mowy](overview.md). Używa wielokanałowego audio, aby zapewnić bardziej precyzyjną [rozpoznawanie mowy](speech-to-text.md) przy użyciu funkcji pomijania szumów, anulowania echa, Beamforming i dereverberation.

Za pomocą zestawu Speech Devices SDK można także tworzyć urządzenia otoczenia, które mają własne [niestandardowe słowo kluczowe](speech-devices-sdk-create-kws.md) , dzięki czemu wskaźnik inicjujący interakcję użytkownika jest unikatowy dla Twojej marki.

Zestaw SDK urządzeń mowy ułatwia różne scenariusze z obsługą głosu, takie jak [asystentów głosowych](https://aka.ms/bots/speech/va), systemów do określania kolejności, [transkrypcji konwersacji](conversation-transcription-service.md)i inteligentnych głośników. Możesz odpowiedzieć użytkownikom z tekstem, mówić z powrotem do nich przy użyciu domyślnego lub [niestandardowego głosu](how-to-customize-voice-font.md), podać wyniki wyszukiwania, [przetłumaczać](speech-translation.md) je na inne języki i nie tylko. Czekamy na zapoznaj się z kompilacją.

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
