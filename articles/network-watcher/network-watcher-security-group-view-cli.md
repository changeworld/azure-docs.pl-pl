---
title: Analizowanie zabezpieczeniami sieci na platformie Azure obserwatora widok sieciowych grup zabezpieczeń — interfejs wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak analizować zabezpieczenia maszyn wirtualnych przy użyciu widoku grupy zabezpieczeń za pomocą wiersza polecenia platformy Azure.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: a986ff4f-7e0c-4994-95e1-4ac824986500
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: fc86b2fd7156ff84b7d91fd39c79caccb815312c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60727809"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli"></a>Analizowanie zabezpieczeń maszyny wirtualnej przy użyciu widoku grupy zabezpieczeń przy użyciu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> - [Program PowerShell](network-watcher-security-group-view-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-security-group-view-cli.md)
> - [Interfejs API REST](network-watcher-security-group-view-rest.md)

Widok grup zabezpieczeń zwraca reguły zabezpieczeń sieci skonfigurowane i obowiązujące, które są stosowane do maszyny wirtualnej. Ta możliwość jest przydatna do inspekcji i diagnozowania sieciowych grup zabezpieczeń i reguł, które są skonfigurowane na maszynie Wirtualnej, aby upewnić się, ruch jest prawidłowo dozwolony lub blokowany. W tym artykule pokazujemy, jak można pobrać reguł skonfigurowane i obowiązujące zabezpieczeń z maszyną wirtualną przy użyciu wiersza polecenia platformy Azure

Aby wykonać kroki opisane w tym artykule, musisz [zainstalować interfejs wiersza polecenia platformy Azure dla systemów Mac, Linux i Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu przyjęto założenie, zostały już wykonane czynności opisane w [utworzyć usługę Network Watcher](network-watcher-create.md) utworzyć usługę Network Watcher.

## <a name="scenario"></a>Scenariusz

Scenariusz, w tym artykule pobiera zasady zabezpieczeń skonfigurowane i obowiązujące dla danej maszyny wirtualnej.

## <a name="get-a-vm"></a>Uzyskiwanie maszyny Wirtualnej

Maszyna wirtualna jest wymagana do uruchamiania `vm list` polecenia cmdlet. Następujące polecenie wyświetla listę maszyn wirtualnych w grupie zasobów:

```azurecli
az vm list -resource-group resourceGroupName
```

Jeśli znasz już maszyny wirtualnej, można użyć `vm show` polecenia cmdlet, aby uzyskać jego identyfikator zasobu:

```azurecli
az vm show -resource-group resourceGroupName -name virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Pobierz widok grup zabezpieczeń

Następnym krokiem jest do pobierania wyników widoku grupy zabezpieczeń.

```azurecli
az network watcher show-security-group-view --resource-group resourceGroupName --vm vmName
```

## <a name="viewing-the-results"></a>Wyświetlanie wyników

Poniższy przykład jest skrócona odpowiedzi zwrócone wyniki. Wyniki pokazują wszystkich reguł zabezpieczeń efektywny i zastosowane na maszynie wirtualnej w grupach w podziale **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**, i  **EffectiveSecurityRules**.

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

## <a name="next-steps"></a>Kolejne kroki

Odwiedź stronę [inspekcji sieciowych grup zabezpieczeń przy użyciu usługi Network Watcher](network-watcher-nsg-auditing-powershell.md) dowiesz się, jak zautomatyzować Weryfikacja sieciowych grup zabezpieczeń.

Dowiedz się więcej na temat reguł zabezpieczeń, które są stosowane do zasobów sieciowych, odwiedzając [Omówienie widoku grupy zabezpieczeń](network-watcher-security-group-view-overview.md)
