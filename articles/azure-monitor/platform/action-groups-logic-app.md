---
title: 'Porady: wyzwalanie złożone akcje przy użyciu alertów usługi Azure Monitor'
description: Dowiedz się, jak utworzyć akcji aplikacji logiki do przetworzenia alerty usługi Azure Monitor.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: e69158a6ee4d8415f52cf458c028cab56f481d8b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60235140"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Porady: wyzwalanie złożone akcje przy użyciu alertów usługi Azure Monitor

W tym artykule przedstawiono sposób konfigurowania i wyzwalacza aplikacji logiki, aby utworzyć konwersacji w Microsoft Teams, gdy zostanie wyzwolony alert.

## <a name="overview"></a>Omówienie
Po wyzwoleniu alertu danych usługi Azure Monitor, wywołuje [grupy akcji](../../azure-monitor/platform/action-groups.md). Grupy akcji umożliwiają wyzwalanie co najmniej jedną akcję, aby powiadomić inne osoby o alercie, a także jego rozwiązania.

Ogólny proces jest:

-   Tworzenie aplikacji logiki dla odpowiedniego typu alertu.

-   Importowanie schematu dla odpowiedniego typu alertu do aplikacji logiki.

-   Zdefiniuj zachowanie aplikacji logiki.

-   Skopiuj punkt końcowy HTTP w aplikacji logiki do grupy akcji platformy Azure.

Proces jest podobny, jeśli chcesz, aby aplikację logiki, aby wykonywać różne akcje.

## <a name="create-an-activity-log-alert-administrative"></a>Tworzenie alertu dziennika aktywności: Administracyjne

1.  W witrynie Azure portal wybierz **Utwórz zasób** w lewym górnym rogu.

2.  Wyszukaj i wybierz pozycję **aplikacji logiki**, a następnie wybierz **Utwórz**.

3.  Nadaj aplikacji logiki **nazwa**, wybierz **grupy zasobów**i tak dalej.

    ![Tworzenie aplikacji logiki](media/action-groups-logic-app/create-logic-app-dialog.png "tworzenie aplikacji logiki")

4.  Wybierz **Utwórz** do tworzenia aplikacji logiki. Komunikat podręczny wskazuje, że utworzono aplikację logiki. Wybierz **Uruchom zasobów** otworzyć **Projektant aplikacji logiki**.

5.  Wybierz wyzwalacz: **Po odebraniu żądania HTTP**.

    ![Wyzwalacze aplikacji logiki](media/action-groups-logic-app/logic-app-triggers.png "wyzwalacze aplikacji logiki")

6.  Wybierz **Edytuj** zmienić wyzwalacza żądania HTTP.

    ![Wyzwalaniem na żądanie HTTP](media/action-groups-logic-app/http-request-trigger-shape.png "wyzwalaniem na żądanie HTTP")

7.  Wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**.

    ![Użyj przykładowego ładunku](media/action-groups-logic-app/use-sample-payload-button.png "Użyj przykładowego ładunku")

8.  Skopiuj i wklej następujący schemat przykładowe okno dialogowe:

    ```json
        {
            "schemaId": "Microsoft.Insights/activityLogs",
            "data": {
                "status": "Activated",
                "context": {
                "activityLog": {
                    "authorization": {
                    "action": "microsoft.insights/activityLogAlerts/write",
                    "scope": "/subscriptions/…"
                    },
                    "channels": "Operation",
                    "claims": "…",
                    "caller": "logicappdemo@contoso.com",
                    "correlationId": "91ad2bac-1afa-4932-a2ce-2f8efd6765a3",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-04-03T22:33:11.762469+00:00",
                    "eventDataId": "ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0",
                    "level": "Informational",
                    "operationName": "microsoft.insights/activityLogAlerts/write",
                    "operationId": "61f59fc8-1442-4c74-9f5f-937392a9723c",
                    "resourceId": "/subscriptions/…",
                    "resourceGroupName": "LOGICAPP-DEMO",
                    "resourceProviderName": "microsoft.insights",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:33:36.1068742+00:00",
                    "resourceType": "microsoft.insights/activityLogAlerts"
                }
                },
                "properties": {}
            }
        }
    ```

9. **Projektant aplikacji logiki** wyświetli okno wyskakujące celu odnotowania, że żądania wysyłane do aplikacji logiki, należy ustawić **Content-Type** nagłówka do **application/json**. Zamknij okno podręczne. Alert usługi Azure Monitor ustawia nagłówek.

    ![Ustaw nagłówek Content-Type](media/action-groups-logic-app/content-type-header.png "Ustaw nagłówek Content-Type")

