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
ms.openlocfilehash: cb9f03ab87079ba33135840e3e7599d2e8cc95e9
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/11/2018
ms.locfileid: "42054146"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Typowe polecenia programu PowerShell do tworzenia i zarządzania maszynami wirtualnymi platformy Azure

W tym artykule omówiono niektóre z poleceń programu Azure PowerShell, które służą do tworzenia i zarządzania maszynami wirtualnymi w subskrypcji platformy Azure.  Aby uzyskać bardziej szczegółową pomoc dotyczącą przełączniki wiersza polecenia i opcji, możesz użyć **Get-Help** *polecenia*.

Zobacz [How to install and configure Azure PowerShell](/powershell/azure/overview) (Jak zainstalować i skonfigurować program Azure PowerShell), aby uzyskać informacje na temat instalowania najnowszej wersji programu Azure PowerShell, wybierania subskrypcji i logowania się do konta.

Te zmienne mogą być przydatne dla Ciebie, jeśli z więcej niż jedno z poleceń w tym artykule:

- $location — lokalizacja maszyny wirtualnej. Możesz użyć [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation) można znaleźć [regionu geograficznego](https://azure.microsoft.com/regions/) który Ci odpowiada.
- $myResourceGroup — Nazwa grupy zasobów, która zawiera maszynę wirtualną.
- $myVM — Nazwa maszyny wirtualnej.

## <a name="create-a-vm---simplified"></a>Tworzenie maszyny Wirtualnej — uproszczony

| Zadanie | Polecenie |
| ---- | ------- |
| Utwórz prostą maszynę Wirtualną | [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) — nazwa $myVM <BR></BR><BR></BR> New-AzureRMVM zawiera zbiór *uproszczone* parametrów jest wymagany w przypadku jednej nazwy. Wartość-nazwa będzie używana jako nazwa dla wszystkich zasobów wymaganych do tworzenia nowej maszyny Wirtualnej. Można określić więcej, ale to wszystko, co jest wymagane.|
| Tworzenie maszyny wirtualnej na podstawie obrazu niestandardowego | New-AzureRmVm - ResourceGroupName $myResourceGroup — nazwa ImageName $myVM "myImage"-lokalizacji $location  <BR></BR><BR></BR>Musisz już utworzono własne [obrazu zarządzanego](capture-image-resource.md). Można użyć obrazu, aby wiele, identycznych maszyn wirtualnych. |



## <a name="create-a-vm-configuration"></a>Utwórz konfigurację maszyny Wirtualnej

| Zadanie | Polecenie |
| ---- | ------- |
| Utwórz konfigurację maszyny Wirtualnej |$vm = [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig) - VMName $myVM - VMSize "Standard_D1_v1"<BR></BR><BR></BR>Konfiguracja maszyny Wirtualnej służy do definiowania lub zaktualizować ustawień maszyny wirtualnej. Konfiguracja jest inicjowany za pomocą nazwy maszyny Wirtualnej i jej [rozmiar](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Dodawanie ustawień konfiguracji |$vm = [AzureRmVMOperatingSystem zestaw](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) - VM $vm-Windows - ComputerName $myVM-Credential $cred - ProvisionVMAgent - EnableAutoUpdate<BR></BR><BR></BR>Ustawienia systemu operacyjnego, w tym [poświadczenia](https://technet.microsoft.com/library/hh849815.aspx) są dodawane do obiektu konfiguracji, która została wcześniej utworzona za pomocą poleceń New-AzureRmVMConfig. |
| Dodawanie interfejsu sieciowego |$vm = [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/Add-AzureRmVMNetworkInterface) - VM $vm — identyfikator $nic. Identyfikator<BR></BR><BR></BR>Maszyna wirtualna musi mieć [interfejs sieciowy](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) komunikowanie się w sieci wirtualnej. Można również użyć [Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) można pobrać obiektu interfejsu sieciowego. |
| Określ obraz platformy |$vm = [AzureRmVMSourceImage zestaw](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage) - VM $vm - Nazwa_wydawcy "publisher_name"-oferują "publisher_offer" - Skus "product_sku"-"najnowszej" wersji<BR></BR><BR></BR>[Obraz informacji](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) jest dodawany do obiektu konfiguracji, która została wcześniej utworzona za pomocą poleceń New-AzureRmVMConfig. Obiekt zwrócony z tego polecenia jest używane tylko w sytuacji, gdy ustawisz dysku systemu operacyjnego, aby użyć obrazu platformy. |
| Tworzenie maszyny wirtualnej |[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) - ResourceGroupName $myResourceGroup — lokalizacja $location - VM $vm<BR></BR><BR></BR>Wszystkie zasoby są tworzone w [grupy zasobów](../../azure-resource-manager/powershell-azure-resource-manager.md). Przed uruchomieniem tego polecenia, należy uruchomić New-AzureRmVMConfig, AzureRmVMOperatingSystem zestaw, zestaw AzureRmVMSourceImage, Add-AzureRmVMNetworkInterface i Set-AzureRmVMOSDisk. |
| Aktualizowanie maszyny Wirtualnej |[Update-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Pobierz bieżącą konfigurację maszyn wirtualnych za pomocą polecenia Get-AzureRmVM, zmieniać ustawienia konfiguracji dla obiektu maszyny Wirtualnej, a następnie uruchom to polecenie. |

## <a name="get-information-about-vms"></a>Uzyskaj informacje o maszynach wirtualnych

| Zadanie | Polecenie |
| ---- | ------- |
| Wyświetlanie listy maszyn wirtualnych w ramach subskrypcji |[Get-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvm) |
| Wyświetlanie listy maszyn wirtualnych w grupie zasobów |Get-AzureRmVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Aby uzyskać listę grup zasobów w ramach subskrypcji, użyj [Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresourcegroup). |
| Uzyskiwanie informacji o maszynie wirtualnej |Get-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>Zarządzanie maszynami wirtualnymi
| Zadanie | Polecenie |
| --- | --- |
| Uruchamianie maszyny wirtualnej |[Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Zatrzymywanie maszyny wirtualnej |[Stop-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Uruchom ponownie uruchomionej maszyny Wirtualnej |[Restart-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/restart-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Usuwanie maszyny wirtualnej |[Remove-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |


## <a name="next-steps"></a>Kolejne kroki
* Zobacz podstawowe kroki tworzenia maszyny wirtualnej w [Utwórz maszynę Wirtualną Windows przy użyciu usługi Resource Manager i programu PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

