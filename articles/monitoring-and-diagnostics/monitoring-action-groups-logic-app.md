---
title: Wyzwalanie złożonych akcji z alerty monitora Azure i grup akcji
description: Dowiedz się, jak utworzyć akcję aplikacji logiki do przetworzenia alerty monitora Azure.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 14e562234152d2f1f2f2d2b57b34cd5724d3dd14
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753097"
---
# <a name="create-a-logic-app-action"></a>Utworzenie aplikacji logiki

W tym artykule przedstawiono sposób ustawiania i wyzwolić aplikacji logiki, aby utworzyć konwersacji w Teams firmy Microsoft po zgłoszeniu alertu.

## <a name="overview"></a>Przegląd
Gdy alert monitora Azure wyzwala, wywołuje [grupy akcji](monitoring-action-groups.md). Akcja grupy umożliwiają wyzwolenia co najmniej jednej akcji, aby powiadomić inne osoby o alert, a także jego rozwiązania.

Ogólny proces jest:

-   Tworzenie aplikacji logiki dla odpowiedniego typu alertu.

-   Zaimportować schematu dla odpowiedniego typu alertu do aplikacji logiki.

-   Zdefiniuj zachowanie aplikacji logiki.

-   Skopiuj punkt końcowy HTTP aplikacji logiki do grupy Azure akcji.

Proces jest podobny, jeśli mają aplikację logiki, aby wykonywać różne akcje.

## <a name="create-an-activity-log-alert-administrative"></a>Utwórz alert dziennika aktywności: administracyjne

1.  W portalu Azure wybierz **Utwórz zasób** w lewym górnym rogu.

2.  Wyszukaj i wybierz **aplikacji logiki**, a następnie wybierz pozycję **Utwórz**.

3.  Nadaj aplikację logiki **nazwa**, wybierz **grupy zasobów**i tak dalej.

    ![Tworzenie aplikacji logiki](media/monitoring-action-groups/create-logic-app-dialog.png "tworzenie aplikacji logiki")

4.  Wybierz **Utwórz** do tworzenia aplikacji logiki. Komunikat podręczny wskazuje, że utworzono aplikację logiki. Wybierz **uruchamianie zasobów** otworzyć **projektanta aplikacji logiki**.

5.  Wybierz wyzwalacz: **zostanie odebrane żądanie HTTP podczas**.

    ![Wyzwalacze aplikacji logiki](media/monitoring-action-groups/logic-app-triggers.png "wyzwalacze aplikacji logiki")

6.  Wybierz **Edytuj** Aby zmienić wyzwalacz żądania HTTP.

    ![Wyzwalacze żądania HTTP](media/monitoring-action-groups/http-request-trigger-shape.png "wyzwalaczy żądania HTTP")

7.  Wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**.

    ![Użyj ładunku próbki](media/monitoring-action-groups/use-sample-payload-button.png "użyć ładunku próbki")

8.  Skopiuj i wklej następujący schemat próbki w oknie dialogowym:

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

9. **Projektanta aplikacji logiki** wyświetli okno wyskakujące w celu odnotowania, że żądanie wysłane do aplikacji logiki należy ustawić **Content-Type** nagłówka do **application/json**. Zamknij okno podręczne. Alert monitora Azure ustawia nagłówek.

    ![Ustaw nagłówek Content-Type](media/monitoring-action-groups/content-type-header.png "wartość nagłówka Content-Type")

10. Wybierz **+** **nowy krok** , a następnie wybierz **Dodaj akcję**.

    ![Dodaj akcję](media/monitoring-action-groups/add-action.png "Dodaj akcję")

11. Wyszukaj i wybierz łącznik Teams firmy Microsoft. Wybierz **Teams firmy Microsoft — ogłoszenie** akcji.

    ![Akcje Teams Microsoft](media/monitoring-action-groups/microsoft-teams-actions.png "akcje Teams firmy Microsoft")

12. Skonfiguruj działanie Teams firmy Microsoft. **Projektanta aplikacji logiki** żąda uwierzytelnienia na koncie usługi Office 365. Wybierz **identyfikator zespołu** i **identyfikator kanału** do wysyłania wiadomości.

13. Skonfiguruj wiadomości przy użyciu kombinacji tekst statyczny oraz odwołania do \<pola\> w zawartości dynamicznej. Skopiuj i wklej następujący tekst w **komunikat** pola:

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Następnie wyszukaj i Zamień \<pola\> tagami zawartości dynamicznej o takiej samej nazwie.

    > [!NOTE]
    > Istnieją dwa pola dynamiczne, które są nazywane **stan**. Dodaj obu tych pól do wiadomości. W polu **dziennik aktywności** zbioru właściwości i Usuń inne pole. Umieść kursor nad **stan** pola, aby wyświetlić odwołanie do pola w pełni kwalifikowana, jak pokazano na poniższym zrzucie ekranu:

    ![Akcja Teams firmy Microsoft: wysłać wiadomość](media/monitoring-action-groups/teams-action-post-message.png "akcji Teams firmy Microsoft: opublikowanie wiadomości")

14. W górnej części **projektanta aplikacji logiki**, wybierz pozycję **zapisać** zapisać aplikację logiki.

