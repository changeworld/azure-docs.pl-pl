---
title: Wyzwalanie złożonych akcji z alertami monitorowania Azure i grup działań
description: Dowiedz się, jak utworzyć akcję aplikacji logiki do procesu Azure monitorowanie alertów.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: eafb2bcf0175190748c9dd020051cbebfcaee1fd
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263888"
---
# <a name="create-a-logic-app-action"></a>Utworzenie aplikacji logiki
## <a name="overview"></a>Przegląd ##
Gdy alert monitora Azure wyzwala, wywołuje [grupy akcji](monitoring-action-groups.md). Akcja grupy umożliwiają wyzwolenia co najmniej jednej akcji do powiadamiania osób alertu, a nawet jego rozwiązania.

W tym artykule przedstawiono sposób ustawiania i wyzwolić aplikacji logiki, aby utworzyć konwersacji w Teams firmy Microsoft po zgłoszeniu alertu.

Ogólny proces jest:

-   Tworzenie aplikacji logiki dla odpowiedniego typu alertu

-   Importowanie schematu dla odpowiedniego typu alertu do aplikacji logiki

-   Zdefiniuj zachowanie aplikacji logiki

-   Skopiuj punkt końcowy HTTP aplikacji logiki do grupy akcji Azure

Proces jest podobny, jeśli mają aplikację logiki, aby wykonywać różne akcje.

## <a name="create-an-activity-log-alert--administrative"></a>Utwórz alert dziennika aktywności — administracyjne

1.  Kliknij przycisk **Utwórz zasób** (+) znajdujący się w lewym górnym rogu witryny Azure Portal.

2.  Wyszukaj i wybierz pozycję **Aplikacja logiki**. Kliknij przycisk **Utwórz**.

3.  Nadaj nazwę aplikacji logiki, wybierz grupę zasobów itp.

    ![Utwórz logiki aplikacji w oknie dialogowym](media/monitoring-action-groups/create-logic-app-dialog.png "Utwórz logiki aplikacji w oknie dialogowym")

4.  Kliknij przycisk Utwórz, aby utworzyć aplikację logiki. Wyskakujące okienko zostanie wyświetlony po utworzeniu aplikacji logiki. Kliknij przycisk Uruchom zasobów, aby otworzyć projektanta Logic Apps.

5.  Wybierz wyzwalacz **zostanie odebrane żądanie HTTP podczas**.

    ![Wyzwalacze aplikacji logiki](media/monitoring-action-groups/logic-app-triggers.png "wyzwalacze aplikacji logiki")

6.  Wybierz **Edytuj** Aby zmienić wyzwalacz żądania HTTP

    ![Kształt wyzwalacza żądania HTTP](media/monitoring-action-groups/http-request-trigger-shape.png "kształtu wyzwalacza żądania HTTP")

7.  Wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**.

    ![Przykładowy ładunek przycisk](media/monitoring-action-groups/use-sample-payload-button.png "przykładowy przycisk ładunku")

8.  Skopiuj i wklej następujący schemat próbki w oknie dialogowym.

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

9. Projektant aplikacji logiki będzie wyświetlana Uwaga przypominający, że żądanie wysłane do aplikacji logiki musi wartość nagłówka typu zawartości application/json. Przejdź dalej i zamknąć okno dialogowe. Alert monitora Azure zostanie w tym poprawnie.

    ![Nagłówek Content-Type](media/monitoring-action-groups/content-type-header.png "nagłówka Content-Type")

10. Wybierz **+ nowy krok** , a następnie wybierz **Dodaj akcję**.

    ![Dodaj akcję](media/monitoring-action-groups/add-action.png "dodać akcję")

11. Wyszukaj i wybierz łącznik Teams firmy Microsoft. Wybierz **Teams firmy Microsoft — ogłoszenie** akcji.

    ![Akcje Teams Microsoft](media/monitoring-action-groups/microsoft-teams-actions.png "akcje Teams firmy Microsoft")

12. Skonfiguruj działanie Teams firmy Microsoft. Projektant aplikacji logiki zostanie wyświetlony monit o uwierzytelnić na koncie usługi Office 365. Wybierz **identyfikator zespołu** i **identyfikator kanału** do wysyłania wiadomości.

13. Skonfiguruj **komunikat** przy użyciu kombinacji tekst statyczny oraz odwołania do \<pola\> w zawartości dynamicznej. Wycinanie i wklejanie następujący tekst na pole wiadomość.

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Następnie wyszukaj i Zamień \<pola\> tagami zawartości dynamicznej o takiej samej nazwie.

    **[UWAGA!]**  Są dwa pola dynamicznego o nazwie **stan**. Dodaj oba pola stanu do wiadomości. W zbiorze właściwości dziennik aktywności i usunąć innych. Jeśli umieść kursor myszy nad **stan** pola zobaczysz odwołanie do pola w pełni kwalifikowana, jak pokazano na zrzucie ekranu

    ![Zespoły Akcja — ogłoszenie](media/monitoring-action-groups/teams-action-post-message.png "Akcja zespoły — ogłoszenie")

14. Zapisz aplikację logiki, klikając przycisk Zapisz w górnej części projektanta

15. Kliknij go, aby rozwinąć spowodować kształt żądania HTTP. Skopiuj adres URL protokołu HTTP POST.

    ![ADRES URL HTTP POST](media/monitoring-action-groups/http-post-url.png "ADRESU URL HTTP POST")

