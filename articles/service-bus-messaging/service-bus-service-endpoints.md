---
title: Punkty końcowe usługi sieci wirtualnej i zasady usługi Azure Service Bus | Dokumentacja firmy Microsoft
description: Dodaj punkt końcowy usługi elementu Microsoft.ServiceBus z siecią wirtualną.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: aschhab
ms.openlocfilehash: 0801469d586e6f2d6514927cdc7b894900a3aa35
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61471965"
---
# <a name="use-virtual-network-service-endpoints-with-azure-service-bus"></a>Punkty końcowe usługi sieci wirtualnej za pomocą usługi Azure Service Bus

Integracja usługi Service Bus za pomocą [punkty końcowe usługi Virtual Network (VNet)] [ vnet-sep] umożliwia bezpieczny dostęp do możliwości obsługi komunikatów z obciążeń, takie jak maszyny wirtualne, które są powiązane z sieciami wirtualnymi , przy użyciu ścieżki ruchu sieciowego zabezpieczonego na obu końcach.

Po skonfigurowaniu, może być powiązane z co najmniej jeden punkt końcowy usługi podsieci sieci wirtualnej, odpowiednich przestrzeni nazw usługi Service Bus nie będzie już akceptować ruch z dowolnego miejsca, ale uprawnień sieci wirtualnych. Z perspektywy sieci wirtualnej powiązanie przestrzeni nazw usługi Service Bus z punktu końcowego usługi służy do konfigurowania izolowanych sieci tunelu z podsieci sieci wirtualnej do obsługi wiadomości usługi.

Wynik jest prywatne i izolowany relacji między obciążeniami usług związanych z podsieci i odpowiednich przestrzeni nazw usługi Service Bus, pomimo adres sieciowy dostrzegalnych obsługi komunikatów usługi punktu końcowego są w zakresie publicznych adresów IP.

>[!WARNING]
> Implementowanie integracji sieci wirtualnych może uniemożliwić innym usługom platformy Azure interakcję z usługą Service Bus.
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
> - Usługa Azure App Service
> - Azure Functions

> [!IMPORTANT]
> Sieci wirtualne są obsługiwane tylko w [w warstwie Premium](service-bus-premium-messaging.md) przestrzeni nazw usługi Service Bus.

## <a name="enable-service-endpoints-with-service-bus"></a>Włączyć punkty końcowe usługi z usługą Service Bus

Ważną kwestią w przypadku korzystania z punktów końcowych usługi sieci wirtualnej z usługą Service Bus jest, nie należy włączać tych punktów końcowych w aplikacji, które mieszać przestrzeni nazw usługi Service Bus warstwy standardowa i Premium. Ponieważ w warstwie standardowa nie obsługuje sieci wirtualne, punkt końcowy jest ograniczona do Premium przestrzeni nazw w warstwie tylko.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Scenariusze zaawansowane zabezpieczenia obsługiwane przez Integracja sieci wirtualnej 

Rozwiązania, które wymagają bezpieczeństwa ścisłej i związane i gdzie podsieci sieci wirtualnej segmentacji między usługami compartmentalized ogólnie rzecz biorąc nadal potrzebują zaufanych ścieżek komunikacji między usługami znajdującymi się w tych przedziałów.

Wszelkie natychmiastowego trasy IP między przedziały, w tym te wartości protokołu HTTPS za pośrednictwem protokołu TCP/IP, niesie ze sobą ryzyko wykorzystania luk w zabezpieczeniach z warstwy sieciowej w górę. Usługi przesyłania komunikatów zapewniają zaufanych ścieżek komunikacji w pełni izolowane, gdzie komunikaty nawet są zapisywane na dysku, ponieważ ich przejść między stronami. Obciążeń z dwóch odrębnych sieci wirtualnych, które to samo wystąpienie usługi Service Bus są powiązane mogą komunikować się sprawnie i niezawodnie za pośrednictwem wiadomości, gdy integralności granic izolacji sieci odpowiednich są zachowywane.
 
Oznacza to, że compartments bezpieczeństwa poufnych rozwiązań w chmurze nie tylko dostęp do możliwości platformy Azure wiodącą w branży niezawodne i skalowalne asynchronicznej obsługi komunikatów, ale mogą teraz używać wiadomości do tworzenie zaufanych ścieżek komunikacji między bezpiecznych rozwiązań, które są natury bezpieczniejszy niż co to jest osiągalna z dowolnym trybie komunikacji peer-to-peer, łącznie z protokołu HTTPS i innych protokołów gniazda korzystającej z protokołu TLS.

## <a name="binding-service-bus-to-virtual-networks"></a>Powiązania usługi Service Bus z sieciami wirtualnymi

*Reguły sieci wirtualnej* to funkcja zabezpieczeń zapory, która kontroluje, czy serwer usługi Azure Service Bus akceptuje połączenia z podsieci określonej sieci wirtualnej.

Powiązania przestrzeni nazw usługi Service Bus z siecią wirtualną jest procesem dwuetapowym. Najpierw należy utworzyć **punkt końcowy usługi sieci wirtualnej** w podsieci sieci wirtualnej i włącz ją dla elementu "Microsoft.ServiceBus", jak wyjaśniono w [Przegląd punktów końcowych usługi] [ vnet-sep]. Po dodaniu punktu końcowego usługi, możesz powiązać przestrzeni nazw usługi Service Bus za pomocą *reguły sieci wirtualnej*.

Reguła sieci wirtualnej jest skojarzenie przestrzeni nazw usługi Service Bus z podsieci sieci wirtualnej. Gdy istnieje reguła, wszystkie obciążenia związane z podsiecią uzyskują dostęp do przestrzeni nazw usługi Service Bus. Usługi Service Bus sama nigdy nie nawiązuje połączenia wychodzące, nie trzeba uzyskać dostęp i w związku z tym nigdy nie udzielany jest dostęp umożliwiający podsieci, należy włączyć tę regułę.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Tworzenie reguły sieci wirtualnej przy użyciu szablonów usługi Azure Resource Manager

Następujący szablon usługi Resource Manager umożliwia dodanie reguły sieci wirtualnej do istniejącej przestrzeni nazw usługi Service Bus.

Parametry szablonu:

* **namespaceName**: Przestrzeń nazw usługi Service Bus.
* **virtualNetworkingSubnetId**: Pełną ścieżkę Menedżera zasobów w podsieci sieci wirtualnej; na przykład `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` domyślne podsieci sieci wirtualnej.

> [!NOTE]
> Choć Brak reguł odmowy, możliwe, szablon usługi Azure Resource Manager jest domyślna akcja równa **"Zezwalaj"** która nie stanowi ograniczenia połączeń.
> Podczas tworzenia reguł sieci wirtualnej lub zapory, możemy zmienić ***"defaultAction"***
> 
> from
> ```json
> "defaultAction": "Allow"
> ```
> na
> ```json
> "defaultAction": "Deny"
> ```
>

Szablon:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
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
                    "service": "Microsoft.ServiceBus"
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
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
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
- [Filtrowanie IP magistrali usługi platformy Azure][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: service-bus-ip-filtering.md
