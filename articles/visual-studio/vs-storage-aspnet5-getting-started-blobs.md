---
title: Rozpoczynanie pracy z usługą blob storage i Visual Studio podłączone usługi (systemu Windows platformy ASP.NET Core) | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę, przy użyciu usługi Azure Blob storage w projekcie programu Visual Studio ASP.NET Core, po utworzeniu konta magazynu przy użyciu usług połączonych programu Visual Studio
services: storage
author: ghogen
manager: douge
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: 388c4d5f28e87f5cfe26336771d30fa44c6f9ef0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62123012"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Rozpoczynanie pracy z usługą Azure Blob storage i Visual Studio podłączone usługi (systemu Windows platformy ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

W tym artykule opisano sposób rozpoczęcia pracy przy użyciu usługi Azure Blob storage w programie Visual Studio po użytkownik utworzył, lub odwołanie do konta usługi Azure storage w projektach programu ASP.NET Core przy użyciu programu Visual Studio **podłączone usługi** funkcji. **Podłączone usługi** operacji instaluje odpowiednie pakiety NuGet dostępu do magazynu platformy Azure w swoim projekcie i dodaje parametry połączenia dla konta magazynu do plików konfiguracji projektu. (Zobacz [dokumentacja usługi Storage](https://azure.microsoft.com/documentation/services/storage/) ogólne informacje o usłudze Azure Storage.)

Usługa Azure Blob storage jest usługą służącą do przechowywania dużych ilości danych bez struktury, które są dostępne z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Pojedynczy obiekt blob może być dowolnego rozmiaru. Obiekty BLOB mogą mieć elementów, takich jak obrazy, pliki audio i wideo, nieprzetworzone dane i pliki dokumentów. W tym artykule opisano sposób rozpoczęcia pracy z usługą blob storage, po utworzeniu konta usługi Azure storage za pomocą programu Visual Studio **podłączone usługi** w projektach programu ASP.NET Core.

Tak samo, jak żywe plików w folderach, obiekty BLOB magazynu na żywo w kontenerach. Po utworzeniu obiektu blob należy utworzyć co najmniej jeden kontener, w tym obiektów blob. Na przykład w obiekcie blob o nazwie "Pamiętnik" możesz tworzyć kontenery wywoływane "obrazy", do przechowywania obrazów i innym o nazwie "audio" do przechowywania plików dźwiękowych. Po utworzeniu kontenerów, możesz przekazać pojedynczych plików do nich. Zobacz [Szybki start: Przekazywanie, pobieranie i wyświetlanie listy obiektów blob przy użyciu platformy .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md) więcej informacji na temat programowe operowanie obiektów blob.

Niektóre z interfejsów API usługi Azure Storage są asynchroniczne i kodu w tym artykule przyjęto założenie, że metody asynchroniczne są używane. Zobacz [programowania asynchronicznego](https://docs.microsoft.com/dotnet/csharp/async) Aby uzyskać więcej informacji.

## <a name="access-blob-containers-in-code"></a>Dostęp do kontenerów obiektów blob w kodzie

Aby uzyskać programowy dostęp do obiektów blob w projektach programu ASP.NET Core, musisz Dodaj następujący kod, jeśli nie znajduje się już:

1. Dodaj niezbędne `using` instrukcji:

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Pobierz `CloudStorageAccount` obiekt, który reprezentuje dane konta magazynu. Użyj poniższego kodu, można pobrać z parametrów połączenia magazynu i informacje o koncie magazynu z konfiguracji usługi platformy Azure:

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. Użyj `CloudBlobClient` można uzyskać `CloudBlobContainer` odwołanie do istniejącego kontenera na koncie magazynu:

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>Utwórz kontener w kodzie

Można również użyć `CloudBlobClient` do utworzenia kontenera na koncie magazynu przez wywołanie metody `CreateIfNotExistsAsync`:

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

Aby udostępnić pliki znajdujące się w kontenerze dla wszystkich użytkowników, należy ustawić kontener jako publiczny:

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>Przekazywanie obiektu blob do kontenera

Aby przekazać plik obiektów blob w kontenerze, Pobierz odwołanie do kontenera, a następnie użyj, aby uzyskać odwołanie do obiektu blob. Następnie przekaż dowolny strumień danych do tego odwołania, wywołując `UploadFromStreamAsync` metody. Ta operacja tworzy obiekt blob, jeśli nie jest jeszcze określony i zastępuje istniejący obiekt blob. 

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

Aby wyświetlić listę obiektów blob w kontenerze, następnie wywołać pierwszy Pobierz odwołanie do kontenera, jego `ListBlobsSegmentedAsync` metodę, aby pobrać obiekty BLOB i/lub zawarte w nim katalogi. Aby uzyskać dostęp z bogatego zestawu właściwości i metod zwróconego do `IListBlobItem`, rzutować go na `CloudBlockBlob`, `CloudPageBlob`, lub `CloudBlobDirectory` obiektu. Jeśli nie znasz typ obiektu blob, należy zastosować Sprawdzanie typu, aby określić, do którego obiektu rzutować.

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

Zobacz [Szybki start: Przekazywanie, pobieranie i wyświetlanie listy obiektów blob przy użyciu platformy .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container) inne sposoby wyświetlania zawartości określonego kontenera obiektów blob.

## <a name="download-a-blob"></a>Pobieranie obiektu blob

Aby pobrać obiekt blob, Uzyskaj pierwszego odwołania do obiektu blob, następnie wywołać `DownloadToStreamAsync` metody. W poniższym przykładzie użyto `DownloadToStreamAsync` metodę, aby przesłać zawartość obiektu blob do obiektu strumienia, który można zapisać jako plik lokalny.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

Zobacz [Szybki start: Przekazywanie, pobieranie i wyświetlanie listy obiektów blob przy użyciu platformy .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs) inne sposoby zapisywanie obiektów blob jako plików.

## <a name="delete-a-blob"></a>Usuwanie obiektu blob

Aby usunąć obiekt blob, Uzyskaj pierwszego odwołania do obiektu blob, następnie wywołać `DeleteAsync` metody:

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>Kolejne kroki

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
