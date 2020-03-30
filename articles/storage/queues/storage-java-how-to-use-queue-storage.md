---
title: Jak korzystać z magazynu kolejek z języka Java — Usługa Azure Storage
description: Dowiedz się, jak używać magazynu kolejek do tworzenia i usuwania kolejek oraz wstawiania, pobierania i usuwania wiadomości za pomocą biblioteki klienta usługi Azure Storage w języku Java.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 7658b8541e7a79a5e547a6649b35681446e34b0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067143"
---
# <a name="how-to-use-queue-storage-from-java"></a>Jak używać Magazynu kolejek w języku Java

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

W tym przewodniku pokazano, jak wykonywać typowe scenariusze przy użyciu usługi Azure Queue storage. Przykłady zostały napisane w języku Java i wymagają użycia [zestawu SDK usługi Azure Storage dla języka Java][Azure Storage SDK for Java]. Scenariusze obejmują **wstawianie,** **podglądanie,** **uzyskiwanie**i **usuwanie** wiadomości kolejki, a także **tworzenie** i **usuwanie** kolejek. Aby uzyskać więcej informacji na temat kolejek, zobacz [sekcję Następne kroki.](#next-steps)

> [!NOTE]
> Jest dostępny zestaw SDK dla deweloperów korzystających z usługi Azure Storage na urządzeniach z systemem Android. Aby uzyskać więcej informacji, zobacz [zestaw SDK usługi Azure Storage dla systemu Android][Azure Storage SDK for Android].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Tworzenie aplikacji Java

W tym przewodniku użyjesz funkcji magazynu, które można uruchomić w aplikacji Java lokalnie lub w kodzie uruchomionym w aplikacji sieci web na platformie Azure.

Aby to zrobić, należy zainstalować zestaw Java Development Kit (JDK) i utworzyć konto magazynu platformy Azure w ramach subskrypcji platformy Azure. Po wykonaniu tej sytuacji należy sprawdzić, czy system deweloperski spełnia minimalne wymagania i zależności, które są wymienione w [pliku SDK usługi Azure Storage dla][Azure Storage SDK for Java] java repozytorium w usłudze GitHub. Jeśli system spełnia te wymagania, można wykonać instrukcje dotyczące pobierania i instalowania bibliotek usługi Azure Storage dla środowiska Java w systemie z tego repozytorium. Po wykonaniu tych zadań będzie można utworzyć aplikację Java, która używa przykładów w tym artykule.

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurowanie aplikacji w celu uzyskania dostępu do magazynu kolejek

Dodaj następujące instrukcje importu do górnej części pliku Java, w którym chcesz użyć interfejsów API magazynu platformy Azure, aby uzyskać dostęp do kolejek:

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurowanie ciągu połączenia magazynu platformy Azure

W kliencie usługi Azure Storage punkty końcowe i poświadczenia wymagane do uzyskania dostępu do usług zarządzania danymi są przechowywane w parametrach połączenia magazynu. W aplikacji klienckiej należy podać parametry połączenia magazynu we wskazanym poniżej formacie, używając nazwy konta magazynu i podstawowego klucza dostępu do konta magazynu widocznego w witrynie [Azure Portal](https://portal.azure.com) jako wartości *AccountName* i *AccountKey*. W tym przykładzie pokazano, jak można zadeklarować pole statyczne w celu przechowywania parametrów połączenia:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

W aplikacji działającej w ramach roli na platformie Microsoft Azure ten ciąg może być przechowywany w pliku konfiguracji usługi *ServiceConfiguration.cscfg*i można uzyskać do niego dostęp za pomocą wywołania metody **RoleEnvironment.getConfigurationSettings.** Oto przykład pobierania parametrów połączenia z **ustawienia** o nazwie *StorageConnectionString* w pliku konfiguracji usługi:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

W poniższych przykładach założono, że uzyskano parametry połączenia za pomocą jednej z tych dwóch metod.

## <a name="how-to-create-a-queue"></a>Jak: Tworzenie kolejki
**Obiekt CloudQueueClient** umożliwia uzyskanie obiektów referencyjnych dla kolejek. Poniższy kod tworzy **CloudQueueClient** obiektu. (Uwaga: Istnieją dodatkowe sposoby tworzenia obiektów **CloudStorageAccount;** aby uzyskać więcej informacji, zobacz **CloudStorageAccount** w [odwołaniu SDK klienta usługi Azure Storage].)

Użyj **CloudQueueClient** obiektu, aby uzyskać odwołanie do kolejki, której chcesz użyć. Można utworzyć kolejkę, jeśli nie istnieje.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

   // Create the queue client.
   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

   // Retrieve a reference to a queue.
   CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Create the queue if it doesn't already exist.
   queue.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-add-a-message-to-a-queue"></a>Jak: Dodawanie wiadomości do kolejki
Aby wstawić komunikat do istniejącej kolejki, najpierw utwórz nową klasę **CloudQueueMessage**. Następnie wywołaj metodę **addMessage.** Klasę **CloudQueueMessage** można utworzyć przy użyciu ciągu (w formacie UTF-8) lub tablicy bajtów. Oto kod, który tworzy kolejkę (jeśli nie istnieje) i wstawia komunikat "Hello, World".

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.createIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-peek-at-the-next-message"></a>Jak: Zajrzeć do następnej wiadomości
Możesz zajrzeć do wiadomości z przodu kolejki bez usuwania go z kolejki, dzwoniąc **do peekMessage**.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message.
    CloudQueueMessage peekedMessage = queue.peekMessage();

    // Output the message value.
    if (peekedMessage != null)
    {
      System.out.println(peekedMessage.getMessageContentAsString());
   }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Jak: Zmienianie zawartości wiadomości w kolejce
Możesz zmienić zawartość komunikatu w kolejce. Jeśli komunikat reprezentuje zadanie robocze, możesz użyć tej funkcji, aby zaktualizować stan zadania. Poniższy kod aktualizuje komunikat kolejki o nową zawartość i ustawia rozszerzenie limitu czasu widoczności o kolejne 60 sekund. Rozszerzenie limitu czasu widoczności oszczędza stan pracy skojarzonej z wiadomością i daje klientowi kolejną minutę na kontynuowanie pracy nad wiadomością. Możesz użyć tej metody do śledzenia wieloetapowych przepływów pracy związanych z komunikatami kolejek, bez konieczności rozpoczynania od nowa, gdy dany etap nie powiedzie się ze względu na awarię sprzętu lub oprogramowania. Zazwyczaj stosuje się również liczbę ponownych prób. Jeśli komunikat zostanie ponowiony więcej niż *n* razy, zostanie usunięty. Jest to zabezpieczenie przed komunikatami, które wyzwalają błąd aplikacji zawsze, gdy są przetwarzane.

Poniższy przykładowy kod przeszukuje kolejkę wiadomości, lokalizuje pierwszą wiadomość, która pasuje do zawartości "Hello, World", a następnie modyfikuje zawartość wiadomości i kończy pracę.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // The maximum number of messages that can be retrieved is 32.
    final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

    // Loop through the messages in the queue.
    for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
    {
        // Check for a specific string.
        if (message.getMessageContentAsString().equals("Hello, World"))
        {
            // Modify the content of the first matching message.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 30 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 30, updateFields, null, null);
            break;
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

Alternatywnie poniższy kod przykładowe aktualizacje tylko pierwszy widoczny komunikat w kolejce.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage message = queue.retrieveMessage();

    if (message != null)
    {
        // Modify the message content.
        message.setMessageContent("Updated contents.");
        // Set it to be visible in 60 seconds.
        EnumSet<MessageUpdateFields> updateFields =
            EnumSet.of(MessageUpdateFields.CONTENT,
            MessageUpdateFields.VISIBILITY);
        // Update the message.
        queue.updateMessage(message, 60, updateFields, null, null);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-get-the-queue-length"></a>Jak: Uzyskać długość kolejki
Możesz uzyskać szacunkową liczbę komunikatów w kolejce. Metoda **downloadAttributes** pyta usługę Kolejka dla kilku bieżących wartości, w tym liczby komunikatów w kolejce. Liczba jest przybliżona, ponieważ wiadomości można dodać lub usunąć po usługa Kolejka odpowiada na żądanie. **GetApproximateMessageCount** Metoda zwraca ostatnią wartość pobraną przez wywołanie **downloadAttributes**, bez wywoływania usługi kolejki.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Download the approximate message count from the server.
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count.
    long cachedMessageCount = queue.getApproximateMessageCount();

    // Display the queue length.
    System.out.println(String.format("Queue length: %d", cachedMessageCount));
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-dequeue-the-next-message"></a>Jak: Dequeue następnej wiadomości
Kod usuwa wiadomość z kolejki w dwóch krokach. Po **wywołaniu retrieveMessage,** pojawi się następna wiadomość w kolejce. Wiadomość zwrócona z **retrieveMessage** staje się niewidoczna dla innych wiadomości odczytu kodu z tej kolejki. Domyślnie komunikat pozostanie niewidoczny przez 30 sekund. Aby zakończyć usuwanie wiadomości z kolejki, należy również wywołać **deleteMessage**. Ten dwuetapowy proces usuwania komunikatów gwarantuje, że jeśli kod nie będzie w stanie przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu będzie w stanie uzyskać ten sam komunikat i ponowić próbę. Wywołania kodu **deleteMessage** zaraz po przetworzyniu wiadomości.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    if (retrievedMessage != null)
    {
        // Process the message in less than 30 seconds, and then delete the message.
        queue.deleteMessage(retrievedMessage);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="additional-options-for-dequeuing-messages"></a>Dodatkowe opcje usuwania wiadomości z kolejkowania
Istnieją dwa sposoby dostosowania pobierania komunikatów z kolejki. Po pierwsze można uzyskać komunikaty zbiorczo (do 32). Po drugie można ustawić dłuższy lub krótszy limit czasu niewidoczności, dzięki czemu kod będzie mieć więcej lub mniej czasu na pełne przetworzenie każdego komunikatu.

Poniższy przykład kodu używa **retrieveMessages** metody, aby uzyskać 20 komunikatów w jednym wywołaniu. Następnie przetwarza każdą wiadomość za pomocą **for** pętli. Ustawia również limit czasu niewidzialności do pięciu minut (300 sekund) dla każdej wiadomości. Pięć minut rozpoczyna się dla wszystkich wiadomości w tym samym czasie, więc po upływie pięciu minut od wywołania **retrieveMessage,** wszystkie wiadomości, które nie zostały usunięte staną się widoczne ponownie.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Do processing for all messages in less than 5 minutes,
        // deleting each message after processing.
        queue.deleteMessage(message);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-list-the-queues"></a>Jak: Lista kolejek
Aby uzyskać listę bieżących kolejek, należy wywołać **CloudQueueClient.listQueues(),** która zwróci kolekcję **CloudQueue** obiektów.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient =
        storageAccount.createCloudQueueClient();

    // Loop through the collection of queues.
    for (CloudQueue queue : queueClient.listQueues())
    {
        // Output each queue name.
        System.out.println(queue.getName());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-delete-a-queue"></a>Jak: Usuwanie kolejki
Aby usunąć kolejkę i wszystkie wiadomości zawarte w niej, wywołać **deleteIfExists** metody **cloudqueue** obiektu.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue if it exists.
    queue.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="next-steps"></a>Następne kroki
Teraz, gdy poznasz podstawy magazynu kolejek, skorzystaj z tych łączy, aby dowiedzieć się więcej o bardziej złożonych zadaniach magazynowania.

* [Zestaw SDK usługi Azure Storage dla języka Java][Azure Storage SDK for Java]
* [Dokumentacja zestawu SDK klienta usługi Azure Storage][Azure Storage Client SDK Reference]
* [Interfejs API REST usług Azure Storage][Azure Storage Services REST API]
* [Blog zespołu usługi Azure Storage][Azure Storage Team Blog]

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Dokumentacja zestawu SDK klienta usługi Azure Storage]: https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html
[Azure Storage Services REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
