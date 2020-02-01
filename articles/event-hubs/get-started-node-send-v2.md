---
title: Wysyłanie lub odbieranie zdarzeń z usługi Azure Event Hubs przy użyciu środowiska Node. js (Najnowsza wersja)
description: Ten artykuł zawiera wskazówki dotyczące tworzenia aplikacji node. js, która wysyła/odbiera zdarzenia do/z usługi Azure Event Hubs przy użyciu najnowszego pakietu Azure/Event-Hub w wersji 5.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: b523e4a7b463564cbfeb407c91b7bb05317f8166
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906372"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-nodejs--azureevent-hubs-version-5"></a>Wysyłanie zdarzeń do i odbieranie zdarzeń z centrów zdarzeń przy użyciu środowiska Node. js (Azure/Event-Hubs w wersji 5)

Azure Event Hubs to platforma przesyłania strumieniowego danych Big Data i usługa pozyskiwania zdarzeń, która może odbierać i przetwarzać miliony zdarzeń na sekundę. Centra zdarzeń mogą przetwarzać i przechowywać zdarzenia, dane lub telemetrię wytwarzane przez rozproszone oprogramowanie i urządzenia. Dane wysyłane do centrum zdarzeń można przekształcać i przechowywać za pomocą dowolnego dostawcy analiz w czasie rzeczywistym lub adapterów wsadowych/magazynowych. Aby uzyskać więcej informacji, zobacz [Event Hubs Omówienie](event-hubs-about.md) i [Event Hubs funkcji](event-hubs-features.md).

Ten przewodnik Szybki Start zawiera opis sposobu tworzenia aplikacji node. js, które mogą wysyłać zdarzenia do i odbierać zdarzenia z centrum zdarzeń.

