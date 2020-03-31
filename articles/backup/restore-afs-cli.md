---
title: Przywracanie udziałów plików platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak przywrócić zapasowe udziały plików platformy Azure w magazynie usług Recovery Services za pomocą interfejsu wiersza polecenia platformy Azure
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 63b2be2fe24c1274ed1581b7b849de578c978842
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76931049"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>Przywracanie udziałów plików platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure

Narzędzie cli platformy Azure zapewnia środowisko wiersza polecenia do zarządzania zasobami platformy Azure. Jest to doskonałe narzędzie do tworzenia automatyzacji niestandardowej do korzystania z zasobów platformy Azure. W tym artykule wyjaśniono, jak przywrócić cały udział plików lub określonych plików z punktu przywracania utworzonego przez [usługę Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) przy użyciu interfejsu wiersza polecenia platformy Azure. Te kroki można również wykonać przy użyciu programu [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) lub w witrynie [Azure Portal](backup-afs.md).

Na końcu tego artykułu dowiesz się, jak wykonać następujące operacje za pomocą interfejsu wiersza polecenia platformy Azure:

* Wyświetlanie punktów przywracania dla kopii zapasowej udziału plików platformy Azure.
* Przywracanie pełnego udziału plików platformy Azure.
* Przywracanie pojedynczych plików lub folderów.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Aby zainstalować interfejs wiersza polecenia lokalnie i korzystać z niego, należy korzystać z interfejsu wiersza polecenia platformy Azure w wersji 2.0.18 lub nowszej. Aby znaleźć wersję interfejsu wiersza polecenia, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz już udział plików platformy Azure, który jest wspierany przez usługę Azure Backup. Jeśli go nie masz, zobacz [Tworzenie kopii zapasowych udziałów plików platformy Azure w wierszu polecenia,](backup-afs-cli.md) aby skonfigurować kopię zapasową dla udziału plików. W tym artykule należy użyć następujących zasobów:

| Udział plików  | Konto magazynu | Region | Szczegóły                                                      |
| ----------- | --------------- | ------ | ------------------------------------------------------------ |
| *pliki azurefiles*  | *afsaccount (konto afsaccount)*      | Okręg wyborczy EastUS | Kopia zapasowa oryginalnego źródła przy użyciu usługi Azure Backup                 |
| *pliki azurefiles1* | *afaccount1*      | Okręg wyborczy EastUS | Źródło docelowe używane do odzyskiwania lokalizacji alternatywnej |

Można użyć podobnej struktury dla udziałów plików, aby wypróbować różne typy przywracania wyjaśnione w tym artykule.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Pobieranie punktów odzyskiwania dla udziału plików platformy Azure

Użyj polecenia cmdlet [listy punktów odzyskiwania kopii zapasowej az,](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) aby wyświetlić listę wszystkich punktów odzyskiwania dla kopii zapasowej udziału plików.

