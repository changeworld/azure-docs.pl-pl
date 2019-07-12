---
title: Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure
description: Dowiedz się, jak dodać niestandardowe akcje do interfejsu API REST platformy Azure. Ten artykuł przeprowadzi wymagania i najlepsze rozwiązania dotyczące punktów końcowych, którzy chcą zaimplementować akcje niestandardowe.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6fbd20c201e1b141b7276e3283599b00cdefd118
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795298"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure

Ten artykuł będzie przejście przez wymagania i najlepsze rozwiązania dotyczące tworzenia punktów końcowych niestandardowego dostawcy zasobów platformy Azure, które implementują niestandardowe akcje. Jeśli nie jesteś zaznajomiony z niestandardowych dostawców zasobów platformy Azure, zobacz [omówienie dotyczące zasobów niestandardowych dostawców](./custom-providers-overview.md).

## <a name="how-to-define-an-action-endpoint"></a>Jak zdefiniować punkt końcowy akcji

**Punktu końcowego** jest adresem URL, który wskazuje usługa, która implementuje ten kontrakt bazowego między nim a Azure. Punkt końcowy jest zdefiniowana w dostawcy zasobów niestandardowych i może być dowolny publicznie dostępny adres URL. Poniższy przykład zawiera **akcji** o nazwie `myCustomAction` implementowany przez `endpointURL`.

Przykładowe **ResourceProvider**:

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

**Punktu końcowego** implementującej **akcji** musi obsługiwać żądań i odpowiedzi dla nowego interfejsu API na platformie Azure. Gdy dostawca zasobów niestandardowych o **akcji** jest utworzony, zostanie wygenerowany nowy zestaw interfejsów API na platformie Azure. W tym przypadku akcja wygeneruje nową akcję Azure interfejsu API dla `POST` wywołania:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Żądanie platformy Azure przychodzących interfejsu API:

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

Tego żądania będą następnie przekazywane do **punktu końcowego** w postaci:

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

Podobnie, odpowiedź z **punktu końcowego** jest następnie przekazywany do klienta. Powinien zwrócić odpowiedź z punktu końcowego:

- Prawidłowy dokument obiektu JSON. Wszystkie tablice i ciągi powinny można zagnieździć w górnym obiektu.
- `Content-Type` Nagłówek powinien być ustawiony na "application/json; charset = utf-8 ".

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

Odpowiedź dostawcy zasobu niestandardowego platformy Azure:

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

## <a name="calling-a-custom-action"></a>Podczas wywoływania akcji niestandardowej

Istnieją dwa główne sposoby wywoływania niestandardowej akcji poza dostawcy zasobów niestandardowych:

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

Parametr | Wymagane | Opis
---|---|---
Akcja | *Tak* | Nazwa akcji zdefiniowane w **ResourceProvider**.
identyfikatory | *Tak* | Identyfikator zasobu **ResourceProvider**.
Treść żądania | *Brak* | Treść żądania, który zostanie wysłany do **punktu końcowego**.

### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

> [!NOTE]
> Akcje obsługują ograniczoną liczbę w szablonach usługi Resource Manager platformy Azure. Aby akcja ma być wywoływana wewnątrz szablonu, musi zawierać [ `list` ](../azure-resource-manager/resource-group-template-functions-resource.md#list) prefiks w jego nazwę.

Przykładowe **ResourceProvider** z listy akcji:

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

Przykład szablonu usługi Resource Manager platformy Azure:

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

Parametr | Wymagane | Opis
---|---|---
resourceIdentifier | *Tak* | Identyfikator zasobu **ResourceProvider**.
apiVersion | *Tak* | Wersja interfejsu API środowiska uruchomieniowego zasobów. Zawsze należy "2018-09-01-preview".
functionValues | *Brak* | Treść żądania, który zostanie wysłany do **punktu końcowego**.

## <a name="next-steps"></a>Następne kroki

- [Omówienie usługi Azure Resource niestandardowych dostawców](./custom-providers-overview.md)
- [Szybki start: Tworzenie niestandardowego dostawcy zasobów platformy Azure i wdrażanie zasobów niestandardowych](./create-custom-provider.md)
- [Samouczek: Tworzenie akcji niestandardowych i zasobów na platformie Azure](./tutorial-custom-providers-101.md)
- [Instrukcje: Dodawanie zasobów niestandardowych do interfejsu API REST platformy Azure](./custom-providers-resources-endpoint-how-to.md)
