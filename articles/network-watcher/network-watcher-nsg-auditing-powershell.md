---
title: Zautomatyzować NSG inspekcji z widokiem grupy zabezpieczeń obserwatora sieci Azure | Dokumentacja firmy Microsoft
description: Ta strona zawiera instrukcje dotyczące sposobu konfigurowania inspekcji grupy zabezpieczeń sieci
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 78a01bcf-74fe-402a-9812-285f3501f877
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 227ea446a75c167be27128b15de1d3c216e6856d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34363380"
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Automatyzowanie NSG inspekcji z widokiem grupy zabezpieczeń obserwatora sieci platformy Azure

Klienci często występują z żądaniem kontrolować stan zabezpieczeń infrastruktury. Nie różni się ich maszyn wirtualnych na platformie Azure się tego żądania. Należy mieć podobny profil zabezpieczeń na podstawie reguł sieciowej grupy zabezpieczeń (NSG) stosowane. Korzystając z widoku grupy zabezpieczeń, można teraz uzyskać listę reguł zastosowane do maszyny Wirtualnej w ramach grupy NSG. Można zdefiniować złotego profil zabezpieczeń NSG i zainicjować widok grupy zabezpieczeń w okresach co tydzień i porównania danych wyjściowych do profilu złotego i utworzyć raport. W ten sposób można zidentyfikować z łatwością wszystkich maszyn wirtualnych, które nie są zgodne z profil zabezpieczeń wymaganych.

Jeśli nie znasz z grup zabezpieczeń sieci, zobacz [Przegląd zabezpieczeń sieci](../virtual-network/security-overview.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu należy porównać znanej dobrej linii bazowej, aby zabezpieczeń grupy Wyświetl wyniki zwracane dla maszyny wirtualnej.

W tym scenariuszu przyjęto zostały już wykonane kroki przedstawione w [utworzyć obserwatora sieciowego](network-watcher-create.md) utworzyć obserwatora sieciowego. Scenariusz również założono, że grupa zasobów o prawidłową maszynę wirtualną istnieje ma być używany.

## <a name="scenario"></a>Scenariusz

Scenariusz omówione w tym artykule pobiera widok grupy zabezpieczeń dla maszyny wirtualnej.

W tym scenariuszu obejmują:

- Pobierz zestaw znanych rozsądną regułą
- Pobieranie maszyny wirtualnej z interfejsu API Rest
- Pobierz widok grupy zabezpieczeń dla maszyny wirtualnej
- Ocena odpowiedzi

## <a name="retrieve-rule-set"></a>Pobieranie zestawu reguł

Pierwszym krokiem w tym przykładzie jest praca z istniejącej linii bazowej. Poniższy przykład jest niektórych json wyodrębniony z istniejącej grupy zabezpieczeń sieci przy użyciu `Get-AzureRmNetworkSecurityGroup` polecenia cmdlet, które jest używane jako linii bazowej, w tym przykładzie.

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

## <a name="convert-rule-set-to-powershell-objects"></a>Konwertuj zestawu reguł do obiektów programu PowerShell

W tym kroku są możemy odczytu pliku json, który został utworzony wcześniej przy użyciu reguł, które powinny znajdować się w grupie zabezpieczeń sieci w ramach tego przykładu.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Pobrać obserwatora sieciowego

Następnym krokiem jest w celu pobrania wystąpienia obserwatora sieciowego. `$networkWatcher` Zmienna jest przekazywana do `AzureRmNetworkWatcherSecurityGroupView` polecenia cmdlet.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>Uzyskiwanie maszyny Wirtualnej

Maszyna wirtualna jest wymagana do uruchamiania `Get-AzureRmNetworkWatcherSecurityGroupView` polecenia cmdlet przed. Poniższy przykład pobiera obiekt maszyny Wirtualnej.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Pobierz widok grupy zabezpieczeń

Następnym krokiem jest do pobierania wyników widoku grupy zabezpieczeń. Ten wynik jest porównywany json "baseline", która została przedstawiona wcześniej.

```powershell
$secgroup = Get-AzureRmNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>Analizowanie wyników

Odpowiedzi są grupowane według interfejsów sieciowych. Różne typy zwracane zasady zaczynają obowiązywać i domyślne reguły zabezpieczeń. Wynik jest dalsze wyszczególnieniem sposób stosowania, podsieć lub wirtualnych kart sieciowych.

Poniższy skrypt programu PowerShell porównanie wyników widoku grupy zabezpieczeń do istniejących danych wyjściowych grupy NSG. Poniższy przykład jest prosty przykład sposobu wyniki mogą być porównywane z `Compare-Object` polecenia cmdlet.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

Poniższy przykład jest wynikiem. Można zauważyć, że dwa zasad ustawionych w pierwszej regule nie były widoczne w porównaniu.

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

Jeśli ustawienia zostały zmienione, zobacz [Zarządzaj grupami zabezpieczeń sieci](../virtual-network/manage-network-security-group.md) do śledzenia sieci zabezpieczeń grupy i zabezpieczeń reguł, które w danym.













