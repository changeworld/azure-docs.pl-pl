---
title: Reguły zapory na magistrali usług platformy Azure | Dokumentacja firmy Microsoft
description: Jak używać reguł zapory zezwala na połączenia z określonych adresów IP usługi Azure Service Bus.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 04/23/2019
ms.author: aschhab
ms.openlocfilehash: 540435e3e018ae77477030ae8b9f727d71782121
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62736815"
---
# <a name="use-firewall-rules"></a>Użyj reguł zapory

W przypadku scenariuszy, w których usługi Azure Service Bus jest dostępna tylko w niektórych witrynach dobrze znanych reguł zapory umożliwiają skonfigurowanie reguły do akceptowania ruchu pochodzącego z określonych adresów IPv4. Na przykład te adresy może być udostępnianych przez firmy bramy translatora adresów Sieciowych.

## <a name="when-to-use"></a>Kiedy stosować

Jeśli szukasz do Instalatora usługi Service Bus, w której powinna odbierać ruch tylko z określonego zakresu adresów IP i odrzucić wszystkie inne elementy, a następnie można wykorzystać *zapory* blokowanie punktów końcowych usługi Service Bus, z innych adresów IP. Na przykład używasz usługi Service Bus za pomocą [Azure Express Route] [ express-route] na tworzenie prywatnych połączeń w infrastrukturze lokalnej. 

## <a name="how-filter-rules-are-applied"></a>Sposób stosowania reguły filtrowania

Reguły filtrowania adresów IP są stosowane na poziomie przestrzeni nazw usługi Service Bus. W związku z tym zasady stosowane do wszystkich połączeń z klientami przy użyciu dowolnego obsługiwanego protokołu.

Każda próba połączenia z adresu IP, który nie jest zgodny regułę dozwolonych adresów IP w usłudze Service Bus, przestrzeń nazw zostanie odrzucone jako nieautoryzowany. Odpowiedź nie mogą zawierać reguły adresów IP.

## <a name="default-setting"></a>Ustawienie domyślne

Domyślnie **filtru IP** siatki w portalu usługi Service Bus jest pusty. To ustawienie domyślne oznacza, że przestrzeń nazw akceptuje połączenia z dowolnego adresu IP. To ustawienie domyślne jest odpowiednikiem regułę, która akceptuje zakres adresów IP 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Ocenę reguł filtrowania adresów IP

Reguły filtrowania adresów IP są stosowane w kolejności i pierwszej reguły, który jest zgodny z adresem IP określa akcję Zaakceptuj lub Odrzuć.

>[!WARNING]
> Implementowanie reguł zapory można zapobiec interakcji z usługą Service Bus innych usług platformy Azure.
>
> Zaufane usługi firmy Microsoft nie są obsługiwane podczas filtrowania adresów IP (reguły zapory) są wykonywane i zostanie udostępniona wkrótce.
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
> - Azure App Service
> - Azure Functions

### <a name="creating-a-virtual-network-and-firewall-rule-with-azure-resource-manager-templates"></a>Tworzenie wirtualnej sieci i reguła zapory przy użyciu szablonów usługi Azure Resource Manager

> [!IMPORTANT]
> Zapory i sieci wirtualne są obsługiwane tylko w **premium** warstwy usługi Service Bus.

Następujący szablon usługi Resource Manager umożliwia dodanie reguły sieci wirtualnej do istniejącej przestrzeni nazw usługi Service Bus.

Parametry szablonu:

- **ipMask** jest pojedynczy adres IPv4 lub bloku adresów IP w notacji CIDR. Na przykład w CIDR 70.37.104.0/24 notacji reprezentuje 256 adresów IPv4 z 70.37.104.0 70.37.104.255 z 24 określającą liczbę bitów znaczące prefiks dla zakresu.

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

Aby wdrożyć szablon, postępuj zgodnie z instrukcjami dotyczącymi [usługi Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Kolejne kroki

Ograniczanie dostępu do usługi Service Bus, sieciami wirtualnymi platformy Azure zobacz następujące łącze:

- [Punkty końcowe usługi sieci wirtualnej dla usługi Service Bus][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
