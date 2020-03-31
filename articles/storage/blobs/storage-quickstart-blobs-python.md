---
title: 'Szybki start: biblioteka magazynu obiektów Blob platformy Azure w wersji 12 — Python'
description: W tym przewodniku Szybki start dowiesz się, jak utworzyć kontener i obiektowy magazyn za pomocą biblioteki klienta magazynu obiektów Blob platformy Azure w wersji 12 dla języka Python. Następnie dowiesz się, jak pobrać obiekt blob na komputer lokalny i jak wyświetlać listę wszystkich obiektów blob w kontenerze.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 8daf7380e859cd2f9b5890c716f7b7d95e6c3fe4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061362"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Szybki start: zarządzanie obiektami blob za pomocą modułu SDK języka Python w wersji 12

W tym przewodniku Szybki start nauczysz się zarządzać obiektami blob za pomocą języka Python. Obiekty blob to obiekty, które mogą zawierać duże ilości danych tekstowych lub binarnych, w tym obrazy, dokumenty, multimedia strumieniowe i dane archiwum. Będziesz przesyłać, pobierać i wystawiać obiekty blob, a także tworzyć i usuwać kontenery.

[API reference documentation](/python/api/azure-storage-blob) | [Przykłady](https://docs.microsoft.com/azure/storage/common/storage-samples-python?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples) kodu źródłowego | pakietu[(Indeks pakietu Python)](https://pypi.org/project/azure-storage-blob/) | [biblioteki referencyjnej](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob)interfejsu API

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Konto usługi Azure Storage. [Utwórz konto magazynu](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/) 2.7, 3.5 lub wyższy.

> [!NOTE]
> Aby rozpocząć pracę z poprzednią wersją SDK, zobacz [Szybki start: Zarządzanie obiektami blob za pomocą sdk języka Python v2.1](storage-quickstart-blobs-python-legacy.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji przeprowadzi Cię proces przygotowania projektu do pracy z biblioteką klienta magazynu obiektów Blob platformy Azure w wersji 12 dla języka Python.

### <a name="create-the-project"></a>Tworzenie projektu

Utwórz aplikację języka Python o nazwie *blob-szybki start-v12*.

1. W oknie konsoli (takich jak cmd, PowerShell lub Bash) utwórz nowy katalog dla projektu.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Przełącz się do nowo utworzonego katalogu *blob-szybki start-v12.*

    ```console
    cd blob-quickstart-v12
    ```

1. Z boku katalogu *blob-szybki start-v12* należy utworzyć inny katalog o nazwie *data*. W tym miejscu zostaną utworzone i przechowywane pliki danych obiektów blob.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Zainstaluj pakiet

Będąc jeszcze w katalogu aplikacji, zainstaluj bibliotekę klienta magazynu obiektów `pip install` Blob platformy Azure dla pakietu Języka Python za pomocą polecenia.

```console
pip install azure-storage-blob
```

To polecenie instaluje bibliotekę klienta magazynu obiektów Blob platformy Azure dla pakietu Języka Python i wszystkie biblioteki, od których zależy. W takim przypadku jest to tylko biblioteka podstawowa platformy Azure dla języka Python.

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu projektu:

1. Otwieranie nowego pliku tekstowego w edytorze kodu
1. Dodawanie `import` instrukcji
1. Tworzenie struktury programu, w tym podstawowej obsługi wyjątków

    Oto kod:

    ```python
    import os, uuid
    from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient

    try:
        print("Azure Blob storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

1. Zapisz nowy plik jako *blob-quickstart-v12.py* w katalogu *blob-szybki start-v12.*

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Model obiektu

Usługa Azure Blob storage jest zoptymalizowana pod kątem przechowywania ogromnych ilości nieustrukturyzowanych danych. Dane bez struktury są danymi, które nie są zgodne z żadnym modelem lub definicją danych, jak na przykład dane tekstowe lub binarne. Magazyn obiektów blob oferuje trzy typy zasobów:

* Konto magazynu
* Kontener na koncie magazynu
* Obiekt blob w kontenerze

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury magazynu obiektów blob](./media/storage-blobs-introduction/blob1.png)

Użyj następujących klas języka Python do interakcji z tymi zasobami:

* [BlobServiceClient:](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) `BlobServiceClient` Klasa umożliwia manipulowanie zasobami usługi Azure Storage i kontenerami obiektów blob.
* [ContainerClient:](/python/api/azure-storage-blob/azure.storage.blob.containerclient) `ContainerClient` Klasa umożliwia manipulowanie kontenerami usługi Azure Storage i ich obiektów blob.
* [BlobClient:](/python/api/azure-storage-blob/azure.storage.blob.blobclient) `BlobClient` Klasa umożliwia manipulowanie obiektów blob usługi Azure Storage.

## <a name="code-examples"></a>Przykłady kodu

Te przykładowe fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta magazynu obiektów Blob platformy Azure dla języka Python:

* [Pobieranie parametrów połączenia](#get-the-connection-string)
* [Tworzenie kontenera](#create-a-container)
* [Przekazywanie obiektów blob do kontenera](#upload-blobs-to-a-container)
* [Wyświetlanie listy obiektów blob w kontenerze](#list-the-blobs-in-a-container)
* [Pobieranie obiektów blob](#download-blobs)
* [Usuwanie kontenera](#delete-a-container)

### <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

Poniższy kod pobiera parametry połączenia dla konta magazynu ze zmiennej środowiskowej utworzonej w sekcji [Konfigurowanie ciągu połączenia magazynu.](#configure-your-storage-connection-string)

Dodaj ten kod `try` wewnątrz bloku:

```python
# Retrieve the connection string for use with the application. The storage
# connection string is stored in an environment variable on the machine
# running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable is
# created after the application is launched in a console or with Visual Studio,
# the shell or application needs to be closed and reloaded to take the
# environment variable into account.
connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-container"></a>Tworzenie kontenera

Zdecyduj o nazwie nowego kontenera. Poniższy kod dołącza wartość UUID do nazwy kontenera, aby upewnić się, że jest unikatowa.

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać więcej informacji o nazewnictwie kontenerów i obiektów blob, zobacz temat [Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Utwórz wystąpienie klasy [BlobServiceClient,](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) wywołując [metodę from_connection_string.](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) Następnie należy wywołać [metodę create_container,](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) aby faktycznie utworzyć kontener na koncie magazynu.

Dodaj ten kod na `try` końcu bloku:

```python
# Create the BlobServiceClient object which will be used to create a container client
blob_service_client = BlobServiceClient.from_connection_string(connect_str)

# Create a unique name for the container
container_name = "quickstart" + str(uuid.uuid4())

# Create the container
container_client = blob_service_client.create_container(container_name)
```

### <a name="upload-blobs-to-a-container"></a>Przekazywanie obiektów blob do kontenera

Fragment kodu w brzmieniu:

1. Tworzy plik tekstowy w katalogu lokalnym.
1. Pobiera odwołanie do [obiektu BlobClient,](/python/api/azure-storage-blob/azure.storage.blob.blobclient) wywołując [metodę get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) w pliku [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) z sekcji [Utwórz kontener.](#create-a-container)
1. Przekazuje lokalny plik tekstowy do obiektu blob, wywołując [metodę upload_blob.](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-)

Dodaj ten kod na `try` końcu bloku:

```python
# Create a file in local data directory to upload and download
local_path = "./data"
local_file_name = "quickstart" + str(uuid.uuid4()) + ".txt"
upload_file_path = os.path.join(local_path, local_file_name)

# Write text to the file
file = open(upload_file_path, 'w')
file.write("Hello, World!")
file.close()

# Create a blob client using the local file name as the name for the blob
blob_client = blob_service_client.get_blob_client(container=container_name, blob=local_file_name)

print("\nUploading to Azure Storage as blob:\n\t" + local_file_name)

# Upload the created file
with open(upload_file_path, "rb") as data:
    blob_client.upload_blob(data)
```

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Lista obiektów blob w kontenerze, wywołując [list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) metody. W takim przypadku tylko jeden obiekt blob został dodany do kontenera, więc operacja listy zwraca tylko ten jeden obiekt blob.

Dodaj ten kod na `try` końcu bloku:

```python
print("\nListing blobs...")

# List the blobs in the container
blob_list = container_client.list_blobs()
for blob in blob_list:
    print("\t" + blob.name)
```

### <a name="download-blobs"></a>Pobieranie obiektów blob

Pobierz poprzednio utworzony obiekt blob, wywołując [metodę download_blob.](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) Przykładowy kod dodaje sufiks "POBIERZ" do nazwy pliku, dzięki czemu można zobaczyć oba pliki w lokalnym systemie plików.

Dodaj ten kod na `try` końcu bloku:

```python
# Download the blob to a local file
# Add 'DOWNLOAD' before the .txt extension so you can see both files in the data directory
download_file_path = os.path.join(local_path, str.replace(local_file_name ,'.txt', 'DOWNLOAD.txt'))
print("\nDownloading blob to \n\t" + download_file_path)

with open(download_file_path, "wb") as download_file:
    download_file.write(blob_client.download_blob().readall())
```

### <a name="delete-a-container"></a>Usuwanie kontenera

Poniższy kod czyści zasoby utworzone przez aplikację, usuwając cały kontener przy użyciu [metody delete_container.](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) Można również usunąć pliki lokalne, jeśli chcesz.

Aplikacja wstrzymuje wprowadzanie danych przez użytkownika, wywołując `input()` przed usunięciem obiektów blob, kontenera i plików lokalnych. Jest to dobra okazja, aby sprawdzić, czy zasoby zostały utworzone poprawnie, zanim zostaną usunięte.

Dodaj ten kod na `try` końcu bloku:

```python
# Clean up
print("\nPress the Enter key to begin clean up")
input()

print("Deleting blob container...")
container_client.delete_container()

print("Deleting the local source and downloaded files...")
os.remove(upload_file_path)
os.remove(download_file_path)

print("Done")
```

## <a name="run-the-code"></a>Uruchamianie kodu

Ta aplikacja tworzy plik testowy w folderze lokalnym i przekazuje go do magazynu obiektów Blob. W przykładzie następnie wyświetla listę obiektów blob w kontenerze i pobiera plik o nowej nazwie, dzięki czemu można porównać stare i nowe pliki.

Przejdź do katalogu zawierającego plik *blob-quickstart-v12.py,* a następnie `python` wykonaj następujące polecenie, aby uruchomić aplikację.

```console
python blob-quickstart-v12.py
```

Dane wyjściowe aplikacji są podobne do następującego przykładu:

```output
Azure Blob storage v12 - Python quickstart sample

Uploading to Azure Storage as blob:
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Listing blobs...
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Downloading blob to
        ./data/quickstartcf275796-2188-4057-b6fb-038352e35038DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Przed rozpoczęciem procesu oczyszczania sprawdź folder *danych* pod kątem dwóch plików. Możesz je otworzyć i sprawdzić, czy są identyczne.

Po zweryfikowaniu plików naciśnij klawisz **Enter,** aby usunąć pliki testowe i zakończyć prezentację.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak przesyłać, pobierać i wystawiać obiekty blob za pomocą języka Python.

Aby wyświetlić przykładowe aplikacje magazynu obiektów Blob, przejdź do:

> [!div class="nextstepaction"]
> [Przykłady zestawów SDK magazynu obiektów blob platformy Azure w wersji 12 python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Aby dowiedzieć się więcej, zobacz [zestaw SDK platformy Azure dla języka Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/README.md).
* Aby uzyskać samouczki, przykłady, przewodniki Szybki start i inną dokumentację, odwiedź [witrynę Azure for Python Developers](/azure/python/).
