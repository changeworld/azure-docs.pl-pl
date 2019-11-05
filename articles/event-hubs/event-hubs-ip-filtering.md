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
ms.openlocfilehash: f96c25dbb85ed92141636487f10d861a8c5e5f28
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468426"
---
# <a name="use-firewall-rules"></a>Korzystanie z reguł zapory

W przypadku scenariuszy, w których usługa Azure Event Hubs powinna być dostępna tylko z niektórych dobrze znanych lokacji, reguły zapory umożliwiają konfigurowanie reguł akceptowania ruchu pochodzącego z określonych adresów IPv4. Na przykład te adresy mogą być tymi, które należą do firmowej bramy translatora adresów sieciowych.

## <a name="when-to-use"></a>Kiedy stosować

Jeśli chcesz skonfigurować przestrzeń nazw Event Hubs w taki sposób, że powinna ona odbierać ruch z tylko określonego zakresu adresów IP i odrzucać wszystko inne, możesz użyć *reguły zapory* , aby zablokować punkty końcowe centrum zdarzeń z innych adresów IP. Na przykład jeśli używasz Event Hubs z usługą [Azure Express Route][express-route], możesz utworzyć *regułę zapory* , aby ograniczyć ruch z adresów IP infrastruktury lokalnej.

## <a name="how-filter-rules-are-applied"></a>Jak są stosowane reguły filtrowania

Reguły filtru IP są stosowane na poziomie przestrzeni nazw Event Hubs. W związku z tym reguły są stosowane do wszystkich połączeń z klientów przy użyciu dowolnego obsługiwanego protokołu.

Wszystkie próby połączenia z adresu IP, które nie pasują do dozwolonej reguły adresów IP w przestrzeni nazw Event Hubs, są odrzucane jako nieautoryzowane. Odpowiedź nie zawiera wzmianki o regule adresów IP.

## <a name="default-setting"></a>Ustawienie domyślne

Domyślnie siatka **filtrów IP** w portalu dla Event Hubs jest pusta. To ustawienie domyślne oznacza, że centrum zdarzeń akceptuje połączenia z dowolnego adresu IP. To ustawienie domyślne jest równoważne z regułą akceptującą zakres adresów IP 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Obliczanie reguły filtru IP

Reguły filtrowania adresów IP są stosowane w podanej kolejności, a pierwsza reguła zgodna z adresem IP określa akcję Akceptuj lub Odrzuć.

>[!WARNING]
> Zaimplementowanie zapór może uniemożliwić innym usługom platformy Azure współdziałanie z Event Hubs.
>
> Zaufane usługi firmy Microsoft nie są obsługiwane, gdy są implementowane filtrowanie adresów IP (zapory) i wkrótce zostaną udostępnione.
>
> Typowe scenariusze platformy Azure, które nie współpracują z filtrowaniem adresów IP (należy zauważyć, że lista **nie** jest wyczerpująca) —
> - Azure Monitor
> - Azure Stream Analytics
> - Integracja z usługą Azure Event Grid
> - Trasy usługi Azure IoT Hub
> - Device Explorer usługi Azure IoT
>
> Poniższe usługi firmy Microsoft muszą znajdować się w sieci wirtualnej
> - Azure Web Apps
> - Azure Functions

### <a name="creating-a-firewall-rule-with-azure-resource-manager-templates"></a>Tworzenie reguły zapory za pomocą szablonów Azure Resource Manager

> [!IMPORTANT]
> Reguły zapory są obsługiwane w warstwach **standardowa** i **dedykowana** Event Hubs. Nie są obsługiwane w warstwie Podstawowa.

Poniższy szablon Menedżer zasobów umożliwia dodanie reguły filtru IP do istniejącej przestrzeni nazw Event Hubs.

Parametry szablonu:

- **ipMask** to pojedynczy adres IPv4 lub blok adresów IP w notacji CIDR. Na przykład w notacji CIDR 70.37.104.0/24 reprezentuje adresy IPv4 256 z 70.37.104.0 do 70.37.104.255, z 24 wskazujące liczbę znaczących bitów prefiksu dla zakresu.

> [!NOTE]
> Chociaż nie ma możliwych reguł Odmów, szablon Azure Resource Manager ma ustawioną akcję domyślną **"Zezwalaj"** , która nie ogranicza połączeń.
> Podczas tworzenia reguł Virtual Network lub zapór należy zmienić wartość ***"DefaultAction"***
> 
> wniosek
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

Aby ograniczyć dostęp do Event Hubs do sieci wirtualnych platformy Azure, Skorzystaj z następującego linku:

- [Virtual Network punkty końcowe usługi dla Event Hubs][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md
