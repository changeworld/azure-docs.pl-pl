---
title: Rozpoczynanie pracy z obiektu blob magazynu i Visual Studio połączone usługi (usługi w chmurze) | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę przy użyciu magazynu obiektów Blob platformy Azure w projektu usługi w chmurze w programie Visual Studio po połączeniu z kontem magazynu za pomocą programu Visual Studio połączone usługi
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: 1144a958-f75a-4466-bb21-320b7ae8f304
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 7c13e7d1f48365e9c1a345b1770d16724c5ed6a1
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Wprowadzenie do usługi Azure Blob Storage i Visual Studio połączone usługi (usług w chmurze projekty)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Przegląd
W tym artykule opisano, jak rozpocząć pracę z magazynu obiektów Blob Azure, po utworzeniu lub odwołuje się do konta usługi Azure Storage za pomocą programu Visual Studio **dodać usług połączonych** projektu usług okna dialogowego w chmurze programu Visual Studio. Poniżej opisano sposób dostępu i tworzenie kontenerów obiektów blob oraz sposób wykonywania typowych zadań, takich jak przekazywanie, wyświetlania i pobieranie obiektów blob. Przykłady są napisane w języku C\# i użyj [Biblioteka klienta usługi Microsoft Azure Storage dla platformy .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Magazyn obiektów Blob Azure to usługa do przechowywania dużych ilości danych bez struktury, którego mogą uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Pojedynczego obiektu blob może być dowolnym rozmiarze. Obiekty BLOB można np. obrazów, plików audio i wideo, nieprzetworzone dane i pliki dokumentów.

Tak samo, jak żywe plików w folderach, na żywo magazynu obiektów blob w kontenerach. Po utworzeniu magazynu należy utworzyć kontenerach w magazynie. Na przykład magazynu o nazwie "Pamiętnik", można tworzyć kontenery w magazynie o nazwie "obrazy" do przechowywania obrazów i innej o nazwie "audio" do przechowywania plików audio. Po utworzeniu kontenerów, możesz przekazać pliki poszczególnych obiektów blob do nich.

* Aby uzyskać więcej informacji na programowo manipulowanie obiektów blob, zobacz [Rozpoczynanie pracy z magazynem obiektów Blob platformy Azure przy użyciu platformy .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).
* Aby uzyskać ogólne informacje na temat usługi Azure Storage, zobacz [dokumentacji magazynu](https://azure.microsoft.com/documentation/services/storage/).
* Aby uzyskać ogólne informacje o usługach w chmurze Azure, zobacz [dokumentacji usługi w chmurze](https://azure.microsoft.com/documentation/services/cloud-services/).
* Aby uzyskać więcej informacji na temat programowania aplikacji programu ASP.NET, zobacz [ASP.NET](http://www.asp.net).

## <a name="access-blob-containers-in-code"></a>Dostęp do kontenerów obiektów blob w kodzie
Do uzyskania programowego dostępu do obiektów blob w projekty usługi w chmurze, należy dodać następujące elementy, jeśli nie są one już istnieje.

1. Dodaj następujące deklaracje przestrzeni nazw kod na początku każdego pliku C# w którym chcesz uzyskania programowego dostępu do magazynu Azure.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Pobierz **CloudStorageAccount** obiekt, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu można pobrać parametry połączenia magazynu, a informacje o koncie magazynu z konfiguracji usługi Azure.
   
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));
3. Pobierz **CloudBlobClient** obiekt, aby odwołać istniejącego kontenera na koncie magazynu.
   
        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
4. Pobierz **CloudBlobContainer** obiekt, aby odwołać kontenera konkretnego obiektu blob.
   
        // Get a reference to a container named "mycontainer."
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [!NOTE]
> Korzystanie ze wszystkich kodem przedstawionym w poprzedniej procedurze przed kodu pokazano w poniższych sekcjach.
> 
> 

## <a name="create-a-container-in-code"></a>Tworzenie kontenera w kodzie
> [!NOTE]
> Niektórych interfejsów API służących do wykonywania wywołań wychodzących do magazynu Azure w ASP.NET są asynchroniczne. Zobacz [programowanie asynchroniczne z Async i Await](http://msdn.microsoft.com/library/hh191443.aspx) Aby uzyskać więcej informacji. Kod w poniższym przykładzie przyjęto założenie, że używasz metody programowania asynchronicznego.
> 
> 

Aby utworzyć kontener na koncie magazynu, jest wszystko co należy zrobić, dodaj wywołanie do **CreateIfNotExistsAsync** zgodnie z poniższym kodem:

    // If "mycontainer" doesn't exist, create it.
    await container.CreateIfNotExistsAsync();


Aby udostępnić pliki w kontenerze dla wszystkich użytkowników, można ustawić kontener jako publiczny przy użyciu następującego kodu.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });


