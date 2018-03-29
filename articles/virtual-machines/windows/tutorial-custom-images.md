---
title: Tworzenie niestandardowych obrazów maszyn wirtualnych przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Samouczek — Tworzenie niestandardowego obrazu maszyny Wirtualnej przy użyciu programu Azure PowerShell.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 443f47b98ea063c6fe1f0b3517c00b6cf3692161
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-custom-image-of-an-azure-vm-using-powershell"></a>Tworzenie niestandardowego obrazu maszyny Wirtualnej platformy Azure przy użyciu programu PowerShell

Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Obrazy niestandardowe mogą służyć do ładowania początkowego konfiguracji, na przykład do wstępnego ładowania aplikacji, konfiguracji aplikacji i innych konfiguracji systemu operacyjnego. W tym samouczku utworzysz własny niestandardowy obraz maszyny wirtualnej platformy Azure. Omawiane kwestie:

> [!div class="checklist"]
> * Narzędzie Sysprep i generalize maszyny wirtualne
> * Tworzenie obrazu niestandardowego
> * Tworzenie maszyny wirtualnej na podstawie obrazu niestandardowego
> * Wyświetlanie listy wszystkich obrazów w subskrypcji
> * Usuwanie obrazu


## <a name="before-you-begin"></a>Przed rozpoczęciem

W poniższych krokach wyjaśniono szczegółowo, jak na podstawie istniejącej maszyny wirtualnej utworzyć obraz niestandardowy do ponownego użycia, za pomocą którego można utworzyć nowe wystąpienia maszyn wirtualnych.

Do utworzenia przykładu przedstawionego w tym samouczku potrzebna jest istniejąca maszyna wirtualna. W razie potrzeby [ten przykładowy skrypt](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) pomoże Ci go utworzyć. Podczas pracy z tym samouczkiem zamień w odpowiednich przypadkach nazwy maszyn wirtualnych i grup zasobów.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli do zainstalowania i używania programu PowerShell lokalnie, w tym samouczku, wymaga AzureRM wersji modułu 5.6.0 lub nowszym. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="prepare-vm"></a>Przygotowywanie maszyny Wirtualnej

Aby utworzyć obraz maszyny wirtualnej, należy przygotować maszyny Wirtualnej uogólnianie maszyny Wirtualnej, dealokowanie, a następnie oznaczenie źródłowej maszyny Wirtualnej, ponieważ uogólniony na platformie Azure.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Maszyny Wirtualnej systemu Windows za pomocą programu Sysprep do uogólnienia

Program Sysprep usuwa wszystkie informacje osobiste konto, między innymi i przygotowuje komputer do użycia jako obraz. Aby uzyskać więcej informacji o narzędziu Sysprep, zobacz [sposobu użycia programu Sysprep: wprowadzenie](http://technet.microsoft.com/library/bb457073.aspx).


1. Połączenie z maszyną wirtualną.
2. Otwórz okno Wiersz polecenia jako administrator. Zmień katalog na *%windir%\system32\sysprep*, a następnie uruchom *sysprep.exe*.
3. W **narzędzie przygotowania systemu** okno dialogowe, wybierz opcję *wprowadź systemu Out-of-Box Experience (OOBE)*i upewnij się, że *Generalize* pole wyboru jest zaznaczone.
4. W **opcje zamykania**, wybierz pozycję *zamknięcia* , a następnie kliknij przycisk **OK**.
5. Po zakończeniu działania programu Sysprep, zamyka maszyny wirtualnej. **Nie uruchamiaj ponownie maszynę Wirtualną**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Cofanie przydziału i oznaczanie maszyny wirtualnej jako uogólnionej

Aby utworzyć obraz, maszyna wirtualna musi być alokację oraz oznaczony jako uogólniony na platformie Azure.

Cofnięcie przydziału maszynę Wirtualną przy użyciu [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm).

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Force
```

Ustaw stan maszyny wirtualnej na `-Generalized` przy użyciu [AzureRmVm zestawu](/powershell/module/azurerm.compute/set-azurermvm). 
   
```azurepowershell-interactive
Set-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Generalized
```


## <a name="create-the-image"></a>Tworzenie obrazu

Teraz można utworzyć obrazu maszyny wirtualnej za pomocą [AzureRmImageConfig nowy](/powershell/module/azurerm.compute/new-azurermimageconfig) i [AzureRmImage nowy](/powershell/module/azurerm.compute/new-azurermimage). W poniższym przykładzie utworzono obraz o nazwie *myImage* za pomocą maszyny wirtualnej o nazwie *myVM*.

Przełącz maszynę wirtualną. 

```azurepowershell-interactive
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
```

Utwórz konfigurację obrazu.

```azurepowershell-interactive
$image = New-AzureRmImageConfig -Location EastUS -SourceVirtualMachineId $vm.ID 
```

Tworzenie obrazu.

```azurepowershell-interactive
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Tworzenie maszyn wirtualnych z obrazu

Teraz, gdy masz obrazu można utworzyć jeden lub więcej nowych maszyn wirtualnych z obrazu. Tworzenie maszyny Wirtualnej z obrazu niestandardowego jest bardzo podobny do tworzenia maszyny Wirtualnej przy użyciu obrazu z witryny Marketplace. Korzystając z obrazu z witryny Marketplace, musisz podać informacje dotyczące obrazu, dostawca obrazu, oferty, jednostki SKU i wersji. Za pomocą parametru uproszczony ustawiony dla [AzureRMVM nowy]() polecenia cmdlet, wystarczy podać nazwę niestandardowego obrazu tak długo, jak jest w tej samej grupie zasobów. 

To przykładowe polecenie tworzy Maszynę wirtualną o nazwie *myVMfromImage* z *myImage*w *myResourceGroup*.


```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```

## <a name="image-management"></a>Zarządzanie obrazami 

Oto kilka przykładów typowych zadań zarządzania dla obrazu i sposobu ich ukończenia przy użyciu programu PowerShell.

Wyświetl listę wszystkich obrazów według nazwy.

```azurepowershell-interactive
$images = Find-AzureRMResource -ResourceType Microsoft.Compute/images 
$images.name
```

Usuwanie obrazu. W tym przykładzie obraz o nazwie *myOldImage* jest usuwany z grupy *myResourceGroup*.

```azurepowershell-interactive
Remove-AzureRmImage `
    -ImageName myOldImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku został utworzony obraz niestandardowy maszyny wirtualnej. W tym samouczku omówiono:

> [!div class="checklist"]
> * Narzędzie Sysprep i generalize maszyny wirtualne
> * Tworzenie obrazu niestandardowego
> * Tworzenie maszyny wirtualnej na podstawie obrazu niestandardowego
> * Wyświetlanie listy wszystkich obrazów w subskrypcji
> * Usuwanie obrazu

Przejdź do następnego samouczkiem, aby poznać sposób wysokiej dostępności maszyny wirtualnej.

> [!div class="nextstepaction"]
> [Tworzenie maszyn wirtualnych o wysokiej dostępności](tutorial-availability-sets.md)



