---
title: Tworzenie maszyny Wirtualnej na podstawie obrazu zarządzanego na platformie Azure | Dokumentacja firmy Microsoft
description: Utwórz maszynę wirtualną Windows z uogólnionego obrazu zarządzanego przy użyciu programu Azure PowerShell lub portalu Azure w modelu wdrażania usługi Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: cynthn
ms.openlocfilehash: 6baf784068b1fba0c35d2848b8d2dda4f1064a2d
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867984"
---
# <a name="create-a-vm-from-a-managed-image"></a>Tworzenie maszyny Wirtualnej na podstawie obrazu zarządzanego

Możesz utworzyć wiele maszyn wirtualnych z zarządzanego obrazu maszyny Wirtualnej przy użyciu programu PowerShell lub witryny Azure portal. Zarządzany obraz maszyny Wirtualnej zawiera informacje niezbędne do utworzenia maszyny Wirtualnej, w tym dyski systemu operacyjnego i danych. Dyski VHD, które tworzą obrazu, w tym dyski systemu operacyjnego i dysków z danymi, są przechowywane jako dysków zarządzanych. 

Musisz mieć już [tworzenie zarządzanego obrazu maszyny Wirtualnej](capture-image-resource.md) służące do tworzenia nowej maszyny Wirtualnej. 

## <a name="use-the-portal"></a>Używanie portalu

1. Otwórz [portal Azure](https://portal.azure.com).
2. W menu po lewej stronie wybierz pozycję **Wszystkie zasoby**. Można sortować zasobów przez **typu** aby łatwo znaleźć obrazów.
3. Wybierz obraz, który chcesz używać, z listy. Obraz **Przegląd** zostanie otwarta strona.
4. Kliknij przycisk **+ Utwórz maszynę Wirtualną** z menu.
5. Wprowadź informacje o maszynie wirtualnej. Nazwa użytkownika i hasło wprowadzone w tym miejscu są używane na potrzeby logowania się do maszyny wirtualnej. Po zakończeniu kliknij przycisk **OK**. Utwórz nową maszynę Wirtualną w istniejącej grupy zasobów lub wybierz **Utwórz nową** Aby utworzyć nową grupę zasobów, aby zapisać maszynę Wirtualną.
6. Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz pozycje **Wyświetl wszystkie** lub zmień filtr **Obsługiwany typ dysku**. 
7. W obszarze **ustawienia**, wprowadź wymagane zmiany i kliknij przycisk **OK**. 
8. Na stronie podsumowania, powinien zostać wyświetlony swoją nazwę obrazu dla **obrazu prywatnego**. Kliknij przycisk **Ok** rozpocząć wdrażanie maszyny wirtualnej.


## <a name="use-powershell"></a>Korzystanie z programu PowerShell

Można użyć programu PowerShell do utworzenia maszyny Wirtualnej z obrazu przy użyciu uproszczony zestaw parametrów dla [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm) polecenia cmdlet. Obraz musi znajdować się w tej samej grupie zasobów, której chcesz utworzyć maszynę Wirtualną.

W tym przykładzie wymaga AzureRM modułu wersji 5.6.0 lub nowszej. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).

Uproszczony zestaw dla polecenia New-AzureRmVm wymaga jedynie, że Podaj imię i nazwisko, nazwę grupy i obraz zasobu utworzyć maszynę Wirtualną z obrazu, ale użyje wartości parametrów **— nazwa** parametru jako nazwy wszystkich zasobów jej tworzy automatycznie. W tym przykładzie firma Microsoft zapewnia bardziej szczegółowe nazwy dla każdego zasobu, ale pozwól polecenia cmdlet, utwórz je automatycznie. Można również utworzyć zasoby, takie jak sieci wirtualnej, a wcześniej i przekazywać nazwę jednostki w poleceniu cmdlet. Jeśli można je znaleźć według nazwy będzie używać istniejących zasobów.

Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVMFromImage*w *myResourceGroup* grupę zasobów za pomocą obrazu o nazwie *myImage*. 


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



## <a name="next-steps"></a>Kolejne kroki
Aby zarządzać swoją nową maszyną wirtualną przy użyciu programu Azure PowerShell, zobacz [Utwórz Windows maszyn wirtualnych i zarządzanie przy użyciu modułu Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

