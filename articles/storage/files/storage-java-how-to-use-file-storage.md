---
title: Tworzenie oprogramowania dla usługi Azure Files za pomocą języka Java | Dokumentacja firmy Microsoft
description: Dowiedz się, jak opracować aplikacje Java i usług korzystających z usługi Azure Files do przechowywania danych plików.
services: storage
author: roygara
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 238e5971e79b192e0ef422dcd452859ff7566580
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721671"
---
# <a name="develop-for-azure-files-with-java"></a>Tworzenie oprogramowania dla usługi Azure Files za pomocą języka Java
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>Informacje o tym samouczku
W tym samouczku przedstawiono podstawy korzystania z języka Java do tworzenia aplikacji lub usług, które używają usługi Azure Files do przechowywania danych plików. W tym samouczku utworzymy aplikację konsolową w języku ją i pokazują, jak wykonywać podstawowe działania za pomocą języka Java i usługi Azure Files:

* Tworzenie i usuwanie udziałów plików platformy Azure
* Tworzenie i usuwanie katalogów
* Wyliczanie plików i katalogów w udziale plików platformy Azure
* Przekazywanie, pobieranie i usuwanie pliku

> [!Note]  
> Ponieważ usługi Azure Files można uzyskać dostęp za pośrednictwem protokołu SMB, istnieje możliwość napisania aplikacji uzyskujących dostęp do udziału plików platformy Azure przy użyciu standardowych klas w języku Java we/wy. W tym artykule opisano sposób pisania aplikacji korzystających z zestawu SDK Java magazynu platformy Azure, w który używa [API REST usługi pliki Azure](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) na komunikowanie się z usługą Azure Files.

## <a name="create-a-java-application"></a>Tworzenie aplikacji Java
Aby utworzyć przykłady, konieczne będzie Java Development Kit (JDK) i [zestawu SDK usługi Azure Storage dla języka Java](https://github.com/Azure/azure-storage-java). Należy również utworzono konto magazynu platformy Azure.

## <a name="set-up-your-application-to-use-azure-files"></a>Konfigurowanie aplikacji do użycia usługi Azure Files
Aby użyć interfejsów API usługi Azure storage, należy dodać następującą instrukcję na początku pliku Java, gdy chcesz uzyskać dostęp do usługi storage z.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurowanie parametrów połączenia usługi Azure storage
Aby korzystać z usługi Azure Files, musisz nawiązać połączenie z kontem usługi Azure storage. Pierwszym krokiem będzie można skonfigurować parametry połączenia, które będą używane do łączenia się z kontem magazynu. Czynnością jest zdefiniowanie zmienną statyczną, aby to zrobić.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> Zastąp your_storage_account_name i your_storage_account_key rzeczywistymi wartościami dla konta magazynu.
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>Łączenie się z kontem usługi Azure storage
Aby połączyć z kontem magazynu, należy użyć **CloudStorageAccount** obiektu, przekazując ciąg połączenia do jego **przeanalizować** metody.

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** zgłasza InvalidKeyException, dlatego należy go umieścić wewnątrz bloku try/catch.

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure
Wszystkie pliki i katalogi w usłudze Azure Files znajdują się w kontenerze o nazwie **udziału**. Konto magazynu może zawierać dowolną liczbę akcji, pojemności konta zezwala na to. Aby uzyskać dostęp do udziału i jego zawartości, należy użyć klienta usługi Azure Files.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Za pomocą klienta usługi Azure Files, możesz następnie Uzyskaj odwołanie do udziału.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Aby utworzyć udział, użyj **createIfNotExists** metody obiektu CloudFileShare.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

W tym momencie **udostępnianie** zawiera odwołanie do udział o nazwie **sampleshare**.

## <a name="delete-an-azure-file-share"></a>Usuwanie udziału plików platformy Azure
Trwa usuwanie udziału odbywa się przez wywołanie metody **deleteIfExists** metody dla obiektu CloudFileShare. Poniżej przedstawiono przykładowy kod, który tak.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the file client.
   CloudFileClient fileClient = storageAccount.createCloudFileClient();

   // Get a reference to the file share
   CloudFileShare share = fileClient.getShareReference("sampleshare");

   if (share.deleteIfExists()) {
       System.out.println("sampleshare deleted");
   }
} catch (Exception e) {
    e.printStackTrace();
}
```

## <a name="create-a-directory"></a>Tworzenie katalogu
Można również zorganizować magazynu przez umieszczenie plików w podkatalogach zamiast ich wszystkich w katalogu głównym. Usługa pliki systemu Azure umożliwia tworzenie katalogów tyle dopuszcza Twoje konto. Poniższy kod utworzy podkatalog o nazwie **sampledir** w katalogu głównym.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

## <a name="delete-a-directory"></a>Usuwanie katalogu
Usunięcie katalogu jest prostym zadaniem, jednak należy zauważyć, że nie można usunąć katalogu, który nadal zawiera pliki lub katalogi innych.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Wyliczanie plików i katalogów w udziale plików platformy Azure
Uzyskiwanie listy plików i katalogów w udziale łatwo odbywa się przez wywołanie metody **listFilesAndDirectories** CloudFileDirectory odwołanie. Metoda zwraca listę obiektów ListFileItem, które można wykonać iterację na. Na przykład poniższy kod wyświetli listę plików i katalogów w katalogu głównym.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Przekazywanie pliku
W tej sekcji dowiesz się, jak można przekazać pliku z magazynu lokalnego do katalogu głównego udziału.

Pierwszym krokiem podczas przekazywania pliku jest Uzyskaj odwołanie do katalogu, w której się znajduje. Można to zrobić, wywołując **getRootDirectoryReference** metody obiektu udziału.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Teraz, gdy odwołanie do katalogu głównego udziału, możesz przekazać plik na go za pomocą następującego kodu.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>Pobieranie pliku
Jednym z dłuższymi operacje, które należy wykonać względem usługi Azure Files jest do pobierania plików. W poniższym przykładzie kodu SampleFile.txt pobiera i wyświetla jego zawartość.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

## <a name="delete-a-file"></a>Usuwanie pliku
Inna operacja usługi Azure Files typowych jest usunięcie pliku. Poniższy kod usuwa plik o nazwie SampleFile.txt przechowywany w katalogu o nazwie **sampledir**.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory where the file to be deleted is in
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

String filename = "SampleFile.txt"
CloudFile file;

file = containerDir.getFileReference(filename)
if ( file.deleteIfExists() ) {
    System.out.println(filename + " was deleted");
}
```

## <a name="next-steps"></a>Kolejne kroki
Jeśli chcesz dowiedzieć się więcej o innych interfejsów API magazynu Azure, skorzystaj z poniższych linków.

* [Platforma Azure dla deweloperów języka Java](/java/azure)/)
* [Usługa Azure Storage SDK dla języka Java](https://github.com/azure/azure-storage-java)
* [Usługa Azure Storage SDK dla systemu Android](https://github.com/azure/azure-storage-android)
* [Dokumentacja zestawu SDK klienta usługi Azure Storage](http://dl.windowsazure.com/storage/javadoc/)
* [Interfejs API REST usług Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog zespołu odpowiedzialnego za usługę Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/)
* [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](../common/storage-use-azcopy.md)
* [Troubleshooting Azure Files problems - Windows (Rozwiązywanie problemów z usługą Azure Files — Windows)](storage-troubleshoot-windows-file-connection-problems.md)
