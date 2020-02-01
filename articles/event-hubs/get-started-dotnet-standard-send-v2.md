---
title: Wysyłanie lub odbieranie zdarzeń z usługi Azure Event Hubs przy użyciu platformy .NET (najnowsze)
description: Ten artykuł zawiera Przewodnik dotyczący tworzenia aplikacji .NET Core, która wysyła/odbiera zdarzenia do/z usługi Azure Event Hubs przy użyciu najnowszego pakietu Azure. Messaging. EventHubs.
services: event-hubs
documentationcenter: na
author: spelluru
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: fc9ffc62e765f3b8c8fa418f3189f84686987821
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907117"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs---net-core-azuremessagingeventhubs"></a>Wysyłanie zdarzeń do i odbieranie zdarzeń z usługi Azure Event Hubs — .NET Core (Azure. Messaging. EventHubs) 
Event Hubs to usługa, która przetwarza duże ilości danych zdarzeń (danych telemetrycznych) z podłączonych urządzeń i aplikacji. Po zebraniu danych do Event Hubs można przechowywać dane przy użyciu klastra magazynu lub zdarzenia procesów. Można na przykład przekształcić dane zdarzeń przy użyciu dostawcy analiz w czasie rzeczywistym. Ta funkcja zbierania i przetwarzania zdarzeń na dużą skalę jest kluczowym składnikiem nowoczesnych architektur aplikacji, w tym Internet rzeczy (IoT). Aby zapoznać się ze szczegółowym omówieniem usługi Event Hubs, zobacz [Omówienie usługi Event Hubs](event-hubs-about.md) i [Funkcje usługi Event Hubs](event-hubs-features.md).

W tym samouczku pokazano, jak wysyłać zdarzenia do i odbierać zdarzenia z centrum zdarzeń przy użyciu zestaw .NET Core SDK Event Hubs. 

