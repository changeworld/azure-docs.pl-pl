---
title: 'Szybki start: biblioteka klienta magazynu obiektów Blob platformy Azure w wersji 2.1 dla języka Python'
description: Ten przewodnik Szybki start przedstawia tworzenie konta magazynu i kontenera w magazynie obiektów (blob). Następnie należy użyć biblioteki klienta magazynu w wersji 2.1 dla języka Python, aby przekazać obiekt blob do usługi Azure Storage, pobrać obiekt blob i wyświetlić listę obiektów blob w kontenerze.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: seo-python-october2019
ms.openlocfilehash: 4b0248604b6e9189d5275177a4960e4c352e8215
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76906438"
---
# <a name="quickstart-manage-blobs-with-python-v21-sdk"></a>Szybki start: zarządzanie obiektami blob za pomocą sdk języka Python w wersji 2.1

W tym przewodniku Szybki start nauczysz się zarządzać obiektami blob za pomocą języka Python. Obiekty blob to obiekty, które mogą zawierać duże ilości danych tekstowych lub binarnych, w tym obrazy, dokumenty, multimedia strumieniowe i dane archiwum. Będziesz przesyłać, pobierać i wystawiać obiekty blob, a także tworzyć i usuwać kontenery.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Konto usługi Azure Storage. [Utwórz konto magazynu](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/).
- [Zestaw SDK usługi Azure Storage dla języka Python](https://github.com/Azure/azure-sdk-for-python).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="download-the-sample-application"></a>Pobieranie przykładowej aplikacji

[Przykładowa aplikacja](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) używana w tym przewodniku Szybki start to podstawowa aplikacja w języku Python.  

Użyj następującego polecenia [git,](https://git-scm.com/) aby pobrać aplikację do środowiska programistycznego. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Aby przejrzeć program Python, otwórz plik *example.py* w katalogu głównym repozytorium.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurowanie parametrów połączenia magazynu

W aplikacji podaj nazwę konta magazynu i klucz konta, aby utworzyć obiekt `BlockBlobService`.

1. Otwórz plik *example.py* w Eksploratorze rozwiązań w środowisku IDE.

1. Zastąp `accountname` i `accountkey` wartości nazwą i kluczem konta magazynu:

    ```python
    block_blob_service = BlockBlobService(
        account_name='accountname', account_key='accountkey')
    ```

1. Zapisz i zamknij plik.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Przykładowy program tworzy plik testowy w folderze *Dokumenty,* przesyła plik do magazynu obiektów Blob, wyświetla listę obiektów blob w pliku i pobiera plik o nowej nazwie.

1. Zainstaluj zależności:

    ```console
    pip install azure-storage-blob==2.1.0
    ```

1. Przejdź do przykładowej aplikacji:

    ```console
    cd storage-blobs-python-quickstart
    ```

1. Uruchom przykład:

    ```console
    python example.py
    ```

    Zostaną wyświetlone komunikaty podobne do następujących danych wyjściowych:
  
    ```output
    Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    List blobs in the container
             Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Downloading blob to     C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
    ```

1. Przed kontynuowaniem przejdź do folderu *Dokumenty* i sprawdź, czy nie ma dwóch plików.

    * *QuickStart_\<uniwersalny unikatowy identyfikator\>*
    * *QuickStart_\<uniwersalnych unikatowych _DOWNLOADED identyfikatorów\>*

1. Możesz je otworzyć i sprawdzić, czy są takie same.

    Można również użyć narzędzia, takiego jak [Eksplorator usługi Azure Storage](https://storageexplorer.com). Jest to dobre do przeglądania plików w magazynie obiektów Blob. Usługa Azure Storage Explorer to bezpłatne narzędzie dla różnych platform, które umożliwia dostęp do informacji o koncie magazynu. 

1. Po zapoznaniu się z plikami naciśnij dowolny klawisz, aby zakończyć próbkę i usunąć pliki testowe.

## <a name="learn-about-the-sample-code"></a>Dowiedz się więcej o przykładowym kodzie

Teraz, gdy wiesz już, jak działa aplikacja przykładowa, otwórz plik *example.py* i przyjrzyj się kodowi.

### <a name="get-references-to-the-storage-objects"></a>Pobieranie odwołań do obiektów magazynu

Ta sekcja poświęcona jest tworzeniu wystąpień obiektów, tworzeniu nowego kontenera, a następnie konfigurowaniu uprawnień w kontenerze, tak aby obiekty blob były publiczne. Zadzwonisz do kontenera `quickstartblobs`. 

```python
# Create the BlockBlockService that the system uses to call the Blob service for the storage account.
block_blob_service = BlockBlobService(
    account_name='accountname', account_key='accountkey')

# Create a container called 'quickstartblobs'.
container_name = 'quickstartblobs'
block_blob_service.create_container(container_name)

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(
    container_name, public_access=PublicAccess.Container)
```

Najpierw należy utworzyć odwołania do obiektów używane w celu uzyskania dostępu do magazynu obiektów blob i zarządzania nim. Te obiekty są powiązane i każdy obiekt jest używany przez kolejny na liście.

* Utwórz wystąpienie obiektu **BlockBlobService**, który wskazuje usługę Blob service na koncie magazynu. 

* Utwórz wystąpienie obiektu **CloudBlobContainer** reprezentujące kontener, do którego uzyskujesz dostęp. System używa kontenerów do organizowania obiektów blob, takich jak foldery na komputerze do organizowania plików.

Gdy istnieje już kontener CloudBlobContainer, utwórz wystąpienie obiektu **CloudBlockBlob** wskazujące na konkretny obiekt blob, którego chcesz użyć. Następnie możesz przekazywać, pobierać i kopiować obiekt blob zgodnie z potrzebami.

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać więcej informacji na temat nazw kontenerów i obiektów blob, zobacz [Nazywanie i odwoływanie się do kontenerów, obiektów blob i metadanych](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

### <a name="upload-blobs-to-the-container"></a>Przekazywanie obiektów blob do kontenera

Usługa Blob Storage obsługuje blokowe, uzupełnialne i stronicowe obiekty blob. Blokowe obiekty blob mogą mieć rozmiar nawet do 4,7 TB i mogą to być dowolne pliki, od arkuszy kalkulacyjnych programu Excel po duże pliki wideo. Można użyć dołączania obiektów blob do rejestrowania, gdy chcesz napisać do pliku, a następnie zachować dodawanie więcej informacji. Obiekty BLOB strony są używane głównie dla plików wirtualnego dysku twardego (VHD), które z powrotem infrastruktury jako maszyny wirtualne usługi (IaaS VMs). Blokowe obiekty blob są używane najczęściej. Ten przewodnik Szybki start używa bloków obiektów blob.

Aby przekazać plik do obiektu blob, uzyskaj pełną ścieżkę pliku, łącząc nazwę katalogu i nazwę pliku na dysku lokalnym. Następnie możesz przekazać plik do określonej ścieżki przy użyciu metody `create_blob_from_path`. 

Przykładowy kod tworzy plik lokalny używany przez system do przekazywania i pobierania, przechowując plik, który system przekazuje jako *full_path_to_file,* a nazwę obiektu blob jako *local_file_name*. W tym przykładzie przekazuje plik `quickstartblobs`do kontenera o nazwie:

```python
# Create a file in Documents to test the upload and download.
local_path = os.path.expanduser("~\Documents")
local_file_name = "QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file = os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file, 'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name.
block_blob_service.create_blob_from_path(
    container_name, local_file_name, full_path_to_file)
```

Istnieje kilka metod przekazywania, których można użyć z usługą Blob Storage. Na przykład w przypadku strumienia pamięci możesz użyć metody `create_blob_from_stream` zamiast metody `create_blob_from_path`. 

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Poniższy kod `generator` tworzy `list_blobs` dla metody. Kod pętli za pośrednictwem listy obiektów blob w kontenerze i drukuje ich nazwy do konsoli.

```python
# List the blobs in the container.
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>Pobieranie obiektów blob


Pobierz obiekty BLOB na `get_blob_to_path` dysk lokalny przy użyciu tej metody.
Poniższy kod pobiera obiekt blob, który został wcześniej przekazany. System dołącza *_DOWNLOADED* do nazwy obiektu blob, dzięki czemu można zobaczyć oba pliki na dysku lokalnym.

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, local_file_name.replace(
   '.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(
    container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli nie potrzebujesz już obiektów blob przekazanych podczas pracy z tym przewodnikiem Szybki start, możesz usunąć cały kontener za pomocą metody `delete_container`. Aby zamiast tego usunąć pojedyncze pliki, użyj metody `delete_blob`.

```python
# Clean up resources. This includes the container and the temp files.
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```

## <a name="resources-for-developing-python-applications-with-blobs"></a>Zasoby używane do tworzenia aplikacji Python z obiektami blob

Aby uzyskać więcej informacji na temat programowania języka Python z magazynem obiektów Blob, zobacz te dodatkowe zasoby:

### <a name="binaries-and-source-code"></a>Pliki binarne i kod źródłowy

- W witrynie GitHub wyświetl [kod źródłowy biblioteki klienta Python](https://github.com/Azure/azure-storage-python) dla usługi Azure Storage, a następnie pobierz i zainstaluj go.

### <a name="client-library-reference-and-samples"></a>Dokumentacja i przykłady dotyczące biblioteka klienta

- Aby uzyskać więcej informacji na temat biblioteki klienta języka Python, zobacz [biblioteki usługi Azure Storage dla języka Python](https://docs.microsoft.com/python/api/overview/azure/storage).
- Zapoznaj się z [przykładami magazynu Blob Storage](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob) napisanymi przy użyciu biblioteki klienta Python.

## <a name="next-steps"></a>Następne kroki
 
W tym przewodniku Szybki start przedstawiono metodę transferowania plików między dyskiem lokalnym i usługą Azure Blob Storage przy użyciu języka Python. 

Aby uzyskać więcej informacji o Eksploratorze magazynu i obiektach blob, zobacz [Zarządzanie zasobami magazynu obiektów Blob platformy Azure za pomocą Eksploratora magazynu](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
