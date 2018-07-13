---
title: Tworzenie maszyny Wirtualnej za pomocą statyczny publiczny adres IP — interfejs wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć maszyny Wirtualnej ze statycznym publicznym adresem IP przy użyciu interfejsu wiersza polecenia (CLI) platformy Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bd44971162a79e53b731c5c89316f14e8bb0a1a6
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38651963"
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-cli"></a>Tworzenie maszyny Wirtualnej ze statycznym publicznym adresem IP przy użyciu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [Program PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell (klasyczny)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [usługi Resource Manager i Model Klasyczny](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). W tym artykule opisano, przy użyciu modelu wdrażania usługi Resource Manager zalecanego przez firmę Microsoft w przypadku większości nowych wdrożeń zamiast klasycznego modelu wdrażania.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name = "create"></a>Tworzenie maszyny Wirtualnej

Wartości "" dla zmiennych w opisanych poniżej tworzenie zasobów za pomocą ustawień z tego scenariusza. Zmień wartości, zgodnie z potrzebami w danym środowisku.

1. Zainstaluj [interfejsu wiersza polecenia platformy Azure w wersji 2.0](/cli/azure/install-az-cli2) Jeśli już nie jest ona zainstalowana.
2. Tworzenie publicznego i prywatnego pary kluczy SSH dla maszyn wirtualnych systemu Linux, wykonując kroki opisane w [tworzenie prywatnych i publicznych pary kluczy SSH dla maszyn wirtualnych systemu Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Z powłoki poleceń, zaloguj się przy użyciu polecenia `az login`.
4. Wykonywanie skryptu, który następuje na komputerze z systemem Linux lub Mac, aby utworzyć maszynę Wirtualną. Azure publiczny adres IP, sieci wirtualnej, interfejs sieciowy i zasoby maszyny Wirtualnej musi istnieć w tej samej lokalizacji. Chociaż zasoby nie muszą istnieć w tej samej grupie zasobów, w poniższym skrypcie im.

```bash
RgName="IaaSStory"
Location="westus"

# Create a resource group.

az group create \
--name $RgName \
--location $Location

# Create a public IP address resource with a static IP address using the --allocation-method Static option.
# If you do not specify this option, the address is allocated dynamically. The address is assigned to the
# resource from a pool of IP adresses unique to each Azure region. The DnsName must be unique within the
# Azure location it's created in. Download and view the file from https://www.microsoft.com/en-us/download/details.aspx?id=41653#
# that lists the ranges for each region.

PipName="PIPWEB1"
DnsName="iaasstoryws1"
az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--allocation-method Static \
--dns-name $DnsName

# Create a virtual network with one subnet

VnetName="TestVNet"
VnetPrefix="192.168.0.0/16"
SubnetName="FrontEnd"
SubnetPrefix="192.168.1.0/24"
az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $SubnetName \
--subnet-prefix $SubnetPrefix

# Create a network interface connected to the VNet with a static private IP address and associate the public IP address
# resource to the NIC.

NicName="NICWEB1"
PrivateIpAddress="192.168.1.101"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $SubnetName \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress \
--public-ip-address $PipName

# Create a new VM with the NIC

VmName="WEB1"

# Replace the value for the VmSize variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article.
VmSize="Standard_DS1"

# Replace the value for the OsImage variable with a value for *urn* from the output returned by entering
# the `az vm image list` command. 

OsImage="credativ:Debian:8:latest"
Username='adminuser'

# Replace the following value with the path to your public key file.
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
# If creating a Windows VM, remove the previous line and you'll be prompted for the password you want to configure for the VM.
```

Oprócz tworzenia maszyny Wirtualnej, skrypt tworzy:
- Premium jednego dysku zarządzanego domyślnie, ale ma inne opcje typ dysku, które można utworzyć. Odczyt [Utwórz Maszynę wirtualną systemu Linux przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artykuł, aby uzyskać szczegółowe informacje.
- Sieć wirtualna, podsieci, kart Sieciowych i zasobów publicznych adresów IP. Alternatywnie, można użyć *istniejących* sieci wirtualnej, podsieci, karta sieciowa lub zasobów publicznych adresów IP. Aby dowiedzieć się, jak korzystać z istniejącymi zasobami sieciowymi, a nie tworzenia dodatkowych zasobów, wprowadź `az vm create -h`.

## <a name = "validate"></a>Sprawdź poprawność tworzenia maszyny Wirtualnej i publicznego adresu IP

1. Wprowadź polecenie `az resource list --resouce-group IaaSStory --output table` umożliwia wyświetlenie listy zasobów utworzonych przez skrypt. Powinna istnieć pięć zasobów dane wyjściowe: sieci interfejsu, dysku, publiczny adres IP, sieć wirtualna i maszyny wirtualnej.
2. Wprowadź polecenie `az network public-ip show --name PIPWEB1 --resource-group IaaSStory --output table`. W dane wyjściowe, zanotuj wartość ustawienia **IpAddress** i że wartość **wartości elementu PublicIpAllocationMethod** jest *statyczne*.
3. Przed wykonaniem następującego polecenia, Usuń <>, Zastąp *Username* z nazwa używana w przypadku **Username** skryptu i Zastąp zmienną *ipAddress*z **ipAddress** z poprzedniego kroku. Uruchom następujące polecenie, aby nawiązać połączenie z maszyną Wirtualną: `ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>`. 

## <a name= "clean-up"></a>Usuwanie maszyny Wirtualnej i skojarzonych zasobów

Zalecane jest, aby usunąć zasoby utworzone w tym ćwiczeniu, jeśli nie używasz ich w środowisku produkcyjnym. Maszynę Wirtualną, publiczny adres IP i zasoby dysku naliczenia opłat, tak długo, jak one aprowizowania. Aby usunąć zasoby utworzone podczas tego ćwiczenia, wykonaj następujące czynności:

1. Aby wyświetlić zasoby w grupie zasobów, należy uruchomić `az resource list --resource-group IaaSStory` polecenia.
2. Upewnij się, że nie ma żadnych zasobów w grupie zasobów innej niż zasoby utworzone przez skrypt w tym artykule. 
3. Aby usunąć wszystkie zasoby utworzone w tym ćwiczeniu, uruchom `az group delete -n IaaSStory` polecenia. Polecenie usuwa grupę zasobów i wszystkie zasoby, które zawiera.
 
## <a name="set-ip-addresses-within-the-operating-system"></a>Ustawić adresy IP w ramach systemu operacyjnego

Należy nigdy ręcznie przypisać publiczny adres IP przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej. Zalecane jest, że nie zostanie statycznie przypisany prywatny adres IP, przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej, o ile to konieczne, takie jak czas [przypisywanie wielu adresów IP do maszyny Wirtualnej z systemem Windows](virtual-network-multiple-ip-addresses-cli.md). Jeśli ręcznie ustawić jako prywatny adres IP w ramach systemu operacyjnego, upewnij się, że jej jako ten sam adres prywatny adres IP przypisany do platformy Azure [interfejsu sieciowego](virtual-network-network-interface-addresses.md#change-ip-address-settings), lub można utracić łączność z maszyną wirtualną. Dowiedz się więcej o [prywatny adres IP](virtual-network-network-interface-addresses.md#private) ustawienia.

## <a name="next-steps"></a>Kolejne kroki

Dowolny ruch sieciowy może przepływać do i z maszyn wirtualnych utworzonych w tym artykule. Można zdefiniować reguły zabezpieczeń ruchu przychodzącego i wychodzącego w ramach grupy zabezpieczeń sieci, które ograniczają ruch może przepływać do i z interfejsu sieciowego i/lub podsieci. Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, zobacz [omówienie grupy zabezpieczeń sieci](security-overview.md).