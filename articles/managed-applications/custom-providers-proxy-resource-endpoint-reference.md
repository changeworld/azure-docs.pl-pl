---
title: Dokumentacja serwera proxy zasobu niestandardowego
description: Odwołanie do zasobu niestandardowego serwera proxy dla dostawców zasobów niestandardowych usługi Azure. Ten artykuł będzie przejście przez wymagania dotyczące wdrażania serwera proxy zasobów niestandardowych punktów końcowych.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: bbb907a7d73036352d4f5b8f36ccefacd89681ae
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478864"
---
# <a name="custom-resource-proxy-reference"></a>Odwołanie do zasobu niestandardowego serwera Proxy

Ten artykuł będzie przejście przez wymagania dotyczące wdrażania serwera proxy zasobów niestandardowych punktów końcowych. Jeśli nie jesteś zaznajomiony z niestandardowych dostawców zasobów platformy Azure, zobacz [omówienie dotyczące zasobów niestandardowych dostawców](./custom-providers-overview.md).

## <a name="how-to-define-a-proxy-resource-endpoint"></a>Jak zdefiniować punkt końcowy zasobu serwera proxy

Zasób serwera proxy mogą być tworzone przez określenie **routingType** "Proxy".

Dostawcy zasobów niestandardowych próbki:

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

## <a name="building-proxy-resource-endpoint"></a>Tworzenie punktu końcowego z zasobu serwera proxy

**Punktu końcowego** zasobu "Proxy", który zawiera **punktu końcowego** musi obsługiwać żądań i odpowiedzi dla nowego interfejsu API na platformie Azure. W tym przypadku **resourceType** spowoduje wygenerowanie nowego zasobu platformy Azure interfejsu API dla `PUT`, `GET`, i `DELETE` do wykonywania operacji CRUD na pojedynczy zasób także `GET` do pobrania wszystkich istniejących zasobów.

> [!NOTE]
> `id`, `name`, I `type` pola nie są wymagane, ale są potrzebne do integracji zasobów niestandardowych z istniejących ekosystem platformy Azure.

Próbka zasobów:

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

Odwołanie parametru:

Właściwość | Sample | Opis
---|---|---
name | '{myCustomResourceName}' | Nazwa zasobu niestandardowego.
type | 'Microsoft.CustomProviders/resourceProviders/{resourceTypeName}' | Przestrzeń nazw typu zasobu.
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources / {myCustomResourceName} " | Identyfikator zasobu.

### <a name="create-a-custom-resource"></a>Tworzenie zasobów niestandardowych

Żądanie platformy Azure przychodzących interfejsu API:

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

Tego żądania będą następnie przekazywane do **punktu końcowego** w postaci:

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

Podobnie, odpowiedź z **punktu końcowego** jest następnie przekazywany do klienta. Powinien zwrócić odpowiedź z punktu końcowego:

- Prawidłowy dokument obiektu JSON. Wszystkie tablice i ciągi powinny można zagnieździć w górnym obiektu.
- `Content-Type` Nagłówek powinien być ustawiony na "application/json; charset = utf-8 ".

**Punkt końcowy** odpowiedzi:

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

Odpowiedź dostawcy zasobu niestandardowego platformy Azure:

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

### <a name="remove-a-custom-resource"></a>Usuń zasób niestandardowy

Żądanie platformy Azure przychodzących interfejsu API:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Tego żądania będą następnie przekazywane do **punktu końcowego** w postaci:

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Podobnie odpowiedzi z **punktu końcowego** jest następnie przekazywany do klienta. Powinien zwrócić odpowiedź z punktu końcowego:

- Prawidłowy dokument obiektu JSON. Wszystkie tablice i ciągi powinny można zagnieździć w górnym obiektu.
- `Content-Type` Nagłówek powinien być ustawiony na "application/json; charset = utf-8 ".

**Punkt końcowy** odpowiedzi:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Odpowiedź dostawcy zasobu niestandardowego platformy Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>Pobieranie zasobów niestandardowych

Żądanie platformy Azure przychodzących interfejsu API:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Tego żądania będą następnie przekazywane do **punktu końcowego** w postaci:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Podobnie, odpowiedź z **punktu końcowego** jest następnie przekazywany do klienta. Powinien zwrócić odpowiedź z punktu końcowego:

- Prawidłowy dokument obiektu JSON. Wszystkie tablice i ciągi powinny można zagnieździć w górnym obiektu.
- `Content-Type` Nagłówek powinien być ustawiony na "application/json; charset = utf-8 ".

**Punkt końcowy** odpowiedzi:

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

Odpowiedź dostawcy zasobu niestandardowego platformy Azure:

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

### <a name="enumerate-all-custom-resources"></a>Wyliczyć wszystkie zasoby niestandardowe

Żądanie platformy Azure przychodzących interfejsu API:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Tego żądania będą następnie przekazywane do **punktu końcowego** w postaci:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources
```

Podobnie, odpowiedź z **punktu końcowego** jest następnie przekazywany do klienta. Powinien zwrócić odpowiedź z punktu końcowego:

- Prawidłowy dokument obiektu JSON. Wszystkie tablice i ciągi powinny można zagnieździć w górnym obiektu.
- `Content-Type` Nagłówek powinien być ustawiony na "application/json; charset = utf-8 ".
- Lista zasobów powinny być objęte najwyższego poziomu `value` właściwości.

**Punkt końcowy** odpowiedzi:

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

Odpowiedź dostawcy zasobu niestandardowego platformy Azure:

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

## <a name="next-steps"></a>Kolejne kroki

- [Omówienie usługi Azure Resource niestandardowych dostawców](./custom-providers-overview.md)
- [Samouczek: Tworzenie niestandardowego dostawcy zasobów platformy Azure i wdrażanie zasobów niestandardowych](./create-custom-provider.md)
- [Instrukcje: Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure](./custom-providers-action-endpoint-how-to.md)
- [Odwołanie: Odwołanie do pamięci podręcznej zasobów niestandardowych](./custom-providers-proxy-cache-resource-endpoint-reference.md)
