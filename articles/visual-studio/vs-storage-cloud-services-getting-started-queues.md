---
title: Wprowadzenie do magazynu kolejek przy użyciu programu Visual Studio (usługi w chmurze)
description: Jak rozpocząć korzystanie z magazynu kolejki platformy Azure w projekcie usługi w chmurze w programie Visual Studio po nawiązaniu połączenia z kontem magazynu przy użyciu usług połączonych z programem Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 603bb2b9a862ad4ed2cbde63e2d82b9a82fbeaa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298776"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Wprowadzenie do usługi Azure Queue storage i usług połączonych z programem Visual Studio (projekty usług w chmurze)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Omówienie
W tym artykule opisano, jak rozpocząć korzystanie z magazynu kolejki platformy Azure w programie Visual Studio po utworzeniu konta magazynu platformy Azure lub odwoływaniu się do niego w projekcie usług w chmurze przy użyciu okna dialogowego **Dodaj połączone usługi** programu Visual Studio.

Pokażemy Ci, jak utworzyć kolejkę w kodzie. Pokażemy również, jak wykonywać podstawowe operacje kolejki, takie jak dodawanie, modyfikowanie, odczytywanie i usuwanie wiadomości kolejki. Przykłady są zapisywane w kodzie języka C# i używają [biblioteki klienta usługi Microsoft Azure Storage dla platformy .NET.](https://msdn.microsoft.com/library/azure/dn261237.aspx)

Operacja **Dodaj połączone usługi** instaluje odpowiednie pakiety NuGet, aby uzyskać dostęp do magazynu platformy Azure w projekcie i dodaje parametry połączenia dla konta magazynu do plików konfiguracji projektu.

