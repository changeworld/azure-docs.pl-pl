---
title: Funkcje szablonu - tablice i obiekty
description: W tym artykule opisano funkcje używane w szablonie usługi Azure Resource Manager do pracy z macierzami i obiektami.
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: 0b4bb80f6d7a7cc20a8b2dcc71e890f2ada7c5be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156379"
---
# <a name="array-and-object-functions-for-arm-templates"></a>Funkcje tablic i obiektów dla szablonów ARM

Menedżer zasobów udostępnia kilka funkcji do pracy z macierzami i obiektami w szablonie usługi Azure Resource Manager (ARM).

* [tablica](#array)
* [Łączonej](#coalesce)
* [Concat](#concat)
* [Zawiera](#contains)
* [tworzenieArray](#createarray)
* [Pusty](#empty)
* [Pierwszym](#first)
* [Przecięcia](#intersection)
* [Json](#json)
* [Ostatnio](#last)
* [Długość](#length)
* [Max](#max)
* [Min](#min)
* [Zakres](#range)
* [Pominąć](#skip)
* [wziąć](#take)
* [Unii](#union)

Aby uzyskać tablicę wartości ciągów rozdzielonych wartością, zobacz [split](template-functions-string.md#split).

## <a name="array"></a>tablica

`array(convertToArray)`

Konwertuje wartość na tablicę.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| convertToArray |Tak |int, ciąg, tablica lub obiekt |Wartość do konwersji na tablicę. |

### <a name="return-value"></a>Wartość zwracana

Tablicy.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/array.json) pokazuje, jak używać funkcji tablicy z różnymi typami.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "intToConvert": {
            "type": "int",
            "defaultValue": 1
        },
        "stringToConvert": {
            "type": "string",
            "defaultValue": "efgh"
        },
        "objectToConvert": {
            "type": "object",
            "defaultValue": {"a": "b", "c": "d"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "intOutput": {
            "type": "array",
            "value": "[array(parameters('intToConvert'))]"
        },
        "stringOutput": {
            "type": "array",
            "value": "[array(parameters('stringToConvert'))]"
        },
        "objectOutput": {
            "type": "array",
            "value": "[array(parameters('objectToConvert'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| intOutput (Nieuprzejmych) | Tablica | [1] |
| ciągOutput | Tablica | ["efgh"] |
| ObjectOutput | Tablica | [{"a": "b", "c": "d"}] |

Aby wdrożyć ten przykładowy szablon za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/array.json
```

Aby wdrożyć ten przykładowy szablon za pomocą programu PowerShell, należy użyć:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/array.json
```

## <a name="coalesce"></a>Łączonej

`coalesce(arg1, arg2, arg3, ...)`

Zwraca pierwszą wartość niekwa,0 z parametrów. Puste ciągi, puste tablice i puste obiekty nie mają wartości null.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| argument1 |Tak |int, ciąg, tablica lub obiekt |Pierwsza wartość do przetestowania dla null. |
| dodatkowe args |Nie |int, ciąg, tablica lub obiekt |Dodatkowe wartości do przetestowania pod kątem wartości null. |

### <a name="return-value"></a>Wartość zwracana

Wartość pierwszych parametrów innych niż null, które mogą być ciągiem, int, tablicą lub obiektem. Null, jeśli wszystkie parametry mają wartość null.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/coalesce.json) pokazuje dane wyjściowe z różnych zastosowań zlewki.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "objectToTest": {
            "type": "object",
            "defaultValue": {
                "null1": null,
                "null2": null,
                "string": "default",
                "int": 1,
                "object": {"first": "default"},
                "array": [1]
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').string)]"
        },
        "intOutput": {
            "type": "int",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').int)]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').object)]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').array)]"
        },
        "emptyOutput": {
            "type": "bool",
            "value": "[empty(coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| ciągOutput | Ciąg | default |
| intOutput (Nieuprzejmych) | int | 1 |
| ObjectOutput | Obiekt | {"pierwszy": "default"} |
| arrayOutput | Tablica | [1] |
| emptyOutput (pluć) | Wartość logiczna | True |

Aby wdrożyć ten przykładowy szablon za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/coalesce.json
```

Aby wdrożyć ten przykładowy szablon za pomocą programu PowerShell, należy użyć:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/coalesce.json
```

## <a name="concat"></a>concat

`concat(arg1, arg2, arg3, ...)`

Łączy wiele tablic i zwraca tablicę łączoną lub łączy wiele wartości ciągów i zwraca ciąg łączony.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| argument1 |Tak |tablica lub ciąg |Pierwsza tablica lub ciąg do łączenia. |
| dodatkowe argumenty |Nie |tablica lub ciąg |Dodatkowe tablice lub ciągi w kolejności sekwencyjnej do łączenia. |

Ta funkcja może przyjmować dowolną liczbę argumentów i może akceptować ciągi lub tablice dla parametrów. Jednak nie można podać zarówno tablice i ciągi dla parametrów. Tablice są łączone tylko z innymi tablicami.

### <a name="return-value"></a>Wartość zwracana

Ciąg lub tablica łączonych wartości.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) pokazuje, jak połączyć dwie tablice.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstArray": {
            "type": "array",
            "defaultValue": [
                "1-1",
                "1-2",
                "1-3"
            ]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": [
                "2-1",
                "2-2",
                "2-3"
            ]
        }
    },
    "resources": [
    ],
    "outputs": {
        "return": {
            "type": "array",
            "value": "[concat(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| return | Tablica | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

Aby wdrożyć ten przykładowy szablon za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

Aby wdrożyć ten przykładowy szablon za pomocą programu PowerShell, należy użyć:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) pokazuje, jak połączyć dwie wartości ciągu i zwrócić ciąg łączony.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "prefix": {
            "type": "string",
            "defaultValue": "prefix"
        }
    },
    "resources": [],
    "outputs": {
        "concatOutput": {
            "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]",
            "type" : "string"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| konatOutput | Ciąg | prefiks-5yj4yjf5mbg72 |

Aby wdrożyć ten przykładowy szablon za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

Aby wdrożyć ten przykładowy szablon za pomocą programu PowerShell, należy użyć:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

## <a name="contains"></a>zawiera

`contains(container, itemToFind)`

Sprawdza, czy tablica zawiera wartość, obiekt zawiera klucz lub ciąg zawiera podciąg. W porównaniu ciągów jest rozróżniana wielkość liter. Jednak podczas testowania, jeśli obiekt zawiera klucz, porównanie jest bez uwzględniania wielkości liter.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| kontener |Tak |tablica, obiekt lub ciąg |Wartość, która zawiera wartość do znalezienia. |
| itemToZnajduj |Tak |ciąg lub int |Wartość do znalezienia. |

### <a name="return-value"></a>Wartość zwracana

**Wartość true,** jeśli element zostanie znaleziony; w przeciwnym razie **False**.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) pokazuje, jak używać zawiera z różnymi typami:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "OneTwoThree"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringTrue": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'e')]"
        },
        "stringFalse": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'z')]"
        },
        "objectTrue": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'one')]"
        },
        "objectFalse": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'a')]"
        },
        "arrayTrue": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'three')]"
        },
        "arrayFalse": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'four')]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| ciągTrue | Wartość logiczna | True |
