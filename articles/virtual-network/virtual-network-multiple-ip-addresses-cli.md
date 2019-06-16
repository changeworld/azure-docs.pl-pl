---
title: Maszyna wirtualna z wieloma adresami IP przy użyciu wiersza polecenia platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak przypisać wiele adresów IP do maszyny wirtualnej przy użyciu interfejsu wiersza polecenia (CLI) platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: kumud
ms.openlocfilehash: 3b00bbb5903156da625b7caaca9b1a2cff212421
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64699359"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli"></a>Przypisywanie wielu adresów IP do maszyn wirtualnych przy użyciu wiersza polecenia platformy Azure

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

W tym artykule wyjaśniono, jak utworzyć maszynę wirtualną (VM) za pomocą modelu wdrażania usługi Azure Resource Manager przy użyciu wiersza polecenia platformy Azure. Wiele adresów IP nie można przypisać do zasobów utworzonych za pomocą klasycznego modelu wdrażania. Aby dowiedzieć się więcej na temat modeli wdrażania platformy Azure, przeczytaj [omówienie modeli wdrażania](../resource-manager-deployment-model.md) artykułu.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Tworzenie maszyny Wirtualnej z wieloma adresami IP

Poniższe kroki wyjaśniają, jak można utworzyć maszyny wirtualnej w przykładzie z wieloma adresami IP zgodnie z opisem w tym scenariuszu. Zmienianie wartości zmiennej "" i typy adresów IP, zgodnie z wymaganiami, implementacji. 

