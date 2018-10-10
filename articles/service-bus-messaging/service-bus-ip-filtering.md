---
title: Azure filtry połączenia usługi Service Bus IP | Dokumentacja firmy Microsoft
description: Jak używać filtrowania z określonych adresów IP do usługi Azure Service Bus w celu połączenia bloków adresów IP.
services: service-bus
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: clemensv
ms.openlocfilehash: c6e9eef762d4a9eb95685d94c61ce10d499bb155
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2018
ms.locfileid: "48884807"
---
# <a name="use-ip-filters"></a>Użyj filtrów IP

W scenariuszach, w których usługi Azure Service Bus jest dostępna tylko w pewnych dobrze znanych witryn *filtru IP* funkcja umożliwia skonfigurowanie reguł dla odrzuca lub akceptowanie ruchu pochodzącego z określonych adresów IPv4. Na przykład te adresy może być udostępnianych przez firmy bramy translatora adresów Sieciowych.

## <a name="when-to-use"></a>Kiedy stosować

Istnieją dwa przypadki użycia określonych, w których jest przydatne blokowanie punktów końcowych usługi Service Bus na potrzeby niektórych adresów IP:

- Service Bus powinny odbierać ruch tylko z określonego zakresu adresów IP i odrzucić wszystkie inne elementy. Na przykład używasz usługi Service Bus za pomocą [Azure Express Route] [ express-route] na tworzenie prywatnych połączeń w infrastrukturze lokalnej.
- Należy odrzucić ruch z adresów IP, które zostały zidentyfikowane jako podejrzane przez administratora usługi Service Bus.

## <a name="how-filter-rules-are-applied"></a>Sposób stosowania reguły filtrowania

Reguły filtrowania adresów IP są stosowane na poziomie przestrzeni nazw usługi Service Bus. W związku z tym zasady stosowane do wszystkich połączeń z klientami przy użyciu dowolnego obsługiwanego protokołu.

Każda próba połączenia z adresu IP, który nieautoryzowane dopasowania, odrzucenia jako regułę wydzielenia adresów IP w przestrzeni nazw usługi Service Bus. Odpowiedź nie mogą zawierać reguły adresów IP.

## <a name="default-setting"></a>Ustawienie domyślne

Domyślnie **filtru IP** siatki w portalu usługi Service Bus jest pusty. To ustawienie domyślne oznacza, że przestrzeń nazw akceptuje połączenia z dowolnego adresu IP. To ustawienie domyślne jest odpowiednikiem regułę, która akceptuje zakres adresów IP 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Ocenę reguł filtrowania adresów IP

Reguły filtrowania adresów IP są stosowane w kolejności i pierwszej reguły, który jest zgodny z adresem IP określa akcję Zaakceptuj lub Odrzuć.

Na przykład jeśli chcesz zaakceptować adresów w 70.37.104.0/24 zakresu i odrzucić wszystkie inne elementy, pierwszą regułę w siatce powinna obsługiwać 70.37.104.0/24 zakresu adresów. Następną regułę należy odrzucić wszystkie adresy, używając 0.0.0.0/0 zakresu.

> [!NOTE]
> Odrzuca adresy IP można zapobiec interakcji z usługą Service Bus innych usług platformy Azure (np. usługi Azure Stream Analytics, Azure Virtual Machines lub Device Explorer w portalu).

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Tworzenie reguły sieci wirtualnej przy użyciu szablonów usługi Azure Resource Manager

> ! [WAŻNE] Sieci wirtualne są obsługiwane tylko w **premium** warstwy usługi Service Bus.

Następujący szablon usługi Resource Manager umożliwia dodanie reguły sieci wirtualnej do istniejącej przestrzeni nazw usługi Service Bus.

Parametry szablonu:

- **ipFilterRuleName** musi być unikatowy, bez uwzględniania wielkości liter, alfanumerycznego ciągu maksymalnie 128 znaków.
- **ipFilterAction** jest **Odrzuć** lub **Akceptuj** jako akcję do zastosowania dla tej reguły filtru adresu IP.
- **ipMask** jest pojedynczy adres IPv4 lub bloku adresów IP w notacji CIDR. Na przykład w CIDR 70.37.104.0/24 notacji reprezentuje 256 adresów IPv4 z 70.37.104.0 70.37.104.255 z 24 określającą liczbę bitów znaczące prefiks dla zakresu.

```json
{  
   "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{     
          "namespaceName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the namespace"
             }
          },
          "ipFilterRuleName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the Authorization rule"
             }
          },
          "ipFilterAction":{  
             "type":"string",
             "allowedValues": ["Reject", "Accept"],
             "metadata":{  
                "description":"IP Filter Action"
             }
          },
          "IpMask":{  
             "type":"string",
             "metadata":{  
                "description":"IP Mask"
             }
          }
      },
    "resources": [
        {
            "apiVersion": "2018-01-01-preview",
            "name": "[concat(parameters('namespaceName'), '/', parameters('ipFilterRuleName'))]",
            "type": "Microsoft.ServiceBus/Namespaces/IPFilterRules",
            "properties": {
                "FilterName":"[parameters('ipFilterRuleName')]",
                "Action":"[parameters('ipFilterAction')]",              
                "IpMask": "[parameters('IpMask')]"
            }
        } 
    ]
}
```

Aby wdrożyć szablon, postępuj zgodnie z instrukcjami dotyczącymi [usługi Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Kolejne kroki

Ograniczanie dostępu do usługi Service Bus, sieciami wirtualnymi platformy Azure zobacz następujące łącze:

- [Punkty końcowe usługi sieci wirtualnej dla usługi Service Bus][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services