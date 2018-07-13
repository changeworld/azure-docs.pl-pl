---
title: Tworzenie maszyny Wirtualnej za pomocą statyczny publiczny adres IP — Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć maszyny Wirtualnej ze statycznym publicznym adresem IP przy użyciu programu PowerShell.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ad975ab9-d69f-45c1-9e45-0d3f0f51e87e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 68656db0b76a29e7ab36fd6fa9ad4647712233ee
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38696587"
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-powershell"></a>Tworzenie maszyny Wirtualnej ze statycznym publicznym adresem IP przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [Program PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell (klasyczny)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Azure Resource Manager i model klasyczny](../resource-manager-deployment-model.md). W tym artykule opisano, przy użyciu modelu wdrażania usługi Resource Manager zalecanego przez firmę Microsoft w przypadku większości nowych wdrożeń zamiast klasycznego modelu wdrażania.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="start-your-script"></a>Uruchom skrypt
Możesz pobrać pełną skrypt programu PowerShell, używane [tutaj](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/virtual-network-deploy-static-pip-arm-ps.ps1). Wykonaj poniższe kroki, aby zmienić skrypt zadziałał w Twoim środowisku.

Zmień wartości zmiennych poniżej na podstawie wartości, których chcesz używać dla danego wdrożenia. Następujące wartości są mapowane do scenariusza używane w tym artykule:

```powershell
# Set variables resource group
$rgName                = "IaaSStory"
$location              = "West US"

# Set variables for VNet
$vnetName              = "WTestVNet"
$vnetPrefix            = "192.168.0.0/16"
$subnetName            = "FrontEnd"
$subnetPrefix          = "192.168.1.0/24"

# Set variables for storage
$stdStorageAccountName = "iaasstorystorage"

# Set variables for VM
$vmSize                = "Standard_A1"
$diskSize              = 127
$publisher             = "MicrosoftWindowsServer"
$offer                 = "WindowsServer"
$sku                   = "2012-R2-Datacenter"
$version               = "latest"
$vmName                = "WEB1"
$osDiskName            = "osdisk"
$nicName               = "NICWEB1"
$privateIPAddress      = "192.168.1.101"
$pipName               = "PIPWEB1"
$dnsName               = "iaasstoryws1"
```

## <a name="create-the-necessary-resources-for-your-vm"></a>Tworzenie zasobów niezbędnych dla maszyny Wirtualnej
Przed utworzeniem maszyny Wirtualnej, należy grupę zasobów, sieć wirtualną, publiczny adres IP i kart Sieciowych, ma być używany przez maszynę Wirtualną.

1. Utwórz nową grupę zasobów.

    ```powershell
    New-AzureRmResourceGroup -Name $rgName -Location $location
    ```

2. Tworzenie sieci wirtualnej i podsieci.

    ```powershell
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName `
        -AddressPrefix $vnetPrefix -Location $location

    Add-AzureRmVirtualNetworkSubnetConfig -Name $subnetName `
        -VirtualNetwork $vnet -AddressPrefix $subnetPrefix

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

3. Utwórz zasób publicznego adresu IP. 

    ```powershell
    $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName `
        -AllocationMethod Static -DomainNameLabel $dnsName -Location $location
    ```

4. Utwórz interfejs sieciowy (karta sieciowa) dla maszyny Wirtualnej w podsieci, utworzoną za pomocą publicznego adresu IP. Zwróć uwagę, pierwszego polecenia cmdlet pobierania sieci wirtualnej platformy Azure, jest to konieczne, ponieważ `Set-AzureRmVirtualNetwork` zostało wykonane, aby zmienić istniejącej sieci wirtualnej.

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
        -Subnet $subnet -Location $location -PrivateIpAddress $privateIPAddress `
        -PublicIpAddress $pip
    ```

5. Utwórz konto magazynu do obsługi dysku systemu operacyjnego maszyny Wirtualnej.

    ```powershell
    $stdStorageAccount = New-AzureRmStorageAccount -Name $stdStorageAccountName `
    -ResourceGroupName $rgName -Type Standard_LRS -Location $location
    ```

## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej
Teraz, że wszystkie niezbędne zasoby znajdują się w miejscu, można utworzyć nowej maszyny Wirtualnej.

1. Utwórz obiekt konfiguracji dla maszyny Wirtualnej.

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
    ```

2. Uzyskiwanie poświadczeń konta administratora lokalnego maszyn wirtualnych.

    ```powershell
    $cred = Get-Credential -Message "Type the name and password for the local administrator account."
    ```

3. Utwórz obiekt konfiguracji maszyny Wirtualnej.

    ```powershell
    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    ```

4. Ustaw obraz systemu operacyjnego maszyny Wirtualnej.

    ```powershell
    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher `
        -Offer $offer -Skus $sku -Version $version
    ```

5. Konfigurowanie dysku systemu operacyjnego.

    ```powershell
    $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
    $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage
    ```

6. Dodaj kartę Sieciową do maszyny Wirtualnej.

    ```powershell
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id -Primary
    ```

7. Tworzenie maszyny Wirtualnej.

    ```powershell
    New-AzureRmVM -VM $vmConfig -ResourceGroupName $rgName -Location $location
    ```

8. Zapisz plik skryptu.

## <a name="run-the-script"></a>Uruchom skrypt

Po wprowadzeniu niezbędnych zmian, należy uruchomić skrypt poprzedniego. Maszyna wirtualna jest tworzona po kilku minutach.

## <a name="set-ip-addresses-within-the-operating-system"></a>Ustawić adresy IP w ramach systemu operacyjnego

Należy nigdy ręcznie przypisać publiczny adres IP przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej. Zalecane jest, że nie zostanie statycznie przypisany prywatny adres IP, przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej, o ile to konieczne, takie jak czas [przypisywanie wielu adresów IP do maszyny Wirtualnej z systemem Windows](virtual-network-multiple-ip-addresses-powershell.md). Jeśli ręcznie ustawić jako prywatny adres IP w ramach systemu operacyjnego, upewnij się, że jej jako ten sam adres prywatny adres IP przypisany do platformy Azure [interfejsu sieciowego](virtual-network-network-interface-addresses.md#change-ip-address-settings), lub można utracić łączność z maszyną wirtualną. Dowiedz się więcej o [prywatny adres IP](virtual-network-network-interface-addresses.md#private) ustawienia.

## <a name="next-steps"></a>Kolejne kroki

Dowolny ruch sieciowy może przepływać do i z maszyn wirtualnych utworzonych w tym artykule. Można zdefiniować reguły zabezpieczeń ruchu przychodzącego i wychodzącego w ramach grupy zabezpieczeń sieci, które ograniczają ruch może przepływać do i z interfejsu sieciowego i/lub podsieci. Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, zobacz [omówienie grupy zabezpieczeń sieci](security-overview.md).