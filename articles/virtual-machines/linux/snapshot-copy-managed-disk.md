---
title: Tworzenie migawki dysku VHD na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzenie kopii wirtualnego dysku twardego na platformie Azure jako kopię w górę lub do rozwiązywania problemów.
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/11/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 37b77847fc55d2aa90b313d6746d82b402f42ae6
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55460747"
---
# <a name="create-a-snapshot"></a>Utwórz migawkę 

Utworzenie migawki dysku systemu operacyjnego lub dane kopii zapasowej lub rozwiązywania problemów dotyczących maszyny Wirtualnej. Migawka jest pełna, tylko do odczytu kopię dysku VHD. 

## <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure 

Poniższy przykład wymaga użycia [Cloud Shell](https://shell.azure.com/bash) lub zainstalować interfejs wiersza polecenia platformy Azure.

Poniższe kroki pokazują, jak wykonać migawkę przy użyciu **Tworzenie migawki az** polecenia **— dysk źródłowy** parametru. W poniższym przykładzie założono, że istnieje Maszynę wirtualną o nazwie *myVM* w *myResourceGroup* grupy zasobów.

Pobieranie przy użyciu Identyfikatora dysku [az vm show](/cli/azure/vm#az-vm-show).

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Utworzenie migawki o nazwie *kopii zapasowej osDisk* przy użyciu [Tworzenie migawki az](/cli/azure/snapshot#az-snapshot-create).

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Jeśli chcesz przechowywać migawek w strefie utworzenia niezawodnego magazynu, należy je utworzyć w regionie, który obsługuje [strefy dostępności](../../availability-zones/az-overview.md) i obejmują **— jednostka sku Standard_ZRS** parametru.

Można wyświetlić listę migawek przy użyciu [az snapshot list](/cli/azure/snapshot#az-snapshot-list).

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Uruchamianie w lewym górnym rogu, kliknij przycisk **Utwórz zasób** i wyszukaj **migawki**. Wybierz **migawki** w wynikach wyszukiwania.
3. W **migawki** bloku kliknij **Utwórz**.
4. Wprowadź **nazwa** migawki.
5. Wybierz istniejącą grupę zasobów lub wpisz nazwę dla nowego. 
7. Aby uzyskać **dysku źródłowego**, wybierz dysku zarządzanego do migawki.
8. Wybierz **typ konta** służące do przechowywania migawki. Użyj **standardowych dysków Twardych** chyba że potrzebne w udziale o wysokiej wydajności dysków SSD.
9. Kliknij pozycję **Utwórz**.


## <a name="next-steps"></a>Kolejne kroki

 Trwa tworzenie dysku zarządzanego z migawki, a następnie Dołączanie nowego dysku zarządzanego jako dysk systemu operacyjnego, utwórz maszynę wirtualną z migawki. Aby uzyskać więcej informacji, zobacz [tworzenie maszyny Wirtualnej na podstawie migawki](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) skryptu.

