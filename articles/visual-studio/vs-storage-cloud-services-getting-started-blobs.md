---
title: Wprowadzenie do magazynu obiektów blob przy użyciu programu Visual Studio (usługi w chmurze)
description: Jak rozpocząć korzystanie z magazynu obiektów Blob platformy Azure w projekcie usługi w chmurze w programie Visual Studio po nawiązaniu połączenia z kontem magazynu przy użyciu usług połączonych z programem Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: 1144a958-f75a-4466-bb21-320b7ae8f304
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d151e55f627166d8ad7d8affa53740e86cd1e501
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298796"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Wprowadzenie do usługi Azure Blob Storage i usług połączonych z programem Visual Studio (projekty usług w chmurze)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Omówienie
W tym artykule opisano, jak rozpocząć pracę z usługą Azure Blob Storage po utworzeniu konta usługi Azure Storage lub do którego nawiązano do niego przy użyciu okna dialogowego **Dodaj połączone usługi** programu Visual Studio w projekcie usług w chmurze programu Visual Studio. Pokażemy Ci, jak uzyskać dostęp do kontenerów obiektów blob i tworzyć je oraz jak wykonywać typowe zadania, takie jak przekazywanie, wystawianie i pobieranie obiektów blob. Przykłady są zapisywane\# w języku C i używają [biblioteki klienta usługi Microsoft Azure Storage dla platformy .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Usługa Azure Blob Storage to usługa do przechowywania dużych ilości nieustrukturyzowanych danych, do których można uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Pojedynczy obiekt blob może mieć dowolny rozmiar. Obiekty BLOB mogą być takimi jak obrazy, pliki audio i wideo, nieprzetworzone dane i pliki dokumentów.

Podobnie jak pliki w folderach, obiekty blob magazynu na żywo w kontenerach. Po utworzeniu magazynu należy utworzyć jeden lub więcej kontenerów w magazynie. Na przykład w magazynie o nazwie "Notatnik" można utworzyć kontenery w magazynie o nazwie "obrazy" do przechowywania obrazów i inny o nazwie "audio" do przechowywania plików audio. Po utworzeniu kontenerów można przekazać do nich poszczególne pliki obiektów blob.

* Aby uzyskać więcej informacji na temat programowego manipulowania obiektami blob, zobacz [Wprowadzenie do magazynu obiektów Blob platformy Azure przy użyciu platformy .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).
* Aby uzyskać ogólne informacje na temat usługi Azure Storage, zobacz [dokumentację magazynu](https://azure.microsoft.com/documentation/services/storage/).
* Aby uzyskać ogólne informacje na temat usług w chmurze azure, zobacz [dokumentację usług w chmurze](https://azure.microsoft.com/documentation/services/cloud-services/).
* Aby uzyskać więcej informacji na temat programowania aplikacji ASP.NET, zobacz [ASP.NET](https://www.asp.net).

## <a name="access-blob-containers-in-code"></a>Dostęp do kontenerów obiektów blob w kodzie
Aby programowo uzyskiwać dostęp do obiektów blob w projektach usługi w chmurze, należy dodać następujące elementy, jeśli nie są one jeszcze obecne.

1. Dodaj następujące deklaracje obszaru nazw kodu do góry dowolnego pliku Języka C#, w którym chcesz programowo uzyskać dostęp do usługi Azure Storage.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Pobierz **CloudStorageAccount** obiektu, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu, aby uzyskać parametry połączenia magazynu i informacje o koncie magazynu z konfiguracji usługi platformy Azure.
   
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));
3. Pobierz **CloudBlobClient** obiektu do odwoływania się do istniejącego kontenera na koncie magazynu.
   
        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
4. Pobierz **CloudBlobContainer** obiektu do odwoływania się do określonego kontenera obiektu blob.
   
        // Get a reference to a container named "mycontainer."
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [!NOTE]
> Użyj całego kodu pokazanego w poprzedniej procedurze przed kodem pokazanym w poniższych sekcjach.
> 
> 

## <a name="create-a-container-in-code"></a>Tworzenie kontenera w kodzie
> [!NOTE]
> Niektóre interfejsy API, które wykonują wywołania usługi Azure Storage w ASP.NET są asynchroniczne. Zobacz [programowanie asynchroniczne z Async i Czekaj, aby](https://msdn.microsoft.com/library/hh191443.aspx) uzyskać więcej informacji. Kod w poniższym przykładzie zakłada, że używasz metod programowania asynchroniowego.
> 
> 

Aby utworzyć kontener na koncie magazynu, wszystko, co musisz zrobić, to dodać wywołanie **CreateIfNotExistsAsync** jak w poniższym kodzie:

    // If "mycontainer" doesn't exist, create it.
    await container.CreateIfNotExistsAsync();


Aby udostępnić pliki w kontenerze wszystkim, można ustawić kontener do publicznej wiadomości przy użyciu następującego kodu.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });


