---
title: Dokumentacja serwera proxy zasobu niestandardowego
description: Odwołanie do niestandardowego serwera proxy zasobów dla dostawców zasobów niestandardowych platformy Azure. W tym artykule zostaną przejmują wymagania dotyczące punktów końcowych implementujących zasoby niestandardowe serwera proxy.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 46b38686b39836f3d4bfb80686d514f932a79bf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650463"
---
# <a name="custom-resource-proxy-reference"></a>Odwołanie do niestandardowego serwera proxy zasobów

W tym artykule zostaną przejmują wymagania dotyczące punktów końcowych implementujących zasoby niestandardowe serwera proxy. Jeśli nie znasz dostawców zasobów niestandardowych platformy Azure, zobacz [omówienie dostawców zasobów niestandardowych](overview.md).

## <a name="how-to-define-a-proxy-resource-endpoint"></a>Jak zdefiniować punkt końcowy zasobu serwera proxy

Zasób serwera proxy można utworzyć, określając **typ routingu** do "Serwera proxy".

Przykładowy dostawca zasobów niestandardowych:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
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

## <a name="building-proxy-resource-endpoint"></a>Tworzenie punktu końcowego zasobów serwera proxy

**Punkt końcowy,** który implementuje **punkt końcowy** zasobu "Proxy" musi obsługiwać żądanie i odpowiedź dla nowego interfejsu API na platformie Azure. W takim przypadku **resourceType** wygeneruje nowy `PUT` `GET`interfejs `DELETE` API zasobów platformy Azure dla programu `GET` , i wykonać CRUD na jednym zasobie, a także pobrać wszystkie istniejące zasoby.

> [!NOTE]
> Pola `id` `name`, `type` i pola nie są wymagane, ale są potrzebne do integracji zasobu niestandardowego z istniejącym ekosystemem platformy Azure.

Przykładowy zasób:

``` JSON
{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Odwołanie do parametru:

Właściwość | Sample | Opis
---|---|---
name | '{myCustomResourceName}' | Nazwa zasobu niestandardowego.
type | 'Microsoft.CustomProviders/resourceProviders/{resourceTypeName}' | Obszar nazw typu zasobu.
id | "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupGroupName}/<br>dostawcy/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{myCustomResourceName}' | Identyfikator zasobu.

### <a name="create-a-custom-resource"></a>Tworzenie zasobu niestandardowego

Żądanie przychodzące interfejsu API platformy Azure:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resource-provider-name}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Wniosek ten zostanie następnie przekazany do **punktu końcowego** w formularzu:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Podobnie odpowiedź z **punktu końcowego** jest następnie przekazywane z powrotem do klienta. Odpowiedź z punktu końcowego powinna zwrócić:

- Prawidłowy dokument obiektu JSON. Wszystkie tablice i ciągi powinny być zagnieżdżone pod górnym obiektem.
- Nagłówek `Content-Type` powinien być ustawiony na "application/json; charset=utf-8".

**Punkt końcowy** Odpowiedzi:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Odpowiedź dostawcy zasobów niestandardowych platformy Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="remove-a-custom-resource"></a>Usuwanie zasobu niestandardowego

Żądanie przychodzące interfejsu API platformy Azure:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Wniosek ten zostanie następnie przekazany do **punktu końcowego** w formularzu:

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Podobnie odpowiedź z **punktu końcowego** jest następnie przekazywane z powrotem do klienta. Odpowiedź z punktu końcowego powinna zwrócić:

- Prawidłowy dokument obiektu JSON. Wszystkie tablice i ciągi powinny być zagnieżdżone pod górnym obiektem.
- Nagłówek `Content-Type` powinien być ustawiony na "application/json; charset=utf-8".

**Punkt końcowy** Odpowiedzi:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Odpowiedź dostawcy zasobów niestandardowych platformy Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>Pobieranie zasobu niestandardowego

Żądanie przychodzące interfejsu API platformy Azure:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Wniosek ten zostanie następnie przekazany do **punktu końcowego** w formularzu:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Podobnie odpowiedź z **punktu końcowego** jest następnie przekazywane z powrotem do klienta. Odpowiedź z punktu końcowego powinna zwrócić:

- Prawidłowy dokument obiektu JSON. Wszystkie tablice i ciągi powinny być zagnieżdżone pod górnym obiektem.
- Nagłówek `Content-Type` powinien być ustawiony na "application/json; charset=utf-8".

**Punkt końcowy** Odpowiedzi:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Odpowiedź dostawcy zasobów niestandardowych platformy Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="enumerate-all-custom-resources"></a>Wyliczaj wszystkie zasoby niestandardowe

Żądanie przychodzące interfejsu API platformy Azure:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Wniosek ten zostanie następnie przekazany do **punktu końcowego** w formularzu:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources
```

Podobnie odpowiedź z **punktu końcowego** jest następnie przekazywane z powrotem do klienta. Odpowiedź z punktu końcowego powinna zwrócić:

- Prawidłowy dokument obiektu JSON. Wszystkie tablice i ciągi powinny być zagnieżdżone pod górnym obiektem.
- Nagłówek `Content-Type` powinien być ustawiony na "application/json; charset=utf-8".
- Lista zasobów powinny być umieszczone w `value` ramach właściwości najwyższego poziomu.

**Punkt końcowy** Odpowiedzi:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

Odpowiedź dostawcy zasobów niestandardowych platformy Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Następne kroki

- [Omówienie dostawców zasobów niestandardowych platformy Azure](overview.md)
- [Szybki start: tworzenie niestandardowego dostawcy zasobów platformy Azure i wdrażanie zasobów niestandardowych](./create-custom-provider.md)
- [Samouczek: Tworzenie niestandardowych akcji i zasobów na platformie Azure](./tutorial-get-started-with-custom-providers.md)
- [Jak: Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure](./custom-providers-action-endpoint-how-to.md)
- [Odwołanie: Odwołanie do niestandardowej pamięci podręcznej zasobów](proxy-cache-resource-endpoint-reference.md)
