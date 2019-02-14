---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 82b3349afd10b585a10619229a2bc6d849d71524
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2019
ms.locfileid: "56247047"
---
## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupę zasobów platformy Azure można utworzyć za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Do utworzenia konta magazynu (ogólnego przeznaczenia) służy polecenie [az storage account create](/cli/azure/storage/account). Konta magazynu można używać z wszystkimi czterema usługami: obiektami blob, plikami, tabelami i kolejkami. 

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

Najpierw wyświetl klucze konta magazynu przy użyciu polecenia [az storage account keys list](/cli/azure/storage/account/keys):

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
