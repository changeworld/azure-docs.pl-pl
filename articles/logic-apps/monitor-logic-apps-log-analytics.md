---
title: Monitoruj aplikacje logiki przy użyciu dzienników usługi Azure Monitor
description: Rozwiązywanie problemów z aplikacjami logiki przez konfigurowanie dzienników usługi Azure Monitor i zbieranie danych diagnostycznych dla aplikacji logiki azure
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 3e41f92f9e41f7a05102e8c0e1c2edb81fa50bf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270240"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-azure-logic-apps"></a>Konfigurowanie dzienników usługi Azure Monitor i zbieranie danych diagnostycznych dla aplikacji logiki azure

Aby uzyskać bogatsze informacje debugowania aplikacji logiki w czasie wykonywania, można skonfigurować i używać [dzienników usługi Azure Monitor](../azure-monitor/platform/data-platform-logs.md) do rejestrowania i przechowywania informacji o danych i zdarzeniach środowiska wykonawczego, takich jak zdarzenia wyzwalania, uruchamianie zdarzeń i zdarzenia akcji w [obszarze roboczym usługi Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md). [Usługa Azure Monitor](../azure-monitor/overview.md) ułatwia monitorowanie środowiska chmurowego i lokalnego, dzięki czemu można łatwiej zachować ich dostępność i wydajność. Za pomocą dzienników usługi Azure Monitor można utworzyć [zapytania dziennika,](../azure-monitor/log-query/log-query-overview.md) które ułatwią zbieranie i przeglądanie tych informacji. Można również [użyć tych danych diagnostycznych z innymi usługami platformy Azure,](#extend-data)takimi jak Usługa Azure Storage i usługi Azure Event Hubs.

Aby skonfigurować rejestrowanie dla aplikacji logiki, można [włączyć usługi Log Analytics podczas tworzenia aplikacji logiki](#logging-for-new-logic-apps)lub można zainstalować rozwiązanie do [zarządzania aplikacjami logiki](#install-management-solution) w obszarze roboczym usługi Log Analytics dla istniejących aplikacji logiki. To rozwiązanie zawiera zagregowane informacje dla uruchomień aplikacji logiki i zawiera szczegółowe informacje, takie jak stan, czas wykonywania, stan ponownego składania reklamacji i identyfikatory korelacji. Następnie, aby włączyć rejestrowanie i tworzenie zapytań dotyczących tych informacji, [należy skonfigurować dzienniki usługi Azure Monitor](#set-up-resource-logs).

W tym artykule pokazano, jak włączyć usługę Log Analytics podczas tworzenia aplikacji logiki, jak zainstalować i skonfigurować rozwiązanie do zarządzania aplikacjami logiki oraz jak skonfigurować i utworzyć zapytania dla dzienników usługi Azure Monitor.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem potrzebny jest [obszar roboczy usługi Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md). Jeśli nie masz obszaru roboczego, dowiedz się, [jak utworzyć obszar roboczy usługi Log Analytics.](../azure-monitor/learn/quick-create-workspace.md)

<a name="logging-for-new-logic-apps"></a>

## <a name="enable-log-analytics-for-new-logic-apps"></a>Włącz usługę Log Analytics dla nowych aplikacji logiki

Podczas tworzenia aplikacji logiki można włączyć usługi Log Analytics.

1. W [witrynie Azure portal](https://portal.azure.com)w okienku, w którym podajesz informacje do tworzenia aplikacji logiki, wykonaj następujące kroki:

   1. W obszarze **Analiza dzienników**wybierz pozycję **Włączone**.

   1. Na liście **obszar roboczy usługi Log Analytics** wybierz obszar roboczy, w którym chcesz wysłać dane z aplikacji logiki.

      ![Podawanie informacji na temat aplikacji logiki](./media/monitor-logic-apps-log-analytics/create-logic-app-details.png)

      Po zakończeniu tego kroku platforma Azure tworzy aplikację logiki, która jest teraz skojarzona z obszarem roboczym usługi Log Analytics. Ponadto ten krok automatycznie instaluje rozwiązanie do zarządzania aplikacjami logiki w obszarze roboczym.

1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

1. Po uruchomieniu aplikacji logiki, aby wyświetlić uruchomień aplikacji logiki, [wykonaj następujące kroki](#view-logic-app-runs).

<a name="install-management-solution"></a>

## <a name="install-logic-apps-management-solution"></a>Instalowanie rozwiązania do zarządzania aplikacjami logiki

Jeśli podczas tworzenia aplikacji logiki została włączona usługa Log Analytics, pomiń ten krok. Masz już zainstalowane rozwiązanie do zarządzania aplikacjami logiki w obszarze roboczym usługi Log Analytics.

1. W polu wyszukiwania [portalu Azure](https://portal.azure.com) `log analytics workspaces`wprowadź , a następnie wybierz **obszary robocze usługi Log Analytics**.

   ![Wybierz "Obszary robocze analizy dzienników"](./media/monitor-logic-apps-log-analytics/find-select-log-analytics-workspaces.png)

1. W obszarze **Obszary robocze usługi Log Analytics**wybierz obszar roboczy.

   ![Wybieranie obszaru roboczego usługi Log Analytics](./media/monitor-logic-apps-log-analytics/select-log-analytics-workspace.png)

1. W okienku **Przegląd w** obszarze Wprowadzenie do usługi **Log Analytics** > **konfigurowanie rozwiązań monitorujących**wybierz pozycję **Wyświetl rozwiązania**.

   ![W okienku przeglądu wybierz "Wyświetl rozwiązania"](./media/monitor-logic-apps-log-analytics/log-analytics-workspace.png)

1. W obszarze **Przegląd**wybierz pozycję **Dodaj**.

   ![W okienku przeglądu dodaj nowe rozwiązanie](./media/monitor-logic-apps-log-analytics/add-logic-apps-management-solution.png)

1. Po otwarciu **portalu Marketplace** w `logic apps management`polu wyszukiwania wprowadź pozycję , i wybierz **pozycję Logic Apps Management**.

   ![W marketplace wybierz "Logic Apps Management"](./media/monitor-logic-apps-log-analytics/select-logic-apps-management.png)

1. W okienku opisu rozwiązania wybierz pozycję **Utwórz**.

   ![Wybierz "Utwórz", aby dodać rozwiązanie "Logic Apps Management"](./media/monitor-logic-apps-log-analytics/create-logic-apps-management-solution.png)

1. Przejrzyj i potwierdź obszar roboczy usługi Log Analytics, w którym chcesz zainstalować rozwiązanie, i wybierz pozycję **Utwórz** ponownie.

   ![Wybierz "Utwórz" dla "Logic Apps Management"](./media/monitor-logic-apps-log-analytics/confirm-log-analytics-workspace.png)

   Po wdrożeniu rozwiązania do grupy zasobów platformy Azure, która zawiera obszar roboczy usługi Log Analytics, rozwiązanie pojawi się w okienku podsumowania obszaru roboczego.

   ![Okienko podsumowania obszaru roboczego](./media/monitor-logic-apps-log-analytics/workspace-summary-pane-logic-apps-management.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Konfigurowanie dzienników usługi Azure Monitor

Podczas przechowywania informacji o zdarzeniach i danych środowiska uruchomieniowego w [dziennikach usługi Azure Monitor](../azure-monitor/platform/data-platform-logs.md)można utworzyć [kwerendy dziennika,](../azure-monitor/log-query/log-query-overview.md) które ułatwiają znajdowanie i przeglądanie tych informacji.

1. W [witrynie Azure portal](https://portal.azure.com)znajdź i wybierz aplikację logiki.

1. W menu aplikacji logiki w obszarze **Monitorowanie**wybierz pozycję **Ustawienia** > diagnostyczne**Dodaj ustawienie diagnostyczne**.

   ![W sekcji "Monitorowanie" wybierz "Ustawienia diagnostyczne" > "Dodaj ustawienie diagnostyczne"](./media/monitor-logic-apps-log-analytics/logic-app-diagnostics.png)

1. Aby utworzyć to ustawienie, wykonaj następujące czynności:

   1. Podaj nazwę tego ustawienia.

   1. Wybierz **pozycję Wyślij do analizy dzienników**.

   1. W przypadku **subskrypcji**wybierz subskrypcję platformy Azure skojarzoną z obszarem roboczym usługi Log Analytics.

   1. W obszarze **roboczym usługi Log Analytics**wybierz obszar roboczy, którego chcesz użyć.

   1. W **obszarze dziennik**wybierz kategorię Czas przepływu **pracy,** która określa kategorię zdarzeń, którą chcesz zarejestrować.

   1. Aby wybrać wszystkie dane, w **obszarze metryki**wybierz pozycję **Wszystkiemetry .**

   1. Po zakończeniu wybierz pozycję **Zapisz**.

   Przykład:

   ![Wybierz obszar roboczy usługi Log Analytics i dane do rejestrowania](./media/monitor-logic-apps-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-logic-app-runs"></a>

## <a name="view-logic-app-runs-status"></a>Wyświetl stan uruchomień aplikacji logiki

Po uruchomieniu aplikacji logiki można wyświetlić dane dotyczące tych uruchomień w obszarze roboczym usługi Log Analytics.

1. W [witrynie Azure portal](https://portal.azure.com)znajdź i otwórz obszar roboczy usługi Log Analytics.

1. W menu obszaru roboczego wybierz polecenie **Podsumowanie** > obszaru roboczego Zarządzanie**aplikacjami logiki**.

   ![Stan i liczba uruchamiania aplikacji logiki](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary.png)

   > [!NOTE]
   > Jeśli kafelek Zarządzanie aplikacjami logiki nie pokazuje od razu wyników po uruchomieniu, spróbuj wybrać **opcję Odśwież** lub poczekaj przez krótki czas, zanim spróbuj ponownie.

   W tym miejscu uruchomień aplikacji logiki są pogrupowane według nazwy lub stanu wykonania. Na tej stronie przedstawiono również szczegółowe informacje o niepowodzeniach w akcjach lub wyzwalaczy dla uruchomionych aplikacji logiki.

   ![Podsumowanie stanu dla aplikacji logiki jest uruchamiane](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary-details.png)

1. Aby wyświetlić wszystkie przebiegi dla określonej aplikacji logiki lub stanu, wybierz wiersz dla tej aplikacji logiki lub stanu.

   Oto przykład, który pokazuje wszystkie przebiegi dla określonej aplikacji logiki:

   ![Wyświetlanie uruchomień aplikacji logiki i stanu](./media/monitor-logic-apps-log-analytics/logic-app-run-details.png)

   W przypadku akcji, w których [skonfigurowano śledzone właściwości,](#extend-data)można również wyświetlić te właściwości, wybierając **pozycję Widok** w kolumnie **Śledzone właściwości.** Aby przeszukać śledzone właściwości, użyj filtru kolumn.

   ![Wyświetlanie śledzonych właściwości aplikacji logiki](./media/monitor-logic-apps-log-analytics/logic-app-tracked-properties.png)

   > [!NOTE]
   > Śledzone właściwości lub ukończone zdarzenia mogą wystąpić 10-15 minut opóźnienia przed pojawieniem się w obszarze roboczym usługi Log Analytics.
   > Ponadto funkcja **Ponowne przesłanie** na tej stronie jest obecnie niedostępna.

1. Aby filtrować wyniki, można wykonać filtrowanie po stronie klienta i po stronie serwera.

   * **Filtr po stronie klienta**: Dla każdej kolumny wybierz filtry, które chcesz, na przykład:

     ![Przykładowe filtry kolumn](./media/monitor-logic-apps-log-analytics/filters.png)

   * **Filtr po stronie serwera:** Aby wybrać określone okno czasowe lub ograniczyć liczbę wyświetlanych uruchomień, użyj formantu zakresu u góry strony. Domyślnie tylko 1000 rekordów pojawia się naraz.

     ![Zmienianie przedziału czasu](./media/monitor-logic-apps-log-analytics/change-interval.png)

1. Aby wyświetlić wszystkie akcje i ich szczegóły dla określonego przebiegu, wybierz wiersz dla uruchomienia aplikacji logiki.

   Oto przykład, który pokazuje wszystkie akcje i wyzwalacze dla określonego uruchomienia aplikacji logiki:

   ![Wyświetlanie akcji uruchamiania aplikacji logiki](./media/monitor-logic-apps-log-analytics/logic-app-action-details.png)

<!-------------
   * **Resubmit**: You can resubmit one or more logic apps runs that failed, succeeded, or are still running. Select the check boxes for the runs that you want to resubmit, and then select **Resubmit**.

     ![Resubmit logic app runs](./media/monitor-logic-apps-log-analytics/logic-app-resubmit.png)
--------------->

<a name="extend-data"></a>

## <a name="send-diagnostic-data-to-azure-storage-and-azure-event-hubs"></a>Wysyłanie danych diagnostycznych do usługi Azure Storage i usługi Azure Event Hubs

Wraz z dziennikami usługi Azure Monitor można rozszerzyć sposób korzystania z danych diagnostycznych aplikacji logiki z innymi usługami platformy Azure, na przykład:

* [Archiwizuj dzienniki zasobów platformy Azure na koncie magazynu](../azure-monitor/platform/resource-logs-collect-storage.md)
* [Strumieniuj dzienniki platformy Azure do usługi Azure Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

Następnie można uzyskać monitorowanie w czasie rzeczywistym przy użyciu danych telemetrycznych i analiz z innych usług, takich jak [usługa Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) i usługa Power [BI.](../azure-monitor/platform/powerbi.md) Przykład:

* [Przesyłanie strumieniowe danych z centrów zdarzeń do usługi Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analizowanie danych strumieniowych za pomocą usługi Stream Analytics i tworzenie pulpitu nawigacyjnego analizy w czasie rzeczywistym w usłudze Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Na podstawie lokalizacji, w których chcesz wysłać dane diagnostyczne, upewnij się, że najpierw [utworzysz konto magazynu platformy Azure](../storage/common/storage-create-storage-account.md) lub [utworzysz Centrum zdarzeń platformy Azure](../event-hubs/event-hubs-create.md). Następnie można wybrać miejsca docelowe, w których chcesz wysłać te dane. Okresy przechowywania mają zastosowanie tylko wtedy, gdy używasz konta magazynu.

![Wysyłanie danych do konta magazynu platformy Azure lub centrum zdarzeń](./media/monitor-logic-apps-log-analytics/diagnostics-storage-event-hub-log-analytics.png)

<a name="diagnostic-event-properties"></a>

## <a name="azure-monitor-diagnostics-events"></a>Zdarzenia diagnostyczne usługi Azure Monitor

Każde zdarzenie diagnostyczne ma szczegółowe informacje o aplikacji logiki i tego zdarzenia, na przykład stan, czas rozpoczęcia, godzina zakończenia i tak dalej. Aby programowo skonfigurować monitorowanie, śledzenie i rejestrowanie, można użyć tych informacji za pomocą [interfejsu API REST dla usługi Azure Logic Apps](https://docs.microsoft.com/rest/api/logic) i interfejsu API REST dla usługi Azure [Monitor.](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) Można również użyć `clientTrackingId` `trackedProperties` właściwości i, które pojawiają się w 

* `clientTrackingId`: Jeśli nie podano, platforma Azure automatycznie generuje ten identyfikator i koreluje zdarzenia w uruchomionym aplikacji logiki, w tym wszystkie zagnieżdżone przepływy pracy, które są wywoływane z aplikacji logiki. Ten identyfikator można ręcznie określić w wyzwalaczu, przekazując nagłówek z niestandardową wartością `x-ms-client-tracking-id` identyfikatora w żądaniu wyzwalacza. Można użyć wyzwalacza żądania, wyzwalacza HTTP lub wyzwalacza elementu webhook.

* `trackedProperties`: Aby śledzić dane wejściowe lub wyjściowe `trackedProperties` w danych diagnostycznych, można dodać sekcję do akcji za pomocą projektanta aplikacji logiki lub bezpośrednio w definicji JSON aplikacji logiki. Śledzone właściwości można śledzić tylko dane wejściowe i wyjściowe pojedynczej `correlation` akcji, ale można użyć właściwości zdarzeń do skorelowania między akcjami w przebiegu. Aby śledzić więcej niż jedną właściwość, `trackedProperties` co najmniej jedną właściwości, dodaj sekcję i właściwości, które mają zostać określone w definicji akcji.

  Oto przykład, który pokazuje, jak definicja akcji **inicjuj zmienną** zawiera śledzone właściwości z danych wejściowych akcji, gdzie dane wejściowe są tablicą, a nie rekordem.

  ``` json
  {
     "Initialize_variable": {
        "type": "InitializeVariable",
        "inputs": {
           "variables": [
              {
                 "name": "ConnectorName", 
                 "type": "String", 
                 "value": "SFTP-SSH" 
              }
           ]
        },
        "runAfter": {},
        "trackedProperties": { 
           "myTrackedPropertyName": "@action().inputs.variables[0].value"
        }
     }
  }
  ```

  W tym przykładzie pokazano wiele śledzonych właściwości:

  ``` json
  "HTTP": {
     "type": "Http",
     "inputs": {
        "body": "@triggerBody()",
        "headers": {
           "Content-Type": "application/json"
        },
        "method": "POST",
        "uri": "http://store.fabrikam.com",
     },
     "runAfter": {},
     "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
     }
  }
  ```

W tym przykładzie pokazano, `ActionCompleted` jak zdarzenie zawiera `clientTrackingId` i `trackedProperties` atrybuty:

```json
{
   "time": "2016-07-09T17:09:54.4773148Z",
   "workflowId": "/subscriptions/XXXXXXXXXXXXXXX/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp",
   "resourceId": "/subscriptions/<subscription-ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp/runs/<run-ID>/actions/Http",
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
         "workflowId": "<logic-app-workflow-ID>",
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
         "myTrackedPropertyName": "<value>"
      }
   }
}
```

## <a name="next-steps"></a>Następne kroki

* [Tworzenie zapytań monitorowania i śledzenia](../logic-apps/create-monitoring-tracking-queries.md)
* [Monitorowanie komunikatów B2B przy użyciu dzienników usługi Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)
