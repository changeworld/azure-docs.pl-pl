---
title: Konfigurowanie prywatnych adresów IP dla maszyn wirtualnych — narzędzie interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak skonfigurować prywatne adresy IP dla maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure (CLI).
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: kumud
ms.openlocfilehash: f4643aae0b28861f4ddb99d8dace749e62f930b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199482"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli"></a>Konfigurowanie prywatnych adresów IP dla maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> Następujące przykładowe polecenia interfejsu wiersza polecenia platformy Azure oczekują istniejącego prostego środowiska. Jeśli chcesz uruchomić polecenia, ponieważ są one wyświetlane w tym dokumencie, najpierw skompiluj środowisko testowe opisane w [create a vnet](quick-create-cli.md).

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Określanie statycznego prywatnego adresu IP podczas tworzenia maszyny Wirtualnej

Aby utworzyć maszynę wirtualną o nazwie *DNS01* w podsieci *FrontEnd* sieci wirtualnej o nazwie *TestVNet* o statycznym prywatnym adresie IP *192.168.1.101,* wykonaj następujące kroki:

1. Jeśli jeszcze tego nie zrobiłeś, zainstaluj i skonfiguruj najnowszą [platformę Azure CLI](/cli/azure/install-azure-cli) i zaloguj się do konta platformy Azure przy użyciu [logowania az.](/cli/azure/reference-index)

2. Utwórz publiczny adres IP dla maszyny Wirtualnej za pomocą polecenia [tworzenia sieci az public-ip.](/cli/azure/network/public-ip) Lista wyświetlana po danych wyjściowych zawiera opis używanych parametrów.

    > [!NOTE]
    > W tym i kolejnych krokach, w zależności od środowiska, można lub trzeba użyć różnych wartości dla argumentów.

    ```azurecli
    az network public-ip create \
    --name TestPIP \
    --resource-group TestRG \
    --location centralus \
    --allocation-method Static
    ```

    Oczekiwane dane wyjściowe:

   ```json
   {
        "publicIp": {
            "idleTimeoutInMinutes": 4,
            "ipAddress": "52.176.43.167",
            "provisioningState": "Succeeded",
            "publicIPAllocationMethod": "Static",
            "resourceGuid": "79e8baa3-33ce-466a-846c-37af3c721ce1"
        }
    }
    ```

   * `--resource-group`: Nazwa grupy zasobów, w której ma być utworzony publiczny adres IP.
   * `--name`: Nazwa publicznego adresu IP.
   * `--location`: Region platformy Azure, w którym można utworzyć publiczny adres IP.

