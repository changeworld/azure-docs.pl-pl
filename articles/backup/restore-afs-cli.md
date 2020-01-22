---
title: Przywracanie udziałów plików platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia platformy Azure przywrócić kopie zapasowe udziałów plików platformy Azure w magazynie Recovery Services
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 8d91031ff9ee0d4f32cc74ff7031d39ddab3e2b4
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294411"
---
# <a name="restore-azure-file-shares-with-cli"></a>Przywracanie udziałów plików platformy Azure przy użyciu interfejsu wiersza polecenia

Interfejs wiersza polecenia platformy Azure udostępnia środowisko wiersza polecenia do zarządzania zasobami platformy Azure. To doskonałe narzędzie do tworzenia niestandardowych automatyzacji do korzystania z zasobów platformy Azure. W tym artykule wyjaśniono, jak przywrócić cały udział plików lub określone pliki z punktu przywracania utworzonego przez usługę [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) przy użyciu interfejsu wiersza polecenia platformy Azure. Te kroki można również wykonać przy użyciu programu [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) lub w witrynie [Azure Portal](backup-afs.md).

Po zakończeniu tego samouczka dowiesz się, jak wykonać poniższe operacje przy użyciu interfejsu wiersza polecenia platformy Azure:

* Wyświetlanie punktów przywracania dla kopii zapasowej udziału plików platformy Azure
* Przywróć pełny udział plików platformy Azure
* Przywracanie pojedynczych plików lub folderów

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Aby zainstalować interfejs wiersza polecenia lokalnie i korzystać z niego, należy korzystać z interfejsu wiersza polecenia platformy Azure w wersji 2.0.18 lub nowszej. Aby znaleźć wersję interfejsu wiersza polecenia, `run az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz już udział plików platformy Azure, którego kopia zapasowa została utworzona przez usługę Azure Backup. Jeśli go nie masz, zapoznaj się z tematem tworzenie kopii zapasowej udziału plików na potrzeby kopii zapasowych [udziałów plików platformy Azure przy użyciu interfejsu wiersza polecenia](backup-afs-cli.md) . W tym artykule będziemy korzystać z następujących zasobów:

| Udział plików  | Konto magazynu | Region | Szczegóły                                                      |
| ----------- | --------------- | ------ | ------------------------------------------------------------ |
| *migracji pamięci*  | *afsaccount*      | EastUS | Utworzono kopię zapasową oryginalnego źródła przy użyciu Azure Backup                 |
| *azurefiles1* | *afaccount1*      | EastUS | Źródło docelowe używane do odzyskiwania lokalizacji alternatywnej |

Możesz użyć podobnej struktury dla udziałów plików, aby wypróbować różne typy przywracania wyjaśnione w tym dokumencie.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Pobierz punkty odzyskiwania dla udziału plików platformy Azure

Użyj polecenia [AZ Backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) , aby wyświetlić listę wszystkich punktów odzyskiwania kopii zapasowej udziału plików.

Poniższy przykład pobiera listę punktów odzyskiwania dla udziału plików *migracji pamięci* na koncie magazynu *afsaccount* .

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --backup-management-type azurestorage --item-name “AzureFileShare;azurefiles” --workload-type azurefileshare --out table
```

Możesz również wykonać powyższe polecenie cmdlet przy użyciu "przyjaznej nazwy" dla kontenera i elementu, dostarczając następujące dwa dodatkowe parametry:

* **--Backup-Management-Type**: *azurestorage*
* **--Typ obciążenia**: *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

Zestaw wyników będzie listą punktów odzyskiwania wraz ze szczegółami dotyczącymi czasu i spójności poszczególnych punktów przywracania.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

Atrybut **name** w danych wyjściowych odpowiada nazwie punktu odzyskiwania, który może być używany jako wartość parametru **--RP-Name** w operacji odzyskiwania.

## <a name="full-share-recovery-using-azure-cli"></a>Odzyskiwanie pełnego udziału przy użyciu interfejsu wiersza polecenia platformy Azure

Można użyć tej opcji przywracania, aby przywrócić pełny udział plików w lokalizacji oryginalnej lub alternatywnej.

Zdefiniuj następujące parametry, aby wykonać operacje przywracania:

