---
title: Sprawdź stan, skonfiguruj rejestrowanie i uzyskiwanie alertów - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Stan Monitora wydajności dla usługi logic apps rejestrowanie danych diagnostycznych i Konfigurowanie alertów
author: jeffhollan
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: 5c1b1e15-3b6c-49dc-98a6-bdbe7cb75339
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/21/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: f11db2009328118dda036057918ba853f5032200
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293527"
---
# <a name="monitor-status-set-up-diagnostics-logging-and-turn-on-alerts-for-azure-logic-apps"></a>Monitorowanie stanu, konfigurowanie rejestrowania diagnostyki i Włącz alerty dla usługi Azure Logic Apps

Po [tworzenie i uruchamianie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md), można sprawdzić historię uruchomień, Historia wyzwalacza, stanu i wydajności. Monitorowanie zdarzeń w czasie rzeczywistym i debugowanie bardziej rozbudowane, skonfiguruj [rejestrowania diagnostyki](#azure-diagnostics) aplikacji logiki. W ten sposób można [Znajdowanie i wyświetlać zdarzenia](#find-events), takie jak zdarzenia wyzwalacza, uruchom zdarzeń i zdarzenia akcji. Możesz także użyć tej funkcji [danych diagnostycznych z innymi usługami](#extend-diagnostic-data), takie jak usługi Azure Storage i Azure Event Hubs. 

Aby otrzymywać powiadomień o awarii lub innych możliwych problemów, skonfiguruj [alerty](#add-azure-alerts). Na przykład można utworzyć alertu wykrywa "więcej niż pięć uruchamia się niepowodzeniem w ciągu godziny." Można również skonfigurować monitorowanie, śledzenie i rejestrowanie programowo przy użyciu [ustawienia zdarzeń diagnostyki Azure i właściwości](#diagnostic-event-properties).

## <a name="view-runs-and-trigger-history-for-your-logic-app"></a>Uruchamia widok i historii wyzwalacza dla aplikacji logiki

1. Aby znaleźć aplikację logiki w [portalu Azure](https://portal.azure.com), w menu głównym Azure wybierz **wszystkie usługi**. W polu wyszukiwania wpisz "aplikacje logiki", a następnie wybierz pozycję **Logic apps**.

   ![Znajdź aplikację logiki](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   Azure portal zawiera wszystkie aplikacje logiki, które są skojarzone z subskrypcją platformy Azure. 

2. Wybierz aplikację logiki, a następnie wybierz **omówienie**.

   Portalu Azure zawiera historię uruchomień i historii wyzwalacza dla aplikacji logiki. Na przykład:

   ![Historia i wyzwalacza historii uruchomieniu aplikacji logiki](media/logic-apps-monitor-your-logic-apps/overview.png)

   * **Uruchamia historii** zawiera wszystkie elementy aplikacji logiki. 
   * **Wyzwalanie historii** pokazuje wszystkie działania wyzwalacza aplikacji logiki.

   Opis stanu, zobacz [rozwiązywania problemów z aplikacją logiki](../logic-apps/logic-apps-diagnosing-failures.md).

   > [!TIP]
   > Jeśli nie możesz znaleźć dane, które należy oczekiwać na pasku narzędzi wybierz **Odśwież**.

3. Aby wyświetlić kroki z określonego działania, w obszarze **uruchamia historii**, wybierz opcję uruchomienia. 

   Widok monitora zawiera każdego kroku w uruchomienia. Na przykład:

   ![Akcje dla określonego przebiegu](media/logic-apps-monitor-your-logic-apps/monitor-view-updated.png)

4. Aby uzyskać więcej informacji o przebiegu, wybierz **Uruchom szczegóły**. Te informacje zawiera podsumowanie kroków, stan, dane wejściowe i wyniki dla przebiegu. 

   ![Wybierz polecenie "Uruchom szczegóły"](media/logic-apps-monitor-your-logic-apps/run-details.png)

   Na przykład można uzyskać przy uruchomieniu **identyfikator korelacji**, który może być konieczne, gdy używasz [interfejsu API REST dla usługi Logic Apps](https://docs.microsoft.com/rest/api/logic).

5. Aby uzyskać szczegółowe informacje o określonym kroku, należy wybrać tego kroku. Teraz możesz przejrzeć szczegóły, takie jak wejść, wyjść i wszelkie błędy, które wystąpiły w tym kroku. Na przykład:

   ![Szczegółowe informacje krok](media/logic-apps-monitor-your-logic-apps/monitor-view-details.png)
   
   > [!NOTE]
   > Wszystkie szczegóły środowiska uruchomieniowego i zdarzenia są szyfrowane w ramach usługi Logic Apps. Są one odszyfrowane tylko wtedy, gdy użytkownik zażąda do wyświetlania tych danych. Można też kontrolować dostęp do tych zdarzeń o [based kontroli dostępu (RBAC)](../role-based-access-control/overview.md).

6. Aby uzyskać szczegółowe informacje o zdarzeniu określonego wyzwalacza, przejdź wstecz do **omówienie** okienka. W obszarze **wyzwolenia historii**, wybierz zdarzenia wyzwalacza. Teraz można przejrzeć szczegóły, takie jak wejściach i wyjściach, na przykład:

   ![Szczegóły danych wyjściowych zdarzenia wyzwalacza](media/logic-apps-monitor-your-logic-apps/trigger-details.png)

<a name="azure-diagnostics"></a>

## <a name="turn-on-diagnostics-logging-for-your-logic-app"></a>Włącz diagnostykę, rejestrowanie aplikacji logiki

Bardziej rozbudowane debugowanie z szczegóły środowiska uruchomieniowego i zdarzenia, można skonfigurować z funkcji rejestrowania diagnostyki [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Analiza dzienników jest usługą platformy Azure, który monitoruje chmurze i lokalnych środowiskach, które pomagają zachować ich dostępności i wydajności. 

Przed rozpoczęciem, musisz mieć obszaru roboczego analizy dzienników. Dowiedz się [Tworzenie obszaru roboczego analizy dzienników](../log-analytics/log-analytics-quick-create-workspace.md).

1. W [portalu Azure](https://portal.azure.com), Znajdź i wybierz aplikację logiki. 

2. W menu bloku aplikacji logiki w obszarze **monitorowanie**, wybierz **diagnostyki** > **ustawień diagnostycznych**.

   ![Przejdź do monitorowania, diagnostyki, ustawień diagnostycznych](media/logic-apps-monitor-your-logic-apps/logic-app-diagnostics.png)

3. W obszarze **ustawień diagnostycznych**, wybierz **na**.

   ![Włączanie dzienników diagnostycznych](media/logic-apps-monitor-your-logic-apps/turn-on-diagnostics-logic-app.png)

4. Teraz wybierz analizy dzienników kategorii obszaru roboczego i zdarzenia logowania, jak pokazano:

   1. Wybierz **wysyłać do analizy dzienników**. 
   2. W obszarze **analizy dzienników**, wybierz **Konfiguruj**. 
   3. W obszarze **obszarów roboczych OMS**, wybierz obszar roboczy analizy dzienników na potrzeby rejestrowania.
   4. W obszarze **dziennika**, wybierz pozycję **WorkflowRuntime** kategorii.
   5. Interwał metryki.
   6. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.

   ![Wybierz obszar roboczy analizy dzienników i danych logowania](media/logic-apps-monitor-your-logic-apps/send-diagnostics-data-log-analytics-workspace.png)

Teraz można znaleźć zdarzeń i inne dane dla zdarzenia wyzwalacza, uruchom zdarzenia i akcji.

<a name="find-events"></a>

## <a name="find-events-and-data-for-your-logic-app"></a>Znajdź zdarzenia i dane aplikacji logiki

Aby znaleźć i wyświetlić zdarzenia w aplikacji logiki, takie jak wyzwolenia zdarzenia, uruchom zdarzenia i akcji, wykonaj następujące kroki.

1. W [portalu Azure](https://portal.azure.com), wybierz **wszystkie usługi**. Wyszukaj "analizy dzienników", a następnie wybierz **analizy dzienników** w sposób pokazany poniżej:

   ![Wybierz pozycję "Analizy dzienników"](media/logic-apps-monitor-your-logic-apps/browseloganalytics.png)

2. W obszarze **analizy dzienników**, Znajdź i zaznacz pozycję obszaru roboczego analizy dzienników. 

   ![Wybierz obszar roboczy analizy dzienników](media/logic-apps-monitor-your-logic-apps/selectla.png)

3. W obszarze **zarządzania**, wybierz **wyszukiwania dziennika**.

   ![Wybierz pozycję "Dziennik wyszukiwania"](media/logic-apps-monitor-your-logic-apps/log-search.png)

4. W polu wyszukiwania określ pola, które chcesz odnaleźć, a następnie naciśnij klawisz **Enter**. Po rozpoczęciu wprowadzania, zobacz możliwe dopasowania i operacje, które są dostępne. 

   Na przykład, aby znaleźć zdarzenia pierwszych 10, które wystąpiły, wprowadź i wybierz tego zapytania wyszukiwania: **wyszukiwania kategorii == "WorkflowRuntime" | ograniczyć 10**

   ![Wprowadź ciąg wyszukiwania](media/logic-apps-monitor-your-logic-apps/oms-start-query.png)

   Dowiedz się więcej o [jak wyszukiwania danych analizy dzienników](../log-analytics/log-analytics-log-searches.md).

5. Na stronie wyników na pasku po lewej stronie Wybierz przedział czasu, który chcesz wyświetlić.
Aby zawęzić kwerendę, dodając filtr, wybierz **+ Dodaj**.

   ![Wybierz przedział czasu dla wyników zapytania](media/logic-apps-monitor-your-logic-apps/query-results.png)

6. W obszarze **Dodaj filtry**, wprowadź nazwę filtru, aby umożliwić znalezienie filtr ma. Wybierz filtr, a następnie wybierz pozycję **+ Dodaj**.

   W tym przykładzie użyto słowa "status" do znalezienia zdarzeń nie powiodło się w obszarze **AzureDiagnostics**.
   Tutaj filtr dla **status_s** jest już wybrana.

   ![Wybierz filtr](media/logic-apps-monitor-your-logic-apps/log-search-add-filter.png)

7. Na pasku po lewej stronie wybierz wartości filtru, który chcesz użyć, a następnie wybierz pozycję **Zastosuj**.

   ![Wybierz wartości filtru, wybierz polecenie "Zastosuj"](media/logic-apps-monitor-your-logic-apps/log-search-apply-filter.png)

8. Teraz wróć do zapytania, które tworzysz. Zapytanie jest aktualizowana z wybranego filtru i wartość. Poprzednie wyniki teraz są zbyt filtrowane.

   ![Wróć do filtrowane wyniki zapytania](media/logic-apps-monitor-your-logic-apps/log-search-query-filtered-results.png)

9. Aby zapisać kwerendę do użycia w przyszłości, należy wybrać **zapisać**. Dowiedz się [jak zapisać zapytanie](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md#save-oms-query).

<a name="extend-diagnostic-data"></a>

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Rozszerzanie, jak i gdzie używać danych diagnostycznych z innymi usługami

Wraz z Azure Log Analytics można rozszerzyć, jak używasz aplikacji logiki danych diagnostycznych z innymi usługami Azure, na przykład: 

* [Archiwum, które dzienniki diagnostyczne platformy Azure w magazynie Azure](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [Strumieniowe przesyłanie dzienników diagnostycznych platformy Azure do usługi Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

Następnie monitorowanie za pomocą telemetrii i analiza z innych usług, takich jak get w czasie rzeczywistym [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) i [usługi Power BI](../log-analytics/log-analytics-powerbi.md). Na przykład:

* [Strumień danych z usługi Event Hubs Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analizować dane przesyłane strumieniowo z usługi Stream Analytics i utworzyć pulpit nawigacyjny analiz w czasie rzeczywistym w usłudze Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Oparte na opcje, które chcesz skonfigurować, upewnij się, że możesz pierwszy [utworzyć konto magazynu Azure](../storage/common/storage-create-storage-account.md) lub [tworzenia Centrum zdarzeń Azure](../event-hubs/event-hubs-create.md). Następnie wybierz opcje dla których chcesz wysyłać dane diagnostyczne:

![Wysyłanie danych do magazynu Azure konta lub zdarzenia koncentratora.](./media/logic-apps-monitor-your-logic-apps/storage-account-event-hubs.png)

> [!NOTE]
> Okresy przechowywania mają zastosowanie tylko wtedy, gdy chcesz użyć konta magazynu.

<a name="add-azure-alerts"></a>

## <a name="set-up-alerts-for-your-logic-app"></a>Ustawianie alertów dla aplikacji logiki

Aby monitorować Przekroczono próg dla aplikacji logiki lub określonych metryk, skonfiguruj [alertów w usłudze Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md). Dowiedz się więcej o [metryki na platformie Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md). 

Aby skonfigurować alerty bez [Azure Log Analytics](../log-analytics/log-analytics-overview.md), wykonaj następujące kroki. Bardziej zaawansowane kryteria alertów i akcji [Konfigurowanie analizy dzienników](#azure-diagnostics) zbyt.

1. W menu bloku aplikacji logiki w obszarze **monitorowanie**, wybierz **diagnostyki** > **reguły alertów** > **Dodaj alert** w sposób pokazany poniżej:

   ![Dodawanie alertu dla aplikacji logiki](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

2. Na **dodać regułę alertu** bloku tworzenia alertu, jak pokazano:

   1. W obszarze **zasobów**, wybierz aplikację logiki, jeśli nie już wybrana. 
   2. Nadaj nazwę i opis alertu.
   3. Wybierz **Metryka** lub zdarzenie, które mają być śledzone.
   4. Wybierz **warunku**, określ **próg** dla metryki i wybierz **okres** monitorowania ta metryka.
   5. Wybierz, czy do wysyłania wiadomości e-mail dla alertu. 
   6. Określ inne adresy e-mail do wysyłania alertu. 
   Można również określić adres URL elementu webhook gdzie chcesz wysyłać alert.

   Na przykład ta reguła wysyła alert, gdy jest pięć lub więcej uruchamia się niepowodzeniem w ciągu godziny:

   ![Utwórz metryki regułę alertu](media/logic-apps-monitor-your-logic-apps/create-alert-rule.png)

> [!TIP]
> Aby uruchomić aplikację logiki z poziomu alertu, można dołączyć [wyzwalacza żądania](../connectors/connectors-native-reqres.md) w przepływie pracy, który umożliwia wykonywanie zadań takich jak te przykłady:
> 
> * [POST do Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Wyślij tekstu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Dodaj komunikat do kolejki](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<a name="diagnostic-event-properties"></a>

## <a name="azure-diagnostics-event-settings-and-details"></a>Ustawienia zdarzeń diagnostyki Azure i szczegóły

Każde zdarzenie diagnostyczne zawiera szczegółowe informacje o aplikacji logiki oraz czy zdarzenie, na przykład stan i godzina rozpoczęcia, godziny zakończenia, itp. Aby programowo skonfigurować monitorowanie, śledzenia i rejestrowania, można użyć tych informacji z [interfejsu API REST dla usługi Azure Logic Apps](https://docs.microsoft.com/rest/api/logic) i [interfejsu API REST Azure Diagnostics](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftlogicworkflows).

Na przykład `ActionCompleted` zdarzenie ma `clientTrackingId` i `trackedProperties` właściwości, które służą do monitorowania i śledzenia:

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

* `clientTrackingId`: Jeśli nie zostanie podana Azure automatycznie generuje ten identyfikator i są powiązane zdarzenia na uruchamianie aplikacji logiki, w tym wszystkie zagnieżdżone przepływy pracy wywoływanych z aplikacji logiki. Można ręcznie określić ten identyfikator z wyzwalaczy przekazując `x-ms-client-tracking-id` nagłówek o niestandardowych wartość identyfikator w żądaniu wyzwalacza. Można użyć wyzwalacza żądania, wyzwalacz protokołu HTTP lub wyzwalacza elementu webhook.

* `trackedProperties`: Aby śledzić wejściowych i wyjściowych danych diagnostycznych, można dodać właściwości śledzonych do akcji w definicji JSON aplikację logiki. Właściwości śledzonych można śledzić tylko jednej akcji wejściach i wyjściach, ale może użyć `correlation` właściwości zdarzenia do skorelowania w akcji do uruchomienia.

  Aby śledzić co najmniej jednej właściwości, należy dodać `trackedProperties` sekcji i właściwości, aby definicji działania. Na przykład załóżmy, że chcesz śledzić dane, takie jak "Identyfikator zamówienia" w obrębie telemetrii:

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

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie szablonów dla wdrożenia aplikacji logiki i zarządzania zleceniami](../logic-apps/logic-apps-create-deploy-template.md)
* [Scenariusze B2B z pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Monitorowanie komunikatów B2B](../logic-apps/logic-apps-monitor-b2b-message.md)
