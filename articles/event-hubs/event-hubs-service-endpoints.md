---
title: Usługa punkty końcowe sieci wirtualnej — usługa Azure Event Hubs | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje na temat dodawania punktu końcowego usługi elementu Microsoft.EventHub z siecią wirtualną.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 03/12/2019
ms.author: shvija
ms.openlocfilehash: 7b5a62f81238d1ae2b627c395613066350b36efe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60343447"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Punkty końcowe usługi sieci wirtualnej za pomocą usługi Azure Event Hubs

Integracja usługi Event Hubs za pomocą [punkty końcowe usługi Virtual Network (VNet)] [ vnet-sep] umożliwia bezpieczny dostęp do możliwości obsługi komunikatów z obciążeń, takich jak maszyny wirtualne, które są powiązane wirtualny sieci przy użyciu ścieżki ruchu sieciowego zabezpieczonego na obu końcach.

Po skonfigurowaniu do powiązany z co najmniej jeden punkt końcowy usługi podsieci sieci wirtualnej odpowiednich nazw usługi Event Hubs nie jest już akceptuje ruch z dowolnego miejsca, ale uprawnień podsieci w sieciach wirtualnych. Z perspektywy sieci wirtualnej powiązanie punktu końcowego usługi do przestrzeni nazw usługi Event Hubs służy do konfigurowania izolowanych sieci tunelu z podsieci sieci wirtualnej do obsługi komunikatów usługi. 

Wynik jest prywatne i izolowany relacji między obciążeniami usług związanych z podsieci i odpowiednich przestrzeni nazw usługi Event Hubs, pomimo adres sieciowy dostrzegalnych obsługi komunikatów usługi punktu końcowego są w zakresie publicznych adresów IP. Jest to wyjątek to zachowanie. Włączanie punktu końcowego usługi, domyślnie, włącza denyall reguły zapory adresów IP skojarzone z siecią wirtualną. Możesz dodać określone adresy IP w zapory adresów IP, aby umożliwić dostęp do publicznego punktu końcowego Centrum zdarzeń. 


>[!WARNING]
> Implementowanie integracji sieci wirtualnych może uniemożliwić innym usługom platformy Azure interakcję z usługą Event Hubs.
>
> Zaufane usługi nie są obsługiwane, gdy sieci wirtualne są implementowane firmy Microsoft.
>
> Typowe scenariusze platformy Azure, która nie działa z sieciami wirtualnymi (należy pamiętać, że lista jest **nie** wyczerpująca) —
> - Azure Monitor
> - Usługa Azure Stream Analytics
> - Integracja z usługą Azure Event Grid
> - Azure IoT Hub Routes
> - Usługa Azure IoT Device Explorer
> - Azure Data Explorer
>
> Poniżej Microsoft usług są wymagane w sieci wirtualnej
> - Azure Web Apps
> - Azure Functions

> [!IMPORTANT]
> Sieci wirtualne są obsługiwane w warstwach **Standardowa** i **Dedykowana** usługi Event Hubs. Nie są obsługiwane w warstwie Podstawowa.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Scenariusze zaawansowane zabezpieczenia obsługiwane przez Integracja sieci wirtualnej 

Rozwiązania, które wymagają bezpieczeństwa ścisłej i związane i gdzie podsieci sieci wirtualnej segmentacji między usługami compartmentalized nadal należy zaufanych ścieżek komunikacji między usługami znajdującymi się w tych przedziałów.

Wszelkie natychmiastowego trasy IP między przedziały, w tym te wartości protokołu HTTPS za pośrednictwem protokołu TCP/IP, niesie ze sobą ryzyko wykorzystania luk w zabezpieczeniach z warstwy sieciowej w górę. Usługi przesyłania komunikatów zapewniają zaufanych ścieżek komunikacji w pełni izolowane, gdzie komunikaty nawet są zapisywane na dysku, ponieważ ich przejść między stronami. Obciążeń z dwóch odrębnych sieci wirtualnych, które do tego samego wystąpienia usługi Event Hubs są powiązane mogą komunikować się sprawnie i niezawodnie za pośrednictwem wiadomości, gdy integralności granic izolacji sieci odpowiednich są zachowywane.
 
Oznacza to, że compartments bezpieczeństwa poufnych rozwiązań w chmurze nie tylko dostęp do możliwości platformy Azure wiodącą w branży niezawodne i skalowalne asynchronicznej obsługi komunikatów, ale mogą teraz używać wiadomości do tworzenie zaufanych ścieżek komunikacji między bezpiecznych rozwiązań, które są natury bezpieczniejszy niż co to jest osiągalna z dowolnym trybie komunikacji peer-to-peer, łącznie z protokołu HTTPS i innych protokołów gniazda korzystającej z protokołu TLS.

## <a name="bind-event-hubs-to-virtual-networks"></a>Powiązania usługi Event Hubs z sieciami wirtualnymi

*Reguły sieci wirtualnej* to funkcja zabezpieczeń zapory, która kontroluje, czy przestrzeń nazw usługi Azure Event Hubs akceptuje połączenia z podsieci określonej sieci wirtualnej.

Powiązania przestrzeni nazw usługi Event Hubs do sieci wirtualnej jest procesem dwuetapowym. Najpierw należy utworzyć **punkt końcowy usługi sieci wirtualnej** w podsieci sieci wirtualnej i włącz ją dla elementu "Microsoft.EventHub", jak wyjaśniono w [Przegląd punktów końcowych usługi] [ vnet-sep]. Po dodaniu punktu końcowego usługi, możesz powiązać przestrzeni nazw usługi Event Hubs za pomocą *reguły sieci wirtualnej*.

Reguła sieci wirtualnej jest skojarzeniem przestrzeni nazw usługi Event Hubs za pomocą podsieci sieci wirtualnej. Gdy istnieje reguła, wszystkie obciążenia związane z podsiecią uzyskują dostęp do przestrzeni nazw usługi Event Hubs. Usługa Event Hubs sam nigdy nie ustanawia wychodzące połączenia, nie trzeba uzyskać dostęp i w związku z tym nigdy nie uzyskuje dostęp do podsieci przez włączenie tej reguły.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Tworzenie reguły sieci wirtualnej za pomocą szablonów usługi Azure Resource Manager

Następujący szablon usługi Resource Manager umożliwia dodanie reguły sieci wirtualnej do istniejącej przestrzeni nazw usługi Event Hubs.

Parametry szablonu:

* **namespaceName**: Przestrzeń nazw usługi Event Hubs.
* **vnetRuleName**: Nazwa reguły sieci wirtualnej, która ma zostać utworzony.
* **virtualNetworkingSubnetId**: Pełną ścieżkę Menedżera zasobów w podsieci sieci wirtualnej; na przykład `subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` domyślne podsieci sieci wirtualnej.

> [!NOTE]
> Choć Brak reguł odmowy, możliwe, szablon usługi Azure Resource Manager jest domyślna akcja równa **"Zezwalaj"** która nie stanowi ograniczenia połączeń.
> Podczas tworzenia reguł sieci wirtualnej lub zapory, możemy zmienić ***"defaultAction"***
> 
> z
> ```json
> "defaultAction": "Allow"
> ```
> na
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
        }
      },
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.EventHub"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
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
