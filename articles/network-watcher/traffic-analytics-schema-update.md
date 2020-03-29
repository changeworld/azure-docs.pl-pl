---
title: Aktualizacja schematu analizy ruchu azure — marzec 2020 | Dokumenty firmy Microsoft
description: Przykładowe zapytania z nowymi polami w schemacie analizy ruchu.
services: network-watcher
documentationcenter: na
author: vinigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/06/2020
ms.author: vinigam
ms.openlocfilehash: 0e9d37e3a89473e59b94168f8f8c80e7a6621107
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969059"
---
# <a name="sample-queries-with-new-fields-in-traffic-analytics-schema-august-2019-schema-update"></a>Przykładowe zapytania z nowymi polami w schemacie usługi Traffic Analytics (aktualizacja schematu z sierpnia 2019 r.)

[Schemat dziennika analizy ruchu](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema) został zaktualizowany w celu uwzględnienia następujących nowych pól: **SrcPublicIPs_s** , **DestPublicIPs_s**, **NSGRule_s**. W ciągu najbliższych kilku miesięcy przestawione zostaną następujące starsze pola: **VMIP_s**, **Subscription_g**, **Region_s,** **NSGRules_s,** **Subnet_s**, **VM_s**, **NIC_s**, **PublicIPs_s**, **FlowCount_d**.
Nowe pola zawierają informacje o źródłowych i docelowych adresach IP oraz upraszczają kwerendy.

Poniżej znajdują się trzy przykłady pokazujące, jak zastąpić stare pola nowymi.

## <a name="example-1---vmip_s-subscription_g-region_s-subnet_s-vm_s-nic_s-publicips_s"></a>Przykład 1 - VMIP_s, Subscription_g, Region_s, Subnet_s, VM_s, NIC_s, PublicIPs_s

Nie musimy wywnioskować przypadków źródła i miejsca docelowego dla platformy Azure i zewnętrznych przepływów publicznych z pola FlowDirection_s dla azurepublic i externalpublic przepływów w szczególności. W przypadku urządzenia WUS (Network Virtual Appliance) pole FlowDirection_s może być również niewłaściwe.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend isAzureOrExternalPublicFlows = FlowType_s in ("AzurePublic", "ExternalPublic")
| extend SourceAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), VM1_s),
SourceAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), SrcIP_s),
SourceAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subscription_g, "N/A"), Subscription1_g),
SourceAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Region_s, "N/A"), Region1_s),
SourceAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subnet_s, "N/A"), Subnet1_s),
SourceAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', NIC_s, "N/A"), NIC1_s),
DestAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), VM2_s),
DestAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), DestIP_s),
DestAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subscription_g, "N/A"), Subscription2_g),
DestAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Region_s, "N/A"), Region2_s),
DestAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subnet_s, "N/A"), Subnet2_s),
DestAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', NIC_s, "N/A"), NIC2_s),
SourcePublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'I', PublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'O', PublicIPs_s, "N/A")
```


```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend SourceAzureVM = iif(isnotempty(VM1_s), VM1_s, "N/A"),
SourceAzureVMIP = iif(isnotempty(SrcIP_s), SrcIP_s, "N/A"),
SourceAzureVMSubscription = iif(isnotempty(Subscription1_g), Subscription1_g, "N/A"),
SourceAzureRegion = iif(isnotempty(Region1_s), Region1_s, "N/A"),
SourceAzureSubnet = iif(isnotempty(Subnet1_s), Subnet1_s, "N/A"),
SourceAzureNIC = iif(isnotempty(NIC1_s), NIC1_s, "N/A"),
DestAzureVM = iif(isnotempty(VM2_s), VM2_s, "N/A"),
DestAzureVMIP = iif(isnotempty(DestIP_s), DestIP_s, "N/A"),
DestAzureVMSubscription = iif(isnotempty(Subscription2_g), Subscription2_g, "N/A"),
DestAzureRegion = iif(isnotempty(Region2_s), Region2_s, "N/A"),
DestAzureSubnet = iif(isnotempty(Subnet2_s), Subnet2_s, "N/A"),
DestAzureNIC = iif(isnotempty(NIC2_s), NIC2_s, "N/A"),
SourcePublicIPsAggregated = iif(isnotempty(SrcPublicIPs_s), SrcPublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isnotempty(DestPublicIPs_s), DestPublicIPs_s, "N/A")
```


## <a name="example-2---nsgrules_s"></a>Przykład 2 - NSGRules_s

Wcześniejsze pole było format: <Wartość indeksu 0)>|<NSG_RULENAME>|<Flow Direction>|<Flow Status>|<FlowCount ProcessedByRule>

Wcześniej użyliśmy do agregowania danych w nsg i nsgrules. Teraz nie agregujemy. Tak więc NSGList_s zawiera tylko jedną grupę pozarządową i NSGRules_s również zawierać tylko jedną regułę. Więc usunęliśmy skomplikowane formatowanie tutaj i to samo można znaleźć w innych dziedzinach, jak wspomniano poniżej:

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| extend NSGName = NSGList_s // remains same
| extend NSGRuleName = NSGRuleComponents[1],
         FlowDirection = NSGRuleComponents[2],
         FlowStatus = NSGRuleComponents[3],
         FlowCountProcessedByRule = NSGRuleComponents[4]
| project NSGName, NSGRuleName, FlowDirection, FlowStatus, FlowCountProcessedByRule
```

```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| project NSGName = NSGList_s,
NSGRuleName = NSGRule_s ,
FlowDirection = FlowDirection_s,
FlowStatus = FlowStatus_s,
FlowCountProcessedByRule = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d
```

## <a name="example-3---flowcount_d"></a>Przykład 3 - FlowCount_d

Ponieważ nie mamy danych klubowych w całej NSG, FlowCount_d jest po prostu AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d.
Tylko 1 z powyższych 4 będzie niezerowy, a reszta trzy będzie 0. I to wskazuje stan i liczbę w nic, gdzie przepływ został przechwycony.

Jeśli przepływ był dozwolony, jedno z pól poprzedzonych "Dozwolone" zostanie wypełnione. Inne jedno pole poprzedzone "Odmowa" zostanie wypełnione.
Jeśli przepływ był przychodzący, jedno z pól sufiksów z polem "d"\_jak "InFlows_d" zostanie wypełnione. W przeciwnym razie "OutFlows_d" zostaną zaludnione.

W zależności od warunków powyżej 2 wiemy, który z 4 będzie wypełniony.


## <a name="next-steps"></a>Następne kroki
Aby uzyskać odpowiedzi na często zadawane pytania, zobacz Często zadawane pytania [dotyczące analizy ruchu,](traffic-analytics-faq.md) aby uzyskać szczegółowe informacje o [funkcjach,](traffic-analytics.md) zobacz Dokumentacja analizy ruchu
