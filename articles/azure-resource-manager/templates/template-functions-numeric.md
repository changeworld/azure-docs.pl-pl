---
title: Funkcje szablonu - numeryczne
description: W tym artykule opisano funkcje używane w szablonie usługi Azure Resource Manager do pracy z liczbami.
ms.topic: conceptual
ms.date: 11/08/2017
ms.openlocfilehash: 2ca5c539036d002b83b8141132a0ebf2530dc6af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156348"
---
# <a name="numeric-functions-for-arm-templates"></a>Funkcje numeryczne dla szablonów ARM

Menedżer zasobów udostępnia następujące funkcje do pracy z liczbami całkowitymi w szablonie usługi Azure Resource Manager (ARM):

* [add](#add)
* [copyIndex (copyIndex)](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [Max](#max)
* [Min](#min)
* [Mod](#mod)
* [Mul](#mul)
* [Sub](#sub)

<a id="add" />

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="add"></a>add
`add(operand1, operand2)`

Zwraca sumę dwóch podanych liczby całkowitej.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
|operand1 |Tak |int |Pierwszy numer do dodania. |
|operand2 |Tak |int |Drugi numer do dodania. |

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita zawierająca sumę parametrów.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json) dodaje dwa parametry.

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

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| addResult (wynik) | int | 8 |

Aby wdrożyć ten przykładowy szablon za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json
```

Aby wdrożyć ten przykładowy szablon za pomocą programu PowerShell, należy użyć:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json
```

<a id="copyindex" />

## <a name="copyindex"></a>copyIndex (copyIndex)
`copyIndex(loopName, offset)`

Zwraca indeks pętli iteracji.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| nazwa pętli | Nie | ciąg | Nazwa pętli do uzyskania iteracji. |
| przesunięcie |Nie |int |Liczba dodania do wartości iteracji opartej na wartości zerowej. |

### <a name="remarks"></a>Uwagi

Ta funkcja jest zawsze używana z obiektem **kopiowania.** Jeśli nie podano żadnej wartości dla **przesunięcia,** zwracana jest bieżąca wartość iteracji. Wartość iteracji zaczyna się od zera. Podczas definiowania zasobów lub zmiennych można użyć pętli iteracji.

Właściwość **loopName** umożliwia określenie, czy copyIndex odnosi się do iteracji zasobu lub iteracji właściwości. Jeśli dla **loopName**nie jest podana żadna wartość, używana jest bieżąca iteracja typu zasobu. Podaj wartość **loopName** podczas iteracji we właściwości.

Aby uzyskać pełny opis sposobu korzystania z **copyIndex,** zobacz [Tworzenie wielu wystąpień zasobów w usłudze Azure Resource Manager](copy-resources.md).

Na przykład użycia **copyIndex** podczas definiowania zmiennej zobacz [Zmienne](template-syntax.md#variables).

### <a name="example"></a>Przykład

W poniższym przykładzie przedstawiono pętlę kopiowania i wartość indeksu zawartą w nazwie.

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

Liczba całkowita reprezentująca bieżący indeks iteracji.

<a id="div" />

## <a name="div"></a>div
`div(operand1, operand2)`

Zwraca podział liczby całkowitej dwóch podanych liczby całkowitej.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| operand1 |Tak |int |Liczba podzielona. |
| operand2 |Tak |int |Liczba używana do dzielenia. Nie może być 0. |

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita reprezentująca podział.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json) dzieli jeden parametr przez inny parametr.

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

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| divResult | int | 2 |

Aby wdrożyć ten przykładowy szablon za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json
```

Aby wdrożyć ten przykładowy szablon za pomocą programu PowerShell, należy użyć:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json
```

<a id="float" />

## <a name="float"></a>float
`float(arg1)`

Konwertuje wartość na liczbę zmiennoprzecinową. Tej funkcji należy używać tylko podczas przekazywania parametrów niestandardowych do aplikacji, takich jak aplikacja logiki.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| argument1 |Tak |ciąg lub int |Wartość do konwersji na liczbę zmiennoprzecinową. |

### <a name="return-value"></a>Wartość zwracana
Liczba zmiennoprzecinowa.

### <a name="example"></a>Przykład

W poniższym przykładzie pokazano, jak używać float do przekazywania parametrów do aplikacji logiki:

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

Konwertuje określoną wartość na liczę całkowitą.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| wartośćToKonwert |Tak |ciąg lub int |Wartość do konwersji na całkowitą. |

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita przekonwertowanych wartości.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json) konwertuje wartość parametru dostarczoną przez użytkownika na wartość całkowitą.

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

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| intResult | int | 4 |

Aby wdrożyć ten przykładowy szablon za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

Aby wdrożyć ten przykładowy szablon za pomocą programu PowerShell, należy użyć:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

<a id="max" />

## <a name="max"></a>max
`max (arg1)`

Zwraca maksymalną wartość z tablicy liczby całkowitych lub listy liczby całkowitych oddzielonych przecinkami.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| argument1 |Tak |tablica liczby całkowitej lub oddzielona przecinkami lista liczby całkowitej |Kolekcja, aby uzyskać maksymalną wartość. |

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita reprezentująca maksymalną wartość z kolekcji.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) pokazuje, jak używać max z tablicą i listą liczby całkowitej:

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

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| arrayOutput | int | 5 |
| intOutput (Nieuprzejmych) | int | 5 |

Aby wdrożyć ten przykładowy szablon za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

Aby wdrożyć ten przykładowy szablon za pomocą programu PowerShell, należy użyć:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

<a id="min" />

## <a name="min"></a>min
`min (arg1)`

Zwraca wartość minimalną z tablicy liczby całkowitych lub listy liczby całkowitych oddzielonych przecinkami.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| argument1 |Tak |tablica liczby całkowitej lub oddzielona przecinkami lista liczby całkowitej |Kolekcja, aby uzyskać minimalną wartość. |

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita reprezentująca minimalną wartość z kolekcji.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) pokazuje, jak używać min z tablicą i listą liczby całkowitej:

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

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| arrayOutput | int | 0 |
| intOutput (Nieuprzejmych) | int | 0 |

Aby wdrożyć ten przykładowy szablon za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

Aby wdrożyć ten przykładowy szablon za pomocą programu PowerShell, należy użyć:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

<a id="mod" />

## <a name="mod"></a>Mod
`mod(operand1, operand2)`

Zwraca pozostałą część podziału liczby całkowitej przy użyciu dwóch podanych liczby całkowitej.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| operand1 |Tak |int |Liczba podzielona. |
| operand2 |Tak |int |Liczba, która jest używana do dzielenia, Nie może być 0. |

### <a name="return-value"></a>Wartość zwracana
Liczba całkowita reprezentująca pozostałą część.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json) zwraca pozostałą część dzielenia jednego parametru przez inny parametr.

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

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| modResult ( modResult ) | int | 1 |

Aby wdrożyć ten przykładowy szablon za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

Aby wdrożyć ten przykładowy szablon za pomocą programu PowerShell, należy użyć:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

<a id="mul" />

## <a name="mul"></a>Mul
`mul(operand1, operand2)`

Zwraca mnożenie dwóch podanych liczby całkowitej.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| operand1 |Tak |int |Pierwsza liczba do pomnożenia. |
| operand2 |Tak |int |Druga liczba do pomnożenia. |

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita reprezentująca mnożenie.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json) mnoży jeden parametr przez inny parametr.

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

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| mulResult (wynik) | int | 15 |

Aby wdrożyć ten przykładowy szablon za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

Aby wdrożyć ten przykładowy szablon za pomocą programu PowerShell, należy użyć:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

<a id="sub" />

## <a name="sub"></a>Sub
`sub(operand1, operand2)`

Zwraca odejmowanie dwóch podanych liczby całkowitej.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| operand1 |Tak |int |Liczba, od którą jest odejmowana. |
| operand2 |Tak |int |Liczba, która jest odejmowana. |

### <a name="return-value"></a>Wartość zwracana
Liczba całkowita reprezentująca odejmowanie.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json) odejmuje jeden parametr od innego parametru.

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

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| podresult | int | 4 |

Aby wdrożyć ten przykładowy szablon za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

Aby wdrożyć ten przykładowy szablon za pomocą programu PowerShell, należy użyć:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać opis sekcji w szablonie usługi Azure Resource Manager, zobacz [Tworzenie szablonów usługi Azure Resource Manager](template-syntax.md).
* Aby scalić wiele szablonów, zobacz [Używanie połączonych szablonów z usługą Azure Resource Manager](linked-templates.md).
* Aby iterować określoną liczbę razy podczas tworzenia typu zasobu, zobacz [Tworzenie wielu wystąpień zasobów w usłudze Azure Resource Manager](copy-resources.md).
* Aby zobaczyć, jak wdrożyć utworzony szablon, zobacz [Wdrażanie aplikacji za pomocą szablonu usługi Azure Resource Manager](deploy-powershell.md).

