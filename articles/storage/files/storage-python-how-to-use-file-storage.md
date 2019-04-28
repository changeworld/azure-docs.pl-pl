---
title: Tworzenie oprogramowania dla usługi Azure Files za pomocą języka Python | Dokumentacja firmy Microsoft
description: Dowiedz się, jak opracować aplikacje języka Python i usług korzystających z usługi Azure Files do przechowywania danych plików.
services: storage
author: roygara
ms.service: storage
ms.devlang: python
ms.topic: article
ms.date: 12/14/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ecb3ef82196c3b6febd44850b47f467ba37facc2
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763525"
---
# <a name="develop-for-azure-files-with-python"></a>Tworzenie oprogramowania dla usługi Azure Files za pomocą języka Python
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

W tym samouczku przedstawiono podstawy korzystania z języka Python do tworzenia aplikacji lub usług, które używają usługi Azure Files do przechowywania danych plików. W tym samouczku utworzymy prostą aplikację konsolową ją i pokazują, jak wykonywać podstawowe działania za pomocą języka Python i usługi Azure Files:

* Tworzenie udziałów plików platformy Azure
* Tworzenie katalogów
* Wyliczanie plików i katalogów w udziale plików platformy Azure
* Przekazywanie, pobieranie i usuwanie pliku

> [!Note]  
> Ponieważ usługi Azure Files można uzyskać dostęp za pośrednictwem protokołu SMB, istnieje możliwość napisania prostej aplikacji uzyskujących dostęp do udziału plików platformy Azure przy użyciu standardowych klas we/wy języka Python i funkcje. W tym artykule opisano sposób pisania aplikacji korzystających z zestawu SDK języka Python magazynu platformy Azure, w którym używa [API REST usługi pliki Azure](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) na komunikowanie się z usługą Azure Files.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Pobierz i zainstaluj zestaw SDK usługi Azure Storage dla języka Python

[Zestawu SDK usługi Azure Storage dla języka Python](https://github.com/azure/azure-storage-python) wymaga Python 2.7 3.3, 3.4, 3.5 i 3.6.
 
## <a name="install-via-pypi"></a>Instalowanie za pomocą PyPi

Aby zainstalować, za pomocą indeksu pakietów języka Python (PyPI), wpisz:

```bash
pip install azure-storage-file
```

> [!NOTE]
> Jeśli uaktualniasz z zestawu SDK usługi Azure Storage dla języka Python w wersji 0.36 lub starszej, należy odinstalować starsze przy użyciu zestawu SDK `pip uninstall azure-storage` przed zainstalowaniem najnowszy pakiet.

Dla metod instalacji alternatywny, odwiedź stronę [zestawu SDK usługi Azure Storage dla języka Python w usłudze GitHub](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Wyświetl przykładowej aplikacji
f, aby wyświetlać i uruchamiać aplikacji przykładowej, który pokazuje, jak używać języka Python za pomocą usługi Azure Files, zobacz [usługi Azure Storage: Wprowadzenie do usługi Azure Files w języku Python](https://github.com/Azure-Samples/storage-file-python-getting-started). 

Aby uruchomić przykładową aplikację, upewnij się, zainstalowano zarówno `azure-storage-file` i `azure-storage-common` pakietów.

## <a name="set-up-your-application-to-use-azure-files"></a>Konfigurowanie aplikacji do użycia usługi Azure Files
Dodaj następujący kod w górnej części każdego pliku źródłowego języka Python, w której chcesz uzyskać programowy dostęp do usługi Azure Storage.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Konfigurowanie połączenia z usługą Azure Files 
`FileService` Obiekt umożliwia pracę z udziałów, katalogów i plików. Poniższy kod tworzy `FileService` przy użyciu konta nazwy i klucza konta magazynu. Zastąp wartości `<myaccount>` i `<mykey>` nazwą i kluczem konta.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure
W poniższym przykładzie kodu można użyć `FileService` obiekt, aby utworzyć udział, jeśli nie istnieje.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Tworzenie katalogu
Można również zorganizować magazynowania, umieszczając pliki wewnątrz podkatalogi zamiast ich wszystkich w katalogu głównym. Usługa pliki systemu Azure umożliwia tworzenie katalogów tyle dopuszcza Twoje konto. Poniższy kod utworzy podkatalogu o nazwie **sampledir** w katalogu głównym.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Wyliczanie plików i katalogów w udziale plików platformy Azure
Aby wyświetlić listę plików i katalogów w udziale, należy użyć **listy\_katalogi\_i\_pliki** metody. Ta metoda zwraca generator. Poniższy kod wyjścia **nazwa** z poszczególnych plików i katalogów w udziale, do konsoli.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Przekazywanie pliku 
Udział plików platformy Azure zawiera co najmniej, katalog główny, w którym mogą znajdować się pliki. W tej sekcji dowiesz się, jak można przekazać pliku z magazynu lokalnego do katalogu głównego udziału.

Aby utworzyć plik i przekazywania danych, użyj `create_file_from_path`, `create_file_from_stream`, `create_file_from_bytes` lub `create_file_from_text` metody. Są one ogólne metodach segmentu niezbędne, gdy rozmiar danych przekracza 64 MB.

`create_file_from_path` wysyła zawartość pliku z określonej ścieżki i `create_file_from_stream` przesyła zawartość z otwartego pliku/strumienia. `create_file_from_bytes` przekazuje tablicę bajtów, i `create_file_from_text` przekazuje określoną wartość tekstową przy użyciu określonego kodowania (wartość domyślna to UTF-8).

Poniższy przykład przekazuje zawartość **sunset.png** mezzanine do **myfile** pliku.

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None, # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

## <a name="download-a-file"></a>Pobieranie pliku
Aby pobrać dane z pliku, użyj `get_file_to_path`, `get_file_to_stream`, `get_file_to_bytes`, lub `get_file_to_text`. Są one ogólne metodach segmentu niezbędne, gdy rozmiar danych przekracza 64 MB.

Poniższy przykład demonstruje użycie `get_file_to_path` do pobierania zawartości **myfile** plik i zapisz go na **sunset.png poza** pliku.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Usuwanie pliku
Na koniec, aby usunąć plik, należy wywołać `delete_file`.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot"></a>Tworzenie migawki udziału
Można utworzyć punktu w czasie kopia swoje cały udział plików.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Tworzenie migawki udziału z metadanymi**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Listy udziałów i migawki 
Możesz wyświetlić listę wszystkich migawek dla określonego udziału.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Przeglądaj migawkę udziału
Możesz przeglądać zawartość migawki udziału, każdej do pobierania plików i katalogów z tego punktu w czasie.

```python
directories_and_files = list(file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Pobierz plik z migawki udziału
Możesz pobrać plik z migawki udziału dla danego scenariusza przywracania.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Usuwanie migawki pojedynczy udział  
Możesz usunąć migawkę udziału w jednym.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Usuwanie udziału, gdy istnieje migawek udziałów
Nie można usunąć udziału, który zawiera migawki, chyba że najpierw zostaną usunięte wszystkie migawki.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>Kolejne kroki
Teraz, kiedy znasz sposoby manipulowania usługi Azure Files za pomocą języka Python, skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* [Centrum deweloperów języka Python](https://azure.microsoft.com/develop/python/)
* [Interfejs API REST usług Azure Storage](https://msdn.microsoft.com/library/azure/dd179355)
* [Microsoft Azure Storage SDK dla języka Python](https://github.com/Azure/azure-storage-python)
