---
title: Aktualizacja schematu usługi Azure Traffic Analytics — marzec 2020 | Dokumenty firmy Microsoft
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
ms.openlocfilehash: 4fe981576e3f6e58b0886d9c0d2eb2915d8b7720
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396625"
---
# <a name="sample-queries-with-new-fields-in-the-traffic-analytics-schema-august-2019-schema-update"></a>Przykładowe zapytania z nowymi polami w schemacie analizy ruchu (aktualizacja schematu sierpień 2019 r.)

[Schemat dziennika analizy ruchu](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema) obejmuje następujące nowe pola: **SrcPublicIPs_s**, **DestPublicIPs_s**, **NSGRule_s**. Nowe pola zawierają informacje o źródłowych i docelowych adresach IP i upraszczają kwerendy.

W ciągu najbliższych kilku miesięcy przestawione zostaną następujące starsze pola: **VMIP_s**, **Subscription_g**, **Region_s,** **NSGRules_s,** **Subnet_s**, **VM_s**, **NIC_s**, **PublicIPs_s**, **FlowCount_d**.

Poniższe trzy przykłady pokazują, jak zastąpić stare pola nowymi.

## <a name="example-1-vmip_s-subscription_g-region_s-subnet_s-vm_s-nic_s-and-publicips_s-fields"></a>Przykład 1: pola VMIP_s, Subscription_g, Region_s, Subnet_s, VM_s, NIC_s i PublicIPs_s

Nie musimy wywnioskować przypadków źródłowych i docelowych z pola **FlowDirection_s** dla przepływów publicznych i zewnętrznych platformy Azure. Używanie pola **FlowDirection_s** dla wirtualnego urządzenia sieciowego może być również niewłaściwe.

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

## <a name="example-2-nsgrules_s-field"></a>Przykład 2: pole NSGRules_s

Stare pole używało formatu:

<Wartość indeksu 0)>|<>NSG_ RuleName |<Flow Direction>|<Flow Status>|<FlowCount ProcessedByRule>

Nie agregujemy już danych w sieciowej grupie zabezpieczeń (NSG). W zaktualizowanym schemacie **NSGList_s** zawiera tylko jedną grupę ndsg. Również **NSGRules** zawiera tylko jedną regułę. Usunęliśmy skomplikowane formatowanie tutaj i w innych polach, jak pokazano w przykładzie.

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

## <a name="example-3-flowcount_d-field"></a>Przykład 3: pole FlowCount_d

Ponieważ nie mamy danych klubu w całej NSG, **FlowCount_d** jest po prostu:

**AllowedInFlows_d****DeniedInFlows_d** + **DeniedOutFlows_d AllowedOutFlows_d** + **DeniedOutFlows_d**  + 

Tylko jedno z czterech pól będzie niezerowe. Pozostałe trzy pola będą zerowe. Pola wypełniają się, aby wskazać stan i liczbę w karcie sieciowej, w której przepływ został przechwycony.

Aby zilustrować te warunki:

- Jeśli przepływ był dozwolony, jedno z poprzedzonych pól "Dozwolone" zostanie wypełnione.
- Jeśli przepływ został odrzucony, jedno z poprzedzonych pól "Odmowa" zostanie wypełnione.
- Jeśli przepływ był przychodzący, jedno z pól sufiksów "InFlows_d" zostanie wypełnione.
- Jeśli przepływ był wychodzący, jedno z pól sufiksów "OutFlows_d" zostanie wypełnione.

W zależności od warunków wiemy, które z czterech pól zostanie zaludnione.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać odpowiedzi na często zadawane pytania, zobacz [Analiza ruchu często zadawane pytania.](traffic-analytics-faq.md)
- Aby wyświetlić szczegółowe informacje o funkcjach, zobacz [dokumentację usługi Traffic Analytics](traffic-analytics.md).
