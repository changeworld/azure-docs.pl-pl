---
title: Jak używać magazynu obiektów (Blob) z C++ - Azure | Dokumentacja firmy Microsoft
description: Przechowuj dane niestrukturalne w chmurze za pomocą magazynu obiektów Blob platformy Azure (obiekt).
services: storage
author: MichaelHauss
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/21/2018
ms.author: michaelhauss
ms.openlocfilehash: d3297ae7bc4a5ac7e2a43d9d44a05365004b685f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31418806"
---
# <a name="how-to-use-blob-storage-from-c"></a>Jak używać magazynu obiektów Blob w języku C++

W tym przewodniku przedstawiono sposób wykonywania typowych scenariuszy przy użyciu usługi magazynu obiektów Blob platformy Azure. Przykłady są napisane w C++ i użyj [biblioteki klienta usługi Azure Storage dla języka C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md). Omówione scenariusze obejmują przekazywanie, wyświetlanie, pobieranie i usuwanie obiektów blob.  

> [!NOTE]
> Ten przewodnik jest przeznaczony dla biblioteki klienta magazynu Azure dla języka C++ w wersji 1.0.0 i powyżej. Firma Microsoft zaleca używanie najnowszej wersji biblioteki klienta usługi Storage dla języka C++, dostępne za pośrednictwem [NuGet](http://www.nuget.org/packages/wastorage) lub [GitHub](https://github.com/Azure/azure-storage-cpp).

## <a name="what-is-blob-storage"></a>Co to jest magazyn obiektów Blob?

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Tworzenie aplikacji C++
W tym przewodniku użyje funkcji magazynu, które mogą być uruchamiane w ramach aplikacji C++.  

Aby to zrobić, należy zainstalować bibliotekę klienta usługi Azure Storage dla języka C++ i Utwórz konto magazynu Azure w ramach subskrypcji platformy Azure.   

Aby zainstalować bibliotekę klienta usługi Azure Storage dla języka C++, można użyć następujących metod:

* **Linux:** postępuj zgodnie z instrukcjami [biblioteki klienta usługi Azure Storage dla języka C++ README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) strony.  
* **System Windows:** w programie Visual Studio, kliknij przycisk **Narzędzia > Menedżera pakietów NuGet > konsoli Menedżera pakietów**. Wpisz następujące polecenie w [Konsola Menedżera pakietów NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) i naciśnij klawisz **ENTER**.  
  
     Pakiet instalacyjny wastorage

## <a name="configure-your-application-to-access-blob-storage"></a>Konfigurowanie aplikacji dostęp do magazynu obiektów Blob
Dodaj następujące instrukcje na początku pliku C++, której chcesz używać interfejsów API magazynu Azure na dostęp do obiektów blob obejmują:  

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Konfiguracja parametrów połączenia usługi Azure storage
Klienta usługi Azure storage używa parametrów połączenia magazynu do przechowywania punktów końcowych i poświadczeń do uzyskiwania dostępu do danych usługi zarządzania. Podczas uruchamiania w aplikacji klienckiej, należy podać parametry połączenia magazynu w następującym formacie, przy użyciu nazwy konta magazynu i klucz dostępu do magazynu dla konta magazynu na liście [Azure Portal](https://portal.azure.com) dla *AccountName* i *AccountKey* wartości. Aby uzyskać informacje dotyczące kont magazynu i klucze dostępu, zobacz [o kontach magazynu Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Ten przykład przedstawia, jak można zadeklarować pola statycznego do przechowywania parametrów połączenia:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Aby przetestować aplikację w lokalnym komputerze z systemem Windows, można użyć Microsoft Azure [emulatora magazynu](../storage-use-emulator.md) zainstalowane z [zestawu Azure SDK](https://azure.microsoft.com/downloads/). Emulator magazynu jest narzędziem, która symuluje dostępnej na platformie Azure na komputerze deweloperskim lokalnych usług obiektów Blob, kolejki i tabeli. W poniższym przykładzie pokazano, jak można zadeklarować pole statyczne, aby mógł pomieścić parametry połączenia z lokalnym emulatorze magazynu:

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Aby uruchomić emulatora magazynu Azure, wybierz **Start** przycisk lub naciśnij przycisk **Windows** klucza. Rozpocznij wpisywanie **emulatora magazynu Azure**i wybierz **emulatora magazynu Azure Microsoft** z listy aplikacji.  

Poniższe przykłady założono użycie jednej z tych dwóch metod można pobrać parametry połączenia magazynu.  

## <a name="retrieve-your-connection-string"></a>Pobranie parametrów połączenia
Można użyć **cloud_storage_account** klasy do reprezentowania informacje o koncie magazynu. Aby uzyskać informacje o koncie magazynu z parametrów połączenia magazynu, można użyć **przeanalizować** metody.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Następnie należy pobrać odwołanie do **cloud_blob_client** klasa umożliwia pobieranie obiektów, które reprezentują kontenery i obiekty BLOB przechowywane w magazynie obiektów Blob. Poniższy kod tworzy **cloud_blob_client** przy użyciu obiektu konta magazynu, możemy pobrać powyżej:  

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>Porady: Tworzenie kontenera
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

Domyślnie nowy kontener jest prywatny i należy określić klucz dostępu do magazynu, aby pobierać obiekty BLOB z tego kontenera. Jeśli chcesz udostępnić pliki (BLOB) w kontenerze wszystkim użytkownikom, możesz ustawić kontener jako publiczny przy użyciu następującego kodu:  

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Wszyscy użytkownicy Internetu mogą wyświetlać obiekty BLOB w kontenerze publicznym, ale można zmodyfikować lub usunąć je tylko wtedy, gdy klucz dostępu.  

## <a name="how-to-upload-a-blob-into-a-container"></a>Porady: przekazywanie obiektu blob do kontenera
Azure obsługuje magazynu obiektów Blob blokować obiektów blob i stronicowe obiekty BLOB. W większości przypadków zalecane jest użycie blokowych obiektów blob.  

Aby przekazać plik do blokowego obiektu blob, pobierz odwołanie do kontenera i uzyskaj za jego pomocą odwołanie do blokowego obiektu blob. Po utworzeniu odwołanie do obiektu blob, dowolny strumień danych można przekazać do niej przez wywołanie metody **upload_from_stream** metody. Ta operacja spowoduje utworzenie obiektu blob, jeśli jeszcze nie istnieje, lub jego zastąpienie, jeśli już istnieje. W poniższym przykładzie przedstawiono, jak przekazać obiekt blob do kontenera, zakładając, że kontener został już utworzony.  

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

Alternatywnie można użyć **upload_from_file** metody, aby przekazać plik do blokowego obiektu blob.

## <a name="how-to-list-the-blobs-in-a-container"></a>Porady: wyświetlanie obiektów blob w kontenerze
Aby wyświetlić listę obiektów blob w kontenerze, należy najpierw uzyskać odwołanie do kontenera. Następnie można użyć kontenera **list_blobs** metodę, aby pobrać obiekty BLOB i/lub zawarte w nim katalogi. Aby dostęp do bogatego zestawu właściwości i metod zwróconego **list_blob_item**, należy wywołać **list_blob_item.as_blob** metodę, aby pobrać **cloud_blob** obiekt, lub **list_blob.as_directory** metody pobierania obiektu cloud_blob_directory. Poniższy kod przedstawia sposób pobierania i zwracania identyfikatora URI poszczególnych elementów w **Moje kontenera próbki** kontenera:

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

Więcej szczegółów na listę działań, zobacz [listy zasobów magazynu Azure w języku C++](../storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Porady: pobieranie obiektów blob
Aby pobrać obiekty BLOB, należy najpierw pobrać odwołanie do obiektu blob, a następnie wywołać **download_to_stream** metody. W poniższym przykładzie użyto **download_to_stream** metodę, aby przesłać zawartość obiektu blob do obiektu strumienia, który można następnie zachować do pliku lokalnego.  

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

Alternatywnie można użyć **download_to_file** metodę, aby pobrać zawartość obiektu blob do pliku.
Ponadto umożliwia także **download_text** metodę, aby pobrać zawartość obiektu blob jako ciąg tekstowy.  

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

## <a name="how-to-delete-blobs"></a>Porady: usuwanie obiektów blob
Aby usunąć obiekt blob, najpierw pobrać odwołanie do obiektu blob, a następnie wywołać **delete_blob** dla niego metodę.  

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
Teraz, kiedy znasz już podstawy magazynu obiektów blob, skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat usługi Azure Storage.  

* [Jak używać magazynu kolejek w języku C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Jak używać magazynu tabel w języku C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Lista zasobów magazynu Azure w języku C++](../storage-c-plus-plus-enumeration.md)
* [Biblioteka klienta usługi Storage for C++ — dokumentacja](http://azure.github.io/azure-storage-cpp)
* [Dokumentacja usługi Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Transfer danych za pomocą narzędzia wiersza polecenia AzCopy](../storage-use-azcopy.md)

