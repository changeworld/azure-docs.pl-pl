---
title: Tworzenie obrazu zarządzanego na platformie Azure | Dokumentacja firmy Microsoft
description: Tworzenie obrazu zarządzanego uogólnionej maszyny Wirtualnej lub wirtualnego dysku twardego na platformie Azure. Obrazy może służyć do tworzenia wielu maszyn wirtualnych, które korzystają z dysków zarządzanych.
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
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: aa1858a27d4df413deb562391251a523c28673ad
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787951"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Tworzenie obrazu zarządzanego uogólnionej maszyny Wirtualnej na platformie Azure

Można utworzyć zasobu obrazu zarządzanego z uogólnionej maszyny wirtualnej (VM), która jest przechowywana jako dysk zarządzany lub dysk niezarządzany na koncie magazynu. Obraz, który następnie może służyć do tworzenia wielu maszyn wirtualnych. Aby uzyskać informacje dotyczące zarządzanych obrazy są rozliczane, zobacz [cennika usługi Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/). 

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="generalize-the-windows-vm-using-sysprep"></a>Uogólnianie maszyny wirtualnej z systemem Windows za pomocą narzędzia Sysprep

Narzędzie Sysprep usuwa wszystkie konta osobistego i informacje o zabezpieczeniach, a następnie przygotowuje komputer, który ma być używany jako obraz. Aby uzyskać informacji o narzędziu Sysprep, zobacz [Omówienie programu Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Upewnij się, że role serwera uruchomionego na maszynie są obsługiwane przez program Sysprep. Aby uzyskać więcej informacji, zobacz [Obsługa narzędzia Sysprep dla ról serwera](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Po uruchomieniu programu Sysprep na maszynie Wirtualnej tej maszyny Wirtualnej jest uznawana za *uogólniony* i nie można uruchomić ponownie. Proces uogólniania maszyny wirtualnej jest nieodwracalny. Należy zachować, oryginalnym działania maszyny Wirtualnej, należy utworzyć [kopiowania maszyny wirtualnej](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) i uogólnianie jego kopię. 
>
> Jeśli użytkownik chce uruchomić programu Sysprep przed przekazaniem wirtualnego dysku twardego (VHD) na platformie Azure po raz pierwszy, upewnij się, masz [przygotować maszyny Wirtualnej](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  
> 
> 

Aby uogólnić maszynę Wirtualną Windows, wykonaj następujące kroki:

1. Zaloguj się do usługi Windows maszyny Wirtualnej.
   
2. Otwórz okno wiersza polecenia jako administrator. Zmień katalog na % windir%\system32\sysprep, a następnie uruchom `sysprep.exe`.
   
3. W **narzędzie przygotowywania systemu** okno dialogowe, wybierz opcję **wprowadź System Out-of-Box środowiska (OOBE)** i wybierz **Generalize** pole wyboru.
   
4. Aby uzyskać **opcje zamykania**, wybierz opcję **zamknięcia**.
   
5. Kliknij przycisk **OK**.
   
    ![Uruchom program Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)

6. Po zakończeniu działania programu Sysprep, zamknięcie maszyny Wirtualnej. Nie uruchamiaj ponownie maszyny Wirtualnej.


## <a name="create-a-managed-image-in-the-portal"></a>Tworzenie obrazu zarządzanego w portalu 

1. Otwórz [portal Azure](https://portal.azure.com).

2. W menu po lewej stronie wybierz **maszyn wirtualnych** i następnie wybierz maszynę Wirtualną z listy.

3. W **maszyny wirtualnej** strony dla maszyny Wirtualnej, w prawym górnym menu wybierz **przechwytywania**.

   **Tworzenie obrazu** zostanie wyświetlona strona.

4. Aby uzyskać **nazwa**, zaakceptuj nazwę wstępnie wypełnione albo wprowadź nazwę, której chcesz użyć dla obrazu.

5. Aby uzyskać **grupy zasobów**, wybierz opcję **Utwórz nową** i wprowadź nazwę lub wybierz **Użyj istniejącej** i wybierz grupę zasobów, do użycia z listy rozwijanej.

6. Jeśli chcesz usunąć źródłową maszynę Wirtualną po obraz został utworzony, wybierz opcję **automatycznie Usuń tę maszynę wirtualną po utworzeniu obrazu**.

7. Jeśli chcesz, aby możliwość używania obrazu w dowolnym [strefy dostępności](../../availability-zones/az-overview.md), wybierz opcję **na** dla **odporności stref**.

8. Wybierz **Utwórz** do utworzenia obrazu.

9. Po utworzeniu obrazu możesz znaleźć go w formie **obraz** zasób na liście zasobów w grupie zasobów.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Tworzenie obrazu maszyny wirtualnej przy użyciu programu Powershell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Tworzenie obrazu bezpośrednio z poziomu maszyny Wirtualnej sprawdza, czy obraz, który zawiera wszystkie dyski skojarzone z maszyną Wirtualną, w tym dysk systemu operacyjnego i dysków z danymi. Ten przykład przedstawia sposób tworzenia obrazu zarządzanego z maszyny Wirtualnej używa dysków zarządzanych.

Przed rozpoczęciem upewnij się, że masz najnowszą wersję modułu Azure PowerShell. Aby znaleźć wersję, uruchom `Get-Module -ListAvailable Az` w programie PowerShell. Jeśli musisz uaktualnić, zobacz [Instalowanie programu Azure PowerShell na Windows przy użyciu funkcji PowerShellGet](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, uruchom `Connect-AzAccount` do utworzenia połączenia z platformą Azure.


> [!NOTE]
> Jeśli chcesz przechowywać obraz w magazyn strefowo nadmiarowy, należy je utworzyć w regionie, który obsługuje [strefy dostępności](../../availability-zones/az-overview.md) i obejmują `-ZoneResilient` parametr w konfiguracji obrazu (`New-AzImageConfig` polecenie).

Aby utworzyć obraz maszyny Wirtualnej, wykonaj następujące kroki:

1. Utwórz zmienne.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Upewnij się, że maszyna wirtualna została wycofana.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Ustaw stan maszyny wirtualnej do **Uogólniono**. 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Pobierz maszynę wirtualną. 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Utwórz konfigurację obrazu.

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. Utwórz obraz.

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Tworzenie obrazu na podstawie dysku zarządzanego przy użyciu programu PowerShell

Jeśli chcesz utworzyć obraz na dysku systemu operacyjnego, należy określić identyfikator dysku zarządzanego jako dysk systemu operacyjnego:

    
1. Utwórz zmienne. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. Uzyskiwanie maszyny Wirtualnej.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. Pobierz identyfikator dysku zarządzanego.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Utwórz konfigurację obrazu.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Utwórz obraz.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Tworzenie obrazu na podstawie migawki przy użyciu programu Powershell

Można utworzyć obrazu zarządzanego z migawki uogólnionej maszyny Wirtualnej, wykonując następujące czynności:

    
1. Utwórz zmienne. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Pobieranie migawki.

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Utwórz konfigurację obrazu.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Utwórz obraz.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vhd-in-a-storage-account"></a>Tworzenie obrazu na podstawie dysku VHD na koncie magazynu

Utworzenie obrazu zarządzanego z uogólnionego wirtualnego dysku twardego systemu operacyjnego na koncie magazynu. Potrzebujesz identyfikatora URI dysku VHD na koncie magazynu, który znajduje się w następującym formacie: https://*mystorageaccount*.blob.core.windows.net/*vhdcontainer* /  *vhdfilename.VHD*. W tym przykładzie wirtualny dysk twardy znajduje się w *mystorageaccount*, w kontenerze o nazwie *vhdcontainer*, a nazwa pliku wirtualnego dysku twardego jest *vhdfilename.vhd*.


1.  Utwórz zmienne.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. Zatrzymaj/Cofnij Przydział maszyny Wirtualnej.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Oznaczanie maszyny Wirtualnej jako uogólnionej.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Tworzenie obrazu przy użyciu uogólnionego wirtualnego dysku twardego systemu operacyjnego.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Kolejne kroki
- [Tworzenie maszyny Wirtualnej na podstawie obrazu zarządzanego](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).    

