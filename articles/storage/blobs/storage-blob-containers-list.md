---
title: Wyświetlanie listy kontenerów obiektów blob za pomocą usługi .NET — usługa Azure Storage
description: Dowiedz się, jak wyświetlić listę kontenerów obiektów blob na koncie usługi Azure Storage przy użyciu biblioteki klienta platformy .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 155b8f5d50c7b106daff8dab4df17200b844c988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135908"
---
# <a name="list-blob-containers-with-net"></a>Wyświetlanie listy kontenerów obiektów blob z zasadą .NET

Podczas listy kontenerów na koncie usługi Azure Storage z kodu, można określić szereg opcji, aby zarządzać, jak wyniki są zwracane z usługi Azure Storage. W tym artykule pokazano, jak wyświetlić listę kontenerów przy użyciu [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).  

## <a name="understand-container-listing-options"></a>Opis opcji wyświetlania kontenerów

Aby wyświetlić listę kontenerów na koncie magazynu, wywołanie jednej z następujących metod:

- [ListaZasegmentowane](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListaKontenersegmentedasync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

Przeciążenia dla tych metod zapewniają dodatkowe opcje zarządzania, jak kontenery są zwracane przez operację aukcji. Te opcje są opisane w poniższych sekcjach.

### <a name="manage-how-many-results-are-returned"></a>Zarządzanie zwracanymi wynikami

Domyślnie operacja aukcji zwraca maksymalnie 5000 wyników naraz. Aby zwrócić mniejszy zestaw wyników, podaj wartość `maxresults` niezerową dla parametru podczas wywoływania jednej z **metod ListContainerSegmented.**

Jeśli konto magazynu zawiera więcej niż 5000 kontenerów lub `maxresults` jeśli określono wartość dla takiej, że operacja listy zwraca podzbiór kontenerów na koncie magazynu, a następnie usługa Azure Storage zwraca *token kontynuacji* z listą kontenerów. Token kontynuacji jest nieprzezroczystą wartością, której można użyć do pobrania następnego zestawu wyników z usługi Azure Storage.

W kodzie sprawdź wartość tokenu kontynuacji, aby ustalić, czy jest null. Gdy token kontynuacji jest null, a następnie zestaw wyników jest zakończona. Jeśli token kontynuacji nie jest null, a następnie wywołać **ListContainersSegmented** lub **ListContainersSegmentedAsync** ponownie, przekazywanie w token kontynuacji, aby pobrać następny zestaw wyników, dopóki token kontynuacji jest null.

### <a name="filter-results-with-a-prefix"></a>Filtrowanie wyników z prefiksem

Aby filtrować listę kontenerów, należy `prefix` określić ciąg parametru. Ciąg prefiksu może zawierać jeden lub więcej znaków. Usługa Azure Storage zwraca tylko kontenery, których nazwy zaczynają się od tego prefiksu.

### <a name="return-metadata"></a>Zwracanie metadanych

Aby zwrócić metadane kontenera z wynikami, określ wartość **metadanych** dla [wyliczenia ContainerListingDetails.](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) Usługa Azure Storage zawiera metadane z każdego zwracanego kontenera, więc nie trzeba również wywoływać jedną z **metod FetchAttributes,** aby pobrać metadane kontenera.

## <a name="example-list-containers"></a>Przykład: Kontenery listy

Poniższy przykład asynchronicznie wyświetla listę kontenerów na koncie magazynu, które zaczynają się od określonego prefiksu. Przykład zawiera listę kontenerów w przyrostach 5 wyników w czasie i używa tokenu kontynuacji, aby uzyskać następny segment wyników. W przykładzie zwraca również metadane kontenera z wynikami.

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix)
{
    Console.WriteLine("List all containers beginning with prefix {0}, plus container metadata:", prefix);

    try
    {
        ContainerResultSegment resultSegment = null;
        BlobContinuationToken continuationToken = null;

        do
        {
            // List containers beginning with the specified prefix, returning segments of 5 results each.
            // Passing null for the maxResults parameter returns the max number of results (up to 5000).
            // Requesting the container's metadata with the listing operation populates the metadata,
            // so it's not necessary to also call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, 5, continuationToken, null, null);

            // Enumerate the containers returned.
            foreach (var container in resultSegment.Results)
            {
                Console.WriteLine("\tContainer:" + container.Name);

                // Write the container's metadata keys and values.
                foreach (var metadataItem in container.Metadata)
                {
                    Console.WriteLine("\t\tMetadata key: " + metadataItem.Key);
                    Console.WriteLine("\t\tMetadata value: " + metadataItem.Value);
                }
            }

            // Get the continuation token. If not null, get the next segment.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0} : {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Zobacz też

[Lista kontenerów](/rest/api/storageservices/list-containers2)
[wyliczających zasoby obiektów blob](/rest/api/storageservices/enumerating-blob-resources)
