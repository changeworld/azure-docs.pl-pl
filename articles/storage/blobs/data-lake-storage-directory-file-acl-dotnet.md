---
title: Zestaw SDK usługi Azure Data Lake Storage Gen2 .NET dla plików & list ACL
description: Biblioteka klienta usługi Azure Storage służy do zarządzania katalogami oraz listami kontroli dostępu do plików i katalogów (ACL) na kontach magazynu z włączoną hierarchiczną przestrzenią nazw (HNS).
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: b83d0d2d765b60585832f1a3e7c610f05eac075c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061579"
---
# <a name="use-net-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Zarządzanie katalogami, plikami i listami ALs w usłudze Azure Data Lake Storage Gen2 za pomocą platformy .NET

W tym artykule pokazano, jak używać platformy .NET do tworzenia katalogów, plików i uprawnień oraz zarządzania nimi na kontach magazynu z włączoną hierarchiczną przestrzenią nazw (HNS). 

[Pakiet (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) | [Przykłady](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [odwołania interfejsu API](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake) | [Gen1 do mapowania](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | Gen2[Give Feedback](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Konto magazynu z włączoną hierarchiczną przestrzenią nazw (HNS). Postępuj zgodnie z [tymi](data-lake-storage-quickstart-create-account.md) instrukcjami, aby je utworzyć.

## <a name="set-up-your-project"></a>konfigurowanie projektu

Aby rozpocząć, należy zainstalować pakiet [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet.

Aby uzyskać więcej informacji dotyczących instalowania pakietów NuGet, zobacz [Instalowanie pakietów w programie Visual Studio i zarządzanie nimi przy użyciu Menedżera pakietów NuGet](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio).

Następnie dodaj je przy użyciu instrukcji do górnej części pliku kodu.

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>Łączenie się z kontem

Aby użyć urywków w tym artykule, należy utworzyć [datalakeServiceClient wystąpienie,](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) które reprezentuje konto magazynu. 

### <a name="connect-by-using-an-account-key"></a>Łączenie się przy użyciu klucza konta

Jest to najprostszy sposób na połączenie się z kontem. 

W tym przykładzie tworzy [DataLakeServiceClient wystąpienie](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) przy użyciu klucza konta.

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

### <a name="connect-by-using-azure-active-directory-ad"></a>Połącz się przy użyciu usługi Azure Active Directory (AD)

Do uwierzytelniania aplikacji za pomocą usługi Azure AD można użyć [biblioteki klienta tożsamości platformy Azure dla platformy .NET.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)

W tym przykładzie tworzy [DataLakeServiceClient wystąpienie](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) przy użyciu identyfikatora klienta, klucz tajny klienta i identyfikator dzierżawy.  Aby uzyskać te wartości, zobacz [Uzyskiwanie tokenu z usługi Azure AD do autoryzowania żądań z aplikacji klienckiej.](../common/storage-auth-aad-app.md)

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

```

> [!NOTE]
> Aby uzyskać więcej przykładów, zobacz biblioteki klienta tożsamości platformy Azure dla dokumentacji platformy .NET.For more examples, see the Azure identity client library for .NET documentation.For more examples, see the [Azure identity client library for .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) documentation.For more

## <a name="create-a-file-system"></a>Tworzenie systemu plików

System plików działa jako kontener dla plików. Można go utworzyć, wywołując [metodę DataLakeServiceClient.CreateFileSystem.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync)

W tym przykładzie `my-file-system`tworzy system plików o nazwie . 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>Tworzenie katalogu

Utwórz odwołanie do katalogu, wywołując [metodę DataLakeFileSystemClient.CreateDirectoryAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync)

W tym przykładzie `my-directory` dodaje katalog o nazwie do systemu plików, `my-subdirectory`a następnie dodaje podkatasty o nazwie . 

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

Zmień nazwę lub przenieś katalog, wywołując [metodę DataLakeDirectoryClient.RenameAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) Przekaż ścieżkę żądanego katalogu parametrem. 

W tym przykładzie zmienia nazwę podkata directory na nazwę `my-subdirectory-renamed`.

```cs
public async Task<DataLakeDirectoryClient> 
    RenameDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory");

    return await directoryClient.RenameAsync("my-directory/my-subdirectory-renamed");
}
```

W tym przykładzie `my-subdirectory-renamed` przenosi katalog o nazwie do podkatasii katalogu o nazwie `my-directory-2`. 

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

Usuń katalog, wywołując [metodę DataLakeDirectoryClient.Delete.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete)

W tym przykładzie usuwa `my-directory`katalog o nazwie .  

```cs
public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    directoryClient.Delete();
}
```

## <a name="manage-a-directory-acl"></a>Zarządzanie listy ACL katalogu

Pobierz listę kontroli dostępu (ACL) katalogu, wywołując [metodę DataLakeDirectoryClientClient.GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) i ustaw listę ACL, wywołując metodę [DataLakeDirectoryClientClient.SetAccessControlList.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist)

> [!NOTE]
> Jeśli aplikacja autoryzuje dostęp przy użyciu usługi Azure Active Directory (Azure AD), upewnij się, że podmiot zabezpieczeń używany przez aplikację do autoryzowania dostępu został przypisany [do roli Właściciel danych obiektu Blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Aby dowiedzieć się więcej o sposobie stosowania uprawnień listy ACL i skutkach ich zmiany, zobacz [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

W tym przykładzie pobiera i ustawia `my-directory`listy ACL katalogu o nazwie . Ciąg `user::rwx,group::r-x,other::rw-` daje użytkownikowi właścicielem uprawnienia do odczytu, zapisu i wykonywania, daje grupie posiadającej tylko uprawnienia do odczytu i wykonywania i daje wszystkim innym uprawnienia do odczytu i zapisu.

```cs
public async Task ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        await directoryClient.GetAccessControlAsync();

    Console.WriteLine(directoryAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    directoryClient.SetAccessControlList(accessControlList);

}

```

## <a name="upload-a-file-to-a-directory"></a>Przekazywanie pliku do katalogu

Najpierw utwórz odwołanie do pliku w katalogu docelowym, tworząc wystąpienie klasy [DataLakeFileClient.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) Przekaż plik, wywołując [metodę DataLakeFileClient.AppendAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) Upewnij się, aby zakończyć przekazywanie przez wywołanie [DataLakeFileClient.FlushAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync) metody.

W tym przykładzie plik tekstowy `my-directory`jest przesyłany do katalogu o nazwie .    

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

> [!TIP]
> Jeśli rozmiar pliku jest duży, kod będzie musiał wykonać wiele wywołań do [DataLakeFileClient.AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync). Należy rozważyć użycie [DataLakeFileClient.UploadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) metody zamiast. W ten sposób można przekazać cały plik w jednym wywołaniu. 
>
> Zobacz następną sekcję, aby uzyskać przykład.

## <a name="upload-a-large-file-to-a-directory"></a>Przekazywanie dużego pliku do katalogu

Użyj [DataLakeFileClient.UploadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) metody przekazywania dużych plików bez konieczności wykonywania wielu wywołań do [DataLakeFileClient.AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) metody.

```cs
public async Task UploadFileBulk(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.GetFileClient("uploaded-file.txt");

    FileStream fileStream =
        File.OpenRead("C:\\file-to-upload.txt");

    await fileClient.UploadAsync(fileStream);

}

```

## <a name="manage-a-file-acl"></a>Zarządzanie plikem ACL

Pobierz listę kontroli dostępu (ACL) pliku, wywołując [metodę DataLakeFileClient.GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) i ustaw listę ACL, wywołując metodę [DataLakeFileClient.SetAccessControlList.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist)

> [!NOTE]
> Jeśli aplikacja autoryzuje dostęp przy użyciu usługi Azure Active Directory (Azure AD), upewnij się, że podmiot zabezpieczeń używany przez aplikację do autoryzowania dostępu został przypisany [do roli Właściciel danych obiektu Blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Aby dowiedzieć się więcej o sposobie stosowania uprawnień listy ACL i skutkach ich zmiany, zobacz [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

W tym przykładzie pobiera i ustawia `my-file.txt`ACL pliku o nazwie . Ciąg `user::rwx,group::r-x,other::rw-` daje użytkownikowi właścicielem uprawnienia do odczytu, zapisu i wykonywania, daje grupie posiadającej tylko uprawnienia do odczytu i wykonywania i daje wszystkim innym uprawnienia do odczytu i zapisu.

```cs
public async Task ManageFileACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("hello.txt");

    PathAccessControl FileAccessControl =
        await fileClient.GetAccessControlAsync();

    Console.WriteLine(FileAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    fileClient.SetAccessControlList(accessControlList);
}
```

## <a name="download-from-a-directory"></a>Pobieranie z katalogu 

Najpierw utwórz [wystąpienie DataLakeFileClient,](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) które reprezentuje plik, który chcesz pobrać. Użyj [DataLakeFileClient.ReadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync) metody i przeanalizować wartość zwracaną, aby uzyskać [Stream](https://docs.microsoft.com/dotnet/api/system.io.stream) obiektu. Użyj dowolnego interfejsu API przetwarzania plików .NET, aby zapisać bajty ze strumienia do pliku. 

W tym przykładzie użyto [BinaryReader](https://docs.microsoft.com/dotnet/api/system.io.binaryreader) i [FileStream](https://docs.microsoft.com/dotnet/api/system.io.filestream) do zapisywania bajtów w pliku. 

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

Lista zawartości katalogu przez wywołanie [FileSystemClient.GetPathsAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) metody, a następnie wyliczanie za pomocą wyników.

W tym przykładzie można wydrukować nazwy każdego pliku `my-directory`znajdującego się w katalogu o nazwie .

```cs
public async Task ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient)
{
    IAsyncEnumerator<PathItem> enumerator = 
        fileSystemClient.GetPathsAsync("my-directory").GetAsyncEnumerator();

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

## <a name="see-also"></a>Zobacz też

* [Dokumentacja referencyjna interfejsu API](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake)
* [Pakiet (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
* [Próbki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Mapowanie gen1 do gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [Znane problemy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Przekaż opinię](https://github.com/Azure/azure-sdk-for-net/issues)

