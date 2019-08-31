---
title: Funkcje szablonu Azure Resource Manager — porównanie | Microsoft Docs
description: Opisuje funkcje, które mają być używane w szablonie Azure Resource Manager do porównywania wartości.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: tomfitz
ms.openlocfilehash: 05e51af62be974f925fefc46ad63c489f27fd163
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194821"
---
# <a name="comparison-functions-for-azure-resource-manager-templates"></a>Funkcje porównania dla Azure Resource Manager szablonów

Menedżer zasobów udostępnia kilka funkcji do dokonywania porównań w szablonach.

* [ubiegł](#equals)
* [mniejszą](#greater)
* [greaterOrEquals](#greaterorequals)
* [wcześniejsz](#less)
* [lessOrEquals](#lessorequals)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="equals"></a>równa się
`equals(arg1, arg2)`

Sprawdza, czy dwie wartości są równe siebie.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |int, string, array lub object |Pierwsza wartość do sprawdzenia równości. |
| arg2 |Tak |int, string, array lub object |Druga wartość do sprawdzenia równości. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **wartość true** , jeśli wartości są równe. w przeciwnym razie **false**.

### <a name="remarks"></a>Uwagi

Funkcja Equals jest często używana z elementem, `condition` aby sprawdzić, czy zasób został wdrożony.

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json) sprawdza różne typy wartości dla równości. Wszystkie wartości domyślne zwracają wartość true.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 1
        },
        "firstString": {
            "type": "string",
            "defaultValue": "a"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "firstObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[equals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[equals(parameters('firstString'), parameters('secondString'))]"
        },
        "checkArrays": {
            "type": "bool",
            "value": "[equals(parameters('firstArray'), parameters('secondArray'))]"
        },
        "checkObjects": {
            "type": "bool",
            "value": "[equals(parameters('firstObject'), parameters('secondObject'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Value |
| ---- | ---- | ----- |
| checkInts | Bool | Prawda |
| checkStrings | Bool | Prawda |
| checkArrays | Bool | Prawda |
| checkObjects | Bool | Prawda |

Aby wdrożyć ten przykładowy szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json
```

Aby wdrożyć ten przykładowy szablon przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json 
```

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) używa [nie](resource-group-template-functions-logical.md#not) z **równą**.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu to:

| Name | Typ | Value |
| ---- | ---- | ----- |
| checkNotEquals | Bool | Prawda |

Aby wdrożyć ten przykładowy szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

Aby wdrożyć ten przykładowy szablon przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json 
```

## <a name="greater"></a>mniejszą
`greater(arg1, arg2)`

Sprawdza, czy pierwsza wartość jest większa od drugiej wartości.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |int lub String |Pierwsza wartość dla większego porównania. |
| arg2 |Tak |int lub String |Druga wartość dla większego porównania. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **wartość true** , jeśli pierwsza wartość jest większa niż druga wartość; w przeciwnym razie **false**.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) sprawdza, czy wartość jest większa od drugiej.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greater(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greater(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Value |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | Prawda |

Aby wdrożyć ten przykładowy szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json
```

Aby wdrożyć ten przykładowy szablon przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json 
```

## <a name="greaterorequals"></a>greaterOrEquals
`greaterOrEquals(arg1, arg2)`

Sprawdza, czy pierwsza wartość jest większa lub równa drugiej wartości.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |int lub String |Pierwsza wartość dla porównania wyższego lub równego. |
| arg2 |Tak |int lub String |Druga wartość dla porównania wyższego lub równego. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **wartość true** , jeśli pierwsza wartość jest większa lub równa drugiej wartości; w przeciwnym razie **false**.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) sprawdza, czy jedna wartość jest większa lub równa drugiej.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Value |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | Prawda |

Aby wdrożyć ten przykładowy szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json
```

Aby wdrożyć ten przykładowy szablon przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json 
```

## <a name="less"></a>mniej
`less(arg1, arg2)`

Sprawdza, czy pierwsza wartość jest mniejsza od drugiej wartości.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |int lub String |Pierwsza wartość dla mniejszego porównania. |
| arg2 |Tak |int lub String |Druga wartość dla mniejszego porównania. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **wartość true** , jeśli pierwsza wartość jest mniejsza od drugiej wartości; w przeciwnym razie **false**.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) sprawdza, czy jedna wartość jest mniejsza od drugiej.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[less(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[less(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Value |
| ---- | ---- | ----- |
| checkInts | Bool | Prawda |
| checkStrings | Bool | False |

Aby wdrożyć ten przykładowy szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json
```

Aby wdrożyć ten przykładowy szablon przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json 
```

## <a name="lessorequals"></a>lessOrEquals
`lessOrEquals(arg1, arg2)`

Sprawdza, czy pierwsza wartość jest mniejsza lub równa drugiej wartości.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Tak |int lub String |Pierwsza wartość dla porównania less lub równa się. |
| arg2 |Tak |int lub String |Druga wartość dla porównania less lub równa się. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **wartość true** , jeśli pierwsza wartość jest mniejsza lub równa drugiej wartości; w przeciwnym razie **false**.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) sprawdza, czy jedna wartość jest mniejsza lub równa drugiej.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Value |
| ---- | ---- | ----- |
| checkInts | Bool | Prawda |
| checkStrings | Bool | False |

Aby wdrożyć ten przykładowy szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json
```

Aby wdrożyć ten przykładowy szablon przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json 
```

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać opis sekcje szablonu usługi Azure Resource Manager, zobacz [tworzenia usługi Azure Resource Manager](resource-group-authoring-templates.md).
* Aby scalić wiele szablonów, zobacz [przy użyciu szablonów połączonych z usługą Azure Resource Manager](resource-group-linked-templates.md).
* Do iteracji określoną liczbę razy podczas tworzenia dla typu zasobów, zobacz [tworzenie wielu wystąpień zasobów w usłudze Azure Resource Manager](resource-group-create-multiple.md).
* Aby dowiedzieć się, jak wdrożyć utworzony szablon, zobacz [wdrażanie aplikacji przy użyciu szablonu Azure Resource Manager](resource-group-template-deploy.md).

