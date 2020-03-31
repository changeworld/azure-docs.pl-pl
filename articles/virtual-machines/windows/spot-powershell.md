---
title: Wdrażanie maszyn wirtualnych platformy Azure w punktach za pomocą programu PowerShell
description: Dowiedz się, jak używać programu Azure PowerShell do wdrażania maszyn wirtualnych w miejscu, aby zaoszczędzić na kosztach.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 17186d1d7b50ea872dc47eca8c2c4491787d2a38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77158948"
---
# <a name="preview-deploy-spot-vms-using-azure-powershell"></a>Wersja zapoznawcza: wdrażanie maszyn wirtualnych punktowych przy użyciu programu Azure PowerShell


Korzystanie z [maszyn wirtualnych spot](spot-vms.md) pozwala na wykorzystanie naszej niewykorzystanej pojemności przy znacznych oszczędnościach kosztów. W dowolnym momencie, gdy platforma Azure potrzebuje pojemności z powrotem, infrastruktura platformy Azure będzie eksmitować maszyny wirtualne spot. W związku z tym maszyny wirtualne spot są idealne dla obciążeń, które mogą obsługiwać przerwy, takie jak zadania przetwarzania wsadowego, środowiska deweloperów/testów, duże obciążenia obliczeniowe i inne.

Ceny maszyn wirtualnych punktowych są zmienne na podstawie regionu i jednostki SKU. Aby uzyskać więcej informacji, zobacz Ceny maszyn wirtualnych dla [systemów Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) i [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Aby uzyskać więcej informacji na temat ustawiania ceny maksymalnej, zobacz [Maszyny wirtualne spot - Cennik](spot-vms.md#pricing).

Masz możliwość, aby ustawić maksymalną cenę jesteś gotów zapłacić, za godzinę, dla maszyny Wirtualnej. Maksymalna cena maszyny Wirtualnej spot można ustawić w dolarach amerykańskich (USD), przy użyciu miejsc po przecinku do 5. Na przykład wartość `0.98765`będzie maksymalna cena $0.98765 USD za godzinę. Jeśli ustawisz maksymalną `-1`cenę, maszyna wirtualna nie zostanie eksmitowana na podstawie ceny. Cena za maszynę wirtualną będzie bieżącą ceną spot lub ceną standardowej maszyny Wirtualnej, która kiedykolwiek jest mniejsza, o ile dostępna jest pojemność i przydział.

> [!IMPORTANT]
> Wystąpienia spot są obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest zalecana dla obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>



## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Utwórz spotVM przy użyciu [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) do utworzenia konfiguracji. Uwzględnij `-Priority Spot` `-MaxPrice` i ustaw na:
- `-1`więc maszyna wirtualna nie jest eksmitowany na podstawie ceny.
- do 5 cyfr. Na przykład `-MaxPrice .98765` oznacza, że maszyna wirtualna zostanie cofnięta alokacja, gdy cena spotVM idzie około $.98765 za godzinę.


W tym przykładzie tworzy spotVM, który nie zostanie cofnięty na podstawie cen (tylko wtedy, gdy platforma Azure potrzebuje pojemność z powrotem).

```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be a Spot VM

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1| `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Po utworzeniu maszyny Wirtualnej można zbadać, aby wyświetlić maksymalną cenę dla wszystkich maszyn wirtualnych w grupie zasobów.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="next-steps"></a>Następne kroki

Maszynę wirtualną punktu można również utworzyć przy użyciu [interfejsu wiersza polecenia platformy Azure](../linux/spot-cli.md) lub [szablonu](../linux/spot-template.md).

Jeśli wystąpi błąd, zobacz [Kody błędów](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).