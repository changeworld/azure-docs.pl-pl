---
title: Wysyłanie i odbieranie zdarzeń za pomocą środowiska Node.js — Azure Event Hubs | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: e67be59e0ed78b2080986acb73a33fc87599c9d3
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65539347"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Wysyłanie zdarzeń do lub odbieranie zdarzeń z usługi Azure Event Hubs przy użyciu środowiska Node.js

Usługa Azure Event Hubs jest danych big Data, platformy i usługa przyjmowania zdarzeń, który umożliwia odbieranie wiadomości i przetwarzanie milionów zdarzeń na sekundę przesyłania strumieniowego. Usługa Event Hubs pozwala przetwarzać i przechowywać zdarzenia, dane lub dane telemetryczne generowane przez rozproszone oprogramowanie i urządzenia. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Aby zapoznać się ze szczegółowym omówieniem usługi Event Hubs, zobacz [Omówienie usługi Event Hubs](event-hubs-about.md) i [Funkcje usługi Event Hubs](event-hubs-features.md).

W tym samouczku opisano sposób tworzenia aplikacji w technologii Node.js do wysyłania zdarzeń do lub odbieranie zdarzeń z Centrum zdarzeń.

> [!NOTE]
> Ten przewodnik Szybki start możesz pobrać jako przykład z witryny [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), zastąpić ciągi `EventHubConnectionString` i `EventHubName` wartościami swojego centrum zdarzeń, a następnie uruchomić go. Alternatywnie możesz utworzyć własne rozwiązanie, wykonując kroki opisane w tym samouczku.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

- Aktywne konto platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Środowisko node.js w wersji 8.x lub nowszy. Pobierz najnowszą wersję LTS [ https://nodejs.org ](https://nodejs.org).
- Visual Studio Code (zalecane) lub dowolnym środowiskiem IDE
- **Tworzenie przestrzeni nazw usługi Event Hubs i Centrum zdarzeń**. Pierwszym krokiem jest skorzystanie z witryny [Azure Portal](https://portal.azure.com) w celu utworzenia przestrzeni nazw typu Event Hubs i uzyskania poświadczeń zarządzania wymaganych przez aplikację do komunikacji z centrum zdarzeń. Aby utworzyć obszar nazw i Centrum zdarzeń, wykonaj procedurę opisaną w [w tym artykule](event-hubs-create.md), następnie kontynuuj czynności w ramach tego samouczka. Następnie Pobierz parametry połączenia dla przestrzeni nazw Centrum zdarzeń, postępując zgodnie z instrukcjami opisanymi w artykule: [Pobieranie parametrów połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). W dalszej części tego samouczka możesz użyć parametrów połączenia.


### <a name="install-npm-package"></a>Zainstaluj pakiet npm
Aby zainstalować [pakietów Menedżera npm dla usługi Event Hubs](https://www.npmjs.com/package/@azure/event-hubs), otwórz wiersz polecenia, który ma `npm` w ścieżce, zmień katalog na folder, w którym chcesz mieć Twoich przykładów, a następnie uruchom to polecenie

```shell
npm install @azure/event-hubs
```

Aby zainstalować [pakietów Menedżera npm dla hosta procesora zdarzeń](https://www.npmjs.com/package/@azure/event-processor-host), uruchom poniższe polecenie zamiast tego

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Wysyłanie zdarzeń

W tej sekcji pokazano, jak utworzyć aplikację Node.js, która wysyła zdarzenia do Centrum zdarzeń. 

1. Otwórz ulubionym edytorze, takich jak [programu Visual Studio Code](https://code.visualstudio.com). 
2. Utwórz plik o nazwie `send.js` i Wklej poniższego kodu, które do niej.
    ```javascript
    const { EventHubClient } = require("@azure/event-hubs");

    // Define connection string and the name of the Event Hub
    const connectionString = "";
    const eventHubsName = "";

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
4. Następnie uruchom polecenie `node send.js` w wierszu polecenia do wykonania tego pliku. Spowoduje to wysłanie 100 zdarzeń do Centrum zdarzeń

Gratulacje! Wysłano zdarzeń do Centrum zdarzeń.


## <a name="receive-events"></a>Odbieranie zdarzeń

W tej sekcji pokazano, jak utworzyć aplikację Node.js, która odbiera zdarzenia z jednej partycji domyślna grupa odbiorców w ramach Centrum zdarzeń. 

1. Otwórz ulubionym edytorze, takich jak [programu Visual Studio Code](https://code.visualstudio.com). 
2. Utwórz plik o nazwie `receive.js` i Wklej poniższego kodu, które do niej.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs");

    // Define connection string and related Event Hubs entity name here
    const connectionString = "";
    const eventHubsName = "";

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
3. W powyższym kodzie, należy wprowadzić parametry połączenia i nazwę Centrum zdarzeń.
4. Następnie uruchom polecenie `node receive.js` w wierszu polecenia do wykonania tego pliku. To zostaną odebrane zdarzenia z jednej z partycji domyślna grupa odbiorców w Centrum zdarzeń

Gratulacje! Odebrano zdarzenia z Centrum zdarzeń.

## <a name="receive-events-using-event-processor-host"></a>Odbieranie zdarzeń za pomocą hosta procesora zdarzeń

W tej sekcji pokazano, jak odbierać zdarzenia z Centrum zdarzeń za pomocą platformy Azure [EventProcessorHost](event-hubs-event-processor-host.md) w aplikacji Node.js. EventProcessorHost Udostępniający pomaga efektywnie odbieranie zdarzeń z Centrum zdarzeń, tworząc odbiorniki wszystkich partycji w grupie odbiorców Centrum zdarzeń. Jego metadanych punktów kontrolnych na odebrane komunikaty w regularnych odstępach czasu w usłudze Azure Blob Storage. Takie podejście ułatwia nadal otrzymywać wiadomości od tam, gdzie Przerwano w późniejszym czasie.

1. Otwórz ulubionym edytorze, takich jak [programu Visual Studio Code](https://code.visualstudio.com). 
2. Utwórz plik o nazwie `receiveAll.js` i Wklej poniższego kodu, które do niej.
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Define connection string and related Event Hubs entity name here
    const eventHubConnectionString = "";
    const eventHubName = "";
    const storageConnectionString = "";

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
3. Wprowadź parametry połączenia i nazwę Centrum zdarzeń w powyższym kodzie wraz z parametrów połączenia dla usługi Azure Blob Storage
4. Następnie uruchom polecenie `node receiveAll.js` w wierszu polecenia do wykonania tego pliku.

Gratulacje! Odebrano zdarzenia z Centrum zdarzeń za pomocą hosta procesora zdarzeń. To zostaną odebrane zdarzenia z partycji domyślna grupa odbiorców w Centrum zdarzeń

## <a name="next-steps"></a>Kolejne kroki
Przeczytaj następujące artykuły:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkcje i terminologią dotyczącą usługi Azure Event Hubs](event-hubs-features.md)
- [Event Hubs — często zadawane pytania](event-hubs-faq.md)
- Zapoznaj się z innymi przykłady środowiska Node.js dla [usługi Event Hubs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) i [hosta procesora zdarzeń](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) w witrynie GitHub
