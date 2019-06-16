---
title: Rozwiń dysk systemu operacyjnego maszyny Wirtualnej z systemem Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Zwiększyć rozmiar dysku systemu operacyjnego maszyny wirtualnej przy użyciu programu Azure Powershell w modelu wdrażania usługi Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: kirpas
ms.subservice: disks
ms.openlocfilehash: 81e6b5558ab90f154ebf121a558704b00b97444d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64684324"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Sposobu rozszerzania dysku systemu operacyjnego maszyny wirtualnej

Po utworzeniu nowej maszyny wirtualnej (VM) w grupie zasobów przez wdrożenie obrazu z [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/), domyślny dysk systemu operacyjnego jest często 127 GB (niektóre obrazy mają mniejsze rozmiary dysku systemu operacyjnego domyślnie). Mimo iż możliwe jest dodawanie dysków danych do maszyny wirtualnej (ich liczba zależy od wybranej jednostki magazynowej), a ponadto zaleca się instalowanie aplikacji i obciążeń intensywnie wykorzystujących procesor CPU na tych dodatkowych dyskach, klienci często muszą rozszerzać dysk systemu operacyjnego w celu obsługi niektórych scenariuszy, takich jak następujące:

- Obsługa starszych aplikacji, które instalują składniki na dysku systemu operacyjnego.
- Migrowanie fizycznego komputera lub maszyny wirtualnej ze środowiska lokalnego z większym dyskiem systemu operacyjnego.