15. Otwórz z istniejącej grupy akcji i Dodaj akcję, aby odwołać aplikacji logiki. Jeśli nie masz istniejącej grupy akcji, zobacz [tworzenia grup działań w portalu Azure i zarządzanie nimi](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-action-groups) go utworzyć. Należy pamiętać zapisać zmiany.

    ![Aktualizowanie grupy akcji](media/monitoring-action-groups/update-action-group.png "akcji grupy aktualizacji")

Przy następnym alert wywołuje grupie akcji nosi nazwę aplikacji logiki.

## <a name="create-a-service-health-alert"></a>Utwórz alert kondycji usługi

Wpisy kondycji usługi platformy Azure są częścią dziennik aktywności. Proces tworzenia alert jest podobny do [tworzenia alertu dziennika aktywności](#create-an-activity-log-alert-administrative), ale kilka zmian:

- Kroki od 1 do 7, są takie same.
- W kroku 8 należy użyć następującego schematu próbki wyzwalacza żądania HTTP:

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

-  Krok 9 i 10 są takie same.
-  Dla kroki od 11 do 14 należy wykonać poniższe czynności:

   1. Wybierz **+** **nowy krok** , a następnie wybierz **Dodaj warunek**. Ustaw następujące warunki, aby upewnić się, że aplikacja logiki wykonuje podczas wprowadzania danych jest zgodny tylko te wartości:
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == 0.1.1`

      !["Usługa kondycji ładunku warunku"](media/monitoring-action-groups/service-health-payload-condition.png "warunku ładunku usługi kondycji")

   1. W **w przypadku wartości PRAWDA** warunku, postępuj zgodnie z instrukcjami kroki od 11 do 13 w [utworzenia alertu dziennika aktywności](#create-an-activity-log-alert-administrative) Aby dodać akcję Teams firmy Microsoft.

   1. Zdefiniuj wiadomości przy użyciu kombinacji kodu HTML i zawartość dynamiczną. Skopiuj i wklej następującą zawartość do **komunikat** pola. Zastąp `[incidentType]`, `[trackingID]`, `[title]`, i `[communication]` pola tagami zawartości dynamicznej o tej samej nazwie:

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

       !["Usługa kondycji warunek akcji po"](media/monitoring-action-groups/service-health-true-condition-post-action.png "akcji po warunek o wartości true usługi kondycji")

   1. Aby uzyskać **w przypadku wartości FAŁSZ** warunku, przydatne komunikat:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["Usługa kondycji fałszywego warunku akcji po"](media/monitoring-action-groups/service-health-false-condition-post-action.png "akcji po fałszywego warunku usługi kondycji")

- Krok 15 jest taka sama. Postępuj zgodnie z instrukcjami, aby zapisać aplikację logiki i zaktualizować grupy działań.

## <a name="create-a-metric-alert"></a>Utwórz alert metryki

Proces tworzenia metryki alert jest podobny do [tworzenia alertu dziennika aktywności](#create-an-activity-log-alert-administrative), ale drobne zmiany:

- Kroki od 1 do 7, są takie same.
- W kroku 8 należy użyć następującego schematu próbki wyzwalacza żądania HTTP:

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

- Krok 9 i 10 są takie same.
- Dla kroki od 11 do 14 należy wykonać poniższe czynności:

   1. Wybierz **+** **nowy krok** , a następnie wybierz **Dodaj warunek**. Ustaw następujące warunki, aby upewnić się, że aplikacja logiki wykonuje podczas wprowadzania danych jest zgodny tylko te wartości:
       - `schemaId == AzureMonitorMetricAlert`
       - `version == 2.0`

       !["Metryki ładunku alertu warunku"](media/monitoring-action-groups/metric-alert-payload-condition.png "warunku metryki ładunku alertu")

   1. W **w przypadku wartości PRAWDA** warunku, Dodaj **dla każdego** pętli i akcji Teams firmy Microsoft. Zdefiniuj wiadomości przy użyciu kombinacji kodu HTML i zawartość dynamiczną.

       !["Metryki alertu warunek akcji po"](media/monitoring-action-groups/metric-alert-true-condition-post-action.png "akcji po metryki warunek o wartości true alertu")

   1. W **w przypadku wartości FAŁSZ** warunku, definiowania akcji Teams firmy Microsoft do komunikowania się, że metryki alert nie odpowiada oczekiwania aplikacji logiki. Zawiera ładunek JSON. Zwróć uwagę, jak odwoływać się do `triggerBody` zawartości dynamicznej `json()` wyrażenia.

       !["Metryki alertu fałszywego warunku akcji po"](media/monitoring-action-groups/metric-alert-false-condition-post-action.png "akcji po metryki fałszywego warunku alertu")

- Krok 15 jest taka sama. Postępuj zgodnie z instrukcjami, aby zapisać aplikację logiki i zaktualizować grupy działań.

## <a name="next-steps"></a>Kolejne kroki
* Pobierz [Przegląd alertów dziennik aktywności platformy Azure](monitoring-overview-alerts.md) i Dowiedz się, jak otrzymywać alerty.  
* Dowiedz się, jak [skonfigurować alerty, gdy kondycja usługi Azure notification jest przesyłana](monitoring-activity-log-alerts-on-service-notifications.md).
* Dowiedz się więcej o [grupy akcji](monitoring-action-groups.md).