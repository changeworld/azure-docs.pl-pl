---
title: 'Azure: Szybki start — tworzenie obiektu blob w magazynie obiektów przy użyciu języka Python | Microsoft Docs'
description: Ten przewodnik Szybki start przedstawia tworzenie konta magazynu i kontenera w magazynie obiektów (blob). Następnie przy użyciu biblioteki klienta języka Python przekażesz obiekt blob do usługi Azure Storage, pobierzesz obiekt blob i wyświetlisz listę obiektów blob w kontenerze.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/14/2018
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 0eb4558b7c9c08fc6df964a7e45328a83c60352b
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721984"
---
# <a name="quickstart-upload-download-and-list-blobs-with-python"></a>Szybki start: przekazywanie i pobieranie obiektów blob oraz wyświetlanie ich listy za pomocą języka Python

W tym przewodniku szybki start dowiesz się, jak za pomocą języka Python przekazywać, pobierać i wyświetlać listę blokowych obiektów BLOB w kontenerze w usłudze Azure Blob Storage. Obiekty blob są po prostu obiektami, które mogą przechowywać dowolną ilość danych tekstowych lub binarnych (takich jak obrazy, dokumenty, multimedia strumieniowe, dane archiwalne itp.), a także różnią się w usłudze Azure Storage z udziałów plików, tabel bez schematu i kolejek komunikatów. (Aby uzyskać więcej informacji, zobacz [wprowadzenie do usługi Azure Storage](/azure/storage/common/storage-introduction)).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Upewnij się, że masz zainstalowane następujące dodatkowe elementy wymagane wstępnie:

