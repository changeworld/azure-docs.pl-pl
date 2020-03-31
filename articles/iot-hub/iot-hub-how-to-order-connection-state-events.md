---
title: Zamawianie zdarzeń połączenia urządzenia fr Usługi Azure IoT Hub w/usługi Azure Cosmos DB
description: W tym artykule opisano sposób zamawiania i rejestrowania zdarzeń połączenia urządzenia z usługi Azure IoT Hub przy użyciu usługi Azure Cosmos DB w celu utrzymania stanu najnowszego połączenia
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: asrastog
ms.openlocfilehash: 210c2e74305ba99b4ac3a12625d0b7f5fc47ba43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954249"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Porządkowanie zdarzeń połączenia urządzenia pochodzących z usługi Azure IoT Hub przy użyciu usługi Azure Cosmos DB

Usługa Azure Event Grid ułatwia tworzenie aplikacji opartych na zdarzeniach i łatwe integrowanie zdarzeń IoT w rozwiązaniach biznesowych. W tym artykule otrzymasz od użytkownika konfiguracji, której można używać do śledzenia i przechowywania najnowszego stanu połączenia urządzenia w usłudze Cosmos DB. Użyjemy numeru sekwencyjnyego dostępnego w zdarzeniach Podłączone do urządzenia i Odłączone urządzenie i będziemy przechowywać najnowszy stan w usłudze Cosmos DB. Będziemy używać procedury składowanej, która jest logiką aplikacji, która jest wykonywana względem kolekcji w usłudze Cosmos DB.

Numer sekwencyjny jest reprezentacją ciągu liczby szesnastkowej. Można użyć porównania ciągów, aby zidentyfikować większą liczbę. Jeśli konwertujesz ciąg na hex, liczba ta będzie liczbą 256-bitową. Numer sekwencyjny jest ściśle wzrasta, a najnowsze zdarzenie będzie miało większą liczbę niż inne zdarzenia. Jest to przydatne, jeśli masz częste urządzenie łączy się i rozłącza i chcesz upewnić się, że tylko najnowsze zdarzenie jest używane do wyzwalania akcji podrzędnej, ponieważ usługa Azure Event Grid nie obsługuje zamawiania zdarzeń.

