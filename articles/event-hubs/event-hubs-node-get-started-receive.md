---
title: Odbieranie zdarzeń z usługi Azure Event Hubs przy użyciu środowiska Node.js | Dokumentacja firmy Microsoft
description: Dowiedz się, jak odbieranie zdarzeń z usługi Event Hubs przy użyciu środowiska Node.js.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 09/18/2018
ms.author: shvija
ms.openlocfilehash: 6d5b52c8a5dd0306a349cac5e67eecc809005c6f
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49429188"
---
# <a name="receive-events-from-azure-event-hubs-using-nodejs"></a>Odbieranie zdarzeń z usługi Azure Event Hubs przy użyciu środowiska Node.js

Azure Event Hubs to system zarządzania zdarzeń o wysokim stopniu skalowalności, który może obsługiwać miliony zdarzeń na sekundę, dzięki czemu aplikacje mogą przetwarzać oraz analizować duże ilości danych wytworzonych przez podłączone urządzenia i innymi systemami. Po zebraniu danych do Centrum zdarzeń, może odbierać i obsługa zdarzeń z użyciem w trakcie obsługi lub funkcji przekazywania z innymi systemami analizy. Istnieje również możliwość przechwytywania danych zdarzeń w usłudze Azure Storage lub Azure Data Lake Store, przed jego przetworzeniem.  

Aby dowiedzieć się więcej na temat usługi Event Hubs, zobacz [Przegląd usługi Event Hubs](event-hubs-about.md).

W tym samouczku pokazano, jak odbierać zdarzenia z Centrum zdarzeń za pomocą platformy Azure [EventProcessorHost](event-hubs-event-processor-host.md) w aplikacji Node.js. EventProcessorHost Udostępniający pomaga efektywnie odbieranie zdarzeń z Centrum zdarzeń, tworząc odbiorniki wszystkich partycji w grupie odbiorców Centrum zdarzeń. Jego metadanych punktów kontrolnych na odebrane komunikaty w regularnych odstępach czasu w usłudze Azure Blob Storage. Takie podejście ułatwia nadal otrzymywać wiadomości od tam, gdzie Przerwano w późniejszym czasie.

Kod dla tego przewodnika Szybki Start jest dostępny w [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/processor).

> [!NOTE]
>  Wysyłanie zdarzeń do usługi Event Hubs przy użyciu środowiska Node.js, znajduje się w artykule: [wysyłanie zdarzeń do usługi Azure Event Hubs przy użyciu środowiska Node.js](event-hubs-node-get-started-send.md). 

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

- Środowisko node.js w wersji 8.x lub nowszy. Pobierz najnowszą wersję LTS [ https://nodejs.org ](https://nodejs.org). Nie należy używać starszej wersji środowiska node.js LTS. 
- Aktywne konto platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][].

## <a name="create-a-namespace-and-event-hub"></a>Tworzenie przestrzeni nazw i Centrum zdarzeń
Pierwszym krokiem jest używanie witryny Azure portal do tworzenia przestrzeni nazw usługi Event Hubs z Centrum zdarzeń. Jeśli nie masz istniejącą grupę, można utworzyć te jednostki, postępując zgodnie z instrukcjami w [tworzenie przestrzeni nazw usługi Event Hubs i Centrum zdarzeń za pomocą witryny Azure portal](event-hubs-create.md).

## <a name="create-a-storage-account-and-container"></a>Utwórz konto magazynu i kontener
Aby używać klasy EventProcessorHost, musi mieć konto usługi Azure Storage. Informacje o stanie, takie jak dzierżawy na partycje i punkty kontrolne w strumieniu zdarzeń są współużytkowane odbiorców za pomocą kontenera usługi Azure Storage. Można utworzyć konta usługi Azure Storage, postępując zgodnie z instrukcjami wyświetlanymi w [w tym artykule](../storage/common/storage-quickstart-create-account.md).

