---
title: Wysyłanie zdarzeń do usługi Azure Event Hubs przy użyciu środowiska Node.js | Dokumentacja firmy Microsoft
description: Wprowadzenie do wysyłania zdarzeń do usługi Event Hubs przy użyciu środowiska Node.js.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 09/18/2018
ms.author: shvija
ms.openlocfilehash: 3497abdd571282974d85ebaa58d06b47cbe3b159
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368772"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>Wysyłanie zdarzeń do usługi Azure Event Hubs przy użyciu środowiska Node.js

Azure Event Hubs to system zarządzania zdarzeń o wysokim stopniu skalowalności, który może obsługiwać miliony zdarzeń na sekundę, dzięki czemu aplikacje mogą przetwarzać oraz analizować duże ilości danych wytworzonych przez podłączone urządzenia i innymi systemami. Po zebraniu danych do Centrum zdarzeń, może odbierać i obsługa zdarzeń z użyciem w trakcie obsługi lub funkcji przekazywania z innymi systemami analizy.

Aby dowiedzieć się więcej na temat usługi Event Hubs, zobacz [Przegląd usługi Event Hubs](event-hubs-about.md).

W tym samouczku opisano sposób wysyłania zdarzeń do Centrum zdarzeń z aplikacji napisanych w języku Node.js. Aby odbierać zdarzenia przy użyciu pakiet hosta procesora zdarzeń środowiska Node.js, zobacz [do odpowiedniego artykułu Receive](event-hubs-node-get-started-receive.md).

Kod dla tego przewodnika Szybki Start jest dostępny na [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client). 

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

- Środowisko node.js w wersji 8.x lub nowszy. Pobierz najnowszą wersję LTS [ https://nodejs.org ](https://nodejs.org).
- Aktywne konto platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][].
- Visual Studio Code (zalecane) lub dowolnym środowiskiem IDE

## <a name="create-a-namespace-and-event-hub"></a>Tworzenie przestrzeni nazw i Centrum zdarzeń
Pierwszym krokiem jest używanie witryny Azure portal do tworzenia przestrzeni nazw usługi Event Hubs z Centrum zdarzeń. Jeśli nie masz istniejącą grupę, można utworzyć te jednostki, postępując zgodnie z instrukcjami w [tworzenie przestrzeni nazw usługi Event Hubs i Centrum zdarzeń za pomocą witryny Azure portal](event-hubs-create.md).

## <a name="clone-the-sample-git-repository"></a>Sklonowania przykładowego repozytorium usługi Git
Sklonowania przykładowego repozytorium Git z [Github](https://github.com/Azure/azure-event-hubs-node) na swojej maszynie. 

## <a name="install-nodejs-package"></a>Zainstaluj pakiet Node.js
Zainstaluj pakiet Node.js dla usługi Azure Event Hubs na swojej maszynie. 

```nodejs
npm install @azure/event-hubs
```

## <a name="clone-the-git-repository"></a>Klonowanie repozytorium Git
Pobierz lub sklonuj [przykładowe](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples) z usługi Github. 

## <a name="send-events"></a>Wysyłanie zdarzeń
Zestaw SDK, które zostały sklonowane zawiera kilka przykładów, które pokazują, jak do wysyłania zdarzeń do Centrum zdarzeń za pomocą środowiska node.js. W tym przewodniku Szybki Start użyjesz **simpleSender.js** przykład. Sprawdź zdarzenia odbierane, otwórz kolejny terminala i odbieranie zdarzeń za pomocą [otrzymywać przykładowe](event-hubs-node-get-started-receive.md).

1. Otwórz projekt w programie Visual Studio Code. 
2. Utwórz plik o nazwie **ENV** w obszarze **klienta** folderu. Skopiuj i wklej przykładowy zmienne środowiskowe z **sample.env** w folderze głównym.
3. Skonfiguruj parametry połączenia Centrum zdarzeń, nazwy Centrum zdarzeń i punkt końcowy magazynu. Możesz skopiować parametry połączenia Centrum zdarzeń z **podstawowe parametry połączenia** klucza w ramach **RootManageSharedAccessKey** na stronie Centrum zdarzeń w witrynie Azure portal. Aby uzyskać szczegółowe instrukcje, zobacz [pobieranie parametrów połączenia](event-hubs-quickstart-portal.md#create-an-event-hubs-namespace).
4. W interfejsie wiersza polecenia platformy Azure, przejdź do **klienta** ścieżki folderu. Instalowanie pakietów węzła i skompiluj projekt, uruchamiając następujące polecenia:

    ```nodejs
    npm i
    npm run build
    ```
5. Rozpocznij wysyłanie zdarzeń za pomocą następującego polecenia: 

    ```nodejs
    node dist/examples/simpleSender.js
    ```


## <a name="review-the-sample-code"></a>Przejrzyj przykładowy kod 
Poniżej przedstawiono przykładowy kod do wysyłania zdarzeń do Centrum zdarzeń za pomocą środowiska node.js. Można ręcznie utworzyć plik sampleSender.js i uruchom go, aby wysyłać zdarzenia do Centrum zdarzeń. 

```nodejs
const { EventHubClient, EventPosition } = require('@azure/event-hubs');

const client = EventHubClient.createFromConnectionString(process.env["EVENTHUB_CONNECTION_STRING"], process.env["EVENTHUB_NAME"]);

async function main() {
    // NOTE: For receiving events from Azure Stream Analytics, please send Events to an EventHub where the body is a JSON object/array.
    // const eventData = { body: { "message": "Hello World" } };
    const data = { body: "Hello World 1" };
    const delivery = await client.send(data);
    console.log("message sent successfully.");
}

main().catch((err) => {
    console.log(err);
});

```

## <a name="next-steps"></a>Kolejne kroki

Zobacz następujące artykuły, aby dowiedzieć się więcej o usłudze Event Hubs:

* [Odbieranie zdarzeń za pomocą środowiska Node.js](event-hubs-node-get-started-receive.md)
* [Przykłady w witrynie GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)

<!-- Links -->
[bezpłatne konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
