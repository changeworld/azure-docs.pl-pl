---
title: Samouczek — wyzwalanie aplikacji logiki azure
description: W tym samouczku pokazano, jak korzystać z usługi routingu zdarzeń usługi Azure Event Grid, tworzyć zautomatyzowane procesy do wykonywania akcji usługi Azure Logic Apps na podstawie zdarzeń Usługi IoT Hub.
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: tutorial
ms.date: 11/21/2019
ms.author: robinsh
ms.openlocfilehash: 334b7b2c59b328e8eff3c7c2b9c3ed46bffc3442
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74706436"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Samouczek: Wysyłanie powiadomień e-mail o zdarzeniach usługi Azure IoT Hub przy użyciu siatki zdarzeń i aplikacji logiki

Usługa Azure Event Grid pozwala reagować na zdarzenia usługi IoT Hub dzięki akcjom wyzwalanym w podrzędnych aplikacjach biznesowych.

W tym artykule otoczyła się przykładowa konfiguracja, która używa usługi IoT Hub i siatki zdarzeń. Na końcu masz aplikację logiki platformy Azure skonfigurowaną do wysyłania wiadomości e-mail z powiadomieniem za każdym razem, gdy urządzenie zostanie dodane do centrum IoT Hub. 

## <a name="prerequisites"></a>Wymagania wstępne

