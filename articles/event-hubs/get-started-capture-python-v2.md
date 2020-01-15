---
title: Odczytuj Event Hubs platformy Azure przechwycone dane z aplikacji języka Python | Microsoft Docs
description: W tym artykule opisano sposób pisania kodu w języku Python w celu przechwytywania danych wysyłanych do centrum zdarzeń i odczytywania przechwyconych danych zdarzeń z usługi Azure Storage.
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2019
ms.author: spelluru
ms.openlocfilehash: d7a0c757f7314e45d5b4d13273df984739912b27
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942414"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-using-python"></a>Przechwytywanie danych Event Hubs w usłudze Azure Storage i odczytywanie ich przy użyciu języka Python 
Można użyć konfigurowania centrum zdarzeń, aby dane wysyłane do centrum zdarzeń były przechwytywane w usłudze Azure Storage lub Azure Data Lake Storage. W tym artykule pokazano, jak za pomocą napisać kod języka Python wysyłać zdarzenia do centrum zdarzeń i odczytywać przechwycone dane z usługi Azure Blob Storage. Aby uzyskać więcej informacji na temat tej funkcji, zobacz [Omówienie funkcji przechwytywania Event Hubs](event-hubs-capture-overview.md).

W tym przykładzie użyto [zestawu Azure Python SDK](https://azure.microsoft.com/develop/python/) do zademonstrowania funkcji przechwytywania. Sender.py program wysyła symulowanych danych telemetrycznych środowiska usługi Event Hubs w formacie JSON. Centrum zdarzeń jest skonfigurowany do używania funkcji przechwytywania do zapisywania tych danych do magazynu obiektów Blob w partiach. Aplikacja capturereader.py odczytuje tych obiektów blob i tworzy plik dołączania na każdym urządzeniu. Aplikacja następnie zapisuje dane do plików CSV.

> [!IMPORTANT]
> Ten przewodnik Szybki Start używa wersji 5 zestawu SDK języka Python platformy Azure Event Hubs. Aby uzyskać szybki Start, który używa starej wersji 1 zestawu SDK języka Python, zobacz [ten artykuł](event-hubs-capture-python.md). Jeśli używasz wersji 1 zestawu SDK, zalecamy przeprowadzenie migracji kodu do najnowszej wersji. Aby uzyskać szczegółowe informacje, zobacz [Przewodnik migracji](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).

W ramach tego przewodnika Szybki start wykonasz następujące czynności: 

> [!div class="checklist"]
> * Utwórz w Azure Portal konto magazynu obiektów blob platformy Azure i kontener.
> * Utwórz przestrzeń nazw Event Hubs przy użyciu Azure Portal.
> * Utwórz centrum zdarzeń z włączoną funkcją przechwytywania i połącz je z kontem magazynu.
> * Wyślij dane do centrum zdarzeń przy użyciu skryptu języka Python.
> * Odczytywanie i przetwarzanie plików z Event Hubs przechwytywania przy użyciu innego skryptu języka Python.

## <a name="prerequisites"></a>Wymagania wstępne

- Środowisko Python 2,7 i 3,5 lub nowsze z `pip` zainstalowane i zaktualizowane.
- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
- [Utwórz przestrzeń nazw Event Hubs i centrum zdarzeń w przestrzeni nazw](event-hubs-create.md). Zanotuj nazwę obszaru nazw Event Hubs, nazwę centrum zdarzeń i podstawowy klucz dostępu dla przestrzeni nazw. Uzyskaj klucz dostępu, wykonując instrukcje podane w artykule: [pobieranie parametrów połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Domyślna nazwa klucza to: **RootManageSharedAccessKey**. Nie potrzebujesz parametrów połączenia dla tego samouczka. Wystarczy tylko klucz podstawowy. 
- Wykonaj następujące kroki, aby utworzyć **konto usługi Azure Storage** i **kontener obiektów BLOB**:
    1. [Utwórz konto usługi Azure Storage](/storage/common/storage-quickstart-create-account.md?tabs=azure-portal).
    2. [Utwórz kontener obiektów BLOB w magazynie](/storage/blobs/storage-quickstart-blobs-portal.md#create-a-container). 
    3. [Pobierz parametry połączenia z kontem magazynu](/storage/common/storage-configure-connection-string?#view-and-copy-a-connection-string)

        Zanotuj **Parametry połączenia** i **nazwę kontenera**. Będziesz ich używać później w kodzie. 
- Włącz funkcję **przechwytywania** dla centrum zdarzeń, wykonując następujące instrukcje: [Włączanie przechwytywania Event Hubs przy użyciu Azure Portal](event-hubs-capture-enable-through-portal.md). Wybierz konto magazynu i kontener obiektów blob, który został utworzony w poprzednim kroku. Funkcję można również włączyć podczas tworzenia centrum zdarzeń. 

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Utwórz skrypt w języku Python do wysyłania zdarzeń do Centrum zdarzeń
W tej sekcji utworzysz skrypt języka Python, który wysyła zdarzenia 200 (10 urządzeń * 20 zdarzeń) do centrum zdarzeń. Te zdarzenia to przykładowe odczytywanie informacji o środowisku wysyłane w formacie JSON. 

1. Otwórz swój ulubiony Edytor Python, taki jak [Visual Studio Code][Visual Studio Code].
2. Tworzenie skryptu o nazwie **sender.py**. 
3. Wklej poniższy kod do sender.py. Szczegóły można znaleźć w komentarzach do kodu. 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # this scripts simulates production of events for 10 devices
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # create a producer client to produce/publish events to the event hub
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # for each device, produce 20 events 
        event_data_batch = producer.create_batch() # create a batch. you will add events to the batch later. 
        for dev in devices:
            # create a dummy reading
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # convert reading into a JSON string
            event_data_batch.add(EventData(s)) # add event data to the batch
        producer.send_batch(event_data_batch) # send the batch of events to the event hub
    
    # close the producer    
    producer.close()
    ```
4. Zastąp następujące wartości w skryptach:
    1. Zastąp `EVENT HUBS NAMESPACE CONNECTION STRING` parametrami połączenia dla przestrzeni nazw Event Hubs.
    2. Zastąp `EVENT HUB NAME` nazwą centrum zdarzeń. 
5. Uruchom skrypt, aby wysłać zdarzenia do centrum zdarzeń. 
6. W Azure Portal można sprawdzić, czy centrum zdarzeń odebrało komunikaty. Przejdź do widoku **komunikaty** w sekcji **metryki** . Odśwież stronę, aby zaktualizować wykres. Wyświetlenie komunikatów zostało odebrane może potrwać kilka sekund. 

    [![sprawdzić, czy centrum zdarzeń odebrało komunikaty](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Utworzyć skrypt języka Python, aby odczytywać Twoje pliki przechwytywania
W tym przykładzie przechwycone dane są przechowywane w usłudze Azure Blob Storage. Skrypt w tej sekcji odczytuje pliki danych przechwytywania z usługi Azure Storage i generuje pliki CSV, aby można było łatwo otworzyć i wyświetlić zawartość. Zobaczysz 10 plików w bieżącym katalogu roboczym aplikacji. Te pliki będą zawierać odczyty środowiska dla 10 urządzeń. 

1. W edytorze języka Python Utwórz skrypt o nazwie **capturereader.py**. Ten skrypt odczytuje pliki przechwycony i tworzy plik na urządzeniu tylko zapisywanie danych tylko dla tego urządzenia.
2. Wklej poniższy kod do capturereader.py. Szczegóły można znaleźć w komentarzach do kodu. 
   
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
        # create a blob container client
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # list all the blobs in the container
        for blob in blob_list:
            #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # create a blob client for the blob
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # construct a file name based on the blob name
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # open the file to write. create if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # write blob contents into the file
                processBlob2(cleanName) # convert the file into a CSV file
                os.remove(cleanName) # remove the original downloaded file
                # delete the blob from the container after it's read
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
4. Zastąp `<AZURE STORAGE CONNECTION STRING>` parametrami połączenia dla konta usługi Azure Storage. Nazwa kontenera utworzonego w tym samouczku to: **Capture**. Jeśli użyto innej nazwy dla kontenera, Zastąp `capture` nazwą kontenera na koncie magazynu. 

## <a name="run-the-scripts"></a>Uruchom skrypty
1. Otwórz wiersz polecenia, którego języka Python w ścieżce, a następnie uruchom następujące polecenia, aby zainstalować wstępnie wymagane pakiety języka Python:
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```
2. Zmień katalog na wszędzie tam, gdzie został zapisany sender.py i capturereader.py i uruchom następujące polecenie:
   
   ```
   python sender.py
   ```
   
   To polecenie uruchamia nowy proces języka Python do uruchomienia nadawcy.
3. Poczekaj kilka minut do przechwycenia do uruchomienia. Następnie wpisz następujące polecenie w oknie oryginalnego polecenia:
   
   ```
   python capturereader.py
   ```

   Tego procesora przechwytywania korzysta z lokalnego katalogu, aby pobrać wszystkie obiekty BLOB z kontenera/konta magazynu. Tych, które nie są puste przetwarza i zapisuje wyniki jako plików CSV do katalogu lokalnego.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z przykładami w języku Python w witrynie GitHub [tutaj](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples). 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
