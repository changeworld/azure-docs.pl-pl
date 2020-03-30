---
title: Odczytywanie usług Azure Event Hubs przechwyconych danych z aplikacji języka Python (najnowsze)
description: W tym artykule pokazano, jak napisać kod języka Python do przechwytywania danych, które są wysyłane do centrum zdarzeń i odczytać przechwycone dane zdarzeń z konta magazynu platformy Azure.
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: 34583ef49b2f919391af3fe5700a558b2dc40700
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77187244"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-by-using-python-azure-eventhub-version-5"></a>Przechwytywanie danych usługi Event Hubs w usłudze Azure Storage i odczytywanie ich przy użyciu języka Python (usługa Azure eventhub w wersji 5)

Centrum zdarzeń można skonfigurować w taki sposób, aby dane wysyłane do centrum zdarzeń były przechwytywane na koncie magazynu platformy Azure lub usłudze Azure Data Lake Storage Gen 1 lub Gen 2. W tym artykule pokazano, jak napisać kod języka Python, aby wysłać zdarzenia do Centrum zdarzeń i odczytać przechwycone dane z **magazynu obiektów Blob platformy Azure**. Aby uzyskać więcej informacji na temat tej funkcji, zobacz [Omówienie funkcji przechwytywania centrów zdarzeń](event-hubs-capture-overview.md).

