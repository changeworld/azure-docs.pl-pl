---
title: Tworzenie maszyny wirtualnej ze statycznym prywatnym adresem IP — Azure PowerShell
description: Dowiedz się, jak utworzyć maszynę wirtualną z prywatnym adresem IP przy użyciu programu PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2019
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 1745ca176fac18b4903686cb556670531ee40a1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244760"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>Tworzenie maszyny wirtualnej ze statycznym prywatnym adresem IP przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Maszynę wirtualną (VM) można utworzyć ze statycznym prywatnym adresem IP. Przypisz statyczny prywatny adres IP, a nie adres dynamiczny, jeśli chcesz wybrać adres z podsieci jest przypisany do maszyny Wirtualnej. Dowiedz się więcej o [statycznych prywatnych adresach IP](virtual-network-ip-addresses-overview-arm.md#allocation-method). Aby zmienić prywatny adres IP przypisany do istniejącej maszyny Wirtualnej z dynamicznego na statyczny lub pracować z publicznymi adresami IP, zobacz [Dodawanie, zmienianie lub usuwanie adresów IP](virtual-network-network-interface-addresses.md).

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Poniższe kroki można wykonać z komputera lokalnego lub przy użyciu usługi Azure Cloud Shell. Aby korzystać z komputera lokalnego, upewnij się, że [masz zainstalowany program Azure PowerShell](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Aby użyć usługi Azure Cloud Shell, wybierz try **it** w prawym górnym rogu dowolnego następującego pola poleceń. Powłoka w chmurze podpisuje Cię na platformie Azure.

1. Jeśli używasz powłoki chmury, przejdź do kroku 2. Otwórz sesję polecenia i zaloguj `Connect-AzAccount`się na platformie Azure za pomocą pliku .
2. Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład tworzy grupę zasobów w regionie Wschodnia platforma Azure stanów USA:

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. Utwórz konfigurację podsieci i sieć wirtualną za pomocą poleceń [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) i [New-AzVirtualNetwork:](/powershell/module/az.network/new-azvirtualnetwork)

   ```azurepowershell-interactive
   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object for use in a later step.
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

4. Utwórz interfejs sieciowy w sieci wirtualnej i przypisz prywatny adres IP z podsieci do interfejsu sieciowego za pomocą poleceń [New-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig) i [New-AzNetworkInterface:](/powershell/module/az.network/new-aznetworkinterface)

   ```azurepowershell-interactive
   $IpConfigName1 = "IPConfig-1"
   $IpConfig1     = New-AzNetworkInterfaceIpConfig `
     -Name $IpConfigName1 `
     -Subnet $Subnet `
     -PrivateIpAddress 10.0.0.4 `
     -Primary

   $NIC = New-AzNetworkInterface `
     -Name MyNIC `
     -ResourceGroupName $RgName `
     -Location $Location `
     -IpConfiguration $IpConfig1
   ```

5. Utwórz konfigurację maszyny wirtualnej za pomocą [programu New-AzVMConfig,](/powershell/module/Az.Compute/New-AzVMConfig)a następnie utwórz maszynę wirtualną za pomocą [programu New-AzVM](/powershell/module/az.Compute/New-azVM). Po wyświetleniu monitu podaj nazwę użytkownika i hasło, które mają być używane jako poświadczenia logowania dla maszyny Wirtualnej:

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> Chociaż można dodać ustawienia prywatnego adresu IP do systemu operacyjnego, zalecamy nie robić tego dopiero po [przeczytaniu Dodaj prywatny adres IP do systemu operacyjnego](virtual-network-network-interface-addresses.md#private).
> 
> 
> <a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>
> 
> [!IMPORTANT]
> Aby uzyskać dostęp do maszyny Wirtualnej z Internetu, należy przypisać publiczny adres IP do maszyny Wirtualnej. Można również zmienić dynamiczne przypisanie prywatnego adresu IP na przypisanie statyczne. Aby uzyskać szczegółowe informacje, zobacz [Dodawanie lub zmienianie adresów IP](virtual-network-network-interface-addresses.md). Ponadto zaleca się ograniczenie ruchu sieciowego do maszyny Wirtualnej przez skojarzenie sieciowej grupy zabezpieczeń z interfejsem sieciowym, podsiecią, w której utworzono interfejs sieciowy lub obie te elementy. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie grupami zabezpieczeń sieci .](manage-network-security-group.md)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, można użyć [Remove-AzResourceGroup,](/powershell/module/az.resources/remove-azresourcegroup) aby usunąć grupę zasobów i wszystkie zasoby, które zawiera:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [prywatnych adresach IP](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) i przypisywaniu [statycznego prywatnego adresu IP](virtual-network-network-interface-addresses.md#add-ip-addresses) do maszyny wirtualnej platformy Azure.
- Dowiedz się więcej o tworzeniu maszyn wirtualnych [z systemem Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i [Windows.](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
