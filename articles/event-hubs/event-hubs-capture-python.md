---
title: Odczyt przechwycone dane z aplikacji języka Python — Azure Event Hubs | Dokumentacja firmy Microsoft
description: Przykład, który korzysta z zestawu Azure Python SDK w celu pokazanie sposobu używania funkcji przechwytywania usługi Event Hubs.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 639bc4ff9c69bca3d5f8bca6967bfc3e8e6a13d4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60822413"
---
# <a name="event-hubs-capture-walkthrough-python"></a>Wskazówki koncentratory przechwytywania zdarzeń: Python

Przechwytywanie jest funkcją usługi Azure Event Hubs. Służy on do automatycznie dostarczać strumień danych do konta magazynu obiektów Blob platformy Azure wybranym Centrum zdarzeń. Ta możliwość ułatwia przeprowadzenie wsadowo na danych przesyłanych strumieniowo w czasie rzeczywistym. W tym artykule opisano sposób używania funkcji przechwytywania usługi Event Hubs za pomocą języka Python. Aby uzyskać więcej informacji na temat przechwytywania usługi Event Hubs, zobacz [artykuł z omówieniem](event-hubs-capture-overview.md).

W tym przykładzie użyto [zestawu Azure Python SDK](https://azure.microsoft.com/develop/python/) do zademonstrowania funkcji przechwytywania. Sender.py program wysyła symulowanych danych telemetrycznych środowiska usługi Event Hubs w formacie JSON. Centrum zdarzeń jest skonfigurowany do używania funkcji przechwytywania do zapisywania tych danych do magazynu obiektów Blob w partiach. Aplikacja capturereader.py odczytuje tych obiektów blob i tworzy plik dołączania na każdym urządzeniu. Aplikacja następnie zapisuje dane do plików CSV.

## <a name="what-youll-accomplish"></a>Będzie wykonywać

1. Utwórz konto magazynu obiektów Blob platformy Azure i kontener obiektów blob w ramach, za pomocą witryny Azure portal.
2. Tworzenie przestrzeni nazw usługi Event Hubs przy użyciu witryny Azure portal.
3. Tworzenie Centrum zdarzeń za pomocą funkcji przechwytywania, włączone za pomocą witryny Azure portal.
4. Wysłać dane do Centrum zdarzeń za pomocą skryptu języka Python.
5. Odczytywanie plików przechwytywania i przetwarzać je przy użyciu innego skryptu języka Python.

## <a name="prerequisites"></a>Wymagania wstępne

- Python 2.7.x
- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
- Aktywne [usługi Event Hubs przestrzeni nazw i Centrum zdarzeń](event-hubs-create.md). 
- Włącz **przechwytywania** funkcji dla Centrum zdarzeń, wykonując instrukcje z: [Włączanie przechwytywania usługi Event Hubs przy użyciu witryny Azure portal](event-hubs-capture-enable-through-portal.md)

## <a name="create-an-azure-blob-storage-account"></a>Tworzenie konta magazynu obiektów Blob platformy Azure
1. Zaloguj się w witrynie [Azure Portal][Azure portal].
2. W okienku po lewej stronie portalu, wybierz **New** > **magazynu** > **konta magazynu**.
3. Wykonaj zaznaczenia w **Tworzenie konta magazynu** okienka, a następnie wybierz **Utwórz**.
   
   ![Okienko "Tworzenie konta magazynu"][1]
4. Po wyświetleniu **wdrożenia zakończone pomyślnie** komunikatu, wybierz nazwę nowego konta magazynu i w **Essentials** okienka, a następnie wybierz **obiektów blob**. Gdy **usługi Blob service** zostanie otwarte okienko, wybierz **+ kontener** u góry. Nazwa kontenera **przechwytywania**, a następnie Zamknij **usługi Blob service** okienka.
5. Wybierz **klucze dostępu** w okienku po lewej stronie a następnie skopiuj nazwę konta magazynu i wartość **klucz1**. Zapisz te wartości w Notatniku lub innej tymczasowej lokalizacji.

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Utwórz skrypt w języku Python do wysyłania zdarzeń do Centrum zdarzeń
1. Otwórz ulubionym edytorze języka Python, takich jak [programu Visual Studio Code][Visual Studio Code].
2. Tworzenie skryptu o nazwie **sender.py**. Ten skrypt wysyła 200 zdarzenia do Centrum zdarzeń. Są one proste odczyty środowiska wysłana w formacie JSON.
3. Wklej następujący kod do sender.py:
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus.control_client import ServiceBusService
   
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
4. Zaktualizuj poprzedni kod, aby używać nazwy obszaru nazw, wartość klucza i nazwy Centrum zdarzeń, które uzyskane podczas tworzenia przestrzeni nazw usługi Event Hubs.

## <a name="create-a-python-script-to-read-your-capture-files"></a>Utworzyć skrypt języka Python, aby odczytywać Twoje pliki przechwytywania

1. Wypełnij okienku, a następnie wybierz **Utwórz**.
2. Tworzenie skryptu o nazwie **capturereader.py**. Ten skrypt odczytuje pliki przechwycony i tworzy plik na urządzeniu tylko zapisywanie danych tylko dla tego urządzenia.
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
4. Wklej odpowiednie wartości dla nazwy konta magazynu i klucz w wywołaniu `startProcessing`.

## <a name="run-the-scripts"></a>Uruchom skrypty
1. Otwórz wiersz polecenia, którego języka Python w ścieżce, a następnie uruchom następujące polecenia, aby zainstalować wstępnie wymagane pakiety języka Python:
   
   ```
   pip install azure-storage
   pip install azure-servicebus
   pip install avro
   ```
   
   Jeśli masz starszą wersję albo **usługi azure storage** lub **azure**, może być konieczne użycie **— uaktualnienie** opcji.
   
   Również może być konieczne uruchomienie następującego polecenia (opcja nie jest wymagane w większości systemów):
   
   ```
   pip install cryptography
   ```
2. Zmień katalog na wszędzie tam, gdzie został zapisany sender.py i capturereader.py i uruchom następujące polecenie:
   
   ```
   start python sender.py
   ```
   
   To polecenie uruchamia nowy proces języka Python do uruchomienia nadawcy.
3. Poczekaj kilka minut do przechwycenia do uruchomienia. Następnie wpisz następujące polecenie w oknie oryginalnego polecenia:
   
   ```
   python capturereader.py
   ```

   Tego procesora przechwytywania korzysta z lokalnego katalogu, aby pobrać wszystkie obiekty BLOB z kontenera/konta magazynu. Tych, które nie są puste przetwarza i zapisuje wyniki jako plików CSV do katalogu lokalnego.

## <a name="next-steps"></a>Kolejne kroki

Możesz dowiedzieć się więcej na temat usługi Event Hubs, korzystając z następujących linków:

* [Omówienie usługi Event Hubs przechwytywania][Overview of Event Hubs Capture]
* [Przykładowe aplikacje korzystające z usługi Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Przegląd usługi Event Hubs][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