16. Otwórz z istniejącej grupy akcji i Dodaj akcję, aby odwołać aplikacji logiki. Jeśli nie masz istniejącej grupy akcji, zobacz <https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-action-groups> go utworzyć. Należy pamiętać zapisać zmiany.

    ![Grupy akcji aktualizacji](media/monitoring-action-groups/update-action-group.png "grupy akcji aktualizacji")

Przy następnym alert wywołuje grupie akcji nosi nazwę aplikacji logiki.

## <a name="create-a-service-health-alert"></a>Utwórz alert usługi kondycji

Wpisy usługi kondycji są częścią dziennik aktywności, więc ten proces przypomina z następującymi zmianami

1.  Kroki od 1 do 7, są takie same.
2.  Użyj następującego schematu próbki dla wyzwalacza HTTP w kroku 8.

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

3.  Kroki od 9 10 są takie same.
4.  Krok 11-Użyj funkcji z procesem opisanym niżej.
5.  Polecenie **+ nowy krok** przycisk i wybierz polecenie **Dodaj warunek**. Ustaw następujące warunki, aby upewnić się, że aplikację logiki wykonywana tylko wtedy, gdy dane wejściowe jest zgodna z tych wartości
    - schemaId == Microsoft.Insights/activityLogs
    - Element eventSource == ServiceHealth
    - Wersja == 0.1.1

    !["Usługa kondycji ładunku warunku"](media/monitoring-action-groups/service-health-payload-condition.png "usługi kondycji ładunku warunku")

6. W **w przypadku wartości PRAWDA** warunku, Dodaj akcję Teams firmy Microsoft, wykonując kroki od 11 — 13 z poprzedniego przykładu.

7. Zdefiniuj wiadomości przy użyciu kombinacji kodu HTML i [zawartość dynamiczną]. Skopiuj i Wklej zawartość poniżej na pole wiadomość. Zastąp [incidentType], [trackingID], [nazwa] i [komunikacja] pola tagami zawartości dynamicznej o takiej samej nazwie

    ```html
    <p>
    <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
    <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
    <strong>Title:</strong>&nbsp;[title]</p>
    <p>
    <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details log into the Azure Service Health dashboard</a>
    </p>
    <p>[communication]</p>
    ```

    !["Usługa kondycji warunek o wartości true post akcji"](media/monitoring-action-groups/service-health-true-condition-post-action.png "akcji po warunek o wartości true kondycji usługi")

8. Dla **w przypadku wartości FAŁSZ** warunku Podaj przydatne wiadomości

    ```html
    <p><strong>Service Health Alert</strong></p>
    <p><b>Unrecognized alert schema</b></p>
    <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details log into the Azure Service Health dashboard\</a></p>
    ```

    !["Usługa kondycji fałszywego warunku akcji po"](media/monitoring-action-groups/service-health-false-condition-post-action.png "akcji po fałszywego warunku usługi kondycji")

9.  Wykonaj kroki 15 – 16 poprzedni przykład, aby zapisać aplikację logiki i zaktualizować grupy akcji

## <a name="metric-alert"></a>Metryki alertu

1.  Kroki od 1 do 7, są takie same, co w pierwszym przykładzie
2.  Użyj następującego schematu próbki dla wyzwalacza HTTP w kroku 8.

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

3.  Kroki od 9 10 są takie same.
4.  Krok 11-Użyj funkcji z procesem opisanym niżej.
5.  Polecenie **+ nowy krok** przycisk i wybierz polecenie **Dodaj warunek**. Ustaw następujące warunki, aby upewnić się, że aplikację logiki wykonywana tylko wtedy, gdy dane wejściowe jest zgodna z tych wartości

    - schemaId == AzureMonitorMetricAlert
    - wersja == 2.0

    !["Metryki ładunku alertu warunku"](media/monitoring-action-groups/metric-alert-payload-condition.png "warunku metryki ładunku alertu")

6.  W **w przypadku wartości PRAWDA** warunku dodamy **dla każdego** kształt i akcji Teams firmy Microsoft i zdefiniuj wiadomości przy użyciu kombinacji kodu HTML i [zawartość dynamiczną]

    !["Metryki alertu warunek akcji po"](media/monitoring-action-groups/metric-alert-true-condition-post-action.png "akcji po metryki warunek o wartości true alertu")

7.  W **w przypadku wartości FAŁSZ** kształtu, zdefiniuj akcji Teams firmy Microsoft, które komunikują się, że metryka alertu nie są zgodne oczekiwań aplikacji logiki i obejmują ładunek JSON. Należy zwrócić uwagę, jak firma Microsoft odwołania zawartość dynamiczną triggerBody w wyrażeniu json().

    !["Metryki alertu fałszywego warunku akcji po"](media/monitoring-action-groups/metric-alert-false-condition-post-action.png "akcji po metryki fałszywego warunku alertu")

8.  Wykonaj kroki 15 – 16 pierwszym przykładzie, aby zapisać aplikację logiki i zaktualizować grupy akcji

## <a name="next-steps"></a>Następne kroki ##
* Pobierz [Przegląd alertów dotyczących działań w dzienniku](monitoring-overview-alerts.md)i dowiedzieć się, jak otrzymywać alerty.  
* Dowiedz się, jak [skonfigurować alerty, gdy powiadomienie usługi kondycji jest przesyłana](monitoring-activity-log-alerts-on-service-notifications.md).
* Dowiedz się więcej o [grupy akcji](monitoring-action-groups.md)