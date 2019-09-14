---
title: Wysyłanie i odbieranie zdarzeń przy użyciu środowiska Node. js — Azure Event Hubs | Microsoft Docs
description: Ten artykuł zawiera wskazówki dotyczące tworzenia aplikacji Node.js, która wysyła zdarzenia z usługi Azure Event Hubs.
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: spelluru
ms.openlocfilehash: 3bb222d3197ef37d56767300d71cc350d25a37bd
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984483"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Wysyłanie zdarzeń do i odbieranie zdarzeń z usługi Azure Event Hubs przy użyciu środowiska Node. js

Azure Event Hubs to usługa do przesyłania strumieniowego danych Big Data i usługi pozyskiwania zdarzeń, które mogą odbierać i przetwarzać miliony zdarzeń na sekundę. Usługa Event Hubs pozwala przetwarzać i przechowywać zdarzenia, dane lub dane telemetryczne generowane przez rozproszone oprogramowanie i urządzenia. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Aby zapoznać się ze szczegółowym omówieniem usługi Event Hubs, zobacz [Omówienie usługi Event Hubs](event-hubs-about.md) i [Funkcje usługi Event Hubs](event-hubs-features.md).

W tym samouczku opisano sposób tworzenia aplikacji node. js w celu wysyłania zdarzeń do zdarzeń z centrum zdarzeń lub ich odbierania.

> [!NOTE]
> Ten przewodnik Szybki start możesz pobrać jako przykład z witryny [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), zastąpić ciągi `EventHubConnectionString` i `EventHubName` wartościami swojego centrum zdarzeń, a następnie uruchomić go. Alternatywnie możesz utworzyć własne rozwiązanie, wykonując kroki opisane w tym samouczku.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

- Aktywne konto platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Środowisko node.js w wersji 8.x lub nowszy. Pobierz najnowszą wersję LTS [ https://nodejs.org ](https://nodejs.org).
- Visual Studio Code (zalecane) lub dowolnym środowiskiem IDE
- **Utwórz przestrzeń nazw Event Hubs i centrum zdarzeń**. Pierwszym krokiem jest skorzystanie z witryny [Azure Portal](https://portal.azure.com) w celu utworzenia przestrzeni nazw typu Event Hubs i uzyskania poświadczeń zarządzania wymaganych przez aplikację do komunikacji z centrum zdarzeń. Aby utworzyć obszar nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md), a następnie przejdź do poniższych kroków opisanych w tym samouczku. Następnie Pobierz parametry połączenia dla przestrzeni nazw centrum zdarzeń, postępując zgodnie z instrukcjami zawartymi w artykule: [Pobieranie parametrów połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). W dalszej części tego samouczka możesz użyć parametrów połączenia.


### <a name="install-npm-package"></a>Zainstaluj pakiet npm
Aby zainstalować [pakiet npm dla Event Hubs](https://www.npmjs.com/package/@azure/event-hubs), Otwórz wiersz polecenia, który znajduje `npm` się w jego ścieżce, Zmień katalog na folder, w którym chcesz uzyskać przykłady, a następnie Uruchom to polecenie.

```shell
npm install @azure/event-hubs
```

Aby zainstalować [pakiet npm dla hosta procesora zdarzeń](https://www.npmjs.com/package/@azure/event-processor-host), zamiast tego uruchom poniższe polecenie

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Wysyłanie zdarzeń

W tej sekcji pokazano, jak utworzyć aplikację Node. js, która wysyła zdarzenia do centrum zdarzeń. 

1. Otwórz ulubiony Edytor, taki jak [Visual Studio Code](https://code.visualstudio.com). 
2. Utwórz plik o nazwie `send.js` i wklej do niego Poniższy kod. Uzyskaj parametry połączenia dla przestrzeni nazw centrum zdarzeń, postępując zgodnie z instrukcjami opisanymi w sekcji [Pobieranie parametrów połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). 

    ```javascript
    const { EventHubClient } = require("@azure/event-hubs");

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
3. Wprowadź parametry połączenia i nazwę centrum zdarzeń w powyższym kodzie
4. Następnie uruchom polecenie `node send.js` w wierszu polecenia, aby wykonać ten plik. Spowoduje to wysłanie zdarzeń 100 do centrum zdarzeń

Gratulacje! Wysłano zdarzenia do centrum zdarzeń.


## <a name="receive-events"></a>Odbieranie zdarzeń

W tej sekcji przedstawiono sposób tworzenia aplikacji node. js, która odbiera zdarzenia z pojedynczej partycji domyślnej grupy odbiorców w centrum zdarzeń. 

1. Otwórz ulubiony Edytor, taki jak [Visual Studio Code](https://code.visualstudio.com). 
2. Utwórz plik o nazwie `receive.js` i wklej do niego Poniższy kod.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs");

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
4. Następnie uruchom polecenie `node receive.js` w wierszu polecenia, aby wykonać ten plik. Spowoduje to odebranie zdarzeń z jednej z partycji domyślnej grupy odbiorców w centrum zdarzeń

Gratulacje! Odebrano zdarzenia z centrum zdarzeń.

## <a name="receive-events-using-event-processor-host"></a>Odbieranie zdarzeń za pomocą hosta procesora zdarzeń

W tej sekcji pokazano, jak odbierać zdarzenia z centrum zdarzeń przy użyciu usługi Azure [klasy eventprocessorhost](event-hubs-event-processor-host.md) w aplikacji node. js. EventProcessorHost Udostępniający pomaga efektywnie odbieranie zdarzeń z Centrum zdarzeń, tworząc odbiorniki wszystkich partycji w grupie odbiorców Centrum zdarzeń. Jego metadanych punktów kontrolnych na odebrane komunikaty w regularnych odstępach czasu w usłudze Azure Blob Storage. Takie podejście ułatwia nadal otrzymywać wiadomości od tam, gdzie Przerwano w późniejszym czasie.

1. Otwórz ulubiony Edytor, taki jak [Visual Studio Code](https://code.visualstudio.com). 
2. Utwórz plik o nazwie `receiveAll.js` i wklej do niego Poniższy kod.
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
3. Wprowadź parametry połączenia i nazwę centrum zdarzeń w powyższym kodzie wraz z parametrami połączenia dla Blob Storage platformy Azure
4. Następnie uruchom polecenie `node receiveAll.js` w wierszu polecenia, aby wykonać ten plik.

Gratulacje! Odebrano zdarzenia z centrum zdarzeń za pomocą hosta procesora zdarzeń. Spowoduje to odebranie zdarzeń ze wszystkich partycji domyślnej grupy odbiorców w centrum zdarzeń

## <a name="next-steps"></a>Następne kroki
Przeczytaj następujące artykuły:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkcje i terminologia w usłudze Azure Event Hubs](event-hubs-features.md)
- [Event Hubs — często zadawane pytania](event-hubs-faq.md)
- Zapoznaj się z innymi przykładami środowiska Node. js dla [Event Hubs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) i [hosta procesora zdarzeń](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) w witrynie GitHub
