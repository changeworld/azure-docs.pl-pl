---
title: Adresy maszynę Wirtualną za pomocą wielu IP przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można przypisać wiele adresów IP do maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure (CLI).
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: jimdial
ms.openlocfilehash: c11883156f53ab53ebe6f84d66232f81f8cf31ff
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli"></a>Przypisz wielu adresów IP do maszyn wirtualnych przy użyciu wiersza polecenia platformy Azure

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

W tym artykule opisano sposób tworzenia maszyny wirtualnej (VM) za pośrednictwem modelu wdrażania usługi Azure Resource Manager przy użyciu wiersza polecenia platformy Azure. Nie można przypisać wiele adresów IP do zasobów została utworzona za pośrednictwem klasycznego modelu wdrażania. Aby dowiedzieć się więcej na temat modeli wdrażania platformy Azure, przeczytaj [zrozumieć modele wdrażania](../resource-manager-deployment-model.md) artykułu.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Utwórz maszynę Wirtualną z wielu adresów IP

Czynności, które wykonują wyjaśniono, jak można utworzyć maszyny wirtualnej w przykładzie z wielu adresów IP, zgodnie z opisem w scenariuszu. Zmienianie wartości zmiennej "" i typy adresów IP, zgodnie z wymaganiami, implementacji. 

