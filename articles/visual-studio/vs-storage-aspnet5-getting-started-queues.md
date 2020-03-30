---
title: Wprowadzenie do magazynu kolejek przy użyciu programu Visual Studio (ASP.NET Core)
description: Jak rozpocząć korzystanie z magazynu kolejek platformy Azure w projekcie ASP.NET Core w programie Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 11/14/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5cdf6f2644788674df91b533c9444fc88ab30b09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72300021"
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Wprowadzenie do magazynu kolejek i usług połączonych z programem Visual Studio (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

W tym artykule opisano, jak rozpocząć korzystanie z magazynu kolejki platformy Azure w programie Visual Studio po utworzeniu konta magazynu platformy Azure lub odwoływaniu się do niego w projekcie ASP.NET Core przy użyciu funkcji **Połączonych usług** programu Visual Studio. **Operacja Połączone usługi** instaluje odpowiednie pakiety NuGet, aby uzyskać dostęp do magazynu platformy Azure w projekcie i dodaje parametry połączenia dla konta magazynu do plików konfiguracji projektu. (Zobacz [dokumentację magazynu, aby](https://azure.microsoft.com/documentation/services/storage/) uzyskać ogólne informacje na temat usługi Azure Storage).

Usługa Azure queue storage to usługa do przechowywania dużej liczby wiadomości, do których można uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionych wywołań przy użyciu protokołu HTTP lub HTTPS. Pojedyncza kolejka może mieć rozmiar do 64 kilobajtów (KB), a kolejka może zawierać miliony wiadomości, do całkowitego limitu pojemności konta magazynu. Zobacz też [Wprowadzenie do usługi Azure Queue storage przy użyciu platformy .NET, aby](../storage/queues/storage-dotnet-how-to-use-queues.md) uzyskać szczegółowe informacje na temat programowego manipulowania kolejkami.

Aby rozpocząć, najpierw utwórz kolejkę platformy Azure na koncie magazynu. W tym artykule pokazano, jak utworzyć kolejkę w języku C# i jak wykonywać podstawowe operacje kolejki, takie jak dodawanie, modyfikowanie, czytanie i usuwanie wiadomości kolejki.  Kod używa biblioteki klienta usługi Azure Storage dla platformy .NET. Aby uzyskać więcej informacji na temat ASP.NET, zobacz [ASP.NET](https://www.asp.net).

Niektóre interfejsy API usługi Azure Storage są asynchroniczne, a kod w tym artykule zakłada, że używane są metody asynchroniczne. Aby uzyskać więcej informacji, zobacz [programowanie asynchroniczne.](https://docs.microsoft.com/dotnet/csharp/async)

## <a name="access-queues-in-code"></a>Kolejki dostępu w kodzie

Aby uzyskać dostęp do kolejek w projektach ASP.NET Core, dołącz następujące elementy w dowolnym pliku źródłowym języka C#, który uzyskuje dostęp do magazynu kolejki platformy Azure. Użyj całego tego kodu przed kodem w sekcjach, które należy wykonać.

1. Dodaj niezbędne `using` instrukcje:
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Pobierz `CloudStorageAccount` obiekt, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu, aby uzyskać parametry połączenia magazynu i informacje o koncie magazynu z konfiguracji usługi platformy Azure:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Pobierz `CloudQueueClient` obiekt, aby odwoływać się do obiektów kolejki na koncie magazynu:

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Pobierz `CloudQueue` obiekt, aby odwołać się do określonej kolejki:

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>Tworzenie kolejki w kodzie

Aby utworzyć kolejkę platformy `CreateIfNotExistsAsync`Azure w kodzie, zadzwoń:

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>Dodawanie wiadomości do kolejki

Aby wstawić wiadomość do istniejącej `CloudQueueMessage` kolejki, utwórz `AddMessageAsync` nowy obiekt, a następnie wywołaj metodę. Obiekt `CloudQueueMessage` może być utworzony z ciągu (w formacie UTF-8) lub tablicy bajtowej.

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>Czytanie wiadomości w kolejce

Możesz zajrzeć do wiadomości z przodu kolejki bez usuwania go z `PeekMessageAsync` kolejki, wywołując metodę:

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Odczytywanie i usuwanie wiadomości w kolejce

Kod można usunąć (dequeue) wiadomość z kolejki w dwóch krokach.

1. Wywołanie, `GetMessageAsync` aby uzyskać następną wiadomość w kolejce. Wiadomość zwrócona `GetMessageAsync` staje się niewidoczna dla innych wiadomości odczytu kodu z tej kolejki. Domyślnie komunikat pozostanie niewidoczny przez 30 sekund.
1. Aby zakończyć usuwanie wiadomości z `DeleteMessageAsync`kolejki, zadzwoń do niego .

Ten dwuetapowy proces usuwania komunikatów gwarantuje, że jeśli kod nie będzie w stanie przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu będzie w stanie uzyskać ten sam komunikat i ponowić próbę. Następujące wywołania `DeleteMessageAsync` kodu zaraz po przetworzyniu wiadomości:

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>Dodatkowe opcje usuwania wiadomości z kolejkowania

Istnieją dwa sposoby dostosowywania pobierania wiadomości z kolejki. Po pierwsze można uzyskać komunikaty zbiorczo (do 32). Po drugie można ustawić dłuższy lub krótszy limit czasu niewidoczności, dzięki czemu kod będzie mieć więcej lub mniej czasu na pełne przetworzenie każdego komunikatu. Poniższy przykład kodu `GetMessages` używa metody, aby uzyskać 20 wiadomości w jednym wywołaniu. Następnie przetwarza każdą wiadomość `foreach` za pomocą pętli. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu. Należy zauważyć, że pięciominutowy timer uruchamia się dla wszystkich wiadomości w tym samym czasie, więc po upływie pięciu minut wszystkie wiadomości, które nie zostały usunięte, stają się ponownie widoczne.

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

Możesz uzyskać szacunkową liczbę komunikatów w kolejce. Metoda `FetchAttributes` prosi usługę kolejki, aby pobrać atrybuty kolejki, w tym liczbę wiadomości. Właściwość `ApproximateMethodCount` zwraca ostatnią wartość pobraną `FetchAttributes` przez metodę, bez wywoływania usługi kolejki.

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Użyj wzorca Async-Await ze wspólnymi interfejsami API kolejki

W tym przykładzie pokazano, jak używać wzorca asynchronii z typowymi interfejsami API kolejki kończącymi się na `Async`. Gdy używana jest metoda asynchroniowa, wzorzec asynchronii zawiesza wykonanie lokalne do momentu zakończenia wywołania. To zachowanie umożliwia bieżącego wątku do innych prac, które pomaga uniknąć wąskich gardeł wydajności i poprawia ogólną szybkość reakcji aplikacji.

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

Aby usunąć kolejkę i wszystkie zawarte w `Delete` niej wiadomości, należy wywołać metodę w obiekcie kolejki:

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
