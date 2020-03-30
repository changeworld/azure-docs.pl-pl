---
title: Używanie języka JavaScript dla plików & list ACl w usłudze Azure Data Lake Storage Gen2
description: Użyj biblioteki klienta usługi Azure Storage Data Lake dla języka JavaScript do zarządzania katalogami oraz listami kontroli dostępu do plików i katalogów (ACL) na kontach magazynu z włączoną hierarchiczną przestrzenią nazw (HNS).
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 04d0d23bdbdaeda6a4823c900badb3133ba9eeae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061536"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Używanie języka JavaScript do zarządzania katalogami, plikami i listami ACl w usłudze Azure Data Lake Storage Gen2

W tym artykule pokazano, jak używać języka JavaScript do tworzenia katalogów, plików i uprawnień na kontach magazynu z włączoną hierarchiczną przestrzenią nazw (HNS) i zarządzanie nimi. 

[Przykłady pakietu (Menedżera pakietów](https://www.npmjs.com/package/@azure/storage-file-datalake) | [węzłów)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | [podają opinię](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Konto magazynu z włączoną hierarchiczną przestrzenią nazw (HNS). Postępuj zgodnie z [tymi](data-lake-storage-quickstart-create-account.md) instrukcjami, aby je utworzyć.
> * Jeśli używasz tego pakietu w aplikacji Node.js, musisz Node.js 8.0.0 lub nowszej.

## <a name="set-up-your-project"></a>konfigurowanie projektu

Zainstaluj bibliotekę klienta usługi Data Lake dla języka JavaScript, otwierając okno terminala, a następnie wpisując następujące polecenie.

```javascript
npm install @azure/storage-file-datalake
```

Zaimportuj pakiet, `storage-file-datalake` umieszczając tę instrukcję u góry pliku kodu. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Łączenie się z kontem 

Aby użyć urywków w tym artykule, należy utworzyć **datalakeServiceClient wystąpienie,** które reprezentuje konto magazynu. 

### <a name="connect-by-using-an-account-key"></a>Łączenie się przy użyciu klucza konta

Jest to najprostszy sposób na połączenie się z kontem. 

W tym przykładzie tworzy **DataLakeServiceClient wystąpienie** przy użyciu klucza konta.

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential = 
     new StorageSharedKeyCredential(accountName, accountKey);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;             
}      

```
> [!NOTE]
> Ta metoda autoryzacji działa tylko dla aplikacji Node.js. Jeśli planujesz uruchomić kod w przeglądarce, możesz autoryzować za pomocą usługi Azure Active Directory (AD). 

### <a name="connect-by-using-azure-active-directory-ad"></a>Połącz się przy użyciu usługi Azure Active Directory (AD)

Można użyć [biblioteki klienta tożsamości platformy Azure dla JS](https://www.npmjs.com/package/@azure/identity) do uwierzytelniania aplikacji za pomocą usługi Azure AD.

W tym przykładzie tworzy **DataLakeServiceClient wystąpienie** przy użyciu identyfikatora klienta, klucz tajny klienta i identyfikator dzierżawy.  Aby uzyskać te wartości, zobacz [Uzyskiwanie tokenu z usługi Azure AD do autoryzowania żądań z aplikacji klienckiej.](../common/storage-auth-aad-app.md)

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> Aby uzyskać więcej przykładów, zobacz bibliotekę klienta tożsamości platformy Azure dla dokumentacji [JS.](https://www.npmjs.com/package/@azure/identity)

## <a name="create-a-file-system"></a>Tworzenie systemu plików

System plików działa jako kontener dla plików. Można go utworzyć, uzyskując **wystąpienie FileSystemClient,** a następnie wywołując **metodę FileSystemClient.Create.**

W tym przykładzie `my-file-system`tworzy system plików o nazwie . 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>Tworzenie katalogu

Utwórz odwołanie do katalogu, uzyskując **wystąpienie DirectoryClient,** a następnie wywołując metodę **DirectoryClient.create.**

W tym przykładzie `my-directory` dodaje katalog o nazwie do systemu plików. 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>Zmienianie nazwy lub przenoszenie katalogu

Zmień nazwę lub przenieś katalog, wywołując **metodę DirectoryClient.rename.** Przekaż ścieżkę żądanego katalogu parametrem. 

W tym przykładzie zmienia nazwę podkata directory na nazwę `my-directory-renamed`.

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

W tym przykładzie `my-directory-renamed` przenosi katalog o nazwie do podkatasii katalogu o nazwie `my-directory-2`. 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>Usuwanie katalogu

Usuń katalog, wywołując **metodę DirectoryClient.delete.**

W tym przykładzie usuwa `my-directory`katalog o nazwie .   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="manage-a-directory-acl"></a>Zarządzanie listy ACL katalogu

W tym przykładzie pobiera, a następnie `my-directory`ustawia ACL katalogu o nazwie . W tym przykładzie daje użytkownikowi właścicielem uprawnienia do odczytu, zapisu i wykonywania, daje grupie właściciel tylko uprawnienia do odczytu i wykonywania i daje wszystkim innym dostęp do odczytu.

> [!NOTE]
> Jeśli aplikacja autoryzuje dostęp przy użyciu usługi Azure Active Directory (Azure AD), upewnij się, że podmiot zabezpieczeń używany przez aplikację do autoryzowania dostępu został przypisany [do roli Właściciel danych obiektu Blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Aby dowiedzieć się więcej o sposobie stosowania uprawnień listy ACL i skutkach ich zmiany, zobacz [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

## <a name="upload-a-file-to-a-directory"></a>Przekazywanie pliku do katalogu

Najpierw przeczytaj plik. W tym przykładzie użyto `fs` node.js modułu. Następnie utwórz odwołanie do pliku w katalogu docelowym, tworząc **wystąpienie FileClient,** a następnie wywołując metodę **FileClient.create.** Przekaż plik, wywołując **Metodę FileClient.append.** Upewnij się, aby zakończyć przekazywanie przez wywołanie **FileClient.flush** metody.

W tym przykładzie plik tekstowy `my-directory`jest przesyłany do katalogu o nazwie .'

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs') 

  var content = "";
  
  fs.readFile('mytestfile.txt', (err, data) => { 
      if (err) throw err; 

      content = data.toString();

  }) 
  
  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="manage-a-file-acl"></a>Zarządzanie plikem ACL

W tym przykładzie pobiera, a następnie `upload-file.txt`ustawia ACL pliku o nazwie . W tym przykładzie daje użytkownikowi właścicielem uprawnienia do odczytu, zapisu i wykonywania, daje grupie właściciel tylko uprawnienia do odczytu i wykonywania i daje wszystkim innym dostęp do odczytu.

> [!NOTE]
> Jeśli aplikacja autoryzuje dostęp przy użyciu usługi Azure Active Directory (Azure AD), upewnij się, że podmiot zabezpieczeń używany przez aplikację do autoryzowania dostępu został przypisany [do roli Właściciel danych obiektu Blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Aby dowiedzieć się więcej o sposobie stosowania uprawnień listy ACL i skutkach ich zmiany, zobacz [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt"); 
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);        
}
```

## <a name="download-from-a-directory"></a>Pobieranie z katalogu

Najpierw utwórz **wystąpienie FileSystemClient,** które reprezentuje plik, który chcesz pobrać. Użyj **metody FileSystemClient.read,** aby odczytać plik. Następnie napisz plik. W tym przykładzie użyto `fs` node.js moduł, aby to zrobić. 

> [!NOTE]
> Ta metoda pobierania pliku działa tylko w przypadku aplikacji Node.js. Jeśli planujesz uruchomić kod w przeglądarce, zobacz bibliotekę klienta usługi Azure Storage File Data Lake dla pliku readme [JavaScript,](https://www.npmjs.com/package/@azure/storage-file-datalake) aby uzyskać przykład, jak to zrobić w przeglądarce. 

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);
 
  async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", (data) => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
  }   
  
  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>Wyświetlanie zawartości katalogu

W tym przykładzie można wydrukować nazwy każdego katalogu i pliku `my-directory`znajdującego się w katalogu o nazwie .

```javascript
async function ListFilesInDirectory(fileSystemClient) {
  
  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {
    
    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="see-also"></a>Zobacz też

* [Pakiet (Menedżer pakietów węzłów)](https://www.npmjs.com/package/@azure/storage-file-datalake)
* [Próbki](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [Przekaż opinię](https://github.com/Azure/azure-sdk-for-java/issues)