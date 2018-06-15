---
title: Tworzenie maszyny Wirtualnej z obrazu zarządzanych na platformie Azure | Dokumentacja firmy Microsoft
description: Utwórz maszynę wirtualną systemu Windows z uogólniony obraz zarządzanych za pomocą programu Azure PowerShell lub portalu Azure w modelu wdrażania usługi Resource Manager.
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
ms.openlocfilehash: 1d543bd9590664e74cff70cf55e8f7bd42f2c6f0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
ms.locfileid: "30239059"
---
# <a name="create-a-vm-from-a-managed-image"></a>Utwórz maszynę Wirtualną z zarządzanego obrazu

Możesz utworzyć wiele maszyn wirtualnych z do zarządzanego obrazu maszyny Wirtualnej przy użyciu programu PowerShell lub w portalu Azure. Do zarządzanego obrazu maszyny Wirtualnej zawiera informacje niezbędne do tworzenia maszyn wirtualnych, dysków systemu operacyjnego i danych. Wirtualne dyski twarde, które tworzą obrazu, w tym zarówno dysków systemu operacyjnego i dysków z danymi, są przechowywane jako dyski zarządzanych. 

Musisz mieć już [utworzony obraz maszyny Wirtualnej zarządzanej](capture-image-resource.md) do użycia podczas tworzenia nowej maszyny Wirtualnej. 

## <a name="use-the-portal"></a>Używanie portalu

1. Otwórz [portal Azure](https://portal.azure.com).
2. W menu po lewej stronie wybierz pozycję **Wszystkie zasoby**. Można sortować zasobów przez **typu** można łatwo odnaleźć obrazów.
3. Wybierz obraz, który ma być używany z listy. Obraz **omówienie** zostanie otwarta strona.
4. Kliknij przycisk **+ Utwórz wirtualna** z menu.
5. Wprowadź informacje o maszynie wirtualnej. Nazwa użytkownika i hasło wprowadzone w tym miejscu są używane na potrzeby logowania się do maszyny wirtualnej. Po zakończeniu kliknij przycisk **OK**. Utwórz nową maszynę Wirtualną w istniejącej grupie Resrouce lub wybierz **Utwórz nowy** Aby utworzyć nową grupę zasobów do przechowywania maszyny Wirtualnej.
6. Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz pozycje **Wyświetl wszystkie** lub zmień filtr **Obsługiwany typ dysku**. 
7. W obszarze **ustawienia**, wprowadź wymagane zmiany i kliknij przycisk **OK**. 
8. Na stronie Podsumowanie, powinny pojawić się nazwę obrazu dla **prywatnej obrazu**. Kliknij przycisk **Ok** rozpocząć wdrażanie maszyny wirtualnej.


## <a name="use-powershell"></a>Korzystanie z programu PowerShell

Można utworzyć Maszynę wirtualną z obrazu za pomocą parametru uproszczony, ustaw dla programu PowerShell [AzureRmVm nowy](/powershell/module/azurerm.compute/new-azurermvm) polecenia cmdlet. Obraz musi być w tej samej grupie zasobów, której chcesz utworzyć maszynę Wirtualną.

W tym przykładzie wymaga AzureRM wersji modułu 5.6.0 lub nowszym. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).

Uproszczony zestaw New AzureRmVm wymaga tylko Podaj nazwę, grupy i obrazu Nazwa zasobu do utworzenia maszyny Wirtualnej z obrazu, ale będzie używać wartości parametrów **— nazwa** parametr jako nazwę wszystkie zasoby it tworzy automatycznie. W tym przykładzie mamy bardziej szczegółowe nazwy dla każdego zasobu, ale pozwól cmdlet je utworzyć automatycznie. Można również utworzyć zasobów, takich jak sieci wirtualnej, wcześniejsze i przekazywania nazwy do polecenia cmdlet. Będzie on używać istniejących zasobów, jeśli można je znaleźć według nazwy.

Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVMFromImage*w *myResourceGroup* grupy zasobów z obrazu o nazwie *myImage*. 


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
Aby zarządzać nową maszynę wirtualną w taki sposób, z programu Azure PowerShell, zobacz [tworzenia i zarządzania maszynami wirtualnymi systemu Windows za pomocą modułu Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

