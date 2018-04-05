---
title: Zarządzanie udziałami plików platformy Azure przy użyciu wiersza polecenia platformy Azure
description: Dowiedz się, jak zarządzać usługą Azure Files przy użyciu wiersza polecenia platformy Azure.
services: storage
documentationcenter: na
author: wmgries
manager: jeconnoc
editor: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: 066a43b553be18a5a0bc889fff441824df301b98
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="managing-azure-file-shares-using-the-azure-cli"></a>Zarządzanie udziałami plików platformy Azure przy użyciu wiersza polecenia platformy Azure
[Azure Files](storage-files-introduction.md) to łatwy w użyciu system plików w chmurze firmy Microsoft. Udziały plików platformy Azure można instalować w systemach Windows, Linux i macOS. W tym przewodniku przedstawiono podstawowe informacje dotyczące pracy z udziałami plików platformy Azure przy użyciu wiersza polecenia platformy Azure. Instrukcje: 

> [!div class="checklist"]
> * Tworzenie grupy zasobów i konta magazynu
> * Tworzenie udziału plików platformy Azure 
> * Tworzenie katalogu
> * Przekazywanie pliku 
> * Pobieranie pliku
> * Tworzenie i używanie migawki udziału

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia platformy Azure i korzystać z niego lokalnie, ten przewodnik będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby odnaleźć wersję, uruchom polecenie **az --version**. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). 

