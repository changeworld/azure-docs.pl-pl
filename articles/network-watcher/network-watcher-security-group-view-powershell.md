---
title: Analizowanie zabezpieczeń sieci — widok grupy zabezpieczeń — Azure PowerShell
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
ms.openlocfilehash: 4cba2c7e25b5f76b0638da1c551514f102247ae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840795"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-powershell"></a>Analizowanie zabezpieczeń maszyny wirtualnej za pomocą widoku grupy zabezpieczeń przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> - [Powershell](network-watcher-security-group-view-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-security-group-view-cli.md)
> - [INTERFEJS API ODPOCZYNKU](network-watcher-security-group-view-rest.md)

Widok grupy zabezpieczeń zwraca skonfigurowane i skuteczne reguły zabezpieczeń sieci, które są stosowane do maszyny wirtualnej. Ta funkcja jest przydatna do inspekcji i diagnozowania grup zabezpieczeń sieciowych i reguł skonfigurowanych na maszynie wirtualnej, aby upewnić się, że ruch jest poprawnie dozwolony lub odrzucany. W tym artykule pokażemy, jak pobrać skonfigurowane i skuteczne reguły zabezpieczeń na maszynie wirtualnej za pomocą programu PowerShell


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu `Get-AzNetworkWatcherSecurityGroupView` należy uruchomić polecenie cmdlet, aby pobrać informacje o regułie zabezpieczeń.

W tym scenariuszu przyjęto założenie, że postępuje już kroki w [Tworzenie obserwatora sieci,](network-watcher-create.md) aby utworzyć obserwatora sieci.

## <a name="scenario"></a>Scenariusz

Scenariusz omówiony w tym artykule pobiera skonfigurowane i skuteczne reguły zabezpieczeń dla danej maszyny wirtualnej.

## <a name="retrieve-network-watcher"></a>Pobieranie obserwatora sieci

Pierwszym krokiem jest pobranie wystąpienia Obserwatora sieci. Ta zmienna jest `Get-AzNetworkWatcherSecurityGroupView` przekazywana do polecenia cmdlet.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
```

## <a name="get-a-vm"></a>Uzyskaj maszynę wirtualną

Maszyna wirtualna jest wymagana `Get-AzNetworkWatcherSecurityGroupView` do uruchomienia polecenia cmdlet przeciwko. Poniższy przykład pobiera obiekt maszyny Wirtualnej.

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name testvm1
```

## <a name="retrieve-security-group-view"></a>Pobieranie widoku grupy zabezpieczeń

Następnym krokiem jest pobranie wyniku widoku grupy zabezpieczeń.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="viewing-the-results"></a>Wyświetlanie wyników

Poniższy przykład jest skrócona odpowiedź zwracanych wyników. Wyniki pokazują wszystkie skuteczne i stosowane reguły zabezpieczeń na maszynie wirtualnej w podziale na grupy **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**i **EffectiveSecurityRules**.

```
NetworkInterfaces : [
                      {
                        "NetworkInterfaceSecurityRules": [
                          {
                            "Name": "default-allow-rdp",
                            "Etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
                            "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/securityRules/default-allow-rdp",
                            "Protocol": "TCP",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "3389",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "Access": "Allow",
                            "Priority": 1000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "DefaultSecurityRules": [
                          {
                            "Name": "AllowVnetInBound",
                            "Id": "/subscriptions00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/defaultSecurityRules/",
                            "Description": "Allow inbound traffic from all VMs in VNET",
                            "Protocol": "*",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "*",
                            "SourceAddressPrefix": "VirtualNetwork",
                            "DestinationAddressPrefix": "VirtualNetwork",
                            "Access": "Allow",
                            "Priority": 65000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "EffectiveSecurityRules": [
                          {
                            "Name": "DefaultOutboundDenyAll",
                            "Protocol": "All",
                            "SourcePortRange": "0-65535",
                            "DestinationPortRange": "0-65535",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "ExpandedSourceAddressPrefix": [],
                            "ExpandedDestinationAddressPrefix": [],
                            "Access": "Deny",
                            "Priority": 65500,
                            "Direction": "Outbound"
                          },
                          ...
                        ]
                      }
                    ]
```

## <a name="next-steps"></a>Następne kroki

Odwiedź [stronę Inspekcja grup zabezpieczeń sieci (NSG) z funkcją Kontrola sieci,](network-watcher-nsg-auditing-powershell.md) aby dowiedzieć się, jak zautomatyzować sprawdzanie poprawności grup zabezpieczeń sieciowych.


