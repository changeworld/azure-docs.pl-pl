---
title: Wprowadzenie do usługi queue storage i Visual Studio podłączone usługi (systemu Windows platformy ASP.NET Core) | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę, przy użyciu usługi Azure queue storage w projektach programu ASP.NET Core w programie Visual Studio
services: storage
author: ghogen
manager: douge
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: 8a91614e7dfb804e6a902967ce60f898ed0e54ad
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60508477"
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Wprowadzenie do usługi queue storage i Visual Studio podłączone usługi (systemu Windows platformy ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

W tym artykule opisano sposób rozpoczęcia pracy przy użyciu usługi Azure Queue storage w programie Visual Studio po użytkownik utworzył, lub odwołanie do konta usługi Azure storage w projektach programu ASP.NET Core przy użyciu programu Visual Studio **podłączone usługi** funkcji. **Podłączone usługi** operacji instaluje odpowiednie pakiety NuGet dostępu do magazynu platformy Azure w swoim projekcie i dodaje parametry połączenia dla konta magazynu do plików konfiguracji projektu. (Zobacz [dokumentacja usługi Storage](https://azure.microsoft.com/documentation/services/storage/) ogólne informacje o usłudze Azure Storage.)

Usługi Azure queue storage jest usługą służącą do przechowywania dużej liczby wiadomości, które są dostępne z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionych połączeń HTTP lub HTTPS. Pojedynczy komunikat z kolejki, może być maksymalnie 64 kilobajtów (KB), rozmiar, a kolejka może zawierać miliony komunikatów — maksymalnie nieprzekraczającą całkowitego limitu pojemności konta magazynu. Zobacz też [Rozpoczynanie pracy z usługą Azure Queue storage przy użyciu platformy .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) szczegółowe informacje na temat programowe operowanie kolejek.

Aby rozpocząć pracę, należy najpierw utworzyć kolejki platformy Azure w ramach konta magazynu. Następnie w tym artykule przedstawiono, jak utworzyć kolejkę w języku C# i sposobu wykonywania operacji kolejki podstawowych, takich jak dodawanie, modyfikowanie, odczytywanie i usuwanie wiadomości w kolejce.  Kod używa biblioteki klienta usługi Azure Storage dla platformy .NET. Aby uzyskać więcej informacji na temat platformy ASP.NET, zobacz [ASP.NET](https://www.asp.net).

Niektóre z interfejsów API usługi Azure Storage są asynchroniczne i kodu w tym artykule przyjęto założenie, że metody asynchroniczne są używane. Zobacz [programowania asynchronicznego](https://docs.microsoft.com/dotnet/csharp/async) Aby uzyskać więcej informacji.

## <a name="access-queues-in-code"></a>Dostęp do kolejki w kodzie

Aby uzyskać dostęp do kolejki w projektach programu ASP.NET Core, obejmują następujące elementy w dowolnym języku C# plik źródłowy, który uzyskuje dostęp do usługi Azure queue storage. Użyj wszystkich ten kod przed kodem w kolejnych sekcjach.

1. Dodaj niezbędne `using` instrukcji:
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Pobierz `CloudStorageAccount` obiekt, który reprezentuje dane konta magazynu. Aby uzyskać parametry połączenia z magazynem i informacje o koncie magazynu z konfiguracji usługi platformy Azure, użyj następującego kodu:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz `CloudQueueClient` obiektu do odwołania obiekty w ramach konta magazynu:

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Pobierz `CloudQueue` obiekt, aby odwoływać się do określonej kolejki:

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>Tworzenie kolejki w kodzie

Aby utworzyć kolejki systemu Azure w kodzie, wywołaj `CreateIfNotExistsAsync`:

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>Dodawanie komunikatu do kolejki

Aby wstawić komunikat do istniejącej kolejki, Utwórz nowy `CloudQueueMessage` obiektu, a następnie wywołaj `AddMessageAsync` metody. Element `CloudQueueMessage` obiektu można tworzyć na podstawie ciągu (w formacie UTF-8) lub tablicą bajtów.

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>Przeczytaj komunikat w kolejce

Użytkownik może wglądu do wiadomości uzyskać kolejki bez jego usuwania z kolejki, wywołując `PeekMessageAsync` metody:

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Odczyt i usuwanie komunikatu z kolejki

Kod można usunąć (kolejki) komunikatu z kolejki w dwóch etapach.

1. Wywołaj `GetMessageAsync` można pobrać następnej wiadomości w kolejce. Komunikat zwrócony z `GetMessageAsync` staje się niewidoczny dla innego kodu odczytującego komunikaty z tej kolejki. Domyślnie komunikat pozostanie niewidoczny przez 30 sekund.
1. Aby zakończyć usuwanie komunikatu z kolejki, należy wywołać `DeleteMessageAsync`.

Ten dwuetapowy proces usuwania komunikatów gwarantuje, że jeśli kod nie będzie w stanie przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu będzie w stanie uzyskać ten sam komunikat i ponowić próbę. Poniższy kod wywoła `DeleteMessageAsync` natychmiast po przetworzeniu komunikatu:

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>Dodatkowe opcje usuwania z kolejki komunikatów

Istnieją dwa sposoby dostosowania pobierania komunikatów z kolejki. Po pierwsze można uzyskać komunikaty zbiorczo (do 32). Po drugie można ustawić dłuższy lub krótszy limit czasu niewidoczności, dzięki czemu kod będzie mieć więcej lub mniej czasu na pełne przetworzenie każdego komunikatu. Poniższy przykład kodu wykorzystuje `GetMessages` metodę, aby pobrać 20 komunikatów w jednym wywołaniu. Następnie przetwarza każdy komunikat przy użyciu `foreach` pętli. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu. Należy pamiętać, że pięciu minut, który uruchamia czasomierz dla wszystkich komunikatów w tym samym czasie, więc po upływie pięciu minut komunikaty, które nie zostały usunięte stają się widoczne ponownie.

```cs
// Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
//   delete each message after processing.

foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

## <a name="get-the-queue-length"></a>Pobieranie długości kolejki

Możesz uzyskać szacunkową liczbę komunikatów w kolejce. `FetchAttributes` Metoda prosi usługę kolejki o pobranie atrybutów kolejki, w tym liczby komunikatów. `ApproximateMethodCount` Właściwość zwraca ostatnią wartość pobraną przez `FetchAttributes` metody bez wywoływania usługi kolejki.

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Użyj wzorca Async-Await z kolejką wspólnych interfejsów API

W tym przykładzie pokazano, jak używać async-await wzorca z typowymi kolejki interfejsów API, kończąc `Async`. Gdy jest używana metoda asynchroniczna, async-await wzorzec zawiesi lokalne wykonanie do momentu ukończenia wywołania. Takie zachowanie umożliwia wykonywanie innych zadań, która pomaga uniknąć problemów z wydajnością i poprawia ogólną szybkość reakcji aplikacji bieżącego wątku.

```cs
// Create a message to add to the queue.
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message.
await messageQueue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="delete-a-queue"></a>Usuwanie kolejki

Aby usunąć kolejkę i wszystkie zawarte w niej komunikaty, wywołaj `Delete` metody na obiekt kolejki:

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Kolejne kroki

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