## <a name="prerequisites"></a>Wymagania wstępne

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz [utworzyć bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

* Aktywne konto interfejsu API usługi Azure Cosmos DB SQL. Jeśli jeszcze go nie utworzono, zobacz [Tworzenie konta bazy danych](../cosmos-db/create-sql-api-java.md#create-a-database-account) dla instruktażu.

* Kolekcja w bazie danych. Zobacz [Dodawanie kolekcji](../cosmos-db/create-sql-api-java.md#add-a-container) dla instruktażu. Podczas tworzenia kolekcji, `/id` użyj klucza partycji.

* Centrum IoT Hub na platformie Azure. Jeśli jeszcze go nie masz, zobacz przewodnik [Wprowadzenie do usługi IoT Hub](iot-hub-csharp-csharp-getstarted.md).

## <a name="create-a-stored-procedure"></a>Tworzenie procedury składowanej

Najpierw utwórz procedurę składowaną i ustaw ją tak, aby uruchamiała logikę, która porównuje numery sekwencji zdarzeń przychodzących i rejestruje najnowsze zdarzenie na urządzenie w bazie danych.

1. W interfejsie API SQL usługi Cosmos DB DB wybierz pozycję Elementy **Eksploratora** > **Items** > danych**Nowa procedura składowana**.

   ![Tworzenie procedury składowanej](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. Wprowadź **latestdeviceConnectionState** dla identyfikatora procedury składowanej i wklej następujące elementy w **treści Procedura składowana**. Należy zauważyć, że ten kod powinien zastąpić istniejący kod w treści procedury składowanej. Ten kod zachowuje jeden wiersz na identyfikator urządzenia i rejestruje najnowszy stan połączenia tego identyfikatora urządzenia, identyfikując najwyższy numer sekwencyjny.

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

    ![zapisz procedurę składowaną](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

Najpierw należy utworzyć aplikację logiki i dodać wyzwalacz usługi Event Grid, który monitoruje grupę zasobów dla maszyny wirtualnej.

### <a name="create-a-logic-app-resource"></a>Tworzenie zasobu aplikacji logiki

1. W [portalu Azure](https://portal.azure.com)wybierz pozycję **+Utwórz zasób**, wybierz **pozycję Integracja,** a następnie **aplikację logiki**.

   ![Tworzenie aplikacji logiki](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Nadaj aplikacji logiki nazwę unikatową w ramach subskrypcji, a następnie wybierz tę samą subskrypcję, grupę zasobów i lokalizację, które są skojarzone z centrum IoT.

   ![Nowa aplikacja logiki](./media/iot-hub-how-to-order-connection-state-events/new-logic-app.png)

3. Wybierz **pozycję Utwórz,** aby utworzyć aplikację logiki.

   Teraz został utworzony zasób platformy Azure dla Twojej aplikacji logiki. Po wdrożeniu Twojej aplikacji logiki na platformie Azure projektant aplikacji usługi Logic Apps pokaże szablony dla typowych wzorców, więc możesz szybciej rozpocząć pracę.

   > [!NOTE]
   > Aby ponownie znaleźć i otworzyć aplikację logiki, wybierz **grupę zasobów** i wybierz grupę zasobów, której używasz do tego sposobu. Następnie wybierz nową aplikację logiki. Spowoduje to otwarcie projektanta aplikacji logiki.

4. W logic app designer przewiń w prawo, aż zobaczysz typowe wyzwalacze. W obszarze **Szablony**wybierz **pozycję Pusta aplikacja logiki,** aby utworzyć aplikację logiki od podstaw.

### <a name="select-a-trigger"></a>Wybieranie wyzwalacza

Wyzwalacz to konkretne zdarzenie, które uruchamia aplikację logiki. W tym samouczku wyzwalacz, który uruchamia przepływ pracy, odbiera żądanie za pośrednictwem protokołu HTTP.

1. Na pasku wyszukiwania łączników i wyzwalaczy wpisz **http** i naciśnij klawisz Enter.

2. Wybierz pozycję **Żądanie — Po odebraniu żądania HTTP** jako wyzwalacz.

   ![Wybieranie wyzwalacza żądania HTTP](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. Wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**.

   ![Generowanie schematu za pomocą przykładowego ładunku](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

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

   ![Wklej próbkę ładunku JSON](./media/iot-hub-how-to-order-connection-state-events/paste-sample-payload.png)

5. Może pojawić się wyskakujące powiadomienie **Pamiętaj, aby w żądaniu uwzględnić nagłówek Content-Type ustawiony na wartość application/json**. Można zignorować ten komunikat i przejść do następnej sekcji.

### <a name="create-a-condition"></a>Tworzenie warunku

W przepływie pracy aplikacji logiki warunki pomagają uruchamiać określone akcje po przejściu tego określonego warunku. Po spełnieniu warunku można zdefiniować żądaną akcję. W tym samouczku warunkiem jest sprawdzenie, czy eventType jest podłączony do urządzenia lub urządzenie odłączone. Akcja będzie wykonać procedurę składowaną w bazie danych.

1. Wybierz **+ Nowy krok,** a następnie **wbudowany**, a następnie znajdź i wybierz **warunek**. Kliknij pozycję **Wybierz wartość,** a pojawi się okno z zawartością dynamiczną — polami, które można zaznaczyć. Wypełnij pola, jak pokazano poniżej, aby wykonać je tylko dla zdarzeń Podłączone do urządzenia i odłączone od urządzenia:

   * Wybierz wartość: **eventType** - wybierz tę opcję z pól w zawartości dynamicznej, które pojawiają się po kliknięciu tego pola.
   * Zmień "jest równa" do **końca .**
   * Wybierz wartość: **nected**.

     ![Warunek wypełnienia](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

2. W oknie dialogowym **if true** kliknij pozycję **Dodaj akcję**.
  
   ![Dodaj akcję, jeśli true](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

3. Wyszukaj usługę Cosmos DB i wybierz **usługę Azure Cosmos DB — wykonywanie procedury składowanej**

   ![Szukaj cosmosdb](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

4. Wypełnij **cosmosdb-connection** dla **nazwy połączenia** i wybierz wpis w tabeli, a następnie wybierz pozycję **Utwórz**. Zostanie wyświetlony panel **Wykonaj procedurę składowaną.** Wprowadź wartości pól:

   **Identyfikator bazy danych**: ToDoList

   **Identyfikator kolekcji**: Przedmioty

   **Identyfikator Sproc**: NajnowszeDeviceConnectionState

5. Wybierz **pozycję Dodaj nowy parametr**. W wyświetlonym menu rozwijanym zaznacz pola obok **klawisza partycji** i **parametrów procedury składowanej,** a następnie kliknij dowolne miejsce na ekranie; dodaje pole dla wartości klucza partycji i pole dla parametrów dla procedury składowanej.

   ![wypełnianie akcji aplikacji logiki](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Teraz wprowadź wartość klucza partycji i parametry, jak pokazano poniżej. Pamiętaj, aby umieścić w nawiasach i cudzysłowach, jak pokazano. Może być trzeba kliknąć przycisk **Dodaj zawartość dynamiczną,** aby uzyskać prawidłowe wartości, których można użyć tutaj.

   ![wypełnianie akcji aplikacji logiki](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure-2.png)

7. W górnej części okienka, w którym jest wysuwa się **komunikat Dla każdego**, w obszarze Wybierz dane **wyjściowe z poprzednich kroków**upewnij się, że jest zaznaczona **opcja Treść.**

   ![wypełnianie aplikacji logiki dla każdego](./media/iot-hub-how-to-order-connection-state-events/logicapp-foreach-body.png)

8. Zapisz aplikację logiki.

### <a name="copy-the-http-url"></a>Kopiowanie adresu URL HTTP

Przed opuszczeniem Projektanta aplikacji logiki skopiuj adres URL, który aplikacja logiki nasłuchuje dla wyzwalacza. Ten adres URL zostanie użyty do skonfigurowania usługi Event Grid.

1. Kliknij pole konfiguracji wyzwalacza **Po odebraniu żądania HTTP**, aby je rozwinąć.

2. Skopiuj wartość pola **Adres URL żądania HTTP POST**, wybierając przycisk widoczny obok tego pola.

   ![Kopiowanie adresu URL żądania HTTP POST](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Zapisz ten adres URL — zostanie użyty w następnej sekcji.

## <a name="configure-subscription-for-iot-hub-events"></a>Konfigurowanie subskrypcji zdarzeń usługi IoT Hub

W tej sekcji skonfigurujesz usługę IoT Hub pod kątem publikowania zdarzeń na bieżąco.

1. W witrynie Azure Portal przejdź do centrum IoT Hub.

2. Wybierz **opcję Zdarzenia**.

   ![Wyświetlanie szczegółów usługi Event Grid](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. Wybierz **+ subskrypcję zdarzeń**.

   ![Tworzenie nowej subskrypcji zdarzeń](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. Wypełnij **szczegóły subskrypcji zdarzeń**: Podaj opisową nazwę i wybierz schemat siatki **zdarzeń**.

5. Wypełnij pola **Typy zdarzeń.** Na liście rozwijanej wybierz tylko **urządzenie połączone** i **urządzenie odłączone** z menu. Kliknij dowolne miejsce na ekranie, aby zamknąć listę i zapisać wybrane opcje.

   ![Ustawianie typów zdarzeń do wyszukania](./media/iot-hub-how-to-order-connection-state-events/set-event-types.png)

6. W przypadku **opcji Szczegóły punktu końcowego**wybierz opcję Typ punktu **końcowego** jako hak sieci Web i kliknij wybierz punkt końcowy i wklej adres URL skopiowany z aplikacji logiki i potwierdź wybór.

   ![Wybieranie adresu URL punktu końcowego](./media/iot-hub-how-to-order-connection-state-events/endpoint-select.png)

7. Formularz powinien teraz wyglądać podobnie do następującego przykładu:

   ![Przykładowy formularz subskrypcji zdarzeń](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

   Wybierz pozycję **Utwórz**, aby zapisać subskrypcję zdarzeń.

## <a name="observe-events"></a>Obserwowanie zdarzeń

Teraz, gdy subskrypcja zdarzeń jest skonfigurowana, przetestujmy, podłączając urządzenie.

### <a name="register-a-device-in-iot-hub"></a>Rejestrowanie urządzenia w Centrum IoT

1. W centrum IoT Hub wybierz pozycję **Urządzenia IoT**.

2. Wybierz **pozycję +Dodaj** u góry okienka.

3. W polu **Identyfikator urządzenia** wpisz ciąg `Demo-Device-1`.

4. Wybierz **pozycję Zapisz**.

5. Można dodać wiele urządzeń o różnych identyfikatorach urządzeń.

   ![Urządzenia dodane do centrum](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Kliknij ponownie urządzenie; teraz zostaną wypełnione parametry połączenia i klucze. Skopiuj **ciąg połączenia — klucz podstawowy** do późniejszego użycia.

   ![ConnectionString dla urządzenia](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>Uruchom symulator Raspberry Pi

Użyjmy symulatora sieci Web Raspberry Pi do symulacji połączenia urządzenia.

[Uruchom symulator Raspberry Pi](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>Uruchamianie przykładowej aplikacji na symulatorze internetowym Raspberry Pi

Spowoduje to wyzwolenie zdarzenia podłączonego do urządzenia.

1. W obszarze kodowania zastąp symbol zastępczy w wierszu 15 ciągiem połączenia urządzenia usługi Azure IoT Hub zapisanym na końcu poprzedniej sekcji.

   ![Wklejanie w ciągu połączenia urządzenia](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. Uruchom aplikację, wybierając **pozycję Uruchom**.

Zobaczysz coś podobnego do następującego danych wyjściowych, który pokazuje dane czujnika i komunikaty, które są wysyłane do centrum IoT hub.

   ![Uruchamianie aplikacji](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   Kliknij **przycisk Zatrzymaj,** aby zatrzymać symulator i wyzwolić zdarzenie **Rozłączone urządzenie.**

Teraz uruchomisz przykładową aplikację, aby zebrać dane z czujników i wysłać je do centrum IoT Hub.

### <a name="observe-events-in-cosmos-db"></a>Obserwowanie zdarzeń w usłudze Cosmos DB

Wyniki wykonanej procedury składowanej można zobaczyć w dokumencie usługi Cosmos DB. Oto jak to wygląda. Każdy wiersz zawiera najnowszy stan połączenia urządzenia na urządzenie.

   ![Jak do wyniku](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

Zamiast korzystać z [witryny Azure portal,](https://portal.azure.com)można wykonać kroki Usługi IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure. Aby uzyskać szczegółowe informacje, zobacz strony interfejsu wiersza polecenia platformy Azure, [aby utworzyć subskrypcję zdarzeń](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) i utworzyć urządzenie [IoT.](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Użycie zasobów w tym samouczku powoduje naliczanie opłat w ramach Twojej subskrypcji platformy Azure. Po zakończeniu wypróbowania samouczka i testowania wyników wyłącz lub usuń zasoby, których nie chcesz przechowywać.

Jeśli nie chcesz utracić efektów pracy z aplikacją logiki, nie usuwaj jej, tylko wyłącz.

1. Przejdź do aplikacji logiki.

2. Na **bloku Przegląd** wybierz pozycję **Usuń** lub **Wyłącz**.

    Każda subskrypcja może zawierać jedno centrum IoT Hub, z którego korzystanie jest bezpłatne. Jeśli w samouczku utworzono bezpłatne centrum, nie musisz go usuwać, ponieważ nie generuje ono kosztów.

3. Przejdź do centrum IoT Hub.

4. Na **bloku Przegląd** wybierz pozycję **Usuń**.

    Być może zdecydujesz się zachować centrum IoT Hub, ale usunąć utworzoną subskrypcję zdarzeń.

5. W centrum IoT Hub wybierz pozycję **Event Grid**.

6. Wybierz subskrypcję zdarzeń, którą chcesz usunąć.

7. Wybierz pozycję **Usuń**.

Aby usunąć konto usługi Azure Cosmos DB z witryny Azure Portal, kliknij prawym przyciskiem myszy nazwę konta i kliknij polecenie **Usuń konto**. Zobacz szczegółowe instrukcje [dotyczące usuwania konta usługi Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/manage-account).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [reagowaniu na zdarzenia usługi IoT Hub przy użyciu siatki zdarzeń do wyzwalania akcji](../iot-hub/iot-hub-event-grid.md)

* [Wypróbuj samouczek zdarzeń usługi IoT Hub](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* Dowiedz się, co jeszcze możesz zrobić z [siatką zdarzeń](../event-grid/overview.md)