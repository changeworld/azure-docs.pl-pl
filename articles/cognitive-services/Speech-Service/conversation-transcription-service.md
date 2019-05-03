---
title: Usługa transkrypcji konwersacji — usługi mowy
titleSuffix: Azure Cognitive Services
description: Usługa transkrypcji konwersacji jest zaawansowaną funkcją usług przetwarzania mowy, łączącą rozpoznawanie mowy w czasie rzeczywistym, identyfikacja osoby mówiącej i diarization. Usługa transkrypcji konwersacji to idealne rozwiązanie dla przepisywania spotkań w osoby z możliwością odróżnić prelegentów, usługa powiadomi Cię o tym, kto powiedział, co i kiedy, umożliwiając uczestników, aby skoncentrować się na spotkanie i szybko wykonać kolejne czynności dotyczącymi kolejnych kroków. Ta funkcja także usprawnia ułatwień dostępu. Przy użyciu transkrypcji mogą aktywnie angażować uczestników z wadą słuchu.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 2c3269a9e57c0392e152fedfb640b9fae91a4989
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025937"
---
# <a name="what-is-the-conversation-transcription-service"></a>Co to jest usługa transkrypcji konwersacji?

Usługa transkrypcji konwersacji jest zaawansowaną funkcją usług przetwarzania mowy, łączącą rozpoznawanie mowy w czasie rzeczywistym, identyfikacja osoby mówiącej i diarization. Usługa transkrypcji konwersacji to idealne rozwiązanie dla przepisywania spotkań w osoby z możliwością odróżnić prelegentów, usługa powiadomi Cię o tym, kto powiedział, co i kiedy, umożliwiając uczestników, aby skoncentrować się na spotkanie i szybko wykonać kolejne czynności dotyczącymi kolejnych kroków. Ta funkcja także usprawnia ułatwień dostępu. Przy użyciu transkrypcji mogą aktywnie angażować uczestników z wadą słuchu.   

Usługa transkrypcji konwersacji dostarcza dokładne uznanie z modelami mowy z możliwością, które można dostosować, aby zrozumieć branży i słownictwa specyficzny dla firmy. Ponadto może być sparowane usługi transkrypcji konwersacji z zestawu Speech Devices SDK, aby poprawić komfort mikrofon wielu urządzeń.

>[!NOTE]
> Obecnie w przypadku małych spotkań zalecana jest usługa transkrypcji konwersacji. Jeśli chcesz rozszerzyć usługę transkrypcji konwersacji dla dużych spotkań na dużą skalę, skontaktuj się z nami.

Ten diagram przedstawia sprzętu, oprogramowania i usług, które działają w połączeniu z usługą transkrypcji konwersacji.

![Diagram usługi transkrypcji konwersacji importu](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> Cykliczne tablicę siedmiu mikrofon przy użyciu geometrii określonej konfiguracji jest wymagana. Specyfikacja i projektowania szczegółowe informacje, zobacz [mikrofon zestawu SDK urządzenia mowy Microsoft](https://aka.ms/cts/microphone). Aby dowiedzieć się więcej, lub kupić zestaw deweloperski, zobacz [Pobierz Microsoft Speech zestawu SDK urządzenia](https://aka.ms/cts/getsdk).

## <a name="get-started-with-conversation-transcription"></a>Rozpoczynanie pracy z usługą transkrypcji konwersacji

Istnieją trzy kroki, które należy wykonać, aby rozpocząć pracę z usługą transkrypcji konwersacji.

1. Zbierz próbki głosu użytkowników.
2. Generowanie profilów użytkowników przy użyciu próbki głosu użytkownika
3. Identyfikowanie użytkowników (użytkowników) i transkrypcja mowy przy użyciu zestawu SDK rozpoznawania mowy

## <a name="collect-user-voice-samples"></a>Zbierz próbki głosu użytkownika

Pierwszym krokiem jest do zbierania nagrania audio z każdego użytkownika. Mowy użytkownika powinny być rejestrowane w środowisku cichy bez hałas w tle. Zalecana długość dla każdej próbki audio jest od 30 sekund do dwóch minut. Dłużej próbki audio w wyniku polepszenia dokładności przy identyfikowaniu głośników. Dźwięk musi być mono kanału o częstotliwość próbkowania 16 KHz.

Poza wyżej wskazówek jak audio jest rejestrowana i przechowywanych zależy od użytkownika — bezpiecznej bazie danych jest zalecane. W następnej sekcji omówimy, jak dźwięk ten jest używany do generowania profile użytkowników, które są używane przy użyciu zestawu SDK rozpoznawania mowy do rozpoznawania głośników.

## <a name="generate-user-profiles"></a>Generowanie profili użytkowników

Następnie konieczne będzie wysyłać nagrania audio zostały zebrane w usłudze generowania podpisu do sprawdzania poprawności audio i generowanie profilów użytkowników. [Usługi generowania podpisu](https://aka.ms/cts/signaturegenservice) to zbiór interfejsów API REST, dzięki czemu Generowanie i pobieranie profilów użytkowników.

Aby utworzyć profil użytkownika, musisz użyć `GenerateVoiceSignature` interfejsu API. Dostępne są szczegóły specyfikacji i przykładowy kod:

> [!NOTE]
> Usługa transkrypcji konwersacji jest obecnie dostępna w następujących regionach: `centralus` i `eastasia`.

* [Specyfikacja REST](https://aka.ms/cts/signaturegenservice)
* [Jak używać usługi transkrypcji konwersacji](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>Transkrypcja i zidentyfikować prelegentów

Usługa transkrypcji konwersacji oczekuje wielokanałowy strumieni audio i profilów użytkowników jako dane wejściowe do generowania transkrypcji i zidentyfikować głośników. Dźwięk i użytkownika dane profilu są wysyłane do usługi transkrypcji konwersacji, przy użyciu zestawu Speech Devices SDK. Jak wspomniano wcześniej cykliczne tablicę siedmiu mikrofon i zestawu Speech Devices SDK są wymagane do korzystania z usługi transkrypcji konwersacji.

>[!NOTE]
> Specyfikacja i projektowania szczegółowe informacje, zobacz [mikrofon zestawu SDK urządzenia mowy Microsoft](https://aka.ms/cts/microphone). Aby dowiedzieć się więcej, lub kupić zestaw deweloperski, zobacz [Pobierz Microsoft Speech zestawu SDK urządzenia](https://aka.ms/cts/getsdk).

Aby dowiedzieć się, jak używać usługi transkrypcji konwersacji z zestawu Speech Devices SDK, zobacz [jak używać usługi transkrypcji konwersacji](https://aka.ms/cts/howto).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat zestawu Speech Devices SDK](speech-devices-sdk.md)