3. Uruchom polecenie [tworzenia sieci AZ,](/cli/azure/network/nic) aby utworzyć kartę sieciową ze statycznym prywatnym adresem IP. Lista wyświetlana po danych wyjściowych zawiera opis używanych parametrów. 
   
    ```azurecli
    az network nic create \
    --resource-group TestRG \
    --name TestNIC \
    --location centralus \
    --subnet FrontEnd \
    --private-ip-address 192.168.1.101 \
    --vnet-name TestVNet
    ```

    Oczekiwane dane wyjściowe:
   
    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.101",
                "privateIPAllocationMethod": "Static",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>"
        }
    }
    ```
    
    Parametry:

    * `--private-ip-address`: Statyczny prywatny adres IP karty sieciowej.
    * `--vnet-name`: Nazwa sieci wirtualnej, w której ma być tworzęna kartę sieciową.
    * `--subnet`: Nazwa podsieci, w której ma być utworzona karta sieciowa.

4. Uruchom polecenie [azure vm create,](/cli/azure/vm/nic) aby utworzyć maszynę wirtualną przy użyciu publicznego adresu IP i karty sieciowej utworzonej wcześniej. Lista wyświetlana po danych wyjściowych zawiera opis używanych parametrów.
   
    ```azurecli
    az vm create \
    --resource-group TestRG \
    --name DNS01 \
    --location centralus \
    --image Debian \
    --admin-username adminuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics TestNIC
    ```

    Oczekiwane dane wyjściowe:
   
    ```json
    {
        "fqdns": "",
        "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01",
        "location": "centralus",
        "macAddress": "00-0D-3A-92-C1-66",
        "powerState": "VM running",
        "privateIpAddress": "192.168.1.101",
        "publicIpAddress": "",
        "resourceGroup": "TestRG"
    }
    ```
   
   Parametry inne niż podstawowe [parametry az vm.](/cli/azure/vm)

   * `--nics`: Nazwa karty sieciowej, do której jest dołączona maszyna wirtualna.
   
Zaleca się, aby nie przypisywać statycznie prywatnego adresu IP przypisanego do maszyny wirtualnej platformy Azure w systemie operacyjnym maszyny wirtualnej, chyba że jest to konieczne, na przykład podczas [przypisywania wielu adresów IP do maszyny wirtualnej systemu Windows](virtual-network-multiple-ip-addresses-cli.md). Jeśli ręcznie ustawisz prywatny adres IP w systemie operacyjnym, upewnij się, że jest to ten sam adres, co prywatny adres IP przypisany do [interfejsu sieciowego](virtual-network-network-interface-addresses.md#change-ip-address-settings)platformy Azure lub możesz utracić łączność z maszyną wirtualną. Dowiedz się więcej o [ustawieniach prywatnych adresów IP.](virtual-network-network-interface-addresses.md#private)

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Pobieranie statycznych prywatnych informacji o adresie IP maszyny Wirtualnej

Uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure, aby przestrzegać wartości *dla prywatnej metody alloc IP* i *prywatnego adresu IP:*

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

Oczekiwane dane wyjściowe:

```json
"192.168.1.101"
```

Aby wyświetlić określone informacje IP karty sieciowej dla tej maszyny Wirtualnej, należy w szczególności zbadać kartę sieciową:

```azurecli
az network nic show \
-g testrg \
-n testnic \
--query 'ipConfigurations[0].{PrivateAddress:privateIpAddress,IPVer:privateIpAddressVersion,IpAllocMethod:p
rivateIpAllocationMethod,PublicAddress:publicIpAddress}'
```

Dane wyjściowe wyglądają mniej więcej tak:

```json
{
    "IPVer": "IPv4",
    "IpAllocMethod": "Static",
    "PrivateAddress": "192.168.1.101",
    "PublicAddress": null
}
```

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Usuwanie statycznego prywatnego adresu IP z maszyny Wirtualnej

Nie można usunąć statycznego prywatnego adresu IP z karty sieciowej w usłudze Azure CLI dla wdrożeń usługi Azure Resource Manager. Musisz:
- Tworzenie nowej karty sieciowej, która korzysta z dynamicznego adresu IP
- Ustaw kartę sieciową na maszynie wirtualnej, wykonaj nowo utworzoną kartę sieciową. 

Aby zmienić kartę sieciową dla maszyny Wirtualnej używaną w poprzednich poleceniach, wykonaj następujące kroki:

1. Uruchom polecenie **azure network nic create,** aby utworzyć nową kartę sieciową przy użyciu dynamicznej alokacji adresów IP z nowym adresem IP. Ponieważ nie określono adresu IP, metodą alokacji jest **Dynamic**.

    ```azurecli
    az network nic create     \
    --resource-group TestRG     \
    --name TestNIC2     \
    --location centralus     \
    --subnet FrontEnd    \
    --vnet-name TestVNet
    ```        
   
    Oczekiwane dane wyjściowe:

    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.4",
                "privateIPAllocationMethod": "Dynamic",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "0808a61c-476f-4d08-98ee-0fa83671b010"
        }
    }
    ```

2. Uruchom polecenie **zestawu maszyn wirtualnych platformy azure,** aby zmienić kartę sieciową używaną przez maszynę wirtualną.
   
    ```azurecli
   az vm nic set --resource-group TestRG --vm-name DNS01 --nics TestNIC2
    ```

    Oczekiwane dane wyjściowe:
   
    ```json
    [
        {
            "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC3",
            "primary": true,
            "resourceGroup": "TestRG"
        }
    ]
    ```

    > [!NOTE]
    > Jeśli maszyna wirtualna jest wystarczająco duża, aby mieć więcej niż jedną kartę sieciową, uruchom polecenie **usuwania nic sieci azure,** aby usunąć starą kartę sieciową.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zarządzaniu [ustawieniami adresu IP](virtual-network-network-interface-addresses.md).
