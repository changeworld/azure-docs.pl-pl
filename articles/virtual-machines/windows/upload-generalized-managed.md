---
title: Tworzenie zarządzanej maszyny wirtualnej platformy Azure na podstawie uogólnionego lokalnego dysku VHD | Microsoft Docs
description: Przekaż uogólniony wirtualny dysk twardy do platformy Azure i użyj go do utworzenia nowych maszyn wirtualnych w modelu wdrażania Menedżer zasobów.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/25/2018
ms.author: cynthn
ms.openlocfilehash: ead44c321ffb7afb69295d1bf8c0e1acd26cb9ce
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749072"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Przekazywanie uogólnionego wirtualnego dysku twardego i używanie go do tworzenia nowych maszyn wirtualnych na platformie Azure

W tym artykule przedstawiono sposób użycia programu PowerShell do przekazywania dysku VHD uogólnionej maszyny wirtualnej do platformy Azure, tworzenia obrazu z dysku VHD i tworzenia nowej maszyny wirtualnej na podstawie tego obrazu. Można przekazać wirtualny dysk twardy wyeksportowany z lokalnego narzędzia do wirtualizacji lub z innej chmury. Używanie [Managed disks](managed-disks-overview.md) dla nowej maszyny wirtualnej upraszcza zarządzanie maszyną wirtualną i zapewnia lepszą dostępność, gdy maszyna wirtualna jest umieszczona w zestawie dostępności. 

Przykładowy skrypt zawiera [przykładowy skrypt służący do przekazywania wirtualnego dysku twardego do platformy Azure i tworzenia nowej maszyny wirtualnej](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

- Przed przekazaniem dysku VHD na platformę Azure należy wykonać następujące czynności [Przygotuj plik VHD lub VHDX systemu Windows w celu przekazania go do platformy Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Przejrzyj [plan migracji do Managed disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) przed rozpoczęciem migracji, aby [Managed disks](managed-disks-overview.md).

 


## <a name="generalize-the-source-vm-by-using-sysprep"></a>Uogólnij źródłową maszynę wirtualną przy użyciu programu Sysprep

Narzędzie Sysprep między innymi usuwa wszystkie informacje osobiste związane z kontem i przygotowuje maszynę do używania jako obraz. Aby uzyskać szczegółowe informacje o programie Sysprep, zobacz [Omówienie programu Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Upewnij się, że role serwera uruchomione na komputerze są obsługiwane przez program Sysprep. Aby uzyskać więcej informacji, zobacz [Obsługa programu Sysprep dla ról serwera](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Jeśli planujesz uruchomienie programu Sysprep przed przekazaniem wirtualnego dysku twardego do platformy Azure po raz pierwszy, upewnij się, że [maszyna wirtualna została przygotowana](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Zaloguj się do maszyny wirtualnej z systemem Windows.
2. Otwórz okno wiersza polecenia jako administrator. Zmień katalog na%windir%\System32\Sysprep, a następnie uruchom `sysprep.exe`.
3. W oknie dialogowym **Narzędzie przygotowywania systemu** wybierz opcję **Wprowadź system out-of-Box Experience (OOBE)** i upewnij się, że pole wyboru **generalize** jest włączone.
4. W obszarze **Opcje zamykania**wybierz pozycję **Zamknij**.
5. Kliknij przycisk **OK**.
   
    ![Uruchom program Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Po zakończeniu działania narzędzia Sysprep zamyka ono maszynę wirtualną. Nie uruchamiaj ponownie maszyny wirtualnej.


## <a name="upload-the-vhd-to-your-storage-account"></a>Przekazywanie wirtualnego dysku twardego do konta magazynu

Możesz teraz przekazać dysk VHD bezpośrednio do dysku zarządzanego. Aby uzyskać instrukcje, zobacz [przekazywanie wirtualnego dysku twardego do platformy Azure przy użyciu Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).


## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Utwórz obraz zarządzany na podstawie przekazanego wirtualnego dysku twardego 

Utwórz obraz zarządzany na podstawie uogólnionego dysku zarządzanego systemu operacyjnego. Zastąp następujące wartości własnymi informacjami.


Najpierw ustaw niektóre parametry:

```powershell
$location = "East US" 
$imageName = "myImage"
```

Utwórz obraz przy użyciu uogólnionego wirtualnego dysku twardego systemu operacyjnego.

```powershell
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Teraz, gdy masz już obraz, możesz za jego pomocą utworzyć jedną lub więcej nowych maszyn wirtualnych. Ten przykład tworzy maszynę wirtualną o nazwie *myVM* z *obrazu*w liście *zasobów*.


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -ImageName $imageName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Następne kroki

Zaloguj się do nowej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [jak nawiązać połączenie i zalogować się do maszyny wirtualnej platformy Azure z systemem Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

