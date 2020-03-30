---
title: Jak używać magazynu obiektów (Blob) z języka C++ — Azure | Dokumenty firmy Microsoft
description: Przechowywanie danych nieustrukturyzowanych w chmurze za pomocą magazynu obiektów (obiektowy) platformy Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/21/2018
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 0a9015e33f5456efeac7f7c887995ac4a69f0259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941807"
---
# <a name="how-to-use-blob-storage-from-c"></a>Jak korzystać z magazynu obiektów Blob z języka C++

W tym przewodniku pokazano, jak wykonywać typowe scenariusze przy użyciu magazynu obiektów Blob platformy Azure. Przykłady pokazują, jak przekazywać, listy, pobierać i usuwać obiekty blob. Przykłady są napisane w języku C++ i korzystają z [biblioteki klienta usługi Azure Table Storage dla języka C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).   

Aby dowiedzieć się więcej o magazynie obiektów Blob, zobacz [Wprowadzenie do magazynu obiektów Blob platformy Azure.](storage-blobs-introduction.md)

> [!NOTE]
> Ten przewodnik jest przeznaczony do użycia z biblioteką klienta usługi Azure Storage dla języka C++ w wersji 1.0.0 lub wyższej. Firma Microsoft zaleca korzystanie z najnowszej wersji biblioteki klienta magazynu dla języka C++, dostępnej za pośrednictwem [NuGet](https://www.nuget.org/packages/wastorage) lub [GitHub.](https://github.com/Azure/azure-storage-cpp)

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Tworzenie aplikacji języka C++
W tym przewodniku użyjesz funkcji magazynu, które można uruchomić w aplikacji Języka C++.  

W tym celu należy zainstalować bibliotekę klienta usługi Azure Storage dla języka C++ i utworzyć konto usługi Azure Storage w ramach subskrypcji platformy Azure.   

Możesz zainstalować bibliotekę klienta usługi Azure Storage dla języka C++, korzystając z następujących metod:

* **Linux:** Postępuj zgodnie z instrukcjami podanymi w [bibliotece klienta usługi Azure Storage dla języka C++ README: Wprowadzenie na](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) stronie Linux.
* **Windows:** W systemie Windows użyj [vcpkg](https://github.com/microsoft/vcpkg) jako menedżera zależności. Postępuj zgodnie z [szybkim startem,](https://github.com/microsoft/vcpkg#quick-start) aby zainicjować vcpkg. Następnie użyj następującego polecenia, aby zainstalować bibliotekę:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Można znaleźć przewodnik, jak skompilować kod źródłowy i eksportować do NuGet w pliku [README.](https://github.com/Azure/azure-storage-cpp#download--install)

## <a name="configure-your-application-to-access-blob-storage"></a>Konfigurowanie aplikacji w celu uzyskiwania dostępu do magazynu obiektów Blob
Dodaj następujące instrukcje include w górnej części pliku C++, w którym chcesz użyć interfejsów API magazynu platformy Azure w celu uzyskania dostępu do obiektów blob:  

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Konfigurowanie ciągu połączenia magazynu platformy Azure
W kliencie usługi Azure Storage punkty końcowe i poświadczenia wymagane do uzyskania dostępu do usług zarządzania danymi są przechowywane w parametrach połączenia magazynu. Podczas uruchamiania w aplikacji klienckiej, należy podać parametry połączenia magazynu w następującyformat, przy użyciu nazwy konta magazynu i klucz dostępu do magazynu dla konta magazynu wymienionych w [witrynie Azure Portal](https://portal.azure.com) dla *AccountName* i *AccountKey* wartości. Aby uzyskać informacje na temat kont magazynu i kluczy dostępu, zobacz [Informacje o kontach usługi Azure Storage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). W tym przykładzie pokazano, jak można zadeklarować pole statyczne w celu przechowywania parametrów połączenia:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Aby przetestować aplikację na lokalnym komputerze z systemem Windows, można użyć [emulatora magazynu](../storage-use-emulator.md) platformy Microsoft Azure zainstalowanego przy użyciu [narzędzia Azure SDK](https://azure.microsoft.com/downloads/). Emulator magazynu to narzędzie, które symuluje usługi obiektów Blob, Queue i Table dostępne na platformie Azure na komputerze deweloperskim lokalnym. W tym przykładzie pokazano, jak można zadeklarować pole statyczne w celu przechowywania parametrów połączenia z lokalnym emulatorem magazynu:

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Aby uruchomić emulator magazynu platformy Azure, wybierz przycisk **Start** lub naciśnij klawisz **Windows.** Zacznij wpisywać **emulator usługi Azure Storage**i wybierz **emulator usługi Microsoft Azure Storage** z listy aplikacji.  

W poniższych przykładach założono, że uzyskano parametry połączenia za pomocą jednej z tych dwóch metod.  

## <a name="retrieve-your-storage-account"></a>Pobieranie konta magazynu
Możesz użyć klasy **cloud_storage_account** do reprezentowania informacji o koncie magazynu. Aby pobrać informacje o koncie magazynu z parametrów połączenia usługi Storage, użyj metody **parse**.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Następnie uzyskać odwołanie do **klasy cloud_blob_client,** ponieważ umożliwia pobieranie obiektów, które reprezentują kontenery i obiekty blob przechowywane w magazynie obiektów Blob. Poniższy kod tworzy **obiekt cloud_blob_client** przy użyciu obiektu konta magazynu, który odzyskaliśmy powyżej:  

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>Jak: Tworzenie kontenera
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

Domyślnie nowy kontener jest prywatny i należy określić klucz dostępu do magazynu, aby pobrać obiekty BLOB z tego kontenera. Jeśli chcesz udostępnić pliki (obiekty BLOB) w kontenerze wszystkim, możesz ustawić kontener jako publiczny przy użyciu następującego kodu:  

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Każda osoba w Internecie może zobaczyć obiekty blob w kontenerze publicznym, ale można je modyfikować lub usuwać tylko wtedy, gdy masz odpowiedni klucz dostępu.  

## <a name="how-to-upload-a-blob-into-a-container"></a>Jak: Przekazywanie obiektu blob do kontenera
Usługa Azure Blob storage obsługuje blokowe obiekty blob i obiekty blob strony. W większości przypadków zalecane jest użycie blokowych obiektów blob.  

Aby przekazać plik do blokowego obiektu blob, pobierz odwołanie do kontenera i uzyskaj za jego pomocą odwołanie do blokowego obiektu blob. Po uzyskaniu odwołania do obiektu blob można przekazać dowolny strumień danych do niego, wywołując **metodę upload_from_stream.** Ta operacja spowoduje utworzenie obiektu blob, jeśli jeszcze nie istnieje, lub jego zastąpienie, jeśli już istnieje. W poniższym przykładzie przedstawiono, jak przekazać obiekt blob do kontenera, zakładając, że kontener został już utworzony.  

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

Alternatywnie można użyć **metody upload_from_file,** aby przekazać plik do bloku obiektu blob.

## <a name="how-to-list-the-blobs-in-a-container"></a>Jak: Lista obiektów blob w kontenerze
Aby wyświetlić listę obiektów blob w kontenerze, należy najpierw uzyskać odwołanie do kontenera. Następnie można użyć list_blobs **metody** kontenera, aby pobrać obiekty blob i/lub katalogi w nim. Aby uzyskać dostęp do bogatego zestawu właściwości i metod zwracanego **list_blob_item,** należy wywołać **metodę list_blob_item.as_blob,** aby uzyskać obiekt **cloud_blob** lub **metodę list_blob.as_directory,** aby uzyskać obiekt cloud_blob_directory. Poniższy kod pokazuje, jak pobrać i wyprowadzić identyfikator URI każdego elementu w kontenerze **my-sample-container:**

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

Aby uzyskać więcej informacji na temat operacji wyświetlania listy, zobacz [Lista zasobów usługi Azure Storage w języku C++](../storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Jak: Pobieranie obiektów blob
Aby pobrać obiekty BLOB, najpierw pobierz odwołanie do obiektu blob, a następnie wywołaj **metodę download_to_stream.** W poniższym przykładzie użyto **metody download_to_stream** do przeniesienia zawartości obiektu blob do obiektu strumienia, który następnie można utrwalić do pliku lokalnego.  

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

Alternatywnie można użyć **metody download_to_file,** aby pobrać zawartość obiektu blob do pliku.
Ponadto można również użyć **metody download_text,** aby pobrać zawartość obiektu blob jako ciąg tekstowy.  

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

## <a name="how-to-delete-blobs"></a>Jak: Usuwanie obiektów blob
Aby usunąć obiekt blob, najpierw uzyskać odwołanie do obiektu blob, a następnie wywołać **delete_blob** metody na nim.  

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

## <a name="next-steps"></a>Następne kroki
Teraz, gdy znasz podstawy magazynu obiektów blob, skorzystaj z tych łączy, aby dowiedzieć się więcej o usłudze Azure Storage.  

* [Jak używać usługi Queue Storage z poziomu języka C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Jak korzystać z magazynu tabel z języka C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Wyświetlanie listy zasobów usługi Azure Storage w języku C++](../storage-c-plus-plus-enumeration.md)
* [Biblioteka klienta magazynu dla odwołania języka C++](https://azure.github.io/azure-storage-cpp)
* [Dokumentacja usługi Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Przesyłanie danych za pomocą narzędzia wiersza polecenia AzCopy](../storage-use-azcopy.md)

