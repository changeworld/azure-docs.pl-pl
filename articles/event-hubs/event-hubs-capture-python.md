---
title: 'Szybki start: odczytywanie przechwyconych danych z aplikacji Języka Python — Usługi Azure Event Hubs'
description: 'Szybki start: skrypty, które używają zestawu SDK języka Azure Python w celu zademonstrowania funkcji przechwytywania centrów zdarzeń.'
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 01/15/2020
ms.author: shvija
ms.openlocfilehash: 6c830cf871c2ae650bb61e8b3712a664e9e405d4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77187288"
---
# <a name="quickstart-event-hubs-capture-walkthrough-python-azure-eventhub-version-1"></a>Szybki start: przewodnik przechwytywania centrów zdarzeń: Python (usługa azure-eventhub w wersji 1)

Przechwytywanie jest funkcją usługi Azure Event Hubs. Przechwytywania można użyć do automatycznego dostarczania danych przesyłania strumieniowego w centrum zdarzeń do konta magazynu obiektów Blob platformy Azure do wyboru. Ta funkcja ułatwia przetwarzanie wsadowe na danych przesyłania strumieniowego w czasie rzeczywistym. W tym artykule opisano sposób korzystania z usługi Przechwytywanie zdarzeń w języku Python. Aby uzyskać więcej informacji na temat przechwytywania centrów zdarzeń, zobacz [Przechwytywanie zdarzeń za pośrednictwem usługi Azure Event Hubs][Overview of Event Hubs Capture].

W tym instruktażu użyto [zestawu SDK języka Azure Python,](https://azure.microsoft.com/develop/python/) aby zademonstrować funkcję przechwytywania. Program *sender.py* wysyła symulowane dane telemetryczne środowiskowe do centrów zdarzeń w formacie JSON. Centrum zdarzeń używa funkcji Przechwytywanie do zapisywania tych danych do magazynu obiektów Blob w partiach. Aplikacja *capturereader.py* odczytuje te obiekty BLOB, tworzy plik dołączania dla każdego urządzenia i zapisuje dane do plików *csv* na każdym urządzeniu.

> [!WARNING]
> Ten przewodnik Szybki start jest przeznaczony dla wersji 1 sdk języka Python usługi Azure Event Hubs. Zaleca się [migrację](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md) kodu do [wersji 5 SDK języka Python](get-started-capture-python-v2.md).

W tym instruktażu: 

> [!div class="checklist"]
> * Utwórz konto i kontener magazynu obiektów Blob platformy Azure w witrynie Azure portal.
> * Włącz przechwytywanie centrów zdarzeń i kieruj go na swoje konto magazynu.
> * Wysyłaj dane do centrum zdarzeń przy użyciu skryptu Języka Python.
> * Odczytywanie i przetwarzanie plików z usługi Event Hubs Capture przy użyciu innego skryptu języka Python.

## <a name="prerequisites"></a>Wymagania wstępne

- Python 3.4 lub `pip` nowszy, z zainstalowanym i zaktualizowanym.
  
- Subskrypcja platformy Azure. Jeśli go nie masz, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
  
- Aktywny obszar nazw centrum zdarzeń i centrum zdarzeń utworzone przez postępując zgodnie z instrukcjami w [przewodniku Szybki start: Tworzenie centrum zdarzeń przy użyciu portalu Azure](event-hubs-create.md). Zanotuj nazwy obszaru nazw i centrum zdarzeń, które będą używane w dalszej części tego przewodnika. 
  
  > [!NOTE]
  > Jeśli masz już kontener magazynu do użycia, można włączyć przechwytywanie i wybrać kontener magazynu podczas tworzenia Centrum zdarzeń. 
  > 
  
- Nazwa klucza udostępnionego dostępu w centrach zdarzeń i wartość klucza podstawowego. Znajdź lub utwórz te wartości w obszarze **Zasady dostępu współdzielonego** na stronie Centra zdarzeń. Domyślna nazwa klucza dostępu to **RootManageSharedAccessKey**. Skopiuj nazwę klucza dostępu i wartość klucza podstawowego, której chcesz użyć w dalszej części tego przewodnika. 

## <a name="create-an-azure-blob-storage-account-and-container"></a>Tworzenie konta i kontenera magazynu obiektów Blob platformy Azure

Utwórz konto magazynu i kontener do użycia w przechwytywaniu. 

1. Zaloguj się do [Portalu Azure][Azure portal].
2. W lewej nawigacji wybierz pozycję **Konta magazynu**, a następnie na ekranie **Konta magazynu** wybierz pozycję **Dodaj**.
3. Na ekranie tworzenia konta magazynu wybierz grupę subskrypcji i zasobów i nadaj kontu magazynu nazwę. Pozostałe zaznaczenia można pozostawić domyślnie. Wybierz **pozycję Recenzja + utwórz**, przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**. 
   
   ![Tworzenie konta magazynu][1]
   
4. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu,** a na ekranie **Przegląd** konta magazynu wybierz pozycję **Kontenery**.
5. Na ekranie **Kontenery** wybierz pozycję **+ Kontener**. 
6. Na ekranie **Nowy kontener** nadaj kontenerowi nazwę, a następnie wybierz przycisk **OK**. Zanotuj nazwę kontenera do użycia w dalszej części przewodnika. 
7. W lewej nawigacji na ekranie **Kontenery** wybierz pozycję **Klawisze dostępu**. Skopiuj **nazwę konta magazynu**i wartość **Klucz** pod **kluczem1**, aby użyć jej w dalszej części przewodnika.
 
## <a name="enable-event-hubs-capture"></a>Włącz przechwytywanie centrów zdarzeń

1. W witrynie Azure portal przejdź do centrum zdarzeń, wybierając jego obszar nazw centrów zdarzeń z **wszystkich zasobów,** wybierając **centra zdarzeń** w lewej nawigacji, a następnie wybierając centrum zdarzeń. 
2. Na ekranie **Przegląd** centrum zdarzeń wybierz pozycję **Przechwyć zdarzenia**.
3. Na ekranie **Przechwytywanie** wybierz pozycję **Włączone**. Następnie w obszarze **Kontener usługi Azure Storage**wybierz pozycję Wybierz **kontener**. 
4. Na ekranie **Kontenery** wybierz kontener magazynu, którego chcesz użyć, a następnie wybierz pozycję **Wybierz**. 
5. Na ekranie **Przechwytywanie** wybierz pozycję **Zapisz zmiany**. 

## <a name="create-a-python-script-to-send-events-to-event-hub"></a>Tworzenie skryptu języka Python w celu wysyłania zdarzeń do Centrum zdarzeń
Ten skrypt wysyła 200 zdarzeń do centrum zdarzeń. Wydarzenia są proste odczyty środowiskowe wysyłane w JSON.

1. Otwórz swój ulubiony edytor Pythona, na przykład [Visual Studio Code][Visual Studio Code].
2. Utwórz nowy plik o nazwie *sender.py*. 
3. Wklej poniższy kod do *sender.py*. Zastąp własne wartości \<dla> obszaru nazw \<Centrów zdarzeń,> \<AccessKeyName,> \<wartości klucza podstawowego i> eventhub.
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus.control_client import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='<namespace>', shared_access_key_name='<AccessKeyName>', shared_access_key_value='<primary key value>')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('<eventhub>', s)
       print(y)
   ```
4. Zapisz plik.

## <a name="create-a-python-script-to-read-capture-files"></a>Tworzenie skryptu języka Python do odczytu plików przechwytywania

Ten skrypt odczytuje przechwycone pliki i tworzy plik dla każdego urządzenia, aby zapisać dane tylko dla tego urządzenia.

1. W edytorze Python utwórz nowy plik o nazwie *capturereader.py*. 
2. Wklej poniższy kod do *capturereader.py*. Zastąp zapisane \<wartości dla> konta \<pamięci masowej,> \<klucza dostępu do konta magazynu i>.
   
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
           if not parsed_json['id'] in dict:
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
       print('Processor started using path: ' + os.getcwd())
       block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
       generator = block_blob_service.list_blobs(container)
       for blob in generator:
           #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
           if blob.properties.content_length > 508:
               print('Downloaded a non empty blob: ' + blob.name)
               cleanName = str.replace(blob.name, '/', '_')
               block_blob_service.get_blob_to_path(container, blob.name, cleanName)
               processBlob(cleanName)
               os.remove(cleanName)
           block_blob_service.delete_blob(container, blob.name)
   startProcessing('<storageaccount>', '<storage account access key>', '<storagecontainer>')
   ```

