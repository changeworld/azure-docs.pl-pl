---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: 4ad977dc8cbaa85360092dbfd391a3c3b88f67bb
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747925"
---
## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupę zasobów platformy Azure można utworzyć za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

```azurecli-interactive
az group create \
    --name <resource-group-name> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Do utworzenia konta magazynu (ogólnego przeznaczenia) służy polecenie [az storage account create](/cli/azure/storage/account). Konta magazynu można używać z wszystkimi czterema usługami: obiektami blob, plikami, tabelami i kolejkami.

Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group <resource-group-name> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>Podawanie poświadczeń konta magazynu

Większość poleceń interfejsu wiersza polecenia platformy Azure zawartych w tym samouczku wymaga poświadczeń do konta magazynu. Dostępnych jest kilka opcji podania tych poświadczeń, ale najprostszy sposób polega na ustawieniu zmiennych środowiskowych `AZURE_STORAGE_ACCOUNT` i `AZURE_STORAGE_KEY`.

> [!NOTE]
> W tym artykule pokazano, jak ustawić zmienne środowiskowe za pomocą bash. Inne środowiska mogą wymagać modyfikacji składni.

Najpierw Wyświetl klucze konta magazynu przy użyciu polecenia [AZ Storage account Keys list](/cli/azure/storage/account/keys) . Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

```azurecli-interactive
az storage account keys list \
    --account-name <account-name> \
    --resource-group <resource-group-name> \
    --output table
```

Teraz ustaw zmienne środowiskowe `AZURE_STORAGE_ACCOUNT` i `AZURE_STORAGE_KEY`. Można to zrobić w powłoce bash za pomocą polecenia `export`. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

```bash
export AZURE_STORAGE_ACCOUNT="<account-name>"
export AZURE_STORAGE_KEY="<account-key>"
```

Aby uzyskać więcej informacji na temat pobierania kluczy dostępu do konta przy użyciu Azure Portal, zobacz **klucze dostępu** w obszarze [Zarządzaj ustawieniami konta magazynu w Azure Portal](../articles/storage/common/storage-account-manage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#access-keys).