---
title: Wysyłanie i odbieranie zdarzeń z usługi Azure Event Hubs przy użyciu języka Java (starsza wersja)
description: Ten artykuł zawiera Przewodnik dotyczący tworzenia aplikacji Java, która wysyła/odbiera zdarzenia do/z usługi Azure Event Hubs przy użyciu starego pakietu Azure-eventhubs.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: a2cce90b5aa28dac6ff945ac48f70bfd319683b9
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029898"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-eventhubs"></a>Wysyłanie zdarzeń do i odbieranie zdarzeń z usługi Azure Event Hubs (Azure-eventhubs) przy użyciu języka Java

W tym samouczku pokazano, jak tworzyć aplikacje Java do wysyłania zdarzeń do i odbierania zdarzeń z usługi Azure Event Hubs.

Azure Event Hubs to platforma do pozyskiwania i strumieniowego przesyłania danych, która umożliwia odbieranie i przetwarzanie milionów zdarzeń na sekundę. Usługa Event Hubs pozwala przetwarzać i przechowywać zdarzenia, dane lub dane telemetryczne generowane przez rozproszone oprogramowanie i urządzenia. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Szczegółowe omówienie Event Hubs można znaleźć w temacie Event Hubs Overview i Event Hubs features.

> [!WARNING]
> Ten przewodnik Szybki Start używa starych pakietów **Azure-eventhubs** i **Azure-eventhubs-EPH** . Zalecamy [Migrowanie](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md) kodu do korzystania z najnowszego pakietu [Azure-Messaging-eventhubs](get-started-java-send-v2.md) . 


## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

- Aktywne konto platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Środowisko projektowe Java. W tym samouczku są stosowane [Przezaćmienie](https://www.eclipse.org/).
- **Utwórz przestrzeń nazw Event Hubs i centrum zdarzeń**. Pierwszym krokiem jest skorzystanie z witryny [Azure Portal](https://portal.azure.com) w celu utworzenia przestrzeni nazw typu Event Hubs i uzyskania poświadczeń zarządzania wymaganych przez aplikację do komunikacji z centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md). Następnie Pobierz wartość klucza dostępu dla centrum zdarzeń, wykonując instrukcje podane w artykule: [pobieranie parametrów połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Klucz dostępu został użyty w kodzie, napisany w dalszej części tego samouczka. Domyślna nazwa klucza to: **RootManageSharedAccessKey**.

## <a name="send-events"></a>Wysyłanie zdarzeń 
W tej sekcji pokazano, jak utworzyć aplikację Java do wysyłania zdarzeń do centrum zdarzeń. 

> [!NOTE]
> Ten przewodnik Szybki start możesz pobrać jako przykład z witryny [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend), zastąpić ciągi `EventHubConnectionString` i `EventHubName` wartościami swojego centrum zdarzeń, a następnie uruchomić go. Alternatywnie możesz utworzyć własne rozwiązanie, wykonując kroki opisane w tym samouczku.

### <a name="add-reference-to-azure-event-hubs-library"></a>Dodaj odwołanie do biblioteki usługi Azure Event Hubs

Biblioteka klienta Java dla Event Hubs jest dostępna do użycia w projektach Maven z [repozytorium Central Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Można odwołać się do tej biblioteki za pomocą następującej deklaracji zależności w pliku projektu Maven:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
```

W przypadku różnych typów środowisk kompilacji można jawnie uzyskać najnowsze wydane pliki JAR z [repozytorium Maven Central](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

W przypadku prostego wydawcy zdarzeń zaimportuj pakiet *com. Microsoft. Azure. eventhubs* dla klas klienta Event Hubs i pakietu *com. Microsoft. Azure. ServiceBus* dla klas narzędzi, takich jak typowe wyjątki, które są udostępniane klientowi usługi Azure Service Bus Messaging. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Pisanie kodu w celu wysyłania komunikatów do centrum zdarzeń

Na potrzeby poniższego przykładu należy w ulubionym środowisku programowania Java utworzyć nowy projekt Maven dla aplikacji konsoli lub powłoki. Dodaj klasę o nazwie `SimpleSend`i Dodaj następujący kod do klasy:

```java
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
            
            
    }
 }
```

### <a name="construct-connection-string"></a>Utworzyć parametry połączenia

Klasa ConnectionStringBuilder służy do konstruowania wartość parametrów połączenia w celu przekazania do wystąpienia klienta usługi Event Hubs. Zastąp symbole zastępcze wartości, które zostały uzyskane podczas tworzenia przestrzeni nazw i Centrum zdarzeń:

```java
        final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
                .setNamespaceName("<EVENTHUB NAMESPACE") 
                .setEventHubName("EVENT HUB")
                .setSasKeyName("RootManageSharedAccessKey")
                .setSasKey("SHARED ACCESS KEY");
```

### <a name="write-code-to-send-events"></a>Pisanie kodu w celu wysyłania zdarzeń

Utwórz zdarzenie pojedynczej poprzez przekształcanie ciąg do jego kodowania UTF-8 bajtów. Następnie utwórz nowe wystąpienie klienta usługi Event Hubs z parametrów połączenia i wysyłanie wiadomości:   

```java 
        final Gson gson = new GsonBuilder().create();

        // The Executor handles all asynchronous tasks and this is passed to the EventHubClient instance.
        // This enables the user to segregate their thread pool based on the work load.
        // This pool can then be shared across multiple EventHubClient instances.
        // The following sample uses a single thread executor, as there is only one EventHubClient instance,
        // handling different flavors of ingestion to Event Hubs here.
        final ScheduledExecutorService executorService = Executors.newScheduledThreadPool(4);

        // Each EventHubClient instance spins up a new TCP/SSL connection, which is expensive.
        // It is always a best practice to reuse these instances. The following sample shows this.
        final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);


        try {
            for (int i = 0; i < 10; i++) {

                String payload = "Message " + Integer.toString(i);
                byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
                EventData sendEvent = EventData.create(payloadBytes);

                // Send - not tied to any partition
                // Event Hubs service will round-robin the events across all Event Hubs partitions.
                // This is the recommended & most reliable way to send to Event Hubs.
                ehClient.sendSync(sendEvent);
            }

            System.out.println(Instant.now() + ": Send Complete...");
            System.out.println("Press Enter to stop.");
            System.in.read();
        } finally {
            ehClient.closeSync();
            executorService.shutdown();
        }

