---
title: Zarządzanie publicznym dostępem do odczytu kontenerów i obiektów blob
titleSuffix: Azure Storage
description: Dowiedz się, jak udostępnić kontenery i obiekty BLOB dla dostępu anonimowego i jak uzyskać do nich programowy dostęp.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 4d9a54c220861b19d67b07998e609ee72897446a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255485"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Zarządzanie dostępem anonimowym w trybie odczytu do kontenerów i obiektów blob

Możesz włączyć anonimowy, publiczny dostęp do odczytu do kontenera i jego obiektów blob w usłudze Azure Blob Storage. W ten sposób możesz przyznać dostęp tylko do odczytu do tych zasobów bez udostępniania klucza konta i konieczności wymagania sygnatury dostępu współdzielonego.

Publiczny dostęp do odczytu jest najlepszy dla scenariuszy, w których chcesz, aby niektóre obiekty blob były zawsze dostępne dla anonimowego dostępu do odczytu. Aby uzyskać bardziej szczegółową kontrolę, można utworzyć podpis dostępu współdzielonego. Podpisy dostępu współdzielonego umożliwiają zapewnienie ograniczonego dostępu przy użyciu różnych uprawnień przez określony czas. Aby uzyskać więcej informacji na temat tworzenia podpisów dostępu współdzielonego, zobacz [Korzystanie z sygnatur dostępu współdzielonego (SAS) w usłudze Azure Storage](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Udzielanie użytkownikom anonimowym uprawnień do kontenerów i obiektów blob

Domyślnie kontener i wszystkie jego obiekty blob mogą być dostępne tylko dla użytkownika, który otrzymał odpowiednie uprawnienia. Aby przyznać anonimowym użytkownikom prawa dostępu do odczytu do kontenera i jego obiektów blob, można ustawić poziom dostępu publicznego do kontenera. Po przyznaniu praw dostępu publicznego do kontenera użytkownicy anonimowi mogą odczytywać obiekty blob w ramach dostępnego publicznie kontenera bez autoryzowania żądania.

Kontener można skonfigurować z następującymi uprawnieniami:

- **Brak publicznego dostępu do odczytu:** Kontener i jego obiekty blob są dostępne tylko przez właściciela konta magazynu. Jest to wartość domyślna dla wszystkich nowych kontenerów.
- **Publiczny dostęp do odczytu tylko dla obiektów blob:** Obiekty BLOB w kontenerze mogą być odczytywane przez żądanie anonimowe, ale dane kontenera nie są dostępne. Klienci anonimowi nie mogą wyliczyć obiektów blob w kontenerze.
- **Publiczny dostęp do odczytu kontenera i jego obiektów blob:** Wszystkie dane kontenera i obiektu blob można odczytać za pomocą anonimowego żądania. Klienci mogą wyliczać obiekty BLOB w kontenerze według żądania anonimowego, ale nie można wyliczyć kontenerów w ramach konta magazynu.

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Ustawianie poziomu dostępu publicznego kontenera w witrynie Azure portal

Z [witryny Azure portal](https://portal.azure.com)można zaktualizować poziom dostępu publicznego dla jednego lub więcej kontenerów:

1. Przejdź do przeglądu konta magazynu w witrynie Azure portal.
1. W obszarze **Usługa obiektów Blob** na bloku menu wybierz pozycję **Blobs**.
1. Wybierz kontenery, dla których chcesz ustawić poziom dostępu publicznego.
1. Użyj przycisku **Zmień poziom dostępu,** aby wyświetlić ustawienia dostępu publicznego.
1. Wybierz żądany poziom dostępu publicznego z listy rozwijanej **Poziom dostępu publicznego** i kliknij przycisk OK, aby zastosować zmianę do wybranych kontenerów.

Poniższy zrzut ekranu pokazuje, jak zmienić poziom dostępu publicznego dla wybranych kontenerów.

![Zrzut ekranu przedstawiający ustawianie poziomu dostępu publicznego w portalu](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> Nie można zmienić poziomu dostępu publicznego dla pojedynczego obiektu blob. Poziom dostępu publicznego jest ustawiony tylko na poziomie kontenera.

### <a name="set-container-public-access-level-with-net"></a>Ustawianie poziomu dostępu publicznego kontenera za pomocą platformy .NET

Aby ustawić uprawnienia dla kontenera przy użyciu biblioteki klienta usługi Azure Storage dla platformy .NET, najpierw pobierz istniejące uprawnienia kontenera, wywołując jedną z następujących metod:

- [Getpermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissions)
- [GetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissionsasync)

Następnie ustaw **właściwość PublicAccess** na obiekcie [BlobContainerPermissions,](/dotnet/api/microsoft.azure.storage.blob.blobcontainerpermissions) który jest zwracany przez metodę **GetPermissions.**

Na koniec wywołać jedną z następujących metod, aby zaktualizować uprawnienia kontenera:

- [Setpermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

W poniższym przykładzie ustawia uprawnienia kontenera do pełnego publicznego dostępu do odczytu. Aby ustawić uprawnienia do publicznego dostępu do odczytu tylko dla obiektów blob, ustaw właściwość **PublicAccess** na **BlobContainerPublicAccessType.Blob**. Aby usunąć wszystkie uprawnienia dla użytkowników anonimowych, ustaw właściwość na **BlobContainerPublicAccessType.Off**.

```csharp
private static async Task SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    await container.SetPermissionsAsync(permissions);

    Console.WriteLine("Container {0} - permissions set to {1}", container.Name, permissions.PublicAccess);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Anonimowy dostęp do kontenerów i obiektów blob

Klient, który uzyskuje dostęp do kontenerów i obiektów blob anonimowo można użyć konstruktorów, które nie wymagają poświadczeń. Poniższe przykłady pokazują kilka różnych sposobów odwoływania się do kontenerów i obiektów blob anonimowo.

### <a name="create-an-anonymous-client-object"></a>Tworzenie anonimowego obiektu klienta

Można utworzyć nowy obiekt klienta usługi dla dostępu anonimowego, podając punkt końcowy magazynu obiektów Blob dla konta. Jednak należy również znać nazwę kontenera na tym koncie, który jest dostępny dla dostępu anonimowego.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint for your account.
    CloudBlobClient blobClient = new CloudBlobClient(
        new Uri(@"https://storagesamples.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. 
    // Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

### <a name="reference-a-container-anonymously"></a>Anonimowy odwołów się do kontenera

Jeśli masz adres URL do kontenera, który jest anonimowo dostępny, można go użyć do odwołania się bezpośrednio do kontenera.

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    // Note this is only possible when the container supports full public read access.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

### <a name="reference-a-blob-anonymously"></a>Anonimowo odwoływanie się do obiektu blob

Jeśli masz adres URL obiektu blob, który jest dostępny dla dostępu anonimowego, możesz odwoływać się do obiektu blob bezpośrednio przy użyciu tego adresu URL:

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

## <a name="next-steps"></a>Następne kroki

- [Autoryzowanie dostępu do usługi Azure Storage](../common/storage-auth.md)
- [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage przy użyciu sygnatur dostępu współdzielonego (SAS)](../common/storage-sas-overview.md)
- [Interfejs API REST usługi Blob Service](/rest/api/storageservices/blob-service-rest-api)
