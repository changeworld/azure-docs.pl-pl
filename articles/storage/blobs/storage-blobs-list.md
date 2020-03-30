---
title: Lista obiektów blob z usługą .NET — usługa Azure Storage
description: Dowiedz się, jak wyświetlić listę obiektów blob w kontenerze na koncie usługi Azure Storage przy użyciu biblioteki klienta platformy .NET. Przykłady kodu pokazują, jak wyświetlić listę obiektów blob w aukcji płaskiej lub jak hierarchicznie wyświetlać obiekty blob, tak jakby były zorganizowane w katalogi lub foldery.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/25/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: eb62883859a3efeb1c05deb38d8a40fba76e9cdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137924"
---
# <a name="list-blobs-with-net"></a>Lista obiektów blob z .NET

Podczas listy obiektów blob z kodu, można określić szereg opcji, aby zarządzać, jak wyniki są zwracane z usługi Azure Storage. Można określić liczbę wyników do zwrócenia w każdym zestawie wyników, a następnie pobrać kolejne zestawy. Można określić prefiks do zwracania obiektów blob, których nazwy zaczynają się od tego znaku lub ciągu. Możesz też wystawić obiekty BLOB w płaskiej strukturze listy lub hierarchicznie. Hierarchiczna lista zwraca obiekty blob tak, jakby były zorganizowane w foldery. 

W tym artykule pokazano, jak wyświetlić listę obiektów blob przy użyciu [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).  

## <a name="understand-blob-listing-options"></a>Opis opcji listy obiektów blob

Aby wyświetlić listę obiektów blob na koncie magazynu, wywołanie jednej z następujących metod:

