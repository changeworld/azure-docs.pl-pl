---
title: Azure Quickstart — przekazywanie, pobieranie i wyświetlanie listy obiektów blob w usłudze Azure Storage przy użyciu języka Python | Microsoft Docs
description: W tym przewodniku Szybki start utworzysz konto magazynu i kontener. Następnie przy użyciu biblioteki klienta języka Python przekażesz obiekt blob do usługi Azure Storage, pobierzesz obiekt blob i wyświetlisz listę obiektów blob w kontenerze.
services: storage
author: craigshoemaker
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 03/15/2018
ms.author: cshoe
ms.openlocfilehash: 9ea8fdcd2d21729117101748e20e52029c315e15
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-python"></a>Szybki start: przekazywanie, pobieranie i wyświetlanie listy obiektów blob przy użyciu języka Python

Dzięki temu przewodnikowi Szybki start dowiesz się, w jaki sposób za pomocą języka Python przekazywać, pobierać i wyświetlać listę blokowych obiektów blob w kontenerze usługi Azure Blob Storage. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start: 
* Zainstaluj język [Python](https://www.python.org/downloads/)
* Pobierz i zainstaluj [zestaw SDK usługi Azure Storage dla języka Python](https://github.com/Azure/azure-sdk-for-python) 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Pobieranie przykładowej aplikacji
[Przykładowa aplikacja](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) używana w tym przewodniku Szybki start to podstawowa aplikacja języka Python.  

Użyj narzędzia [git](https://git-scm.com/), aby pobrać kopię tej aplikacji do swojego środowiska projektowego. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

To polecenie klonuje repozytorium do lokalnego folderu git. Aby otworzyć program Python, odszukaj folder storage-blobs-python-quickstart i plik example.py.  

## <a name="configure-your-storage-connection-string"></a>Konfigurowanie parametrów połączenia magazynu
W aplikacji należy podać nazwę konta magazynu i klucz konta, aby utworzyć obiekt `BlockBlobService`. Otwórz plik `example.py` w Eksploratorze rozwiązań w środowisku IDE. Zastąp wartości **accountname** i **accountkey** własną nazwą konta i własnym kluczem. 

```python 
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
```

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej
Ta aplikacja przykładowa tworzy plik testowy w folderze „Documents”. Aplikacja przykładowa przesyła plik testowy do usługi Blob Storage, wyświetla listę obiektów blob w kontenerze i pobiera plik z nową nazwą. 

Uruchom przykład. Poniższej przedstawiono przykładowe dane wyjściowe zwracane po uruchomieniu aplikacji:
  
```
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
Po naciśnięciu dowolnego klawisza w celu kontynuowania aplikacja przykładowa usunie kontener magazynu i pliki. Przed kontynuowaniem sprawdź, czy w folderze „Documents” znajdują się te dwa pliki. Możesz je otworzyć i sprawdzić, czy są identyczne.

Możesz również wyświetlić pliki w usłudze Blob Storage za pomocą narzędzia takiego jak [Eksplorator usługi Azure Storage](http://storageexplorer.com). Eksplorator usługi Azure Storage to darmowe narzędzie międzyplatformowe, które umożliwia dostęp do informacji na koncie magazynu. 

Po zweryfikowaniu plików naciśnij dowolny klawisz, aby zakończyć demonstrację i usunąć pliki testowe. Teraz, gdy wiesz już, jak działa aplikacja przykładowa, otwórz plik example.py i przyjrzyj się kodowi. 

## <a name="understand-the-sample-code"></a>Omówienie przykładowego kodu

W kolejnej części omówimy przykładowy kod, aby wyjaśnić, w jaki sposób działa.

### <a name="get-references-to-the-storage-objects"></a>Pobieranie odwołań do obiektów magazynu
Najpierw należy utworzyć odwołania do obiektów używane w celu uzyskania dostępu do usługi Blob Storage i zarządzania nią. Te obiekty są powiązane i każdy obiekt jest używany przez kolejny na liście.

* Utwórz wystąpienie obiektu **BlockBlobService**, który wskazuje usługę Blob service na koncie magazynu. 

* Utwórz wystąpienie obiektu **CloudBlobContainer** reprezentujące kontener, do którego uzyskujesz dostęp. Kontenery są używane do porządkowania obiektów blob w ten sam sposób, w jaki foldery na komputerze są używane do porządkowania plików.

Gdy masz już kontener Cloud Blob, możesz utworzyć wystąpienie obiektu **CloudBlockBlob** wskazujące konkretny obiekt blob, który Cię interesuje, i wykonywać operacje takie jak przekazywanie, pobieranie i kopiowanie.

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać dodatkowe informacje o regułach nazewnictwa kontenerów i obiektów blob, zobacz [Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Ta sekcja poświęcona jest tworzeniu wystąpień obiektów, tworzeniu nowego kontenera, a następnie konfigurowaniu uprawnień w kontenerze, tak aby obiekty blob były publiczne. Kontener nazywa się **quickstartblobs**. 

```python 
# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
 
# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs'
block_blob_service.create_container(container_name) 

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(container_name, public_access=PublicAccess.Container)
```
### <a name="upload-blobs-to-the-container"></a>Przekazywanie obiektów blob do kontenera

Usługa Blob Storage obsługuje blokowe, uzupełnialne i stronicowe obiekty blob. Blokowe obiekty blob są używane najczęściej i dlatego zostały użyte w tym przewodniku Szybki start.  

Aby przekazać plik do obiektu blob, uzyskaj pełną ścieżkę pliku, łącząc nazwę katalogu i nazwę pliku na dysku lokalnym. Następnie możesz przekazać plik do określonej ścieżki przy użyciu metody **create\_blob\_from\_path**. 

Przykładowy kod tworzy plik lokalny do zastosowania w przypadku przekazywania i pobierania, przechowujący plik do przekazania jako **file\_path\_to\_file** i nazwę obiektu blob jako **local\_file\_name**. Następujący kod przykładowy przekazuje plik do kontenera o nazwie **quickstartblobs**.

```python
# Create a file in Documents to test the upload and download.
local_path=os.path.expanduser("~\Documents")
local_file_name ="QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file =os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name
block_blob_service.create_blob_from_path(container_name, local_file_name, full_path_to_file)
```

Istnieje kilka metod przekazywania, których można użyć z usługą Blob Storage. Na przykład jeśli masz strumień pamięci, możesz użyć metody **create\_blob\_from\_stream** zamiast metody **create\_blob\_from\_path**. 

Blokowe obiekty blob mogą mieć rozmiar nawet do 4,7 TB i mogą to być dowolne pliki, od arkuszy kalkulacyjnych programu Excel po duże pliki wideo. Stronicowe obiekty blob są używane głównie do tworzenia plików VHD służących do obsługi maszyn wirtualnych IaaS. Uzupełnialne obiekty blob są używane do rejestrowania, na przykład w sytuacji, w której konieczny jest zapis do pliku, a następnie dodawanie kolejnych informacji. Większość obiektów przechowywanych w usłudze Blob Storage to blokowe obiekty blob.

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Pobierz listę plików w kontenerze, używając metody **list_blobs**. Ta metoda zwraca generator. Poniższy kod umożliwia pobranie listy obiektów blob, a następnie przetwarza je w pętli, wyświetlając nazwy obiektów blob odnalezionych w kontenerze.  

```python
# List the blobs in the container
print("\nList blobs in the container")
    generator = block_blob_service.list_blobs(container_name)
    for blob in generator:
        print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>Pobieranie obiektów blob

Pobierz obiekty blob na dysk lokalny, używając metody **get\_blob\_to\_path**. Poniższy kod pozwala pobrać obiekt blob przekazany w poprzedniej sekcji. Ciąg „_DOWNLOADED” jest dodawany jako sufiks do nazwy obiektu blob, co pozwala zobaczyć oba pliki na dysku lokalnym. 

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, string.replace(local_file_name ,'.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli nie potrzebujesz już obiektów blob przekazanych podczas pracy z tym przewodnikiem Szybki start, możesz usunąć cały kontener, korzystając z metody **delete\_container**. Jeśli utworzone pliki nie są już potrzebne, możesz użyć metody **delete\_blob**, aby je usunąć.

```python
# Clean up resources. This includes the container and the temp files
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```
## <a name="resources-for-developing-python-applications-with-blobs"></a>Zasoby używane do tworzenia aplikacji Python z obiektami blob

Zobacz dodatkowe zasoby używane podczas tworzenia aplikacji Python z magazynem obiektów blob:

### <a name="binaries-and-source-code"></a>Pliki binarne i kod źródłowy

- W witrynie GitHub wyświetl [kod źródłowy biblioteki klienta Python](https://github.com/Azure/azure-storage-python) dla usługi Azure Storage, a następnie pobierz i zainstaluj go.

### <a name="client-library-reference-and-samples"></a>Dokumentacja i przykłady dotyczące biblioteka klienta

- Aby uzyskać więcej informacji na temat biblioteki klienta Python, zobacz [dokumentację interfejsu API języka Python](https://docs.microsoft.com/python/api/overview/azure/storage).
- Zapoznaj się z [przykładami magazynu Blob Storage](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob) napisanymi przy użyciu biblioteki klienta Python.

## <a name="next-steps"></a>Następne kroki
 
W tym przewodniku Szybki start przedstawiono metodę transferowania plików między dyskiem lokalnym i usługą Azure Blob Storage przy użyciu języka Python. Aby dowiedzieć się więcej na temat pracy z usługą Blob Storage, przejdź do instrukcji dotyczących magazynu obiektów blob.

> [!div class="nextstepaction"]
> [Instrukcje: Operacje wykonywane w usłudze Blob Storage](./storage-python-how-to-use-blob-storage.md)
 
Aby uzyskać więcej informacji na temat Eksploratora usługi Storage i obiektów blob, zapoznaj się artykułem [Manage Azure Blob storage resources with Storage Explorer](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Zarządzanie zasobami usługi Azure Blob Storage za pomocą Eksploratora usługi Microsoft Azure Storage).