> [!IMPORTANT]
> Ten przewodnik Szybki Start używa nowej biblioteki **Azure. Messaging. EventHubs** . Aby uzyskać szybki Start, który używa starej biblioteki **Microsoft. Azure. EventHubs** , zobacz [ten artykuł](event-hubs-dotnet-standard-getstarted-send.md). 

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja Microsoft Azure**. Do korzystania z usług platformy Azure, w tym usługi Azure Event Hubs, potrzebna jest subskrypcja.  Jeśli nie masz istniejącego konta platformy Azure, możesz zarejestrować się w celu korzystania z [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/) lub skorzystać z korzyści dla subskrybentów MSDN podczas [tworzenia konta](https://azure.microsoft.com).
- **Microsoft Visual Studio 2019**. Biblioteka klienta Event Hubs platformy Azure korzysta z nowych funkcji wprowadzonych w C# 8,0.  Nadal można używać biblioteki ze starszymi wersjami programu C#, ale niektóre jej funkcje nie będą dostępne.  Aby włączyć te funkcje, należy [wskazać .NET Core 3,0](/dotnet/standard/frameworks#how-to-specify-target-frameworks) lub [określić wersję językową](/dotnet/csharp/language-reference/configure-language-version#override-a-default) , która ma być używana (8,0 lub nowsza). Jeśli używasz programu Visual Studio, wersje starsze niż Visual Studio 2019 nie są zgodne z narzędziami wymaganymi do C# kompilowania projektów 8,0. Program Visual Studio 2019, w tym bezpłatna wersja Community, można pobrać [tutaj](https://visualstudio.microsoft.com/vs/)
- **Utwórz przestrzeń nazw Event Hubs i centrum zdarzeń**. Pierwszym krokiem jest skorzystanie z witryny [Azure Portal](https://portal.azure.com) w celu utworzenia przestrzeni nazw typu Event Hubs i uzyskania poświadczeń zarządzania wymaganych przez aplikację do komunikacji z centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md). Następnie Pobierz **Parametry połączenia dla przestrzeni nazw Event Hubs** , wykonując instrukcje z artykułu: [pobieranie parametrów połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Te parametry połączenia będą potrzebne w dalszej części tego samouczka.

## <a name="send-events"></a>Wysyłanie zdarzeń 
W tej sekcji przedstawiono sposób tworzenia aplikacji konsolowej .NET Core w celu wysyłania zdarzeń do centrum zdarzeń. 

### <a name="create-a-console-application"></a>Tworzenie aplikacji konsolowej

1. Start Visual Studio 2019. 
1. Wybierz pozycję **Utwórz nowy projekt**. 
1. W oknie dialogowym **Tworzenie nowego projektu** wykonaj następujące czynności: Jeśli to okno dialogowe nie jest widoczne, wybierz pozycję **plik** w menu, wybierz pozycję **Nowy**, a następnie wybierz pozycję **projekt**. 
    1. Wybierz **C#** pozycję dla języka programowania.
    1. Wybierz pozycję **konsola** dla typu aplikacji. 
    1. Wybierz pozycję **aplikacja konsoli (.NET Core)** z listy wyników. 
    1. Następnie wybierz opcję **Dalej**. 

        ![Okno dialogowe Nowy projekt](./media/getstarted-dotnet-standard-send-v2/new-send-project.png)    
1. Wprowadź **EventHubsSender** dla nazwy projektu, **EventHubsQuickStart** dla nazwy rozwiązania, a następnie wybierz przycisk **OK** , aby utworzyć projekt. 

    ![C#Aplikacja konsolowa >](./media/getstarted-dotnet-standard-send-v2/project-solution-names.png)

### <a name="add-the-event-hubs-nuget-package"></a>Dodawanie pakietu NuGet usługi Event Hubs

1. Wybierz kolejno pozycje **narzędzia** > **menedżer pakietów NuGet** > **konsola Menedżera pakietów** z menu. 
1. Uruchom następujące polecenie, aby zainstalować pakiet NuGet **Azure. Messaging. EventHubs** :

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```


### <a name="write-code-to-send-messages-to-the-event-hub"></a>Pisanie kodu w celu wysyłania komunikatów do centrum zdarzeń

1. Dodaj następujące instrukcje `using` na początku pliku **program.cs** :

    ```csharp
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Dodaj stałe do klasy `Program` dla parametrów połączenia Event Hubs i nazwy centrum zdarzeń. Zastąp symbole zastępcze w nawiasach odpowiednimi wartościami uzyskanymi podczas tworzenia centrum zdarzeń. Upewnij się, że `{Event Hubs namespace connection string}` jest parametrem połączenia na poziomie przestrzeni nazw, a nie parametrem centrum zdarzeń. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

3. Zastąp metodę `Main` poniższymi metodami `async Main`. Szczegóły można znaleźć w komentarzach do kodu. 

    ```csharp
        static async Task Main()
        {
            // Create a producer client that you can use to send events to an event hub
            await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
            {
                // Create a batch of events 
                using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

                // Add events to the batch. An event is a represented by a collection of bytes and metadata. 
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First event")));
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second event")));
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Third event")));

                // Use the producer client to send the batch of events to the event hub
                await producerClient.SendAsync(eventBatch);
                Console.WriteLine("A batch of 3 events has been published.");
            }
        }
    ```
5. Skompiluj projekt i upewnij się, że nie ma żadnych błędów.
6. Uruchom program i poczekaj na komunikat potwierdzenia. 
7. W Azure Portal można sprawdzić, czy centrum zdarzeń odebrało komunikaty. Przejdź do widoku **komunikaty** w sekcji **metryki** . Odśwież stronę, aby zaktualizować wykres. Wyświetlenie komunikatów zostało odebrane może potrwać kilka sekund. 

    [![sprawdzić, czy centrum zdarzeń odebrało komunikaty](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Aby uzyskać więcej informacji o pełnym kodzie źródłowym, zobacz [ten plik w witrynie GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample03_PublishAnEventBatch.cs)

## <a name="receive-events"></a>Odbieranie zdarzeń
W tej sekcji pokazano, jak napisać aplikację konsolową .NET Core, która odbiera komunikaty z centrum zdarzeń przy użyciu procesora zdarzeń. Procesor zdarzeń upraszcza odbieranie zdarzeń z centrów zdarzeń przez zarządzanie trwałymi punktami kontrolnymi i równoległymi odbiorami z tych centrów zdarzeń. Procesor zdarzeń jest skojarzony z określonym centrum zdarzeń i grupą konsumentów. Odbiera zdarzenia z wielu partycji w centrum zdarzeń, przekazując je do delegata procedury obsługi na potrzeby przetwarzania za pomocą podania kodu. 


### <a name="create-an-azure-storage-and-a-blob-container"></a>Tworzenie usługi Azure Storage i kontenera obiektów BLOB
W tym przewodniku szybki start użyjesz usługi Azure Storage jako magazynu punktów kontrolnych. Wykonaj następujące kroki, aby utworzyć konto usługi Azure Storage. 

1. [Utwórz konto usługi Azure Storage](/storage/common/storage-quickstart-create-account.md?tabs=azure-portal)
2. [Tworzenie kontenera obiektów BLOB](/storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Pobierz parametry połączenia z kontem magazynu](/storage/common/storage-configure-connection-string?#view-and-copy-a-connection-string)

    Zanotuj parametry połączenia i nazwę kontenera. Będziesz ich używać w kodzie Receive. 


### <a name="create-a-project-for-the-receiver"></a>Tworzenie projektu dla odbiorcy

1. W oknie Eksplorator rozwiązań kliknij prawym przyciskiem myszy rozwiązanie **EventHubQuickStart** , wskaż polecenie **Dodaj**, a następnie wybierz pozycję **Nowy projekt**. 
1. Wybierz pozycję **aplikacja konsoli (.NET Core)** , a **następnie**wybierz pozycję Dalej. 
1. **EventHubsReceiver** zdarzeń dla **nazwy projektu**i wybierz pozycję **Utwórz**. 

### <a name="add-the-event-hubs-nuget-package"></a>Dodawanie pakietu NuGet usługi Event Hubs

1. Wybierz kolejno pozycje **narzędzia** > **menedżer pakietów NuGet** > **konsola Menedżera pakietów** z menu. 
1. Uruchom następujące polecenie, aby zainstalować pakiet NuGet **Azure. Messaging. EventHubs** :

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. Uruchom następujące polecenie, aby zainstalować pakiet NuGet **platformy Azure. Messaging. EventHubs. Processor** :

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>Aktualizowanie metody Main 

1. Dodaj następujące instrukcje `using` w górnej części pliku **program.cs** .

    ```csharp
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Dodaj stałe do klasy `Program` dla parametrów połączenia Event Hubs i nazwy centrum zdarzeń. Zastąp symbole zastępcze w nawiasach odpowiednimi wartościami uzyskanymi podczas tworzenia centrum zdarzeń. Zastąp symbole zastępcze w nawiasach odpowiednimi wartościami uzyskanymi podczas tworzenia centrum zdarzeń i konta magazynu (klucze dostępu — podstawowe parametry połączenia). Upewnij się, że `{Event Hubs namespace connection string}` jest parametrem połączenia na poziomie przestrzeni nazw, a nie parametrem centrum zdarzeń.

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
3. Zastąp metodę `Main` poniższymi metodami `async Main`. Szczegóły można znaleźć w komentarzach do kodu. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            BlobContainerClient storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            EventProcessorClient processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 10 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(10));

            // Stop the processing
            await processor.StopProcessingAsync();
        }    
    ```
1. Teraz Dodaj następujące metody obsługi zdarzeń i błędów do klasy. 

    ```csharp
        static Task ProcessEventHandler(ProcessEventArgs eventArgs)
        { 
            // Write the body of the event to the console window
            Console.WriteLine("\tRecevied event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray())); 
            return Task.CompletedTask; 
        }

        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            // Write details about the error to the console window
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }    
    ```
1. Skompiluj projekt i upewnij się, że nie ma żadnych błędów.

    > [!NOTE]
    > Aby uzyskać więcej informacji, zobacz [ten plik w witrynie GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.cs).
6. Uruchom aplikację odbiornika. 
1. Powinien zostać wyświetlony komunikat informujący o odebraniu zdarzenia. 

    ![Odebrane zdarzenie](./media/getstarted-dotnet-standard-send-v2/event-received.png)

    Te zdarzenia to trzy zdarzenia, które zostały wysłane do centrum zdarzeń wcześniej przez uruchomienie programu nadawcy. 


## <a name="next-steps"></a>Następne kroki
Zapoznaj się z przykładami w witrynie GitHub. 

- [Przykłady usługi Event Hubs w usłudze GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Przykłady procesora zdarzeń w witrynie GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
