---
title: Analizowanie zabezpieczeń sieci z widokiem grupy obserwatora zabezpieczeń Azure sieci - PowerShell | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób analizowania zabezpieczeń maszyn wirtualnych z widokiem grupy zabezpieczeń przy użyciu programu PowerShell.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 04e76b49-6a1b-4d0f-9a9b-51cf2f4df5a2
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 3983055cd580c263d39b908c61a16ed14353c9a4
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
ms.locfileid: "23864175"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-powershell"></a>Analizowanie zabezpieczeń maszyny wirtualnej z widokiem grupy zabezpieczeń przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> - [Program PowerShell](network-watcher-security-group-view-powershell.md)
> - [Interfejs wiersza polecenia 1.0](network-watcher-security-group-view-cli-nodejs.md)
> - [Interfejs wiersza polecenia 2.0](network-watcher-security-group-view-cli.md)
> - [Interfejs API REST](network-watcher-security-group-view-rest.md)

Widok grupy zabezpieczeń zwraca reguły zabezpieczeń sieci skonfigurowane i skuteczne, które są stosowane do maszyny wirtualnej. Ta funkcja jest przydatna do inspekcji i diagnostyki sieciowych grup zabezpieczeń i reguł, które są skonfigurowane na maszynie Wirtualnej dla zapewnienia ruchu jest poprawnie dozwolony lub niedozwolony. W tym artykule firma Microsoft opisano, jak pobrać zasady skonfigurowane i skuteczne zabezpieczeń do maszyny wirtualnej przy użyciu programu PowerShell

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu można uruchomić `Get-AzureRmNetworkWatcherSecurityGroupView` polecenia cmdlet można pobrać danych reguły zabezpieczeń.

W tym scenariuszu przyjęto zostały już wykonane kroki przedstawione w [utworzyć obserwatora sieciowego](network-watcher-create.md) utworzyć obserwatora sieciowego.

## <a name="scenario"></a>Scenariusz

Scenariusz omówione w tym artykule pobiera reguły zabezpieczeń skonfigurowane i efektywny dla podanej maszyny wirtualnej.

## <a name="retrieve-network-watcher"></a>Pobrać obserwatora sieciowego

Pierwszym krokiem jest pobrać wystąpienia obserwatora sieciowego. Ta zmienna jest przekazywana do `Get-AzureRmNetworkWatcherSecurityGroupView` polecenia cmdlet.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
```

## <a name="get-a-vm"></a>Uzyskiwanie maszyny Wirtualnej

Maszyna wirtualna jest wymagana do uruchamiania `Get-AzureRmNetworkWatcherSecurityGroupView` polecenia cmdlet przed. Poniższy przykład pobiera obiekt maszyny Wirtualnej.

```powershell
$VM = Get-AzurermVM -ResourceGroupName testrg -Name testvm1
```

## <a name="retrieve-security-group-view"></a>Pobierz widok grupy zabezpieczeń

Następnym krokiem jest do pobierania wyników widoku grupy zabezpieczeń.

```powershell
$secgroup = Get-AzureRmNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="viewing-the-results"></a>Wyświetlanie wyników

Poniższy przykład jest skróconą odpowiedzi wyników zwróconych. Wyniki Pokaż wszystkie reguły zabezpieczeń skuteczne i zastosowane na maszynie wirtualnej podziale grupy **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**, i **EffectiveSecurityRules**.

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

Odwiedź stronę [inspekcji sieci zabezpieczeń grupy (NSG) z obserwatora sieciowego](network-watcher-nsg-auditing-powershell.md) sposób automatyzacji sprawdzania poprawności grup zabezpieczeń sieci.


