---
title: Punkty końcowe usługi sieci wirtualnej i zasady usługi Azure Event Hubs | Dokumentacja firmy Microsoft
description: Dodaj punkt końcowy usługi elementu Microsoft.EventHub z siecią wirtualną.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: 29b5f877065029dc271e49c1afd6d547def58a6e
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2018
ms.locfileid: "49408136"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Punkty końcowe usługi sieci wirtualnej za pomocą usługi Azure Event Hubs

Integracja usługi Event Hubs za pomocą [punkty końcowe usługi Virtual Network (VNet)] [ vnet-sep] umożliwia bezpieczny dostęp do możliwości obsługi komunikatów z obciążeń, takich jak maszyny wirtualne, które są powiązane wirtualny sieci przy użyciu ścieżki ruchu sieciowego zabezpieczonego na obu końcach. 

> [!IMPORTANT]
> Sieci wirtualne są obsługiwane w **standardowa** i **dedykowanych** warstw usługi Event Hubs. Nie jest obsługiwana w warstwie podstawowa. 

Po skonfigurowaniu, może być powiązane z co najmniej jeden punkt końcowy usługi podsieci sieci wirtualnej, odpowiednich nazw usługi Event Hubs nie jest już akceptuje ruch z dowolnego miejsca, ale uprawnień podsieci w sieciach wirtualnych. Z perspektywy sieci wirtualnej powiązanie punktu końcowego usługi do przestrzeni nazw usługi Event Hubs służy do konfigurowania izolowanych sieci tunelu z podsieci sieci wirtualnej do obsługi komunikatów usługi.

Wynik jest prywatne i izolowany relacji między obciążeniami usług związanych z podsieci i odpowiednich przestrzeni nazw usługi Event Hubs, pomimo adres sieciowy dostrzegalnych obsługi komunikatów usługi punktu końcowego są w zakresie publicznych adresów IP.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Scenariusze zaawansowane zabezpieczenia obsługiwane przez Integracja sieci wirtualnej 

Rozwiązania, które wymagają bezpieczeństwa ścisłej i związane i gdzie podsieci sieci wirtualnej segmentacji między usługami compartmentalized ogólnie rzecz biorąc nadal potrzebują zaufanych ścieżek komunikacji między usługami znajdującymi się w tych przedziałów.

Wszelkie natychmiastowego trasy IP między przedziały, w tym te wartości protokołu HTTPS za pośrednictwem protokołu TCP/IP, niesie ze sobą ryzyko wykorzystania luk w zabezpieczeniach z warstwy sieciowej w górę. Usługi przesyłania komunikatów zapewniają zaufanych ścieżek komunikacji w pełni izolowane, gdzie komunikaty nawet są zapisywane na dysku, ponieważ ich przejść między stronami. Obciążeń z dwóch odrębnych sieci wirtualnych, które do tego samego wystąpienia usługi Event Hubs są powiązane mogą komunikować się sprawnie i niezawodnie za pośrednictwem wiadomości, gdy integralności granic izolacji sieci odpowiednich są zachowywane.
 
Oznacza to, że compartments bezpieczeństwa poufnych rozwiązań w chmurze nie tylko dostęp do możliwości platformy Azure wiodącą w branży niezawodne i skalowalne asynchronicznej obsługi komunikatów, ale mogą teraz używać wiadomości do tworzenie zaufanych ścieżek komunikacji między bezpiecznych rozwiązań, które są natury bezpieczniejszy niż co to jest osiągalna z dowolnym trybie komunikacji peer-to-peer, łącznie z protokołu HTTPS i innych protokołów gniazda korzystającej z protokołu TLS.

## <a name="bind-event-hubs-to-virtual-networks"></a>Powiązania usługi Event Hubs z sieciami wirtualnymi

*Reguły sieci wirtualnej* to funkcja zabezpieczeń zapory, która kontroluje, czy przestrzeń nazw usługi Azure Event Hubs akceptuje połączenia z podsieci określonej sieci wirtualnej.

Powiązania przestrzeni nazw usługi Event Hubs do sieci wirtualnej jest procesem dwuetapowym. Najpierw należy utworzyć **punkt końcowy usługi sieci wirtualnej** w podsieci sieci wirtualnej i włącz ją dla elementu "Microsoft.EventHub", jak wyjaśniono w [Przegląd punktów końcowych usługi] [ vnet-sep]. Po dodaniu punktu końcowego usługi, możesz powiązać przestrzeni nazw usługi Event Hubs za pomocą *reguły sieci wirtualnej*.

Reguła sieci wirtualnej jest skojarzeniem o nazwie przestrzeni nazw usługi Event Hubs za pomocą podsieci sieci wirtualnej. Gdy istnieje reguła, wszystkie obciążenia związane z podsiecią uzyskują dostęp do przestrzeni nazw usługi Event Hubs. Usługa Event Hubs sam nigdy nie ustanawia wychodzące połączenia, nie trzeba uzyskać dostęp i w związku z tym nigdy nie uzyskuje dostęp do podsieci przez włączenie tej reguły.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Tworzenie reguły sieci wirtualnej za pomocą szablonów usługi Azure Resource Manager

Następujący szablon usługi Resource Manager umożliwia dodanie reguły sieci wirtualnej do istniejącej przestrzeni nazw usługi Event Hubs.

Parametry szablonu:

* **namespaceName**: przestrzeń nazw usługi Event Hubs.
* **vnetRuleName**: nazwę reguły sieci wirtualnej ma zostać utworzony.
* **virtualNetworkingSubnetId**: pełną ścieżkę Menedżera zasobów w podsieci sieci wirtualnej; na przykład `subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` domyślne podsieci sieci wirtualnej.

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
          "vnetRuleName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the Authorization rule"
             }
          },
          "virtualNetworkSubnetId":{  
             "type":"string",
             "metadata":{  
                "description":"subnet Azure Resource Manager ID"
             }
          }
      },
    "resources": [
        {
            "apiVersion": "2018-01-01-preview",
            "name": "[concat(parameters('namespaceName'), '/', parameters('vnetRuleName'))]",
            "type":"Microsoft.EventHub/namespaces/VirtualNetworkRules",         
            "properties": {             
                "virtualNetworkSubnetId": "[parameters('virtualNetworkSubnetId')]"  
            }
        } 
    ]
}
```

Aby wdrożyć szablon, postępuj zgodnie z instrukcjami dotyczącymi [usługi Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat sieci wirtualnych zobacz następujące linki:

- [Punkty końcowe usługi sieci wirtualnej platformy Azure][vnet-sep]
- [Filtrowanie adresów IP centrów zdarzeń platformy Azure][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: event-hubs-ip-filtering.md