``` 

Tworzenie i uruchom program i upewnij się, że nie ma żadnych błędów.

Gratulacje! Wysłano komunikaty do centrum zdarzeń.

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>Dodatek: Jak wiadomości są kierowane do partycji Centrum zdarzeń

Przed pobraniem wiadomości przez konsumentów, mają do opublikowania partycji najpierw przez wydawców. Po opublikowaniu wiadomości do Centrum zdarzeń, które synchronicznie przy użyciu metody sendSync() obiektu com.microsoft.azure.eventhubs.EventHubClient, wiadomości mogą zostać wysłane do określonej partycji lub rozpowszechniane do wszystkich partycji, dostępne w sposób działania okrężnego w zależności od tego, czy klucz partycji jest określony, czy nie.

Jeśli nie określono ciąg reprezentujący klucz partycji, klucz zostanie skrótu do określenia partycji do wysłania zdarzenia do.

Jeśli nie ustawiono klucza partycji, następnie komunikaty będą round robined do wszystkich dostępnych partycji

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

// close the client at the end of your program
eventHubClient.closeSync();

```

## <a name="receive-events"></a>Odbieranie zdarzeń
Kod w tym samouczku jest oparty na [kodzie EventProcessorSample w usłudze GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample), który można sprawdzić, aby zobaczyć pełną działającą aplikację.

### <a name="receive-messages-with-eventprocessorhost-in-java"></a>Odbieranie komunikatów EventProcessorHost w języku Java

**Klasy eventprocessorhost** to Klasa języka Java, która upraszcza odbieranie zdarzeń z Event Hubs przez zarządzanie trwałymi punktami kontrolnymi i równoległymi odbiorami z tych Event Hubs. Za pomocą klasy EventProcessorHost, można podzielić zdarzenia między wieloma odbiornikami, nawet w przypadku hostowania w różnych węzłach. W tym przykładzie przedstawiono, jak używać klasy EventProcessorHost dla jednego odbiornika.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Aby korzystać z klasy eventprocessorhost, musisz mieć konto usługi Azure Storage [konto magazynu platformy Azure]:

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i wybierz pozycję **Utwórz zasób** po lewej stronie ekranu.
2. Wybierz pozycję **Magazyn**, a następnie pozycję **konto magazynu**. W oknie **Tworzenie konta magazynu** wpisz nazwę konta magazynu. Wypełnij pozostałe pola, wybierz żądany region, a następnie wybierz pozycję **Utwórz**.
   
    ![Tworzenie konta magazynu w Azure Portal](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-azure-storage-account.png)

