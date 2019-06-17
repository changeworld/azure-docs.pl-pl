---
title: Sprawdź stan, konfigurowanie rejestrowania i Otrzymuj alerty — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Monitorowanie stanu, rejestrowanie danych diagnostycznych i Konfigurowanie alertów w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 5c1b1e15-3b6c-49dc-98a6-bdbe7cb75339
ms.date: 07/21/2017
ms.openlocfilehash: 80776f9284752e8554486cb458096ccc9319949e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61325280"
---
# <a name="monitor-status-set-up-diagnostics-logging-and-turn-on-alerts-for-azure-logic-apps"></a>Monitorowanie stanu, skonfigurować rejestrowanie diagnostyczne i Włącz alerty w usłudze Azure Logic Apps

Po zakończeniu [tworzenia i uruchamiania aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md), można sprawdzić w historii uruchamiania, historii wyzwalania, stanu i wydajności. Do monitorowania zdarzeń w czasie rzeczywistym i bardziej zaawansowane debugowanie, konfiguracja [rejestrowania diagnostycznego](#azure-diagnostics) aplikacji logiki. W ten sposób możesz [wyszukiwanie i Podgląd zdarzeń](#find-events), takie jak wyzwalać zdarzenia, zdarzeń uruchamiania i zdarzenia akcji. Możesz również użyć tej funkcji [dane diagnostyczne z innymi usługami](#extend-diagnostic-data), takich jak Azure Storage i Azure Event Hubs. 

Aby otrzymywać powiadomienia o awarii lub innych problemów, możliwe, należy skonfigurować [alerty](#add-azure-alerts). Na przykład można utworzyć alertu, który wykryje "więcej niż pięć uruchomienia zakończą się niepowodzeniem w ciągu godziny." Możesz również skonfigurować monitorowanie, śledzenie i rejestrowanie programowo przy użyciu [ustawienia zdarzeń diagnostycznych platformy Azure i właściwości](#diagnostic-event-properties).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="view-runs-and-trigger-history-for-your-logic-app"></a>Wyświetl uruchomienia i historii wyzwalacza aplikacji logiki

1. Aby znaleźć aplikację logiki w [witryny Azure portal](https://portal.azure.com), w głównym menu platformy Azure, wybierz **wszystkich usług**. W polu wyszukiwania wpisz "logic apps", a następnie wybierz **Logic apps**.

   ![Znajdź swoją aplikację logiki](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   Witryna Azure portal pokazuje wszystkie aplikacje logiki, które są skojarzone z subskrypcją platformy Azure. 

2. Wybierz aplikację logiki, a następnie wybierz **Przegląd**.

   Witryna Azure portal pokazuje historii uruchamiania i historii wyzwalacza aplikacji logiki. Na przykład:

   ![Uruchomienia aplikacji logiki, historii wyzwalania i Historia](media/logic-apps-monitor-your-logic-apps/overview.png)

   * **Historia przebiegów** pokazuje wszystkie uruchomienia aplikacji logiki. 
   * **Historii wyzwalania** pokazuje wszystkie działania wyzwalacza aplikacji logiki.

   Opis stanu, zobacz [Rozwiązywanie problemów z aplikacją logiki](../logic-apps/logic-apps-diagnosing-failures.md).

   > [!TIP]
   > Jeśli nie widzisz danych, których oczekujesz, na pasku narzędzi wybierz **Odśwież**.

3. Aby wyświetlić kroki z określonego uruchomienia, w obszarze **Historia przebiegów**, go uruchomić. 

   Widok monitorowania przedstawia każdego kroku w danym przebiegu. Na przykład:

   ![Akcje w przypadku określonego uruchomienia](media/logic-apps-monitor-your-logic-apps/monitor-view-updated.png)

4. Aby uzyskać więcej informacji o przebiegu, wybierz opcję **szczegóły przebiegu**. Te informacje zawiera podsumowanie kroków, stan, danych wejściowych i danych wyjściowych dla przebiegu. 

   ![Wybierz opcję "Run szczegóły"](media/logic-apps-monitor-your-logic-apps/run-details.png)

   Na przykład można uzyskać przebiegu **identyfikator korelacji**, który może być konieczne, gdy używasz [interfejsu API REST dla usługi Logic Apps](https://docs.microsoft.com/rest/api/logic).

5. Aby uzyskać szczegółowe informacje dotyczące określonego etapu, wybierz ten krok. Teraz możesz przejrzeć szczegóły, takie jak dane wejściowe, dane wyjściowe i wszelkie błędy, które wystąpiły w tym kroku. Na przykład:

   ![Szczegóły kroku](media/logic-apps-monitor-your-logic-apps/monitor-view-details.png)
   
   > [!NOTE]
   > Wszystkie szczegóły środowiska uruchomieniowego i zdarzenia są szyfrowane w usłudze Logic Apps. Są one odszyfrowywane, tylko wtedy, gdy użytkownik zażąda do wyświetlania tych danych. Można także kontrolować dostęp do tych zdarzeń za pomocą [based kontroli dostępu (RBAC)](../role-based-access-control/overview.md).

6. Aby uzyskać szczegółowe informacje o zdarzeniu określonego wyzwalacza, wróć do obszaru **Przegląd** okienka. W obszarze **historii wyzwalania**, wybierz zdarzenie wyzwalacza. Teraz możesz przejrzeć szczegóły, takie jak dane wejściowe i wyjściowe, na przykład:

   ![Szczegóły danych wyjściowych zdarzenia wyzwalacza](media/logic-apps-monitor-your-logic-apps/trigger-details.png)

<a name="azure-diagnostics"></a>

## <a name="turn-on-diagnostics-logging-for-your-logic-app"></a>Włącz diagnostykę rejestrowanie dla aplikacji logiki

Dla bardziej zaawansowane debugowanie za pomocą szczegóły środowiska uruchomieniowego i zdarzeń, możesz skonfigurować rejestrowanie za pomocą diagnostyki [dzienniki usługi Azure Monitor](../log-analytics/log-analytics-overview.md). Usługa Azure Monitor to usługa platformy Azure, która monitoruje środowiska chmurowe i lokalne środowiska, aby pomóc w zachowania ich dostępności i wydajności. 

Przed rozpoczęciem wykonywania tej procedury, musisz mieć obszar roboczy usługi Log Analytics. Dowiedz się, [jak utworzyć obszar roboczy usługi Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

1. W [witryny Azure portal](https://portal.azure.com), Znajdź i wybierz swoją aplikację logiki. 

2. W bloku menu aplikacji logiki w ramach **monitorowanie**, wybierz **diagnostyki** > **ustawień diagnostycznych**.

   ![Przejdź do monitorowania, diagnostyki, ustawienia diagnostyczne](media/logic-apps-monitor-your-logic-apps/logic-app-diagnostics.png)

3. W obszarze **ustawień diagnostycznych**, wybierz **na**.

   ![Włączanie dzienników diagnostycznych](media/logic-apps-monitor-your-logic-apps/turn-on-diagnostics-logic-app.png)

4. Teraz należy wybrać kategorię obszar roboczy i zdarzeń usługi Log Analytics do rejestrowania, jak pokazano:

   1. Wybierz **wysyłanie do usługi Log Analytics**. 
   2. W obszarze **usługi Log Analytics**, wybierz **Konfiguruj**. 
   3. W obszarze **obszarów roboczych pakietu OMS**, wybierz obszar roboczy na potrzeby rejestrowania.
      > [!NOTE]
      > Obszary robocze OMS są teraz nazywane obszarami roboczymi usługi Log Analytics.
   4. W obszarze **dziennika**, wybierz opcję **środowiska wykonawczego przepływów pracy** kategorii.
   5. Interwał metryki.
   6. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.

   ![Wybierz obszar roboczy usługi Log Analytics i dane dotyczące rejestrowania](media/logic-apps-monitor-your-logic-apps/send-diagnostics-data-log-analytics-workspace.png)

Teraz można znaleźć zdarzenia oraz inne dane dla zdarzenia wyzwalacza, uruchom zdarzeń i zdarzeń akcji.

<a name="find-events"></a>

## <a name="find-events-and-data-for-your-logic-app"></a>Znajdź zdarzenia i dane aplikacji logiki

Aby znaleźć i wyświetlić zdarzenia w aplikacji logiki, takich jak wyzwalać zdarzenia, uruchom zdarzeń i akcji, wykonaj następujące kroki.

1. W [witryny Azure portal](https://portal.azure.com), wybierz **wszystkich usług**. Wyszukaj "log analytics", a następnie wybierz **usługi Log Analytics** jak pokazano poniżej:

   ![Wybierz pozycję "Usługi Log Analytics"](media/logic-apps-monitor-your-logic-apps/browseloganalytics.png)

2. W obszarze **usługi Log Analytics**, Znajdź i wybierz swój obszar roboczy usługi Log Analytics. 

   ![Wybierz swój obszar roboczy usługi Log Analytics](media/logic-apps-monitor-your-logic-apps/selectla.png)

3. W obszarze **zarządzania**, wybierz **wyszukiwanie w dzienniku**.

   ![Wybierz pozycję "Log Search"](media/logic-apps-monitor-your-logic-apps/log-search.png)

4. W polu wyszukiwania, należy określić to pole, który chcesz odnaleźć, a następnie naciśnij klawisz **Enter**. Gdy zaczniesz pisać, zobaczysz możliwych dopasowań i operacje, których można użyć. 

   Na przykład, aby znaleźć zdarzenia pierwszych 10, które wystąpiły, wprowadź i wybierz tego zapytania wyszukiwania: **wyszukiwania kategorii == "WorkflowRuntime" | limit 10**

   ![Wprowadź ciąg wyszukiwania](media/logic-apps-monitor-your-logic-apps/oms-start-query.png)

   Dowiedz się więcej o [sposób znaleźć dane w dziennikach w usłudze Azure Monitor](../log-analytics/log-analytics-log-searches.md).

5. Na stronie wyników na pasku po lewej stronie Wybierz przedział czasu, który chcesz wyświetlić.
Aby zawęzić zapytanie, dodając filtr, wybierz **+ Dodaj**.

   ![Wybierz przedział czasu dla wyników zapytania](media/logic-apps-monitor-your-logic-apps/query-results.png)

6. W obszarze **Dodaj filtry**, wprowadź nazwę filtru, aby móc odnaleźć filtru chcesz. Wybierz filtr, a następnie wybierz **+ Dodaj**.

   W tym przykładzie użyto słowa "status" znalezienia zdarzeń nie powiodło się w obszarze **AzureDiagnostics**.
   Tutaj filtr dla **status_s** jest zaznaczona.

   ![Wybierz filtr](media/logic-apps-monitor-your-logic-apps/log-search-add-filter.png)

7. Na pasku po lewej stronie wybierz wartości filtru, który chcesz użyć, a następnie wybierz **Zastosuj**.

   ![Wybierz wartości filtru, wybierz pozycję "Zastosuj"](media/logic-apps-monitor-your-logic-apps/log-search-apply-filter.png)

8. Teraz wróć do zapytania, które tworzysz. Zapytanie jest aktualizowana o wybrany filtr, a wartość. Twoje poprzednie wyniki są teraz filtrowane zbyt.

   ![Wróć do zapytania przy użyciu filtrowane wyniki](media/logic-apps-monitor-your-logic-apps/log-search-query-filtered-results.png)

9. Aby zapisać zapytanie do użycia w przyszłości, wybierz **Zapisz**. Dowiedz się, [jak zapisać zapytania](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md#save-oms-query).

<a name="extend-diagnostic-data"></a>

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Rozszerzanie, jak i, w którym można korzystać z danych diagnostycznych z innymi usługami

Wraz z dzienników usługi Azure Monitor można rozszerzyć, wykorzystania danych diagnostycznych z aplikacji logiki z innymi usługami platformy Azure, na przykład: 

* [Archiwum, które dzienniki diagnostyczne platformy Azure w usłudze Azure Storage](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Dzienniki diagnostyczne platformy Azure Stream do usługi Azure Event Hubs](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) 

Następnie monitorowanie przy użyciu danych telemetrycznych i analitycznych z innych usług, takich jak get w czasie rzeczywistym [usługi Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) i [usługi Power BI](../azure-monitor/platform/powerbi.md). Na przykład:

* [Stream dane z usługi Event Hubs do usługi Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analizuj dane przesyłane strumieniowo przy użyciu usługi Stream Analytics i utworzyć pulpit nawigacyjny analizy w czasie rzeczywistym w usłudze Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Oparte na opcje, które chcesz skonfigurować, upewnij się, że możesz pierwszy [Tworzenie konta usługi Azure storage](../storage/common/storage-create-storage-account.md) lub [Tworzenie Centrum zdarzeń Azure](../event-hubs/event-hubs-create.md). Następnie wybierz opcje dla której chcesz wysyłać dane diagnostyczne:

![Wysłać dane do Centrum konta lub zdarzenia usługi Azure storage](./media/logic-apps-monitor-your-logic-apps/storage-account-event-hubs.png)

> [!NOTE]
> Okresy przechowywania mają zastosowanie tylko wtedy, gdy użytkownik chce użyć konta magazynu.

<a name="add-azure-alerts"></a>

## <a name="set-up-alerts-for-your-logic-app"></a>Ustawianie alertów dla aplikacji logiki

Do monitorowania określonych metryk lub przekroczono progi dla aplikacji logiki, skonfiguruj [alertów na platformie Azure](../azure-monitor/platform/alerts-overview.md). Dowiedz się więcej o [metryki na platformie Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md). 

Aby skonfigurować alerty bez [dzienniki usługi Azure Monitor](../log-analytics/log-analytics-overview.md), wykonaj następujące kroki. Dla bardziej zaawansowanych kryteria alertów i działań [Konfigurowanie dzienników usługi Azure Monitor](#azure-diagnostics) zbyt.

1. W bloku menu aplikacji logiki w ramach **monitorowanie**, wybierz **diagnostyki** > **reguły alertów** > **Dodaj alert dotyczący**jak pokazano poniżej:

   ![Dodawanie alertu dla aplikacji logiki](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

2. Na **Dodawanie reguły alertów** bloku Utwórz alert, jak pokazano:

   1. W obszarze **zasobów**, wybierz swoją aplikację logiki, jeśli nie zostało jeszcze zaznaczone. 
   2. Wprowadź nazwę i opis alertu.
   3. Wybierz **metryki** lub zdarzenie, które mają być śledzone.
   4. Wybierz **warunek**, określ **próg** metryki i wybierz **okres** do monitorowania tej metryki.
   5. Wybierz, czy chcesz wysyłać wiadomości e-mail dla alertu. 
   6. Określ inne adresy e-mail do wysyłania alertu. 
   Można również określić adres URL elementu webhook, które chcesz wysyłać alert.

   Na przykład ta zasada wysyła alert, gdy jest to pięć lub więcej uruchomienia zakończą się niepowodzeniem w ciągu godziny:

   ![Utwórz regułę alertu metryki](media/logic-apps-monitor-your-logic-apps/create-alert-rule.png)

> [!TIP]
> Aby uruchomić aplikację logiki z poziomu alertu, można dołączyć [wyzwalacza żądania](../connectors/connectors-native-reqres.md) w przepływie pracy, który umożliwia wykonywanie zadań takich jak te przykłady:
> 
> * [POST do Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Wyślij wiadomość SMS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Dodawanie komunikatu do kolejki](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<a name="diagnostic-event-properties"></a>

## <a name="azure-diagnostics-event-settings-and-details"></a>Ustawienia zdarzeń usługi Azure Diagnostics i szczegóły

Każde zdarzenie diagnostyczne zawiera szczegółowe informacje o aplikacji logiki i tego zdarzenia, na przykład, stan, godzinę rozpoczęcia, czas zakończenia oraz itd. Można skonfigurować programowo monitorowania, śledzenia i rejestrowania, można użyć tych informacji z [interfejsu API REST dla usługi Azure Logic Apps](https://docs.microsoft.com/rest/api/logic) i [interfejsu API REST dla usługi Azure Diagnostics](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows).

Na przykład `ActionCompleted` zdarzenie ma `clientTrackingId` i `trackedProperties` właściwości, które służą do śledzenia i monitorowania:

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

* `clientTrackingId`: Jeśli nie zostanie podana, Azure automatycznie generuje ten identyfikator i jest skorelowane zdarzenia w przebiegu aplikacji logiki, w tym wszelkie zagnieżdżone przepływy pracy, są nazywane z aplikacji logiki. Można ręcznie określić tego Identyfikatora przy użyciu wyzwalacza, przekazując `x-ms-client-tracking-id` nagłówek o wartość niestandardową identyfikator w żądaniu wyzwalacza. Można użyć wyzwalacza żądania, wyzwalacz protokołu HTTP lub wyzwalacza elementu webhook.

* `trackedProperties`: Aby śledzić dane wejściowe lub wyjściowe w danych diagnostycznych, można dodać śledzonych do akcji w definicji JSON aplikacji logiki. Śledzone właściwości można śledzić w tylko jednej akcji dane wejściowe i wyjściowe, ale można użyć `correlation` właściwości zdarzenia do skorelowania różnych akcji do uruchomienia.

  Aby śledzić jeden lub więcej właściwości, należy dodać `trackedProperties` sekcji i żądane właściwości w definicji działania. Na przykład załóżmy, że chcesz śledzić dane, takie jak "Identyfikator zamówienia" w danych telemetrycznych:

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

* [Tworzenie szablonów dla wdrożenia aplikacji logiki i release management](../logic-apps/logic-apps-create-deploy-template.md)
* [Scenariusze B2B z pakietem integracyjnym dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Monitorowanie komunikatów B2B](../logic-apps/logic-apps-monitor-b2b-message.md)
