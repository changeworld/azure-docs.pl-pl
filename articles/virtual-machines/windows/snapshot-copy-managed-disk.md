---
title: Tworzenie migawki dysku VHD na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć kopię maszynie Wirtualnej platformy Azure, można użyć jako kopii zapasowej lub Rozwiązywanie problemów z.
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.openlocfilehash: 87d78178c32aea3ae601983ec14e9df0732b59e2
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091304"
---
# <a name="create-a-snapshot"></a>Utwórz migawkę

Migawka jest pełna, tylko do odczytu kopię wirtualnego dysku twardego (VHD). Można utworzyć migawkę dysku systemu operacyjnego lub dane wirtualnego dysku twardego do użycia jako kopii zapasowej lub rozwiązywać problemy z maszyny wirtualnej (VM). 

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

Poniższe kroki pokazują jak skopiuj dysk VHD, utworzyć konfigurację migawki i migawki dysku przy użyciu [New AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot) polecenia cmdlet. 

Przed rozpoczęciem upewnij się, masz najnowszą wersję modułu programu AzureRM.Compute PowerShell, który musi być w wersji 5.7.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli korzystasz z programu PowerShell lokalnie, uruchom [Connect-AzureRmAccount](https://docs.microsoft.com/powershell/module/azurerm.profile/connect-azurermaccount) do utworzenia połączenia z platformą Azure.

1. Ustaw niektóre parametry: 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  
```

2. Uzyskiwanie maszyny Wirtualnej:

 ```azurepowershell-interactive
$vm = get-azurermvm `
   -ResourceGroupName $resourceGroupName 
   -Name $vmName
```

3. Utwórz konfigurację migawki. W tym przykładzie ma migawki dysku systemu operacyjnego:

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig 
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id 
   -Location $location 
   -CreateOption copy
```
   
   > [!NOTE]
   > Jeśli chcesz przechowywać migawek w strefie utworzenia niezawodnego magazynu, utwórz go w regionie, który obsługuje [strefy dostępności](../../availability-zones/az-overview.md) i obejmują `-SkuName Standard_ZRS` parametru.   
   
4. Migawki:

 ```azurepowershell-interactive
New-AzureRmSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
```


## <a name="next-steps"></a>Kolejne kroki

Trwa tworzenie dysku zarządzanego z migawki, a następnie Dołączanie nowego dysku zarządzanego jako dysk systemu operacyjnego, utwórz maszynę wirtualną z migawki. Aby uzyskać więcej informacji, zobacz przykład w [tworzenie maszyny Wirtualnej na podstawie migawki przy użyciu programu PowerShell](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