3. Wybierz nowo utworzone konto magazynu, a następnie wybierz pozycję **klucze dostępu**:
   
    ![Uzyskaj klucze dostępu w Azure Portal](./media/event-hubs-dotnet-framework-getstarted-receive-eph/select-azure-storage-access-keys.png)

    Skopiuj wartość klucz 1 do lokalizacji tymczasowej. Będziesz jej używać w dalszej części tego samouczka.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Tworzenie projektu języka Java za pomocą hosta EventProcessor

Biblioteka klienta Java dla Event Hubs jest dostępna do użycia w projektach Maven z [repozytorium Central Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)i może być przywoływana przy użyciu następującej deklaracji zależności w pliku projektu Maven: 

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>2.4.0</version>
</dependency>
```

W przypadku różnych typów środowisk kompilacji można jawnie uzyskać najnowsze wydane pliki JAR z [repozytorium Maven Central](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22).

1. Na potrzeby poniższego przykładu należy w ulubionym środowisku programowania Java utworzyć nowy projekt Maven dla aplikacji konsoli lub powłoki. Klasa jest nazywana `ErrorNotificationHandler`.     
   
    ```java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   
    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. Użyj następującego kodu, aby utworzyć nową klasę o nazwie `EventProcessorSample`. Zastąp symbole zastępcze wartości użyte do utworzenia event hub i konto magazynu:
   
   ```java
   package com.microsoft.azure.eventhubs.samples.eventprocessorsample;

   import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
   import com.microsoft.azure.eventhubs.EventData;
   import com.microsoft.azure.eventprocessorhost.CloseReason;
   import com.microsoft.azure.eventprocessorhost.EventProcessorHost;
   import com.microsoft.azure.eventprocessorhost.EventProcessorOptions;
   import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   import com.microsoft.azure.eventprocessorhost.IEventProcessor;
   import com.microsoft.azure.eventprocessorhost.PartitionContext;

   import java.util.concurrent.ExecutionException;
   import java.util.function.Consumer;

   public class EventProcessorSample
   {
       public static void main(String args[]) throws InterruptedException, ExecutionException
       {
           String consumerGroupName = "$Default";
           String namespaceName = "----NamespaceName----";
           String eventHubName = "----EventHubName----";
           String sasKeyName = "----SharedAccessSignatureKeyName----";
           String sasKey = "----SharedAccessSignatureKey----";
           String storageConnectionString = "----AzureStorageConnectionString----";
           String storageContainerName = "----StorageContainerName----";
           String hostNamePrefix = "----HostNamePrefix----";
        
           ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey);
        
           EventProcessorHost host = new EventProcessorHost(
                EventProcessorHost.createHostName(hostNamePrefix),
                eventHubName,
                consumerGroupName,
                eventHubConnectionString.toString(),
                storageConnectionString,
                storageContainerName);
        
           System.out.println("Registering host named " + host.getHostName());
           EventProcessorOptions options = new EventProcessorOptions();
           options.setExceptionNotification(new ErrorNotificationHandler());

           host.registerEventProcessor(EventProcessor.class, options)
           .whenComplete((unused, e) ->
           {
               if (e != null)
               {
                   System.out.println("Failure while registering: " + e.toString());
                   if (e.getCause() != null)
                   {
                       System.out.println("Inner exception: " + e.getCause().toString());
                   }
               }
           })
           .thenAccept((unused) ->
           {
               System.out.println("Press enter to stop.");
               try 
               {
                   System.in.read();
               }
               catch (Exception e)
               {
                   System.out.println("Keyboard read failed: " + e.toString());
               }
           })
           .thenCompose((unused) ->
           {
               return host.unregisterEventProcessor();
           })
           .exceptionally((e) ->
           {
               System.out.println("Failure while unregistering: " + e.toString());
               if (e.getCause() != null)
               {
                   System.out.println("Inner exception: " + e.getCause().toString());
               }
               return null;
           })
           .get(); // Wait for everything to finish before exiting main!
        
           System.out.println("End of sample");
       }
    ```
