---
title: Tworzenie maszyny Wirtualnej ze statycznym prywatnym adresem IP — Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć maszynę wirtualną przy użyciu prywatnego adresu IP przy użyciu programu PowerShell.
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
ms.openlocfilehash: 9115386b0543e1ac840aec29fc7f57e7c98c03bb
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64685336"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>Tworzenie maszyny wirtualnej ze statycznym prywatnym adresem IP przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można utworzyć maszynę wirtualną (VM) za pomocą statycznego prywatnego adresu IP. Przypisz statyczny prywatny adres IP, a nie adres dynamiczny, jeśli chcesz wybrać, które adres z podsieci jest przypisany do maszyny Wirtualnej. Dowiedz się więcej o [statyczne prywatne adresy IP](virtual-network-ip-addresses-overview-arm.md#allocation-method). Aby zmienić prywatnego adresu IP przypisane do istniejącej maszyny Wirtualnej z dynamicznego na statyczne lub pracować z publicznych adresów IP, zobacz [Dodawanie, zmienianie lub usuwanie adresów IP](virtual-network-network-interface-addresses.md).

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Z komputera lokalnego lub za pomocą usługi Azure Cloud Shell, można wykonać następujące czynności. Aby korzystać z komputera lokalnego, upewnij się, że [zainstalowany program Azure PowerShell](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Aby użyć usługi Azure Cloud Shell, wybierz **wypróbuj** w prawym górnym rogu pola dowolnego polecenia, która jest zgodna. Cloud Shell zaloguje się do platformy Azure.

1. Jeśli używasz powłoki Cloud, przejdź do kroku 2. Otwórz polecenia sesji i logowania do platformy Azure przy `Connect-AzAccount`.
2. Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład tworzy grupę zasobów w regionie wschodnie stany USA Azure:

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. Utwórz konfigurację podsieci i sieci wirtualnej z [New AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) i [New AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) poleceń:

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

4. Utwórz interfejs sieciowy w sieci wirtualnej i przypisać prywatny adres IP z podsieci z interfejsem sieciowym za pomocą [New AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig) i [New AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) polecenia:

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

5. Utwórz konfigurację maszyny Wirtualnej za pomocą [New AzVMConfig](/powershell/module/Az.Compute/New-AzVMConfig), a następnie utwórz maszynę Wirtualną za pomocą [New-AzVM](/powershell/module/az.Compute/New-azVM). Po wyświetleniu monitu podaj nazwę użytkownika i hasło, które ma być używany jako poświadczenia logowania dla maszyny Wirtualnej:

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> Że można dodawać prywatnej ustawienia adresu IP do systemu operacyjnego, firma Microsoft zaleca przeciwnym razie dopiero po odczytu [Dodaj prywatny adres IP do systemu operacyjnego](virtual-network-network-interface-addresses.md#private).
> 
> 
> <a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>
> 
> [!IMPORTANT]
> Dostęp do maszyny Wirtualnej z Internetu, należy przypisać publiczny adres IP do maszyny Wirtualnej. Można również zmienić dynamicznego przydzielania prywatnych adresów IP do przypisania statycznego. Aby uzyskać więcej informacji, zobacz [apletu Dodaj lub zmień adresy IP](virtual-network-network-interface-addresses.md). Ponadto zaleca się ograniczenie ruchu sieciowego z maszyną wirtualną przez skojarzenie sieciowej grupy zabezpieczeń do interfejsu sieciowego i/lub podsieci, w której został utworzony interfejs sieciowy w. Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami zabezpieczeń sieci](manage-network-security-group.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie będą już potrzebne, możesz użyć [AzResourceGroup Usuń](/powershell/module/az.resources/remove-azresourcegroup) Aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [prywatnych adresów IP](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) i przypisywanie [statyczny prywatny adres IP](virtual-network-network-interface-addresses.md#add-ip-addresses) na maszynie wirtualnej platformy Azure.
- Dowiedz się więcej na temat tworzenia [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) maszyn wirtualnych.
