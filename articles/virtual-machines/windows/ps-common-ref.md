---
title: Typowe poleceń programu PowerShell dla usługi Azure Virtual Machines | Dokumentacja firmy Microsoft
description: Typowe polecenia programu PowerShell ułatwiających rozpoczęcie pracy tworzenia i zarządzania nimi maszyn wirtualnych systemu Windows na platformie Azure.
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
ms.openlocfilehash: ff861c21250a042191651ab4a4cffbf3928e4f26
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738568"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Typowe polecenia programu PowerShell do tworzenia i zarządzania maszynami wirtualnymi Azure

W tym artykule omówiono niektóre polecenia programu Azure PowerShell, które służy do tworzenia i zarządzania maszynami wirtualnymi w Twojej subskrypcji platformy Azure.  Szczegółowe, aby uzyskać pomoc dotyczącą przełączniki wiersza polecenia i opcje, można użyć **Get-Help** *polecenia*.

Zobacz [How to install and configure Azure PowerShell](/powershell/azure/overview) (Jak zainstalować i skonfigurować program Azure PowerShell), aby uzyskać informacje na temat instalowania najnowszej wersji programu Azure PowerShell, wybierania subskrypcji i logowania się do konta.

Tych zmiennych mogą być przydatne dla Ciebie, jeśli działa więcej niż jedno z poleceń w tym artykule:

- $location — lokalizacja maszyny wirtualnej. Można użyć [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation) można znaleźć [regionu geograficznego](https://azure.microsoft.com/regions/) działający za Ciebie.
- $myResourceGroup — Nazwa grupy zasobów, która zawiera maszynę wirtualną.
- $myVM - Nazwa maszyny wirtualnej.

## <a name="create-a-vm---simplified"></a>Utwórz maszynę Wirtualną — uproszczony

| Zadanie | Polecenie |
| ---- | ------- |
| Tworzenie prostego maszyny Wirtualnej | [Nowy AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) -Name $myVM <BR></BR><BR></BR> Nowe AzureRMVM ma zestaw *uproszczony* parametrów jest wymagany w przypadku jednej nazwy. Wartość-nazwa będzie używana jako nazwa dla wszystkie zasoby wymagane do utworzenia nowej maszyny Wirtualnej. Można określić więcej, ale jest to wszystko, co jest wymagane.|
| Tworzenie maszyny wirtualnej na podstawie obrazu niestandardowego | Nowe AzureRmVm - ResourceGroupName $myResourceGroup-Name Nazwa_obrazu $myVM "myImage"-lokalizacji $location  <BR></BR><BR></BR>Musisz już utworzono własne [zarządzanego obrazu](capture-image-resource.md). Możliwe użycie obrazu, aby wiele identycznych maszyn wirtualnych. |



## <a name="create-a-vm-configuration"></a>Utwórz konfigurację maszyny Wirtualnej

| Zadanie | Polecenie |
| ---- | ------- |
| Utwórz konfigurację maszyny Wirtualnej |$vm = [AzureRmVMConfig nowy](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig) - VMName $myVM - VMSize "Standard_D1_v1"<BR></BR><BR></BR>Konfiguracja maszyny Wirtualnej służy do definiowania lub zaktualizuj ustawienia dla maszyny Wirtualnej. Konfiguracja jest inicjowana z nazwą maszyny Wirtualnej i jej [rozmiar](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Dodaj ustawienia konfiguracji |$vm = [AzureRmVMOperatingSystem zestaw](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) - VM $vm-Windows - ComputerName $myVM-poświadczeń $cred - ProvisionVMAgent - EnableAutoUpdate<BR></BR><BR></BR>Ustawienia systemu operacyjnego, w tym [poświadczenia](https://technet.microsoft.com/library/hh849815.aspx) zostaną dodane do utworzonego wcześniej przy użyciu AzureRmVMConfig nowy obiekt konfiguracji. |
| Dodawanie interfejsu sieciowego |$vm = [AzureRmVMNetworkInterface Dodaj](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Add-AzureRmVMNetworkInterface) - VM $vm-$identyfikator karty sieciowej. Identyfikator<BR></BR><BR></BR>Maszyna wirtualna musi mieć [interfejsu sieciowego](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) komunikowanie się w sieci wirtualnej. Można również użyć [Get AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) można pobrać obiektu interfejsu sieciowego. |
| Określ obraz platformy |$vm = [AzureRmVMSourceImage zestaw](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage) - VM $vm - PublisherName "publisher_name"-oferty "publisher_offer" - jednostki SKU "product_sku"-"najnowszej" wersji<BR></BR><BR></BR>[Informacji zawartych w obrazie](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) jest dodawana do utworzonego wcześniej przy użyciu AzureRmVMConfig nowy obiekt konfiguracji. Obiekt zwrócony z tego polecenia jest używana tylko w przypadku ustawienia dysku systemu operacyjnego, aby używać obrazu platformy. |
| Tworzenie maszyny wirtualnej |[Nowy AzureRmVM]() - ResourceGroupName $myResourceGroup-lokalizacji $location - VM $vm<BR></BR><BR></BR>Wszystkie zasoby są tworzone w [grupy zasobów](../../azure-resource-manager/powershell-azure-resource-manager.md). Przed uruchomieniem tego polecenia, uruchom nowy AzureRmVMConfig, AzureRmVMOperatingSystem zestaw AzureRmVMSourceImage zestawu, Dodaj AzureRmVMNetworkInterface i AzureRmVMOSDisk zestawu. |
| Aktualizacja maszyny Wirtualnej |[Update-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Pobierz bieżącą konfigurację maszyny Wirtualnej przy użyciu Get AzureRmVM, Zmień ustawienia konfiguracji w obiektu maszyny Wirtualnej, a następnie uruchom to polecenie. |

## <a name="get-information-about-vms"></a>Pobierz informacje o maszynach wirtualnych

| Zadanie | Polecenie |
| ---- | ------- |
| Lista maszyn wirtualnych, w ramach subskrypcji |[Get-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvm) |
| Lista maszyn wirtualnych, w grupie zasobów |Get-AzureRmVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Aby uzyskać listę grup zasobów w ramach subskrypcji, użyj [Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresourcegroup). |
| Uzyskiwanie informacji o maszynie wirtualnej |Get-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>Zarządzanie maszynami wirtualnymi
| Zadanie | Polecenie |
| --- | --- |
| Uruchamianie maszyny wirtualnej |[Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Zatrzymywanie maszyny wirtualnej |[Stop-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Uruchom ponownie uruchomionej maszyny Wirtualnej |[Restart-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/restart-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Usuwanie maszyny wirtualnej |[Remove-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |


## <a name="next-steps"></a>Kolejne kroki
* Podstawowe czynności wykonywane w celu utworzenia maszyny wirtualnej w [Utwórz maszynę Wirtualną z systemem Windows przy użyciu usługi Resource Manager i programu PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

