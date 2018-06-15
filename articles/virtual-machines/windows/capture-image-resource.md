---
title: Tworzenie zarządzanego obrazu na platformie Azure | Dokumentacja firmy Microsoft
description: Tworzenie zarządzanego obrazu maszyny Wirtualnej lub wirtualnego dysku twardego uogólnionego na platformie Azure. Obrazy mogą służyć do tworzenia wiele maszyn wirtualnych, które używają dysków zarządzanych.
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
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 4445787fd559c6d0a6dfc891910cb9a139a6907e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31602573"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Tworzenie zarządzanego obrazu uogólniony maszyny wirtualnej na platformie Azure

Można utworzyć zasobu zarządzanego obrazu z ogólnych maszyny Wirtualnej, która jest przechowywana jako dysków zarządzanych lub niezarządzanych dysku na koncie magazynu. Aby utworzyć wiele maszyn wirtualnych można następnie obrazu. 

## <a name="generalize-the-windows-vm-using-sysprep"></a>Maszyny Wirtualnej systemu Windows za pomocą programu Sysprep do uogólnienia

Program Sysprep usuwa wszystkie informacje osobiste konto, między innymi i przygotowuje komputer do użycia jako obraz. Aby uzyskać więcej informacji o narzędziu Sysprep, zobacz [sposobu użycia programu Sysprep: wprowadzenie](http://technet.microsoft.com/library/bb457073.aspx).

Upewnij się, że ról serwera uruchomionych na komputerze są obsługiwane przez program Sysprep. Aby uzyskać więcej informacji, zobacz [Obsługa programu Sysprep dla ról serwera](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Po uruchomieniu programu sysprep na maszynie Wirtualnej, jest on uznawany za *uogólniony* i nie można uruchomić ponownie. Proces uogólnianie maszyny Wirtualnej jest nieodwracalne. Jeśli trzeba zachować oryginalne działania maszyny Wirtualnej, należy podjąć [kopiowania maszyny wirtualnej](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) i generalize kopii. 
>
> Jeśli korzystasz z programu Sysprep przed przekazaniem dysk VHD do platformy Azure po raz pierwszy, upewnij się, masz [przygotować maszyny Wirtualnej](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) przed uruchomieniem programu Sysprep.  
> 
> 

1. Zaloguj się do maszyny wirtualnej systemu Windows.
2. Otwórz okno Wiersz polecenia jako administrator. Zmień katalog na **%windir%\system32\sysprep**, a następnie uruchom `sysprep.exe`.
3. W **narzędzie przygotowania systemu** okno dialogowe, wybierz opcję **wprowadź systemu Out-of-Box Experience (OOBE)** i upewnij się, że **Generalize** pole wyboru jest zaznaczone.
4. W **opcje zamykania**, wybierz pozycję **zamknięcia**.
5. Kliknij przycisk **OK**.
   
    ![Uruchom program Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Po zakończeniu działania programu Sysprep, zamyka maszyny wirtualnej. Nie uruchamiaj ponownie maszyny Wirtualnej.


## <a name="create-a-managed-image-in-the-portal"></a>Tworzenie zarządzanego obrazu w portalu 

1. Otwórz [portal](https://portal.azure.com).
2. W menu po lewej stronie kliknij maszyn wirtualnych, a następnie wybierz maszynę Wirtualną z listy.
3. Na stronie dla maszyny Wirtualnej, w menu górnym, kliknij przycisk **przechwytywania**.
3. W **nazwa**, wpisz nazwę, która ma zostać użyte na potrzeby obrazu.
4. W **grupy zasobów** wybierz opcję **Utwórz nowy** i wpisz nazwę lub wybierz **Użyj istniejącego** i wybierz grupę zasobów do użycia z listy rozwijanej.
5. Jeśli chcesz usunąć źródłowej maszyny Wirtualnej po obraz został utworzony, wybierz pozycję **automatycznie Usuń tę maszynę wirtualną po utworzeniu obrazu**.
6. Po zakończeniu kliknij przycisk **Utwórz**.
16. Po utworzeniu obrazu, zobaczysz go jako **obrazu** zasobu na liście zasobów w grupie zasobów.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Tworzenie obrazu maszyny wirtualnej przy użyciu programu Powershell

Tworzenie obrazu bezpośrednio z maszyny Wirtualnej sprawdza, czy obraz zawiera wszystkie dyski skojarzonych z maszyną Wirtualną, w tym dysku systemu operacyjnego i dysków z danymi. W tym przykładzie przedstawiono sposób tworzenia zarządzanego obrazu z maszyny Wirtualnej używa dyskach zarządzanych.


Przed rozpoczęciem upewnij się, że masz najnowszą wersję modułu programu AzureRM.Compute PowerShell. W tym artykule wymaga AzureRM wersji modułu 5.7.0 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.


> [!NOTE]
> Jeśli chcesz przechowywać obrazu w strefie odporność pamięci masowej, należy go utworzyć w regionie, który obsługuje [stref dostępności](../../availability-zones/az-overview.md) i obejmują `-ZoneResilient` parametr w konfiguracji obrazu.


1. Utwórz niektóre zmienne.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Upewnij się, że cofnięciu przydziału maszyny Wirtualnej.

    ```azurepowershell-interactive
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Ustaw stan maszyny wirtualnej na **Uogólniono**. 
   
    ```azurepowershell-interactive
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Przełącz maszynę wirtualną. 

    ```azurepowershell-interactive
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Utwórz konfigurację obrazu.

    ```azurepowershell-interactive
    $image = New-AzureRmImageConfig -Location $location -SourceVirtualMachineId $vm.ID 
    ```
6. Tworzenie obrazu.

    ```azurepowershell-interactive
    New-AzureRmImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 
## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Tworzenie obrazu z dyskiem zarządzanym przy użyciu programu PowerShell

Jeśli chcesz utworzyć obraz dysku systemu operacyjnego, można również utworzyć obrazu, określając identyfikatorze dysku zarządzanego jako dysk systemu operacyjnego.

    
1. Utwórz niektóre zmienne. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Uzyskiwanie maszyny Wirtualnej.

   ```azurepowershell-interactive
   $vm = Get-AzureRmVm -Name $vmName -ResourceGroupName $rgName
   ```

3. Pobierz identyfikator dysku zarządzanego.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Utwórz konfigurację obrazu.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Tworzenie obrazu.

    ```azurepowershell-interactive
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Tworzenie obrazu z migawki za pomocą programu Powershell

Można utworzyć obraz zarządzanego z migawki uogólniony maszyny wirtualnej.

    
1. Utwórz niektóre zmienne. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Pobieranie migawki.

   ```azurepowershell-interactive
   $snapshot = Get-AzureRmSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Utwórz konfigurację obrazu.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Tworzenie obrazu.

    ```azurepowershell-interactive
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-image-from-a-vhd-in-a-storage-account"></a>Tworzenie obrazu z pliku VHD na koncie magazynu

Tworzenie zarządzanego obrazu z uogólnionego wirtualnego dysku twardego systemu operacyjnego na koncie magazynu. Potrzebujesz identyfikatora URI dysku VHD na koncie magazynu, który jest w formacie https://*mojekontomagazynu*.blob.core.windows.net/*kontenera*/*vhd_filename.vhd*. W tym przykładzie używamy wirtualny dysk twardy jest w *mojekontomagazynu* w kontenerze o nazwie *vhdcontainer* i nazwa pliku wirtualnego dysku twardego jest *osdisk.vhd*.


1.  Najpierw należy ustawić wspólne parametry:

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```
2. Step\deallocate maszyny Wirtualnej.

    ```azurepowershell-interactive
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Maszyna wirtualna zostać oznaczone jako uogólniony.

    ```azurepowershell-interactive
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized 
    ```
4.  Utworzyć obraz przy użyciu programu uogólniony wirtualny dysk twardy systemu operacyjnego.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Kolejne kroki
- Teraz możesz [utworzyć Maszynę wirtualną z uogólniony obraz zarządzanych](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

