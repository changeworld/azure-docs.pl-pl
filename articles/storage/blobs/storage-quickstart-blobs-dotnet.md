---
title: 'Szybki start: biblioteka magazynu obiektów Blob platformy Azure w wersji 12 — .NET'
description: W tym przewodniku Szybki start dowiesz się, jak utworzyć kontener i obiektowy magazyn za pomocą biblioteki klienta magazynu obiektów Blob platformy Azure w wersji 12 dla platformy .NET. Następnie dowiesz się, jak pobrać obiekt blob na komputer lokalny i jak wyświetlać listę wszystkich obiektów blob w kontenerze.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 5cfb0430bc94d347afd75bc01170a71a7ad53565
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240506"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-net"></a>Szybki start: biblioteka klienta magazynu obiektów Blob platformy Azure w wersji 12 dla platformy .NET

Wprowadzenie do biblioteki klienta magazynu obiektów Blob platformy Azure w wersji 12 dla platformy .NET. Azure Blob Storage to rozwiązanie do magazynowania obiektów w chmurze firmy Microsoft. Wykonaj kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Usługa Blob Storage jest zoptymalizowana pod kątem przechowywania olbrzymich ilości danych bez struktury.

> [!NOTE]
> Aby rozpocząć pracę z poprzednią wersją SDK, zobacz [Szybki start: Biblioteka klienta magazynu obiektów Blob platformy Azure dla platformy .NET](storage-quickstart-blobs-dotnet-legacy.md).

Użyj biblioteki klienta magazynu obiektów Blob platformy Azure w wersji 12 dla platformy .NET, aby:

* Tworzenie kontenera
* Przekazywanie obiektu blob do usługi Azure Storage
* Lista wszystkich obiektów blob w kontenerze
* Pobieranie obiektu blob na komputer lokalny
* Usuwanie kontenera

