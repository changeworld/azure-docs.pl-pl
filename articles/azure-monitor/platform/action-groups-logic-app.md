---
title: Jak wyzwolić złożone akcje za pomocą alertów usługi Azure Monitor
description: Dowiedz się, jak utworzyć akcję aplikacji logiki do przetwarzania alertów usługi Azure Monitor.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 07/18/2018
ms.subservice: alerts
ms.openlocfilehash: 655a3acc44a1418778b37fbef85e5df75d042317
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206240"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Jak wyzwolić złożone akcje za pomocą alertów usługi Azure Monitor

W tym artykule pokazano, jak skonfigurować i wyzwolić aplikację logiki, aby utworzyć konwersację w usłudze Microsoft Teams po uruchomieniu alertu.

## <a name="overview"></a>Omówienie

Gdy wyzwala alert usługi Azure Monitor, wywołuje [grupę akcji](../../azure-monitor/platform/action-groups.md). Grupy akcji umożliwiają wyzwolenie jednej lub więcej akcji w celu powiadomienia innych osób o wpisie, a także skorygowanie go.

Ogólny proces jest:

-   Utwórz aplikację logiki dla odpowiedniego typu alertu.

-   Zaimportuj ładunek próbki dla odpowiedniego typu alertu do aplikacji logiki.

-   Zdefiniuj zachowanie aplikacji logiki.

-   Skopiuj punkt końcowy HTTP aplikacji logiki do grupy akcji platformy Azure.

Proces jest podobny, jeśli chcesz, aby aplikacja logiki wykonać inną akcję.

## <a name="create-an-activity-log-alert-administrative"></a>Tworzenie alertu dziennika aktywności: Administracyjne

1.  W witrynie Azure portal wybierz pozycję **Utwórz zasób** w lewym górnym rogu.

2.  Wyszukaj i wybierz **pozycję Aplikacja logiki**, a następnie wybierz pozycję **Utwórz**.

3.  Nadaj aplikacji logiki **nazwę**, wybierz **grupę zasobów**i tak dalej.

    ![Tworzenie aplikacji logiki](media/action-groups-logic-app/create-logic-app-dialog.png "Tworzenie aplikacji logiki")

4.  Wybierz **pozycję Utwórz,** aby utworzyć aplikację logiki. Wyskakującym okienku wskazuje, że aplikacja logiki jest tworzony. Wybierz **pozycję Uruchom zasób,** aby otworzyć **Projektanta aplikacji logiki**.

5.  Wybierz wyzwalacz: **po odebraniu żądania HTTP**.

    ![Wyzwalacze aplikacji logiki](media/action-groups-logic-app/logic-app-triggers.png "Wyzwalacze aplikacji logiki")

6.  Wybierz **edytuj,** aby zmienić wyzwalacz żądania HTTP.

    ![Wyzwalacze żądań HTTP](media/action-groups-logic-app/http-request-trigger-shape.png "Wyzwalacze żądań HTTP")

7.  Wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**.

    ![Używanie przykładowego ładunku](media/action-groups-logic-app/use-sample-payload-button.png "Używanie przykładowego ładunku")

8.  Skopiuj i wklej następujący ładunek próbki do okna dialogowego:

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

9. **Projektant aplikacji logiki** wyświetla wyskakujące okno, aby przypomnieć, że żądanie wysłane do aplikacji logiki musi ustawić nagłówek **typu zawartości** na **application/json**. Zamknij wyskakujące okno. Alert usługi Azure Monitor ustawia nagłówek.

    ![Ustawianie nagłówka Typ zawartości](media/action-groups-logic-app/content-type-header.png "Ustawianie nagłówka Typ zawartości")

10. Wybierz **+** **pozycję Nowy krok,** a następnie wybierz pozycję Dodaj **akcję**.

    ![Dodawanie akcji](media/action-groups-logic-app/add-action.png "Dodawanie akcji")

11. Wyszukaj i wybierz łącznik usługi Microsoft Teams. Wybierz akcję **Microsoft Teams — opublikuj komunikat.**

    ![Akcje usługi Microsoft Teams](media/action-groups-logic-app/microsoft-teams-actions.png "Akcje usługi Microsoft Teams")

