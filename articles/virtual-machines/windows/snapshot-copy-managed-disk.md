---
title: Tworzenie migawki dysku VHD na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć kopię maszynie Wirtualnej platformy Azure, można użyć jako kopii zapasowej lub Rozwiązywanie problemów z.
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: b3b9095cd7ee3fa12523b14f59cc06820b9e4382
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64692219"
---
# <a name="create-a-snapshot"></a>Tworzenie migawki

Migawka jest pełna, tylko do odczytu kopię wirtualnego dysku twardego (VHD). Można utworzyć migawkę dysku systemu operacyjnego lub dane wirtualnego dysku twardego do użycia jako kopii zapasowej lub rozwiązywać problemy z maszyny wirtualnej (VM).

Jeśli zamierzasz utworzyć nową maszynę Wirtualną przy użyciu migawki, zalecamy nie pozostawia żadnych śladów zamykanie maszyny Wirtualnej przed wykonaniem migawki, aby umożliwić wyczyszczenie wszystkie procesy, które są w toku.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Z menu po lewej stronie wybierz **Utwórz zasób**, a następnie wyszukaj i wybierz pozycję **migawki**.
3. W **migawki** wybierz **Utwórz**. **Utwórz migawkę** zostanie wyświetlone okno.
4. Wprowadź **nazwa** migawki.
5. Wybierz istniejącą [grupy zasobów](../../azure-resource-manager/resource-group-overview.md#resource-groups) lub wprowadź nazwę nowej. 
6. Wybierz **lokalizację** centrum danych Azure.  
7. Aby uzyskać **dysku źródłowego**, wybierz dysku zarządzanego do migawki.
8. Wybierz **typ konta** służące do przechowywania migawki. Wybierz **Standard_HDD**, chyba że potrzebujesz migawki mają być przechowywane na dysku o wysokiej wydajności.
9. Wybierz pozycję **Utwórz**.

## <a name="use-powershell"></a>Korzystanie z programu PowerShell

Poniższe kroki pokazują jak skopiuj dysk VHD, utworzyć konfigurację migawki i migawki dysku przy użyciu [New AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) polecenia cmdlet. 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

1. Ustaw niektóre parametry: 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. Uzyskiwanie maszyny Wirtualnej:

   ```azurepowershell-interactive
   $vm = get-azvm `
   -ResourceGroupName $resourceGroupName 
   -Name $vmName
   ```

3. Utwórz konfigurację migawki. W tym przykładzie ma migawki dysku systemu operacyjnego:

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig 
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id 
   -Location $location 
   -CreateOption copy
   ```
   
   > [!NOTE]
   > Jeśli chcesz przechowywać migawek w strefie utworzenia niezawodnego magazynu, utwórz go w regionie, który obsługuje [strefy dostępności](../../availability-zones/az-overview.md) i obejmują `-SkuName Standard_ZRS` parametru.   
   
4. Migawki:

   ```azurepowershell-interactive
   New-AzSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>Kolejne kroki

Trwa tworzenie dysku zarządzanego z migawki, a następnie Dołączanie nowego dysku zarządzanego jako dysk systemu operacyjnego, utwórz maszynę wirtualną z migawki. Aby uzyskać więcej informacji, zobacz przykład w [tworzenie maszyny Wirtualnej na podstawie migawki przy użyciu programu PowerShell](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
