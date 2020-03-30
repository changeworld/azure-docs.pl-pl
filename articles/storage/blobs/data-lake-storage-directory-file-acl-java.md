---
title: Zestaw Java SDK usługi Azure Data Lake Storage Gen2 dla plików & list ACL
description: Biblioteki usługi Azure Storage dla oprogramowania Java umożliwiają zarządzanie katalogami oraz listami kontroli dostępu do plików i katalogów (ACL) na kontach magazynu z włączoną hierarchiczną przestrzenią nazw (HNS).
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 45870dd7d3035b6b49340fd6e8016794088e775a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061554"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Używanie języka Java do zarządzania katalogami, plikami i listami ACl w usłudze Azure Data Lake Storage Gen2

W tym artykule pokazano, jak używać oprogramowania Java do tworzenia katalogów, plików i uprawnień oraz zarządzania nimi na kontach magazynu z włączoną hierarchiczną przestrzenią nazw (HNS). 

[Pakiet (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) | [Próbki](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | [API odniesienia](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.1/index.html) | [Gen1 do Mapowania](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | Gen2[Give Feedback](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Konto magazynu z włączoną hierarchiczną przestrzenią nazw (HNS). Postępuj zgodnie z [tymi](data-lake-storage-quickstart-create-account.md) instrukcjami, aby je utworzyć.

## <a name="set-up-your-project"></a>konfigurowanie projektu

Aby rozpocząć, otwórz [tę stronę](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) i znajdź najnowszą wersję biblioteki Java. Następnie otwórz plik *pom.xml* w edytorze tekstu. Dodaj element zależności, który odwołuje się do tej wersji.

Jeśli zamierzasz uwierzytelnić aplikację kliencką przy użyciu usługi Azure Active Directory (AD), dodaj zależność do biblioteki klienta tajnego usługi Azure. Zobacz [Dodawanie pakietu tajnej biblioteki klienta do projektu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Następnie dodaj te instrukcje importowania do pliku kodu.

```java
import com.azure.core.credential.TokenCredential;
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.RolePermissions;
```

## <a name="connect-to-the-account"></a>Łączenie się z kontem 

Aby użyć urywków w tym artykule, należy utworzyć **datalakeServiceClient wystąpienie,** które reprezentuje konto magazynu. 

### <a name="connect-by-using-an-account-key"></a>Łączenie się przy użyciu klucza konta

Jest to najprostszy sposób na połączenie się z kontem. 

W tym przykładzie tworzy **DataLakeServiceClient wystąpienie** przy użyciu klucza konta.

```java

static public DataLakeServiceClient GetDataLakeServiceClient
(String accountName, String accountKey){

    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();

    builder.credential(sharedKeyCredential);
    builder.endpoint("https://" + accountName + ".dfs.core.windows.net");

    return builder.buildClient();
}      
```

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Połącz się przy użyciu usługi Azure Active Directory (Azure AD)

Za pomocą [biblioteki klienta tożsamości platformy Azure dla języka Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) można uwierzytelnić aplikację za pomocą usługi Azure AD.

W tym przykładzie tworzy **DataLakeServiceClient wystąpienie** przy użyciu identyfikatora klienta, klucz tajny klienta i identyfikator dzierżawy.  Aby uzyskać te wartości, zobacz [Uzyskiwanie tokenu z usługi Azure AD do autoryzowania żądań z aplikacji klienckiej.](../common/storage-auth-aad-app.md)

```java
static public DataLakeServiceClient GetDataLakeServiceClient
    (String accountName, String clientId, String ClientSecret, String tenantID){

    String endpoint = "https://" + accountName + ".dfs.core.windows.net";
        
    ClientSecretCredential clientSecretCredential = new ClientSecretCredentialBuilder()
    .clientId(clientId)
    .clientSecret(ClientSecret)
    .tenantId(tenantID)
    .build();
           
    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();
    return builder.credential(clientSecretCredential).endpoint(endpoint).buildClient();
 } 
```

> [!NOTE]
> Aby uzyskać więcej przykładów, zobacz [bibliotekę klienta tożsamości platformy Azure dla](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) dokumentacji języka Java.


## <a name="create-a-file-system"></a>Tworzenie systemu plików

System plików działa jako kontener dla plików. Można go utworzyć, wywołując **metodę DataLakeServiceClient.createFileSystem.**

W tym przykładzie `my-file-system`tworzy system plików o nazwie . 

```java
static public DataLakeFileSystemClient CreateFileSystem
(DataLakeServiceClient serviceClient){

    return serviceClient.createFileSystem("my-file-system");
}
```

## <a name="create-a-directory"></a>Tworzenie katalogu

Utwórz odwołanie do katalogu, wywołując **metodę DataLakeFileSystemClient.createDirectory.**

W tym przykładzie `my-directory` dodaje katalog o nazwie do systemu plików, `my-subdirectory`a następnie dodaje podkatasty o nazwie . 

```java
static public DataLakeDirectoryClient CreateDirectory
(DataLakeServiceClient serviceClient, String fileSystemName){
    
    DataLakeFileSystemClient fileSystemClient =
    serviceClient.getFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.createDirectory("my-directory");

    return directoryClient.createSubDirectory("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>Zmienianie nazwy lub przenoszenie katalogu

Zmień nazwę lub przenieś katalog, wywołując **metodę DataLakeDirectoryClientClient.rename.** Przekaż ścieżkę żądanego katalogu parametrem. 

W tym przykładzie zmienia nazwę podkata directory na nazwę `my-subdirectory-renamed`.

```java
static public DataLakeDirectoryClient
    RenameDirectory(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-subdirectory-renamed");
}
```

W tym przykładzie `my-subdirectory-renamed` przenosi katalog o nazwie do podkatasii katalogu o nazwie `my-directory-2`. 

```java
static public DataLakeDirectoryClient MoveDirectory
(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory-renamed");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>Usuwanie katalogu

Usuń katalog, wywołując **metodę DataLakeDirectoryClient.deleteWithResponse.**

W tym przykładzie usuwa `my-directory`katalog o nazwie .   

```java
static public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient){
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    directoryClient.deleteWithResponse(true, null, null, null);
}
```

## <a name="manage-a-directory-acl"></a>Zarządzanie listy ACL katalogu

W tym przykładzie pobiera, a następnie `my-directory`ustawia ACL katalogu o nazwie . W tym przykładzie daje użytkownikowi właścicielem uprawnienia do odczytu, zapisu i wykonywania, daje grupie właściciel tylko uprawnienia do odczytu i wykonywania i daje wszystkim innym dostęp do odczytu.

> [!NOTE]
> Jeśli aplikacja autoryzuje dostęp przy użyciu usługi Azure Active Directory (Azure AD), upewnij się, że podmiot zabezpieczeń używany przez aplikację do autoryzowania dostępu został przypisany [do roli Właściciel danych obiektu Blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Aby dowiedzieć się więcej o sposobie stosowania uprawnień listy ACL i skutkach ich zmiany, zobacz [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

```java
static public void ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        directoryClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = directoryAccessControl.getAccessControlList();
       
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
             
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);
  
    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);
  
    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);
  
    PathPermissions permissions = new PathPermissions();
  
    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    directoryClient.setPermissions(permissions, null, null);

    pathPermissions = directoryClient.getAccessControl().getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}

```

## <a name="upload-a-file-to-a-directory"></a>Przekazywanie pliku do katalogu

Najpierw utwórz odwołanie do pliku w katalogu docelowym, tworząc wystąpienie klasy **DataLakeFileClient.** Przekaż plik, wywołując **metodę DataLakeFileClient.append.** Upewnij się, aby zakończyć przekazywanie przez wywołanie **DataLakeFileClient.FlushAsync** metody.

W tym przykładzie plik tekstowy `my-directory`jest przesyłany do katalogu o nazwie .'

```java
static public void UploadFile(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.createFile("uploaded-file.txt");

    File file = new File("C:\\mytestfile.txt");

    InputStream targetStream = new FileInputStream(file);

    long fileSize = file.length();

    fileClient.append(targetStream, 0, fileSize);

    fileClient.flush(fileSize);
}
```

> [!TIP]
> Jeśli rozmiar pliku jest duży, kod będzie musiał wykonać wiele wywołań **do Metody DataLakeFileClient.append.** Należy rozważyć użycie **DataLakeFileClient.uploadFromFile** metody zamiast. W ten sposób można przekazać cały plik w jednym wywołaniu. 
>
> Zobacz następną sekcję, aby uzyskać przykład.

## <a name="upload-a-large-file-to-a-directory"></a>Przekazywanie dużego pliku do katalogu

Użyj **DataLakeFileClient.uploadFromFile** metody przekazywania dużych plików bez konieczności wykonywania wielu wywołań do **DataLakeFileClient.append** metody.

```java
static public void UploadFileBulk(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.getFileClient("uploaded-file.txt");

    fileClient.uploadFromFile("C:\\mytestfile.txt");

    }

```


## <a name="manage-a-file-acl"></a>Zarządzanie plikem ACL

W tym przykładzie pobiera, a następnie `upload-file.txt`ustawia ACL pliku o nazwie . W tym przykładzie daje użytkownikowi właścicielem uprawnienia do odczytu, zapisu i wykonywania, daje grupie właściciel tylko uprawnienia do odczytu i wykonywania i daje wszystkim innym dostęp do odczytu.

> [!NOTE]
> Jeśli aplikacja autoryzuje dostęp przy użyciu usługi Azure Active Directory (Azure AD), upewnij się, że podmiot zabezpieczeń używany przez aplikację do autoryzowania dostępu został przypisany [do roli Właściciel danych obiektu Blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Aby dowiedzieć się więcej o sposobie stosowania uprawnień listy ACL i skutkach ich zmiany, zobacz [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

```java
static public void ManageFileACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    PathAccessControl fileAccessControl =
        fileClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = fileAccessControl.getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
           
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);

    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);

    PathPermissions permissions = new PathPermissions();

    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    fileClient.setPermissions(permissions, null, null);

    pathPermissions = fileClient.getAccessControl().getAccessControlList();
   
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}
```

## <a name="download-from-a-directory"></a>Pobieranie z katalogu

Najpierw utwórz **wystąpienie DataLakeFileClient,** które reprezentuje plik, który chcesz pobrać. Użyj **Metody DataLakeFileClient.read,** aby odczytać plik. Użyj dowolnego interfejsu API przetwarzania plików .NET, aby zapisać bajty ze strumienia do pliku. 

```java
static public void DownloadFile(DataLakeFileSystemClient fileSystemClient)
    throws FileNotFoundException, java.io.IOException{

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    File file = new File("C:\\downloadedFile.txt");

    OutputStream targetStream = new FileOutputStream(file);

    fileClient.read(targetStream);

    targetStream.close();
      
}

```

## <a name="list-directory-contents"></a>Wyświetlanie zawartości katalogu

W tym przykładzie można wydrukować nazwy każdego pliku `my-directory`znajdującego się w katalogu o nazwie .

```java
static public void ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient){
        
    ListPathsOptions options = new ListPathsOptions();
    options.setPath("my-directory");
     
    PagedIterable<PathItem> pagedIterable = 
    fileSystemClient.listPaths(options, null);

    java.util.Iterator<PathItem> iterator = pagedIterable.iterator();
       
    PathItem item = iterator.next();

    while (item != null)
    {
        System.out.println(item.getName());


        if (!iterator.hasNext())
        {
            break;
        }
            
        item = iterator.next();
    }

}
```

## <a name="see-also"></a>Zobacz też

* [Dokumentacja referencyjna interfejsu API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.1/index.html)
* [Pakiet (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [Próbki](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Mapowanie gen1 do gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Znane problemy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Przekaż opinię](https://github.com/Azure/azure-sdk-for-java/issues)