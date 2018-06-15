---
title: Skonfiguruj alerty dotyczące kondycji usługi platformy Azure z usługi ServiceNow | Dokumentacja firmy Microsoft
description: Pobierz spersonalizowany powiadomień dotyczących zdarzeń usługi kondycji do Twojego wystąpienia usługi ServiceNow.
author: shawntabrizi
services: service-health
documentationcenter: service-health
ms.assetid: ''
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: 867a8c0b478df9d2b7690b8b914ded7c42558583
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30178872"
---
# <a name="configure-service-health-alerts-with-servicenow"></a>Skonfigurować alerty dotyczące kondycji usługi ServiceNow

W tym artykule przedstawiono sposób integracji alerty dotyczące kondycji usługi platformy Azure z usługi ServiceNow przy użyciu elementu webhook. Po skonfigurowaniu integracji elementu webhook z wystąpieniem usługi ServiceNow, otrzymujesz alerty przy użyciu istniejącej infrastruktury powiadomień po dotyczy problemów dotyczących usługi Azure. Za każdym razem, gdy zgłoszeniu alertu Health usługi Azure, wywołania elementu webhook za pośrednictwem interfejsu API REST usługi ServiceNow na inicjowanych przez skrypty.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>Tworzenie inicjowanych przez skrypty interfejsu API REST usługi ServiceNow
1.  Upewnij się, że zalogowali się i zalogowano na Twojej [ServiceNow](https://www.servicenow.com/) konta.

2.  Przejdź do **usług sieci Web systemu** części usługi ServiceNow i wybierz **inicjowanych przez skrypty interfejsów API REST**.

    !["Usługa sieci Web inicjowanych przez skrypty" części usługi ServiceNow](./media/webhook-alerts/servicenow-sws-section.png)

3.  Wybierz **nowy** do utworzenia nowej usługi REST inicjowanych przez skrypty.
 
    ![Przycisk "Nowy inicjowanych przez skrypty interfejsu API REST" w ServiceNow](./media/webhook-alerts/servicenow-new-button.png)

4.  Dodaj **nazwa** do interfejsu API REST i zestaw **Identyfikatora interfejsu API** do `azureservicehealth`.

5.  Wybierz **przesłać**.

    !["Ustawienia interfejsu API REST" w ServiceNow](./media/webhook-alerts/servicenow-restapi-settings.png)

6.  Wybierz interfejsu API REST, które zostały utworzone, a następnie w obszarze **zasobów** wybierz pozycję **nowy**.

    !["Karta zasobów" w ServiceNow](./media/webhook-alerts/servicenow-resources-tab.png)

7.  **Nazwa** nowego zasobu `event` i zmienić **metoda HTTP** do `POST`.

8.  W **skryptu** Dodaj następujący kod JavaScript:

    >[!NOTE]
    >Musisz zaktualizować `<secret>`,`<group>`, i `<email>` wartości w poniższym skrypcie.
    >* `<secret>` powinien być losowy ciąg znaków, takich jak identyfikator GUID
    >* `<group>` powinna to być grupa usługi ServiceNow, którego chcesz przypisać zdarzenia do
    >* `<email>` powinny być określone osoby, które chcesz przypisać zdarzenia do (opcjonalnie)
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
                } else if (event.data.context.activityLog.properties.incidentType == "Information" || event.data.context.activityLog.properties.incidentType == "ActionRequired") {
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

9.  Na karcie Zabezpieczenia, usuń zaznaczenie pola wyboru **wymaga uwierzytelniania** i wybierz **przesyłania**. `<secret>` Należy zamiast tego zestawu chroni tego interfejsu API.

    ![Pole wyboru "Wymaga uwierzytelniania" w ServiceNow](./media/webhook-alerts/servicenow-resource-settings.png)

10.  W sekcji inicjowanych przez skrypty interfejsów API REST, należy odnaleźć **Base API ścieżki** nowego interfejsu API REST:

     !["Podstawowego ścieżki interfejsu API" w ServiceNow](./media/webhook-alerts/servicenow-base-api-path.png)

11.  Pełna integracja adres URL wygląda następująco:
        
         https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>


## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Utwórz alert przy użyciu usługi ServiceNow w portalu Azure
### <a name="for-a-new-action-group"></a>Dla nowej grupy akcji:
1. Wykonaj kroki od 1 do 8 w [w tym artykule](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md) ma być tworzony alert z nową grupą akcji.

2. Zdefiniuj na liście **akcje**:

    a. **Typ akcji:** *elementu Webhook*

    b. **Szczegóły:** ServiceNow **integracji adres URL** należy wcześniej zapisać.

    c. **Nazwa:** dla elementu Webhook nazwy, aliasu lub identyfikator.

3. Wybierz **zapisać** po zakończeniu można utworzyć alertu.

### <a name="for-an-existing-action-group"></a>Dla istniejącej grupy akcji:
1. W [portalu Azure](https://portal.azure.com/), wybierz pozycję **Monitor**.

2. W **ustawienia** zaznacz **grupy akcji**.

3. Znajdź i wybierz grupę akcji, którą chcesz edytować.

4. Dodaj do listy **akcje**:

    a. **Typ akcji:** *elementu Webhook*

    b. **Szczegóły:** ServiceNow **integracji adres URL** należy wcześniej zapisać.

    c. **Nazwa:** dla elementu Webhook nazwy, aliasu lub identyfikator.

5. Wybierz **zapisać** na koniec można zaktualizować grupy działań.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testowanie za pomocą żądania HTTP POST integracją elementu webhook
1. Utwórz ładunku kondycji usługi, który chcesz wysłać. Można znaleźć przykład usługi kondycji elementu webhook ładunku w [elementów Webhook dla działania Azure rejestrowania alertów](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Utwórz żądanie HTTP POST w następujący sposób:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
3. Powinien zostać wyświetlony `200 OK` odpowiedź z komunikatem "Utworzone zdarzenie".

4. Przejdź do [ServiceNow](https://www.servicenow.com/) aby upewnić się, że integracją zostało pomyślnie skonfigurowane.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się, jak [skonfigurować powiadomienia elementu webhook dla istniejących systemów zarządzania problem](service-health-alert-webhook-guide.md).
- Przegląd [schemat alertu elementu webhook dziennika aktywności](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Dowiedz się więcej o [usługi powiadomień o kondycji](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Dowiedz się więcej o [grupy akcji](../monitoring-and-diagnostics/monitoring-action-groups.md).