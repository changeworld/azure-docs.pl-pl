---
title: Rozwiązywanie problemów z alertami dzienników w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Typowe problemy, błędy i rozwiązania dla reguł alertów dzienników na platformie Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 0c7189f1d43a114532b30b0c1aabe6f7cd4402d8
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578717"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>Rozwiązywanie problemów z alertami dzienników w usłudze Azure Monitor  

## <a name="overview"></a>Omówienie

W tym artykule pokazano, jak rozwiązywanie typowych problemów występujących podczas konfigurowania alertów dzienników w usłudze Azure Monitor. Zapewnia również rozwiązania często zadawane pytania dotyczące funkcji lub konfiguracji alertów dzienników. 

Termin **alertów dzienników** opisuje alerty, że ognia na podstawie dziennika zapytania w [obszaru roboczego usługi Log Analytics](../learn/tutorial-viewdata.md) lub [usługi Application Insights](../../azure-monitor/app/analytics.md). Dowiedz się więcej o funkcji, terminologii i typy w [rejestrowania alertów — omówienie](../platform/alerts-unified-log.md).

> [!NOTE]
> W tym artykule nie bierze pod uwagę przypadków, gdy witryna Azure portal Wyświetla i alertu wyzwolona reguła i powiadomień, wykonywane przez skojarzonych grup akcji. W takich przypadkach można znaleźć szczegółowe informacje w artykule na [grup akcji](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>Alert dziennika nie został uruchomiony

Poniżej przedstawiono niektóre typowe przyczyny, dlaczego skonfigurowanego [reguł alertów dzienników w usłudze Azure Monitor](../platform/alerts-log.md) nie wyświetla stan [jako *wyzwolone* Oczekiwano](../platform/alerts-managing-alert-states.md). 

### <a name="data-ingestion-time-for-logs"></a>Czas wprowadzania danych dla dzienników

Alert dziennika okresowo działa na podstawie zapytania [usługi Log Analytics](../learn/tutorial-viewdata.md) lub [usługi Application Insights](../../azure-monitor/app/analytics.md). Ponieważ usługa Azure Monitor przetwarza wielu terabajtów danych od tysięcy klientów z różnych źródeł na całym świecie, usługa jest podatny na różnych opóźnienie czasowe. Aby uzyskać więcej informacji, zobacz [czas wprowadzania danych w usłudze Azure Monitor dziennikach](../platform/data-ingestion-time.md).

Aby zminimalizować opóźnienie pozyskiwania danych, system czeka i ponawia zapytanie alertu wiele razy, jeśli stwierdzi, że potrzebne dane nie są jeszcze pozyskiwane. System ma wykładniczo zwiększa czasu oczekiwania. Dziennik alertów wyzwalaczy tylko po danych jest dostępna, więc ich opóźnienie może być spowodowany pozyskiwanie danych wolnego dziennika. 

### <a name="incorrect-time-period-configured"></a>Skonfigurowano nieprawidłowy okres.

Zgodnie z opisem w artykule na [terminologii dla dziennika alertów](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types), czas okresu określone w konfiguracji określa zakres czasu dla zapytania. Zapytanie zwraca tylko te rekordy, które zostały utworzone w tym zakresie czasu. Okres ogranicza dane pobrana dla zapytania dotyczącego dziennika zapobiec nadużyciu i zmierzone dowolnego polecenia na czas (takich jak *temu*) używanych w zapytaniu dziennika. Na przykład jeśli okres czasu jest ustawiony na 60 minut, a zapytanie jest uruchomione o 13:15, tylko rekordy utworzone z zakresu od 12:15:00 do 13:15 czasu są używane do zapytania dotyczącego dziennika. Jeśli zapytanie dziennika korzysta z czasu polecenia podobnego *temu (1d)*, zapytanie nadal korzysta z danych zakresu od 12:15:00 do 13:15 czasu ponieważ okres czasu jest ustawiona na tym interval.*

Dlatego sprawdzanie okresu w konfiguracji zgodnego z zapytaniem. Na przykład, o których wspomniano wcześniej, jeśli używa zapytania dotyczącego dziennika *temu (1d)* pokazany z zielonym znacznikiem to okres czasu powinien być ustawiony do 24 godzin lub 1440 minut (jak wskazano w czerwony), aby upewnić się, wykonuje zapytanie, zgodnie z oczekiwaniami.

![Okres](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Pomiń alerty ustawiono opcję

Zgodnie z opisem w kroku 8 artykułu na [tworzenia reguł alertów dzienników w witrynie Azure portal](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal), podaj alertów dzienników **Pomiń alerty** opcję Pomiń akcje wyzwalania i powiadomień w skonfigurowanym czas. Co w efekcie użytkownik może uznać, że alert nie fire w rzeczywistości go zostały, ale zostało pominięte.  

![Pomiń alerty](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Reguła alertu pomiaru metryki jest nieprawidłowy

**Alerty dzienników pomiar metryki** jest podtypem alertów dzienników, które mają specjalne funkcje i składnia ograniczeniami zapytanie alertu. Pomiar metryki reguł alertów dzienników wymaga kwerendy, dane wyjściowe jako szeregów czasowych metryki; oznacza to tabelę zawierającą różne równie o rozmiarze okresach czasu oraz odpowiadające im wartości zagregowanych. Ponadto użytkownicy mogą wybierać ma dodatkowe zmienne tabeli obok elementy AggregatedValue. Te zmienne mogą służyć do sortowania tabeli. 

Na przykład załóżmy, że regułę alertu pomiaru metryki dziennika został skonfigurowany jako:

- Zapytanie było: `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- okresu o długości 6 godzin
- Próg wynoszący 50
- Logika alertu trzy kolejne naruszenia
- Łączny na wybrany jako $table

Ponieważ polecenie zawiera *... Sumuj według* oraz podać dwie zmienne (sygnatura czasowa & $table), system wybiera $table do *agregacji po*. Sortuje tabelę wyników za pomocą pola *$table* pokazany poniżej i następnie przegląda wielu elementy AggregatedValue dla każdego typu tabeli (na przykład availabilityResults), aby zobaczyć, jeśli było kolejne naruszenia 3 lub więcej.

![Metryki pomiaru wykonywania zapytań z wieloma wartościami](media/alert-log-troubleshoot/LogMMQuery.png)

Jako *agregacji po* jest zdefiniowany w *$table*, dane są sortowane według kolumny $table (jak kolor czerwony); a następnie możemy grupy i poszukaj typy *agregacji po* pola (to znaczy) $table dla przykład: wartości availabilityResults jest traktowany jako jeden wykres na jednostkę (jako wyróżnione w kolorze pomarańczowym). W tej jednostce wartość wykres/usługa alertu dotyczącego wyszukuje trzy kolejne naruszenia występujących (jak pokazano w kolorze zielonym) dla alertu, które będą wyzwalane dla wartości z tabeli "availabilityResults". Podobnie jeśli w dowolnej innej wartości $table jeśli są widoczne trzy kolejne naruszenia — inny powiadomień o alertach będą wyzwalane przez to samo; przy użyciu alertów usługi automatycznie sortowania wartości w jedną kreślenia/jednostkę (tak jak w kolorze pomarańczowym) według czasu.

Teraz załóżmy, pomiar metryki reguł alertów dzienników został zmodyfikowany, a zapytanie było `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` z pozostałą częścią konfiguracji pozostała taka sama jak przed dołączeniem alertu logikę trzy kolejne naruszenia. Opcja "Agregacji po" będzie w tym przypadku domyślnie: sygnatura czasowa. Ponieważ tylko jedną wartość znajduje się w zapytaniu dla *... Sumuj według* (to znaczy) *sygnatura czasowa*; podobny do poprzedniego przykładu, na końcu wykonywania dane wyjściowe będą, jak pokazano poniżej.

   ![Metryki pomiaru wykonywania zapytań o pojedynczej wartości](media/alert-log-troubleshoot/LogMMtimestamp.png)

Jako *agregacji po* jest zdefiniowana na sygnatury czasowej, dane są sortowane na *sygnatura czasowa* kolumnę (jak kolor czerwony); a następnie możemy Grupuj według sygnatur czasowych na przykład: wartości `2018-10-17T06:00:00Z` jest traktowany jako jeden wykres na jednostkę (tak wyróżnione kolorem pomarańczowym). W tej jednostce wartość wykres/alertu usługa będzie odnaleźć nie kolejne naruszenia występujące (jak każda wartość sygnatury czasowej ma tylko jeden wpis) i dlatego alertu nigdy nie będą wyzwalane. Dlatego w takim przypadku użytkownik musi albo:

- Dodaj fikcyjną zmiennej lub istniejącej zmiennej (na przykład $table) do poprawnie sortowanie gotowe, przy użyciu pola "Agregacji po" skonfigurowane
- Skonfiguruj ponownie używana logika alertu na podstawie reguły alertów (lub) *łącznie naruszenia* zamiast odpowiednio

## <a name="log-alert-fired-unnecessarily"></a>Alert dziennika uruchamiane niepotrzebnie

Szczegółowe dalej są niektóre typowe przyczyny, dlaczego skonfigurowanego [reguł alertów dzienników w usłudze Azure Monitor](../platform/alerts-log.md) mogą być wyzwalane podczas wyświetlania w [Azure Alerts](../platform/alerts-managing-alert-states.md), gdy nie będziesz już go do uruchomienia.

### <a name="alert-triggered-by-partial-data"></a>Alert wyzwolony przez częściowe dane

Włączanie usługi Log Analytics i usługi Application Insights Analytics jest zależna od opóźnienia pozyskiwania i przetwarzania; z powodu, w momencie uruchamiania zapytanie alertu dzienników podana — mogą wystąpić przypadek żadne dane, które są dostępne lub tylko niektórych danych, które są dostępne. Aby uzyskać więcej informacji, zobacz [dziennika czas wprowadzania danych w usłudze Azure Monitor](../platform/data-ingestion-time.md).

W zależności od sposobu skonfigurowania reguły alertu może być źle wielopaliwowego w przypadku nie lub częściowe dane w dziennikach w czasie wykonywania alertu. W takich przypadkach zaleca się zmiany zapytanie alertu ani konfiguracji. 

Na przykład, jeśli skonfigurowano reguł alertów dzienników do wyzwalania, gdy liczba wyników z zapytania analizy jest mniejsza niż 5, a następnie alert uruchamiają gdy nie ma danych (zero rekordu) lub częściowe wyniki (jeden rekord). Jednak opóźnieniem pozyskiwania danych tego samego zapytania przy użyciu pełnych danych może zawierać wynik 10 rekordów.

### <a name="alert-query-output-misunderstood"></a>Dane wyjściowe zapytanie alertu źle zrozumiane

Możesz podać logiki dla dziennika alertów w zapytania usługi analytics. Zapytania usługi analytics może używać różnych danych big data i funkcji matematycznych.  Alerty usług wykonuje zapytanie w odstępach czasu określonych danych dla określonego okresu. Alerty usługi sprawia, że wprowadzono subtelne zmiany zapytania oparte na wybranego typu alertu. Ta zmiana mogą być wyświetlane w sekcji "Wyślij zapytanie w celu wykonania" *konfigurowanie logiki sygnału* ekranu, jak pokazano poniżej: ![Zapytanie do wykonania](media/alert-log-troubleshoot/LogAlertPreview.png)

Co to jest wyświetlany w **zapytanie do wykonania** pole jest działa usługa alertu dotyczącego dziennika. Możesz uruchomić określonego zapytania, a także przedział czasu za pośrednictwem [portalu analiza](../log-query/portals.md) lub [interfejsu API analizy](https://docs.microsoft.com/rest/api/loganalytics/) Jeśli chcesz zrozumieć, jakie zapytanie alertu danych wyjściowych może być przed faktycznie utworzenia alertu.

## <a name="log-alert-was-disabled"></a>Alert dziennika zostało wyłączone.

Poniżej przedstawiono niektóre przyczyny z powodu której [reguł alertów dzienników w usłudze Azure Monitor](../platform/alerts-log.md) mogą być wyłączone przez usługi Azure Monitor.

### <a name="resource-on-which-alert-was-created-no-longer-exists"></a>Zasób, na którym został utworzony alert nie jest już istnieje

Utworzone w usłudze Azure Monitor reguł alertów dzienników docelowych określonych zasobów, takich jak Azure Log Analytics w obszarze roboczym aplikacji usługi Azure Application Insights i zasobów platformy Azure. Usługa alertu dotyczącego dziennika zostanie uruchomi, zapytania usługi analytics podany w zasadzie dla określonego celu. Jednak po utworzeniu reguły często użytkowników przejdź do usunięcia z platformy Azure lub przeniesienia wewnątrz platformy Azure — element docelowy reguły alertu. Zgodnie z celem reguł alertów dzienników nie jest już prawidłowy, wykonywanie reguły kończy się niepowodzeniem.

W takich przypadkach usługi Azure Monitor Wyłącz alert dziennika i upewnij się, że klienci nie są rozliczane niepotrzebnie, gdy reguła sam nie jest w stanie wykonać stale inwestycją okres, takich jak tygodnia. Użytkownicy mogą znaleźć na dokładny czas, w którym reguł alertów dzienników został wyłączony przez usługi Azure Monitor za pośrednictwem [dziennika aktywności platformy Azure](../../azure-resource-manager/resource-group-audit.md). W dzienniku aktywności platformy Azure po wyłączeniu reguł alertów dzienników przez platformę Azure, zostanie dodane zdarzenie w dzienniku aktywności platformy Azure.

Zdarzenie próbkowania w dzienniku aktywności platformy Azure dla reguły alertu wyłączanie z powodu braku ciągłe; Poniżej przedstawiono.

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

### <a name="query-used-in-log-alert-is-not-valid"></a>Zapytanie używane w dzienniku alertów jest nieprawidłowe

Każdej reguły alertu dziennika utworzonego w usłudze Azure Monitor w ramach konfiguracji należy określić zapytania usługi analytics do wykonania, okresowo przez usługę alertów. Podczas zapytania usługi analytics może mieć poprawną składnię w czasie tworzenia reguły lub aktualizacji. Kilka razy w okresie, zapytanie, podaj w dzienniku reguły alertu mogą tworzyć problemy składni i spowodować wykonanie reguła będzie kończyć się niepowodzeniem. Niektóre typowe przyczyny, dlaczego zapytania usługi analytics podawany reguł alertów dzienników można opracować błędy są:

- Zapytania są zapisywane do [działającym na wielu zasobach](../log-query/cross-workspace-query.md) i przynajmniej jedna z określonych zasobów, obecnie nie istnieją.
- Wystąpił nie przepływu danych do korzystania z platformy analizy z powodu której [wykonywania zapytań powoduje błąd](https://dev.loganalytics.io/documentation/Using-the-API/Errors) jako nie ma żadnych danych dla podanego zapytania.
- Zmiany w [język zapytań](https://docs.microsoft.com/azure/kusto/query/) miały miejsce w jakie polecenia i funkcje zostały poprawione formatu. Dlatego wcześniej podane zapytanie w regule alertu nie jest już prawidłowy.

Użytkownik będą ostrzegani to zachowanie najpierw za pomocą [usługi Azure Advisor](../../advisor/advisor-overview.md). Zalecenia zostanie dodany do określonego dziennika regułę alertu w usłudze Azure Advisor do kategorii o wysokiej dostępności przy użyciu średni wpływ i opis, jak "Naprawą usługi reguł alertów dzienników, aby upewnić się, monitorowanie". Jeśli kwerenda alertu w regule alertu określonego dziennika nie zostanie zmieniona po upływie siedmiu dni, zapewniając zaleceń w usłudze Azure Advisor. Następnie usługa Azure Monitor Wyłącz alert dziennika i upewnij się, że klienci nie są rozliczane niepotrzebnie, gdy reguła sam nie jest w stanie wykonać stale inwestycją okres, takich jak tygodnia.

Użytkownicy mogą znaleźć na dokładny czas, w którym reguł alertów dzienników został wyłączony przez usługi Azure Monitor za pośrednictwem [dziennika aktywności platformy Azure](../../azure-resource-manager/resource-group-audit.md). W dzienniku aktywności platformy Azure gdy reguł alertów dzienników jest wyłączona przez platformę Azure — zostanie dodane zdarzenie w dzienniku aktywności platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [alerty dzienników w alertów platformy Azure](../platform/alerts-unified-log.md)
- Dowiedz się więcej o [usługi Application Insights](../../azure-monitor/app/analytics.md)
- Dowiedz się więcej o [rejestrowania zapytań](../log-query/log-query-overview.md)
