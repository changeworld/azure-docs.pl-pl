---
title: Używanie interfejsu wiersza polecenia platformy Azure z usługą Azure Storage
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure (Azure CLI) z usługą Azure Storage, aby tworzyć i zarządzać kontami magazynu oraz korzystać z obiektów blob i plików platformy Azure.
services: storage
author: tamram
ms.service: storage
ms.devlang: azurecli
ms.topic: article
ms.date: 06/02/2017
ms.author: tamram
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: f8e745b214ced865ac41d72bdfd5e44ca36b803a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460459"
---
# <a name="using-the-azure-cli-with-azure-storage"></a>Używanie interfejsu wiersza polecenia platformy Azure z usługą Azure Storage

Międzyplatformowy interfejs wiersza polecenia platformy Azure typu open source udostępnia zestaw poleceń do pracy z platformą Azure. Zapewnia to wiele funkcji dostępnych w [Azure Portal](https://portal.azure.com), w tym zaawansowanego dostępu do danych.

W tym przewodniku pokazano, jak za pomocą [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) wykonywać kilka zadań pracujących z zasobami na koncie usługi Azure Storage. Zalecamy pobranie i zainstalowanie lub uaktualnienie do najnowszej wersji interfejsu wiersza polecenia przed rozpoczęciem korzystania z tego przewodnika.

W przykładach w przewodniku założono użycie powłoki bash w Ubuntu, ale inne platformy powinny działać podobnie. 

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym przewodniku założono, że rozumiesz podstawowe pojęcia związane z usługą Azure Storage. Przyjęto również założenie, że można spełnić wymagania dotyczące tworzenia konta określone poniżej dla platformy Azure i usługi magazynu.

### <a name="accounts"></a>Konta
* **Konto platformy Azure**: Jeśli nie masz jeszcze subskrypcji platformy Azure, [Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* **Konto magazynu**: zobacz sekcję [Tworzenie konta magazynu](storage-quickstart-create-account.md) w temacie [Informacje o kontach magazynu Azure](storage-create-storage-account.md).

### <a name="install-the-azure-cli"></a>Zainstaluj interfejs wiersza polecenia platformy Azure

Pobierz i zainstaluj interfejs wiersza polecenia platformy Azure, postępując zgodnie z instrukcjami podanymi w temacie [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-az-cli2).

> [!TIP]
> Jeśli masz problemy z instalacją, zapoznaj się z sekcją [Rozwiązywanie problemów z instalacją](/cli/azure/install-az-cli2) artykułu i przewodnikiem [rozwiązywania problemów](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md) w witrynie GitHub.
>

## <a name="working-with-the-cli"></a>Praca z interfejsem wiersza polecenia

Po zainstalowaniu interfejsu wiersza polecenia można użyć `az` polecenie w interfejsie użytkownika (bash, Terminal, wiersz polecenia), aby uzyskać dostęp do polecenia interfejsu wiersza polecenia platformy Azure. Wpisz `az` polecenie, aby wyświetlić pełną listę podstawowych poleceń (następujące przykładowe dane wyjściowe zostały obcięte):

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

W interfejsie wiersza polecenia wykonaj polecenie `az storage --help`, aby wyświetlić podgrupy poleceń `storage`. Opisy podgrup zawierają przegląd funkcji dostępnych w interfejsie wiersza polecenia platformy Azure do pracy z zasobami magazynu.

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

## <a name="connect-the-cli-to-your-azure-subscription"></a>Łączenie interfejsu wiersza polecenia z subskrypcją platformy Azure

Aby móc korzystać z zasobów w ramach subskrypcji platformy Azure, musisz najpierw zalogować się do konta platformy Azure przy użyciu `az login`. Istnieje kilka sposobów logowania:

* **Logowanie interakcyjne**: `az login`
* **Zaloguj się przy użyciu nazwy użytkownika i hasła**: `az login -u johndoe@contoso.com -p VerySecret`
  * To nie działa z kontami Microsoft i kontami korzystającymi z uwierzytelniania wieloskładnikowego.
* **Zaloguj się przy użyciu nazwy głównej usługi**: `az login --service-principal -u http://azure-cli-2016-08-05-14-31-15 -p VerySecret --tenant contoso.onmicrosoft.com`

## <a name="azure-cli-sample-script"></a>Przykładowy skrypt interfejsu wiersza polecenia platformy Azure

Następnie będziemy współpracować z małym skryptem powłoki, który wystawia kilka podstawowych poleceń interfejsu wiersza polecenia platformy Azure w celu współdziałania z zasobami usługi Azure Storage. Skrypt najpierw tworzy nowy kontener na koncie magazynu, a następnie przekazuje do tego kontenera istniejący plik (jako obiekt blob). Następnie wyświetla listę wszystkich obiektów BLOB w kontenerze, a wreszcie pobiera plik do miejsca docelowego na określonym komputerze lokalnym.

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

**Konfigurowanie i uruchamianie skryptu**

1. Otwórz swój ulubiony Edytor tekstu, a następnie skopiuj i wklej poprzedni skrypt do edytora.

2. Następnie zaktualizuj zmienne skryptu, aby odzwierciedlały ustawienia konfiguracji. Zastąp następujące wartości jako określone:

   * **\<storage_account_name\>** Nazwa konta magazynu.
   * **\<storage_account_key\>** Podstawowy lub pomocniczy klucz dostępu dla konta magazynu.
   * **\<container_name\>** Nazwa nowego kontenera do utworzenia, na przykład "Azure-CLI-Sample-Container".
   * **\<blob_name\>** Nazwa docelowego obiektu BLOB w kontenerze.
   * **\<file_to_upload\>** Ścieżka do małego pliku na komputerze lokalnym, na przykład "~/images/HelloWorld.png".
   * **\<destination_file\>** Ścieżka pliku docelowego, na przykład "~/downloadedImage.png".

3. Po zaktualizowaniu wymaganych zmiennych Zapisz skrypt i wyjdź z edytora. W następnych krokach przyjęto założenie, że nazwa skryptu **my_storage_sample. sh**.

4. Oznacz skrypt jako plik wykonywalny, w razie potrzeby: `chmod +x my_storage_sample.sh`

5. Uruchom skrypt. Na przykład w bash: `./my_storage_sample.sh`

Powinny zostać wyświetlone dane wyjściowe podobne do następujących, a **\<destination_file\>** określony w skrypcie powinien pojawić się na komputerze lokalnym.

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
> Poprzednie dane wyjściowe są w formacie **tabeli** . Można określić, który format danych wyjściowych ma być używany przez określenie argumentu `--output` w poleceń interfejsu wiersza polecenia lub skonfigurować go globalnie przy użyciu `az configure`.
>

## <a name="manage-storage-accounts"></a>Zarządzanie kontami magazynu

### <a name="create-a-new-storage-account"></a>Tworzenie nowego konta magazynu
Aby móc użyć usługi Azure Storage, musisz mieć konto magazynu. Nowe konto usługi Azure Storage można utworzyć po skonfigurowaniu komputera do nawiązania połączenia z subskrypcją.

```azurecli
az storage account create \
    --location <location> \
    --name <account_name> \
    --resource-group <resource_group> \
    --sku <account_sku>
```

* `--location` [wymagane]: lokalizacja. Na przykład "zachodnie stany USA".
* `--name` [wymagane]: nazwa konta magazynu. Nazwa musi mieć długość od 3 do 24 znaków i używać tylko małych znaków alfanumerycznych.
* `--resource-group` [wymagane]: Nazwa grupy zasobów.
* `--sku` [wymagane]: jednostka SKU konta magazynu. Dozwolone wartości:
  * `Premium_LRS`
  * `Standard_GRS`
  * `Standard_LRS`
  * `Standard_RAGRS`
  * `Standard_ZRS`
  * `Standard_GZRS` (wersja zapoznawcza)
  * `Standard_RAGZRS` (wersja zapoznawcza)

### <a name="set-default-azure-storage-account-environment-variables"></a>Ustaw domyślne zmienne środowiskowe konta usługi Azure Storage

W ramach subskrypcji platformy Azure można mieć wiele kont magazynu. Aby wybrać jedną z nich do użycia dla wszystkich kolejnych poleceń magazynu, można ustawić następujące zmienne środowiskowe:

Najpierw wyświetl klucze konta magazynu przy użyciu polecenia [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive
az storage account keys list \
    --account-name <account_name> \
    --resource-group <resource_group> \
    --output table
```

Teraz, gdy masz klucz, możesz zdefiniować go i nazwę konta jako zmienne środowiskowe:

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_KEY=<key>
```

Innym sposobem ustawienia domyślnego konta magazynu jest użycie parametrów połączenia. Najpierw Pobierz parametry połączenia za pomocą polecenia `show-connection-string`:

```azurecli
az storage account show-connection-string \
    --name <account_name> \
    --resource-group <resource_group>
```

Następnie skopiuj parametry połączenia danych wyjściowych i Ustaw zmienną środowiskową `AZURE_STORAGE_CONNECTION_STRING` (może być konieczne ujęcie parametrów połączenia w cudzysłowach):

```azurecli
export AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
```

> [!NOTE]
> We wszystkich przykładach w poniższych sekcjach tego artykułu założono, że ustawisz zmienne środowiskowe `AZURE_STORAGE_ACCOUNT` i `AZURE_STORAGE_KEY`.

## <a name="create-and-manage-blobs"></a>Tworzenie obiektów blob i zarządzanie nimi
Azure Blob Storage to usługa służąca do przechowywania dużych ilości danych bez struktury, takich jak dane tekstowe lub binarne, do których można uzyskiwać dostęp z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. W tej sekcji założono, że znasz już pojęcia związane z usługą Azure Blob Storage. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z usługą Azure Blob Storage za pomocą platformy .NET](../blobs/storage-dotnet-how-to-use-blobs.md) i [obiektów BLOB](/rest/api/storageservices/blob-service-concepts).

### <a name="create-a-container"></a>Tworzenie kontenera
Każdy obiekt BLOB w usłudze Azure Storage musi znajdować się w kontenerze. Kontener można utworzyć przy użyciu polecenia `az storage container create`:

```azurecli
az storage container create --name <container_name>
```

Można ustawić jeden z trzech poziomów dostępu do odczytu dla nowego kontenera poprzez określenie opcjonalnego argumentu `--public-access`:

* `off` (wartość domyślna): dane kontenera są prywatne dla właściciela konta.
* `blob`: publiczny dostęp do odczytu dla obiektów BLOB.
* `container`: publiczny dostęp do odczytu i listy do całego kontenera.

Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem anonimowym w trybie odczytu do kontenerów i obiektów blob](../blobs/storage-manage-access-to-resources.md).

### <a name="upload-a-blob-to-a-container"></a>Przekazywanie obiektu blob do kontenera
Usługa Azure Blob Storage obsługuje blokowe, dołączanie i stronicowe obiekty blob. Przekaż obiekty blob do kontenera za pomocą polecenia `blob upload`:

```azurecli
az storage blob upload \
    --file <local_file_path> \
    --container-name <container_name> \
    --name <blob_name>
```

Jeśli chcesz przekazać bezpośrednio do folderu znajdującego się w kontenerze na koncie magazynu, Zastąp `--name <blob_name>` z `--name <folder/blob_name>`.

 Domyślnie polecenie `blob upload` przekazuje pliki *. VHD do stronicowych obiektów blob lub blokowe obiekty blob w przeciwnym razie. Aby określić inny typ podczas przekazywania obiektu BLOB, można użyć argumentu `--type` — dozwolone wartości to `append`, `block`i `page`.

 Aby uzyskać więcej informacji na temat różnych typów obiektów blob, zobacz temat [Omówienie blokowych obiektów blob, dołączania obiektów blob i stronicowych obiektów BLOB](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).


### <a name="download-a-blob-from-a-container"></a>Pobieranie obiektu blob z kontenera
W tym przykładzie pokazano, jak pobrać obiekt BLOB z kontenera:

```azurecli
az storage blob download \
    --container-name mycontainer \
    --name myblob.png \
    --file ~/mydownloadedblob.png
```

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Utwórz listę obiektów BLOB w kontenerze za pomocą polecenia [AZ Storage BLOB list](/cli/azure/storage/blob) .

```azurecli
az storage blob list \
    --container-name mycontainer \
    --output table
```

### <a name="copy-blobs"></a>Kopiuj obiekty blob
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

W powyższym przykładzie kontener docelowy musi już istnieć na docelowym koncie magazynu, aby operacja kopiowania zakończyła się pomyślnie. Ponadto źródłowy obiekt blob wskazany za pomocą argumentu `--source-uri` musi zawierać token sygnatury dostępu współdzielonego (SAS) albo musi być dostępny publicznie, jak w tym przykładzie.

### <a name="delete-a-blob"></a>Usuwanie obiektu blob
Aby usunąć obiekt BLOB, użyj `blob delete` polecenia:

```azurecli
az storage blob delete --container-name <container_name> --name <blob_name>
```

### <a name="set-the-content-type"></a>Ustawianie typu zawartości

Typ zawartości, określany też jako typ MIME, identyfikuje format danych w obiekcie blob. Przeglądarki i inne oprogramowanie umożliwia określenie sposobu przetwarzania danych na podstawie typu zawartości. Na przykład typ zawartości dla obrazów PNG jest `image/png`. Aby ustawić typ zawartości, użyj `blob update` polecenia:

```azurecli
az storage blob update
    --container-name <container_name> 
    --name <blob_name>
    --content-type <content_type>
```

## <a name="create-and-manage-file-shares"></a>Tworzenie udziałów plików i zarządzanie nimi
Azure Files oferuje udostępniony magazyn dla aplikacji przy użyciu protokołu SMB (Server Message Block). Microsoft Azure maszyny wirtualne i usługi w chmurze, a także aplikacje lokalne, mogą udostępniać dane plików za pośrednictwem zainstalowanych udziałów. Udziałami plików i danymi plików można zarządzać za pomocą interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji na temat Azure Files, zobacz [wprowadzenie do Azure Files](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Tworzenie udziału plików
Udział plików platformy Azure to udział plików SMB na platformie Azure. Wszystkie katalogi i pliki muszą zostać utworzone w udziale plików. Konto może zawierać nieograniczoną liczbę udziałów, a udział może przechowywać nieograniczoną liczbę plików — do limitów pojemności konta magazynu. Poniższy przykład tworzy udział plików o nazwie Moje **udostępnianie**.

```azurecli
az storage share create --name myshare
```

### <a name="create-a-directory"></a>Tworzenie katalogu
Katalog zawiera hierarchiczną strukturę w udziale plików platformy Azure. Poniższy przykład tworzy katalog o nazwie **myDir** w udziale plików.

```azurecli
az storage directory create --name myDir --share-name myshare
```

Ścieżka katalogu może zawierać wiele poziomów, na przykład **katalog1/dir2**. Należy jednak upewnić się, że wszystkie katalogi nadrzędne istnieją przed utworzeniem podkatalogu. Na przykład w przypadku ścieżki **katalog1/dir2**należy najpierw utworzyć katalog **katalog1**, a następnie utworzyć katalog **dir2**.

### <a name="upload-a-local-file-to-a-share"></a>Przekaż plik lokalny do udziału
Poniższy przykład przekazuje plik z **~/temp/SampleFile.txt** do katalogu głównego udziału plików **udziału** . `--source` argument określa istniejący plik lokalny do przekazania.

```azurecli
az storage file upload --share-name myshare --source ~/temp/samplefile.txt
```

Podobnie jak w przypadku tworzenia katalogów, można określić ścieżkę katalogu w udziale, aby przekazać plik do istniejącego katalogu w udziale:

```azurecli
az storage file upload --share-name myshare/myDir --source ~/temp/samplefile.txt
```

Plik w udziale może mieć rozmiar do 1 TB.

### <a name="list-the-files-in-a-share"></a>Wyświetlanie listy plików w udziale
Możesz wyświetlić listę plików i katalogów w udziale za pomocą polecenia `az storage file list`:

```azurecli
# List the files in the root of a share
az storage file list --share-name myshare --output table

# List the files in a directory within a share
az storage file list --share-name myshare/myDir --output table

# List the files in a path within a share
az storage file list --share-name myshare --path myDir/mySubDir/MySubDir2 --output table
```

### <a name="copy-files"></a>Kopiowanie plików      
Można skopiować plik do innego pliku, pliku do obiektu BLOB lub obiektu BLOB do pliku. Na przykład, aby skopiować plik do katalogu w innym udziale:        
        
```azurecli
az storage file copy start \
--source-share share1 --source-path dir1/file.txt \
--destination-share share2 --destination-path dir2/file.txt     
```

## <a name="create-share-snapshot"></a>Utwórz migawkę udziału
Migawkę udziału można utworzyć przy użyciu polecenia `az storage share snapshot`:

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

Możesz wyświetlić migawki udziału określonego udziału przy użyciu `az storage share list --include-snapshots`

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
Możesz również przejść do konkretnej migawki udziału, aby wyświetlić jej zawartość przy użyciu `az storage file list`. Jeden musi określać nazwę udziału `--share-name <snare name>` i sygnaturę czasową `--snapshot '2017-10-04T19:45:18.0000000Z'`

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
### <a name="restore-from-share-snapshots"></a>Przywracanie z migawek udziałów

Można przywrócić plik, kopiując lub pobierając plik z migawki udziału przy użyciu polecenia `az storage file download`

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
## <a name="delete-share-snapshot"></a>Usuń migawkę udziału
Migawkę udziału można usunąć za pomocą polecenia `az storage share delete`, dostarczając parametr `--snapshot` z sygnaturą czasową migawki udziału:

```cli
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Przykładowe dane wyjściowe
```json
{
  "deleted": true
}
```

## <a name="next-steps"></a>Następne kroki
Oto kilka dodatkowych zasobów, aby dowiedzieć się więcej o pracy z interfejsem wiersza polecenia platformy Azure. 

* [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Dokumentacja poleceń interfejsu wiersza polecenia platformy Azure](/cli/azure)
* [Interfejs wiersza polecenia platformy Azure w witrynie GitHub](https://github.com/Azure/azure-cli)
