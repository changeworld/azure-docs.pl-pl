---
title: Konfigurowanie prywatnych adresów IP dla maszyn wirtualnych - wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować prywatnych adresów IP maszyn wirtualnych za pomocą interfejsu wiersza polecenia platformy Azure (CLI).
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f4f6a40fde23ee70391c5057762f17ce1eb44123
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli"></a>Konfigurowanie prywatnych adresów IP dla maszyny wirtualnej przy użyciu wiersza polecenia platformy Azure

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

W tym artykule opisano model wdrażania usługi Resource Manager. Możesz również [Zarządzanie statycznego prywatnego adresu IP w klasycznym modelu wdrażania](virtual-networks-static-private-ip-classic-cli.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> Następujące przykładowe polecenia interfejsu wiersza polecenia Azure oczekują prostego istniejącego środowiska. Aby uruchomić polecenia wyświetlaną w tym dokumencie, najpierw utworzyć środowisko testowe opisane w [utworzyć sieć wirtualną](quick-create-cli.md).

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Określanie statycznego prywatnego adresu IP podczas tworzenia maszyny Wirtualnej

Aby utworzyć Maszynę wirtualną o nazwie *DNS01* w *frontonu* podsieci sieci wirtualnej o nazwie *TestVNet* z statycznego prywatnego adresu IP z *192.168.1.101*pełne następujące kroki:

1. Jeśli nie zostało jeszcze, instalowania i konfigurowania najnowszej [Azure CLI 2.0](/cli/azure/install-az-cli2) i zaloguj się do platformy Azure konta przy użyciu [logowania az](/cli/azure/reference-index#az_login). 

2. Tworzenie publicznego adresu IP dla maszyny Wirtualnej z [utworzyć az sieci publicznej ip](/cli/azure/network/public-ip#az_network_public_ip_create) polecenia. Lista wyświetlana po danych wyjściowych zawiera opis używanych parametrów.

    > [!NOTE]
    > Może być lub muszą używać różnych wartości argumentów w tej i kolejnych krokach, w zależności od środowiska.
   
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

   * `--resource-group`: Nazwa grupy zasobów, w której chcesz utworzyć publiczny adres IP.
   * `--name`: Nazwa publicznego adresu IP.
   * `--location`: Region platformy azure, w której chcesz utworzyć publiczny adres IP.

3. Uruchom [tworzenie kart interfejsu sieciowego az](/cli/azure/network/nic#az_network_nic_create) polecenie, aby utworzyć z kartą Sieciową za pomocą statycznego prywatnego adresu IP. Lista wyświetlana po danych wyjściowych zawiera opis używanych parametrów. 
   
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

    * `--private-ip-address`: Statycznego prywatnego adresu IP dla karty sieciowej.
    * `--vnet-name`: Nazwa sieci wirtualnej, w której chcesz utworzyć karty sieciowej.
    * `--subnet`: Nazwa podsieci, w której chcesz utworzyć karty sieciowej.

4. Uruchom [tworzenia maszyny wirtualnej azure](/cli/azure/vm/nic#az_vm_nic_create) polecenie, aby utworzyć maszynę Wirtualną za pomocą publicznego adresu IP i NIC utworzone wcześniej. Lista wyświetlana po danych wyjściowych zawiera opis używanych parametrów.
   
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
   
   Parametrów innych niż podstawowe [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create) parametrów.

   * `--nics`: Nazwa karty Sieciowej, do której jest dołączona maszyna wirtualna.
   
Zaleca się, że nie zostanie statycznie przypisany prywatny adres IP przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny Wirtualnej, o ile to konieczne, takie jak kiedy [przypisywanie wielu adresów IP do maszyny Wirtualnej systemu Windows](virtual-network-multiple-ip-addresses-cli.md). Jeśli ręcznie ustawić prywatnego adresu IP w ramach systemu operacyjnego, upewnij się, czy jest ten sam adres prywatny adres IP przypisany do platformy Azure [interfejsu sieciowego](virtual-network-network-interface-addresses.md#change-ip-address-settings), lub można utracić łączność z maszyną wirtualną. Dowiedz się więcej o [prywatnego adresu IP](virtual-network-network-interface-addresses.md#private) ustawienia.

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Pobrać statycznych prywatne informacje o adresie IP dla maszyny Wirtualnej

Uruchom następujące polecenie wiersza polecenia platformy Azure, aby przyjrzeć się wartości *metody alokacji prywatnego adresu IP* i *prywatny adres IP*:

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

Oczekiwane dane wyjściowe:

```json
"192.168.1.101"
```

Aby wyświetlić określone informacje IP karty interfejsu sieciowego dla tej maszyny Wirtualnej, w szczególności kwerendy karty Sieciowej:

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

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Usuń statycznego prywatnego adresu IP z maszyny Wirtualnej

Nie można usunąć statycznego prywatnego adresu IP z karty Sieciowej w wiersza polecenia platformy Azure dla wdrożenia usługi Azure Resource Manager. Należy:
- Utwórz nowy kartę Sieciową, która korzysta z dynamicznego adresu IP
- Ustawić karty Sieciowej dla maszyny Wirtualnej nowo utworzonej karty sieciowej. 

Aby zmienić karty Sieciowej dla maszyny Wirtualnej, używany w powyższych poleceń, wykonaj następujące kroki:

1. Uruchom **tworzenie kart interfejsu sieciowego azure** polecenie, aby utworzyć nowe przy użyciu alokacji dynamicznego adresu IP za pomocą nowego adresu IP karty Sieciowej. Żaden adres IP, ponieważ określono metodę alokacji jest **dynamiczne**.

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

2. Uruchom **zestawu azure vm** polecenie, aby zmienić używane przez maszynę Wirtualną kartę Sieciową.
   
    ```azurecli
    azure vm set -g TestRG -n DNS01 -N TestNIC2
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
    > Jeśli maszyna wirtualna jest wystarczająco duży, aby mieć więcej niż jedną kartę Sieciową, uruchom **usunąć kart interfejsu sieciowego azure** polecenie, aby usunąć stare karty sieciowej.

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji o zarządzaniu [ustawienia adresu IP](virtual-network-network-interface-addresses.md).