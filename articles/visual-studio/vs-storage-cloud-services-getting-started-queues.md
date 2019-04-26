---
title: Wprowadzenie do usługi queue storage i Visual Studio podłączone usługi (usługi w chmurze) | Dokumentacja firmy Microsoft
description: Jak rozpocząć korzystanie z usługi Azure Queue storage projektu usługi w chmurze w programie Visual Studio, po nawiązaniu połączenia z kontem magazynu za pomocą programu Visual Studio podłączone usługi
services: storage
author: ghogen
manager: douge
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 28a7de1b43d793641237197aea841022996b07e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60505644"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Wprowadzenie do usługi Azure Queue storage i Visual Studio połączonych usług (usługi w chmurze projektów)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Omówienie
W tym artykule opisano sposób rozpoczęcia pracy przy użyciu usługi Azure Queue storage w programie Visual Studio po użytkownik utworzył, lub odwołanie do konta usługi Azure storage w projekt usług w chmurze przy użyciu programu Visual Studio **Dodaj usługi połączone** okna dialogowego.

Poniżej opisano sposób tworzenia kolejki w kodzie. Ponadto pokażemy sposób wykonywania operacji kolejki podstawowych, takich jak dodawanie, modyfikowanie, odczytywanie i usuwanie wiadomości w kolejce. Przykłady są napisane w kodzie języka C# i użyj [biblioteki klienta usługi Microsoft Azure Storage dla platformy .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

**Dodaj usługi połączone** operacji instaluje odpowiednie pakiety NuGet dostępu do magazynu platformy Azure w swoim projekcie i dodaje parametry połączenia dla konta magazynu do plików konfiguracji projektu.

