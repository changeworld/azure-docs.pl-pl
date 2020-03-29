---
title: Zalecenia dotyczące tablicy mikrofonów zestawu SDK urządzeń mowy
titleSuffix: Azure Cognitive Services
description: Urządzenia mowy SDK mikrofon zalecenia tablicy. Te geometrie tablicy są zalecane do użytku z microsoft audio stack.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: erhopf
ms.openlocfilehash: a87bdd7a55036e8b70f0bc5816d2b587c1569202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77168137"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Zalecenia dotyczące tablicy mikrofonów zestawu urządzeń mowy SDK

W tym artykule dowiesz się, jak zaprojektować macierz mikrofonów dla zestawu SDK urządzeń mowy.

Zestaw SDK urządzeń mowy działa najlepiej z macierzą mikrofonów zaprojektowaną zgodnie z następującymi wytycznymi, w tym geometrią mikrofonu i wyborem komponentów. Podano również wytyczne dotyczące integracji i zagadnień elektrycznych.

## <a name="microphone-geometry"></a>Geometria mikrofonu

Następujące geometrie tablicy są zalecane do użycia z microsoft audio stack. Lokalizacja źródeł dźwięku i odrzucenie szumu otoczenia jest lepsza dzięki większej liczbie mikrofonów z zależnościami od określonych aplikacji, scenariuszy użytkownika i formatu urządzenia.

|     | Tablica cykliczna |     | Tablica liniowa |     |
| --- | -------------- | --- | ------------ | --- |
|     | <img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/> | <img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/> |
| \#Mikrofonów | 7 | 4 | 4 | 2 |
| Geometrii | 6 Zewnętrzny, 1 środek, Promień = 42,5 mm, równomiernie rozmieszczona | 3 Zewnętrzne, 1 środek, promień = 42,5 mm, równomiernie rozmieszczone | Długość = 120 mm, odstęp = 40 mm | Odstęp = 40 mm |

Kanały mikrofonowe powinny być zamawiane zgodnie z numerami przedstawionymi dla każdej powyższej tablicy, zwiększając się z 0. Stos audio firmy Microsoft będzie wymagać dodatkowego strumienia referencyjnego odtwarzania dźwięku w celu anulowania echa.

## <a name="component-selection"></a>Wybór komponentu

Elementy mikrofonu powinny być dobrane w taki sposób, aby dokładnie odtworzyć sygnał wolny od szumów i zniekształceń.

Zalecane właściwości podczas wybierania mikrofonów to:

| Parametr | Zalecane |
| --------- | ----------- |
| Snr | \>= 65 dB (sygnał 1 kHz 94 dBSPL, szum ważony A) |
| Dopasowywanie amplitudy | ± 1 dB @ 1 kHz |
| Dopasowywanie faz | ± 2° przy 1 kHz |
| Punkt przeciążenia akustycznego (AOP) | \>= 120 dBSPL (THD = 10%) |
| Szybkość transmisji bitów | Minimum 24-bitowe |
| Częstotliwość próbkowania | Minimum 16 kHz\* |
| Odpowiedź częstotliwościowa | ± 3 dB, 200-8000 Hz Zmienna maska\* |
| Niezawodność | Zakres temperatur przechowywania od -40°C do 70°C<br />Zakres temperatur pracy od -20°C do 55°C |

\*_Wyższe częstotliwości próbkowania lub "szersze" zakresy częstotliwości mogą być niezbędne do zastosowań komunikacyjnych wysokiej jakości (VoIP)_

Dobry wybór komponentów musi być połączony z dobrą integracją elektroakustyczną, aby uniknąć pogorszenia wydajności użytych komponentów. Unikalne przypadki użycia mogą również wymagać dodatkowych wymagań (na przykład: zakresy temperatur pracy).

## <a name="microphone-array-integration"></a>Integracja macierzy mikrofonowej

Wydajność macierzy mikrofonu po zintegrowaniu z urządzeniem będzie się różnić od specyfikacji komponentu. Ważne jest, aby upewnić się, że mikrofony są dobrze dopasowane po integracji. W związku z tym wydajność urządzenia mierzona po stałym nałowieniu lub korektorze powinna spełniać następujące zalecenia:

| Parametr          | Zalecane                                        |
| ------------------ | -------------------------------------------------- |
| Snr                | \>63 dB (sygnał 1 kHz 94 dBSPL, hałas ważony A) |
| Czułość wyjściowa | -26 dBFS/Pa @ 1 kHz (zalecane)                  |
| Dopasowywanie amplitudy | ± 2 dB, 200-8000 Hz                                |
| Thd%\*             | ≤ 1%, 200-8000 Hz, 94 dBSPL, 5.             |
| Odpowiedź częstotliwościowa | ± 6 dB, 200-8000 Hz Zmienna maska\*\*              |