- [CloudBlobClient.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient.ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

Aby wyświetlić listę obiektów blob w kontenerze, wywołanie jednej z następujących metod:

- [CloudBlobContainer.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer.ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

Przeciążenia dla tych metod zapewniają dodatkowe opcje zarządzania, jak obiekty blob są zwracane przez operację aukcji. Te opcje są opisane w poniższych sekcjach.

### <a name="manage-how-many-results-are-returned"></a>Zarządzanie zwracanymi wynikami

Domyślnie operacja aukcji zwraca maksymalnie 5000 wyników naraz. Aby zwrócić mniejszy zestaw wyników, podaj wartość `maxresults` niezerową dla parametru podczas wywoływania jednej z metod **ListBlobs.**

Jeśli operacja aukcji zwraca więcej niż 5000 obiektów blob `maxresults` lub jeśli określono wartość dla takiej, że operacja listy zwraca podzbiór kontenerów na koncie magazynu, a następnie usługa Azure Storage zwraca *token kontynuacji* z listą obiektów blob. Token kontynuacji jest nieprzezroczystą wartością, której można użyć do pobrania następnego zestawu wyników z usługi Azure Storage.

W kodzie sprawdź wartość tokenu kontynuacji, aby ustalić, czy jest null. Gdy token kontynuacji jest null, a następnie zestaw wyników jest zakończona. Jeśli token kontynuacji nie jest null, a następnie wywołać operację listy ponownie, przekazywanie w tokenie kontynuacji, aby pobrać następny zestaw wyników, dopóki token kontynuacji jest null.

### <a name="filter-results-with-a-prefix"></a>Filtrowanie wyników z prefiksem

Aby filtrować listę kontenerów, należy `prefix` określić ciąg parametru. Ciąg prefiksu może zawierać jeden lub więcej znaków. Usługa Azure Storage zwraca tylko obiekty BLOB, których nazwy zaczynają się od tego prefiksu.

### <a name="return-metadata"></a>Zwracanie metadanych

Aby zwrócić metadane obiektów blob z wynikami, określ wartość **metadanych** dla [wyliczenia BlobListingDetails.](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) Usługa Azure Storage zawiera metadane z każdym zwróconym obiektem blob, więc nie trzeba wywoływać jednej z metod **FetchAttributes** w tym kontekście, aby pobrać metadane obiektu blob.

### <a name="flat-listing-versus-hierarchical-listing"></a>Aukcja płaska a lista hierarchiczna

Obiekty BLOB w usłudze Azure Storage są zorganizowane w płaskim paradygmacie, a nie w hierarchicznym paradygmacie (takim jak klasyczny system plików). Można jednak organizować obiekty blob w *katalogi wirtualne,* aby naśladować strukturę folderów. Katalog wirtualny stanowi część nazwy obiektu blob i jest wskazywany przez znak ogranicznika.

Aby uporządkować obiekty blob w katalogach wirtualnych, użyj znaku ogranicznika w nazwie obiektu blob. Domyślnym znakiem ogranicznika jest ukośnik do przodu (/), ale można określić dowolny znak jako ogranicznik.

Jeśli nazwa obiektu blob przy użyciu ogranicznika, następnie można wybrać listę obiektów blob hierarchicznie. W przypadku operacji hierarchicznej listy usługa Azure Storage zwraca wszystkie katalogi wirtualne i obiekty blob pod obiektem nadrzędnym. Operację listy można wywołać cyklicznie, aby przechodzić przez hierarchię, podobnie jak programowo przechodzić przez klasyczny system plików.

## <a name="use-a-flat-listing"></a>Korzystanie z aukcji płaskiej

Domyślnie operacja aukcji zwraca obiekty BLOB na aukcji płaskiej. W aukcji płaskiej obiekty blob nie są zorganizowane według katalogu wirtualnego.

Poniższy przykład zawiera listę obiektów blob w określonym kontenerze przy użyciu płaskiej listy, z opcjonalnym rozmiarem segmentu określony i zapisuje nazwę obiektu blob w oknie konsoli.

```csharp
private static async Task ListBlobsFlatListingAsync(CloudBlobContainer container, int? segmentSize)
{
    BlobContinuationToken continuationToken = null;
    CloudBlob blob;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned
        // and execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(string.Empty,
                true, BlobListingDetails.Metadata, segmentSize, continuationToken, null, null);

            foreach (var blobItem in resultSegment.Results)
            {
                // A flat listing operation returns only blobs, not virtual directories.
                blob = (CloudBlob)blobItem;

                // Write out some blob properties.
                Console.WriteLine("Blob name: {0}", blob.Name);
            }

            Console.WriteLine();

           // Get the continuation token and loop until it is null.
           continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

Dane wyjściowe próbki są podobne do:

```
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

## <a name="use-a-hierarchical-listing"></a>Używanie listy hierarchicznej

Po wywołaniu operacji aukcji hierarchicznie usługa Azure Storage zwraca katalogi wirtualne i obiekty blob na pierwszym poziomie hierarchii. [Właściwość Prefiks](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) każdego katalogu wirtualnego jest ustawiona, dzięki czemu można przekazać prefiks w wywołaniu cyklicznym, aby pobrać następny katalog.

Aby hierarchicznie wyświetlić listę `useFlatBlobListing` obiektów blob, należy ustawić parametr metody aukcji na **false**.

Poniższy przykład zawiera listę obiektów blob w określonym kontenerze przy użyciu płaskiej listy, z opcjonalnym rozmiarem segmentu określony i zapisuje nazwę obiektu blob w oknie konsoli.

```csharp
private static async Task ListBlobsHierarchicalListingAsync(CloudBlobContainer container, string prefix)
{
    CloudBlobDirectory dir;
    CloudBlob blob;
    BlobContinuationToken continuationToken = null;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned and
        // execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(prefix,
                false, BlobListingDetails.Metadata, null, continuationToken, null, null);
            foreach (var blobItem in resultSegment.Results)
            {
                // A hierarchical listing may return both virtual directories and blobs.
                if (blobItem is CloudBlobDirectory)
                {
                    dir = (CloudBlobDirectory)blobItem;

                    // Write out the prefix of the virtual directory.
                    Console.WriteLine("Virtual directory prefix: {0}", dir.Prefix);

                    // Call recursively with the prefix to traverse the virtual directory.
                    await ListBlobsHierarchicalListingAsync(container, dir.Prefix);
                }
                else
                {
                    // Write out the name of the blob.
                    blob = (CloudBlob)blobItem;

                    Console.WriteLine("Blob name: {0}", blob.Name);
                }
                Console.WriteLine();
            }

            // Get the continuation token and loop until it is null.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

Dane wyjściowe próbki są podobne do:

```
Virtual directory prefix: FolderA/
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt

Virtual directory prefix: FolderA/FolderB/
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt

Virtual directory prefix: FolderA/FolderB/FolderC/
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

> [!NOTE]
> Migawek obiektów blob nie można wymienić w operacji listy hierarchicznej.

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Następne kroki

- [Lista obiektów blob](/rest/api/storageservices/list-blobs)
- [Wyliczanie zasobów obiektów blob](/rest/api/storageservices/enumerating-blob-resources)
