---
title: Zarządzanie kopiami zapasowymi udziałów plików platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure do zarządzania i monitorowania udziałów plików platformy Azure kopii zapasowej przez usługę Azure Backup.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 44a49913abd99b285397b8b78ad9d4c0f9df52ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934887"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>Zarządzanie kopiami zapasowymi udziałów plików platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure

Narzędzie cli platformy Azure zapewnia środowisko wiersza polecenia do zarządzania zasobami platformy Azure. Jest to doskonałe narzędzie do tworzenia automatyzacji niestandardowej do korzystania z zasobów platformy Azure. W tym artykule wyjaśniono, jak wykonywać zadania związane z zarządzaniem i monitorowaniem udziałów plików platformy Azure, które są archiwizowane przez [usługę Azure Backup.](https://docs.microsoft.com/azure/backup/backup-overview) Można również wykonać te kroki za pomocą [witryny Azure portal](https://portal.azure.com/). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Aby zainstalować i używać interfejsu wiersza polecenia lokalnie, należy uruchomić interfejsu wiersza polecenia platformy Azure w wersji 2.0.18 lub nowszej. Aby znaleźć wersję interfejsu wiersza polecenia, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że masz już udział plików platformy Azure kopii zapasowej przez [usługę Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview). Jeśli go nie masz, zobacz [Tworzenie kopii zapasowych udziałów plików platformy Azure w wierszu polecenia,](backup-afs-cli.md) aby skonfigurować kopię zapasową dla udziałów plików. W tym artykule należy użyć następujących zasobów:

* **Grupa zasobów**: *azurefiles*
* **RecoveryServicesVault**: *azurefilesvault*
* **Konto magazynu:** *afsaccount*
* **Udział plików**: *azurefiles*

## <a name="monitor-jobs"></a>Monitorowanie zadań

Podczas wyzwalania operacji tworzenia kopii zapasowej lub przywracania usługa tworzenia kopii zapasowych tworzy zadanie śledzenia. Aby monitorować ukończone lub aktualnie uruchomione zadania, użyj polecenia cmdlet [listy zadań kopii zapasowej az.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) W wierszu polecenia można również [zawiesić aktualnie uruchomione zadanie](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) lub [poczekać, aż zadanie zakończy się.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait)

W poniższym przykładzie jest wyświetlany stan zadań tworzenia kopii zapasowych dla magazynu usług odzyskiwania *azurefilesvault:*

```azurecli-interactive
az backup job list --resource-group azurefiles --vault-name azurefilesvault
```

```output
[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "location": null,
    "name": "d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "properties": {
      "actionsInfo": null,
      "activityId": "3cef43ed-0af4-43e2-b9cb-1322c496ccb4",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:29.718011",
      "endTime": "2020-01-13T08:05:29.180606+00:00",
      "entityFriendlyName": "azurefiles",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.462595+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "location": null,
    "name": "1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "properties": {
      "actionsInfo": null,
      "activityId": "2663449c-94f1-4735-aaf9-5bb991e7e00c",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:28.145216",
      "endTime": "2020-01-13T08:05:27.519826+00:00",
      "entityFriendlyName": "azurefilesresource",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.374610+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  }
]
```

## <a name="modify-policy"></a>Modyfikowanie zasad

Zasady tworzenia kopii zapasowych można zmodyfikować, aby zmienić częstotliwość tworzenia kopii zapasowych lub zakres przechowywania przy użyciu [zasad zestawu elementów kopii zapasowej az](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-set-policy).

Aby zmienić zasady, należy zdefiniować następujące parametry:

* **--container-name**: Nazwa konta magazynu, na którym znajduje się udział plików. Aby pobrać **nazwę** lub **przyjazną nazwę** kontenera, użyj polecenia [az backup container list.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--name**: Nazwa udziału plików, dla którego chcesz zmienić zasady. Aby pobrać **nazwę** lub **przyjazną nazwę** elementu kopii zapasowej, użyj polecenia [az backup item list.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)
* **--policy-name**: Nazwa zasady tworzenia kopii zapasowych, którą chcesz ustawić dla udziału plików. Za pomocą [listy zasad tworzenia kopii zapasowych az](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-list) można wyświetlić wszystkie zasady przechowalni.

W poniższym przykładzie ustawia zasadę tworzenia kopii zapasowych *schedule2* dla udziału plików *azurefiles* obecnego na koncie magazynu *afsaccount.*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

Poprzednie polecenie można również uruchomić przy użyciu przyjaznych nazw kontenera i elementu, podając następujące dwa dodatkowe parametry:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

**Atrybut Name** w danych wyjściowych odpowiada nazwie zadania utworzonego przez usługę tworzenia kopii zapasowych dla operacji zasad zmiany. Aby śledzić stan zadania, użyj polecenia cmdlet [zadania kopii zapasowej az.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="stop-protection-on-a-file-share"></a>Zatrzymywanie ochrony udziału plików

Istnieją dwa sposoby na zatrzymanie ochrony udziałów plików platformy Azure:

* Zatrzymaj wszystkie przyszłe zadania tworzenia kopii zapasowych i *usuń* wszystkie punkty odzyskiwania.
* Zatrzymaj wszystkie przyszłe zadania tworzenia kopii zapasowych, ale *pozostaw* punkty odzyskiwania.

Może istnieć koszt związany z opuszczeniem punktów odzyskiwania w magazynie, ponieważ podstawowe migawki utworzone przez usługę Azure Backup zostaną zachowane. Zaletą opuszczenia punktów odzyskiwania jest opcja, aby przywrócić udział plików później, jeśli chcesz. Aby uzyskać informacje na temat kosztów opuszczenia punktów odzyskiwania, zobacz [szczegóły cen](https://azure.microsoft.com/pricing/details/storage/files). Jeśli zdecydujesz się usunąć wszystkie punkty odzyskiwania, nie możesz przywrócić udziału plików.

Aby zatrzymać ochronę udziału plików, należy zdefiniować następujące parametry:

* **--container-name**: Nazwa konta magazynu, na którym znajduje się udział plików. Aby pobrać **nazwę** lub **przyjazną nazwę** kontenera, użyj polecenia [az backup container list.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--item-name**: Nazwa udziału plików, dla którego chcesz zatrzymać ochronę. Aby pobrać **nazwę** lub **przyjazną nazwę** elementu kopii zapasowej, użyj polecenia [az backup item list.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)

### <a name="stop-protection-and-retain-recovery-points"></a>Zatrzymaj ochronę i zachowaj punkty odzyskiwania

Aby zatrzymać ochronę przy zachowaniu danych, użyj polecenia cmdlet [ochrony kopii zapasowej az.](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable)

Poniższy przykład zatrzymuje ochronę udziału plików *azurefiles,* ale zachowuje wszystkie punkty odzyskiwania.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

Poprzednie polecenie można również uruchomić przy użyciu przyjaznej nazwy kontenera i elementu, podając następujące dwa dodatkowe parametry:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

**Atrybut Name** w danych wyjściowych odpowiada nazwie zadania utworzonego przez usługę tworzenia kopii zapasowej dla operacji ochrony zatrzymania. Aby śledzić stan zadania, użyj polecenia cmdlet [zadania kopii zapasowej az.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

### <a name="stop-protection-without-retaining-recovery-points"></a>Zatrzymaj ochronę bez zatrzymywania punktów odzyskiwania

Aby zatrzymać ochronę bez zachowywania punktów odzyskiwania, użyj polecenia cmdlet [az backup protection disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) z opcją **delete-backup-data** ustawioną na **true**.

Poniższy przykład zatrzymuje ochronę udziału plików *azurefiles* bez zachowywania punktów odzyskiwania.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

Poprzednie polecenie można również uruchomić przy użyciu przyjaznej nazwy kontenera i elementu, podając następujące dwa dodatkowe parametry:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>Wznawianie ochrony udziału plików

Jeśli została zatrzymana ochrona udziału plików platformy Azure, ale zachowane punkty odzyskiwania, można wznowić ochronę później. Jeśli nie zachowasz punktów odzyskiwania, nie możesz wznowić ochrony.

Aby wznowić ochronę udziału plików, należy zdefiniować następujące parametry:

* **--container-name**: Nazwa konta magazynu, na którym znajduje się udział plików. Aby pobrać **nazwę** lub **przyjazną nazwę** kontenera, użyj polecenia [az backup container list.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--item-name**: Nazwa udziału plików, dla którego chcesz wznowić ochronę. Aby pobrać **nazwę** lub **przyjazną nazwę** elementu kopii zapasowej, użyj polecenia [az backup item list.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)
* **--policy-name**: Nazwa zasady tworzenia kopii zapasowych, dla której chcesz wznowić ochronę udziału plików.

W poniższym przykładzie użyto polecenia cmdlet [wznowienia ochrony kopii zapasowej az,](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) aby wznowić ochronę udziału plików *azurefiles* przy użyciu zasad tworzenia kopii zapasowych *schedule1.*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

Poprzednie polecenie można również uruchomić przy użyciu przyjaznej nazwy kontenera i elementu, podając następujące dwa dodatkowe parametry:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

**Atrybut Name** w danych wyjściowych odpowiada nazwie zadania utworzonego przez usługę tworzenia kopii zapasowej dla operacji ochrony życiorysów. Aby śledzić stan zadania, użyj polecenia cmdlet [zadania kopii zapasowej az.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="unregister-a-storage-account"></a>Wyrejestrowanie konta magazynu

Jeśli chcesz chronić udziały plików na określonym koncie magazynu przy użyciu innego magazynu usług odzyskiwania, najpierw [zatrzymaj ochronę dla wszystkich udziałów plików na](#stop-protection-on-a-file-share) tym koncie magazynu. Następnie wyrejestruj konto z magazynu usług odzyskiwania, który jest obecnie używany do ochrony.

Aby wyrejestrować konto magazynu, należy podać nazwę kontenera. Aby pobrać **nazwę** lub **przyjazną nazwę** kontenera, użyj polecenia [az backup container list.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)

Poniższy przykład wyrejestrowywa konto magazynu *afsaccount* z *azurefilesvault* przy użyciu [pliku cmdlet kopii zapasowej az wyrejestrowania.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-unregister)

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

Poprzednią polecenie cmdlet można również uruchomić przy użyciu przyjaznej nazwy kontenera, podając następujący dodatkowy parametr:

* **--backup-management-type**: *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z tworzeniem kopii zapasowej udziałów plików platformy Azure](troubleshoot-azure-files.md).
