---
title: Analizowanie zabezpieczeń sieci za pomocą widoku grupy zabezpieczeń — interfejs wiersza polecenia platformy Azure
titleSuffix: Azure Network Watcher
description: W tym artykule opisano sposób używania interfejsu wiersza polecenia platformy Azure do analizowania zabezpieczeń maszyn wirtualnych za pomocą widoku grupy zabezpieczeń.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 73f1efc512bf031021791da8cc55bc4e7d98a812
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840779"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli"></a>Analizowanie zabezpieczeń maszyny wirtualnej za pomocą widoku grupy zabezpieczeń przy użyciu interfejsu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> - [Powershell](network-watcher-security-group-view-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-security-group-view-cli.md)
> - [INTERFEJS API ODPOCZYNKU](network-watcher-security-group-view-rest.md)

Widok grupy zabezpieczeń zwraca skonfigurowane i skuteczne reguły zabezpieczeń sieci, które są stosowane do maszyny wirtualnej. Ta funkcja jest przydatna do inspekcji i diagnozowania grup zabezpieczeń sieciowych i reguł skonfigurowanych na maszynie wirtualnej, aby upewnić się, że ruch jest poprawnie dozwolony lub odrzucany. W tym artykule pokażemy, jak pobrać skonfigurowane i skuteczne reguły zabezpieczeń do maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure

Aby wykonać kroki opisane w tym artykule, należy [zainstalować interfejs wiersza polecenia platformy Azure dla komputerów Mac, Linux i Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu przyjęto założenie, że postępuje już kroki w [Tworzenie obserwatora sieci,](network-watcher-create.md) aby utworzyć obserwatora sieci.

## <a name="scenario"></a>Scenariusz

Scenariusz omówiony w tym artykule pobiera skonfigurowane i skuteczne reguły zabezpieczeń dla danej maszyny wirtualnej.

## <a name="get-a-vm"></a>Uzyskaj maszynę wirtualną

Maszyna wirtualna jest wymagana `vm list` do uruchomienia polecenia cmdlet. Następujące polecenie zawiera listę maszyn wirtualnych w grupie zasobów:

```azurecli
az vm list -resource-group resourceGroupName
```

Gdy znasz maszynę wirtualną, `vm show` możesz użyć polecenia cmdlet, aby uzyskać jej identyfikator zasobu:

```azurecli
az vm show -resource-group resourceGroupName -name virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Pobieranie widoku grupy zabezpieczeń

Następnym krokiem jest pobranie wyniku widoku grupy zabezpieczeń.

```azurecli
az network watcher show-security-group-view --resource-group resourceGroupName --vm vmName
```

## <a name="viewing-the-results"></a>Wyświetlanie wyników

Poniższy przykład jest skrócona odpowiedź zwracanych wyników. Wyniki pokazują wszystkie skuteczne i stosowane reguły zabezpieczeń na maszynie wirtualnej w podziale na grupy **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**i **EffectiveSecurityRules**.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
      "resourceGroup": "{resourceGroupName}",
      "securityRuleAssociations": {
        "defaultSecurityRules": [
          {
            "access": "Allow",
            "description": "Allow inbound traffic from all VMs in VNET",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "*",
            "direction": "Inbound",
            "etag": null,
            "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups//providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/AllowVnetInBound",
            "name": "AllowVnetInBound",
            "priority": 65000,
            "protocol": "*",
            "provisioningState": "Succeeded",
            "resourceGroup": "",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "*"
          }...
        ],
        "effectiveSecurityRules": [
          {
            "access": "Deny",
            "destinationAddressPrefix": "*",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": null,
            "expandedSourceAddressPrefix": null,
            "name": "DefaultOutboundDenyAll",
            "priority": 65500,
            "protocol": "All",
            "sourceAddressPrefix": "*",
            "sourcePortRange": "0-65535"
          },
          {
            "access": "Allow",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "expandedSourceAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "name": "DefaultRule_AllowVnetOutBound",
            "priority": 65000,
            "protocol": "All",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "0-65535"
          },...
        ],
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "resourceGroup": "{resourceGroupName}",
          "securityRules": [
            {
              "access": "Allow",
              "description": null,
              "destinationAddressPrefix": "*",
              "destinationPortRange": "3389",
              "direction": "Inbound",
              "etag": "W/\"efb606c1-2d54-475a-ab20-da3f80393577\"",
              "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "name": "default-allow-rdp",
              "priority": 1000,
              "protocol": "TCP",
              "provisioningState": "Succeeded",
              "resourceGroup": "{resourceGroupName}",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*"
            }
          ]
        },
        "subnetAssociation": null
      }
    }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

Odwiedź [stronę Inspekcja grup zabezpieczeń sieci (NSG) z funkcją Kontrola sieci,](network-watcher-nsg-auditing-powershell.md) aby dowiedzieć się, jak zautomatyzować sprawdzanie poprawności grup zabezpieczeń sieciowych.

Dowiedz się więcej o regułach zabezpieczeń stosowanych do zasobów sieciowych, odwiedzając [omówienie widoku grupy zabezpieczeń](network-watcher-security-group-view-overview.md)