* **--Container-Name** to nazwa konta magazynu obsługującego kopię zapasową oryginalnego udziału plików. Aby pobrać **nazwę** lub **przyjazną nazwę** kontenera, użyj polecenia [AZ Backup Container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--Item-Name** to nazwa kopii zapasowej oryginalnego pliku, który ma być używany dla operacji przywracania. Aby pobrać **nazwę** lub **przyjazną nazwę** elementu kopii zapasowej, użyj polecenia [AZ Backup Item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .

### <a name="restore-full-share-to-the-original-location"></a>Przywróć pełny udział w oryginalnej lokalizacji

W przypadku przywracania do oryginalnej lokalizacji nie trzeba określać parametrów związanych z elementem docelowym. Należy podać tylko **konflikt** .

W poniższym przykładzie użyto polecenia [AZ Backup Restore Restore-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) z trybem przywracania ustawionym na *originallocation* , aby przywrócić udział plików *migracji pamięci* w oryginalnej lokalizacji przy użyciu punktu odzyskiwania 932883129628959823, który został uzyskany w przypadku [pobrania punktów odzyskiwania dla udziału plików platformy Azure](#fetch-recovery-points-for-the-azure-file-share):

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

Atrybut **name** w danych wyjściowych odpowiada nazwie zadania, które jest tworzone przez usługę kopii zapasowej dla operacji przywracania. Aby śledzić stan zadania, użyj polecenia [AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

### <a name="restore-full-share-to-an-alternate-location"></a>Przywróć pełny udział w lokalizacji alternatywnej

Możesz użyć tej opcji, aby przywrócić udział plików w alternatywnej lokalizacji i zachować oryginalny udział plików jako. Określ następujące parametry odzyskiwania lokalizacji alternatywnej:

* **--Target-Storage-account**: konto magazynu, do którego zostanie przywrócona zawartość kopii zapasowej. Docelowe konto magazynu musi znajdować się w tej samej lokalizacji co magazyn.
* **--docelowy udział plików**: udział plików w docelowym koncie magazynu, do którego zostanie przywrócona zawartość kopii zapasowej.
* **--Target-folder**: folder w udziale plików, do którego przywracane są dane. Jeśli kopia zapasowa ma zostać przywrócona do folderu głównego, nadaj wartości folderu docelowego jako pusty ciąg.
* **--Rozwiąż konflikt**: instrukcje w przypadku konfliktu z przywróconymi danymi. Akceptuje **zastępowanie** lub **pomijanie**.

Poniższy przykład używa [AZ Backup Restore Restore-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) z trybem przywracania jako *alternatelocation* , aby przywrócić udział plików *migracji pamięci* na koncie magazynu *afsaccount* do *azurefiles1 "* udział plików na koncie magazynu *afaccount1* .

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

Atrybut **name** w danych wyjściowych odpowiada nazwie zadania, które jest tworzone przez usługę kopii zapasowej dla operacji przywracania. Aby śledzić stan zadania, użyj polecenia [AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="item-level-recovery"></a>Odzyskiwanie na poziomie elementu

Ta opcja przywracania służy do przywracania pojedynczych plików lub folderów w lokalizacji oryginalnej lub alternatywnej.

Zdefiniuj następujące parametry, aby wykonać operacje przywracania:

* **--Container-Name** to nazwa konta magazynu obsługującego kopię zapasową oryginalnego udziału plików. Aby pobrać **nazwę** lub **przyjazną nazwę** kontenera, użyj polecenia [AZ Backup Container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--Item-Name** to nazwa kopii zapasowej oryginalnego udziału plików, który ma być używany dla operacji przywracania. Aby pobrać **nazwę** lub **przyjazną nazwę** elementu kopii zapasowej, użyj polecenia [AZ Backup Item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .

Określ następujące parametry dla elementów, które mają zostać odzyskane:

* **Sourcefilepath**: ścieżka bezwzględna pliku, która ma zostać przywrócona w udziale plików jako ciąg. Ta ścieżka jest tą samą ścieżką, która jest używana w pliku [AZ Storage File Download](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-download) lub [AZ Storage File show](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-show) interfejsu wiersza polecenia.
* **SourceFileType**: Wybierz, czy wybrano katalog lub plik. Akceptuje **katalog** lub **plik**.
* **ResolveConflict**: instrukcja, jeśli wystąpił konflikt z przywróconymi danymi. Akceptuje **zastępowanie** lub **pomijanie**.

### <a name="restore-individual-filesfolders-to-the-original-location"></a>Przywróć pojedyncze pliki/foldery do oryginalnej lokalizacji

Użyj polecenia [AZ Backup Restore Restore-migracji pamięci](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) z trybem przywracania ustawionym na *originallocation* w celu przywrócenia określonych plików/folderów do ich oryginalnej lokalizacji.

Poniższy przykład przywraca plik *"RestoreTest. txt"* w oryginalnej lokalizacji: *migracji pamięci* .

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Atrybut **name** w danych wyjściowych odpowiada nazwie zadania, które jest tworzone przez usługę kopii zapasowej dla operacji przywracania. Aby śledzić stan zadania, użyj polecenia [AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

### <a name="restore-individual-filesfolders-to-an-alternate-location"></a>Przywracanie pojedynczych plików/folderów do lokalizacji alternatywnej

Aby przywrócić określone pliki/foldery do innej lokalizacji, użyj polecenia [AZ Backup Restore Restore-migracji pamięci](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) cmdlet z trybem przywracania ustawionym na *alternatelocation* i określ następujące parametry powiązane z elementem docelowym:

* **--Target-Storage-account**: konto magazynu, do którego zostanie przywrócona zawartość kopii zapasowej. Docelowe konto magazynu musi znajdować się w tej samej lokalizacji co magazyn.
* **--docelowy udział plików**: udział plików w docelowym koncie magazynu, do którego zostanie przywrócona zawartość kopii zapasowej.
* **--Target-folder**: folder w udziale plików, do którego przywracane są dane. Jeśli kopia zapasowa ma zostać przywrócona do folderu głównego, nadaj jej wartość jako pusty ciąg.

Poniższy przykład przywraca plik *RestoreTest. txt* znajdujący się pierwotnie w udziale *migracji pamięci* do lokalizacji alternatywnej: folder *restoredata* w udziale *azurefiles1* hostowanym na koncie magazynu *afaccount1* .

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Atrybut **name** w danych wyjściowych odpowiada nazwie zadania, które jest tworzone przez usługę kopii zapasowej dla operacji przywracania. Aby śledzić stan zadania, użyj polecenia [AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="next-steps"></a>Następne kroki

Informacje na temat [zarządzania kopiami zapasowymi udziałów plików platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](manage-afs-backup-cli.md)
