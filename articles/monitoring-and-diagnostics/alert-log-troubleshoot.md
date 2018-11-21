---
title: Rozwiązywanie problemów z alertami dzienników w usłudze Azure Monitor
description: Typowe problemy, błędów i rozwiązania dla dziennika alertów reguły na platformie Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: e2326f56ad367f744bc7895bc8c4bfd6f32d0310
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2018
ms.locfileid: "52264883"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>Rozwiązywanie problemów z alertami dzienników w usłudze Azure Monitor  
## <a name="overview"></a>Przegląd
W tym artykule pokazano, jak rozwiązywanie typowych problemów występujących podczas konfigurowania alertów dzienników w usłudze Azure monitor. Zapewnia również rozwiązania często zadawane pytania dotyczące funkcji lub konfiguracji alertów dzienników. 

Termin **alertów dzienników** do opisania alerty, że ognia na podstawie niestandardowych zapytania w [usługi Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) lub [usługi Application Insights](../application-insights/app-insights-analytics.md). Dowiedz się więcej o funkcji, terminologii i typy w [rejestrowania alertów — omówienie](monitor-alerts-unified-log.md).

> [!NOTE]
> W tym artykule nie bierze pod uwagę przypadków, gdy witryna Azure portal Wyświetla i alertu wyzwolona reguła i powiadomień, wykonywane przez skojarzonych grup akcji. W takich przypadkach można znaleźć szczegółowe informacje w artykule na [grup akcji](monitoring-action-groups.md).


## <a name="log-alert-didnt-fire"></a>Alert dziennika nie został uruchomiony

Poniżej przedstawiono niektóre typowe przyczyny, dlaczego skonfigurowanego [reguł alertów dzienników w usłudze Azure Monitor](alert-log.md) nie wyświetla stan [jako *wyzwolone* Oczekiwano](monitoring-alerts-managing-alert-states.md). 

### <a name="data-ingestion-time-for-logs"></a>Czas wprowadzania danych dla dzienników
Alert dziennika okresowo działa na podstawie zapytania [usługi Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) lub [usługi Application Insights](../application-insights/app-insights-analytics.md). Ponieważ usługi Log Analytics przetwarza wielu terabajtów danych od tysięcy klientów z różnych źródeł na całym świecie, usługa jest podatny na różnych opóźnienie czasowe. Aby uzyskać więcej informacji, zobacz [czas wprowadzania danych w usłudze Log Analytics](../log-analytics/log-analytics-data-ingestion-time.md).

Aby zminimalizować opóźnienie pozyskiwania danych, system czeka i ponawia zapytanie alertu wiele razy, jeśli stwierdzi, że potrzebne dane nie są jeszcze pozyskiwane. System ma wykładniczo zwiększa czasu oczekiwania. Dziennik alertów wyzwalaczy tylko po danych jest dostępna, więc ich opóźnienie może być spowodowany pozyskiwanie danych wolnego dziennika. 

