---
title: Transkrypcja konwersacji — usługi mowy
titleSuffix: Azure Cognitive Services
description: Transkrypcja konwersacji jest zaawansowaną funkcją usług przetwarzania mowy, łączącą rozpoznawanie mowy w czasie rzeczywistym, identyfikacja osoby mówiącej i diarization. Transkrypcja konwersacji to idealne rozwiązanie dla przepisywania spotkań w osoby z możliwością odróżnić prelegentów, usługa powiadomi Cię o tym, kto powiedział, co i kiedy, umożliwiając uczestników, aby skoncentrować się na spotkanie i szybko wykonać kolejne czynności dotyczącymi kolejnych kroków. Ta funkcja także usprawnia ułatwień dostępu. Przy użyciu transkrypcji mogą aktywnie angażować uczestników z wadą słuchu.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: a31921e229a4bbfccd6fdd871666aad1eeef3232
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243876"
---
# <a name="what-is-conversation-transcription"></a>Co to jest transkrypcji konwersacji?

Transkrypcja konwersacji jest zaawansowaną funkcją usług przetwarzania mowy, łączącą rozpoznawanie mowy w czasie rzeczywistym, identyfikacja osoby mówiącej i diarization. Transkrypcja konwersacji to idealne rozwiązanie dla przepisywania spotkań w osoby z możliwością odróżnić prelegentów, usługa powiadomi Cię o tym, kto powiedział, co i kiedy, umożliwiając uczestników, aby skoncentrować się na spotkanie i szybko wykonać kolejne czynności dotyczącymi kolejnych kroków. Ta funkcja także usprawnia ułatwień dostępu. Przy użyciu transkrypcji mogą aktywnie angażować uczestników z wadą słuchu.   

Transkrypcja konwersacji zapewnia dokładny uznanie z modelami mowy z możliwością, które można dostosować, aby zrozumieć branży i słownictwa specyficzny dla firmy. Ponadto może być sparowane transkrypcji konwersacji za pomocą zestawu Speech Devices SDK, aby poprawić komfort mikrofon wielu urządzeń.

>[!NOTE]
> Obecnie transkrypcji konwersacji jest zalecane w przypadku małych spotkań. Jeśli chcesz rozszerzyć transkrypcji konwersacji dla dużych spotkań na dużą skalę, skontaktuj się z nami.

Ten diagram przedstawia sprzętu, oprogramowania i usług, które współpracuje z konwersacji transkrypcji.

![Diagram transkrypcji konwersacji importu](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> Cykliczne tablicę siedmiu mikrofon przy użyciu geometrii określonej konfiguracji jest wymagana. Specyfikacja i projektowania szczegółowe informacje, zobacz [mikrofon zestawu SDK urządzenia mowy Microsoft](https://aka.ms/cts/microphone). Aby dowiedzieć się więcej, lub kupić zestaw deweloperski, zobacz [Pobierz Microsoft Speech zestawu SDK urządzenia](https://aka.ms/cts/getsdk).

## <a name="get-started-with-conversation-transcription"></a>Rozpoczynanie pracy z usługą transkrypcji konwersacji

Istnieją trzy kroki, które należy wykonać, aby rozpocząć pracę z konwersacji transkrypcji.

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
> Konwersacja transkrypcja jest obecnie dostępna w "en US" i "zh-CN" w następujących regionach: `centralus` i `eastasia`.

* [Specyfikacja REST](https://aka.ms/cts/signaturegenservice)
* [Jak używać transkrypcji konwersacji](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>Transkrypcja i zidentyfikować prelegentów

Transkrypcja konwersacji oczekuje wielokanałowy strumieni audio i profilów użytkowników jako dane wejściowe do generowania transkrypcji i zidentyfikować głośników. Dźwięk i użytkownika dane profilu są wysyłane do usługi rozmowy transkrypcji przy użyciu zestawu Speech Devices SDK. Jak wspomniano wcześniej cykliczne tablicę siedmiu mikrofon i zestawu Speech Devices SDK są wymagane do użycia transkrypcji konwersacji.

>[!NOTE]
> Specyfikacja i projektowania szczegółowe informacje, zobacz [mikrofon zestawu SDK urządzenia mowy Microsoft](https://aka.ms/cts/microphone). Aby dowiedzieć się więcej, lub kupić zestaw deweloperski, zobacz [Pobierz Microsoft Speech zestawu SDK urządzenia](https://aka.ms/cts/getsdk).

Aby dowiedzieć się, jak transkrypcji konwersacji za pomocą zestawu Speech Devices SDK, zobacz [sposób używania transkrypcji konwersacji](https://aka.ms/cts/howto).


## <a name="quick-start-with-a-sample-app"></a>Szybki Start z przykładowej aplikacji

Zestawu SDK urządzenia mowy firmy Microsoft zawiera przewodnik Szybki start Przykładowa aplikacja dla wszystkich urządzeń dotyczące przykładów. Transkrypcja konwersacji jest jednym z nich. Znaleźć go w [Szybki Start dla systemu android z zestawu SDK urządzenia mowy](https://aka.ms/sdsdk-quickstart) przykładową aplikację i jego kod źródłowy, do której można się odwołać.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat zestawu Speech Devices SDK](speech-devices-sdk.md)