| stringFalse (własn. | Wartość logiczna | False |
| objectTrue | Wartość logiczna | True |
| objectFalse (własnik obiektu) | Wartość logiczna | False |
| tablicaTrue | Wartość logiczna | True |
| arrayFalse (panelFalse) | Wartość logiczna | False |

Aby wdrożyć ten przykładowy szablon za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

Aby wdrożyć ten przykładowy szablon za pomocą programu PowerShell, należy użyć:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

## <a name="createarray"></a>createarray

`createArray (arg1, arg2, arg3, ...)`

Tworzy tablicę na podstawie parametrów.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| argument1 |Tak |Ciąg, całkowita, tablica lub obiekt |Pierwsza wartość w tablicy. |
| dodatkowe argumenty |Nie |Ciąg, całkowita, tablica lub obiekt |Dodatkowe wartości w tablicy. |

### <a name="return-value"></a>Wartość zwracana

Tablicy.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/createarray.json) pokazuje, jak używać createArray z różnymi typami:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringArray": {
            "type": "array",
            "value": "[createArray('a', 'b', 'c')]"
        },
        "intArray": {
            "type": "array",
            "value": "[createArray(1, 2, 3)]"
        },
        "objectArray": {
            "type": "array",
            "value": "[createArray(parameters('objectToTest'))]"
        },
        "arrayArray": {
            "type": "array",
            "value": "[createArray(parameters('arrayToTest'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| stringArray | Tablica | ["a", "b", "c"] |
| intArray (IntArray) | Tablica | [1, 2, 3] |
| objectArray (właso) | Tablica | [{"jeden": "a", "dwa": "b", "trzy": "c"}] |
| arrayArray (własnomię) | Tablica | [["jeden", "dwa", "trzy"]] |

Aby wdrożyć ten przykładowy szablon za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/createarray.json
```

Aby wdrożyć ten przykładowy szablon za pomocą programu PowerShell, należy użyć:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/createarray.json
```

## <a name="empty"></a>empty

`empty(itemToTest)`

Określa, czy tablica, obiekt lub ciąg jest pusty.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| itemToTest |Tak |tablica, obiekt lub ciąg |Wartość, aby sprawdzić, czy jest pusta. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **wartość True,** jeśli wartość jest pusta; w przeciwnym razie **False**.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) sprawdza, czy tablica, obiekt i ciąg są puste.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": []
        },
        "testObject": {
            "type": "object",
            "defaultValue": {}
        },
        "testString": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testArray'))]"
        },
        "objectEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testObject'))]"
        },
        "stringEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testString'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| arrayEmpty | Wartość logiczna | True |
