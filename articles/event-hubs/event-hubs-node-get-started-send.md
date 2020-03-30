---
title: Wysyłanie lub odbieranie zdarzeń z usługi Azure Event Hubs przy użyciu języka JavaScript (starsza wersja)
description: Ten artykuł zawiera przewodnik do tworzenia aplikacji JavaScript, która wysyła/odbiera zdarzenia do/z usługi Azure Event Hubs przy użyciu starego pakietu azure/event-hubs w wersji 2.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 0a4b76bd1febca864cab6060fbdbd96dd0061cff
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77162620"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-javascript-azureevent-hubs-version-2"></a>Szybki start: wysyłanie zdarzeń do centrum zdarzeń platformy Azure@azure/event-hubs lub odbieranie ich przy użyciu języka JavaScript (wersja 2)
Ten przewodnik Szybki start pokazuje, jak tworzyć aplikacje JavaScript do wysyłania zdarzeń i odbierania zdarzeń z centrum zdarzeń przy użyciu pakietu JavaScript usługi azure/event-hubs w wersji 2. 

> [!WARNING]
> Ten przewodnik Szybki start używa starego pakietu azure/event-hubs w wersji 2. Aby uzyskać przewodnik Szybki start, który używa **najnowszej wersji 5** pakietu, zobacz [Wysyłanie i odbieranie zdarzeń przy użyciu usługi azure/eventhubs w wersji 5](get-started-node-send-v2.md). Aby przenieść aplikację ze starego pakietu na nowy, zobacz [Przewodnik do migracji z usługi azure/eventhubs w wersji 1 do wersji 5](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md). 


## <a name="prerequisites"></a>Wymagania wstępne

Jeśli jesteś nowy w usłudze Azure Event Hubs, zobacz [Centrum zdarzeń omówienie](event-hubs-about.md) przed rozpoczęciem tego przewodnika Szybki start. 

Do wykonania kroków tego przewodnika Szybki start niezbędne jest spełnienie następujących wymagań wstępnych:

