---
title: Wysyłanie i odbieranie zdarzeń przy użyciu środowiska Node. js — Event Hubs platformy Azure
description: Ten artykuł zawiera wskazówki dotyczące tworzenia aplikacji Node.js, która wysyła zdarzenia z usługi Azure Event Hubs.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/09/2020
ms.author: spelluru
ms.openlocfilehash: 9ea6febc781422a72ac6547338c8b21239331083
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942518"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Wysyłanie zdarzeń do i odbieranie zdarzeń z usługi Azure Event Hubs przy użyciu środowiska Node. js 

Azure Event Hubs to usługa do przesyłania strumieniowego danych Big Data i usługi pozyskiwania zdarzeń, które mogą odbierać i przetwarzać miliony zdarzeń na sekundę. Usługa Event Hubs pozwala przetwarzać i przechowywać zdarzenia, dane lub dane telemetryczne generowane przez rozproszone oprogramowanie i urządzenia. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Aby zapoznać się ze szczegółowym omówieniem usługi Event Hubs, zobacz [Omówienie usługi Event Hubs](event-hubs-about.md) i [Funkcje usługi Event Hubs](event-hubs-features.md).

W tym samouczku opisano sposób tworzenia aplikacji node. js w celu wysyłania zdarzeń do zdarzeń z centrum zdarzeń lub ich odbierania.