\*\*_Do pomiaru THD wymagany jest głośnik o niskim stopniu zniekształcenia (np._

\*\*_"Szersze" zakresy częstotliwości mogą być niezbędne do zastosowań komunikacyjnych wysokiej jakości (VoIP)_

## <a name="speaker-integration-recommendations"></a>Zalecenia dotyczące integracji prelegentów

Ponieważ anulowanie echa jest konieczne w przypadku urządzeń do rozpoznawania mowy, które zawierają głośniki, dostępne są dodatkowe zalecenia dotyczące wyboru i integracji głośników.

| Parametr | Zalecane |
| --------- | ----------- |
| Zagadnienia dotyczące liniowości | Nie jest wymagane nieliniowe przetwarzanie po odwołaniu do głośnika, w przeciwnym razie wymagany jest sprzętowy strumień odwołania do sprzężenia sprzężenia zwrotnego |
| Sprzężenie zwrotne głośnika | Dostarczane za pośrednictwem WASAPI, prywatnych interfejsów API, niestandardowej wtyczki ALSA (Linux) lub dostarczane za pośrednictwem kanału oprogramowania układowego |
| Thd% | 3. Oktawy Pasma minimum 5. zamówienie, 70 dBA Odtwarzanie @ 0,8 m ≤ 6,3%, 315-500 Hz ≤ 5%, 630-5000 Hz |
| Echo sprzęganie do mikrofonów | \>-10 dB TCLw przy użyciu metody ITU-T G.122 załącznik B.4, znormalizowanej do poziomu mikrofonu<br />TCLw = TCLwmeasured \+ (poziom zmierzony - docelowa czułość wyjściowa)<br />TCLw = TCLwmeasured \+ (poziom zmierzony - (-26)) |

## <a name="integration-design-architecture"></a>Architektura projektowania integracji

Podczas integrowania mikrofonów z urządzeniem są niezbędne następujące wskazówki dotyczące architektury:

| Parametr | Zalecenie |
| --------- | -------------- |
| Podobieństwo portu mikrofonowego | Wszystkie porty mikrofonu mają taką samą długość w matrycy |
| Wymiary portu mikrofonowego | Rozmiar portu Ø0,8-1,0 mm. Długość portu / \< średnica portu 2 |
| Uszczelnienie mikrofonu         | Uszczelniające uszczelki równomiernie realizowane w stos-up. Zalecane \> 70% stopień sprężania uszczelek piankowych |
| Niezawodność mikrofonu     | Siatka powinna być stosowana w celu zapobiegania kurzowi i przedostawaniu się (między płytką drukowaną dla dolnych mikrofonów i uszczelniającej uszczelką/pokrywą górną) |
| Izolacja mikrofonu       | Gumowe uszczelki i odłączanie drgań przez konstrukcję, szczególnie do izolowania wszelkich ścieżek wibracyjnych dzięki zintegrowanym głośnikom |
| Zegar pobierania próbek      | Dźwięk urządzenia musi być wolny od drgań i drop-outów o niskim dryfcie |
| Możliwość rejestrowania   | Urządzenie musi być w stanie jednocześnie rejestrować poszczególne strumienie surowców kanałowych |
| USB                 | Wszystkie urządzenia wejściowe audio USB muszą ustawić deskryptory zgodnie z [specyfikacją USB Audio Devices Rev3](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) |
| Geometria mikrofonu | Sterowniki muszą poprawnie zaimplementować [deskryptory geometrii tablicy mikrofonu](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) |
| Odnajdywania     | Urządzenia nie mogą mieć żadnego niewykrywalnego lub niekontrolowanego sprzętu, oprogramowania układowego ani nieliniowych algorytmów przetwarzania dźwięku innych firm do/z urządzenia |
| Format przechwytywania      | Formaty przechwytywania muszą wykorzystywać minimalną częstotliwość próbkowania 16 kHz i zalecaną głębokość 24-bitową |

## <a name="electrical-architecture-considerations"></a>Zagadnienia dotyczące architektury elektrycznej

W stosownych przypadkach tablice mogą być podłączone do hosta USB (takiego jak SoC, który uruchamia stos audio firmy Microsoft) i interfejsów do usług mowy lub innych aplikacji.

Składniki sprzętowe, takie jak konwersja PDM-TDM, powinny zapewnić zachowanie zakresu dynamicznego i modułu SNR mikrofonów w obrębie ponownych próbników.

Szybka pamięć USB Audio Class 2.0 powinna być obsługiwana w dowolnym mcu audio w celu zapewnienia przepustowości niezbędnej do siedmiu kanałów przy wyższych szybkościach próbkowania i głębi bitowych.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o SDK urządzeń mowy](speech-devices-sdk.md)
