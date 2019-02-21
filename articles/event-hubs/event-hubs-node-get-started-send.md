---
title: Wysyłanie zdarzeń za pomocą środowiska Node.js — Azure Event Hubs | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera wskazówki dotyczące tworzenia aplikacji Node.js, która wysyła zdarzenia z usługi Azure Event Hubs.
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 02/19/2019
ms.author: spelluru
ms.openlocfilehash: ec3182d11f1b2ffa31acd05fa1f2db695f3f2cf7
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447722"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>Wysyłanie zdarzeń do usługi Azure Event Hubs przy użyciu środowiska Node.js

Usługa Azure Event Hubs jest danych big Data, platformy i usługa przyjmowania zdarzeń, który umożliwia odbieranie wiadomości i przetwarzanie milionów zdarzeń na sekundę przesyłania strumieniowego. Usługa Event Hubs pozwala przetwarzać i przechowywać zdarzenia, dane lub dane telemetryczne generowane przez rozproszone oprogramowanie i urządzenia. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Aby zapoznać się ze szczegółowym omówieniem usługi Event Hubs, zobacz [Omówienie usługi Event Hubs](event-hubs-about.md) i [Funkcje usługi Event Hubs](event-hubs-features.md).

W tym samouczku opisano sposób wysyłania zdarzeń do Centrum zdarzeń z aplikacji napisanych w języku Node.js.

> [!NOTE]
> Ten przewodnik Szybki start możesz pobrać jako przykład z witryny [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), zastąpić ciągi `EventHubConnectionString` i `EventHubName` wartościami swojego centrum zdarzeń, a następnie uruchomić go. Alternatywnie możesz utworzyć własne rozwiązanie, wykonując kroki opisane w tym samouczku.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

- Środowisko node.js w wersji 8.x lub nowszy. Pobierz najnowszą wersję LTS [ https://nodejs.org ](https://nodejs.org).
- Visual Studio Code (zalecane) lub dowolnym środowiskiem IDE

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Tworzenie przestrzeni nazw usługi Event Hubs i centrum zdarzeń
Pierwszym krokiem jest skorzystanie z witryny [Azure Portal](https://portal.azure.com) w celu utworzenia przestrzeni nazw typu Event Hubs i uzyskania poświadczeń zarządzania wymaganych przez aplikację do komunikacji z centrum zdarzeń. Aby utworzyć obszar nazw i Centrum zdarzeń, wykonaj procedurę opisaną w [w tym artykule](event-hubs-create.md), następnie kontynuuj czynności w ramach tego samouczka.

Uzyskaj parametry połączenia dla przestrzeni nazw centrum zdarzeń, postępując zgodnie z instrukcjami opisanymi w sekcji [Pobieranie parametrów połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Te parametry połączenia będą potrzebne w dalszej części tego samouczka.

## <a name="clone-the-sample-git-repository"></a>Sklonowania przykładowego repozytorium usługi Git
Sklonowania przykładowego repozytorium Git z [GitHub](https://github.com/Azure/azure-event-hubs-node) na swojej maszynie. 

## <a name="install-nodejs-package"></a>Zainstaluj pakiet Node.js
Zainstaluj pakiet Node.js dla usługi Azure Event Hubs na swojej maszynie. 

```shell
npm install @azure/event-hubs
```

## <a name="clone-the-git-repository"></a>Klonowanie repozytorium Git
Pobierz lub sklonuj [przykładowe](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples) z usługi GitHub. 

## <a name="send-events"></a>Wysyłanie zdarzeń
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


## <a name="review-the-sample-code"></a>Przejrzyj przykładowy kod 
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

## <a name="next-steps"></a>Kolejne kroki
W tym przewodniku Szybki Start zostały wysłane wiadomości do Centrum zdarzeń za pomocą środowiska Node.js. Aby dowiedzieć się, jak odbierać zdarzenia z Centrum zdarzeń za pomocą środowiska Node.js, zobacz [odbieranie zdarzeń z Centrum zdarzeń — Node.js](event-hubs-node-get-started-receive.md)

Obejrzyj inne przykłady dla platformy Node.js dla usługi Event Hubs w witrynie [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/).