> [!IMPORTANT]
> Ten przewodnik Szybki Start używa wersji 5 zestawu SDK JavaScript platformy Azure Event Hubs. Aby zapoznać się z przewodnikiem Szybki Start, który używa wersji 2 zestawu SDK języka JavaScript, zobacz [ten artykuł](event-hubs-node-get-started-send.md). 

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego przewodnika Szybki start niezbędne jest spełnienie następujących wymagań wstępnych:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).  
- Node. js w wersji 8. x lub nowszej. Pobierz najnowszą [wersję długoterminowej pomocy technicznej (LTS)](https://nodejs.org).  
- Visual Studio Code (zalecane) lub inne zintegrowane środowisko programistyczne (IDE).  
- Aktywna przestrzeń nazw Event Hubs i centrum zdarzeń. Aby je utworzyć, wykonaj następujące czynności: 

   1. W [Azure Portal](https://portal.azure.com)Utwórz przestrzeń nazw typu *Event Hubs*, a następnie Uzyskaj poświadczenia zarządzania wymagane przez aplikację do komunikacji z centrum zdarzeń. 
   1. Aby utworzyć obszar nazw i centrum zdarzeń, wykonaj instrukcje podane w [przewodniku szybki start: tworzenie centrum zdarzeń przy użyciu Azure Portal](event-hubs-create.md).
   1. Kontynuuj, postępując zgodnie z instrukcjami wyświetlanymi w tym przewodniku Szybki Start. 
   1. Aby uzyskać parametry połączenia dla przestrzeni nazw centrum zdarzeń, postępuj zgodnie z instrukcjami w temacie [pobieranie parametrów połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Zapisz parametry połączenia do użycia w dalszej części tego przewodnika Szybki Start.

### <a name="install-the-npm-package"></a>Instalowanie pakietu npm
Aby zainstalować [pakiet npm (Node Package Manager) dla Event Hubs](https://www.npmjs.com/package/@azure/event-hubs), Otwórz wiersz polecenia, który ma *npm* w swojej ścieżce, Zmień katalog na folder, w którym chcesz przechowywać przykłady, a następnie uruchom następujące polecenie:

```shell
npm install @azure/event-hubs
```

Po stronie odbiorczej należy zainstalować dwa dodatkowe pakiety. W tym przewodniku szybki start użyjesz usługi Azure Blob Storage, aby utrwalać punkty kontrolne, dzięki czemu program nie odczytuje zdarzeń, które zostały już odczytane. Wykonuje punkty kontrolne metadanych dla odebranych komunikatów w regularnych odstępach czasu w obiekcie blob. Takie podejście ułatwia dalsze otrzymywanie komunikatów później od miejsca, w którym zostało przerwane.

Uruchom następujące polecenia:

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Wysyłanie zdarzeń

W tej sekcji utworzysz aplikację Node. js, która wysyła zdarzenia do centrum zdarzeń.

1. Otwórz ulubiony Edytor, taki jak [Visual Studio Code](https://code.visualstudio.com).
1. Utwórz plik o nazwie *send. js*i wklej do niego następujący kod:

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // Create a producer client to send messages to the event hub.
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // Prepare a batch of three events.
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // Send the batch to the event hub.
      await producer.sendBatch(batch);

      // Close the producer client.
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
1. W kodzie Użyj wartości rzeczywistych, aby zamienić następujące elementy:
    * `EVENT HUBS NAMESPACE CONNECTION STRING` 
    * `EVENT HUB NAME`
1. Uruchom `node send.js`, aby wykonać ten plik. To polecenie wysyła wsadowe trzy zdarzenia do centrum zdarzeń.
1. W Azure Portal Sprawdź, czy centrum zdarzeń odebrało komunikaty. W sekcji **metryki** Przełącz się do widoku **komunikaty** . Odśwież stronę, aby zaktualizować wykres. Wyświetlenie komunikatów może potrwać kilka sekund.

    [![sprawdzić, czy centrum zdarzeń odebrało komunikaty](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Aby uzyskać pełny kod źródłowy, w tym dodatkowe komentarze informacyjne, przejdź do [strony GitHub sendEvents. js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js).

Gratulacje! Wysłano zdarzenia do centrum zdarzeń.


## <a name="receive-events"></a>Odbieranie zdarzeń
W tej sekcji otrzymujesz zdarzenia z centrum zdarzeń przy użyciu magazynu punktów kontrolnych usługi Azure Blob Storage w aplikacji node. js. Wykonuje punkty kontrolne metadanych dla odebranych komunikatów w regularnych odstępach czasu w usłudze Azure Storage BLOB. Takie podejście ułatwia dalsze otrzymywanie komunikatów później od miejsca, w którym zostało przerwane.

### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Tworzenie konta usługi Azure Storage i kontenera obiektów BLOB
Aby utworzyć konto usługi Azure Storage i kontener obiektów blob, wykonaj następujące czynności:

1. [Utwórz konto usługi Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [Tworzenie kontenera obiektów BLOB na koncie magazynu](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [Pobierz parametry połączenia z kontem magazynu](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

Pamiętaj, aby zarejestrować parametry połączenia i nazwę kontenera do późniejszego użycia w kodzie odbioru.

### <a name="write-code-to-receive-events"></a>Pisanie kodu w celu odbierania zdarzeń

1. Otwórz ulubiony Edytor, taki jak [Visual Studio Code](https://code.visualstudio.com).
1. Utwórz plik o nazwie *Receive. js*i wklej do niego następujący kod:

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
      // Create a blob container client and a blob checkpoint store using the client.
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // Create a consumer client for the event hub by specifying the checkpoint store.
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // Subscribe to the events, and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // Update the checkpoint.
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // After 30 seconds, stop processing.
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
1. W kodzie Użyj wartości rzeczywistych, aby zastąpić następujące wartości:
    - `EVENT HUBS NAMESPACE CONNECTION STRING`
    - `EVENT HUB NAME`
    - `AZURE STORAGE CONNECTION STRING`
    - `BLOB CONTAINER NAME`
1. Uruchom `node receive.js` w wierszu polecenia, aby wykonać ten plik. Okno powinno wyświetlać komunikaty dotyczące odebranych zdarzeń.

    > [!NOTE]
    > Aby uzyskać pełny kod źródłowy, w tym dodatkowe komentarze informacyjne, przejdź do [strony GitHub receiveEventsUsingCheckpointStore. js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js).

Gratulacje! Odebrano zdarzenia z centrum zdarzeń. Program odbiorczy otrzyma zdarzenia ze wszystkich partycji domyślnej grupy odbiorców w centrum zdarzeń.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z tymi przykładami w witrynie GitHub:

- [Przykłady kodu JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [Przykłady języka TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
