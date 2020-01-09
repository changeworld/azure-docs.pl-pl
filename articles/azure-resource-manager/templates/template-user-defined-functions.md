---
title: Funkcje zdefiniowane przez użytkownika w szablonach
description: Opisuje sposób definiowania i używania funkcji zdefiniowanych przez użytkownika w szablonie Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: e2e68c6f6b9fc9eb40d282d87572ce1e805a11a8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483806"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Funkcje zdefiniowane przez użytkownika w szablonie Azure Resource Manager

W ramach szablonu można tworzyć własne funkcje. Te funkcje są dostępne do użycia w szablonie. Funkcje zdefiniowane przez użytkownika są niezależne od [standardowych funkcji szablonów](template-functions.md) , które są automatycznie dostępne w ramach szablonu. Utwórz własne funkcje, jeśli masz skomplikowane wyrażenia, które są wielokrotnie używane w szablonie.

W tym artykule opisano sposób dodawania funkcji zdefiniowanych przez użytkownika w szablonie Azure Resource Manager.

## <a name="define-the-function"></a>Zdefiniuj funkcję

Funkcje wymagają wartości przestrzeni nazw, aby uniknąć konfliktów nazw z funkcjami szablonu. W poniższym przykładzie pokazano funkcję zwracającą nazwę konta magazynu:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

## <a name="use-the-function"></a>Korzystanie z funkcji

Poniższy przykład pokazuje, jak wywołać funkcję.

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="limitations"></a>Ograniczenia

Podczas definiowania funkcji użytkownika istnieją pewne ograniczenia:

* Funkcja nie może uzyskać dostępu do zmiennych.
* Funkcja może używać tylko parametrów, które są zdefiniowane w funkcji. Gdy używana jest funkcja [Parameters](template-functions-deployment.md#parameters) w funkcji zdefiniowanej przez użytkownika, są ograniczone do parametrów tej funkcji.
* Funkcja nie może wywoływać innych funkcji zdefiniowanych przez użytkownika.
* Funkcja nie może używać funkcji [Reference](template-functions-resource.md#reference) ani żadnej z funkcji [list](template-functions-resource.md#list) .
* Parametry funkcji nie mogą mieć wartości domyślnych.


## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o dostępnych właściwościach funkcji zdefiniowanych przez użytkownika, zobacz [Opis struktury i składni szablonów Azure Resource Manager](template-syntax.md).
* Listę dostępnych funkcji szablonu można znaleźć w temacie [Azure Resource Manager Template Functions](template-functions.md).
