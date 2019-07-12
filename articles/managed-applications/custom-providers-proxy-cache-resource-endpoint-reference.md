---
title: Dokumentacja pamięci podręcznej zasobu niestandardowego
description: Dokumentacja pamięci podręcznej zasobów niestandardowych dostawców zasobów niestandardowych usługi Azure. Ten artykuł będzie przejście przez wymagania dotyczące implementowania pamięci podręcznej zasobów niestandardowych punktów końcowych.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 2970e69e825dacb6f548b3e66a830f221ece0b1c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795223"
---
# <a name="custom-resource-cache-reference"></a>Odwołanie do pamięci podręcznej zasobów niestandardowych

Ten artykuł będzie przejście przez wymagania dotyczące implementowania pamięci podręcznej zasobów niestandardowych punktów końcowych. Jeśli nie jesteś zaznajomiony z niestandardowych dostawców zasobów platformy Azure, zobacz [omówienie dotyczące zasobów niestandardowych dostawców](./custom-providers-overview.md).

## <a name="how-to-define-a-cache-resource-endpoint"></a>Jak zdefiniować punkt końcowy zasobu pamięci podręcznej

Zasób serwera proxy mogą być tworzone przez określenie **routingType** "Proxy pamięci podręcznej".

Dostawcy zasobów niestandardowych próbki:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy, Cache",
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

**Punktu końcowego** zasobu "Serwer Proxy, pamięć podręczną", który zawiera **punktu końcowego** musi obsługiwać żądań i odpowiedzi dla nowego interfejsu API na platformie Azure. W tym przypadku **resourceType** spowoduje wygenerowanie nowego zasobu platformy Azure interfejsu API dla `PUT`, `GET`, i `DELETE` do wykonywania operacji CRUD na pojedynczy zasób także `GET` do pobrania wszystkich istniejących zasobów:

> [!NOTE]
> Interfejsu API platformy Azure spowoduje wygenerowanie metody żądania `PUT`, `GET`, i `DELETE`, ale pamięcią podręczną **punktu końcowego** tylko musi obsługiwać `PUT` i `DELETE`.
> Zaleca się, że **punktu końcowego** implementuje również `GET`.

### <a name="create-a-custom-resource"></a>Tworzenie zasobów niestandardowych

Żądanie platformy Azure przychodzących interfejsu API:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
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
- Dostawca zasobów niestandardowych spowoduje zastąpienie `name`, `type`, i `id` pola dla żądania.
- Dostawca zasobów niestandardowych zwróci tylko pola w obszarze `properties` obiektu dla punktu końcowego pamięci podręcznej.

**Punkt końcowy** odpowiedzi:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

`name`, `id`, I `type` pola zostanie automatycznie wygenerowany dla niestandardowych zasobów przez dostawcę zasobów niestandardowych.

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

Podobnie, odpowiedź z **punktu końcowego** jest następnie przekazywany do klienta. Powinien zwrócić odpowiedź z punktu końcowego:

- Prawidłowy dokument obiektu JSON. Wszystkie tablice i ciągi powinny można zagnieździć w górnym obiektu.
- `Content-Type` Nagłówek powinien być ustawiony na "application/json; charset = utf-8 ".
- Zwracana jest odpowiedź 200 poziomie dostawcy zasobów platformy Azure Custom tylko spowoduje usunięcie elementu z pamięci podręcznej. Nawet jeśli zasób nie istnieje, **punktu końcowego** powinna zwrócić 204.

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

Żądanie będzie **nie** przekazane **punktu końcowego**.

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

Spowoduje to żądanie **nie** przekazane **punktu końcowego**.

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
- [Szybki start: Tworzenie niestandardowego dostawcy zasobów platformy Azure i wdrażanie zasobów niestandardowych](./create-custom-provider.md)
- [Samouczek: Tworzenie akcji niestandardowych i zasobów na platformie Azure](./tutorial-custom-providers-101.md)
- [Instrukcje: Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure](./custom-providers-action-endpoint-how-to.md)
- [Odwołanie: Odwołanie do zasobu niestandardowego serwera Proxy](./custom-providers-proxy-resource-endpoint-reference.md)
