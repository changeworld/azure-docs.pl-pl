---
title: Wysyłanie lub odbieranie zdarzeń z usługi Azure Event Hubs przy użyciu języka Python (najnowsze)
description: Ten artykuł zawiera przewodnik do tworzenia aplikacji języka Python, która wysyła/odbiera zdarzenia do/z usługi Azure Event Hubs przy użyciu najnowszego pakietu azure eventhub w wersji 5.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 7c971dcac702318d15a27736828092e987468ca3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77162977"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub-version-5"></a>Wysyłanie zdarzeń do lub odbieranie zdarzeń z centrów zdarzeń przy użyciu języka Python (usługa azure-eventhub w wersji 5)
Ten przewodnik Szybki start pokazuje, jak wysyłać zdarzenia i odbierać zdarzenia z centrum zdarzeń przy użyciu pakietu **języka Azure Eventhub w wersji 5** języka Python.

> [!IMPORTANT]
> Ten przewodnik Szybki start korzysta z najnowszego pakietu azure eventhub w wersji 5. Aby uzyskać szybki start, który używa starego pakietu azure eventhub w wersji 1, zobacz [Wysyłanie i odbieranie zdarzeń przy użyciu usługi azure-eventhub w wersji 1](event-hubs-python-get-started-send.md). 

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli jesteś nowy w usłudze Azure Event Hubs, zobacz [Centrum zdarzeń omówienie](event-hubs-about.md) przed rozpoczęciem tego przewodnika Szybki start. 

Do wykonania kroków tego przewodnika Szybki start niezbędne jest spełnienie następujących wymagań wstępnych:

- **Subskrypcja platformy Microsoft Azure**. Aby korzystać z usług platformy Azure, w tym usługi Azure Event Hubs, potrzebujesz subskrypcji.  Jeśli nie masz istniejącego konta platformy Azure, możesz zarejestrować się w celu [uzyskania bezpłatnej wersji próbnej](https://azure.microsoft.com/free/) lub skorzystać z korzyści dla subskrybenta MSDN podczas [tworzenia konta.](https://azure.microsoft.com)
- Python 2.7 lub 3.5 lub nowsza, z zainstalowanym i zaktualizowanym programem PIP.
- Pakiet Języka Python dla centrów zdarzeń. 

    Aby zainstalować pakiet, uruchom to polecenie w wierszu polecenia, w który ma Pythona w swojej ścieżce:

    ```cmd
    pip install azure-eventhub
    ```

    Zainstaluj następujący pakiet do odbierania zdarzeń przy użyciu magazynu obiektów Blob platformy Azure jako magazynu punktu kontrolnego:

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```
- **Utwórz obszar nazw Centrów zdarzeń i centrum zdarzeń**. Pierwszym krokiem jest użycie [witryny Azure Portal](https://portal.azure.com) do utworzenia obszaru nazw typu Event Hubs i uzyskania poświadczeń zarządzania, których aplikacja potrzebuje do komunikowania się z centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md). Następnie pobierz **ciąg połączenia dla obszaru nazw Centrum zdarzeń,** postępując zgodnie z instrukcjami z artykułu: [Pobierz parametry połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Ciąg połączenia należy użyć w dalszej części tego przewodnika Szybki start.

## <a name="send-events"></a>Wysyłanie zdarzeń
W tej sekcji utworzysz skrypt języka Python, aby wysłać zdarzenia do centrum zdarzeń, które zostały utworzone wcześniej.

1. Otwórz swój ulubiony edytor Pythona, na przykład [Visual Studio Code](https://code.visualstudio.com/).
2. Utwórz skrypt o nazwie *send.py*. Ten skrypt wysyła partię zdarzeń do centrum zdarzeń, które zostały utworzone wcześniej.
3. Wklej następujący kod do *send.py:*

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
    > Pełny kod źródłowy, w tym komentarze informacyjne, przejdź do [strony GitHub send_async.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).

## <a name="receive-events"></a>Odbieranie zdarzeń
Ten przewodnik Szybki start używa magazynu obiektów Blob platformy Azure jako magazynu punktów kontrolnych. Magazyn punktu kontrolnego jest używany do utrwalania punktów kontrolnych (czyli ostatnio odczytu pozycji).  

### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Tworzenie konta magazynu platformy Azure i kontenera obiektów blob
Utwórz konto magazynu platformy Azure i kontener obiektów blob w nim, wykonując następujące kroki:

1. [Tworzenie konta usługi Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Tworzenie kontenera obiektów blob](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Pobierz ciąg połączenia z kontem magazynu](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

Pamiętaj, aby nagrać parametry połączenia i nazwę kontenera do późniejszego użycia w kodzie odbierania.


### <a name="create-a-python-script-to-receive-events"></a>Tworzenie skryptu języka Python w celu odbierania zdarzeń

W tej sekcji utworzysz skrypt języka Python, aby odbierać zdarzenia z centrum zdarzeń:

1. Otwórz swój ulubiony edytor Pythona, na przykład [Visual Studio Code](https://code.visualstudio.com/).
2. Utwórz skrypt o nazwie *recv.py*.
3. Wklej następujący kod do *recv.py:*

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
    > Pełny kod źródłowy wraz z dodatkowymi komentarzami informacyjnymi można znaleźć na [stronie GitHub recv_with_checkpoint_store_async.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py).


### <a name="run-the-receiver-app"></a>Uruchamianie aplikacji odbiorcy

Aby uruchomić skrypt, otwórz wiersz polecenia, który ma Pythona w swojej ścieżce, a następnie uruchom to polecenie:

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>Uruchamianie aplikacji nadawcy

Aby uruchomić skrypt, otwórz wiersz polecenia, który ma Pythona w swojej ścieżce, a następnie uruchom to polecenie:

```bash
python send.py
```

Okno odbiornika powinny wyświetlać komunikaty, które zostały wysłane do centrum zdarzeń.


## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start zdarzenia zostały wysłane i odebrane asynchronicznie. Aby dowiedzieć się, jak synchronicznie wysyłać i odbierać zdarzenia, przejdź do [strony sync_samples GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

Dla wszystkich przykładów (zarówno synchroniczne i asynchroniczne) w usłudze GitHub, przejdź do [biblioteki klienta usługi Azure Event Hubs dla przykładów języka Python.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)
