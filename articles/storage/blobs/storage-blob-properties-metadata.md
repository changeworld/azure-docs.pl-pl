---
title: Zarządzanie właściwościami i metadanymi obiektu blob za pomocą usługi .NET — Usługa Azure Storage
description: Dowiedz się, jak ustawić i pobrać właściwości systemu i przechowywać niestandardowe metadane na obiektach blob na koncie usługi Azure Storage przy użyciu biblioteki klienta platformy .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: b4abd7e29dec67ddc1be50a2a6703da2a25551d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137666"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>Zarządzanie właściwościami obiektu blob i metadanymi za pomocą platformy .NET

Oprócz danych, które zawierają, obiekty blobs obsługują właściwości systemu i metadane zdefiniowane przez użytkownika. W tym artykule pokazano, jak zarządzać właściwościami systemu i metadanymi zdefiniowanymi przez użytkownika za pomocą [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-properties-and-metadata"></a>Właściwości i metadane — informacje

- **Właściwości systemu:** Właściwości systemu istnieją dla każdego zasobu magazynu obiektów Blob. Niektóre z nich można odczytać lub ustawić, podczas gdy inne są tylko do odczytu. W obszarze osłon niektóre właściwości systemu odpowiadają niektórym standardowym nagłówkom HTTP. Biblioteka klienta usługi Azure Storage dla platformy .NET przechowuje te właściwości.

- **Metadane zdefiniowane przez użytkownika:** Metadane zdefiniowane przez użytkownika składają się z jednej lub kilku par nazwa-wartość, które można określić dla zasobu magazynu obiektów Blob. Metadane służy do przechowywania dodatkowych wartości z zasobem. Wartości metadanych są tylko do własnych celów i nie wpływają na zachowanie zasobu.

Pobieranie metadanych i wartości właściwości zasobu magazynu obiektów Blob jest procesem dwuetapowym. Przed odczytywaniem tych wartości, należy jawnie `FetchAttributes` pobrać `FetchAttributesAsync` je wywołując lub metody. Wyjątkiem od tej reguły `Exists` `ExistsAsync` jest to, `FetchAttributes` że metody i wywołać odpowiednią metodę w ramach obejmuje. Po wywołaniu jednej z tych metod nie trzeba `FetchAttributes`również dzwonić .

> [!IMPORTANT]
> Jeśli okaże się, że wartości właściwości lub metadanych dla zasobu `FetchAttributes` magazynu `FetchAttributesAsync` nie zostały wypełnione, sprawdź, czy kod wywołuje lub metody.

## <a name="set-and-retrieve-properties"></a>Ustawianie i pobieranie właściwości

Poniższy przykład kodu `ContentType` `ContentLanguage` ustawia właściwości i system na obiekcie blob.

```csharp
public static async Task SetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        Console.WriteLine("Setting blob properties.");

        // You must explicitly set the MIME ContentType every time
        // the properties are updated or the field will be cleared.
        blob.Properties.ContentType = "text/plain";
        blob.Properties.ContentLanguage = "en-us";

        // Set the blob's properties.
        await blob.SetPropertiesAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

Aby pobrać właściwości obiektu blob, wywołać `FetchAttributes` lub `FetchAttributesAsync` metody na `Properties` obiekcie blob, aby wypełnić właściwość. Poniższy przykład kodu pobiera właściwości systemu obiektu blob i wyświetla niektóre wartości:

```csharp
private static async Task GetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        // Fetch the blob properties.
        await blob.FetchAttributesAsync();

        // Display some of the blob's property values.
        Console.WriteLine(" ContentLanguage: {0}", blob.Properties.ContentLanguage);
        Console.WriteLine(" ContentType: {0}", blob.Properties.ContentType);
        Console.WriteLine(" Created: {0}", blob.Properties.Created);
        Console.WriteLine(" LastModified: {0}", blob.Properties.LastModified);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

## <a name="set-and-retrieve-metadata"></a>Ustawianie i pobieranie metadanych

Metadane można określić jako jedną lub więcej par nazwa-wartość w zasób obiektu blob lub kontenera. Aby ustawić metadane, dodaj pary `Metadata` nazwa wartość do kolekcji na zasób. Następnie należy wywołać jedną z następujących metod, aby zapisać wartości:

- [SetMetadata (SetMetadata)](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataSync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)

Pary nazw/wartości metadanych są prawidłowymi nagłówkami HTTP i powinny być zgodne ze wszystkimi ograniczeniami dotyczącymi nagłówków HTTP. Nazwy metadanych muszą być prawidłowymi nazwami nagłówków HTTP i prawidłowymi identyfikatorami języka C#, mogą zawierać tylko znaki ASCII i powinny być traktowane jako niewrażliwe na wielkości liter. Wartości metadanych kodowane przez [base64](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string) lub [kodowane przez adres URL](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode) zawierające znaki inne niż ASCII.

Nazwa metadanych musi być zgodna z konwencjami nazewnictwa identyfikatorów języka C#. Nazwy metadanych zachowują przypadek używany podczas ich tworzenia, ale są niewrażliwe na argumenty podczas ustawiania lub odczytu. Jeśli dwa lub więcej nagłówków metadanych o tej samej nazwie są przesyłane dla zasobu, usługa Azure Blob storage zwraca kod błędu HTTP 400 (Złe żądanie).

Poniższy przykład kodu ustawia metadane na obiekcie blob. Jedna wartość jest ustawiana `Add` przy użyciu metody kolekcji. Druga wartość jest ustawiana przy użyciu składni klucza/wartości niejawnej.

```csharp
public static async Task AddBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Add metadata to the blob by calling the Add method.
        blob.Metadata.Add("docType", "textDocuments");

        // Add metadata to the blob by using key/value syntax.
        blob.Metadata["category"] = "guidance";

        // Set the blob's metadata.
        await blob.SetMetadataAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

Aby pobrać metadane, `FetchAttributes` `FetchAttributesAsync` wywołać lub metody na obiekcie `Metadata` blob lub kontenera, aby wypełnić kolekcję, a następnie przeczytaj wartości, jak pokazano w poniższym przykładzie.

```csharp
public static async Task ReadBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Fetch blob attributes in order to populate 
        // the blob's properties and metadata.
        await blob.FetchAttributesAsync();

        Console.WriteLine("Blob metadata:");

        // Enumerate the blob's metadata.
        foreach (var metadataItem in blob.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Zobacz też

- [Ustaw operację Właściwości obiektu blob](/rest/api/storageservices/set-blob-properties)
- [Pobierz operację Właściwości obiektu blob](/rest/api/storageservices/get-blob-properties)
- [Ustawianie operacji metadanych obiektów blob](/rest/api/storageservices/set-blob-metadata)
- [Pobierz operację metadanych obiektów blob](/rest/api/storageservices/get-blob-metadata)
