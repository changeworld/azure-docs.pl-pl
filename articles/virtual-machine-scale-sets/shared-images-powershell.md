---
title: Użyj udostępnionego obrazu maszyny Wirtualnej w celu utworzenia zestawu skalowania w usłudze Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać programu Azure PowerShell do tworzenia udostępnionych obrazów maszyn wirtualnych będzie używana do wdrażania zestawów skalowania maszyn wirtualnych na platformie Azure.
services: virtual-machine-scale-sets
documentationcenter: ''
author: axayjo
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2019
ms.author: akjosh; cynthn
ms.custom: ''
ms.openlocfilehash: 055242c3118ce9d972d55cdc6a21bf623679a0c1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242048"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-powershell"></a>Tworzenie i używanie udostępnianych obrazów dla zestawów skalowania maszyn wirtualnych z programem Azure PowerShell

Podczas tworzenia zestawu skalowania należy wskazać obraz używany do wdrożenia wystąpień maszyn wirtualnych. Usługi udostępnione galerii obrazów znacznie upraszcza obraz niestandardowy do udostępniania całej organizacji. Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Obrazy niestandardowe mogą służyć do ładowania początkowego konfiguracji, na przykład do wstępnego ładowania aplikacji, konfiguracji aplikacji i innych konfiguracji systemu operacyjnego. 

Galeria obrazów współdzielona umożliwia udostępnianie niestandardowych obrazów maszyn wirtualnych z innymi osobami w organizacji, w ramach lub w wielu regionach, w ramach dzierżawy usługi AAD. Wybierz obrazy, które chcesz udostępnić, regiony mają być dostępne w nich i, którym chcesz udostępnić je za pomocą. Możesz utworzyć wiele galerii, dzięki czemu można grupować logicznie udostępnianych obrazów. 

Galeria jest zasobem najwyższego poziomu, który zapewnia kontrolę dostępu pełnej opartej na rolach (RBAC). Obrazy mogą być poddany kontroli wersji, a użytkownik może replikować każdej wersji obrazu z innym zestawem regiony platformy Azure. Galeria działa tylko z zarządzanych obrazów. 

Funkcja galerii obrazów współdzielona ma wiele typów zasobów. Firma Microsoft będzie przy użyciu lub kompilowania w tym artykule:

| Resource | Opis|
|----------|------------|
| **Zarządzany obraz** | Jest to podstawowy obraz, które mogą być używane autonomicznie lub użyty do utworzenia **wersję obrazu** w galerii obrazów. Obrazy zarządzane są tworzone na podstawie uogólnionego maszyn wirtualnych. Zarządzany obraz to specjalny typ wirtualnego dysku twardego, który można utworzyć wiele maszyn wirtualnych i może teraz służyć do tworzenia wersji udostępnionego obrazu. |
| **Galeria obrazów** | W portalu Azure Marketplace, takich jak **galerii obrazów** to repozytorium do zarządzania i udostępniania obrazów, ale możesz kontrolować, kto ma dostęp. |
| **Definicję obrazu** | Obrazy w galerii są zdefiniowane i zawierają informacje dotyczące obrazu i wymagania dotyczące korzystania z niego wewnętrznie. Dotyczy to również, czy obraz jest Windows lub Linux, informacje o wersji i wymagań dotyczących minimalnej i maksymalnej pamięci. Jest definicja typu obrazu. |
| **Wersja obrazu** | **Wersję obrazu** , które jest używane do utworzenia maszyny Wirtualnej, podczas korzystania z galerii. Może mieć wiele wersji obrazu, zgodnie z potrzebami w danym środowisku. Gdy używasz, takie jak zarządzany obraz **wersję obrazu** tworzenie maszyny Wirtualnej, wersja obrazu jest używany do tworzenia nowych dysków dla maszyny Wirtualnej. Wersje obrazów można wielokrotnie. |

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poniższych krokach wyjaśniono szczegółowo, jak na podstawie istniejącej maszyny wirtualnej utworzyć obraz niestandardowy do ponownego użycia, za pomocą którego można tworzyć nowe wystąpienia maszyn wirtualnych.

