---
title: Funkcje szablonów Menedżera zasobów Azure - porównania | Dokumentacja firmy Microsoft
description: Zawiera opis funkcji można użyć w szablonie usługi Azure Resource Manager porównuje wartości.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2017
ms.author: tomfitz
ms.openlocfilehash: 364a271d84f9abfe99c7c674a6c504ce94318ac9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359713"
---
# <a name="comparison-functions-for-azure-resource-manager-templates"></a>Porównanie funkcji dla szablonów usługi Azure Resource Manager

Resource Manager zapewnia kilka funkcji dla porównywanie w szablonach.

* [równa się](#equals)
* [większa](#greater)
* [greaterOrEquals](#greaterorequals)
* [mniej](#less)
* [lessOrEquals](#lessorequals)

## <a name="equals"></a>równa się
`equals(arg1, arg2)`

Sprawdza, czy dwie wartości równa siebie nawzajem.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |int, string, tablicy lub obiektu |Pierwsza wartość do sprawdzenia pod kątem równości. |
| Arg2 |Yes |int, string, tablicy lub obiektu |Druga wartość do sprawdzenia pod kątem równości. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **True** Jeśli wartości są równe; w przeciwnym razie **False**.

### <a name="remarks"></a>Uwagi

Funkcja jest równe jest często używane z `condition` element, aby sprawdzić, czy zasób jest wdrożona.

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

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json) sprawdza różnego rodzaju wartości pod kątem równości. Zwraca wartość True, wszystkie wartości domyślne.

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| checkInts | wartość logiczna | True |
| checkStrings | wartość logiczna | True |
| checkArrays | wartość logiczna | True |
| checkObjects | wartość logiczna | True |

Aby wdrożyć ten przykładowy szablon z wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json
```

Aby wdrożyć szablon ten przykład przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json 
```

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) używa [nie](resource-group-template-functions-logical.md#not) z **jest równe**.

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

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| checkNotEquals | wartość logiczna | True |

Aby wdrożyć ten przykładowy szablon z wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

Aby wdrożyć szablon ten przykład przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json 
```

## <a name="greater"></a>większa
`greater(arg1, arg2)`

Sprawdza, czy pierwsza wartość jest większa od drugiej wartości.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |wewnętrznym lub ciągiem |Pierwsza wartość do porównania większa. |
| Arg2 |Yes |wewnętrznym lub ciągiem |Druga wartość do porównania większa. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **True** Jeśli pierwsza wartość jest większa niż wartość drugiej; w przeciwnym razie **False**.

### <a name="example"></a>Przykład

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) sprawdza, czy jednej wartości jest większy niż drugi.

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| checkInts | wartość logiczna | False |
| checkStrings | wartość logiczna | True |

Aby wdrożyć ten przykładowy szablon z wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json
```

Aby wdrożyć szablon ten przykład przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json 
```

## <a name="greaterorequals"></a>greaterOrEquals
`greaterOrEquals(arg1, arg2)`

Sprawdza, czy pierwsza wartość jest większa niż lub równa drugiej wartości.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |wewnętrznym lub ciągiem |Pierwsza wartość do porównania większy lub równy. |
| Arg2 |Yes |wewnętrznym lub ciągiem |Druga wartość do porównania większy lub równy. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **True** Jeśli pierwsza wartość jest większa niż lub równa wartości drugiego; w przeciwnym razie **False**.

### <a name="example"></a>Przykład

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) sprawdza, czy jedną wartość jest większa niż lub równa innych.

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| checkInts | wartość logiczna | False |
| checkStrings | wartość logiczna | True |

Aby wdrożyć ten przykładowy szablon z wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json
```

Aby wdrożyć szablon ten przykład przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json 
```

## <a name="less"></a>mniej
`less(arg1, arg2)`

Sprawdza, czy pierwsza wartość jest mniejsza niż wartość drugiej.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |wewnętrznym lub ciągiem |Pierwsza wartość do porównania mniej. |
| Arg2 |Yes |wewnętrznym lub ciągiem |Druga wartość mniej porównania. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **True** Jeśli pierwsza wartość jest mniejsza niż wartość drugiej; w przeciwnym razie **False**.

### <a name="example"></a>Przykład

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) sprawdza, czy jedną wartość jest mniejsza niż innych.

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| checkInts | wartość logiczna | True |
| checkStrings | wartość logiczna | False |

Aby wdrożyć ten przykładowy szablon z wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json
```

Aby wdrożyć szablon ten przykład przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json 
```

## <a name="lessorequals"></a>lessOrEquals
`lessOrEquals(arg1, arg2)`

Sprawdza, czy pierwsza wartość jest mniejsza niż lub równa drugiej wartości.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |wewnętrznym lub ciągiem |Pierwsza wartość dla mniej lub porównania równości. |
| Arg2 |Yes |wewnętrznym lub ciągiem |Druga wartość dla mniej lub porównania równości. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **True** Jeśli pierwsza wartość jest mniejsza niż lub równa wartości drugiego w przeciwnym razie **False**.

### <a name="example"></a>Przykład

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) sprawdza, czy jedną wartość jest mniejsza lub równa do drugiego.

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| checkInts | wartość logiczna | True |
| checkStrings | wartość logiczna | False |

Aby wdrożyć ten przykładowy szablon z wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json
```

Aby wdrożyć szablon ten przykład przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json 
```

## <a name="next-steps"></a>Kolejne kroki
* Opis części szablonu usługi Azure Resource Manager, zobacz [szablonów Authoring Azure Resource Manager](resource-group-authoring-templates.md).
* Aby scalić wiele szablonów, zobacz [za pomocą szablonów połączonych z usługą Azure Resource Manager](resource-group-linked-templates.md).
* Do wykonywania iteracji określoną liczbę razy podczas tworzenia typu zasobu, zobacz [utworzyć wiele wystąpień zasobów usługi Azure Resource Manager](resource-group-create-multiple.md).
* Aby zobaczyć, jak wdrożyć szablon został utworzony, zobacz [wdrażania aplikacji przy użyciu szablonu usługi Azure Resource Manager](resource-group-template-deploy.md).

