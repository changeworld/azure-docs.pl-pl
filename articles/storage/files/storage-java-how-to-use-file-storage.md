---
title: Tworzenie plików platformy Azure za pomocą oprogramowania Java | Dokumenty firmy Microsoft
description: Dowiedz się, jak tworzyć aplikacje i usługi java, które używają plików Azure Do przechowywania danych plików.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 28a280ea7c3bf9ef84a1fff05da5090ed526fb12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73837472"
---
# <a name="develop-for-azure-files-with-java"></a>Develop for Azure Files with Java (Tworzenie oprogramowania dla usługi Azure Files przy użyciu języka Java)
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>Informacje o tym samouczku
W tym samouczku zostaną zademonstrowane podstawy używania oprogramowania Java do tworzenia aplikacji lub usług, które używają usługi Azure Files do przechowywania danych plików. W tym samouczku utworzymy aplikację konsoli i pokażemy, jak wykonywać podstawowe akcje za pomocą plików Java i Azure:

* Tworzenie i usuwanie udziałów plików platformy Azure
* Tworzenie i usuwanie katalogów
* Wyliczaj pliki i katalogi w udziale plików platformy Azure
* Przekazywanie, pobieranie i usuwanie pliku

> [!Note]  
> Ponieważ usługa Azure Files mogą być dostępne za pośrednictwem SMB, istnieje możliwość pisania aplikacji, które uzyskują dostęp do udziału plików platformy Azure przy użyciu standardowych klas We/Wy Języka Java. W tym artykule opisano sposób pisania aplikacji korzystających z narzędzia Azure Storage Java SDK, który używa [interfejsu API REST plików azure](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) do komunikacji z usługą Azure Files.

## <a name="create-a-java-application"></a>Tworzenie aplikacji Java
Aby utworzyć próbki, potrzebny będzie zestaw Java Development Kit (JDK) i [zestaw SDK usługi Azure Storage dla języka Java.](https://github.com/Azure/azure-storage-java) Należy również utworzyć konto magazynu platformy Azure.

## <a name="set-up-your-application-to-use-azure-files"></a>Konfigurowanie aplikacji do używania plików platformy Azure
Aby użyć interfejsów API magazynu platformy Azure, dodaj następującą instrukcję do górnej części pliku Java, z którego zamierzasz uzyskać dostęp do usługi magazynu.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurowanie ciągu połączenia magazynu platformy Azure
Aby korzystać z usługi Azure Files, musisz połączyć się z kontem magazynu platformy Azure. Pierwszym krokiem będzie skonfigurowanie ciągu połączenia, którego użyjemy do nawiązania połączenia z kontem magazynu. Zdefiniujmy zmienną statyczną, aby to zrobić.

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

## <a name="connecting-to-an-azure-storage-account"></a>Łączenie się z kontem magazynu platformy Azure
Aby połączyć się z kontem magazynu, należy użyć **CloudStorageAccount** obiektu, przekazując ciąg połączenia do jego metody **analizy.**

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** zgłasza InvalidKeyException więc musisz umieścić go wewnątrz bloku try/catch.

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure
Wszystkie pliki i katalogi w usłudze Azure Files znajdują się w kontenerze o nazwie **Share**. Twoje konto pamięci masowej może mieć tyle udziałów, ile pozwala na to pojemność konta. Aby uzyskać dostęp do udziału i jego zawartości, należy użyć klienta usługi Azure Files.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Za pomocą klienta usługi Azure Files, można następnie uzyskać odwołanie do udziału.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Aby faktycznie utworzyć udział, należy użyć **metody createIfNotExists** obiektu CloudFileShare.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

W tym momencie **akcja** posiada odwołanie do udziału o nazwie **sampleshare**.

## <a name="delete-an-azure-file-share"></a>Usuwanie udziału plików platformy Azure
Usuwanie udziału odbywa się przez wywołanie **deleteIfExists** metody na CloudFileShare obiektu. Oto przykładowy kod, który to robi.

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
Można również zorganizować magazyn, umieszczając pliki wewnątrz podkatalogów zamiast mieć wszystkie z nich w katalogu głównym. Usługa Azure Files umożliwia utworzenie tylu katalogów, na ile pozwoli na to twoje konto. Poniższy kod utworzy podkatalog o nazwie **sampledir** w katalogu głównym.

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
Usunięcie katalogu jest prostym zadaniem, chociaż należy zauważyć, że nie można usunąć katalogu, który nadal zawiera pliki lub inne katalogi.

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

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Wyliczaj pliki i katalogi w udziale plików platformy Azure
Uzyskanie listy plików i katalogów w ramach udziału jest łatwo zrobić przez wywołanie **listFilesAndDirectories** na CloudFileDirectory odwołania. Metoda zwraca listę Obiektów ListFileItem, które można iterować na. Na przykład poniższy kod wyświetli listę plików i katalogów wewnątrz katalogu głównego.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Przekazywanie pliku
W tej sekcji dowiesz się, jak przekazać plik z magazynu lokalnego do katalogu głównego udziału.

Pierwszym krokiem przekazywania pliku jest uzyskanie odwołania do katalogu, w którym powinien się znajdować. Można to zrobić, wywołując **metodę getRootDirectoryReference** obiektu udziału.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Teraz, gdy masz odwołanie do katalogu głównego udziału, możesz przekazać plik do niego za pomocą następującego kodu.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>Pobieranie pliku
Jednym z częstszych operacji, które będą wykonywane w usłudze Azure Files jest pobieranie plików. W poniższym przykładzie kod pobiera Plik SampleFile.txt i wyświetla jego zawartość.

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
Inną wspólną operacją usługi Azure Files jest usunięcie pliku. Poniższy kod usuwa plik o nazwie SampleFile.txt przechowywany wewnątrz katalogu o nazwie **sampledir**.

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

## <a name="next-steps"></a>Następne kroki
Jeśli chcesz dowiedzieć się więcej o innych interfejsach API magazynu platformy Azure, skorzystaj z tych łączy.

* [Azure dla deweloperów Java](/java/azure)/)
* [Zestaw SDK usługi Azure Storage dla języka Java](https://github.com/azure/azure-storage-java)
* [Zestaw SDK usługi Azure Storage dla systemu Android](https://github.com/azure/azure-storage-android)
* [Dokumentacja zestawu SDK klienta usługi Azure Storage](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html)
* [Interfejs API REST usług Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog zespołu usługi Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/)
* [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](../common/storage-use-azcopy.md)
* [Troubleshooting Azure Files problems - Windows (Rozwiązywanie problemów z usługą Azure Files — Windows)](storage-troubleshoot-windows-file-connection-problems.md)