* [Python](https://www.python.org/downloads/)
* [Zestaw SDK usługi Azure Storage dla języka Python](https://github.com/Azure/azure-sdk-for-python)

## <a name="download-the-sample-application"></a>Pobieranie przykładowej aplikacji
[Przykładowa aplikacja](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) używana w tym przewodniku Szybki start to podstawowa aplikacja w języku Python.  

Użyj narzędzia [git](https://git-scm.com/), aby pobrać kopię tej aplikacji do swojego środowiska projektowego. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

To polecenie klonuje repozytorium *Azure-Samples/storage-blobs-python-quickstart* do folderu lokalnego narzędzia git. Aby uruchomić program w języku Python, otwórz plik *example.py* w folderze głównym repozytorium.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurowanie parametrów połączenia magazynu
W aplikacji podaj nazwę konta magazynu i klucz konta, aby utworzyć obiekt `BlockBlobService`. Otwórz plik *example.py* w Eksploratorze rozwiązań w środowisku IDE. Zastąp wartości `accountname` i `accountkey` nazwą konta i kluczem. 

```python
block_blob_service = BlockBlobService(
    account_name='accountname', account_key='accountkey')
```

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej
Ta aplikacja przykładowa tworzy plik testowy w folderze *Documents*. Aplikacja przykładowa przesyła plik testowy do usługi Blob Storage, wyświetla listę obiektów blob w kontenerze i pobiera plik z nową nazwą. 

Najpierw zainstaluj zależności, uruchamiając polecenie `pip install`:

```python
    pip install azure-storage-blob
```

Następnie uruchom przykład. Zostaną wyświetlone komunikaty podobne do następujących danych wyjściowych:
  
```output
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
Przed kontynuowaniem sprawdź, czy w folderze *Documents* znajdują się te dwa pliki. Możesz je otworzyć i sprawdzić, czy są takie same.

Możesz również wyświetlić pliki w usłudze Blob Storage za pomocą narzędzia takiego jak [Eksplorator usługi Azure Storage](https://storageexplorer.com). Eksplorator usługi Azure Storage to darmowe narzędzie międzyplatformowe, które umożliwia dostęp do informacji na koncie magazynu. 

Po zweryfikowaniu plików naciśnij dowolny klawisz, aby zakończyć demonstrację i usunąć pliki testowe. Teraz, gdy wiesz już, jak działa aplikacja przykładowa, otwórz plik *example.py* i przyjrzyj się kodowi. 

## <a name="understand-the-sample-code"></a>Omówienie przykładowego kodu

Omówmy teraz przykładowy kod, aby wyjaśnić, w jaki sposób działa.

### <a name="get-references-to-the-storage-objects"></a>Pobieranie odwołań do obiektów magazynu
Najpierw należy utworzyć odwołania do obiektów używane w celu uzyskania dostępu do magazynu obiektów blob i zarządzania nim. Te obiekty są powiązane i każdy obiekt jest używany przez kolejny na liście.

* Utwórz wystąpienie obiektu **BlockBlobService**, który wskazuje usługę Blob service na koncie magazynu. 

* Utwórz wystąpienie obiektu **CloudBlobContainer** reprezentujące kontener, do którego uzyskujesz dostęp. Kontenery są używane do porządkowania obiektów blob w ten sam sposób, w jaki foldery na komputerze są używane do porządkowania plików.

Gdy istnieje już kontener CloudBlobContainer, utwórz wystąpienie obiektu **CloudBlockBlob** wskazujące na konkretny obiekt blob, którego chcesz użyć. Następnie możesz przekazywać, pobierać i kopiować obiekt blob zgodnie z potrzebami.

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać dodatkowe informacje o regułach nazewnictwa kontenerów i obiektów blob, zobacz [Nazewnictwo i odwołania do kontenerów, obiektów blob i metadanych](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Ta sekcja poświęcona jest tworzeniu wystąpień obiektów, tworzeniu nowego kontenera, a następnie konfigurowaniu uprawnień w kontenerze, tak aby obiekty blob były publiczne. Kontener nazywa się **quickstartblobs**. 

```python
# Create the BlockBlockService that is used to call the Blob service for the storage account.
block_blob_service = BlockBlobService(
    account_name='accountname', account_key='accountkey')

# Create a container called 'quickstartblobs'.
container_name = 'quickstartblobs'
block_blob_service.create_container(container_name)

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(
    container_name, public_access=PublicAccess.Container)
```
### <a name="upload-blobs-to-the-container"></a>Przekazywanie obiektów blob do kontenera

Usługa Blob Storage obsługuje blokowe, uzupełnialne i stronicowe obiekty blob. Blokowe obiekty blob są używane najczęściej i dlatego zostały użyte w tym przewodniku Szybki start.  

Aby przekazać plik do obiektu blob, uzyskaj pełną ścieżkę pliku, łącząc nazwę katalogu i nazwę pliku na dysku lokalnym. Następnie możesz przekazać plik do określonej ścieżki przy użyciu metody `create_blob_from_path`. 

Przykładowy kod tworzy plik lokalny do użycia podczas przekazywania i pobierania, przechowujący plik do przekazania jako *full_path_to_file* i nazwę obiektu BLOB jako *local_file_name*. Następujący kod przykładowy przekazuje plik do kontenera o nazwie **quickstartblobs**.

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

Blokowe obiekty blob mogą mieć rozmiar nawet do 4,7 TB i mogą to być dowolne pliki, od arkuszy kalkulacyjnych programu Excel po duże pliki wideo. Stronicowe obiekty blob są używane głównie na potrzeby plików VHD służących do obsługi maszyn wirtualnych usługi IaaS. Uzupełnialne obiekty blob są używane do rejestrowania, na przykład w sytuacji, w której konieczny jest zapis do pliku, a następnie dodawanie kolejnych informacji. Większość obiektów przechowywanych w usłudze Blob Storage to blokowe obiekty blob.

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Pobierz listę plików w kontenerze za pomocą metody `list_blobs`. Ta metoda zwraca generator. Poniższy kod umożliwia pobranie listy obiektów blob, a następnie przetwarza je w pętli, wyświetlając nazwy obiektów blob odnalezionych w kontenerze.  

```python
# List the blobs in the container.
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>Pobieranie obiektów blob

Pobierz obiekty blob na dysk lokalny przy użyciu `get_blob_to_path` metody. Poniższy kod pozwala pobrać obiekt blob przekazany w poprzedniej sekcji. Ciąg *_DOWNLOADED* jest dodawany jako sufiks do nazwy obiektu blob, co pozwala zobaczyć oba pliki na dysku lokalnym. 

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, string.replace(
    local_file_name, '.txt', '_DOWNLOADED.txt'))
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

Aby uzyskać dodatkowe informacje dotyczące tworzenia aplikacji w języku Python z magazynem obiektów blob, zapoznaj się z następującymi zasobami dodatkowymi:

### <a name="binaries-and-source-code"></a>Pliki binarne i kod źródłowy

- W witrynie GitHub wyświetl [kod źródłowy biblioteki klienta Python](https://github.com/Azure/azure-storage-python) dla usługi Azure Storage, a następnie pobierz i zainstaluj go.

### <a name="client-library-reference-and-samples"></a>Dokumentacja i przykłady dotyczące biblioteka klienta

- Aby uzyskać więcej informacji na temat biblioteki klienta Python, zapoznaj się z [dokumentacją interfejsu API języka Python](https://docs.microsoft.com/python/api/overview/azure/storage).
- Zapoznaj się z [przykładami magazynu Blob Storage](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob) napisanymi przy użyciu biblioteki klienta Python.

## <a name="next-steps"></a>Następne kroki
 
W tym przewodniku Szybki start przedstawiono metodę transferowania plików między dyskiem lokalnym i usługą Azure Blob Storage przy użyciu języka Python. Aby dowiedzieć się więcej na temat pracy z usługą Blob Storage, przejdź do instrukcji dotyczących magazynu obiektów blob.

> [!div class="nextstepaction"]
> [Instrukcje: Operacje wykonywane w usłudze Blob Storage](./storage-python-how-to-use-blob-storage.md)
 
Aby uzyskać więcej informacji na temat Eksploratora usługi Storage i obiektów blob, zapoznaj się artykułem [Manage Azure Blob storage resources with Storage Explorer](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Zarządzanie zasobami usługi Azure Blob Storage za pomocą Eksploratora usługi Microsoft Azure Storage).
