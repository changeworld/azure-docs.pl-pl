---
title: Tworzenie plików platformy Azure z Python | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrażać aplikacje Python i usług, które korzystają z plików Azure do przechowywania plików danych.
services: storage
documentationcenter: python
author: wmgries
manager: aungoo
editor: tamram
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 09/19/2017
ms.author: tamram
ms.openlocfilehash: 1102fd516b5497b4c482986b64fa7c96e9ccc54a
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738265"
---
# <a name="develop-for-azure-files-with-python"></a>Tworzenie plików platformy Azure z języka Python
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

W tym samouczku przedstawiono podstawy do tworzenia aplikacji lub usługi, które korzystają z plików Azure do przechowywania danych plików za pomocą języka Python. W tym samouczku utworzymy prostej aplikacji konsolowej ją i pokazują, jak wykonywać podstawowe działania z języka Python i plików platformy Azure:

* Tworzenie udziałów plików na platformę Azure
* Tworzenie katalogów
* Wyliczanie plików i katalogów w udziale plików na platformę Azure
* Przekazywanie, pobieranie i usuwanie pliku

> [!Note]  
> Ponieważ pliki Azure mogą uzyskiwać dostęp za pośrednictwem protokołu SMB, istnieje możliwość zapisu proste aplikacje, które uzyskują dostęp do udziału plików na platformę Azure przy użyciu standardowych klasy we/wy Python i funkcje. W tym artykule opisano sposób pisania aplikacji, które używają usługi Azure SDK Python magazynu, która używa [interfejsu API REST plików Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/file-service-rest-api) do komunikowania się do usługi pliki Azure.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Pobierz i zainstaluj magazynu Azure SDK dla języka Python

Azure Storage SDK for Python wymaga Python 2.7, 3.3, 3.4, 3.5 lub 3,6 i składa się z 4 pakietów różnych: `azure-storage-blob`, `azure-storage-file`, `azure-storage-table` i `azure-storage-queue`. W tym samouczku zamierzamy użyj `azure-storage-file` pakietu.
 
## <a name="install-via-pypi"></a>Zainstaluj za pośrednictwem PyPi

Aby zainstalować za pomocą indeksu pakietów języka Python (PyPI), wpisz:

```bash
pip install azure-storage-file
```


> [!NOTE]
> Jeśli uaktualniasz z zestawu SDK usługi Magazyn Azure dla języka Python w wersji 0.36 lub starszej, najpierw musisz ją odinstalować przy użyciu `pip uninstall azure-storage` jako już nie udostępnimy zestawu SDK usługi Magazyn dla języka Python w jednym pakiecie.
> 
> 

Dla metod instalacji alternatywny, odwiedź stronę [zestawu SDK usługi Magazyn Azure dla języka Python w usłudze Github](https://github.com/Azure/azure-storage-python/).

## <a name="set-up-your-application-to-use-azure-files"></a>Konfigurowanie aplikacji do korzystania z plików Azure
Dodaj następujący kod w górnej części dowolnego pliku źródłowego Python mają do uzyskania programowego dostępu do magazynu Azure.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Skonfiguruj połączenie do usługi pliki Azure 
`FileService` Obiektu umożliwia pracę z udziałów, katalogów i plików. Poniższy kod tworzy `FileService` przy użyciu klucza nazwy i konta konta magazynu. Zastąp `<myaccount>` i `<mykey>` z nazwą konta i klucz.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure
W poniższym przykładzie kodu, można użyć `FileService` obiekt, aby utworzyć udział, jeśli nie istnieje.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Tworzenie katalogu
Możesz również dzielić magazynu przez umieszczenie plików wewnątrz podkatalogów zamiast wszystkich z nich w katalogu głównym. Usługa pliki Azure umożliwia tworzenie katalogów tyle dopuszcza Twoje konto. Poniższy kod będzie utworzyć podkatalogu o nazwie **sampledir** w katalogu głównym.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Wyliczanie plików i katalogów w udziale plików na platformę Azure
Aby wyświetlić listę plików i katalogów w udziale, użyj **listy\_katalogów\_i\_pliki** metody. Ta metoda zwraca generator. Poniższy kod wyjścia **nazwa** z poszczególnych plików i katalogów w udziale, do konsoli.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Przekazywanie pliku 
Udział plików na platformę Azure zawiera co najmniej, katalog główny, w którym mogą znajdować się pliki. W tej sekcji dowiesz się, jak można przekazać pliku z magazynu lokalnego do katalogu głównego udziału.

Aby utworzyć plik i przekazywanie danych, użyj `create_file_from_path`, `create_file_from_stream`, `create_file_from_bytes` lub `create_file_from_text` metody. Są one wysokiego poziomu metodach podziału niezbędne, gdy rozmiar danych przekroczy 64 MB.

`create_file_from_path` wysyła zawartość pliku z określonej ścieżki i `create_file_from_stream` przekazuje zawartość z otwartego pliku/strumienia. `create_file_from_bytes` przekazuje tablicę bajtów, i `create_file_from_text` przekazuje wartość określony tekst przy użyciu określonego kodowania (wartość domyślna to UTF-8).

Poniższy przykład przekazuje zawartość **sunset.png** pliku do **mój_plik** pliku.

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
Aby pobrać dane z pliku, należy użyć `get_file_to_path`, `get_file_to_stream`, `get_file_to_bytes`, lub `get_file_to_text`. Są one wysokiego poziomu metodach podziału niezbędne, gdy rozmiar danych przekroczy 64 MB.

W poniższym przykładzie pokazano, za pomocą `get_file_to_path` do pobrania zawartości **mój_plik** plik i zapisać go do **sunset.png poza** pliku.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Usuwanie pliku
Na koniec, aby usunąć plik, należy wywołać `delete_file`.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot-preview"></a>Tworzenie migawki udziału (wersja zapoznawcza)
Można utworzyć punktu w czasie kopia udziału całego pliku.

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
Można wyświetlić listę wszystkich migawek dla akcji.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Przeglądaj migawki udziału
Istnieje możliwość przeglądania zawartości każdej migawki udziału do pobierania plików i katalogów z tego punktu w czasie.

```python
directories_and_files = list(file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Pobierz plik z migawki udziału
Można pobrać pliku z udziału migawki dla danego scenariusza przywracania.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Usuń migawki jednego udziału  
Możesz usunąć migawki jednego udziału.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Usuń udział, gdy istnieją migawki udziału
Nie można usunąć udziału, który zawiera migawki, chyba że najpierw zostaną usunięte wszystkie migawki.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>Kolejne kroki
Teraz, kiedy znasz już sposobu modyfikowania plików Azure z języka Python, skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* [Centrum deweloperów języka Python](/develop/python/)
* [Interfejs API REST usług Azure Storage](http://msdn.microsoft.com/library/azure/dd179355)
* [Magazyn Microsoft Azure SDK dla języka Python](https://github.com/Azure/azure-storage-python)