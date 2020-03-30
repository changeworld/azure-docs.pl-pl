---
title: Tworzenie maszyny Wirtualnej ze statycznym publicznym adresem IP — Program PowerShell | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć maszynę wirtualną ze statycznym publicznym adresem IP przy użyciu programu PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ad975ab9-d69f-45c1-9e45-0d3f0f51e87e
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: 0eb4f86a2484486658171ab4b099794e4ba3e4bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76043392"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-powershell"></a>Tworzenie maszyny wirtualnej ze statycznym publicznym adresem IP przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można utworzyć maszynę wirtualną ze statycznym publicznym adresem IP. Publiczny adres IP umożliwia komunikację z maszyną wirtualną z Internetu. Przypisz statyczny publiczny adres IP, a nie adres dynamiczny, aby upewnić się, że adres nigdy się nie zmienia. Dowiedz się więcej o [statycznych publicznych adresach IP](virtual-network-ip-addresses-overview-arm.md#allocation-method). Aby zmienić publiczny adres IP przypisany do istniejącej maszyny wirtualnej z dynamicznego na statyczny lub pracować z prywatnymi adresami IP, zobacz [Dodawanie, zmienianie lub usuwanie adresów IP](virtual-network-network-interface-addresses.md). Publiczne adresy IP mają [opłatę nominalną](https://azure.microsoft.com/pricing/details/ip-addresses)i istnieje [limit](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) liczby publicznych adresów IP, których można używać na subskrypcję.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Poniższe kroki można wykonać z komputera lokalnego lub przy użyciu usługi Azure Cloud Shell. Aby korzystać z komputera lokalnego, upewnij się, że [masz zainstalowany program Azure PowerShell](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Aby użyć usługi Azure Cloud Shell, wybierz try **it** w prawym górnym rogu dowolnego następującego pola poleceń. Powłoka w chmurze podpisuje Cię na platformie Azure.

1. Jeśli używasz powłoki chmury, przejdź do kroku 2. Otwórz sesję polecenia i zaloguj `Connect-AzAccount`się na platformie Azure za pomocą pliku .
2. Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład tworzy grupę zasobów w regionie Wschodnia platforma Azure stanów USA:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location EastUS
   ```

3. Utwórz maszynę wirtualną za pomocą polecenia [New-AzVM.](/powershell/module/az.Compute/New-azVM) Opcja `-AllocationMethod "Static"` przypisuje statyczny publiczny adres IP do maszyny wirtualnej. Poniższy przykład tworzy maszynę wirtualną systemu Windows Server ze statycznym, podstawowym publicznym adresem IP jednostki SKU o nazwie *myPublicIpAddress*. Po wyświetleniu monitu podaj nazwę użytkownika i hasło, które mają być używane jako poświadczenia logowania dla maszyny wirtualnej:

   ```azurepowershell-interactive
   New-AzVm `
     -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -PublicIpAddressName "myPublicIpAddress" `
     -AllocationMethod "Static"
   ```

   Jeśli publiczny adres IP musi być standardową jednostką SKU, należy [utworzyć publiczny adres IP](virtual-network-public-ip-address.md#create-a-public-ip-address), utworzyć interfejs [sieciowy,](virtual-network-network-interface.md#create-a-network-interface) [przypisać publiczny adres IP do interfejsu sieciowego,](virtual-network-network-interface-addresses.md#add-ip-addresses)a następnie [utworzyć maszynę wirtualną z interfejsem sieciowym](virtual-network-network-interface-vm.md#add-existing-network-interfaces-to-a-new-vm)w oddzielnych krokach. Dowiedz się więcej o [publicznych jednostkach SKU adresów IP](virtual-network-ip-addresses-overview-arm.md#sku). Jeśli maszyna wirtualna zostanie dodana do puli zaplecza publicznego modułu równoważenia obciążenia platformy Azure, jednostka SKU publicznego adresu IP maszyny wirtualnej musi być zgodna z jednostką SKU publicznego adresu IP modułu równoważenia obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Azure Load Balancer](../load-balancer/concepts-limitations.md#skus).

4. Wyświetl publiczny adres IP przypisany i potwierdź, że został on utworzony jako adres statyczny, z [adresem Get-AzPublicIpAddress:](/powershell/module/az.network/get-azpublicipaddress)

   ```azurepowershell-interactive
   Get-AzPublicIpAddress `
     -ResourceGroupName "myResourceGroup" `
     -Name "myPublicIpAddress" `
     | Select "IpAddress", "PublicIpAllocationMethod" `
     | Format-Table
   ```

   Platforma Azure przypisała publiczny adres IP z adresów używanych w regionie, w którego utworzono maszynę wirtualną. Możesz pobrać listę zakresów (prefiksów) dla następujących chmur platformy Azure: [Publiczna](https://www.microsoft.com/download/details.aspx?id=56519), [Instytucje rządowe USA](https://www.microsoft.com/download/details.aspx?id=57063), [Chiny](https://www.microsoft.com/download/details.aspx?id=57062) i [Niemcy](https://www.microsoft.com/download/details.aspx?id=57064).

> [!WARNING]
> Nie należy modyfikować ustawień adresu IP w systemie operacyjnym maszyny wirtualnej. System operacyjny nie jest świadomy publicznych adresów IP platformy Azure. Chociaż można dodać ustawienia prywatnego adresu IP do systemu operacyjnego, zalecamy, aby nie robić tego, chyba że jest to konieczne, a dopiero po [przeczytaniu Dodaj prywatny adres IP do systemu operacyjnego](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, można użyć [Remove-AzResourceGroup,](/powershell/module/az.resources/remove-azresourcegroup) aby usunąć grupę zasobów i wszystkie zasoby, które zawiera:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [publicznych adresach IP na](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) platformie Azure
- Dowiedz się więcej o wszystkich [ustawieniach publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Dowiedz się więcej o [prywatnych adresach IP](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) i przypisywaniu [statycznego prywatnego adresu IP](virtual-network-network-interface-addresses.md#add-ip-addresses) do maszyny wirtualnej platformy Azure
- Dowiedz się więcej o tworzeniu maszyn wirtualnych [z systemem Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