1. Zainstaluj [Azure CLI 2.0](/cli/azure/install-az-cli2) Jeśli nie masz już zainstalowany.
2. Tworzenie SSH publiczne i prywatne parę kluczy dla maszyn wirtualnych systemu Linux, wykonując kroki opisane w [tworzenie SSH publiczne i prywatne parę kluczy dla maszyn wirtualnych systemu Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Z powłoki poleceń, zaloguj się za pomocą polecenia `az login` i wybierz subskrypcję używasz.
4. Wykonywanie skryptu, znajdujący się na komputerze z systemem Linux lub Mac, aby utworzyć maszynę Wirtualną. Skrypt tworzy grupę zasobów, jedną sieć wirtualną (VNet), jedną kartą Sieciową o trzy konfiguracje adresów IP i maszyny Wirtualnej z dwie karty sieciowe podłączone do niego. Karta sieciowa, publiczny adres IP, wirtualnych sieci i zasobów maszyny Wirtualnej musi istnieć w tej samej subskrypcji i lokalizacji. Jeśli zasoby nie ma występować w tej samej grupie zasobów, w poniższym skrypcie robią.

```bash
    
#!/bin/sh
    
RgName="myResourceGroup"
Location="westcentralus"
az group create --name $RgName --location $Location
    
# Create a public IP address resource with a static IP address using the `--allocation-method Static` option. If you
# do not specify this option, the address is allocated dynamically. The address is assigned to the resource from a pool
# of IP adresses unique to each Azure region. Download and view the file from
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

azure network nic ip-config create \
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
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes rticle. The script fails if the VM size
# is not supported in the location you select. Run the `azure vm sizes --location estcentralus` command to get a full list
# of VMs in US West Central, for example.

VmSize="Standard_DS1"

# Replace the value for the OsImage variable value with a value for *urn* from the utput returned by entering the
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

Oprócz tworzenia maszyny Wirtualnej z kartą Sieciową z 3 konfiguracje adresów IP, tworzy skrypt:

- Pojedynczy premium zarządzać dysku domyślnie, ale ma inne opcje typu dysku, które można utworzyć. Odczyt [Utwórz Maszynę wirtualną systemu Linux przy użyciu programu Azure CLI 2.0](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artykułu, aby uzyskać szczegółowe informacje.
- Sieć wirtualną z jedną podsiecią i dwa publiczne adresy IP. Alternatywnie można użyć *istniejących* sieci wirtualnej, podsieci, karta sieciowa lub zasobów publicznych adresów IP. Aby dowiedzieć się, jak korzystać z istniejących zasobów sieciowych, a nie tworzenia dodatkowych zasobów, wprowadź `az vm create -h`.

Publiczne adresy IP ma nominalnego opłatą. Aby dowiedzieć się więcej o cenach adresu IP, przeczytaj [cennik adres IP](https://azure.microsoft.com/pricing/details/ip-addresses) strony. Istnieje ograniczona liczba publicznych adresów IP, których można użyć w ramach subskrypcji. Aby uzyskać więcej informacji o limitach, przeczytaj artykuł dotyczący [limitów platformy Azure](../azure-subscription-service-limits.md#networking-limits).

Po utworzeniu maszyny Wirtualnej, wprowadź `az network nic show --name MyNic1 --resource-group myResourceGroup` polecenie, aby wyświetlić konfigurację karty Sieciowej. Wprowadź `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` Aby wyświetlić listę konfiguracji adresów IP powiązanych z karty sieciowej.

Dodaj prywatnych adresów IP do systemu operacyjnego maszyny Wirtualnej, wykonując kroki odpowiednie dla systemu operacyjnego w [adresów IP Dodaj do systemu operacyjnego maszyny Wirtualnej](#os-config) sekcji tego artykułu.

## <a name="add"></a>Dodaj adresy IP do maszyny Wirtualnej

Dodatkowe prywatne i publiczne adresy IP można dodać do istniejącego interfejsu sieci platformy Azure, wykonując kroki, które należy wykonać. Przykłady opracowano na [scenariusza](#Scenario) opisane w tym artykule.

1. Otwórz powłokę poleceń i wykonaj pozostałe kroki w tej sekcji w ramach jednej sesji. Jeśli nie masz jeszcze interfejsu wiersza polecenia Azure zainstalowany i skonfigurowany, wykonaj kroki [instalacji Azure CLI 2.0](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) artykułu i logowania do platformy Azure konta `az-login` polecenia.

2. Wykonaj kroki jednego z następujących sekcji, w zależności od wymagań:

    **Dodaj prywatnego adresu IP**
    
    Aby dodać prywatnego adresu IP do karty Sieciowej, należy utworzyć konfigurację protokołu IP za pomocą polecenia poniżej. Statyczny adres IP musi być nieużywany adres podsieci.

    ```bash
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    Tworzenie konfiguracji tyle, ile potrzebujesz, przy użyciu nazwy konfiguracji unikatowy i prywatnych adresów IP (w przypadku konfiguracji statycznych adresów IP).

    **Dodaj publiczny adres IP**
    
    Publiczny adres IP jest dodawany przez kojarzenie go z nową konfigurację adresu IP lub istniejącej konfiguracji adresu IP. Wykonaj kroki opisane w sekcji, które należy wykonać, ile potrzebujesz.

    Publiczne adresy IP ma nominalnego opłatą. Aby dowiedzieć się więcej o cenach adresu IP, przeczytaj [cennik adres IP](https://azure.microsoft.com/pricing/details/ip-addresses) strony. Istnieje ograniczona liczba publicznych adresów IP, których można użyć w ramach subskrypcji. Aby uzyskać więcej informacji o limitach, przeczytaj artykuł dotyczący [limitów platformy Azure](../azure-subscription-service-limits.md#networking-limits).

    - **Skojarz zasób nowej konfiguracji adresu IP**
    
        Po dodaniu publicznego adresu IP w nowej konfiguracji adresu IP, musisz również dodać prywatnego adresu IP, ponieważ wszystkie konfiguracje adresów IP muszą mieć prywatnego adresu IP. Możesz dodać istniejący zasób publiczny adres IP lub Utwórz nową. Aby utworzyć nową, wprowadź następujące polecenie:
    
        ```bash
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        Aby utworzyć nową konfigurację adresu IP za pomocą statycznego prywatnego adresu IP oraz skojarzonych z nimi *myPublicIP3* publicznego adresu IP adresów zasobów, wprowadź następujące polecenie:

        ```bash
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **Skojarz zasób do istniejącej konfiguracji IP** zasób publicznego adresu IP może być skojarzony tylko konfiguracją protokołu IP, który nie ma jeszcze skojarzone. Można określić, czy konfiguracja IP ma skojarzone publicznego adresu IP, wprowadzając następujące polecenie:

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

        Ponieważ **PublicIpAddressId** kolumny dla *IpConfig 3* jest puste w danych wyjściowych, żaden z zasobów publiczny adres IP jest obecnie z nią skojarzona. Dodaj istniejący zasób publicznego adresu IP do IpConfig 3 lub wprowadź następujące polecenie, aby go utworzyć:

        ```bash
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        Wprowadź następujące polecenie, aby skojarzyć zasób publicznego adresu IP do istniejącej konfiguracji IP o nazwie *IPConfig 3*:
    
        ```bash
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. Wyświetl prywatnych adresów IP i zasobu publicznego adresu IP identyfikatory przypisane do karty Sieciowej, wprowadzając następujące polecenie:

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
    

4. Dodaj prywatnych adresów IP dodany do karty Sieciowej do maszyny Wirtualnej systemu operacyjnego, zgodnie z instrukcjami w [adresów IP Dodaj do systemu operacyjnego maszyny Wirtualnej](#os-config) sekcji tego artykułu. Nie dodawaj publiczne adresy IP do systemu operacyjnego.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
