---
title: Korzystanie z platformy Azure .NET dla plików & list ACL w Azure Data Lake Storage Gen2 (wersja zapoznawcza)
description: Biblioteka klienta usługi Azure Storage umożliwia zarządzanie katalogami oraz list kontroli dostępu do plików i katalogów (ACL) na kontach magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS).
author: normesta
ms.service: storage
ms.date: 11/24/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: a756518688b5b1f8b854165de69d3444b772eabc
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931219"
---
# <a name="use-net-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>Korzystanie z platformy .NET dla plików & list ACL w Azure Data Lake Storage Gen2 (wersja zapoznawcza)

W tym artykule przedstawiono sposób korzystania z platformy .NET do tworzenia katalogów, plików i uprawnień w ramach kont magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS), oraz do zarządzania nimi. 

> [!IMPORTANT]
> Pakiet NuGet [Azure. Storage. Files. datalake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/12.0.0-preview.6) , który jest proponowany w tym artykule, jest obecnie w publicznej wersji zapoznawczej.

[Pakiet (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/12.0.0-preview.6) | [przykładów](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [odwołania do interfejsów API](https://azuresdkdocs.blob.core.windows.net/$web/dotnet/Azure.Storage.Files.DataLake/12.0.0-preview.6/api/index.html) | [Mapowanie Gen1 do Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [Przekaż opinię](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Subskrypcja platformy Azure. Zobacz artykuł [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Konto magazynu z włączoną hierarchiczną przestrzenią nazw (SNS). Postępuj zgodnie z [tymi](data-lake-storage-quickstart-create-account.md) instrukcjami, aby je utworzyć.

## <a name="set-up-your-project"></a>konfigurowanie projektu

Aby rozpocząć, zainstaluj pakiet NuGet [Azure. Storage. Files. datalake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) .

Aby uzyskać więcej informacji o instalowaniu pakietów NuGet, zobacz [Instalowanie i zarządzanie pakietami w programie Visual Studio przy użyciu Menedżera pakietów NuGet](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio).

Następnie Dodaj te instrukcje using na początku pliku kodu.

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>Połącz z kontem

Aby użyć fragmentów kodu w tym artykule, należy utworzyć wystąpienie **DataLakeServiceClient** reprezentujące konto magazynu. Najprostszym sposobem na uzyskanie jednego jest użycie klucza konta. 

Ten przykład tworzy wystąpienie **DataLakeServiceClient** przy użyciu klucza konta.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

## <a name="create-a-file-system"></a>Tworzenie systemu plików

System plików działa jako kontener dla plików. Można go utworzyć, wywołując metodę **FileSystemClient. CreateFileSystemAsync** .

Ten przykład tworzy system plików o nazwie `my-file-system`. 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>Tworzenie katalogu

Utwórz odwołanie do katalogu, wywołując metodę **FileSystemClient. CreateDirectoryAsync** .

Ten przykład dodaje katalog o nazwie `my-directory` do systemu plików, a następnie dodaje podkatalog o nazwie `my-subdirectory`. 

```cs
public async Task<DataLakeDirectoryClient> CreateDirectory
    (DataLakeServiceClient serviceClient, string fileSystemName)
{
    DataLakeFileSystemClient fileSystemClient =
        serviceClient.GetFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        await fileSystemClient.CreateDirectoryAsync("my-directory");

    return await directoryClient.CreateSubDirectoryAsync("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>Zmienianie nazwy lub przenoszenie katalogu

Zmień nazwę lub Przenieś katalog, wywołując metodę **DirectoryClient. RenameAsync** . Przekaż ścieżkę do żądanego katalogu jako parametr. 

Ten przykład zmienia nazwę podkatalogu na nazwę `my-subdirectory-renamed`.

```cs
public async Task<DataLakeDirectoryClient> 
    RenameDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory");

    return await directoryClient.RenameAsync("my-directory/my-subdirectory-renamed");
}
```

Ten przykład przenosi katalog o nazwie `my-subdirectory-renamed` do podkatalogu katalogu o nazwie `my-directory-2`. 

```cs
public async Task<DataLakeDirectoryClient> MoveDirectory
    (DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
            fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory-renamed");

    return await directoryClient.RenameAsync("my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>Usuwanie katalogu

Usuń katalog, wywołując metodę **DirectoryClient. Delete** .

Ten przykład usuwa katalog o nazwie `my-directory`.  

```cs
public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    directoryClient.Delete();
}
```

## <a name="manage-a-directory-acl"></a>Zarządzanie listą ACL katalogów

Pobierz listę kontroli dostępu (ACL) katalogu, wywołując metodę **directoryClient. GetAccessControlAsync** i ustawiając listę ACL przez wywołanie metody **directoryClient. SetAccessControl** .

Ten przykład pobiera i ustawia listę ACL katalogu o nazwie `my-directory`. Ciąg `user::rwx,group::r-x,other::rw-` przyznaje użytkownikowi właściciela uprawnienia do odczytu, zapisu i wykonywania, nadaje grupie będącej właścicielem tylko uprawnienia do odczytu i wykonywania oraz daje wszystkim innym uprawnienie do odczytu i zapisu.

```cs
public async Task ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        await directoryClient.GetAccessControlAsync();

    Console.WriteLine(directoryAccessControl.Acl);

    directoryClient.SetAccessControl("user::rwx,group::r-x,other::rw-");

}

```

## <a name="upload-a-file-to-a-directory"></a>Przekaż plik do katalogu

Najpierw Utwórz odwołanie do pliku w katalogu docelowym, tworząc wystąpienie klasy **DataLakeFileClient** . Przekaż plik, wywołując metodę **DataLakeFileClient. AppendAsync** . Upewnij się, że ukończono przekazywanie, wywołując metodę **DataLakeFileClient. FlushAsync** .

Ten przykład przekazuje plik tekstowy do katalogu o nazwie `my-directory`.    

```cs
public async Task UploadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = await directoryClient.CreateFileAsync("uploaded-file.txt");

    FileStream fileStream = 
        File.OpenRead("C:\\file-to-upload.txt");

    long fileSize = fileStream.Length;

    await fileClient.AppendAsync(fileStream, offset: 0);

    await fileClient.FlushAsync(position: fileSize);

}
```

## <a name="manage-a-file-acl"></a>Zarządzanie listą ACL plików

Pobieranie listy kontroli dostępu (ACL) pliku przez wywołanie metody **DataLakeFileClient. GetAccessControlAsync** i ustawienie listy ACL przez wywołanie metody **FileClient. SetAccessControl** .

Ten przykład pobiera i ustawia listę ACL pliku o nazwie `my-file.txt`. Ciąg `user::rwx,group::r-x,other::rw-` przyznaje użytkownikowi właściciela uprawnienia do odczytu, zapisu i wykonywania, nadaje grupie będącej właścicielem tylko uprawnienia do odczytu i wykonywania oraz daje wszystkim innym uprawnienie do odczytu i zapisu.

```cs
public async Task ManageFileACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("hello.txt");

    PathAccessControl FileAccessControl =
        await fileClient.GetAccessControlAsync();

    Console.WriteLine(FileAccessControl.Acl);

    fileClient.SetAccessControl("user::rwx,group::r-x,other::rw-");
}
```

## <a name="download-from-a-directory"></a>Pobierz z katalogu 

Najpierw Utwórz wystąpienie **DataLakeFileClient** reprezentujące plik, który chcesz pobrać. Użyj metody **FileClient. ReadAsync** i Przeanalizuj wartość zwracaną, aby uzyskać obiekt [strumienia](https://docs.microsoft.com/dotnet/api/system.io.stream) . Użyj dowolnego interfejsu API przetwarzania plików platformy .NET, aby zaoszczędzić bajty ze strumienia do pliku. 

Ten przykład używa [BinaryReader](https://docs.microsoft.com/dotnet/api/system.io.binaryreader) i [FILESTREAM](https://docs.microsoft.com/dotnet/api/system.io.filestream) do zapisywania bajtów do pliku. 

```cs
public async Task DownloadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("my-image.png");

    Response<FileDownloadInfo> downloadResponse = await fileClient.ReadAsync();

    BinaryReader reader = new BinaryReader(downloadResponse.Value.Content);

    FileStream fileStream = 
        File.OpenWrite("C:\\my-image-downloaded.png");

    int bufferSize = 4096;

    byte[] buffer = new byte[bufferSize];

    int count;

    while ((count = reader.Read(buffer, 0, buffer.Length)) != 0)
    {
        fileStream.Write(buffer, 0, count);
    }

    await fileStream.FlushAsync();

    fileStream.Close();
}
```

## <a name="list-directory-contents"></a>Wyświetlanie zawartości katalogu

Wyświetlanie zawartości katalogu przez wywołanie metody **FileSystemClient. ListPathsAsync** , a następnie Wyliczenie przez wyniki.

W tym przykładzie program drukuje nazwy poszczególnych plików znajdujących się w katalogu o nazwie `my-directory`.

```cs
public async Task ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient)
{
    IAsyncEnumerator<PathItem> enumerator = 
        fileSystemClient.ListPathsAsync("my-directory").GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    PathItem item = enumerator.Current;

    while (item != null)
    {
        Console.WriteLine(item.Name);

        if (!await enumerator.MoveNextAsync())
        {
            break;
        }
                
        item = enumerator.Current;
    }

}
```

## <a name="see-also"></a>Zobacz także

* [Dokumentacja interfejsu API](https://azuresdkdocs.blob.core.windows.net/$web/dotnet/Azure.Storage.Files.DataLake/12.0.0-preview.6/api/index.html)
* [Pakiet (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/12.0.0-preview.6)
* [Przykłady](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Mapowanie Gen1 do Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [Znane problemy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Przekaż opinię](https://github.com/Azure/azure-sdk-for-net/issues)