10. Wybierz **+** **nowy krok** , a następnie wybierz **Dodaj akcję**.

    ![Dodaj akcję](media/action-groups-logic-app/add-action.png "Dodaj akcję")

11. Wyszukaj i wybierz łącznik usługi Microsoft Teams. Wybierz **Microsoft Teams — Opublikuj wiadomość** akcji.

    ![Działania Microsoft Teams](media/action-groups-logic-app/microsoft-teams-actions.png "działania Microsoft Teams")

12. Skonfiguruj akcję Microsoft Teams. **Projektant aplikacji logiki** prosi o uwierzytelniania do konta usługi Office 365. Wybierz **identyfikator zespołu** i **identyfikator kanału** można wysłać wiadomości do.

13. Konfigurowanie wiadomości przy użyciu kombinacji tekst statyczny oraz odwołania do \<pola\> w zawartości dynamicznej. Skopiuj i wklej poniższy tekst do **komunikat** pola:

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Następnie wyszukaj i Zamień \<pola\> za pomocą tagów dynamicznych zawartości, o takiej samej nazwie.

    > [!NOTE]
    > Istnieją dwa pola dynamicznych, które noszą **stan**. Dodaj oba te pola do wiadomości. Użyj pola, które znajduje się w **activityLog** zbiór właściwości i Usuń inne pole. Umieszczaj kursor nad **stan** pola, aby zobaczyć odwołania do pól w pełni kwalifikowaną, jak pokazano na poniższym zrzucie ekranu:

    ![Akcja Microsoft Teams: Opublikuj wiadomość w](media/action-groups-logic-app/teams-action-post-message.png "działania Microsoft Teams: Opublikuj wiadomość")

14. W górnej części **Projektant aplikacji logiki**, wybierz opcję **Zapisz** można zapisać aplikacji logiki.

15. Otwórz istniejące grupy akcji i Dodaj akcję, aby odwoływać się do aplikacji logiki. Jeśli nie masz istniejącej grupy akcji, zobacz [tworzenie grup akcji w witrynie Azure portal i zarządzanie nimi](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) ją utworzyć. Należy pamiętać zapisać zmiany.

    ![Zaktualizuj grupę akcji](media/action-groups-logic-app/update-action-group.png "zaktualizować grupy akcji")

Przy następnym alert wywołuje Twojej grupy akcji aplikacji logiki jest wywoływana.

## <a name="create-a-service-health-alert"></a>Utwórz alert kondycji usługi

Usługa Azure Service Health wpisy są częścią dziennika aktywności. Proces tworzenia alertu jest podobne do [Tworzenie alertu dziennika aktywności](#create-an-activity-log-alert-administrative), ale za pomocą kilku zmian:

- Kroki od 1 do 7 są takie same.
- W kroku 8 Użyj następujący schemat przykładowych dla wyzwalacza żądania HTTP:

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "…",
                        "service": "…",
                        "region": "Global",
                        "communication": "…",
                        "incidentType": "Incident",
                        "trackingId": "…",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{"ImpactedRegions"}]",
                        "defaultLanguageTitle": "…",
                        "defaultLanguageContent": "…",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

