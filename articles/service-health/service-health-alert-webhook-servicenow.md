---
title: Wysyłanie alertów kondycji usługi platformy Azure za pomocą usługi ServiceNow
description: Uzyskaj spersonalizowane powiadomienia o zdarzeniach kondycji usługi do wystąpienia ServiceNow.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 3daae05aabff571010d043cf5602847e95ea29f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654107"
---
# <a name="send-azure-service-health-alerts-with-servicenow-using-webhooks"></a>Wysyłanie alertów kondycji usługi platformy Azure za pomocą usługi ServiceNow przy użyciu elementów webhook

W tym artykule pokazano, jak zintegrować alerty kondycji usługi platformy Azure z ServiceNow przy użyciu elementu webhook. Po skonfigurowaniu integracji elementu webhook z wystąpieniem ServiceNow, otrzymasz alerty za pośrednictwem istniejącej infrastruktury powiadomień, gdy problemy z usługą Platformy Azure wpływają na Ciebie. Za każdym razem, gdy alert usługi Azure Service Health jest uruchamiany, wywołuje element webhook za pośrednictwem interfejsu API REST skryptów ServiceNow.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>Tworzenie skryptowego interfejsu API REST w usłudze ServiceNow

1.  Upewnij się, że zarejestrowałeś się i zalogowałeś się na swoje konto [ServiceNow.](https://www.servicenow.com/)

1.  Przejdź do sekcji **System Web Services** w servicenow i wybierz pozycję **Scripted REST API .**

    ![Sekcja "Scripted Web Service" w ServiceNow](./media/webhook-alerts/servicenow-sws-section.png)

1.  Wybierz **pozycję Nowy,** aby utworzyć nową usługę SCRIPTED REST.
 
    ![Przycisk "Nowy interfejs API REST skryptów" w servicenow](./media/webhook-alerts/servicenow-new-button.png)

1.  Dodaj **nazwę** do interfejsu API REST i `azureservicehealth`ustaw identyfikator interfejsu **API** na .

1.  Wybierz pozycję **Prześlij**.

    !["Ustawienia interfejsu API REST" w servicenow](./media/webhook-alerts/servicenow-restapi-settings.png)

1.  Wybierz utworzony interfejs API REST, a następnie na karcie **Zasoby** wybierz pozycję **Nowy**.

    ![Karta "Zasób" w serwisie Teraz](./media/webhook-alerts/servicenow-resources-tab.png)

1.  **Nazwij** `event` nowy zasób `POST`i zmień metodę **HTTP** na .

1.  W sekcji **Skrypt** dodaj następujący kod JavaScript:

    >[!NOTE]
    >Musisz zaktualizować `<secret>`,`<group>`i `<email>` wartość w poniższym skrypcie.
    >* `<secret>`powinien być losowym ciągiem, takim jak identyfikator GUID
    >* `<group>`powinna być grupą ServiceNow, do której chcesz przypisać zdarzenie
    >* `<email>`powinna być konkretna osoba, do której chcesz przypisać zdarzenie (opcjonalnie)
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

1.  Na karcie zabezpieczeń wyjmij pole wyboru **Wymaga uwierzytelniania** i wybierz pozycję **Prześlij**. Ustawiona `<secret>` ochrona tego interfejsu API zamiast tego.

    ![Pole wyboru "Wymaga uwierzytelniania" w ServiceNow](./media/webhook-alerts/servicenow-resource-settings.png)

1.  W sekcji Scripted REST APIs należy znaleźć **podstawową ścieżkę interfejsu API** dla nowego interfejsu API REST:

     !["Podstawowa ścieżka interfejsu API" w ServiceNow](./media/webhook-alerts/servicenow-base-api-path.png)

1.  Pełny adres URL integracji wygląda następująco:
        
         https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>


## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Tworzenie alertu przy użyciu usługi ServiceNow w witrynie Azure portal
### <a name="for-a-new-action-group"></a>Dla nowej grupy akcji:
1. Wykonaj kroki od 1 do 8 w [tym artykule,](../azure-monitor/platform/alerts-activity-log-service-notifications.md) aby utworzyć alert z nową grupą akcji.

1. Zdefiniuj na liście **akcji:**

    a. **Typ akcji:** *Element webhook*

    b. **Szczegóły:** Adres **URL integracji** UsługiNow, który został wcześniej zapisany.

    d. **Nazwa:** Nazwa, alias lub identyfikator elementu webhook.

1. Wybierz **pozycję Zapisz** po wykonaniu, aby utworzyć alert.

### <a name="for-an-existing-action-group"></a>W przypadku istniejącej grupy akcji:
1. W [witrynie Azure portal](https://portal.azure.com/)wybierz pozycję **Monitor**.

1. W sekcji **Ustawienia** wybierz pozycję **Grupy akcji**.

1. Znajdź i wybierz grupę akcji, którą chcesz edytować.

1. Dodaj do listy **akcji:**

    a. **Typ akcji:** *Element webhook*

    b. **Szczegóły:** Adres **URL integracji** UsługiNow, który został wcześniej zapisany.

    d. **Nazwa:** Nazwa, alias lub identyfikator elementu webhook.

1. Wybierz **pozycję Zapisz** po zakończeniu, aby zaktualizować grupę akcji.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testowanie integracji elementu webhook za pomocą żądania HTTP POST
1. Utwórz ładunek kondycji usługi, który chcesz wysłać. Przykładowy ładunek elementu webhook kondycji usługi można znaleźć w [witrynach Webhooks dla alertów dziennika aktywności platformy Azure.](../azure-monitor/platform/activity-log-alerts-webhook.md)

1. Utwórz żądanie HTTP POST w następujący sposób:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Powinna pojawić `200 OK` się odpowiedź z komunikatem "Zdarzenie utworzone".

1. Przejdź do [ServiceNow,](https://www.servicenow.com/) aby potwierdzić, że integracja została pomyślnie skonfigurowana.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [skonfigurować powiadomienia dotyczące elementu webhook dla istniejących systemów zarządzania problemami](service-health-alert-webhook-guide.md).
- Przejrzyj [schemat programu webhook alertu dziennika aktywności](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Dowiedz się więcej o [powiadomieniach o kondycji usługi](../azure-monitor/platform/service-notifications.md).
- Dowiedz się więcej o [grupach akcji](../azure-monitor/platform/action-groups.md).