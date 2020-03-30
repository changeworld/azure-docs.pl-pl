---
title: Maszyna wirtualna z wieloma adresami IP przy użyciu interfejsu wiersza polecenia platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak przypisać wiele adresów IP do maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure (CLI).
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
ms.openlocfilehash: 144f30463adb3dfbce1717e06548baccc8286f8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240238"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli"></a>Przypisywanie wielu adresów IP do maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

W tym artykule wyjaśniono, jak utworzyć maszynę wirtualną (VM) za pośrednictwem modelu wdrażania usługi Azure Resource Manager przy użyciu interfejsu wiersza polecenia platformy Azure. Wielu adresów IP nie można przypisać do zasobów utworzonych za pośrednictwem klasycznego modelu wdrażania. Aby dowiedzieć się więcej o modelach wdrażania platformy Azure, przeczytaj artykuł [Zrozumieć modele wdrażania.](../resource-manager-deployment-model.md)

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Tworzenie maszyny wirtualnej z wieloma adresami IP

Kroki, które należy wykonać wyjaśnić, jak utworzyć przykładową maszynę wirtualną z wieloma adresami IP, zgodnie z opisem w scenariuszu. Zmień wartości zmiennych w typach adresów "" i IP, zgodnie z wymaganiami, dla implementacji. 

