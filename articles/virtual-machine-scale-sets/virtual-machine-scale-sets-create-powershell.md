---
title: "Tworzenie zestawu skalowania maszyn wirtualnych przy użyciu programu Azure PowerShell | Microsoft Docs"
description: "Dowiedz się, jak szybko utworzyć skalę maszyny wirtualnej przy użyciu programu Azure PowerShell"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: iainfou
ms.openlocfilehash: c5ca90ae303d62c4ad453971f84da78866b0c599
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/18/2018
---
# <a name="create-a-virtual-machine-scale-set-with-azure-powershell"></a>Tworzenie zestawu skalowania maszyn wirtualnych przy użyciu programu Azure PowerShell
Zestaw skalowania maszyn wirtualnych umożliwia wdrożenie zestawu identycznych, automatycznie skalowanych maszyn wirtualnych, oraz zarządzanie nimi. Maszyny wirtualne w zestawie skalowania można skalować ręcznie lub można zdefiniować reguły skalowania automatycznego na podstawie użycia zasobów, takich jak procesor CPU, zapotrzebowanie na pamięć lub ruch sieciowy. W tym artykule wprowadzającym opisano tworzenie zestawu skalowania maszyn wirtualnych za pomocą programu Azure PowerShell. Zestaw skalowania można również utworzyć przy użyciu [interfejsu wiersza polecenia platformy Azure w wersji 2.0](virtual-machine-scale-sets-create-cli.md) lub [witryny Azure Portal](virtual-machine-scale-sets-create-portal.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga modułu Azure PowerShell w wersji 4.4.1 lub nowszej. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.


## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Zanim będzie można utworzyć zestaw skalowania, utwórz grupę zasobów za pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *EastUS*:

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Location "EastUS"
```


## <a name="create-load-balancer"></a>Tworzenie modułu równoważenia obciążenia
Wystąpienia maszyn wirtualnych w zestawie skalowania są połączone z modułem równoważenia obciążenia. Moduł równoważenia obciążenia platformy Azure jest modułem w warstwie 4 (TCP, UDP), który zapewnia wysoką dostępność, rozkładając ruch przychodzący na maszyny wirtualne w dobrej kondycji. Sonda kondycji modułu równoważenia obciążenia monitoruje określony port na każdej maszynie wirtualnej i dystrybuuje ruch tylko do działającej maszyny wirtualnej.

Utwórz sieć wirtualną i moduł równoważenia obciążenia, który ma publiczny adres IP i rozpowszechnia ruch internetowy na porcie 80. Aby utworzyć te zasoby, skopiuj i wklej następujące polecenia cmdlet programu PowerShell:

```azurepowershell-interactive
# Create a virtual network subnet
$subnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVnet" `
  -Location "EastUS" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet

# Create a public IP address
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -AllocationMethod Static `
  -Name "myPublicIP"

# Create a frontend and backend IP pool
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"

# Create a Network Address Translation (NAT) pool
$inboundNATPool = New-AzureRmLoadBalancerInboundNatPoolConfig `
  -Name "myRDPRule" `
  -FrontendIpConfigurationId $frontendIP.Id `
  -Protocol TCP `
  -FrontendPortRangeStart 50001 `
  -FrontendPortRangeEnd 50010 `
  -BackendPort 3389

# Create the load balancer
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName "myResourceGroup" `
  -Name "myLoadBalancer" `
  -Location "EastUS" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -InboundNatPool $inboundNATPool

# Create a load balancer health probe on port 80
Add-AzureRmLoadBalancerProbeConfig -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol TCP `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2

# Create a load balancer rule to distribute traffic on port 80
Add-AzureRmLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol TCP `
  -FrontendPort 80 `
  -BackendPort 80

# Update the load balancer configuration
Set-AzureRmLoadBalancer -LoadBalancer $lb

# Create IP address configurations
$ipConfig = New-AzureRmVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -LoadBalancerInboundNatPoolsId $inboundNATPool.Id `
  -SubnetId $vnet.Subnets[0].Id
```


## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania
Teraz utwórz zestaw skalowania maszyn wirtualnych przy użyciu polecenia [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). W poniższym przykładzie pokazano tworzenie zestawu skalowania o nazwie *myScaleSet*, używającego obrazu platformy systemu *Windows Server 2016 Datacenter*. Obiekt *vmssConfig* tworzy 2 wystąpienia maszyn wirtualnych w lokalizacji East US (wschodnie stany USA) z poświadczeniami określonymi za pomocą zmiennych *adminUsername* i *securePassword*. Podaj swoje własne poświadczenia i utwórz zestaw skalowania w następujący sposób:

```azurepowershell-interactive
# Provide your own secure password for use with the VM instances
$securePassword = "P@ssword!"
$adminUsername = "azureuser"

# Create a config object
$vmssConfig = New-AzureRmVmssConfig `
    -Location "EastUS" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic

# Reference a virtual machine image from the gallery
Set-AzureRmVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher "MicrosoftWindowsServer" `
  -ImageReferenceOffer "WindowsServer" `
  -ImageReferenceSku "2016-Datacenter" `
  -ImageReferenceVersion "latest"

# Set up information for authenticating with the virtual machine
Set-AzureRmVmssOsProfile $vmssConfig `
  -AdminUsername $adminUsername `
  -AdminPassword $securePassword `
  -ComputerNamePrefix "myVM"

# Attach the virtual network to the config object
Add-AzureRmVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig

# Create the scale set with the config object (this step might take a few minutes)
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmssConfig
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych trwa kilka minut.


## <a name="install-iis-webserver"></a>Instalowanie serwera internetowego usług IIS
Aby przetestować zestaw skalowania, użyj niestandardowego rozszerzenia skryptu w celu pobrania i uruchomienia skryptu, który instaluje usługi IIS na wystąpieniach maszyn wirtualnych. To rozszerzenie jest przydatne w przypadku konfiguracji po wdrożeniu, instalowania oprogramowania lub każdego innego zadania związanego z konfiguracją lub zarządzaniem. Aby uzyskać więcej informacji, zobacz [Omówienie niestandardowego rozszerzenia skryptu](../virtual-machines/windows/extensions-customscript.md).

Zastosuj niestandardowe rozszerzenie skryptu, które instaluje usługi IIS, w następujący sposób:

```azurepowershell-interactive
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroup" `
            -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```


## <a name="test-your-web-server"></a>Testowanie serwera internetowego
Aby zobaczyć, jak działa serwer internetowy, uzyskaj publiczny adres IP modułu równoważenia obciążenia za pomocą polecenia [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). W poniższym przykładzie pokazano uzyskiwanie adres IP utworzonego w grupie zasobów *myResourceGroup*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Wprowadź publiczny adres IP modułu równoważenia obciążenia w przeglądarce internetowej. Moduł równoważenia obciążenia kieruje ruch do jednego z wystąpień maszyn wirtualnych, jak pokazano w poniższym przykładzie:

![Uruchamianie witryny usług IIS](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy grupa zasobów, zestaw skalowania i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) w następujący sposób:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup"
```


## <a name="next-steps"></a>Następne kroki
W tym artykule wprowadzającym utworzyliśmy podstawowy zestaw skalowania i użyliśmy niestandardowego rozszerzenia skryptu w celu zainstalowania podstawowego serwera internetowego usług IIS na wystąpieniach maszyn wirtualnych. W celu osiągnięcia większej skalowalności i automatyzacji rozszerz swój zestaw skalowania, korzystając z następujących artykułów z instrukcjami:

- [Wdrażanie aplikacji na zestawach skalowania maszyn wirtualnych](virtual-machine-scale-sets-deploy-app.md)
- Skalowanie automatyczne za pomocą [programu Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md), [interfejsu wiersza polecenia platformy Azure](virtual-machine-scale-sets-autoscale-cli.md) lub [witryny Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Używanie automatycznych uaktualnień systemu operacyjnego dla wystąpień maszyn wirtualnych zestawu skalowania](virtual-machine-scale-sets-automatic-upgrade.md)
