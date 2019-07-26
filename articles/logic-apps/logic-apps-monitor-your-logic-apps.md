---
title: Sprawdź stan, skonfiguruj rejestrowanie i Pobierz alerty — Azure Logic Apps | Microsoft Docs
description: Monitoruj stan, rejestruj dane diagnostyczne i Konfiguruj alerty dla Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 5c1b1e15-3b6c-49dc-98a6-bdbe7cb75339
ms.date: 07/21/2017
ms.openlocfilehash: 840124a35ed4389699757e011aaf5d05a9400836
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385526"
---
# <a name="monitor-status-set-up-diagnostics-logging-and-turn-on-alerts-for-azure-logic-apps"></a>Monitorowanie stanu, Konfigurowanie rejestrowania diagnostyki i włączanie alertów dla Azure Logic Apps

Po [utworzeniu i uruchomieniu aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) możesz sprawdzać jej historię uruchamiania, historię wyzwalania, stan i wydajność. Aby monitorować zdarzenia w czasie rzeczywistym i bogatsze debugowanie, skonfiguruj [Rejestrowanie diagnostyki](#azure-diagnostics) dla aplikacji logiki. W ten sposób możesz [znajdować i wyświetlać zdarzenia](#find-events), takie jak zdarzenia wyzwalacza, zdarzenia uruchamiania i zdarzenia akcji. Ponadto tych [danych diagnostycznych możesz używać z innymi usługami](#extend-diagnostic-data), takimi jak Azure Storage i Azure Event Hubs. 

Aby otrzymywać powiadomienia o błędach lub innych możliwych problemach, skonfiguruj [alerty](#add-azure-alerts). Na przykład można utworzyć alert, który wykrywa "gdy więcej niż pięć przebiegów zakończy się niepowodzeniem w ciągu godziny". Można również skonfigurować monitorowanie, śledzenie i rejestrowanie programowo przy użyciu [Diagnostyka Azure ustawień i właściwości zdarzeń](#diagnostic-event-properties).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="view-runs-and-trigger-history-for-your-logic-app"></a>Wyświetlanie historii uruchamiania i wyzwalania dla aplikacji logiki

1. Aby znaleźć aplikację logiki w [Azure Portal](https://portal.azure.com), w głównym menu platformy Azure wybierz pozycję **wszystkie usługi**. W polu wyszukiwania wpisz ciąg "Aplikacje logiki" i wybierz pozycję **Aplikacje logiki**.

   ![Znajdowanie aplikacji logiki](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   W Azure Portal są wyświetlane wszystkie aplikacje logiki skojarzone z subskrypcją platformy Azure. 

2. Wybierz aplikację logiki, a następnie wybierz pozycję **Przegląd**.

   Azure Portal pokazuje historię uruchamiania i historię wyzwalania dla aplikacji logiki. Przykład:

   ![Historia uruchamiania aplikacji logiki i historii wyzwalacza](media/logic-apps-monitor-your-logic-apps/overview.png)

   * **Historia przebiegów** pokazuje wszystkie uruchomienia aplikacji logiki. 
   * **Historia wyzwalacza** przedstawia wszystkie działania wyzwalające dla aplikacji logiki.

   Opisy stanu można znaleźć w temacie [Rozwiązywanie problemów z aplikacją logiki](../logic-apps/logic-apps-diagnosing-failures.md).

   > [!TIP]
   > Jeśli nie znajdziesz oczekiwanych danych, na pasku narzędzi wybierz pozycję **Odśwież**.

3. Aby wyświetlić kroki z określonego przebiegu, w obszarze **historia uruchamiania**wybierz opcję ten przebieg. 

   Widok monitora przedstawia każdy krok w tym przebiegu. Na przykład:

   ![Akcje dla określonego przebiegu](media/logic-apps-monitor-your-logic-apps/monitor-view-updated.png)

4. Aby uzyskać więcej informacji na temat przebiegu, wybierz pozycję **Uruchom szczegóły**. Te informacje podsumowują kroki, stan, dane wejściowe i wyjściowe dla uruchomienia. 

   ![Wybierz pozycję "Uruchom szczegóły"](media/logic-apps-monitor-your-logic-apps/run-details.png)

   Można na przykład uzyskać **Identyfikator korelacji**uruchomienia, który może być potrzebny, gdy UŻYJESZ [interfejsu API REST dla Logic Apps](https://docs.microsoft.com/rest/api/logic).

5. Aby uzyskać szczegółowe informacje na temat określonego kroku, należy wybrać ten krok. Teraz możesz przejrzeć szczegóły, takie jak dane wejściowe, wyjścia i wszelkie błędy, które wystąpiły w tym kroku. Na przykład:

   ![Szczegóły kroku](media/logic-apps-monitor-your-logic-apps/monitor-view-details.png)
   
   > [!NOTE]
   > Wszystkie szczegóły i zdarzenia środowiska uruchomieniowego są szyfrowane w ramach usługi Logic Apps. Są one odszyfrowywane tylko wtedy, gdy użytkownik zażąda wyświetlania tych danych. Możesz również kontrolować dostęp do tych zdarzeń przy użyciu [Access Control opartego na rolach (RBAC) na platformie Azure](../role-based-access-control/overview.md).

6. Aby uzyskać szczegółowe informacje o określonym zdarzeniu wyzwalacza, Wróć do okienka **Przegląd** . W obszarze **historia wyzwalacza**wybierz zdarzenie wyzwalania. Teraz możesz przejrzeć szczegóły, takie jak dane wejściowe i wyjściowe, na przykład:

   ![Szczegóły danych wyjściowych zdarzenia wyzwalacza](media/logic-apps-monitor-your-logic-apps/trigger-details.png)

<a name="azure-diagnostics"></a>

## <a name="turn-on-diagnostics-logging-for-your-logic-app"></a>Włączanie rejestrowania diagnostycznego dla aplikacji logiki

Aby uzyskać bardziej szczegółowe informacje na temat debugowania i zdarzeń środowiska uruchomieniowego, można skonfigurować rejestrowanie diagnostyki przy użyciu [dzienników Azure monitor](../log-analytics/log-analytics-overview.md). Azure Monitor to usługa platformy Azure, która monitoruje środowiska chmurowe i lokalne, aby ułatwić zachowanie ich dostępności i wydajności. 

Przed rozpoczęciem należy mieć obszar roboczy Log Analytics. Dowiedz się [, jak utworzyć obszar roboczy log Analytics](../azure-monitor/learn/quick-create-workspace.md).

1. W [Azure Portal](https://portal.azure.com)Znajdź i wybierz aplikację logiki. 

2. W menu blok aplikacji logiki w obszarze **monitorowanie**wybierz pozycję **Ustawienia diagnostyczne** **diagnostyki** > .

   ![Przejdź do pozycji monitorowanie, Diagnostyka, ustawienia diagnostyczne](media/logic-apps-monitor-your-logic-apps/logic-app-diagnostics.png)

3. W obszarze **Ustawienia diagnostyki**wybierz pozycję **włączone**.

   ![Włączanie dzienników diagnostycznych](media/logic-apps-monitor-your-logic-apps/turn-on-diagnostics-logic-app.png)

4. Teraz wybierz obszar roboczy Log Analytics i kategorię zdarzenia do rejestrowania, jak pokazano:

   1. Wybierz pozycję **Wyślij do log Analytics**. 
   2. W obszarze **log Analytics**wybierz pozycję **Konfiguruj**. 
   3. W obszarze **obszary robocze OMS**wybierz obszar roboczy, który ma być używany do rejestrowania.
      > [!NOTE]
      > Obszary robocze OMS są teraz nazywane obszarami roboczymi usługi Log Analytics.
   4. W obszarze **Dziennik**wybierz kategorię element **WorkflowRuntime** .
   5. Wybierz interwał metryki.
   6. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.

   ![Wybierz Log Analytics obszar roboczy i dane do rejestrowania](media/logic-apps-monitor-your-logic-apps/send-diagnostics-data-log-analytics-workspace.png)

Teraz można znaleźć zdarzenia i inne dane dla zdarzeń wyzwalających, zdarzeń uruchamiania i zdarzeń akcji.

<a name="find-events"></a>

## <a name="find-events-and-data-for-your-logic-app"></a>Znajdowanie zdarzeń i danych aplikacji logiki

Aby znaleźć i wyświetlić zdarzenia w aplikacji logiki, takie jak zdarzenia wyzwalania, zdarzenia uruchamiania i zdarzenia akcji, wykonaj następujące kroki.

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **wszystkie usługi**. Wyszukaj ciąg "log Analytics", a następnie wybierz **log Analytics** jak pokazano poniżej:

   ![Wybierz pozycję "Log Analytics"](media/logic-apps-monitor-your-logic-apps/browseloganalytics.png)

2. W obszarze **log Analytics**Znajdź i wybierz obszar roboczy log Analytics. 

   ![Wybierz obszar roboczy Log Analytics](media/logic-apps-monitor-your-logic-apps/selectla.png)

3. W obszarze **Zarządzanie**wybierz pozycję **przeszukiwanie dzienników**.

   ![Wybierz pozycję "wyszukiwanie w dzienniku"](media/logic-apps-monitor-your-logic-apps/log-search.png)

4. W polu wyszukiwania Określ pole, które chcesz znaleźć, a następnie naciśnij klawisz **Enter**. Po rozpoczęciu wpisywania zobaczysz możliwe dopasowania i operacje, których można użyć. 

   Na przykład, aby znaleźć 10 najważniejszych zdarzeń, które wystąpiły, wprowadź i wybierz to zapytanie wyszukiwania: **Kategoria wyszukiwania = = "WorkflowRuntime" | limit 10**

   ![Wprowadź ciąg wyszukiwania](media/logic-apps-monitor-your-logic-apps/oms-start-query.png)

   Dowiedz się więcej o [sposobach znajdowania danych w dziennikach Azure monitor](../log-analytics/log-analytics-log-searches.md).

5. Na stronie wyniki na lewym pasku Wybierz przedział czasu, który chcesz wyświetlić.
Aby uściślić zapytanie poprzez dodanie filtru, wybierz pozycję **+ Dodaj**.

   ![Wybierz przedział czasu dla wyników zapytania](media/logic-apps-monitor-your-logic-apps/query-results.png)

6. W obszarze **Dodaj filtry**wprowadź nazwę filtru, aby można było znaleźć żądany filtr. Wybierz filtr i wybierz pozycję **+ Dodaj**.

   W tym przykładzie za pomocą słowa "status" można znaleźć nieudane zdarzenia w obszarze **AzureDiagnostics**.
   Tutaj jest już zaznaczony filtr dla **status_s** .

   ![Wybierz filtr](media/logic-apps-monitor-your-logic-apps/log-search-add-filter.png)

7. Na pasku po lewej stronie wybierz wartość filtru, której chcesz użyć, a następnie wybierz pozycję **Zastosuj**.

   ![Wybierz pozycję wartość filtru, a następnie wybierz pozycję Zastosuj.](media/logic-apps-monitor-your-logic-apps/log-search-apply-filter.png)

8. Teraz wróć do kompilowanego zapytania. Zapytanie zostało zaktualizowane z wybranym filtrem i wartością. Twoje poprzednie wyniki są teraz filtrowane.

   ![Wróć do zapytania z przefiltrowanymi wynikami](media/logic-apps-monitor-your-logic-apps/log-search-query-filtered-results.png)

9. Aby zapisać zapytanie do użycia w przyszłości, wybierz pozycję **Zapisz**. Dowiedz się [, jak zapisać zapytanie](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md#save-oms-query).

<a name="extend-diagnostic-data"></a>

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Dowiesz się, jak i gdzie używać danych diagnostycznych z innymi usługami

Wraz z Azure Monitorymi dziennikami możesz dołożyć, jak używać danych diagnostycznych aplikacji logiki z innymi usługami platformy Azure, na przykład: 

* [Archiwizowanie dzienników Diagnostyka Azure w usłudze Azure Storage](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Przesyłanie strumieniowe dzienników Diagnostyka Azure do platformy Azure Event Hubs](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) 

Następnie można uzyskać monitorowanie w czasie rzeczywistym, korzystając z danych telemetrycznych i analiz z innych usług, takich jak [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) i [Power BI](../azure-monitor/platform/powerbi.md). Na przykład:

* [Przesyłanie strumieniowe danych z Event Hubs do Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analizowanie danych przesyłanych strumieniowo za pomocą Stream Analytics i tworzenie pulpitu nawigacyjnego analizy w czasie rzeczywistym w programie Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Na podstawie opcji, które chcesz skonfigurować, upewnij się, że najpierw [utworzono konto usługi Azure Storage](../storage/common/storage-create-storage-account.md) lub [utworzono centrum zdarzeń platformy Azure](../event-hubs/event-hubs-create.md). Następnie wybierz opcje, dla których chcesz wysłać dane diagnostyczne:

![Wysyłanie danych do konta usługi Azure Storage lub centrum zdarzeń](./media/logic-apps-monitor-your-logic-apps/storage-account-event-hubs.png)

> [!NOTE]
> Okresy przechowywania są stosowane tylko w przypadku wybrania opcji korzystania z konta magazynu.

<a name="add-azure-alerts"></a>

## <a name="set-up-alerts-for-your-logic-app"></a>Konfigurowanie alertów dla aplikacji logiki

Aby monitorować określone metryki lub przekroczyły progi dla aplikacji logiki, skonfiguruj [alerty na platformie Azure](../azure-monitor/platform/alerts-overview.md). Dowiedz się więcej o [metrykach na platformie Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md). 

Aby skonfigurować alerty bez [dzienników Azure monitor](../log-analytics/log-analytics-overview.md), wykonaj następujące kroki. Aby uzyskać bardziej zaawansowane kryteria i akcje alertów, [Skonfiguruj również dzienniki Azure monitor](#azure-diagnostics) .

1. W menu blok aplikacji logiki w obszarze **monitorowanie**wybierz pozycję**reguły** > alertów **diagnostycznych** > **Dodaj alert** , jak pokazano poniżej:

   ![Dodawanie alertu dla aplikacji logiki](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

2. W bloku **Dodawanie reguły alertów** Utwórz alert, jak pokazano poniżej:

   1. W obszarze **zasób**wybierz aplikację logiki, jeśli nie została jeszcze wybrana. 
   2. Nadaj nazwę i opis alertu.
   3. Wybierz **metrykę** lub zdarzenie, które chcesz śledzić.
   4. Wybierz **warunek**, określ **próg** dla metryki i wybierz **okres** monitorowania tej metryki.
   5. Wybierz, czy chcesz wysyłać pocztę dla alertu. 
   6. Określ inne adresy e-mail do wysłania alertu. 
   Możesz również określić adres URL elementu webhook, na którym chcesz wysłać Alert.

   Na przykład ta reguła wysyła alert, jeśli co najmniej pięć przebiegów zakończy się niepowodzeniem w ciągu godziny:

   ![Utwórz regułę alertu metryki](media/logic-apps-monitor-your-logic-apps/create-alert-rule.png)

> [!TIP]
> Aby uruchomić aplikację logiki z poziomu alertu, można dołączyć [wyzwalacz żądania](../connectors/connectors-native-reqres.md) w przepływie pracy, który umożliwia wykonywanie zadań takich jak następujące przykłady:
> 
> * [Opublikuj do zapasu czasu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Wyślij tekst](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Dodawanie komunikatu do kolejki](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<a name="diagnostic-event-properties"></a>

## <a name="azure-diagnostics-event-settings-and-details"></a>Diagnostyka Azure ustawienia i szczegóły zdarzenia

Każde zdarzenie diagnostyczne zawiera szczegółowe informacje o aplikacji logiki oraz o tym zdarzeniu, na przykład o stanie, czasie rozpoczęcia, czasie zakończenia itd. Aby programowo skonfigurować monitorowanie, śledzenie i rejestrowanie, można użyć tych szczegółów z [interfejsem API REST dla Azure Logic Apps](https://docs.microsoft.com/rest/api/logic) i [interfejsu api REST dla Diagnostyka Azure](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows).

Na przykład `ActionCompleted` zdarzenie `clientTrackingId` ma właściwości i `trackedProperties` , których można użyć do śledzenia i monitorowania:

``` json
{
    "time": "2016-07-09T17:09:54.4773148Z",
    "workflowId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
    "resourceId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
    "category": "WorkflowRuntime",
    "level": "Information",
    "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
    "properties": {
        "$schema": "2016-06-01",
        "startTime": "2016-07-09T17:09:53.4336305Z",
        "endTime": "2016-07-09T17:09:53.5430281Z",
        "status": "Succeeded",
        "code": "OK",
        "resource": {
            "subscriptionId": "<subscription-ID>",
            "resourceGroupName": "MyResourceGroup",
            "workflowId": "cff00d5458f944d5a766f2f9ad142553",
            "workflowName": "MyLogicApp",
            "runId": "08587361146922712057",
            "location": "westus",
            "actionName": "Http"
        },
        "correlation": {
            "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
            "clientTrackingId": "<my-custom-tracking-ID>"
        },
        "trackedProperties": {
            "myTrackedProperty": "<value>"
        }
    }
}
```

* `clientTrackingId`: Jeśli nie zostanie podany, platforma Azure automatycznie generuje ten identyfikator i skorelowanie zdarzeń w ramach przebiegu aplikacji logiki, w tym wszystkich zagnieżdżonych przepływów pracy, które są wywoływane z poziomu aplikacji logiki. Można ręcznie określić ten identyfikator z wyzwalacza, przekazując `x-ms-client-tracking-id` nagłówek z wartością niestandardowego identyfikatora w żądaniu wyzwalacza. Można użyć wyzwalacza żądania, wyzwalacza HTTP lub wyzwalacza elementu webhook.

* `trackedProperties`: Aby śledzić dane wejściowe lub wyjściowe w danych diagnostycznych, można dodać prześledzone właściwości do akcji w definicji JSON aplikacji logiki. Śledzone właściwości mogą śledzić tylko dane wejściowe i wyjściowe pojedynczej akcji, ale można użyć `correlation` właściwości zdarzeń do skorelowania między akcjami w przebiegu.

  Aby śledzić jedną lub więcej właściwości, Dodaj `trackedProperties` sekcję i właściwości, które chcesz wykonać w definicji akcji. Załóżmy na przykład, że chcesz śledzić dane takie jak "Identyfikator zamówienia" w telemetrii:

  ``` json
  "myAction": {
    "type": "http",
    "inputs": {
        "uri": "http://uri",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": "@triggerBody()"
    },
    "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
    }
  }
  ```
  Oto inny przykład, który używa akcji **Inicjuj zmienną** . Przykład dodaje śledzone właściwości z danych wejściowych akcji, gdzie dane wejściowe są tablicą, a nie rekordem.  

  ``` json
  "actions": { 
   "Initialize_variable": { 
      "inputs": { 
         "variables": [{ 
            "name": "ConnectorName", 
            "type": "String", 
            "value": "SFTP-SSH" 
         }]
      },
      "runAfter": {},
      "trackedProperties": { 
         "Track1": "@action().inputs.variables[0].value"
      },
      "type": "InitializeVariable"
   } 
  }
  ```

## <a name="next-steps"></a>Kolejne kroki

* [Automatyzowanie wdrożenia aplikacji logiki](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Scenariusze B2B z Pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Monitorowanie komunikatów B2B](../logic-apps/logic-apps-monitor-b2b-message.md)
