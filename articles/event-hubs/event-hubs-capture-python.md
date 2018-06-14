---
title: Wskazówki przechwytywania centra zdarzeń platformy Azure | Dokumentacja firmy Microsoft
description: Próbka, która używa zestawu Azure SDK Python celu pokazanie sposobu używania funkcji przechwytywania centrów zdarzeń.
services: event-hubs
documentationcenter: ''
author: djrosanova
manager: timlt
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: 97cadbde2ddedade1a8688f1380b9ff9194613e7
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2018
ms.locfileid: "28016461"
---
# <a name="event-hubs-capture-walkthrough-python"></a>Wskazówki przechwytywania centra zdarzeń: Python

Przechwyć to funkcja usługi Azure Event hubs. Można go automatycznie dostarczyć dane przesyłane strumieniowo z Centrum zdarzeń do konta magazynu obiektów Blob platformy Azure wybranych przez użytkownika. Funkcja ta ułatwia wykonywania wsadowego przetwarzania na dane przesyłane strumieniowo w czasie rzeczywistym. W tym artykule opisano sposób użycia przechwytywania centra zdarzeń języka Python. Aby uzyskać więcej informacji na temat przechwytywania centra zdarzeń, zobacz [artykuł z omówieniem](event-hubs-capture-overview.md).

W przykładzie użyto [Azure Python SDK](https://azure.microsoft.com/develop/python/) aby zademonstrować funkcja przechwytywania. Sender.py program wysyła symulowane środowiska telemetrii usługi Event Hubs w formacie JSON. Centrum zdarzeń jest skonfigurowany do używania funkcji przechwytywania do zapisania tych danych do magazynu obiektów Blob w partiach. Aplikacja capturereader.py odczytuje te obiekty BLOB i tworzy plik append na urządzenie. Aplikacja następnie zapisuje dane do plików CSV.

## <a name="what-youll-accomplish"></a>Będziesz wykonywać

1. Tworzenie konta magazynu obiektów Blob platformy Azure i kontener obiektów blob w ramach, za pomocą portalu Azure.
2. Centra zdarzeń w przestrzeni nazw, należy utworzyć za pomocą portalu Azure.
3. Tworzenie Centrum zdarzeń z funkcją przechwytywania włączone, za pomocą portalu Azure.
4. Wysyłanie danych do Centrum zdarzeń za pomocą skryptu języka Python.
5. Odczytywać pliki przechwytywania i przetwarzanie ich przy użyciu innego skryptu języka Python.

## <a name="prerequisites"></a>Wymagania wstępne

- Python 2.7.x
- Subskrypcja platformy Azure
- Aktywny [centra zdarzeń w przestrzeni nazw i zdarzeń Centrum](event-hubs-create.md)

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="create-an-azure-blob-storage-account"></a>Tworzenie konta magazynu obiektów Blob platformy Azure
1. Zaloguj się w witrynie [Azure Portal][Azure portal].
2. W okienku po lewej stronie portalu, wybierz **nowy** > **magazynu** > **konta magazynu**.
3. Zakończenie zaznaczenia w **utworzyć konto magazynu** okienka, a następnie wybierz **Utwórz**.
   
   ![W okienku "Tworzenie konta magazynu"][1]
4. Po **wdrożeń zakończyło się pomyślnie** wiadomości, wybierz nazwę nowego konta magazynu i w **Essentials** okienka, a następnie wybierz **obiekty BLOB**. Gdy **usługa Blob** okienko zostanie otwarty, wybierz **+ kontener** u góry. Nazwa kontenera **przechwytywania**, a następnie Zamknij **usługa Blob** okienka.
5. Wybierz **klucze dostępu** w okienku po lewej stronie i kopiowania nazwę konta magazynu i wartości **klucz1**. Zapisz te wartości w Notatniku lub tymczasowej lokalizacji.

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Utwórz skrypt w języku Python do wysyłania zdarzeń do Centrum zdarzeń
1. Otwórz ulubionego edytora języka Python, takie jak [Visual Studio Code][Visual Studio Code].
2. Utwórz skrypt o nazwie **sender.py**. Ten skrypt wysyła 200 zdarzeń do Centrum zdarzeń. Są to proste odczyty środowiska wysyłane w formacie JSON.
3. Wklej następujący kod do sender.py:
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('INSERT YOUR EVENT HUB NAME', s)
       print y
   ```
4. Zaktualizuj poprzedni kod do użycia z nazwą przestrzeni nazw, wartość klucza i nazwy Centrum zdarzeń uzyskany po utworzeniu przestrzeni nazw usługi Event Hubs.

## <a name="create-a-python-script-to-read-your-capture-files"></a>Utwórz skrypt w języku Python do odczytu plików przechwytywania

1. Wypełnianie okienku i wybierz **Utwórz**.
2. Utwórz skrypt o nazwie **capturereader.py**. Ten skrypt odczytuje pliki przechwycony i tworzy plik na urządzeniu zapisywanie danych tylko dla tego urządzenia.
3. Wklej następujący kod do capturereader.py:
   
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
4. Wklej odpowiednie wartości dla nazwy konta magazynu i klucza w wywołaniu `startProcessing`.

## <a name="run-the-scripts"></a>Uruchom skrypty
1. Otwórz wiersz polecenia z języka Python w jego ścieżki, a następnie uruchom następujące polecenia, aby zainstalować wstępnie wymagane pakiety języka Python:
   
   ```
   pip install azure-storage
   pip install azure-servicebus
   pip install avro
   ```
   
   Jeśli masz starszą wersję albo **magazyn azure** lub **azure**, konieczne może być **— uaktualnienie** opcji.
   
   Może być również konieczne uruchom następujące polecenie (nie jest konieczny w większości systemów):
   
   ```
   pip install cryptography
   ```
2. Zmień katalog na wszędzie tam, gdzie zapisany sender.py i capturereader.py i uruchom to polecenie:
   
   ```
   start python sender.py
   ```
   
   To polecenie uruchamia nowy proces Python do uruchomienia nadawcy.
3. Poczekaj kilka minut do przechwycenia do uruchomienia. Do oryginalnego okna polecenia wpisz następujące polecenie:
   
   ```
   python capturereader.py
   ```

   Tego procesora przechwytywania korzysta z lokalnego katalogu, aby pobrać wszystkie obiekty BLOB z konta/kontenera magazynu. Przetwarza, które nie są puste, a następnie zapisuje wyniki jako pliki CSV do katalogu lokalnego.

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat usługi Event Hubs można poznać przy użyciu następujących łączy:

* [Omówienie usługi Event hubs przechwytywania][Overview of Event Hubs Capture]
* [Przykładowe aplikacje korzystające z usługi Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Przegląd usługi Event Hubs][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