1. Zainstaluj [interfejsu wiersza polecenia platformy Azure,](/cli/azure/install-azure-cli) jeśli nie masz jeszcze zainstalowanego.
2. Utwórz parę kluczy publicznych i prywatnych SSH dla maszyn wirtualnych z systemem Linux, wykonując kroki w [pary Tworzenie klucza publicznego i prywatnego SSH dla maszyn wirtualnych z systemem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Z powłoki poleceń zaloguj `az login` się za pomocą polecenia i wybierz subskrypcję, której używasz.
4. Utwórz maszynę wirtualną, wykonując skrypt, który następuje na komputerze z systemem Linux lub Mac. Skrypt tworzy grupę zasobów, jedną sieć wirtualną (VNet), jedną kartę sieciową z trzema konfiguracjami IP i maszynę wirtualną z dołączonymi do niej dwiema kartami sieciowymi. Wszystkie zasoby karty sieciowej, publicznego adresu IP, sieci wirtualnej i maszyny wirtualnej muszą istnieć w tej samej lokalizacji i subskrypcji. Chociaż zasoby nie wszystkie muszą istnieć w tej samej grupie zasobów, w poniższym skrypcie robią.

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

Oprócz tworzenia maszyny Wirtualnej z kartą sieciową z 3 konfiguracjami IP, skrypt tworzy:

- Domyślnie jeden dysk zarządzany w wersji premium, ale dostępne są inne opcje typu dysku, który można utworzyć. Przeczytaj [artykuł Tworzenie maszyny Wirtualnej systemu Linux przy użyciu interfejsu wiersza polecenia platformy Azure, aby](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) uzyskać szczegółowe informacje.
- Sieć wirtualna z jedną podsiecią i dwoma publicznymi adresami IP. Alternatywnie można użyć istniejących zasobów sieci *wirtualnej,* podsieci, karty sieciowej lub publicznego adresu IP. Aby dowiedzieć się, jak używać istniejących zasobów `az vm create -h`sieciowych zamiast tworzyć dodatkowe zasoby, wprowadź .

Publiczne adresy IP mają symboliczną opłatę. Aby dowiedzieć się więcej o cenach adresów IP, przeczytaj stronę [cennika adresu IP.](https://azure.microsoft.com/pricing/details/ip-addresses) Istnieje ograniczenie liczby publicznych adresów IP, które mogą być używane w subskrypcji. Aby uzyskać więcej informacji o limitach, przeczytaj artykuł dotyczący [limitów platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

Po utworzeniu maszyny Wirtualnej `az network nic show --name MyNic1 --resource-group myResourceGroup` wprowadź polecenie, aby wyświetlić konfigurację karty sieciowej. Wprowadź, `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` aby wyświetlić listę konfiguracji IP skojarzonych z kartą sieciową.

Dodaj prywatne adresy IP do systemu operacyjnego maszyny Wirtualnej, wykonując kroki dotyczące systemu operacyjnego w sekcji [Dodaj adresy IP do systemu operacyjnego maszyny Wirtualnej](#os-config) w tym artykule.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>Dodawanie adresów IP do maszyny Wirtualnej

Można dodać dodatkowe prywatne i publiczne adresy IP do istniejącego interfejsu sieciowego platformy Azure, wykonując następujące kroki. Przykłady opierają się na [scenariuszu](#scenario) opisanym w tym artykule.

1. Otwórz powłokę poleceń i wykonaj pozostałe kroki w tej sekcji w ramach jednej sesji. Jeśli nie masz jeszcze zainstalowanego i skonfigurowany interfejs wiersza polecenia platformy Azure, wykonaj kroki `az-login` w artykule [instalacji interfejsu wiersza polecenia platformy Azure](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) i zaloguj się do konta platformy Azure za pomocą polecenia.

2. Wykonaj czynności opisane w jednej z poniższych sekcji w oparciu o wymagania:

    **Dodawanie prywatnego adresu IP**
    
    Aby dodać prywatny adres IP do karty sieciowej, należy utworzyć konfigurację IP za pomocą następującego polecenia. Statyczny adres IP musi być nieużytym adresem podsieci.

    ```azurecli
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    Utwórz dowolną liczbę konfiguracji, używając unikatowych nazw konfiguracji i prywatnych adresów IP (w przypadku konfiguracji ze statycznymi adresami IP).

    **Dodawanie publicznego adresu IP**
    
    Publiczny adres IP jest dodawany przez skojarzenie go z nową konfiguracją IP lub istniejącą konfiguracją IP. Wykonaj kroki w jednej z sekcji, które należy wykonać, zgodnie z wymaganiami.

    Publiczne adresy IP mają symboliczną opłatę. Aby dowiedzieć się więcej o cenach adresów IP, przeczytaj stronę [cennika adresu IP.](https://azure.microsoft.com/pricing/details/ip-addresses) Istnieje ograniczenie liczby publicznych adresów IP, które mogą być używane w subskrypcji. Aby uzyskać więcej informacji o limitach, przeczytaj artykuł dotyczący [limitów platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

    - **Kojarzenie zasobu z nową konfiguracją ip**
    
        Za każdym razem, gdy dodajesz publiczny adres IP w nowej konfiguracji IP, należy również dodać prywatny adres IP, ponieważ wszystkie konfiguracje IP muszą mieć prywatny adres IP. Można dodać istniejący zasób publicznego adresu IP lub utworzyć nowy. Aby utworzyć nową, wprowadź następujące polecenie:
    
        ```azurecli
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        Aby utworzyć nową konfigurację IP ze statycznym prywatnym adresem IP i skojarzonym z nim publicznym zasóbem adresu IP *myPublicIP3,* wprowadź następujące polecenie:

        ```azurecli
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **Kojarzenie zasobu z istniejącą konfiguracją ip** Publiczny zasób adresu IP może być skojarzony tylko z konfiguracją IP, która nie ma jeszcze skojarzonego. Można określić, czy konfiguracja IP ma skojarzony publiczny adres IP, wprowadzając następujące polecenie:

        ```azurecli
        az network nic ip-config list \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
        ```

        Zwrócone wyjście:
    
            Name        PublicIpAddressId
            
            ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
            IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
            IPConfig-3

        Ponieważ **PublicIpAddressId** kolumna dla *IpConfig-3* jest puste w danych wyjściowych, nie publiczny adres IP zasób jest obecnie skojarzony z nim. Do protokołu IpConfig-3 można dodać istniejący publiczny zasób adresu IP lub wprowadzić następujące polecenie, aby je utworzyć:

        ```azurecli
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        Wprowadź następujące polecenie, aby skojarzyć publiczny zasób adresu IP z istniejącą konfiguracją IP o nazwie *IPConfig-3*:
    
        ```azurecli
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. Wyświetl prywatne adresy IP i identyfikatory zasobów publicznego adresu IP przypisane do karty sieciowej, wprowadzając następujące polecenie:

    ```azurecli
    az network nic ip-config list \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    Zwrócone wyjście: <br>
    
        Name        PrivateIpAddress    PrivateIpAllocationMethod   PublicIpAddressId
        
        ipconfig1   10.0.0.4            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
        IPConfig-2  10.0.0.5            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
        IPConfig-3  10.0.0.6            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    

4. Dodaj prywatne adresy IP dodane do karty sieciowej do systemu operacyjnego maszyny Wirtualnej, postępując zgodnie z instrukcjami w sekcji [Dodaj adresy IP do systemu operacyjnego maszyny Wirtualnej](#os-config) w tym artykule. Nie należy dodawać publicznych adresów IP do systemu operacyjnego.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
