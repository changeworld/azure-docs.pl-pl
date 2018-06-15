---
title: Tworzenie plików platformy Azure z językiem Java | Dokumentacja firmy Microsoft
description: Informacje o opracowywaniu aplikacji Java i usług, które korzystają z plików Azure do przechowywania plików danych.
services: storage
documentationcenter: java
author: wmgries
manager: aungoo
editor: tamram
ms.assetid: 3bfbfa7f-d378-4fb4-8df3-e0b6fcea5b27
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: a9585bc77a73cbd84fb2efa201a5745c62f3360a
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738204"
---
# <a name="develop-for-azure-files-with-java"></a>Tworzenie plików platformy Azure z językiem Java
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>Informacje o tym samouczku
W tym samouczku przedstawiono podstawy do tworzenia aplikacji lub usługi, które korzystają z plików Azure do przechowywania danych plików za pomocą języka Java. W tym samouczku utworzymy aplikację konsoli ją i pokazują, jak wykonywać podstawowe działania z języka Java i plików platformy Azure:

* Tworzenie i usuwanie udziałów plików na platformę Azure
* Tworzenie i usuwanie katalogów
* Wyliczanie plików i katalogów w udziale plików na platformę Azure
* Przekazywanie, pobieranie i usuwanie pliku

> [!Note]  
> Ponieważ pliki Azure mogą uzyskiwać dostęp za pośrednictwem protokołu SMB, istnieje możliwość pisać aplikacje, które uzyskują dostęp do udziału plików na platformę Azure przy użyciu standardowych klas Java we/wy. W tym artykule opisano sposób pisania aplikacji, które używają SDK Java magazynu Azure, która używa [interfejsu API REST plików Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/file-service-rest-api) do komunikowania się do usługi pliki Azure.

## <a name="create-a-java-application"></a>Tworzenie aplikacji Java
Aby utworzyć próbek, konieczne będzie Java Development Kit (JDK) i [Azure Storage SDK for Java](https://github.com/Azure/azure-storage-java). Należy również utworzono konto magazynu platformy Azure.

## <a name="set-up-your-application-to-use-azure-files"></a>Konfigurowanie aplikacji do korzystania z plików Azure
Aby używać interfejsów API magazynu Azure, dodaj następującą instrukcję na początku pliku języka Java, gdy chcesz uzyskać dostęp do usługi magazynu z.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurowanie parametrów połączenia usługi Azure storage
Aby używać plików Azure, należy do łączenia się z kontem magazynu platformy Azure. Pierwszym krokiem jest skonfigurowanie parametrów połączenia, które będą używane do łączenia się z kontem magazynu. Umożliwia zdefiniowanie zmienną statyczną, w tym celu.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> Zamień your_storage_account_name i your_storage_account_key rzeczywiste wartości dla konta magazynu.
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>Łączenie z kontem magazynu platformy Azure
Do łączenia się z kontem magazynu, należy użyć **CloudStorageAccount** parametry połączenia do przekazania obiektu jego **przeanalizować** metody.

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** InvalidKeyException zgłasza wyjątek, dlatego należy go umieścić wewnątrz bloku try/catch.

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure
Wszystkich plików i katalogów w plikach Azure znajdują się w kontenerze o nazwie **udziału**. Twoje konto magazynu może mieć dowolną liczbę akcji zezwala pojemności Twojego konta. Aby uzyskać dostęp do udziału i jego zawartość, musisz użyć klienta usługi pliki Azure.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Za pomocą klienta usługi pliki Azure można następnie uzyskać odwołania do udziału.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Aby rzeczywiście utworzyć udział, użyj **createIfNotExists** metody CloudFileShare obiektu.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

W tym momencie **udostępnianie** zawiera odwołanie do udziału o nazwie **sampleshare**.

## <a name="delete-an-azure-file-share"></a>Usuń udział plików na platformę Azure
Usuwanie udziału odbywa się przez wywołanie metody **deleteIfExists** metody dla obiekt CloudFileShare. Oto przykładowy kod, który robi to.

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
Możesz również dzielić magazynu przez umieszczenie plików w podkatalogach zamiast wszystkich z nich w katalogu głównym. Usługa pliki Azure umożliwia tworzenie katalogów tyle dopuszcza Twoje konto. Poniższy kod utworzy podkatalogu o nazwie **sampledir** w katalogu głównym.

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
Usunięcie katalogu jest proste zadania, jednak należy zauważyć, że nie można usunąć katalogu, w którym nadal zawiera pliki lub katalogi innych.

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

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Wyliczanie plików i katalogów w udziale plików na platformę Azure
Uzyskiwanie listy plików i katalogów w udziale łatwo odbywa się przez wywołanie metody **listFilesAndDirectories** na odwołanie CloudFileDirectory. Metoda zwraca listę ListFileItem obiekty, które można wykonać iterację na. Na przykład następujący kod będzie zawierała listę plików i katalogów w katalogu głównym.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Przekazywanie pliku
W tej sekcji dowiesz się, jak można przekazać pliku z magazynu lokalnego do katalogu głównego udziału.

Pierwszym krokiem podczas przekazywania pliku jest uzyskać odwołania do katalogu, której się znajduje. Można to zrobić przez wywołanie metody **getRootDirectoryReference** metody obiektu udziału.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Teraz, gdy masz odwołania do katalogu głównego udziału, możesz przekazać plik na przy użyciu następującego kodu.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>Pobieranie pliku
Jednym z częstsze operacje, które będą wykonywane względem plików Azure jest do pobierania plików. W poniższym przykładzie kod pobiera SampleFile.txt i wyświetla jego zawartość.

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
Inna operacja wspólne pliki Azure jest usuwanie plików. Poniższy kod usuwa plik o nazwie SampleFile.txt przechowywany w katalogu o nazwie **sampledir**.

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

* [Azure dla deweloperów języka Java](/java/azure)/)
* [Magazyn Azure SDK dla języka Java](https://github.com/azure/azure-storage-java)
* [Magazyn Azure SDK dla systemu Android](https://github.com/azure/azure-storage-android)
* [Odwołanie do zestawu SDK klienta usługi Azure Storage](http://dl.windowsazure.com/storage/javadoc/)
* [Interfejs API REST usług Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog zespołu odpowiedzialnego za usługę Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/)
* [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](../common/storage-use-azcopy.md)
* [Troubleshooting Azure Files problems - Windows (Rozwiązywanie problemów z usługą Azure Files — Windows)](storage-troubleshoot-windows-file-connection-problems.md)