## <a name="clone-the-git-repository"></a>Klonowanie repozytorium Git
Pobierz lub sklonuj [przykładowe](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/) z usługi Github. 

## <a name="install-the-eventprocessorhost"></a>Zainstaluj klasy EventProcessorHost
Zainstaluj EventProcessorHost dla modułu usługi Event Hubs. 

```nodejs
npm install @azure/event-processor-host
```

## <a name="receive-events-using-eventprocessorhost"></a>Odbieranie zdarzeń za pomocą klasy EventProcessorHost
Zestaw SDK, które zostały sklonowane zawiera wiele przykładów ukazujących sposoby odbieranie zdarzeń z Centrum zdarzeń za pomocą środowiska Node.js. W tym przewodniku Szybki Start użyjesz **singleEPH.js** przykład. Sprawdź zdarzenia odbierane, operacji otwórz kolejny terminala i wysyłanie zdarzeń za pomocą [wysłany przykład](event-hubs-node-get-started-send.md).

1. Otwórz projekt w programie Visual Studio Code. 
2. Utwórz plik o nazwie **ENV** w obszarze **procesora** folderu. Skopiuj i wklej przykładowy zmienne środowiskowe z **sample.env** w folderze głównym.
3. Skonfiguruj parametry połączenia Centrum zdarzeń, nazwy Centrum zdarzeń i punkt końcowy magazynu. Możesz skopiować parametry połączenia Centrum zdarzeń z **podstawowe parametry połączenia** klucza w ramach **RootManageSharedAccessKey** na stronie Centrum zdarzeń w witrynie Azure portal. Aby uzyskać szczegółowe instrukcje, zobacz [pobieranie parametrów połączenia](event-hubs-create.md#create-an-event-hubs-namespace).
4. W interfejsie wiersza polecenia platformy Azure, przejdź do **procesora** ścieżki folderu. Instalowanie pakietów węzła i skompiluj projekt, uruchamiając następujące polecenia:

    ```nodejs
    npm i
    npm run build
    ```
5. Odbieranie zdarzeń za pomocą hosta procesora zdarzeń, uruchamiając następujące polecenie:

    ```nodejs
    node dist/examples/singleEph.js
    ```

## <a name="review-the-sample-code"></a>Przejrzyj przykładowy kod 
Poniżej przedstawiono przykładowy kod, aby odbierać zdarzenia z Centrum zdarzeń za pomocą środowiska node.js. Można ręcznie utworzyć plik sampleEph.js i uruchom go, aby odbierać zdarzenia do Centrum zdarzeń. 

  ```nodejs
  const { EventProcessorHost, delay } = require("@azure/event-processor-host");

  const path = process.env.EVENTHUB_NAME;
  const storageCS = process.env.STORAGE_CONNECTION_STRING;
  const ehCS = process.env.EVENTHUB_CONNECTION_STRING;
  const storageContainerName = "test-container";
  
  async function main() {
    // Create the Event Processo Host
    const eph = EventProcessorHost.createFromConnectionString(
      EventProcessorHost.createHostName("my-host"),
      storageCS,
      storageContainerName,
      ehCS,
      {
        eventHubPath: path
      },
      onEphError: (error) => {
        console.log("This handler will notify you of any internal errors that happen " +
        "during partition and lease management: %O", error);
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

```
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

Więcej przykładów można znaleźć [tutaj](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples).


## <a name="next-steps"></a>Kolejne kroki

Odwiedź następujące strony, aby dowiedzieć się więcej o usłudze Event Hubs:

* [Wysyłanie zdarzeń za pomocą środowiska Node.js](event-hubs-go-get-started-send.md)
* [Przykłady usługi Event Hubs](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/)
* [Przechwytywanie zdarzeń do usługi Azure Storage lub Data Lake Store](event-hubs-capture-overview.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)

<!-- Links -->
[bezpłatne konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
