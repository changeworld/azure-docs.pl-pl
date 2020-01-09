---
title: Reguły zapory Azure Service Bus | Microsoft Docs
description: Jak używać reguł zapory do zezwalania na połączenia z określonych adresów IP do Azure Service Bus.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: 59afdb0e273511f3d8255a9c859b86f93e0b7269
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462410"
---
# <a name="azure-service-bus---use-firewall-rules"></a>Azure Service Bus — używanie reguł zapory

W przypadku scenariuszy, w których Azure Service Bus są dostępne tylko z niektórych dobrze znanych lokacji, reguły zapory umożliwiają skonfigurowanie reguł do akceptowania ruchu pochodzącego z określonych adresów IPv4. Na przykład te adresy może być udostępnianych przez firmy bramy translatora adresów Sieciowych.

## <a name="when-to-use"></a>Kiedy stosować

Jeśli chcesz skonfigurować Service Bus taki, że powinien on odbierać ruch tylko z określonego zakresu adresów IP i odrzucać wszystko inne, możesz użyć *zapory* do zablokowania Service Bus punktów końcowych z innych adresów IP. Na przykład w celu utworzenia prywatnych połączeń z infrastrukturą lokalną używasz Service Bus z usługą [Azure Express Route][express-route] . 

## <a name="how-filter-rules-are-applied"></a>Sposób stosowania reguły filtrowania

Reguły filtru IP są stosowane na poziomie przestrzeni nazw Service Bus. W związku z tym zasady stosowane do wszystkich połączeń z klientami przy użyciu dowolnego obsługiwanego protokołu.

Wszystkie próby połączenia z adresu IP, które nie pasują do dozwolonej reguły adresów IP w przestrzeni nazw Service Bus, są odrzucane jako nieautoryzowane. Odpowiedź nie mogą zawierać reguły adresów IP.

## <a name="default-setting"></a>Ustawienie domyślne

Domyślnie siatka **filtrów IP** w portalu dla Service Bus jest pusta. To ustawienie domyślne oznacza, że przestrzeń nazw akceptuje połączenia dowolnego adresu IP. To ustawienie domyślne jest odpowiednikiem regułę, która akceptuje zakres adresów IP 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Ocenę reguł filtrowania adresów IP

Reguły filtrowania adresów IP są stosowane w kolejności, a pierwsza reguła zgodna z adresem IP określa akcję Akceptuj lub Odrzuć.

>[!WARNING]
> Implementowanie reguł zapory może uniemożliwić innym usługom platformy Azure współdziałanie z Service Bus.
>
> Zaufane usługi firmy Microsoft nie są obsługiwane, gdy są implementowane filtrowanie adresów IP (reguły zapory) i wkrótce zostaną udostępnione.
>
> Typowe scenariusze platformy Azure, które nie współpracują z filtrowaniem adresów IP (należy zauważyć, że lista **nie** jest wyczerpująca) —
> - Azure Stream Analytics
> - Integracja z usługą Azure Event Grid
> - Trasy usługi Azure IoT Hub
> - Device Explorer usługi Azure IoT
>
> Poniższe usługi firmy Microsoft muszą znajdować się w sieci wirtualnej
> - Azure App Service
> - Stan usługi Funkcje Azure

### <a name="creating-a-virtual-network-and-firewall-rule-with-azure-resource-manager-templates"></a>Tworzenie sieci wirtualnej i reguły zapory za pomocą szablonów Azure Resource Manager

> [!IMPORTANT]
> Zapory i sieci wirtualne są obsługiwane tylko w warstwie **premium** Service Bus.

Poniższy szablon Menedżer zasobów umożliwia dodanie reguły sieci wirtualnej do istniejącej Service Bus przestrzeni nazw.

Parametry szablonu:

- **ipMask** jest pojedynczy adres IPv4 lub bloku adresów IP w notacji CIDR. Na przykład w CIDR 70.37.104.0/24 notacji reprezentuje 256 adresów IPv4 z 70.37.104.0 70.37.104.255 z 24 określającą liczbę bitów znaczące prefiks dla zakresu.

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
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
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
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
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

Aby ograniczyć dostęp do Service Bus do sieci wirtualnych platformy Azure, Skorzystaj z następującego linku:

- [Virtual Network punkty końcowe usługi dla Service Bus][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
