---
title: Kolejność zdarzeń połączenia urządzenia z usługi Azure IoT Hub przy użyciu usługi Azure Cosmos DB | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób kolejność i rejestrowanie zdarzeń połączenie urządzenia z usługi Azure IoT Hub przy użyciu usługi Azure Cosmos DB do utrzymania najnowszy stan połączenia
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: asrastog
ms.openlocfilehash: 77615705ade42a2afcc8e3a9f662b0551a2411fd
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582457"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Zamówienie urządzenia zdarzenia połączeń z usługi Azure IoT Hub przy użyciu usługi Azure Cosmos DB

Usługa Azure Event Grid pomoże Ci tworzyć aplikacje oparte na zdarzeniach i łatwe integrowanie zdarzeń IoT w swoje rozwiązania biznesowe. Ten artykuł przeprowadzi Instalatora, który może służyć do śledzenia i przechowywać najnowszy stan połączenia urządzenia w usłudze Cosmos DB. Firma Microsoft Użyj numeru sekwencji dostępne w połączonych urządzeń i Urządzenie odłączone zdarzeń i przechowywania najnowszy stan w usłudze Cosmos DB. Firma Microsoft zamierza użyć procedury składowanej, która jest logiki aplikacji, która jest wykonywana względem kolekcji w usłudze Cosmos DB.

Numer sekwencji jest ciąg reprezentujący liczbę szesnastkową. Porównanie ciągu służy do identyfikowania większą liczbę. Jeśli konwertujesz ciągu na wartość szesnastkową, liczba będzie równa 256-bitową liczbą. Numer sekwencyjny jest dokładnie rosnącej, a najnowszą zdarzenia będzie mieć wyższy numer niż inne zdarzenia. Jest to przydatne, jeśli częstego urządzenie nawiązuje połączenie odłącza i chcesz upewnić się, że najnowsze zdarzenie służy do wyzwalania akcji podrzędnych, zgodnie z usługi Azure Event Grid nie obsługuje określanie kolejności zdarzeń.

