---
title: Rozwiązywanie problemów dotyczących alertów dzienników w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Typowe problemy, błędów i rozwiązania dla reguł alertów dzienników na platformie Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 03a6ea45577b4a4bf57501b1834f91438feb4e2b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66477861"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Rozwiązywanie problemów dotyczących alertów dzienników w usłudze Azure Monitor  

W tym artykule pokazano, jak rozwiązać typowe problemy, które może się zdarzyć, gdy konfigurujesz alertów dzienników w usłudze Azure Monitor. Zapewnia również rozwiązania typowych problemów z funkcją konfiguracji alertów dzienników. 

Termin *alerty dzienników* opisują zasady, że ognia na podstawie dziennika zapytania w [obszaru roboczego usługi Azure Log Analytics](../learn/tutorial-viewdata.md) lub [usługi Azure Application Insights](../../azure-monitor/app/analytics.md). Dowiedz się więcej o funkcji, terminologii i typy w [alerty dzienników w usłudze Azure Monitor](../platform/alerts-unified-log.md).

> [!NOTE]
> W tym artykule nie bierze pod uwagę w przypadkach, gdy witryna Azure portal zawiera regułę alertu wyzwolona i powiadomienia nie jest wykonywane przez grupę skojarzone z akcją. W takich przypadkach się ze szczegółami [tworzenie grup akcji w witrynie Azure portal i zarządzanie nimi](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>Alert dziennika nie został uruchomiony

Poniżej przedstawiono niektóre typowe przyczyny, dlaczego stan skonfigurowanego [reguł alertów dzienników w usłudze Azure Monitor](../platform/alerts-log.md) nie wyświetla [jako *wyzwolone* Oczekiwano](../platform/alerts-managing-alert-states.md). 

### <a name="data-ingestion-time-for-logs"></a>Czas wprowadzania danych dla dzienników

Alert dziennika okresowo działa na podstawie zapytania [usługi Log Analytics](../learn/tutorial-viewdata.md) lub [usługi Application Insights](../../azure-monitor/app/analytics.md). Ponieważ usługa Azure Monitor przetwarza wielu terabajtów danych od tysięcy klientów z różnych źródeł na całym świecie, usługa jest podatny na opóźnień w różnym czasie. Aby uzyskać więcej informacji, zobacz [czas wprowadzania danych w dziennikach w usłudze Azure Monitor](../platform/data-ingestion-time.md).

Aby uniknąć opóźnień, system czeka i ponawia zapytanie alertu wiele razy, jeśli stwierdzi, że potrzebne dane nie są jeszcze pozyskiwane. System ma wykładniczo zwiększa czasu oczekiwania. Alert dziennika jest wyzwalany, tylko wtedy, gdy dane są dostępne, więc może być opóźnienie, ze względu na wolne pozyskiwanie danych dziennika. 

### <a name="incorrect-time-period-configured"></a>Skonfigurowano nieprawidłowy okres.

Zgodnie z opisem w artykule na [terminologii dla dziennika alertów](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types), okres czasu, określona w konfiguracji określa zakres czasu dla zapytania. Zapytanie zwraca tylko te rekordy, które zostały utworzone w tym zakresie. 

W okresie ogranicza dane pobierane dla zapytania dziennika zapobiec nadużyciu i jego zmierzone dowolnego polecenia na czas (takich jak **temu**) używanych w zapytaniu dziennika. Na przykład jeśli okres czasu jest ustawiony na 60 minut, a zapytanie jest uruchomione o 13:15, tylko rekordy utworzone z zakresu od 12:15:00 do 13:15 czasu są używane do zapytania dotyczącego dziennika. Jeśli zapytanie dziennika korzysta z czasu polecenia podobnego **temu (1d)** , zapytanie nadal tylko używa danych między 12:15 PM i 13:15 czasu, ponieważ w okresie jest równa tego interwału.

Sprawdź, czy okres czasu w konfiguracji zgodnego z zapytaniem. W przykładzie przedstawionym wcześniej Jeśli używa zapytania dotyczącego dziennika **temu (1d)** z zielonym znacznikiem, okres czasu powinien być ustawiony na 24 godziny lub 1440 minut (oznaczone na czerwono). To ustawienie zapewni, że zapytanie działa zgodnie z oczekiwaniami.

![Okres](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Pomiń alerty ustawiono opcję

Zgodnie z opisem w kroku 8 artykułu na [tworzenia reguł alertów dzienników w witrynie Azure portal](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal), podaj alertów dzienników **Pomiń alerty** opcję Pomiń akcje wyzwalania i powiadomień w skonfigurowanym czas. W rezultacie może się wydawać, że nie wyzwolenie alertu. W rzeczywistości ona uruchomiony, ale zostało pominięte.  

![Pomiń alerty](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Reguła alertu pomiaru metryki jest nieprawidłowy

*Alerty dzienników pomiar metryki* jest podtypem alertów dzienników, które mają specjalne funkcje i składnia ograniczeniami zapytanie alertu. Reguła alertu dziennika pomiar metryki wymaga kwerendy, dane wyjściowe jako szeregów czasowych metryki. Dane wyjściowe jest tabelę zawierającą różne, równej wielkości okresów oraz odpowiadające im wartości zagregowanych. 

Istnieje możliwość mają dodatkowe zmienne w tabeli obok **elementy AggregatedValue**. Tych zmiennych może służyć do sortowania tabeli. 

Na przykład załóżmy, że regułę alertu pomiaru metryki dziennika został skonfigurowany jako:

- Zapytania `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- okresu o długości 6 godzin
- Próg wynoszący 50
- Logika alertu trzy kolejne naruszenia
- **Agregacji po** wybrana jako **$table**

Ponieważ polecenie zawiera **podsumowywania... przez** i udostępnia dwie zmienne (**sygnatura czasowa** i **$table**), system wybiera **$table** dla **agregacji po** . System sortuje tabelę wyników według **$table** pola, jak pokazano na poniższym zrzucie ekranu. Następnie odbywa się na wielu **elementy AggregatedValue** wystąpień dla każdego typu tabeli (takich jak **availabilityResults**) aby zobaczyć, czy wystąpiły co najmniej trzy kolejne naruszenia.

![Wykonanie kwerendy pomiar metryki z wieloma wartościami](media/alert-log-troubleshoot/LogMMQuery.png)

Ponieważ **agregacji po** jest zdefiniowany w **$table**, dane są sortowane na **$table** kolumny (oznaczone na czerwono). Firma Microsoft grupy i poszukaj typy **agregacji po** pola. 

Na przykład w przypadku **$table**, wartości **availabilityResults** jest traktowany jako jeden wykres na jednostkę (oznaczone kolorem pomarańczowym). W tej wartości wykresu/jednostki usługi alertów sprawdza trzy kolejne naruszenia (oznaczone na zielono). Naruszeń wyzwolić alert w przypadku wartości tabeli **availabilityResults**. 

Podobnie jeśli trzy kolejne naruszenia wystąpi dla każdej innej wartości parametru **$table**, kolejne powiadomienie alert zostanie wywołany przez ten sam efekt. Usługa alertu dotyczącego automatycznie sortowania wartości w jedną kreślenia/jednostkę (oznaczone kolorem pomarańczowym) czasu.

Teraz załóżmy, że regułę alertu pomiaru metryki dziennik został zmodyfikowany, a zapytanie dotyczyło `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`. Pozostała część konfiguracji pozostaje taka sama jak przed tym alertów logikę trzy kolejne naruszenia. **Agregacji po** opcji w tym przypadku jest **sygnatura czasowa** domyślnie. Tylko jedna wartość znajduje się w zapytaniu dla **podsumowywania... przez** (czyli **sygnatura czasowa**). Tak jak wcześniej przykładzie danych wyjściowych na końcu wykonywania będzie tak jak przedstawiono to w następujący sposób.

   ![Pomiar metryki wykonywania zapytań o pojedynczej wartości.](media/alert-log-troubleshoot/LogMMtimestamp.png)

Ponieważ **agregacji po** jest zdefiniowany w **sygnatura czasowa**, dane są sortowane na **sygnatura czasowa** kolumny (oznaczone na czerwono). Następnie możemy Grupuj według **sygnatura czasowa**. Na przykład wartości `2018-10-17T06:00:00Z` jest traktowany jako jeden wykres na jednostkę (oznaczone kolorem pomarańczowym). Ten wykres wartość na jednostkę, usługa alertu dotyczącego zawiera nie kolejne naruszenia (ponieważ każda **sygnatura czasowa** wartość ma tylko jeden wpis). Dlatego nigdy nie zostanie wywołany alert. W takim przypadku użytkownik musi:

- Dodaj fikcyjną zmienna lub zmienna (takich jak **$table**) aby poprawnie sortować przy użyciu **agregacji po** pola.
- Ponownie skonfiguruj regułę alertu do używana logika alertu na podstawie **łącznie naruszenia** zamiast tego.

## <a name="log-alert-fired-unnecessarily"></a>Alert dziennika uruchamiane niepotrzebnie

Skonfigurowany [reguł alertów dzienników w usłudze Azure Monitor](../platform/alerts-log.md) mogą być wyzwalane nieoczekiwanie, wyświetlając go w [Azure Alerts](../platform/alerts-managing-alert-states.md). W poniższych sekcjach opisano niektóre typowe przyczyny.

### <a name="alert-triggered-by-partial-data"></a>Alert wyzwolony przez częściowe dane

Usługa log Analytics i usługi Application Insights jest zależna od opóźnienia pozyskiwanie i przetwarzanie. Po uruchomieniu zapytanie alertu dzienników może się okazać, że żadne dane nie są dostępne lub tylko niektóre dane są dostępne. Aby uzyskać więcej informacji, zobacz [dziennika czas wprowadzania danych w usłudze Azure Monitor](../platform/data-ingestion-time.md).

W zależności od sposobu skonfigurowania reguły alertu ma może się zdarzyć, jeśli nie ma danych lub częściowe dane w dziennikach w czasie wykonywania alertu. W takich przypadkach zaleca się zmiany zapytanie alertu ani konfiguracji. 

Na przykład jeśli konfigurujesz reguł alertów dzienników, aby być wyzwalane, gdy liczba wyników zapytania usługi analytics jest mniejsza niż 5, ten alert jest wyzwalany, gdy nie ma danych (zero rekordu) lub częściowe wyniki (jeden rekord). Jednak po opóźnieniu pozyskiwania danych, tego samego zapytania przy użyciu pełnych danych może być wynikiem 10 rekordów.

### <a name="alert-query-output-is-misunderstood"></a>Zapytanie alertu w danych wyjściowych jest źle zrozumiane.

Możesz podać logiki dla dziennika alertów w zapytania usługi analytics. Zapytania usługi analytics można użyć różnych danych big data i funkcji matematycznych. Usługa alertu dotyczącego uruchamia zapytanie w odstępach czasu określonych danych w określonym przedziale czasu. Usługa alertu dotyczącego sprawia, że wprowadzono subtelne zmiany do zapytań, w zależności od typu alertu. Możesz wyświetlić tej zmiany w **zapytanie do wykonania** sekcji na **konfigurowanie logiki sygnału** ekranu:

![Zapytanie do wykonania](media/alert-log-troubleshoot/LogAlertPreview.png)

**Zapytanie do wykonania** pole jest działa usługa alertu dotyczącego dziennika. Jeśli chcesz zrozumieć, jakie zapytanie alertu danych wyjściowych może być przed przystąpieniem do tworzenia alertu, można uruchomić określonego zapytania i przedziału czasu za pośrednictwem [portalu analiza](../log-query/portals.md) lub [interfejsu API analizy](https://docs.microsoft.com/rest/api/loganalytics/).

## <a name="log-alert-was-disabled"></a>Alert dziennika zostało wyłączone.

W poniższych sekcjach wymieniono niektóre przyczyny, dlaczego usługa Azure Monitor może wyłączyć [reguł alertów dzienników](../platform/alerts-log.md).

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>Zasób, w którym został utworzony alert nie jest już istnieje

Zaloguj się reguły alertów utworzone w lokalizacji docelowej usługi Azure Monitor na określony zasób, takich jak obszar roboczy usługi Azure Log Analytics, aplikację usługi Azure Application Insights i zasobów platformy Azure. Usługa alertu dotyczącego dziennika wykona zapytania usługi analytics podany w zasadzie dla określonego celu. Jednak po utworzeniu reguły użytkownicy często przejdź do usunięcia z platformy Azure — lub przeniesienia wewnątrz platformy Azure — celem reguł alertów dzienników. Ponieważ element docelowy reguły alertu nie jest już prawidłowy, wykonywanie reguły kończy się niepowodzeniem.

W takich przypadkach usługi Azure Monitor wyłącza alert dziennika i gwarantuje, że nie są naliczane niepotrzebnie gdy reguła nie może działać w sposób ciągły sporej liczbie okres (na przykład tydzień). Można znaleźć dokładny czas, gdy usługa Azure Monitor wyłączony alertu dziennika za pomocą [dziennika aktywności platformy Azure](../../azure-resource-manager/resource-group-audit.md). W dzienniku aktywności platformy Azure zostanie dodane zdarzenie, gdy usługa Azure Monitor wyłącza reguł alertów dzienników.

Następujące przykładowe zdarzenie w dzienniku aktywności platformy Azure jest dla reguły alertu, który został wyłączony z powodu błędu ciągłe.

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

### <a name="query-used-in-a-log-alert-is-not-valid"></a>Zapytanie używane w alercie dziennika jest nieprawidłowe

Każdej reguły alertu dziennika utworzonego w usłudze Azure Monitor w ramach konfiguracji należy określić zapytania usługi analytics, która usługa alertu dotyczącego będzie okresowo uruchamiany. Zapytania usługi analytics może być poprawną składnię w czasie tworzenia reguły lub aktualizacji. Jednak czasami przedziale czasu kwerendy w reguł alertów dzienników mogą tworzyć problemy składni i spowodować wykonanie reguły nie powiedzie się. Niektóre typowe przyczyny, dlaczego warto tworzyć zapytania usługi analytics podawany reguł alertów dzienników błędów są:

- Zapytania są zapisywane do [działającym na wielu zasobach](../log-query/cross-workspace-query.md). A co najmniej jeden z określonych zasobów już istnieje.
- [alert dziennika typu pomiaru metryki](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) skonfigurowane ustawiono alert zapytania nie jest zgodne z normami składni
- Wystąpił brak przepływu danych do korzystania z platformy analizy. [Wykonywania zapytań powoduje błąd](https://dev.loganalytics.io/documentation/Using-the-API/Errors) , ponieważ nie ma żadnych danych dla podanego zapytania.
- Zmiany w [języka zapytań](https://docs.microsoft.com/azure/kusto/query/) obejmują poprawione format poleceń i funkcji. Dlatego podane wcześniej w regule alertu zapytanie nie jest już prawidłowy.

[Usługa Azure Advisor](../../advisor/advisor-overview.md) ostrzega o to zachowanie. Zalecenie jest dodawany do określonych reguł alertów dzienników w usłudze Azure Advisor w obszarze kategorii wysoka dostępność dzięki średni wpływ wraz z opisami "Napraw swoje reguł alertów dzienników w celu zapewnienia monitorowania." Jeśli zapytanie alertu w przypadku reguł alertów dzienników nie jest usuwane po usługi Azure Advisor ma pod warunkiem zalecenie przez siedem dni, usługi Azure Monitor będzie Wyłącz alert dziennika i upewnij się, że nie są naliczane niepotrzebnie gdy reguła nie może działać w sposób ciągły dla może być zmieniany (okresu Podobnie jak tygodnia).

Możesz znaleźć dokładny czas, gdy usługa Azure Monitor wyłączony reguł alertów dzienników, wyszukując zdarzenie w [dziennika aktywności platformy Azure](../../azure-resource-manager/resource-group-audit.md).

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [alerty dzienników w usłudze Azure](../platform/alerts-unified-log.md).
- Dowiedz się więcej o [usługi Application Insights](../../azure-monitor/app/analytics.md).
- Dowiedz się więcej o [rejestrowania zapytań](../log-query/log-query-overview.md).