| objectEmpty | Wartość logiczna | True |
| stringaPty | Wartość logiczna | True |

Aby wdrożyć ten przykładowy szablon za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

Aby wdrożyć ten przykładowy szablon za pomocą programu PowerShell, należy użyć:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

## <a name="first"></a>Pierwszym

`first(arg1)`

Zwraca pierwszy element tablicy lub pierwszy znak ciągu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| argument1 |Tak |tablica lub ciąg |Wartość, aby pobrać pierwszy element lub znak. |

### <a name="return-value"></a>Wartość zwracana

Typ (ciąg, int, tablica lub obiekt) pierwszego elementu w tablicy lub pierwszy znak ciągu.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) pokazuje, jak używać pierwszej funkcji z tablicą i ciągiem.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[first(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[first('One Two Three')]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| arrayOutput | Ciąg | jeden |
| ciągOutput | Ciąg | O |

Aby wdrożyć ten przykładowy szablon za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

Aby wdrożyć ten przykładowy szablon za pomocą programu PowerShell, należy użyć:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

## <a name="intersection"></a>Przecięcia

`intersection(arg1, arg2, arg3, ...)`

Zwraca pojedynczą tablicę lub obiekt ze wspólnymi elementami z parametrów.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| argument1 |Tak |tablica lub obiekt |Pierwsza wartość używana do znajdowania wspólnych elementów. |
| argument 2 |Tak |tablica lub obiekt |Druga wartość do użycia do znajdowania wspólnych elementów. |
| dodatkowe argumenty |Nie |tablica lub obiekt |Dodatkowe wartości do użycia do znajdowania wspólnych elementów. |

### <a name="return-value"></a>Wartość zwracana

Tablica lub obiekt ze wspólnymi elementami.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json) pokazuje, jak używać przecięcia z tablicami i obiektami:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "z", "three": "c"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[intersection(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[intersection(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| ObjectOutput | Obiekt | {"jeden": "a", "trzy": "c"} |
| arrayOutput | Tablica | ["dwa", "trzy"] |

Aby wdrożyć ten przykładowy szablon za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/intersection.json
```

Aby wdrożyć ten przykładowy szablon za pomocą programu PowerShell, należy użyć:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/intersection.json
```

## <a name="json"></a>json

`json(arg1)`

Zwraca obiekt JSON.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| argument1 |Tak |ciąg |Wartość do konwersji na JSON. |

### <a name="return-value"></a>Wartość zwracana

Obiekt JSON z określonego ciągu lub pusty obiekt, gdy określono **wartość null.**

### <a name="remarks"></a>Uwagi

Jeśli chcesz dołączyć wartość parametru lub zmienną w obiekcie JSON, użyj funkcji [concat,](template-functions-string.md#concat) aby utworzyć ciąg, który zostanie przekazyny do funkcji.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/json.json) pokazuje, jak używać funkcji json z tablicami i obiektami:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testValue": {
            "type": "string",
            "defaultValue": "demo value"
        }
    },
    "resources": [
    ],
    "outputs": {
        "jsonOutput": {
            "type": "object",
            "value": "[json('{\"a\": \"b\"}')]"
        },
        "nullOutput": {
            "type": "bool",
            "value": "[empty(json('null'))]"
        },
        "paramOutput": {
            "type": "object",
            "value": "[json(concat('{\"a\": \"', parameters('testValue'), '\"}'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| jsonOutput (Nieumieja) | Obiekt | {"a": "b"} |
| nullOutput (Nieuprzejmie) | Wartość logiczna | True |
| paramOutput | Obiekt | {"a": "wartość demo"}

Aby wdrożyć ten przykładowy szablon za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/json.json
```

Aby wdrożyć ten przykładowy szablon za pomocą programu PowerShell, należy użyć:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/json.json
```

## <a name="last"></a>ostatni

`last (arg1)`

Zwraca ostatni element tablicy lub ostatni znak ciągu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| argument1 |Tak |tablica lub ciąg |Wartość do pobrania ostatniego elementu lub znaku. |

### <a name="return-value"></a>Wartość zwracana

Typ (ciąg, int, tablica lub obiekt) ostatniego elementu w tablicy lub ostatniego znaku ciągu.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) pokazuje, jak używać ostatniej funkcji z tablicą i ciągiem.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[last(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[last('One Two Three')]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| arrayOutput | Ciąg | trzy |
| ciągOutput | Ciąg | e |

Aby wdrożyć ten przykładowy szablon za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

Aby wdrożyć ten przykładowy szablon za pomocą programu PowerShell, należy użyć:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

## <a name="length"></a>length

`length(arg1)`

Zwraca liczbę elementów w tablicy, znaki w ciągu lub właściwości na poziomie głównym w obiekcie.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| argument1 |Tak |tablica, ciąg lub obiekt |Tablica do użycia do uzyskania liczby elementów, ciąg do użycia do uzyskania liczby znaków lub obiekt do użycia do uzyskania liczby właściwości na poziomie głównym. |

### <a name="return-value"></a>Wartość zwracana

Int.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) pokazuje, jak używać długości z tablicą i ciągiem:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "stringToTest": {
            "type": "string",
            "defaultValue": "One Two Three"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {
                "propA": "one",
                "propB": "two",
                "propC": "three",
                "propD": {
                    "propD-1": "sub",
                    "propD-2": "sub"
                }
            }
        }
    },
    "resources": [],
    "outputs": {
        "arrayLength": {
            "type": "int",
            "value": "[length(parameters('arrayToTest'))]"
        },
        "stringLength": {
            "type": "int",
            "value": "[length(parameters('stringToTest'))]"
        },
        "objectLength": {
            "type": "int",
            "value": "[length(parameters('objectToTest'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| tablicaLength | int | 3 |
| stringLength (len) | int | 13 |
| obiektLength | int | 4 |

Aby wdrożyć ten przykładowy szablon za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

Aby wdrożyć ten przykładowy szablon za pomocą programu PowerShell, należy użyć:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

Tej funkcji z tablicą można użyć, aby określić liczbę iteracji podczas tworzenia zasobów. W poniższym przykładzie **nazwa użytkownika parametrów** będzie odwoływać się do tablicy nazw używanych podczas tworzenia witryn sieci web.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

Aby uzyskać więcej informacji na temat korzystania z tej funkcji z tablicą, zobacz [Tworzenie wielu wystąpień zasobów w usłudze Azure Resource Manager](copy-resources.md).

## <a name="max"></a>max

`max(arg1)`

Zwraca maksymalną wartość z tablicy liczby całkowitych lub listy liczby całkowitych oddzielonych przecinkami.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| argument1 |Tak |tablica liczby całkowitej lub oddzielona przecinkami lista liczby całkowitej |Kolekcja, aby uzyskać maksymalną wartość. |

### <a name="return-value"></a>Wartość zwracana

Int reprezentujący maksymalną wartość.

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

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

## <a name="min"></a>min

`min(arg1)`

Zwraca wartość minimalną z tablicy liczby całkowitych lub listy liczby całkowitych oddzielonych przecinkami.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| argument1 |Tak |tablica liczby całkowitej lub oddzielona przecinkami lista liczby całkowitej |Kolekcja, aby uzyskać minimalną wartość. |

### <a name="return-value"></a>Wartość zwracana

Int reprezentujący wartość minimalną.

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

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

## <a name="range"></a>range

`range(startIndex, count)`

Tworzy tablicę liczb całkowitych z początkowej liczby całkowitej i zawiera liczbę elementów.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| Startindex |Tak |int |Pierwsza liczba całkowita w tablicy. Suma startIndex i count nie może być większa niż 2147483647. |
| count |Tak |int |Liczba liczb całkowitych w tablicy. Musi być nieujemna liczba całkowita do 10000. |

### <a name="return-value"></a>Wartość zwracana

Tablica liczby całkowitych.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/range.json) pokazuje, jak korzystać z funkcji zakresu:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "startingInt": {
            "type": "int",
            "defaultValue": 5
        },
        "numberOfElements": {
            "type": "int",
            "defaultValue": 3
        }
    },
    "resources": [],
    "outputs": {
        "rangeOutput": {
            "type": "array",
            "value": "[range(parameters('startingInt'),parameters('numberOfElements'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| rangeOutput | Tablica | [5, 6, 7] |

Aby wdrożyć ten przykładowy szablon za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/range.json
```

Aby wdrożyć ten przykładowy szablon za pomocą programu PowerShell, należy użyć:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/range.json
```

## <a name="skip"></a>Pomiń

`skip(originalValue, numberToSkip)`

Zwraca tablicę ze wszystkimi elementami po określonej liczbie w tablicy lub zwraca ciąg ze wszystkimi znakami po określonej liczbie w ciągu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| originalValue |Tak |tablica lub ciąg |Tablica lub ciąg do pominięcia. |
| numerToSkip |Tak |int |Liczba elementów lub znaków do pominięcia. Jeśli ta wartość wynosi 0 lub mniej, zwracane są wszystkie elementy lub znaki w wartości. Jeśli jest większy niż długość tablicy lub ciągu, zwracana jest pusta tablica lub ciąg. |

### <a name="return-value"></a>Wartość zwracana

Tablica lub ciąg.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) pomija określoną liczbę elementów w tablicy i określoną liczbę znaków w ciągu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToSkip": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToSkip": {
            "type": "int",
            "defaultValue": 4
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[skip(parameters('testArray'),parameters('elementsToSkip'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[skip(parameters('testString'),parameters('charactersToSkip'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| arrayOutput | Tablica | ["trzy"] |
| ciągOutput | Ciąg | dwa trzy |

Aby wdrożyć ten przykładowy szablon za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

Aby wdrożyć ten przykładowy szablon za pomocą programu PowerShell, należy użyć:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

## <a name="take"></a>wziąć

`take(originalValue, numberToTake)`

Zwraca tablicę z określoną liczbą elementów od początku tablicy lub ciąg z określoną liczbą znaków od początku ciągu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| originalValue |Tak |tablica lub ciąg |Tablica lub ciąg do podjęcia elementów z. |
| numerDoTake |Tak |int |Liczba elementów lub znaków do podjęcia. Jeśli ta wartość jest 0 lub mniej, zwracana jest pusta tablica lub ciąg. Jeśli jest większy niż długość danej tablicy lub ciągu, wszystkie elementy w tablicy lub ciągu są zwracane. |

### <a name="return-value"></a>Wartość zwracana

Tablica lub ciąg.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) przyjmuje określoną liczbę elementów z tablicy i znaki z ciągu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToTake": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToTake": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[take(parameters('testArray'),parameters('elementsToTake'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[take(parameters('testString'),parameters('charactersToTake'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| arrayOutput | Tablica | ["jeden", "dwa"] |
| ciągOutput | Ciąg | on |

Aby wdrożyć ten przykładowy szablon za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

Aby wdrożyć ten przykładowy szablon za pomocą programu PowerShell, należy użyć:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

## <a name="union"></a>unia

`union(arg1, arg2, arg3, ...)`

Zwraca pojedynczą tablicę lub obiekt ze wszystkimi elementami z parametrów. Zduplikowane wartości lub klucze są uwzględniane tylko raz.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| argument1 |Tak |tablica lub obiekt |Pierwsza wartość używana do łączenia elementów. |
| argument 2 |Tak |tablica lub obiekt |Druga wartość do użycia dla łączenia elementów. |
| dodatkowe argumenty |Nie |tablica lub obiekt |Dodatkowe wartości do użycia dla elementów łączących. |

### <a name="return-value"></a>Wartość zwracana

Tablica lub obiekt.

### <a name="example"></a>Przykład

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json) pokazuje, jak używać unii z tablicami i obiektami:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c1"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"three": "c2", "four": "d", "five": "e"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["three", "four"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[union(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[union(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| ObjectOutput | Obiekt | {"jeden": "a", "dwa": "b", "trzy": "c2", "cztery": "d", "pięć": "e"} |
| arrayOutput | Tablica | ["jeden", "dwa", "trzy", "cztery"] |

Aby wdrożyć ten przykładowy szablon za pomocą interfejsu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/union.json
```

Aby wdrożyć ten przykładowy szablon za pomocą programu PowerShell, należy użyć:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/union.json
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać opis sekcji w szablonie usługi Azure Resource Manager, zobacz [Tworzenie szablonów usługi Azure Resource Manager](template-syntax.md).
* Aby scalić wiele szablonów, zobacz [Używanie połączonych szablonów z usługą Azure Resource Manager](linked-templates.md).
* Aby iterować określoną liczbę razy podczas tworzenia typu zasobu, zobacz [Tworzenie wielu wystąpień zasobów w usłudze Azure Resource Manager](copy-resources.md).
* Aby zobaczyć, jak wdrożyć utworzony szablon, zobacz [Wdrażanie aplikacji za pomocą szablonu usługi Azure Resource Manager](deploy-powershell.md).

