---
title: Tworzenie maszyny Wirtualnej na podstawie obrazu zarządzanego na platformie Azure | Dokumentacja firmy Microsoft
description: Utwórz maszynę wirtualną Windows z uogólnionego obrazu zarządzanego przy użyciu programu Azure PowerShell lub portalu Azure w modelu wdrażania usługi Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 3ae730389b54fc1034bfac3ffdc7e56a2dc5f3fc
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718973"
---
# <a name="create-a-vm-from-a-managed-image"></a>Tworzenie maszyny Wirtualnej na podstawie obrazu zarządzanego

Można utworzyć wiele maszyn wirtualnych (VM) z maszyny Wirtualnej platformy Azure zarządzanego obrazu przy użyciu witryny Azure portal lub programu PowerShell. Zarządzany obraz maszyny Wirtualnej zawiera informacje niezbędne do utworzenia maszyny Wirtualnej, w tym dyski systemu operacyjnego i danych. Wirtualne dyski twarde (VHD), które tworzą obrazu, w tym dyski systemu operacyjnego i wszelkich dysków z danymi, są przechowywane jako dysków zarządzanych. 

Przed utworzeniem nowej maszyny Wirtualnej, konieczne będzie [tworzenie zarządzanego obrazu maszyny Wirtualnej](capture-image-resource.md) do użycia jako obraz źródłowy i przyznać dostęp do odczytu do obrazu dla każdego użytkownika, które powinny mieć dostępu do obrazu. 


## <a name="use-the-portal"></a>Używanie portalu

1. Otwórz [portal Azure](https://portal.azure.com).
2. W menu po lewej stronie wybierz **wszystkie zasoby**. Można sortować zasobów przez **typu** aby łatwo znaleźć obrazów.
3. Wybierz obraz, który chcesz używać, z listy. Obraz **Przegląd** zostanie otwarta strona.
4. Wybierz **Utwórz maszynę Wirtualną** z menu.
5. Wprowadź informacje o maszynie wirtualnej. Nazwa użytkownika i hasło wprowadzone w tym miejscu będzie służyć do logowania do maszyny wirtualnej. Po zakończeniu wybierz **OK**. Utwórz nową maszynę Wirtualną w istniejącej grupy zasobów lub wybierz **Utwórz nową** Aby utworzyć nową grupę zasobów, aby zapisać maszynę Wirtualną.
6. Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz pozycje **Wyświetl wszystkie** lub zmień filtr **Obsługiwany typ dysku**. 
7. W obszarze **ustawienia**, wprowadź niezbędne zmiany i wybierz **OK**. 
8. Na stronie podsumowania, powinien zostać wyświetlony swoją nazwę obrazu, na liście jako **obrazu prywatnego**. Wybierz **Ok** rozpocząć wdrażanie maszyny wirtualnej.


## <a name="use-powershell"></a>Korzystanie z programu PowerShell

Można użyć programu PowerShell, aby utworzyć Maszynę wirtualną z obrazu za pomocą uproszczony zestaw parametrów dla [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) polecenia cmdlet. Obraz musi znajdować się w tej samej grupie zasobów, w których zostaną utworzone maszyny Wirtualnej.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

Uproszczony zestaw parametrów dla [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) tylko wymaga podania nazwy grupy zasobów i nazwy obrazu utworzyć maszynę Wirtualną z obrazu. Nowe AzVm użyje wartości **— nazwa** jako nazwy wszystkich zasobów, które automatycznie tworzy parametr. W tym przykładzie firma Microsoft zapewnia bardziej szczegółowe nazwy poszczególnych zasobów, ale pozwól polecenia cmdlet, utwórz je automatycznie. Możesz również tworzyć zasoby wcześniej, takich jak sieci wirtualnej i przekazać nazwę zasobu do polecenia cmdlet. Nowe AzVm będzie korzystać z istniejących zasobów, jeśli można je znaleźć według nazwy.

Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVMFromImage*w *myResourceGroup* grupę zasobów za pomocą obrazu o nazwie *myImage*. 


```azurepowershell-interactive
New-AzVm `
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



## <a name="next-steps"></a>Następne kroki
[Tworzenie i zarządzanie maszynami wirtualnymi Windows za pomocą modułu Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

