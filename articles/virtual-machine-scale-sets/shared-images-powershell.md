---
title: Tworzenie zestawu skalowania na platformie Azure za pomocą udostępnionych obrazów maszyn wirtualnych
description: Dowiedz się, jak używać programu Azure PowerShell do tworzenia udostępnionych obrazów maszyn wirtualnych do użycia do wdrażania zestawów skalowania maszyny wirtualnej na platformie Azure.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 5f4eca88614a98f0caf87d04847029328042edd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368735"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-powershell"></a>Tworzenie i używanie udostępnionych obrazów dla zestawów skalowania maszyn wirtualnych za pomocą programu Azure PowerShell

Podczas tworzenia zestawu skalowania należy wskazać obraz używany do wdrożenia wystąpień maszyn wirtualnych. Usługa Shared Image Gallery znacznie upraszcza niestandardowe udostępnianie obrazów w całej organizacji. Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Obrazy niestandardowe mogą służyć do ładowania początkowego konfiguracji, na przykład do wstępnego ładowania aplikacji, konfiguracji aplikacji i innych konfiguracji systemu operacyjnego. 

Galeria obrazów udostępnionych umożliwia udostępnianie niestandardowych obrazów maszyn wirtualnych innym osobom w organizacji, w obrębie lub między regionami, w dzierżawie usługi AAD. Wybierz obrazy, które chcesz udostępnić, regiony, w których chcesz je udostępnić i komu chcesz je udostępnić. Można utworzyć wiele galerii, aby logicznie grupować udostępnione obrazy. 

Galeria jest zasobem najwyższego poziomu, który zapewnia pełną kontrolę dostępu opartą na rolach (RBAC). Obrazy mogą być wersjona i można wybrać do replikacji każdej wersji obrazu do innego zestawu regionów platformy Azure. Galeria działa tylko z zarządzanymi obrazami. 

Funkcja Galeria obrazów udostępnionych ma wiele typów zasobów. Będziemy używać lub budować te w tym artykule:

| Zasób | Opis|
|----------|------------|
| **Obraz zarządzany** | Jest to podstawowy obraz, który może być używany samodzielnie lub używany do tworzenia **wersji obrazu** w galerii obrazów. Obrazy zarządzane są tworzone na uogólnionych maszynach wirtualnych. Obraz zarządzany jest specjalnym typem dysku VHD, który może służyć do tworzenia wielu maszyn wirtualnych i może być teraz używany do tworzenia wersji obrazów udostępnionych. |
| **Galeria zdjęć** | Podobnie jak w portalu Azure **Marketplace, galeria obrazów** jest repozytorium do zarządzania i udostępniania obrazów, ale można kontrolować, kto ma dostęp. |
| **Definicja obrazu** | Obrazy są definiowane w galerii i zawierają informacje o obrazie i wymaganiach dotyczących używania go wewnętrznie. Obejmuje to, czy obraz jest Windows lub Linux, informacje o wersji i minimalne i maksymalne wymagania dotyczące pamięci. Jest to definicja typu obrazu. |
| **Wersja obrazu** | **Wersja obrazu** jest to, czego używasz do tworzenia maszyny Wirtualnej podczas korzystania z galerii. W zależności od środowiska może być dostępnych wiele wersji obrazu. Podobnie jak w przypadku obrazu zarządzanego, podczas tworzenia maszyny Wirtualnej przy użyciu **wersji obrazu** wersja obrazu jest używana do tworzenia nowych dysków dla maszyny Wirtualnej. Wersje obrazów mogą być używane wiele razy. |

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poniższych krokach wyjaśniono szczegółowo, jak na podstawie istniejącej maszyny wirtualnej utworzyć obraz niestandardowy do ponownego użycia, za pomocą którego można tworzyć nowe wystąpienia maszyn wirtualnych.

Aby ukończyć przykład w tym artykule, musisz mieć istniejący obraz zarządzany. Możesz wykonać [samouczek: Tworzenie i używanie niestandardowego obrazu dla zestawów skalowania maszyny wirtualnej za pomocą programu Azure PowerShell,](tutorial-use-custom-image-powershell.md) aby utworzyć go w razie potrzeby. Jeśli obraz zarządzany zawiera dysk danych, rozmiar dysku danych nie może być większy niż 1 TB.

Podczas pracy nad tym artykułem w razie potrzeby zastąp nazwy grupy zasobów i maszyn wirtualnych.


[!INCLUDE [virtual-machines-common-shared-images-ps](../../includes/virtual-machines-common-shared-images-powershell.md)]

## <a name="create-a-scale-set-from-the-shared-image-version"></a>Tworzenie zestawu skalowania z wersji obrazu udostępnionego

Utwórz zestaw skalowania maszyn wirtualnych przy użyciu polecenia [New-AzVmss](/powershell/module/az.compute/new-azvmss). Poniższy przykład tworzy zestaw skalowania z nowej wersji obrazu w centrum danych *południowo-środkowej usa.* Po wyświetleniu monitu ustaw własne poświadczenia administracyjne dla wystąpień maszyny Wirtualnej w zestawie skalowania:


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
  -ImageReferenceId $imageVersion.Id

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


## <a name="next-steps"></a>Następne kroki

Zasób Shared Image Gallery można również utworzyć przy użyciu szablonów. Dostępnych jest kilka szablonów szybki start platformy Azure: 

- [Tworzenie galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Tworzenie definicji obrazu w galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Tworzenie wersji obrazu w galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Tworzenie maszyny Wirtualnej z wersji obrazu](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Aby uzyskać więcej informacji na temat galerii obrazów udostępnionych, zobacz [Omówienie](shared-image-galleries.md). Jeśli napotkasz problemy, zobacz [Rozwiązywanie problemów z udostępnionymi galeriami obrazów](troubleshooting-shared-images.md).
