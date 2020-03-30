---
title: Używanie platformy .NET do zarządzania właściwościami i metadanymi kontenera obiektów blob
titleSuffix: Azure Storage
description: Dowiedz się, jak ustawić i pobrać właściwości systemu i przechowywać niestandardowe metadane w kontenerach obiektów blob na koncie usługi Azure Storage przy użyciu biblioteki klienta platformy .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: c66b521b5cd75825fcafe07b24d5d527c45f5153
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135925"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>Zarządzanie właściwościami kontenera i metadanymi za pomocą platformy .NET

Kontenery obiektów blob obsługują właściwości systemu i metadane zdefiniowane przez użytkownika, oprócz danych, które zawierają. W tym artykule pokazano, jak zarządzać właściwościami systemu i metadanymi zdefiniowanymi przez użytkownika za pomocą [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-properties-and-metadata"></a>Właściwości i metadane — informacje

- **Właściwości systemu:** Właściwości systemu istnieją dla każdego zasobu magazynu obiektów Blob. Niektóre z nich można odczytać lub ustawić, podczas gdy inne są tylko do odczytu. W obszarze osłon niektóre właściwości systemu odpowiadają niektórym standardowym nagłówkom HTTP. Biblioteka klienta usługi Azure Storage dla platformy .NET przechowuje te właściwości.

- **Metadane zdefiniowane przez użytkownika:** Metadane zdefiniowane przez użytkownika składają się z jednej lub kilku par nazwa-wartość, które można określić dla zasobu magazynu obiektów Blob. Metadane służy do przechowywania dodatkowych wartości z zasobem. Wartości metadanych są tylko do własnych celów i nie wpływają na zachowanie zasobu.

Pobieranie wartości właściwości i metadanych dla zasobu magazynu obiektów Blob jest procesem dwuetapowym. Przed odczytywaniem tych wartości, należy jawnie pobrać je wywołując **FetchAttributes** lub **FetchAttributesAsync** metody. Wyjątek od tej **reguły** jest, że exists i **existsAsync** metody wywołać odpowiednie **FetchAttributes** metody w ramach obejmuje. Po wywołaniu jednej z tych metod, nie trzeba również wywoływać **FetchAttributes**.

> [!IMPORTANT]
> Jeśli okaże się, że wartości właściwości lub metadanych dla zasobu magazynu nie zostały wypełnione, sprawdź, czy kod wywołuje **FetchAttributes** lub **FetchAttributesAsync** metody.

Pary nazw metadanych/wartości są prawidłowymi nagłówkami HTTP, dlatego powinny być zgodne ze wszystkimi ograniczeniami dotyczącymi nagłówków HTTP. Nazwy metadanych muszą być prawidłowymi nazwami nagłówków HTTP i prawidłowymi identyfikatorami języka C#, mogą zawierać tylko znaki ASCII i powinny być traktowane jako niewrażliwe na wielkości liter. Wartości metadanych zawierające znaki inne niż ASCII powinny być zakodowane w u podstaw 64 lub zakodowane w adresie URL.

## <a name="retrieve-container-properties"></a>Pobieranie właściwości kontenera

Aby pobrać właściwości kontenera, należy wywołać jedną z następujących metod:

- [FetchAttributes (ŚciąganiePrzywijanie)](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [FetchAttributesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

Poniższy przykład kodu pobiera właściwości systemu kontenera i zapisuje niektóre wartości właściwości w oknie konsoli:

```csharp
private static async Task ReadContainerPropertiesAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch some container properties and write out their values.
        await container.FetchAttributesAsync();
        Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri);
        Console.WriteLine("Public access level: {0}", container.Properties.PublicAccess);
        Console.WriteLine("Last modified time in UTC: {0}", container.Properties.LastModified);
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

Metadane można określić jako jedną lub więcej par nazwa-wartość w zasób obiektu blob lub kontenera. Aby ustawić metadane, dodaj pary nazwa-wartość do kolekcji **metadanych** w zasobie, a następnie wyzwłaj jedną z następujących metod, aby zapisać wartości:

- [SetMetadata (SetMetadata)](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [SetMetadataSync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

Nazwa metadanych musi być zgodna z konwencjami nazewnictwa identyfikatorów języka C#. Nazwy metadanych zachowują sprawę, z którą zostały utworzone, ale są niewrażliwe na argumenty podczas ustawiania lub odczytywania. Jeśli dwa lub więcej nagłówków metadanych o tej samej nazwie są przesyłane do zasobu, magazyn obiektów Blob zwraca kod błędu HTTP 400 (Złe żądanie).

Poniższy przykład kodu ustawia metadane w kontenerze. Jedna wartość jest ustawiona przy użyciu kolekcji **Add** metody. Druga wartość jest ustawiana przy użyciu składni klucza/wartości niejawnej. Oba są prawidłowe.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        // Set the container's metadata.
        await container.SetMetadataAsync();
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

Aby pobrać metadane, **wywołać FetchAttributes** lub **FetchAttributesAsync** metody na obiekt blob lub kontenera, aby wypełnić kolekcji **metadanych,** a następnie odczytać wartości, jak pokazano w poniższym przykładzie.

```csharp
public static async Task ReadContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch container attributes in order to populate the container's properties and metadata.
        await container.FetchAttributesAsync();

        // Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
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

- [Pobierz operację Właściwości kontenera](/rest/api/storageservices/get-container-properties)
- [Ustawianie operacji metadanych kontenera](/rest/api/storageservices/set-container-metadata)
- [Pobierz operację metadanych kontenera](/rest/api/storageservices/set-container-metadata)
