---
title: Typowe poleceń programu PowerShell dla usługi Azure Virtual Machines | Dokumentacja firmy Microsoft
description: Typowe polecenia programu PowerShell ułatwiających rozpoczęcie pracy tworzenia i zarządzania nimi maszyn wirtualnych systemu Windows na platformie Azure.
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/17/2017
ms.author: davidmu
ms.openlocfilehash: f84473e73a32da43cc6cc80b21deb49ab4f3ceb9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Typowe polecenia programu PowerShell do tworzenia i zarządzania maszynami wirtualnymi Azure

W tym artykule omówiono niektóre polecenia programu Azure PowerShell, które służy do tworzenia i zarządzania maszynami wirtualnymi w Twojej subskrypcji platformy Azure.  Szczegółowe, aby uzyskać pomoc dotyczącą przełączniki wiersza polecenia i opcje, można użyć **Get-Help** *polecenia*.

Zobacz [How to install and configure Azure PowerShell](/powershell/azure/overview) (Jak zainstalować i skonfigurować program Azure PowerShell), aby uzyskać informacje na temat instalowania najnowszej wersji programu Azure PowerShell, wybierania subskrypcji i logowania się do konta.

Tych zmiennych mogą być przydatne dla Ciebie, jeśli działa więcej niż jedno z poleceń w tym artykule:

