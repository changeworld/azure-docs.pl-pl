---
title: Analizowanie zabezpieczeniami sieci na platformie Azure obserwatora widok sieciowych grup zabezpieczeń — interfejs API REST | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób analizować zabezpieczenia maszyn wirtualnych przy użyciu widoku grupy zabezpieczeń za pomocą programu PowerShell.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: a2f418fe-f5d2-43ed-8dc3-df0ed2a4d4ac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: a2f55a65d88b499384fc961c3d6a479bac804ba8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59051533"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-rest-api"></a>Analizowanie zabezpieczeń maszyny wirtualnej przy użyciu widoku grupy zabezpieczeń przy użyciu interfejsu API REST

> [!div class="op_single_selector"]
> - [Program PowerShell](network-watcher-security-group-view-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-security-group-view-cli.md)
> - [Interfejs API REST](network-watcher-security-group-view-rest.md)

Widok grup zabezpieczeń zwraca reguły zabezpieczeń sieci skonfigurowane i obowiązujące, które są stosowane do maszyny wirtualnej. Ta możliwość jest przydatna do inspekcji i diagnozowania sieciowych grup zabezpieczeń i reguł, które są skonfigurowane na maszynie Wirtualnej, aby upewnić się, ruch jest prawidłowo dozwolony lub blokowany. W tym artykule pokazujemy, jak można pobrać reguł zabezpieczeń efektywny i zastosowane do maszyny wirtualnej przy użyciu interfejsu API REST


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu możesz wywołać interfejs API Rest obserwatora sieci można pobrać widoku grupy zabezpieczeń dla maszyny wirtualnej. ARMclient jest używane do wywołania interfejsu API REST przy użyciu programu PowerShell. ARMClient znajduje się na chocolatey na [ARMClient na narzędzia Chocolatey](https://chocolatey.org/packages/ARMClient)

W tym scenariuszu przyjęto założenie, zostały już wykonane czynności opisane w [utworzyć usługę Network Watcher](network-watcher-create.md) utworzyć usługę Network Watcher. Scenariusz również założenie, że grupa zasobów o prawidłową maszyna wirtualna istnieje ma być używany.

## <a name="scenario"></a>Scenariusz

Scenariusz, w tym artykule pobiera zasady zabezpieczeń efektywny i zastosowanych dla danej maszyny wirtualnej.

## <a name="log-in-with-armclient"></a>Zaloguj się przy użyciu ARMClient

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Pobieranie maszyny wirtualnej

Poniższy kod, uruchom następujący skrypt, aby zwrócić machineThe wirtualnego musi zmienne:

- **subscriptionId** — identyfikator subskrypcji można również pobrać za pomocą **Get AzSubscription** polecenia cmdlet.
- **resourceGroupName** — Nazwa grupy zasobów, która zawiera maszyny wirtualne.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Informacje, które jest wymagane jest **identyfikator** typ `Microsoft.Compute/virtualMachines` w odpowiedzi, jak pokazano w poniższym przykładzie:

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft
.Network/networkInterfaces/{nicName}"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Com
pute/virtualMachines/{vmName}/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute
/virtualMachines/{vmName}",
      "name": "{vmName}"
    }
  ]
}
```

## <a name="get-security-group-view-for-virtual-machine"></a>Pobierz widok grupy zabezpieczeń dla maszyny wirtualnej

Poniższy przykład żądań widok grup zabezpieczeń, docelowej maszyny wirtualnej. Wyniki z tego przykładu może służyć do porównania z zasadami i zdefiniowanych przez pochodzenia do wyszukania nieaktualną konfigurację zabezpieczeń.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName

$requestBody = @"
{
    'targetResourceId': '${targetUri}'

}
"@
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/securityGroupView?api-version=2016-12-01" $requestBody -verbose
```

## <a name="view-the-response"></a>Wyświetlanie odpowiedzi

Następujące przykładowe dane stanowią odpowiedź zwrócona z poprzedniego polecenia. Wyniki pokazują wszystkich reguł zabezpieczeń efektywny i zastosowane na maszynie wirtualnej w grupach w podziale **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**, i  **EffectiveSecurityRules**.

```json

{
  "networkInterfaces": [
    {
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "securityRules": [
            {
              "name": "default-allow-rdp",
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
              "properties": {
                "provisioningState": "Succeeded",
                "protocol": "TCP",
                "sourcePortRange": "*",
                "destinationPortRange": "3389",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 1000,
                "direction": "Inbound"
              }
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "name": "AllowVnetInBound",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/",
            "properties": {
              "provisioningState": "Succeeded",
              "description": "Allow inbound traffic from all VMs in VNET",
              "protocol": "*",
              "sourcePortRange": "*",
              "destinationPortRange": "*",
              "sourceAddressPrefix": "VirtualNetwork",
              "destinationAddressPrefix": "VirtualNetwork",
              "access": "Allow",
              "priority": 65000,
              "direction": "Inbound"
            }
          },
          ...
        ],
        "effectiveSecurityRules": [
          {
            "name": "DefaultOutboundDenyAll",
            "protocol": "All",
            "sourcePortRange": "0-65535",
            "destinationPortRange": "0-65535",
            "sourceAddressPrefix": "*",
            "destinationAddressPrefix": "*",
            "access": "Deny",
            "priority": 65500,
            "direction": "Outbound"
          },
          ...
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki

Odwiedź stronę [inspekcji sieciowych grup zabezpieczeń przy użyciu usługi Network Watcher](network-watcher-security-group-view-powershell.md) dowiesz się, jak zautomatyzować Weryfikacja sieciowych grup zabezpieczeń.


