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
ms.openlocfilehash: f03bfde8f7ea37989756ad47678369e94b831438
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60203231"
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
- **Tworzenie przestrzeni nazw usługi Event Hubs i Centrum zdarzeń**. Pierwszym krokiem jest skorzystanie z witryny [Azure Portal](https://portal.azure.com) w celu utworzenia przestrzeni nazw typu Event Hubs i uzyskania poświadczeń zarządzania wymaganych przez aplikację do komunikacji z centrum zdarzeń. Aby utworzyć obszar nazw i Centrum zdarzeń, wykonaj procedurę opisaną w [w tym artykule](event-hubs-create.md), następnie kontynuuj czynności w ramach tego samouczka. Następnie Pobierz parametry połączenia dla przestrzeni nazw Centrum zdarzeń, postępując zgodnie z instrukcjami opisanymi w artykule: [Pobieranie parametrów połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Te parametry połączenia będą potrzebne w dalszej części tego samouczka.
- Klonuj [przykładowym repozytorium GitHub](https://github.com/Azure/azure-event-hubs-node) na swojej maszynie. 


## <a name="send-events"></a>Wysyłanie zdarzeń
W tej sekcji pokazano, jak utworzyć aplikację Node.js, która wysyła zdarzenia do Centrum zdarzeń. 

### <a name="install-nodejs-package"></a>Zainstaluj pakiet Node.js
Zainstaluj pakiet Node.js dla usługi Azure Event Hubs na swojej maszynie. 

```shell
npm install @azure/event-hubs
```

Jeśli jeszcze nie zostało sklonowane repozytorium Git, jak wspomniano w sekcji wymagań wstępnych, Pobierz [przykładowe](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples) z usługi GitHub. 

Zestaw SDK, które zostały sklonowane zawiera kilka przykładów, które pokazują, jak do wysyłania zdarzeń do Centrum zdarzeń za pomocą środowiska node.js. W tym przewodniku Szybki Start użyjesz **simpleSender.js** przykład. Sprawdź zdarzenia odbierane, otwórz kolejny terminala i odbieranie zdarzeń za pomocą [otrzymywać przykładowe](event-hubs-node-get-started-receive.md).

1. Otwórz projekt w programie Visual Studio Code. 
2. Utwórz plik o nazwie **ENV** w obszarze **klienta** folderu. Skopiuj i wklej przykładowy zmienne środowiskowe z **sample.env** w folderze głównym.
3. Skonfiguruj parametry połączenia Centrum zdarzeń, nazwy Centrum zdarzeń i punkt końcowy magazynu. Aby uzyskać instrukcje dotyczące pobierania parametrów połączenia dla Centrum zdarzeń [pobieranie parametrów połączenia](event-hubs-create.md#create-an-event-hubs-namespace).
4. W interfejsie wiersza polecenia platformy Azure, przejdź do **klienta** ścieżki folderu. Instalowanie pakietów węzła i skompiluj projekt, uruchamiając następujące polecenia:

    ```shell
    npm i
    npm run build
    ```
5. Rozpocznij wysyłanie zdarzeń za pomocą następującego polecenia: 

    ```shell
    node dist/examples/simpleSender.js
    ```


### <a name="review-the-sample-code"></a>Przejrzyj przykładowy kod 
Przejrzyj przykładowy kod w pliku simpleSender.js do wysyłania zdarzeń do Centrum zdarzeń.

```javascript
"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
const lib_1 = require("../lib");
const dotenv = require("dotenv");
dotenv.config();
const connectionString = "EVENTHUB_CONNECTION_STRING";
const entityPath = "EVENTHUB_NAME";
const str = process.env[connectionString] || "";
const path = process.env[entityPath] || "";

async function main() {
    const client = lib_1.EventHubClient.createFromConnectionString(str, path);
    const data = {
        body: "Hello World!!"
    };
    const delivery = await client.send(data);
    console.log(">>> Sent the message successfully: ", delivery.tag.toString());
    console.log(delivery);
    console.log("Calling rhea-promise sender close directly. This should result in sender getting reconnected.");
    await Object.values(client._context.senders)[0]._sender.close();
    // await client.close();
}

main().catch((err) => {
    console.log("error: ", err);
});

```

Pamiętaj, aby ustawić zmienne środowiskowe przed uruchomieniem skryptu. Możesz skonfigurować je w wiersza polecenia, jak pokazano w poniższym przykładzie, lub użyj [pakietu dotenv](https://www.npmjs.com/package/dotenv#dotenv). 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

## <a name="receive-events"></a>Odbieranie zdarzeń
W tym samouczku pokazano, jak odbierać zdarzenia z Centrum zdarzeń za pomocą platformy Azure [EventProcessorHost](event-hubs-event-processor-host.md) w aplikacji Node.js. EventProcessorHost Udostępniający pomaga efektywnie odbieranie zdarzeń z Centrum zdarzeń, tworząc odbiorniki wszystkich partycji w grupie odbiorców Centrum zdarzeń. Jego metadanych punktów kontrolnych na odebrane komunikaty w regularnych odstępach czasu w usłudze Azure Blob Storage. Takie podejście ułatwia nadal otrzymywać wiadomości od tam, gdzie Przerwano w późniejszym czasie.

Kod dla tego przewodnika Szybki Start jest dostępny w [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/processor).

### <a name="clone-the-git-repository"></a>Klonowanie repozytorium Git
Pobierz lub sklonuj [przykładowe](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/) z usługi GitHub. 

### <a name="install-the-eventprocessorhost"></a>Zainstaluj klasy EventProcessorHost
Zainstaluj EventProcessorHost dla modułu usługi Event Hubs. 

```shell
npm install @azure/event-processor-host
```

### <a name="receive-events-using-eventprocessorhost"></a>Odbieranie zdarzeń za pomocą klasy EventProcessorHost
Zestaw SDK, które zostały sklonowane zawiera wiele przykładów ukazujących sposoby odbieranie zdarzeń z Centrum zdarzeń za pomocą środowiska Node.js. W tym przewodniku Szybki Start użyjesz **singleEPH.js** przykład. Sprawdź zdarzenia odbierane, operacji otwórz kolejny terminala i wysyłanie zdarzeń za pomocą [wysłany przykład](event-hubs-node-get-started-send.md).

1. Otwórz projekt w programie Visual Studio Code. 
2. Utwórz plik o nazwie **ENV** w obszarze **procesora** folderu. Skopiuj i wklej przykładowy zmienne środowiskowe z **sample.env** w folderze głównym.
3. Skonfiguruj parametry połączenia Centrum zdarzeń, nazwy Centrum zdarzeń i punkt końcowy magazynu. Możesz skopiować parametry połączenia Centrum zdarzeń z **podstawowe parametry połączenia** klucza w ramach **RootManageSharedAccessKey** na stronie Centrum zdarzeń w witrynie Azure portal. Aby uzyskać szczegółowe instrukcje, zobacz [pobieranie parametrów połączenia](event-hubs-create.md#create-an-event-hubs-namespace).
4. W interfejsie wiersza polecenia platformy Azure, przejdź do **procesora** ścieżki folderu. Instalowanie pakietów węzła i skompiluj projekt, uruchamiając następujące polecenia:

    ```shell
    npm i
    npm run build
    ```
5. Odbieranie zdarzeń za pomocą hosta procesora zdarzeń, uruchamiając następujące polecenie:

    ```shell
    node dist/examples/singleEph.js
    ```

### <a name="review-the-sample-code"></a>Przejrzyj przykładowy kod 
Poniżej przedstawiono przykładowy kod, aby odbierać zdarzenia z Centrum zdarzeń za pomocą środowiska node.js. Można ręcznie utworzyć plik sampleEph.js i uruchom go, aby odbierać zdarzenia do Centrum zdarzeń. 

  ```javascript
  const { EventProcessorHost, delay } = require("@azure/event-processor-host");

  const path = process.env.EVENTHUB_NAME;
  const storageCS = process.env.STORAGE_CONNECTION_STRING;
  const ehCS = process.env.EVENTHUB_CONNECTION_STRING;
  const storageContainerName = "test-container";
  
  async function main() {
    // Create the Event Processor Host
    const eph = EventProcessorHost.createFromConnectionString(
      EventProcessorHost.createHostName("my-host"),
      storageCS,
      storageContainerName,
      ehCS,
      {
        eventHubPath: path,
        onEphError: (error) => {
          console.log("This handler will notify you of any internal errors that happen " +
          "during partition and lease management: %O", error);
        }
      }
    );
    let count = 0;
    // Message event handler
    const onMessage = async (context/*PartitionContext*/, data /*EventData*/) => {
      console.log(">>>>> Rx message from '%s': '%s'", context.partitionId, data.body);
      count++;
      // let us checkpoint every 100th message that is received across all the partitions.
      if (count % 100 === 0) {
        return await context.checkpoint();
      }
    };
    // Error event handler
    const onError = (error) => {
      console.log(">>>>> Received Error: %O", error);
    };
    // start the EPH
    await eph.start(onMessage, onError);
    // After some time let' say 2 minutes
    await delay(120000);
    // This will stop the EPH.
    await eph.stop();
  }
  
  main().catch((err) => {
    console.log(err);
  });
      
  ```

Pamiętaj, aby ustawić zmienne środowiskowe przed uruchomieniem skryptu. Możesz skonfigurować w wierszu polecenia, jak pokazano w poniższym przykładzie, lub użyj [pakietu dotenv](https://www.npmjs.com/package/dotenv#dotenv). 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

Więcej przykładów można znaleźć [tutaj](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples).


## <a name="next-steps"></a>Kolejne kroki
Przeczytaj następujące artykuły:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkcje i terminologią dotyczącą usługi Azure Event Hubs](event-hubs-features.md)
- [Event Hubs — często zadawane pytania](event-hubs-faq.md)
- Obejrzyj inne przykłady dla platformy Node.js dla usługi Event Hubs w witrynie [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/).
