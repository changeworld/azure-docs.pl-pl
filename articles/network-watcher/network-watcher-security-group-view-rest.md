---
title: Analizowanie zabezpieczeń sieci — widok grupy zabezpieczeń — interfejs API rest platformy Azure
titleSuffix: Azure Network Watcher
description: W tym artykule opisano sposób używania programu PowerShell do analizowania zabezpieczeń maszyn wirtualnych za pomocą widoku grupy zabezpieczeń.
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
ms.openlocfilehash: c9c76e9c06d4c45a096cff79dac82bb80ebe25d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840744"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-rest-api"></a>Analizowanie zabezpieczeń maszyny wirtualnej za pomocą widoku grupy zabezpieczeń przy użyciu interfejsu API REST

> [!div class="op_single_selector"]
> - [Powershell](network-watcher-security-group-view-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-security-group-view-cli.md)
> - [INTERFEJS API ODPOCZYNKU](network-watcher-security-group-view-rest.md)

Widok grupy zabezpieczeń zwraca skonfigurowane i skuteczne reguły zabezpieczeń sieci, które są stosowane do maszyny wirtualnej. Ta funkcja jest przydatna do inspekcji i diagnozowania grup zabezpieczeń sieciowych i reguł skonfigurowanych na maszynie wirtualnej, aby upewnić się, że ruch jest poprawnie dozwolony lub odrzucany. W tym artykule pokażemy, jak pobrać skuteczne i zastosowane reguły zabezpieczeń na maszynie wirtualnej przy użyciu interfejsu API REST


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu można wywołać interfejsu API odpoczynku obserwatora sieci, aby uzyskać widok grupy zabezpieczeń dla maszyny wirtualnej. ARMclient jest używany do wywoływania interfejsu API REST przy użyciu programu PowerShell. ARMClient znajduje się na chocolatey w [ARMClient na Chocolatey](https://chocolatey.org/packages/ARMClient)

W tym scenariuszu przyjęto założenie, że postępuje już kroki w [Tworzenie obserwatora sieci,](network-watcher-create.md) aby utworzyć obserwatora sieci. Scenariusz zakłada również, że grupa zasobów z prawidłową maszyną wirtualną istnieje do użycia.

## <a name="scenario"></a>Scenariusz

Scenariusz opisany w tym artykule pobiera skuteczne i stosowane reguły zabezpieczeń dla danej maszyny wirtualnej.

## <a name="log-in-with-armclient"></a>Zaloguj się za pomocą ARMClient

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Pobieranie maszyny wirtualnej

Uruchom następujący skrypt, aby zwrócić maszynę wirtualnąPoniżejszowy kod wymaga zmiennych:

- **subscriptionId** — identyfikator subskrypcji można również pobrać za pomocą polecenia cmdlet **Get-AzSubscription.**
- **resourceGroupName** — nazwa grupy zasobów zawierającej maszyny wirtualne.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Informacje, które są **id** potrzebne jest identyfikator `Microsoft.Compute/virtualMachines` pod typem w odpowiedzi, jak pokazano w poniższym przykładzie:

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

W poniższym przykładzie żąda widoku grupy zabezpieczeń docelowej maszyny wirtualnej. Wyniki z tego przykładu mogą służyć do porównania z reguł i zabezpieczeń zdefiniowanych przez jednostki, aby wyszukać dryft konfiguracji.

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

Poniższy przykład jest odpowiedzią zwróconą z poprzedniego polecenia. Wyniki pokazują wszystkie skuteczne i stosowane reguły zabezpieczeń na maszynie wirtualnej w podziale na grupy **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**i **EffectiveSecurityRules**.

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

## <a name="next-steps"></a>Następne kroki

Odwiedź [stronę Inspekcja grup zabezpieczeń sieci (NSG) z funkcją Kontrola sieci,](network-watcher-security-group-view-powershell.md) aby dowiedzieć się, jak zautomatyzować sprawdzanie poprawności grup zabezpieczeń sieciowych.


