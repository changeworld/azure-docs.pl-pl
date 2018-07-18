---
title: Przewodnik Szybki start platformy Azure — tworzenie obiektu blob w magazynie obiektów przy użyciu zestawu SDK usługi Storage dla języka Java w wersji 7 | Microsoft Docs
description: Ten przewodnik Szybki start przedstawia tworzenie konta magazynu i kontenera w magazynie obiektów (blob). Następnie przy użyciu biblioteki klienta języka Java przekażesz obiekt blob do usługi Azure Storage, pobierzesz obiekt blob i wyświetlisz listę obiektów blob w kontenerze.
services: storage
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: rogarana
ms.openlocfilehash: 30d31a7f4b77864549dcb9e27030ba19c4fd84fe
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38606613"
---
# <a name="quickstart-upload-download-and-list-blobs-using-java-sdk-v7"></a>Szybki start: przekazywanie, pobieranie i wyświetlanie listy obiektów blob za pomocą zestawu SDK dla języka Java w wersji 7

Dzięki tej skróconej instrukcji dowiesz się, w jaki sposób za pomocą języka Java przekazywać, pobierać i wyświetlać listę blokowych obiektów blob w kontenerze usługi Azure Blob Storage.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start:

* Zainstaluj zintegrowane środowisko projektowe z integracją narzędzia Maven

* Możesz również zainstalować i skonfigurować narzędzie Maven tak, aby działało z poziomu wiersza polecenia.

