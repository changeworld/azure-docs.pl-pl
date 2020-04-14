---
title: Zestaw SDK usługi Azure Data Lake Storage Gen2 Python dla plików & list ACL
description: Użyj python zarządzać katalogami i list kontroli dostępu do plików i katalogów (ACL) na kontach magazynu z hierarchiczną przestrzenią nazw (HNS) włączoną.
author: normesta
ms.service: storage
ms.date: 04/10/2020
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: a79f3110206a01b9b974952f0ec0d299644be11f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262353"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Używanie języka Python do zarządzania katalogami, plikami i listami AL w usłudze Azure Data Lake Storage Gen2

W tym artykule pokazano, jak używać języka Python do tworzenia katalogów, plików i uprawnień oraz zarządzania nimi na kontach magazynu z włączoną hierarchiczną przestrzenią nazw (HNS). 

[Pakiet (Python Package Index)](https://pypi.org/project/azure-storage-file-datalake/) | [Próbki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [odwołania](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0/azure.storage.filedatalake.html) | interfejsu API[Gen1 do mapowania](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | Gen2[Give Feedback](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Konto magazynu z włączoną hierarchiczną przestrzenią nazw (HNS). Postępuj zgodnie z [tymi](data-lake-storage-quickstart-create-account.md) instrukcjami, aby je utworzyć.

## <a name="set-up-your-project"></a>konfigurowanie projektu

Zainstaluj bibliotekę klienta usługi Azure Data Lake Storage dla języka Python przy użyciu [pip](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake
```

Dodaj te instrukcje importu do górnej części pliku kodu.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Łączenie się z kontem

Aby użyć urywków w tym artykule, należy utworzyć **datalakeServiceClient wystąpienie,** które reprezentuje konto magazynu. 

### <a name="connect-by-using-an-account-key"></a>Łączenie się przy użyciu klucza konta

Jest to najprostszy sposób na połączenie się z kontem. 

W tym przykładzie tworzy **DataLakeServiceClient wystąpienie** przy użyciu klucza konta.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Zastąp wartość symbolu zastępczego `storage_account_name` nazwą konta magazynu.

- Zastąp wartość symbolu `storage_account_key` zastępczego kluczem dostępu do konta magazynu.

### <a name="connect-by-using-azure-active-directory-ad"></a>Połącz się przy użyciu usługi Azure Active Directory (AD)

Można użyć [biblioteki klienta tożsamości platformy Azure dla języka Python](https://pypi.org/project/azure-identity/) do uwierzytelniania aplikacji za pomocą usługi Azure AD.

W tym przykładzie tworzy **DataLakeServiceClient wystąpienie** przy użyciu identyfikatora klienta, klucz tajny klienta i identyfikator dzierżawy.  Aby uzyskać te wartości, zobacz [Uzyskiwanie tokenu z usługi Azure AD do autoryzowania żądań z aplikacji klienckiej.](../common/storage-auth-aad-app.md)

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> Aby uzyskać więcej przykładów, zobacz [bibliotekę klienta tożsamości platformy Azure dla](https://pypi.org/project/azure-identity/) dokumentacji języka Python.

## <a name="create-a-file-system"></a>Tworzenie systemu plików

System plików działa jako kontener dla plików. Można go utworzyć, wywołując **metodę FileSystemDataLakeServiceClient.create_file_system.**

W tym przykładzie `my-file-system`tworzy system plików o nazwie .

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>Tworzenie katalogu

Utwórz odwołanie do katalogu, wywołując **metodę FileSystemClient.create_directory.**

W tym przykładzie `my-directory` dodaje katalog o nazwie do systemu plików. 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>Zmienianie nazwy lub przenoszenie katalogu

Zmień nazwę lub przenieś katalog, wywołując **metodę DataLakeDirectoryClient.rename_directory.** Przekaż ścieżkę żądanego katalogu parametrem. 

W tym przykładzie zmienia nazwę podkata directory na nazwę `my-subdirectory-renamed`.

```python
def rename_directory():
    try:
       
       file_system_client = service_client.get_file_system_client(file_system="my-file-system")
       directory_client = file_system_client.get_directory_client("my-directory")
       
       new_dir_name = "my-directory-renamed"
       directory_client.rename_directory(rename_destination=directory_client.file_system_name + '/' + new_dir_name)

    except Exception as e:
     print(e) 
```

## <a name="delete-a-directory"></a>Usuwanie katalogu

Usuń katalog, wywołując **metodę DataLakeDirectoryClient.delete_directory.**

W tym przykładzie usuwa `my-directory`katalog o nazwie .  

```python
def delete_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")
        directory_client = file_system_client.get_directory_client("my-directory")

        directory_client.delete_directory()
    except Exception as e:
     print(e) 
```

## <a name="manage-directory-permissions"></a>Zarządzanie uprawnieniami do katalogów

Pobierz listę kontroli dostępu (ACL) katalogu, wywołując **metodę DataLakeDirectoryClient.get_access_control** i ustaw listę ACL, wywołując metodę **DataLakeDirectoryClient.set_access_control.**

> [!NOTE]
> Jeśli aplikacja autoryzuje dostęp przy użyciu usługi Azure Active Directory (Azure AD), upewnij się, że podmiot zabezpieczeń używany przez aplikację do autoryzowania dostępu został przypisany [do roli Właściciel danych obiektu Blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Aby dowiedzieć się więcej o sposobie stosowania uprawnień listy ACL i skutkach ich zmiany, zobacz [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

W tym przykładzie pobiera i ustawia `my-directory`listy ACL katalogu o nazwie . Ciąg `rwxr-xrw-` daje użytkownikowi właścicielem uprawnienia do odczytu, zapisu i wykonywania, daje grupie posiadającej tylko uprawnienia do odczytu i wykonywania i daje wszystkim innym uprawnienia do odczytu i zapisu.

```python
def manage_directory_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_dir_permissions = "rwxr-xrw-"
        
        directory_client.set_access_control(permissions=new_dir_permissions)
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
    
    except Exception as e:
     print(e) 
```

## <a name="upload-a-file-to-a-directory"></a>Przekazywanie pliku do katalogu 

Najpierw utwórz odwołanie do pliku w katalogu docelowym, tworząc wystąpienie klasy **DataLakeFileClient.** Przekaż plik, wywołując **metodę DataLakeFileClient.append_data.** Upewnij się, aby zakończyć przekazywanie, wywołując **DataLakeFileClient.flush_data** metody.

W tym przykładzie plik tekstowy `my-directory`jest przesyłany do katalogu o nazwie .   

```python
def upload_file_to_directory():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.create_file("uploaded-file.txt")
        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.append_data(data=file_contents, offset=0, length=len(file_contents))

        file_client.flush_data(len(file_contents))

    except Exception as e:
      print(e) 
```

> [!TIP]
> Jeśli rozmiar pliku jest duży, kod będzie musiał wykonać wiele wywołań **do Metody DataLakeFileClient.append_data.** Należy rozważyć użycie **DataLakeFileClient.upload_data** metody zamiast. W ten sposób można przekazać cały plik w jednym wywołaniu. 

## <a name="upload-a-large-file-to-a-directory"></a>Przekazywanie dużego pliku do katalogu

Metoda **DataLakeFileClient.upload_data** umożliwia przekazywanie dużych plików bez konieczności wykonywania wielu wywołań metody **DataLakeFileClient.append_data.**

```python
def upload_file_to_directory_bulk():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.upload_data(file_contents, overwrite=True)

    except Exception as e:
      print(e) 
```

## <a name="manage-file-permissions"></a>Zarządzanie uprawnieniami do plików

Pobierz listę kontroli dostępu (ACL) pliku, wywołując **metodę DataLakeFileClient.get_access_control** i ustaw listę ACL, wywołując metodę **DataLakeFileClient.set_access_control.**

> [!NOTE]
> Jeśli aplikacja autoryzuje dostęp przy użyciu usługi Azure Active Directory (Azure AD), upewnij się, że podmiot zabezpieczeń używany przez aplikację do autoryzowania dostępu został przypisany [do roli Właściciel danych obiektu Blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Aby dowiedzieć się więcej o sposobie stosowania uprawnień listy ACL i skutkach ich zmiany, zobacz [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

W tym przykładzie pobiera i ustawia `my-file.txt`ACL pliku o nazwie . Ciąg `rwxr-xrw-` daje użytkownikowi właścicielem uprawnienia do odczytu, zapisu i wykonywania, daje grupie posiadającej tylko uprawnienia do odczytu i wykonywania i daje wszystkim innym uprawnienia do odczytu i zapisu.

```python
def manage_file_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_file_permissions = "rwxr-xrw-"
        
        file_client.set_access_control(permissions=new_file_permissions)
        
        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e) 
```

## <a name="download-from-a-directory"></a>Pobieranie z katalogu 

Otwórz plik lokalny do zapisania. Następnie utwórz **wystąpienie DataLakeFileClient,** które reprezentuje plik, który chcesz pobrać. Wywołanie **DataLakeFileClient.read_file** odczytu bajtów z pliku, a następnie zapisać te bajty do pliku lokalnego. 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        download = file_client.download_file()

        downloaded_bytes = download.readall()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## <a name="list-directory-contents"></a>Wyświetlanie zawartości katalogu

Lista zawartości katalogu przez wywołanie **FileSystemClient.get_paths** metody, a następnie wyliczanie za pomocą wyników.

W tym przykładzie można wydrukować ścieżkę każdego podkatalogu `my-directory`i pliku znajdującego się w katalogu o nazwie .

```python
def list_directory_contents():
    try:
        
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        paths = file_system_client.get_paths(path="my-directory")

        for path in paths:
            print(path.name + '\n')

    except Exception as e:
     print(e) 
```

## <a name="see-also"></a>Zobacz też

* [Dokumentacja referencyjna interfejsu API](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html)
* [Pakiet (indeks pakietu Python)](https://pypi.org/project/azure-storage-file-datalake/)
* [Samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Mapowanie gen1 do gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Znane problemy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Przekaż opinię](https://github.com/Azure/azure-sdk-for-python/issues)
