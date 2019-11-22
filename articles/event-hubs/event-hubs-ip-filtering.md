---
title: Reguły zapory Event Hubs platformy Azure | Microsoft Docs
description: Użyj reguł zapory, aby zezwolić na połączenia z określonych adresów IP z platformą Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 12/06/2018
ms.author: spelluru
ms.openlocfilehash: 2350586501fae84726aa2aa2438ea676b90c1dbb
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279690"
---
# <a name="use-firewall-rules"></a>Korzystanie z reguł zapory

W przypadku scenariuszy, w których usługa Azure Event Hubs powinna być dostępna tylko z niektórych dobrze znanych lokacji, reguły zapory umożliwiają konfigurowanie reguł akceptowania ruchu pochodzącego z określonych adresów IPv4. Na przykład te adresy może być udostępnianych przez firmy bramy translatora adresów Sieciowych.

## <a name="when-to-use"></a>Kiedy stosować

Jeśli chcesz skonfigurować przestrzeń nazw Event Hubs w taki sposób, że powinna ona odbierać ruch z tylko określonego zakresu adresów IP i odrzucać wszystko inne, możesz użyć *reguły zapory* , aby zablokować punkty końcowe centrum zdarzeń z innych adresów IP. Na przykład jeśli używasz Event Hubs z usługą [Azure Express Route][express-route], możesz utworzyć *regułę zapory* , aby ograniczyć ruch z adresów IP infrastruktury lokalnej.

## <a name="how-filter-rules-are-applied"></a>Sposób stosowania reguły filtrowania

Reguły filtrowania adresów IP są stosowane na poziomie przestrzeni nazw usługi Event Hubs. W związku z tym zasady stosowane do wszystkich połączeń z klientami przy użyciu dowolnego obsługiwanego protokołu.

Wszystkie próby połączenia z adresu IP, które nie pasują do dozwolonej reguły adresów IP w przestrzeni nazw Event Hubs, są odrzucane jako nieautoryzowane. Odpowiedź nie mogą zawierać reguły adresów IP.

## <a name="default-setting"></a>Ustawienie domyślne

Domyślnie **filtru IP** siatki w portalu usługi Event Hubs jest pusty. To ustawienie domyślne oznacza, że Centrum zdarzeń akceptuje połączenia z dowolnego adresu IP. To ustawienie domyślne jest odpowiednikiem regułę, która akceptuje zakres adresów IP 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Ocenę reguł filtrowania adresów IP

Reguły filtrowania adresów IP są stosowane w kolejności, a pierwszej reguły, który jest zgodny z adresem IP określa akcji Zaakceptuj lub Odrzuć.

>[!WARNING]
> Zaimplementowanie zapór może uniemożliwić innym usługom platformy Azure współdziałanie z Event Hubs.
>
> Zaufane usługi firmy Microsoft nie są obsługiwane, gdy są implementowane filtrowanie adresów IP (zapory) i wkrótce zostaną udostępnione.
>
> Typowe scenariusze platformy Azure, które nie współpracują z filtrowaniem adresów IP (należy zauważyć, że lista **nie** jest wyczerpująca) —
> - Usługa Azure Stream Analytics
> - Integracja z usługą Azure Event Grid
> - Trasy usługi Azure IoT Hub
> - Device Explorer usługi Azure IoT
>
> Poniższe usługi firmy Microsoft muszą znajdować się w sieci wirtualnej
> - Azure Web Apps
> - Azure Functions

### <a name="creating-a-firewall-rule-with-azure-resource-manager-templates"></a>Tworzenie reguły zapory za pomocą szablonów Azure Resource Manager

> [!IMPORTANT]
> Reguły zapory są obsługiwane w warstwach **standardowa** i **dedykowana** Event Hubs. Nie jest obsługiwana w warstwie podstawowa.

Następujący szablon usługi Resource Manager umożliwia dodawanie reguły filtrowania adresów IP do istniejącej przestrzeni nazw usługi Event Hubs.

Parametry szablonu:

- **ipMask** jest pojedynczy adres IPv4 lub bloku adresów IP w notacji CIDR. Na przykład w CIDR 70.37.104.0/24 notacji reprezentuje 256 adresów IPv4 z 70.37.104.0 70.37.104.255 z 24 określającą liczbę bitów znaczące prefiks dla zakresu.

> [!NOTE]
> Chociaż nie ma możliwych reguł Odmów, szablon Azure Resource Manager ma ustawioną akcję domyślną **"Zezwalaj"** , która nie ogranicza połączeń.
> Podczas tworzenia reguł Virtual Network lub zapór należy zmienić wartość ***"DefaultAction"***
> 
> from
> ```json
> "defaultAction": "Allow"
> ```
> to
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
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
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
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Aby wdrożyć szablon, postępuj zgodnie z instrukcjami dotyczącymi [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Następne kroki

Ograniczanie dostępu do usługi Event Hubs sieciami wirtualnymi platformy Azure zobacz następujące łącze:

- [Virtual Network punkty końcowe usługi dla Event Hubs][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md
