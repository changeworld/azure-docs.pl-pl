---
title: Za pomocą usługi Azure CLI 2.0 z usługą Azure Storage | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure (Azure CLI) 2.0, z usługą Azure Storage, aby utworzyć i zarządzać kontami magazynu i pracy z plikami i obiekty BLOB platformy Azure. Azure CLI 2.0 to narzędzie i platform napisanych w języku Python.
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 06/02/2017
ms.author: rogarana
ms.openlocfilehash: 68e101ebec4a90d8c0f39eedeef33d252c720ed1
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737372"
---
# <a name="using-the-azure-cli-20-with-azure-storage"></a>Za pomocą usługi Azure CLI 2.0 z usługą Azure Storage

Open source, obsługujący wiele platform 2.0 interfejsu wiersza polecenia Azure udostępnia zestaw poleceń do pracy z platformą Azure. Zapewnia wiele funkcji w [portalu Azure](https://portal.azure.com), w tym dostęp do zaawansowanych danych.

W tym przewodniku zostanie przedstawiony zostanie sposób użycia [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) wykonać kilka czynności, Praca z zasobów na koncie magazynu Azure. Firma Microsoft zaleca, Pobierz i zainstaluj lub uaktualnienie do najnowszej wersji 2.0 interfejsu wiersza polecenia przed przy użyciu tego przewodnika.

Przykłady w podręczniku założono korzystanie z powłoki Bash na Ubuntu, ale innych platform, należy wykonać podobnie. 

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym przewodniku założono, że rozumiesz podstawowe pojęcia dotyczące środowiska usługi Azure Storage. Przyjęto założenie, że jesteś w stanie spełnić wymagania dotyczące tworzenia konta, określonych poniżej platformy Azure i usługi magazynu.

### <a name="accounts"></a>Konta
* **Konto platformy Azure**: Jeśli nie masz już subskrypcję platformy Azure, [utworzyć bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* **Konto magazynu**: zobacz sekcję [Tworzenie konta magazynu](storage-create-storage-account.md#create-a-storage-account) w temacie [Informacje o kontach magazynu Azure](storage-create-storage-account.md).

### <a name="install-the-azure-cli-20"></a>Instalacja interfejsu wiersza polecenia platformy Azure 2.0

Pobierz i zainstaluj 2.0 interfejsu wiersza polecenia platformy Azure, wykonując czynności opisane w [zainstalować Azure CLI 2.0](/cli/azure/install-az-cli2).

> [!TIP]
> Jeśli masz problemy z instalacją, zapoznaj się [instalacji Rozwiązywanie problemów z](/cli/azure/install-az-cli2#installation-troubleshooting) sekcji tego artykułu oraz [zainstalować Rozwiązywanie problemów z](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md) przewodnik w witrynie GitHub.
>

## <a name="working-with-the-cli"></a>Praca z poziomu interfejsu wiersza polecenia

Po zainstalowaniu interfejsu wiersza polecenia, można użyć `az` polecenia w interfejsie wiersza polecenia (Bash, Terminal, wiersza polecenia) do poleceń wiersza polecenia platformy Azure. Typ `az` polecenie, aby wyświetlić pełną listę poleceń podstawowej (następujące przykładowe dane wyjściowe zostały obcięte):

```
     /\
    /  \    _____   _ _ __ ___
   / /\ \  |_  / | | | \'__/ _ \
  / ____ \  / /| |_| | | |  __/
 /_/    \_\/___|\__,_|_|  \___|


Welcome to the cool new Azure CLI!

Here are the base commands:

    account          : Manage subscriptions.
    acr              : Manage Azure container registries.
    acs              : Manage Azure Container Services.
    ad               : Synchronize on-premises directories and manage Azure Active Directory
                       resources.
    ...
```

W interfejsie wiersza polecenia, wykonaj polecenie `az storage --help` do listy `storage` polecenia podgrup. Opisy podgrupy, które zawierają omówienie funkcji, który zapewnia wiersza polecenia platformy Azure do pracy z zasobami magazynu.

```
Group
    az storage: Durable, highly available, and massively scalable cloud storage.

Subgroups:
    account  : Manage storage accounts.
    blob     : Object storage for unstructured data.
    container: Manage blob storage containers.
    cors     : Manage Storage service Cross-Origin Resource Sharing (CORS).
    directory: Manage file storage directories.
    entity   : Manage table storage entities.
    file     : File shares that use the standard SMB 3.0 protocol.
    logging  : Manage Storage service logging information.
    message  : Manage queue storage messages.
    metrics  : Manage Storage service metrics.
    queue    : Use queues to effectively scale applications according to traffic.
    share    : Manage file shares.
    table    : NoSQL key-value storage using semi-structured datasets.
```

## <a name="connect-the-cli-to-your-azure-subscription"></a>Interfejsu wiersza polecenia nawiązać połączenia z subskrypcją platformy Azure

Do pracy z zasobami w Twojej subskrypcji platformy Azure, musi najpierw logujesz się do konta platformy Azure z `az login`. Istnieje kilka metod, które możesz zalogować się:

* **Logowanie interakcyjne**: `az login`
* **Zaloguj się przy użyciu nazwy użytkownika i hasła**: `az login -u johndoe@contoso.com -p VerySecret`
  * To nie działa z konta Microsoft lub konta, które korzystają z uwierzytelniania wieloskładnikowego.
* **Zaloguj się przy użyciu nazwy głównej usługi**: `az login --service-principal -u http://azure-cli-2016-08-05-14-31-15 -p VerySecret --tenant contoso.onmicrosoft.com`

## <a name="azure-cli-20-sample-script"></a>Azure CLI 2.0 przykładowy skrypt

Następnie będzie pracujemy ze skryptem małych powłoki, który wystawia kilka podstawowych poleceń Azure CLI 2.0 do interakcji z zasobami magazynu platformy Azure. Skrypt najpierw tworzy nowy kontener na koncie magazynu, a następnie przekazuje istniejącego pliku (jako obiektu blob) do tego kontenera. Wyświetla listę wszystkich obiektów blob w kontenerze, a na koniec pobierze plik do miejsca docelowego na komputerze lokalnym, który określisz.

```bash
#!/bin/bash
# A simple Azure Storage example script

export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

export container_name=<container_name>
export blob_name=<blob_name>
export file_to_upload=<file_to_upload>
export destination_file=<destination_file>

echo "Creating the container..."
az storage container create --name $container_name

echo "Uploading the file..."
az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name

echo "Listing the blobs..."
az storage blob list --container-name $container_name --output table

echo "Downloading the file..."
az storage blob download --container-name $container_name --name $blob_name --file $destination_file --output table

echo "Done"
```

**Skonfiguruj i uruchom skrypt**

1. Otwórz w ulubionym edytorze tekstów, a następnie skopiuj i Wklej powyższy skrypt w edytorze.

2. Następnie zaktualizuj zmienne skryptu, aby odzwierciedlić ustawień konfiguracji. Zastąp następujące wartości określonych:

   * **\<storage_account_name\>**  nazwę konta magazynu.
   * **\<storage_account_key\>**  klucz podstawowy lub pomocniczy dostępu dla konta magazynu.
   * **\<container_name\>**  A Nazwa nowego kontenera, aby utworzyć, takie jak "azure-cli — próbki container".
   * **\<blob_name\>**  nazwę docelowego obiektu blob w kontenerze.
   * **\<file_to_upload\>**  ścieżkę do małych plików na komputerze lokalnym, takich jak "~ / images/HelloWorld.png".
   * **\<destination_file\>**  docelowego pliku ścieżki, takie jak "~ / downloadedImage.png".

3. Po zaktualizowaniu niezbędne zmienne zapisać skrypt, a następnie zamknij Edytor. Następnych krokach założono nazwanego skryptu **my_storage_sample.sh**.

4. Należy oznaczyć skrypt jako plik wykonywalny, w razie potrzeby: `chmod +x my_storage_sample.sh`

5. Uruchom skrypt. Na przykład w Bash: `./my_storage_sample.sh`

Powinny pojawić się dane wyjściowe podobne do następujących i **\<destination_file\>** podana w skrypcie powinien zostać wyświetlony na komputerze lokalnym.

```
Creating the container...
{
  "created": true
}
Uploading the file...
Percent complete: %100.0
Listing the blobs...
Name       Blob Type      Length  Content Type              Last Modified
---------  -----------  --------  ------------------------  -------------------------
README.md  BlockBlob        6700  application/octet-stream  2017-05-12T20:54:59+00:00
Downloading the file...
Name
---------
README.md
Done
```

> [!TIP]
> Powyższy wynik jest **tabeli** format. Można określić formatu do użycia przez określenie wyjściowy, który `--output` argument w poleceniach interfejsu wiersza polecenia lub ustaw ją za pomocą `az configure`.
>

## <a name="manage-storage-accounts"></a>Zarządzanie kontami magazynu

### <a name="create-a-new-storage-account"></a>Tworzenie nowego konta magazynu
Aby móc użyć usługi Azure Storage, musisz mieć konto magazynu. Po skonfigurowaniu komputera, można utworzyć nowe konto magazynu Azure [nawiązać połączenia z subskrypcją](#connect-to-your-azure-subscription).

```azurecli
az storage account create \
    --location <location> \
    --name <account_name> \
    --resource-group <resource_group> \
    --sku <account_sku>
```

* `--location` [Wymagane]: lokalizacji. Na przykład "zachodnie stany USA".
* `--name` [Wymagane]: Nazwa konta magazynu. Nazwa musi składać się z 3 do 24 znaków i zawierać tylko małe znaki alfanumeryczne.
* `--resource-group` [Wymagane]: Nazwa grupy zasobów.
* `--sku` [Wymagane]: Konto magazynu wersji. Dozwolone wartości:
  * `Premium_LRS`
  * `Standard_GRS`
  * `Standard_LRS`
  * `Standard_RAGRS`
  * `Standard_ZRS`


### <a name="set-default-azure-storage-account-environment-variables"></a>Ustaw zmienne środowiskowe domyślne konto magazynu Azure
Może mieć wielu kont magazynu w ramach subskrypcji platformy Azure. Aby wybrać jeden z nich do użycia dla wszystkich poleceń kolejnych magazynu, można ustawić zmienne środowiskowe:

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Innym sposobem ustawienia domyślne konto magazynu jest przy użyciu ciągu połączenia. Najpierw należy pobrać ciągu połączenia z `show-connection-string` polecenia:

```azurecli
az storage account show-connection-string \
    --name <account_name> \
    --resource-group <resource_group>
```

Następnie skopiuj parametry połączenia danych wyjściowych i ustaw `AZURE_STORAGE_CONNECTION_STRING` zmiennej środowiskowej (może być konieczne parametry połączenia, należy ująć w cudzysłowy):

```azurecli
export AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
```

> [!NOTE]
> Wszystkie przykłady w następujących sekcjach w tym artykule założono, że ustawiono `AZURE_STORAGE_ACCOUNT` i `AZURE_STORAGE_ACCESS_KEY` zmiennych środowiskowych.
>

## <a name="create-and-manage-blobs"></a>Tworzenie i zarządzanie nimi obiektów blob
Magazyn obiektów Blob Azure to usługa do przechowywania dużych ilości danych bez struktury, takich jak dane tekstowe lub binarne, którego mogą uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. W tej sekcji założono, że znasz już pojęcia dotyczące magazynu obiektów Blob platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z magazynem obiektów Blob platformy Azure przy użyciu platformy .NET](../blobs/storage-dotnet-how-to-use-blobs.md) i [pojęcia dotyczące usługi Blob](/rest/api/storageservices/blob-service-concepts).

### <a name="create-a-container"></a>Tworzenie kontenera
Każdy obiekt blob w magazynie Azure musi być w kontenerze. Kontener można utworzyć przy użyciu `az storage container create` polecenia:

```azurecli
az storage container create --name <container_name>
```

Można ustawić jedną z trzech poziomów dostępu do odczytu dla nowego kontenera, określając opcjonalny `--public-access` argumentu:

* `off` (domyślnie): dane w kontenerze są prywatne dla właściciela konta.
* `blob`: Publiczny dostęp do odczytu obiektów blob.
* `container`: Publiczny dostęp do odczytu i listy do całego kontenera.

Aby uzyskać więcej informacji, zobacz [Zarządzanie anonimowy dostęp do odczytu do kontenerów i obiektów blob](../blobs/storage-manage-access-to-resources.md).

### <a name="upload-a-blob-to-a-container"></a>Przekazywanie obiektu blob do kontenera
Azure Blob storage obsługuje blok, Dołącz i stronicowe. Przekaż obiekty BLOB do kontenera za pomocą `blob upload` polecenia:

```azurecli
az storage blob upload \
    --file <local_file_path> \
    --container-name <container_name> \
    --name <blob_name>
```

 Domyślnie `blob upload` polecenie wysyła pliki *.vhd stronicowych obiektów blob lub blokowych obiektów blob w inny sposób. Aby określić inny typ podczas ładowania obiektu blob, można użyć `--type` argumentu--dozwolone wartości są `append`, `block`, i `page`.

 Aby uzyskać więcej informacji o typach różnych obiektów blob, zobacz [opis blokowych obiektów blob, Dołącz obiektów blob i stronicowe obiekty BLOB](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).


### <a name="download-a-blob-from-a-container"></a>Pobieranie obiektu blob z kontenera
W tym przykładzie pokazano, jak pobrać obiektu blob z kontenera:

```azurecli
az storage blob download \
    --container-name mycontainer \
    --name myblob.png \
    --file ~/mydownloadedblob.png
```

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Wyświetlanie obiektów blob w kontenerze o [az magazynu obiektów blob listy](/cli/azure/storage/blob#az_storage_blob_list) polecenia.

```azurecli
az storage blob list \
    --container-name mycontainer \
    --output table
```

### <a name="copy-blobs"></a>Kopiowanie obiektów blob
Można asynchronicznie kopiować obiekty blob w obrębie konta magazynu i regionu lub między różnymi kontami magazynu i regionami.

W poniższym przykładzie pokazano sposób kopiowania obiektów blob z jednego konta magazynu do innego. Najpierw trzeba utworzyć kontener na źródłowym koncie magazynu, określając publiczny dostęp do odczytu do jego obiektów blob. Następnie plik zostanie przekazany do kontenera, a potem obiekt blob z tego kontenera zostanie skopiowany do kontenera na docelowym koncie magazynu.

```azurecli
# Create container in source account
az storage container create \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --name sourcecontainer \
    --public-access blob

# Upload blob to container in source account
az storage blob upload \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --container-name sourcecontainer \
    --file ~/Pictures/sourcefile.png \
    --name sourcefile.png

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

W powyższym przykładzie kontener docelowy musi już istnieć na koncie magazynu docelowy operacji kopiowania powiodła się. Ponadto źródłowy obiekt blob wskazany za pomocą argumentu `--source-uri` musi zawierać token sygnatury dostępu współdzielonego (SAS) albo musi być dostępny publicznie, jak w tym przykładzie.

### <a name="delete-a-blob"></a>Usuwanie obiektu blob
Aby usunąć obiekt blob, użyj `blob delete` polecenia:

```azurecli
az storage blob delete --container-name <container_name> --name <blob_name>
```

## <a name="create-and-manage-file-shares"></a>Tworzenie i Zarządzanie udziałami plików
Usługa pliki Azure oferuje współużytkowany magazyn dla aplikacji używających protokołu bloku komunikatów serwera (SMB). Maszyny wirtualne Microsoft Azure i usługi w chmurze, a także aplikacje lokalne mogą udostępniać dane za pośrednictwem zainstalowanych udziałów. Możesz zarządzać udziałami plików i danych plików za pomocą wiersza polecenia platformy Azure. Aby uzyskać więcej informacji na pliki Azure, zobacz [wprowadzenie do usługi pliki Azure](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Tworzenie udziału plików
Udział plików na platformę Azure jest udziałem plików SMB na platformie Azure. Wszystkie pliki i katalogi, należy utworzyć w udziale plików. Konto może zawierać nieograniczoną liczbę udziałów, a udział może przechowywać nieograniczoną liczbę plików do osiągnięcia limitu pojemności konta magazynu. Poniższy przykład tworzy udział plików o nazwie **moj_udzial**.

```azurecli
az storage share create --name myshare
```

### <a name="create-a-directory"></a>Tworzenie katalogu
Katalog udostępnia strukturę hierarchiczną w udziale plików na platformę Azure. Poniższy przykład tworzy katalog o nazwie **myDir** w udziale plików.

```azurecli
az storage directory create --name myDir --share-name myshare
```

Ścieżki katalogu może zawierać wiele poziomów, na przykład **katalog1/dir2**. Należy jednak upewnij się, że wszystkie katalogi nadrzędne istnieć przed utworzeniem podkatalogu. Na przykład dla ścieżki **katalog1/dir2**, należy najpierw utworzyć katalog **katalog1**, następnie utwórz katalog **dir2**.

### <a name="upload-a-local-file-to-a-share"></a>Przekazanie pliku lokalnego do udziału
Poniższy przykład przekazuje plik z **~/temp/samplefile.txt** do głównego **moj_udzial** udziału plików. `--source` Argument określa istniejący plik lokalny do przekazania.

```azurecli
az storage file upload --share-name myshare --source ~/temp/samplefile.txt
```

Jako o utworzenie katalogu, można określić ścieżkę katalogu w udziale można przekazać pliku do istniejącego katalogu w obrębie udziału:

```azurecli
az storage file upload --share-name myshare/myDir --source ~/temp/samplefile.txt
```

Plik w udziale może być o rozmiarze do 1 TB.

### <a name="list-the-files-in-a-share"></a>Lista plików w udziale
Można wyświetlić listę plików i katalogów w udziale, przy użyciu `az storage file list` polecenia:

```azurecli
# List the files in the root of a share
az storage file list --share-name myshare --output table

# List the files in a directory within a share
az storage file list --share-name myshare/myDir --output table

# List the files in a path within a share
az storage file list --share-name myshare --path myDir/mySubDir/MySubDir2 --output table
```

### <a name="copy-files"></a>Kopiowanie plików      
Można skopiować pliku do innego pliku, pliki do obiektu blob, obiekty blob do pliku. Na przykład, aby skopiować plik do katalogu w inny udział:        
        
```azurecli
az storage file copy start \
--source-share share1 --source-path dir1/file.txt \
--destination-share share2 --destination-path dir2/file.txt     
```

## <a name="create-share-snapshot"></a>Tworzenie migawki udziału
Można utworzyć migawki udziału za pomocą `az storage share snapshot` polecenia:

```cli
az storage share snapshot -n <share name>
```

Przykładowe dane wyjściowe
```json
{
  "metadata": {},
  "name": "<share name>",
  "properties": {
    "etag": "\"0x8D50B7F9A8D7F30\"",
    "lastModified": "2017-10-04T23:28:22+00:00",
    "quota": null
  },
  "snapshot": "2017-10-04T23:28:35.0000000Z"
}
```

### <a name="list-share-snapshots"></a>Wyświetlanie listy migawek udziałów

Możesz podać migawek udział w udziale określonym za pomocą `az storage share list --include-snapshots`

```cli
az storage share list --include-snapshots
```

**Przykładowe dane wyjściowe**
```json
[
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:44:13.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:45:18.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": null
  }
]
```

### <a name="browse-share-snapshots"></a>Przeglądanie migawek udziałów
Może również przeglądania do migawki, aby wyświetlić jego zawartości przy użyciu akcji `az storage file list`. Trzeba określić nazwę udziału `--share-name <snare name>` i znacznik czasu `--snapshot '2017-10-04T19:45:18.0000000Z'`

```azurecli-interactive
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

**Przykładowe dane wyjściowe**
```
Name            Content Length    Type    Last Modified
--------------  ----------------  ------  ---------------
HelloWorldDir/                    dir
IMG_0966.JPG    533568            file
IMG_1105.JPG    717711            file
IMG_1341.JPG    608459            file
IMG_1405.JPG    652156            file
IMG_1611.JPG    442671            file
IMG_1634.JPG    1495999           file
IMG_1635.JPG    974058            file

```
### <a name="restore-from-share-snapshots"></a>Przywracanie z migawki udziału

Można przywrócić plik, kopiując lub pobierania pliku z udziału migawki za pomocą `az storage file download` polecenia

```azurecli-interactive
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```
**Przykładowe dane wyjściowe**
```
{
  "content": null,
  "metadata": {},
  "name": "IMG_0966.JPG",
  "properties": {
    "contentLength": 533568,
    "contentRange": "bytes 0-533567/533568",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentType": "application/octet-stream"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D50B5F49F7ACDF\"",
    "lastModified": "2017-10-04T19:37:03+00:00",
    "serverEncrypted": true
  }
}
```
## <a name="delete-share-snapshot"></a>Usuń migawki udziału
Można usunąć migawki udziału za pomocą `az storage share delete` polecenia zapewniając `--snapshot` parametr ze znacznikiem czasu migawki udziału:

```cli
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Przykładowe dane wyjściowe
```json
{
  "deleted": true
}
```

## <a name="next-steps"></a>Kolejne kroki
Poniżej przedstawiono dodatkowe zasoby dowiedzieć się więcej o pracy z 2.0 interfejsu wiersza polecenia platformy Azure.

* [Wprowadzenie do usługi Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Dokumentacja poleceń interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure)
* [Azure CLI 2.0 w witrynie GitHub](https://github.com/Azure/azure-cli)
