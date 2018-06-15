---
title: Konwertuj maszyny wirtualnej systemu Windows z dysków niezarządzanych do zarządzanych dysków - Azure zarządzanych dysków | Dokumentacja firmy Microsoft
description: Sposób konwertowania maszyny Wirtualnej systemu Windows z dysków niezarządzanych do zarządzanych dysków za pomocą programu PowerShell w modelu wdrażania usługi Resource Manager
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: cynthn
ms.openlocfilehash: 92168ba5605e119d42ba40ee694cebb3ad116041
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2018
ms.locfileid: "29804219"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Konwertuj maszynę wirtualną systemu Windows z dysków niezarządzanych do zarządzanych dysków

Jeśli masz istniejące Windows maszynach wirtualnych (VM) korzystające z niezarządzanego dysków, można przekonwertować maszyny wirtualne do zarządzanych dysków za pomocą [dysków zarządzanych Azure](managed-disks-overview.md) usługi. Ten proces konwersji zarówno dysk systemu operacyjnego i wszystkich dysków dołączonych danych.

W tym artykule przedstawiono sposób konwertowania maszyn wirtualnych przy użyciu programu Azure PowerShell. Jeśli musisz zainstalować lub uaktualnić go, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="before-you-begin"></a>Przed rozpoczęciem


* Przegląd [planowanie migracji do zarządzanych dysków](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Przegląd [— często zadawane pytania dotyczące migracji do zarządzanych dysków](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>Konwertuj maszyn wirtualnych z jednego wystąpienia
W tej sekcji omówiono sposób konwertowania maszyn wirtualnych Azure jednym wystąpieniu z dysków niezarządzanych do zarządzanych dysków. (W przypadku maszyn wirtualnych w zestawie dostępności, zobacz następną sekcję). 

1. Cofnięcie przydziału maszyny Wirtualnej za pomocą [Stop AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) polecenia cmdlet. Poniższy przykład cofa alokację maszyny Wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`: 

  ```azurepowershell-interactive
  $rgName = "myResourceGroup"
  $vmName = "myVM"
  Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
  ```

2. Konwertuj maszynę Wirtualną do zarządzanych dysków za pomocą [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) polecenia cmdlet. Następujący proces konwertuje poprzedniej maszyny Wirtualnej, w tym dysku systemu operacyjnego i wszelkie dyski danych i uruchomienie maszyny wirtualnej:

  ```azurepowershell-interactive
  ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
  ```



## <a name="convert-vms-in-an-availability-set"></a>Konwertuj maszyn wirtualnych w zestawie dostępności

Jeśli maszyny wirtualne, które ma zostać przekonwertowany na zarządzany dyski znajdują się w zestawie dostępności, należy najpierw przekonwertować zestaw z zestawem dostępności zarządzanego dostępności.

1. Konwertuj zestawu za pomocą dostępności [AzureRmAvailabilitySet aktualizacji](/powershell/module/azurerm.compute/update-azurermavailabilityset) polecenia cmdlet. Poniższy przykład aktualizuje zestaw o nazwie dostępności `myAvailabilitySet` w grupie zasobów o nazwie `myResourceGroup`:

  ```azurepowershell-interactive
  $rgName = 'myResourceGroup'
  $avSetName = 'myAvailabilitySet'

  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
  ```

  Jeśli znajduje się regionu, w których wartość jego dostępność ma tylko 2 domen błędów zarządzanych, ale liczba domen błędów niezarządzanym jest 3, to polecenie przedstawia błąd podobny do "określona liczba domen błędów 3 musi należeć do zakresu od 1 do 2." Aby rozwiązać problem, zaktualizuj domeny błędów 2 i aktualizacji `Sku` do `Aligned` w następujący sposób:

  ```azurepowershell-interactive
  $avSet.PlatformFaultDomainCount = 2
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
  ```

2. Cofnięcie przydziału i przekonwertować maszyny wirtualne w zestawie dostępności. Poniższy skrypt cofa alokację każdej maszyny Wirtualnej za pomocą [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) konwertuje go za pomocą polecenia cmdlet, [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk)i ponownie go uruchamia automatycznie jako od siebie procesu konwersji :

  ```azurepowershell-interactive
  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

  foreach($vmInfo in $avSet.VirtualMachinesReferences)
  {
     $vm = Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
  }
  ```


## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli występuje błąd podczas konwersji lub Jeśli maszyna wirtualna jest w stanie awarii z powodu problemów dotyczących w poprzedniej konwersji, uruchom `ConvertTo-AzureRmVMManagedDisk` ponownie polecenia cmdlet. Proste ponownych prób zwykle odblokowuje sytuacji.
Przed przekonwertowaniem, upewnij się, wszystkie rozszerzenia maszyny Wirtualnej są w stanie pomyślnie udostępnianie lub konwersji zakończy się niepowodzeniem z kodem błędu 409.


## <a name="next-steps"></a>Kolejne kroki

[Konwertuj dyski standardowe zarządzanego na — wersja premium](convert-disk-storage.md)

Podjąć tylko do odczytu kopię maszyny Wirtualnej za pomocą [migawki](snapshot-copy-managed-disk.md).

