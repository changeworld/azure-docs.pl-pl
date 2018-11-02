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
ms.openlocfilehash: 0e1cb2cdd6270590def11479cc5859d996d84caa
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2018
ms.locfileid: "50749058"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>Rozwiązywanie problemów z alertami dzienników w usłudze Azure Monitor  

## <a name="overview"></a>Przegląd
Ten artykuł pokazuje, że obsługa typowych problemów występujących podczas konfigurowania alertów dzienników w usłudze Azure monitor. I zapewnić rozwiązanie na często zadawane pytania dotyczące funkcji lub konfiguracji alertów dzienników. Termin **alertów dzienników** do opisania alerty, gdy sygnał jest zapytanie niestandardowe na podstawie [usługi Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) lub [usługi Application Insights](../application-insights/app-insights-analytics.md). Dowiedz się więcej o funkcji, terminologii i typów z [rejestrowania alertów — omówienie](monitor-alerts-unified-log.md).

> [!NOTE]
> W tym artykule nie bierze pod uwagę przypadków, gdy reguła alertu zostanie wyświetlone jako wyzwolone w witrynie Azure portal i powiadomienia za pośrednictwem skojarzonych grup akcji. W takich przypadkach można znaleźć szczegółowe informacje w artykule na [grup akcji](monitoring-action-groups.md).


## <a name="log-alert-didnt-fire"></a>Alert dziennika nie został uruchomiony

Dlaczego niektóre typowe przyczyny są szczegółowe dalej skonfigurowanych [reguł alertów dzienników w usłudze Azure Monitor](alert-log.md) nie wyzwalane podczas wyświetlania w [Azure Alerts](monitoring-alerts-managing-alert-states.md), gdy spodziewasz się ona do uruchomienia. 

### <a name="data-ingestion-time-for-logs"></a>Czas wprowadzania danych dla dzienników
Dziennika alertów działa przez okresowe uruchamianie zapytania klienta na podstawie [usługi Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) lub [usługi Application Insights](../application-insights/app-insights-analytics.md). Oba są obsługiwane przez możliwości analizy, który przetwarza ogromne ilości danych dzienników i oferuje funkcję na tym samym. Jak usługa Log Analytics obejmuje przetwarzanie wielu terabajtów z tysięcy klientów i z różnorodnych źródeł danych na całym świecie — usługa jest podatny na czas opóźnienia. Aby uzyskać więcej informacji, zobacz [czas wprowadzania danych w usłudze Log Analytics](../log-analytics/log-analytics-data-ingestion-time.md).

Aby wyeliminować opóźnienie pozyskiwania danych, które mogą wystąpić w usłudze Log Analytics lub dzienników usługi Application Insights alert dziennika czeka i ponawia próbę po pewnym czasie, kiedy go stwierdza, że dane nie są jeszcze pozyskiwane w okresie alertów. Alerty dziennika ma zestaw wykładniczo zwiększa czas oczekiwania, aby upewnij się, że poczekamy czasu niezbędne dane można pozyskać przez usługę Log Analytics. Dlatego jeśli dzienniki żądanych przez reguły alertów dzienników jest narażony na opóźnienia pozyskiwania, następnie alertu dziennika wyzwoli tylko wtedy, gdy dane są dostępne w usługi Log Analytics po pozyskiwania i po odstępu czasu wykładniczego z powodu dziennika usługa alertu dotyczącego ponawianie próby wiele razy w międzyczasie .

