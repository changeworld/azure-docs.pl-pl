---
title: 'Samouczek: używanie zdarzeń IoT Hub do wyzwalania Azure Logic Apps'
description: 'Samouczek: korzystanie z usługi routingu zdarzeń Azure Event Grid, Tworzenie zautomatyzowanych procesów w celu wykonywania Azure Logic Apps akcji opartych na zdarzeniach IoT Hub.'
services: iot-hub
documentationcenter: ''
author: kgremban
manager: philmea
editor: ''
ms.service: iot-hub
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: kgremban
ms.openlocfilehash: e003cb650b0589ab43c984850838c56cbbf1ff2f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74106775"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-logic-apps"></a>Samouczek: wysyłanie powiadomień e-mail dotyczących zdarzeń usługi Azure IoT Hub przy użyciu Logic Apps

Usługa Azure Event Grid pozwala reagować na zdarzenia usługi IoT Hub dzięki akcjom wyzwalanym w podrzędnych aplikacjach biznesowych.

W tym artykule przedstawiono przykładową konfigurację, w której zastosowano usługi IoT Hub i Event Grid. Wykonanie tych instrukcji pozwoli Ci skonfigurować aplikację logiki platformy Azure umożliwiającą wysyłanie wiadomości e-mail z powiadomieniem za każdym razem, gdy do usługi IoT Hub zostanie dodane urządzenie. 

## <a name="prerequisites"></a>Wymagania wstępne

