---
title: 'Szybki start: biblioteka magazynu kolejki platformy Azure w wersji 12 — JavaScript'
description: Dowiedz się, jak utworzyć kolejkę i dodać wiadomości do kolejki za pomocą biblioteki JavaScript w języku 12 kolejki platformy Azure. Następnie dowiesz się, jak odczytywać i usuwać wiadomości z kolejki. Dowiesz się również, jak usunąć kolejkę.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/13/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: 59a5308d2c0a1fa2e1f38f2fe3da3a2cc29448be
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78199788"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-javascript"></a>Szybki start: biblioteka klienta magazynu usługi Azure Queue w wersji 12 dla języka JavaScript

Wprowadzenie do biblioteki klienta usługi Azure Queue storage w wersji 12 dla języka JavaScript. Usługa Azure Queue storage to usługa do przechowywania dużej liczby wiadomości do późniejszego pobierania i przetwarzania. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Użyj biblioteki klienta magazynu usługi Azure Queue storage w wersji 12 dla języka JavaScript, aby:

* Tworzenie kolejki
* Dodawanie wiadomości do kolejki
* Wgląd do wiadomości w kolejce
* Aktualizowanie wiadomości w kolejce
* Odbieranie wiadomości z kolejki
* Usuwanie wiadomości z kolejki
* Usuwanie kolejki