Domyślnie polecenia interfejsu wiersza polecenia platformy Azure zwracają kod JSON (JavaScript Object Notation), który de facto jest sposobem wysyłania i odbierania komunikatów z interfejsów API REST. W celu ułatwienia pracy z odpowiedziami JSON przykłady w tym przewodniku korzystają z parametru zapytania w poleceniach interfejsu wiersza polecenia platformy Azure. Ten parametr używa [języka zapytań JMESPath](http://jmespath.org/) do analizowania danych JSON. Dowiedz się więcej na temat sposobu manipulowania wynikami poleceń interfejsu wiersza polecenia platformy Azure, wykonując [Samouczek dotyczący zapytań JMESPath](http://jmespath.org/tutorial.html).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Jeśli nie masz jeszcze grupy zasobów platformy Azure, możesz utworzyć nową grupę za pomocą polecenia [az group create](/cli/azure/group#create). 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *Wschodnie stany USA*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu
Konto magazynu to udostępniona pula magazynu, w której można wdrażać udziały plików platformy Azure lub inne zasoby magazynu, takie jak obiekty blob i kolejki. Konto magazynu może zawierać nieograniczoną liczbę udziałów plików, a udział może obejmować nieograniczoną liczbę plików, przy czym nie można przekroczyć limitów pojemności konta magazynu.

W tym przykładzie tworzone jest konto magazynu o nazwie `mystorageaccount<random number>` przy użyciu polecenia [az storage account create](/cli/azure/storage/account#create), a następnie nazwa tego konta magazynu jest umieszczana w zmiennej `$STORAGEACCT`. Nazwa konta magazynu musi być unikatowa. Użyj zmiennej `$RANDOM`, aby dołączyć numer na końcu nazwy konta magazynu w celu zapewnienia jej unikatowości. 

```azurecli-interactive 
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

### <a name="get-the-storage-account-key"></a>Pobieranie klucza konta magazynu
Klucze konta magazynu służą do kontrolowania dostępu do zasobów na koncie magazynu. Są one tworzone automatycznie podczas tworzenia konta magazynu. Klucze dla danego konta magazynu możesz pobrać przy użyciu polecenia [az storage account keys list](/cli/azure/storage/account/keys#list). 

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure
Teraz możesz utworzyć swój pierwszy udział plików platformy Azure. Udziały plików można utworzyć przy użyciu polecenia [az storage share create](/cli/azure/storage/share#create). W tym przykładzie tworzony jest udział plików platformy Azure o nazwie `myshare`. 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

> [!Important]  
> Nazwy udziałów muszą składać się z małych liter, cyfr i pojedynczych łączników, ale nie mogą zaczynać się od łącznika. Szczegółowe informacje o nazwach plików i udziałów plików można znaleźć w temacie [Naming and Referencing Shares, Directories, Files, and Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Nazywanie i odwoływanie się do udziałów, katalogów, plików i metadanych).

## <a name="manipulating-the-contents-of-the-azure-file-share"></a>Manipulowanie zawartością udziału plików platformy Azure
Po utworzeniu udziału plików platformy Azure możesz zainstalować ten udział plików w systemie [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) lub [macOS](storage-how-to-use-files-mac.md) za pomocą protokołu SMB. Możesz też manipulować udziałem plików platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure. Jest to korzystniejsze od instalowania udziału plików za pomocą protokołu SMB, ponieważ wszystkie żądania przekazywane za pośrednictwem interfejsu wiersza polecenia platformy Azure są przekazywane przy użyciu interfejsu API REST plików, co umożliwia tworzenie, modyfikowanie oraz usuwanie plików i katalogów w udziale plików z następujących lokalizacji:

- Powłoka Bash w usłudze Cloud Shell (która nie może instalować udziałów plików za pośrednictwem protokołu SMB).
- Klienci, którzy nie mogą instalować udziałów SMB, tacy jak klienci lokalni bez odblokowanego portu 445.
- Scenariusze bez serwerów, tak jak w przypadku rozwiązania [Azure Functions](../../azure-functions/functions-overview.md). 

### <a name="create-directory"></a>Tworzenie katalogu
Aby utworzyć nowy katalog o nazwie *myDirectory* w katalogu głównym udziału plików platformy Azure, użyj polecenia [`az storage directory create`](/cli/azure/storage/directory#az_storage_directory_create).

```azurecli-interactive
az storage directory create \
   --account-name $STORAGEACCT \
   --account-key $STORAGEKEY \
   --share-name "myshare" \
   --name "myDirectory" 
```

### <a name="upload-a-file"></a>Przekazywanie pliku
Aby zademonstrować przekazywanie pliku przy użyciu polecenia [`az storage file upload`](/cli/azure/storage/file#az_storage_file_upload), najpierw należy utworzyć plik do przekazania w obrębie dysku tymczasowego usługi Cloud Shell w interfejsie wiersza polecenia platformy Azure. W poniższym przykładzie utworzymy, a następnie przekażemy plik.

```azurecli-interactive
date > ~/clouddrive/SampleUpload.txt

az storage file upload \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --source "~/clouddrive/SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Jeśli korzystasz z interfejsu wiersza polecenia platformy Azure lokalnie, zastąp ciąg `~/clouddrive` ścieżką, która istnieje na Twojej maszynie.

Po przekazaniu pliku możesz upewnić się, że plik został przekazany do udziału plików platformy Azure, korzystając z polecenia [`az storage file list`](/cli/azure/storage/file#az_storage_file_list).

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Pobieranie pliku
Za pomocą polecenia [`az storage file download`](/cli/azure/storage/file#az_storage_file_download) możesz pobrać kopię pliku, który został przekazany na dysk tymczasowy usługi Cloud Shell.

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before
rm -rf ~/clouddrive/SampleDownload.txt

az storage file download \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt" \
    --dest "~/clouddrive/SampleDownload.txt"
```

### <a name="copy-files"></a>Kopiowanie plików
Jednym z często wykonywanych zadań jest kopiowanie plików z jednego udziału plików do innego lub do/z kontenera usługi Azure Blob Storage. Aby zademonstrować tę funkcję, możesz utworzyć nowy udział i skopiować przekazany plik do tego nowego udziału przy użyciu polecenia [az storage file copy](/cli/azure/storage/file/copy). 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2"

az storage directory create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --name "myDirectory2"

az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-share "myshare" \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share "myshare2" \
    --destination-path "myDirectory2/SampleCopy.txt"
```

Teraz, po wyświetleniu listy plików w nowym udziale, skopiowany plik powinien być widoczny.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --output table
```

Polecenie `az storage file copy start` jest wygodne w przypadku przenoszenia plików ad hoc między udziałami plików platformy Azure i kontenerami usługi Azure Blob Storage, jednak do przenoszenia większej liczby plików lub plików o większych rozmiarach zalecamy używanie narzędzia AzCopy. Dowiedz się więcej o [narzędziu AzCopy dla systemu Linux](../common/storage-use-azcopy-linux.md) i [narzędziu AzCopy dla systemu Windows](../common/storage-use-azcopy.md). Narzędzie AzCopy musi być zainstalowane lokalnie — nie jest dostępne w usłudze Cloud Shell 

## <a name="create-and-modify-share-snapshots"></a>Tworzenie i modyfikowanie migawek udziałów
Jedną z dodatkowych przydatnych czynności, które można wykonywać na udziałach plików platformy Azure, jest tworzenie migawek udziałów. Migawka zachowuje określony moment w czasie dla udziału plików platformy Azure. Migawki udziałów są podobne do technologii systemów operacyjnych, które być może już znasz, takich jak:
- Migawki [Menedżera woluminów logicznych (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) dla systemu Linux
- Migawki [Systemu plików firmy Apple (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) dla systemu macOS 
- [Usługa kopiowania woluminów w tle (VSS)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636) dla systemów plików systemu Windows, takich jak NTFS i ReFS

Migawkę udziału możesz utworzyć przy użyciu polecenia [`az storage share snapshot`](/cli/azure/storage/share#az_storage_share_snapshot).

```azurecli-interactive
SNAPSHOT=$(az storage share snapshot \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Przeglądanie zawartości migawki udziału
Zawartość migawki udziału można przeglądać, przekazując sygnaturę czasową migawki udziału przechwyconą w zmiennej `$SNAPSHOT` do polecenia `az storage file list`.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --snapshot $SNAPSHOT \
    --output table
```

### <a name="list-share-snapshots"></a>Wyświetlanie listy migawek udziałów
Za pomocą następującego polecenia można wyświetlić listę migawek wykonanych dla udziału.

```azurecli-interactive
az storage share list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --include-snapshot \
    --query "[? name=='myshare' && snapshot!=null]" | tr -d '"'
```

### <a name="restore-from-a-share-snapshot"></a>Przywracanie na podstawie migawki udziału
Aby przywrócić plik, użyj polecenia `az storage file copy start`, którego używaliśmy wcześniej. Najpierw usuniemy przekazany plik `SampleUpload.txt`, abyśmy mogli przywrócić go z migawki.

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt"

# Build the source URI for snapshot restore
URI=$(az storage account show \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --query "primaryEndpoints.file" | tr -d '"')

URI=$URI"myshare/myDirectory/SampleUpload.txt?sharesnapshot="$SNAPSHOT

# Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-uri $URI \
    --destination-share "myshare" \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Usuwanie migawki udziału
Aby usunąć migawkę udziału, użyj polecenia [`az storage share delete`](/cli/azure/storage/share#az_storage_share_delete) ze zmienną zawierającą odwołanie `$SNAPSHOT` do parametru `--snapshot`.

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --snapshot $SNAPSHOT
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy skończysz, możesz usunąć grupę zasobów i wszystkie pokrewne zasoby za pomocą polecenia [`az group delete`](/cli/azure/group#delete). 

```azurecli-interactive 
az group delete --name "myResourceGroup"
```

Zasoby możesz również usuwać pojedynczo:
- Aby usunąć udziały plików platformy Azure utworzone na potrzeby tego przewodnika Szybki start.

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --delete-snapshots include

az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2" \
    --delete-snapshots include
```

- Aby usunąć konto magazynu (spowoduje to niejawne usunięcie utworzonych udziałów plików platformy Azure oraz wszelkich innych zasobów magazynu, które mogły zostać utworzone, takich jak kontener usługi Azure Blob Storage).

```azurecli-interactive
az storage account delete \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --yes
```

## <a name="next-steps"></a>Następne kroki
- [Zarządzanie udziałami plików za pomocą witryny Azure Portal](storage-how-to-use-files-portal.md)
- [Zarządzanie udziałami plików przy użyciu programu Azure PowerShell](storage-how-to-use-files-powershell.md)
- [Zarządzanie udziałami plików za pomocą Eksploratora usługi Storage](storage-how-to-use-files-storage-explorer.md)
- [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)