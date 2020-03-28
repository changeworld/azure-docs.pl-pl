---
title: Tworzenie i używanie dostawcy niestandardowego
description: W tym samouczku pokazano, jak utworzyć i używać dostawcy niestandardowego platformy Azure. Użyj dostawców niestandardowych, aby zmienić przepływy pracy na platformie Azure.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 09df78955de6423244c2d8ec94e1e1c06ecab257
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75650030"
---
# <a name="create-and-use-a-custom-provider"></a>Tworzenie i używanie dostawcy niestandardowego

Dostawca niestandardowy to umowa między platformą Azure a punktem końcowym. Za pomocą dostawców niestandardowych można zmieniać przepływy pracy na platformie Azure. Ten samouczek pokazuje proces tworzenia dostawcy niestandardowego. Jeśli nie znasz dostawców niestandardowych platformy Azure, zobacz [omówienie dostawców zasobów niestandardowych platformy Azure.](overview.md)

## <a name="create-a-custom-provider"></a>Tworzenie dostawcy niestandardowego

> [!NOTE]
> Ten samouczek nie pokazuje, jak autor punktu końcowego. Jeśli nie masz punktu końcowego RESTFUL, wykonaj [samouczek na temat tworzenia punktów końcowych RESTful](./tutorial-custom-providers-function-authoring.md), który jest podstawą dla bieżącego samouczka.

Po utworzeniu punktu końcowego można utworzyć niestandardowego dostawcy do generowania umowy między dostawcą a punktem końcowym. Za pomocą dostawcy niestandardowego można określić listę definicji punktów końcowych:

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

Właściwość | Wymagany | Opis
---|---|---
**Nazwa** | Tak | Nazwa definicji punktu końcowego. Platforma Azure udostępnia tę nazwę za pośrednictwem interfejsu API w obszarze /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders<br>/resourceProviders/{resourceProviderName}/{endpointDefinitionName}
**typ routingu** | Nie | Typ kontraktu punktu końcowego. Jeśli wartość nie jest określona, domyślnie jest to "Proxy".
**Punktu końcowego** | Tak | Punkt końcowy do kierowania żądań do. Ten punkt końcowy obsługuje odpowiedzi i wszelkie skutki uboczne żądania.

Wartość **punktu końcowego** jest adresem URL wyzwalacza aplikacji funkcji platformy Azure. `<yourapp>`Symbole `<funcname>`zastępcze , i `<functionkey>` zastępcze muszą zostać zastąpione wartościami dla utworzonej aplikacji funkcji.

## <a name="define-custom-actions-and-resources"></a>Definiowanie akcji i zasobów niestandardowych

Dostawca niestandardowy zawiera listę definicji punktów końcowych modelowane w ramach **akcji** i **resourceTypes** właściwości. Właściwość **akcji** jest mapowana na akcje niestandardowe udostępniane przez dostawcę niestandardowego, a właściwość **resourceTypes** jest zasobami niestandardowymi. W tym samouczku dostawca **actions** niestandardowy `myCustomAction` ma właściwość akcji `myCustomResources`o nazwie i **właściwość resourceTypes** o nazwie .

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

## <a name="deploy-the-custom-provider"></a>Wdrażanie dostawcy niestandardowego

> [!NOTE]
> Należy zastąpić wartości **punktu końcowego** z adresu URL wyzwalacza z aplikacji funkcji utworzonej w poprzednim samouczku.

Poprzedniego dostawcy niestandardowego można wdrożyć przy użyciu szablonu usługi Azure Resource Manager:

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

## <a name="use-custom-actions-and-resources"></a>Używanie akcji i zasobów niestandardowych

Po utworzeniu dostawcy niestandardowego można użyć nowych interfejsów API platformy Azure. Na poniższych kartach wyjaśniono, jak dzwonić i używać dostawcy niestandardowego.

### <a name="custom-actions"></a>Akcje niestandardowe

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

> [!NOTE]
> Należy zastąpić `{subscriptionId}` symbole zastępcze i `{resourceGroupName}` grupy subskrypcji i zasobów, w którym wdrożono dostawcy niestandardowego.

```azurecli-interactive
az resource invoke-action --action myCustomAction \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider \
                          --request-body
                            '{
                                "hello": "world"
                            }'
```

Parametr | Wymagany | Opis
---|---|---
*Działania* | Tak | Nazwa akcji zdefiniowanej w dostawcy niestandardowym
*Identyfikatory* | Tak | Identyfikator zasobu dostawcy niestandardowego
*organ żądania* | Nie | Treść żądania, która zostanie wysłana do punktu końcowego

# <a name="template"></a>[Szablonu](#tab/template)

Brak.

---

### <a name="custom-resources"></a>Zasoby niestandardowe

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

> [!NOTE]
> Należy zastąpić `{subscriptionId}` symbole zastępcze i `{resourceGroupName}` grupy subskrypcji i zasobów, w którym wdrożono dostawcy niestandardowego.

#### <a name="create-a-custom-resource"></a>Tworzenie zasobu niestandardowego

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

Parametr | Wymagany | Opis
---|---|---
*is-full-object* | Tak | Wskazuje, czy obiekt właściwości zawiera inne opcje, takie jak lokalizacja, tagi, jednostka SKU lub plan.
*Identyfikator* | Tak | Identyfikator zasobu niestandardowego. Ten identyfikator jest rozszerzeniem identyfikatora zasobu dostawcy niestandardowego.
*Właściwości* | Tak | Treść żądania, która zostanie wysłana do punktu końcowego.

#### <a name="delete-a-custom-resource"></a>Usuwanie zasobu niestandardowego

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parametr | Wymagany | Opis
---|---|---
*Identyfikator* | Tak | Identyfikator zasobu niestandardowego. Ten identyfikator jest rozszerzeniem identyfikatora zasobu dostawcy niestandardowego.

#### <a name="retrieve-a-custom-resource"></a>Pobieranie zasobu niestandardowego

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parametr | Wymagany | Opis
---|---|---
*Identyfikator* | Tak | Identyfikator zasobu niestandardowego. Ten identyfikator jest rozszerzeniem identyfikatora zasobu dostawcy niestandardowego.

# <a name="template"></a>[Szablonu](#tab/template)

Przykładowy szablon Menedżera zasobów:

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

Parametr | Wymagany | Opis
---|---|---
*nazwa typu zasobu* | Tak | `name` Wartość **resourceTypes** właściwość zdefiniowana w dostawcy niestandardowego.
*nazwa zasobu* | Tak | Nazwa wystąpienia dostawcy niestandardowego.
*niestandardoweName zasobów źródłowych* | Tak | Niestandardowa nazwa zasobu.

---

> [!NOTE]
> Po zakończeniu wdrażania i przy użyciu dostawcy niestandardowego należy pamiętać, aby wyczyścić wszystkie utworzone zasoby, w tym aplikację funkcji platformy Azure.

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się o dostawcach niestandardowych. Aby uzyskać więcej informacji, zobacz:

- [Jak: Dodawanie akcji niestandardowych do interfejsu API rest platformy Azure](./custom-providers-action-endpoint-how-to.md)
- [Jak: Dodawanie zasobów niestandardowych do interfejsu API REST platformy Azure](./custom-providers-resources-endpoint-how-to.md)
