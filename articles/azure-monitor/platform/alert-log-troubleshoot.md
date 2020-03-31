---
title: Rozwiązywanie problemów z alertami dziennika w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Typowe problemy, błędy i rozwiązania reguł alertów dziennika na platformie Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: acb9784b745fa90fc9cd264162930020e6d64751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249037"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Rozwiązywanie problemów z alertami dziennika w usłudze Azure Monitor  

W tym artykule pokazano, jak rozwiązać typowe problemy, które mogą wystąpić podczas konfigurowania alertów dziennika w usłudze Azure Monitor. Zapewnia również rozwiązania typowych problemów z funkcjonalnością lub konfiguracją alertów dziennika.

Alerty dziennika termin opisać *reguły,* które są uruchamiane na podstawie kwerendy dziennika w [obszarze roboczym usługi Azure Log Analytics](../learn/tutorial-viewdata.md) lub w [usłudze Azure Application Insights.](../../azure-monitor/app/analytics.md) Dowiedz się więcej o funkcjach, terminologii i typach w [obszarze Alerty dziennika w usłudze Azure Monitor](../platform/alerts-unified-log.md).

> [!NOTE]
> W tym artykule nie uwzględni się przypadków, w których portal Azure zawiera regułę alertu wyzwalaną, a powiadomienie nie jest wykonywane przez skojarzoną grupę akcji. W takich przypadkach zobacz szczegóły w [obszarze Tworzenie grup akcji i zarządzanie nimi w witrynie Azure portal](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>Alert dziennika nie został odpalił

Oto kilka typowych powodów, dla których stan skonfigurowana [reguły alertów dziennika w usłudze Azure Monitor](../platform/alerts-log.md) nie jest pokazywany [jako *zwolniony,* gdy jest oczekiwany.](../platform/alerts-managing-alert-states.md)

### <a name="data-ingestion-time-for-logs"></a>Czas pozyskiwania danych dla dzienników

Alert dziennika okresowo uruchamia kwerendę na podstawie [usługi Log Analytics](../learn/tutorial-viewdata.md) lub application [insights](../../azure-monitor/app/analytics.md). Ponieważ usługa Azure Monitor przetwarza wiele terabajtów danych od tysięcy klientów z różnych źródeł na całym świecie, usługa jest podatna na różne opóźnienia czasowe. Aby uzyskać więcej informacji, zobacz [Czas pozyskiwania danych w dziennikach usługi Azure Monitor](../platform/data-ingestion-time.md).

Aby zmniejszyć opóźnienia, system czeka i ponawia kwerendę alertów wiele razy, jeśli znajdzie potrzebne dane nie jest jeszcze polonek. System ma wykładniczo zwiększający czas oczekiwania ustawiony. Alert dziennika jest wyzwalany tylko wtedy, gdy dane są dostępne, więc opóźnienie może być spowodowane powolnym pozyskiwania danych dziennika.

### <a name="incorrect-time-period-configured"></a>Skonfigurowano niepoprawny okres czasu

Jak opisano w artykule na [temat terminologii alertów dziennika](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types), okres podany w konfiguracji określa zakres czasu dla kwerendy. Kwerenda zwraca tylko rekordy, które zostały utworzone w tym zakresie.

Okres ogranicza dane pobrane dla kwerendy dziennika, aby zapobiec nadużyciom i omija każde polecenie czasu (jak **temu)** używane w kwerendzie dziennika. Na przykład Jeśli okres jest ustawiony na 60 minut, a kwerenda jest uruchamiana o godzinie 13:15, tylko rekordy utworzone między 12:15 PM i 13:15 PM są używane dla kwerendy dziennika. Jeśli kwerenda dziennika używa polecenia czasu, takiego jak **temu (1d),** kwerenda nadal używa tylko danych między 12:15 PM i 1:15 PM, ponieważ okres jest ustawiony na ten interwał.

Sprawdź, czy okres w konfiguracji jest zgodny z zapytaniem. W przykładzie pokazanym wcześniej, jeśli kwerenda dziennika używa **temu (1d)** z zielonym znacznikiem, okres czasu powinien być ustawiony na 24 godziny lub 1440 minut (oznaczony na czerwono). To ustawienie gwarantuje, że kwerenda działa zgodnie z oczekiwaniami.

![Okres](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Opcja Wygaszanie alertów jest ustawiona

Jak opisano w kroku 8 artykułu na [temat tworzenia reguły alertu dziennika w witrynie Azure portal](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal)alerty dziennika zapewniają **pomiń alerty** opcja, aby pominąć wyzwalanie i akcje powiadomień przez skonfigurowany czas. W rezultacie można by pomyśleć, że alert nie wystrzelił. W rzeczywistości, to nie ogień, ale został stłumiony.  

![Pomijanie alertów](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Reguła alertu pomiaru metryki jest niepoprawna

*Alerty dziennika pomiaru metryki* są podtypem alertów dziennika, które mają specjalne możliwości i składnię zapytania o ograniczony alert. Reguła dla alertu dziennika pomiaru metryki wymaga, aby dane wyjściowe kwerendy były szeregami czasów metryki. Oznacza to, że dane wyjściowe to tabela z odrębnymi, równymi rozmiarami okresami wraz z odpowiednimi wartościami zagregowanymi.

Można wybrać dodatkowe zmienne w tabeli obok **AggregatedValue**. Te zmienne mogą służyć do sortowania tabeli.

Załóżmy na przykład, że reguła alertu dziennika pomiaru metryki została skonfigurowana w następujący sposób:

- Zapytanie`search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- Okres 6 godzin
- Próg 50
- Logika alarmu trzech kolejnych naruszeń
- **Agregu po wybraniu** **jako $table**

Ponieważ polecenie zawiera **podsumowanie ... przez** i zapewnia dwie zmienne (**sygnatura czasowa** i **$table),** system wybiera **$table** dla **Aggregate Upon**. System sortuje tabelę wyników według pola **$table,** jak pokazano na poniższym zrzucie ekranu. Następnie patrzy na wiele **zagregowaneWycenniki wartości** dla każdego typu tabeli (np. **dostępnośćWyniki),** aby sprawdzić, czy istnieją trzy lub więcej kolejnych naruszeń.

![Wykonanie kwerendy pomiaru metryki z wieloma wartościami](media/alert-log-troubleshoot/LogMMQuery.png)

Ponieważ **agregacja na** jest zdefiniowana na **$table,** dane są sortowane w **kolumnie $table** (oznaczonej kolorem czerwonym). Następnie grupujemy i szukamy typów pola **Agreguj po.**

Na przykład dla **$table**, wartości **dostępnościWyniki** będą traktowane jako jeden wykres/encję (wskazane na pomarańczowo). W tej wartości wykresu/jednostki usługa alertów sprawdza trzy kolejne naruszenia (oznaczone na zielono). Naruszenia wyzwalają alert dla **wartości tabeliWyniki.**

Podobnie, jeśli trzy kolejne naruszenia zdarzają się dla jakiejkolwiek innej wartości **$table,** zostanie wywołane kolejne powiadomienie o tym samym. Usługa alertów automatycznie sortuje wartości w jednym wykresie/encji (oznaczonej na pomarańczowo) według czasu.

Teraz załóżmy, że reguła dla alertu dziennika `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`pomiaru metryki została zmodyfikowana, a kwerenda była . Pozostała część konfiguracji pozostała taka sama jak poprzednio, łącznie z logiką alertu dla trzech kolejnych naruszeń. **Opcja Agreguj po** w tym przypadku jest domyślnie **sygnaturą czasową.** Tylko jedna wartość jest podana w kwerendzie **podsumowują ... przez** (czyli **sygnaturę czasowa).** Podobnie jak w poprzednim przykładzie dane wyjściowe na końcu wykonania będzie jak pokazano w następujący sposób.

   ![Wykonanie kwerendy pomiaru metryki z wartością pojedynczą](media/alert-log-troubleshoot/LogMMtimestamp.png)

Ponieważ **agreguj na** jest zdefiniowany na **sygnatury czasowej,** dane są sortowane w kolumnie **sygnatury czasowej** (oznaczonej kolorem czerwonym). Następnie grupujemy według **sygnatury czasowej**. Na przykład wartości `2018-10-17T06:00:00Z` dla będą traktowane jako jeden wykres/encja (oznaczone na pomarańczowo). W tej wartości wykres/encja usługa alertu nie znajdzie żadnych kolejnych naruszeń (ponieważ każda wartość **sygnatury czasowej** ma tylko jeden wpis). Więc alert nigdy nie jest wyzwalany. W takim przypadku użytkownik musi:

- Dodaj zmienną manekina lub istniejącą zmienną (taką jak **$table),** aby poprawnie sortować za pomocą pola **Agreguj po.**
- Ponownie skonfiguruj regułę alertu, aby zamiast tego używać logiki alertu opartej na **całkowitym naruszeniu.**

## <a name="log-alert-fired-unnecessarily"></a>Alert dziennika odpalony niepotrzebnie

Skonfigurowana [reguła alertów dziennika w usłudze Azure Monitor](../platform/alerts-log.md) może zostać wyzwolona nieoczekiwanie podczas wyświetlania jej w [alertach platformy Azure.](../platform/alerts-managing-alert-states.md) W poniższych sekcjach opisano niektóre typowe przyczyny.

### <a name="alert-triggered-by-partial-data"></a>Alert wyzwalany przez dane częściowe

Usługa Log Analytics i usługa Application Insights podlegają opóźnieniom i przetwarzaniu. Po uruchomieniu kwerendy alertu dziennika może się okazać, że żadne dane nie są dostępne lub tylko niektóre dane są dostępne. Aby uzyskać więcej informacji, zobacz [Rejestrowanie czasu pozyskiwania danych w usłudze Azure Monitor](../platform/data-ingestion-time.md).

W zależności od sposobu skonfigurowania reguły alertu nieprawidłowe odsłanianie może się zdarzyć, jeśli w dziennikach nie ma żadnych danych lub częściowych danych w czasie wykonywania alertu. W takich przypadkach zalecamy zmianę zapytania lub konfiguracji alertu.

Na przykład jeśli skonfigurujesz regułę alertu dziennika, która ma być wyzwalana, gdy liczba wyników z kwerendy analitycznej jest mniejsza niż 5, alert jest wyzwalany, gdy nie ma żadnych danych (rekord zerowy) lub wyników częściowych (jeden rekord). Ale po opóźnieniu pozyskiwania danych, ta sama kwerenda z pełnymi danymi może dostarczyć wynik 10 rekordów.

### <a name="alert-query-output-is-misunderstood"></a>Dane wyjściowe kwerendy alertu są niezrozumiana

Należy podać logikę alertów dziennika w kwerendzie analitycznej. Zapytanie analityczne może używać różnych dużych zbiorów danych i funkcji matematycznych. Usługa alertów uruchamia kwerendę w odstępach czasu określonych z danymi dla określonego okresu czasu. Usługa alertów wprowadza subtelne zmiany w kwerendzie na podstawie typu alertu. Tę zmianę można wyświetlić w sekcji **Kwerenda, która ma zostać wykonana,** na ekranie **Konfigurowanie logiki sygnału:**

![Kwerenda do wykonania](media/alert-log-troubleshoot/LogAlertPreview.png)

**Kwerenda do wykonania** pole jest to, co działa usługa alertu dziennika. Jeśli chcesz zrozumieć, jakie może być dane wyjściowe kwerendy alertu przed utworzeniem alertu, możesz uruchomić podane zapytanie i czas za pośrednictwem [portalu Analytics](../log-query/portals.md) lub [interfejsu API Analytics](https://docs.microsoft.com/rest/api/loganalytics/).

## <a name="log-alert-was-disabled"></a>Alert dziennika został wyłączony

W poniższych sekcjach przedstawiono kilka powodów, dla których usługa Azure Monitor może wyłączyć [regułę alertów dziennika.](../platform/alerts-log.md)

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>Zasób, w którym utworzono alert, już nie istnieje

Reguły alertów dziennika utworzone w usłudze Azure Monitor są przeznaczone dla określonego zasobu, takiego jak obszar roboczy usługi Azure Log Analytics, aplikacja usługi Azure Application Insights i zasób platformy Azure. Usługa alertu dziennika uruchomi następnie kwerendę analityczną podana w regule dla określonego obiektu docelowego. Ale po utworzeniu reguły użytkownicy często przechodzą do usuwania z platformy Azure — lub przenieść wewnątrz platformy Azure — miejsce docelowe reguły alertu dziennika. Ponieważ obiekt docelowy reguły alertu nie jest już prawidłowy, wykonanie reguły kończy się niepowodzeniem.

W takich przypadkach usługa Azure Monitor wyłącza alert dziennika i zapewnia, że nie są rozliczane niepotrzebnie, gdy reguła nie może działać w sposób ciągły dla znacznego okresu (np. tydzień). Możesz dowiedzieć się dokładną godzinę, kiedy usługa Azure Monitor wyłączyła alert dziennika za pośrednictwem [dziennika aktywności platformy Azure.](../../azure-resource-manager/management/view-activity-logs.md) W dzienniku aktywności platformy Azure zdarzenie jest dodawane, gdy usługa Azure Monitor wyłącza regułę alertu dziennika.

Poniższe przykładowe zdarzenie w dzienniku aktywności platformy Azure dotyczy reguły alertu, która została wyłączona z powodu ciągłego błędu.

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

### <a name="query-used-in-a-log-alert-is-not-valid"></a>Kwerenda używana w alertie dziennika jest nieprawidłowa

Każda reguła alertu dziennika utworzona w usłudze Azure Monitor w ramach jego konfiguracji musi określać kwerendę analityczną, która będzie działać okresowo usługi alertów. Kwerenda analityczna może mieć poprawną składnię w momencie tworzenia lub aktualizowania reguły. Ale czasami, w okresie czasu, kwerendy zawarte w regule alertu dziennika może rozwijać problemy ze składnią i spowodować niepowodzenie wykonywania reguły. Niektóre typowe powody, dla których kwerenda analityczna podana w regule alertu dziennika może tworzyć błędy, to:

- Kwerenda jest zapisywana w celu [uruchomienia w wielu zasobach](../log-query/cross-workspace-query.md). I jeden lub więcej określonych zasobów już nie istnieje.
- [Skonfigurowany alert dziennika typów pomiarów metrycznych](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) ma kwerendę alertu niezgodną z normami składni
- Nie było przepływu danych do platformy analitycznej. [Wykonanie kwerendy daje błąd,](https://dev.loganalytics.io/documentation/Using-the-API/Errors) ponieważ nie ma żadnych danych dla podanej kwerendy.
- Zmiany w [języku kwerend](https://docs.microsoft.com/azure/kusto/query/) obejmują poprawiony format poleceń i funkcji. Dlatego kwerenda podana wcześniej w regule alertu nie jest już prawidłowa.

[Usługa Azure Advisor](../../advisor/advisor-overview.md) ostrzega o tym zachowaniu. Zalecenie jest dodawane dla określonej reguły alertu dziennika w usłudze Azure Advisor, w kategorii Wysoka dostępność o średnim wpływie i opisie "Napraw regułę alertu dziennika, aby zapewnić monitorowanie". Jeśli kwerenda alertów w regule alertu dziennika nie zostanie naprawiona po tym, jak usługa Azure Advisor dostarczyła zalecenie przez siedem dni, usługa Azure Monitor wyłączy alert dziennika i upewni się, że nie jest naliczana niepotrzebnie, gdy reguła nie może być uruchamiana w sposób ciągły przez spory okres ( jak tydzień).

Dokładny czas wyłączenia reguły alertów dziennika przez usługę Azure Monitor można znaleźć, wyszukując zdarzenie w [dzienniku aktywności platformy Azure.](../../azure-resource-manager/management/view-activity-logs.md)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [alertach dziennika na platformie Azure](../platform/alerts-unified-log.md).
- Dowiedz się więcej o [usłudze Application Insights](../../azure-monitor/app/analytics.md).
- Dowiedz się więcej o [zapytaniach dziennika](../log-query/log-query-overview.md).
