---
title: Tworzenie i korzystanie z niestandardowego dostawcę usługi Azure
description: W tym samouczku zaczną przedstawić sposób tworzenia i wykorzystywania niestandardowego dostawcy.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 65a8e60d8216e1da16af987c9e699e24ecaec3ec
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800001"
---
# <a name="authoring-a-restful-endpoint-for-custom-providers"></a>Tworzenie elementów punkt końcowy usługi RESTful dla dostawcy niestandardowi

Dostawcy niestandardowi umożliwiają dostosowywanie przepływów pracy na platformie Azure. Niestandardowy dostawca jest kontrakt między platformą Azure i `endpoint`. W tym samouczku będzie przejście przez proces tworzenia niestandardowego dostawcy. Jeśli nie jesteś zaznajomiony z dostawców niestandardowych usługi Azure, zobacz [omówienie dotyczące zasobów niestandardowych dostawców](./custom-providers-overview.md).

W tym samouczku jest dzielony na następujące czynności:

- Co to jest dostawcy niestandardowego
- Definiowanie akcji niestandardowych i zasoby
- Wdrażanie niestandardowego dostawcy

W tym samouczku zostanie skompilowany w ramach następujących samouczków:

- [Tworzenie elementów punkt końcowy usługi RESTful dla dostawcy niestandardowi](./tutorial-custom-providers-function-authoring.md)

## <a name="creating-a-custom-provider"></a>Tworzenie niestandardowego dostawcy

> [!NOTE]
> W tym samouczku zostanie przechodzi przez tworzenie punktu końcowego. Postępuj zgodnie z [samouczek dotyczący tworzenia punktów końcowych RESTful](./tutorial-custom-providers-function-authoring.md) Jeśli nie masz punkt końcowy usługi RESTful.

Gdy `endpoint` jest utworzony, można wygenerować Tworzenie niestandardowego dostawcy do generowania kontrakt między go i `endpoint`. Niestandardowego dostawcy umożliwia określenie listy definicji punktów końcowych.

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

Właściwość | Wymagane | Opis
---|---|---
name | *Tak* | Nazwa definicji punktu końcowego. Azure udostępni tę nazwę, za pośrednictwem jego interfejsu API w obszarze "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
routingType | *Brak* | Określa typ kontraktu z `endpoint`. Jeśli nie zostanie określony, zostaną domyślnie "Proxy".
endpoint | *Tak* | Punkt końcowy, aby kierować żądania do. Obsłuży odpowiedzi, a także wszelkie efekty uboczne żądania.

W tym przypadku `endpoint` jest adres URL wyzwalacza funkcji platformy Azure. `<yourapp>`, `<funcname>`, I `<functionkey>` należy zastąpić wartościami dla utworzonej funkcji.

## <a name="defining-custom-actions-and-resources"></a>Definiowanie akcji niestandardowych i zasoby

Niestandardowego dostawcy zawiera listę definicji punktów końcowych modelowane w obszarze `actions` i `resourceTypes`. `actions` mapy w celu akcje niestandardowe udostępniane przez dostawcę niestandardowego, podczas gdy `resourceTypes` są zasoby niestandardowe. W tym samouczku zdefiniujemy niestandardowego dostawcy z `action` o nazwie `myCustomAction` i `resourceType` o nazwie `myCustomResources`.

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ],
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ]
  },
  "location": "eastus"
}
```

Zastąp `endpoint` za pomocą adresu URL wyzwalacza z funkcji utworzony wcześniej w poprzednim samouczku.

## <a name="deploying-the-custom-provider"></a>Wdrażanie niestandardowego dostawcy

> [!NOTE]
> `endpoint` Należy zastąpić adres URL funkcji.

Powyższe niestandardowego dostawcy można wdrożyć przy użyciu szablonu usługi Azure Resource Manager.

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders",
            "name": "myCustomProvider",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "actions": [
                    {
                        "name": "myCustomAction",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ],
                "resourceTypes": [
                    {
                        "name": "myCustomResources",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ]
            }
        }
    ]
}
```