Aby ukończyć przykład, w tym artykule, konieczne jest posiadanie istniejącego obrazu zarządzanego. Możesz wykonać [samouczka: Tworzenie i używanie niestandardowego obrazu dla zestawów skalowania maszyn wirtualnych przy użyciu programu Azure PowerShell](tutorial-use-custom-image-powershell.md) aby je utworzyć, jeśli to konieczne. Jeśli zarządzany obraz zawiera dysk z danymi, rozmiar dysku danych nie może być więcej niż 1 TB.

Podczas pracy nad artykułu, Zamień grupę zasobów i maszynę Wirtualną przypadkach nazwy.


[!INCLUDE [virtual-machines-common-shared-images-ps](../../includes/virtual-machines-common-shared-images-powershell.md)]

## <a name="create-a-scale-set-from-the-shared-image-version"></a>Tworzenie zestawu skalowania na podstawie wersji udostępnionego obrazu

Utwórz zestaw skalowania maszyn wirtualnych przy użyciu polecenia [New-AzVmss](/powershell/module/az.compute/new-azvmss). Poniższy przykład tworzy zestawu skalowania na podstawie nowej wersji obrazu w *południowo-środkowe stany USA* centrum danych. Po wyświetleniu monitu Ustaw poświadczenia administracyjne dla wystąpień maszyn wirtualnych w zestawie skalowania:


```azurepowershell-interactive
# Define variables
$resourceGroupName = "myVMSSRG"
$scaleSetName = "myScaleSet"
$location = "South Central US"
$cred = Get-Credential

# Create a resource group
New-AzResourceGroup -ResourceGroupName $resourceGroupName -Location $location

# Create a netowrking pieces
$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $resourceGroupName `
  -Name "myVnet" `
  -Location $location `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName $resourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -Name "myPublicIP"
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
$inboundNATPool = New-AzLoadBalancerInboundNatPoolConfig `
  -Name "myRDPRule" `
  -FrontendIpConfigurationId $frontendIP.Id `
  -Protocol TCP `
  -FrontendPortRangeStart 50001 `
  -FrontendPortRangeEnd 50010 `
  -BackendPort 3389
# Create the load balancer and health probe
$lb = New-AzLoadBalancer `
  -ResourceGroupName $resourceGroupName `
  -Name "myLoadBalancer" `
  -Location $location `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -InboundNatPool $inboundNATPool
Add-AzLoadBalancerProbeConfig -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol TCP `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol TCP `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe (Get-AzLoadBalancerProbeConfig -Name "myHealthProbe" -LoadBalancer $lb)
Set-AzLoadBalancer -LoadBalancer $lb

# Create IP address configurations
$ipConfig = New-AzVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -LoadBalancerInboundNatPoolsId $inboundNATPool.Id `
  -SubnetId $vnet.Subnets[0].Id

# Create a configuration 
$vmssConfig = New-AzVmssConfig `
    -Location $location `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode "Automatic"

# Reference the image version
Set-AzVmssStorageProfile $vmssConfig `
  -OsDiskCreateOption "FromImage" `
  -Id $imageVersion.Id

# Complete the configuration
Set-AzVmssOsProfile $vmssConfig `
  -AdminUsername $cred.UserName `
  -AdminPassword $cred.Password `
  -ComputerNamePrefix "myVM"
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig

# Create the scale set 
New-AzVmss `
  -ResourceGroupName $resourceGroupName `
  -Name $scaleSetName `
  -VirtualMachineScaleSet $vmssConfig
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych trwa kilka minut.

[!INCLUDE [virtual-machines-common-gallery-list-ps](../../includes/virtual-machines-common-gallery-list-ps.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-ps](../../includes/virtual-machines-common-shared-images-update-delete-ps.md)]


## <a name="next-steps"></a>Kolejne kroki

Można również utworzyć zasób galerii obrazów udostępnione za pomocą szablonów. Dostępnych jest kilka szablonów szybkiego startu platformy Azure: 

- [Tworzenie udostępnionego galerii obrazów](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Utwórz definicję obrazu w udostępnionym galerii obrazów](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Utwórz wersję obrazu w udostępnionym galerii obrazów](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Utwórz Maszynę wirtualną z obrazu wersji](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Aby uzyskać więcej informacji na temat udostępnione, galerie obrazów zobacz [Przegląd](shared-image-galleries.md). Jeśli napotkasz problemy, zobacz [Rozwiązywanie problemów udostępnione galerie obrazów](troubleshooting-shared-images.md).
