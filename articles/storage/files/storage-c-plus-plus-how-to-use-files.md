---
title: Tworzenie oprogramowania dla usługi Azure Files przy użyciu języka C++ | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia aplikacji w języku C++ i usług korzystających z usługi Azure Files do przechowywania danych plików.
services: storage
author: renashahmsft
ms.service: storage
ms.topic: article
ms.date: 09/19/2017
ms.author: renashahmsft
ms.subservice: files
ms.openlocfilehash: 3432794c6b6d2323392f0b56b364400d05b10afc
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649712"
---
# <a name="develop-for-azure-files-with-c"></a>Tworzenie oprogramowania dla usługi Azure Files przy użyciu języka C++

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

## <a name="about-this-tutorial"></a>Informacje o tym samouczku

W tym samouczku dowiesz się, jak wykonywać podstawowe operacje w usłudze Azure Files. Za pomocą przykładów w języku C++ dowiesz się, jak utworzyć katalogów i udziałów, Przekaż, wyświetlania i usuwania plików. Jeśli jesteś nowym użytkownikiem usługi Azure Files, przeprowadzając Koncepcje opisane w kolejnych sekcjach będą przydatne zrozumieć przykłady.

* Tworzenie i usuwanie udziałów plików platformy Azure
* Tworzenie i usuwanie katalogów
* Wyliczanie plików i katalogów w udziale plików platformy Azure
* Przekazywanie, pobieranie i usuwanie pliku
* Ustaw limit przydziału (maksymalny rozmiar) udziału plików platformy Azure
* Utworzenie sygnatury dostępu współdzielonego (klucza SAS) dla pliku, która używa zasad dostępu współdzielonego zdefiniowanych w udziale.

> [!Note]  
> Ponieważ usługi Azure Files można uzyskać dostęp za pośrednictwem protokołu SMB, istnieje możliwość napisania prostej aplikacji uzyskujących dostęp do udziału plików platformy Azure przy użyciu standardowych klas we/wy C++ i funkcji. W tym artykule opisano sposób pisania aplikacji, które używają SDK C++ magazynu platformy Azure, która używa [interfejsu API REST plików](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) na komunikowanie się z usługą Azure Files.

## <a name="create-a-c-application"></a>Tworzenie aplikacji języka C++

Aby utworzyć przykłady, należy zainstalować bibliotekę klienta usługi Azure Storage 2.4.0 dla języka C++. Należy również utworzono konto magazynu platformy Azure.

Aby zainstalować klienta usługi Storage Azure 2.4.0 dla języka C++, można użyć jednej z następujących metod:

* **Linux:** Wykonaj instrukcje podane w [biblioteki klienta usługi Azure Storage dla języka C++ w pliku README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) strony.
* **Windows:** W programie Visual Studio, kliknij przycisk **narzędzia &gt; Menedżera pakietów NuGet &gt; Konsola Menedżera pakietów**. Wpisz następujące polecenie w [Konsola Menedżera pakietów NuGet](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) i naciśnij klawisz **ENTER**.
  

```powershell
Install-Package wastorage
```

## <a name="set-up-your-application-to-use-azure-files"></a>Konfigurowanie aplikacji do użycia usługi Azure Files

Dodaj następujące obejmują instrukcji na górze pliku źródłowego języka C++, które są potrzebne do manipulowania usługi Azure Files:

```cpp
#include <was/storage_account.h>
#include <was/file.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurowanie parametrów połączenia usługi Azure storage

Używanie usługi File storage, musisz nawiązać połączenie z kontem usługi Azure storage. Pierwszym krokiem będzie można skonfigurować parametry połączenia, które będą używane do łączenia się z kontem magazynu. Czynnością jest zdefiniowanie zmienną statyczną, aby to zrobić.

```cpp
// Define the connection-string with your values.
const utility::string_t
storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="connecting-to-an-azure-storage-account"></a>Łączenie się z kontem usługi Azure storage

Możesz użyć **cloud_storage_account** klasy do reprezentowania informacje o Twoim koncie magazynu. Aby pobrać informacje o koncie magazynu z parametrów połączenia usługi Storage, użyj metody **parse**.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure

Wszystkie pliki i katalogi w udziale plików platformy Azure znajdują się w kontenerze o nazwie **udostępnianie**. Konto magazynu może zawierać dowolną liczbę akcji, pojemności konta zezwala na to. Aby uzyskać dostęp do udziału i jego zawartości, należy użyć klienta usługi Azure Files.

```cpp
// Create the Azure Files client.
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();
```

Za pomocą klienta usługi Azure Files, możesz następnie Uzyskaj odwołanie do udziału.

```cpp
// Get a reference to the file share
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
```

Aby utworzyć udział, użyj **create_if_not_exists** metody **cloud_file_share** obiektu.

```cpp
if (share.create_if_not_exists()) {
    std::wcout << U("New share created") << std::endl;
}
```

