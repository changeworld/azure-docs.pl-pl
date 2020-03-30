---
title: Wprowadzenie do magazynu obiektów blob przy użyciu programu Visual Studio (ASP.NET Core)
description: Jak rozpocząć korzystanie z magazynu obiektów Blob platformy Azure w projekcie programu Visual Studio ASP.NET Core po utworzeniu konta magazynu przy użyciu usług połączonych z programem Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 81df41470c893f569fd17345e8bdf4b29641ec64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298833"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Wprowadzenie do usługi Azure Blob Storage i usług połączonych z programem Visual Studio (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

W tym artykule opisano, jak rozpocząć korzystanie z magazynu obiektów Blob platformy Azure w programie Visual Studio po utworzeniu konta magazynu platformy Azure lub odwoływaniu się do niego w projekcie ASP.NET Core przy użyciu funkcji **Połączonych usług** programu Visual Studio. **Operacja Połączone usługi** instaluje odpowiednie pakiety NuGet, aby uzyskać dostęp do magazynu platformy Azure w projekcie i dodaje parametry połączenia dla konta magazynu do plików konfiguracji projektu. (Zobacz [dokumentację magazynu, aby](https://azure.microsoft.com/documentation/services/storage/) uzyskać ogólne informacje na temat usługi Azure Storage).

Usługa Azure Blob storage to usługa do przechowywania dużych ilości nieustrukturyzowanych danych, do których można uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Pojedynczy obiekt blob może mieć dowolny rozmiar. Obiekty BLOB mogą być takimi jak obrazy, pliki audio i wideo, nieprzetworzone dane i pliki dokumentów. W tym artykule opisano, jak rozpocząć pracę z magazynem obiektów blob po utworzeniu konta magazynu platformy Azure przy użyciu **usług połączonych** programu Visual Studio w projekcie ASP.NET Core.

Podobnie jak pliki w folderach, obiekty blob magazynu na żywo w kontenerach. Po utworzeniu obiektu blob, należy utworzyć jeden lub więcej kontenerów w tym obiekcie blob. Na przykład w obiekcie blob o nazwie "Notatnik" można utworzyć kontenery o nazwie "obrazy" do przechowywania obrazów i inny o nazwie "audio" do przechowywania plików audio. Po utworzeniu kontenerów można przekazać do nich poszczególne pliki. Zobacz [Szybki start: Przekazywanie, pobieranie i listy obiektów blob przy użyciu platformy .NET,](../storage/blobs/storage-quickstart-blobs-dotnet.md) aby uzyskać więcej informacji na temat programowo manipulowania obiektami blob.

Niektóre interfejsy API usługi Azure Storage są asynchroniczne, a kod w tym artykule zakłada, że używane są metody asynchroniczne. Aby uzyskać więcej informacji, zobacz [programowanie asynchroniczne.](https://docs.microsoft.com/dotnet/csharp/async)

## <a name="access-blob-containers-in-code"></a>Dostęp do kontenerów obiektów blob w kodzie

Aby programowo uzyskiwać dostęp do obiektów blob w projektach ASP.NET Core, należy dodać następujący kod, jeśli nie jest jeszcze obecny:

1. Dodaj niezbędne `using` instrukcje:

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Pobierz `CloudStorageAccount` obiekt, który reprezentuje informacje o koncie magazynu. Użyj następującego kodu, aby uzyskać parametry połączenia magazynu i informacje o koncie magazynu z konfiguracji usługi platformy Azure:

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. Użyj `CloudBlobClient` obiektu, aby `CloudBlobContainer` uzyskać odwołanie do istniejącego kontenera na koncie magazynu:

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>Tworzenie kontenera w kodzie

Można również użyć `CloudBlobClient` do utworzenia kontenera na `CreateIfNotExistsAsync`koncie magazynu, dzwoniąc:

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

Aby udostępnić pliki w kontenerze wszystkim, ustaw kontener jako publiczny:

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>Przekazywanie obiektu blob do kontenera

Aby przekazać plik obiektu blob do kontenera, uzyskać odwołanie do kontenera i użyć go, aby uzyskać odwołanie do obiektu blob. Następnie przekaż dowolny strumień danych do `UploadFromStreamAsync` tego odwołania, wywołując metodę. Ta operacja tworzy obiekt blob, jeśli jeszcze go nie ma i zastępuje istniejący obiekt blob. 

```cs
// Get a reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with the contents of a local file
// named "myfile".
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    await blockBlob.UploadFromStreamAsync(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Aby wyświetlić listę obiektów blob w kontenerze, `ListBlobsSegmentedAsync` najpierw uzyskać odwołanie do kontenera, a następnie wywołać jego metodę, aby pobrać obiekty blobs i/lub katalogi w nim. Aby uzyskać dostęp do bogatego zestawu `IListBlobItem`właściwości i metod `CloudBlockBlob` `CloudPageBlob`zwracanego `CloudBlobDirectory` , rzutuj go na , , lub obiekt. Jeśli nie znasz typu obiektu blob, użyj sprawdzania typu, aby określić, do którego rzutowania ma być rzutowanie.

```cs
BlobContinuationToken token = null;
do
{
    BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
    token = resultSegment.ContinuationToken;

    foreach (IListBlobItem item in resultSegment.Results)
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
} while (token != null);
```

Zobacz [Szybki start: Przekazywanie, pobieranie i listy obiektów blob przy użyciu platformy .NET,](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container) aby uzyskać inne sposoby wyświetlania listy zawartości kontenera obiektów blob.

## <a name="download-a-blob"></a>Pobieranie obiektu blob

Aby pobrać obiekt blob, najpierw uzyskać odwołanie do `DownloadToStreamAsync` obiektu blob, a następnie wywołać metodę. W poniższym przykładzie `DownloadToStreamAsync` użyto metody do przeniesienia zawartości obiektu blob do obiektu strumienia, który można następnie zapisać jako plik lokalny.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

Zobacz [Szybki start: Przekazywanie, pobieranie i listy obiektów blob przy użyciu platformy .NET,](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs) aby uzyskać inne sposoby zapisywania obiektów blob jako plików.

## <a name="delete-a-blob"></a>Usuwanie obiektu blob

Aby usunąć obiekt blob, najpierw uzyskać odwołanie do `DeleteAsync` obiektu blob, a następnie wywołać metodę:

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
