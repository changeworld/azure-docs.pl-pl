---
title: Wyświetlanie listy kontenerów obiektów BLOB przy użyciu platformy .NET — Azure Storage
description: Dowiedz się, jak wyświetlać listę kontenerów obiektów BLOB na koncie usługi Azure Storage za pomocą biblioteki klienckiej platformy .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: a76b83218a194c2b5cbf3ce582e8094014004123
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803381"
---
# <a name="list-blob-containers-with-net"></a>Wyświetlanie listy kontenerów obiektów BLOB przy użyciu platformy .NET

Po wyświetleniu listy kontenerów na koncie usługi Azure Storage w kodzie możesz określić szereg opcji zarządzania wynikami zwracanymi z usługi Azure Storage. W tym artykule przedstawiono sposób wyświetlania listy kontenerów przy użyciu [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage/client).  

## <a name="understand-container-listing-options"></a>Informacje o opcjach wyświetlania kontenera

Aby wyświetlić listę kontenerów na koncie magazynu, wywołaj jedną z następujących metod:

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

Przeciążenia tych metod zawierają dodatkowe opcje zarządzania kontenerami zwracanymi przez operację tworzenia listy. Te opcje są opisane w poniższych sekcjach.

### <a name="manage-how-many-results-are-returned"></a>Zarządzanie liczbą zwracanych wyników

Domyślnie operacja tworzenia listy zwraca do 5000 wyników jednocześnie. Aby zwrócić mniejszy zestaw wyników, podaj wartość różną od zera dla parametru `maxresults` podczas wywoływania jednej z metod **ListContainerSegmented** .

Jeśli konto magazynu zawiera więcej niż 5000 kontenerów lub jeśli określono wartość `maxresults` w taki sposób, że operacja tworzenia listy zwróci podzestaw kontenerów na koncie magazynu, usługa Azure Storage zwraca *token kontynuacji* z listą opakowania. Token kontynuacji jest wartością nieprzezroczystą, która służy do pobierania następnego zestawu wyników z usługi Azure Storage.

W kodzie Sprawdź wartość tokenu kontynuacji, aby określić, czy ma ona wartość null. Gdy token kontynuacji ma wartość null, zestaw wyników jest zakończony. Jeśli token kontynuacji nie ma wartości null, ponownie wywołaj **ListContainersSegmented** lub **ListContainersSegmentedAsync** , przekazując token kontynuacji, aby pobrać następny zestaw wyników, dopóki token kontynuacji nie będzie miał wartości null.

### <a name="filter-results-with-a-prefix"></a>Filtruj wyniki przy użyciu prefiksu

Aby odfiltrować listę kontenerów, Określ ciąg dla parametru `prefix`. Ciąg prefiksu może zawierać jeden lub więcej znaków. Usługa Azure Storage zwraca tylko te kontenery, których nazwy rozpoczynają się od tego prefiksu.

### <a name="return-container-metadata"></a>Zwracanie metadanych kontenera

Aby zwrócić metadane kontenera z wynikami, określ wartość **metadanych** dla wyliczenia [ContainerListDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) . Usługa Azure Storage obejmuje metadane z każdym zwracanym kontenerem, dlatego nie trzeba również wywoływać jednej z metod **FetchAttributes** , aby pobrać metadane kontenera.

## <a name="example-list-containers"></a>Przykład: kontenery list

Poniższy przykład asynchronicznie wyświetla listę kontenerów na koncie magazynu, które zaczynają się od określonego prefiksu. Przykład zawiera listę kontenerów z przyrostem 5 wyników w czasie i używa tokenu kontynuacji w celu uzyskania następnego segmentu wyników. Przykład zwraca również metadane kontenera z wynikami.

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
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

## <a name="see-also"></a>Zobacz także

[Kontenery List](/rest/api/storageservices/list-containers2)
[wyliczanie zasobów obiektów BLOB](/rest/api/storageservices/enumerating-blob-resources)