[Dokumentacja](https://docs.microsoft.com/javascript/api/@azure/storage-queue/) | dokumentacji interfejsu API[Przykłady kodu źródłowego pakietu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue) | [(Menedżera pakietów węzłów)](https://www.npmjs.com/package/@azure/storage-queue) | [Samples](https://docs.microsoft.com/azure/storage/common/storage-samples-javascript?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/)
* Konto magazynu platformy Azure — [tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Bieżący [plik Node.js](https://nodejs.org/en/download/) dla systemu operacyjnego.

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji znajdziesz informacje o przygotowaniu projektu do pracy z biblioteką klienta magazynu usługi Azure Queue w wersji 12 dla języka JavaScript.

### <a name="create-the-project"></a>Tworzenie projektu

Utwórz aplikację Node.js o nazwie *queues-szybki start-v12*.

1. W oknie konsoli (takich jak cmd, PowerShell lub Bash) utwórz nowy katalog dla projektu.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Przełącz się do nowo utworzonego katalogu *kolejki-szybki start-v12.*

    ```console
    cd queues-quickstart-v12
    ```

1. Utwórz nowy plik tekstowy o nazwie *package.json*. Ten plik definiuje projekt Node.js. Zapisz ten plik w katalogu *kolejki-szybki start-v12.* Oto zawartość pliku:

    ```json
    {
        "name": "queues-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-queue SDK version 12 to interact with Azure Queue storage",
        "main": "queues-quickstart-v12.js",
        "scripts": {
            "start": "node queues-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-queue": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    Jeśli chcesz, możesz umieścić `author` swoje własne imię i nazwisko w polu.

### <a name="install-the-package"></a>Zainstaluj pakiet

Będąc jeszcze w katalogu *kolejki-szybki start-v12,* zainstaluj bibliotekę klienta magazynu usługi `npm install` Azure Queue dla pakietu JavaScript za pomocą polecenia.

```console
npm install
```

 To polecenie odczytuje plik *package.json* i instaluje bibliotekę klienta usługi Azure Queue storage w wersji 12 dla pakietu JavaScript i wszystkie biblioteki, od których zależy.

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu projektu:

1. Otwieranie kolejnego nowego pliku tekstowego w edytorze kodu
1. Dodawanie `require` wywołań w celu załadowania modułów platformy Azure i node.js
1. Tworzenie struktury programu, w tym bardzo podstawowa obsługa wyjątków

    Oto kod:

    ```javascript
    const { QueueClient } = require("@azure/storage-queue");
    const uuidv1 = require("uuid/v1");

    async function main() {
        console.log("Azure Queue storage v12 - JavaScript quickstart sample");
        // Quick start code goes here
    }

    main().then(() => console.log("\nDone")).catch((ex) => console.log(ex.message));

    ```

1. Zapisz nowy plik jako *kolejki-szybki start-v12.js* w katalogu *kolejki-szybki start-v12.*

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Model obiektu

Azure Queue Storage to usługa służąca do przechowywania dużej liczby komunikatów. Komunikat kolejki może mieć rozmiar do 64 KB. Kolejka może zawierać miliony wiadomości, do całkowitego limitu pojemności konta magazynu. Kolejki są często używane do tworzenia zaległości pracy do przetwarzania asynchronicznie. Magazyn kolejek oferuje trzy typy zasobów:

* Konto magazynu
* Kolejka na koncie magazynu
* Wiadomości w kolejce

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury magazynu kolejek](./media/storage-queues-introduction/queue1.png)

Użyj następujących klas JavaScript do interakcji z tymi zasobami:

* [QueueServiceClient](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueserviceclient): `QueueServiceClient` Umożliwia zarządzanie wszystkimi kolejkami na koncie magazynu.
* [QueueClient:](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient) `QueueClient` Klasa umożliwia zarządzanie i manipulowanie poszczególnych kolejek i jej wiadomości.
* [QueueMessage:](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queuemessage) `QueueMessage` Klasa reprezentuje poszczególne obiekty zwracane podczas wywoływania [receiveMessages](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient?view=azure-node-latest#receivemessages-queuereceivemessageoptions-) w kolejce.

## <a name="code-examples"></a>Przykłady kodu

Te przykładowe fragmenty kodu pokazują, jak wykonać następujące akcje za pomocą biblioteki klienta magazynu usługi Azure Queue dla języka JavaScript:

* [Pobieranie parametrów połączenia](#get-the-connection-string)
* [Tworzenie kolejki](#create-a-queue)
* [Dodawanie wiadomości do kolejki](#add-messages-to-a-queue)
* [Wgląd do wiadomości w kolejce](#peek-at-messages-in-a-queue)
* [Aktualizowanie wiadomości w kolejce](#update-a-message-in-a-queue)
* [Odbieranie wiadomości z kolejki](#receive-messages-from-a-queue)
* [Usuwanie wiadomości z kolejki](#delete-messages-from-a-queue)
* [Usuwanie kolejki](#delete-a-queue)

### <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

Poniższy kod pobiera parametry połączenia dla konta magazynu ze zmiennej środowiskowej utworzonej w sekcji [Konfigurowanie ciągu połączenia magazynu.](#configure-your-storage-connection-string)

Dodaj ten kod `main` wewnątrz funkcji:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be 
// closed and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-queue"></a>Tworzenie kolejki

Zdecyduj o nazwie nowej kolejki. Poniższy kod dołącza wartość UUID do nazwy kolejki, aby upewnić się, że jest unikatowa.

> [!IMPORTANT]
> Nazwy kolejek mogą zawierać tylko małe litery, cyfry i łączniki i muszą zaczynać się od litery lub liczby. Przed i za każdym łącznikiem musi znajdować się znak inny niż łącznik. Nazwa musi również zawierać od 3 do 63 znaków. Aby uzyskać więcej informacji na temat nazywania kolejek, zobacz [Nazywanie kolejek i metadanych](https://docs.microsoft.com/rest/api/storageservices/naming-queues-and-metadata).

Utwórz wystąpienie klasy [QueueClient.](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient) Następnie należy [wywołać](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#create-queuecreateoptions-) metodę tworzenia, aby utworzyć kolejkę na koncie magazynu.

Dodaj ten kod na `main` końcu funkcji:

```javascript
// Create a unique name for the queue
const queueName = "quickstart" + uuidv1();

console.log("\nCreating queue...");
console.log("\t", queueName);

// Instantiate a QueueClient which will be used to create and manipulate a queue
const queueClient = new QueueClient(AZURE_STORAGE_CONNECTION_STRING, queueName);

// Create the queue
const createQueueResponse = await queueClient.create();
console.log("Queue created, requestId:", createQueueResponse.requestId);
```

### <a name="add-messages-to-a-queue"></a>Dodawanie wiadomości do kolejki

Poniższy fragment kodu dodaje wiadomości do kolejki, wywołując [sendMessage](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) metody. Zapisuje również [QueueMessage zwrócony](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queuemessage) `sendMessage` z trzeciego wywołania. Zwrócony `sendMessageResponse` jest używany do aktualizacji zawartości wiadomości w dalszej części programu.

Dodaj ten kod na `main` końcu funkcji:

```javascript
console.log("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.sendMessage("First message");
await queueClient.sendMessage("Second message");
const sendMessageResponse = await queueClient.sendMessage("Third message");

console.log("Messages added, requestId:", sendMessageResponse.requestId);
```

### <a name="peek-at-messages-in-a-queue"></a>Wgląd do wiadomości w kolejce

Zajrzeć do wiadomości w kolejce, wywołując [metodę peekMessages.](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) Metoda `peekMessages` pobiera jeden lub więcej wiadomości z przodu kolejki, ale nie zmienia widoczności wiadomości.

Dodaj ten kod na `main` końcu funkcji:

```javascript
console.log("\nPeek at the messages in the queue...");

// Peek at messages in the queue
const peekedMessages = await queueClient.peekMessages({ numberOfMessages : 5 });

for (i = 0; i < peekedMessages.peekedMessageItems.length; i++) {
    // Display the peeked message
    console.log("\t", peekedMessages.peekedMessageItems[i].messageText);
}
```

### <a name="update-a-message-in-a-queue"></a>Aktualizowanie wiadomości w kolejce

Zaktualizuj zawartość wiadomości, wywołując [metodę updateMessage.](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--undefined---number--queueupdatemessageoptions-) Metoda `updateMessage` może zmienić limit czasu widoczności wiadomości i zawartość. Zawartość wiadomości musi być ciągiem zakodowanym w uiścić UTF-8 o rozmiarze do 64 KB. Wraz z nową zawartością, przekazać `messageId` i `popReceipt` z odpowiedzi, która została zapisana wcześniej w kodzie. Właściwości `sendMessageResponse` identyfikują komunikat, który ma być aktualizowany.

```javascript
console.log("\nUpdating the third message in the queue...");

// Update a message using the response saved when calling sendMessage earlier
updateMessageResponse = await queueClient.updateMessage(
    sendMessageResponse.messageId,
    sendMessageResponse.popReceipt,
    "Third message has been updated"
);

console.log("Message updated, requestId:", updateMessageResponse.requestId);
```

### <a name="receive-messages-from-a-queue"></a>Odbieranie wiadomości z kolejki

Pobierz wcześniej dodane wiadomości, wywołując metodę [receiveMessages.](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-)  W `numberOfMessages` polu przekaż maksymalną liczbę wiadomości do odebrania dla tego wywołania.

Dodaj ten kod na `main` końcu funkcji:

```javascript
console.log("\nReceiving messages from the queue...");

// Get messages from the queue
const receivedMessagesResponse = await queueClient.receiveMessages({ numberOfMessages : 5 });

console.log("Messages received, requestId:", receivedMessagesResponse.requestId);
```

### <a name="delete-messages-from-a-queue"></a>Usuwanie wiadomości z kolejki

Usuń wiadomości z kolejki po ich odebraniu i przetworzyniu. W takim przypadku przetwarzanie jest po prostu wyświetlanie komunikatu na konsoli.

Usuń wiadomości, wywołując metodę [deleteMessage.](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) Wszystkie wiadomości, które nie zostały jawnie usunięte, po pewnym czasie staną się ponownie widoczne w kolejce, aby uzyskać kolejną szansę na ich przetworzenie.

Dodaj ten kod na `main` końcu funkcji:

```javascript
// 'Process' and delete messages from the queue
for (i = 0; i < receivedMessagesResponse.receivedMessageItems.length; i++) {
    receivedMessage = receivedMessagesResponse.receivedMessageItems[i];

    // 'Process' the message
    console.log("\tProcessing:", receivedMessage.messageText);

    // Delete the message
    const deleteMessageResponse = await queueClient.deleteMessage(
        receivedMessage.messageId,
        receivedMessage.popReceipt
    );
    console.log("\tMessage deleted, requestId:", deleteMessageResponse.requestId);
}
```

### <a name="delete-a-queue"></a>Usuwanie kolejki

Poniższy kod czyści zasoby utworzone przez aplikację, usuwając kolejkę przy użyciu metody [usuwania.](https://docs.microsoft.com/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-)

Dodaj ten kod na `main` końcu funkcji i zapisz plik:

```javascript
// Delete the queue
console.log("\nDeleting queue...");
const deleteQueueResponse = await queueClient.delete();
console.log("Queue deleted, requestId:", deleteQueueResponse.requestId);
```

## <a name="run-the-code"></a>Uruchamianie kodu

Ta aplikacja tworzy i dodaje trzy komunikaty do kolejki platformy Azure. Kod wyświetla listę wiadomości w kolejce, a następnie pobiera je i usuwa, zanim na koniec usunie kolejkę.

W oknie konsoli przejdź do katalogu zawierającego plik *kolejki-szybki start-v12.js,* `node` a następnie wykonaj następujące polecenie, aby uruchomić aplikację.

```console
node queues-quickstart-v12.js
```

Dane wyjściowe aplikacji są podobne do następującego przykładu:

```output
Azure Queue storage v12 - JavaScript quickstart sample

Creating queue...
         quickstartc095d120-1d04-11ea-af30-090ee231305f
Queue created, requestId: 5c0bc94c-6003-011b-7c11-b13d06000000

Adding messages to the queue...
Messages added, requestId: a0390321-8003-001e-0311-b18f2c000000

Peek at the messages in the queue...
         First message
         Second message
         Third message

Updating the third message in the queue...
Message updated, requestId: cb172c9a-5003-001c-2911-b18dd6000000

Receiving messages from the queue...
Messages received, requestId: a039036f-8003-001e-4811-b18f2c000000
        Processing: First message
        Message deleted, requestId: 4a65b82b-d003-00a7-5411-b16c22000000
        Processing: Second message
        Message deleted, requestId: 4f0b2958-c003-0030-2a11-b10feb000000
        Processing: Third message has been updated
        Message deleted, requestId: 6c978fcb-5003-00b6-2711-b15b39000000

Deleting queue...
Queue deleted, requestId: 5c0bca05-6003-011b-1e11-b13d06000000

Done
```

Krok po kroku kodu w debugerze i sprawdź witryny [Azure portal](https://portal.azure.com) w trakcie całego procesu. Sprawdź swoje konto magazynu, aby sprawdzić, czy wiadomości w kolejce są tworzone i usuwane.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak utworzyć kolejkę i dodać do niej wiadomości za pomocą kodu JavaScript. Następnie nauczyłeś się podglądać, pobierać i usuwać wiadomości. Na koniec dowiedziałeś się, jak usunąć kolejkę wiadomości.

Aby uzyskać samouczki, przykłady, szybkie uruchamianie i inną dokumentację, odwiedź:

> [!div class="nextstepaction"]
> [Dokumentacja platformy Azure dla języka JavaScript](https://docs.microsoft.com/azure/javascript/)

* Aby dowiedzieć się więcej, zobacz [bibliotekę klienta kolejki usługi Azure Storage dla języka JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue).
* Aby wyświetlić więcej przykładowych aplikacji magazynu kolejki platformy Azure, przejdź do [przykładów biblioteki klienta usługi Azure Queue storage w wersji 12 JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples).