Każda osoba w Internecie może zobaczyć obiekty blob w kontenerze publicznym, ale można je modyfikować lub usuwać tylko wtedy, gdy masz odpowiedni klucz dostępu.

## <a name="upload-a-blob-into-a-container"></a>Przekazywanie obiektu blob do kontenera
Usługa Azure Storage obsługuje blokowe obiekty blob i obiekty blob strony. W większości przypadków zalecane jest użycie blokowych obiektów blob.

Aby przekazać plik do blokowego obiektu blob, pobierz odwołanie do kontenera i uzyskaj za jego pomocą odwołanie do blokowego obiektu blob. Po uzyskaniu odwołania do obiektu blob możesz przekazać do niego dowolny strumień danych, wywołując metodę **UploadFromStream** . Ta operacja tworzy obiekt blob, jeśli jeszcze nie istniał, lub go zastępuje, jeśli już istniał. W poniższym przykładzie przedstawiono, jak przekazać obiekt blob do kontenera, zakładając, że kontener został już utworzony.

    // Retrieve a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze
Aby wyświetlić listę obiektów blob w kontenerze, należy najpierw uzyskać odwołanie do kontenera. Następnie można użyć metody **ListBlobs** kontenera, aby pobrać obiekty blob i/lub zawarte w nim katalogi. Aby uzyskać dostęp do bogatego zestawu właściwości i metod dla zwróconego **obiektu IListBlobItem,** należy go przerzucić na obiekt **CloudBlockBlob**, **CloudPageBlob**lub **CloudBlobDirectory.** Jeśli typ jest nieznany, można zastosować sprawdzanie typu, aby określić, do którego obiektu rzutować obiekt. Poniższy kod przedstawia sposób pobierania i zwracania identyfikatora URI poszczególnych elementów w kontenerze **photos**:

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

Jak pokazano w poprzednim przykładzie kodu usługa obiektów blob ma pojęcie katalogów w kontenerach, jak również. Jest to tak, że można zorganizować obiekty blob w strukturze bardziej podobne do folderu. Rozważmy na przykład następujący zestaw blokowych obiektów blob w kontenerze o nazwie **photos**:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Podczas wywoływania **ListBlobs** w kontenerze (jak w poprzednim przykładzie), kolekcja zwrócona zawiera **CloudBlobDirectory** i **CloudBlockBlob** obiektów reprezentujących katalogi i obiekty blob zawarte na najwyższym poziomie. Oto wynikowe dane wyjściowe:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Opcjonalnie można ustawić parametr **UseFlatBlobListing** metody **ListBlobs** na wartość **true**. Powoduje to, że każdy obiekt blob jest zwracany jako **CloudBlockBlob**, niezależnie od katalogu. Oto wezwanie do **ListBlobs:**

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

a oto wyniki:

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
Aby usunąć obiekt blob, najpierw uzyskać odwołanie do obiektu blob, a następnie **wywołać Delete** metody.

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

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

