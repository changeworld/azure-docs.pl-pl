---
title: Tworzenie hosta bastionu przy użyciu usługi Azure Powershell | Dokumenty firmy Microsoft
description: W tym artykule dowiesz się, jak utworzyć hosta Bastionu platformy Azure
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: c3e4c2f2bac45f2e366764473a34b0536bb4cc44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76990457"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Tworzenie hosta bastionu platformy Azure przy użyciu programu Azure PowerShell

W tym artykule pokazano, jak utworzyć hosta bastionu platformy Azure przy użyciu programu PowerShell. Po aprowizyjce usługi Azure Bastion w sieci wirtualnej bezproblemowe środowisko RDP/SSH jest dostępne dla wszystkich maszyn wirtualnych w tej samej sieci wirtualnej. Wdrożenie usługi Azure Bastion jest na sieć wirtualną, a nie na subskrypcję/konto lub maszynę wirtualną.

Opcjonalnie można utworzyć hosta Bastion platformy Azure przy użyciu [witryny Azure portal.](bastion-create-host-portal.md)

## <a name="before-you-begin"></a>Przed rozpoczęciem

Sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Tworzenie hosta bastionu

Ta sekcja ułatwia tworzenie nowego zasobu bastionu platformy Azure przy użyciu programu Azure PowerShell.

1. Utwórz sieć wirtualną i podsieć Bastion platformy Azure. Należy utworzyć podsieć Bastion platformy Azure przy użyciu wartości nazwy **AzureBastionSubnet**. Ta wartość umożliwia platformie Azure wiedzieć, które podsieci wdrożyć zasoby bastionu. Jest to inna niż podsieć bramy. Należy użyć podsieci co najmniej /27 lub większej podsieci (/27, /26 itd.). Utwórz **usługę AzureBastionSubnet** bez żadnych tabel tras lub delegacji. Jeśli używasz sieciowych grup zabezpieczeń w **usłudze AzureBastionSubnet,** zapoznaj się z artykułem [Praca z sieciami roboczymi.](bastion-nsg.md)

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName " myBastionRG " -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Utwórz publiczny adres IP dla bastionu platformy Azure. Publiczny adres IP jest publicznym adresem IP zasobu Bastion, na którym będzie dostępny protokół RDP/SSH (za port 443). Publiczny adres IP musi znajdować się w tym samym regionie co zasób Bastion, który tworzysz.

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. Utwórz nowy zasób Bastion platformy Azure w sieci AzureBastionSubnet sieci wirtualnej. Tworzenie i wdrażanie zasobu Bastion zajmuje około 5 minut.

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName " myBastionRG " -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>Następne kroki

* Przeczytaj często zadawane pytania dotyczące [bastionu, aby](bastion-faq.md) uzyskać dodatkowe informacje.

* Aby używać grup zabezpieczeń sieciowych w podsieci Bastion platformy Azure, zobacz [Praca z sieciami roboczymi](bastion-nsg.md).