* Konto e-mail od dostawcy obsługiwanego przez usługę Azure Logic Apps, na przykład Office 365 Outlook, Outlook.com lub Gmail. To konto e-mail służy do wysyłania powiadomień o zdarzeniach. Aby uzyskać pełną listę łączników obsługiwanych przez usługę Logic Apps, zobacz [Omówienie łączników](https://docs.microsoft.com/connectors/).
* Aktywne konto platformy Azure. Jeśli go nie masz, możesz [utworzyć bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
* Centrum IoT Hub na platformie Azure. Jeśli jeszcze go nie masz, zobacz przewodnik [Wprowadzenie do usługi IoT Hub](../iot-hub/iot-hub-csharp-csharp-getstarted.md). 

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

Najpierw utwórz aplikację logiki i dodaj wyzwalacz siatki zdarzeń, który monitoruje grupę zasobów dla maszyny wirtualnej. 

### <a name="create-a-logic-app-resource"></a>Tworzenie zasobu aplikacji logiki

1. W [witrynie Azure portal](https://portal.azure.com)wybierz pozycję **Utwórz zasób**, a następnie wpisz "aplikacja logiki" w polu wyszukiwania i wybierz polecenie Return. Wybierz **aplikację logiki** z wyników.

   ![Tworzenie aplikacji logiki](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. Na następnym ekranie wybierz pozycję **Utwórz**. 

1. Nadaj aplikacji logiki nazwę unikatową w ramach subskrypcji, a następnie wybierz tę samą subskrypcję, grupę zasobów i lokalizację, które są skojarzone z centrum IoT. 

   ![Pola do tworzenia aplikacji logiki](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Wybierz **pozycję Utwórz**.

1. Po utworzeniu zasobu przejdź do aplikacji logiki. Aby to zrobić, wybierz **grupę zasobów**, a następnie wybierz grupę zasobów utworzoną dla tego samouczka. Następnie znajdź aplikację logiki na liście zasobów i wybierz ją. 

1. W Projektancie aplikacji logiki przejrzyj stronę w dół, aby wyświetlić **pozycję Szablony**. Wybierz **pustą aplikację logiki,** dzięki czemu można tworzyć aplikację logiki od podstaw.

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

1. Wybierz pozycję **Nowy krok**. Spowoduje to otwarcie okna **wybierania akcji**.

1. Wyszukaj nazwę **E-mail**.

1. W oparciu o Twojego dostawcę poczty e-mail znajdź i wybierz zgodny łącznik. Ten samouczek używa **usługi Office 365 Outlook**. Kroki dla innych dostawców poczty e-mail są podobne. 

   ![Wybieranie łącznika dostawcy poczty e-mail](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

1. Wybierz akcję **Wyślij wiadomość e-mail**. 

1. W razie potrzeby zaloguj się do swojego konta e-mail. 

1. Utwórz szablon wiadomości e-mail. 

   * **Do**: wpisz adres e-mail, na który mają być wysyłane wiadomości e-mail z powiadomieniami. Na potrzeby tego samouczka użyj konta e-mail, które nadaje się do celów testowych. 

   * **Temat**: Wypełnij tekst tematu. Po kliknięciu pola tekstowego Temat można wybrać zawartość dynamiczną do uwzględnienia. Na przykład w tym `IoT Hub alert: {event Type}`samouczku używa . Jeśli nie widzisz zawartości dynamicznej, wybierz hiperłącze **Dodaj zawartość dynamiczną** — spowoduje to jego włączanie i wyłączanie.

   * **Treść**: Napisz tekst wiadomości e-mail. Za pomocą narzędzia do wybierania wybierz właściwości JSON w celu dołączenia zawartości dynamicznej opartej na danych zdarzenia. Jeśli nie widzisz zawartości dynamicznej, zaznacz hiperłącze **Dodaj zawartość dynamiczną** w polu tekstowym **Treść.** Jeśli nie pokazuje chcesz pól, kliknij *więcej* na ekranie Zawartość dynamiczna, aby uwzględnić pola z poprzedniej akcji.

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

1. W witrynie Azure Portal przejdź do centrum IoT Hub. Można to zrobić, wybierając **grupy zasobów,** a następnie wybierz grupę zasobów dla tego samouczka, a następnie wybierz centrum IoT z listy zasobów.

2. Wybierz **opcję Zdarzenia**.

   ![Wyświetlanie szczegółów usługi Event Grid](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Wybierz **subskrypcję zdarzenia**. 

   ![Tworzenie nowej subskrypcji zdarzeń](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Utwórz subskrypcję zdarzeń, korzystając z następujących wartości: 

   * **Szczegóły subskrypcji zdarzeń:** Podaj opisową nazwę i wybierz **schemat siatki zdarzeń**.

   * **Typy zdarzeń:** W **filtrze do typów zdarzeń**odznacz wszystkie opcje z wyjątkiem **utworzonego urządzenia**.

       ![typy zdarzeń subskrypcji](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)

   * **Szczegóły punktu końcowego:** Wybierz typ punktu końcowego jako **hak sieci Web** i wybierz pozycję Wybierz punkt *końcowy* i wklej adres URL skopiowany z aplikacji logiki i potwierdź wybór.

     ![wybieranie adresu url punktu końcowego](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

   Po zakończeniu okienko powinno wyglądać następująco: 

    ![Przykładowy formularz subskrypcji zdarzeń](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Możesz teraz zapisać subskrypcję zdarzeń, aby otrzymywać powiadomienia o wszystkich urządzeniach tworzonych w centrum IoT Hub. Jednak w tym samouczku użyjemy opcjonalnych filtrów, aby odfiltrować konkretne urządzenia. Wybierz **pozycję Filtry** w górnej części okienka.

6. Wybierz **pozycję Dodaj nowy filtr**. Wypełnij pola tymi wartościami:

   * **Klucz**: `Subject`Wybierz .

   * **Operator**: `String begins with`Wybierz .

   * **Wartość**: `devices/Building1_` Wprowadź, aby filtrować zdarzenia urządzenia w budynku 1.
  
   Dodaj kolejny filtr z tymi wartościami:

   * **Klucz**: `Subject`Wybierz .

   * **Operator**: `String ends with`Wybierz .

   * **Wartość**: `_Temperature` Wprowadź, aby filtrować zdarzenia urządzenia związane z temperaturą.

   Karta **Filtry** subskrypcji zdarzeń powinna teraz wyglądać podobnie do tego obrazu:

   ![Dodawanie filtrów do subskrypcji zdarzeń](./media/publish-iot-hub-events-to-logic-apps/event-subscription-filters.png)

7. Wybierz pozycję **Utwórz**, aby zapisać subskrypcję zdarzeń.

## <a name="create-a-new-device"></a>Tworzenie nowego urządzenia

Aby przetestować aplikację logiki, utwórz nowe urządzenie w celu wyzwolenia wiadomości e-mail z powiadomieniem o zdarzeniu. 

1. W centrum IoT Hub wybierz pozycję **Urządzenia IoT**. 

2. Wybierz **pozycję Nowy**.

3. W polu **Identyfikator urządzenia** wpisz ciąg `Building1_Floor1_Room1_Light`.

4. Wybierz **pozycję Zapisz**. 

5. Aby przetestować filtry subskrypcji zdarzeń, możesz dodać wiele urządzeń z różnymi identyfikatorami. Wypróbuj te przykłady: 

   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

   Jeśli dodano cztery przykłady, lista urządzeń IoT powinna wyglądać następująco:

   ![Lista urządzeń Centrum IoT](./media/publish-iot-hub-events-to-logic-apps/iot-hub-device-list.png)

6. Po dodaniu kilku urządzeń do centrum IoT Hub sprawdź pocztę e-mail, aby zobaczyć, które z nich wyzwoliły działanie aplikacji logiki. 

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

Nie musisz korzystać z witryny Azure Portal — kroki w centrum IoT Hub możesz wykonać przy użyciu interfejsu wiersza polecenia platformy Azure. Aby uzyskać szczegółowe informacje, zobacz strony interfejsu wiersza polecenia platformy Azure, [aby utworzyć subskrypcję zdarzeń](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) i utworzyć urządzenie [IoT.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Użycie zasobów w tym samouczku powoduje naliczanie opłat w ramach Twojej subskrypcji platformy Azure. Po zakończeniu wypróbowania samouczka i testowania wyników wyłącz lub usuń zasoby, których nie chcesz przechowywać. 

Aby usunąć wszystkie zasoby utworzone w tym samouczku, usuń grupę zasobów. 

1. Wybierz **pozycję Grupy zasobów**, a następnie wybierz grupę zasobów utworzoną dla tego samouczka.

2. W okienku Grupa zasobów wybierz pozycję **Usuń grupę zasobów**. Zostanie wyświetlony monit o wprowadzenie nazwy grupy zasobów, a następnie można ją usunąć. Wszystkie zasoby w nim zawarte są również usuwane.

Jeśli nie chcesz usuwać wszystkich zasobów, możesz zarządzać nimi jeden po drugim. 

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