-  Kroki 9 i 10, są takie same.
-  Aby uzyskać kroki od 11 do 14 należy wykonać poniższe czynności:

   1. Wybierz **+** **nowy krok** , a następnie wybierz **Dodaj warunek**. Ustaw następujące warunki, dzięki czemu aplikacja logiki wykonuje tylko wtedy, gdy dane wejściowe jest zgodna z poniższych wartości.  Wprowadzając w polu tekstowym wartość wersji, umieść ją w cudzysłowie ("0.1.1") aby upewnić się, że jest oceniane jako ciąg, a nie typu liczbowego.  System nie uwzględnia cudzysłowu, jeśli wróć do strony, ale podstawowy kod nadal utrzymuje typu string.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      !["Service Health ładunku warunek"](media/action-groups-logic-app/service-health-payload-condition.png "warunek ładunku Service Health")

   1. W **w przypadku opcji true** warunku, postępuj zgodnie z instrukcjami wyświetlanymi w kroki od 11 do 13 w [Tworzenie alertu dziennika aktywności](#create-an-activity-log-alert-administrative) Aby dodać akcję Microsoft Teams.

   1. Zdefiniuj wiadomości przy użyciu kombinacji kodu HTML i zawartości dynamicznej. Skopiuj i wklej następującą zawartość do **komunikat** pola. Zastąp `[incidentType]`, `[trackingID]`, `[title]`, i `[communication]` pola za pomocą tagów dynamicznych zawartości, o takiej samej nazwie:

       ```html
       <p>
       <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
       <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
       <strong>Title:</strong>&nbsp;[title]</p>
       <p>
       <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.</a>
       </p>
       <p>[communication]</p>
       ```

       !["Service Health warunek jest prawdziwy wpis action"](media/action-groups-logic-app/service-health-true-condition-post-action.png "akcji po warunek jest prawdziwy Service Health")

   1. Aby uzyskać **w przypadku wartości FAŁSZ** warunku, przydatne komunikat:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["Service Health fałszywe wpis action"](media/action-groups-logic-app/service-health-false-condition-post-action.png "akcji po fałszywe Service Health")

- Krok 15 jest taka sama. Postępuj zgodnie z instrukcjami, aby zapisać aplikację logiki i zaktualizować grupy akcji.

## <a name="create-a-metric-alert"></a>Tworzenie alertu metryki

Proces tworzenia alertu dotyczącego metryki jest podobny do [Tworzenie alertu dziennika aktywności](#create-an-activity-log-alert-administrative), ale za pomocą kilku zmian:

- Kroki od 1 do 7 są takie same.
- W kroku 8 Użyj następujący schemat przykładowych dla wyzwalacza żądania HTTP:

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "…",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "…",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "…",
        "portalLink": "…"
        },
        "properties": {}
    }
    }
    ```

- Kroki 9 i 10, są takie same.
- Aby uzyskać kroki od 11 do 14 należy wykonać poniższe czynności:

  1. Wybierz **+** **nowy krok** , a następnie wybierz **Dodaj warunek**. Ustaw następujące warunki, dzięki czemu aplikacja logiki wykonuje tylko wtedy, gdy dane wejściowe jest zgodna z tych wartości poniżej. Podczas wprowadzania wartość wersji w polu tekstowym, umieść ją ("2.0") w cudzysłowie do zapewniają, że wartość zostanie ocenione jako ciąg, a nie typu liczbowego.  System nie uwzględnia cudzysłowu, jeśli wróć do strony, ale podstawowy kod nadal utrzymuje typu string. 
     - `schemaId == AzureMonitorMetricAlert`
     - `version == "2.0"`
       
       !["Ładunek alertu metryki warunek"](media/action-groups-logic-app/metric-alert-payload-condition.png "warunek ładunku alertu metryki")

  2. W **w przypadku opcji true** warunku, należy dodać **dla każdego** pętli i działania Microsoft Teams. Zdefiniuj wiadomości przy użyciu kombinacji kodu HTML i zawartości dynamicznej.

      !["Akcji po metryki warunek jest prawdziwy alertu"](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "akcji po warunek alertu metryki")

  3. W **w przypadku wartości FAŁSZ** warunku, zdefiniuj akcję Microsoft Teams do komunikowania się, że alert dotyczący metryki nie jest zgodna oczekiwania aplikacji logiki. Uwzględnij ładunek JSON. Zwróć uwagę, jak utworzyć odwołanie do `triggerBody` zawartości dynamicznej w `json()` wyrażenia.

      !["Fałszywe alertu metryki wpis action"](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "akcji po fałszywe alertu metryki")

- Krok 15 jest taka sama. Postępuj zgodnie z instrukcjami, aby zapisać aplikację logiki i zaktualizować grupy akcji.

## <a name="calling-other-applications-besides-microsoft-teams"></a>Podczas wywoływania innych aplikacji, oprócz Microsoft Teams
Usługa Logic Apps zawiera szereg różnych łączników, które pozwalają do wyzwalania akcji w szerokim zakresie aplikacji i baz danych. Slack, SQL Server, Oracle, Salesforce, przedstawiono kilka sposobów. Aby uzyskać więcej informacji na temat łączników, zobacz [łączników aplikacji logiki](../../connectors/apis-list.md).  

## <a name="next-steps"></a>Kolejne kroki
* Pobierz [Przegląd alertów dziennika aktywności platformy Azure](../../azure-monitor/platform/alerts-overview.md) i Dowiedz się, jak otrzymywać alerty.  
* Dowiedz się, jak [skonfigurować alerty, gdy zostaje opublikowany powiadomienie usługi Azure Service Health](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
* Dowiedz się więcej o [grup akcji](../../azure-monitor/platform/action-groups.md).
