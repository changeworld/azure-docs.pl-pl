---
title: Tworzenie plików platformy Azure za pomocą języka Python | Dokumenty firmy Microsoft
description: Dowiedz się, jak tworzyć aplikacje i usługi języka Python, które używają usługi Azure Files do przechowywania danych plików.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 139e3009722761172b7bbd57805a7f5b07e55fc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68699394"
---
# <a name="develop-for-azure-files-with-python"></a>Develop for Azure Files with Python (Tworzenie oprogramowania dla usługi Azure Files przy użyciu języka Python)
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

W tym samouczku zostaną zademonstrowane podstawy korzystania z języka Python do tworzenia aplikacji lub usług, które używają usługi Azure Files do przechowywania danych plików. W tym samouczku utworzymy prostą aplikację konsoli i pokażemy, jak wykonywać podstawowe akcje za pomocą języka Python i usługi Azure Files:

* Tworzenie udziałów plików platformy Azure
* Tworzenie katalogów
* Wyliczaj pliki i katalogi w udziale plików platformy Azure
* Przekazywanie, pobieranie i usuwanie pliku

> [!Note]  
> Ponieważ usługi Azure Files mogą być dostępne za pośrednictwem SMB, istnieje możliwość pisania prostych aplikacji, które uzyskują dostęp do udziału plików platformy Azure przy użyciu standardowych klas i funkcji we/wy języka Python. W tym artykule opisano sposób pisania aplikacji korzystających z narzędzia Azure Storage Python SDK, który używa [interfejsu API REST plików azure](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) do komunikacji z usługą Azure Files.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Pobieranie i instalowanie pakietu Azure Storage SDK dla języka Python

Zestaw [SDK usługi Azure Storage dla języka Python](https://github.com/azure/azure-storage-python) wymaga języka Python 2.7, 3.3, 3.4, 3.5 lub 3.6.
 
## <a name="install-via-pypi"></a>Zainstaluj przez PyPi

Aby zainstalować za pomocą indeksu pakietów Pythona (PyPI), wpisz:

```bash
pip install azure-storage-file
```

> [!NOTE]
> Jeśli uaktualniasz z zestawu Azure Storage SDK dla języka Python w wersji 0.36 lub starszej, odinstaluj starszy zestaw SDK przed `pip uninstall azure-storage` zainstalowaniem najnowszego pakietu.

Aby uzyskać alternatywne metody instalacji, odwiedź [zestaw SDK usługi Azure Storage dla języka Python w usłudze GitHub.](https://github.com/Azure/azure-storage-python/)

## <a name="view-the-sample-application"></a>Wyświetlanie przykładowej aplikacji
f Aby wyświetlić i uruchomić przykładową aplikację, która pokazuje, jak używać języka Python z usługą Azure Files, zobacz [Usługa Azure Storage: Wprowadzenie do usługi Azure Files w języku Python](https://github.com/Azure-Samples/storage-file-python-getting-started). 

Aby uruchomić przykładową aplikację, upewnij się, że zainstalowano zarówno pakiety, `azure-storage-file` jak i `azure-storage-common` pakiety.

## <a name="set-up-your-application-to-use-azure-files"></a>Konfigurowanie aplikacji do używania plików platformy Azure
Dodaj następujące w górnej części dowolnego pliku źródłowego języka Python, w którym chcesz programowo uzyskać dostęp do usługi Azure Storage.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Konfigurowanie połączenia z usługą Azure Files 
Obiekt `FileService` umożliwia pracę z udziałami, katalogami i plikami. Poniższy kod `FileService` tworzy obiekt przy użyciu nazwy konta magazynu i klucza konta. Zastąp wartości `<myaccount>` i `<mykey>` nazwą i kluczem konta.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure
W poniższym przykładzie kodu `FileService` można użyć obiektu do utworzenia udziału, jeśli nie istnieje.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Tworzenie katalogu
Można również zorganizować magazyn, umieszczając pliki w podkatachiach zamiast mieć wszystkie z nich w katalogu głównym. Usługa Azure Files umożliwia utworzenie tylu katalogów, na ile pozwoli na to twoje konto. Poniższy kod utworzy podkatalog o nazwie **sampledir** w katalogu głównym.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Wyliczaj pliki i katalogi w udziale plików platformy Azure
Aby wyświetlić listę plików i katalogów w udziale, użyj katalogu **listy\_\_i\_** metody plików. Ta metoda zwraca generator. Poniższy kod wyprowadza **nazwę** każdego pliku i katalogu w udziale do konsoli.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Przekazywanie pliku 
Udział plików platformy Azure zawiera co najmniej katalog główny, w którym mogą przebywać pliki. W tej sekcji dowiesz się, jak przekazać plik z magazynu lokalnego do katalogu głównego udziału.

Aby utworzyć plik i przekazać `create_file_from_path`dane, użyj , `create_file_from_stream`lub `create_file_from_bytes` `create_file_from_text` metod. Są to metody wysokiego poziomu, które wykonują niezbędne fragmentowanie, gdy rozmiar danych przekracza 64 MB.

`create_file_from_path`przesyła zawartość pliku z określonej ścieżki `create_file_from_stream` i przesyła zawartość z już otwartego pliku/strumienia. `create_file_from_bytes`przesyła tablicę bajtów `create_file_from_text` i przekazuje określoną wartość tekstową przy użyciu określonego kodowania (domyślnie wartość UTF-8).

Poniższy przykład przesyła zawartość pliku **sunset.png** do pliku **myfile.**

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None,  # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

## <a name="download-a-file"></a>Pobieranie pliku
Aby pobrać dane z `get_file_to_path`pliku, `get_file_to_bytes`użyj `get_file_to_text`, `get_file_to_stream`, , lub . Są to metody wysokiego poziomu, które wykonują niezbędne fragmentowanie, gdy rozmiar danych przekracza 64 MB.

Poniższy przykład pokazuje `get_file_to_path` użycie do pobrania zawartości pliku **myfile** i zapisania go w pliku **out-sunset.png.**

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Usuwanie pliku
Na koniec, aby usunąć `delete_file`plik, zadzwoń .

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot"></a>Tworzenie migawki udziału
Można utworzyć kopię punktu w czasie całego udziału plików.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Tworzenie migawki udziału za pomocą metadanych**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Lista udziałów i migawek 
Można wyświetlić listę wszystkich migawek dla określonego udziału.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Przeglądanie migawki udziału
Można przeglądać zawartość każdej migawki udziału, aby pobrać pliki i katalogi z tego punktu w czasie.

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Pobierz plik z migawki udziału
Możesz pobrać plik z migawki udziału dla scenariusza przywracania.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Usuwanie migawki pojedynczego udziału  
Można usunąć migawkę pojedynczego udziału.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Usuwanie udziału, gdy istnieją migawki udziału
Nie można usunąć udziału zawierającego migawki, chyba że wszystkie migawki zostaną najpierw usunięte.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>Następne kroki
Teraz, gdy już wiesz, jak manipulować usługi Azure Files za pomocą języka Python, skorzystaj z tych łączy, aby dowiedzieć się więcej.

* [Centrum deweloperów języka Python](https://azure.microsoft.com/develop/python/)
* [Interfejs API REST usług Azure Storage](https://msdn.microsoft.com/library/azure/dd179355)
* [Zestaw SDK magazynu platformy Microsoft Azure dla języka Python](https://github.com/Azure/azure-storage-python)