W poniższym przykładzie pobiera listę punktów odzyskiwania dla udziału pliku *azurefiles* na koncie magazynu *afsaccount.*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --backup-management-type azurestorage --item-name “AzureFileShare;azurefiles” --workload-type azurefileshare --out table
```

Poprzednią polecenie cmdlet można również uruchomić przy użyciu przyjaznej nazwy kontenera i elementu, podając następujące dwa dodatkowe parametry:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

Zestaw wyników jest listą punktów odzyskiwania ze szczegółami czasu i spójności dla każdego punktu przywracania.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

**Atrybut Name** w danych wyjściowych odpowiada nazwie punktu odzyskiwania, który może być używany jako wartość parametru **--rp-name** w operacjach odzyskiwania.

## <a name="full-share-recovery-by-using-the-azure-cli"></a>Pełne odzyskiwanie udziału przy użyciu interfejsu wiersza polecenia platformy Azure

Za pomocą tej opcji przywracania można przywrócić pełny udział plików w oryginalnej lub alternatywnej lokalizacji.

Zdefiniuj następujące parametry do wykonywania operacji przywracania:

* **--container-name**: Nazwa konta magazynu, na którym znajduje się kopia zapasowa oryginalnego udziału plików. Aby pobrać nazwę lub przyjazną nazwę kontenera, użyj polecenia [az backup container list.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--item-name**: Nazwa kopii zapasowej oryginalnego udziału pliku, którego chcesz użyć do operacji przywracania. Aby pobrać nazwę lub przyjazną nazwę elementu kopii zapasowej, użyj polecenia [az backup item list.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)

### <a name="restore-a-full-share-to-the-original-location"></a>Przywracanie pełnego udziału do oryginalnej lokalizacji

Po przywróceniu do oryginalnej lokalizacji nie trzeba określać parametrów związanych z obiektem docelowym. Należy podać tylko **rozwiązanie konfliktu.**

W poniższym przykładzie użyto polecenia cmdlet [przywracania przywracania kopii zapasowej az](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) z trybem przywracania ustawionym na *originallocation* w celu przywrócenia udziału pliku *azurefiles* w oryginalnej lokalizacji. Używasz punktu odzyskiwania 932883129628959823, który został uzyskany w [punktach odzyskiwania pobierania dla udziału plików platformy Azure:](#fetch-recovery-points-for-the-azure-file-share)

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

**Atrybut Name** w danych wyjściowych odpowiada nazwie zadania utworzonego przez usługę tworzenia kopii zapasowej dla operacji przywracania. Aby śledzić stan zadania, użyj polecenia cmdlet [zadania kopii zapasowej az.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

### <a name="restore-a-full-share-to-an-alternate-location"></a>Przywracanie pełnego udziału do lokalizacji alternatywnej

Za pomocą tej opcji można przywrócić udział plików w lokalizacji alternatywnej i zachować oryginalny udział plików w stanie takim, w jakim jest. Określ następujące parametry dla odzyskiwania lokalizacji alternatywnej:

* **--target-storage-account:** konto magazynu, na którym przywrócono kopię zapasową zawartości. Docelowe konto magazynu musi znajdować się w tej samej lokalizacji co magazyn.
* **--target-file-share:** Udział plików na docelowym koncie magazynu, na którym przywrócono kopię zapasową zawartości.
* **--target-folder**: Folder pod udziałem plików, do którego dane są przywracane. Jeśli kopia zapasowa ma zostać przywrócona do folderu głównego, należy podać wartości folderu docelowego jako pusty ciąg.
* **--resolve-conflict:** Instrukcja, jeśli występuje konflikt z przywróconymi danymi. Akceptuje **zastąpienie** lub **pominięcie**.

W poniższym przykładzie użyto [az backup restore restore-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) z trybem przywracania jako *alternatelocation,* aby przywrócić udział pliku *azurefiles* na koncie magazynu *afsaccount* do udziału plików *azurefiles1 na* koncie magazynu *afaccount1.*

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

**Atrybut Name** w danych wyjściowych odpowiada nazwie zadania utworzonego przez usługę tworzenia kopii zapasowej dla operacji przywracania. Aby śledzić stan zadania, użyj polecenia cmdlet [zadania kopii zapasowej az.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="item-level-recovery"></a>Odzyskiwanie na poziomie elementu

Za pomocą tej opcji przywracania można przywrócić poszczególne pliki lub foldery w oryginalnej lub alternatywnej lokalizacji.

Zdefiniuj następujące parametry do wykonywania operacji przywracania:

* **--container-name**: Nazwa konta magazynu, na którym znajduje się kopia zapasowa oryginalnego udziału plików. Aby pobrać nazwę lub przyjazną nazwę kontenera, użyj polecenia [az backup container list.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--item-name**: Nazwa kopii zapasowej oryginalnego udziału pliku, którego chcesz użyć do operacji przywracania. Aby pobrać nazwę lub przyjazną nazwę elementu kopii zapasowej, użyj polecenia [az backup item list.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)

Określ następujące parametry dla elementów, które chcesz odzyskać:

* **SourceFilePath**: Bezwzględna ścieżka pliku, który ma zostać przywrócony w udziale plików, jako ciąg. Ta ścieżka jest tą samą ścieżką, która jest używana w [pliku az storage download](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-download) lub plik [az storage zawiera](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-show) polecenia interfejsu wiersza polecenia.
* **SourceFileType**: Wybierz, czy jest wybrany katalog, czy plik. Akceptuje **katalog** lub **plik**.
* **ResolveConflict**: Instrukcja, jeśli występuje konflikt z przywróconymi danymi. Akceptuje **zastąpienie** lub **pominięcie**.

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>Przywracanie pojedynczych plików lub folderów do oryginalnej lokalizacji

Użyj polecenia cmdlet [przywracania przywracania kopii zapasowej az](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) z trybem przywracania ustawionym na *originallocation,* aby przywrócić określone pliki lub foldery do ich pierwotnej lokalizacji.

Poniższy przykład przywraca plik *RestoreTest.txt* w oryginalnej lokalizacji: udział pliku *azurefiles.*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

**Atrybut Name** w danych wyjściowych odpowiada nazwie zadania utworzonego przez usługę tworzenia kopii zapasowej dla operacji przywracania. Aby śledzić stan zadania, użyj polecenia cmdlet [zadania kopii zapasowej az.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>Przywracanie pojedynczych plików lub folderów do lokalizacji alternatywnej

Aby przywrócić określone pliki lub foldery do lokalizacji alternatywnej, użyj polecenia cmdlet [przywracania przywracania kopii zapasowej az](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) z trybem przywracania ustawionym na *alternatywną lokalizację* i określ następujące parametry związane z obiektem docelowym:

* **--target-storage-account:** konto magazynu, na którym przywrócono kopię zapasową zawartości. Docelowe konto magazynu musi znajdować się w tej samej lokalizacji co magazyn.
* **--target-file-share:** Udział plików na docelowym koncie magazynu, na którym przywrócono kopię zapasową zawartości.
* **--target-folder**: Folder pod udziałem plików, do którego dane są przywracane. Jeśli kopia zapasowa ma zostać przywrócona do folderu głównego, nadaj wartość folderu docelowego jako pusty ciąg.

Poniższy przykład przywraca plik *RestoreTest.txt* pierwotnie obecny w udziale pliku *azurefiles* do alternatywnej lokalizacji: folder *restoredata* w udziale plików *azurefiles1* hostowany na koncie magazynu *afaccount1.*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

**Atrybut Name** w danych wyjściowych odpowiada nazwie zadania utworzonego przez usługę tworzenia kopii zapasowej dla operacji przywracania. Aby śledzić stan zadania, użyj polecenia cmdlet [zadania kopii zapasowej az.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [zarządzać kopiami zapasowymi udziału plików platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure.](manage-afs-backup-cli.md)