## <a name="prerequisites"></a>Wymagania wstępne

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz [utworzyć bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

* Aktywne konto interfejsu API SQL usługi Azure Cosmos DB. Jeśli jeden nie utworzono jeszcze, zobacz [Tworzenie konta bazy danych](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-dotnet#create-a-database-account) przewodnik.

* Kolekcja w bazie danych. Zobacz [dodania kolekcji](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-dotnet#add-a-collection) przewodnik.

* Centrum IoT Hub na platformie Azure. Jeśli jeszcze go nie masz, zobacz przewodnik [Wprowadzenie do usługi IoT Hub](../iot-hub/iot-hub-csharp-csharp-getstarted.md). 

## <a name="create-a-stored-procedure"></a>Utwórz procedurę składowaną

Najpierw utwórz procedurę składowaną i ustaw dla niej zapasowej uruchamiają logikę, porównuje liczb sekwencji zdarzeń przychodzących, która rejestruje najnowsze zdarzenie każdego urządzenia w bazie danych.

1. Wybierz interfejs API SQL Cosmos DB **Eksplorator danych** > **elementów** > **nową procedurę przechowywaną**.

   ![Utwórz procedurę składowaną](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. Wprowadź identyfikator procedury składowanej i wklej następujący kod w "treść procedury składowanej". Należy pamiętać, że ten kod, należy zastąpić istniejący kod w treści procedury składowanej. Ten kod obsługuje jeden wiersz na identyfikator urządzenia i rejestruje najnowszy stan połączenia ten identyfikator urządzenia, identyfikując najwyższy numer kolejny. 

    ```javascript
    // SAMPLE STORED PROCEDURE
    function UpdateDevice(deviceId, moduleId, hubName, connectionState, connectionStateUpdatedTime, sequenceNumber) {
      var collection = getContext().getCollection();
      var response = {};
      
      var docLink = getDocumentLink(deviceId, moduleId);

      var isAccepted = collection.readDocument(docLink, function(err, doc) {
        if (err) {
          console.log('Cannot find device ' + docLink + ' - ');
          createDocument();
        } else {
          console.log('Document Found - ');
          replaceDocument(doc);
        }
      });

      function replaceDocument(document) {
        console.log(
          'Old Seq :' +
            document.sequenceNumber +
            ' New Seq: ' +
            sequenceNumber +
            ' - '
        );
        if (sequenceNumber > document.sequenceNumber) {
          document.connectionState = connectionState;
          document.connectionStateUpdatedTime = connectionStateUpdatedTime;
          document.sequenceNumber = sequenceNumber;

          console.log('replace doc - ');

          isAccepted = collection.replaceDocument(docLink, document, function(
            err,
            updated
          ) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(updated);
            }
          });
        } else {
          getContext()
            .getResponse()
            .setBody('Old Event - current: ' + document.sequenceNumber + ' Incoming: ' + sequenceNumber);
        }
      }
      function createDocument() {
        document = {
          id: deviceId + '-' + moduleId,
          deviceId: deviceId,
          moduleId: moduleId,
          hubName: hubName,
          connectionState: connectionState,
          connectionStateUpdatedTime: connectionStateUpdatedTime,
          sequenceNumber: sequenceNumber
        };
        console.log('Add new device - ' + collection.getAltLink());
        isAccepted = collection.createDocument(
          collection.getAltLink(),
          document,
          function(err, doc) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(doc);
            }
          }
        );
      }

      function getDocumentLink(deviceId, moduleId) {
        return collection.getAltLink() + '/docs/' + deviceId + '-' + moduleId;
      }
    }
    ```

3. Zapisz procedurę składowaną: 

    ![Zapisz procedurę składowaną](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

Najpierw należy utworzyć aplikację logiki i dodać wyzwalacz usługi Event Grid, który monitoruje grupę zasobów dla maszyny wirtualnej. 

### <a name="create-a-logic-app-resource"></a>Tworzenie zasobu aplikacji logiki

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Nowy** > **Integracja** > **Aplikacja logiki**.

   ![Tworzenie aplikacji logiki](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Nadaj aplikacji logiki nazwę unikatową w ramach subskrypcji, a następnie wybierz tę samą subskrypcję, grupę zasobów i lokalizację, które są skojarzone z centrum IoT. 

3. Wybierz **Przypnij do pulpitu nawigacyjnego**, następnie wybierz **Utwórz**.

   Teraz został utworzony zasób platformy Azure dla Twojej aplikacji logiki. Po wdrożeniu Twojej aplikacji logiki na platformie Azure projektant aplikacji usługi Logic Apps pokaże szablony dla typowych wzorców, więc możesz szybciej rozpocząć pracę.

   > [!NOTE] 
   > Po wybraniu **Przypnij do pulpitu nawigacyjnego**, aplikacja logiki automatycznie zostanie otwarty w Projektancie aplikacji logiki. W przeciwnym razie możesz ręcznie znaleźć i otworzyć swoją aplikację logiki.

4. W projektancie aplikacji usługi Logic Apps w obszarze **Szablony** wybierz pozycję **Pusta aplikacja logiki**, aby rozpocząć tworzenie aplikacji logiki od podstaw.

### <a name="select-a-trigger"></a>Wybieranie wyzwalacza

Wyzwalacz to konkretne zdarzenie, które uruchamia aplikację logiki. W tym samouczku wyzwalacz, który uruchamia przepływ pracy, odbiera żądanie za pośrednictwem protokołu HTTP.  

1. Na pasku wyszukiwania łączników i wyzwalaczy wpisz ciąg **HTTP**.

2. Wybierz pozycję **Żądanie — Po odebraniu żądania HTTP** jako wyzwalacz. 

   ![Wybieranie wyzwalacza żądania HTTP](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. Wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**. 

   ![Użyj przykładowego ładunku do wygenerowania schematu](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

4. Wklej poniższy przykładowy kod JSON w polu tekstowym, a następnie wybierz pozycję **Gotowe**:

   ```json
   [{
    "id": "fbfd8ee1-cf78-74c6-dbcf-e1c58638ccbd",
    "topic":
      "/SUBSCRIPTIONS/DEMO5CDD-8DAB-4CF4-9B2F-C22E8A755472/RESOURCEGROUPS/EGTESTRG/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/MYIOTHUB",
    "subject": "devices/Demo-Device-1",
    "eventType": "Microsoft.Devices.DeviceConnected",
    "eventTime": "2018-07-03T23:20:11.6921933+00:00",
    "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
      "hubName": "MYIOTHUB",
      "deviceId": "48e44e11-1437-4907-83b1-4a8d7e89859e",
      "moduleId": ""
    },
    "dataVersion": "1",
    "metadataVersion": "1"
   }]
   ```

5. Może pojawić się wyskakujące powiadomienie **Pamiętaj, aby w żądaniu uwzględnić nagłówek Content-Type ustawiony na wartość application/json**. Można zignorować ten komunikat i przejść do następnej sekcji. 

### <a name="create-a-condition"></a>Utwórz warunek

W przepływie pracy aplikacji logiki warunki pomoc, uruchom określonych akcji po pomyślnym tego określonego warunku. Po spełnieniu warunku można zdefiniować żądaną akcję. W tym samouczku warunek jest Sprawdź, czy typ zdarzenia jest podłączone urządzenie lub urządzenia, odłączony. Ta akcja będzie można wykonać procedurę składowaną w bazie danych. 

1. Wybierz **nowy krok** następnie **elementy wbudowane** i **warunek**. 

2. Wprowadź warunek, jak pokazano poniżej, można wykonać to tylko zdarzenia podłączone urządzenia i odłączone urządzenia:

  * Wybierz wartość: **typ zdarzenia**
  * Zmień "jest równa" **kończy się ciągiem**
  * Wybierz wartość: **nected**

   ![Wprowadź warunek](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

3. Jeśli warunek jest spełniony, polecenie **Dodaj akcję**.
  
   ![Dodawanie akcji w przypadku opcji true](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

4. Wyszukiwanie usługi Cosmos DB i wybierz polecenie **Azure Cosmos DB — wykonaj procedurę składowaną**

   ![Wyszukiwania dla usługi CosmosDB](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

5. Formularz jest wypełniany dla funkcji Execute przechowywane nabywania wybierając wartości z bazy danych. Wprowadź wartość klucza partycji i parametrów, jak pokazano poniżej. 

   ![Wypełnij akcji aplikacji logiki](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Zapisz aplikację logiki. 

### <a name="copy-the-http-url"></a>Kopiowanie adresu URL HTTP

Przed opuszczeniem Projektant aplikacji logiki, skopiuj adres URL, do którego Twoja aplikacja logiki jest nasłuchiwanie wyzwalacza. Ten adres URL zostanie użyty do skonfigurowania usługi Event Grid. 

1. Kliknij pole konfiguracji wyzwalacza **Po odebraniu żądania HTTP**, aby je rozwinąć. 

2. Skopiuj wartość pola **Adres URL żądania HTTP POST**, wybierając przycisk widoczny obok tego pola. 

   ![Kopiowanie adresu URL żądania HTTP POST](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Zapisz ten adres URL — zostanie użyty w następnej sekcji. 

## <a name="configure-subscription-for-iot-hub-events"></a>Konfigurowanie subskrypcji zdarzeń usługi IoT Hub

W tej sekcji skonfigurujesz usługę IoT Hub pod kątem publikowania zdarzeń na bieżąco. 

1. W witrynie Azure Portal przejdź do centrum IoT Hub. 

2. Wybierz pozycję **Zdarzenia**.

   ![Wyświetlanie szczegółów usługi Event Grid](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. Wybierz pozycję **Subskrypcja zdarzeń**. 

   ![Tworzenie nowej subskrypcji zdarzeń](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. Utwórz subskrypcję zdarzeń, korzystając z następujących wartości: 

   * **Typ zdarzenia**: Usuń zaznaczenie pola wyboru Subskrybuj wszystkie typy zdarzeń i wybierz **urządzenie połączone** i **Urządzenie odłączone** z menu.

   * **Szczegóły punktu końcowego**: wybierz typ punktu końcowego **Webhook**, a następnie kliknij wybrany punkt końcowy, wklej adres URL skopiowany z aplikacji logiki i potwierdź wybór.

       ![wybieranie adresu url punktu końcowego](./media/iot-hub-how-to-order-connection-state-events/endpoint-url.png)

   * **Szczegóły subskrypcji zdarzeń**: wprowadź opisową nazwę, a następnie wybierz pozycję **schemat siatki zdarzeń**.
   Formularz wygląda podobnie do poniższego przykładu: 

       ![Przykładowy formularz subskrypcji zdarzeń](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

5. Wybierz pozycję **Utwórz**, aby zapisać subskrypcję zdarzeń.

## <a name="observe-events"></a>Sprawdź zdarzenia

Po skonfigurowaniu subskrypcji zdarzeń możemy przetestować, łącząc urządzenia.

### <a name="register-a-device-in-iot-hub"></a>Rejestrowanie urządzenia w usłudze IoT Hub

1. W centrum IoT Hub wybierz pozycję **Urządzenia IoT**. 

2. Wybierz pozycję **Dodaj**.

3. W polu **Identyfikator urządzenia** wpisz ciąg `Demo-Device-1`.

4. Wybierz pozycję **Zapisz**. 

5. Można dodać wiele urządzeń przy użyciu innego urządzenia identyfikatorów.

   ![Jak wynik](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Kopiuj **parametry połączenia — klucz podstawowy** do późniejszego użycia.

   ![Jak wynik](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>Uruchom symulator urządzenia Raspberry Pi

1. Użyjmy symulator internetowy urządzenia Raspberry Pi, aby zasymulować połączenie z urządzeniem.

[Uruchom symulator urządzenia Raspberry Pi](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>Uruchamianie aplikacji przykładowej na symulator internetowy urządzenia Raspberry Pi

To spowoduje wyzwolenie zdarzenia do podłączonych urządzeń.

1. W obszarze kodowania Zamień symbol zastępczy w wierszu 15 na parametry połączenia urządzenia Azure IoT Hub.

   ![Jak wynik](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. Uruchom aplikację, klikając **Uruchom**.

Powinny być widoczne następujące dane wyjściowe, dane czujników i komunikaty, które są wysyłane do usługi IoT hub.

   ![Jak wynik](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   Kliknij przycisk **zatrzymać** przestanie symulator i wyzwalacza **Urządzenie odłączone** zdarzeń.

Masz teraz uruchomić przykładową aplikację do zbierania danych z czujników i wysyłać je do Centrum IoT hub. 

### <a name="observe-events-in-cosmos-db"></a>Sprawdź zdarzenia w usłudze Cosmos DB

Wyniki wykonanych procedury składowanej widoczne w dokumencie usługi Cosmos DB. Oto, jak to wygląda. Każdy wiersz zawiera najnowszy stan połączenia urządzenia na urządzeniu.

   ![Jak wynik](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

Zamiast używania [witryny Azure portal](http://portal.azure.com), można wykonać kroki usługi IoT Hub przy użyciu wiersza polecenia platformy Azure. Aby uzyskać szczegółowe informacje, zobacz strony wiersza polecenia platformy Azure dla [Utwórz subskrypcję zdarzeń](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) i [tworzenie urządzeń IoT](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Użycie zasobów w tym samouczku powoduje naliczanie opłat w ramach Twojej subskrypcji platformy Azure. Gdy zakończysz testy w ramach tego samouczka, wyłącz lub usuń zasoby, które nie są potrzebne. 

Jeśli nie chcesz utracić efektów pracy z aplikacją logiki, nie usuwaj jej, tylko wyłącz. 

1. Przejdź do aplikacji logiki.

2. Na **Przegląd** bloku wybierz **Usuń** lub **wyłączyć**. 

Każda subskrypcja może zawierać jedno centrum IoT Hub, z którego korzystanie jest bezpłatne. Jeśli w samouczku utworzono bezpłatne centrum, nie musisz go usuwać, ponieważ nie generuje ono kosztów.

1. Przejdź do centrum IoT Hub. 

2. Na **Przegląd** bloku wybierz **Usuń**. 

Być może zdecydujesz się zachować centrum IoT Hub, ale usunąć utworzoną subskrypcję zdarzeń. 

1. W centrum IoT Hub wybierz pozycję **Event Grid**.

2. Wybierz subskrypcję zdarzeń, którą chcesz usunąć. 

3. Wybierz pozycję **Usuń**. 

Aby usunąć konto usługi Azure Cosmos DB w witrynie Azure portal, kliknij prawym przyciskiem myszy nazwę konta, a następnie kliknij przycisk **Usuń konto**. Zobacz szczegółowe instrukcje dotyczące [usuwanie konta usługi Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/manage-account#delete).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [reagowanie na zdarzenia IoT Hub przy użyciu usługi Event Grid do wyzwalania akcji](../iot-hub/iot-hub-event-grid.md)

* [Wypróbuj samouczek zdarzeń usługi IoT Hub](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* Dowiedz się więcej o tym, co jeszcze można zrobić za pomocą [usługi Event Grid](../event-grid/overview.md)


