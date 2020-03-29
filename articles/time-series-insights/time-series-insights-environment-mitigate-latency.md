---
title: Jak monitorować i zmniejszać ograniczanie ograniczania przepustowości — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: Dowiedz się, jak monitorować, diagnozować i ograniczać problemy z wydajnością, które powodują opóźnienia i ograniczanie przepustowości w usłudze Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 245a0b18187ff1c1b226e94b03374f2c071e51c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314831"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Monitorowanie i ograniczanie ograniczania ograniczania przepustowości w celu zmniejszenia opóźnień w usłudze Azure Time Series Insights

Gdy ilość przychodzących danych przekracza konfigurację środowiska, może wystąpić opóźnienie lub ograniczanie w usłudze Azure Time Series Insights.

Można uniknąć opóźnień i ograniczania przepustowości, poprawnie konfigurując środowisko pod kątem ilości danych, które chcesz analizować.

Najprawdopodobniej wystąpi opóźnienie i ograniczanie przepustowości podczas:

- Dodaj źródło zdarzeń, które zawiera stare dane, które mogą przekraczać przydzieloną szybkość transferu danych przychodzących (usługa Time Series Insights będzie musiała nadrobić zaległości).
- Dodaj więcej źródeł zdarzeń do środowiska, co powoduje wzrost z dodatkowych zdarzeń (które mogą przekroczyć możliwości środowiska).
- Wypychaj duże ilości zdarzeń historycznych do źródła zdarzeń, co powoduje opóźnienie (usługa Time Series Insights będzie musiała nadrobić zaległości).
- Dołącz do danych referencyjnych za pomocą danych telemetrycznych, co powoduje większy rozmiar zdarzenia. Z punktu widzenia ograniczania przepustowości pakiet danych o rozmiarze 32 KB jest traktowany jako 32 zdarzenia o rozmiarze 1 KB. Maksymalny dozwolony rozmiar zdarzenia wynosi 32 KB; pakiety danych większe niż 32 KB są obcinane.

## <a name="video"></a>Film wideo

### <a name="learn-about-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>Dowiedz się więcej o zachowaniu danych usługi Time Series Insights i sposobie planowania.</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>Monitorowanie opóźnień i ograniczania przepustowości za pomocą alertów

Alerty mogą pomóc w diagnozowaniu i ograniczaniu problemów z opóźnieniami występujących w twoim środowisku.

