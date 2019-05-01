---
title: Analizowanie zabezpieczeniami sieci na platformie Azure obserwatora widok sieciowych grup zabezpieczeń — PowerShell | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób analizować zabezpieczenia maszyn wirtualnych przy użyciu widoku grupy zabezpieczeń za pomocą programu PowerShell.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 04e76b49-6a1b-4d0f-9a9b-51cf2f4df5a2
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 389eab13193b313d8609bfa54d5e0dc42329f5ad
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64720778"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-powershell"></a>Analizowanie zabezpieczeń maszyny wirtualnej przy użyciu widoku grupy zabezpieczeń przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> - [Program PowerShell](network-watcher-security-group-view-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-security-group-view-cli.md)
> - [Interfejs API REST](network-watcher-security-group-view-rest.md)

Widok grup zabezpieczeń zwraca reguły zabezpieczeń sieci skonfigurowane i obowiązujące, które są stosowane do maszyny wirtualnej. Ta możliwość jest przydatna do inspekcji i diagnozowania sieciowych grup zabezpieczeń i reguł, które są skonfigurowane na maszynie Wirtualnej, aby upewnić się, ruch jest prawidłowo dozwolony lub blokowany. W tym artykule pokazujemy, jak można pobrać reguł skonfigurowane i obowiązujące zabezpieczeń z maszyną wirtualną przy użyciu programu PowerShell


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu możesz uruchomić `Get-AzNetworkWatcherSecurityGroupView` polecenia cmdlet, aby pobrać informacje dotyczące reguły zabezpieczeń.

W tym scenariuszu przyjęto założenie, zostały już wykonane czynności opisane w [utworzyć usługę Network Watcher](network-watcher-create.md) utworzyć usługę Network Watcher.

## <a name="scenario"></a>Scenariusz

Scenariusz, w tym artykule pobiera zasady zabezpieczeń skonfigurowane i obowiązujące dla danej maszyny wirtualnej.

## <a name="retrieve-network-watcher"></a>Retrieve Network Watcher

Pierwszym krokiem jest można pobrać wystąpienia usługi Network Watcher. Ta zmienna jest przekazywana do `Get-AzNetworkWatcherSecurityGroupView` polecenia cmdlet.

```powershell
$nw = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
```

## <a name="get-a-vm"></a>Uzyskiwanie maszyny Wirtualnej

Maszyna wirtualna jest wymagana do uruchamiania `Get-AzNetworkWatcherSecurityGroupView` polecenia cmdlet względem. Poniższy przykład powoduje pobranie obiektu maszyny Wirtualnej.

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name testvm1
```

## <a name="retrieve-security-group-view"></a>Pobierz widok grup zabezpieczeń

Następnym krokiem jest do pobierania wyników widoku grupy zabezpieczeń.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="viewing-the-results"></a>Wyświetlanie wyników

Poniższy przykład jest skrócona odpowiedzi zwrócone wyniki. Wyniki pokazują wszystkich reguł zabezpieczeń efektywny i zastosowane na maszynie wirtualnej w grupach w podziale **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**, i  **EffectiveSecurityRules**.

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

## <a name="next-steps"></a>Kolejne kroki

Odwiedź stronę [inspekcji sieciowych grup zabezpieczeń przy użyciu usługi Network Watcher](network-watcher-nsg-auditing-powershell.md) dowiesz się, jak zautomatyzować Weryfikacja sieciowych grup zabezpieczeń.


