---
title: Wysyłanie lub odbieranie zdarzeń z usługi Azure Event Hubs przy użyciu języka Python (Najnowsza wersja)
description: Ten artykuł zawiera Przewodnik dotyczący tworzenia aplikacji w języku Python, która wysyła/odbiera zdarzenia do/z usługi Azure Event Hubs przy użyciu najnowszego pakietu Azure-eventhub w wersji 5.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: d977ae9ea8b78664ac1d3a318f58553da696c089
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906361"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub-version-5"></a>Wysyłanie zdarzeń do i odbieranie zdarzeń z centrów zdarzeń przy użyciu języka Python (Azure-eventhub w wersji 5)

Azure Event Hubs to platforma przesyłania strumieniowego danych Big Data i usługa pozyskiwania zdarzeń, która może odbierać i przetwarzać miliony zdarzeń na sekundę. Centra zdarzeń mogą przetwarzać i przechowywać zdarzenia, dane lub telemetrię wytwarzane przez rozproszone oprogramowanie i urządzenia. Dane wysyłane do centrum zdarzeń można przekształcać i przechowywać za pomocą dowolnego dostawcy analiz w czasie rzeczywistym lub adapterów wsadowych/magazynowych. Aby uzyskać więcej informacji, zobacz [Event Hubs Omówienie](event-hubs-about.md) i [Event Hubs funkcji](event-hubs-features.md).

W tym przewodniku szybki start opisano sposób tworzenia aplikacji języka Python, które mogą wysyłać zdarzenia do lub odbierać zdarzenia z centrum zdarzeń.

> [!IMPORTANT]
> Ten przewodnik Szybki Start używa wersji 5 zestawu SDK języka Python platformy Azure Event Hubs. Aby uzyskać szybki Start, który używa wersji 1 zestawu SDK języka Python, zobacz [ten artykuł](event-hubs-python-get-started-send.md). 

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego przewodnika Szybki start niezbędne jest spełnienie następujących wymagań wstępnych:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
- Aktywna przestrzeń nazw Event Hubs i centrum zdarzeń. Aby je utworzyć, postępuj zgodnie z instrukcjami w [przewodniku szybki start: tworzenie centrum zdarzeń przy użyciu Azure Portal](event-hubs-create.md). Zapisz nazwy przestrzeni nazw i centrów zdarzeń do użycia w dalszej części tego przewodnika Szybki Start.
- Nazwa klucza dostępu współdzielonego i wartość klucza podstawowego dla przestrzeni nazw Event Hubs. Pobierz nazwę i wartość klucza dostępu, postępując zgodnie z instrukcjami w obszarze [pobieranie parametrów połączenia centrów zdarzeń](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Domyślna nazwa klucza dostępu to *RootManageSharedAccessKey*. Zapisz nazwę klucza i wartość klucza podstawowego do użycia w dalszej części tego przewodnika Szybki Start.
- Środowisko Python 2,7 lub 3,5 lub nowsze z zainstalowanym i zaktualizowanym programem PIP.
- Pakiet języka Python dla Event Hubs. 

    Aby zainstalować pakiet, Uruchom to polecenie w wierszu polecenia zawierającym Język Python w ścieżce:

    ```cmd
    pip install azure-eventhub
    ```

    Zainstaluj następujący pakiet na potrzeby otrzymywania zdarzeń za pomocą usługi Azure Blob Storage jako magazynu punktów kontrolnych:

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```

## <a name="send-events"></a>Wysyłanie zdarzeń
W tej sekcji utworzysz skrypt języka Python służący do wysyłania zdarzeń do centrum zdarzeń utworzonego wcześniej.

1. Otwórz swój ulubiony Edytor Python, taki jak [Visual Studio Code](https://code.visualstudio.com/).
2. Tworzenie skryptu o nazwie *send.py*. Ten skrypt wysyła wsadowe zdarzenia do centrum zdarzeń utworzonego wcześniej.
3. Wklej następujący kod do *send.py*:

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData

    async def run():
        # Create a producer client to send messages to the event hub.
        # Specify a connection string to your event hubs namespace and
            # the event hub name.
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # Create a batch.
            event_data_batch = await producer.create_batch()

            # Add events to the batch.
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))

            # Send the batch of events to the event hub.
            await producer.send_batch(event_data_batch)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())

    ```

    > [!NOTE]
    > Aby uzyskać pełny kod źródłowy, w tym komentarze informacyjne, przejdź do [strony send_async. PR serwisu GitHub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).

## <a name="receive-events"></a>Odbieranie zdarzeń
Ten przewodnik Szybki Start używa usługi Azure Blob Storage jako magazynu punktów kontrolnych. Magazyn punktów kontrolnych jest używany do utrwalania punktów kontrolnych (czyli ostatnich pozycji odczytu).  

### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Tworzenie konta usługi Azure Storage i kontenera obiektów BLOB
Utwórz konto usługi Azure Storage i kontener obiektów blob, wykonując następujące czynności:

1. [Utwórz konto usługi Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Tworzenie kontenera obiektów BLOB](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Pobierz parametry połączenia z kontem magazynu](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

Pamiętaj, aby zarejestrować parametry połączenia i nazwę kontenera do późniejszego użycia w kodzie odbioru.


### <a name="create-a-python-script-to-receive-events"></a>Utwórz skrypt w języku Python do odbierania zdarzeń

W tej sekcji utworzysz skrypt języka Python do odbierania zdarzeń z centrum zdarzeń:

1. Otwórz swój ulubiony Edytor Python, taki jak [Visual Studio Code](https://code.visualstudio.com/).
2. Tworzenie skryptu o nazwie *recv.py*.
3. Wklej następujący kod do *recv.py*:

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore


    async def on_event(partition_context, event):
        # Print the event data.
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))

        # Update the checkpoint so that the program doesn't read the events
        # that it has already read when you run it next time.
        await partition_context.update_checkpoint(event)

    async def main():
        # Create an Azure blob checkpoint store to store the checkpoints.
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")

        # Create a consumer client for the event hub.
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # Call the receive method.
            await client.receive(on_event=on_event)

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # Run the main method.
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > Aby uzyskać pełny kod źródłowy, w tym dodatkowe komentarze informacyjne, przejdź do [strony recv_with_checkpoint_store_async serwisu GitHub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py).


### <a name="run-the-receiver-app"></a>Uruchamianie aplikacji odbiornika

Aby uruchomić skrypt, Otwórz okno wiersza polecenia, którego języka Python w ścieżce, a następnie uruchom następujące polecenie:

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>Uruchamianie aplikacji nadawcy

Aby uruchomić skrypt, Otwórz okno wiersza polecenia, którego języka Python w ścieżce, a następnie uruchom następujące polecenie:

```bash
python send.py
```

W oknie odbiorcy powinny być wyświetlane komunikaty wysłane do centrum zdarzeń.


## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki Start zostały wysłane i odebrane zdarzenia asynchronicznie. Aby dowiedzieć się, jak wysyłać i odbierać zdarzenia synchronicznie, przejdź do [strony sync_samples usługi GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

We wszystkich przykładach (synchronicznych i asynchronicznych) w witrynie GitHub przejdź do [biblioteki klienta Event Hubs platformy Azure, aby uzyskać przykłady dla języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples).
