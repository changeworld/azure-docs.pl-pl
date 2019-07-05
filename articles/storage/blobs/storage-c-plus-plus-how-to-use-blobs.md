---
title: Jak używać magazynu obiektów (Blob) w języku C++ — Azure | Dokumentacja firmy Microsoft
description: Store danych bez struktury w chmurze za pomocą magazynu obiektów Blob platformy Azure (obiekt).
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mhopkins
ms.reviewer: seguler
ms.subservice: blobs
ms.openlocfilehash: 32601fc15e6b6a25a1005b6d95499d4aae4fb0d2
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461186"
---
# <a name="how-to-use-blob-storage-from-c"></a>Jak używać magazynu obiektów Blob w języku C++

Ten przewodnik pokazuje, jak realizować typowe scenariusze za pomocą usługi Azure Blob storage. W przykładach pokazano, jak przekazywanie, listy, pobieranie i usuwanie obiektów blob. Przykłady są napisane w języku C++ i korzystają z [biblioteki klienta usługi Azure Table Storage dla języka C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).   

Aby dowiedzieć się więcej o usłudze Blob storage, zobacz [wprowadzenie do usługi Azure Blob storage](storage-blobs-introduction.md).

> [!NOTE]
> Ten przewodnik jest przeznaczony do użycia z biblioteką klienta usługi Azure Storage dla języka C++ w wersji 1.0.0 lub wyższej. Firma Microsoft zaleca używanie najnowszej wersji biblioteki klienta usługi Storage dla języka C++, dostępne za pośrednictwem [NuGet](https://www.nuget.org/packages/wastorage) lub [GitHub](https://github.com/Azure/azure-storage-cpp).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Tworzenie aplikacji języka C++
W tym przewodniku użyje funkcji magazynu, które mogą być uruchamiane w ramach aplikacji w języku C++.  

W tym celu należy zainstalować bibliotekę klienta usługi Azure Storage dla języka C++ i utworzyć konto usługi Azure Storage w ramach subskrypcji platformy Azure.   

Możesz zainstalować bibliotekę klienta usługi Azure Storage dla języka C++, korzystając z następujących metod:

* **Linux:** Wykonaj instrukcje podane w [biblioteki klienta usługi Azure Storage dla języka C++ w pliku README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) strony.  
* **Windows:** W programie Visual Studio kliknij pozycję **Narzędzia > Menedżer pakietów NuGet > Konsola menedżera pakietów**. Wpisz następujące polecenie w [Konsola Menedżera pakietów NuGet](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) i naciśnij klawisz **ENTER**.  
  
     Install-Package wastorage

## <a name="configure-your-application-to-access-blob-storage"></a>Konfigurowanie aplikacji dostępu do magazynu obiektów Blob
Dodaj następujące obejmują instrukcji na górze pliku C++, w której chcesz użyć interfejsów API magazynu Azure dostęp do obiektów blob:  

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Konfigurowanie parametrów połączenia usługi Azure storage
W kliencie usługi Azure Storage punkty końcowe i poświadczenia wymagane do uzyskania dostępu do usług zarządzania danymi są przechowywane w parametrach połączenia magazynu. Podczas uruchamiania w aplikacji klienckiej, należy podać parametry połączenia magazynu w następującym formacie, przy użyciu nazwy konta magazynu oraz klucz dostępu do magazynu dla konta magazynu na liście [witryny Azure Portal](https://portal.azure.com) dla *AccountName* i *AccountKey* wartości. Aby uzyskać informacje na temat kont magazynu i klucze dostępu, zobacz [o kontach magazynu Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). W tym przykładzie pokazano, jak można zadeklarować pole statyczne w celu przechowywania parametrów połączenia:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Aby przetestować aplikację na lokalnym komputerze Windows, należy użyć Microsoft Azure [emulatora magazynu](../storage-use-emulator.md) który został zainstalowany przy użyciu [zestawu Azure SDK](https://azure.microsoft.com/downloads/). Emulator magazynu jest narzędziem, która symuluje sieć usługi obiektów Blob, kolejek i tabel, które muszą być dostępne na platformie Azure na lokalnej maszynie do programowania. W tym przykładzie pokazano, jak można zadeklarować pole statyczne w celu przechowywania parametrów połączenia z lokalnym emulatorem magazynu:

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Aby uruchomić emulatora usługi Azure storage, zaznacz **Start** przycisk lub naciśnij klawisz **Windows** klucza. Rozpocznij wpisywanie ciągu **emulatora usługi Azure Storage**i wybierz **emulatora systemu Microsoft Azure Storage** z listy aplikacji.  

W poniższych przykładach założono, że uzyskano parametry połączenia za pomocą jednej z tych dwóch metod.  

## <a name="retrieve-your-storage-account"></a>Pobrać konta usługi storage
Możesz użyć **cloud_storage_account** klasy do reprezentowania informacje o Twoim koncie magazynu. Aby pobrać informacje o koncie magazynu z parametrów połączenia usługi Storage, użyj metody **parse**.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Następnie Pobierz odwołanie do **cloud_blob_client** klasy, ponieważ pozwala pobrać obiekty reprezentujące kontenerów i obiektów blob przechowywanych w magazynie obiektów Blob. Poniższy kod tworzy **cloud_blob_client** przy użyciu obiektu konta magazynu, możemy pobrać powyżej:  

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>Instrukcje: Tworzenie kontenera
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

W tym przykładzie pokazano, jak utworzyć kontener, jeśli jeszcze nie istnieje:  

```cpp
try
{
    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Create the container if it doesn't already exist.
    container.create_if_not_exists();
}
catch (const std::exception& e)
{
    std::wcout << U("Error: ") << e.what() << std::endl;
}  
```

Domyślnie nowy kontener jest prywatny, i podaj klucz dostępu do magazynu, aby pobierać obiekty BLOB z tego kontenera. Jeśli chcesz udostępnić pliki (obiektów BLOB) w kontenerze wszystkim użytkownikom, można ustawić kontener jako publiczny, używając następującego kodu:  

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Wszyscy użytkownicy Internetu mogą wyświetlać obiekty BLOB w kontenerze publicznym, ale użytkownik może je modyfikować lub usuwać tylko wtedy, gdy masz odpowiedni klucz dostępu.  

## <a name="how-to-upload-a-blob-into-a-container"></a>Instrukcje: Przekazywanie obiektu blob do kontenera
Azure Blob storage obsługuje block, obiekty BLOB i stronicowe obiekty BLOB. W większości przypadków zalecane jest użycie blokowych obiektów blob.  

Aby przekazać plik do blokowego obiektu blob, pobierz odwołanie do kontenera i uzyskaj za jego pomocą odwołanie do blokowego obiektu blob. Po uzyskaniu odwołania do obiektu blob możesz przekazać dowolny strumień danych do niego, wywołując **upload_from_stream** metody. Ta operacja spowoduje utworzenie obiektu blob, jeśli jeszcze nie istnieje, lub jego zastąpienie, jeśli już istnieje. W poniższym przykładzie przedstawiono, jak przekazać obiekt blob do kontenera, zakładając, że kontener został już utworzony.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Create or overwrite the "my-blob-1" blob with contents from a local file.
concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
blockBlob.upload_from_stream(input_stream);
input_stream.close().wait();

// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
// Retrieve a reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
blob2.upload_text(U("more text"));

// Retrieve a reference to a blob named "my-blob-3".
azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
blob3.upload_text(U("other text"));  
```

Alternatywnie, można użyć **upload_from_file** metodę, aby przekazać plik do blokowego obiektu blob.

## <a name="how-to-list-the-blobs-in-a-container"></a>Instrukcje: Wyświetlanie listy obiektów blob w kontenerze
Aby wyświetlić listę obiektów blob w kontenerze, należy najpierw uzyskać odwołanie do kontenera. Następnie można użyć kontenera **list_blobs** metodę, aby pobrać obiekty BLOB i/lub zawarte w nim katalogi. Aby uzyskać dostęp z bogatego zestawu właściwości i metod zwróconego do **list_blob_item**, należy wywołać **list_blob_item.as_blob** metodę, aby uzyskać **cloud_blob** obiektu, lub **list_blob.as_directory** metodę, aby pobrać obiekt cloud_blob_directory. Poniższy kod przedstawia sposób pobierania i zwracania identyfikatora URI poszczególnych elementów w **kontenera mój przykład** kontenera:

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Output URI of each item.
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
    }
    else
    {
        std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
}
```

Aby uzyskać szczegółowe informacje na temat wyświetlania listy operacji, zobacz [zasobów magazynu Azure listy w języku C++](../storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Instrukcje: Pobieranie obiektów blob
Aby pobrać obiekty BLOB, należy najpierw pobrać odwołanie do obiektu blob, a następnie wywołać **download_to_stream** metody. W poniższym przykładzie użyto **download_to_stream** metodę, aby przesłać zawartość obiektu blob do obiektu strumienia, który można następnie zachować w pliku lokalnym.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Save blob contents to a file.
concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
blockBlob.download_to_stream(output_stream);

std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();

outfile.write((char *)&data[0], buffer.size());
outfile.close();  
```

Alternatywnie, można użyć **download_to_file** metody do pobierania zawartości obiektu blob do pliku.
Ponadto umożliwia także **download_text** metody do pobierania zawartości obiektu blob jako ciąg tekstowy.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

// Download the contents of a blog as a text string.
utility::string_t text = text_blob.download_text();
```

## <a name="how-to-delete-blobs"></a>Instrukcje: Usuwanie obiektów blob
Aby usunąć obiekt blob, najpierw pobrać odwołanie do obiektu blob, a następnie wywołać **delete_blob** metody na nim.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Delete the blob.
blockBlob.delete_blob();
```

## <a name="next-steps"></a>Kolejne kroki
Teraz, kiedy znasz już podstawy usługi blob storage, skorzystaj z poniższych linków, aby dowiedzieć się więcej o usłudze Azure Storage.  

* [Jak używać magazynu kolejek w języku C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Jak używać magazynu tabel w języku C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Lista zasobów usługi Azure Storage w języku C++](../storage-c-plus-plus-enumeration.md)
* [Biblioteka klienta usługi Storage for C++ Concepts](https://azure.github.io/azure-storage-cpp)
* [Dokumentacja usługi Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](../storage-use-azcopy.md)