> [!IMPORTANT]
> Ten przewodnik Szybki Start używa wersji 5 zestawu SDK skryptu Java Event Hubs platformy Azure. Aby uzyskać szybki Start, który używa starej wersji 2 zestawu SDK skryptu Java, zobacz [ten artykuł](event-hubs-node-get-started-send.md). Jeśli używasz wersji 2 zestawu SDK, zalecamy przeprowadzenie migracji kodu do najnowszej wersji. Aby uzyskać szczegółowe informacje, zobacz [Przewodnik migracji](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

- Aktywne konto platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Środowisko node.js w wersji 8.x lub nowszy. Pobierz najnowszą wersję LTS [ https://nodejs.org ](https://nodejs.org).
- Visual Studio Code (zalecane) lub dowolnym środowiskiem IDE
- **Utwórz przestrzeń nazw Event Hubs i centrum zdarzeń**. Pierwszym krokiem jest skorzystanie z witryny [Azure Portal](https://portal.azure.com) w celu utworzenia przestrzeni nazw typu Event Hubs i uzyskania poświadczeń zarządzania wymaganych przez aplikację do komunikacji z centrum zdarzeń. Aby utworzyć obszar nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md), a następnie przejdź do poniższych kroków opisanych w tym samouczku. Następnie Pobierz parametry połączenia dla przestrzeni nazw centrum zdarzeń, wykonując instrukcje podane w artykule: [pobieranie parametrów połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Te parametry połączenia będą potrzebne w dalszej części tego samouczka.


### <a name="install-npm-packages"></a>Zainstaluj pakiety npm
Aby zainstalować [pakiet npm dla Event Hubs](https://www.npmjs.com/package/@azure/event-hubs), Otwórz wiersz polecenia, który ma `npm` w jego ścieżce, Zmień katalog na folder, w którym chcesz uzyskać przykłady, a następnie Uruchom to polecenie.

```shell
npm install @azure/event-hubs
```

Po stronie odbiorczej należy zainstalować dwa dodatkowe pakiety. W tym przewodniku szybki start użyjesz platformy Azure Blob Storage do utrwalania punktów kontrolnych, aby program nie czytał zdarzeń, które zostały już odczytane. Tworzy punkty kontrolne metadanych dla odebranych komunikatów w regularnych odstępach czasu w obiekcie blob. Takie podejście ułatwia nadal otrzymywać wiadomości od tam, gdzie Przerwano w późniejszym czasie.

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Wysyłanie zdarzeń

W tej sekcji pokazano, jak utworzyć aplikację Node. js, która wysyła zdarzenia do centrum zdarzeń. 

1. Otwórz ulubiony Edytor, taki jak [Visual Studio Code](https://code.visualstudio.com). 
2. Utwórz plik o nazwie `send.js` i wklej do niego następujący kod. 

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");
    
    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {
    
      // create a producer client to send messages to the event hub
      const producer = new EventHubProducerClient(connectionString, eventHubName);
    
      // prepare a batch of three events
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    
    
      // send the batch to the event hub
      await producer.sendBatch(batch);
    
      // close the producer client
      await producer.close();
    
      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Nie zapomnij zastąpić **parametrów połączenia** i wartości **nazw centrum zdarzeń** w kodzie. 
5. Uruchom polecenie `node send.js`, aby wykonać ten plik. Spowoduje to wysłanie partii trzech zdarzeń do centrum zdarzeń
6. W Azure Portal można sprawdzić, czy centrum zdarzeń odebrało komunikaty. Przejdź do widoku **komunikaty** w sekcji **metryki** . Odśwież stronę, aby zaktualizować wykres. Wyświetlenie komunikatów zostało odebrane może potrwać kilka sekund. 

    [![sprawdzić, czy centrum zdarzeń odebrało komunikaty](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Aby uzyskać więcej informacji o pełnym kodzie źródłowym, zobacz [ten plik w witrynie GitHub](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js)

Gratulacje! Wysłano zdarzenia do centrum zdarzeń. 


## <a name="receive-events"></a>Odbieranie zdarzeń 
W tej sekcji pokazano, jak odbierać zdarzenia z centrum zdarzeń przy użyciu magazynu punktów kontrolnych obiektów blob platformy Azure w aplikacji node. js. Jego metadanych punktów kontrolnych na odebrane komunikaty w regularnych odstępach czasu w usłudze Azure Blob Storage. Takie podejście ułatwia nadal otrzymywać wiadomości od tam, gdzie Przerwano w późniejszym czasie.

### <a name="create-an-azure-storage-and-a-blob-container"></a>Tworzenie usługi Azure Storage i kontenera obiektów BLOB
Wykonaj następujące kroki, aby utworzyć konto usługi Azure Storage w tym kontenerze obiektów BLOB. 

1. [Utwórz konto usługi Azure Storage](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal)
2. [Tworzenie kontenera obiektów BLOB](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Pobierz parametry połączenia z kontem magazynu](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Zanotuj parametry połączenia i nazwę kontenera. Będziesz ich używać w kodzie Receive. 

### <a name="write-code-to-receive-events"></a>Pisanie kodu w celu odbierania zdarzeń

1. Otwórz ulubiony Edytor, taki jak [Visual Studio Code](https://code.visualstudio.com). 
2. Utwórz plik o nazwie `receive.js` i wklej do niego następujący kod. Szczegóły można znaleźć w komentarzach do kodu. 
    ```javascript
    const { EventHubConsumerClient } = require("@azure/event-hubs");
    const { ContainerClient } = require("@azure/storage-blob");    
    const { BlobCheckpointStore } = require("@azure/eventhubs-checkpointstore-blob");
    
    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";    
    const eventHubName = "EVENT HUB NAME";
    const consumerGroup = "$Default"; // name of the default consumer group
    const storageConnectionString = "AZURE STORAGE CONNECTION STRING";
    const containerName = "BLOB CONTAINER NAME";
    
    async function main() {
      // create a blob container client and a blob checkpoint store using the client
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);
    
      // create a consumer client for the event hub by specifying the checkpoint store
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);
    
      // subscribe for the events and specify handlers for processing the events and errors. 
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // update the checkpoint 
            await context.updateCheckpoint(events[events.length - 1]);
          },
    
          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );
        
      // after 30 seconds, stop processing
      await new Promise((resolve) => {
        setTimeout(async () => {
          await subscription.close();
          await consumerClient.close();
          resolve();
        }, 30000);
      });
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });    
    ```
3. Nie zapomnij określić **następujących wartości** w kodzie: 
    - Parametry połączenia z przestrzenią nazw Event Hubs
    - Nazwa centrum zdarzeń
    - Parametry połączenia z kontem usługi Azure Storage
    - Nazwa kontenera obiektów BLOB
5. Następnie uruchom polecenie `node receive.js` w wierszu polecenia, aby wykonać ten plik. Komunikaty o odebranych zdarzeniach powinny być wyświetlane w oknie. 

    > [!NOTE]
    > Aby uzyskać więcej informacji, zobacz [ten plik w witrynie GitHub](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js).

Gratulacje! Odebrano zdarzenia z centrum zdarzeń. Program odbiorczy otrzyma zdarzenia ze wszystkich partycji domyślnej grupy odbiorców w centrum zdarzeń

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z tymi przykładami w witrynie GitHub:

- [Przykłady kodu JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [Przykłady języka TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)