W tym momencie **udostępnianie** zawiera odwołanie do udział o nazwie **udziału mój przykład**.

## <a name="delete-an-azure-file-share"></a>Usuwanie udziału plików platformy Azure

Trwa usuwanie udziału odbywa się przez wywołanie metody **delete_if_exists** metody dla obiektu cloud_file_share. Poniżej przedstawiono przykładowy kod, który tak.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// delete the share if exists
share.delete_share_if_exists();
```

## <a name="create-a-directory"></a>Tworzenie katalogu

Magazyn można organizować przez umieszczenie plików w podkatalogach zamiast ich wszystkich w katalogu głównym. Usługa pliki systemu Azure umożliwia tworzenie katalogów tyle dopuszcza Twoje konto. Poniższy kod utworzy katalog o nazwie **katalog Moje przykładu** w obszarze katalogu głównego, a także podkatalog o nazwie **podkatalogu mój przykład**.

```cpp
// Retrieve a reference to a directory
azure::storage::cloud_file_directory directory = share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Return value is true if the share did not exist and was successfully created.
directory.create_if_not_exists();

// Create a subdirectory.
azure::storage::cloud_file_directory subdirectory =
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
subdirectory.create_if_not_exists();
```

## <a name="delete-a-directory"></a>Usuwanie katalogu

Usunięcie katalogu jest prostym zadaniem, jednak należy zauważyć, że nie można usunąć katalogu, który nadal zawiera pliki lub katalogi innych.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// Get a reference to the directory.
azure::storage::cloud_file_directory directory =
  share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Get a reference to the subdirectory you want to delete.
azure::storage::cloud_file_directory sub_directory =
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));

// Delete the subdirectory and the sample directory.
sub_directory.delete_directory_if_exists();

directory.delete_directory_if_exists();
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Wyliczanie plików i katalogów w udziale plików platformy Azure

Uzyskiwanie listy plików i katalogów w udziale łatwo odbywa się przez wywołanie metody **list_files_and_directories** na **cloud_file_directory** odwołania. Aby uzyskać dostęp z bogatego zestawu właściwości i metod zwróconego do **list_file_and_directory_item**, należy wywołać **list_file_and_directory_item.as_file** metodę, aby uzyskać **cloud_file**  obiektu lub **list_file_and_directory_item.as_directory** metodę, aby uzyskać **cloud_file_directory** obiektu.

Poniższy kod przedstawia sposób pobierania i zwracania identyfikatora URI poszczególnych elementów w katalogu głównym udziału.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir =
  share.get_root_directory_reference();

// Output URI of each item.
azure::storage::list_file_and_directory_result_iterator end_of_results;

for (auto it = directory.list_files_and_directories(); it != end_of_results; ++it)
{
    if(it->is_directory())
    {
        ucout << "Directory: " << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
    else if (it->is_file())
    {
        ucout << "File: " << it->as_file().uri().primary_uri().to_string() << std::endl;
    }
}
```

## <a name="upload-a-file"></a>Przekazywanie pliku

Co najmniej udziału plików platformy Azure zawiera katalog główny, w którym mogą znajdować się pliki. W tej sekcji dowiesz się, jak można przekazać pliku z magazynu lokalnego do katalogu głównego udziału.

Pierwszym krokiem podczas przekazywania pliku jest Uzyskaj odwołanie do katalogu, w której się znajduje. Można to zrobić, wywołując **get_root_directory_reference** metody obiektu udziału.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();
```

Teraz, gdy odwołanie do katalogu głównego udziału, możesz przekazać plik na niego. W tym przykładzie przekazywany z pliku, z pliku tekstowego i ze strumienia.

```cpp
// Upload a file from a stream.
concurrency::streams::istream input_stream =
  concurrency::streams::file_stream<uint8_t>::open_istream(_XPLATSTR("DataFile.txt")).get();

azure::storage::cloud_file file1 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));
file1.upload_from_stream(input_stream);

// Upload some files from text.
azure::storage::cloud_file file2 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
file2.upload_text(_XPLATSTR("more text"));

// Upload a file from a file.
azure::storage::cloud_file file4 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
file4.upload_from_file(_XPLATSTR("DataFile.txt"));
```

## <a name="download-a-file"></a>Pobieranie pliku

Aby pobrać pliki, należy najpierw pobrać odwołanie do pliku, a następnie wywołać **download_to_stream** metodę, aby przenieść zawartość pliku do obiektu strumienia, który można następnie zachować w pliku lokalnym. Alternatywnie, można użyć **download_to_file** metody do pobierania zawartości pliku do pliku lokalnego. Możesz użyć **download_text** metody do pobierania zawartości pliku w postaci ciągu tekstowego.

W poniższym przykładzie użyto **download_to_stream** i **download_text** metody, aby zademonstrować, pobieranie plików, które zostały utworzone w poprzednich sekcjach.

```cpp
// Download as text
azure::storage::cloud_file text_file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
utility::string_t text = text_file.download_text();
ucout << "File Text: " << text << std::endl;