12. Skonfiguruj akcję Usługi Microsoft Teams. **Projektant aplikacji logiki** prosi o uwierzytelnienie konta usługi Office 365. Wybierz **identyfikator zespołu** i **identyfikator kanału,** do który chcesz wysłać wiadomość.

13. Skonfiguruj wiadomość przy użyciu kombinacji tekstu statycznego i odwołań do \<pól\> w zawartości dynamicznej. Skopiuj i wklej następujący tekst do pola **Wiadomość:**

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Następnie wyszukaj \<\> i zastąp pola dynamicznymi znacznikami zawartości o tej samej nazwie.

    > [!NOTE]
    > Istnieją dwa dynamiczne pola o nazwie **status**. Dodaj oba te pola do wiadomości. Użyj pola, które znajduje się w worku właściwości **activityLog** i usuń inne pole. Umieść kursor nad polem **stanu,** aby wyświetlić w pełni kwalifikowane odwołanie do pola, jak pokazano na poniższym zrzucie ekranu:

    ![Akcja usługi Microsoft Teams: publikowanie wiadomości](media/action-groups-logic-app/teams-action-post-message.png "Akcja usługi Microsoft Teams: publikowanie wiadomości")

14. U góry **projektanta aplikacji logiki**wybierz pozycję **Zapisz,** aby zapisać aplikację logiki.

15. Otwórz istniejącą grupę akcji i dodaj akcję, aby odwołać się do aplikacji logiki. Jeśli nie masz istniejącej grupy akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi w witrynie Azure Portal,](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) aby ją utworzyć. Nie zapomnij zapisać zmian.

    ![Aktualizowanie grupy akcji](media/action-groups-logic-app/update-action-group.png "Aktualizowanie grupy akcji")

Następnym razem, gdy alert wywołuje grupę akcji, zostanie wywołana aplikacja logiki.

## <a name="create-a-service-health-alert"></a>Tworzenie alertu kondycji usługi

Wpisy usługi Azure Service Health są częścią dziennika działań. Proces tworzenia alertu jest podobny do [tworzenia alertu dziennika aktywności,](#create-an-activity-log-alert-administrative)ale z kilkoma zmianami:

- Kroki od 1 do 7 są takie same.
- W kroku 8 użyj następującego przykładowego ładunku dla wyzwalacza żądania HTTP:

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
                        "title": "...",
                        "service": "...",
                        "region": "Global",
                        "communication": "...",
                        "incidentType": "Incident",
                        "trackingId": "...",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{"ImpactedRegions"}]",
                        "defaultLanguageTitle": "...",
                        "defaultLanguageContent": "...",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "...",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