> [!IMPORTANT]
> Zmiana rozmiaru dysku systemu operacyjnego dla maszyny wirtualnej platformy Azure spowoduje jej ponowne uruchomienie.
>
> Po rozwinięciu na dyskach, należy [rozszerzyć woluminu w ramach systemu operacyjnego](#expand-the-volume-within-the-os) z zalet większy dysk.
> 


[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


## <a name="resize-a-managed-disk"></a>Zmiana rozmiaru dysku zarządzanego

Otwórz okno programu Powershell ISE lub Powershell w trybie administracyjnym:

1. Zaloguj się do konta usługi Microsoft Azure w trybie zarządzania zasobami, a następnie wybierz swoją subskrypcję w następujący sposób:
   
   ```powershell
   Connect-AzAccount
   Select-AzSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Ustaw nazwę swojej grupy zasobów i nazwę maszyny wirtualnej w następujący sposób:
   
   ```powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Uzyskaj odwołanie do maszyny wirtualnej w następujący sposób:
   
   ```powershell
   $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Zatrzymaj maszynę wirtualną przed zmianą rozmiaru dysku w następujący sposób:
   
    ```Powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Uzyskaj odwołanie do zarządzanego dysku systemu operacyjnego. Ustaw rozmiar zarządzanego dysku systemu operacyjnego na żądaną wartość i zaktualizuj dysk w następujący sposób:
   
   ```Powershell
   $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > Nowy rozmiar powinien być większy niż istniejący rozmiar dysku. Maksymalna dozwolona wartość to 2048 GB dla dysków systemu operacyjnego. (Istnieje możliwość rozwinąć obiektu blob dysku VHD poza ten rozmiar, ale system operacyjny będzie można tylko do pracy z pierwszym 2048 GB miejsca).
   > 
   > 
6. Zaktualizowanie maszyny wirtualnej może potrwać kilka sekund. Po zakończeniu wykonywania polecenia uruchom ponownie maszynę wirtualną w następujący sposób:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```

To wszystko! Teraz połącz protokół RDP z maszyną wirtualną, otwórz okno Zarządzanie komputerem (lub Zarządzanie dyskiem) i rozszerz dysk przy użyciu nowo przydzielonego miejsca.

## <a name="resize-an-unmanaged-disk"></a>Zmiana rozmiaru dysku niezarządzanego

Otwórz okno programu Powershell ISE lub Powershell w trybie administracyjnym:

1. Zaloguj się do konta usługi Microsoft Azure w trybie zarządzania zasobami, a następnie wybierz swoją subskrypcję w następujący sposób:
   
   ```Powershell
   Connect-AzAccount
   Select-AzSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Ustaw nazwę swojej grupy zasobów i nazwę maszyny wirtualnej w następujący sposób:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Uzyskaj odwołanie do maszyny wirtualnej w następujący sposób:
   
   ```Powershell
   $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Zatrzymaj maszynę wirtualną przed zmianą rozmiaru dysku w następujący sposób:
   
    ```Powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Ustaw rozmiar dysku niezarządzanego systemu operacyjnego na żądaną wartość i zaktualizuj maszynę Wirtualną w następujący sposób:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > Nowy rozmiar powinien być większy niż istniejący rozmiar dysku. Maksymalna dozwolona wartość to 2048 GB dla dysków systemu operacyjnego. (Istnieje możliwość rozwinąć obiektu blob dysku VHD poza ten rozmiar, ale system operacyjny będzie można tylko do pracy z pierwszym 2048 GB miejsca).
   > 
   > 
   
6. Zaktualizowanie maszyny wirtualnej może potrwać kilka sekund. Po zakończeniu wykonywania polecenia uruchom ponownie maszynę wirtualną w następujący sposób:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```


## <a name="scripts-for-os-disk"></a>Skrypty dla dysku systemu operacyjnego

Oto kompletny skrypt, dla której można się odwołać w przypadku dysków zarządzanych i niezarządzanych:


**Dyski zarządzane**

```Powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

**Dyski niezarządzane**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzVM -ResourceGroupName $rgName -VM $vm
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="resizing-data-disks"></a>Zmienianie rozmiaru dysków z danymi

Ten artykuł koncentruje się głównie na rozszerzeniu dysku systemu operacyjnego maszyny wirtualnej, ale skryptu można także rozszerzyć dyski danych dołączone do maszyny Wirtualnej. Aby na przykład rozszerzyć pierwszy dysk danych dołączony do maszyny wirtualnej, zamień obiekt `OSDisk` elementu `StorageProfile` na tablicę `DataDisks` i przy użyciu indeksu liczbowego uzyskaj odwołanie do pierwszego dołączonego dysku danych, jak pokazano poniżej:

**Dysk zarządzany**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```


**Dysk niezarządzany**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```



Podobnie możesz odwoływać się do innych dysków danych dołączonych do maszyny Wirtualnej, przy użyciu indeksu, jak pokazano powyżej lub **nazwa** właściwością dysku:


**Dysk zarządzany**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Dysk niezarządzany**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Zwiększanie pojemności w ramach systemu operacyjnego

Po rozwinięciu dysku dla maszyny Wirtualnej, musisz przejść do systemu operacyjnego, a następnie rozwiń węzeł woluminu, który ma obejmować nowe miejsce. Istnieje kilka metod dotyczące rozszerzania partycji. W tej sekcji omówiono łączenie maszyn wirtualnych przy użyciu połączenia RDP, aby rozszerzyć za pomocą partycji **narzędzia DiskPart**.

1. Otwórz z połączeniem RDP z maszyną wirtualną.

2.  Otwórz wiersz polecenia i wpisz **narzędzia diskpart**.

2.  W **narzędzia DISKPART** należy wpisać `list volume`. Zanotuj wolumin, który ma zostać rozszerzony.

3.  W **narzędzia DISKPART** należy wpisać `select volume <volumenumber>`. Spowoduje to wybranie woluminu *volumenumber* , którą chcesz rozszerzyć na ciągłe, puste miejsce na tym samym dysku.

4.  W **narzędzia DISKPART** należy wpisać `extend [size=<size>]`. Spowoduje to rozszerzenie wybranego woluminu przez *rozmiar* wyrażony w megabajtach (MB).


## <a name="next-steps"></a>Kolejne kroki

Można również dołączyć dyski przy użyciu [witryny Azure portal](attach-managed-disk-portal.md).
