---
title: Skonfiguruj prywatne adresy IP dla maszyn wirtualnych — interfejs wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować prywatnych adresów IP dla maszyn wirtualnych przy użyciu interfejsu wiersza polecenia (CLI) platformy Azure.
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
ms.openlocfilehash: 06016cf7a8ba10a9a8f49f90da99a26aaa072441
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60516445"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli"></a>Skonfiguruj prywatne adresy IP dla maszyny wirtualnej przy użyciu wiersza polecenia platformy Azure

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

W tym artykule opisano model wdrażania usługi Resource Manager. Możesz również [Zarządzanie statyczny prywatny adres IP w klasycznym modelu wdrażania](virtual-networks-static-private-ip-classic-cli.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> Następujące przykładowe polecenia wiersza polecenia platformy Azure oczekują istniejącego środowiska proste. Jeśli chcesz uruchamiać polecenia, ponieważ są one wyświetlane w tym dokumencie, najpierw utworzyć środowisko testowe opisane w [tworzenie sieci wirtualnej](quick-create-cli.md).

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Określ statyczny prywatny adres IP podczas tworzenia maszyny Wirtualnej

Aby utworzyć Maszynę wirtualną o nazwie *DNS01* w *frontonu* podsieci sieci wirtualnej o nazwie *TestVNet* za pomocą statycznego prywatnego adresu IP z *192.168.1.101*pełne następujące kroki:

1. Jeśli nie zostało jeszcze, po zainstalowaniu i skonfigurowaniu najnowsze [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i zaloguj się do platformy Azure konta przy użyciu [az login](/cli/azure/reference-index).

2. Tworzenie publicznego adresu IP dla maszyny Wirtualnej przy użyciu [tworzenie sieci az public-ip](/cli/azure/network/public-ip) polecenia. Lista wyświetlana po danych wyjściowych zawiera opis używanych parametrów.

    > [!NOTE]
    > Może chcesz lub konieczność użycia różnych wartości argumenty w tym oraz kolejnych kroków, w zależności od środowiska.

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
   * `--location`: Region platformy Azure, w której chcesz utworzyć publiczny adres IP.

3. Uruchom [tworzenie az sieciowego](/cli/azure/network/nic) polecenie, aby utworzyć z kartą Sieciową za pomocą statycznego prywatnego adresu IP. Lista wyświetlana po danych wyjściowych zawiera opis używanych parametrów. 
   
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

    * `--private-ip-address`: Statyczny prywatny adres IP dla karty sieciowej.
    * `--vnet-name`: Nazwa sieci wirtualnej, w której chcesz utworzyć kartę sieciową.
    * `--subnet`: Nazwa podsieci, w której ma zostać utworzona karta sieciowa

4. Uruchom [tworzenie maszyny wirtualnej platformy azure](/cli/azure/vm/nic) polecenie, aby utworzyć maszynę Wirtualną przy użyciu publicznego adresu IP i NIC został wcześniej utworzony. Lista wyświetlana po danych wyjściowych zawiera opis używanych parametrów.
   
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
   
   Parametrów innych niż podstawowe [tworzenie az vm](/cli/azure/vm) parametrów.

   * `--nics`: Nazwa karty Sieciowej, do której jest dołączona maszyna wirtualna.
   
Zalecane jest, że nie zostanie statycznie przypisany prywatny adres IP, przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej, o ile to konieczne, takie jak czas [przypisywanie wielu adresów IP do maszyny Wirtualnej z systemem Windows](virtual-network-multiple-ip-addresses-cli.md). Jeśli ręcznie ustawić jako prywatny adres IP w ramach systemu operacyjnego, upewnij się, że jej jako ten sam adres prywatny adres IP przypisany do platformy Azure [interfejsu sieciowego](virtual-network-network-interface-addresses.md#change-ip-address-settings), lub można utracić łączność z maszyną wirtualną. Dowiedz się więcej o [prywatny adres IP](virtual-network-network-interface-addresses.md#private) ustawienia.

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Pobierz statyczne prywatne informacje o adresie IP dla maszyny Wirtualnej

Uruchom następujące polecenie z wiersza polecenia platformy Azure, aby obserwować wartości dla *metody alokacji prywatny adres IP* i *prywatny adres IP*:

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

Oczekiwane dane wyjściowe:

```json
"192.168.1.101"
```

Aby wyświetlać informacje dotyczące adresu IP karty sieciowej dla tej maszyny Wirtualnej, zapytania specjalnie karty Sieciowej:

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

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Usuń statyczny prywatny adres IP z maszyny Wirtualnej

Nie można usunąć statyczny prywatny adres IP z karty Sieciowej w interfejsie wiersza polecenia platformy Azure, w przypadku wdrożeń usługi Azure Resource Manager. Musisz mieć:
- Tworzenie nowej karty Sieciowej, która używa dynamicznego adresu IP
- Ustaw karty Sieciowej na maszynę Wirtualną nowo utworzonej karty sieciowej. 

Aby zmienić karty Sieciowej dla maszyny Wirtualnej używane w poprzednich poleceniach, wykonaj następujące czynności:

1. Uruchom **kart sieciowych sieci platformy azure utworzyć** polecenie, aby utworzyć nowej alokacji dynamicznego adresu IP przy użyciu nowego adresu IP karty Sieciowej. Ponieważ żaden adres IP jest określona, metodą alokacji jest metoda **dynamiczne**.

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

2. Uruchom **zestawu maszyn wirtualnych platformy azure** polecenie, aby zmienić karty Sieciowej używanej przez maszynę Wirtualną.
   
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
    > Jeśli maszyna wirtualna jest wystarczająco duży, aby mieć więcej niż jedną kartę Sieciową, uruchom **Usuń kartę sieciową sieci platformy azure** polecenie, aby usunąć stare karty sieciowej.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o zarządzaniu [ustawienia adresu IP](virtual-network-network-interface-addresses.md).
