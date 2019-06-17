---
title: Reguły zapory usługi Azure Event Hubs | Dokumentacja firmy Microsoft
description: Użyj reguł zapory zezwala na połączenia z określonych adresów IP usługi Azure Event Hubs.
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
ms.openlocfilehash: ccb2fa7b0805b332957513c52c0c1051d068d2cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60821674"
---
# <a name="use-firewall-rules"></a>Użyj reguł zapory

W przypadku scenariuszy, w których usługa Azure Event Hubs powinny być tylko dostępne z określonych witryn dobrze znanych reguł zapory umożliwiają skonfigurowanie reguły do akceptowania ruchu pochodzącego z określonych adresów IPv4. Na przykład te adresy może być udostępnianych przez firmy bramy translatora adresów Sieciowych.

## <a name="when-to-use"></a>Kiedy stosować

Jeśli chcesz skonfigurować przestrzeni nazw usługi Event Hubs takie, które klient powinien otrzymać ruchu z tylko określony zakres adresów IP i odrzucić wszystkie inne elementy, a następnie można wykorzystać *reguły zapory* blokowanie punktów końcowych Centrum zdarzeń, z inne adresy IP. Na przykład, jeśli korzystasz z usługi Event Hubs za pomocą [Azure Express Route][express-route], możesz utworzyć *reguły zapory* do ograniczania ruchu z lokalnej infrastruktury IP adresy.

## <a name="how-filter-rules-are-applied"></a>Sposób stosowania reguły filtrowania

Reguły filtrowania adresów IP są stosowane na poziomie przestrzeni nazw usługi Event Hubs. W związku z tym zasady stosowane do wszystkich połączeń z klientami przy użyciu dowolnego obsługiwanego protokołu.

Brak autoryzacji wszelkie próby połączenia z adresu IP, który nie jest zgodny regułę dozwolonych adresów IP w przestrzeni nazw jest odrzucana jako usługi Event Hubs. Odpowiedź nie mogą zawierać reguły adresów IP.

## <a name="default-setting"></a>Ustawienie domyślne

Domyślnie **filtru IP** siatki w portalu usługi Event Hubs jest pusty. To ustawienie domyślne oznacza, że Centrum zdarzeń akceptuje połączenia z dowolnego adresu IP. To ustawienie domyślne jest odpowiednikiem regułę, która akceptuje zakres adresów IP 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Ocenę reguł filtrowania adresów IP

Reguły filtrowania adresów IP są stosowane w kolejności, a pierwszej reguły, który jest zgodny z adresem IP określa akcji Zaakceptuj lub Odrzuć.

>[!WARNING]
> Wdrażanie zapory uniemożliwia innych usług platformy Azure interakcji z usługą Event Hubs.
>
> Zaufane usługi firmy Microsoft nie są obsługiwane podczas filtrowania adresów IP (zapory) są wykonywane i zostanie udostępniona wkrótce.
>
> Typowe scenariusze platformy Azure, które nie działają z filtrowania adresów IP (należy pamiętać, że lista jest **nie** wyczerpująca) —
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

### <a name="creating-a-firewall-rule-with-azure-resource-manager-templates"></a>Tworzenie reguły zapory przy użyciu szablonów usługi Azure Resource Manager

> [!IMPORTANT]
> Reguły zapory są obsługiwane w **standardowa** i **dedykowanych** warstw usługi Event Hubs. Nie są obsługiwane w warstwie Podstawowa.

Następujący szablon usługi Resource Manager umożliwia dodawanie reguły filtrowania adresów IP do istniejącej przestrzeni nazw usługi Event Hubs.

Parametry szablonu:

- **ipMask** jest pojedynczy adres IPv4 lub bloku adresów IP w notacji CIDR. Na przykład w CIDR 70.37.104.0/24 notacji reprezentuje 256 adresów IPv4 z 70.37.104.0 70.37.104.255 z 24 określającą liczbę bitów znaczące prefiks dla zakresu.

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

Aby wdrożyć szablon, postępuj zgodnie z instrukcjami dotyczącymi [usługi Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Kolejne kroki

Ograniczanie dostępu do usługi Event Hubs sieciami wirtualnymi platformy Azure zobacz następujące łącze:

- [Punkty końcowe usługi sieci wirtualnej dla usługi Event Hubs][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md