Ten samouczek korzysta ze środowiska [Eclipse](http://www.eclipse.org/downloads/) z konfiguracją „Eclipse IDE dla deweloperów Java”.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Pobieranie przykładowej aplikacji

[Przykładowa aplikacja](https://github.com/Azure-Samples/storage-blobs-java-quickstart) używana w tym przewodniku Szybki start to podstawowa aplikacja konsoli.  

Użyj narzędzia [git](https://git-scm.com/), aby pobrać kopię tej aplikacji do swojego środowiska projektowego. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

To polecenie klonuje repozytorium do lokalnego folderu git. Aby otworzyć projekt, włącz środowisko Eclipse i zamknij ekran powitalny. Wybierz kolejno pozycje **File** (Plik) i **Open Projects from File system** (Otwórz projekty z systemu plików). Upewnij się, że zaznaczono opcję **Detect and configure project natures** (Wykryj i skonfiguruj natury projektów). Wybierz opcję **Directory** (Katalog), a następnie przejdź do miejsca przechowywania sklonowanego repozytorium i wybierz katalog **javaBlobsQuickstart**. Upewnij się, że projekt **javaBlobsQuickstarts** jest wyświetlany jako projekt Eclipse, a następnie wybierz pozycję **Finish** (Zakończ).

Po zakończeniu importowania projektu otwórz plik **AzureApp.java** (znajdujący się w lokalizacji **blobQuickstart.blobAzureApp** w katalogu **src/main/java**) i zamień wartości `accountname` i `accountkey` w ciągu `storageConnectionString`. Następnie uruchom aplikację.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]    

## <a name="configure-your-storage-connection-string"></a>Konfigurowanie parametrów połączenia magazynu
    
W aplikacji należy wprowadzić parametry połączenia konta magazynu. Otwórz plik **AzureApp.Java**. Znajdź zmienną `storageConnectionString` i wklej wartość parametrów połączenia skopiowaną w poprzedniej sekcji. Zmienna `storageConnectionString` powinna wyglądać mniej więcej tak:

```java
public static final String storageConnectionString =
"DefaultEndpointsProtocol=https;" +
"AccountName=<account-name>;" +
"AccountKey=<account-key>";
```

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Ta aplikacja przykładowa tworzy plik testowy w katalogu domyślnym (Moje dokumenty w przypadku użytkowników systemu Windows), przesyła go do usługi Blob Storage, tworzy listę obiektów blob w kontenerze, a następnie pobiera plik z nową nazwą, tak aby można było porównać stary i nowy plik. 

Uruchom aplikację przykładową, używając narzędzia Maven w wierszu polecenia. Otwórz powłokę i przejdź do pozycji **blobAzureApp** w sklonowanym katalogu. Następnie wprowadź ciąg `mvn compile exec:java`. 

Poniżej znajduje się przykład danych wyjściowych w przypadku uruchomienia aplikacji w systemie Windows.

```
Azure Blob storage quick start sample
Creating container: quickstartcontainer
Creating a sample file at: C:\Users\<user>\AppData\Local\Temp\sampleFile514658495642546986.txt
Uploading the sample file 
URI of blob is: https://myexamplesacct.blob.core.windows.net/quickstartcontainer/sampleFile514658495642546986.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.

Deleting the container
Deleting the source, and downloaded files
```

Zanim przejdziesz dalej, sprawdź katalog domyślny (dla użytkowników systemu Windows jest to katalog Moje dokumenty) dla tych dwóch plików. Możesz je otworzyć i sprawdzić, czy są identyczne. Skopiuj adres URL dla obiektu blob z okna konsoli i wklej go do przeglądarki, aby wyświetlić zawartość pliku w usłudze Blob Storage. Po naciśnięciu klawisza Enter kontener magazynu i pliki zostaną usunięte. 

Możesz również wyświetlić pliki w usłudze Blob Storage za pomocą narzędzia takiego jak [Eksplorator usługi Azure Storage](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Eksplorator usługi Azure Storage to darmowe narzędzie międzyplatformowe, które umożliwia dostęp do informacji na koncie magazynu.

Po zweryfikowaniu plików naciśnij klawisz Enter, aby zakończyć demonstrację i usunąć pliki testowe. Wiesz już, jak działa aplikacja przykładowa. Otwórz plik **AzureApp.java** i spójrz na kod. 

## <a name="understand-the-sample-code"></a>Omówienie przykładowego kodu

W kolejnej części omówimy przykładowy kod, aby wyjaśnić, w jaki sposób działa.

### <a name="get-references-to-the-storage-objects"></a>Pobieranie odwołań do obiektów magazynu

Najpierw należy utworzyć odwołania do obiektów używane w celu uzyskania dostępu do usługi Blob Storage i zarządzania nią. Te obiekty są powiązane — każdy obiekt jest używany przez kolejny na liście.

* Utwórz wystąpienie obiektu [CloudStorageAccount](/java/api/com.microsoft.azure.management.storage._storage_account) wskazujące konto magazynu.

    Obiekt **CloudStorageAccount** reprezentuje konto magazynu i umożliwia ustawienie właściwości konta magazynu oraz uzyskanie do nich dostępu przez programowanie. Skorzystaj z obiektu **CloudStorageAccount**, aby utworzyć wystąpienie obiektu **CloudBlobClient**, które jest konieczne, aby uzyskać dostęp do usługi Blob Service.

* Utwórz wystąpienie obiektu **CloudBlobClient**, które wskazuje na [usługę Blob Service](/java/api/com.microsoft.azure.storage.blob._cloud_blob_client) na koncie magazynu.

    Obiekt **CloudBlobClient** zapewnia punkt dostępu do usługi Blob Service, umożliwiając ustawienie właściwości magazynu obiektów blob i uzyskanie do nich dostępu przez programowanie. Użycie obiektu **CloudBlobClient** umożliwia utworzenie wystąpienia obiektu **CloudBlobContainer**, który jest konieczny do utworzenia kontenerów.

* Utwórz wystąpienie obiektu [CloudBlobContainer](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container) reprezentujące kontener, do którego uzyskujesz dostęp. Kontenery są używane do porządkowania obiektów blob w ten sam sposób, w jaki foldery na komputerze są używane do porządkowania plików.    

    Gdy już masz obiekt **CloudBlobContainer**, możesz tworzyć odwołanie do obiektu [CloudBlockBlob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob) wskazującego konkretny obiekt blob, który Cię interesuje, i wykonywać operacje przekazywania, pobierania, kopiowania itp.

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać dodatkowe informacje o regułach nazewnictwa kontenerów i obiektów blob, zobacz [Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

### <a name="create-a-container"></a>Tworzenie kontenera

Ta sekcja poświęcona jest tworzeniu wystąpień obiektów, tworzeniu nowego kontenera, a następnie konfigurowaniu uprawnień w kontenerze, tak aby obiekty blob były publiczne i można było do nich uzyskać dostęp za pomocą samego adresu URL. Kontener nazywa się **quickstartblobs**. 

W tym przykładzie użyto metody [CreateIfNotExists](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.createifnotexists), ponieważ chcemy utworzyć nowy kontener za każdym razem, gdy jest uruchamiana aplikacja przykładowa. W środowisku produkcyjnym, w którym korzystasz z tego samego kontenera w całej aplikacji, lepiej jest wywołać metodę **CreateIfNotExists** tylko raz. Możesz również utworzyć kontener wcześniej, aby nie było konieczne tworzenie go w kodzie.

```java
// Parse the connection string and create a blob client to interact with Blob storage
storageAccount = CloudStorageAccount.parse(storageConnectionString);
blobClient = storageAccount.createCloudBlobClient();
container = blobClient.getContainerReference("quickstartcontainer");

// Create the container if it does not exist with public access.
System.out.println("Creating container: " + container.getName());
container.createIfNotExists(BlobContainerPublicAccessType.CONTAINER, new BlobRequestOptions(), new OperationContext());
```

### <a name="upload-blobs-to-the-container"></a>Przekazywanie obiektów blob do kontenera

Usługa Blob Storage obsługuje blokowe, uzupełnialne i stronicowe obiekty blob. Blokowe obiekty blob są używane najczęściej i dlatego zostały użyte w tym przewodniku Szybki start. 

Aby przekazać plik do obiektu blob, pobierz odwołanie do obiektu blob w kontenerze docelowym. Po uzyskaniu odwołania do obiektu blob możesz przekazać do niego dane przy użyciu polecenia [CloudBlockBlob.Upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload#com_microsoft_azure_storage_blob__cloud_block_blob_upload_final_InputStream_final_long). Ta operacja tworzy obiekt blob, jeśli jeszcze nie istnieje, lub zastępuje go, jeśli już istnieje.

Kod przykładowy tworzy plik lokalny do użycia podczas przekazywania i pobierania. Plik do przekazania jest zapisywany jako **source**, a nazwa obiektu blob to **blob**. Następujący kod przykładowy przekazuje plik do kontenera o nazwie **quickstartblobs**.

```java
//Creating a sample file
sourceFile = File.createTempFile("sampleFile", ".txt");
System.out.println("Creating a sample file at: " + sourceFile.toString());
Writer output = new BufferedWriter(new FileWriter(sourceFile));
output.write("Hello Azure!");
output.close();

//Getting a blob reference
CloudBlockBlob blob = container.getBlockBlobReference(sourceFile.getName());

//Creating blob and uploading file to it
System.out.println("Uploading the sample file ");
blob.uploadFromFile(sourceFile.getAbsolutePath());
```

Istnieje kilka metod przekazywania, w tym [upload](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload), [uploadBlock](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadblock), [uploadFullBlob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadfullblob), [uploadStandardBlobTier](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadstandardblobtier) i [uploadText](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadtext), których można używać z usługą Blob Storage. Na przykład jeśli masz ciąg, możesz użyć metody UploadText zamiast metody Upload. 

Blokowe obiekty blob mogą mieć formę dowolnego typu pliku tekstowego lub binarnego. Stronicowe obiekty blob są używane głównie do tworzenia plików VHD służących do obsługi maszyn wirtualnych IaaS. Uzupełnialne obiekty blob są używane do rejestrowania, na przykład w sytuacji, w której konieczny jest zapis do pliku, a następnie dodawanie kolejnych informacji. Większość obiektów przechowywanych w usłudze Blob Storage to blokowe obiekty blob.

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Możesz uzyskać listę plików w kontenerze za pomocą polecenia [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobs#com_microsoft_azure_storage_blob__cloud_blob_container_listBlobs). Poniższy kod umożliwia pobranie listy obiektów blob, a następnie przetwarza je w pętli, wyświetlając identyfikatory URI odnalezionych obiektów blob. Możesz skopiować identyfikator URI z okna polecenia i wkleić go do przeglądarki, aby wyświetlić plik.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

### <a name="download-blobs"></a>Pobieranie obiektów blob

Można pobierać obiekty blob na dysk lokalny przy użyciu polecenia [CloudBlob.DownloadToFile](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.downloadtofile#com_microsoft_azure_storage_blob__cloud_blob_downloadToFile_final_String).

Następujący kod pobiera obiekty blob przekazane w poprzedniej sekcji, dodając sufiks „_DOWNLOADED” do nazwy obiektu blob, tak aby oba pliki były widoczne na dysku lokalnym. 

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and 
// haven't changed the blob we're interested in, so we can reuse it. 
// Here we are creating a new file to download to. Alternatively you can also pass in the path as a string into downloadToFile method: blob.downloadToFile("/path/to/new/file").
downloadedFile = new File(sourceFile.getParentFile(), "downloadedFile.txt");
blob.downloadToFile(downloadedFile.getAbsolutePath());
```

### <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już obiektów blob przekazanych podczas pracy z tym przewodnikiem Szybki start, możesz usunąć cały kontener, korzystając z polecenia [CloudBlobContainer.DeleteIfExists](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.deleteifexists#com_microsoft_azure_storage_blob__cloud_blob_container_deleteIfExists). Spowoduje to również usunięcie plików w kontenerze.

```java
try {
if(container != null)
    container.deleteIfExists();
} catch (StorageException ex) {
System.out.println(String.format("Service error. Http code: %d and error code: %s", ex.getHttpStatusCode(), ex.getErrorCode()));
}

System.out.println("Deleting the source, and downloaded files");

if(downloadedFile != null)
downloadedFile.deleteOnExit();
        
if(sourceFile != null)
sourceFile.deleteOnExit();
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przedstawiono metodę transferowania plików między dyskiem lokalnym i usługą Azure Blob Storage przy użyciu języka Java. Aby dowiedzieć się więcej na temat pracy z językiem Java, przejdź do repozytorium kodu źródłowego w witrynie GitHub.

> [!div class="nextstepaction"]
> [Azure Storage SDK for Java (Zestaw SDK usługi Azure Storage dla języka Java)](https://github.com/azure/azure-storage-java) 
> [API Reference (Dokumentacja interfejsu API)](https://docs.microsoft.com/en-us/java/api/storage/client?view=azure-java-stable)
> [Code Samples for Java (Przykłady kodu języka Java)](../common/storage-samples-java.md)

* Aby uzyskać więcej informacji na temat Eksploratora usługi Storage i obiektów blob, zapoznaj się artykułem [Manage Azure Blob storage resources with Storage Explorer](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Zarządzanie zasobami usługi Azure Blob Storage za pomocą Eksploratora usługi Storage).