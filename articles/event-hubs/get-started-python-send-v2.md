---
title: Wysyłanie i odbieranie zdarzeń przy użyciu języka Python — Azure Event Hubs | Microsoft Docs
description: Ten artykuł zawiera Przewodnik dotyczący tworzenia aplikacji w języku Python, która wysyła zdarzenia do usługi Azure Event Hubs.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/08/2020
ms.author: spelluru
ms.openlocfilehash: 11548ba180a2dd6541240431d670812448c502b8
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981585"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-python"></a>Wysyłanie zdarzeń do i odbieranie zdarzeń z Event Hubs przy użyciu języka Python

Azure Event Hubs to platforma do pozyskiwania i strumieniowego przesyłania danych, która umożliwia odbieranie i przetwarzanie milionów zdarzeń na sekundę. Usługa Event Hubs pozwala przetwarzać i przechowywać zdarzenia, dane lub dane telemetryczne generowane przez rozproszone oprogramowanie i urządzenia. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Aby zapoznać się ze szczegółowym omówieniem usługi Event Hubs, zobacz [Omówienie usługi Event Hubs](event-hubs-about.md) i [Funkcje usługi Event Hubs](event-hubs-features.md).

W tym samouczku opisano sposób tworzenia aplikacji w języku Python w celu wysyłania zdarzeń do zdarzeń z centrum zdarzeń lub ich odbierania.

> [!IMPORTANT]
> Ten przewodnik Szybki Start używa wersji 5 zestawu SDK języka Python platformy Azure Event Hubs. Aby uzyskać szybki Start, który używa starej wersji 1 zestawu SDK języka Python, zobacz [ten artykuł](event-hubs-python-get-started-send.md). Jeśli używasz wersji 1 zestawu SDK, zalecamy przeprowadzenie migracji kodu do najnowszej wersji. Aby uzyskać szczegółowe informacje, zobacz [Przewodnik migracji](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).


## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
- Aktywna przestrzeń nazw Event Hubs i centrum zdarzeń utworzone zgodnie z instrukcjami podanymi w [przewodniku szybki start: tworzenie centrum zdarzeń przy użyciu Azure Portal](event-hubs-create.md). Zanotuj nazwy przestrzeni nazw i centrów zdarzeń do użycia w dalszej części tego przewodnika.
- Nazwa klucza dostępu współdzielonego i wartość klucza podstawowego dla przestrzeni nazw Event Hubs. Pobierz nazwę i wartość klucza dostępu, postępując zgodnie z instrukcjami podanymi w polu [pobieranie parametrów połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Domyślna nazwa klucza dostępu to **RootManageSharedAccessKey**. Skopiuj nazwę klucza i wartość klucza podstawowego do użycia w dalszej części tego instruktażu.
- Środowisko Python 2,7 i 3,5 lub nowsze z `pip` zainstalowane i zaktualizowane.
- Pakiet języka Python dla Event Hubs. Aby zainstalować pakiet, Uruchom to polecenie w wierszu polecenia zawierającym Język Python w ścieżce:

    ```cmd
    pip install azure-eventhub
    ```

    Zainstaluj ten pakiet do otrzymywania zdarzeń za pomocą usługi Azure Blob Storage jako magazynu punktów kontrolnych.

    ```cmd
    pip install azure-eventhub-checkpointstoreblobaio
    ```

## <a name="send-events"></a>Wysyłanie zdarzeń
W tej sekcji utworzysz skrypt języka Python służący do wysyłania zdarzeń do centrum zdarzeń utworzonego wcześniej.

1. Otwórz swój ulubiony Edytor Python, taki jak [Visual Studio Code](https://code.visualstudio.com/)
2. Tworzenie skryptu o nazwie **send.py**. Ten skrypt wysyła partię zdarzeń do utworzonego wcześniej centrum zdarzeń.
3. Wklej poniższy kod do send.py. Szczegóły można znaleźć w komentarzach do kodu.

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData

    async def run():
        # create a producer client to send messages to the event hub
        # specify connection string to your event hubs namespace and
            # the event hub name
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # create a batch
            event_data_batch = await producer.create_batch()

            # add events to the batch
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))

            # send the batch of events to the event hub
            await producer.send_batch(event_data_batch)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())

    ```

    > [!NOTE]
    > Aby uzyskać pełny kod źródłowy z bardzo przydatnymi komentarzami, zobacz [ten plik w witrynie GitHub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py)

## <a name="receive-events"></a>Odbieranie zdarzeń
Ten przewodnik Szybki Start używa Blob Storage platformy Azure jako magazynu punktów kontrolnych. Magazyn punktów kontrolnych jest używany do utrwalania punktów kontrolnych (ostatnia pozycja odczytu).  

### <a name="create-an-azure-storage-and-a-blob-container"></a>Tworzenie usługi Azure Storage i kontenera obiektów BLOB
Wykonaj następujące kroki, aby utworzyć konto usługi Azure Storage w tym kontenerze obiektów BLOB.

1. [Utwórz konto usługi Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Tworzenie kontenera obiektów BLOB](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Pobierz parametry połączenia z kontem magazynu](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Zanotuj parametry połączenia i nazwę kontenera. Będziesz ich używać w kodzie Receive.


### <a name="create-python-script-to-receive-events"></a>Utwórz skrypt języka Python do odbierania zdarzeń

W tej sekcji utworzysz skrypt języka Python do odbierania zdarzeń z centrum zdarzeń:

1. Otwórz swój ulubiony Edytor Python, taki jak [Visual Studio Code](https://code.visualstudio.com/)
2. Tworzenie skryptu o nazwie **recv.py**.
3. Wklej poniższy kod do recv.py. Szczegóły można znaleźć w komentarzach do kodu.

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore


    async def on_event(partition_context, event):
        # print the event data
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))

        # update the checkpoint so that the program doesn't read the events
        # that it has already read when you run it next time
        await partition_context.update_checkpoint(event)

    async def main():
        # create an Azure blob checkpoint store to store the checkpoints
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")

        # create a consumer client for the event hub
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # call the receive method
            await client.receive(on_event=on_event)

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # run the main method
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > Aby uzyskać pełny kod źródłowy z bardzo przydatnymi komentarzami, zobacz [ten plik w witrynie GitHub](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py)


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

Komunikaty, które zostały wysłane do centrum zdarzeń, powinny być widoczne w oknie odbiorcy.


## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki Start zostały wysłane i odebrane zdarzenia asynchronicznie. Aby dowiedzieć się, jak wysyłać i odbierać zdarzenia synchronicznie, zobacz przykłady w [tej lokalizacji](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

Wszystkie przykłady (zarówno synchronizacja, jak i Async) znajdują się w witrynie [GitHub.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)
