---
title: Punkty końcowe usługi sieci wirtualnej i zasady usługi Azure Service Bus | Dokumentacja firmy Microsoft
description: Dodaj punkt końcowy usługi Microsoft.ServiceBus do sieci wirtualnej.
services: event-hubs
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: clemensv
ms.openlocfilehash: 7716ff503bd492cc4b5d510758cb20d74eb82a4f
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036715"
---
# <a name="use-virtual-network-service-endpoints-with-azure-service-bus"></a>Punkty końcowe usługi sieci wirtualnej za pomocą usługi Azure Service Bus

Integracja usługi Service Bus z [punktów końcowych usługi Virtual Network (VNet)] [ vnet-sep] umożliwia bezpieczny dostęp do możliwości obsługi komunikatów z obciążeń, takich jak maszyny wirtualne, które są powiązane z siecią wirtualną , ze ścieżką ruchu sieciowego są zabezpieczone po obu stronach. 

Po skonfigurowaniu może być powiązane z co najmniej jeden punkt końcowy usługi podsieci sieci wirtualnej odpowiednich przestrzeń nazw magistrali usług nie będzie akceptował ruchu z dowolnego miejsca, ale uprawnień sieci wirtualnej. Z perspektywy sieci wirtualnej powiązanie dla punktu końcowego przestrzeni nazw usługi Service Bus konfiguruje izolowane sieci tunelu w podsieci sieci wirtualnej z usługą obsługi wiadomości.

Wynik jest prywatny i izolowane relację między obciążeń związanych z podsieci i odpowiednich nazw usługi Service Bus, mimo adres sieciowy zauważalne obsługi komunikatów usługi punktu końcowego są w zakresie publicznego adresu IP.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Scenariusze zaawansowane zabezpieczenia obsługiwane przez integrację sieci wirtualnej 

Rozwiązania wymagające zabezpieczeń ścisłej i związane i gdy podsieci sieci wirtualnej zapewniają segmentacji między usługami compartmentalized, zazwyczaj nadal konieczne ścieżki komunikacji między usługami znajdującej się w tych przedziałów.

Wszelkie natychmiastowego trasy IP między przedziałów, w tym te wartości HTTPS za pośrednictwem protokołu TCP/IP, niesie ryzyko wykorzystania luk w zabezpieczeniach z warstwy sieci w górę. Moduł obsługi Podaj ścieżki komunikacji całkowicie izolowanych, w których wiadomości nawet są zapisywane na dysku, zgodnie z ich przechodzenie między stronami. Obciążeń w dwóch odrębnych sieci wirtualnych znajdujących się w tym samym wystąpieniu usługi Service Bus są powiązane mogą komunikować się wydajne i niezawodne za pośrednictwem wiadomości, gdy integralności granic izolacji sieci odpowiednich są zachowywane.
 
Oznacza to, że compartments bezpieczeństwa poufnych rozwiązań w chmurze nie tylko dostęp do platformy Azure w branży niezawodnych i skalowalne asynchroniczne możliwości obsługi komunikatów, ale mogą teraz przy użyciu wiadomości do tworzenia ścieżek komunikacji między bezpieczne rozwiązanie, które są z założenia bezpieczniejsze niż jest to poprzez dowolny tryb komunikacji peer-to-peer, łącznie z protokołu HTTPS i innych protokołów zabezpieczonego protokołem TLS gniazda.

## <a name="binding-service-bus-to-virtual-networks"></a>Powiązanie usługi Service Bus do sieci wirtualnych

*Zasady sieci wirtualnej* to funkcja zabezpieczeń zapory, która kontroluje, czy serwer usługi Azure Service Bus akceptuje połączenia z podsiecią określonej sieci wirtualnej.

Powiązanie nazw usługi Service Bus do sieci wirtualnej jest procesem dwuetapowym. Najpierw należy utworzyć **punkt końcowy usługi sieci wirtualnej** w podsieci sieci wirtualnej i włącz go do "Microsoft.ServiceBus", jak wyjaśniono w [omówienie punktu końcowego usługi] [ vnet-sep]. Po dodaniu punktu końcowego usługi można powiązać przestrzeni nazw usługi Service Bus za pomocą *reguły sieci wirtualnej*.

Reguła sieci wirtualnej jest skojarzenie nazwanych przestrzeni nazw usługi Service Bus z podsiecią sieci wirtualnej. Gdy istnieje reguła, wszystkie obciążenia związane z podsiecią udzielany jest dostęp do przestrzeni nazw usługi Service Bus. Usługi Service Bus sama nigdy nie ustanawia wychodzące połączenie, nie trzeba uzyskać dostęp i jest w związku z tym nigdy nie uzyskuje dostęp do podsieci, należy włączyć tę regułę.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Tworzenie reguły sieci wirtualnej przy użyciu szablonów usługi Azure Resource Manager

Następujący szablon usługi Resource Manager umożliwia dodanie reguły sieci wirtualnej do istniejącej przestrzeni nazw usługi Service Bus.

Parametry szablonu:

* **Nazwa przestrzeni nazw**: przestrzeń nazw magistrali usług.
* **vnetRuleName**: nazwę reguły sieć wirtualna ma zostać utworzony.
* **virtualNetworkingSubnetId**: w pełni kwalifikowana Menedżera zasobów dla podsieci sieci wirtualnej; na przykład `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` domyślne podsieci sieci wirtualnej.

Szablon:

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
            "type":"Microsoft.ServiceBus/namespaces/VirtualNetworkRules",           
            "properties": {             
                "virtualNetworkSubnetId": "[parameters('virtualNetworkSubnetId')]"  
            }
        } 
    ]
}
```

Aby wdrożyć szablon, postępuj zgodnie z instrukcjami dla [usługi Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o sieciach wirtualnych zobacz następujące linki:

- [Punkty końcowe usługi sieci wirtualnej platformy Azure][vnet-sep]
- [Filtrowanie Azure Service Bus IP][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: service-bus-ip-filtering.md