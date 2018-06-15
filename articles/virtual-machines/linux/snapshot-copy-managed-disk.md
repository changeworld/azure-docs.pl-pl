---
title: Tworzenie migawki wirtualnego dysku twardego na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć kopię dysku VHD na platformie Azure jako kopii zapasowej lub podczas rozwiązywania problemów.
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
ms.date: 03/20/2018
ms.author: cynthn
ms.openlocfilehash: e5882b2ddc708544a7715da13c1f0d18384ce4e3
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
ms.locfileid: "30318905"
---
# <a name="create-a-snapshot"></a>Utwórz migawkę 

Utwórz migawkę dysku systemu operacyjnego lub dane kopii zapasowej lub rozwiązywać problemy z maszyny Wirtualnej. Migawka jest pełna, tylko do odczytu kopię dysku VHD. 

## <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure 

Poniższy przykład wymaga Azure CLI 2.0 zainstalowany, a zalogowany do konta platformy Azure. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). 

Poniższe kroki przedstawiają sposób wykonania migawki za pomocą `az snapshot create` z `--source-disk` parametru. W poniższym przykładzie założono, że istnieje maszyna wirtualna o nazwie `myVM` w `myResourceGroup` grupy zasobów.

Pobierz identyfikator dysku
```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

Utworzenie migawki o nazwie *kopii zapasowej osDisk*.

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Jeśli chcesz przechowywać w strefie odporność pamięci masowej migawki, należy utworzyć ją w regionie, który obsługuje [stref dostępności](../../availability-zones/az-overview.md) i obejmują `--sku Standard_ZRS` parametru.

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Począwszy od lewej górnej kliknij **Utwórz zasób** i wyszukaj **migawki**.
3. W bloku migawki kliknij **Utwórz**.
4. Wprowadź **nazwa** dla migawki.
5. Wybierz istniejącą [grupę zasobów](../../azure-resource-manager/resource-group-overview.md#resource-groups) lub wprowadź nazwę nowej grupy zasobów. 
6. Wybierz centrum danych Azure lokalizacji.  
7. Dla **dysku źródłowego**, wybierz zarządzany dysk do migawki.
8. Wybierz **typ konta** do przechowywania migawki. Firma Microsoft zaleca **Standard_LRS** chyba że ma być przechowywane na dysku wysokiej wydajności.
9. Kliknij przycisk **Utwórz**.


## <a name="next-steps"></a>Kolejne kroki

 Utwórz maszynę wirtualną z migawki, tworząc dysków zarządzanych z migawki, a następnie Trwa dołączanie nowego dysku zarządzanego jako dysk systemu operacyjnego. Aby uzyskać więcej informacji, zobacz [tworzenie maszyny Wirtualnej z migawki](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) skryptu.

