---
title: Wysyłanie i odbieranie komunikatów — usługi Azure Event Hubs przy użyciu języka Python | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wysyłać zdarzenia do odbierania zdarzeń z i Przechwytywanie zdarzeń przesyłanych strumieniowo za pomocą usługi Event Hubs przy użyciu języka Python.
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2018
ms.author: shvija
ms.openlocfilehash: 0960862da9e65cff4d957e97a7488dc7a245651a
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742122"
---
# <a name="how-to-use-azure-event-hubs-from-a-python-application"></a>Jak używać usługi Azure Event Hubs z poziomu aplikacji Python
Azure Event Hubs to platforma do pozyskiwania i strumieniowego przesyłania danych, która umożliwia odbieranie i przetwarzanie milionów zdarzeń na sekundę. Usługa Event Hubs pozwala przetwarzać i przechowywać zdarzenia, dane lub dane telemetryczne generowane przez rozproszone oprogramowanie i urządzenia. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Aby uzyskać więcej informacji, zobacz [wprowadzenie do usługi Event Hubs](event-hubs-what-is-event-hubs.md). 

Ten artykuł zawiera linki do artykułów, które pokazują, jak wykonać następujące zadania z aplikacji napisanych w **Python**:

- [Wysyłanie zdarzeń do Centrum zdarzeń](#send-events-to-event-hubs)
- [Odbieranie zdarzeń z Centrum zdarzeń](#receive-events-from-event-hubs)
- Odczytaj przechwycone dane zdarzeń z usługi Azure Storage. 

## <a name="prerequisites"></a>Wymagania wstępne
- Utwórz centrum zdarzeń, wykonując następujące czynności: [Azure Portal](event-hubs-create.md), szablon [interfejsu wiersza polecenia platformy Azure](event-hubs-quickstart-cli.md), [Azure PowerShell](event-hubs-quickstart-powershell.md), [Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub.md). 
- Python 3.4 lub nowszy zainstalowany na tym komputerze.

## <a name="install-python-package"></a>Zainstaluj pakiet języka Python

Aby zainstalować pakiet języka Python dla usługi Event Hubs, Otwórz okno wiersza polecenia, którego języka Python w ścieżce, a następnie uruchom następujące polecenie: 

```bash
pip install azure-eventhub
```

## <a name="send-events-to-event-hubs"></a>Wysyłanie zdarzeń do usługi Event Hubs
Poniższy kod pokazuje, jak wysyłać zdarzenia do Centrum zdarzeń z aplikacji w języku Python: 

1. Utwórz zmienne do przechowywania adresu URL w Centrum zdarzeń, nazwę klucza i wartości klucza. 

    ```python
    # Import classes from Event Hubs python package
    from azure.eventhub import EventHubClient, Receiver, Offset
    
    # Address can be in either of these formats:
    # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
    # "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
    # For example:
    ADDRESS = "amqps://<MyEventHubNamspaceName>.servicebus.windows.net/<MyEventHubName>"
    
    # SAS policy and key are not required if they are encoded in the URL
    USER = "<Name of the access key. Default name: RootManageSharedAccessKey>"
    KEY = "<The access key>"
    ```

2. Tworzenie klienta usługi Event Hubs, Dodawanie nadawcy, uruchom klienta, wysyłanie zdarzeń za pomocą nadawcy i następnie Zatrzymaj klienta, gdy wszystko będzie gotowe. 

    ```python
    # Create an Event Hubs client
    client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
    
    # Add a sender to the client
    sender = client.add_sender(partition="0")
    
    # Run the Event Hub client
    client.run()
    
    # Send event to the event hub
    sender.send(EventData("<MyEventData>"))
    
    # Stop the Event Hubs client
    client.stop()
    
    ```

Aby uzyskać kompletny samouczek na temat sposobu wysyłania zdarzeń do Centrum zdarzeń z aplikacji napisanych w języku Python, zobacz [w tym artykule](event-hubs-python-get-started-send.md).

## <a name="receive-events-from-event-hubs"></a>Odbieranie zdarzeń z usługi Event Hubs
Poniższy kod przedstawia sposób odbierać zdarzenia z Centrum zdarzeń z aplikacji w języku Python: 

```python

# Create an Event Hubs client
client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)

# Add a receiver to the client
receiver = client.add_receiver(
    CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)

# Run the Event Hubs client
client.run()

# Receive event data from the event hub
for event_data in receiver.receive(timeout=100):
    last_offset = event_data.offset
    last_sn = event_data.sequence_number
    print("Received: {}, {}".format(last_offset, last_sn))

# Stop the Event Hubs client
client.stop()
```

Aby uzyskać kompletny samouczek na temat sposobu odbieranie zdarzeń z Centrum zdarzeń z aplikacji napisanych w języku Python, zobacz [w tym artykule](event-hubs-python-get-started-receive.md)

## <a name="read-capture-event-data-from-azure-storage"></a>Odczyt przechwytywania zdarzeń danych z usługi Azure Storage
Poniższy kod pokazuje, jak odczytywać przechwycone dane zdarzeń przechowywane w **magazynie obiektów blob platformy Azure** z aplikacji języka Python: Włącz funkcję **przechwytywania** dla centrum zdarzeń, wykonując następujące instrukcje: [Włącz przechwytywanie Event Hubs przy użyciu Azure Portal](event-hubs-capture-enable-through-portal.md). Następnie wyślij niektóre zdarzenia do Centrum zdarzeń przed sprawdzeniem kodu. 

```python
import os
import string
import json
import avro.schema
from avro.datafile import DataFileReader, DataFileWriter
from avro.io import DatumReader, DatumWriter
from azure.storage.blob import BlockBlobService

def processBlob(filename):
    reader = DataFileReader(open(filename, 'rb'), DatumReader())
    dict = {}
    for reading in reader:
        parsed_json = json.loads(reading["Body"])
        if not 'id' in parsed_json:
            return
        if not dict.has_key(parsed_json['id']):
            list = []
            dict[parsed_json['id']] = list
        else:
            list = dict[parsed_json['id']]
            list.append(parsed_json)
    reader.close()
    for device in dict.keys():
        deviceFile = open(device + '.csv', "a")
        for r in dict[device]:
            deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')

def startProcessing(accountName, key, container):
    print 'Processor started using path: ' + os.getcwd()
    block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
    generator = block_blob_service.list_blobs(container)
    for blob in generator:
        #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
        if blob.properties.content_length > 508:
            print('Downloaded a non empty blob: ' + blob.name)
            cleanName = string.replace(blob.name, '/', '_')
            block_blob_service.get_blob_to_path(container, blob.name, cleanName)
            processBlob(cleanName)
            os.remove(cleanName)
        block_blob_service.delete_blob(container, blob.name)
startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'capture')
```

Aby uzyskać kompletny samouczek dotyczący sposobu odczytywania przechwyconych danych usługi Event Hubs w usłudze Azure blob storage z poziomu aplikacji napisanych w języku Python, zobacz [w tym artykule](event-hubs-capture-python.md)

## <a name="github-samples"></a>Przykłady dla usługi GitHub
Można znaleźć więcej przykładów w języku Python w [repozytorium azure zdarzenia koncentratorów — python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs).

## <a name="next-steps"></a>Kolejne kroki
Zapoznaj się z artykułami w sekcji pojęcia, zaczynając od [omówienie funkcji usługi Event Hubs](event-hubs-features.md).