### <a name="incorrect-time-period-configured"></a>Skonfigurowano nieprawidłowy okres.
Zgodnie z opisem w artykule na [terminologii dla dziennika alertów](monitor-alerts-unified-log.md#log-search-alert-rule---definition-and-types), okres czasu określona w konfiguracji określa zakres czasu dla zapytania. Zapytanie zwraca tylko te rekordy, które zostały utworzone w tym zakresie czasu. Okres ogranicza dane pobrana dla zapytania dotyczącego dziennika zapobiec nadużyciu i zmierzone dowolnego polecenia na czas (np. temu) używanych w zapytaniu dziennika. 
*Na przykład jeśli okres czasu jest ustawiony na 60 minut, a zapytanie jest uruchomione o 13:15, tylko rekordy utworzone z zakresu od 12:15:00 do 13:15 czasu jest zwracana do wykonania zapytania dotyczącego dziennika. Jeśli zapytanie dziennika korzysta z czasu polecenia, takie jak temu teraz 1d, zapytanie dziennika zostanie uruchomione tylko dla danych zakresu od 12:15:00 do 13:15:00 - tak, jakby istnieją dane dla ostatnich 60 minut. A nie przez siedem dni, danych, jak to określono w dzienniku zapytań.*

Oparte na logice zapytania, sprawdź, jeśli podano odpowiedni okres czasu w konfiguracji. Na przykład, o których wspomniano wcześniej Jeśli dziennik zapytanie używa temu (1d), przedstawione przy użyciu zielonego znacznika — a następnie przedziale czasu powinna być ustawiona do 24 godzin lub 1440 minut (co zostało wskazane na czerwono), zapewnienie dostarczone zapytanie jest wykonywany poprawnie przewidywanych.
    ![Okres](./media/monitor-alerts-unified/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Pomiń alerty ustawiono opcję
Zgodnie z opisem w kroku 8 artykułu na [tworzenia reguł alertów dzienników w witrynie Azure portal](alert-log.md#managing-log-alerts-from-the-azure-portal), alertu dziennika udostępnia opcję konfigurowania automatycznych pomijanie reguły alertu i zapobiec powiadomień/wyzwalacza przez określony czas. Pomiń alerty opcji spowoduje, że alert dziennika do wykonania podczas nie wyzwolenie akcji grupy przez czas określony w **Pomiń alerty** opcji, a więc użytkownik może mieć wrażenie tego alertu nie fire, chociaż w rzeczywistości zostało pominięte, zgodnie z konfiguracją .
    ![Pomiń alerty](./media/monitor-alerts-unified/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Reguła alertu pomiaru metryki jest nieprawidłowy
Typ metryki pomiaru reguł alertów dzienników jest podtypem alertów dzienników, funkcje specjalne, które z kolei wykorzystuje ograniczeń składni zapytań alertu. Alert dziennika pomiar metryki, że reguła wymaga danych wyjściowych zapytanie alertu, aby zapewnić szeregów czasowych metryki — tabelę zawierającą różne równej wielkości okresy wraz z odpowiednimi wartościami elementy AggregatedValue obliczane. Ponadto użytkownicy mogą wybierać znajdują się w tabeli dodatkowe zmienne, wraz z elementy AggregatedValue, takich jak komputer, węzeł itp. można sortować przy użyciu danych z tabeli.

Na przykład załóżmy, że reguł alertów dzienników pomiar metryki został skonfigurowany jako:
- Zapytanie było: `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- okresu o długości 6 godzin
- Próg wynoszący 50
- Logika alertu trzy kolejne naruszenia
- Łączny na wybrany jako $table

Teraz, ponieważ w poleceniu użyliśmy podsumowanie … oraz podać dwie zmienne: sygnatura czasowa & $table; Alert usługi wybierze $table "Agregacji po" - zasadniczo sortowanie tabeli wyników według pola: $table - pokazany poniżej i Wyświetlę wielu elementy AggregatedValue dla każdej tabeli wpisz (na przykład availabilityResults) w celu sprawdzenia, jeśli było kolejne naruszenia 3 lub więcej.

   ![Metryki pomiaru wykonywania zapytań z wieloma wartościami](./media/monitor-alerts-unified/LogMMQuery.png)

Jak "Łączna od" jest $table — dane są sortowane według kolumny $table (jak kolor czerwony); Firma Microsoft grupy i Znajdź typ pola "Agregacji po" (to znaczy) $table — na przykład: wartości availabilityResults jest traktowany jako jeden wykres na jednostkę (jako wyróżnione w kolorze pomarańczowym). W tym wykres wartość na jednostkę — usługa alertu dotyczącego wyszukuje trzy kolejne naruszenia występujących (jak pokazano w kolorze zielonym) dla alertu, które będą wyzwalane dla wartości z tabeli "availabilityResults". Podobnie jeśli w dowolnej innej wartości $table jeśli są widoczne trzy kolejne naruszenia — inny powiadomień o alertach będą wyzwalane przez takie same; przy użyciu alertów usługi automatycznie sortowania wartości w jedną kreślenia/jednostkę (tak jak w kolorze pomarańczowym) według czasu.

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
 
Przedstawionego na **zapytanie do wykonania** sekcja jest jakie dziennika alertu usługa zostanie uruchomiona; użytkownik może uruchomić określonej kwerendy, a także przedział czasu za pośrednictwem [portalu analiza](../log-analytics/log-analytics-log-search-portals.md) lub [interfejsu API analizy](https://docs.microsoft.com/en-us/rest/api/loganalytics/) -Jeśli chcą wiedzieć przed tworzenia alertu, może to być dane wyjściowe zapytanie alertu.
 
## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [alerty dzienników w alertów platformy Azure](monitor-alerts-unified-log.md)
* Dowiedz się więcej o [usługi Application Insights](../application-insights/app-insights-analytics.md)
* Dowiedz się więcej o [usługi Log Analytics](../log-analytics/log-analytics-overview.md). 