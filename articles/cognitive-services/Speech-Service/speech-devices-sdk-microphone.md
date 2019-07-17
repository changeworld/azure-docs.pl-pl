---
title: Zalecenia tablicy Speech Devices SDK mikrofonu — usług przetwarzania mowy
titleSuffix: Azure Cognitive Services
description: Speech Devices SDK mikrofon tablicy zalecenia. Następujące geometrii tablicy są zalecane do użytku z programem Microsoft Audio Stack. Lokalizacja źródła dźwięku i odrzucenia hałasu w otoczeniu zwiększona z większą liczbą mikrofon z zależnościami na określonych aplikacji, scenariusze dotyczące użytkowników i urządzenia współczynnika postaci.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: erhopf
ms.openlocfilehash: cb30b476471e140f96fa1d159e9a16898f529607
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277490"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Zalecenia dotyczące macierzy mikrofon zestawu SDK urządzenia mowy

W tym artykule dowiesz się, jak projektować tablicy mikrofon dla zestawu Speech Devices SDK.

Zestawu Speech Devices SDK działa najlepiej z tablicy mikrofonu, który został zaprojektowany tak, zgodnie z następującymi wytycznymi w tym wybór geometry, jak i składnika mikrofonu. Wskazówki dotyczące jest również podana w integracji oraz zagadnienia dotyczące elektrycznego.

## <a name="microphone-geometry"></a>Mikrofon geometrii

Następujące geometrii tablicy są zalecane do użytku z programem Microsoft Audio Stack. Lokalizacja źródła dźwięku i odrzucenia hałasu w otoczeniu zwiększona z większą liczbą mikrofon z zależnościami na określonych aplikacji, scenariusze dotyczące użytkowników i urządzenia współczynnika postaci.

|          | Tablica cykliczne    |       |  Liniowy tablicy              |                |
|----------|-------------------|-------------------|----------------|----------------|
|          |<img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/>|<img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/>|<img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/>|<img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/>|
| \# Mikrofonu  | 7                 | 4                 | 4              | 2              |
| Geometrii | 6 1 zewnętrzne Centrum, promień = 42,5 mm, mają równe odstępy| 3 zewnętrzne 1 Centrum, promień = 42,5 mm, mają równe odstępy | Długość = 120 mm, odstępy = 40 mm | Odstępy = 40 mm |

Mikrofon kanałów powinny być uporządkowane według numerowanie przedstawiony dla każdego powyżej tablicy, zwiększenie od 0.  Stos Audio firmy Microsoft będzie wymagać strumień odwołanie dodatkowe odtwarzanie przeprowadzić echa.

## <a name="component-selection"></a>Wybór składników

Składniki mikrofonu, należy wybrać do wiernie oddawać sygnał od szumu i zakłóceń.

Zalecane właściwości podczas wybierania mikrofon są:

| Parametr                         | Zalecane                       |
|-----------------------------------|-----------------------------------|
| SNR                               | \>= 65 dB (dBSPL sygnału 94 1 kHz, hałasu A)   |
| Dopasowywanie amplitudy                | ± 1 dB @ 1 kHz                     |
| Faza dopasowania                    | ° 2 @ 1 kHz                       |
| Punkt przeciążenia akustyczne (AOP)     | \>= 120 dBSPL (tej = 10%)          |
| Szybkość transmisji bitów                          | Co najmniej 24-bitowego                    |
| Częstotliwość próbkowania                     | Minimalna kHz 16\*                   |
| Częstotliwość odpowiedzi                | granicach 3 dB, Hz 200 8000 maska liczb zmiennoprzecinkowych\*|
| Niezawodność                       | Zakres temperatur magazynu C-40 do 70 C<br />Dopuszczalne temperatury C-20-55 C  |

*\* Większe częstotliwości próbkowania lub zakresy częstotliwości "szersze" może być konieczne w przypadku aplikacji o wysokiej jakości komunikacji (VoIP)*

Wybór składnika dobrej muszą łączyć się z dobrej electroacoustic integracji w celu uniknięcia liczby wydajność składniki używane. Unikatowymi przypadkami użycia mogą również wymagają dodatkowych wymagań (na przykład: działające zakresów temperatury).

## <a name="microphone-array-integration"></a>Integracja z mikrofonu tablicy

Wydajność mikrofonów po zintegrowaniu urządzenia będą się różnić ze specyfikacji składników. Należy upewnić się, że mikrofon są również zgodne po integracji. W związku z tym wydajność urządzenia mierzona po wszelkich trwale uzyskać lub EQ powinny spełniać następujące zalecenia:

|  Parametr        |    Zalecane |
|--------------------|----------------------------------------------------|
|  SNR                 | \> 63 dB (dBSPL sygnału 94 1 kHz, hałasu A) |
|  Czułość danych wyjściowych  | -26 dBFS/Pa @ kHz 1 (zalecane) |
|  Dopasowywanie amplitudy  | granicach 2 dB Hz 200 8000 |
| % TEJ\*                 | ≤ 1%, 200 8000 Hz, 94 dBSPL, kolejność 5. |
|  Częstotliwość odpowiedzi  | granicach dB 6, Hz 200 8000 maska liczb zmiennoprzecinkowych\*\* |

