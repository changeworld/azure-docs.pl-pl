---
title: Automatyzowanie sieciowej grupy zabezpieczeń inspekcji przy użyciu widoku grupy zabezpieczeń obserwatora sieci platformy Azure | Dokumentacja firmy Microsoft
description: Ta strona zawiera instrukcje dotyczące sposobu konfigurowania inspekcji sieciowej grupy zabezpieczeń
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 78a01bcf-74fe-402a-9812-285f3501f877
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 016d68de90088314250fef1fcfdb57d7f155ef79
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64707166"
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Automatyzowanie sieciowej grupy zabezpieczeń inspekcji przy użyciu widoku grupy zabezpieczeń obserwatora sieci platformy Azure

Klienci często muszą stawiać z żądaniem kontrolować poziom bezpieczeństwa infrastruktury. Ten problem nie różni się dla maszyn wirtualnych na platformie Azure. Należy mieć podobny profil zabezpieczeń na podstawie reguł sieciowej grupy zabezpieczeń (NSG), stosowane. Korzystając z widoku grupy zabezpieczeń, możesz teraz uzyskać listę reguł zastosowane do maszyny Wirtualnej w ramach sieciowej grupy zabezpieczeń. Można zdefiniować złoty profil zabezpieczeń sieciowej grupy zabezpieczeń i zainicjować widok grup zabezpieczeń w erze co tydzień i porównasz dane wyjściowe do profilu złoty i utworzyć raport. W ten sposób można zidentyfikować z łatwością wszystkich maszyn wirtualnych, które nie są zgodne z profilu zabezpieczeń wymaganych.

Jeśli nie jesteś zaznajomiony z sieciowymi grupami zabezpieczeń, zobacz [Omówienie zabezpieczeń sieci](../virtual-network/security-overview.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu możesz porównać znane dobre linii bazowej do wyników widoku grupy zabezpieczeń dla maszyny wirtualnej.

W tym scenariuszu przyjęto założenie, zostały już wykonane czynności opisane w [utworzyć usługę Network Watcher](network-watcher-create.md) utworzyć usługę Network Watcher. Scenariusz również założenie, że grupa zasobów o prawidłową maszyna wirtualna istnieje ma być używany.

## <a name="scenario"></a>Scenariusz

Scenariusz, w tym artykule pobiera widok grupy zabezpieczeń dla maszyny wirtualnej.

W tym scenariuszu wykonasz następujące czynności:

- Pobieranie zestawu znanych rozsądną regułą
- Pobierz maszynę wirtualną przy użyciu interfejsu API Rest
- Pobierz widok grupy zabezpieczeń dla maszyny wirtualnej
- Oceń odpowiedzi

## <a name="retrieve-rule-set"></a>Pobieranie zestawu reguł

Pierwszym krokiem w tym przykładzie jest pracować z istniejącego planu bazowego. Poniższy przykład przedstawia niektóre json wyodrębnione z istniejącej grupy zabezpieczeń sieci przy użyciu `Get-AzNetworkSecurityGroup` polecenia cmdlet, która jest używana jako linii bazowej, w tym przykładzie.

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

## <a name="convert-rule-set-to-powershell-objects"></a>Konwertowanie zestawu reguł do obiektów programu PowerShell

W tym kroku będziemy są odczytu pliku json, który został wcześniej utworzony za pomocą reguł, które powinny być w sieciowej grupie zabezpieczeń, w tym przykładzie.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Retrieve Network Watcher

Następnym krokiem jest można pobrać wystąpienia usługi Network Watcher. `$networkWatcher` Zmienna jest przekazywana do `AzNetworkWatcherSecurityGroupView` polecenia cmdlet.

```powershell
$nw = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>Uzyskiwanie maszyny Wirtualnej

Maszyna wirtualna jest wymagana do uruchamiania `Get-AzNetworkWatcherSecurityGroupView` polecenia cmdlet względem. Poniższy przykład powoduje pobranie obiektu maszyny Wirtualnej.

```powershell
$VM = Get-AzVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Pobierz widok grup zabezpieczeń

Następnym krokiem jest do pobierania wyników widoku grupy zabezpieczeń. Ten wynik jest porównywana do formatu json "baseline", pokazaną wcześniej.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>Analizowanie wyników

Odpowiedzi są grupowane według interfejsów sieciowych. Różnych typów reguł, zwracane są skuteczne i domyślne reguły zabezpieczeń. Wynik jest dalszych rozbiciu sposób stosowania, w podsieci lub wirtualnej karty sieciowej.

Poniższy skrypt programu PowerShell porównuje wyniki widok grup zabezpieczeń do istniejącego pliku wyjściowego z sieciową grupą zabezpieczeń. Poniższy przykład to prosty przykład, jak można porównać wyniki z `Compare-Object` polecenia cmdlet.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

Poniższy przykład jest wynikiem. Widać, że dwie reguły, które w pierwszej regule ustawiono nie był obecny w porównaniu.

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

## <a name="next-steps"></a>Kolejne kroki

Jeśli ustawienia zostały zmienione, zobacz [Zarządzanie sieciowymi grupami zabezpieczeń](../virtual-network/manage-network-security-group.md) ułatwiają śledzenie reguły zabezpieczeń sieci grupy i zabezpieczeń, które są w danym.