[Dokumentacja](/dotnet/api/azure.storage.blobs) | dokumentacji interfejsu API[Przykłady pakietu kodu źródłowego](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs) | (NuGet) kod źródłowy[(Biblioteka](https://www.nuget.org/packages/Azure.Storage.Blobs) | [api)](https://docs.microsoft.com/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/)
* Konto magazynu platformy Azure — [tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Bieżący [sdk .NET Core](https://dotnet.microsoft.com/download/dotnet-core) dla systemu operacyjnego. Pamiętaj, aby uzyskać SDK, a nie środowisko uruchomieniowe.

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji przeprowadzi Cię proces przygotowania projektu do pracy z biblioteką klienta magazynu obiektów Blob platformy Azure w wersji 12 dla platformy .NET.

### <a name="create-the-project"></a>Tworzenie projektu

Utwórz aplikację .NET Core o nazwie *BlobQuickstartV12*.

1. W oknie konsoli (na przykład cmd, PowerShell `dotnet new` lub Bash) użyj polecenia, aby utworzyć nową aplikację konsoli o nazwie *BlobQuickstartV12*. To polecenie tworzy prosty projekt "Hello World" C# z jednym plikiem źródłowym: *Program.cs*.

   ```console
   dotnet new console -n BlobQuickstartV12
   ```

1. Przełącz się do nowo utworzonego katalogu *BlobQuickstartV12.*

   ```console
   cd BlobQuickstartV12
   ```

1. W katalogu *BlobQuickstartV12* należy utworzyć inny katalog o nazwie *data*. W tym miejscu zostaną utworzone i przechowywane pliki danych obiektów blob.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Zainstaluj pakiet

Będąc jeszcze w katalogu aplikacji, zainstaluj bibliotekę klienta magazynu obiektów Blob platformy Azure dla pakietu .NET za pomocą `dotnet add package` polecenia.

```console
dotnet add package Azure.Storage.Blobs
```

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu projektu:

1. Otwieranie pliku *Program.cs* w edytorze
1. Usuwanie `Console.WriteLine("Hello World!");` instrukcji
1. Dodawanie `using` dyrektyw
1. Aktualizowanie `Main` deklaracji metody do obsługi kodu asynchronii

Oto kod:

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System;
using System.IO;
using System.Threading.Tasks;

namespace BlobQuickstartV12
{
    class Program
    {
        static async Task Main()
        {
        }
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Model obiektu

Usługa Azure Blob storage jest zoptymalizowana pod kątem przechowywania ogromnych ilości nieustrukturyzowanych danych. Dane bez struktury są danymi, które nie są zgodne z żadnym modelem lub definicją danych, jak na przykład dane tekstowe lub binarne. Magazyn obiektów blob oferuje trzy typy zasobów:

* Konto magazynu
* Kontener na koncie magazynu
* Obiekt blob w kontenerze

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury magazynu obiektów blob](./media/storage-blobs-introduction/blob1.png)

Użyj następujących klas platformy .NET, aby wchodzić w interakcje z tymi zasobami:

* [BlobServiceClient:](/dotnet/api/azure.storage.blobs.blobserviceclient) `BlobServiceClient` Klasa umożliwia manipulowanie zasobami usługi Azure Storage i kontenerami obiektów blob.
* [BlobContainerClient:](/dotnet/api/azure.storage.blobs.blobcontainerclient) `BlobContainerClient` Klasa umożliwia manipulowanie kontenerami usługi Azure Storage i ich obiektów blob.
* [BlobClient:](/dotnet/api/azure.storage.blobs.blobclient) `BlobClient` Klasa umożliwia manipulowanie obiektów blob usługi Azure Storage.
* [BlobDownloadInfo:](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo) `BlobDownloadInfo` Klasa reprezentuje właściwości i zawartość zwróconą z pobierania obiektu blob.

## <a name="code-examples"></a>Przykłady kodu

Te przykładowe fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta magazynu obiektów Blob platformy Azure dla platformy .NET:

* [Pobieranie parametrów połączenia](#get-the-connection-string)
* [Tworzenie kontenera](#create-a-container)
* [Przekazywanie obiektów blob do kontenera](#upload-blobs-to-a-container)
* [Wyświetlanie listy obiektów blob w kontenerze](#list-the-blobs-in-a-container)
* [Pobieranie obiektów blob](#download-blobs)
* [Usuwanie kontenera](#delete-a-container)

### <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

Poniższy kod pobiera parametry połączenia dla konta magazynu ze zmiennej środowiskowej utworzonej w sekcji [Konfigurowanie ciągu połączenia magazynu.](#configure-your-storage-connection-string)

Dodaj ten kod `Main` wewnątrz metody:

```csharp
Console.WriteLine("Azure Blob storage v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-container"></a>Tworzenie kontenera

Zdecyduj o nazwie nowego kontenera. Poniższy kod dołącza wartość GUID do nazwy kontenera, aby upewnić się, że jest unikatowa.

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać więcej informacji o nazewnictwie kontenerów i obiektów blob, zobacz temat [Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Utwórz wystąpienie klasy [BlobServiceClient.](/dotnet/api/azure.storage.blobs.blobserviceclient) Następnie [wywołaj CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) metody, aby utworzyć kontener na koncie magazynu.

Dodaj ten kod na `Main` końcu metody:

```csharp
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

//Create a unique name for the container
string containerName = "quickstartblobs" + Guid.NewGuid().ToString();

// Create the container and return a container client object
BlobContainerClient containerClient = await blobServiceClient.CreateBlobContainerAsync(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Przekazywanie obiektów blob do kontenera

Fragment kodu w brzmieniu:

1. Tworzy plik tekstowy w lokalnym katalogu *danych.*
1. Pobiera odwołanie do [obiektu BlobClient,](/dotnet/api/azure.storage.blobs.blobclient) wywołując [GetBlobClient](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobclient) metody w kontenerze z [Create sekcji kontenera.](#create-a-container)
1. Przekazuje lokalny plik tekstowy do obiektu blob, wywołując [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync#Azure_Storage_Blobs_BlobClient_UploadAsync_System_IO_Stream_System_Boolean_System_Threading_CancellationToken_) metody. Jeśli obiekt blob nie istnieje, zostanie utworzony przez metodę. W przeciwnym razie zostanie zastąpiony.

Dodaj ten kod na `Main` końcu metody:

```csharp
// Create a local file in the ./data/ directory for uploading and downloading
string localPath = "./data/";
string fileName = "quickstart" + Guid.NewGuid().ToString() + ".txt";
string localFilePath = Path.Combine(localPath, fileName);

// Write text to the file
await File.WriteAllTextAsync(localFilePath, "Hello, World!");

// Get a reference to a blob
BlobClient blobClient = containerClient.GetBlobClient(fileName);

Console.WriteLine("Uploading to Blob storage as blob:\n\t {0}\n", blobClient.Uri);

// Open the file and upload its data
using FileStream uploadFileStream = File.OpenRead(localFilePath);
await blobClient.UploadAsync(uploadFileStream, true);
uploadFileStream.Close();
```

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Lista obiektów blob w kontenerze, wywołując [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) metody. W takim przypadku tylko jeden obiekt blob został dodany do kontenera, więc operacja listy zwraca tylko ten jeden obiekt blob.

Dodaj ten kod na `Main` końcu metody:

```csharp
Console.WriteLine("Listing blobs...");

// List all blobs in the container
await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
{
    Console.WriteLine("\t" + blobItem.Name);
}
```

### <a name="download-blobs"></a>Pobieranie obiektów blob

Pobierz poprzednio utworzony obiekt blob, wywołując [DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync) metody. Przykładowy kod dodaje sufiks "POBRANE" do nazwy pliku, dzięki czemu można zobaczyć oba pliki w lokalnym systemie plików.

Dodaj ten kod na `Main` końcu metody:

```csharp
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension 
// so you can compare the files in the data directory
string downloadFilePath = localFilePath.Replace(".txt", "DOWNLOAD.txt");

Console.WriteLine("\nDownloading blob to\n\t{0}\n", downloadFilePath);

// Download the blob's contents and save it to a file
BlobDownloadInfo download = await blobClient.DownloadAsync();

using (FileStream downloadFileStream = File.OpenWrite(downloadFilePath))
{
    await download.Content.CopyToAsync(downloadFileStream);
    downloadFileStream.Close();
}
```

### <a name="delete-a-container"></a>Usuwanie kontenera

Poniższy kod czyści zasoby utworzone przez aplikację, usuwając cały kontener za pomocą [deleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync). Usuwa również pliki lokalne utworzone przez aplikację.

Aplikacja wstrzymuje wprowadzanie danych przez użytkownika, wywołując `Console.ReadLine` przed usunięciem obiektów blob, kontenera i plików lokalnych. Jest to dobra okazja, aby sprawdzić, czy zasoby zostały faktycznie utworzone poprawnie, zanim zostaną usunięte.

Dodaj ten kod na `Main` końcu metody:

```csharp
// Clean up
Console.Write("Press any key to begin clean up");
Console.ReadLine();

Console.WriteLine("Deleting blob container...");
await containerClient.DeleteAsync();

Console.WriteLine("Deleting the local source and downloaded files...");
File.Delete(localFilePath);
File.Delete(downloadFilePath);

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>Uruchamianie kodu

Ta aplikacja tworzy plik testowy w lokalnym folderze *danych* i przekazuje go do magazynu obiektów Blob. W przykładzie następnie wyświetla listę obiektów blob w kontenerze i pobiera plik o nowej nazwie, dzięki czemu można porównać stare i nowe pliki.

Przejdź do katalogu aplikacji, a następnie skompiluj i uruchom aplikację.

```console
dotnet build
```

```console
dotnet run
```

Dane wyjściowe aplikacji są podobne do następującego przykładu:

```output
Azure Blob storage v12 - .NET quickstart sample

Uploading to Blob storage as blob:
         https://mystorageacct.blob.core.windows.net/quickstartblobs60c70d78-8d93-43ae-954d-8322058cfd64/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Listing blobs...
        quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Downloading blob to
        ./data/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31DOWNLOADED.txt

Press any key to begin clean up
Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Przed rozpoczęciem procesu oczyszczania sprawdź folder *danych* pod kątem dwóch plików. Możesz je otworzyć i sprawdzić, czy są identyczne.

Po zweryfikowaniu plików naciśnij klawisz **Enter,** aby usunąć pliki testowe i zakończyć prezentację.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano następujące czynności: przekazywanie, pobieranie i wyświetlanie listy obiektów blob przy użyciu platformy .NET.

Aby wyświetlić przykładowe aplikacje magazynu obiektów Blob, przejdź do:

> [!div class="nextstepaction"]
> [Przykłady magazynu obiektów Blob platformy Azure w wersji 12 .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

* Aby uzyskać samouczki, przykłady, szybkie uruchamianie i inną dokumentację, odwiedź [witrynę Azure dla deweloperów platformy .NET i .NET Core](/dotnet/azure/).
* Aby dowiedzieć się więcej na temat platformy .NET Core, zobacz [Get started with .NET in 10 minutes (Rozpoczynanie pracy z platformą .NET w 10 minut)](https://www.microsoft.com/net/learn/get-started/).
