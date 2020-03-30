---
title: Tworzenie lub usuwanie kontenera obiektów blob za pomocą platformy .NET — usługa Azure Storage
description: Dowiedz się, jak utworzyć lub usunąć kontener obiektów blob na koncie usługi Azure Storage przy użyciu biblioteki klienta platformy .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: c95ed6dde3c00c0688ccfd58565fd112427c8899
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135942"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>Tworzenie lub usuwanie kontenera w usłudze Azure Storage za pomocą platformy .NET

Obiekty BLOB w usłudze Azure Storage są zorganizowane w kontenery. Przed przekazaniem obiektu blob należy najpierw utworzyć kontener. W tym artykule pokazano, jak tworzyć i usuwać kontenery za pomocą [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="name-a-container"></a>Nadawanie nazwy kontenerowi

Nazwa kontenera musi być prawidłową nazwą DNS, ponieważ stanowi część unikatowego identyfikatora URI używanego do adresu kontenera lub jego obiektów blob. Postępuj zgodnie z tymi regułami podczas nazywania kontenera:

- Nazwy kontenerów mogą mieć długość od 3 do 63 znaków.
- Nazwy kontenerów muszą zaczynać się od litery lub cyfry i mogą zawierać tylko małe litery, cyfry i znak myślnika (-).
- Dwa lub więcej kolejnych znaków kreski nie są dozwolone w nazwach kontenerów.

Identyfikator URI dla kontenera jest w tym formacie:

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>Tworzenie kontenera

Aby utworzyć kontener, należy wywołać jedną z następujących metod:

- [Utwórz](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [Utwórzsynk](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [CreateIfNotExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [UtwórjIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

Metody **Create** i **CreateAsync** zgłaszają wyjątek, jeśli kontener o tej samej nazwie już istnieje.

Metody **CreateIfNotExexists** i **CreateIfNotExistsAsync** zwracają wartość logiczną wskazującą, czy kontener został utworzony. Jeśli kontener o tej samej nazwie już istnieje, te metody zwracają **False,** aby wskazać, że nowy kontener nie został utworzony.

Kontenery są tworzone bezpośrednio pod kontem magazynu. Nie można zagnieżdżać jednego kontenera pod drugim.

Poniższy przykład tworzy kontener asynchronicznie:

```csharp
private static async Task<CloudBlobContainer> CreateSampleContainerAsync(CloudBlobClient blobClient)
{
    // Name the sample container based on new GUID, to ensure uniqueness.
    // The container name must be lowercase.
    string containerName = "container-" + Guid.NewGuid();

    // Get a reference to a sample container.
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Create the container if it does not already exist.
        bool result = await container.CreateIfNotExistsAsync();
        if (result == true)
        {
            Console.WriteLine("Created container {0}", container.Name);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    return container;
}
```

## <a name="create-the-root-container"></a>Tworzenie kontenera głównego

Kontener główny służy jako domyślny kontener dla konta magazynu. Każde konto magazynu może mieć jeden kontener główny, który musi mieć nazwę *$root.*. Kontener główny należy jawnie utworzyć lub usunąć.

Można odwoływać się do obiektu blob przechowywane w kontenerze głównym bez dołączania nazwy kontenera głównego. Kontener główny umożliwia odwoływanie się do obiektu blob na najwyższym poziomie hierarchii kont magazynu. Na przykład można odwoływać się do obiektu blob, który znajduje się w kontenerze głównym w następujący sposób:

`https://myaccount.blob.core.windows.net/default.html`

Poniższy przykład tworzy kontener główny synchronicznie:

```csharp
private static void CreateRootContainer(CloudBlobClient blobClient)
{
    try
    {
        // Create the root container if it does not already exist.
        CloudBlobContainer container = blobClient.GetContainerReference("$root");
        if (container.CreateIfNotExists())
        {
            Console.WriteLine("Created root container.");
        }
        else
        {
            Console.WriteLine("Root container already exists.");
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

## <a name="delete-a-container"></a>Usuwanie kontenera

Aby usunąć kontener w .NET, użyj jednej z następujących metod:

- [Usuwanie](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [UsuńSync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [UsuńIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [UsuńIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)

Metody **Delete** i **DeleteAsync** zgłaszają wyjątek, jeśli kontener nie istnieje.

**Metody DeleteIfExexists** i **DeleteIfExistAsync** zwracają wartość logiczną wskazującą, czy kontener został usunięty. Jeśli określony kontener nie istnieje, te metody zwracają **False,** aby wskazać, że kontener nie został usunięty.

Po usunięciu kontenera nie można utworzyć kontenera o tej samej nazwie przez co najmniej 30 sekund i ewentualnie dłużej. Gdy kontener jest usuwany, próba utworzenia kontenera o tej samej nazwie zakończy się niepowodzeniem przy użyciu kodu błędu HTTP 409 (Konflikt). Wszelkie inne operacje na kontenerze lub obiektów blob, które zawiera zakończy się niepowodzeniem z kodem błędu HTTP 404 (Nie znaleziono), gdy kontener jest usuwany.

Poniższy przykład usuwa określony kontener i obsługuje wyjątek, jeśli kontener nie istnieje:

```csharp
private static async Task DeleteSampleContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Delete the specified container and handle the exception.
        await container.DeleteAsync();
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

W poniższym przykładzie pokazano, jak usunąć wszystkie kontenery, które zaczynają się od określonego prefiksu. Przykład przerwy dzierżawy, jeśli istnieje istniejąca dzierżawa w kontenerze.

```csharp
private static async Task DeleteContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("Delete all containers beginning with the specified prefix");
    try
    {
        foreach (var container in blobClient.ListContainers(prefix))
        {
            Console.WriteLine("\tContainer:" + container.Name);
            if (container.Properties.LeaseState == LeaseState.Leased)
            {
                await container.BreakLeaseAsync(null);
            }

            await container.DeleteAsync();
        }

        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Zobacz też

- [Utwórz operację kontenera](/rest/api/storageservices/create-container)
- [Operacja usuwania kontenera](/rest/api/storageservices/delete-container)