*\*\* Głośnika niski zakłócenia jest wymagany do mierzenia tej (np. Neumanna KH120)*

*\*\* Zakresy częstotliwości "Szersze" mogą być wymagane aplikacje wysokiej jakości komunikacji (VoIP)*

## <a name="speaker-integration-recommendations"></a>Zalecenia dotyczące integracji osoby mówiącej

Jak echa jest konieczne w przypadku urządzeń rozpoznawania mowy, zawierające prelegentów, wybór osoby mówiącej i integracji znajdują się dodatkowe zalecenia.

| Parametr                         | Zalecane                       |
|-----------------------------------|-----------------------------------|
| Zagadnienia dotyczące liniowości          | Nie nieliniowych przetwarzania po dokumentacja prelegenta, w przeciwnym razie strumienia odwołanie oparte na sprzęcie sprzężenia zwrotnego jest wymagana  |
| Sprzężenia zwrotnego osoby mówiącej                  | Udostępniane za pośrednictwem WASAPI, prywatnej interfejsów API, niestandardowe ALSA wtyczki (Linux), czy udostępniane za pośrednictwem kanału oprogramowania układowego      |
| THD%                              | 3 paski oktawy minimalne 5-Rzędna, 70 dBA odtwarzania @ 0,8 m ≤ 6.3%, % 5 ≤ 315 – 500 Hz, Hz 630 5000                 |
| Echo sprzężenia do mikrofon      | \> dB-10 TCLw przy użyciu metody B.4 załącznika G.122 ITU-T znormalizowane do poziomu kontroli integralności uprawnień<br />TCLw = TCLwmeasured \+ (mierzony poziom - docelowe wrażliwości danych wyjściowych)<br />TCLw = TCLwmeasured \+ (mierzony poziom - (-26)) |

## <a name="integration-design-architecture"></a>Integracja projektu architektury

Następujące wskazówki dotyczące architektury są niezbędne, gdy integrowanie mikrofon urządzenia:

| Parametr                         | Zalecenie                    |
|-----------------------------------|-----------------------------------|
| Podobieństwa portu MIC               | Wszystkie porty mikrofonu są tę samą długość tablicy    |
| Wymiary portu MIC               | Port rozmiar Ø0.8 1.0 mm. Port długość / średnicy Port \< 2              |
| Takie pieczętowanie zwiększa MIC                       | Pieczętowanie uszczelki jednakowo zaimplementować w górę stosu. Zaleca się \> 70% kompresji dla uszczelki maskotkę witryny     |
| Niezawodność MIC                   | Siatki powinien być używany do zapobiegania sklep i transferu danych przychodzących (między PCB dla dolnej przenoszone mikrofon i pieczętowania cover uszczelką/górną)  |
| Izolacja MIC                     | Gumka uszczelki i wibracje oddzielenie za pośrednictwem struktury, szczególnie w przypadku izolowanie dowolnej ścieżki wibracje, ze względu na zintegrowane prelegentów      |
| Próbkowania zegara                    | Urządzenia audio nie mogą być zakłócenia i również z niskim odejściem od tego stanu    |
| Funkcja rekordów                 | Urządzenie musi być jednocześnie zapisuje nieprzetworzone strumieni pojedynczy kanał |
| USB                               | Wszystkie urządzenia audio USB danych wejściowych, należy ustawić deskryptorów zgodnie z opisem w [Specyfikacja Rev3 urządzenia Audio USB](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) |
| Mikrofon geometrii               | Sterowniki muszą implementować [mikrofon tablicy geometrii deskryptory](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) poprawnie  |
| Odnajdowanie                   | Urządzenia nie może mieć żadnych undiscoverable lub fluktuacje sprzętu, oprogramowania układowego lub 3 algorytmów opartych na oprogramowaniu nieliniowych audio przetwarzania innej firmy z urządzenia|
| Format przechwytywania                    | Formaty przechwytywania, należy użyć minimalnej próbkowania 16 kHz i zalecana głębi 24-bitowego      |

## <a name="electrical-architecture-considerations"></a>Zagadnienia dotyczące architektury elektrycznych

Gdy to stosowne, tablice mogą być połączone z hostem USB (takich jak SoC, uruchomionym stosu Audio firmy Microsoft) i interfejsy do usług przetwarzania mowy lub innych aplikacji.

Składniki sprzętowe, takie jak konwersja menedżerów PDM można TDM należy upewnić się, czy dynamicznym zakresie i SNR mikrofon są zachowywane w obrębie ponowną próbników.

Szybkie 2.0 klasy Audio USB powinna być obsługiwana w ramach dowolnej audio Unit zapewnić niezbędne przepustowości dla maksymalnie siedem kanałów na wyższe częstotliwości próbkowania i bitowej głębi.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat zestawu Speech Devices SDK](speech-devices-sdk.md)