- $location — lokalizacja maszyny wirtualnej. Można użyć [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation) można znaleźć [regionu geograficznego](https://azure.microsoft.com/regions/) działający za Ciebie.
- $myResourceGroup — Nazwa grupy zasobów, która zawiera maszynę wirtualną.
- $myVM - Nazwa maszyny wirtualnej.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

| Zadanie | Polecenie |
| ---- | ------- |
| Utwórz konfigurację maszyny Wirtualnej |$vm = [AzureRmVMConfig nowy](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig) - VMName $myVM - VMSize "Standard_D1_v1"<BR></BR><BR></BR>Konfiguracja maszyny Wirtualnej służy do definiowania lub zaktualizuj ustawienia dla maszyny Wirtualnej. Konfiguracja jest inicjowana z nazwą maszyny Wirtualnej i jej [rozmiar](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Dodaj ustawienia konfiguracji |$vm = [AzureRmVMOperatingSystem zestaw](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) - VM $vm-Windows - ComputerName $myVM-poświadczeń $cred - ProvisionVMAgent - EnableAutoUpdate<BR></BR><BR></BR>Ustawienia systemu operacyjnego, w tym [poświadczenia](https://technet.microsoft.com/library/hh849815.aspx) zostaną dodane do utworzonego wcześniej przy użyciu AzureRmVMConfig nowy obiekt konfiguracji. |
| Dodawanie interfejsu sieciowego |$vm = [AzureRmVMNetworkInterface Dodaj](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Add-AzureRmVMNetworkInterface) - VM $vm-$identyfikator karty sieciowej. Identyfikator<BR></BR><BR></BR>Maszyna wirtualna musi mieć [interfejsu sieciowego](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) komunikowanie się w sieci wirtualnej. Można również użyć [Get AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) można pobrać obiektu interfejsu sieciowego. |
| Określ obraz platformy |$vm = [AzureRmVMSourceImage zestaw](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage) - VM $vm - PublisherName "publisher_name"-oferty "publisher_offer" - jednostki SKU "product_sku"-"najnowszej" wersji<BR></BR><BR></BR>[Informacji zawartych w obrazie](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) jest dodawana do utworzonego wcześniej przy użyciu AzureRmVMConfig nowy obiekt konfiguracji. Obiekt zwrócony z tego polecenia jest używana tylko w przypadku ustawienia dysku systemu operacyjnego, aby używać obrazu platformy. |
| Ustaw dysk systemu operacyjnego używać obrazu platformy |$vm = [AzureRmVMOSDisk zestaw](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk) - VM $vm-Name "myOSDisk" - VhdUri "http://mystore1.blob.core.windows.net/vhds/myOSDisk.vhd" - CreateOption FromImage<BR></BR><BR></BR>Nazwa dysku systemu operacyjnego i jego lokalizacji w [magazynu](../../storage/common/storage-powershell-guide-full.md) zostanie dodany do obiekt konfiguracji, która została wcześniej utworzona. |
| Ustaw dysku systemu operacyjnego, aby używać uogólniony obraz |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "myOSDisk" -SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk.{guid}.vhd" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk_name.vhd" -CreateOption FromImage -Windows<BR></BR><BR></BR>Nazwa dysku systemu operacyjnego, Lokalizacja obrazu źródłowego i lokalizacji na dysku w [magazynu](../../storage/common/storage-powershell-guide-full.md) zostanie dodany do obiekt konfiguracji. |
| Ustaw dysk systemu operacyjnego używać specjalnych obrazu |$vm = set AzureRmVMOSDisk - VM $vm-Name "myOSDisk" - VhdUri "http://mystore1.blob.core.windows.net/vhds/" - CreateOption Attach - systemu Windows |
| Tworzenie maszyny wirtualnej |[Nowy AzureRmVM]() - ResourceGroupName $myResourceGroup-lokalizacji $location - VM $vm<BR></BR><BR></BR>Wszystkie zasoby są tworzone w [grupy zasobów](../../azure-resource-manager/powershell-azure-resource-manager.md). Przed uruchomieniem tego polecenia, uruchom nowy AzureRmVMConfig, AzureRmVMOperatingSystem zestaw AzureRmVMSourceImage zestawu, Dodaj AzureRmVMNetworkInterface i AzureRmVMOSDisk zestawu. |

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
| Generalize maszyny Wirtualnej |[Set-AzureRmVm](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM -Generalized<BR></BR><BR></BR>Uruchom to polecenie przed uruchomieniem AzureRmVMImage Zapisz. |
| Przechwytywanie maszyny wirtualnej |[Save-AzureRmVMImage](https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvmimage) -ResourceGroupName $myResourceGroup -VMName $myVM -DestinationContainerName "myImageContainer" -VHDNamePrefix "myImagePrefix" -Path "C:\filepath\filename.json"<BR></BR><BR></BR>Maszyna wirtualna musi być [przygotowany, zamknij i uogólniony](prepare-for-upload-vhd-image.md) ma być używany do utworzenia obrazu. Przed uruchomieniem tego polecenia, uruchom zestaw AzureRmVm. |
| Aktualizacja maszyny Wirtualnej |[Update-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Pobierz bieżącą konfigurację maszyny Wirtualnej przy użyciu Get AzureRmVM, Zmień ustawienia konfiguracji w obiektu maszyny Wirtualnej, a następnie uruchom to polecenie. |
| Dodawanie dysku danych do maszyny wirtualnej |[Dodaj AzureRmVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmdatadisk) - VM $vm — nazwy "myDataDisk" - VhdUri "https://mystore1.blob.core.windows.net/vhds/myDataDisk.vhd" - LUN # — buforowanie ReadWrite - DiskSizeinGB # - CreateOption pusta<BR></BR><BR></BR>Get-AzureRmVM umożliwia, Pobierz obiekt VM. Określ liczbę jednostek LUN i rozmiaru dysku. Uruchom AzureRmVM aktualizację można zastosować zmian konfiguracyjnych do maszyny Wirtualnej. Dysk, który możesz dodać nie jest zainicjowany. |
| Usuwanie dysku danych z maszyny wirtualnej |[Remove-AzureRmVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvmdatadisk) -VM $vm -Name "myDataDisk"<BR></BR><BR></BR>Get-AzureRmVM umożliwia, Pobierz obiekt VM. Uruchom AzureRmVM aktualizację można zastosować zmian konfiguracyjnych do maszyny Wirtualnej. |
| Dodaj rozszerzenie do maszyny Wirtualnej |[AzureRmVMExtension zestawu](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) - ResourceGroupName $myResourceGroup — lokalizacji $location - VMName $myVM-Name "extensionName" — wydawcy "publisherName" — typu "extensionType" - TypeHandlerVersion "#. #" — Ustawienia $Settings - ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>Uruchom to polecenie z odpowiednią [informacje o konfiguracji](template-description.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#extensions) rozszerzenia, które chcesz zainstalować. |
| Usuwanie rozszerzenia maszyny wirtualnej |[Remove-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvmextension) -ResourceGroupName $myResourceGroup -Name "extensionName" -VMName $myVM |

## <a name="next-steps"></a>Kolejne kroki
* Podstawowe czynności wykonywane w celu utworzenia maszyny wirtualnej w [Utwórz maszynę Wirtualną z systemem Windows przy użyciu usługi Resource Manager i programu PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