Wszyscy użytkownicy Internetu mogą wyświetlać obiekty BLOB w kontenerze publicznym, ale można zmodyfikować lub usunąć je tylko wtedy, gdy klucz dostępu.

## <a name="upload-a-blob-into-a-container"></a>Przekazywanie obiektu blob do kontenera
Usługa Azure Storage obsługuje blokowe i stronicowe obiekty BLOB. W większości przypadków zalecane jest użycie blokowych obiektów blob.

Aby przekazać plik do blokowego obiektu blob, pobierz odwołanie do kontenera i uzyskaj za jego pomocą odwołanie do blokowego obiektu blob. Po uzyskaniu odwołania do obiektu blob możesz przekazać do niego dowolny strumień danych, wywołując metodę **UploadFromStream** . Ta operacja tworzy obiekt blob, jeśli jeszcze nie istniał, lub go zastępuje, jeśli już istniał. W poniższym przykładzie przedstawiono, jak przekazać obiekt blob do kontenera, zakładając, że kontener został już utworzony.

    // Retrieve a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze
Aby wyświetlić listę obiektów blob w kontenerze, należy najpierw uzyskać odwołanie do kontenera. Następnie można użyć metody **ListBlobs** kontenera, aby pobrać obiekty blob i/lub zawarte w nim katalogi. Aby dostęp do bogatego zestawu właściwości i metod zwróconego **IListBlobItem**, należy rzutować go do **CloudBlockBlob**, **CloudPageBlob**, lub  **CloudBlobDirectory** obiektu. Jeśli typ jest nieznany, można zastosować sprawdzanie typu, aby określić, do którego obiektu rzutować obiekt. Poniższy kod przedstawia sposób pobierania i zwracania identyfikatora URI poszczególnych elementów w kontenerze **photos**:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;

            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }

Jak pokazano w poprzednim przykładzie kodu, usługa blob korzysta z koncepcji katalogów w kontenerach, jak również. Jest to, dzięki czemu można organizować w strukturze więcej folderu przypominającej obiektów blob. Rozważmy na przykład następujący zestaw blokowych obiektów blob w kontenerze o nazwie **photos**:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Podczas wywoływania **ListBlobs** w kontenerze (jak powyższego przykładu) zawiera kolekcji zwróconej **CloudBlobDirectory** i **CloudBlockBlob** obiektów reprezentujący katalogi i obiekty BLOB, zawarty na najwyższym poziomie. Poniżej przedstawiono dane wyjściowe:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Opcjonalnie można ustawić parametr **UseFlatBlobListing** metody **ListBlobs** na wartość **true**. Powoduje to każdy obiekt blob jest zwracana jako **CloudBlockBlob**, niezależnie od tego katalogu. Oto wywołanie **ListBlobs**:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

i poniżej przedstawiono wyniki:

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Aby uzyskać więcej informacji, zobacz [CloudBlobContainer.ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx).

## <a name="download-blobs"></a>Pobieranie obiektów blob
Aby pobrać obiekty blob, należy najpierw pobrać odwołanie do obiektu blob, a następnie wywołać metodę **DownloadToStream**. W poniższym przykładzie użyto metody **DownloadToStream**, aby przesłać zawartość obiektu blob do obiektu strumienia, który można następnie zachować w pliku lokalnym.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

Można również użyć metody **DownloadToStream**, aby pobrać zawartość obiektu blob jako ciąg tekstowy.

    // Get a reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>Usuwanie obiektów blob
Aby usunąć obiekt blob, najpierw pobrać odwołanie do obiektu blob, a następnie wywołać **usunąć** metody.

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>Asynchroniczne wyświetlanie obiektów blob na stronach
Jeśli chcesz wyświetlić dużą liczbę obiektów blob lub kontrolować liczbę wyników zwracanych przez jedną operację wyświetlania listy, możesz wyświetlić obiekty blob na stronach wyników. W tym przykładzie przedstawiono sposób asynchronicznego zwracania wyników na stronach, dzięki czemu wykonanie nie jest blokowane podczas oczekiwania na zwrócenie dużych zestawów wyników.

W tym przykładzie przedstawiono niezhierarchizowaną listę obiektów blob, ale można również uzyskać listę hierarchiczną, ustawiając parametr **useFlatBlobListing** metody **ListBlobsSegmentedAsync** na wartość **false**.

Ze względu na to, że przykładowa metoda wywołuje metodę asynchroniczną, musi być poprzedzona słowem kluczowym **async** i zwracać obiekt **Task**. Słowo kluczowe await określone dla metody **ListBlobsSegmentedAsync** wstrzymuje wykonywanie przykładowej metody do momentu ukończenia zadania wyświetlania listy.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        // List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        // Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        // When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            // This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            // or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

