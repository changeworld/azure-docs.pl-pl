---
title: Rozpoczynanie pracy z usługą blob storage i Visual Studio podłączone usługi (usługi w chmurze) | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę, przy użyciu usługi Azure Blob storage w projekt usługi w chmurze w programie Visual Studio po nawiązaniu połączenia z kontem magazynu za pomocą programu Visual Studio podłączone usługi
services: storage
author: ghogen
manager: douge
ms.assetid: 1144a958-f75a-4466-bb21-320b7ae8f304
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 9f1ef06e0275954343c548d0f6937b9c6fbcfd18
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122944"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Rozpoczynanie pracy z usługą Azure Blob Storage i Visual Studio podłączone usługi (usługi w chmurze projektów)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Omówienie
W tym artykule opisano sposób rozpoczęcia pracy z usługą Azure Blob Storage, po utworzeniu lub odwołanie do konta usługi Azure Storage za pomocą programu Visual Studio **Dodaj usługi połączone** services projektu — okno dialogowe w chmurze programu Visual Studio. Pokażemy ci, jak uzyskać dostęp i utworzyć kontenery obiektów blob oraz sposób wykonywania typowych zadań, takich jak przekazywanie, wyświetlanie i pobieranie obiektów blob. Przykłady są napisane w języku C\# i użyj [biblioteki klienta usługi Microsoft Azure Storage dla platformy .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Usługa Azure Blob Storage jest usługą służącą do przechowywania dużych ilości danych bez struktury, które są dostępne z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Pojedynczy obiekt blob może być dowolnego rozmiaru. Obiekty BLOB mogą mieć elementów, takich jak obrazy, pliki audio i wideo, nieprzetworzone dane i pliki dokumentów.

Tak samo, jak żywe plików w folderach, obiekty BLOB magazynu na żywo w kontenerach. Po utworzeniu magazynu należy utworzyć co najmniej jeden kontener w magazynie. Na przykład magazynu o nazwie "Pamiętnik", możesz tworzyć kontenery z magazynu o nazwie "obrazy", do przechowywania obrazów i innym o nazwie "audio" do przechowywania plików dźwiękowych. Po utworzeniu kontenerów, możesz przekazać pliki poszczególnych obiektów blob do nich.

* Aby uzyskać więcej informacji na temat programowe operowanie obiektów blob, zobacz [wprowadzenie do usługi Azure Blob storage przy użyciu platformy .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).
* Aby uzyskać ogólne informacje na temat usługi Azure Storage, zobacz [dokumentacja usługi Storage](https://azure.microsoft.com/documentation/services/storage/).
* Aby uzyskać ogólne informacje o usługach Azure Cloud Services, zobacz [dokumentacji usług w chmurze](https://azure.microsoft.com/documentation/services/cloud-services/).
* Aby uzyskać więcej informacji na temat programowania aplikacji programu ASP.NET, zobacz [ASP.NET](https://www.asp.net).

## <a name="access-blob-containers-in-code"></a>Dostęp do kontenerów obiektów blob w kodzie
Aby uzyskać programowy dostęp do obiektów blob w projekty usługi w chmurze, należy dodać następujące elementy, jeśli tak nie jest już obecny.

1. Dodaj następujące deklaracje przestrzeni nazw kod na początku każdego pliku C# w którym chcesz uzyskać programowy dostęp do usługi Azure Storage.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Pobierz **CloudStorageAccount** obiekt, który reprezentuje dane konta magazynu. Użyj poniższego kodu, można pobrać z parametrów połączenia magazynu i informacje o koncie magazynu z konfiguracji usługi platformy Azure.
   
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));
3. Pobierz **CloudBlobClient** obiektu do odwołania istniejący kontener na koncie magazynu.
   
        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
4. Pobierz **CloudBlobContainer** obiekt, aby odwoływać się do kontenera obiektów blob określonym.
   
        // Get a reference to a container named "mycontainer."
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [!NOTE]
> Korzystanie z wszystkich kodu pokazany w poprzedniej procedurze przed kodów pokazanych w poniższych sekcjach.
> 
> 

## <a name="create-a-container-in-code"></a>Utwórz kontener w kodzie
> [!NOTE]
> Niektóre interfejsy API, które wykonują wywołania się do usługi Azure Storage na platformie ASP.NET są asynchroniczne. Zobacz [programowanie asynchroniczne z Async i Await](https://msdn.microsoft.com/library/hh191443.aspx) Aby uzyskać więcej informacji. Kod w poniższym przykładzie przyjęto założenie, że używasz metody programowania asynchronicznego.
> 
> 

Aby utworzyć kontener na koncie magazynu, jest wszystko, czego potrzebujesz, aby zrobić, dodaj wywołanie do **CreateIfNotExistsAsync** zgodnie z poniższym kodem:

    // If "mycontainer" doesn't exist, create it.
    await container.CreateIfNotExistsAsync();


Aby pliki w kontenerze były dostępne dla wszystkich użytkowników, możesz ustawić kontener jako publiczny przy użyciu następującego kodu.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });


Wszyscy użytkownicy Internetu mogą wyświetlać obiekty BLOB w kontenerze publicznym, ale użytkownik może je modyfikować lub usuwać tylko wtedy, gdy masz odpowiedni klucz dostępu.

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
Aby wyświetlić listę obiektów blob w kontenerze, należy najpierw uzyskać odwołanie do kontenera. Następnie można użyć metody **ListBlobs** kontenera, aby pobrać obiekty blob i/lub zawarte w nim katalogi. Aby uzyskać dostęp z bogatego zestawu właściwości i metod zwróconego do **IListBlobItem**, należy rzutować go na **CloudBlockBlob**, **CloudPageBlob**, lub  **CloudBlobDirectory** obiektu. Jeśli typ jest nieznany, można zastosować sprawdzanie typu, aby określić, do którego obiektu rzutować obiekt. Poniższy kod przedstawia sposób pobierania i zwracania identyfikatora URI poszczególnych elementów w kontenerze **photos**:

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

Jak pokazano w poprzednim przykładzie kodu, usługę blob service korzysta z koncepcji katalogów w kontenerach, jak również. Jest to, dzięki czemu można organizować obiektów blob w strukturze bardziej przypominającej dla folderu. Rozważmy na przykład następujący zestaw blokowych obiektów blob w kontenerze o nazwie **photos**:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Gdy wywołujesz **ListBlobs** w kontenerze (jak w poprzednim przykładzie), zawiera zbiorze zwróconym **CloudBlobDirectory** i **CloudBlockBlob** obiektów reprezentujący katalogi i obiekty BLOB, znajdujących się na najwyższym poziomie. Oto dane wyjściowe:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Opcjonalnie można ustawić parametr **UseFlatBlobListing** metody **ListBlobs** na wartość **true**. Skutkuje to każdy obiekt blob jest zwracana jako **CloudBlockBlob**, niezależnie od tego katalogu. W tym miejscu to wywołanie **ListBlobs**:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

a Oto wyniki:

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
Aby usunąć obiekt blob, najpierw pobrać odwołanie do obiektu blob, a następnie wywołać **Usuń** metody.

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