Ten przewodnik Szybki start używa [zestawu SDK języka Azure Python](https://azure.microsoft.com/develop/python/) do zademonstrowania funkcji przechwytywania. Aplikacja *sender.py* wysyła symulowane dane telemetryczne środowiska do centrów zdarzeń w formacie JSON. Centrum zdarzeń jest skonfigurowane do używania funkcji Przechwytywanie do zapisywania tych danych w magazynie obiektów Blob w partiach. Aplikacja *capturereader.py* odczytuje te obiekty blob i tworzy plik dołączania dla każdego urządzenia. Następnie aplikacja zapisuje dane w plikach CSV.

> [!IMPORTANT]
> Ten przewodnik Szybki start korzysta z wersji 5 sdk języka Python usługi Azure Event Hubs. Aby uzyskać przewodnik Szybki start, który używa wersji 1 SDK języka Python, zobacz [ten artykuł](event-hubs-capture-python.md). 

W ramach tego przewodnika Szybki start wykonasz następujące czynności: 

> [!div class="checklist"]
> * Utwórz konto i kontener magazynu obiektów Blob platformy Azure w witrynie Azure portal.
> * Utwórz obszar nazw centrum zdarzeń przy użyciu witryny Azure portal.
> * Utwórz centrum zdarzeń z włączoną funkcją Przechwytywanie i połącz je z kontem magazynu.
> * Wysyłaj dane do centrum zdarzeń przy użyciu skryptu Języka Python.
> * Odczytywanie i przetwarzanie plików z usługi Event Hubs Capture przy użyciu innego skryptu języka Python.

## <a name="prerequisites"></a>Wymagania wstępne

- Python 2.7 i 3.5 lub nowsze, z zainstalowanym i zaktualizowanym programem PIP.  
- Subskrypcja platformy Azure. Jeśli go nie masz, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.  
- Aktywny obszar nazw centrów zdarzeń i centrum zdarzeń.
[Utwórz obszar nazw Centrów zdarzeń i centrum zdarzeń w obszarze nazw](event-hubs-create.md). Nagraj nazwę obszaru nazw Centrum zdarzeń, nazwę centrum zdarzeń i podstawowy klucz dostępu dla obszaru nazw. Aby uzyskać klucz dostępu, zobacz [Uzyskiwanie ciągu połączenia usługi Event Hubs](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Domyślna nazwa klucza to *RootManageSharedAccessKey*. Do tego szybkiego startu potrzebny jest tylko klucz podstawowy. Nie potrzebujesz ciągu połączenia.  
- Konto magazynu platformy Azure, kontener obiektów blob na koncie magazynu i parametry połączenia z kontem magazynu. Jeśli nie masz tych elementów, wykonaj następujące czynności:  
    1. [Tworzenie konta magazynu platformy Azure](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal)  
    1. [Tworzenie kontenera obiektów blob na koncie magazynu](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
    1. [Pobierz ciąg połączenia z kontem magazynu](../storage/common/storage-configure-connection-string.md#view-and-copy-a-connection-string)

    Pamiętaj, aby nagrać ciąg połączenia i nazwę kontenera do późniejszego użycia w tym przewodniku Szybki start.  
- Włącz funkcję Przechwytywanie dla centrum zdarzeń. Aby to zrobić, postępuj zgodnie z instrukcjami w [włącz przechwytywanie centrów zdarzeń przy użyciu witryny Azure portal](event-hubs-capture-enable-through-portal.md). Wybierz konto magazynu i kontener obiektów blob utworzony w poprzednim kroku. Funkcję można również włączyć podczas tworzenia centrum zdarzeń.  

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Tworzenie skryptu języka Python w celu wysyłania zdarzeń do centrum zdarzeń
W tej sekcji utworzysz skrypt języka Python, który wysyła 200 zdarzeń (10 urządzeń * 20 zdarzeń) do centrum zdarzeń. Te zdarzenia są przykładowym odczytem środowiska, który jest wysyłany w formacie JSON. 

1. Otwórz swój ulubiony edytor Pythona, na przykład [Visual Studio Code][Visual Studio Code].
2. Utwórz skrypt o nazwie *sender.py*. 
3. Wklej poniższy kod do *sender.py*. 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # This script simulates the production of events for 10 devices.
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # Create a producer client to produce and publish events to the event hub.
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # For each device, produce 20 events. 
        event_data_batch = producer.create_batch() # Create a batch. You will add events to the batch later. 
        for dev in devices:
            # Create a dummy reading.
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # Convert the reading into a JSON string.
            event_data_batch.add(EventData(s)) # Add event data to the batch.
        producer.send_batch(event_data_batch) # Send the batch of events to the event hub.
    
    # Close the producer.    
    producer.close()
    ```
4. Zastąp następujące wartości w skryptach:  
    * Zamień `EVENT HUBS NAMESPACE CONNECTION STRING` na ciąg połączenia dla obszaru nazw Centrum zdarzeń.  
    * Zamień `EVENT HUB NAME` na nazwę centrum zdarzeń.  
5. Uruchom skrypt, aby wysłać zdarzenia do centrum zdarzeń.  
6. W witrynie Azure portal można sprawdzić, czy centrum zdarzeń odebrało wiadomości. Przełącz się do widoku **Wiadomości** w sekcji **Metryki.** Odśwież stronę, aby zaktualizować wykres. Może upłynąć kilka sekund, aby strona wyświetliła, że wiadomości zostały odebrane. 

    [![Sprawdź, czy centrum zdarzeń odebrało wiadomości](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Tworzenie skryptu języka Python w celu odczytu plików przechwytywania
W tym przykładzie przechwycone dane są przechowywane w magazynie obiektów Blob platformy Azure. Skrypt w tej sekcji odczytuje przechwycone pliki danych z konta magazynu platformy Azure i generuje pliki CSV, aby łatwo otwierać i wyświetlać. Zobaczysz 10 plików w bieżącym katalogu roboczym aplikacji. Pliki te będą zawierać odczyty środowiskowe dla 10 urządzeń. 

1. W edytorze Pythona utwórz skrypt o nazwie *capturereader.py*. Ten skrypt odczytuje przechwycone pliki i tworzy plik dla każdego urządzenia, aby zapisać dane tylko dla tego urządzenia.
2. Wklej poniższy kod do *capturereader.py*. 
   
    ```python
    import os
    import string
    import json
    import uuid
    import avro.schema
    
    from azure.storage.blob import ContainerClient, BlobClient
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    
    
    def processBlob2(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed_json = json.loads(reading["Body"])
            if not 'id' in parsed_json:
                return
            if not parsed_json['id'] in dict:
                list = []
                dict[parsed_json['id']] = list
            else:
                list = dict[parsed_json['id']]
                list.append(parsed_json)
        reader.close()
        for device in dict.keys():
            filename = os.getcwd() + '\\' + str(device) + '.csv'
            deviceFile = open(filename, "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
    
    def startProcessing():
        print('Processor started using path: ' + os.getcwd())
        # Create a blob container client.
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # List all the blobs in the container.
        for blob in blob_list:
            # Content_length == 508 is an empty file, so process only content_length > 508 (skip empty files).        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # Create a blob client for the blob.
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # Construct a file name based on the blob name.
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # Open the file to write. Create it if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # Write blob contents into the file.
                processBlob2(cleanName) # Convert the file into a CSV file.
                os.remove(cleanName) # Remove the original downloaded file.
                # Delete the blob from the container after it's read.
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
3. Zamień `AZURE STORAGE CONNECTION STRING` na parametry połączenia dla konta magazynu platformy Azure. Nazwa kontenera utworzonego w tym przewodniku Szybki start to *przechwytywanie*. Jeśli użyto innej nazwy kontenera, należy zastąpić *przechwytywanie* nazwą kontenera na koncie magazynu. 

## <a name="run-the-scripts"></a>Uruchamianie skryptów
1. Otwórz wiersz polecenia, który ma Pythona w swojej ścieżce, a następnie uruchom te polecenia, aby zainstalować pakiety wstępne języka Python:
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```
2. Zmień katalog na katalog, w którym zostały zapisane *sender.py* i *capturereader.py*, i uruchom to polecenie:
   
   ```
   python sender.py
   ```
   
   To polecenie uruchamia nowy proces Pythona, aby uruchomić nadawcę.
3. Poczekaj kilka minut na uruchomienie przechwytywania, a następnie wprowadź następujące polecenie w oryginalnym oknie polecenia:
   
   ```
   python capturereader.py
   ```

   Ten procesor przechwytywania używa katalogu lokalnego do pobrania wszystkich obiektów blob z konta magazynu i kontenera. Przetwarza wszystkie, które nie są puste i zapisuje wyniki jako pliki CSV w katalogu lokalnym.

## <a name="next-steps"></a>Następne kroki
Sprawdź [przykłady Pythona na GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples). 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
