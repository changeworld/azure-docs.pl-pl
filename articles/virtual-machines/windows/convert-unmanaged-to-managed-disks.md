---
title: Konwertuj maszynę wirtualną Windows z dysków niezarządzanych do usługi managed disks - usługi Azure Managed Disks | Dokumentacja firmy Microsoft
description: Jak przekonwertować maszyny Wirtualnej z systemem Windows z dysków niezarządzanych do dysków zarządzanych przy użyciu programu PowerShell w modelu wdrażania usługi Resource Manager
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: rogarana
ms.openlocfilehash: 21505da414b29f2ae9eeea7f9fcad9db2e57c4fe
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702827"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Konwertuj maszynę wirtualną Windows z dysków niezarządzanych do usługi managed disks

Jeśli masz istniejące Windows maszyny wirtualne (VM), które korzystają z dysków niezarządzanych, można przekonwertować maszyny wirtualne do używania dysków zarządzanych za pomocą [usługi Azure Managed Disks](managed-disks-overview.md) usługi. Ten proces konwertuje dysk systemu operacyjnego i wszelkich dołączonych dysków danych.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem


* Przegląd [zaplanować migrację do usługi Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Przegląd [— często zadawane pytania dotyczące migracji do usługi Managed Disks](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>Konwertowanie maszyn wirtualnych z jednego wystąpienia
W tej sekcji opisano sposób konwertowania maszyn wirtualnych platformy Azure z jednego wystąpienia z dysków niezarządzanych do dysków zarządzanych. (W przypadku maszyn wirtualnych w zestawie dostępności, zobacz następną sekcję). 

1. Cofnij Przydział maszyny Wirtualnej przy użyciu [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) polecenia cmdlet. Poniższy przykład powoduje cofnięcie przydziału maszyny Wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`: 

   ```azurepowershell-interactive
   $rgName = "myResourceGroup"
   $vmName = "myVM"
   Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
   ```

2. Konwertowanie maszyny Wirtualnej do usługi managed disks przy użyciu [ConvertTo-AzVMManagedDisk](https://docs.microsoft.com/powershell/module/az.compute/convertto-azvmmanageddisk) polecenia cmdlet. Następujący proces konwertuje poprzedniej maszyny Wirtualnej, łącznie z dysku systemu operacyjnego i wszelkich dysków z danymi i uruchomienie maszyny wirtualnej:

   ```azurepowershell-interactive
   ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
   ```



## <a name="convert-vms-in-an-availability-set"></a>Konwertowanie maszyn wirtualnych w zestawie dostępności

Jeśli maszyny wirtualne, które chcesz przekonwertować zarządzane dyski znajdują się w zestawie dostępności, należy najpierw przekonwertować zestawie dostępności do zarządzanego zestawu dostępności.

1. Konwertowanie zestawu za pomocą dostępności [AzAvailabilitySet aktualizacji](https://docs.microsoft.com/powershell/module/az.compute/update-azavailabilityset) polecenia cmdlet. Poniższy przykład aktualizuje zestawu dostępności o nazwie `myAvailabilitySet` w grupie zasobów o nazwie `myResourceGroup`:

   ```azurepowershell-interactive
   $rgName = 'myResourceGroup'
   $avSetName = 'myAvailabilitySet'

   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
   ```

   Jeśli znajduje się region, w których wartość poziomu dostępności ma tylko 2 domen błędów zarządzanych, ale liczba domen błędów niezarządzane to 3, to polecenie wyświetla błąd podobny do "Liczba domen błędów określonego 3 muszą należeć do zakresu od 1 do 2". Aby naprawić błąd, należy zaktualizować domenę błędów 2 i update `Sku` do `Aligned` w następujący sposób:

   ```azurepowershell-interactive
   $avSet.PlatformFaultDomainCount = 2
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
   ```

2. Cofanie przydziału i konwertowania maszyn wirtualnych w zestawie dostępności. Poniższy skrypt powoduje cofnięcie przydziału każdej maszyny Wirtualnej przy użyciu [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) konwertuje go za pomocą polecenia cmdlet, [ConvertTo-AzVMManagedDisk](https://docs.microsoft.com/powershell/module/az.compute/convertto-azvmmanageddisk)i ponownie uruchamia automatycznie jako od siebie procesu konwersji:

   ```azurepowershell-interactive
   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

   foreach($vmInfo in $avSet.VirtualMachinesReferences)
   {
     $vm = Get-AzVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
   }
   ```


## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli wystąpi błąd podczas konwersji lub maszyny Wirtualnej jest w stanie niepowodzenia ze względu na problemy z konwersją poprzedniej, uruchom `ConvertTo-AzVMManagedDisk` ponownie polecenie cmdlet. Proste ponownych prób zwykle odblokowuje sytuacji.
Przed przekonwertowaniem, upewnij się, wszystkie rozszerzenia maszyny Wirtualnej są w stanie pomyślnie aprowizacji lub konwersja zakończy się niepowodzeniem z kodem błędu 409.


## <a name="convert-using-the-azure-portal"></a>Konwertowanie za pomocą witryny Azure portal

Można również przeprowadzić konwersję dysków niezarządzanych do dysków zarządzanych przy użyciu witryny Azure portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz maszynę Wirtualną z listy maszyn wirtualnych w portalu.
3. W bloku maszyny wirtualnej, wybierz **dysków** z menu.
4. W górnej części **dysków** bloku wybierz **migracji do usługi managed disks**.
5. Jeśli maszyna wirtualna znajduje się w zestawie dostępności, będzie ostrzeżenie na **migracji do usługi managed disks** bloku, który należy przekonwertować najpierw zestawu dostępności. Ostrzeżenie powinien mieć łącze, można kliknąć w celu konwersji zestawu dostępności. Po konwersji zestawu dostępności lub maszyna wirtualna nie znajduje się w zestawie dostępności, kliknij przycisk **migracji** można uruchomić procesu migracji dysków do usługi managed disks. 

Maszyny Wirtualnej zostanie zatrzymana i uruchomiona ponownie po zakończeniu migracji.

## <a name="next-steps"></a>Kolejne kroki

[Konwertowanie dysków zarządzanych w warstwie standardowa do warstwy premium](convert-disk-storage.md)

Wykonaj kopię tylko do odczytu maszyny wirtualnej przy użyciu [migawek](snapshot-copy-managed-disk.md).

