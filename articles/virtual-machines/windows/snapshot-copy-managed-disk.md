---
title: Tworzenie migawki wirtualnego dysku twardego na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć kopię maszyny Wirtualnej platformy Azure do użycia jako kopii zapasowej lub podczas rozwiązywania problemów.
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
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 7d45fd749fea4036d944d740541d8b8607553835
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658159"
---
# <a name="create-a-snapshot"></a>Utwórz migawkę

Utwórz migawkę dysku systemu operacyjnego lub danych, który wystawia wirtualnego dysku twardego do utworzenia kopii zapasowej lub rozwiązywania problemów z maszyny Wirtualnej. Migawka jest pełna, tylko do odczytu kopię dysku VHD. 

## <a name="use-azure-portal-to-take-a-snapshot"></a>Utworzenie migawki za pomocą portalu Azure 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Uruchamianie w lewym górnym rogu, kliknij przycisk **Utwórz zasób** i wyszukaj **migawki**.
3. W bloku migawki kliknij **Utwórz**.
4. Wprowadź **nazwa** dla migawki.
5. Wybierz istniejącą [grupę zasobów](../../azure-resource-manager/resource-group-overview.md#resource-groups) lub wprowadź nazwę nowej grupy zasobów. 
6. Wybierz centrum danych Azure lokalizacji.  
7. Dla **dysku źródłowego**, wybierz zarządzany dysk do migawki.
8. Wybierz **typ konta** do przechowywania migawki. Firma Microsoft zaleca **Standard_LRS** chyba że ma być przechowywane na dysku wysokiej wydajności.
9. Kliknij przycisk **Utwórz**.

## <a name="use-powershell-to-take-a-snapshot"></a>Utworzenie migawki za pomocą programu PowerShell

Poniższe kroki pokazują jak uzyskać dysku VHD do skopiowania, Tworzenie migawki konfiguracji i migawki dysku przy użyciu [AzureRmSnapshot nowy](/powershell/module/azurerm.compute/new-azurermsnapshot) polecenia cmdlet. 

Przed rozpoczęciem upewnij się, że masz najnowszą wersję modułu programu AzureRM.Compute PowerShell. W tym artykule wymaga AzureRM wersji modułu 5.7.0 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

Ustaw niektórych parametrów. 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  
```

Uzyskiwanie maszyny Wirtualnej.

 ```azurepowershell-interactive
$vm = get-azurermvm `
   -ResourceGroupName $resourceGroupName `
   -Name $vmName
```

Utworzenie migawki konfiguracji. W tym przykładzie zamierzamy migawki dysk systemu operacyjnego.

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig `
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
   -Location $location `
   -CreateOption copy
```
   
> [!NOTE]
> Jeśli chcesz przechowywać w strefie odporność pamięci masowej migawki, należy utworzyć ją w regionie, który obsługuje [stref dostępności](../../availability-zones/az-overview.md) i obejmują `-SkuName Standard_ZRS` parametru.   

   
Migawki.

```azurepowershell-interactive
New-AzureRmSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```




## <a name="next-steps"></a>Kolejne kroki

Utwórz maszynę wirtualną z migawki, tworząc dysków zarządzanych z migawki, a następnie Trwa dołączanie nowego dysku zarządzanego jako dysk systemu operacyjnego. Aby uzyskać więcej informacji, zobacz [tworzenie maszyny Wirtualnej z migawki](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) próbki.
