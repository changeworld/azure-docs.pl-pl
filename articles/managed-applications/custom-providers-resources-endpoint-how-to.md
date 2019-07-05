---
title: Dodawanie zasobów niestandardowych do interfejsu API REST platformy Azure
description: Informacje o sposobie dodawania niestandardowych zasobów do interfejsu API REST platformy Azure. Ten artykuł przeprowadzi wymagania i najlepsze rozwiązania dotyczące punktów końcowych, które chcesz wdrożyć zasoby niestandardowe.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b94d59b55a62797e142768dc84ec499d714bd067
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67479020"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Dodawanie zasobów niestandardowych do interfejsu API REST platformy Azure

Ten artykuł będzie przejście przez wymagania i najlepsze rozwiązania dotyczące tworzenia punktów końcowych niestandardowego dostawcy zasobów platformy Azure, które implementuje niestandardowe zasoby. Jeśli nie jesteś zaznajomiony z niestandardowych dostawców zasobów platformy Azure, zobacz [omówienie dotyczące zasobów niestandardowych dostawców](./custom-providers-overview.md).

## <a name="how-to-define-a-resource-endpoint"></a>Jak zdefiniować punkt końcowy zasobu

**Punktu końcowego** jest adresem URL, który wskazuje usługa, która implementuje ten kontrakt bazowego między nim a Azure. Punkt końcowy jest zdefiniowana w dostawcy zasobów niestandardowych i może być dowolny publicznie dostępny adres URL. Poniższy przykład zawiera **resourceType** o nazwie `myCustomResource` implementowany przez `endpointURL`.

Przykładowe **ResourceProvider**:

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

**Punktu końcowego** implementującej **resourceType** musi obsługiwać żądań i odpowiedzi dla nowego interfejsu API na platformie Azure. Gdy dostawca zasobów niestandardowych o **resourceType** jest utworzony, zostanie wygenerowany nowy zestaw interfejsów API na platformie Azure. W tym przypadku **resourceType** spowoduje wygenerowanie nowego zasobu platformy Azure interfejsu API dla `PUT`, `GET`, i `DELETE` do wykonywania operacji CRUD na pojedynczy zasób także `GET` do pobrania wszystkich istniejących zasobów:

Manipulowanie pojedynczego zasobu (`PUT`, `GET`, i `DELETE`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

Pobierz wszystkie zasoby (`GET`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

W przypadku zasobów niestandardowych zasobów niestandardowych dostawców oferuje dwa typy **routingTypes**: "`Proxy`"i"`Proxy, Cache`".

### <a name="proxy-routing-type"></a>Typ routingu z serwera proxy

"`Proxy`" **RoutingType** serwery proxy, wszystkie żądania metody **punktu końcowego** określone w dostawcy zasobów niestandardowych. Kiedy należy używać "`Proxy`":

- Potrzebny jest pełną kontrolę nad odpowiedzi.
- Integrowanie systemów z istniejących zasobów.

Aby dowiedzieć się więcej na temat "`Proxy`" zasobów, zobacz [odwołanie do zasobu niestandardowego serwera proxy](./custom-providers-proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>Typ routingu pamięci podręcznej serwera proxy

"`Proxy, Cache`" **RoutingType** tylko serwery proxy `PUT` i `DELETE` żądania metody **punktu końcowego** określone w dostawcy zasobów niestandardowych. Dostawca zasobów niestandardowych spowoduje automatyczne przywrócenie `GET` żądania oparte na co jest on przechowywany w pamięci podręcznej. Jeśli zasób niestandardowy jest oznaczona za pomocą pamięci podręcznej, dostawca zasobów niestandardowych również dodać / zastępują pola w odpowiedzi do zapewnienia zgodności interfejsów API platformy Azure. Kiedy należy używać "`Proxy, Cache`":

- Tworzenie nowego systemu, który nie ma żadnych istniejących zasobów.
- Praca z istniejących ekosystem platformy Azure.

Aby dowiedzieć się więcej na temat "`Proxy, Cache`" zasobów, zobacz [odwołanie do pamięci podręcznej zasobów niestandardowych](./custom-providers-proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>Tworzenie zasobów niestandardowych

Istnieją dwa sposoby tworzenia zasobów niestandardowych zniżki w stosunku do dostawcy zasobów niestandardowych:

- Interfejs wiersza polecenia platformy Azure
- Szablony usługi Azure Resource Manager

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Tworzenie zasobów niestandardowych:

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

Parametr | Wymagane | Opis
---|---|---
jest pełnej — obiekt | *Tak* | Wskazuje, że obiekt właściwości zawiera inne opcje, takie jak lokalizacja, tagi, jednostki sku i/lub planu.
id | *Tak* | Identyfikator zasobu niestandardowego zasobu. To powinien istnieć wylogować się z **ResourceProvider**
properties | *Tak* | Treść żądania, który zostanie wysłany do **punktu końcowego**.

Usuń niestandardowy zasób platformy Azure:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parametr | Wymagane | Opis
---|---|---
id | *Tak* | Identyfikator zasobu niestandardowego zasobu. To powinien istnieć wylogować się z **ResourceProvider**.

Pobieranie zasobów platformy Azure dla niestandardowych:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parametr | Wymagane | Opis
---|---|---
id | *Tak* | Identyfikator zasobu niestandardowego zasobu. To powinien istnieć wylogować się z **ResourceProvider**

### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

> [!NOTE]
> Zasoby wymagają, że odpowiedź zawiera odpowiednią `id`, `name`, i `type` z **punktu końcowego**.

Szablony usługi Azure Resource Manager wymaga `id`, `name`, i `type` zwracane są poprawnie z podrzędnego punktu końcowego. Odpowiedź zasobu zwrócona powinna mieć postać:

Przykładowe **punktu końcowego** odpowiedzi:

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

Przykład szablonu usługi Resource Manager platformy Azure:

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

Parametr | Wymagane | Opis
---|---|---
resourceTypeName | *Tak* | **Nazwa** z **resourceType** zdefiniowane w niestandardowego dostawcy.
resourceProviderName | *Tak* | Nazwa wystąpienia dostawcy zasobów niestandardowych.
customResourceName | *Tak* | Nazwa zasobu niestandardowego.

## <a name="next-steps"></a>Kolejne kroki

- [Omówienie usługi Azure Resource niestandardowych dostawców](./custom-providers-overview.md)
- [Samouczek: Tworzenie niestandardowego dostawcy zasobów platformy Azure i wdrażanie zasobów niestandardowych](./create-custom-provider.md)
- [Instrukcje: Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure](./custom-providers-action-endpoint-how-to.md)
- [Odwołanie: Odwołanie do zasobu niestandardowego serwera Proxy](./custom-providers-proxy-resource-endpoint-reference.md)
- [Odwołanie: Odwołanie do pamięci podręcznej zasobów niestandardowych](./custom-providers-proxy-cache-resource-endpoint-reference.md)