* Zobacz [Wprowadzenie do usługi Azure Queue storage przy użyciu platformy .NET,](../storage/queues/storage-dotnet-how-to-use-queues.md) aby uzyskać więcej informacji na temat manipulowania kolejkami w kodzie.
* Aby uzyskać ogólne informacje na temat usługi Azure Storage, zobacz [dokumentację magazynu.](https://azure.microsoft.com/documentation/services/storage/)
* Aby uzyskać ogólne informacje na temat usług w chmurze platformy Azure, zobacz [dokumentację usług](https://azure.microsoft.com/documentation/services/cloud-services/) w chmurze.
* Zobacz [ASP.NET,](https://www.asp.net) aby uzyskać więcej informacji na temat programowania aplikacji ASP.NET.

Azure Queue Storage to usługa do przechowywania dużej liczby komunikatów, do której można uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionego połączenia za pomocą protokołu HTTP lub HTTPS. Pojedynczy komunikat z kolejki nie może przekraczać 64 KB, a kolejka może zawierać miliony komunikatów — maksymalnie liczbę nieprzekraczającą całkowitego limitu pojemności konta magazynu.

## <a name="access-queues-in-code"></a>Kolejki dostępu w kodzie
Aby uzyskać dostęp do kolejek w projektach usługi Visual Studio Cloud Services, należy dołączyć następujące elementy do dowolnego pliku źródłowego języka C#, który uzyskuje dostęp do magazynu kolejki platformy Azure.

1. Upewnij się, że deklaracje obszaru nazw w górnej części pliku C# zawierają te **instrukcje.**
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
2. Pobierz **CloudStorageAccount** obiektu, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu, aby uzyskać parametry połączenia magazynu i informacje o koncie magazynu z konfiguracji usługi platformy Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
3. Pobierz **CloudQueueClient** obiektu do odwoływania się do obiektów kolejki na koncie magazynu.  
   
        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
4. Pobierz **CloudQueue** obiektu do odwoływania się do określonej kolejki.
   
        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");

**UWAGA:** Użyj wszystkich powyższych kodu przed kodem w poniższych przykładach.

## <a name="create-a-queue-in-code"></a>Tworzenie kolejki w kodzie
Aby utworzyć kolejkę w kodzie, wystarczy dodać wywołanie **createIfNotExists**.

    // Create the CloudQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>Dodawanie wiadomości do kolejki
Aby wstawić wiadomość do istniejącej kolejki, utwórz nowy obiekt **CloudQueueMessage,** a następnie wywołaj metodę **AddMessage.**

Obiekt **CloudQueueMessage** można utworzyć z ciągu (w formacie UTF-8) lub tablicy bajtowej.

Oto przykład, który wstawia komunikat "Hello, World".

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>Czytanie wiadomości w kolejce
Możesz uzyskać wgląd w komunikat z przodu kolejki bez jego usuwania z kolejki, wywołując metodę **PeekMessage**.

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>Odczytywanie i usuwanie wiadomości w kolejce
Kod można usunąć (de-queue) wiadomość z kolejki w dwóch krokach.

1. Zadzwoń **do GetMessage,** aby uzyskać następną wiadomość w kolejce. Komunikat zwrócony z funkcji **GetMessage** staje się niewidoczny dla innego kodu odczytującego komunikaty z tej kolejki. Domyślnie komunikat pozostanie niewidoczny przez 30 sekund.
2. Aby zakończyć usuwanie wiadomości z kolejki, zadzwoń do **deletemessage**.

Ten dwuetapowy proces usuwania komunikatów gwarantuje, że jeśli kod nie będzie w stanie przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu będzie w stanie uzyskać ten sam komunikat i ponowić próbę. Poniższy kod wywołuje **DeleteMessage** zaraz po przetworzyniu wiadomości.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>Używanie dodatkowych opcji do przetwarzania i usuwania wiadomości kolejki
Istnieją dwa sposoby dostosowania pobierania komunikatów z kolejki.

* Możesz otrzymać partię wiadomości (do 32).
* Można ustawić dłuższy lub krótszy limit czasu niewidzialności, dzięki czemu kod mniej lub bardziej czas, aby w pełni przetworzyć każdą wiadomość. Poniższy przykład kodu wykorzystuje metodę **GetMessages**, aby pobrać 20 komunikatów w jednym wywołaniu. Następnie przetwarza każdy komunikat przy użyciu pętli **foreach**. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu. Należy zauważyć, że okres 5 minut rozpoczyna się dla wszystkich komunikatów w tym samym czasie, więc po upływie 5 minut od wywołania metody **GetMessages** wszystkie komunikaty, które nie zostały usunięte, będą widoczne ponownie.

Oto przykład:

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.

        // Then delete the message after processing
        messageQueue.DeleteMessage(message);

    }

## <a name="get-the-queue-length"></a>Pobieranie długości kolejki
Możesz uzyskać szacunkową liczbę komunikatów w kolejce. Metoda **FetchAttributes** prosi usługę kolejki o pobranie atrybutów kolejki, w tym liczby komunikatów. **Właściwość ApproximateMethodCount** zwraca ostatnią wartość pobraną przez metodę **FetchAttributes** bez wywoływania usługi Queue.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Użyj wzorca Async-Await ze wspólnymi interfejsami API kolejki platformy Azure
W tym przykładzie pokazano, jak używać wzorca Async-Await ze wspólnymi interfejsami API kolejki platformy Azure. Przykład wywołuje asynchroniczne wersji każdej z podanych metod, to może być postrzegane przez **Async** post-fix każdej metody. Gdy używana jest metoda asynchroniowa wzorzec asynchronii wstrzymuje wykonanie lokalne do momentu zakończenia wywołania. To zachowanie umożliwia bieżącego wątku do innych prac, które pomaga uniknąć wąskich gardeł wydajności i poprawia ogólną szybkość reakcji aplikacji. Aby uzyskać szczegółowe informacje o wykorzystaniu wzorca Async-Await w programie .NET, zobacz [Async and Await (C# and Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx) (Async i Await [C# i Visual Basic]).

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Add the message asynchronously
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Delete the message asynchronously
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="delete-a-queue"></a>Usuwanie kolejki
Aby usunąć kolejkę i wszystkie zawarte w niej komunikaty, wywołaj metodę **Delete** na obiekcie kolejki.

    // Delete the queue.
    messageQueue.Delete();

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]

