---
title: Rozpoczynanie pracy z magazynem kolejek i Visual Studio połączone usługi (platformy ASP.NET Core) | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę przy użyciu magazynu kolejek Azure w projekcie platformy ASP.NET Core w programie Visual Studio
services: storage
author: ghogen
manager: douge
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: article
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: e56f79334aa85d9a0c81bed4f00664fee5789676
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Rozpoczynanie pracy z magazynem kolejek i Visual Studio połączone usługi (platformy ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

W tym artykule opisano, jak rozpocząć pracę przy użyciu magazynu kolejek Azure w programie Visual Studio po utworzony lub odwołanie do konta magazynu Azure w projekcie platformy ASP.NET Core za pomocą programu Visual Studio **usług połączonych** funkcji. **Usług połączonych** operacji instaluje odpowiednie pakiety NuGet dostęp do magazynu Azure do projektu i dodaje ten ciąg połączenia dla konta magazynu do plików konfiguracji projektu. (Zobacz [dokumentacji magazynu](https://azure.microsoft.com/documentation/services/storage/) ogólne informacje na temat usługi Azure Storage.)

Magazyn kolejek Azure to usługa do przechowywania dużej liczby komunikatów, które są dostępne z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionego połączenia za pomocą protokołu HTTP lub HTTPS. Pojedynczy komunikat z kolejki może być maksymalnie 64 kilobajty (KB), rozmiar, a kolejka może zawierać miliony komunikatów — maksymalnie całkowitego limitu pojemności konta magazynu. Zobacz też [Rozpoczynanie pracy z magazynem kolejek Azure przy użyciu platformy .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) szczegółowe informacje na temat programowo manipulowanie kolejek.

Aby rozpocząć pracę, należy najpierw utworzyć kolejce platformy Azure na koncie magazynu. Następnie w tym artykule przedstawiono, jak utworzyć kolejkę w języku C# i wykonać podstawowe kolejki operacji, takich jak dodawanie, modyfikowanie, Odczyt i usuwanie wiadomości w kolejce.  W kodzie użyto biblioteki klienta magazynu Azure dla platformy .NET. Aby uzyskać więcej informacji na temat platformy ASP.NET, zobacz [ASP.NET](http://www.asp.net).

Niektóre z interfejsów API usługi magazynu Azure są asynchroniczne, a kod w tym artykule przyjęto założenie, że metody asynchroniczne są używane. Zobacz [programowanie asynchroniczne](https://docs.microsoft.com/dotnet/csharp/async) Aby uzyskać więcej informacji.

## <a name="access-queues-in-code"></a>Uzyskiwanie dostępu do kolejek w kodzie

Aby uzyskać dostęp do kolejki w projektach platformy ASP.NET Core, obejmują następujące elementy w dowolnym języku C# plik źródłowy, który uzyskuje dostęp do magazynu kolejek Azure. Korzystać ze wszystkich ten kod przed kodem w kolejnych sekcjach.

1. Dodaj niezbędne `using` instrukcji:
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Pobierz `CloudStorageAccount` obiekt, który reprezentuje informacje o koncie magazynu. Można pobrać parametry połączenia magazynu i informacji o koncie magazynu z konfiguracji usługi platformy Azure, należy użyć poniższego kodu:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz `CloudQueueClient` obiektu odwołują się obiekty w koncie magazynu:

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Pobierz `CloudQueue` obiekt do odwoływania się do określonej kolejki:

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>Tworzenie kolejki w kodzie

Aby utworzyć kolejki systemu Azure w kodzie, należy wywołać "CreateIfNotExistsAsync":

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>Dodaj komunikat do kolejki

Aby wstawić komunikat do istniejącej kolejki, Utwórz nową `CloudQueueMessage` obiekt, a następnie wywołaj `AddMessageAsync` metody. A `CloudQueueMessage` obiektu można utworzyć z ciągu (w formacie UTF-8) lub tablicy bajtów.

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>Przeczytaj wiadomość w kolejce

Możesz uzyskać wgląd w komunikat z przodu kolejki bez jego usuwania z kolejki, wywołując `PeekMessageAsync` metody:

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Przeczytaj i usuwanie wiadomości w kolejce

Można usunąć kodu (dequeue) wiadomość z kolejki w dwóch krokach.

1. Wywołanie `GetMessageAsync` do pobrania następnej wiadomości w kolejce. Komunikat zwrócony z `GetMessageAsync` staje się niewidoczny dla innego kodu odczytującego komunikaty z tej kolejki. Domyślnie komunikat pozostanie niewidoczny przez 30 sekund.
1. Aby zakończyć usuwanie komunikatu z kolejki, należy wywołać `DeleteMessageAsync`.

Ten dwuetapowy proces usuwania komunikatów gwarantuje, że jeśli kod nie będzie w stanie przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu będzie w stanie uzyskać ten sam komunikat i ponowić próbę. Poniższy kod wywołania `DeleteMessageAsync` natychmiast po przetworzeniu komunikatu:

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>Dodatkowe opcje usuwania komunikatów

Istnieją dwa sposoby dostosowania pobierania komunikatów z kolejki. Po pierwsze można uzyskać komunikaty zbiorczo (do 32). Po drugie można ustawić dłuższy lub krótszy limit czasu niewidoczności, dzięki czemu kod będzie mieć więcej lub mniej czasu na pełne przetworzenie każdego komunikatu. Poniższy przykład kodu wykorzystuje `GetMessages` metodę, aby pobrać 20 komunikatów w jednym wywołaniu. Następnie przetwarza każdy komunikat przy użyciu `foreach` pętli. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu. Należy pamiętać, że pięciu minut czasomierza uruchomienia dla wszystkich wiadomości w tym samym czasie, więc po 5 minut, komunikaty, które nie zostały usunięte stają się widoczne ponownie.

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

Możesz uzyskać szacunkową liczbę komunikatów w kolejce. `FetchAttributes` Metody prosi usługę kolejki o pobranie atrybutów kolejki, w tym liczby komunikatów. `ApproximateMethodCount` Właściwość zwraca ostatnią wartość pobraną przez `FetchAttributes` bez wywoływania usługi kolejki.

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Użyj wzorca Async-Await z kolejką wspólnych interfejsów API

W tym przykładzie przedstawiono użycie async-await wzorca zawierającego typowe kolejka z interfejsów API `Async`. Gdy jest używana metoda asynchroniczna, async-await wzorzec zawiesi lokalne wykonanie do czasu ukończenia wywołania. Takie zachowanie umożliwia wykonywanie innych zadań, która pomaga uniknąć wąskich gardeł zmniejszających wydajność i poprawia ogólną szybkość reakcji aplikacji bieżącego wątku.

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

Aby usunąć kolejkę i wszystkie zawarte w niej komunikaty, wywołaj `Delete` metody dla obiekt kolejki:

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Kolejne kroki

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