* Konto e-mail od dostawcy obsługiwanego przez usługę Azure Logic Apps, na przykład Office 365 Outlook, Outlook.com lub Gmail. To konto e-mail służy do wysyłania powiadomień o zdarzeniach. Aby uzyskać pełną listę łączników obsługiwanych przez usługę Logic Apps, zobacz [Omówienie łączników](https://docs.microsoft.com/connectors/).
* Aktywne konto platformy Azure. Jeśli go nie masz, możesz [utworzyć bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
* Centrum IoT Hub na platformie Azure. Jeśli jeszcze go nie masz, zobacz przewodnik [Wprowadzenie do usługi IoT Hub](../iot-hub/iot-hub-csharp-csharp-getstarted.md). 

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

Najpierw należy utworzyć aplikację logiki i dodać wyzwalacz usługi Event Grid, który monitoruje grupę zasobów dla maszyny wirtualnej. 

### <a name="create-a-logic-app-resource"></a>Tworzenie zasobu aplikacji logiki

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Utwórz zasób** > **Integracja** > **Aplikacja logiki**.

   ![Tworzenie aplikacji logiki](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

2. Nadaj aplikacji logiki nazwę unikatową w ramach subskrypcji, a następnie wybierz tę samą subskrypcję, grupę zasobów i lokalizację, które są skojarzone z centrum IoT. 
3. Wybierz pozycję **Utwórz**.

4. Po utworzeniu zasobu przejdź do aplikacji logiki. 

5. Projektant aplikacji usługi Logic Apps pokaże szablony dla typowych wzorców, dzięki czemu możesz szybciej rozpocząć pracę. W projektancie aplikacji usługi Logic Apps w obszarze **Szablony** wybierz pozycję **Pusta aplikacja logiki**, aby rozpocząć tworzenie aplikacji logiki od podstaw.

### <a name="select-a-trigger"></a>Wybieranie wyzwalacza

Wyzwalacz to konkretne zdarzenie, które uruchamia aplikację logiki. W tym samouczku wyzwalacz, który uruchamia przepływ pracy, odbiera żądanie za pośrednictwem protokołu HTTP.  

1. Na pasku wyszukiwania łączników i wyzwalaczy wpisz ciąg **HTTP**.
2. Wybierz pozycję **Żądanie — Po odebraniu żądania HTTP** jako wyzwalacz. 

   ![Wybieranie wyzwalacza żądania HTTP](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

3. Wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**. 

   ![Wybieranie wyzwalacza żądania HTTP](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

4. Wklej poniższy przykładowy kod JSON w polu tekstowym, a następnie wybierz pozycję **Gotowe**:

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

5. Może pojawić się wyskakujące powiadomienie **Pamiętaj, aby w żądaniu uwzględnić nagłówek Content-Type ustawiony na wartość application/json**. Można zignorować ten komunikat i przejść do następnej sekcji. 

### <a name="create-an-action"></a>Tworzenie akcji

Akcje to kroki, które są wykonywane, gdy wyzwalacz uruchomi przepływ pracy aplikacji logiki. W tym samouczku akcja polega na wysłaniu powiadomienia e-mail pochodzącego od dostawcy poczty e-mail. 

1. Wybierz pozycję **Nowy krok**. Spowoduje to otwarcie okna **Wybierz akcję**.

2. Wyszukaj nazwę **E-mail**.

3. W oparciu o Twojego dostawcę poczty e-mail znajdź i wybierz zgodny łącznik. W tym samouczku jest używana usługa **Office 365 Outlook**. Kroki dla innych dostawców poczty e-mail są podobne. 

   ![Wybieranie łącznika dostawcy poczty e-mail](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

4. Wybierz akcję **Wyślij wiadomość e-mail**. 

5. W razie potrzeby zaloguj się do swojego konta e-mail. 

6. Utwórz szablon wiadomości e-mail. 
   * **Do**: wpisz adres e-mail, na który mają być wysyłane wiadomości e-mail z powiadomieniami. Na potrzeby tego samouczka użyj konta e-mail, które nadaje się do celów testowych. 
   * **Temat** i **Treść**: wpisz tekst wiadomości e-mail. Za pomocą narzędzia do wybierania wybierz właściwości JSON w celu dołączenia zawartości dynamicznej opartej na danych zdarzenia.  

   Przykładowy szablon wiadomości e-mail może wyglądać następująco:

   ![Wpisywanie informacji dotyczących wiadomości e-mail.](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

7. Zapisz aplikację logiki. 

### <a name="copy-the-http-url"></a>Kopiowanie adresu URL HTTP

Przed zamknięciem projektanta aplikacji usługi Logic Apps skopiuj adres URL, za pomocą którego aplikacja logiki odbiera wyzwalacz. Ten adres URL zostanie użyty do skonfigurowania usługi Event Grid. 

1. Kliknij pole konfiguracji wyzwalacza **Po odebraniu żądania HTTP**, aby je rozwinąć. 
2. Skopiuj wartość pola **Adres URL żądania HTTP POST**, wybierając przycisk widoczny obok tego pola. 

   ![Kopiowanie adresu URL żądania HTTP POST](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

3. Zapisz ten adres URL — zostanie użyty w następnej sekcji. 

## <a name="configure-subscription-for-iot-hub-events"></a>Konfigurowanie subskrypcji zdarzeń usługi IoT Hub

W tej sekcji skonfigurujesz usługę IoT Hub pod kątem publikowania zdarzeń na bieżąco. 

1. W witrynie Azure Portal przejdź do centrum IoT Hub. 
2. Wybierz pozycję **Zdarzenia**.

   ![Wyświetlanie szczegółów usługi Event Grid](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Wybierz pozycję **Subskrypcja zdarzeń**. 

   ![Tworzenie nowej subskrypcji zdarzeń](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Utwórz subskrypcję zdarzeń, korzystając z następujących wartości: 
   * **Typ zdarzenia**: usuń zaznaczenie pola wyboru Subskrybuj wszystkie typy zdarzeń i wybierz z menu pozycję **Urządzenie utworzone**.
   * **Szczegóły punktu końcowego**: wybierz typ punktu końcowego **Webhook**, a następnie kliknij wybrany punkt końcowy, wklej adres URL skopiowany z aplikacji logiki i potwierdź wybór.

     ![wybieranie adresu url punktu końcowego](./media/publish-iot-hub-events-to-logic-apps/endpoint-url.png)

   * **Szczegóły subskrypcji zdarzeń**: wprowadź opisową nazwę, a następnie wybierz pozycję **Schemat usługi Event Grid**

   Gdy wszystko będzie gotowe, formularz powinien wyglądać tak: 

    ![Przykładowy formularz subskrypcji zdarzeń](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Możesz teraz zapisać subskrypcję zdarzeń, aby otrzymywać powiadomienia o wszystkich urządzeniach tworzonych w centrum IoT Hub. Jednak w tym samouczku użyjemy opcjonalnych filtrów, aby odfiltrować konkretne urządzenia. Wybierz pozycję **Dodatkowe funkcje** w górnej części formularza. 

6. Utwórz następujące filtry:

   * **Temat zaczyna się od**: wprowadź wartość `devices/Building1_`, aby odfiltrować zdarzenia urządzeń w budynku 1.
   * **Temat kończy się na**: wprowadź wartość `_Temperature`, aby odfiltrować zdarzenia urządzeń związane z temperaturą.

5. Wybierz pozycję **Utwórz**, aby zapisać subskrypcję zdarzeń.

## <a name="create-a-new-device"></a>Tworzenie nowego urządzenia

Aby przetestować aplikację logiki, utwórz nowe urządzenie w celu wyzwolenia wiadomości e-mail z powiadomieniem o zdarzeniu. 

1. W centrum IoT Hub wybierz pozycję **Urządzenia IoT**. 
2. Wybierz pozycję **Dodaj**.
3. W polu **Identyfikator urządzenia** wpisz ciąg `Building1_Floor1_Room1_Temperature`.
4. Wybierz pozycję **Zapisz**. 
5. Aby przetestować filtry subskrypcji zdarzeń, możesz dodać wiele urządzeń z różnymi identyfikatorami. Wypróbuj te przykłady: 
   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

Po dodaniu kilku urządzeń do centrum IoT Hub sprawdź pocztę e-mail, aby zobaczyć, które z nich wyzwoliły działanie aplikacji logiki. 

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

Nie musisz korzystać z witryny Azure Portal — kroki w centrum IoT Hub możesz wykonać przy użyciu interfejsu wiersza polecenia platformy Azure. Więcej informacji zawierają strony z opisem interfejsu wiersza polecenia platformy Azure, które dotyczą [tworzenia subskrypcji zdarzeń](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) i [tworzenia urządzenia IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Użycie zasobów w tym samouczku powoduje naliczanie opłat w ramach Twojej subskrypcji platformy Azure. Gdy zakończysz testy w ramach tego samouczka, wyłącz lub usuń zasoby, które nie są potrzebne. 

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