## <a name="run-the-python-scripts"></a>Uruchamianie skryptów języka Python

1. Otwórz wiersz polecenia, który ma Pythona w swojej ścieżce i uruchom te polecenia, aby zainstalować pakiety wymagane python:
   
   ```cmd
   pip install azure-storage
   pip install azure-servicebus
   pip install avro-python3
   ```
   
   Jeśli masz wcześniejszą `azure-storage` wersję `azure`lub , może `--upgrade` być konieczne użycie tej opcji.
   
   Może być również konieczne uruchomienie następującego polecenia. Uruchomienie tego polecenia nie jest konieczne w większości systemów. 
   
   ```cmd
   pip install cryptography
   ```
   
2. Z katalogu, w którym zostały zapisane *sender.py* i *capturereader.py*, uruchom następujące polecenie:
   
   ```cmd
   start python sender.py
   ```
   
   Polecenie uruchamia nowy proces Pythona, aby uruchomić nadawcę.
   
3. Po zakończeniu działania przechwytywania uruchom następujące polecenie:
   
   ```cmd
   python capturereader.py
   ```

   Procesor przechwytywania pobiera wszystkie niepuste obiekty blob z kontenera konta magazynu i zapisuje wyniki jako pliki *csv* do katalogu lokalnego. 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o Centrach zdarzeń, zobacz: 

* [Omówienie przechwytywania centrów zdarzeń][Overview of Event Hubs Capture]
* [Przykładowe aplikacje korzystające z usługi Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Przegląd usługi Event Hubs][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
