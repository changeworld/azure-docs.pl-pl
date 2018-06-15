---
title: Azure Resource Manager szablonu funkcji - liczbowych | Dokumentacja firmy Microsoft
description: Opisuje funkcje do użycia w szablonu usługi Azure Resource Manager, aby pracować z liczbami.
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
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: 4fc17b997c44560199e65edb01d20c6a24e49877
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34360006"
---
# <a name="numeric-functions-for-azure-resource-manager-templates"></a>Funkcje numeryczne szablonów usługi Azure Resource Manager

Usługa Resource Manager zapewnia następujące funkcje do pracy z liczbami całkowitymi:

* [Dodaj](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [Float](#float)
* [int](#int)
* [max](#max)
* [min](#min)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

<a id="add" />

## <a name="add"></a>dodaj
`add(operand1, operand2)`

Zwraca sumę dwóch podanych liczb całkowitych.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- | 
|operand1 |Yes |int |Pierwszy numer do dodania. |
|operand2 |Yes |int |Druga liczba do dodania. |

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita, która zawiera sumę wartości parametrów.

### <a name="example"></a>Przykład

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json) dodaje dwa parametry.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to add"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to add"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "addResult": {
            "type": "int",
            "value": "[add(parameters('first'), parameters('second'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| addResult | Int | 8 |

Aby wdrożyć ten przykładowy szablon z wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json
```

Aby wdrożyć szablon ten przykład przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json 
```

<a id="copyindex" />

## <a name="copyindex"></a>copyIndex
`copyIndex(loopName, offset)`

Zwraca indeks iteracji pętli. 

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| loopName | Nie | ciąg | Nazwa uzyskania iteracji pętli. |
| Przesunięcie |Nie |int |Numer do dodania do wartość iteracji liczony od zera. |

### <a name="remarks"></a>Uwagi

Ta funkcja jest zawsze używana z **kopiowania** obiektu. Jeśli wartość nie zostanie podana dla **przesunięcie**, jest zwracana wartość bieżącą iterację. Wartość iteracji zaczyna się od zera. Podczas definiowania zasobów lub zmienne, można użyć iteracji pętli.

**LoopName** właściwość umożliwia określenie, czy copyIndex odwołuje się do zasobu iteracji lub właściwość iteracji. Jeśli wartość nie zostanie podana dla **loopName**, bieżącą iterację typu zasobu jest używana. Podaj wartość dla **loopName** podczas iteracji we właściwości. 
 
Pełny opis stosowania **copyIndex**, zobacz [utworzyć wiele wystąpień zasobów usługi Azure Resource Manager](resource-group-create-multiple.md).

Na przykład za pomocą **copyIndex** podczas definiowania zmienną, zobacz [zmienne](resource-group-authoring-templates.md#variables).

### <a name="example"></a>Przykład

W poniższym przykładzie przedstawiono pętlę kopiowania i wartość indeksu uwzględniona w nazwie. 

```json
"resources": [ 
  { 
    "name": "[concat('examplecopy-', copyIndex())]", 
    "type": "Microsoft.Web/sites", 
    "copy": { 
      "name": "websitescopy", 
      "count": "[parameters('count')]" 
    }, 
    ...
  }
]
```

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita reprezentująca indeks bieżącej iteracji.

<a id="div" />

## <a name="div"></a>div
`div(operand1, operand2)`

Zwraca dzielenie liczby całkowitej dwóch podanych liczb całkowitych.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| operand1 |Yes |int |Liczba jest podzielona. |
| operand2 |Yes |int |Liczba, która jest używana do dzielenia. Nie może wynosić 0. |

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita reprezentująca podziału.

### <a name="example"></a>Przykład

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json) dzieli jeden parametr przez inny parametr.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 8,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[div(parameters('first'), parameters('second'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| divResult | Int | 2 |

Aby wdrożyć ten przykładowy szablon z wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json
```

Aby wdrożyć szablon ten przykład przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json 
```

<a id="float" />

## <a name="float"></a>liczba zmiennoprzecinkowa
`float(arg1)`

Konwertuje wartość zmiennoprzecinkową numer punktu. Podczas przekazywania niestandardowych parametrów do aplikacji, takie jak aplikacja logiki tylko użyć tej funkcji.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |ciąg lub int |Wartość do przekonwertowania zmiennoprzecinkowej numer punktu. |

### <a name="return-value"></a>Wartość zwracana
Liczba zmiennoprzecinkowa.

### <a name="example"></a>Przykład

Poniższy przykład przedstawia użycie float do przekazania parametrów do aplikacji logiki:

```json
{
    "type": "Microsoft.Logic/workflows",
    "properties": {
        ...
        "parameters": {
            "custom1": {
                "value": "[float('3.0')]"
            },
            "custom2": {
                "value": "[float(3)]"
            },
```

<a id="int" />

## <a name="int"></a>int
`int(valueToConvert)`

Konwertuje określoną wartość na liczbę całkowitą.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| valueToConvert |Yes |ciąg lub int |Wartość do przekonwertowania na liczbę całkowitą. |

### <a name="return-value"></a>Wartość zwracana

Całkowitą wartość przekonwertowana.

### <a name="example"></a>Przykład

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json) konwertuje wartość parametru dostarczane przez użytkownika do liczby całkowitej.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToConvert": { 
            "type": "string",
            "defaultValue": "4"
        }
    },
    "resources": [
    ],
    "outputs": {
        "intResult": {
            "type": "int",
            "value": "[int(parameters('stringToConvert'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| intResult | Int | 4 |

Aby wdrożyć ten przykładowy szablon z wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

Aby wdrożyć szablon ten przykład przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

<a id="max" />

## <a name="max"></a>maks.
`max (arg1)`

Zwraca maksymalną wartość z tablicy liczb całkowitych lub rozdzielaną przecinkami listę liczb całkowitych.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |tablica liczb całkowitych lub rozdzielaną przecinkami listą liczb całkowitych |Kolekcja można uzyskać wartość maksymalna. |

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita reprezentująca maksymalną wartość z kolekcji.

### <a name="example"></a>Przykład

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) przedstawia sposób użycia max z tablicy i listy liczb całkowitych:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[max(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[max(0,3,2,5,4)]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

Aby wdrożyć ten przykładowy szablon z wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

Aby wdrożyć szablon ten przykład przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

<a id="min" />

## <a name="min"></a>min.
`min (arg1)`

Zwraca minimalną wartość z tablicy liczb całkowitych lub rozdzielaną przecinkami listę liczb całkowitych.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |tablica liczb całkowitych lub rozdzielaną przecinkami listą liczb całkowitych |Kolekcja można uzyskać wartość minimalna. |

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita reprezentująca minimalną wartość z kolekcji.

### <a name="example"></a>Przykład

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) przedstawia sposób użycia min z tablicy i listy liczb całkowitych:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[min(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[min(0,3,2,5,4)]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

Aby wdrożyć ten przykładowy szablon z wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

Aby wdrożyć szablon ten przykład przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

<a id="mod" />

## <a name="mod"></a>mod
`mod(operand1, operand2)`

Zwraca resztę z dzielenia przy użyciu dwóch podanych liczb całkowitych.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| operand1 |Yes |int |Liczba jest podzielona. |
| operand2 |Yes |int |Liczba, która jest używana do dzielenia, nie może wynosić 0. |

### <a name="return-value"></a>Wartość zwracana
Liczba całkowita reprezentująca resztę.

### <a name="example"></a>Przykład

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json) zwraca resztę z dzielenia jeden parametr przez inny parametr.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "modResult": {
            "type": "int",
            "value": "[mod(parameters('first'), parameters('second'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| modResult | Int | 1 |

Aby wdrożyć ten przykładowy szablon z wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

Aby wdrożyć szablon ten przykład przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

<a id="mul" />

## <a name="mul"></a>mul
`mul(operand1, operand2)`

Zwraca iloczyn dwóch podanych liczb całkowitych.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| operand1 |Yes |int |Pierwszy liczbę Aby pomnożyć. |
| operand2 |Yes |int |Druga liczba do wielokrotnie. |

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita reprezentująca mnożenia.

### <a name="example"></a>Przykład

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json) mnoży jeden parametr przez inny parametr.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to multiply"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to multiply"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "mulResult": {
            "type": "int",
            "value": "[mul(parameters('first'), parameters('second'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| mulResult | Int | 15 |

Aby wdrożyć ten przykładowy szablon z wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

Aby wdrożyć szablon ten przykład przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

<a id="sub" />

## <a name="sub"></a>Sub
`sub(operand1, operand2)`

Zwraca odejmowania dwóch podanych liczb całkowitych.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| operand1 |Yes |int |Liczba, która jest odejmowany od. |
| operand2 |Yes |int |Liczba, która jest odejmowany. |

### <a name="return-value"></a>Wartość zwracana
Liczba całkowita reprezentująca odejmowania.

### <a name="example"></a>Przykład

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json) odejmuje jeden parametr z inny parametr.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer subtracted from"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer to subtract"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "subResult": {
            "type": "int",
            "value": "[sub(parameters('first'), parameters('second'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| subResult | Int | 4 |

Aby wdrożyć ten przykładowy szablon z wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

Aby wdrożyć szablon ten przykład przy użyciu programu PowerShell, należy użyć:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

## <a name="next-steps"></a>Kolejne kroki
* Opis części szablonu usługi Azure Resource Manager, zobacz [szablonów Authoring Azure Resource Manager](resource-group-authoring-templates.md).
* Aby scalić wiele szablonów, zobacz [za pomocą szablonów połączonych z usługą Azure Resource Manager](resource-group-linked-templates.md).
* Do wykonywania iteracji określoną liczbę razy podczas tworzenia typu zasobu, zobacz [utworzyć wiele wystąpień zasobów usługi Azure Resource Manager](resource-group-create-multiple.md).
* Aby zobaczyć, jak wdrożyć szablon został utworzony, zobacz [wdrażania aplikacji przy użyciu szablonu usługi Azure Resource Manager](resource-group-template-deploy.md).

