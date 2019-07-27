---
title: Transkrypcja konwersacji — usługa mowy
titleSuffix: Azure Cognitive Services
description: Transkrypcja konwersacji to zaawansowana funkcja usług mowy, która łączy rozpoznawanie mowy w czasie rzeczywistym, identyfikację głosu i diarization. Transkrypcja konwersacji doskonale nadaje się do jego przepisywaniach spotkań osób, z możliwością rozróżniania głośników, dzięki czemu wiadomo, kto i kiedy użytkownicy mogą skoncentrować się na spotkaniu i szybko postępować zgodnie z kolejnymi krokami. Ta funkcja ulepsza również ułatwienia dostępu. Dzięki transkrypcjom można aktywnie angażować uczestników z upośledzeniem słuchu.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: acbf82335523ee226f4b899180f0f22cb94cca5e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562889"
---
# <a name="what-is-conversation-transcription"></a>Co to jest transkrypcja konwersacji?

Transkrypcja konwersacji to zaawansowana funkcja usług mowy, która łączy rozpoznawanie mowy w czasie rzeczywistym, identyfikację głosu i diarization. Transkrypcja konwersacji doskonale nadaje się do jego przepisywaniach spotkań osób, z możliwością rozróżniania głośników, dzięki czemu wiadomo, kto i kiedy użytkownicy mogą skoncentrować się na spotkaniu i szybko postępować zgodnie z kolejnymi krokami. Ta funkcja ulepsza również ułatwienia dostępu. Dzięki transkrypcjom można aktywnie angażować uczestników z upośledzeniem słuchu.   

Transkrypcja konwersacji zapewnia dokładne rozpoznawanie z dostosowywalnymi modelami mowy, które można dostosować, aby zrozumieć branżowe i charakterystyczne dla firmy słownictwo. Ponadto można sparować transkrypcję konwersacji z zestawem SDK usługi Speech Devices, aby zoptymalizować środowisko dla urządzeń z obsługą mikrofonu.

>[!NOTE]
> Obecnie w przypadku małych spotkań zaleca się transkrypcję konwersacji. Jeśli chcesz zwiększyć transkrypcję konwersacji w przypadku dużych spotkań w skali, skontaktuj się z nami.

Ten diagram ilustruje sprzęt, oprogramowanie i usługi, które współpracują z transkrypcją konwersacji.

![Diagram transkrypcji importowania konwersacji](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> Wymagana jest cykliczna siedem macierzy mikrofonów z określoną konfiguracją geometrii. Aby uzyskać szczegółowe informacje dotyczące specyfikacji i projektu, zobacz [mikrofon zestawu SDK urządzenia Microsoft Speech](https://aka.ms/cts/microphone). Aby dowiedzieć się więcej lub zakupić zestaw deweloperski, zobacz temat [pobieranie zestawu SDK urządzenia Microsoft Speech](https://aka.ms/cts/getsdk).

## <a name="get-started-with-conversation-transcription"></a>Wprowadzenie do transkrypcji konwersacji

Istnieją trzy kroki, które należy wykonać, aby rozpocząć pracę z transkrypcją konwersacji.

1. Zbieraj próbki głosu od użytkowników.
2. Generowanie profilów użytkowników przy użyciu przykładów głosu użytkownika
3. Korzystanie z zestawu Speech SDK do identyfikowania użytkowników (głośników) i transkrypcja mowy

## <a name="collect-user-voice-samples"></a>Zbierz przykłady głosu użytkownika

Pierwszym krokiem jest zebranie nagrań dźwiękowych z każdego użytkownika. Funkcja mowy użytkownika powinna być rejestrowana w cichym środowisku bez szumu w tle. Zalecana długość poszczególnych próbek audio to 30 sekund i dwie minuty. Dłuższe próbki audio spowodują zwiększenie dokładności przy identyfikowaniu głośników. Dźwięk musi być kanałem mono z szybkością próbkowania 16 KHz.

Poza powyższymi wskazówkami, w jaki sposób nagrywanie i przechowywanie audio odbywa się do użytkownika — zaleca się bezpieczną bazę danych. W następnej sekcji sprawdzimy, w jaki sposób ten dźwięk jest używany do generowania profilów użytkowników, które są używane z zestawem Speech SDK do rozpoznawania głośników.

## <a name="generate-user-profiles"></a>Generuj profile użytkowników

Następnie należy wysłać zebrane nagrania audio do usługi generacji sygnatur, aby zweryfikować dźwięk i wygenerować profile użytkowników. [Usługa generowania podpisów](https://aka.ms/cts/signaturegenservice) to zestaw interfejsów API REST, które umożliwiają generowanie i pobieranie profilów użytkowników.

Aby utworzyć profil użytkownika, należy użyć `GenerateVoiceSignature` interfejsu API. Dostępne są szczegóły specyfikacji i przykładowy kod:

> [!NOTE]
> Transkrypcja konwersacji jest obecnie dostępna w "en-us" i "zh-CN" w następujących regionach: `centralus` i. `eastasia`

* [Specyfikacja REST](https://aka.ms/cts/signaturegenservice)
* [Jak używać transkrypcji konwersacji](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>Transkrypcja i zidentyfikuj głośniki

Transkrypcja konwersacji oczekuje strumieni audio wielokanałowe i profilów użytkowników jako danych wejściowych do generowania transkrypcji i identyfikowania głośników. Dane profilu audio i użytkownika są wysyłane do usługi transkrypcji konwersacji przy użyciu zestawu Speech Devices SDK. Jak wspomniano wcześniej, cykliczna tablica mikrofonu i zestaw SDK urządzeń mowy są wymagane do korzystania z transkrypcji konwersacji.

>[!NOTE]
> Aby uzyskać szczegółowe informacje dotyczące specyfikacji i projektu, zobacz [mikrofon zestawu SDK urządzenia Microsoft Speech](https://aka.ms/cts/microphone). Aby dowiedzieć się więcej lub zakupić zestaw deweloperski, zobacz temat [pobieranie zestawu SDK urządzenia Microsoft Speech](https://aka.ms/cts/getsdk).

Aby dowiedzieć się, jak używać transkrypcji konwersacji z zestawem SDK urządzeń mowy, zobacz [jak korzystać z transkrypcji konwersacji](https://aka.ms/cts/howto).


## <a name="quick-start-with-a-sample-app"></a>Szybki start z przykładową aplikacją

Zestaw SDK urządzenia Microsoft Speech zawiera przykładową aplikację szybkiego startu dla wszystkich próbek związanych z urządzeniami. Transkrypcja konwersacji jest jednym z nich. Można go znaleźć w [zestawie SDK urządzenia mowy Android — szybki start](https://aka.ms/sdsdk-quickstart) z przykładową aplikacją i jej kodem źródłowym dla Twojego odwołania.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o zestawie SDK urządzeń mowy](speech-devices-sdk.md)
