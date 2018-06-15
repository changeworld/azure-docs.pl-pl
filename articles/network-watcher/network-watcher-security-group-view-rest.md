---
title: Analizowanie zabezpieczeń sieci z widokiem grupy obserwatorów zabezpieczeń Azure sieci - interfejsu API REST | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób analizowania zabezpieczeń maszyn wirtualnych z widokiem grupy zabezpieczeń przy użyciu programu PowerShell.
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
ms.openlocfilehash: 0eec45630fe3467db26620787038f6dd5a05cc72
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
ms.locfileid: "23864322"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-rest-api"></a>Analizowanie zabezpieczeń maszyny wirtualnej z widoku grupy zabezpieczeń przy użyciu interfejsu API REST

> [!div class="op_single_selector"]
> - [Program PowerShell](network-watcher-security-group-view-powershell.md)
> - [Interfejs wiersza polecenia 1.0](network-watcher-security-group-view-cli-nodejs.md)
> - [Interfejs wiersza polecenia 2.0](network-watcher-security-group-view-cli.md)
> - [Interfejs API REST](network-watcher-security-group-view-rest.md)

Widok grupy zabezpieczeń zwraca reguły zabezpieczeń sieci skonfigurowane i skuteczne, które są stosowane do maszyny wirtualnej. Ta funkcja jest przydatna do inspekcji i diagnostyki sieciowych grup zabezpieczeń i reguł, które są skonfigurowane na maszynie Wirtualnej dla zapewnienia ruchu jest poprawnie dozwolony lub niedozwolony. W tym artykule firma Microsoft opisano, jak pobrać zasady zabezpieczeń skuteczne i zastosowane do maszyny wirtualnej przy użyciu interfejsu API REST

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu można wywołać interfejsu API Rest obserwatora sieciowego można pobrać widoku grupy zabezpieczeń dla maszyny wirtualnej. ARMclient służy do wywołania interfejsu API REST przy użyciu programu PowerShell. ARMClient znajduje się na chocolatey w [ARMClient na Chocolatey](https://chocolatey.org/packages/ARMClient)

W tym scenariuszu przyjęto zostały już wykonane kroki przedstawione w [utworzyć obserwatora sieciowego](network-watcher-create.md) utworzyć obserwatora sieciowego. Scenariusz również założono, że grupa zasobów o prawidłową maszynę wirtualną istnieje ma być używany.

## <a name="scenario"></a>Scenariusz

Scenariusz omówione w tym artykule pobiera reguły zabezpieczeń skuteczne i zastosowane dla podanej maszyny wirtualnej.

## <a name="log-in-with-armclient"></a>Zaloguj się za pomocą ARMClient

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Pobieranie maszyny wirtualnej

Uruchom następujący skrypt, aby zwrócić wirtualnego machineThe następujący kod wymaga zmiennych:

- **Identyfikator subskrypcji** — identyfikator subskrypcji można również pobrać z **Get-AzureRMSubscription** polecenia cmdlet.
- **resourceGroupName** — Nazwa grupy zasobów, która zawiera maszyny wirtualne.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Informacje potrzebne jest **identyfikator** w obszarze typu `Microsoft.Compute/virtualMachines` w odpowiedzi, jak pokazano w poniższym przykładzie:

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

Poniższy przykład żądań widok grupy zabezpieczeń docelowej maszyny wirtualnej. Wyniki z tego przykładu może służyć do porównania zasad i zdefiniowanych przez inicjowanie do wyszukania odejście konfiguracji zabezpieczeń.

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

## <a name="view-the-response"></a>Widok odpowiedzi

Poniższy przykład jest odpowiedź zwrócona z poprzedniego polecenia. Wyniki Pokaż wszystkie reguły zabezpieczeń skuteczne i zastosowane na maszynie wirtualnej podziale grupy **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**, i **EffectiveSecurityRules**.

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

Odwiedź stronę [inspekcji sieci zabezpieczeń grupy (NSG) z obserwatora sieciowego](network-watcher-security-group-view-powershell.md) sposób automatyzacji sprawdzania poprawności grup zabezpieczeń sieci.


