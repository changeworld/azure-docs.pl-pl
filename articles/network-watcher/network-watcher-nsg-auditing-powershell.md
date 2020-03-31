---
title: Automatyzacja inspekcji nsg — widok grupy zabezpieczeń
titleSuffix: Azure Network Watcher
description: Ta strona zawiera instrukcje dotyczące konfigurowania inspekcji sieciowej grupy zabezpieczeń
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
ms.openlocfilehash: 59c1b6e6c281a736a79d110bd7d943344bcd5130
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840982"
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Automatyzacja inspekcji sieciowej grupy zabezpieczeń za pomocą widoku grupy zabezpieczeń usługi Azure Network Watcher

Klienci często stają przed wyzwaniem weryfikacji postawy bezpieczeństwa swojej infrastruktury. To wyzwanie nie różni się od ich maszyn wirtualnych na platformie Azure. Ważne jest, aby mieć podobny profil zabezpieczeń oparty na zastosowanych regułach sieciowej grupy zabezpieczeń (NSG). Za pomocą widoku grupy zabezpieczeń można teraz uzyskać listę reguł zastosowanych do maszyny Wirtualnej w sieciowej grupy zabezpieczeń. Można zdefiniować złoty profil zabezpieczeń sieciowej grupy zabezpieczeń i zainicjować widok grupy zabezpieczeń w tygodniu kadencji i porównać dane wyjściowe ze złotym profilem i utworzyć raport. W ten sposób można łatwo zidentyfikować wszystkie maszyny wirtualne, które nie są zgodne z określonym profilem zabezpieczeń.

Jeśli nie znasz grup zabezpieczeń sieciowych, zobacz [Omówienie zabezpieczeń sieci](../virtual-network/security-overview.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu można porównać znane dobre wyniki widoku grupy zabezpieczeń z wynikami widoku grupy zabezpieczeń zwróconymi dla maszyny wirtualnej.

W tym scenariuszu przyjęto założenie, że postępuje już kroki w [Tworzenie obserwatora sieci,](network-watcher-create.md) aby utworzyć obserwatora sieci. Scenariusz zakłada również, że grupa zasobów z prawidłową maszyną wirtualną istnieje do użycia.

## <a name="scenario"></a>Scenariusz

Scenariusz omówiony w tym artykule pobiera widok grupy zabezpieczeń dla maszyny wirtualnej.

W tym scenariuszu:

- Pobieranie znanego dobrego zestawu reguł
- Pobieranie maszyny wirtualnej za pomocą interfejsu API odpoczynku
- Pobierz widok grupy zabezpieczeń dla maszyny wirtualnej
- Oceń odpowiedź

## <a name="retrieve-rule-set"></a>Pobieranie zestawu reguł

Pierwszym krokiem w tym przykładzie jest praca z istniejącą linią bazową. Poniższy przykład jest niektóre json wyodrębnione z `Get-AzNetworkSecurityGroup` istniejącej sieciowej grupy zabezpieczeń przy użyciu polecenia cmdlet, który jest używany jako linia bazowa w tym przykładzie.

```json
[
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "3389",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1000,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "default-allow-rdp",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/default-allow-rdp"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "111",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1010,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "MyRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/MyRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "112",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1020,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "My2ndRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/My2ndRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "5672",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Deny",
        "Priority":  1030,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "ThisRuleNeedsToStay",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/ThisRuleNeedsToStay"
    }
]
```

## <a name="convert-rule-set-to-powershell-objects"></a>Konwertuj zestaw reguł na obiekty programu PowerShell

W tym kroku czytamy plik json, który został utworzony wcześniej z regułami, które mają znajdować się w sieciowej grupie zabezpieczeń w tym przykładzie.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Pobieranie obserwatora sieci

Następnym krokiem jest pobranie wystąpienia Obserwatora sieci. Zmienna `$networkWatcher` jest przekazywana do polecenia `AzNetworkWatcherSecurityGroupView` cmdlet.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
```

## <a name="get-a-vm"></a>Uzyskaj maszynę wirtualną

Maszyna wirtualna jest wymagana `Get-AzNetworkWatcherSecurityGroupView` do uruchomienia polecenia cmdlet przeciwko. Poniższy przykład pobiera obiekt maszyny Wirtualnej.

```powershell
$VM = Get-AzVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Pobieranie widoku grupy zabezpieczeń

Następnym krokiem jest pobranie wyniku widoku grupy zabezpieczeń. Ten wynik jest porównywany do "linii bazowej" json, który został pokazany wcześniej.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>Analiza wyników

Odpowiedź jest pogrupowana według interfejsów sieciowych. Różne typy zwracanych reguł są skuteczne i domyślne reguły zabezpieczeń. Wynik jest dalej podzielony na sposób jego stosowania w podsieci lub wirtualnej karcie sieciowej.

Poniższy skrypt programu PowerShell porównuje wyniki widoku grupy zabezpieczeń z istniejącym wynikiem sieciowej grupy zabezpieczeń. Poniższy przykład jest prostym przykładem tego, `Compare-Object` jak wyniki można porównać z poleceniem cmdlet.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

Poniższy przykład jest wynikiem. Możesz zobaczyć dwie reguły, które były w pierwszym zestawie reguł nie były obecne w porównaniu.

```
Name                     : My2ndRuleDoNotDelete
Description              : 
Protocol                 : *
SourcePortRange          : *
DestinationPortRange     : 112
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Allow
Priority                 : 1020
Direction                : Inbound
SideIndicator            : <=

Name                     : ThisRuleNeedsToStay
Description              : 
Protocol                 : TCP
SourcePortRange          : *
DestinationPortRange     : 5672
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Deny
Priority                 : 1030
Direction                : Inbound
SideIndicator            : <=
```

## <a name="next-steps"></a>Następne kroki

Jeśli ustawienia zostały zmienione, zobacz [Zarządzanie grupami zabezpieczeń sieci,](../virtual-network/manage-network-security-group.md) aby wyśledzić sieciową grupę zabezpieczeń i reguły zabezpieczeń, o których mowa.













