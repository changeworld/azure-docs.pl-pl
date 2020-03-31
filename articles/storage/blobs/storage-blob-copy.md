---
title: Kopiowanie obiektu blob za pomocą platformy .NET — usługa Azure Storage
description: Dowiedz się, jak skopiować obiekt blob na koncie usługi Azure Storage przy użyciu biblioteki klienta platformy .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 9ffa69980f020580376aea447f40ac615f26cf03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135891"
---
# <a name="copy-a-blob-with-net"></a>Kopiowanie obiektu blob za pomocą platformy .NET

W tym artykule pokazano, jak skopiować obiekt blob za pomocą konta usługi Azure Storage. Pokazuje również, jak przerwać operację kopiowania asynchronii. Przykładowy kod używa [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-copying-blobs"></a>Kopiowanie obiektów blob — informacje

Podczas kopiowania obiektu blob w ramach tego samego konta magazynu, jest to operacja synchronicznego. Podczas kopiowania między kontami jest to operacja asynchroniza. [Metody StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) i [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) zwracają wartość identyfikatora kopii, która jest używana do sprawdzania stanu lub przerwania operacji kopiowania.

Źródłowy obiekt blob dla operacji kopiowania może być blok obiektu blob, dołączanie obiektu blob, obiekt blob strony lub migawki. Jeśli docelowy obiekt blob już istnieje, musi mieć ten sam typ obiektu blob, co źródłowy obiekt blob. Wszystkie istniejące docelowe obiekty blob zostaną zastąpione. 

Docelowego obiektu blob nie można zmodyfikować podczas operacji kopiowania. Obiekt docelowy może mieć tylko jedną operację zaległego kopiowania obiektu blob. Innymi słowy obiektu blob nie może być miejscem docelowym dla wielu oczekujących operacji kopiowania.

Cały źródłowy obiekt blob lub plik jest zawsze kopiowany. Kopiowanie zakresu bajtów lub zestawu bloków nie jest obsługiwane.

Gdy obiekt blob jest kopiowany, jego właściwości systemowe są kopiowane do docelowego obiektu blob o tych samych wartościach.

Dla wszystkich typów obiektów blob można sprawdzić [CopyState.Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet) właściwość w docelowym obiekcie blob, aby uzyskać stan operacji kopiowania. Ostateczny obiekt blob zostaną zatwierdzone po zakończeniu kopiowania.

Operacja kopiowania może przybierać dowolną z następujących form:

  - Źródłowy obiekt blob można skopiować do docelowego obiektu blob o innej nazwie. Obiekt docelowy może być istniejącym obiektem blob tego samego typu obiektu blob (blok, dołącz lub strony) lub może być nowym obiektem blob utworzonym przez operację kopiowania.
  - Źródłowy obiekt blob można skopiować do docelowego obiektu blob o tej samej nazwie, skutecznie zastępując docelowy obiekt blob. Taka operacja kopiowania usuwa wszystkie niezatwierdzone bloki i zastępuje metadane docelowego obiektu blob.
  - Plik źródłowy w usłudze Azure File można skopiować do docelowego obiektu blob. Obiekt docelowy może być istniejącym obiektem blob bloku lub może być nowym blokiem blob utworzonym przez operację kopiowania. Kopiowanie z plików do stronicowych obiektów blob lub dołączanie obiektów blob nie jest obsługiwane.
  - Migawkę można skopiować za pomocą podstawowego obiektu blob. Promując migawkę do pozycji podstawowego obiektu blob, można przywrócić wcześniejszą wersję obiektu blob.
  - Migawkę można skopiować do docelowego obiektu blob o innej nazwie. Wynikowy obiekt blob docelowy jest zapisywalny obiekt blob, a nie migawka.

## <a name="copy-a-blob"></a>Kopiowanie obiektu blob

Aby skopiować obiekt blob, wywołanie jednej z następujących metod:

 - [Rozpocznijoskop](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)
 - [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)

Poniższy przykład kodu pobiera odwołanie do obiektu blob utworzonego wcześniej i kopiuje go do nowego obiektu blob w tym samym kontenerze:

```csharp
private static async Task CopyBlockBlobAsync(CloudBlobContainer container)
{
    CloudBlockBlob sourceBlob = null;
    CloudBlockBlob destBlob = null;
    string leaseId = null;

    try
    {
        // Get a block blob from the container to use as the source.
        sourceBlob = container.ListBlobs().OfType<CloudBlockBlob>().FirstOrDefault();

        // Lease the source blob for the copy operation to prevent another client from modifying it.
        // Specifying null for the lease interval creates an infinite lease.
        leaseId = await sourceBlob.AcquireLeaseAsync(null);

        // Get a reference to a destination blob (in this case, a new blob).
        destBlob = container.GetBlockBlobReference("copy of " + sourceBlob.Name);

        // Ensure that the source blob exists.
        if (await sourceBlob.ExistsAsync())
        {
            // Get the ID of the copy operation.
            string copyId = await destBlob.StartCopyAsync(sourceBlob);

            // Fetch the destination blob's properties before checking the copy state.
            await destBlob.FetchAttributesAsync();

            Console.WriteLine("Status of copy operation: {0}", destBlob.CopyState.Status);
            Console.WriteLine("Completion time: {0}", destBlob.CopyState.CompletionTime);
            Console.WriteLine("Bytes copied: {0}", destBlob.CopyState.BytesCopied.ToString());
            Console.WriteLine("Total bytes: {0}", destBlob.CopyState.TotalBytes.ToString());
            Console.WriteLine();
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        // Break the lease on the source blob.
        if (sourceBlob != null)
        {
            await sourceBlob.FetchAttributesAsync();

            if (sourceBlob.Properties.LeaseState != LeaseState.Available)
            {
                await sourceBlob.BreakLeaseAsync(new TimeSpan(0));
            }
        }
    }
}
```

## <a name="abort-a-blob-copy-operation"></a>Przerywanie operacji kopiowania obiektów blob

Przerwanie operacji kopiowania powoduje docelowy obiekt blob o zerowej długości dla bloków obiektów blob, dołączania obiektów blob i stronicowych obiektów blob. Jednak metadane docelowego obiektu blob będą miały nowe wartości skopiowane ze źródłowego obiektu blob lub ustawione jawnie w wywołaniu [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) lub [StartCopyAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) Aby zachować oryginalne metadane przed kopią, przed `StartCopy` wywołaniem `StartCopyAsync`lub zrobić migawkę docelowego obiektu blob lub .

Po przerwaniu trwającej operacji kopiowania obiektów blob docelowy obiekt blob [CopyState.Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet#Microsoft_Azure_Storage_Blob_CopyState_Status) jest ustawiony na [CopyStatus.Aborted](/dotnet/api/microsoft.azure.storage.blob.copystatus?view=azure-dotnet).

[Metody AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy?view=azure-dotnet) i [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync?view=azure-dotnet) anulują trwającą operację kopiowania obiektów blob i pozostawiają docelowy obiekt blob o zerowej długości i pełnych metadanych.

```csharp
// Fetch the destination blob's properties before checking the copy state.
await destBlob.FetchAttributesAsync();

// Check the copy status. If it is still pending, abort the copy operation.
if (destBlob.CopyState.Status == CopyStatus.Pending)
{
    await destBlob.AbortCopyAsync(copyId);
    Console.WriteLine("Copy operation {0} has been aborted.", copyId);
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Następne kroki

Poniższe tematy zawierają informacje dotyczące kopiowania obiektów blob i przerywania trwających operacji kopiowania przy użyciu interfejsów API rest platformy Azure.

- [Kopiowanie obiektu blob](/rest/api/storageservices/copy-blob)
- [Przerwanie kopiowania obiektu blob](/rest/api/storageservices/abort-copy-blob)