3. Utwórz jedną więcej klas o nazwie `EventProcessor`, używając następującego kodu:
   
    ```java
    public static class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        // OnOpen is called when a new event processor instance is created by the host. 
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        // OnClose is called when an event processor instance is being shut down. 
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        // onError is called when an error occurs in EventProcessorHost code that is tied to this partition, such as a receiver failure.
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        // onEvents is called when events are received on this partition of the Event Hub. 
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
            int eventCount = 0;
            for (EventData data : events)
            {
                try
                {
                    System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                            data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
                    eventCount++;
                    
                    // Checkpointing persists the current position in the event stream for this partition and means that the next
                    // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
                    // receiving at the event after this one. 
                    this.checkpointBatchingCount++;
                    if ((checkpointBatchingCount % 5) == 0)
                    {
                        System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                            data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                        // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
                        // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
                        context.checkpoint(data).get();
                    }
                }
                catch (Exception e)
                {
                    System.out.println("Processing failed for an event: " + e.toString());
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
        }
    }
    ```

Instrukcje w tym samouczku obejmują użycie pojedynczego wystąpienia hosta EventProcessorHost. Aby zwiększyć przepływność, zaleca się uruchomienie wielu wystąpień klasy EventProcessorHost, najlepiej na oddzielnych komputerach.  Zapewnia także nadmiarowości. W tych przypadkach różne wystąpienia automatycznie koordynują się ze sobą w celu równoważenia obciążenia odebranych zdarzeń. Jeśli chcesz, aby wiele odbiorników przetwarzało *wszystkie* zdarzenia, musisz użyć koncepcji **ConsumerGroup**. W przypadku odbierania zdarzeń z różnych komputerów dobrym rozwiązaniem może być określenie nazw wystąpień hosta EventProcessorHost w oparciu o komputery (lub role), w których są one wdrażane.

### <a name="publishing-messages-to-eventhub"></a>Publikowanie komunikatów do Centrum zdarzeń

Przed pobraniem wiadomości przez konsumentów, mają do opublikowania partycji najpierw przez wydawców. Warto zauważyć, że po opublikowaniu wiadomości do Centrum zdarzeń, które synchronicznie przy użyciu metody sendSync() obiektu com.microsoft.azure.eventhubs.EventHubClient, wiadomości mogą zostać wysłane do określonej partycji lub dystrybuowane do wszystkich dostępnych partycji w sposób okrężny w zależności od tego, czy określono klucza partycji, czy nie.

Gdy określona jest ciąg reprezentujący klucz partycji, klucz jest przekazywane do określenia partycji do wysłania zdarzenia do.

Jeśli nie ustawiono klucza partycji, następnie wiadomości są round robined do wszystkich dostępnych partycji

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

```

### <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>Implementowanie niestandardowego CheckpointManager EventProcessorHost (EPH)

Interfejs API udostępnia mechanizm do implementowania Menedżera niestandardowego punktu kontrolnego dla scenariuszy, w którym nie jest zgodny z danego przypadku użycia w implementacji domyślnej.

Menedżer punktów kontrolnych domyślne korzysta z magazynu obiektów blob, ale Jeśli zastąpisz Menedżer punktów kontrolnych, używane przez EPH z Twojej własnej implementacji, możesz użyć dowolnego magazynu, które chcesz wykonać kopię implementacji Menedżera punktu kontrolnego.

Utwórz klasę, która implementuje com.microsoft.azure.eventprocessorhost.ICheckpointManager interfejsu

Użyj niestandardowych implementacji Menedżer punktów kontrolnych (com.microsoft.azure.eventprocessorhost.ICheckpointManager)

W ramach implementacji można zastąpić domyślny mechanizm tworzenia punktów kontrolnych i zaimplementować własne punkty kontrolne na podstawie własnego magazynu danych (na przykład SQL Server, CosmosDB i pamięci podręcznej platformy Azure dla Redis). Zaleca się, że magazyn używany do wykonania kopii implementacji Menedżera punktu kontrolnego jest dostępny dla wszystkich wystąpień EPH skuteczność przetwarzania zdarzeń dla grupy odbiorców.

Możesz użyć dowolnego magazynu danych, która jest dostępna w danym środowisku.

Klasa com.microsoft.azure.eventprocessorhost.EventProcessorHost zapewnia dwa konstruktory, które pozwalają na zastąpienie Menedżera punktu kontrolnego dla swojej klasy EventProcessorHost.


## <a name="next-steps"></a>Następne kroki
Przeczytaj następujące artykuły: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkcje i terminologia w usłudze Azure Event Hubs](event-hubs-features.md)
- [Event Hubs — często zadawane pytania](event-hubs-faq.md)

