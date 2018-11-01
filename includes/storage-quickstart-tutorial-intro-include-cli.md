---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: c5cb1c5a005265950f8dcd02c8e7675918751dab
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165168"
---
## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupę zasobów platformy Azure można utworzyć za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Do utworzenia konta magazynu (ogólnego przeznaczenia) służy polecenie [az storage account create](/cli/azure/storage/account#create). Konta magazynu można używać z wszystkimi czterema usługami: obiektami blob, plikami, tabelami i kolejkami. 

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>Podawanie poświadczeń konta magazynu

Większość poleceń interfejsu wiersza polecenia platformy Azure zawartych w tym samouczku wymaga poświadczeń do konta magazynu. Dostępnych jest kilka opcji podania tych poświadczeń, ale najprostszy sposób polega na ustawieniu zmiennych środowiskowych `AZURE_STORAGE_ACCOUNT` i `AZURE_STORAGE_ACCESS_KEY`.

Najpierw wyświetl klucze konta magazynu przy użyciu polecenia [az storage account keys list](/cli/azure/storage/account/keys#list):

```azurecli-interactive
az storage account keys list \
    --account-name mystorageaccount \
    --resource-group myResourceGroup \
    --output table
```

Teraz ustaw zmienne środowiskowe `AZURE_STORAGE_ACCOUNT` i `AZURE_STORAGE_ACCESS_KEY`. Możesz to zrobić w powłoce Bash przy użyciu polecenia `export`:

```bash
export AZURE_STORAGE_ACCOUNT="mystorageaccountname"
export AZURE_STORAGE_ACCESS_KEY="myStorageAccountKey"
```