1. Zainstaluj [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) Jeśli już nie jest ona zainstalowana.
2. Tworzenie publicznego i prywatnego pary kluczy SSH dla maszyn wirtualnych systemu Linux, wykonując kroki opisane w [tworzenie prywatnych i publicznych pary kluczy SSH dla maszyn wirtualnych systemu Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Z powłoki poleceń, zaloguj się przy użyciu polecenia `az login` i wybierz subskrypcję, w przypadku używania.
4. Wykonywanie skryptu, który następuje na komputerze z systemem Linux lub Mac, aby utworzyć maszynę Wirtualną. Skrypt tworzy grupę zasobów, jednej sieci wirtualnej (VNet), jedną kartą Sieciową z trzech konfiguracji adresów IP i Maszynę wirtualną z dwiema kartami sieciowymi, dołączono do niego. Karta sieciowa, publiczny adres IP, sieci wirtualnej i zasobów maszyny Wirtualnej musi istnieć w tej samej lokalizacji i subskrypcji. Chociaż zasoby nie muszą istnieć w tej samej grupie zasobów, w poniższym skrypcie im.

```bash
    
#!/bin/sh
    
RgName="myResourceGroup"
Location="westcentralus"
az group create --name $RgName --location $Location
    
# Create a public IP address resource with a static IP address using the `--allocation-method Static` option. If you
# do not specify this option, the address is allocated dynamically. The address is assigned to the resource from a pool
# of IP addresses unique to each Azure region. Download and view the file from
# https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists the ranges for each region.

PipName="myPublicIP"

# This name must be unique within an Azure location.
DnsName="myDNSName"

az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--dns-name $DnsName\
--allocation-method Static

# Create a virtual network with one subnet

VnetName="myVnet"
VnetPrefix="10.0.0.0/16"
VnetSubnetName="mySubnet"
VnetSubnetPrefix="10.0.0.0/24"

az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $VnetSubnetName \
--subnet-prefix $VnetSubnetPrefix

# Create a network interface connected to the subnet and associate the public IP address to it. Azure will create the
# first IP configuration with a static private IP address and will associate the public IP address resource to it.

NicName="MyNic1"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet1Name \
--private-ip-address 10.0.0.4
--vnet-name $VnetName \
--public-ip-address $PipName
    
# Create a second public IP address, a second IP configuration, and associate it to the NIC. This configuration has a
# static public IP address and a static private IP address.

az network public-ip create \
--resource-group $RgName \
--location $Location \
--name myPublicIP2 \
--dns-name mypublicdns2 \
--allocation-method Static

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--name IPConfig-2 \
--private-ip-address 10.0.0.5 \
--public-ip-name myPublicIP2

# Create a third IP configuration, and associate it to the NIC. This configuration has  static private IP address and   # no public IP address.

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--private-ip-address 10.0.0.6 \
--name IPConfig-3

# Note: Though this article assigns all IP configurations to a single NIC, you can also assign multiple IP configurations
# to any NIC in a VM. To learn how to create a VM with multiple NICs, read the Create a VM with multiple NICs 
# article: https://docs.microsoft.com/azure/virtual-network/virtual-network-deploy-multinic-arm-cli.

# Create a VM and attach the NIC.

VmName="myVm"

# Replace the value for the following **VmSize** variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article. The script fails if the VM size
# is not supported in the location you select. Run the `azure vm sizes --location eastcentralus` command to get a full list
# of VMs in US West Central, for example.

VmSize="Standard_DS1"

# Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
# `az vm image list` command.

OsImage="credativ:Debian:8:latest"

Username="adminuser"

# Replace the following value with the path to your public key file. If you're creating a Windows VM, remove the following
# line and you'll be prompted for the password you want to configure for the VM.

SshKeyValue="~/.ssh/id_rsa.pub"

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $NicName \
--admin-username $Username \
--ssh-key-value $SshKeyValue
```

Oprócz tworzenia maszyny Wirtualnej z kartą Sieciową z 3. konfiguracje adresów IP, skrypt tworzy:

- Premium jednego dysku zarządzanego domyślnie, ale ma inne opcje typ dysku, które można utworzyć. Odczyt [Utwórz Maszynę wirtualną systemu Linux przy użyciu wiersza polecenia platformy Azure](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artykuł, aby uzyskać szczegółowe informacje.
- Sieć wirtualną z jedną podsiecią i dwa publiczne adresy IP. Alternatywnie, można użyć *istniejących* sieci wirtualnej, podsieci, karta sieciowa lub zasobów publicznych adresów IP. Aby dowiedzieć się, jak korzystać z istniejącymi zasobami sieciowymi, a nie tworzenia dodatkowych zasobów, wprowadź `az vm create -h`.

Publiczne adresy IP mają nominalnej. Aby dowiedzieć się więcej o cenach adresów IP, przeczytaj [cennik adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses) strony. Istnieje limit liczby publicznych adresów IP, które mogą być używane w ramach subskrypcji. Aby uzyskać więcej informacji o limitach, przeczytaj artykuł dotyczący [limitów platformy Azure](../azure-subscription-service-limits.md#networking-limits).

Po utworzeniu maszyny Wirtualnej, wprowadź `az network nic show --name MyNic1 --resource-group myResourceGroup` polecenie, aby wyświetlić konfigurację karty Sieciowej. Wprowadź `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` Aby wyświetlić listę konfiguracji adresów IP skojarzone z karty sieciowej.

Dodaj prywatnych adresów IP do systemu operacyjnego maszyny Wirtualnej, wykonując czynności opisane w systemie operacyjnym w [adresy IP Dodaj do systemu operacyjnego maszyny Wirtualnej](#os-config) dalszej części tego artykułu.

## <a name="add"></a>Dodaj adresy IP do maszyny Wirtualnej

Dodatkowe prywatne i publiczne adresy IP można dodać do istniejącego interfejsu sieci platformy Azure, wykonując poniższe kroki. Przykłady opracowano na [scenariusza](#scenario) opisanych w tym artykule.

1. Otwórz powłokę wiersza polecenia i wykonaj pozostałe kroki w tej sekcji w ramach jednej sesji. Jeśli nie masz jeszcze wiersza polecenia platformy Azure zainstalowany i skonfigurowany, wykonaj kroki opisane w [instalacji wiersza polecenia platformy Azure](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) artykułu i zaloguj się do subskrypcji platformy Azure, konto z `az-login` polecenia.

2. Wykonaj kroki opisane w jednej z następujących sekcji, w zależności od wymagań:

    **Dodaj prywatny adres IP**
    
    Aby dodać prywatny adres IP do karty Sieciowej, należy utworzyć konfigurację adresu IP przy użyciu poniższego polecenia. Statyczny adres IP musi być nieużywany adres podsieci.

    ```bash
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    Utwórz konfiguracje tyle, ile jest potrzebne, przy użyciu nazwy unikatowej konfiguracji i prywatnymi adresami IP (w przypadku konfiguracji za pomocą statycznych adresów IP).

    **Dodawanie publicznego adresu IP**
    
    Publiczny adres IP jest dodawany przez skojarzeniem go z nową konfigurację adresu IP lub istniejącej konfiguracji adresu IP. Wykonaj kroki opisane w sekcji, które należy wykonać, ile potrzebujesz.

    Publiczne adresy IP mają nominalnej. Aby dowiedzieć się więcej o cenach adresów IP, przeczytaj [cennik adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses) strony. Istnieje limit liczby publicznych adresów IP, które mogą być używane w ramach subskrypcji. Aby uzyskać więcej informacji o limitach, przeczytaj artykuł dotyczący [limitów platformy Azure](../azure-subscription-service-limits.md#networking-limits).

    - **Skojarz zasób nowej konfiguracji adresu IP**
    
        Zawsze, gdy publiczny adres IP zostanie dodana nowa konfiguracja adresu IP, możesz również dodać prywatny adres IP, ponieważ wszystkie konfiguracje adresów IP musi mieć prywatny adres IP. Możesz dodać istniejący zasób publicznego adresu IP lub Utwórz nową. Aby utworzyć nowe konto, wprowadź następujące polecenie:
    
        ```bash
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        Aby utworzyć nową konfigurację adresu IP za pomocą statycznego prywatnego adresu IP i skojarzonej *myPublicIP3* publiczny adres IP adresów zasobów, wpisz następujące polecenie:

        ```bash
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **Skojarz zasób do istniejącej konfiguracji adresu IP** zasób publicznego adresu IP można skojarzyć tylko do konfiguracji adresu IP, który nie ma jeszcze skojarzone. Można określić, czy konfiguracja adresu IP ma skojarzony publiczny adres IP, wprowadzając następujące polecenie:

        ```bash
        az network nic ip-config list \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
        ```

        Zwrócone dane wyjściowe:
    
            Name        PublicIpAddressId
            
            ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
            IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
            IPConfig-3

        Ponieważ **PublicIpAddressId** kolumny *IpConfig 3* jest puste w danych wyjściowych, nie zasobu publicznego adresu IP jest aktualnie powiązany do niego. Możesz dodać istniejący zasób publicznego adresu IP do IpConfig 3 lub wprowadź następujące polecenie, aby go utworzyć:

        ```bash
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        Wprowadź następujące polecenie, aby skojarzyć zasób publicznego adresu IP do istniejącej konfiguracji adresu IP o nazwie *IPConfig 3*:
    
        ```bash
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. Wyświetl prywatne adresy IP i zasób publicznego adresu IP identyfikatory przypisane do karty Sieciowej, wprowadzając następujące polecenie:

    ```bash
    az network nic ip-config list \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    Zwrócone dane wyjściowe: <br>
    
        Name        PrivateIpAddress    PrivateIpAllocationMethod   PublicIpAddressId
        
        ipconfig1   10.0.0.4            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
        IPConfig-2  10.0.0.5            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
        IPConfig-3  10.0.0.6            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    

4. Dodaj prywatnych adresów IP został dodany do karty Sieciowej w systemie operacyjnym maszyny Wirtualnej, postępując zgodnie z instrukcjami wyświetlanymi w [adresy IP Dodaj do systemu operacyjnego maszyny Wirtualnej](#os-config) dalszej części tego artykułu. Nie należy dodawać publiczne adresy IP do systemu operacyjnego.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
