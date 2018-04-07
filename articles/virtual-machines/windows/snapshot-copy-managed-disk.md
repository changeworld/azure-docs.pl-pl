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
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: c5f4c7224e04b601d7d3fe4da7d8f5f0c02c7039
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
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

Upewnij się, że masz najnowszą wersję zainstalowany moduł AzureRM.Compute PowerShell. Uruchom następujące polecenie, aby go zainstalować.

```
Install-Module AzureRM.Compute -MinimumVersion 2.6.0
```
Aby uzyskać więcej informacji, zobacz [przechowywanie wersji programu Azure PowerShell](/powershell/azure/overview).


1. Ustaw niektórych parametrów. 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$dataDiskName = 'myDisk' 
$snapshotName = 'mySnapshot'  
```

2. Pobierz dysku VHD do skopiowania.

 ```azurepowershell-interactive
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName -DiskName $dataDiskName 
```
3. Tworzenie migawki konfiguracji. 

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig -SourceUri $disk.Id -CreateOption Copy -Location $location 
```
4. Migawki.

 ```azurepowershell-interactive
New-AzureRmSnapshot -Snapshot $snapshot -SnapshotName $snapshotName -ResourceGroupName $resourceGroupName 
```
Jeśli planujesz użyć migawki, aby utworzyć dysk zarządzany i dołączyć maszynę Wirtualną, która musi być wydajnych, użyj parametru `-AccountType Premium_LRS` przy użyciu polecenia New-AzureRmSnapshot. Parametr tworzy migawkę tak, aby była przechowywana jako dysk zarządzane Premium. Dysków zarządzanych w warstwie Premium są droższe niż standardowe. Dlatego upewnij się, że naprawdę potrzebny Premium przed użyciem tego parametru.

## <a name="next-steps"></a>Kolejne kroki

Utwórz maszynę wirtualną z migawki, tworząc dysków zarządzanych z migawki, a następnie Trwa dołączanie nowego dysku zarządzanego jako dysk systemu operacyjnego. Aby uzyskać więcej informacji, zobacz [tworzenie maszyny Wirtualnej z migawki](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) próbki.
