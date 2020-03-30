---
title: Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure
description: Dowiedz się, jak dodać akcje niestandardowe do interfejsu API rest platformy Azure. W tym artykule zostaną przejmują się wymagania i najlepsze rozwiązania dotyczące punktów końcowych, które chcą zaimplementować akcje niestandardowe.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6110a7952b7c29609d2b98e135b61032aec3fa52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650398"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure

W tym artykule zostaną przejść przez wymagania i najlepsze rozwiązania dotyczące tworzenia punktów końcowych dostawcy zasobów niestandardowych platformy Azure, które implementują akcje niestandardowe. Jeśli nie znasz dostawców zasobów niestandardowych platformy Azure, zobacz [omówienie dostawców zasobów niestandardowych](overview.md).

## <a name="how-to-define-an-action-endpoint"></a>Jak zdefiniować punkt końcowy akcji

**Punkt końcowy** to adres URL, który wskazuje na usługę, która implementuje podstawową umowę między nim a platformą Azure. Punkt końcowy jest zdefiniowany w dostawcy zasobów niestandardowych i może być dowolny publicznie dostępny adres URL. Poniższy przykład **action** ma `myCustomAction` akcję o `endpointURL`nazwie implementowane przez .

Przykładowy **resourceprovider:**

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-an-action-endpoint"></a>Tworzenie punktu końcowego akcji

**Punkt końcowy,** który implementuje **akcję** musi obsługiwać żądanie i odpowiedź dla nowego interfejsu API na platformie Azure. Po utworzeniu niestandardowego dostawcy zasobów z **akcją** wygeneruje nowy zestaw interfejsów API na platformie Azure. W takim przypadku akcja wygeneruje nowy `POST` interfejs API akcji platformy Azure dla wywołań:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Żądanie przychodzące interfejsu API platformy Azure:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Wniosek ten zostanie następnie przekazany do **punktu końcowego** w formularzu:

``` HTTP
POST https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Podobnie odpowiedź z **punktu końcowego** jest następnie przekazywane z powrotem do klienta. Odpowiedź z punktu końcowego powinna zwrócić:

- Prawidłowy dokument obiektu JSON. Wszystkie tablice i ciągi powinny być zagnieżdżone pod górnym obiektem.
- Nagłówek `Content-Type` powinien być ustawiony na "application/json; charset=utf-8".

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Odpowiedź dostawcy zasobów niestandardowych platformy Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

## <a name="calling-a-custom-action"></a>Wywoływanie akcji niestandardowej

Istnieją dwa główne sposoby wywoływania akcji niestandardowej poza dostawcą zasobów niestandardowych:

- Interfejs wiersza polecenia platformy Azure
- Szablony usługi Azure Resource Manager

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
az resource invoke-action --action {actionName} \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName} \
                          --request-body \
                            '{
                                "myProperty1": "myPropertyValue1",
                                "myProperty2": {
                                    "myProperty3": "myPropertyValue3"
                                }
                            }'
```

Parametr | Wymagany | Opis
---|---|---
action | *Tak* | Nazwa akcji zdefiniowanej w **pliku ResourceProvider**.
Identyfikatory | *Tak* | Identyfikator zasobu **ResourceProvider**.
organ żądania | *nie* | Treść żądania, która zostanie wysłana do **punktu końcowego**.

### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

> [!NOTE]
> Akcje mają ograniczoną obsługę w szablonach usługi Azure Resource Manager. Aby akcja była wywoływana wewnątrz szablonu, musi [`list`](../templates/template-functions-resource.md#list) zawierać prefiks w jego nazwie.

Przykładowy **resourceprovider** z akcji listy:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "listMyCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Przykładowy szablon usługi Azure Resource Manager:

``` JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "resourceIdentifier": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
        "apiVersion": "2018-09-01-preview",
        "functionValues": {
            "myProperty1": "myPropertyValue1",
            "myProperty2": {
                "myProperty3": "myPropertyValue3"
            }
        }
    },
    "resources": [],
    "outputs": {
        "myCustomActionOutput": {
            "type": "object",
            "value": "[listMyCustomAction(variables('resourceIdentifier'), variables('apiVersion'), variables('functionValues'))]"
        }
    }
}
```

Parametr | Wymagany | Opis
---|---|---
zasobyIdentyfikator | *Tak* | Identyfikator zasobu **ResourceProvider**.
apiVersion | *Tak* | Wersja interfejsu API środowiska wykonawczego zasobu. Powinno to być zawsze "2018-09-01-preview".
functionWartues | *nie* | Treść żądania, która zostanie wysłana do **punktu końcowego**.

## <a name="next-steps"></a>Następne kroki

- [Omówienie dostawców zasobów niestandardowych platformy Azure](overview.md)
- [Szybki start: tworzenie niestandardowego dostawcy zasobów platformy Azure i wdrażanie zasobów niestandardowych](./create-custom-provider.md)
- [Samouczek: Tworzenie niestandardowych akcji i zasobów na platformie Azure](./tutorial-get-started-with-custom-providers.md)
- [Jak: Dodawanie zasobów niestandardowych do interfejsu API REST platformy Azure](./custom-providers-resources-endpoint-how-to.md)