// Download as a stream.
azure::storage::cloud_file stream_file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
stream_file.download_to_stream(output_stream);
std::ofstream outfile("DownloadFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();
outfile.write((char *)&data[0], buffer.size());
outfile.close();
```

## <a name="delete-a-file"></a>Usuwanie pliku

Inna operacja usługi Azure Files typowych jest usunięcie pliku. Poniższy kod usuwa plik o nazwie my przykładowe pliku-3 przechowywane w katalogu głównym.

```cpp
// Get a reference to the root directory for the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

azure::storage::cloud_file_directory root_dir =
  share.get_root_directory_reference();

azure::storage::cloud_file file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

file.delete_file_if_exists();
```

## <a name="set-the-quota-maximum-size-for-an-azure-file-share"></a>Ustaw limit przydziału (maksymalny rozmiar) udziału plików platformy Azure

Możesz ustawić limit przydziału (lub maksymalny rozmiar) udziału plików w gigabajtach. Można również sprawdzić, ile danych jest obecnie przechowywanych w udziale.

Ustawiając limit przydziału dla udziału, można ograniczyć całkowity rozmiar plików przechowywanych w udziale. Jeśli całkowity rozmiar plików w udziale przekroczy ustawiony limit przydziału, klienci nie będą mogli zwiększyć rozmiaru istniejących plików ani tworzyć nowych plików (chyba że pliki będą puste).

W poniższym przykładzie pokazano, jak sprawdzić bieżące użycie udziału oraz jak ustawić limit przydziału w udziale.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client.
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();

// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
if (share.exists())
{
    std::cout << "Current share usage: " << share.download_share_usage() << "/" << share.properties().quota();

    //This line sets the quota to 2560GB
    share.resize(2560);

    std::cout << "Quota increased: " << share.download_share_usage() << "/" << share.properties().quota();

}
```

## <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Generowanie sygnatury dostępu współdzielonego dla pliku lub udziału plików

Można wygenerować sygnatury dostępu współdzielonego (SAS) dla udziału plików lub dla pojedynczego pliku. Można też utworzyć zasady dostępu współdzielonego w udziale plików na potrzeby zarządzania sygnaturami dostępu współdzielonego. Utworzenie zasad dostępu współdzielonego jest zalecane, ponieważ umożliwia cofnięcie sygnatur w przypadku zagrożenia bezpieczeństwa.

W poniższym przykładzie tworzone są zasady dostępu współdzielonego w udziale. Następnie za pomocą tych zasad nakładane są ograniczenia na sygnatury dostępu współdzielonego w pliku w udziale.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client and get a reference to the share
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();

azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

if (share.exists())
{
    // Create and assign a policy
    utility::string_t policy_name = _XPLATSTR("sampleSharePolicy");

    azure::storage::file_shared_access_policy sharedPolicy =
      azure::storage::file_shared_access_policy();

    //set permissions to expire in 90 minutes
    sharedPolicy.set_expiry(utility::datetime::utc_now() +
       utility::datetime::from_minutes(90));

    //give read and write permissions
    sharedPolicy.set_permissions(azure::storage::file_shared_access_policy::permissions::write | azure::storage::file_shared_access_policy::permissions::read);

    //set permissions for the share
    azure::storage::file_share_permissions permissions;

    //retrieve the current list of shared access policies
    azure::storage::shared_access_policies<azure::storage::file_shared_access_policy> policies;

    //add the new shared policy
    policies.insert(std::make_pair(policy_name, sharedPolicy));

    //save the updated policy list
    permissions.set_policies(policies);
    share.upload_permissions(permissions);

    //Retrieve the root directory and file references
    azure::storage::cloud_file_directory root_dir =
        share.get_root_directory_reference();
    azure::storage::cloud_file file =
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));

    // Generate a SAS for a file in the share
    //  and associate this access policy with it.
    utility::string_t sas_token = file.get_shared_access_signature(sharedPolicy);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    azure::storage::cloud_file file_with_sas(azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()));
    utility::string_t text = _XPLATSTR("My sample content");
    file_with_sas.upload_text(text);

    //Download and print URL with SAS.
    utility::string_t downloaded_text = file_with_sas.download_text();
    ucout << downloaded_text << std::endl;
    ucout << azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()).to_string() << std::endl;

}
```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat usługi Azure Storage, zapoznaj się z tymi zasobami:

* [Biblioteka klienta usługi Storage dla języka C++](https://github.com/Azure/azure-storage-cpp)
* [Próbki usługi plików usługi Azure Storage w języku C++](https://github.com/Azure-Samples/storage-file-cpp-getting-started)
* [Azure Storage Explorer](https://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
* [Dokumentacja usługi Azure Storage](https://azure.microsoft.com/documentation/services/storage/)