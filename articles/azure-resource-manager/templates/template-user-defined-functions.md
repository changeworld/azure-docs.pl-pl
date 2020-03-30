---
title: Funkcje zdefiniowane przez użytkownika w szablonach
description: W tym artykule opisano sposób definiowania i używania funkcji zdefiniowanych przez użytkownika w szablonie usługi Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 2c09572a460aa028b23987033d2b77e2aad8a0cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943217"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Funkcje zdefiniowane przez użytkownika w szablonie usługi Azure Resource Manager

W szablonie można tworzyć własne funkcje. Te funkcje są dostępne do użycia w szablonie. Funkcje zdefiniowane przez użytkownika są [oddzielone](template-functions.md) od standardowych funkcji szablonu, które są automatycznie dostępne w szablonie. Tworzenie własnych funkcji, gdy masz skomplikowane wyrażenia, które są wielokrotnie używane w szablonie.

W tym artykule opisano sposób dodawania funkcji zdefiniowanych przez użytkownika w szablonie usługi Azure Resource Manager.

## <a name="define-the-function"></a>Zdefiniuj funkcję

Funkcje wymagają wartości obszaru nazw, aby uniknąć konfliktów nazw z funkcjami szablonu. W poniższym przykładzie pokazano funkcję, która zwraca unikatową nazwę:

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

## <a name="use-the-function"></a>Użyj funkcji

W poniższym przykładzie przedstawiono szablon, który zawiera funkcję zdefiniowaną przez użytkownika. Używa tej funkcji, aby uzyskać unikatową nazwę konta magazynu. Szablon ma parametr o nazwie **storageNamePrefix,** który przekazuje jako parametr do funkcji.

```json
{
 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
 "contentVersion": "1.0.0.0",
 "parameters": {
   "storageNamePrefix": {
     "type": "string",
     "maxLength": 11
   }
 },
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
 "resources": [
   {
     "type": "Microsoft.Storage/storageAccounts",
     "apiVersion": "2019-04-01",
     "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
     "location": "South Central US",
     "sku": {
       "name": "Standard_LRS"
     },
     "kind": "StorageV2",
     "properties": {
       "supportsHttpsTrafficOnly": true
     }
   }
 ]
}
```

## <a name="limitations"></a>Ograniczenia

Podczas definiowania funkcji użytkownika istnieją pewne ograniczenia:

* Funkcja nie może uzyskać dostępu do zmiennych.
* Funkcja może używać tylko parametrów, które są zdefiniowane w funkcji. Podczas korzystania z [funkcji parametrów](template-functions-deployment.md#parameters) w ramach funkcji zdefiniowanej przez użytkownika, jesteś ograniczony do parametrów dla tej funkcji.
* Funkcja nie może wywoływać innych funkcji zdefiniowanych przez użytkownika.
* Funkcja nie może korzystać z funkcji [odwołania](template-functions-resource.md#reference) ani żadnej z funkcji [listy.](template-functions-resource.md#list)
* Parametry funkcji nie mogą mieć wartości domyślnych.


## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o dostępnych właściwościach funkcji zdefiniowanych przez użytkownika, zobacz [Opis struktury i składni szablonów usługi Azure Resource Manager](template-syntax.md).
* Aby uzyskać listę dostępnych funkcji szablonu, zobacz [Funkcje szablonów usługi Azure Resource Manager](template-functions.md).
