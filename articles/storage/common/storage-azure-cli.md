---
title: Przy użyciu wiersza polecenia platformy Azure z usługą Azure Storage | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure (Azure CLI) usługi Azure Storage do tworzenia i zarządzania kontami magazynu i pracy z plikami i obiekty BLOB platformy Azure.
services: storage
author: tamram
ms.service: storage
ms.devlang: azurecli
ms.topic: article
ms.date: 06/02/2017
ms.author: tamram
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: ea7e4757aac0fccf60a44c70e9de6a63c1ec9498
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65147005"
---
# <a name="using-the-azure-cli-with-azure-storage"></a>Używanie interfejsu wiersza polecenia platformy Azure z usługą Azure Storage

Open source, Międzyplatformowe wiersza polecenia platformy Azure udostępnia zestaw poleceń do pracy z platformą Azure. Zapewnia wiele funkcji w [witryny Azure portal](https://portal.azure.com), łącznie z dostępem do zaawansowanych danych.

W tym przewodniku pokazujemy, jak używać [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) wykonać kilka czynności, pracy z zasobami w ramach konta usługi Azure Storage. Zaleca się pobrać i zainstalować lub uaktualnić do najnowszej wersji interfejsu wiersza polecenia, przed rozpoczęciem korzystania z tego przewodnika.

W przykładach w przewodniku założono korzystanie z powłoki Bash w systemie Ubuntu, ale innych platformach, należy wykonać w podobny sposób. 

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym przewodniku założono, że rozumiesz podstawowe pojęcia dotyczące usługi Azure Storage. Przyjęto również założenie, że jesteś w stanie spełnić wymagania dotyczące tworzenia konta, które zostały wyszczególnione poniżej dla platformy Azure i usługi Storage.

### <a name="accounts"></a>Konta
* **Konto platformy Azure**: Jeśli nie masz jeszcze subskrypcji platformy Azure, [Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* **Konto magazynu**: Zobacz [Tworzenie konta magazynu](storage-quickstart-create-account.md) w [kontach magazynu Azure o](storage-create-storage-account.md).

### <a name="install-the-azure-cli"></a>Zainstaluj interfejs wiersza polecenia platformy Azure

Pobierz i zainstaluj wiersza polecenia platformy Azure, wykonując instrukcje opisane w temacie [zainstalować interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2).

> [!TIP]
> Jeśli masz problemy z instalacją, zapoznaj się z [Rozwiązywanie problemów z instalacją](/cli/azure/install-az-cli2) części tego artykułu oraz [zainstalować Rozwiązywanie problemów z](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md) przewodnik w witrynie GitHub.
>

## <a name="working-with-the-cli"></a>Praca z interfejsu wiersza polecenia

Po zainstalowaniu interfejsu wiersza polecenia, można użyć `az` polecenia w interfejsie wiersza polecenia (powłoki Bash, Terminal, wiersza polecenia), aby uzyskać dostęp do poleceń interfejsu wiersza polecenia platformy Azure. Typ `az` polecenie, aby wyświetlić pełną listę podstawowych poleceń (następujące przykładowe dane wyjściowe zostały obcięte):

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

W interfejsie wiersza polecenia, wykonaj polecenie `az storage --help` do listy `storage` polecenia podgrupy. Opisy podgrupy omówiono jej funkcje, które wiersza polecenia platformy Azure umożliwia dostęp do pracy z zasobami magazynu.

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

## <a name="connect-the-cli-to-your-azure-subscription"></a>Połącz interfejs wiersza polecenia do subskrypcji platformy Azure

Aby pracować z zasobami w Twojej subskrypcji platformy Azure, musi pierwszego logowania do konta platformy Azure przy użyciu `az login`. Istnieje kilka sposobów, które możesz się zalogować:

* **Logowania interakcyjnego**: `az login`
* **Zaloguj się przy użyciu nazwy użytkownika i hasła**: `az login -u johndoe@contoso.com -p VerySecret`
  * To nie zadziała za pomocą konta Microsoft lub konta, które korzystają z uwierzytelniania Multi-Factor Authentication.
* **Zaloguj się przy użyciu nazwy głównej usługi**: `az login --service-principal -u http://azure-cli-2016-08-05-14-31-15 -p VerySecret --tenant contoso.onmicrosoft.com`

## <a name="azure-cli-sample-script"></a>Skrypt przykładowy interfejsu wiersza polecenia platformy Azure

Następnie możemy będziesz pracować skrypt powłoki małe, który wystawia kilka podstawowych poleceń interfejsu wiersza polecenia platformy Azure do interakcji z zasobami usługi Azure Storage. Skrypt najpierw tworzy nowy kontener na koncie magazynu, a następnie przekazuje do tego kontenera istniejący plik (jako obiekt blob). Następnie wyświetla listę wszystkich obiektów blob w kontenerze, a na koniec pobiera plik do miejsca docelowego na komputerze lokalnym, który określisz.

```bash
#!/bin/bash
# A simple Azure Storage example script

export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_KEY=<storage_account_key>

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

1. Otwórz swoim ulubionym edytorze tekstów, a następnie skopiuj i Wklej powyższy skrypt w edytorze.

2. Następnie zaktualizuj zmiennych skryptu w celu odzwierciedlenia ustawień konfiguracji. Zastąp następujące wartości, jak określono:

   * **\<storage_account_name\>**  nazwę konta magazynu.
   * **\<storage_account_key\>**  klucz podstawowy lub pomocniczy dostępu do konta magazynu.
   * **\<container_name\>**  A nazwę nowego kontenera, aby utworzyć, takie jak "azure-interfejsu wiersza polecenia — przykładowy container".
   * **\<blob_name\>**  nazwę docelowego obiektu blob w kontenerze.
   * **\<file_to_upload\>**  ścieżkę do małych plików na komputerze lokalnym, takie jak "~ / images/HelloWorld.png".
   * **\<destination_file\>**  miejsce docelowe pliku ścieżkę, taką jak "~ / downloadedImage.png".

3. Po zaktualizowaniu niezbędne zmiennych, Zapisz skrypt i zamknąć Edytor. Kolejne kroki zakładają nazwanego skryptu **my_storage_sample.sh**.

4. Jeśli to konieczne, należy oznaczyć skrypt jako plik wykonywalny: `chmod +x my_storage_sample.sh`

5. Uruchom skrypt. Na przykład w powłoce Bash: `./my_storage_sample.sh`

Dane wyjściowe podobne do następujących, powinny być i **\<destination_file\>** podana w skrypcie powinien pojawić się na komputerze lokalnym.

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
> Dane wyjściowe poprzedniego znajduje się w **tabeli** formatu. Można określić, której dane wyjściowe format używany przez określenie `--output` argument w poleceniach interfejsu wiersza polecenia, lub ustaw go przy użyciu `az configure`.
>

## <a name="manage-storage-accounts"></a>Zarządzanie kontami magazynu

### <a name="create-a-new-storage-account"></a>Tworzenie nowego konta magazynu
Aby móc użyć usługi Azure Storage, musisz mieć konto magazynu. Można utworzyć nowe konto usługi Azure Storage, po skonfigurowaniu komputera, aby nawiązać połączenie z subskrypcją.

```azurecli
az storage account create \
    --location <location> \
    --name <account_name> \
    --resource-group <resource_group> \
    --sku <account_sku>
```

* `--location` [Wymagana]: Lokalizacja. Na przykład "West US".
* `--name` [Wymagana]: Nazwa konta magazynu. Nazwa musi mieć długość 3 do 24 znaków i zawierać tylko małe znaki alfanumeryczne.
* `--resource-group` [Wymagana]: Nazwa grupy zasobów.
* `--sku` [Wymagana]: Jednostki SKU konta magazynu. Dozwolone wartości:
  * `Premium_LRS`
  * `Standard_GRS`
  * `Standard_LRS`
  * `Standard_RAGRS`
  * `Standard_ZRS`

### <a name="set-default-azure-storage-account-environment-variables"></a>Zmienne środowiskowe domyślne konto magazynu platformy Azure

Może mieć wielu kont magazynu w ramach subskrypcji platformy Azure. Aby wybrać jeden z nich do użycia dla wszystkich poleceń kolejnych magazynu, można ustawić zmienne środowiskowe:

Najpierw wyświetl klucze konta magazynu przy użyciu polecenia [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive
az storage account keys list \
    --account-name <account_name> \
    --resource-group <resource_group> \
    --output table
```

Teraz, gdy klucz, jako zmienne środowiskowe można zdefiniować ją i nazwy konta:

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_KEY=<key>
```

Innym sposobem, aby ustawić domyślne konto magazynu jest przy użyciu parametrów połączenia. Najpierw Pobierz parametry połączenia o `show-connection-string` polecenia:

```azurecli
az storage account show-connection-string \
    --name <account_name> \
    --resource-group <resource_group>
```

Następnie skopiuj parametry połączenia danych wyjściowych i ustaw `AZURE_STORAGE_CONNECTION_STRING` zmienna środowiskowa (może być konieczne parametry połączenia, należy ująć w cudzysłów):

```azurecli
export AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
```

> [!NOTE]
> Wszystkie przykłady w poniższych sekcjach tego artykułu przyjęto założenie, że ustawiono `AZURE_STORAGE_ACCOUNT` i `AZURE_STORAGE_KEY` zmiennych środowiskowych.

## <a name="create-and-manage-blobs"></a>Tworzenie i zarządzanie obiektami blob
Usługa Azure Blob storage jest usługą służącą do przechowywania dużych ilości danych niestrukturalnych, takich jak dane tekstowe lub binarne, które są dostępne z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. W tej sekcji założono, że znasz już pojęcia magazynu obiektów Blob platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [wprowadzenie do usługi Azure Blob storage przy użyciu platformy .NET](../blobs/storage-dotnet-how-to-use-blobs.md) i [pojęcia dotyczące usługi Blob](/rest/api/storageservices/blob-service-concepts).

### <a name="create-a-container"></a>Tworzenie kontenera
Każdy obiekt blob w usłudze Azure storage musi być w kontenerze. Kontener można utworzyć za pomocą `az storage container create` polecenia:

```azurecli
az storage container create --name <container_name>
```

Można ustawić jeden z trzech poziomów dostępu do odczytu dla nowego kontenera, określając opcjonalnego `--public-access` argumentu:

* `off` (ustawienie domyślne): Dane w kontenerze są prywatne dla właściciela konta.
* `blob`: Publiczny dostęp do odczytu dla obiektów blob.
* `container`: Publicznego dostępu do odczytu i listy dla całego kontenera.

Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem anonimowym w trybie odczytu do kontenerów i obiektów blob](../blobs/storage-manage-access-to-resources.md).

### <a name="upload-a-blob-to-a-container"></a>Przekazywanie obiektu blob do kontenera
Usługa Azure Blob storage obsługuje bloku, Dołącz i stronicowe obiekty BLOB. Przekazywanie obiektów blob do kontenera przy użyciu `blob upload` polecenia:

```azurecli
az storage blob upload \
    --file <local_file_path> \
    --container-name <container_name> \
    --name <blob_name>
```

Jeśli chcesz przekazać bezpośrednio do folderu w kontenerze na koncie magazynu, należy zastąpić `--name <blob_name>` z `--name <folder/blob_name>`.

 Domyślnie `blob upload` polecenia przekazuje pliki *.vhd do stronicowych obiektów blob lub blokowych obiektów blob w przeciwnym razie. Aby określić inny typ, jeśli przekażesz obiekt blob, można użyć `--type` — dozwolone wartości należą `append`, `block`, i `page`.

 Aby uzyskać więcej informacji na temat typów różnych obiektów blob, zobacz [omówienie blokowych obiektów blob, Uzupełnialnych obiektów blob i stronicowe obiekty BLOB](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).


### <a name="download-a-blob-from-a-container"></a>Pobieranie obiektu blob z kontenera
W tym przykładzie pokazano, jak pobrać obiekt blob z kontenera:

```azurecli
az storage blob download \
    --container-name mycontainer \
    --name myblob.png \
    --file ~/mydownloadedblob.png
```

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Wyświetlanie listy obiektów blob w kontenerze za pomocą [az storage blob list](/cli/azure/storage/blob) polecenia.

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

W powyższym przykładzie kontener docelowy musi już istnieć w docelowym koncie magazynu jako warunek powodzenia operacji kopiowania. Ponadto źródłowy obiekt blob wskazany za pomocą argumentu `--source-uri` musi zawierać token sygnatury dostępu współdzielonego (SAS) albo musi być dostępny publicznie, jak w tym przykładzie.

### <a name="delete-a-blob"></a>Usuwanie obiektu blob
Aby usunąć obiekt blob, użyj `blob delete` polecenia:

```azurecli
az storage blob delete --container-name <container_name> --name <blob_name>
```

## <a name="create-and-manage-file-shares"></a>Tworzenie i Zarządzanie udziałami plików
Usługa Azure Files oferuje współużytkowany magazyn dla aplikacji przy użyciu protokołu bloku komunikatów serwera (SMB). Maszyny wirtualne Microsoft Azure i usług w chmurze, jak również aplikacje lokalne mogą współużytkować dane plików, za pośrednictwem zainstalowanych udziałów. Możesz zarządzać udziałami plików i danych plików za pomocą wiersza polecenia platformy Azure. Aby uzyskać więcej informacji na temat usługi Azure Files, zobacz [wprowadzenie do usługi Azure Files](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Tworzenie udziału plików
Udział plików platformy Azure jest udziałem plików SMB na platformie Azure. Wszystkie pliki i katalogi, należy utworzyć w udziale plików. Konto może zawierać nieograniczoną liczbę udziałów, a udział może przechowywać nieograniczoną liczbę plików, nieprzekraczającą limitów pojemności konta magazynu. Poniższy przykład tworzy udział plików o nazwie **myshare**.

```azurecli
az storage share create --name myshare
```

### <a name="create-a-directory"></a>Tworzenie katalogu
Katalogu zapewnia strukturę hierarchiczną w udziale plików platformy Azure. Poniższy przykład tworzy katalog o nazwie **myDir** w udziale plików.

```azurecli
az storage directory create --name myDir --share-name myshare
```

Ścieżka katalogu może zawierać wiele poziomów, na przykład **katalog1/dir2**. Jednak należy upewnić się, że wszystkie katalogi nadrzędne istnieją przed utworzeniem podkatalogu. Na przykład ścieżka **katalog1/dir2**, musisz najpierw utworzyć katalog **katalog1**, następnie utwórz katalog **dir2**.

### <a name="upload-a-local-file-to-a-share"></a>Przekazanie pliku lokalnego do udziału
Następujący kod przykładowy przekazuje plik z **~/temp/samplefile.txt** do głównego **myshare** udziału plików. `--source` Argument określa istniejący plik lokalny do przekazania.

```azurecli
az storage file upload --share-name myshare --source ~/temp/samplefile.txt
```

Za pomocą utworzenie katalogu, możesz określić ścieżkę katalogu w udziale można przekazać pliku do istniejącego katalogu w ramach udziału:

```azurecli
az storage file upload --share-name myshare/myDir --source ~/temp/samplefile.txt
```

Plik w udziale można maksymalnie 1 TB.

### <a name="list-the-files-in-a-share"></a>Lista plików w udziale
Możesz wyświetlić listę plików i katalogów w udziale za pomocą `az storage file list` polecenia:

```azurecli
# List the files in the root of a share
az storage file list --share-name myshare --output table

# List the files in a directory within a share
az storage file list --share-name myshare/myDir --output table

# List the files in a path within a share
az storage file list --share-name myshare --path myDir/mySubDir/MySubDir2 --output table
```

### <a name="copy-files"></a>Kopiowanie plików      
Można skopiować pliku do innego pliku, plik do obiektu blob oraz obiekty blob do pliku. Na przykład, aby skopiować plik do katalogu w inny udział:        
        
```azurecli
az storage file copy start \
--source-share share1 --source-path dir1/file.txt \
--destination-share share2 --destination-path dir2/file.txt     
```

## <a name="create-share-snapshot"></a>Tworzenie migawki udziału
Migawkę udziału możesz utworzyć przy użyciu `az storage share snapshot` polecenia:

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

Może zawierać listę migawek udziału przy użyciu określonego udziału `az storage share list --include-snapshots`

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
Możesz także może przejść do określonego udziału, migawki, aby wyświetlić jego zawartości za pomocą `az storage file list`. Trzeba określić nazwę udziału `--share-name <snare name>` i sygnatura czasowa `--snapshot '2017-10-04T19:45:18.0000000Z'`

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

Można przywrócić plik, kopiując lub pobieranie pliku z udziału migawki za pomocą `az storage file download` polecenia

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
## <a name="delete-share-snapshot"></a>Usuwanie migawki udziału
Migawkę udziału możesz usunąć za pomocą `az storage share delete` polecenie, podając `--snapshot` parametru z sygnaturą czasową migawki udziału:

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
Poniżej przedstawiono niektóre dodatkowe zasoby dotyczące dowiedzieć się więcej na temat pracy z interfejsem wiersza polecenia platformy Azure. 

* [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Dokumentacja polecenia interfejsu wiersza polecenia platformy Azure](/cli/azure)
* [Wiersza polecenia platformy Azure w witrynie GitHub](https://github.com/Azure/azure-cli)
