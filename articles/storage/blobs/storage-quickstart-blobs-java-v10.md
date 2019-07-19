---
title: 'Przewodnik Szybki Start platformy Azure: Tworzenie obiektu BLOB w magazynie obiektów przy użyciu zestawu SDK magazynu Java v10 | Microsoft Docs'
description: Ten przewodnik Szybki start przedstawia sposób tworzenia kontenera w magazynie obiektów (Azure Blob Storage), przekazywania pliku, wyświetlania listy obiektów i pobierania obiektów za pomocą zestawu SDK usługi Storage dla języka Java.
services: storage
author: mhopkins-msft
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 11/14/2018
ms.author: mhopkins
ms.reviewer: seguler
ms.openlocfilehash: 2aa004ffa98730341c8de105f16feea6e3931a86
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326679"
---
# <a name="quickstart-upload-download-and-list-blobs-by-using-the-java-storage-sdk-v10"></a>Szybki start: Przekazywanie, pobieranie i wyświetlanie listy obiektów BLOB przy użyciu zestawu SDK magazynu Java v10

W tym przewodniku Szybki start dowiesz się, w jaki sposób za pomocą nowego zestawu SDK usługi Storage dla języka Java przekazywać, pobierać i wyświetlać listę blokowych obiektów blob w kontenerze usługi Azure Blob Storage. Nowy zestaw SDK dla języka Java używa modelu programowania reaktywnego z biblioteką RxJava zapewniającą operacje asynchroniczne. Dowiedz się więcej o [reaktywnych rozszerzeniach maszyny wirtualnej języka Java](https://github.com/ReactiveX/RxJava) w bibliotece RxJava. 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Upewnij się, że masz zainstalowane następujące dodatkowe elementy wymagane wstępnie:

* Narzędzie [Maven](https://maven.apache.org/download.cgi) do użycia z poziomu wiersza polecenia lub dowolne wybrane zintegrowane środowisko projektowe Java.
* Zestaw [JDK](https://aka.ms/azure-jdks).

## <a name="download-the-sample-application"></a>Pobieranie przykładowej aplikacji

[Przykładowa aplikacja](https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart) używana w tym przewodniku Szybki start to podstawowa aplikacja konsoli. 

Użyj narzędzia [git](https://git-scm.com/), aby pobrać kopię tej aplikacji do swojego środowiska projektowego.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart.git
```

To polecenie klonuje repozytorium do lokalnego folderu git.

Po zakończeniu importowania projektu otwórz plik **Quickstart.java**, znajdujący się w lokalizacji **src/main/java/quickstart**.

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurowanie parametrów połączenia magazynu
To rozwiązanie wymaga bezpiecznego przechowywania nazwy i klucza konta magazynu. Zapisz je w zmiennych środowiskowych znajdujących się lokalnie na maszynie używanej do uruchamiania aplikacji przykładowej. Aby utworzyć zmienne środowiskowe, postępuj zgodnie z jednym z poniższych przykładów, w zależności od używanego systemu operacyjnego — Linux lub Windows.

### <a name="linux-example"></a>Przykład dla systemu Linux

```bash
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

### <a name="windows-example"></a>Przykład dla systemu Windows

```CMD
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

W tym przykładzie zostanie utworzony plik testowy w katalogu domyślnym, **AppData\Local\Temp**, dla użytkowników systemu Windows. Następnie zostaną wyświetlone monity o wykonanie następujących czynności:

1. Wprowadź odpowiednie polecenia w celu przekazania pliku testowego do usługi Azure Blob Storage.
2. Wyświetl listę obiektów blob w kontenerze.
3. Pobierz przekazany plik pod nową nazwą, aby móc później porównać stary i nowy plik. 

Jeśli chcesz uruchomić aplikację przykładową przy użyciu narzędzia Maven w wierszu polecenia, otwórz powłokę i przejdź do pozycji **storage-blobs-java-v10-quickstart** w sklonowanym katalogu. Następnie wprowadź ciąg `mvn compile exec:java`.

Ten przykład przedstawia dane wyjściowe zwracane w przypadku uruchomienia aplikacji w systemie Windows.

```Output
Created quickstart container
Enter a command
(P)utBlob | (L)istBlobs | (G)etBlob | (D)eleteBlobs | (E)xitSample
# Enter a command :
P
Uploading the sample file into the container: https://<storageaccount>.blob.core.windows.net/quickstart
# Enter a command :
L
Listing blobs in the container: https://<storageaccount>.blob.core.windows.net/quickstart
Blob name: SampleBlob.txt
# Enter a command :
G
Get the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
The blob was downloaded to C:\Users\<useraccount>\AppData\Local\Temp\downloadedFile13097087873115855761.txt
# Enter a command :
D
Delete the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt

# Enter a command :
>> Blob deleted: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
E
Cleaning up the sample and exiting!
```

Możesz kontrolować aplikację przykładową, więc wprowadź polecenia, aby wykonać ten kod. Podczas wprowadzania jest uwzględniana wielkość liter.

Możesz również wyświetlić pliki w usłudze Blob Storage za pomocą narzędzia takiego jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Eksplorator usługi Azure Storage to darmowe narzędzie międzyplatformowe, które umożliwia dostęp do informacji na koncie magazynu. 

Sprawdź pliki. Następnie wprowadź polecenie **E** i naciśnij klawisz **Enter**, aby zakończyć demonstrację i usunąć pliki testowe. Wiesz już, jak działa aplikacja przykładowa. Otwórz plik **Quickstart.java** i spójrz na kod. 

## <a name="understand-the-sample-code"></a>Omówienie przykładowego kodu

W kolejnych sekcjach omówimy przykładowy kod, aby wyjaśnić, w jaki sposób działa.

### <a name="get-references-to-the-storage-objects"></a>Pobieranie odwołań do obiektów magazynu

Najpierw należy utworzyć odwołania do obiektów używane w celu uzyskania dostępu do usługi Blob Storage i zarządzania nią. Te obiekty bazują na siebie nawzajem. Każdy obiekt jest używany przez kolejny obiekt na liście.

1. Utwórz wystąpienie obiektu **StorageURL** wskazujące konto magazynu.

    * Obiekt [StorageURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._storage_u_r_l?view=azure-java-stable) jest reprezentacją konta magazynu. Umożliwia wygenerowanie nowego potoku. 
    * Potok to zbiór zasad, które są używane do manipulowania żądaniami i odpowiedziami za pomocą mechanizmów autoryzacji, rejestrowania i ponawiania. Aby uzyskać więcej informacji, zobacz [HTTP Pipeline (Potok HTTP)](https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview#url-types--http-pipeline).  
    * Za pomocą potoku utwórz wystąpienie obiektu [ServiceURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._service_u_r_l?view=azure-java-stable).
    * Za pomocą obiektu **ServiceURL** obiektów utwórz wystąpienie obiektu [ContainerURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-stable).
    * Obiekt **ContainerURL** jest wymagany do wykonywania operacji na kontenerach obiektów blob.

2. Utwórz wystąpienie obiektu **ContainerURL** reprezentujące kontener, do którego uzyskujesz dostęp. Kontenery porządkują obiekty blob w ten sam sposób, w jaki foldery na komputerze porządkują pliki.

    * Obiekt **ContainerURL** zapewnia punkt dostępu do usługi kontenera. 
    * Możesz utworzyć wystąpienie obiektu [BlobURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._blob_u_r_l?view=azure-java-stable) za pomocą obiektu [ContainerURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-stable).
    * Obiekt **BlobURL** jest wymagany do tworzenia obiektów blob.

3. Utwórz wystąpienie obiektu **BlobURL** wskazujące konkretny obiekt blob, który Cię interesuje. 

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać dodatkowe informacje o regułach nazewnictwa kontenerów i obiektów blob, zobacz [Naming and Referencing Containers, Blobs, and Metadata (Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych)](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

### <a name="create-a-container"></a>Tworzenie kontenera 

W tej sekcji utworzysz wystąpienie obiektu **ContainerURL**. Razem z nim utworzysz nowy kontener. Kontener w przykładowej aplikacji ma nazwę **quickstart**. 

W tym przykładzie użyto metody [ContainerURL.create](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l.create?view=azure-java-stable#com_microsoft_azure_storage_blob__container_u_r_l_create_Metadata_PublicAccessType_Context_), aby tworzyć nowy kontener za każdym razem, gdy jest uruchamiana aplikacja przykładowa. Możesz również utworzyć kontener wcześniej, aby nie było konieczne tworzenie go w kodzie.

```java
// Create a ServiceURL to call the Blob service. We will also use this to construct the ContainerURL
SharedKeyCredentials creds = new SharedKeyCredentials(accountName, accountKey);
// We are using a default pipeline here, you can learn more about it at https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview
final ServiceURL serviceURL = new ServiceURL(new URL("http://" + accountName + ".blob.core.windows.net"), StorageURL.createPipeline(creds, new PipelineOptions()));

// Let's create a container using a blocking call to Azure Storage
// If container exists, we'll catch and continue
containerURL = serviceURL.createContainerURL("quickstart");

try {
    ContainersCreateResponse response = containerURL.create(null, null).blockingGet();
    System.out.println("Container Create Response was " + response.statusCode());
} catch (RestException e){
    if (e instanceof RestException && ((RestException)e).response().statusCode() != 409) {
        throw e;
    } else {
        System.out.println("quickstart container already exists, resuming...");
    }
}
```

### <a name="upload-blobs-to-the-container"></a>Przekazywanie obiektów blob do kontenera

Usługa Blob Storage obsługuje blokowe, uzupełnialne i stronicowe obiekty blob. Blokowe obiekty blob są używane najczęściej. Są one używane w tym przewodniku Szybki start. 

1. Aby przekazać plik do obiektu blob, pobierz odwołanie do obiektu blob w kontenerze docelowym. 
2. Po pobraniu odwołania do obiektu blob możesz przekazać do niego plik, korzystając z dowolnego z następujących interfejsów API:

   * Interfejsy API niskiego poziomu. To na przykład interfejs [BlockBlobURL.upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.upload?view=azure-java-stable#com_microsoft_azure_storage_blob__block_blob_u_r_l_upload_Flowable_ByteBuffer__long_BlobHTTPHeaders_Metadata_BlobAccessConditions_Context_), nazywany też PutBlob, czy [BlockBlobURL.stageBlock](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.stageblock?view=azure-java-stable), nazywany też PutBLock, w wystąpieniu obiektu **BlockBlobURL**. 

   * Interfejsy API wysokiego poziomu w [klasie TransferManager](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._transfer_manager?view=azure-java-stable). Na przykład metoda [TransferManager.uploadFileToBlockBlob](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._transfer_manager.uploadfiletoblockblob?view=azure-java-stable). 

     Ta operacja tworzy obiekt blob, jeśli jeszcze nie istnieje. Jeśli obiekt blob już istnieje, zostanie zastąpiony.

Kod przykładowy tworzy plik lokalny do użycia podczas przekazywania i pobierania. Plik do przekazania jest zapisywany jako **sourceFile**, a adres URL obiektu blob jako **blob**. Następujący kod przykładowy przekazuje plik do kontenera o nazwie **quickstart**.

```java
static void uploadFile(BlockBlobURL blob, File sourceFile) throws IOException {

    FileChannel fileChannel = FileChannel.open(sourceFile.toPath());

    // Uploading a file to the blobURL using the high-level methods available in TransferManager class
    // Alternatively call the Upload/StageBlock low-level methods from BlockBlobURL type
    TransferManager.uploadFileToBlockBlob(fileChannel, blob, 8*1024*1024, null)
        .subscribe(response-> {
            System.out.println("Completed upload request.");
            System.out.println(response.response().statusCode());
        });
}
```

Blokowe obiekty blob mogą mieć formę dowolnego typu pliku tekstowego lub binarnego. Stronicowe obiekty blob są używane głównie do tworzenia plików VHD służących do obsługi maszyn wirtualnych IaaS. Uzupełnialne obiekty blob służą do dołączania danych na końcu i są często używane do rejestrowania. Większość obiektów przechowywanych w usłudze Blob Storage to blokowe obiekty blob.

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Możesz uzyskać listę obiektów w kontenerze za pomocą polecenia [ContainerURL.listBlobsFlatSegment](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l.listblobsflatsegment?view=azure-java-stable). Ta metoda zwraca maksymalnie 5 000 obiektów, wraz ze znacznikiem kontynuacji (znacznikiem „dalej”), jeśli w kontenerze istnieje więcej obiektów do wyświetlenia. Możesz utworzyć funkcję pomocniczą, która wywołuje się wielokrotnie, dopóki w poprzedniej odpowiedzi **listBlobsFlatSegment** istnieje znacznik „dalej”.

```java
static void listBlobs(ContainerURL containerURL) {
    // Each ContainerURL.listBlobsFlatSegment call return up to maxResults (maxResults=10 passed into ListBlobOptions below).
    // To list all Blobs, we are creating a helper static method called listAllBlobs,
    // and calling it after the initial listBlobsFlatSegment call
    ListBlobsOptions options = new ListBlobsOptions(null, null, 10);

    containerURL.listBlobsFlatSegment(null, options)
        .flatMap(containersListBlobFlatSegmentResponse ->
            listAllBlobs(containerURL, containersListBlobFlatSegmentResponse))
                .subscribe(response-> {
                    System.out.println("Completed list blobs request.");
                    System.out.println(response.statusCode());
                });
}

private static Single <ContainersListBlobFlatSegmentResponse> listAllBlobs(ContainerURL url, ContainersListBlobFlatSegmentResponse response) {
    // Process the blobs returned in this result segment (if the segment is empty, blobs() will be null.
    if (response.body().blobs() != null) {
        for (Blob b : response.body().blobs().blob()) {
            String output = "Blob name: " + b.name();
            if (b.snapshot() != null) {
                output += ", Snapshot: " + b.snapshot();
            }
            System.out.println(output);
        }
    }
    else {
        System.out.println("There are no more blobs to list off.");
    }

    // If there is not another segment, return this response as the final response.
    if (response.body().nextMarker() == null) {
        return Single.just(response);
    } else {
        /*
        IMPORTANT: ListBlobsFlatSegment returns the start of the next segment; you MUST use this to get the next
        segment (after processing the current result segment
        */

        String nextMarker = response.body().nextMarker();

        /*
        The presence of the marker indicates that there are more blobs to list, so we make another call to
        listBlobsFlatSegment and pass the result through this helper function.
        */

        return url.listBlobsFlatSegment(nextMarker, new ListBlobsOptions(null, null,1))
            .flatMap(containersListBlobFlatSegmentResponse ->
                listAllBlobs(url, containersListBlobFlatSegmentResponse));
    }
}
```

### <a name="download-blobs"></a>Pobieranie obiektów blob

Pobierz obiekty blob na dysk lokalny, używając metody [BlobURL.download](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._blob_u_r_l.download?view=azure-java-stable).

Poniższy kod pozwala pobrać obiekt blob przekazany w poprzedniej sekcji. Do nazwy obiektu blob jest dodawany sufiks **_DOWNLOADED**, co pozwala zobaczyć oba pliki na dysku lokalnym. 

```java
static void getBlob(BlockBlobURL blobURL, File sourceFile) {
    try {
        // Get the blob using the low-level download method in BlockBlobURL type
        // com.microsoft.rest.v2.util.FlowableUtil is a static class that contains helpers to work with Flowable
        blobURL.download(new BlobRange(0, Long.MAX_VALUE), null, false)
            .flatMapCompletable(response -> {
                AsynchronousFileChannel channel = AsynchronousFileChannel.open(Paths
                    .get(sourceFile.getPath()), StandardOpenOption.CREATE,  StandardOpenOption.WRITE);
                        return FlowableUtil.writeFile(response.body(), channel);
            }).doOnComplete(()-> System.out.println("The blob was downloaded to " + sourceFile.getAbsolutePath()))
            // To call it synchronously add .blockingAwait()
            .subscribe();
    } catch (Exception ex){
        System.out.println(ex.toString());
    }
}
```

### <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już obiektów blob przekazanych podczas pracy z tym przewodnikiem Szybki start, możesz usunąć cały kontener za pomocą metody [ContainerURL.delete](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l.delete?view=azure-java-stable). Ta metoda spowoduje również usunięcie plików w kontenerze.

```java
containerURL.delete(null).blockingGet();
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przedstawiono metodę transferowania plików między dyskiem lokalnym i usługą Azure Blob Storage przy użyciu języka Java. 

> [!div class="nextstepaction"]
> [Kod źródłowy zestawu SDK usługi Storage w wersji 10 dla języka Java](https://github.com/Azure/azure-storage-java/)
> [Dokumentacja interfejsu API](https://docs.microsoft.com/java/api/overview/azure/storage/client?view=azure-java-stable)
> [Dowiedz się więcej o bibliotece RxJava](https://github.com/ReactiveX/RxJava)
