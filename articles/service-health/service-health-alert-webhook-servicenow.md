---
title: Konfigurowanie alertów dotyczących kondycji usługi platformy Azure przy użyciu usługi ServiceNow | Dokumentacja firmy Microsoft
description: Uzyskaj Spersonalizowane powiadomienia dotyczące zdarzenia usługi service health do swojego wystąpienia usługi ServiceNow.
author: stephbaron
ms.author: stbaron
ms.topic: article
ms.service: service-health
ms.date: 11/14/2017
ms.openlocfilehash: f17215a5695128bf2ea507efa0c12fdbba9467d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620944"
---
# <a name="configure-service-health-alerts-with-servicenow"></a>Konfigurowanie alertów dotyczących kondycji usługi przy użyciu usługi ServiceNow

W tym artykule pokazano, jak integrować alerty dotyczące kondycji usługi platformy Azure przy użyciu usługi ServiceNow przy użyciu elementu webhook. Po skonfigurowaniu integrację elementów webhook z wystąpieniem usługi ServiceNow, możesz otrzymywać alerty za pomocą istniejącej infrastruktury powiadomień, gdy napotkasz problemy z usług platformy Azure. Za każdym razem, gdy zostanie wyzwolony alert danych usługi Azure Service Health, wywołuje element webhook przy użyciu interfejsu API REST dla usługi ServiceNow inicjowanych przez skrypty.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>Tworzenie inicjowanych przez skrypty interfejsu API REST w usługi ServiceNow
1.  Upewnij się, podpisali i zalogowano się do Twojej [ServiceNow](https://www.servicenow.com/) konta.

1.  Przejdź do **usług sieci Web systemu** sekcji usługi ServiceNow, a następnie wybierz pozycję **inicjowanych przez skrypty interfejsów API REST**.

    ![W sekcji "inicjowanych przez skrypty Usługa sieci Web" w usługi ServiceNow](./media/webhook-alerts/servicenow-sws-section.png)

1.  Wybierz **New** do utworzenia nowej usługi REST inicjowanych przez skrypty.
 
    ![Przycisk "Nowy inicjowanych przez skrypty interfejsu API REST" w usługi ServiceNow](./media/webhook-alerts/servicenow-new-button.png)

1.  Dodaj **nazwa** do interfejsu API REST i zestawu **identyfikator interfejsu API** do `azureservicehealth`.

1.  Wybierz pozycję **Prześlij**.

    !["Ustawienia interfejsu API REST" w usługi ServiceNow](./media/webhook-alerts/servicenow-restapi-settings.png)

1.  Wybierz interfejs API REST, które zostały utworzone, a następnie w obszarze **zasobów** wybierz opcję **New**.

    !["Zasób karcie" usługi ServiceNow](./media/webhook-alerts/servicenow-resources-tab.png)

1.  **Nazwa** nowy zasób `event` i zmień **metody HTTP** do `POST`.

1.  W **skryptu** sekcji, Dodaj następujący kod JavaScript:

    >[!NOTE]
    >Należy zaktualizować `<secret>`,`<group>`, i `<email>` wartości w poniższym skrypcie.
    >* `<secret>` powinien być losowy ciąg, takich jak identyfikator GUID
    >* `<group>` powinna być grupa usługi ServiceNow, którą chcesz przypisać zdarzenia do
    >* `<email>` powinien być określonej osoby, którą chcesz przypisać zdarzenia do (opcjonalnie)
    >

    ```javascript
    (function process( /*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {
        var apiKey = request.queryParams['apiKey'];
        var secret = '<secret>';
        if (apiKey == secret) {
            var event = request.body.data;
            var responseBody = {};
            if (event.data.context.activityLog.operationName == 'Microsoft.ServiceHealth/incident/action') {
                var inc = new GlideRecord('incident');
                var incidentExists = false;
                inc.addQuery('number', event.data.context.activityLog.properties.trackingId);
                inc.query();
                if (inc.hasNext()) {
                    incidentExists = true;
                    inc.next();
                } else {
                    inc.initialize();
                }
                var short_description = "Azure Service Health";
                if (event.data.context.activityLog.properties.incidentType == "Incident") {
                    short_description += " - Service Issue - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Maintenance") {
                    short_description += " - Planned Maintenance - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Informational" || event.data.context.activityLog.properties.incidentType == "ActionRequired") {
                    short_description += " - Health Advisory - ";
                }
                short_description += event.data.context.activityLog.properties.title;
                inc.short_description = short_description;
                inc.description = event.data.context.activityLog.properties.communication;
                inc.work_notes = "Impacted subscription: " + event.data.context.activityLog.subscriptionId;
                if (incidentExists) {
                    if (event.data.context.activityLog.properties.stage == 'Active') {
                        inc.state = 2;
                    } else if (event.data.context.activityLog.properties.stage == 'Resolved') {
                        inc.state = 6;
                    } else if (event.data.context.activityLog.properties.stage == 'Closed') {
                        inc.state = 7;
                    }
                    inc.update();
                    responseBody.message = "Incident updated.";
                } else {
                    inc.number = event.data.context.activityLog.properties.trackingId;
                    inc.state = 1;
                    inc.impact = 2;
                    inc.urgency = 2;
                    inc.priority = 2;
                    inc.assigned_to = '<email>';
                    inc.assignment_group.setDisplayValue('<group>');
                    var subscriptionId = event.data.context.activityLog.subscriptionId;
                    var comments = "Azure portal Link: https://app.azure.com/h";
                    comments += "/" + event.data.context.activityLog.properties.trackingId;
                    comments += "/" + subscriptionId.substring(0, 3) + subscriptionId.slice(-3);
                    var impactedServices = JSON.parse(event.data.context.activityLog.properties.impactedServices);
                    var impactedServicesFormatted = "";
                    for (var i = 0; i < impactedServices.length; i++) {
                        impactedServicesFormatted += impactedServices[i].ServiceName + ": ";
                        for (var j = 0; j < impactedServices[i].ImpactedRegions.length; j++) {
                            if (j != 0) {
                                impactedServicesFormatted += ", ";
                            }
                            impactedServicesFormatted += impactedServices[i].ImpactedRegions[j].RegionName;
                        }

                        impactedServicesFormatted += "\n";

                    }
                    comments += "\n\nImpacted Services:\n" + impactedServicesFormatted;
                    inc.comments = comments;
                    inc.insert();
                    responseBody.message = "Incident created.";
                }
            } else {
                responseBody.message = "Hello from the other side!";
            }
            response.setBody(responseBody);
        } else {
            var unauthorized = new sn_ws_err.ServiceError();
            unauthorized.setStatus(401);
            unauthorized.setMessage('Invalid apiKey');
            response.setError(unauthorized);
        }
    })(request, response);
    ```

1.  Na karcie Zabezpieczenia, usuń zaznaczenie pola wyboru **wymaga uwierzytelnienia** i wybierz **przesyłania**. `<secret>` Możesz zamiast tego zestaw chroni tego interfejsu API.

    ![Pole wyboru "Wymaga uwierzytelniania" w usługi ServiceNow](./media/webhook-alerts/servicenow-resource-settings.png)

1.  W sekcji pisanie skryptów interfejsów API REST, należy odnaleźć **podstawowy interfejs API ścieżki** nowego interfejsu API REST:

     !["Podstawowego interfejsu API ścieżki" w usługi ServiceNow](./media/webhook-alerts/servicenow-base-api-path.png)

1.  Pełna integracja adres URL wygląda następująco:
        
         https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>


## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Utwórz alert przy użyciu usługi ServiceNow w witrynie Azure portal
### <a name="for-a-new-action-group"></a>Dla nowej grupy akcji:
1. Wykonaj kroki od 1 do 8 w [w tym artykule](../azure-monitor/platform/alerts-activity-log-service-notifications.md) w celu utworzenia alertu z nową grupę akcji.

1. Zdefiniuj na liście **akcje**:

    a. **Typ akcji:** *Element Webhook*

    b. **Szczegóły:** Usługi ServiceNow **adresów URL integracji** zostanie zapisany wcześniej.

    c. **Nazwa:** Nazwy, aliasu lub identyfikator dla elementu Webhook.

1. Wybierz **Zapisz** po zakończeniu tworzenia alertu.

### <a name="for-an-existing-action-group"></a>Dla istniejącej grupy akcji:
1. W [witryny Azure portal](https://portal.azure.com/), wybierz opcję **Monitor**.

1. W **ustawienia** zaznacz **grup akcji**.

1. Znajdź i wybierz grupę akcji, którą chcesz edytować.

1. Dodaj do listy **akcje**:

    a. **Typ akcji:** *Element Webhook*

    b. **Szczegóły:** Usługi ServiceNow **adresów URL integracji** zostanie zapisany wcześniej.

    c. **Nazwa:** Nazwy, aliasu lub identyfikator dla elementu Webhook.

1. Wybierz **Zapisz** po zakończeniu można zaktualizować grupy akcji.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testowanie integracji usługi elementu webhook, za pomocą żądania HTTP POST
1. Utwórz ładunek kondycji usługi, które mają zostać wysłane. Można znaleźć, ładunek elementu webhook przykład usługi kondycji w [alerty dzienników elementy Webhook dla aktywności platformy Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Utwórz żądanie HTTP POST w następujący sposób:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Powinien zostać wyświetlony `200 OK` odpowiedzi z komunikatem "Utworzone zdarzenie".

1. Przejdź do [ServiceNow](https://www.servicenow.com/) aby upewnić się, że integracji usługi zostało pomyślnie skonfigurowane.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się, jak [Konfigurowanie powiadomień elementu webhook dla istniejących systemów zarządzania problem](service-health-alert-webhook-guide.md).
- Przegląd [schemat elementów webhook alertu dziennika aktywności](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Dowiedz się więcej o [usługi powiadomień dotyczących kondycji](../azure-monitor/platform/service-notifications.md).
- Dowiedz się więcej o [grup akcji](../azure-monitor/platform/action-groups.md).