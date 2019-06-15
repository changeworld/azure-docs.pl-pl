---
title: Typowe polecenia programu PowerShell usługi Azure Virtual Machines | Dokumentacja firmy Microsoft
description: Typowe polecenia programu PowerShell ułatwiające rozpoczęcie tworzenia i zarządzania nimi na platformie Azure maszyn wirtualnych Windows.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 64f7e614b72d7d966eaec7acb84a68e8df3698a2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64691054"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Typowe polecenia programu PowerShell do tworzenia i zarządzania maszynami wirtualnymi platformy Azure

W tym artykule omówiono niektóre z poleceń programu Azure PowerShell, które służą do tworzenia i zarządzania maszynami wirtualnymi w subskrypcji platformy Azure.  Aby uzyskać bardziej szczegółową pomoc dotyczącą przełączniki wiersza polecenia i opcji, możesz użyć **Get-Help** *polecenia*.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

Te zmienne mogą być przydatne dla Ciebie, jeśli z więcej niż jedno z poleceń w tym artykule:

- $location — lokalizacja maszyny wirtualnej. Możesz użyć [Get AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) można znaleźć [regionu geograficznego](https://azure.microsoft.com/regions/) który Ci odpowiada.
- $myResourceGroup — Nazwa grupy zasobów, która zawiera maszynę wirtualną.
- $myVM — Nazwa maszyny wirtualnej.

## <a name="create-a-vm---simplified"></a>Tworzenie maszyny Wirtualnej — uproszczony

| Zadanie | Polecenie |
| ---- | ------- |
| Utwórz prostą maszynę Wirtualną | [Nowe AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) — nazwa $myVM <BR></BR><BR></BR> Nowe AzVM zawiera zbiór *uproszczone* parametrów jest wymagany w przypadku jednej nazwy. Wartość-nazwa będzie używana jako nazwa dla wszystkich zasobów wymaganych do tworzenia nowej maszyny Wirtualnej. Można określić więcej, ale to wszystko, co jest wymagane.|
| Tworzenie maszyny wirtualnej na podstawie obrazu niestandardowego | New-AzVm -ResourceGroupName $myResourceGroup -Name $myVM ImageName "myImage" -Location $location  <BR></BR><BR></BR>Musisz już utworzono własne [obrazu zarządzanego](capture-image-resource.md). Można użyć obrazu, aby wiele, identycznych maszyn wirtualnych. |



## <a name="create-a-vm-configuration"></a>Utwórz konfigurację maszyny Wirtualnej

| Zadanie | Polecenie |
| ---- | ------- |
| Utwórz konfigurację maszyny Wirtualnej |$vm = [New AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) - VMName $myVM - VMSize "Standard_D1_v1"<BR></BR><BR></BR>Konfiguracja maszyny Wirtualnej służy do definiowania lub zaktualizować ustawień maszyny wirtualnej. Konfiguracja jest inicjowany za pomocą nazwy maszyny Wirtualnej i jej [rozmiar](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Dodawanie ustawień konfiguracji |$vm = [AzVMOperatingSystem zestaw](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) - VM $vm-Windows - ComputerName $myVM-Credential $cred - ProvisionVMAgent - EnableAutoUpdate<BR></BR><BR></BR>Ustawienia systemu operacyjnego, w tym [poświadczenia](https://technet.microsoft.com/library/hh849815.aspx) są dodawane do utworzonego wcześniej przy użyciu nowego AzVMConfig obiektu konfiguracji. |
| Dodawanie interfejsu sieciowego |$vm = [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMNetworkInterface) -VM $vm -Id $nic.Id<BR></BR><BR></BR>Maszyna wirtualna musi mieć [interfejs sieciowy](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) komunikowanie się w sieci wirtualnej. Można również użyć [Get AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) można pobrać obiektu interfejsu sieciowego. |
| Określ obraz platformy |$vm = [AzVMSourceImage zestaw](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) - VM $vm - Nazwa_wydawcy "publisher_name"-oferują "publisher_offer" - Skus "product_sku"-"najnowszej" wersji<BR></BR><BR></BR>[Obraz informacji](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) jest dodawany do utworzonego wcześniej przy użyciu nowego AzVMConfig obiektu konfiguracji. Obiekt zwrócony z tego polecenia jest używane tylko w sytuacji, gdy ustawisz dysku systemu operacyjnego, aby użyć obrazu platformy. |
| Tworzenie maszyny wirtualnej |[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -ResourceGroupName $myResourceGroup -Location $location -VM $vm<BR></BR><BR></BR>Wszystkie zasoby są tworzone w [grupy zasobów](../../azure-resource-manager/manage-resource-groups-powershell.md). Przed uruchomieniem tego polecenia, uruchomić AzVMConfig nowy, AzVMOperatingSystem zestawu, AzVMSourceImage zestawu, Dodaj AzVMNetworkInterface i AzVMOSDisk zestawu. |
| Aktualizowanie maszyny Wirtualnej |[Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Pobierz bieżącą konfigurację maszyn wirtualnych przy użyciu Get-AzVM, zmieniać ustawienia konfiguracji dla obiektu maszyny Wirtualnej, a następnie uruchom to polecenie. |

## <a name="get-information-about-vms"></a>Uzyskaj informacje o maszynach wirtualnych

| Zadanie | Polecenie |
| ---- | ------- |
| Wyświetlanie listy maszyn wirtualnych w ramach subskrypcji |[Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) |
| Wyświetlanie listy maszyn wirtualnych w grupie zasobów |Get-AzVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Aby uzyskać listę grup zasobów w ramach subskrypcji, użyj [Get AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup). |
| Uzyskiwanie informacji o maszynie wirtualnej |Get-AzVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>Zarządzanie maszynami wirtualnymi
| Zadanie | Polecenie |
| --- | --- |
| Uruchamianie maszyny wirtualnej |[Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Zatrzymywanie maszyny wirtualnej |[Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Uruchom ponownie uruchomionej maszyny Wirtualnej |[Restart-AzVM](https://docs.microsoft.com/powershell/module/az.compute/restart-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Usuwanie maszyny wirtualnej |[Remove-AzVM](https://docs.microsoft.com/powershell/module/az.compute/remove-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |


## <a name="next-steps"></a>Kolejne kroki
* Zobacz podstawowe kroki tworzenia maszyny wirtualnej w [Utwórz maszynę Wirtualną Windows przy użyciu usługi Resource Manager i programu PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

