---
title: Azure Event Hubs IP połączenia filtrów | Dokumentacja firmy Microsoft
description: Użyj filtrowania z określonych adresów IP do usługi Azure Event Hubs w celu połączenia bloków adresów IP.
services: event-hubs
documentationcenter: ''
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: spelluru
ms.openlocfilehash: c229a6f84096ecca892b74f7ce65cb831fa50be3
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886181"
---
# <a name="use-ip-filters"></a>Użyj filtrów IP

W scenariuszach, w których usługa Azure Event Hubs jest dostępna tylko w pewnych dobrze znanych witryn *filtru IP* funkcja umożliwia skonfigurowanie reguł dla odrzuca lub akceptowanie ruchu pochodzącego z określonych adresów IPv4. Na przykład te adresy może być udostępnianych przez firmy bramy translatora adresów Sieciowych.

## <a name="when-to-use"></a>Kiedy stosować

Dwoma ważnymi Użyj przypadki, w których jest przydatne blokowanie punktów końcowych usługi Event Hubs w przypadku niektórych adresów IP są następujące:

- Usługi event hubs powinny odbierać dane tylko z określonego zakresu adresów IP i odrzucić wszystkie inne elementy. Na przykład używasz usługi Event Hubs za pomocą [Azure Express Route] [ express-route] na tworzenie prywatnych połączeń w infrastrukturze lokalnej. 
- Należy odrzucić ruch z adresów IP, które zostały zidentyfikowane jako podejrzane przez administratora usługi Event Hubs.

## <a name="how-filter-rules-are-applied"></a>Sposób stosowania reguły filtrowania

Reguły filtrowania adresów IP są stosowane na poziomie przestrzeni nazw usługi Event Hubs. W związku z tym zasady stosowane do wszystkich połączeń z klientami przy użyciu dowolnego obsługiwanego protokołu.

Każda próba połączenia z adresu IP, który nieautoryzowane dopasowania, odrzucenia wydzielenia reguły adresów IP w przestrzeni nazw usługi Event Hubs jako. Odpowiedź nie mogą zawierać reguły adresów IP.

## <a name="default-setting"></a>Ustawienie domyślne

Domyślnie **filtru IP** siatki w portalu usługi Event Hubs jest pusty. To ustawienie domyślne oznacza, że Centrum zdarzeń akceptuje połączenia z dowolnego adresu IP. To ustawienie domyślne jest odpowiednikiem regułę, która akceptuje zakres adresów IP 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Ocenę reguł filtrowania adresów IP

Reguły filtrowania adresów IP są stosowane w kolejności, a pierwszej reguły, który jest zgodny z adresem IP określa akcji Zaakceptuj lub Odrzuć.

Na przykład jeśli chcesz zaakceptować adresów w 70.37.104.0/24 zakresu i odrzucić wszystkie inne elementy, pierwszą regułę w siatce powinna obsługiwać 70.37.104.0/24 zakresu adresów. Następną regułę należy odrzucić wszystkie adresy, używając 0.0.0.0/0 zakresu.

> [!NOTE]
> Odrzuca adresy IP można zapobiec interakcji z usługą Event Hubs innych usług platformy Azure (np. usługi Azure Stream Analytics, Azure Virtual Machines lub Device Explorer w portalu).

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Tworzenie reguły sieci wirtualnej przy użyciu szablonów usługi Azure Resource Manager

> [!IMPORTANT]
> Sieci wirtualne są obsługiwane w **standardowa** i **dedykowanych** warstw usługi Event Hubs. Nie jest obsługiwana w warstwie podstawowa. 

Następujący szablon usługi Resource Manager umożliwia dodanie reguły sieci wirtualnej do istniejącej przestrzeni nazw usługi Event Hubs.

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
            "type": "Microsoft.EventHub/Namespaces/IPFilterRules",
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

Ograniczanie dostępu do usługi Event Hubs sieciami wirtualnymi platformy Azure zobacz następujące łącze:

- [Punkty końcowe usługi sieci wirtualnej dla usługi Event Hubs][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md