---
title: Funkcje szablonu — ciąg znaków
description: W tym artykule opisano funkcje używane w szablonie usługi Azure Resource Manager do pracy z ciągami.
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: c0517375b273384f263e8ba421995d4afb6c193b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982418"
---
# <a name="string-functions-for-arm-templates"></a>Funkcje ciągów dla szablonów ARM

Menedżer zasobów udostępnia następujące funkcje do pracy z ciągami w szablonach usługi Azure Resource Manager (ARM):

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [Concat](#concat)
* [Zawiera](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [Pusty](#empty)
* [Endswith](#endswith)
* [Pierwszym](#first)
* [Formacie](#format)
* [Identyfikator guid](#guid)
* [Indexof](#indexof)
* [Ostatnio](#last)
* [Lastindexof](#lastindexof)
* [Długość](#length)
* [nowyGuid](#newguid)
* [Padleft](#padleft)
* [Zastąpić](#replace)
* [Pominąć](#skip)
* [split](#split)
* [Startswith](#startswith)
* [Ciąg](#string)
* [Podciąg](#substring)
* [wziąć](#take)
* [Tolower](#tolower)
* [Toupper](#toupper)
* [Przycinanie](#trim)
* [uniqueString](#uniquestring)
* [Identyfikator uri](#uri)
* [uriComponent](#uricomponent)
* [uriComponentToString](#uricomponenttostring)

## <a name="base64"></a>base64

`base64(inputString)`

Zwraca podstawową 64 reprezentację ciągu wejściowego.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| Inputstring |Tak |ciąg |Wartość zwracana jako reprezentacja base64. |

### <a name="return-value"></a>Wartość zwracana

Ciąg zawierający reprezentację base64.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) pokazuje, jak używać funkcji base64.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| base64Output | Ciąg | b25lLCB0d28sIHRocmVl |
| toStringOutput | Ciąg | Raz dwa trzy |
| toJsonOutput | Obiekt | {"jeden": "a", "dwa": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Konwertuje reprezentację base64 na obiekt JSON.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| base64Value |Tak |ciąg |Reprezentacja base64 do konwersji na obiekt JSON. |

### <a name="return-value"></a>Wartość zwracana

Obiekt JSON.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) używa funkcji base64ToJson do konwersji wartości base64:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| base64Output | Ciąg | b25lLCB0d28sIHRocmVl |
| toStringOutput | Ciąg | Raz dwa trzy |
| toJsonOutput | Obiekt | {"jeden": "a", "dwa": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Konwertuje reprezentację base64 na ciąg.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| base64Value |Tak |ciąg |Reprezentacja base64 do konwersji na ciąg. |

### <a name="return-value"></a>Wartość zwracana

Ciąg przekonwertowanych wartości base64.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) używa funkcji base64ToString do konwersji wartości base64:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| base64Output | Ciąg | b25lLCB0d28sIHRocmVl |
| toStringOutput | Ciąg | Raz dwa trzy |
| toJsonOutput | Obiekt | {"jeden": "a", "dwa": "b"} |

## <a name="concat"></a>concat

`concat (arg1, arg2, arg3, ...)`

Łączy wiele wartości ciągów i zwraca ciąg łączony lub łączy wiele tablic i zwraca tablicę łączoną.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| argument1 |Tak |ciąg lub tablica |Pierwszy ciąg lub tablica do łączenia. |
| dodatkowe argumenty |Nie |ciąg lub tablica |Dodatkowe ciągi lub tablice w kolejności sekwencyjnej do łączenia. |

Ta funkcja może przyjmować dowolną liczbę argumentów i może akceptować ciągi lub tablice dla parametrów. Jednak nie można podać zarówno tablice i ciągi dla parametrów. Ciągi są tylko łączone z innymi ciągami.

### <a name="return-value"></a>Wartość zwracana

Ciąg lub tablica łączonych wartości.

### <a name="examples"></a>Przykłady

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

## <a name="contains"></a>zawiera

`contains (container, itemToFind)`

Sprawdza, czy tablica zawiera wartość, obiekt zawiera klucz lub ciąg zawiera podciąg. W porównaniu ciągów jest rozróżniana wielkość liter. Jednak podczas testowania, jeśli obiekt zawiera klucz, porównanie jest bez uwzględniania wielkości liter.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| kontener |Tak |tablica, obiekt lub ciąg |Wartość, która zawiera wartość do znalezienia. |
| itemToZnajduj |Tak |ciąg lub int |Wartość do znalezienia. |

### <a name="return-value"></a>Wartość zwracana

**Wartość true,** jeśli element zostanie znaleziony; w przeciwnym razie **False**.

### <a name="examples"></a>Przykłady

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

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Konwertuje wartość na identyfikator URI danych.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToConvert |Tak |ciąg |Wartość do konwersji na identyfikator URI danych. |

### <a name="return-value"></a>Wartość zwracana

Ciąg sformatowany jako identyfikator URI danych.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) konwertuje wartość na identyfikator URI danych i konwertuje identyfikator URI danych na ciąg:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| dataUriOutput | Ciąg | dane:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | Ciąg | Cześć ludzie! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

Konwertuje wartość sformatowaną identyfikatora URI danych na ciąg.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Tak |ciąg |Wartość identyfikatora URI danych do konwersji. |

### <a name="return-value"></a>Wartość zwracana

Ciąg zawierający przekonwertowane wartości.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) konwertuje wartość na identyfikator URI danych i konwertuje identyfikator URI danych na ciąg:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| dataUriOutput | Ciąg | dane:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | Ciąg | Cześć ludzie! |

## <a name="empty"></a>empty

`empty(itemToTest)`

Określa, czy tablica, obiekt lub ciąg jest pusty.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| itemToTest |Tak |tablica, obiekt lub ciąg |Wartość, aby sprawdzić, czy jest pusta. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **wartość True,** jeśli wartość jest pusta; w przeciwnym razie **False**.

### <a name="examples"></a>Przykłady

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

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Określa, czy ciąg kończy się wartością. Porównanie jest niewrażliwe na argumenty.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToSearch (dojrzał) |Tak |ciąg |Wartość zawierająca element do znalezienia. |
| stringToZnajda |Tak |ciąg |Wartość do znalezienia. |

### <a name="return-value"></a>Wartość zwracana

**Wartość true,** jeśli ostatni znak lub znaki ciągu odpowiadają wartości; w przeciwnym razie **False**.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) pokazuje, jak używać startsWith i endsWith funkcje:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| startsTrue | Wartość logiczna | True |
| startsCapTrue | Wartość logiczna | True |
| startsFalse | Wartość logiczna | False |
| kończyTrue | Wartość logiczna | True |
| endsCapTrue (KończyCapTrue) | Wartość logiczna | True |
| endsFalse (koniec) | Wartość logiczna | False |

## <a name="first"></a>Pierwszym

`first(arg1)`

Zwraca pierwszy znak ciągu lub pierwszy element tablicy.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| argument1 |Tak |tablica lub ciąg |Wartość, aby pobrać pierwszy element lub znak. |

### <a name="return-value"></a>Wartość zwracana

Ciąg pierwszego znaku lub typ (ciąg, int, tablica lub obiekt) pierwszego elementu w tablicy.

### <a name="examples"></a>Przykłady

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

## <a name="format"></a>format

`format(formatString, arg1, arg2, ...)`

Tworzy sformatowany ciąg z wartości wejściowych.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| formatString | Tak | ciąg | Ciąg formatu złożonego. |
| argument1 | Tak | ciąg, liczba całkowita lub wartość logiczna | Wartość do uwzględnienia w sformatowanym ciągu. |
| dodatkowe argumenty | Nie | ciąg, liczba całkowita lub wartość logiczna | Dodatkowe wartości do uwzględnienia w sformatowanym ciągu. |

### <a name="remarks"></a>Uwagi

Ta funkcja służy do formatowania ciągu w szablonie. Używa tych samych opcji formatowania, co metoda [System.String.Format](/dotnet/api/system.string.format) w .NET.

### <a name="examples"></a>Przykłady

Poniższy przykładowy szablon pokazuje, jak używać funkcji formatu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "greeting": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "name": {
            "type": "string",
            "defaultValue": "User"
        },
        "numberToFormat": {
            "type": "int",
            "defaultValue": 8175133
        }
    },
    "resources": [
    ],
    "outputs": {
        "formatTest": {
            "type": "string",
            "value": "[format('{0}, {1}. Formatted number: {2:N0}', parameters('greeting'), parameters('name'), parameters('numberToFormat'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| test formatu | Ciąg | Witam, Użytkownik. Sformatowana liczba: 8,175,133 |

## <a name="guid"></a>Identyfikator GUID

`guid(baseString, ...)`

Tworzy wartość w formacie globalnie unikatowego identyfikatora na podstawie wartości podanych jako parametry.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| baseString |Tak |ciąg |Wartość używana w funkcji mieszania do utworzenia identyfikatora GUID. |
| dodatkowe parametry w razie potrzeby |Nie |ciąg |Można dodać dowolną liczbę ciągów, aby utworzyć wartość określającą poziom unikatowości. |

### <a name="remarks"></a>Uwagi

Ta funkcja jest przydatna, gdy trzeba utworzyć wartość w formacie globalnie unikatowy identyfikator. Podać wartości parametrów, które ograniczają zakres unikatowości dla wyniku. Można określić, czy nazwa jest unikatowa w dół do subskrypcji, grupy zasobów lub wdrożenia.

Zwracana wartość nie jest ciągiem losowym, ale raczej wynikiem funkcji mieszania na parametrach. Zwracana wartość wynosi 36 znaków. Nie jest unikatowy na całym świecie. Aby utworzyć nowy identyfikator GUID, który nie jest oparty na tej wartości skrótu parametrów, należy użyć [newGuid](#newguid) funkcji.

Poniższe przykłady pokazują, jak używać identyfikatora GUID do tworzenia unikatowej wartości dla często używanych poziomów.

Unikatowy zakres subskrypcji

```json
"[guid(subscription().subscriptionId)]"
```

Unikatowy zakres do grupy zasobów

```json
"[guid(resourceGroup().id)]"
```

Unikatowy zakres wdrożenia dla grupy zasobów

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>Wartość zwracana

Ciąg zawierający 36 znaków w formacie unikatowego identyfikatora globalnie.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) zwraca wyniki z guid:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "guidPerSubscription": {
            "value": "[guid(subscription().subscriptionId)]",
            "type": "string"
        },
        "guidPerResourceGroup": {
            "value": "[guid(resourceGroup().id)]",
            "type": "string"
        },
        "guidPerDeployment": {
            "value": "[guid(resourceGroup().id, deployment().name)]",
            "type": "string"
        }
    }
}
```

## <a name="indexof"></a>Indexof

`indexOf(stringToSearch, stringToFind)`

Zwraca pierwszą pozycję wartości w ciągu. Porównanie jest niewrażliwe na argumenty.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToSearch (dojrzał) |Tak |ciąg |Wartość zawierająca element do znalezienia. |
| stringToZnajda |Tak |ciąg |Wartość do znalezienia. |

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita reprezentująca pozycję elementu do znalezienia. Wartość jest od zera. Jeśli element nie zostanie znaleziony, zwracany jest -1.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) pokazuje, jak używać funkcji indexOf i lastIndexOf:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| pierwszyT | int | 0 |
| lastT (wł.) | int | 3 |
| firstString (sznurek) | int | 2 |
| lastString | int | 0 |
| Notfound | int | -1 |

## <a name="last"></a>ostatni

`last (arg1)`

Zwraca ostatni znak ciągu lub ostatni element tablicy.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| argument1 |Tak |tablica lub ciąg |Wartość do pobrania ostatniego elementu lub znaku. |

### <a name="return-value"></a>Wartość zwracana

Ciąg ostatniego znaku lub typu (ciąg, int, tablica lub obiekt) ostatniego elementu w tablicy.

### <a name="examples"></a>Przykłady

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

## <a name="lastindexof"></a>Lastindexof

`lastIndexOf(stringToSearch, stringToFind)`

Zwraca ostatnią pozycję wartości w ciągu. Porównanie jest niewrażliwe na argumenty.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToSearch (dojrzał) |Tak |ciąg |Wartość zawierająca element do znalezienia. |
| stringToZnajda |Tak |ciąg |Wartość do znalezienia. |

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita reprezentująca ostatnią pozycję elementu do znalezienia. Wartość jest od zera. Jeśli element nie zostanie znaleziony, zwracany jest -1.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) pokazuje, jak używać funkcji indexOf i lastIndexOf:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| pierwszyT | int | 0 |
| lastT (wł.) | int | 3 |
| firstString (sznurek) | int | 2 |
| lastString | int | 0 |
| Notfound | int | -1 |

## <a name="length"></a>length

`length(string)`

Zwraca liczbę znaków w ciągu, elementy w tablicy lub właściwości na poziomie głównym w obiekcie.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| argument1 |Tak |tablica, ciąg lub obiekt |Tablica do użycia do uzyskania liczby elementów, ciąg do użycia do uzyskania liczby znaków lub obiekt do użycia do uzyskania liczby właściwości na poziomie głównym. |

### <a name="return-value"></a>Wartość zwracana

Int. 

### <a name="examples"></a>Przykłady

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

## <a name="newguid"></a>nowyGuid

`newGuid()`

Zwraca wartość w formacie unikatowego identyfikatora globalnie. **Ta funkcja może być używana tylko w wartości domyślnej parametru.**

### <a name="remarks"></a>Uwagi

Tej funkcji można używać tylko w wyrażeniu dla domyślnej wartości parametru. Użycie tej funkcji w dowolnym miejscu w szablonie zwraca błąd. Funkcja nie jest dozwolona w innych częściach szablonu, ponieważ zwraca inną wartość za każdym razem, gdy jest wywoływana. Wdrożenie tego samego szablonu o tych samych parametrach nie będzie niezawodnie dawać takie same wyniki.

NewGuid funkcja różni się od funkcji [guid,](#guid) ponieważ nie ma żadnych parametrów. Podczas wywoływania guid z tym samym parametrem, zwraca ten sam identyfikator za każdym razem. Identyfikator guid należy używać, gdy trzeba niezawodnie wygenerować ten sam identyfikator GUID dla określonego środowiska. Użyj newGuid, gdy potrzebujesz innego identyfikatora za każdym razem, takich jak wdrażanie zasobów w środowisku testowym.

Funkcja newGuid używa [guid struktury](/dotnet/api/system.guid) w .NET Framework do generowania globalnie unikatowy identyfikator.

Jeśli [użyjesz opcji, aby ponownie wdrożyć wcześniejsze pomyślne wdrożenie](rollback-on-error.md), a wcześniejsze wdrożenie zawiera parametr, który używa newGuid, parametr nie jest ponownie oceniony. Zamiast tego wartość parametru z wcześniejszego wdrożenia jest automatycznie ponownie w wdrożeniu wycofywania.

W środowisku testowym może być konieczne wielokrotne wdrażanie zasobów, które są dostępne tylko przez krótki czas. Zamiast konstruowania unikatowych nazw, można użyć newGuid z [uniqueString](#uniquestring) do tworzenia unikatowych nazw.

Należy zachować ostrożność ponownego rozmieszczenia szablonu, który opiera się na newGuid funkcji dla wartości domyślnej. Po ponownym wedniesieniu i nie podasz wartości parametru, funkcja jest ponownie indeksowana. Jeśli chcesz zaktualizować istniejący zasób, a nie utworzyć nowy, przekaż wartość parametru z wcześniejszego wdrożenia.

### <a name="return-value"></a>Wartość zwracana

Ciąg zawierający 36 znaków w formacie unikatowego identyfikatora globalnie.

### <a name="examples"></a>Przykłady

Poniższy przykładowy szablon przedstawia parametr z nowym identyfikatorem.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "guidValue": {
            "type": "string",
            "defaultValue": "[newGuid()]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "guidOutput": {
            "type": "string",
            "value": "[parameters('guidValue')]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu różnią się dla każdego wdrożenia, ale będą podobne do:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| guidOutput (włask. | ciąg | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

W poniższym przykładzie użyto newGuid funkcji do utworzenia unikatowej nazwy dla konta magazynu. Ten szablon może działać w środowisku testowym, w którym konto magazynu istnieje przez krótki czas i nie jest ponownie rozmieszczony.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "guidValue": {
            "type": "string",
            "defaultValue": "[newGuid()]"
        }
    },
    "variables": {
        "storageName": "[concat('storage', uniqueString(parameters('guidValue')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "West US",
            "apiVersion": "2018-07-01",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {}
        }
    ],
    "outputs": {
        "nameOutput": {
            "type": "string",
            "value": "[variables('storageName')]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu różnią się dla każdego wdrożenia, ale będą podobne do:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| nazwaOutput | ciąg | magazynnziwvyru7uxie |


## <a name="padleft"></a>Padleft

`padLeft(valueToPad, totalLength, paddingCharacter)`

Zwraca ciąg wyrównany do prawej, dodając znaki po lewej stronie, aż do osiągnięcia całkowitej określonej długości.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| valueToPad |Tak |ciąg lub int |Wartość wyrównana do prawej. |
| całkowita Wartość |Tak |int |Całkowita liczba znaków w zwróconym ciągu. |
| wyściółkaWytka |Nie |pojedynczy znak |Znak do użycia dla lewej dopełnienie, aż do osiągnięcia całkowitej długości. Wartością domyślną jest spacja. |

Jeśli oryginalny ciąg jest dłuższy niż liczba znaków do konsoli, nie są dodawane żadne znaki.

### <a name="return-value"></a>Wartość zwracana

Ciąg z co najmniej określoną liczbą znaków.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) pokazuje, jak zasypać wartość parametru dostarczoną przez użytkownika, dodając znak zerowy, dopóki nie osiągnie całkowitej liczby znaków. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123"
        }
    },
    "resources": [],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[padLeft(parameters('testString'),10,'0')]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| ciągOutput | Ciąg | 0000000123 |

## <a name="replace"></a>Zastąp

`replace(originalString, oldString, newString)`

Zwraca nowy ciąg ze wszystkimi wystąpieniami jednego ciągu zastąpione przez inny ciąg.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| originalString |Tak |ciąg |Wartość, która ma wszystkie wystąpienia jednego ciągu zastąpione przez inny ciąg. |
| stare Sznurek |Tak |ciąg |Ciąg do usunięcia z oryginalnego ciągu. |
| newString (Nowe sznurek) |Tak |ciąg |Ciąg, który ma zostać dodany zamiast usuniętego ciągu. |

### <a name="return-value"></a>Wartość zwracana

Ciąg z zastąpionymi znakami.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) pokazuje, jak usunąć wszystkie myślniki z ciągu dostarczonego przez użytkownika i jak zastąpić część ciągu innym ciągiem.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123-123-1234"
        }
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'-', '')]"
        },
        "secondOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| pierwszyOutput | Ciąg | 1231231234 |
| secondOutput (Plut) | Ciąg | 123-123-xxxx |

## <a name="skip"></a>Pomiń

`skip(originalValue, numberToSkip)`

Zwraca ciąg ze wszystkimi znakami po określonej liczbie znaków lub tablicę ze wszystkimi elementami po określonej liczbie elementów.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| originalValue |Tak |tablica lub ciąg |Tablica lub ciąg do pominięcia. |
| numerToSkip |Tak |int |Liczba elementów lub znaków do pominięcia. Jeśli ta wartość wynosi 0 lub mniej, zwracane są wszystkie elementy lub znaki w wartości. Jeśli jest większy niż długość tablicy lub ciągu, zwracana jest pusta tablica lub ciąg. |

### <a name="return-value"></a>Wartość zwracana

Tablica lub ciąg.

### <a name="examples"></a>Przykłady

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

## <a name="split"></a>split

`split(inputString, delimiter)`

Zwraca tablicę ciągów, która zawiera podciągi ciągu wejściowego, które są rozdzielane przez określone ograniczniki.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| Inputstring |Tak |ciąg |Ciąg do podziału. |
| ogranicznik |Tak |ciąg lub tablica ciągów |Ogranicznik do użycia do dzielenia ciągu. |

### <a name="return-value"></a>Wartość zwracana

Tablica ciągów.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) dzieli ciąg wejściowy przecinkiem i przecinkiem lub średnikiem.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstString": {
            "type": "string",
            "defaultValue": "one,two,three"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "one;two,three"
        }
    },
    "variables": {
        "delimiters": [ ",", ";" ]
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "array",
            "value": "[split(parameters('firstString'),',')]"
        },
        "secondOutput": {
            "type": "array",
            "value": "[split(parameters('secondString'),variables('delimiters'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| pierwszyOutput | Tablica | ["jeden", "dwa", "trzy"] |
| secondOutput (Plut) | Tablica | ["jeden", "dwa", "trzy"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Określa, czy ciąg zaczyna się od wartości. Porównanie jest niewrażliwe na argumenty.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToSearch (dojrzał) |Tak |ciąg |Wartość zawierająca element do znalezienia. |
| stringToZnajda |Tak |ciąg |Wartość do znalezienia. |

### <a name="return-value"></a>Wartość zwracana

**Wartość true,** jeśli pierwszy znak lub znaki ciągu odpowiadają wartości; w przeciwnym razie **False**.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) pokazuje, jak używać startsWith i endsWith funkcje:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| startsTrue | Wartość logiczna | True |
| startsCapTrue | Wartość logiczna | True |
| startsFalse | Wartość logiczna | False |
| kończyTrue | Wartość logiczna | True |
| endsCapTrue (KończyCapTrue) | Wartość logiczna | True |
| endsFalse (koniec) | Wartość logiczna | False |

## <a name="string"></a>ciąg

`string(valueToConvert)`

Konwertuje określoną wartość na ciąg.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| wartośćToKonwert |Tak | Dowolne |Wartość do konwersji na ciąg. Można przekonwertować dowolny typ wartości, w tym obiekty i tablice. |

### <a name="return-value"></a>Wartość zwracana

Ciąg przekonwertowanych wartości.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) pokazuje, jak konwertować różne typy wartości na ciągi znaków:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testObject": {
            "type": "object",
            "defaultValue": {
                "valueA": 10,
                "valueB": "Example Text"
            }
        },
        "testArray": {
            "type": "array",
            "defaultValue": [
                "a",
                "b",
                "c"
            ]
        },
        "testInt": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "resources": [],
    "outputs": {
        "objectOutput": {
            "type": "string",
            "value": "[string(parameters('testObject'))]"
        },
        "arrayOutput": {
            "type": "string",
            "value": "[string(parameters('testArray'))]"
        },
        "intOutput": {
            "type": "string",
            "value": "[string(parameters('testInt'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| ObjectOutput | Ciąg | {"valueA":10,"valueB":"Przykładowy tekst"} |
| arrayOutput | Ciąg | ["a","b,","c"] |
| intOutput (Nieuprzejmych) | Ciąg | 5 |

## <a name="substring"></a>Podciąg

`substring(stringToParse, startIndex, length)`

Zwraca podciąg, który rozpoczyna się od określonej pozycji znaku i zawiera określoną liczbę znaków.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToParse |Tak |ciąg |Oryginalny ciąg, z którego wyodrębniany jest podciąg. |
| Startindex |Nie |int |Pozycja znaku początkowego od zera dla podciągu. |
| length |Nie |int |Liczba znaków podciągów. Musi odnosić się do lokalizacji w ciągu. Musi być zero lub większa. |

### <a name="return-value"></a>Wartość zwracana

Podciąg. Lub pusty ciąg, jeśli długość wynosi zero.

### <a name="remarks"></a>Uwagi

Funkcja kończy się niepowodzeniem, gdy podciąg wykracza poza koniec ciągu lub gdy długość jest mniejsza niż zero. Poniższy przykład kończy się niepowodzeniem z błędem "Parametry indeksu i długości muszą odwoływać się do lokalizacji w ciągu. Parametr indeksu: '0', parametr length: '11', długość parametru ciągu: '10'.".

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) wyodrębnia podciąg z parametru.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        }
    },
    "resources": [],
    "outputs": {
        "substringOutput": {
            "value": "[substring(parameters('testString'), 4, 3)]",
            "type": "string"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| substringOutput | Ciąg | dwa |

## <a name="take"></a>wziąć

`take(originalValue, numberToTake)`

Zwraca ciąg z określoną liczbą znaków od początku ciągu lub tablicę z określoną liczbą elementów od początku tablicy.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| originalValue |Tak |tablica lub ciąg |Tablica lub ciąg do podjęcia elementów z. |
| numerDoTake |Tak |int |Liczba elementów lub znaków do podjęcia. Jeśli ta wartość jest 0 lub mniej, zwracana jest pusta tablica lub ciąg. Jeśli jest większy niż długość danej tablicy lub ciągu, wszystkie elementy w tablicy lub ciągu są zwracane. |

### <a name="return-value"></a>Wartość zwracana

Tablica lub ciąg.

### <a name="examples"></a>Przykłady

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

## <a name="tolower"></a>Tolower

`toLower(stringToChange)`

Konwertuje określony ciąg na małe litery.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToChange (doł. |Tak |ciąg |Wartość do konwersji na małe litery. |

### <a name="return-value"></a>Wartość zwracana

Ciąg konwertowany na małe litery.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) konwertuje wartość parametru na małe litery i na wielkie litery.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| toLowerOutput | Ciąg | Raz dwa trzy |
| toUpperOutput | Ciąg | Raz dwa trzy |

## <a name="toupper"></a>Toupper

`toUpper(stringToChange)`

Konwertuje określony ciąg na wielkie litery.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToChange (doł. |Tak |ciąg |Wartość do konwersji na wielkie litery. |

### <a name="return-value"></a>Wartość zwracana

Ciąg konwertowany na wielkie litery.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) konwertuje wartość parametru na małe litery i na wielkie litery.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| toLowerOutput | Ciąg | Raz dwa trzy |
| toUpperOutput | Ciąg | Raz dwa trzy |

## <a name="trim"></a>Przycinanie

`trim (stringToTrim)`

Usuwa wszystkie początkowe i końcowe znaki odstępu z określonego ciągu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToTrim |Tak |ciąg |Wartość do przycięcia. |

### <a name="return-value"></a>Wartość zwracana

Ciąg bez znaków początkowych i końcowych odstępu.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) przycina znaki odstępu z parametru.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "    one two three   "
        }
    },
    "resources": [],
    "outputs": {
        "return": {
            "type": "string",
            "value": "[trim(parameters('testString'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| return | Ciąg | Raz dwa trzy |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

Tworzy deterministyczny ciąg mieszania na podstawie wartości podanych jako parametry. 

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| baseString |Tak |ciąg |Wartość używana w funkcji mieszania do tworzenia unikatowego ciągu. |
| dodatkowe parametry w razie potrzeby |Nie |ciąg |Można dodać dowolną liczbę ciągów, aby utworzyć wartość określającą poziom unikatowości. |

### <a name="remarks"></a>Uwagi

Ta funkcja jest przydatna, gdy trzeba utworzyć unikatową nazwę zasobu. Podać wartości parametrów, które ograniczają zakres unikatowości dla wyniku. Można określić, czy nazwa jest unikatowa w dół do subskrypcji, grupy zasobów lub wdrożenia. 

Zwracana wartość nie jest ciągiem losowym, ale raczej wynikiem funkcji mieszania. Zwracana wartość wynosi 13 znaków. Nie jest unikatowy na całym świecie. Można połączyć wartość z prefiksem z konwencji nazewnictwa, aby utworzyć nazwę, która ma znaczenie. Poniższy przykład przedstawia format zwracanej wartości. Rzeczywista wartość zależy od podanych parametrów.

    tcvhiyu5h2o5o

Poniższe przykłady pokazują, jak używać uniqueString do tworzenia unikatowej wartości dla często używanych poziomów.

Unikatowy zakres subskrypcji

```json
"[uniqueString(subscription().subscriptionId)]"
```

Unikatowy zakres do grupy zasobów

```json
"[uniqueString(resourceGroup().id)]"
```

Unikatowy zakres wdrożenia dla grupy zasobów

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

W poniższym przykładzie pokazano, jak utworzyć unikatową nazwę konta magazynu na podstawie grupy zasobów. Wewnątrz grupy zasobów nazwa nie jest unikatowa, jeśli jest skonstruowana w ten sam sposób.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

Jeśli trzeba utworzyć nową unikatową nazwę za każdym razem, gdy wdrażasz szablon i nie zamierzasz aktualizować zasobu, możesz użyć funkcji [utcNow](template-functions-date.md#utcnow) z uniqueString. Można użyć tego podejścia w środowisku testowym. Na przykład zobacz [utcNow](template-functions-date.md#utcnow).

### <a name="return-value"></a>Wartość zwracana

Ciąg zawierający 13 znaków.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) zwraca wyniki z uniquestring:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "uniqueRG": {
            "value": "[uniqueString(resourceGroup().id)]",
            "type" : "string"
        },
        "uniqueDeploy": {
            "value": "[uniqueString(resourceGroup().id, deployment().name)]",
            "type" : "string"
        }
    }
}
```

## <a name="uri"></a>Identyfikator uri

`uri (baseUri, relativeUri)`

Tworzy bezwzględny identyfikator URI przez połączenie baseUri i relativeUri ciąg.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| Baseuri |Tak |ciąg |Ciąg uri podstawy. Należy uważać, aby obserwować zachowanie dotyczące obsługi ukośnika końcowego ('/'), jak opisano w poniższej tabeli.  |
| Relativeuri |Tak |ciąg |Względny ciąg uri, aby dodać do ciągu uri podstawy. |

* Jeśli **baseUri** kończy się cięciem końcowym, wynikiem jest po prostu **baseUri,** po którym następuje **relativeUri**.

* Jeśli **baseUri** nie kończy się na końcu ukośnika jeden z dwóch rzeczy się dzieje.  

   * Jeśli **baseUri** nie ma ukośników w ogóle (oprócz "//" w pobliżu przodu) wynik jest po prostu **baseUri** następuje **relativeUri**.

   * Jeśli **baseUri** ma kilka ukośników, ale nie kończy się ukośnikiem, wszystko od ostatniego ukośnika jest usuwane z **baseUri,** a wynik jest **baseUri,** po którym następuje **relativeUri**.
     
Oto kilka przykładów:

```
uri('http://contoso.org/firstpath', 'myscript.sh') -> http://contoso.org/myscript.sh
uri('http://contoso.org/firstpath/', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json/', 'myscript.sh') -> http://contoso.org/firstpath/azuredeploy.json/myscript.sh
```
Aby uzyskać szczegółowe informacje, **baseUri** i **relativeUri** parametry są rozpoznawane zgodnie z informacjami w [RFC 3986, sekcja 5](https://tools.ietf.org/html/rfc3986#section-5).

### <a name="return-value"></a>Wartość zwracana

Ciąg reprezentujący bezwzględny identyfikator URI dla wartości bazowych i względnych.

### <a name="examples"></a>Przykłady

W poniższym przykładzie pokazano, jak skonstruować łącze do szablonu zagnieżdżonego na podstawie wartości szablonu nadrzędnego.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) pokazuje, jak używać uri, uriComponent i uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| uriOutput | Ciąg | `http://contoso.com/resources/nested/azuredeploy.json` |
| składnikOutput | Ciąg | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Ciąg | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

Koduje identyfikator URI.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToEncode |Tak |ciąg |Wartość do zakodowania. |

### <a name="return-value"></a>Wartość zwracana

Ciąg wartości zakodowanej identyfikatora URI.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) pokazuje, jak używać uri, uriComponent i uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| uriOutput | Ciąg | `http://contoso.com/resources/nested/azuredeploy.json` |
| składnikOutput | Ciąg | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Ciąg | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

Zwraca ciąg wartości zakodowanej identyfikatora URI.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Tak |ciąg |Wartość zakodowana w identyfikatorze URI do konwersji na ciąg. |

### <a name="return-value"></a>Wartość zwracana

Zdekodowany ciąg wartości zakodowanej identyfikatora URI.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) pokazuje, jak używać uri, uriComponent i uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| uriOutput | Ciąg | `http://contoso.com/resources/nested/azuredeploy.json` |
| składnikOutput | Ciąg | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Ciąg | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać opis sekcji w szablonie usługi Azure Resource Manager, zobacz [Tworzenie szablonów usługi Azure Resource Manager](template-syntax.md).
* Aby scalić wiele szablonów, zobacz [Używanie połączonych szablonów z usługą Azure Resource Manager](linked-templates.md).
* Aby iterować określoną liczbę razy podczas tworzenia typu zasobu, zobacz [Tworzenie wielu wystąpień zasobów w usłudze Azure Resource Manager](copy-resources.md).
* Aby zobaczyć, jak wdrożyć utworzony szablon, zobacz [Wdrażanie aplikacji za pomocą szablonu usługi Azure Resource Manager](deploy-powershell.md).

