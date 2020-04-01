---
title: Samouczek — przywracanie dysku maszyny Wirtualnej za pomocą kopii zapasowej platformy Azure
description: Dowiedz się, jak przywrócić dysk i utworzyć odzyskaną maszynę wirtualną na platformie Azure przy użyciu usługi Backup i Recovery Services.
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: 8a66cee7e844f0049f2d2ca2f6841943aa267f3e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238734"
---
# <a name="restore-a-disk-and-create-a-recovered-vm-in-azure"></a>Przywracanie dysku i tworzenie odzyskanej maszyny wirtualnej na platformie Azure

Usługa Azure Backup tworzy punkty odzyskiwania przechowywane w geograficznie nadmiarowych magazynach odzyskiwania. Z punktu odzyskiwania można przywrócić całą maszynę wirtualną lub poszczególne pliki. W tym artykule opisano sposób przywracania całej maszyny wirtualnej przy użyciu interfejsu wiersza polecenia. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Wyświetlanie listy i wybieranie punktów odzyskiwania
> * Przywracanie dysku z punktu odzyskiwania
> * Tworzenie maszyny wirtualnej na podstawie przywróconego dysku

Aby uzyskać informacje na temat przywracania dysku i tworzenia odzyskanej maszyny wirtualnej przy użyciu programu PowerShell, zobacz [Back up and restore Azure VMs with PowerShell (Tworzenie kopii zapasowej i przywracanie maszyn wirtualnych platformy Azure przy użyciu programu PowerShell)](backup-azure-vms-automation.md#restore-an-azure-vm).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.18 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania tego samouczka jest wymagana maszyna wirtualna z systemem Linux, chroniona przy użyciu usługi Azure Backup. Aby zasymulować proces przypadkowego usunięcia maszyny wirtualnej i jej odzyskania, utworzysz maszynę wirtualną na podstawie dysku w punkcie odzyskiwania. Jeśli potrzebujesz maszyny wirtualnej z systemem Linux chronionej przy użyciu usługi Azure Backup, zobacz [Tworzenie kopii zapasowej maszyny wirtualnej na platformie Azure za pomocą interfejsu wiersza polecenia](quick-backup-vm-cli.md).

## <a name="backup-overview"></a>Omówienie usługi Backup

Po zainicjowaniu tworzenia kopii zapasowej przez platformę Azure rozszerzenie kopii zapasowej na maszynie wirtualnej tworzy migawkę punktu w czasie. Rozszerzenie kopii zapasowej jest instalowane na maszynie wirtualnej w momencie pierwszego żądania utworzenia kopii zapasowej. Usługa Azure Backup również może utworzyć migawkę bazowego magazynu, jeśli maszyna wirtualna nie jest uruchomiona w momencie tworzenia kopii zapasowej.

Domyślnie usługa Azure Backup tworzy kopię zapasową spójną na poziomie systemu plików. Po utworzeniu migawki w usłudze Azure Backup dane są przesyłane do magazynu usługi Recovery Services. Aby zmaksymalizować wydajność, usługa Azure Backup rozpoznaje i przesyła jedynie te bloki danych, które uległy zmianie od czasu utworzenia poprzedniej kopii zapasowej.

Po ukończeniu przesyłania danych migawka jest usuwana, a utworzony zostaje punkt odzyskiwania.

## <a name="list-available-recovery-points"></a>Wyświetlanie listy dostępnych punktów odzyskiwania

Aby przywrócić dysk, należy wybrać punkt odzyskiwania będący źródłem danych do odzyskania. Domyślne zasady przewidują tworzenie punktów odzyskiwania codziennie i przechowywanie ich przez 30 dni, dzięki czemu możesz wybrać określony punkt w czasie do odzyskania z dostępnego zestawu punktów odzyskiwania.

Aby wyświetlić listę dostępnych punktów odzyskiwania, użyj polecenia [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list). Do odzyskiwania dysków używana jest **nazwa** punktu odzyskiwania. W tym samouczku użyjemy najnowszego dostępnego punktu odzyskiwania. Parametr `--query [0].name` wybiera nazwę najnowszego punktu odzyskiwania, jak pokazano poniżej:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --backup-management-type AzureIaasVM
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```

## <a name="restore-a-vm-disk"></a>Przywracanie dysku maszyny wirtualnej

> [!IMPORTANT]
> Bardzo zdecydowanie zaleca się użycie interfejsu wiersza polecenia Az w wersji 2.0.74 lub nowszej, aby uzyskać wszystkie korzyści wynikające z szybkiego przywracania, w tym przywracanie dysku zarządzanego. Najlepiej, jeśli użytkownik zawsze używa najnowszej wersji.

### <a name="managed-disk-restore"></a>Przywracanie dysku zarządzanego

Jeśli kopia zapasowa maszyny Wirtualnej zarządzała dyskami i celem jest przywrócenie dysków zarządzanych z punktu odzyskiwania, najpierw należy podać konto magazynu platformy Azure. To konto magazynu służy do przechowywania konfiguracji maszyny Wirtualnej i szablonu wdrożenia, który może być później użyty do wdrożenia maszyny Wirtualnej z przywróconych dysków. Następnie należy również podać docelową grupę zasobów dla dysków zarządzanych, które mają zostać przywrócone.

1. Aby utworzyć konto magazynu, użyj polecenia [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create). Nazwa konta magazynu może zawierać tylko małe litery i musi być globalnie unikatowa. Zastąp ciąg *mystorageaccount* własną unikatową nazwą:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Przywróć dysk z punktu odzyskiwania, uruchamiając polecenie [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks). Zastąp ciąg *mystorageaccount* nazwą konta magazynu utworzonego przy użyciu poprzedniego polecenia. Zastąp *myRecoveryPointName* nazwą punktu odzyskiwania uzyskaną w danych wyjściowych z poprzedniego polecenia [az backup recoverypoint list.](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) ***Należy również podać docelową grupę zasobów, do której są przywracane dyski zarządzane.***

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
        --target-resource-group targetRG
    ```

> [!WARNING]
> Jeśli grupa zasobów docelowych nie zostanie podana, dyski zarządzane zostaną przywrócone jako dyski niezarządzane na danym koncie magazynu. Będzie to miało istotne konsekwencje dla czasu przywracania, ponieważ czas całkowicie pochłonięta przywróceniem dysków zależy od danego konta magazynu.

### <a name="unmanaged-disks-restore"></a>Przywracanie dysków niezarządzanych

Jeśli kopia zapasowa maszyny Wirtualnej ma dyski niezarządzane i jeśli celem jest przywrócenie dysków z punktu odzyskiwania, najpierw należy podać konto magazynu platformy Azure. To konto magazynu służy do przechowywania konfiguracji maszyny Wirtualnej i szablonu wdrożenia, który może być później użyty do wdrożenia maszyny Wirtualnej z przywróconych dysków. Domyślnie dyski niezarządzane zostaną przywrócone do ich oryginalnych kont magazynu. Jeśli użytkownik chce przywrócić wszystkie dyski niezarządzane w jednym miejscu, dane konto magazynu może być również używane jako lokalizacja przejściowa dla tych dysków.

Dodatkowe czynności obejmują tworzenie maszyny wirtualnej przy użyciu przywróconego dysku.

1. Aby utworzyć konto magazynu, użyj polecenia [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create). Nazwa konta magazynu może zawierać tylko małe litery i musi być globalnie unikatowa. Zastąp ciąg *mystorageaccount* własną unikatową nazwą:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Przywróć dysk z punktu odzyskiwania, uruchamiając polecenie [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks). Zastąp ciąg *mystorageaccount* nazwą konta magazynu utworzonego przy użyciu poprzedniego polecenia. Zastąp ciąg *myRecoveryPointName* nazwą punktu odzyskiwania uzyskaną wcześniej przy użyciu polecenia [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list):

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```

Jak wspomniano powyżej, dyski niezarządzane zostaną przywrócone do ich oryginalnego konta magazynu. Zapewnia to najlepszą wydajność przywracania. Ale jeśli wszystkie dyski niezarządzane muszą zostać przywrócone do danego konta magazynu, użyj odpowiedniej flagi, jak pokazano poniżej.

```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
        --restore-to-staging-storage-account
    ```

## Monitor the restore job

To monitor the status of restore job, use [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list):

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

Dane wyjściowe są podobne do następującego przykładu informującego, że zadanie przywracania jest w toku (*InProgress*):

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Po zakończeniu *raportów* *o stanie* zadania przywracania niezbędne informacje (konfiguracja maszyny wirtualnej i szablon wdrożenia) zostały przywrócone do konta magazynu.

## <a name="create-a-vm-from-the-restored-disk"></a>Tworzenie maszyny wirtualnej na podstawie przywróconego dysku

Ostatnim krokiem jest utworzenie maszyny Wirtualnej z przywróconych dysków. Szablon wdrożenia pobrany na dane konto magazynu służy do tworzenia maszyny Wirtualnej.

### <a name="fetch-the-job-details"></a>Pobieranie szczegółów zadania

Wynikowe szczegóły zadania podać identyfikator URI szablonu, który może być wyszukiwane i wdrażane. Użyj polecenia show zadania, aby uzyskać więcej szczegółów dla wyzwalane zadanie przywrócone.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414
```

Dane wyjściowe tej kwerendy poda wszystkie szczegóły, ale jesteśmy zainteresowani tylko zawartością konta magazynu. Możemy użyć [możliwości zapytania](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest) platformy Azure CLI, aby pobrać odpowiednie szczegóły

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag

{
  "Config Blob Container Name": "myVM-daa1931199fd4a22ae601f46d8812276",
  "Config Blob Name": "config-myVM-1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414.json",
  "Config Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/config-appvm8-1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json",
  "Job Type": "Recover disks",
  "Recovery point time ": "12/25/2019 10:07:11 PM",
  "Target Storage Account Name": "mystorageaccount",
  "Target resource group": "mystorageaccountRG",
  "Template Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
}
```

### <a name="fetch-the-deployment-template"></a>Pobieranie szablonu wdrożenia

Szablon nie jest bezpośrednio dostępny, ponieważ znajduje się pod kontem magazynu klienta i danym kontenerem. Aby uzyskać dostęp do tego szablonu, potrzebujemy pełnego adresu URL (wraz z tymczasowym tokenem Sygnatury dostępu współdzielonego).

Najpierw wyodrębnij identyfikator Uri obiektu blob szablonu ze szczegółów zadania

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag."""Template Blob Uri"""

"https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
```

Szablon obiektu blob Uri będzie tego formatu i wyodrębnić nazwę szablonu

```https
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

Tak więc nazwa szablonu z ```azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json``` powyższego przykładu będzie i nazwa kontenera jest```myVM-daa1931199fd4a22ae601f46d8812276```

Teraz pobierz token Sygnatury dostępu Współdzielonego dla tego kontenera i szablonu, jak opisano [tutaj](https://docs.microsoft.com/azure/azure-resource-manager/templates/secure-template-with-sas-token?tabs=azure-cli#provide-sas-token-during-deployment)

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group mystorageaccountRG \
    --name mystorageaccount \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
   --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --output tsv \
    --connection-string $connection)
```

### <a name="deploy-the-template-to-create-the-vm"></a>Wdrażanie szablonu w celu utworzenia maszyny Wirtualnej

Teraz wdrożyć szablon, aby utworzyć maszynę wirtualną, jak wyjaśniono [w tym miejscu](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli).

```azurecli-interactive
az group deployment create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

Aby potwierdzić utworzenie maszyny wirtualnej na podstawie odzyskanego dysku, wyświetl listę maszyn wirtualnych w grupie zasobów przy użyciu polecenia [az vm list](/cli/azure/vm?view=azure-cli-latest#az-vm-list), jak pokazano poniżej:

```azurecli-interactive
az vm list --resource-group myResourceGroup --output table
```

## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym samouczkiem przywrócono dysk z punktu odzyskiwania i utworzono na jego podstawie maszynę wirtualną. W tym samouczku omówiono:

> [!div class="checklist"]
>
> * Wyświetlanie listy i wybieranie punktów odzyskiwania
> * Przywracanie dysku z punktu odzyskiwania
> * Tworzenie maszyny wirtualnej na podstawie przywróconego dysku

Przejdź do następnego samouczka, aby dowiedzieć się więcej o przywracaniu poszczególnych plików z punktu odzyskiwania.

> [!div class="nextstepaction"]
> [Przywracanie plików na maszynę wirtualną na platformie Azure](tutorial-restore-files.md)
