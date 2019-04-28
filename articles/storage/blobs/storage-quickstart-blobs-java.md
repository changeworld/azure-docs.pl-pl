---
title: Jak utworzyć obiekt blob w usłudze Azure Storage przy użyciu biblioteki klienta dla języka Java w wersji 7 | Dokumentacja firmy Microsoft
description: Utwórz konto magazynu i kontener w magazynie obiektów (blob). Biblioteka klienta usługi Azure Storage dla języka Java w wersji 7 można następnie użyć do przekażesz obiekt blob do usługi Azure Storage, pobierzesz obiekt blob i wyświetlisz listę obiektów blob w kontenerze.
services: storage
author: roygara
ms.custom: mvc
ms.service: storage
ms.topic: conceptual
ms.date: 02/04/2019
ms.author: rogarana
ms.openlocfilehash: c9aaa22a656d1164ad4e9f9c1ed7539a93dd0457
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128923"
---
# <a name="how-to-upload-download-and-list-blobs-using-the-client-library-for-java-v7"></a>Jak przekazywanie, pobieranie i wyświetlanie listy obiektów blob przy użyciu biblioteki klienta dla języka Java w wersji 7

W tym przewodniku dowiesz się, jak używać biblioteki klienta dla języka Java w wersji 7 można przekazywać, pobierać i listę blokowych obiektów blob w kontenerze usługi Azure Blob storage.

> [!TIP]
> Najnowszą wersję biblioteki klienta usługi Azure Storage dla języka Java jest v10. Firma Microsoft zaleca się, że używasz najnowszej wersji biblioteki klienta, gdy jest to możliwe. Aby rozpocząć pracę, przy użyciu v10, zobacz [Szybki Start: Przekazywanie, pobieranie i wyświetlanie listy obiektów blob za pomocą V10 SDK magazynu Java](storage-quickstart-blobs-java-v10.md).

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Również utworzyć konto usługi Azure storage w [witryny Azure portal](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM). Aby uzyskać pomoc przy tworzeniu konta, zobacz [Tworzenie konta magazynu](../common/storage-quickstart-create-account.md).

Upewnij się, że masz następujące wymagania wstępne:

* Zainstaluj zintegrowane środowisko projektowe z integracją narzędzia Maven.

* Możesz również zainstalować i skonfigurować narzędzie Maven tak, aby działało z poziomu wiersza polecenia.

