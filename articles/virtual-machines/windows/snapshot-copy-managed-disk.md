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
ms.openlocfilehash: bc74a3eea1f99de6080788d6f3fddcac823092dc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75370905"
---
# <a name="create-a-snapshot"></a>Tworzenie migawki

Migawka to pełna kopia tylko do odczytu wirtualnego dysku twardego (VHD). Możesz utworzyć migawkę dysku VHD systemu operacyjnego lub danych, aby użyć go jako kopii zapasowej, lub rozwiązać problemy z maszynami wirtualnymi.

Jeśli zamierzasz utworzyć nową maszynę wirtualną przy użyciu migawki, zalecamy wyczyszczenie maszyny wirtualnej przed wykonaniem migawki, aby wyczyścić wszystkie procesy, które są w toku.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal 

Aby utworzyć migawkę, wykonaj następujące czynności: 
1.  Na [Azure Portal](https://portal.azure.com)wybierz pozycję **Utwórz zasób**.
2. Wyszukaj i wybierz pozycję **migawka**.
3. W oknie **migawki** wybierz pozycję **Utwórz**. Zostanie wyświetlone okno **Utwórz migawkę** .
4. Wprowadź **nazwę** migawki.
5. Wybierz istniejącą [grupę zasobów](../../azure-resource-manager/management/overview.md#resource-groups) lub wprowadź nazwę nowej. 
6. Wybierz **lokalizację** centrum danych Azure.  
7. W polu **dysk źródłowy**wybierz dysk zarządzany do utworzenia migawki.
8. Wybierz **Typ konta** , który ma być używany do przechowywania migawki. Wybierz pozycję **Standard_HDD**, chyba że potrzebujesz, aby migawka była przechowywana na dysku o wysokiej wydajności.
9. Wybierz pozycję **Utwórz**.

## <a name="use-powershell"></a>Używanie programu PowerShell

Poniższe kroki pokazują, jak skopiować dysk VHD i utworzyć konfigurację migawki. Następnie można wykonać migawkę dysku za pomocą polecenia cmdlet [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) . 

 

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
