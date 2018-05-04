## <a name="overview"></a>Przegląd
Podczas tworzenia nowej maszyny wirtualnej (VM) w grupie zasobów przez wdrożenie obrazu z [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/), domyślnym dysku systemu operacyjnego jest często 127 GB (niektóre obrazy mają mniejsze rozmiary dysku systemu operacyjnego domyślnie). Mimo iż możliwe jest dodawanie dysków danych do maszyny wirtualnej (ich liczba zależy od wybranej jednostki magazynowej), a ponadto zaleca się instalowanie aplikacji i obciążeń intensywnie wykorzystujących procesor CPU na tych dodatkowych dyskach, klienci często muszą rozszerzać dysk systemu operacyjnego w celu obsługi niektórych scenariuszy, takich jak następujące:

1. Obsługa starszych aplikacji, które instalują składniki na dysku systemu operacyjnego.
2. Migrowanie fizycznego komputera lub maszyny wirtualnej ze środowiska lokalnego z większym dyskiem systemu operacyjnego.

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: model wdrażania przy użyciu usługi Resource Manager i model klasyczny. W tym artykule opisano używanie modelu usługi Resource Manager. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.
> 
> 
> [!WARNING]
> Zmiana rozmiaru dysku systemu operacyjnego dla maszyny wirtualnej platformy Azure spowoduje ponowne uruchomienie.
>

## <a name="resize-the-os-drive"></a>Zmiana rozmiaru dysku systemu operacyjnego
W tym artykule opisano zadanie zmiany rozmiaru dysku systemu operacyjnego przy użyciu modułów usługi Resource Manager programu [Azure Powershell](/powershell/azureps-cmdlets-docs). Pokazano zmiana rozmiaru dysku systemu operacyjnego dla dysków zarówno Unamanged i kod zarządzany, ponieważ podejście do zmiany rozmiaru dysków różni się od obu typów dysków.

### <a name="for-resizing-unmanaged-disks"></a>Do zmiany rozmiaru dysków niezarządzanego:

Otwórz okno programu Powershell ISE lub Powershell w trybie administracyjnym:

1. Zaloguj się na swoje konto platformy Microsoft Azure w trybie zarządzania zasobami i wybierz swoją subskrypcję w następujący sposób:
   
   ```Powershell
   Connect-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Ustaw nazwę swojej grupy zasobów i nazwę maszyny wirtualnej w następujący sposób:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Uzyskaj odwołanie do maszyny wirtualnej w następujący sposób:
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Zatrzymaj maszynę wirtualną przed zmianą rozmiaru dysku w następujący sposób:
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. A teraz moment, na który wszyscy czekaliśmy! Ustaw rozmiar dysku systemu operacyjnego niezarządzanego na żądaną wartość i zaktualizuj maszynę Wirtualną w następujący sposób:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > Nowy rozmiar powinien być większy niż istniejący rozmiar dysku. Maksymalna dozwolona wartość to 2048 GB dla dysków systemu operacyjnego. (Można rozwinąć obiektu blob dysku VHD poza ten rozmiar, ale system operacyjny będą mogli tylko do pracy z pierwszego 2048 GB miejsca).
   > 
   > 
6. Zaktualizowanie maszyny wirtualnej może potrwać kilka sekund. Po zakończeniu wykonywania polecenia uruchom ponownie maszynę wirtualną w następujący sposób:
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

### <a name="for-resizing-managed-disks"></a>Do zmiany rozmiaru dysków zarządzane:

Otwórz okno programu Powershell ISE lub Powershell w trybie administracyjnym:

1. Zaloguj się na swoje konto platformy Microsoft Azure w trybie zarządzania zasobami i wybierz swoją subskrypcję w następujący sposób:
   
   ```Powershell
   Connect-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Ustaw nazwę swojej grupy zasobów i nazwę maszyny wirtualnej w następujący sposób:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Uzyskaj odwołanie do maszyny wirtualnej w następujący sposób:
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Zatrzymaj maszynę wirtualną przed zmianą rozmiaru dysku w następujący sposób:
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Uzyskaj odwołanie do dysku zarządzanego systemu operacyjnego. Ustaw rozmiar dysku systemu operacyjnego z zarządzanych na żądaną wartość i Aktualizuj dysku w następujący sposób:
   
   ```Powershell
   $disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > Nowy rozmiar powinien być większy niż istniejący rozmiar dysku. Maksymalna dozwolona wartość to 2048 GB dla dysków systemu operacyjnego. (Można rozwinąć obiektu blob dysku VHD poza ten rozmiar, ale system operacyjny będą mogli tylko do pracy z pierwszego 2048 GB miejsca).
   > 
   > 
6. Zaktualizowanie maszyny wirtualnej może potrwać kilka sekund. Po zakończeniu wykonywania polecenia uruchom ponownie maszynę wirtualną w następujący sposób:
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

To wszystko! Teraz połącz protokół RDP z maszyną wirtualną, otwórz okno Zarządzanie komputerem (lub Zarządzanie dyskiem) i rozszerz dysk przy użyciu nowo przydzielonego miejsca.

## <a name="summary"></a>Podsumowanie
W tym artykule rozszerzyliśmy dysk systemu operacyjnego maszyny wirtualnej IaaS przy użyciu modułów usługi Azure Resource Manager programu Powershell. Pełną skrypt użytkownikowi zarówno niezarządzany i kod zarządzany dysków jest przedstawiony poniżej:

Dyski Unamanged:

```Powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```
Dyski zarządzane:

```Powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRMVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="next-steps"></a>Następne kroki
Chociaż w tym artykule, firma Microsoft skupia się głównie na zwiększeniem rozmiaru dysku Unamanged/zarządzane systemu operacyjnego maszyny wirtualnej, rozwinięte skrypt może także służyć do rozszerzania na dyskach danych dołączonych do maszyny Wirtualnej. Aby na przykład rozszerzyć pierwszy dysk danych dołączony do maszyny wirtualnej, zamień obiekt ```OSDisk``` elementu ```StorageProfile``` na tablicę ```DataDisks``` i przy użyciu indeksu liczbowego uzyskaj odwołanie do pierwszego dołączonego dysku danych, jak pokazano poniżej:

Dysk Unamanged:
```Powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```
Dysk zarządzany:
```Powershell
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```

W podobny sposób możesz odwoływać się do innych dysków danych dołączonych do maszyny wirtualnej — przy użyciu indeksu, jak pokazano powyżej, lub przy użyciu właściwości ```Name``` dysku, jak przedstawiono poniżej:

Dysk Unamanged:
```Powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```
Zarządzanych dysku:
```Powershell
(Get-AzureRmDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

Jeśli chcesz dowiedzieć się, jak dołączyć dyski do maszyny wirtualnej usługi Azure Resource Manager, zapoznaj się z tym [artykułem](../articles/virtual-machines/windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

