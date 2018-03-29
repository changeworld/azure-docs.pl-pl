---
title: Rozpoczynanie pracy z obiektu blob magazynu i Visual Studio połączone usługi (platformy ASP.NET Core) | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę przy użyciu magazynu obiektów Blob platformy Azure w projekcie programu Visual Studio platformy ASP.NET Core, po utworzeniu konta magazynu przy użyciu programu Visual Studio połączone usługi
services: storage
documentationcenter: ''
author: kraigb
manager: ghogen
editor: ''
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: kraigb
ms.openlocfilehash: e3814533b955d5b6444692a7b565219d28002262
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Wprowadzenie do obiektów Blob platformy Azure magazynu i Visual Studio połączone usługi (platformy ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

W tym artykule opisano, jak rozpocząć pracę przy użyciu magazynu obiektów Blob platformy Azure w programie Visual Studio po utworzony lub odwołanie do konta magazynu Azure w projekcie platformy ASP.NET Core za pomocą programu Visual Studio **usług połączonych** funkcji. **Usług połączonych** operacji instaluje odpowiednie pakiety NuGet dostęp do magazynu Azure do projektu i dodaje ten ciąg połączenia dla konta magazynu do plików konfiguracji projektu. (Zobacz [dokumentacji magazynu](https://azure.microsoft.com/documentation/services/storage/) ogólne informacje na temat usługi Azure Storage.)

Magazyn obiektów Blob Azure to usługa do przechowywania dużych ilości danych bez struktury, którego mogą uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Pojedynczego obiektu blob może być dowolnym rozmiarze. Obiekty BLOB można np. obrazów, plików audio i wideo, nieprzetworzone dane i pliki dokumentów. W tym artykule opisano, jak rozpocząć pracę z magazynu obiektów blob, po utworzeniu konta magazynu platformy Azure przy użyciu programu Visual Studio **usług połączonych** w projekcie platformy ASP.NET Core.

Tak samo, jak żywe plików w folderach, na żywo magazynu obiektów blob w kontenerach. Po utworzeniu obiektu blob tworzenia kontenerach tego obiektu blob. Na przykład w obiekcie blob o nazwie "Pamiętnik", można tworzyć kontenery o nazwie "obrazy" do przechowywania obrazów i innej o nazwie "audio" do przechowywania plików audio. Po utworzeniu kontenerów, możesz przekazać do nich poszczególnych plików. Zobacz [Szybki Start: przekazywanie, pobieranie i listę obiektów blob przy użyciu platformy .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md) uzyskać więcej informacji o programowo manipulowanie obiektów blob.

Niektóre z interfejsów API usługi magazynu Azure są asynchroniczne, a kod w tym artykule przyjęto założenie, że metody asynchroniczne są używane. Zobacz [programowanie asynchroniczne](https://docs.microsoft.com/dotnet/csharp/async) Aby uzyskać więcej informacji.

## <a name="access-blob-containers-in-code"></a>Dostęp do kontenerów obiektów blob w kodzie

Do uzyskania programowego dostępu do obiektów blob w projektów platformy ASP.NET Core, należy dodać następujący kod, jeśli nie znajduje się już:

1. Dodaj niezbędne `using` instrukcji:

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Pobierz `CloudStorageAccount` obiekt, który reprezentuje informacje o koncie magazynu. Można pobrać parametry połączenia magazynu, a informacje o koncie magazynu z konfiguracji usługi platformy Azure, należy użyć poniższego kodu:

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. Użyj `CloudBlobClient` obiekt, aby pobrać `CloudBlobContainer` odwołanie do istniejącego kontenera na koncie magazynu:

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>Tworzenie kontenera w kodzie

Można również użyć `CloudBlobClient` do utworzenia kontenera na koncie magazynu przez wywołanie metody `CreateIfNotExistsAsync`:

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

Aby udostępnić pliki w kontenerze dla wszystkich użytkowników, należy ustawić kontener jako publiczny:

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>Przekazywanie obiektu blob do kontenera

Aby przekazać plik obiektu blob do kontenera, Pobierz odwołanie do kontenera i użyj go, aby pobrać odwołanie do obiektu blob. Następnie przekaż dowolny strumień danych do tego odwołania, wywołując `UploadFromStreamAsync` metody. Ta operacja tworzy obiektu blob, jeśli nie jest już istnieje i zastępuje istniejący obiekt blob. 

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

Aby wyświetlić listę obiektów blob w kontenerze, następnie wywołaj pierwszy get odwołanie do kontenera jego `ListBlobsSegmentedAsync` metodę, aby pobrać obiekty BLOB i/lub zawarte w nim katalogi. Aby dostęp do bogatego zestawu właściwości i metod zwróconego `IListBlobItem`, rzutować go na `CloudBlockBlob`, `CloudPageBlob`, lub `CloudBlobDirectory` obiektu. Jeśli nie znasz typu obiektu blob, zastosować Sprawdzanie typu, aby określić, które można rzutować na.

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

Zobacz [Szybki Start: przekazywanie, pobieranie i listę obiektów blob przy użyciu platformy .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container) na inne sposoby wyświetlania zawartości kontenera obiektów blob.

## <a name="download-a-blob"></a>Pobieranie obiektu blob

Aby pobrać obiekt blob, pierwszy get odwołaniem do obiektu blob, następnie wywołaj `DownloadToStreamAsync` metody. W poniższym przykładzie użyto `DownloadToStreamAsync` metodę, aby przesłać zawartość obiektu blob do obiektu strumienia, który można zapisać jako plik lokalny.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

Zobacz [Szybki Start: przekazywanie, pobieranie i listę obiektów blob przy użyciu platformy .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs) na inne sposoby zapisywania obiektów blob jako plików.

## <a name="delete-a-blob"></a>Usuwanie obiektu blob

Aby usunąć obiekt blob, pierwszy get odwołaniem do obiektu blob, następnie wywołaj `DeleteAsync` metody:

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>Kolejne kroki

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
