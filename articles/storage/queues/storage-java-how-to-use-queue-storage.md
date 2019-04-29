---
title: Jak używać magazynu kolejek w języku Java | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi kolejek platformy Azure do tworzenia i usuwania kolejki oraz wstawiania, pobieranie i usuwanie wiadomości. Przykłady napisaną w języku Java.
services: storage
author: roygara
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/08/2016
ms.author: rogarana
ms.subservice: queues
ms.openlocfilehash: 7c2fbc168c663cc4dc5981071d40540cfc4797ca
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108950"
---
# <a name="how-to-use-queue-storage-from-java"></a>Jak używać Magazynu kolejek w języku Java
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="overview"></a>Omówienie
W przewodniku opisano, jak realizować typowe scenariusze za pomocą usługi Azure Queue storage. Przykłady zostały napisane w języku Java i wymagają użycia [zestawu SDK usługi Azure Storage dla języka Java][Azure Storage SDK for Java]. Omówione scenariusze obejmują **wstawianie**, **wgląd do**, **wprowadzenie**, i **usuwanie** kolejki komunikatów, a także  **Tworzenie** i **usuwanie** kolejek. Aby uzyskać więcej informacji na temat kolejek, zobacz [następne kroki](#next-steps) sekcji.

Uwaga: Jest dostępny zestaw SDK dla deweloperów korzystających z usługi Azure Storage na urządzeniach z systemem Android. Aby uzyskać więcej informacji, zobacz [zestaw SDK usługi Azure Storage dla systemu Android][Azure Storage SDK for Android].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Tworzenie aplikacji Java
W tym przewodniku użyje funkcji magazynu, które mogą być uruchamiane w ramach aplikacji w języku Java, lokalnie lub w kodzie, w ramach roli Internet lub roli procesu roboczego na platformie Azure.

Aby to zrobić, należy zainstalować zestaw Java Development Kit (JDK) i utworzyć konto magazynu platformy Azure w ramach subskrypcji platformy Azure. Po zostało to zrobione, należy sprawdzić, czy systemie deweloperskim spełnia minimalne wymagania i zależności, które są wymienione w [zestawu SDK usługi Azure Storage dla języka Java] [ Azure Storage SDK for Java] repozytorium w witrynie GitHub. Jeśli system spełnia te wymagania, możesz wykonać instrukcje dotyczące pobierania i instalowania bibliotek usługi Azure Storage dla języka Java w systemie z tego repozytorium. Po zakończeniu tych zadań, można utworzyć aplikacji w języku Java, którego używa przykładów w tym artykule.

## <a name="configure-your-application-to-access-queue-storage"></a>Umożliwia skonfigurowanie aplikacji dostęp do usługi queue storage
Dodaj następujące instrukcje importu na początku pliku Java, którego umożliwia dostęp do kolejek usługi Azure storage interfejsów API:

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

## <a name="setup-an-azure-storage-connection-string"></a>Konfigurowanie parametrów połączenia usługi Azure storage
W kliencie usługi Azure Storage punkty końcowe i poświadczenia wymagane do uzyskania dostępu do usług zarządzania danymi są przechowywane w parametrach połączenia magazynu. Podczas uruchamiania w aplikacji klienckiej, należy podać parametry połączenia magazynu w następującym formacie, przy użyciu nazwy konta magazynu i podstawowy klucz dostępu dla konta magazynu na liście [witryny Azure Portal](https://portal.azure.com) dla *AccountName* i *AccountKey* wartości. W tym przykładzie pokazano, jak można zadeklarować pole statyczne w celu przechowywania parametrów połączenia:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

W aplikacji działającej w ramach roli w systemie Microsoft Azure, te parametry mogą być przechowywane w pliku konfiguracji usługi *ServiceConfiguration.cscfg*i jest dostępny z wywołaniem  **RoleEnvironment.getConfigurationSettings** metody. Oto przykład pobierania parametrów połączenia z **ustawienia** o nazwie *StorageConnectionString* w pliku konfiguracji usługi:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

W poniższych przykładach założono, że uzyskano parametry połączenia za pomocą jednej z tych dwóch metod.

## <a name="how-to-create-a-queue"></a>Instrukcje: Tworzenie kolejki
A **CloudQueueClient** obiektu pozwala uzyskać obiekty odwołanie do kolejki. Poniższy kod tworzy **CloudQueueClient** obiektu. (Uwaga: Istnieją dodatkowe sposoby tworzenia **CloudStorageAccount** obiektów; Aby uzyskać więcej informacji, zobacz **CloudStorageAccount** w [Dokumentacja zestawu SDK klienta usługi Azure Storage].)

Użyj **CloudQueueClient** obiektu, aby pobrać odwołanie do kolejki, w której chcesz użyć. Można utworzyć kolejkę, jeśli nie istnieje.

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

## <a name="how-to-add-a-message-to-a-queue"></a>Instrukcje: Dodawanie komunikatu do kolejki
Aby wstawić komunikat do istniejącej kolejki, najpierw utwórz nową klasę **CloudQueueMessage**. Następnie wywołaj **addMessage** metody. A **CloudQueueMessage** można tworzyć na podstawie ciągu (w formacie UTF-8) lub tablicą bajtów. Oto kod, który tworzy kolejkę (Jeśli nie istnieje) i wstawia komunikat "Hello, World".

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

## <a name="how-to-peek-at-the-next-message"></a>Instrukcje: Podgląd kolejnego komunikatu
Użytkownik może wglądu do wiadomości uzyskać kolejki bez jego usuwania z kolejki, wywołując **peekMessage**.

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

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Instrukcje: Zmiana zawartości komunikatu w kolejce
Możesz zmienić zawartość komunikatu w kolejce. Jeśli komunikat reprezentuje zadanie robocze, możesz użyć tej funkcji, aby zaktualizować stan zadania. Poniższy kod aktualizuje komunikat kolejki o nową zawartość i ustawia rozszerzenie limitu czasu widoczności o kolejne 60 sekund. Operacja ta zapisuje stan pracy powiązanej z komunikatem i daje klientowi kolejną minutę na kontynuowanie pracy nad komunikatem. Możesz użyć tej metody do śledzenia wieloetapowych przepływów pracy związanych z komunikatami kolejek, bez konieczności rozpoczynania od nowa, gdy dany etap nie powiedzie się ze względu na awarię sprzętu lub oprogramowania. Zazwyczaj stosuje się również liczbę ponownych prób. Jeśli komunikat zostanie ponowiony więcej niż *n* razy, zostanie usunięty. Jest to zabezpieczenie przed komunikatami, które wyzwalają błąd aplikacji zawsze, gdy są przetwarzane.

Następujące przykładowe wyszukiwania kodu za pośrednictwem kolejki komunikatów, lokalizuje pierwszy komunikat, który pasuje do "Hello, World" w przypadku zawartości, a następnie modyfikuje zawartość wiadomości i kończy działanie.

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

Alternatywnie Poniższy przykładowy kod aktualizuje tylko pierwszy widoczny komunikat w kolejce.

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

## <a name="how-to-get-the-queue-length"></a>Instrukcje: Pobieranie długości kolejki
Możesz uzyskać szacunkową liczbę komunikatów w kolejce. **DownloadAttributes** metoda prosi usługę kolejki dla kilku wartości bieżące, w tym liczbę liczbę komunikatów w kolejce. Wartość licznika jest przybliżona tylko w przypadku, ponieważ komunikaty mogą dodany lub usunięty, gdy usługa kolejki odpowiada na żądania. **GetApproximateMessageCount** metoda zwraca ostatnią wartość pobraną przez wywołanie **downloadAttributes**, bez wywoływania usługi kolejki.

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

## <a name="how-to-dequeue-the-next-message"></a>Instrukcje: Usuń następny komunikat z kolejki
Kod dequeues komunikatu z kolejki w dwóch etapach. Gdy wywołujesz **retrieveMessage**, uzyskasz następny komunikat w kolejce. Komunikat zwrócony z **retrieveMessage** staje się niewidoczny dla innego kodu odczytującego komunikaty z tej kolejki. Domyślnie komunikat pozostanie niewidoczny przez 30 sekund. Aby zakończyć usuwanie komunikatu z kolejki, musisz również wywołać **deleteMessage**. Ten dwuetapowy proces usuwania komunikatów gwarantuje, że jeśli kod nie będzie w stanie przetworzyć komunikatu z powodu awarii sprzętu lub oprogramowania, inne wystąpienie kodu będzie w stanie uzyskać ten sam komunikat i ponowić próbę. Twój kod wywołuje **deleteMessage** natychmiast po przetworzeniu komunikatu.

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

## <a name="additional-options-for-dequeuing-messages"></a>Dodatkowe opcje usuwania z kolejki komunikatów
Istnieją dwa sposoby dostosowania pobierania komunikatów z kolejki. Po pierwsze można uzyskać komunikaty zbiorczo (do 32). Po drugie można ustawić dłuższy lub krótszy limit czasu niewidoczności, dzięki czemu kod będzie mieć więcej lub mniej czasu na pełne przetworzenie każdego komunikatu.

Poniższy przykład kodu wykorzystuje **retrieveMessages** metodę, aby pobrać 20 komunikatów w jednym wywołaniu. Następnie przetwarza każdy komunikat przy użyciu **dla** pętli. Ustawia również limitu czasu niewidoczności na pięć minut (300 sekund) dla każdego komunikatu. Należy zauważyć, że w ciągu pięciu minut rozpoczyna się dla wszystkich wiadomości, w tym samym czasie, więc po pięciu minut upłynęły od wywołania **retrieveMessages**, wszystkie komunikaty, które nie zostały usunięte, będą widoczne ponownie.

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

## <a name="how-to-list-the-queues"></a>Instrukcje: Lista kolejek
Aby uzyskać listę kolejek bieżącej, należy wywołać **CloudQueueClient.listQueues()** metody, która zwróci zbiór **CloudQueue** obiektów.

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

## <a name="how-to-delete-a-queue"></a>Instrukcje: Usuwanie kolejki
Aby usunąć kolejkę i wszystkie zawarte w niej komunikaty, wywołaj **deleteIfExists** metody **CloudQueue** obiektu.

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

## <a name="next-steps"></a>Kolejne kroki
Teraz, kiedy znasz już podstawy usługi queue storage, skorzystaj z poniższych linków, aby dowiedzieć się więcej o bardziej skomplikowanych zadaniach magazynu.

* [Zestaw SDK usługi Azure Storage dla języka Java][Azure Storage SDK for Java]
* [Dokumentacja zestawu SDK klienta usługi Azure Storage][Dokumentacja zestawu SDK klienta usługi Azure Storage]
* [Interfejs API REST usług Azure Storage][Azure Storage Services REST API]
* [Blog zespołu odpowiedzialnego za usługę Azure Storage][Azure Storage Team Blog]

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Dokumentacja zestawu SDK klienta usługi Azure Storage]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage Services REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
