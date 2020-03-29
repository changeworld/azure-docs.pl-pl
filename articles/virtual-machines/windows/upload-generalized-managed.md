---
title: Tworzenie maszyny wirtualnej z przesłanego uogólnionego dysku wirtualnego
description: Przekaż uogólniony dysk VHD na platformę Azure i użyj go do tworzenia nowych maszyn wirtualnych w modelu wdrażania usługi Resource Manager.
services: virtual-machines-windows
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/12/2019
ms.author: cynthn
ms.openlocfilehash: 3c482caf2407c89ffdb6c55c9184c31e2e3197c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464938"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Przekazywanie uogólnionego dysku VHD i używanie go do tworzenia nowych maszyn wirtualnych na platformie Azure

W tym artykule otrzymasz od ciebie informacje dotyczące przekazywania dysku wirtualnego na platformie Azure przy użyciu programu PowerShell, aby przekazać dysk wirtualny uogólnionej maszyny Wirtualnej na platformę Azure, utworzyć obraz z dysku wirtualnego i utworzyć nową maszynę wirtualną z tego obrazu. Dysk wirtualny wyeksportowany z lokalnego narzędzia do wirtualizacji lub z innej chmury można przekazać dysk wirtualny. Korzystanie z [dysków zarządzanych](managed-disks-overview.md) dla nowej maszyny Wirtualnej upraszcza zarządzanie maszyną wirtualną i zapewnia lepszą dostępność, gdy maszyna wirtualna jest umieszczana w zestawie dostępności. 

Aby uzyskać przykładowy skrypt, zobacz [Przykładowy skrypt, aby przekazać dysk VHD na platformę Azure i utworzyć nową maszynę wirtualną](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

- Przed przekazaniem dowolnego dysku VHD na platformę Azure należy postępować zgodnie z postępującą po [przygotowaniu dysku VHD lub VHDX systemu Windows do przekazania na platformę Azure.](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
- Przejrzyj [plan migracji na dyski zarządzane](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) przed rozpoczęciem migracji na [dyski zarządzane](managed-disks-overview.md).

 
## <a name="generalize-the-source-vm-by-using-sysprep"></a>Uogólnianie źródłowej maszyny Wirtualnej przy użyciu programu Sysprep

Jeśli jeszcze tego nie zrobiłeś, musisz sysprep maszyny wirtualnej przed przekazaniem dysku twardego do platformy Azure. Narzędzie Sysprep między innymi usuwa wszystkie informacje osobiste związane z kontem i przygotowuje maszynę do używania jako obraz. Szczegółowe informacje na temat sysprep można znaleźć w [przeglądzie sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Upewnij się, że role serwera uruchomione na komputerze są obsługiwane przez sysprep. Aby uzyskać więcej informacji, zobacz [Obsługa ról serwera jako sysprep](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Jeśli planujesz uruchomić narzędzie Sysprep przed przesłaniem dysku VHD na platformę Azure po raz pierwszy, upewnij się, że [maszyna wirtualna została przygotowana.](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 
> 
> 

1. Zaloguj się do maszyny wirtualnej z systemem Windows.
2. Otwórz okno wiersza polecenia jako administrator. Zmień katalog na %windir%\system32\sysprep, a `sysprep.exe`następnie uruchom .
3. W oknie dialogowym **Narzędzie przygotowania systemu** wybierz pozycję Enter System **Out-of-Box Experience (OOBE)** i upewnij się, że pole wyboru **Generalize** jest włączone.
4. W obszarze **Opcje zamykania**wybierz **pozycję Shutdown**.
5. Kliknij przycisk **OK**.
   
    ![Uruchamianie sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Po zakończeniu sysprep, zamyka maszynę wirtualną. Nie uruchamiaj ponownie maszyny wirtualnej.


## <a name="upload-the-vhd"></a>Prześlij dysk VHD 

Teraz możesz przesłać dysk VHD bezpośrednio na dysk zarządzany. Aby uzyskać instrukcje, zobacz [Przekazywanie dysku VHD na platformę Azure przy użyciu programu Azure PowerShell.](disks-upload-vhd-to-managed-disk-powershell.md)



Po przekazaniu dysku VHD na dysk zarządzany należy użyć programu [Get-AzDisk,](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk) aby uzyskać dysk zarządzany.

```azurepowershell-interactive
$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="create-the-image"></a>Tworzenie obrazu
Utwórz obraz zarządzany na uogólnionym dysku zarządzanym systemu operacyjnego. Zastąp następujące wartości własnymi informacjami.

Najpierw ustaw kilka zmiennych:

```powershell
$location = 'East US'
$imageName = 'myImage'
$rgName = 'myResourceGroup'
```

Utwórz obraz przy użyciu dysku zarządzanego.

```azurepowershell-interactive
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsState Generalized `
   -OsType Windows `
   -ManagedDiskId $disk.Id
```

Utwórz obraz.

```azurepowershell-interactive
$image = New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```

## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Teraz, gdy masz już obraz, możesz za jego pomocą utworzyć jedną lub więcej nowych maszyn wirtualnych. W tym przykładzie utworzy maszynę wirtualną o nazwie *myVM* z *myImage*, w *myResourceGroup*.


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Image $image.Id `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Następne kroki

Zaloguj się do nowej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Jak połączyć się i zalogować się do maszyny wirtualnej platformy Azure z systemem Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

