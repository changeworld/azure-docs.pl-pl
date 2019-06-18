---
title: Ustawianie i pobieranie właściwości obiektów oraz metadane w usłudze Azure Storage | Dokumentacja firmy Microsoft
description: Store niestandardowych metadanych na obiektach w usłudze Azure Storage i ustawianie i pobieranie właściwości systemu.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: tamram
ms.openlocfilehash: e8a85319a12f04a11e3914716d9ff84cdb6de8d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65787859"
---
# <a name="set-and-retrieve-properties-and-metadata"></a>Ustawianie i pobieranie właściwości oraz metadanych

Obiekty właściwości systemu pomocy technicznej usługi Azure Storage i metadane zdefiniowane przez użytkownika, oprócz danych, które zawierają. W tym artykule omówiono zarządzania właściwości systemu i metadane zdefiniowane przez użytkownika za pomocą [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage/client).

* **Właściwości systemu**: Właściwości systemu istnieje dla każdego zasobu magazynu. Niektóre z nich można je odczytać lub ustawić, podczas gdy inne dotyczą tylko do odczytu. Dzieje się w tle niektóre właściwości systemu odpowiadają pewnych standardowych nagłówków HTTP. Biblioteki klienta usługi Azure Storage utrzymywać tych właściwości.

* **Metadane zdefiniowane przez użytkownika**: Metadane zdefiniowane przez użytkownika składa się z pary nazwa wartość, które określisz dla zasobu usługi Azure Storage. Metadane służy do przechowywania dodatkowych wartości z zasobem. Wartości metadanych są wyłącznie w celach własne, a nie wpływają na sposób działania tego zasobu.

Podczas pobierania wartości właściwości i metadanych dla zasobu magazynu jest procesem dwuetapowym. Przed przeczytaniem tych wartości, musisz je wyraźnie pobrać wywołując **FetchAttributes** lub **FetchAttributesAsync** metody. Wyjątkiem jest, jeśli wywołujesz **Exists** lub **ExistsAsync** metody do zasobu. Podczas wywoływania jednej z następujących metod usługi Azure Storage, wywołuje odpowiednią **FetchAttributes** metody w sposób niewidoczny jako część wywołania **Exists** metody.

> [!IMPORTANT]
> Jeśli okaże się, że wartości właściwości lub metadanych dla zasobu magazynu nie została wypełniona, sprawdź, czy kod wywołuje **FetchAttributes** lub **FetchAttributesAsync** metody.
>
> Pary nazwa/wartość metadanych są prawidłowe nagłówków HTTP i dlatego powinien spełniać wszystkie ograniczenia dotyczące nagłówków HTTP. Nazwy metadanych muszą być prawidłowe nazwy nagłówków HTTP i prawidłowy C# identyfikatorów może zawierać tylko znaki ASCII i powinny być traktowane jako bez uwzględniania wielkości liter. Wartości metadanych zawierające znaki spoza zestawu ASCII powinien być kodowany w formacie Base64 lub zakodowane w adresie URL.

## <a name="setting-and-retrieving-properties"></a>Ustawianie i pobieranie właściwości
Aby pobrać wartości właściwości, należy wywołać **FetchAttributesAsync** metody na obiekt blob lub kontenera, aby wypełnić właściwości, następnie zapoznaj się z wartości.

Aby ustawić właściwości dla obiektu, należy określić właściwość wartość, a następnie wywołaj **setproperties —** metody.

Poniższy przykład kodu tworzy kontener, a następnie zapisuje niektóre wartości właściwości z okna konsoli.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

//Create the service client object for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Create the container if it does not already exist.
container.CreateIfNotExists();

// Fetch container properties and write out their values.
await container.FetchAttributesAsync();
Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
Console.WriteLine("ETag: {0}", container.Properties.ETag);
Console.WriteLine();
```

## <a name="setting-and-retrieving-metadata"></a>Ustawianie i pobieranie metadanych
Jako jeden lub więcej par nazwa wartość do obiektu blob lub kontenera zasobu można określić metadanych. Aby ustawić metadanych, należy dodać pary nazwa wartość do **metadanych** kolekcji na zasób, następnie wywołać **SetMetadata** lub **SetMetadataAsync** metodę, aby zapisać wartości do Usługa.

> [!NOTE]
> Nazwa metadanych musi być zgodna z konwencji nazewnictwa identyfikatorów języka C#.
>
>

Poniższy przykład kodu Ustawia metadane w kontenerze. Jedna wartość można ustawić przy użyciu kolekcji **Dodaj** metody. Inna wartość jest ustawiona, przy użyciu składni niejawny klucz/wartość. Oba są prawidłowe.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    // Add some metadata to the container.
    container.Metadata.Add("docType", "textDocuments");
    container.Metadata["category"] = "guidance";

    // Set the container's metadata.
    await container.SetMetadataAsync();
}
```

Aby pobrać metadane, należy wywołać **FetchAttributes** lub **FetchAttributesAsync** metody na obiekt blob lub kontenera, aby wypełnić **metadanych** kolekcji, następnie zapoznaj się z wartości, jak pokazano w poniższym przykładzie.

```csharp
public static async Task ListContainerMetadataAsync(CloudBlobContainer container)
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
```

## <a name="next-steps"></a>Kolejne kroki
* [Biblioteka klienta usługi Azure Storage dla dokumentacja platformy .NET](/dotnet/api/?term=Microsoft.Azure.Storage)
* [Usługa Azure Blob biblioteki klienta usługi storage dla pakietu .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)
* [Usługa Azure Queue biblioteki klienta usługi storage dla pakietu .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)
* [Usługa Azure pliku biblioteki klienta usługi storage dla pakietu .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)

