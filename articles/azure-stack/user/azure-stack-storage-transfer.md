---
title: Narzędzia do magazynu usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o danych z magazynu usługi Azure Stack narzędzia transferu
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: 4385e982b2a1da52ae55acf50c601108863c452a
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905957"
---
# <a name="use-data-transfer-tools-for-azure-stack-storage"></a>Użyj narzędzia transferu danych dla magazynu usługi Azure Stack

*Dotyczy Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Microsoft Azure Stack zapewnia zestaw usług magazynu dla dysków, obiekty BLOB, tabel, kolejek i funkcji zarządzania kontem. Zestaw narzędzi usługi Azure storage można użyć, jeśli chcesz zarządzać lub przenieść dane do / z magazynu usługi Azure Stack. Ten artykuł zawiera omówienie dostępnych narzędzi.

Wymagania określają, które z następujących narzędzi działa najlepiej dla Ciebie:

* [Narzędzie AzCopy](#azcopy)

    Narzędzie specyficzne dla magazynu, wiersza polecenia, które można pobrać w celu skopiowania danych z jednego obiektu do innego obiektu na koncie magazynu lub między kontami magazynu.

* [Azure PowerShell](#azure-powershell)

    Powłoka wiersza polecenia, opartego na zadaniach oraz językiem skryptowym opracowanym specjalnie do administrowania systemem.

* [Interfejs wiersza polecenia platformy Azure](#azure-cli)

    To narzędzie open source, dla wielu platform, udostępniająca zestaw poleceń do pracy z platformy Azure i usługi Azure Stack.

* [Eksplorator usługi Microsoft storage](#microsoft-azure-storage-explorer)

    Łatwe w użyciu autonomiczną aplikację przy użyciu interfejsu użytkownika.

* [Blobfuse](#blobfuse)

    Sterownik systemu plików wirtualnych usługi Azure Blob Storage, co pozwala na dostęp do istniejących danych blokowych obiektów blob na koncie magazynu za pomocą systemu plików w systemie Linux. 

Ze względu na różnice usług magazynu między platformą Azure i usługi Azure Stack może być niektórych określonych wymagań dotyczących poszczególnych narzędzi opisanych w poniższych sekcjach. Aby porównać magazynu usługi Azure Stack i Azure storage, zobacz [magazynu usługi Azure Stack: Różnice i zagadnienia](azure-stack-acs-differences.md).

## <a name="azcopy"></a>Narzędzie AzCopy

AzCopy to narzędzie wiersza polecenia, zaprojektowane w celu kopiowania danych do i z magazynu obiektów blob oraz tabel Microsoft Azure przy użyciu prostych poleceń z optymalną wydajnością. Możesz skopiować dane z jednego obiektu do drugiego w ramach konta magazynu lub między kontami magazynu.

### <a name="download-and-install-azcopy"></a>Pobierz i zainstaluj narzędzia AzCopy

Istnieją dwie wersje narzędzia AzCopy: Narzędzie AzCopy w Windows i narzędzia AzCopy w systemie Linux.

 - **Narzędzie AzCopy w systemie Windows**
    - Pobierz obsługiwaną wersję programu AzCopy dla usługi Azure Stack. Można zainstalować i korzystanie z narzędzia AzCopy w usłudze Azure Stack taki sam sposób jak platformy Azure. Aby uzyskać więcej informacji, zobacz [narzędzie AzCopy w Windows](../../storage/common/storage-use-azcopy.md).
        - 1811 aktualizowania lub nowsze wersje [Pobierz narzędzia AzCopy 7.3.0](https://aka.ms/azcopyforazurestack20171109).
        - Dla wcześniejszych wersji (aktualizacji 1802 do 1809) [Pobierz narzędzia AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417).

 - **Narzędzie AzCopy w systemie Linux**

    - Można zainstalować i korzystanie z narzędzia AzCopy w usłudze Azure Stack taki sam sposób jak platformy Azure. Aby uzyskać więcej informacji, zobacz [narzędzia AzCopy w systemie Linux](../../storage/common/storage-use-azcopy-linux.md).
    - Poprzednie wersje (aktualizacji 1802 do 1809), można zobaczyć [kroki instalacji narzędzia AzCopy 7.1 i wcześniejszych wersji](../../storage/common/storage-use-azcopy-linux.md#installation-steps-for-azcopy-71-and-earlier-versions).

### <a name="azcopy-command-examples-for-data-transfer"></a>Przykłady poleceń narzędzia AzCopy do transferu danych

Typowe scenariusze dotyczące kopiowania danych z obiektów blob usługi Azure Stack i przykładów. Aby dowiedzieć się więcej, zobacz [narzędzie AzCopy w Windows](../../storage/common/storage-use-azcopy.md) i [narzędzia AzCopy w systemie Linux](../../storage/common/storage-use-azcopy-linux.md).

### <a name="download-all-blobs-to-a-local-disk"></a>Pobierz wszystkie obiekty BLOB na dysk lokalny

**Windows**

```shell
AzCopy.exe /source:https://myaccount.blob.local.azurestack.external/mycontainer /dest:C:\myfolder /sourcekey:<key> /S
```

**Linux**

```bash
azcopy \
    --source https://myaccount.blob.local.azurestack.external/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

### <a name="upload-single-file-to-virtual-directory"></a>Przekaż pojedynczy plik do katalogu wirtualnego

**Windows**

```shell
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.local.azurestack.external/mycontainer/vd /DestKey:key /Pattern:abc.txt
```

**Linux**

```bash
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.local.azurestack.external/mycontainer/vd/abc.txt \
    --dest-key <key>
```

### <a name="move-data-between-azure-and-azure-stack-storage"></a>Przenoszenie danych między magazynu platformy Azure i usługi Azure Stack

Transfer danych asynchronicznej między magazynu platformy Azure i usługi Azure Stack nie jest obsługiwane. Należy określić transfer z **/SyncCopy** lub **— kopia synchroniczna** opcji.

**Windows**

```shell
Azcopy /Source:https://myaccount.blob.local.azurestack.external/mycontainer /Dest:https://myaccount2.blob.core.windows.net/mycontainer2 /SourceKey:AzSKey /DestKey:Azurekey /S /SyncCopy
```

**Linux**

```bash
azcopy \
    --source https://myaccount1.blob.local.azurestack.external/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --sync-copy
```

### <a name="azcopy-known-issues"></a>Znane problemy narzędzia Azcopy

 - Żadnych operacji narzędzia AzCopy na magazyn plików jest niedostępna, ponieważ magazyn plików nie jest jeszcze dostępna w usłudze Azure Stack.
 - Transfer danych asynchronicznej między magazynu platformy Azure i usługi Azure Stack nie jest obsługiwane. Można określić transfer z **/SyncCopy** opcję, aby skopiować dane.
 - Linux wersję narzędzia Azcopy obsługuje tylko 1802 update lub nowszej wersji. I nie obsługuje usługi Table service.

## <a name="azure-powershell"></a>Azure PowerShell

Program Azure PowerShell to moduł udostępniający polecenia cmdlet służące do zarządzania usługami na platformie Azure i usługi Azure Stack. Jest powłoka wiersza polecenia, opartego na zadaniach oraz językiem skryptowym opracowanym specjalnie do administrowania systemem.

### <a name="install-and-configure-powershell-for-azure-stack"></a>Instalowanie i konfigurowanie programu PowerShell dla usługi Azure Stack

Usługa Azure Stack, które zgodna modułów programu Azure PowerShell są wymagane do pracy z usługą Azure Stack. Aby uzyskać więcej informacji, zobacz [Instalowanie programu PowerShell dla usługi Azure Stack](azure-stack-powershell-install.md) i [konfigurowania środowiska PowerShell użytkownika usługi Azure Stack](azure-stack-powershell-configure-user.md) Aby dowiedzieć się więcej.

### <a name="powershell-sample-script-for-azure-stack"></a>Skrypt programu PowerShell dla usługi Azure Stack 

W tym przykładzie założono, że pomyślnie [zainstalowany program PowerShell dla usługi Azure Stack](azure-stack-powershell-install.md). Ten skrypt ułatwia ukończenie konfiguracji i poproś dzierżawy usługi Azure Stack poświadczenia, aby dodać swoje konto do lokalnego środowiska PowerShell. Następnie skrypt będzie Ustaw domyślną subskrypcję platformy Azure, Utwórz nowe konto magazynu na platformie Azure, Utwórz nowy kontener, w tym nowe konto magazynu i przekazać istniejący pliku obrazu (blob) do tego kontenera. Po skrypt zawiera listę wszystkich obiektów blob w kontenerze, spowoduje to utworzyć nowy katalog docelowy na komputerze lokalnym i Pobierz plik obrazu.

1. Zainstaluj [modułów programu Azure PowerShell dla usługi Azure Stack zgodnego](azure-stack-powershell-install.md).
2. Pobierz [narzędzia wymagane do pracy z usługą Azure Stack](azure-stack-powershell-download.md).
3. Otwórz **środowiska Windows PowerShell ISE** i **Uruchom jako Administrator**, kliknij przycisk **pliku** > **New** Aby utworzyć nowy plik skryptu.
4. Skopiuj poniższy skrypt i Wklej do nowego pliku skryptu.
5. Zaktualizuj zmienne skryptu na podstawie własnych ustawień konfiguracji.
   > [!NOTE]
   > Ten skrypt musi być uruchamiany w katalogu głównym **AzureStack_Tools**.

```powershell  
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack environment name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudience = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase.
$Location = "Local" # Choose "Local" as an example.
$ContainerName = "photo" # Give a name to your new container.
$ImageToUpload = "C:\temp\Hello.jpg" # Prepare an image file and a source directory in your local computer.
$DestinationFolder = "C:\temp\download" # A destination directory in your local computer.

# Import the Connect PowerShell module"
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
Import-Module .\Connect\AzureStack.Connect.psm1

# Configure the PowerShell environment
# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRmEnvironment -Name $ARMEvnName -ARMEndpoint $ARMEndPoint 

# Set the GraphEndpointResourceId value
Set-AzureRmEnvironment -Name $ARMEvnName -GraphEndpoint $GraphAudience

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Add-AzureRmAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

# Set a default Azure subscription.
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Create a new Resource Group 
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Create a new storage account.
New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS

# Set a default storage account.
Set-AzureRmCurrentStorageAccount -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName 

# Create a new container.
New-AzureStorageContainer -Name $ContainerName -Permission Off

# Upload a blob into a container.
Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

# List all blobs in a container.
Get-AzureStorageBlob -Container $ContainerName

# Download blobs from the container:
# Get a reference to a list of all blobs in a container.
$blobs = Get-AzureStorageBlob -Container $ContainerName

# Create the destination directory.
New-Item -Path $DestinationFolder -ItemType Directory -Force  

# Download blobs into the local destination directory.
$blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder

# end
```

### <a name="powershell-known-issues"></a>Znane problemy dotyczące programu PowerShell

Bieżąca wersja modułu zgodne programu Azure PowerShell w dla usługi Azure Stack to 1.2.11 dla operacji użytkownika. Różni się od najnowszej wersji programu Azure PowerShell. Różnica ta ma wpływ na działanie usług magazynu:

Format wartości zwracanej `Get-AzureRmStorageAccountKey` w wersji 1.2.11 ma dwie właściwości: `Key1` i `Key2`, podczas gdy bieżąca wersja platformy Azure zwraca tablicę zawierającą wszystkie klucze konta.

```powershell
# This command gets a specific key for a storage account, 
# and works for Azure PowerShell version 1.4, and later versions.
(Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
-AccountName "MyStorageAccount").Value[0]

# This command gets a specific key for a storage account, 
# and works for Azure PowerShell version 1.3.2, and previous versions.
(Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
-AccountName "MyStorageAccount").Key1
```

Aby uzyskać więcej informacji, zobacz [Get AzureRmStorageAccountKey](/powershell/module/azurerm.storage/Get-AzureRmStorageAccountKey).

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure to środowisko wiersza polecenia platformy Azure do zarządzania zasobami platformy Azure. Można go zainstalować w systemach macOS, Linux i Windows i uruchom go z poziomu wiersza polecenia.

Wiersza polecenia platformy Azure jest zoptymalizowany do zarządzania i administrowania zasobami platformy Azure z poziomu wiersza polecenia oraz do tworzenia skryptów automatyzacji działających względem usługi Azure Resource Manager. Udostępnia wiele z tych samych funkcji w portalu usługi Azure Stack, w tym dostęp do zaawansowanych danych.

Usługa Azure Stack wymaga wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby uzyskać więcej informacji na temat instalowania i konfigurowania interfejsu wiersza polecenia platformy Azure z usługą Azure Stack, zobacz [Instalowanie i Konfigurowanie interfejsu wiersza polecenia usługi Azure Stack](azure-stack-version-profiles-azurecli2.md). Aby uzyskać więcej informacji o tym, jak wykonać kilka zadań, pracy z zasobami w ramach konta magazynu usługi Azure Stack przy użyciu wiersza polecenia platformy Azure, zobacz [przy użyciu wiersza polecenia platformy Azure z usługą Azure storage](../../storage/storage-azure-cli.md)

### <a name="azure-cli-sample-script-for-azure-stack"></a>Usługa Azure przykładowy skrypt interfejsu wiersza polecenia dla usługi Azure Stack

Po ukończeniu instalacji interfejsu wiersza polecenia i konfiguracji, możesz spróbować poniższe kroki, aby pracować z przykładowy skrypt małych powłoki do interakcji z zasobami magazynu usługi Azure Stack. Skrypt wykonuje następujące czynności:

* Tworzy nowy kontener na koncie magazynu.
* Przekazuje istniejącego pliku (jako obiekt blob) do kontenera.
* Wyświetla listę wszystkich obiektów blob w kontenerze.
* Pobiera plik do miejsca docelowego na komputerze lokalnym, który określisz.

Przed uruchomieniem tego skryptu, upewnij się, że można pomyślnie nawiązać połączenie i zaloguj się do docelowej usługi Azure Stack.

1. Otwórz swoim ulubionym edytorze tekstów, a następnie skopiuj i Wklej powyższy skrypt w edytorze.
2. Aktualizowanie zmiennych skryptu w celu odzwierciedlenia ustawień konfiguracji.
3. Po zaktualizowaniu niezbędne zmiennych, Zapisz skrypt, a następnie zamknij Edytor. Kolejne kroki zakładają nazwanego skryptu **my_storage_sample.sh**.
4. Jeśli to konieczne, należy oznaczyć skrypt jako plik wykonywalny: `chmod +x my_storage_sample.sh`
5. Uruchom skrypt. Na przykład w powłoce Bash: `./my_storage_sample.sh`

```azurecli
#!/bin/bash
# A simple Azure Stack storage example script

export AZURESTACK_RESOURCE_GROUP=<resource_group_name>
export AZURESTACK_RG_LOCATION="local"
export AZURESTACK_STORAGE_ACCOUNT_NAME=<storage_account_name>
export AZURESTACK_STORAGE_CONTAINER_NAME=<container_name>
export AZURESTACK_STORAGE_BLOB_NAME=<blob_name>
export FILE_TO_UPLOAD=<file_to_upload>
export DESTINATION_FILE=<destination_file>

echo "Creating the resource group..."
az group create --name $AZURESTACK_RESOURCE_GROUP --location $AZURESTACK_RG_LOCATION

echo "Creating the storage account..."
az storage account create --name $AZURESTACK_STORAGE_ACCOUNT_NAME --resource-group $AZURESTACK_RESOURCE_GROUP --account-type Standard_LRS

echo "Creating the blob container..."
az storage container create --name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Uploading the file..."
az storage blob upload --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --file $FILE_TO_UPLOAD --name $AZURESTACK_STORAGE_BLOB_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Listing the blobs..."
az storage blob list --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --output table

echo "Downloading the file..."
az storage blob download --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --name $AZURESTACK_STORAGE_BLOB_NAME --file $DESTINATION_FILE --output table

echo "Done"
```

## <a name="microsoft-azure-storage-explorer"></a>Eksplorator usługi Microsoft Azure storage

Eksplorator usługi Microsoft Azure storage jest autonomiczną aplikacją oferowaną przez firmę Microsoft. Umożliwia łatwą obsługę za pomocą usługi Azure storage i magazynu usługi Azure Stack danych w Windows, macOS i komputerów z systemem Linux. Jeśli chcesz prosty sposób zarządzać danymi magazynu usługi Azure Stack, rozważ, za pomocą Eksploratora usługi Microsoft Azure storage.

* Aby dowiedzieć się więcej na temat konfigurowania usługi Azure storage explorer do pracy z usługą Azure Stack, zobacz [Połącz Eksplorator usługi storage z subskrypcją usługi Azure Stack](azure-stack-storage-connect-se.md).
* Aby dowiedzieć się więcej o Eksploratorze usługi Microsoft Azure storage, zobacz [wprowadzenie do Eksploratora usługi storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md)

## <a name="blobfuse"></a>Blobfuse 

[Blobfuse](https://github.com/Azure/azure-storage-fuse) jest sterownik systemu plików wirtualnych usługi Azure Blob Storage, co pozwala na dostęp do istniejących danych blokowych obiektów blob na koncie magazynu za pomocą systemu plików w systemie Linux. Usługa Azure Blob Storage to usługa magazynu obiektów i dlatego nie jest objęta hierarchicznej przestrzeni nazw. Blobfuse zapewnia tej przestrzeni nazw przy użyciu schematu katalogu wirtualnego z użyciem ukośnika do przodu `/` jako ogranicznik. Blobfuse działa zarówno na platformie Azure i usługi Azure Stack. 

Aby dowiedzieć się więcej na temat instalowania usługi Blob storage jako systemu plików przy użyciu Blobfuse w systemie Linux, zobacz [jak instalowanie usługi Blob storage jako systemu plików przy użyciu Blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux). 

Dla usługi Azure Stack **blobEndpoint** musi być określona oprócz accountName, accountKey/sasToken containerName, podczas konfigurowania poświadczeń konta magazynu w kroku przygotowania do instalowania. 

Podczas tworzenia usługi Azure Stack zestaw powinien być blobEndpoint `myaccount.blob.local.azurestack.external`. W zintegrowanym systemie Azure Stack skontaktuj się z administratorem chmury, jeśli nie masz pewności o punkt końcowy usługi. 

Należy pamiętać, że accountKey i sasToken może być tylko jeden skonfigurowany w danym momencie. Gdy zostanie podany klucz konta magazynu, pliku konfiguracji poświadczenia nie będzie w następującym formacie: 

```
accountName myaccount 
accountKey myaccesskey== 
containerName mycontainer 
blobEndpoint myaccount.blob.local.azurestack.external
```

Gdy token dostępu współdzielonego zostanie podany, pliku konfiguracji poświadczenia nie będzie w następującym formacie:

```  
accountName myaccount 
sasToken ?mysastoken 
containerName mycontainer 
blobEndpoint myaccount.blob.local.azurestack.external
```

## <a name="next-steps"></a>Kolejne kroki

* [Łączenie Eksploratora usługi storage z subskrypcją usługi Azure Stack](azure-stack-storage-connect-se.md)
* [Wprowadzenie do Eksploratora usługi storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Spójnych z platformą Azure storage: różnice i zagadnienia](azure-stack-acs-differences.md)
* [Wprowadzenie do usługi Microsoft Azure storage](../../storage/common/storage-introduction.md)