1. W witrynie Azure portal wybierz środowisko usługi Time Series Insights. Następnie wybierz **alerty**.

   [![Dodawanie alertu do środowiska usługi Time Series Insights](media/environment-mitigate-latency/mitigate-latency-add-alert.png)](media/environment-mitigate-latency/mitigate-latency-add-alert.png#lightbox)

1. Wybierz pozycję **+ Nowa reguła alertu**. Zostanie wyświetlony panel **Utwórz regułę.** Wybierz **pozycję Dodaj** w obszarze **WARUNEK**.

   [![Dodawanie okienka alertów](media/environment-mitigate-latency/mitigate-latency-add-pane.png)](media/environment-mitigate-latency/mitigate-latency-add-pane.png#lightbox)

1. Następnie należy skonfigurować dokładne warunki logiki sygnału.

   [![Konfigurowanie logiki sygnału](media/environment-mitigate-latency/configure-alert-rule.png)](media/environment-mitigate-latency/configure-alert-rule.png#lightbox)

   W tym miejscu można skonfigurować alerty przy użyciu niektórych z następujących warunków:

   |Metryka  |Opis  |
   |---------|---------|
   |**Odebrane bajty przychodzące**     | Liczba nieprzetworzonych bajtów odczytanych ze źródeł zdarzeń. Liczba surowców zwykle zawiera nazwę właściwości i wartość.  |  
   |**Odebrane nieprawidłowe wiadomości przychodzące**     | Liczba nieprawidłowych wiadomości odczytywanych ze wszystkich centrów zdarzeń platformy Azure lub źródeł zdarzeń usługi Azure IoT Hub.      |
   |**Odebrane wiadomości przychodzące**   | Liczba wiadomości odczytywanych ze wszystkich centrów zdarzeń lub źródeł zdarzeń usługi IoT Hubs.        |
   |**Bajty przechowywane przy ruchu przychodzącego**     | Całkowity rozmiar zdarzeń przechowywanych i dostępnych dla kwerendy. Rozmiar jest obliczany tylko na wartości właściwości.        |
   |**Zdarzenia przechowywane w ruchu przychodzącym**    |   Liczba spłaszczonych zdarzeń przechowywanych i dostępnych dla kwerendy.      |
   |**Opóźnienie czasu odebranego komunikatu przychodzącego**   |  Różnica w sekundach między czasem, w której wiadomość jest likżowana w źródle zdarzeń, a czasem przetwarzania w pliku Przychodzącym.      |
   |**Opóźnienie liczby odebranych wiadomości przychodzących**   |  Różnica między numerem sekwencyjnym ostatniej wiadomości w kolejce w partycji źródłowej zdarzenia a numerem sekwencyjnym wiadomości przetwarzanej w pliku przychodzącym.      |

   Wybierz pozycję **Done** (Gotowe).

1. Po skonfigurowaniu żądanej logiki sygnału przejrzyj wizualnie wybraną regułę alertu.

   [![Widok opóźnienia i wykresy](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png)](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png#lightbox)

## <a name="throttling-and-ingress-management"></a>Ograniczanie przepustowości i zarządzanie wnikaniem

* Jeśli jesteś ograniczany, wartość opóźnienia *czasu odebranego komunikatu przystawnym* zostanie zarejestrowana z informacją o tym, ile sekund za środowiskiem usługi TIme Series Insights są od rzeczywistego czasu, w jakim wiadomość trafi do źródła zdarzeń (z wyłączeniem czasu indeksowania appx. 30-60 sekund).  

  *Opóźnienie liczby odebranych wiadomości przychodzących* również powinno mieć wartość, co pozwala określić, ile wiadomości za tobą.  Najprostszym sposobem, aby złapać się jest zwiększenie pojemności środowiska do rozmiaru, który pozwoli Ci przezwyciężyć różnicę.  

  Na przykład jeśli środowisko S1 demonstruje opóźnienie 5 000 000 komunikatów, możesz zwiększyć rozmiar środowiska do sześciu jednostek na około jeden dzień, aby zostać złapanym.  Możesz zwiększyć jeszcze bardziej, aby szybciej nadrobić zaległości. Okres nadrabiania zaległości jest częstym zjawiskiem podczas wstępnego inicjowania obsługi administracyjnej środowiska, szczególnie po podłączeniu go do źródła zdarzeń, które już ma zdarzenia w nim lub gdy zbiorczo przekazujesz wiele danych historycznych.

* Inną techniką jest ustawienie alertu **zdarzenia przechowywane przychodzących** >= próg nieco poniżej całkowitej pojemności środowiska przez okres 2 godzin.  Ten alert może pomóc zrozumieć, jeśli są stale na pojemność, co wskazuje na wysokie prawdopodobieństwo opóźnienia. 

  Na przykład jeśli masz trzy jednostki S1 aprowizowana (lub 2100 zdarzeń na minutę pojemności transferu danych przychodzących), można ustawić alert **zdarzenia przechowywane przychodzących** dla >= 1900 zdarzeń na 2 godziny. Jeśli stale przekraczasz ten próg, a zatem wyzwalasz alert, prawdopodobnie nie jest to zaaprowizowane.  

* Jeśli podejrzewasz, że są ograniczane, można porównać wiadomości **odebrane przychodzących** z wiadomości wychodzących źródła zdarzeń.  Jeśli ruch przychodzący do Centrum zdarzeń jest większy niż **odebrane wiadomości przychodzące,** usługa Time Series Insights prawdopodobnie zostanie ograniczona.

## <a name="improving-performance"></a>Poprawa wydajności

Aby zmniejszyć ograniczanie lub opóźnienia, najlepszym sposobem, aby go poprawić jest zwiększenie pojemności środowiska.

Można uniknąć opóźnień i ograniczania przepustowości, poprawnie konfigurując środowisko pod kątem ilości danych, które chcesz analizować. Aby uzyskać więcej informacji na temat dodawania pojemności do środowiska, przeczytaj [skaluj środowisko](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Następne kroki

- Przeczytaj o [diagnozowaniu i rozwiązywaniu problemów w środowisku usługi Time Series Insights](time-series-insights-diagnose-and-solve-problems.md).

- Dowiedz [się, jak skalować środowisko usługi Time Series Insights](time-series-insights-how-to-scale-your-environment.md).