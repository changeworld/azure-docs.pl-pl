---
title: Usługa Azure Resource Manager funkcje szablonu - logiczne | Dokumentacja firmy Microsoft
description: Opisuje funkcje do używania szablonu usługi Azure Resource Manager w celu określenia wartości logiczne.
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
ms.date: 09/24/2018
ms.author: tomfitz
ms.openlocfilehash: 8745519f1a0fdda7a5feb6ffb3f61e5250bb260a
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47164791"
---
# <a name="logical-functions-for-azure-resource-manager-templates"></a>Funkcje logiczne dla szablonów usługi Azure Resource Manager

Resource Manager udostępnia kilka funkcji składania porównania w szablonach.

* [i](#and)
* [wartość logiczna](#bool)
* [if](#if)
* [not](#not)
* [lub](#or)

## <a name="and"></a>i
`and(arg1, arg2)`

Sprawdza, czy obie wartości parametrów są spełnione.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |wartość logiczna |Pierwsza wartość, aby sprawdzić, czy ma wartość true. |
| argument2 |Yes |wartość logiczna |Druga wartość, aby sprawdzić, czy ma wartość true. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **True** Jeśli obie wartości mają wartość true, a w przeciwnym razie **False**.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) przedstawia sposób użycia funkcji logicznych.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu są:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| andExampleOutput | wartość logiczna | False |
| orExampleOutput | wartość logiczna | True |
| notExampleOutput | wartość logiczna | False |

Aby wdrożyć ten przykładowy szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

Aby wdrożyć ten przykładowy szablon przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

## <a name="bool"></a>wartość logiczna
`bool(arg1)`

Konwertuje parametr na wartość logiczną.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |ciąg lub int |Wartość do przekonwertowania na wartość logiczną. |

### <a name="return-value"></a>Wartość zwracana
Wartość logiczna przekonwertowana wartości.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) pokazuje, jak za pomocą usług bool string lub integer.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "trueString": {
            "value": "[bool('true')]",
            "type" : "bool"
        },
        "falseString": {
            "value": "[bool('false')]",
            "type" : "bool"
        },
        "trueInt": {
            "value": "[bool(1)]",
            "type" : "bool"
        },
        "falseInt": {
            "value": "[bool(0)]",
            "type" : "bool"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| trueString | wartość logiczna | True |
| falseString | wartość logiczna | False |
| trueInt | wartość logiczna | True |
| falseInt | wartość logiczna | False |

Aby wdrożyć ten przykładowy szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/bool.json
```

Aby wdrożyć ten przykładowy szablon przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/bool.json
```

## <a name="if"></a>Jeśli
`if(condition, trueValue, falseValue)`

Zwraca wartość, na podstawie warunku jest wartość PRAWDA lub FAŁSZ.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| warunek |Yes |wartość logiczna |Wartość, aby sprawdzić, czy jest spełniony. |
| trueValue |Yes | ciąg, int, obiekt lub tablica |Wartość zwracana, gdy warunek jest prawdziwy. |
| falseValue |Yes | ciąg, int, obiekt lub tablica |Wartość zwracana, gdy warunek jest fałszywy. |

### <a name="return-value"></a>Wartość zwracana

Zwraca drugi parametr, gdy pierwszy parametr jest **True**; w przeciwnym razie zwraca wartość trzeciego parametru.

### <a name="remarks"></a>Uwagi

Ta funkcja umożliwia warunkowo ustawić właściwości zasobu. Poniższy przykład jest pełny szablon, ale jej przedstawia istotne części warunkowo ustawienia zestawu dostępności.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "availabilitySet": {
            "type": "string",
            "allowedValues": [
                "yes",
                "no"
            ]
        }
    },
    "variables": {
        ...
        "availabilitySetName": "availabilitySet1",
        "availabilitySet": {
            "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
        }
    },
    "resources": [
        {
            "condition": "[equals(parameters('availabilitySet'),'yes')]",
            "type": "Microsoft.Compute/availabilitySets",
            "name": "[variables('availabilitySetName')]",
            ...
        },
        {
            "apiVersion": "2016-03-30",
            "type": "Microsoft.Compute/virtualMachines",
            "properties": {
                "availabilitySet": "[if(equals(parameters('availabilitySet'),'yes'), variables('availabilitySet'), json('null'))]",
                ...
            }
        },
        ...
    ],
    ...
}
```

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) ilustruje sposób używania `if` funkcji.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "yesOutput": {
            "type": "string",
            "value": "[if(equals('a', 'a'), 'yes', 'no')]"
        },
        "noOutput": {
            "type": "string",
            "value": "[if(equals('a', 'b'), 'yes', 'no')]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu są:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| yesOutput | Ciąg | tak |
| noOutput | Ciąg | nie |
| objectOutput | Obiekt | {"test": "wartość1"} |

Aby wdrożyć ten przykładowy szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/if.json
```

Aby wdrożyć ten przykładowy szablon przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/if.json
```

## <a name="not"></a>nie
`not(arg1)`

Konwertuje wartość przeciwną wartość logiczną.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |wartość logiczna |Wartość do przekonwertowania. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **True** po parametrze **False**. Zwraca **False** po parametrze **True**.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) przedstawia sposób użycia funkcji logicznych.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu są:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| andExampleOutput | wartość logiczna | False |
| orExampleOutput | wartość logiczna | True |
| notExampleOutput | wartość logiczna | False |

Aby wdrożyć ten przykładowy szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

Aby wdrożyć ten przykładowy szablon przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) używa **nie** z [jest równa](resource-group-template-functions-comparison.md#equals).

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
```

Dane wyjściowe z poprzedniego przykładu są:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| checkNotEquals | wartość logiczna | True |

Aby wdrożyć ten przykładowy szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

Aby wdrożyć ten przykładowy szablon przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

## <a name="or"></a>lub
`or(arg1, arg2)`

Sprawdza, czy każda wartość parametru jest spełniony.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |wartość logiczna |Pierwsza wartość, aby sprawdzić, czy ma wartość true. |
| argument2 |Yes |wartość logiczna |Druga wartość, aby sprawdzić, czy ma wartość true. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **True** przypadku albo wartość PRAWDA; w przeciwnym razie **False**.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) przedstawia sposób użycia funkcji logicznych.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu są:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| andExampleOutput | wartość logiczna | False |
| orExampleOutput | wartość logiczna | True |
| notExampleOutput | wartość logiczna | False |

Aby wdrożyć ten przykładowy szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

Aby wdrożyć ten przykładowy szablon przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/andornot.json
```

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać opis sekcje szablonu usługi Azure Resource Manager, zobacz [tworzenia usługi Azure Resource Manager](resource-group-authoring-templates.md).
* Aby scalić wiele szablonów, zobacz [przy użyciu szablonów połączonych z usługą Azure Resource Manager](resource-group-linked-templates.md).
* Do iteracji określoną liczbę razy podczas tworzenia dla typu zasobów, zobacz [tworzenie wielu wystąpień zasobów w usłudze Azure Resource Manager](resource-group-create-multiple.md).
* Aby zobaczyć, jak wdrożyć szablon został utworzony, zobacz [wdrażania aplikacji przy użyciu szablonu usługi Azure Resource Manager](resource-group-template-deploy.md).

