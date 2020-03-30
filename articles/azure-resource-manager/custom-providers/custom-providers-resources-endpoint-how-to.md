---
title: Dodawanie zasobów niestandardowych do interfejsu API REST platformy Azure
description: Dowiedz się, jak dodać zasoby niestandardowe do interfejsu API REST platformy Azure. W tym artykule zostaną przejmują się wymagania i najlepsze rozwiązania dotyczące punktów końcowych, które chcą zaimplementować zasoby niestandardowe.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b6c5f5b8e437ad2dc2e8a3be3f3f2ed03a613b44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650528"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Dodawanie zasobów niestandardowych do interfejsu API REST platformy Azure

W tym artykule zostaną przejść przez wymagania i najlepsze rozwiązania dotyczące tworzenia punktów końcowych dostawcy zasobów niestandardowych platformy Azure, który implementuje zasoby niestandardowe. Jeśli nie znasz dostawców zasobów niestandardowych platformy Azure, zobacz [omówienie dostawców zasobów niestandardowych](overview.md).

## <a name="how-to-define-a-resource-endpoint"></a>Jak zdefiniować punkt końcowy zasobu

**Punkt końcowy** to adres URL, który wskazuje na usługę, która implementuje podstawową umowę między nim a platformą Azure. Punkt końcowy jest zdefiniowany w dostawcy zasobów niestandardowych i może być dowolny publicznie dostępny adres URL. W poniższym przykładzie `myCustomResource` znajduje się `endpointURL` **typ zasobu** o nazwie implementowane przez .

Przykładowy **resourceprovider:**

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResource",
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

## <a name="building-a-resource-endpoint"></a>Tworzenie punktu końcowego zasobu

**Punkt końcowy,** który implementuje **resourceType** musi obsługiwać żądanie i odpowiedź dla nowego interfejsu API na platformie Azure. Po utworzeniu niestandardowego dostawcy zasobów z **resourceType,** wygeneruje nowy zestaw interfejsów API na platformie Azure. W takim przypadku **resourceType** wygeneruje nowy `PUT` `GET`interfejs `DELETE` API zasobów platformy Azure dla programu `GET` , i wykonać CRUD na jednym zasobie, a także pobrać wszystkie istniejące zasoby:

Manipulowanie pojedynczym`PUT` `GET`zasobem `DELETE`( , , i ):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

Pobierz wszystkie zasoby`GET`( ):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

W przypadku zasobów niestandardowych dostawcy zasobów oferują dwa`Proxy` **typy routinguTytyty**: " " i "`Proxy, Cache`"".

### <a name="proxy-routing-type"></a>typ routingu serwera proxy

"`Proxy`" " **routingType** proxy wszystkie metody żądania do **punktu końcowego** określonego w dostawcy zasobów niestandardowych. Kiedy używać`Proxy`" ":

- Wymagana jest pełna kontrola nad odpowiedzią.
- Integracja systemów z istniejącymi zasobami.

Aby dowiedzieć`Proxy`się więcej o zasobach " " , zobacz [odwołanie do niestandardowego serwera proxy zasobów](proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>typ routingu pamięci podręcznej serwera proxy

Tylko`Proxy, Cache`serwery **proxy** `PUT` " " `DELETE` " " i metody żądania do **punktu końcowego** określonego w dostawcy zasobów niestandardowych. Dostawca zasobów niestandardowych automatycznie `GET` zwróci żądania na podstawie tego, co ma przechowywane w pamięci podręcznej. Jeśli zasób niestandardowy jest oznaczony za pomocą pamięci podręcznej, dostawca zasobów niestandardowych będzie również dodawać / zastępować pola w odpowiedzi, aby interfejsy API platformy Azure były zgodne. Kiedy używać`Proxy, Cache`" ":

- Tworzenie nowego systemu, który nie ma istniejących zasobów.
- Praca z istniejącym ekosystemem platformy Azure.

Aby dowiedzieć`Proxy, Cache`się więcej o zasobach " " , zobacz [odwołanie do niestandardowej pamięci podręcznej zasobów](proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>Tworzenie zasobu niestandardowego

Istnieją dwa główne sposoby tworzenia zasobu niestandardowego poza dostawcą zasobów niestandardowych:

- Interfejs wiersza polecenia platformy Azure
- Szablony usługi Azure Resource Manager

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Tworzenie zasobu niestandardowego:

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName} \
                   --properties \
                    '{
                        "location": "eastus",
                        "properties": {
                            "myProperty1": "myPropertyValue1",
                            "myProperty2": {
                                "myProperty3": "myPropertyValue3"
                            }
                        }
                    }'
```

Parametr | Wymagany | Opis
---|---|---
is-full-object | *Tak* | Wskazuje, że obiekt właściwości zawiera inne opcje, takie jak lokalizacja, tagi, sku i/lub plan.
id | *Tak* | Identyfikator zasobu niestandardowego. Powinno to istnieć poza **ResourceProvider**
properties | *Tak* | Treść żądania, która zostanie wysłana do **punktu końcowego**.

Usuwanie zasobu niestandardowego platformy Azure:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parametr | Wymagany | Opis
---|---|---
id | *Tak* | Identyfikator zasobu niestandardowego. Powinno to istnieć poza **ResourceProvider**.

Pobieranie zasobu niestandardowego platformy Azure:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parametr | Wymagany | Opis
---|---|---
id | *Tak* | Identyfikator zasobu niestandardowego. Powinno to istnieć poza **ResourceProvider**

### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

> [!NOTE]
> Zasoby wymagają, aby odpowiedź `id` `name`zawierała `type` odpowiednie i z **punktu końcowego**.

Szablony usługi Azure `id`Resource `name`Manager `type` wymagają tego , i są zwracane poprawnie z podrzędnego punktu końcowego. Zwrócona odpowiedź zasobu powinna być w formie:

Przykładowa odpowiedź **punktu końcowego:**

``` JSON
{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3": "myPropertyValue3"
    }
  },
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{customResourceName}",
  "name": "{customResourceName}",
  "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}"
}
```

Przykładowy szablon usługi Azure Resource Manager:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}",
            "name": "{resourceProviderName}/{customResourceName}",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3": "myPropertyValue3"
                }
            }
        }
    ]
}
```

Parametr | Wymagany | Opis
---|---|---
nazwa typu zasobu | *Tak* | **Nazwa** **resourceType** zdefiniowane w dostawcy niestandardowego.
nazwa zasobu | *Tak* | Nazwa wystąpienia dostawcy zasobów niestandardowych.
niestandardoweName zasobów źródłowych | *Tak* | Niestandardowa nazwa zasobu.

## <a name="next-steps"></a>Następne kroki

- [Omówienie dostawców zasobów niestandardowych platformy Azure](overview.md)
- [Szybki start: tworzenie niestandardowego dostawcy zasobów platformy Azure i wdrażanie zasobów niestandardowych](./create-custom-provider.md)
- [Samouczek: Tworzenie niestandardowych akcji i zasobów na platformie Azure](./tutorial-get-started-with-custom-providers.md)
- [Jak: Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure](./custom-providers-action-endpoint-how-to.md)
- [Odwołanie: Odwołanie do niestandardowego serwera proxy zasobów](proxy-resource-endpoint-reference.md)
- [Odwołanie: Odwołanie do niestandardowej pamięci podręcznej zasobów](proxy-cache-resource-endpoint-reference.md)
