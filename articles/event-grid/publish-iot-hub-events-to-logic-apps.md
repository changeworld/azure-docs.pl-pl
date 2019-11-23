---
title: Tutorial - Use IoT Hub events to trigger Azure Logic Apps
description: Tutorial - Using the event routing service of Azure Event Grid, create automated processes to perform Azure Logic Apps actions based on IoT Hub events.
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: tutorial
ms.date: 11/21/2019
ms.author: robinsh
ms.openlocfilehash: 70ad74715446a54605a23a049ebc92a81d7ee673
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423773"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Tutorial: Send email notifications about Azure IoT Hub events using Event Grid and Logic Apps

Usługa Azure Event Grid pozwala reagować na zdarzenia usługi IoT Hub dzięki akcjom wyzwalanym w podrzędnych aplikacjach biznesowych.

This article walks through a sample configuration that uses IoT Hub and Event Grid. At the end, you have an Azure logic app set up to send a notification email every time a device is added to your IoT hub. 

## <a name="prerequisites"></a>Wymagania wstępne

* Konto e-mail od dostawcy obsługiwanego przez usługę Azure Logic Apps, na przykład Office 365 Outlook, Outlook.com lub Gmail. To konto e-mail służy do wysyłania powiadomień o zdarzeniach. Aby uzyskać pełną listę łączników obsługiwanych przez usługę Logic Apps, zobacz [Omówienie łączników](https://docs.microsoft.com/connectors/).
* Aktywne konto platformy Azure. Jeśli go nie masz, możesz [utworzyć bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
* Centrum IoT Hub na platformie Azure. Jeśli jeszcze go nie masz, zobacz przewodnik [Wprowadzenie do usługi IoT Hub](../iot-hub/iot-hub-csharp-csharp-getstarted.md). 

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

First, create a logic app and add an event grid trigger that monitors the resource group for your virtual machine. 

### <a name="create-a-logic-app-resource"></a>Tworzenie zasobu aplikacji logiki

1. In the [Azure portal](https://portal.azure.com), select **Create a resource**, then type "logic app" in the search box and select return. Select **Logic App** from the results.

   ![Tworzenie aplikacji logiki](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. On the next screen, select **Create**. 

1. Nadaj aplikacji logiki nazwę unikatową w ramach subskrypcji, a następnie wybierz tę samą subskrypcję, grupę zasobów i lokalizację, które są skojarzone z centrum IoT. 

   ![Fields for create logic app](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Wybierz pozycję **Utwórz**.

1. Po utworzeniu zasobu przejdź do aplikacji logiki. To do this, select **Resource groups**, then select the resource group you created for this tutorial. Then find the logic app in the list of resources and select it. 

1. In the Logic Apps Designer, page down to see **Templates**. Choose **Blank Logic App** so that you can build your logic app from scratch.

### <a name="select-a-trigger"></a>Wybieranie wyzwalacza

Wyzwalacz to konkretne zdarzenie, które uruchamia aplikację logiki. W tym samouczku wyzwalacz, który uruchamia przepływ pracy, odbiera żądanie za pośrednictwem protokołu HTTP.  

1. Na pasku wyszukiwania łączników i wyzwalaczy wpisz ciąg **HTTP**.

1. Wybierz pozycję **Żądanie — Po odebraniu żądania HTTP** jako wyzwalacz. 

   ![Wybieranie wyzwalacza żądania HTTP](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. Wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**. 

   ![Wybieranie wyzwalacza żądania HTTP](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. Wklej poniższy przykładowy kod JSON w polu tekstowym, a następnie wybierz pozycję **Gotowe**:

   ```json
   [{
     "id": "56afc886-767b-d359-d59e-0da7877166b2",
     "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
     "subject": "devices/LogicAppTestDevice",
     "eventType": "Microsoft.Devices.DeviceCreated",
     "eventTime": "2018-01-02T19:17:44.4383997Z",
     "data": {
       "twin": {
         "deviceId": "LogicAppTestDevice",
         "etag": "AAAAAAAAAAE=",
         "deviceEtag": "null",
         "status": "enabled",
         "statusUpdateTime": "0001-01-01T00:00:00",
         "connectionState": "Disconnected",
         "lastActivityTime": "0001-01-01T00:00:00",
         "cloudToDeviceMessageCount": 0,
         "authenticationType": "sas",
         "x509Thumbprint": {
           "primaryThumbprint": null,
           "secondaryThumbprint": null
         },
         "version": 2,
         "properties": {
           "desired": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           },
           "reported": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           }
         }
       },
       "hubName": "egtesthub1",
       "deviceId": "LogicAppTestDevice"
     },
     "dataVersion": "1",
     "metadataVersion": "1"
   }]
   ```

1. Może pojawić się wyskakujące powiadomienie **Pamiętaj, aby w żądaniu uwzględnić nagłówek Content-Type ustawiony na wartość application/json**. Można zignorować ten komunikat i przejść do następnej sekcji. 

### <a name="create-an-action"></a>Tworzenie akcji

Akcje to kroki, które są wykonywane, gdy wyzwalacz uruchomi przepływ pracy aplikacji logiki. W tym samouczku akcja polega na wysłaniu powiadomienia e-mail pochodzącego od dostawcy poczty e-mail. 

1. Wybierz pozycję **Nowy krok**. This opens a window to **Choose an action**.

1. Wyszukaj nazwę **E-mail**.

1. W oparciu o Twojego dostawcę poczty e-mail znajdź i wybierz zgodny łącznik. W tym samouczku jest używana usługa **Office 365 Outlook**. Kroki dla innych dostawców poczty e-mail są podobne. 

   ![Wybieranie łącznika dostawcy poczty e-mail](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

1. Wybierz akcję **Wyślij wiadomość e-mail**. 

1. W razie potrzeby zaloguj się do swojego konta e-mail. 

1. Utwórz szablon wiadomości e-mail. 

   * **Do**: wpisz adres e-mail, na który mają być wysyłane wiadomości e-mail z powiadomieniami. Na potrzeby tego samouczka użyj konta e-mail, które nadaje się do celów testowych. 

   * **Subject**: Fill in the text for the subject. When you click on the Subject text box, you can select dynamic content to include. For example, this tutorial uses `IoT Hub alert: {event Type}`. If you can't see Dynamic content, select the **Add dynamic content** hyperlink -- this toggles it on and off.

   * **Body**: Write the text for your email. Za pomocą narzędzia do wybierania wybierz właściwości JSON w celu dołączenia zawartości dynamicznej opartej na danych zdarzenia. If you can't see the Dynamic content, select the **Add dynamic content** hyperlink under the **Body** text box. If it doesn't show you the fields you want, click *more* in the Dynamic content screen to include the fields from the previous action.

   Przykładowy szablon wiadomości e-mail może wyglądać następująco:

   ![Wpisywanie informacji dotyczących wiadomości e-mail.](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. Zapisz aplikację logiki. 

### <a name="copy-the-http-url"></a>Kopiowanie adresu URL HTTP

Przed zamknięciem projektanta aplikacji usługi Logic Apps skopiuj adres URL, za pomocą którego aplikacja logiki odbiera wyzwalacz. Ten adres URL zostanie użyty do skonfigurowania usługi Event Grid. 

1. Kliknij pole konfiguracji wyzwalacza **Po odebraniu żądania HTTP**, aby je rozwinąć. 

1. Skopiuj wartość pola **Adres URL żądania HTTP POST**, wybierając przycisk widoczny obok tego pola. 

   ![Kopiowanie adresu URL żądania HTTP POST](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. Zapisz ten adres URL — zostanie użyty w następnej sekcji. 

## <a name="configure-subscription-for-iot-hub-events"></a>Konfigurowanie subskrypcji zdarzeń usługi IoT Hub

W tej sekcji skonfigurujesz usługę IoT Hub pod kątem publikowania zdarzeń na bieżąco. 

1. W witrynie Azure Portal przejdź do centrum IoT Hub. You can do this by selecting **Resource groups**, then select the resource group for this tutorial, and then select your IoT hub from the list of resources.

2. Wybierz pozycję **Zdarzenia**.

   ![Wyświetlanie szczegółów usługi Event Grid](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Wybierz pozycję **Subskrypcja zdarzeń**. 

   ![Tworzenie nowej subskrypcji zdarzeń](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Utwórz subskrypcję zdarzeń, korzystając z następujących wartości: 

   * **Event Subscription Details**: Provide a descriptive name and select **Event Grid Schema**.

   * **Event Types**: In the **Filter to Event Types**, uncheck all of the choices except **Device Created**.

       ![subscription event types](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)

   * **Endpoint Details**: Select Endpoint Type as **Web Hook** and select *select an endpoint* and paste the URL that you copied from your logic app and confirm selection.

     ![wybieranie adresu url punktu końcowego](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

   When you're done, the pane should look like the following example: 

    ![Przykładowy formularz subskrypcji zdarzeń](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Możesz teraz zapisać subskrypcję zdarzeń, aby otrzymywać powiadomienia o wszystkich urządzeniach tworzonych w centrum IoT Hub. Jednak w tym samouczku użyjemy opcjonalnych filtrów, aby odfiltrować konkretne urządzenia. Select **Filters** at the top of the pane.

6. Select **Add new filter**. Fill in the fields with these values:

   * **Key**: Select `Subject`.

   * **Operator**: Select `String begins with`.

   * **Value**:  Enter `devices/Building1_` to filter for device events in building 1.
  
   Add another filter with these values:

   * **Key**: Select `Subject`.

   * **Operator**: Select `String ends with`.

   * **Value**: Enter `_Temperature` to filter for device events related to temperature.

   The **Filters** tab of your event subscription should now look similar to this image:

   ![Adding filters to event subscription](./media/publish-iot-hub-events-to-logic-apps/event-subscription-filters.png)

7. Wybierz pozycję **Utwórz**, aby zapisać subskrypcję zdarzeń.

## <a name="create-a-new-device"></a>Tworzenie nowego urządzenia

Aby przetestować aplikację logiki, utwórz nowe urządzenie w celu wyzwolenia wiadomości e-mail z powiadomieniem o zdarzeniu. 

1. W centrum IoT Hub wybierz pozycję **Urządzenia IoT**. 

2. Wybierz pozycję **Nowy**.

3. W polu **Identyfikator urządzenia** wpisz ciąg `Building1_Floor1_Room1_Light`.

4. Wybierz pozycję **Zapisz**. 

5. Aby przetestować filtry subskrypcji zdarzeń, możesz dodać wiele urządzeń z różnymi identyfikatorami. Wypróbuj te przykłady: 

   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

   If you added the four examples, your list of IoT devices should look like the following image:

   ![IoT Hub device list](./media/publish-iot-hub-events-to-logic-apps/iot-hub-device-list.png)

6. Po dodaniu kilku urządzeń do centrum IoT Hub sprawdź pocztę e-mail, aby zobaczyć, które z nich wyzwoliły działanie aplikacji logiki. 

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

Nie musisz korzystać z witryny Azure Portal — kroki w centrum IoT Hub możesz wykonać przy użyciu interfejsu wiersza polecenia platformy Azure. For details, see the Azure CLI pages for [creating an event subscription](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) and [creating an IoT device](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Użycie zasobów w tym samouczku powoduje naliczanie opłat w ramach Twojej subskrypcji platformy Azure. When you're finished trying out the tutorial and testing your results, disable or delete resources that you don't want to keep. 

To delete all of the resources created in this tutorial, delete the resource group. 

1. Select **Resource groups**, then select the resource group you created for this tutorial.

2. On the Resource group pane, select **Delete resource group**. You are prompted to enter the resource group name, and then you can delete it. All of the resources contained therein are also removed.

If you don't want to remove all of the resources, you can manage them one by one. 

Jeśli nie chcesz utracić efektów pracy z aplikacją logiki, nie usuwaj jej, tylko wyłącz. 

1. Przejdź do aplikacji logiki.

2. W bloku **Przegląd** wybierz pozycję **Usuń** lub **Wyłącz**. 

Każda subskrypcja może zawierać jedno centrum IoT Hub, z którego korzystanie jest bezpłatne. Jeśli w samouczku utworzono bezpłatne centrum, nie musisz go usuwać, ponieważ nie generuje ono kosztów.

1. Przejdź do centrum IoT Hub. 

2. W bloku **Przegląd** wybierz pozycję **Usuń**. 

Być może zdecydujesz się zachować centrum IoT Hub, ale usunąć utworzoną subskrypcję zdarzeń. 

1. W centrum IoT Hub wybierz pozycję **Event Grid**.

2. Wybierz subskrypcję zdarzeń, którą chcesz usunąć. 

3. Wybierz pozycję **Usuń**. 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej — zobacz [Reacting to IoT Hub events by using Event Grid to trigger actions (Reagowanie na zdarzenia usługi IoT Hub przez wyzwalanie akcji za pomocą usługi Event Grid)](../iot-hub/iot-hub-event-grid.md).
* [Dowiedz się, jak porządkować zdarzenia podłączania i odłączania urządzeń](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* Dowiedz się, co jeszcze możesz robić przy użyciu usługi [Event Grid](overview.md).