## <a name="using-custom-actions-and-resources"></a>Przy użyciu akcji niestandardowych i zasobów

Po utworzyliśmy niestandardowego dostawcy, firma Microsoft może korzystać z nowych interfejsów API platformy Azure. Następna sekcja wyjaśniono, jak wywołać i korzystanie z niestandardowego dostawcy.

### <a name="custom-actions"></a>Akcje niestandardowe

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

> [!NOTE]
> `{subscriptionId}` i `{resourceGroupName}` powinien zostać zamieniony subskrypcji i grupy zasobów wdrożonym niestandardowego dostawcy.

```azurecli-interactive
az resource invoke-action --action myCustomAction \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider \
                          --request-body
                            '{
                                "hello": "world"
                            }'
```

Parametr | Wymagane | Opis
---|---|---
Akcja | *Tak* | Nazwa akcji zdefiniowane w utworzonej niestandardowego dostawcy.
identyfikatory | *Tak* | Identyfikator zasobu utworzonego niestandardowego dostawcy.
Treść żądania | *Brak* | Treść żądania, który zostanie wysłany do `endpoint`.

# <a name="templatetabtemplate"></a>[Szablon](#tab/template)

Brak.

---

### <a name="custom-resources"></a>Zasoby niestandardowe

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

> [!NOTE]
> `{subscriptionId}` i `{resourceGroupName}` powinien zostać zamieniony subskrypcji i grupy zasobów wdrożonym niestandardowego dostawcy.

Tworzenie zasobów niestandardowych:

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1 \
                   --properties
                    '{
                        "location": "eastus",
                        "properties": {
                            "hello" : "world"
                        }
                    }'
```

Parametr | Wymagane | Opis
---|---|---
jest pełnej — obiekt | *Tak* | Wskazuje, że obiekt właściwości zawiera inne opcje, takie jak lokalizacja, tagi, jednostki sku i/lub planu.
id | *Tak* | Identyfikator zasobu niestandardowego zasobu. To powinien istnieć zniżki w stosunku do utworzonego niestandardowego dostawcy.
properties | *Tak* | Treść żądania, który zostanie wysłany do `endpoint`.

Usuń niestandardowy zasób platformy Azure:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parametr | Wymagane | Opis
---|---|---
id | *Tak* | Identyfikator zasobu niestandardowego zasobu. To powinien istnieć zniżki w stosunku do utworzonego niestandardowego dostawcy.

Pobieranie zasobów platformy Azure dla niestandardowych:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parametr | Wymagane | Opis
---|---|---
id | *Tak* | Identyfikator zasobu niestandardowego zasobu. To powinien istnieć zniżki w stosunku do utworzonego niestandardowego dostawcy.

# <a name="templatetabtemplate"></a>[Szablon](#tab/template)

Przykład szablonu usługi Resource Manager platformy Azure:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "name": "myCustomProvider/myTestResourceName1",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "hello": "world"
            }
        }
    ]
}
```

Parametr | Wymagane | Opis
---|---|---
resourceTypeName | *Tak* | `name` z *resourceType* zdefiniowane w niestandardowego dostawcy.
resourceProviderName | *Tak* | Nazwa wystąpienia niestandardowego dostawcy.
customResourceName | *Tak* | Nazwa zasobu niestandardowego.

---

> [!NOTE]
> Po zakończeniu wdrażania i korzystania z niestandardowego dostawcy, pamiętaj, aby oczyścić wszystkie zasoby utworzone w tym funkcji platformy Azure.

## <a name="next-steps"></a>Następne kroki

W tym artykule omówiono niestandardowych dostawców. Przejdź do następnego artykułu, aby utworzyć niestandardowego dostawcę.

- [Instrukcje: Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure](./custom-providers-action-endpoint-how-to.md)
- [Instrukcje: Dodawanie zasobów niestandardowych do interfejsu API REST platformy Azure](./custom-providers-resources-endpoint-how-to.md)
