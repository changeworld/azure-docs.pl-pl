---
title: Dziele zapasowe udziałów plików platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure do generacji kopii zapasowych udziałów plików platformy Azure w magazynie usług odzyskiwania
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ff1d8c6245521d2d0262b0440177d65713058742
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844045"
---
# <a name="back-up-azure-file-shares-with-cli"></a>Dziele zapasowe udziałów plików platformy Azure za pomocą interfejsu wiersza polecenia

Interfejs wiersza polecenia platformy Azure (CLI) zapewnia środowisko wiersza polecenia do zarządzania zasobami platformy Azure. Jest to doskonałe narzędzie do tworzenia automatyzacji niestandardowej do korzystania z zasobów platformy Azure. W tym artykule opisano, jak zrobić z pomocą zapasową udziałów plików platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure. Te kroki można również wykonać przy użyciu programu [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) lub w witrynie [Azure Portal](backup-afs.md).

Pod koniec tego samouczka dowiesz się, jak wykonać poniższe operacje za pomocą interfejsu wiersza polecenia platformy Azure:

* Tworzenie magazynu usługi Recovery Services
* Włączanie tworzenia kopii zapasowych dla udziałów plików platformy Azure
* Wyzwalanie kopii zapasowej na żądanie dla udziałów plików

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Aby zainstalować interfejs wiersza polecenia lokalnie i korzystać z niego, należy korzystać z interfejsu wiersza polecenia platformy Azure w wersji 2.0.18 lub nowszej. Aby znaleźć wersję `run az --version`interfejsu wiersza polecenia, . Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-recovery-services-vault"></a>Tworzenie skarbca usług odzyskiwania

Magazyn usługi odzyskiwania jest jednostką, która zapewnia skonsolidowany widok i możliwości zarządzania we wszystkich elementach kopii zapasowej. Gdy zadanie tworzenia kopii zapasowej chronionego zasobu zostaje uruchomione, tworzony jest punkt odzyskiwania w magazynie usługi Recovery Services. Następnie można użyć jednego z tych punktów odzyskiwania w celu przywrócenia danych do danego punktu w czasie.

Wykonaj następujące kroki, aby utworzyć magazyn usług odzyskiwania:

1. Przechowalnia jest umieszczana w grupie zasobów. Jeśli nie masz istniejącej grupy zasobów, utwórz nową z [grupą az create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) . W tym samouczku utworzymy nową grupę zasobów *azurefiles* w regionie wschodnich stanów USA.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

2. Użyj polecenia cmdlet [tworzenia kopii zapasowych az,](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-create) aby utworzyć przechowalnię. Określ tę samą lokalizację dla przechowalni, która została użyta dla grupy zasobów.

    Poniższy przykład tworzy magazyn usług odzyskiwania o nazwie *azurefilesvault* w regionie wschodnich stanów USA.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

3. Określ typ nadmiarowości, który ma być używany w magazynie magazynu. Można użyć [magazynu lokalnie nadmiarowego](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs) lub [magazynu geograficznie nadmiarowego](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

    W poniższym przykładzie ustawia opcję nadmiarowości magazynu dla *azurefilesvault* do **Georedundant** przy użyciu polecenia cmdlet [az backup vault właściwości zestawu.](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set)

    ```azurecli-interactive
    az backup vault backup-properties set --name azurefilesvault --resource-group azurefiles --backup-storage-redundancy Georedundant
    ```

    Aby sprawdzić, czy przechowalnia została pomyślnie utworzona, można użyć polecenia cmdlet [magazynu kopii zapasowych az,](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-show) aby uzyskać szczegółowe informacje o przechowalni. Poniższy przykład wyświetla szczegóły *azurefilesvault* utworzyliśmy w powyższych krokach.

    ```azurecli-interactive
    az backup vault show --name azurefilesvault --resource-group azurefiles --output table
    ```

    Dane wyjściowe będą podobne do następującej odpowiedzi:

    ```output
    Location     Name               ResourceGroup
    ----------   ---------------    ---------------
    eastus       azurefilesvault    azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Włączanie tworzenia kopii zapasowych dla udziałów plików platformy Azure

W tej sekcji przyjęto założenie, że masz już udział plików platformy Azure, dla którego chcesz skonfigurować kopię zapasową. Jeśli go nie masz, utwórz udział plików platformy Azure za pomocą polecenia [az storage create.](https://docs.microsoft.com/cli/azure/storage/share?view=azure-cli-latest#az-storage-share-create)

Aby włączyć tworzenie kopii zapasowych dla udziałów plików, należy utworzyć zasady ochrony, która określa, kiedy zadanie tworzenia kopii zapasowej jest uruchamiane i jak długo są przechowywane punkty odzyskiwania. Zasady tworzenia kopii zapasowych można utworzyć przy użyciu polecenia cmdlet [tworzenia zasad kopii zapasowej az.](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create)

W poniższym przykładzie użyto polecenia cmdlet [enable-for-azurefileshare—](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurefileshare) w poniższym przykładzie, który umożliwia tworzenie kopii zapasowych udziału plików *azurefiles* na koncie magazynu *afsaccount* przy użyciu zasad tworzenia kopii zapasowych *harmonogramu 1:*

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

**Atrybut Name** w danych wyjściowych odpowiada nazwie zadania utworzonego przez usługę tworzenia kopii zapasowej dla włącznie operacji tworzenia **kopii zapasowej.** Aby śledzić stan zadania, użyj polecenia cmdlet [zadania kopii zapasowej az.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Wyzwalanie kopii zapasowej na żądanie dla udziału plików

Jeśli chcesz wyzwolić kopię zapasową na żądanie dla udziału plików zamiast czekać na uruchomienie zadania przez zasady tworzenia kopii zapasowej w zaplanowanym czasie, użyj polecenia cmdlet [ochrony kopii zapasowej az.](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-backup-now)

Aby wyzwolić kopię zapasową na żądanie, należy zdefiniować następujące parametry:

* **--container-name** to nazwa konta magazynu obsługującego udział plików. Aby pobrać **nazwę** lub **przyjazną nazwę** kontenera, użyj polecenia [az backup container list.](/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--item-name** to nazwa udziału plików, dla którego chcesz wyzwolić kopię zapasową na żądanie. Aby pobrać **nazwę** lub **przyjazną nazwę** elementu kopii zapasowej, użyj polecenia [az backup item list.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)
* **--retain-until** określa datę do momentu zachowania punktu odzyskiwania. Wartość powinna być ustawiona w formacie czasu UTC (dd-mm-yyyy).

Poniższy przykład wyzwala kopię zapasową na żądanie dla *azuresfiles* fileshare na koncie magazynu *afsaccount* z przechowywaniem do *20-01-2020*.

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

**Atrybut Name** w danych wyjściowych odpowiada nazwie zadania utworzonego przez usługę tworzenia kopii zapasowej dla operacji "kopia zapasowa na żądanie". Aby śledzić stan zadania, użyj polecenia cmdlet [zadania kopii zapasowej az.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [przywrócić udziały plików platformy Azure za pomocą interfejsu wiersza polecenia](restore-afs-cli.md)
* Dowiedz się, jak [zarządzać ackups udziałów plików platformy Azure z interfejsu wiersza polecenia](manage-afs-backup-cli.md)