* Zobacz [Rozpoczynanie pracy z usługą Azure Queue storage przy użyciu platformy .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) więcej informacji na temat manipulowanie kolejek w kodzie.
* Zobacz [dokumentacja usługi Storage](https://azure.microsoft.com/documentation/services/storage/) ogólne informacje o usłudze Azure Storage.
* Zobacz [dokumentacji usług w chmurze](https://azure.microsoft.com/documentation/services/cloud-services/) ogólne informacje dotyczące usług Azure cloud services.
* Zobacz [ASP.NET](https://www.asp.net) Aby uzyskać więcej informacji na temat programowania aplikacji ASP.NET.

Azure Queue Storage to usługa do przechowywania dużej liczby komunikatów, do której można uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionego połączenia za pomocą protokołu HTTP lub HTTPS. Pojedynczy komunikat z kolejki nie może przekraczać 64 KB, a kolejka może zawierać miliony komunikatów — maksymalnie liczbę nieprzekraczającą całkowitego limitu pojemności konta magazynu.

## <a name="access-queues-in-code"></a>Dostęp do kolejki w kodzie
Aby uzyskać dostęp do kolejki w projektach Visual Studio Cloud Services, należy uwzględnić poniższe elementy do pliku źródłowego języka C#, uzyskujących dostęp do usługi Azure Queue storage.

1. Upewnij się, obejmują deklaracje przestrzeni nazw w górnej części pliku języka C#, te **przy użyciu** instrukcji.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
2. Pobierz **CloudStorageAccount** obiekt, który reprezentuje dane konta magazynu. Użyj poniższego kodu, można pobrać z parametrów połączenia magazynu i informacje o koncie magazynu z konfiguracji usługi platformy Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
3. Pobierz **CloudQueueClient** obiektu do odwołania obiekty w ramach konta magazynu.  
   
        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
4. Pobierz **CloudQueue** obiekt, aby odwoływać się do określonej kolejki.
   
        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");

**UWAGA:** Używać wszystkich powyższy kod przed kodem w następujących przykładach.

## <a name="create-a-queue-in-code"></a>Tworzenie kolejki w kodzie
Aby utworzyć kolejkę w kodzie, wystarczy dodać wywołanie **CreateIfNotExists**.

    // Create the CloudQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>Dodawanie komunikatu do kolejki
Aby wstawić komunikat do istniejącej kolejki, Utwórz nowy **CloudQueueMessage** obiektu, a następnie wywołaj **AddMessage** metody.

A **CloudQueueMessage** obiektu można tworzyć na podstawie ciągu (w formacie UTF-8) lub tablicą bajtów.

Oto przykład, który wstawia komunikat "Hello, World".

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>Przeczytaj komunikat w kolejce
Możesz uzyskać wgląd w komunikat z przodu kolejki bez jego usuwania z kolejki, wywołując metodę **PeekMessage**.

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>Odczyt i usuwanie komunikatu z kolejki
Kod można usunąć (przeglądania kolejki) komunikatu z kolejki w dwóch etapach.

1. Wywołaj **GetMessage** można pobrać następnej wiadomości w kolejce. Komunikat zwrócony z funkcji **GetMessage** staje się niewidoczny dla innego kodu odczytującego komunikaty z tej kolejki. Domyślnie komunikat pozostanie niewidoczny przez 30 sekund.
2. Aby zakończyć usuwanie komunikatu z kolejki, należy wywołać **DeleteMessage**.

Ten dwuetapowy proces usuwania komunikatów gwarantuje, że jeśli kod nie będzie w stanie przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu będzie w stanie uzyskać ten sam komunikat i ponowić próbę. Poniższy kod wywoła **DeleteMessage** natychmiast po przetworzeniu komunikatu.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>Użyj dodatkowych opcji do przetwarzania i usuwanie wiadomości w kolejce
Istnieją dwa sposoby dostosowania pobierania komunikatów z kolejki.

* Możesz uzyskać partię komunikatów (maksymalnie 32).
* Możesz ustawić limit czasu niewidoczności dłuższy lub krótszy, dzięki czemu kod więcej lub mniej czasu na pełne przetworzenie każdego komunikatu. Poniższy przykład kodu wykorzystuje metodę **GetMessages**, aby pobrać 20 komunikatów w jednym wywołaniu. Następnie przetwarza każdy komunikat przy użyciu pętli **foreach**. Ustawia również limitu czasu niewidoczności na pięć minut dla każdego komunikatu. Należy zauważyć, że okres 5 minut rozpoczyna się dla wszystkich komunikatów w tym samym czasie, więc po upływie 5 minut od wywołania metody **GetMessages** wszystkie komunikaty, które nie zostały usunięte, będą widoczne ponownie.

Oto przykład:

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.

        // Then delete the message after processing
        messageQueue.DeleteMessage(message);

    }

## <a name="get-the-queue-length"></a>Pobieranie długości kolejki
Możesz uzyskać szacunkową liczbę komunikatów w kolejce. Metoda **FetchAttributes** prosi usługę kolejki o pobranie atrybutów kolejki, w tym liczby komunikatów. **ApproximateMethodCount** właściwość zwraca ostatnią wartość pobraną przez **FetchAttributes** metody bez wywoływania usługi kolejki.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Używanie wzorca Async-Await z wspólnych interfejsów API usługi Azure Queue
W tym przykładzie przedstawiono sposób użycia wzorca Async-Await z typowych interfejsów API z kolejki usługi Azure. Przykład wywołuje wersji asynchronicznej każdego z danych metod, można to zaobserwować **Async** po awarii poszczególnych metod. Gdy zostanie użyta metoda asynchroniczna async-await wzorzec zawiesi lokalne wykonanie do momentu ukończenia wywołania. Takie zachowanie umożliwia wykonywanie innych zadań, co pomaga uniknąć problemów z wydajnością i poprawia ogólną szybkość reakcji aplikacji bieżącego wątku. Aby uzyskać szczegółowe informacje o wykorzystaniu wzorca Async-Await w programie .NET, zobacz [Async and Await (C# and Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx) (Async i Await [C# i Visual Basic]).

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

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]