### <a name="incorrect-time-period-configured"></a>Skonfigurowano nieprawidłowy okres.
Zgodnie z opisem w artykule na [terminologii dla dziennika alertów](monitor-alerts-unified-log.md#log-search-alert-rule---definition-and-types), czas okresu określone w konfiguracji określa zakres czasu dla zapytania. Zapytanie zwraca tylko te rekordy, które zostały utworzone w tym zakresie czasu. Okres ogranicza dane pobrana dla zapytania dotyczącego dziennika zapobiec nadużyciu i zmierzone dowolnego polecenia na czas (np. temu) używanych w zapytaniu dziennika. 
*Na przykład jeśli okres czasu jest ustawiony na 60 minut, a zapytanie jest uruchomione o 13:15, tylko rekordy utworzone z zakresu od 12:15:00 do 13:15 czasu są używane do zapytania dotyczącego dziennika. Jeśli zapytanie dziennika korzysta z czasu polecenia podobnego *temu (1d)*, zapytanie nadal tylko używa danych między 12:15 PM i 13:15 czasu, ponieważ w okresie jest równa tego interwału.*

Dlatego sprawdzanie okresu w konfiguracji zgodnego z zapytaniem. Na przykład, o których wspomniano wcześniej, jeśli używa zapytania dotyczącego dziennika *temu (1d)* pokazany z zielonym znacznikiem to okres czasu powinien być ustawiony do 24 godzin lub 1440 minut (jak wskazano w czerwony), aby upewnić się, wykonuje zapytanie, zgodnie z oczekiwaniami.

![Okres](./media/monitor-alerts-unified/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Pomiń alerty ustawiono opcję
Zgodnie z opisem w kroku 8 artykułu na [tworzenia reguł alertów dzienników w witrynie Azure portal](alert-log.md#managing-log-alerts-from-the-azure-portal), podaj alertów dzienników **Pomiń alerty** opcję Pomiń akcje wyzwalania i powiadomień w skonfigurowanym czas. Co w efekcie użytkownik może uznać, że alert nie fire w rzeczywistości go zostały, ale zostało pominięte.  

![Pomiń alerty](./media/monitor-alerts-unified/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Reguła alertu pomiaru metryki jest nieprawidłowy
**Alerty dzienników pomiar metryki** jest podtypem alertów dzienników, które mają specjalne funkcje i składnia ograniczeniami zapytanie alertu. Pomiar metryki reguł alertów dzienników wymaga kwerendy, dane wyjściowe jako szeregów czasowych metryki; oznacza to tabelę zawierającą różne równie o rozmiarze okresach czasu oraz odpowiadające im wartości zagregowanych. Ponadto użytkownicy mogą wybierać ma dodatkowe zmienne tabeli obok elementy AggregatedValue. Te zmienne mogą służyć do sortowania tabeli. 

Na przykład załóżmy, że regułę alertu pomiaru metryki dziennika został skonfigurowany jako:
- Zapytanie było: `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- okresu o długości 6 godzin
- Próg wynoszący 50
- Logika alertu trzy kolejne naruszenia
- Łączny na wybrany jako $table

Ponieważ polecenie zawiera *... Sumuj według* oraz podać dwie zmienne (sygnatura czasowa & $table), system wybiera $table do "Agregacji po". Sortuje tabelę wyników za pomocą pola *$table* pokazany poniżej i następnie przegląda wielu elementy AggregatedValue dla każdego typu tabeli (na przykład availabilityResults), aby zobaczyć, jeśli było kolejne naruszenia 3 lub więcej.

![Metryki pomiaru wykonywania zapytań z wieloma wartościami](./media/monitor-alerts-unified/LogMMQuery.png)

Jak "Łączna od" jest $table — dane są sortowane według kolumny $table (jak kolor czerwony); Firma Microsoft grupy i Znajdź typ pola "Agregacji po" (to znaczy) $table — na przykład: wartości availabilityResults jest traktowany jako jeden wykres na jednostkę (jako wyróżnione w kolorze pomarańczowym). W tym wykres wartość na jednostkę — usługa alertu dotyczącego wyszukuje trzy kolejne naruszenia występujących (jak pokazano w kolorze zielonym) dla alertu, które będą wyzwalane dla wartości z tabeli "availabilityResults". Podobnie jeśli w dowolnej innej wartości $table jeśli są widoczne trzy kolejne naruszenia — inny powiadomień o alertach będą wyzwalane przez to samo; przy użyciu alertów usługi automatycznie sortowania wartości w jedną kreślenia/jednostkę (tak jak w kolorze pomarańczowym) według czasu.

Teraz załóżmy, pomiar metryki reguł alertów dzienników został zmodyfikowany, a zapytanie było `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` z pozostałą częścią konfiguracji pozostała taka sama jak przed dołączeniem alertu logikę trzy kolejne naruszenia. Opcja "Agregacji po" będzie w tym przypadku domyślnie: sygnatura czasowa. Ponieważ tylko jedną wartość znajduje się w zapytaniu dla... Sumuj według timestamp (to znaczy); podobny do poprzedniego przykładu, na końcu wykonywania dane wyjściowe będą jak przedstawiono poniżej. 

   ![Metryki pomiaru wykonywania zapytań o pojedynczej wartości](./media/monitor-alerts-unified/LogMMtimestamp.png)

Jak "Agregacji po" jest sygnatura czasowa — dane są sortowane według kolumny sygnatur czasowych (jak kolor czerwony); Następnie możemy Grupuj według sygnatury czasowej — na przykład: wartości `2018-10-17T06:00:00Z` jest traktowany jako jeden wykres na jednostkę (jako wyróżnione w kolorze pomarańczowym). Ten wykres wartość na jednostkę — usługa alertu dotyczącego zawiera że nie kolejne naruszenia występujące (jak każda wartość sygnatury czasowej ma tylko jeden wpis) i dlatego alertu nigdy nie będą wyzwalane. Dlatego w takim przypadku użytkownik musi-
- Dodaj fikcyjną zmiennej lub istniejącej zmiennej (na przykład $table) do poprawnie sortowanie gotowe, przy użyciu pola "Agregacji po" skonfigurowane
- Skonfiguruj ponownie używana logika alertu na podstawie reguły alertów (lub) *łącznie naruszenia* zamiast odpowiednio
 
## <a name="log-alert-fired-unnecessarily"></a>Alert dziennika uruchamiane niepotrzebnie
Szczegółowe dalej są niektóre typowe przyczyny, dlaczego skonfigurowanego [reguł alertów dzienników w usłudze Azure Monitor](alert-log.md) mogą być wyzwalane podczas wyświetlania w [Azure Alerts](monitoring-alerts-managing-alert-states.md), gdy nie będziesz już go do uruchomienia.

### <a name="alert-triggered-by-partial-data"></a>Alert wyzwolony przez częściowe dane
Włączanie usługi Log Analytics i usługi Application Insights Analytics jest zależna od opóźnienia pozyskiwania i przetwarzania; z powodu, w momencie uruchamiania zapytanie alertu dzienników podana — mogą wystąpić przypadek żadne dane, które są dostępne lub tylko niektórych danych, które są dostępne. Aby uzyskać więcej informacji, zobacz [czas wprowadzania danych w usłudze Log Analytics](../log-analytics/log-analytics-data-ingestion-time.md).

W zależności od sposobu skonfigurowania reguły alertu może być źle wielopaliwowego w przypadku, gdy jest nie lub częściowe dane w dziennikach w czasie wykonywania alertu. W takich przypadkach zaleca się, czy zapytanie alertu lub konfiguracji jest zmieniony. *Na przykład jeśli reguła alertu dziennika jest skonfigurowana do wyzwalania, gdy liczba wyników z zapytania analizy jest mniejsza niż () 5; wówczas, gdy nie ma danych (zero rekordu) lub częściowe wyniki (jeden rekord) będą wyzwalane reguły alertu. Gdzie — zgodnie z opóźnieniem pozyskiwania, kiedy tego samego zapytania ma zostać uruchomiona w usłudze Analytics zapytania przy użyciu pełnych danych może zawierać wynik jako 10 rekordów.*

### <a name="alert-query-output-misunderstood"></a>Dane wyjściowe zapytanie alertu źle zrozumiane
Dla dziennika alertów logikę alertów są dostarczane przez użytkownika za pośrednictwem zapytania usługi analytics. Dostarczone zapytanie usługi analytics można używać różnych danych Big Data i funkcji matematycznych, aby utworzyć określone konstrukcji. Alerty usługi wykona zapytań klienta w odstępach czasu określonych danych w przedziale czasu określony; alerty usługi sprawia, że subtelne zmiany do kwerendy dostarczone — oparte na wybranego typu alertu i takie same można być poświadczeniem "Wyślij zapytanie w celu wykonania" w sekcji Konfigurowanie sygnału logikę ekranu, jak przedstawiono poniżej: ![zapytanie do wykonania](./media/monitor-alerts-unified/LogAlertPreview.png)
 
Przedstawionego na **zapytanie do wykonania** sekcja jest jakie dziennika alertu usługa zostanie uruchomiona; użytkownik może uruchomić określonej kwerendy, a także przedział czasu za pośrednictwem [portalu analiza](../log-analytics/log-analytics-log-search-portals.md) lub [interfejsu API analizy](https://docs.microsoft.com/rest/api/loganalytics/) -Jeśli chcą wiedzieć przed tworzenia alertu, może to być dane wyjściowe zapytanie alertu.
 
## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [alerty dzienników w alertów platformy Azure](monitor-alerts-unified-log.md)
* Dowiedz się więcej o [usługi Application Insights](../application-insights/app-insights-analytics.md)
* Dowiedz się więcej o [usługi Log Analytics](../log-analytics/log-analytics-overview.md). 

