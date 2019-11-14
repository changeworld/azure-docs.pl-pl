---
title: Tworzenie migawki wirtualnego dysku twardego na platformie Azure
description: Dowiedz się, jak utworzyć kopię maszyny wirtualnej platformy Azure, która ma być używana jako kopia zapasowa lub w celu rozwiązywania problemów.
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: b564e20ca8aa5acd7fbd4ea69ac2b1cd72e66d5e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075334"
---
# <a name="create-a-snapshot"></a>Tworzenie migawki

Migawka to pełna kopia tylko do odczytu wirtualnego dysku twardego (VHD). Możesz utworzyć migawkę dysku VHD systemu operacyjnego lub danych, aby użyć go jako kopii zapasowej, lub rozwiązać problemy z maszynami wirtualnymi.

Jeśli zamierzasz utworzyć nową maszynę wirtualną przy użyciu migawki, zalecamy wyczyszczenie maszyny wirtualnej przed wykonaniem migawki, aby wyczyścić wszystkie procesy, które są w toku.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Z menu po lewej stronie wybierz pozycję **Utwórz zasób**, a następnie wyszukaj i wybierz pozycję **migawka**.
3. W oknie **migawki** wybierz pozycję **Utwórz**. Zostanie wyświetlone okno **Utwórz migawkę** .
4. Wprowadź **nazwę** migawki.
5. Wybierz istniejącą [grupę zasobów](../../azure-resource-manager/resource-group-overview.md#resource-groups) lub wprowadź nazwę nowej. 
6. Wybierz **lokalizację** centrum danych Azure.  
7. W polu **dysk źródłowy**wybierz dysk zarządzany do utworzenia migawki.
8. Wybierz **Typ konta** , który ma być używany do przechowywania migawki. Wybierz pozycję **Standard_HDD**, chyba że potrzebujesz, aby migawka była przechowywana na dysku o wysokiej wydajności.
9. Wybierz pozycję **Utwórz**.

## <a name="use-powershell"></a>Korzystanie z programu PowerShell

Poniższe kroki pokazują, jak skopiować dysk VHD, utworzyć konfigurację migawki i wykonać migawkę dysku za pomocą polecenia cmdlet [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) . 

 

1. Ustaw niektóre parametry: 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. Pobierz maszynę wirtualną:

   ```azurepowershell-interactive
   $vm = get-azvm `
   -ResourceGroupName $resourceGroupName 
   -Name $vmName
   ```

3. Utwórz konfigurację migawki. W tym przykładzie migawka jest dyskiem systemu operacyjnego:

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig 
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id 
   -Location $location 
   -CreateOption copy
   ```
   
   > [!NOTE]
   > Jeśli chcesz przechowywać migawkę w magazynie odpornym na strefy, utwórz ją w regionie, który obsługuje [strefy dostępności](../../availability-zones/az-overview.md) i Uwzględnij parametr `-SkuName Standard_ZRS`.   
   
4. Zrób migawkę:

   ```azurepowershell-interactive
   New-AzSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>Następne kroki

Tworzenie maszyny wirtualnej na podstawie migawki przez utworzenie dysku zarządzanego na podstawie migawki, a następnie dołączenie nowego dysku zarządzanego jako dysku systemu operacyjnego. Aby uzyskać więcej informacji, zobacz przykład w temacie [Tworzenie maszyny wirtualnej na podstawie migawki przy użyciu programu PowerShell](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