W tym przewodniku używane [Eclipse](https://www.eclipse.org/downloads/) z konfiguracją "Eclipse IDE for Java Developers".

## <a name="download-the-sample-application"></a>Pobieranie przykładowej aplikacji

[Przykładowa aplikacja](https://github.com/Azure-Samples/storage-blobs-java-quickstart) to podstawowa Aplikacja konsoli.  

Użyj narzędzia [git](https://git-scm.com/), aby pobrać kopię tej aplikacji do swojego środowiska projektowego. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

To polecenie klonuje repozytorium do lokalnego folderu git. Aby otworzyć projekt, włącz środowisko Eclipse i zamknij ekran powitalny. Wybierz kolejno pozycje **File** (Plik) i **Open Projects from File system** (Otwórz projekty z systemu plików). Upewnij się, że zaznaczono opcję **Detect and configure project natures** (Wykryj i skonfiguruj natury projektów). Wybierz pozycję **Directory** (Katalog), a następnie przejdź do miejsca przechowywania sklonowanego repozytorium. Wewnątrz sklonowanego repozytorium wybierz folder **blobAzureApp**. Upewnij się, że projekt **blobAzureApp** jest wyświetlany jako projekt Eclipse, a następnie wybierz pozycję **Finish** (Zakończ).

Po zakończeniu importowania projektu otwórz plik **AzureApp.java** (znajdujący się w lokalizacji **blobQuickstart.blobAzureApp** w katalogu **src/main/java**) i zamień wartości `accountname` i `accountkey` w ciągu `storageConnectionString`. Następnie uruchom aplikację. Szczegółowe instrukcje dotyczące wykonywania tych zadań zostały opisane w poniższych sekcjach.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]    

## <a name="configure-your-storage-connection-string"></a>Konfigurowanie parametrów połączenia magazynu
    
W aplikacji należy wprowadzić parametry połączenia konta magazynu. Otwórz plik **AzureApp.Java**. Znajdź zmienną `storageConnectionString` i wklej wartość parametrów połączenia skopiowaną w poprzedniej sekcji. Zmienna `storageConnectionString` powinna wyglądać podobnie do poniższego kodu:

```java
public static final String storageConnectionString =
"DefaultEndpointsProtocol=https;" +
"AccountName=<account-name>;" +
"AccountKey=<account-key>";
```

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Ta aplikacja przykładowa tworzy plik testowy w katalogu domyślnym (*C:\Users\<użytkownik>\AppData\Local\Temp* w przypadku użytkowników systemu Windows), przekazuje go do usługi Blob Storage, tworzy listę obiektów blob w kontenerze, a następnie pobiera plik z nową nazwą, tak aby można było porównać stary i nowy plik. 

Uruchom aplikację przykładową, używając narzędzia Maven w wierszu polecenia. Otwórz powłokę i przejdź do pozycji **blobAzureApp** w sklonowanym katalogu. Następnie wprowadź ciąg `mvn compile exec:java`. 

Poniższy przykład przedstawia dane wyjściowe w przypadku uruchomienia aplikacji w systemie Windows.

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

Zanim przejdziesz dalej, sprawdź, czy katalog domyślny (*C:\Users\<użytkownik>\AppData\Local\Temp* w przypadku użytkowników systemu Windows) zawiera przykładowy plik. Skopiuj adres URL dla obiektu blob z okna konsoli i wklej go do przeglądarki, aby wyświetlić zawartość pliku w usłudze Blob Storage. Jeśli porównasz przykładowy plik w katalogu z zawartością przechowywaną w usłudze Blob Storage, zobaczysz, że są takie same. 

  >[!NOTE]
  >Możesz również wyświetlić pliki w usłudze Blob Storage za pomocą narzędzia takiego jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Eksplorator usługi Azure Storage to darmowe narzędzie międzyplatformowe, które umożliwia dostęp do informacji na koncie magazynu.

Po zweryfikowaniu plików naciśnij klawisz **Enter**, aby zakończyć pokaz i usunąć pliki testowe. Wiesz już, jak działa aplikacja przykładowa. Otwórz plik **AzureApp.java** i spójrz na kod. 

## <a name="understand-the-sample-code"></a>Omówienie przykładowego kodu

W kolejnej części omówimy przykładowy kod, aby wyjaśnić, w jaki sposób działa.

### <a name="get-references-to-the-storage-objects"></a>Pobieranie odwołań do obiektów magazynu

Najpierw należy utworzyć odwołania do obiektów używane w celu uzyskania dostępu do usługi Blob Storage i zarządzania nią. Te obiekty są powiązane — każdy obiekt jest używany przez kolejny na liście.

* Utwórz wystąpienie obiektu [CloudStorageAccount](/java/api/com.microsoft.azure.management.storage.storageaccount) wskazujące konto magazynu.

    Obiekt **CloudStorageAccount** reprezentuje konto magazynu i umożliwia ustawienie właściwości konta magazynu oraz uzyskanie do nich dostępu przez programowanie. Skorzystaj z obiektu **CloudStorageAccount**, aby utworzyć wystąpienie obiektu **CloudBlobClient**, które jest konieczne, aby uzyskać dostęp do usługi Blob Service.

* Utwórz wystąpienie obiektu **CloudBlobClient**, które wskazuje na [usługę Blob Service](/java/api/com.microsoft.azure.storage.blob._cloud_blob_client) na koncie magazynu.

    Obiekt **CloudBlobClient** zapewnia punkt dostępu do usługi Blob Service, umożliwiając ustawienie właściwości magazynu obiektów blob i uzyskanie do nich dostępu przez programowanie. Użycie obiektu **CloudBlobClient** umożliwia utworzenie wystąpienia obiektu **CloudBlobContainer**, który jest konieczny do utworzenia kontenerów.

* Utwórz wystąpienie obiektu [CloudBlobContainer](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container) reprezentujące kontener, do którego uzyskujesz dostęp. Użyj kontenerów, aby uporządkować obiekty blob w ten sam sposób, w jaki foldery na komputerze są używane do porządkowania plików.    

    Gdy już masz obiekt **CloudBlobContainer**, możesz tworzyć wystąpienie do obiektu [CloudBlockBlob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob) wskazujące na konkretny obiekt blob, który Cię interesuje, i wykonywać operacje przekazywania, pobierania, kopiowania itp.

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać dodatkowe informacje o kontenerach, zobacz [Nazewnictwo i odwołania do kontenerów, obiektów blob i metadanych](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

### <a name="create-a-container"></a>Tworzenie kontenera

Ta sekcja poświęcona jest tworzeniu wystąpień obiektów, tworzeniu nowego kontenera, a następnie konfigurowaniu uprawnień w kontenerze, tak aby obiekty blob były publiczne i można było do nich uzyskać dostęp za pomocą samego adresu URL. Kontener nosi nazwę **quickstartcontainer**. 

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

Aby przekazać plik do blokowego obiektu blob, Pobierz odwołanie do obiektu blob w kontenerze docelowym. Po uzyskaniu odwołania do obiektu blob możesz przekazać do niego dane przy użyciu polecenia [CloudBlockBlob.Upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload). Ta operacja tworzy obiekt blob, jeśli jeszcze nie istnieje, lub zastępuje obiekt blob, jeśli już istnieje.

Przykładowy kod tworzy plik lokalny do zastosowania w przypadku przekazywania i pobierania, przechowujący plik do przekazania jako **source** i nazwę obiektu blob w elemencie **blob**. Następujący kod przykładowy przekazuje plik do kontenera o nazwie **quickstartcontainer**.

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

Istnieje kilka metod typu `upload`, w tym metody [upload](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload), [uploadBlock](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadblock), [uploadFullBlob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadfullblob), [uploadStandardBlobTier](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadstandardblobtier) i [uploadText](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadtext), których można używać z usługą Blob Storage. Na przykład jeśli masz ciąg, możesz użyć metody `UploadText` zamiast metody `Upload`. 

Blokowe obiekty blob mogą mieć formę dowolnego typu pliku tekstowego lub binarnego. Stronicowe obiekty blob są używane głównie na potrzeby plików VHD służących do obsługi maszyn wirtualnych usługi IaaS. Uzupełnialne obiekty blob mogą służyć do rejestrowania, na przykład w sytuacji, w której konieczny jest zapis do pliku, a następnie dodawanie kolejnych informacji. Większość obiektów przechowywanych w usłudze Blob Storage to blokowe obiekty blob.

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Możesz uzyskać listę plików w kontenerze za pomocą polecenia [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobs). Poniższy kod umożliwia pobranie listy obiektów blob, a następnie przetwarza je w pętli, wyświetlając identyfikatory URI odnalezionych obiektów blob. Możesz skopiować identyfikator URI z okna polecenia i wkleić go do przeglądarki, aby wyświetlić plik.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

### <a name="download-blobs"></a>Pobieranie obiektów blob

Można pobierać obiekty blob na dysk lokalny przy użyciu polecenia [CloudBlob.DownloadToFile](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.downloadtofile).

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

Jeśli nie potrzebujesz już obiektów blob, które zostały przekazane, możesz usunąć cały kontener, korzystając [CloudBlobContainer.DeleteIfExists](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.deleteifexists). Ta metoda spowoduje również usunięcie plików w kontenerze.

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

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono metodę transferowania plików między dyskiem lokalnym i usługą Azure Blob storage przy użyciu języka Java. Aby dowiedzieć się więcej na temat pracy z językiem Java, przejdź do repozytorium kodu źródłowego w witrynie GitHub.

> [!div class="nextstepaction"]
> [Zestaw SDK usługi Microsoft Azure Storage v10 dla języka Java](https://github.com/azure/azure-storage-java) 
> [dokumentacja interfejsu API języka Java](https://docs.microsoft.com/java/azure/)
> [Code Samples dla języka Java](../common/storage-samples-java.md)
