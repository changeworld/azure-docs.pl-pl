---
title: Tworzenie migawki wirtualnego dysku twardego na platformie Azure
description: Dowiedz się, jak utworzyć kopię maszyny Wirtualnej platformy Azure do użycia jako kopia zapasowa lub rozwiązywanie problemów.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75370905"
---
# <a name="create-a-snapshot"></a>Tworzenie migawki

Migawka to pełna kopia wirtualnego dysku twardego (VHD) tylko do odczytu. Można wykonać migawkę dysku twardego systemu operacyjnego lub dysku twardego z danymi, aby użyć ich jako kopii zapasowej lub rozwiązać problemy z maszyną wirtualną.You can take a snapshot of an OS or data disk VHD to use as a backup, or to troubleshoot virtual machine (VM) issues.

Jeśli zamierzasz użyć migawki do utworzenia nowej maszyny Wirtualnej, zaleca się, aby czysto zamknąć maszynę wirtualną przed wykonaniem migawki, aby wyczyścić wszystkie procesy, które są w toku.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal 

Aby utworzyć migawkę, wykonaj następujące czynności: 
1.  W [witrynie Azure portal](https://portal.azure.com)wybierz pozycję **Utwórz zasób**.
2. Wyszukaj i wybierz **migawkę**.
3. W oknie **Migawka** wybierz pozycję **Utwórz**. Zostanie wyświetle okno **Utwórz migawkę.**
4. Wprowadź **nazwę** migawki.
5. Wybierz istniejącą [grupę zasobów](../../azure-resource-manager/management/overview.md#resource-groups) lub wprowadź nazwę nowej. 
6. Wybierz **lokalizację** centrum danych Azure.  
7. W obszarze **Dysk źródłowy**wybierz dysk zarządzany do migawki.
8. Wybierz **typ konta,** który ma być używany do przechowywania migawki. Wybierz **opcję Standard_HDD**, chyba że migawka jest ujęty na dysku o wysokiej wydajności.
9. Wybierz **pozycję Utwórz**.

## <a name="use-powershell"></a>Korzystanie z programu PowerShell

Poniższe kroki pokazują, jak skopiować dysk VHD i utworzyć konfigurację migawki. Następnie można zrobić migawkę dysku za pomocą polecenia cmdlet [New-AzSnapshot.](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) 

 

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

3. Utwórz konfigurację migawki. W tym przykładzie migawka jest z dysku systemu operacyjnego:

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig 
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id 
   -Location $location 
   -CreateOption copy
   ```
   
   > [!NOTE]
   > Jeśli chcesz przechowywać migawkę w magazynie odpornym na strefy, utwórz ją `-SkuName Standard_ZRS` w regionie, który obsługuje strefy [dostępności](../../availability-zones/az-overview.md) i dołącz parametr.   
   
4. Zrób migawkę:

   ```azurepowershell-interactive
   New-AzSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>Następne kroki

Utwórz maszynę wirtualną na podstawie migawki, tworząc dysk zarządzany z migawki, a następnie dołączając nowy dysk zarządzany jako dysk systemu operacyjnego. Aby uzyskać więcej informacji, zobacz przykład w [Tworzenie maszyny Wirtualnej z migawki z programem PowerShell](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