-  Kroki 9 i 10 są takie same.
-  W przypadku kroków od 11 do 14 należy wykonać następujący proces:

   1. Wybierz **+** **pozycję Nowy krok,** a następnie wybierz pozycję Dodaj **warunek**. Ustaw następujące warunki, aby aplikacja logiki jest wykonywana tylko wtedy, gdy dane wejściowe są zgodne z poniższymi wartościami.  Podczas wprowadzania wartości wersji w polu tekstowym umieść wokół niego cudzysłowy ("0.1.1"), aby upewnić się, że jest ona oceniana jako ciąg, a nie typ numeryczny.  System nie pokazuje cudzysłowów, jeśli powrócisz do strony, ale kod źródłowy nadal zachowuje typ ciągu.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      !["Stan ładowności kondycji usługi"](media/action-groups-logic-app/service-health-payload-condition.png "Warunek ładowności kondycji usługi")

   1. W warunku **if true** postępuj zgodnie z instrukcjami w krokach od 11 do 13 w [Programie Utwórz alert dziennika aktywności,](#create-an-activity-log-alert-administrative) aby dodać akcję Microsoft Teams.

   1. Zdefiniuj wiadomość przy użyciu kombinacji kodu HTML i zawartości dynamicznej. Skopiuj i wklej następującą zawartość do pola **Wiadomość.** `[incidentType]`Zastąp `[title]`pola `[communication]` , `[trackingID]`i dynamiczne znaczniki zawartości o tej samej nazwie:

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

       !["Usługa kondycji prawdziwy stan post działania"](media/action-groups-logic-app/service-health-true-condition-post-action.png "Rzeczywisty stan kondycji usługi po akcji")

   1. W przypadku warunku **Jeśli fałszywe,** podaj użyteczny komunikat:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["Usługa kondycji fałszywego stanu post akcji"](media/action-groups-logic-app/service-health-false-condition-post-action.png "Usługa Kondycja usługi fałszywa akcja postu stanu")

- Krok 15 jest taki sam. Postępuj zgodnie z instrukcjami, aby zapisać aplikację logiki i zaktualizować grupę akcji.

## <a name="create-a-metric-alert"></a>Tworzenie alertu metrycznego

Proces tworzenia alertu metryki jest podobny do [tworzenia alertu dziennika aktywności,](#create-an-activity-log-alert-administrative)ale z kilkoma zmianami:

- Kroki od 1 do 7 są takie same.
- W kroku 8 użyj następującego przykładowego ładunku dla wyzwalacza żądania HTTP:

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "...",
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
        "subscriptionId": "...",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "...",
        "portalLink": "..."
        },
        "properties": {}
    }
    }
    ```

- Kroki 9 i 10 są takie same.
- W przypadku kroków od 11 do 14 należy wykonać następujący proces:

  1. Wybierz **+** **pozycję Nowy krok,** a następnie wybierz pozycję Dodaj **warunek**. Ustaw następujące warunki, aby aplikacja logiki jest wykonywana tylko wtedy, gdy dane wejściowe są zgodne z poniższymi wartościami. Podczas wprowadzania wartości wersji w polu tekstowym, umieścić cudzysłowy wokół niego ("2.0"), aby upewnić się, że jest on oceniany jako ciąg, a nie typ numeryczny.  System nie pokazuje cudzysłowów, jeśli powrócisz do strony, ale kod źródłowy nadal zachowuje typ ciągu. 
     - `schemaId == AzureMonitorMetricAlert`
     - `version == "2.0"`
       
       !["Warunek alarmu metrycznego"](media/action-groups-logic-app/metric-alert-payload-condition.png "Warunek metryki alarmu ładowności")

  1. W warunku **if true** dodaj **dla każdej** pętli i akcji Microsoft Teams. Zdefiniuj wiadomość przy użyciu kombinacji kodu HTML i zawartości dynamicznej.

      !["Metric alert true condition post action"](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "Metryka alertu prawdziwego stanu post akcji")

  1. W **if false** warunek, zdefiniuj akcję usługi Microsoft Teams, aby przekazać, że alert metryki nie odpowiada oczekiwaniom aplikacji logiki. Uwzględnij ładunek JSON. Zwróć uwagę, `triggerBody` jak odwoływać `json()` się do zawartości dynamicznej w wyrażeniu.

      !["Metric alert false condition post action"](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "Metryka alert fałszywy warunek post akcja")

- Krok 15 jest taki sam. Postępuj zgodnie z instrukcjami, aby zapisać aplikację logiki i zaktualizować grupę akcji.

## <a name="calling-other-applications-besides-microsoft-teams"></a>Wywoływanie innych aplikacji poza usługami Microsoft Teams
Logic Apps ma wiele różnych łączników, które umożliwiają wyzwalanie akcji w szerokim zakresie aplikacji i baz danych. Slack, SQL Server, Oracle, Salesforce to tylko niektóre przykłady. Aby uzyskać więcej informacji na temat łączników, zobacz [łączniki aplikacji logiki](../../connectors/apis-list.md).  

## <a name="next-steps"></a>Następne kroki
* Zapoznaj się [z omówieniem alertów dziennika aktywności platformy Azure](../../azure-monitor/platform/alerts-overview.md) i dowiedz się, jak otrzymywać alerty.  
* Dowiedz się, jak [skonfigurować alerty podczas księgowania powiadomienia usługi Azure Service Health.](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)
* Dowiedz się więcej o [grupach akcji](../../azure-monitor/platform/action-groups.md).

