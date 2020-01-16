---
title: Usługa punkty końcowe sieci wirtualnej — usługa Azure Event Hubs | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje na temat dodawania punktu końcowego usługi Microsoft. EventHub do sieci wirtualnej.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 11/26/2019
ms.author: shvija
ms.openlocfilehash: 2ac89444bde4e2efc918aced9d76c099eb792557
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75966009"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Punkty końcowe usługi sieci wirtualnej za pomocą usługi Azure Event Hubs

Integracja Event Hubs z [punktami końcowymi usługi Virtual Network (VNET)][vnet-sep] umożliwia bezpieczny dostęp do możliwości obsługi komunikatów z obciążeń, takich jak maszyny wirtualne powiązane z sieciami wirtualnymi, z zabezpieczoną ścieżką ruchu sieciowego na obu końcach.

Po skonfigurowaniu powiązania z co najmniej jednym punktem końcowym usługi podsieci sieci wirtualnej odpowiednia przestrzeń nazw Event Hubs nie akceptuje już ruchu z dowolnego miejsca, ale autoryzowanych podsieci w sieciach wirtualnych. Z perspektywy sieci wirtualnej powiązanie punktu końcowego usługi do przestrzeni nazw usługi Event Hubs służy do konfigurowania izolowanych sieci tunelu z podsieci sieci wirtualnej do obsługi komunikatów usługi. 

Wynik jest prywatne i izolowany relacji między obciążeniami usług związanych z podsieci i odpowiednich przestrzeni nazw usługi Event Hubs, pomimo adres sieciowy dostrzegalnych obsługi komunikatów usługi punktu końcowego są w zakresie publicznych adresów IP. Wystąpił wyjątek dotyczący tego zachowania. Domyślnie włączenie punktu końcowego usługi powoduje włączenie reguły denyall w zaporze IP skojarzonej z siecią wirtualną. Można dodać określone adresy IP w zaporze IP, aby umożliwić dostęp do publicznego punktu końcowego centrum zdarzeń. 


>[!WARNING]
> Implementowanie integracji sieci wirtualnych może uniemożliwić innym usługom platformy Azure interakcję z usługą Event Hubs.
>
> Zaufane usługi firmy Microsoft nie są obsługiwane w przypadku implementacji sieci wirtualnych.
>
> Typowe scenariusze platformy Azure, które nie współpracują z sieciami wirtualnymi (należy zauważyć, że lista **nie** jest wyczerpująca) —
> - Integracja z Azure Monitor. Nie można przesyłać strumieni diagnostycznych z **innych** usług platformy Azure do Event Hubs. Można jednak włączyć dzienniki diagnostyczne platformy Azure w centrum zdarzeń. Jest to taka sama sytuacja, gdy jest włączona Zapora (filtrowanie adresów IP).
> - Azure Stream Analytics
> - Integracja z usługą Azure Event Grid
> - Trasy usługi Azure IoT Hub
> - Device Explorer usługi Azure IoT
>
> Poniższe usługi firmy Microsoft muszą znajdować się w sieci wirtualnej
> - Azure Web Apps
> - Stan usługi Funkcje Azure

> [!IMPORTANT]
> Sieci wirtualne są obsługiwane w warstwach **Standardowa** i **Dedykowana** usługi Event Hubs. Nie są obsługiwane w warstwie Podstawowa.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Scenariusze zaawansowane zabezpieczenia obsługiwane przez Integracja sieci wirtualnej 

Rozwiązania, które wymagają ścisłych i compartmentalized zabezpieczeń, a wirtualne podsieci sieci zapewniają segmentację między usługami compartmentalized, nadal muszą mieć ścieżki komunikacyjne między usługami znajdującymi się w tych przedziałach.

Wszelkie natychmiastowego trasy IP między przedziały, w tym te wartości protokołu HTTPS za pośrednictwem protokołu TCP/IP, niesie ze sobą ryzyko wykorzystania luk w zabezpieczeniach z warstwy sieciowej w górę. Usługi przesyłania komunikatów zapewniają zaufanych ścieżek komunikacji w pełni izolowane, gdzie komunikaty nawet są zapisywane na dysku, ponieważ ich przejść między stronami. Obciążeń z dwóch odrębnych sieci wirtualnych, które do tego samego wystąpienia usługi Event Hubs są powiązane mogą komunikować się sprawnie i niezawodnie za pośrednictwem wiadomości, gdy integralności granic izolacji sieci odpowiednich są zachowywane.
 
Oznacza to, że compartments bezpieczeństwa poufnych rozwiązań w chmurze nie tylko dostęp do możliwości platformy Azure wiodącą w branży niezawodne i skalowalne asynchronicznej obsługi komunikatów, ale mogą teraz używać wiadomości do tworzenie zaufanych ścieżek komunikacji między bezpiecznych rozwiązań, które są natury bezpieczniejszy niż co to jest osiągalna z dowolnym trybie komunikacji peer-to-peer, łącznie z protokołu HTTPS i innych protokołów gniazda korzystającej z protokołu TLS.

## <a name="bind-event-hubs-to-virtual-networks"></a>Powiązania usługi Event Hubs z sieciami wirtualnymi

*Reguły sieci wirtualnej* to funkcja zabezpieczeń zapory, która kontroluje, czy przestrzeń nazw usługi Azure Event Hubs akceptuje połączenia z podsieci określonej sieci wirtualnej.

Powiązania przestrzeni nazw usługi Event Hubs do sieci wirtualnej jest procesem dwuetapowym. Najpierw należy utworzyć **punkt końcowy usługi Virtual Network** w podsieci Virtual Network i włączyć go dla elementu "Microsoft. EventHub" zgodnie z opisem w temacie [Omówienie punktu końcowego usługi][vnet-sep]. Po dodaniu punktu końcowego usługi, możesz powiązać przestrzeni nazw usługi Event Hubs za pomocą *reguły sieci wirtualnej*.

Reguła sieci wirtualnej jest skojarzeniem przestrzeni nazw Event Hubs z podsiecią sieci wirtualnej. Gdy istnieje reguła, wszystkie obciążenia związane z podsiecią uzyskują dostęp do przestrzeni nazw usługi Event Hubs. Usługa Event Hubs sam nigdy nie ustanawia wychodzące połączenia, nie trzeba uzyskać dostęp i w związku z tym nigdy nie uzyskuje dostęp do podsieci przez włączenie tej reguły.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Tworzenie reguły sieci wirtualnej za pomocą szablonów usługi Azure Resource Manager

Następujący szablon usługi Resource Manager umożliwia dodanie reguły sieci wirtualnej do istniejącej przestrzeni nazw usługi Event Hubs.

Parametry szablonu:

* **namespaceName**: przestrzeń nazw usługi Event Hubs.
* **vnetRuleName**: nazwę reguły sieci wirtualnej ma zostać utworzony.
* **virtualNetworkingSubnetId**: pełną ścieżkę Menedżera zasobów w podsieci sieci wirtualnej; na przykład `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` domyślne podsieci sieci wirtualnej.

> [!NOTE]
> Chociaż nie ma możliwych reguł Odmów, szablon Azure Resource Manager ma ustawioną akcję domyślną **"Zezwalaj"** , która nie ogranicza połączeń.
> Podczas tworzenia reguł Virtual Network lub zapór należy zmienić wartość ***"DefaultAction"***
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

Aby wdrożyć szablon, postępuj zgodnie z instrukcjami dotyczącymi [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat sieci wirtualnych zobacz następujące linki:

- [Punkty końcowe usługi sieci wirtualnej platformy Azure][vnet-sep]
- [Filtrowanie adresów IP Event Hubs platformy Azure][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
