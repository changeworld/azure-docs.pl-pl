---
title: Filtry połączenia IP magistrali usługi Azure | Dokumentacja firmy Microsoft
description: Jak używać IP filtrowania bloku połączeń z określonych adresów IP do usługi Azure Service Bus.
services: service-bus
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: clemensv
ms.openlocfilehash: e009bb9120fafc6edf60b68fab3336b9d1add507
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036066"
---
# <a name="use-ip-filters"></a>Używanie filtrów IP

W scenariuszach, w których usługi Azure Service Bus jest dostępna tylko w pewnych dobrze znanych witryn *filtrów IP* funkcja umożliwia konfigurowanie reguł odrzucenia lub akceptowania ruchu pochodzącego z określonych adresów IPv4. Na przykład te adresy można te firmy bramy translatora adresów Sieciowych.

## <a name="when-to-use"></a>Kiedy stosować

Istnieją dwa przypadków użycia określonego, w których jest przydatna do blokowania punktów końcowych usługi Service Bus dla określonych adresów IP:

- Usługi Service Bus powinny odbierać dane tylko z określonego zakresu adresów IP i odrzucić wszystkie inne elementy. Na przykład używasz usługi Service Bus z [Azure Express Route] [ express-route] do tworzenia prywatnej połączeń z infrastruktury lokalnej.
- Należy odrzucić ruch sieciowy z adresów IP, które zostały zidentyfikowane jako podejrzane przez administratora usługi Service Bus.

## <a name="how-filter-rules-are-applied"></a>Sposób stosowania reguły filtrowania

Reguły filtru IP są stosowane na poziomie przestrzeni nazw usługi Service Bus. W związku z tym zasady stosowane do wszystkich połączeń z klientami za pomocą obsługiwanych protokołów.

Każda próba połączenia z nieautoryzowanym dopasowań odrzucenia wydzielenia reguły IP w przestrzeni nazw usługi Service Bus jako adres IP. Odpowiedź nie mogą zawierać reguły IP.

## <a name="default-setting"></a>Ustawienie domyślne

Domyślnie **filtrów IP** siatki w portalu dla usługi Service Bus jest pusta. To ustawienie domyślne oznacza, że przestrzeń nazw akceptuje połączenia dowolnego adresu IP. To domyślne ustawienie jest odpowiednikiem regułę, która akceptuje 0.0.0.0/0 zakres adresów IP.

## <a name="ip-filter-rule-evaluation"></a>Szacowanie reguły filtru adresu IP

Reguły filtru IP są stosowane w kolejności, pierwszej reguły, który odpowiada adresowi IP określa akcji Zaakceptuj lub Odrzuć.

Na przykład jeśli chcesz zaakceptować adresów w zakresie 70.37.104.0/24 i odrzucić wszystkie inne pierwszą regułę w siatce powinna obsługiwać 70.37.104.0/24 zakresu adresów. Reguły następnej Odrzuć wszystkie adresy przy użyciu 0.0.0.0/0 zakresu.

> [!NOTE]
> Odrzucanie adresów IP można zapobiec interakcji z usługą Service Bus innymi usługami Azure (np. Azure Stream Analytics, maszynach wirtualnych platformy Azure lub Explorer urządzenia w portalu).

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Tworzenie reguły sieci wirtualnej przy użyciu szablonów usługi Azure Resource Manager

Następujący szablon usługi Resource Manager umożliwia dodanie reguły sieci wirtualnej do istniejącej przestrzeni nazw usługi Service Bus.

Parametry szablonu:

- **ipFilterRuleName** musi być unikatowy, bez uwzględniania wielkości liter, alfanumeryczne ciągu maksymalnie 128 znaków.
- **ipFilterAction** jest **Odrzuć** lub **Akceptuj** jako akcja w celu zastosowania reguły filtru adresu IP.
- **ipMask** jest pojedynczy adres IPv4 lub blok adresów IP w notacji CIDR. Na przykład w CIDR 70.37.104.0/24 notacji reprezentuje 256 adresy IPv4 z 70.37.104.0 70.37.104.255 z 24 wskazującą liczbę bitów znaczących prefiks zakresu.

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

Aby wdrożyć szablon, postępuj zgodnie z instrukcjami dla [usługi Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Kolejne kroki

Ograniczanie dostępu do usługi Service Bus do sieci wirtualnych platformy Azure zobacz następujące łącze:

- [Punkty końcowe usługi sieci wirtualnej dla usługi Service Bus][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services