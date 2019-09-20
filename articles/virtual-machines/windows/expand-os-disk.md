---
title: Rozszerzanie dysku systemu operacyjnego maszyny wirtualnej z systemem Windows na platformie Azure | Microsoft Docs
description: Zwiększ rozmiar dysku systemu operacyjnego maszyny wirtualnej przy użyciu programu Azure PowerShell w Menedżer zasobów model wdrażania.
services: virtual-machines-windows
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: kirpas
ms.subservice: disks
ms.openlocfilehash: 692046070ffc04942a5d8a73825f6cb59e462f8b
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147207"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Jak rozszerzyć dysk systemu operacyjnego maszyny wirtualnej

Podczas tworzenia nowej maszyny wirtualnej w grupie zasobów przez wdrożenie obrazu z [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/)domyślny dysk systemu operacyjnego jest często 127 GB (domyślnie niektóre obrazy mają mniejsze rozmiary dysków systemu operacyjnego). Mimo iż możliwe jest dodawanie dysków danych do maszyny wirtualnej (ich liczba zależy od wybranej jednostki magazynowej), a ponadto zaleca się instalowanie aplikacji i obciążeń intensywnie wykorzystujących procesor CPU na tych dodatkowych dyskach, klienci często muszą rozszerzać dysk systemu operacyjnego w celu obsługi niektórych scenariuszy, takich jak następujące:

- Obsługa starszych aplikacji, które instalują składniki na dysku systemu operacyjnego.
- Migrowanie fizycznego komputera lub maszyny wirtualnej ze środowiska lokalnego z większym dyskiem systemu operacyjnego.


> [!IMPORTANT]
> Zmiany rozmiarów dysku systemu operacyjnego maszyny wirtualnej platformy Azure wymagają cofnięcia przydziału maszyny wirtualnej.
>
> Po rozwinięciu dysków należy [rozszerzyć wolumin w systemie operacyjnym](#expand-the-volume-within-the-os) , aby korzystać z większego dysku.
> 


[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


## <a name="resize-a-managed-disk"></a>Zmiana rozmiaru dysku zarządzanego

Otwórz okno programu Powershell ISE lub Powershell w trybie administracyjnym:

1. Zaloguj się do konta Microsoft Azure w trybie zarządzania zasobami i wybierz swoją subskrypcję w następujący sposób:
   
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
   > Nowy rozmiar powinien być większy niż istniejący rozmiar dysku. Maksymalna dozwolona liczba dysków systemu operacyjnego to 2048 GB. (Możliwe jest rozszerzenie obiektu BLOB dysku VHD poza ten rozmiar, ale system operacyjny będzie mógł działać tylko z pierwszym 2048 GB miejsca).
   > 
   > 
6. Zaktualizowanie maszyny wirtualnej może potrwać kilka sekund. Po zakończeniu wykonywania polecenia uruchom ponownie maszynę wirtualną w następujący sposób:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```

To wszystko! Teraz połącz protokół RDP z maszyną wirtualną, otwórz okno Zarządzanie komputerem (lub Zarządzanie dyskiem) i rozszerz dysk przy użyciu nowo przydzielonego miejsca.

## <a name="resize-an-unmanaged-disk"></a>Zmień rozmiar dysku niezarządzanego

Otwórz okno programu Powershell ISE lub Powershell w trybie administracyjnym:

1. Zaloguj się do konta Microsoft Azure w trybie zarządzania zasobami i wybierz swoją subskrypcję w następujący sposób:
   
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
5. Ustaw rozmiar niezarządzanego dysku systemu operacyjnego na żądaną wartość i zaktualizuj maszynę wirtualną w następujący sposób:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > Nowy rozmiar powinien być większy niż istniejący rozmiar dysku. Maksymalna dozwolona liczba dysków systemu operacyjnego to 2048 GB. (Możliwe jest rozszerzenie obiektu BLOB dysku VHD poza ten rozmiar, ale system operacyjny będzie mógł działać tylko z pierwszym 2048 GB miejsca).
   > 
   > 
   
6. Zaktualizowanie maszyny wirtualnej może potrwać kilka sekund. Po zakończeniu wykonywania polecenia uruchom ponownie maszynę wirtualną w następujący sposób:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```


## <a name="scripts-for-os-disk"></a>Skrypty dysku systemu operacyjnego

Poniżej znajduje się kompletny skrypt dotyczący odwołania zarówno dla dysków zarządzanych, jak i niezarządzanych:


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

## <a name="resizing-data-disks"></a>Zmienianie rozmiarów dysków z danymi

Ten artykuł koncentruje się głównie na rozszerzaniu dysku systemu operacyjnego maszyny wirtualnej, ale można również użyć tego skryptu do rozwinięcia dysków danych dołączonych do maszyny wirtualnej. Aby na przykład rozszerzyć pierwszy dysk danych dołączony do maszyny wirtualnej, zamień obiekt `OSDisk` elementu `StorageProfile` na tablicę `DataDisks` i przy użyciu indeksu liczbowego uzyskaj odwołanie do pierwszego dołączonego dysku danych, jak pokazano poniżej:

**Dysk zarządzany**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```


**Dysk niezarządzany**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```



Podobnie możesz odwoływać się do innych dysków z danymi dołączonymi do maszyny wirtualnej przy użyciu indeksu, jak pokazano powyżej, lub właściwość **Nazwa** dysku:


**Dysk zarządzany**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Dysk niezarządzany**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Rozwiń wolumin w systemie operacyjnym

Po rozwinięciu dysku dla maszyny wirtualnej należy przejść do systemu operacyjnego i rozwinąć wolumin, aby obejmował nowe miejsce. Istnieje kilka metod rozszerzania partycji. Ta sekcja obejmuje łączenie maszyny wirtualnej przy użyciu połączenia RDP w celu rozszerzenia partycji za pomocą **narzędzia DiskPart**.

1. Otwórz połączenie RDP z maszyną wirtualną.

2.  Otwórz wiersz polecenia i wpisz **diskpart**.

2.  W wierszu polecenia **narzędzia DiskPart** wpisz `list volume`polecenie. Zanotuj wolumin, który chcesz zwiększyć.

3.  W wierszu polecenia **narzędzia DiskPart** wpisz `select volume <volumenumber>`polecenie. Spowoduje to wybranie *volumenumber* woluminu, który ma zostać rozbudowany do ciągłego, pustego miejsca na tym samym dysku.

4.  W wierszu polecenia **narzędzia DiskPart** wpisz `extend [size=<size>]`polecenie. Spowoduje to rozszerzenie wybranego woluminu o *rozmiar* w megabajtach (MB).


## <a name="next-steps"></a>Następne kroki

Dyski można również dołączyć przy użyciu [Azure Portal](attach-managed-disk-portal.md).
