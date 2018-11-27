---
title: Samouczek — tworzenie niestandardowych obrazów maszyn wirtualnych za pomocą programu Azure PowerShell | Microsoft Docs
description: Z tego samouczka dowiesz się, jak utworzyć niestandardowy obraz maszyny wirtualnej na platformie Azure za pomocą programu Azure PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 415652739c4987deafe820c31499132ec3829c8b
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263319"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-azure-powershell"></a>Samouczek: tworzenie niestandardowego obrazu maszyny wirtualnej na platformie Azure za pomocą programu Azure PowerShell

Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Obrazy niestandardowe mogą służyć do ładowania początkowego konfiguracji, na przykład do wstępnego ładowania aplikacji, konfiguracji aplikacji i innych konfiguracji systemu operacyjnego. W tym samouczku utworzysz własny niestandardowy obraz maszyny wirtualnej platformy Azure. Omawiane kwestie:

> [!div class="checklist"]
> * Używanie narzędzia Sysprep i uogólnianie maszyn wirtualnych
> * Tworzenie obrazu niestandardowego
> * Tworzenie maszyny wirtualnej na podstawie obrazu niestandardowego
> * Wyświetlanie listy wszystkich obrazów w subskrypcji
> * Usuwanie obrazu

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poniższych krokach wyjaśniono szczegółowo, jak na podstawie istniejącej maszyny wirtualnej utworzyć obraz niestandardowy do ponownego użycia, za pomocą którego można utworzyć nowe wystąpienia maszyn wirtualnych.

Do utworzenia przykładu przedstawionego w tym samouczku potrzebna jest istniejąca maszyna wirtualna. W razie potrzeby [ten przykładowy skrypt](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) pomoże Ci go utworzyć. Podczas pracy z tym samouczkiem zamień w odpowiednich przypadkach nazwy maszyn wirtualnych i grup zasobów.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek będzie wymagał modułu AzureRM w wersji 5.7.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="prepare-vm"></a>Przygotowywanie maszyny wirtualnej

Aby utworzyć obraz maszyny wirtualnej, należy przygotować maszynę wirtualną przez uogólnienie jej, cofnięcie przydziału, a następnie oznaczenie źródłowej maszyny wirtualnej jako uogólnionej na platformie Azure.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Uogólnianie maszyny wirtualnej z systemem Windows za pomocą narzędzia Sysprep

Narzędzie Sysprep między innymi usuwa wszystkie informacje osobiste związane z kontem i przygotowuje maszynę do używania jako obraz. Aby uzyskać więcej informacji na temat narzędzia Sysprep, zobacz [Używanie narzędzia Sysprep: wprowadzenie](https://technet.microsoft.com/library/bb457073.aspx).


1. Nawiąż połączenie z maszyną wirtualną.
2. Otwórz okno wiersza polecenia jako administrator. Zmień katalog na *%windir%\system32\sysprep*, a następnie uruchom plik *sysprep.exe*.
3. W oknie dialogowym **Narzędzie przygotowywania systemu** wybierz pozycję *Włącz systemowy tryb OOBE* i upewnij się, że pole wyboru *Uogólnij* jest zaznaczone.
4. W obszarze **Opcje zamykania** wybierz pozycję *Zamknij*, a następnie kliknij przycisk **OK**.
5. Gdy narzędzie Sysprep zakończy działanie, maszyna wirtualna zostanie wyłączona. **Nie uruchamiaj ponownie maszyny wirtualnej**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Cofanie przydziału i oznaczanie maszyny wirtualnej jako uogólnionej

Aby utworzyć obraz, należy cofnąć przydział maszyny wirtualnej i oznaczyć ją jako uogólnioną na platformie Azure.

Cofnij przydział maszyny wirtualnej za pomocą polecenia [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm).

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Force
```

Ustaw stan maszyny wirtualnej na `-Generalized` za pomocą polecenia [Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm). 
   
```azurepowershell-interactive
Set-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Generalized
```


## <a name="create-the-image"></a>Tworzenie obrazu

Teraz możesz utworzyć obraz maszyny wirtualnej za pomocą poleceń [New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) i [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage). W poniższym przykładzie utworzono obraz o nazwie *myImage* za pomocą maszyny wirtualnej o nazwie *myVM*.

Pobierz maszynę wirtualną. 

```azurepowershell-interactive
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
```

Utwórz konfigurację obrazu.

```azurepowershell-interactive
$image = New-AzureRmImageConfig -Location EastUS -SourceVirtualMachineId $vm.ID 
```

Utwórz obraz.

```azurepowershell-interactive
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Tworzenie maszyn wirtualnych z obrazu

Teraz, gdy masz już obraz, możesz za jego pomocą utworzyć jedną lub więcej nowych maszyn wirtualnych. Tworzenie maszyny wirtualnej na podstawie obrazu niestandardowego jest podobne do tworzenia maszyny wirtualnej przy użyciu obrazu z witryny Marketplace. W przypadku obrazu z witryny Marketplace należy podać informacje dotyczące obrazu, dostawcy obrazu, oferty, jednostki SKU i wersji. W przypadku uproszczonego zestawu parametrów dla polecenia cmdlet [New-AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm) wystarczy podać nazwę obrazu niestandardowego, o ile znajduje się on w tej samej grupie zasobów. 

W tym przykładzie jest tworzona maszyna wirtualna o nazwie *myVMfromImage* za pomocą obrazu o nazwie *myImage* w grupie zasobów *myResourceGroup*.


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

Oto kilka przykładów typowych zadań zarządzania obrazami i sposoby ich wykonania za pomocą programu PowerShell.

Wyświetl listę wszystkich obrazów według nazwy.

```azurepowershell-interactive
$images = Get-AzureRMResource -ResourceType Microsoft.Compute/images 
$images.name
```

Usuwanie obrazu. W tym przykładzie obraz o nazwie *myImage* jest usuwany z grupy *myResourceGroup*.

```azurepowershell-interactive
Remove-AzureRmImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony obraz niestandardowy maszyny wirtualnej. W tym samouczku omówiono:

> [!div class="checklist"]
> * Używanie narzędzia Sysprep i uogólnianie maszyn wirtualnych
> * Tworzenie obrazu niestandardowego
> * Tworzenie maszyny wirtualnej na podstawie obrazu niestandardowego
> * Wyświetlanie listy wszystkich obrazów w subskrypcji
> * Usuwanie obrazu

Przejdź do następnego samouczka, aby dowiedzieć się więcej o maszynach wirtualnych o wysokiej dostępności.

> [!div class="nextstepaction"]
> [Tworzenie maszyn wirtualnych o wysokiej dostępności](tutorial-availability-sets.md)