- **Subskrypcja platformy Microsoft Azure**. Aby korzystać z usług platformy Azure, w tym usługi Azure Event Hubs, potrzebujesz subskrypcji.  Jeśli nie masz istniejącego konta platformy Azure, możesz zarejestrować się w celu [uzyskania bezpłatnej wersji próbnej](https://azure.microsoft.com/free/) lub skorzystać z korzyści dla subskrybenta MSDN podczas [tworzenia konta.](https://azure.microsoft.com)
- Node.js w wersji 8.x lub nowszej. Pobierz najnowszą wersję [https://nodejs.org](https://nodejs.org)LTS z .
- Kod programu Visual Studio (zalecane) lub inny IDE
- **Utwórz obszar nazw Centrów zdarzeń i centrum zdarzeń**. Pierwszym krokiem jest użycie [witryny Azure Portal](https://portal.azure.com) do utworzenia obszaru nazw typu Event Hubs i uzyskania poświadczeń zarządzania, których aplikacja potrzebuje do komunikowania się z centrum zdarzeń. Aby utworzyć obszar nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule,](event-hubs-create.md)a następnie wykonaj następujące kroki opisane w tym samouczku. Następnie pobierz ciąg połączenia dla obszaru nazw centrum zdarzeń, postępując zgodnie z instrukcjami z artykułu: [Pobierz ciąg połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Te parametry połączenia będą potrzebne w dalszej części tego samouczka.


### <a name="install-npm-package"></a>Instalowanie pakietu npm
Aby zainstalować [pakiet npm dla centrów zdarzeń,](https://www.npmjs.com/package/@azure/event-hubs/v/2.1.0) `npm` otwórz wiersz polecenia, który znajduje się w jego ścieżce, zmień katalog na folder, w którym chcesz mieć próbki, a następnie uruchom to polecenie

```shell
npm install @azure/event-hubs@2
```

Aby zainstalować [pakiet npm dla hosta procesora zdarzeń,](https://www.npmjs.com/package/@azure/event-processor-host)uruchom polecenie poniżej, zamiast tego

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Wysyłanie zdarzeń

W tej sekcji pokazano, jak utworzyć aplikację JavaScript, która wysyła zdarzenia do centrum zdarzeń. 

> [!NOTE]
> Ten przewodnik Szybki start możesz pobrać jako przykład z witryny [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), zastąpić ciągi `EventHubConnectionString` i `EventHubName` wartościami swojego centrum zdarzeń, a następnie uruchomić go. Alternatywnie możesz utworzyć własne rozwiązanie, wykonując kroki opisane w tym samouczku.

1. Otwórz ulubiony edytor, na przykład [Visual Studio Code](https://code.visualstudio.com). 
2. Utwórz plik `send.js` o nazwie i wklej do niego poniższy kod. Pobierz ciąg połączenia dla obszaru nazw centrum zdarzeń, postępując zgodnie z instrukcjami z artykułu: [Pobierz parametry połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). 

    ```javascript
    const { EventHubClient } = require("@azure/event-hubs@2");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);

      for (let i = 0; i < 100; i++) {
        const eventData = {body: `Event ${i}`};
        console.log(`Sending message: ${eventData.body}`);
        await client.send(eventData);
      }

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Wprowadź parametry połączenia i nazwę Centrum zdarzeń w powyższym kodzie
4. Następnie uruchom `node send.js` polecenie w wierszu polecenia, aby wykonać ten plik. Spowoduje to wysłanie 100 zdarzeń do Centrum zdarzeń

Gratulacje! Zdarzenia zostały wysłane do centrum zdarzeń.


## <a name="receive-events"></a>Odbieranie zdarzeń

W tej sekcji pokazano, jak utworzyć aplikację JavaScript, która odbiera zdarzenia z jednej partycji domyślnej grupy odbiorców w centrum zdarzeń. 

1. Otwórz ulubiony edytor, na przykład [Visual Studio Code](https://code.visualstudio.com). 
2. Utwórz plik `receive.js` o nazwie i wklej do niego poniższy kod.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs@2");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);
      const allPartitionIds = await client.getPartitionIds();
      const firstPartitionId = allPartitionIds[0];

      const receiveHandler = client.receive(firstPartitionId, eventData => {
        console.log(`Received message: ${eventData.body} from partition ${firstPartitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await receiveHandler.stop();

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Wprowadź parametry połączenia i nazwę centrum zdarzeń w powyższym kodzie.
4. Następnie uruchom `node receive.js` polecenie w wierszu polecenia, aby wykonać ten plik. Spowoduje to wyświetlenie zdarzeń z jednej z partycji domyślnej grupy odbiorców w Centrum zdarzeń

Gratulacje! Odebrano już zdarzenia z Centrum zdarzeń.

## <a name="receive-events-using-event-processor-host"></a>Odbieranie zdarzeń za pomocą hosta procesora zdarzeń

W tej sekcji pokazano, jak odbierać zdarzenia z centrum zdarzeń przy użyciu usługi Azure [EventProcessorHost](event-hubs-event-processor-host.md) w aplikacji JavaScript. EventProcessorHost (EPH) pomaga skutecznie odbierać zdarzenia z centrum zdarzeń, tworząc odbiorniki we wszystkich partycjach w grupie konsumentów centrum zdarzeń. To punkty kontrolne metadanych na odebrane wiadomości w regularnych odstępach czasu w obiekcie blob usługi Azure Storage. Takie podejście ułatwia kontynuowanie odbierania wiadomości z miejsca, w którym zostało przerwane w późniejszym czasie.

1. Otwórz ulubiony edytor, na przykład [Visual Studio Code](https://code.visualstudio.com). 
2. Utwórz plik `receiveAll.js` o nazwie i wklej do niego poniższy kod.
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const eventHubConnectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubName = "<EVENT HUB NAME>";

    // Azure Storage connection string
    const storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=<STORAGE ACCOUNT NAME>;AccountKey=<STORAGE ACCOUNT KEY>;EndpointSuffix=core.windows.net";

    async function main() {
      const eph = EventProcessorHost.createFromConnectionString(
        "my-eph",
        storageConnectionString,
        "my-storage-container-name",
        eventHubConnectionString,
        {
          eventHubPath: eventHubName,
          onEphError: (error) => {
            console.log("[%s] Error: %O", error);
          }
        }
      );


      eph.start((context, eventData) => {
        console.log(`Received message: ${eventData.body} from partition ${context.partitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await eph.stop();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });

    ```
3. Wprowadź parametry połączenia i nazwę Centrum zdarzeń w powyższym kodzie wraz z ciągiem połączenia dla usługi Azure Blob Storage
4. Następnie uruchom `node receiveAll.js` polecenie w wierszu polecenia, aby wykonać ten plik.

Gratulacje! Zdarzenia zostały odebrane z Centrum zdarzeń przy użyciu hosta procesora zdarzeń. Spowoduje to wyświetlenie zdarzeń ze wszystkich partycji domyślnej grupy odbiorców w Centrum zdarzeń

## <a name="next-steps"></a>Następne kroki
Przeczytaj następujące artykuły:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkcje i terminologia w usłudze Azure Event Hubs](event-hubs-features.md)
- [Event Hubs — często zadawane pytania](event-hubs-faq.md)
- Zapoznaj się z innymi przykładami javascript dla [centrów zdarzeń](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) i [hosta procesora zdarzeń](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) w usłudze GitHub
