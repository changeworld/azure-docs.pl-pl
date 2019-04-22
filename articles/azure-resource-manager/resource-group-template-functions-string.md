---
title: Funkcje szablonu usługi Azure Resource Manager — ciąg | Dokumentacja firmy Microsoft
description: Opisuje funkcje, które można użyć w szablonie usługi Azure Resource Manager do pracy z ciągami.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/08/2019
ms.author: tomfitz
ms.openlocfilehash: bf9faa34c1f0923761ce583c22ba4084d7bd42a8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59278789"
---
# <a name="string-functions-for-azure-resource-manager-templates"></a>Funkcje ciągów dla szablonów usługi Azure Resource Manager

Usługa Resource Manager zapewnia następujące funkcje do pracy z ciągami:

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [concat](#concat)
* [zawiera](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [pusty](#empty)
* [endsWith](#endswith)
* [pierwszy](#first)
* [Format](#format)
* [Identyfikator GUID](#guid)
* [indexOf](#indexof)
* [last](#last)
* [lastIndexOf](#lastindexof)
* [Długość](#length)
* [newGuid](#newguid)
* [padLeft](#padleft)
* [Zastąp](#replace)
* [skip](#skip)
* [split](#split)
* [startsWith](#startswith)
* [ciąg](#string)
* [podciąg](#substring)
* [Wypełnij](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [trim](#trim)
* [uniqueString](#uniquestring)
* [Identyfikator URI](#uri)
* [uriComponent](#uricomponent)
* [uriComponentToString](#uricomponenttostring)
* [utcNow](#utcnow)

## <a name="base64"></a>base64

`base64(inputString)`

Zwraca reprezentację base64 ciągu wejściowego.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| inputString |Yes |string |Wartość do zwrócenia reprezentacji base64. |

### <a name="return-value"></a>Wartość zwracana

Ciąg zawierający reprezentacji base64.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) pokazuje, jak korzystać z funkcji base64.

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | Raz dwa trzy |
| toJsonOutput | Obiekt | {"jeden": "", "dwóch": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Konwertuje obiekt JSON reprezentacji base64.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| base64Value |Yes |string |Reprezentacji base64, który można przekonwertować na obiekt JSON. |

### <a name="return-value"></a>Wartość zwracana

Obiekt JSON.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) funkcja base64ToJson można przekonwertować wartości base64:

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | Raz dwa trzy |
| toJsonOutput | Obiekt | {"jeden": "", "dwóch": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Konwertuje ciąg reprezentacji base64.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| base64Value |Yes |string |Reprezentacji base64 do przekonwertowania na ciąg. |

### <a name="return-value"></a>Wartość zwracana

Ciąg wartość przekonwertowanego base64.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) funkcja base64ToString można przekonwertować wartości base64:

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | Raz dwa trzy |
| toJsonOutput | Obiekt | {"jeden": "", "dwóch": "b"} |

## <a name="concat"></a>concat

`concat (arg1, arg2, arg3, ...)`

Łączy wiele wartości parametrów i zwraca połączony ciąg lub łączy wiele tablic i zwraca tablicę połączonych.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |tablica lub ciąg |Pierwsza wartość łączenia. |
| dodatkowe argumenty |Nie |string |Dodatkowe wartości w kolejności sekwencyjnej dla łączenia. |

### <a name="return-value"></a>Wartość zwracana
Ciąg lub tablicę wartości łączonych.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) pokazuje, jak łączyć dwóch wartości ciągu i zwraca połączony ciąg.

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| concatOutput | String | prefix-5yj4yjf5mbg72 |

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) pokazuje, jak połączyć dwie tablice.

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| Wróć | Tablica | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

## <a name="contains"></a>zawiera

`contains (container, itemToFind)`

Sprawdza, czy tablica zawiera wartość, obiekt zawiera klucz lub ciąg zawiera podciąg. Porównanie ciągów jest rozróżniana wielkość liter. Jednak podczas testowania, jeśli obiekt zawiera klucz, wynikiem porównania jest rozróżniana wielkość liter.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| kontener |Yes |Array, obiekt lub ciągu |Wartość, która zawiera wartość do znalezienia. |
| itemToFind |Yes |ciąg lub int |Wartość do znalezienia. |

### <a name="return-value"></a>Wartość zwracana

**Wartość true,** Jeśli element zostanie znaleziony; w przeciwnym razie **False**.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) pokazuje sposób użycia zawiera z różnymi typami:

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| stringTrue | Bool | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Konwertuje wartość identyfikatora URI danych.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToConvert |Yes |string |Wartość do przekonwertowania na dane identyfikatora URI. |

### <a name="return-value"></a>Wartość zwracana

Ciąg w formacie identyfikatora URI danych.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) konwertuje wartość identyfikatora URI danych, a następnie konwertuje ciąg identyfikatora URI danych:

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Cześć ludzie! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

Konwertuje danych URI sformatowana wartość na ciąg.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Yes |string |Dane wartości identyfikatora URI do przekonwertowania. |

### <a name="return-value"></a>Wartość zwracana

Ciąg zawierający wartość przekonwertowana.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) konwertuje wartość identyfikatora URI danych, a następnie konwertuje ciąg identyfikatora URI danych:

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Cześć ludzie! |

## <a name="empty"></a>pusty

`empty(itemToTest)`

Określa, czy tablica, obiekt lub ciąg jest pusty.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| itemToTest |Yes |Array, obiekt lub ciągu |Wartość, aby sprawdzić, czy jest pusta. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **True** Jeśli wartość jest pusta; w przeciwnym razie **False**.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) sprawdza, czy Tablica obiektów i ciąg są puste.

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Określa, czy ciąg kończy się wartością. W porównaniu jest rozróżniana wielkość liter.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |string |Wartość, która zawiera element, aby znaleźć. |
| stringToFind |Yes |string |Wartość do znalezienia. |

### <a name="return-value"></a>Wartość zwracana

**Wartość true,** Jeśli ostatni znak lub znaki ciągu pasuje do wartości; w przeciwnym razie **False**.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) przedstawia sposób użycia funkcji startsWith i endsWith:

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="first"></a>pierwszy

`first(arg1)`

Zwraca pierwszy znak ciągu lub pierwszy element tablicy.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |tablicy lub ciągu |Wartość można pobrać pierwszy element lub znak. |

### <a name="return-value"></a>Wartość zwracana

Ciągu pierwszy znak lub typ (ciąg, int, tablicy lub obiektu) do pierwszego elementu w tablicy.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) pokazano, jak pierwsza funkcja z tablicą i ciąg.

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| arrayOutput | String | jeden |
| stringOutput | String | O |

## <a name="format"></a>format

`format(formatString, arg1, arg2, ...)`

Tworzy ciąg formatowania na podstawie wartości wejściowych.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| formatString | Yes | string | Ciąg formatu złożonego. |
| arg1 | Yes | ciąg, liczba całkowita lub atrybut typu wartość logiczna | Wartość, aby uwzględnić w sformatowanym ciągu. |
| dodatkowe argumenty | Nie | ciąg, liczba całkowita lub atrybut typu wartość logiczna | Dodatkowe wartości, aby uwzględnić w sformatowanym ciągu. |

### <a name="remarks"></a>Uwagi

Użyj tej funkcji można sformatować ciągu w szablonie. Używa ona te same opcje formatowania, co [elementu System.String.Format](/dotnet/api/system.string.format) metody na platformie .NET.

### <a name="examples"></a>Przykłady

Następującego przykładowego szablonu pokazuje, jak korzystać z funkcji formatu.

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| formatTest | String | Witaj, użytkownika. Sformatowany numer: 8,175,133 |

## <a name="guid"></a>Identyfikator GUID

`guid(baseString, ...)`

Tworzy wartość w formacie Unikatowy identyfikator globalny na podstawie wartości, podane jako parametry.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| baseString |Yes |string |Wartość używana w funkcji wyznaczania wartości skrótu, aby utworzyć identyfikator GUID. |
| dodatkowe parametry zgodnie z potrzebami |Nie |string |Możesz dodać dowolną liczbę ciągów, w razie potrzeby można utworzyć wartości, który określa poziom unikatowości. |

### <a name="remarks"></a>Uwagi

Ta funkcja jest przydatne, gdy trzeba utworzyć wartość w formacie globalnie unikatowy identyfikator. Możesz podać wartości parametrów, które ograniczają zakres unikatowości dla wyniku. Można określić, czy nazwa jest unikatowa w dół do subskrypcji, grupy zasobów lub wdrażania.

Zwracana wartość nie jest losowy ciąg, ale raczej wynik funkcji skrótu o parametrach. Zwrócona wartość jest 36 znaków. Nie jest unikatowa w skali globalnej. Aby utworzyć nowy identyfikator GUID, który nie jest oparty na wartość tego skrótu parametrów, należy użyć [newGuid](#newguid) funkcji.

Poniższe przykłady pokazują, jak utworzyć unikatową wartość dla często używanych poziomy za pomocą identyfikatora guid.

Unikatowe ograniczone do subskrypcji

```json
"[guid(subscription().subscriptionId)]"
```

Unikatowa w zakresie grupy zasobów

```json
"[guid(resourceGroup().id)]"
```

Unikatowe ograniczone do wdrożenia dla grupy zasobów

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>Wartość zwracana

Ciąg zawierający 36 znaków w formacie globalnie unikatowy identyfikator.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) zwraca wyniki z identyfikatorem guid:

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

## <a name="indexof"></a>indexOf

`indexOf(stringToSearch, stringToFind)`

Zwraca pierwszą pozycję wartości w ciągu. W porównaniu jest rozróżniana wielkość liter.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |string |Wartość, która zawiera element, aby znaleźć. |
| stringToFind |Yes |string |Wartość do znalezienia. |

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita wskazująca pozycję elementu do znalezienia. Wartość jest liczony od zera. Jeśli element nie zostanie znalezione, jest zwracana wartość -1.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) przedstawia sposób użycia funkcji indexOf i lastIndexOf:

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| NotFound | Int | -1 |

## <a name="last"></a>ostatni

`last (arg1)`

Zwraca ostatni znak w ciągu lub ostatniego elementu w tablicy.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |tablicy lub ciągu |Wartość można pobrać ostatniego elementu lub znak. |

### <a name="return-value"></a>Wartość zwracana

Ciągu ostatni znak lub typ ostatniego elementu w tablicy (ciąg, int, tablicy lub obiektu).

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) ilustruje sposób używania ostatniej funkcji za pomocą tablicy i ciąg.

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| arrayOutput | String | trzy |
| stringOutput | String | e |

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

Zwraca pozycję ostatniego wartości w ciągu. W porównaniu jest rozróżniana wielkość liter.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |string |Wartość, która zawiera element, aby znaleźć. |
| stringToFind |Yes |string |Wartość do znalezienia. |

### <a name="return-value"></a>Wartość zwracana

Liczba całkowita, która reprezentuje ostatnia pozycja elementu do znalezienia. Wartość jest liczony od zera. Jeśli element nie zostanie znalezione, jest zwracana wartość -1.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) przedstawia sposób użycia funkcji indexOf i lastIndexOf:

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| NotFound | Int | -1 |

## <a name="length"></a>Długość

`length(string)`

Zwraca liczbę znaków w ciągu lub elementów w tablicy.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |tablicy lub ciągu |Tablica do użycia w celu uzyskania liczba elementów lub ciąg do użycia w celu uzyskania liczby znaków. |

### <a name="return-value"></a>Wartość zwracana

Int. 

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) pokazuje, jak za pomocą usług długość tablicy i parametry:

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
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |

## <a name="newguid"></a>newGuid

`newGuid()`

Zwraca wartość w formacie globalnie unikatowy identyfikator. **Tej funkcji można używać tylko wartości domyślnej dla parametru.**

### <a name="remarks"></a>Uwagi

Tej funkcji w wyrażeniu można używać tylko dla wartości domyślnej parametru. Użycie tej funkcji w jakimkolwiek innym miejscu w szablonie zwraca błąd. Funkcja nie jest dozwolona w innych części szablonu, ponieważ zwraca inną wartość w każdym razem, gdy jest wywoływana. Wdrażanie tego samego szablonu z tymi samymi parametrami w takich sytuacjach przydałaby generowane niezawodnie takie same wyniki.

Funkcja newGuid różni się od [guid](#guid) działać, ponieważ on nie przyjmuje żadnych parametrów. Po wywołaniu guid za pomocą tego samego parametru, zwraca ten sam identyfikator każdorazowo. Za pomocą identyfikatora guid musi być niezawodne wygenerowany tego samego identyfikatora GUID dla określonego środowiska. NewGuid należy używać wtedy, gdy konieczne jest inny identyfikator za każdym razem takich jak wdrażanie zasobów w środowisku testowym.

Jeśli używasz [opcji, aby przeprowadzić ponowne wdrożenie wcześniej pomyślnego wdrożenia](resource-group-template-deploy-rest.md#redeploy-when-deployment-fails)i wcześniejsze wdrożenie zawiera parametr, który używa newGuid, parametr nie jest ponownie oceniane. Zamiast tego wartość tego parametru z wcześniejszych wdrożenia jest automatycznie ponownie we wdrożeniu wycofywania.

W środowisku testowym konieczne może być wielokrotnie wdrażać zasoby znajdujące się tylko przez krótki czas. Zamiast tworzenia unikatowych nazw, można użyć newGuid z [uniqueString](#uniquestring) utworzyć unikatowe nazwy.

Należy zachować ostrożność, ponownego wdrażania szablonu, która korzysta z funkcji newGuid dla wartości domyślnej. Podczas ponownego wdrażania i niepodania wartości dla parametru funkcji są ponownie oceniane. Jeśli chcesz zaktualizowanie istniejącego zasobu, zamiast tworzyć nowy są przekazywane w wartości parametru z wcześniejsze wdrożenie.

### <a name="return-value"></a>Wartość zwracana

Ciąg zawierający 36 znaków w formacie globalnie unikatowy identyfikator.

### <a name="examples"></a>Przykłady

Następującego przykładowego szablonu zawiera parametr o nowy identyfikator.

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

Dane wyjściowe z poprzedniego przykładu różni się dla każdego wdrożenia, ale będzie wyglądać podobnie do:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| guidOutput | string | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

W poniższym przykładzie użyto funkcji newGuid do utworzenia unikatowej nazwy dla konta magazynu. Ten szablon może działać dla środowiska testowego, gdy konto magazynu istnieje przez krótki czas i nie jest ponownie wdrażana.

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

Dane wyjściowe z poprzedniego przykładu różni się dla każdego wdrożenia, ale będzie wyglądać podobnie do:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| nameOutput | string | storagenziwvyru7uxie |


## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

Zwraca ciąg wyrównany do prawej, dodając znaków z lewej strony aż do osiągnięcia określonej całkowitej długości.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| valueToPad |Yes |ciąg lub int |Wartość do wyrównanie do prawej. |
| totalLength |Yes |int |Całkowita liczba znaków w zwracanym ciągu. |
| paddingCharacter |Nie |pojedynczy znak |Znak do użytku dopełnienie po lewej stronie, aż do osiągnięcia całkowita długość. Wartość domyślna to miejsce. |

Jeśli oryginalny ciąg jest dłuższy niż liczba znaków do konsoli, żadne znaki nie są dodawane.

### <a name="return-value"></a>Wartość zwracana

Ciąg, z co najmniej liczba określonych znaków.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) pokazuje, jak do wypełnienia wartości parametru dostarczone przez użytkownika, dodając zero znaków, aż do napotkania całkowita liczba znaków. 

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| stringOutput | String | 0000000123 |

## <a name="replace"></a>Zastąp

`replace(originalString, oldString, newString)`

Zwraca nowy ciąg ze wszystkimi wystąpieniami jednego ciągu, zastąpiona przez innego ciągu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| originalString |Yes |string |Wartość, która zawiera wszystkie wystąpienia jednego ciągu, zastąpiona przez innego ciągu. |
| Staryciąg |Yes |string |Ciąg, który ma zostać usunięty z oryginalnego ciągu. |
| newString |Yes |string |Ciąg, który można dodać zamiast usunięto ciąg. |

### <a name="return-value"></a>Wartość zwracana

Ciąg ze znakami zastąpione.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) pokazuje, jak usunąć wszystkie łączniki z ciągu podanego przez użytkownika i jak zamienić część ciągu innym ciągiem.

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
        "secodeOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| firstOutput | String | 1231231234 |
| secodeOutput | String | 123-123-xxxx |

## <a name="skip"></a>pomiń

`skip(originalValue, numberToSkip)`

Zwraca ciąg zawierający wszystkie znaki po określonej liczbie znaków lub tablicy o liczbie wszystkich elementów po określonej liczbie elementów.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| originalValue |Yes |tablicy lub ciągu |Tablica lub ciąg używany do pomijania. |
| numberToSkip |Yes |int |Liczba elementów lub znaki do pominięcia. Jeśli ta wartość jest mniejsza lub równa 0, zwracane są wszystkie elementy lub znaków w wartości. Jeśli jest większa niż długość tablicy lub ciągu, zwracana jest pusta tablica lub ciąg. |

### <a name="return-value"></a>Wartość zwracana

Tablica lub ciąg.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) pomija określoną liczbę elementów w tablicy, a określoną liczbę znaków w ciągu.

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| arrayOutput | Tablica | ["trzy"] |
| stringOutput | String | dwa trzy |

## <a name="split"></a>split

`split(inputString, delimiter)`

Zwraca tablicę ciągów, zawierającą podciągi ciągu wejściowym są rozdzielane znakami określonych ograniczników.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| inputString |Yes |string |Ciągu do podzielenia. |
| Ogranicznik |Yes |ciąg lub tablicę ciągów |Ogranicznik służące do dzielenia ciągu. |

### <a name="return-value"></a>Wartość zwracana

Tablica ciągów.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) dzieli ciąg wejściowy za pomocą przecinka i przecinkami lub średnikami.

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| firstOutput | Tablica | ["jednego", "2", "3"] |
| secondOutput | Tablica | ["jednego", "2", "3"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Określa, czy ciąg zaczyna się od wartości. W porównaniu jest rozróżniana wielkość liter.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |string |Wartość, która zawiera element, aby znaleźć. |
| stringToFind |Yes |string |Wartość do znalezienia. |

### <a name="return-value"></a>Wartość zwracana

**Wartość true,** Jeśli pierwszy znak lub znaki ciągu pasuje do wartości; w przeciwnym razie **False**.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) przedstawia sposób użycia funkcji startsWith i endsWith:

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="string"></a>string

`string(valueToConvert)`

Konwertuje określoną wartość na ciąg.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| valueToConvert |Yes | Dowolne |Wartość do przekonwertowania na ciąg. Można przekonwertować wartość dowolnego typu, w tym obiekty i tablice. |

### <a name="return-value"></a>Wartość zwracana

Ciąg przekonwertowany wartości.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) pokazuje sposób konwertowania różnych typów wartości do ciągów:

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| objectOutput | String | {"Wartośća": 10, "Wartośćb": "Przykład Text"} |
| arrayOutput | String | ["a","b","c"] |
| intOutput | String | 5 |

## <a name="substring"></a>podciąg

`substring(stringToParse, startIndex, length)`

Zwraca podciąg, który rozpoczyna się od określonej pozycji znaku i zawiera określoną liczbę znaków.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToParse |Yes |string |Oryginalny ciąg znaków, z której jest wyodrębniany podciąg. |
| startIndex |Nie |int |Liczony od zera znaku pozycja początkowa podciąg. |
| Długość |Nie |int |Liczba znaków podciąg. Musi odwoływać się do lokalizacji w ciągu. Musi mieć wartość zero lub większą. |

### <a name="return-value"></a>Wartość zwracana

Podciąg. Lub ciąg pusty, jeśli długość jest równa zero.

### <a name="remarks"></a>Uwagi

Funkcja kończy się niepowodzeniem, jeśli podciąg wykracza poza koniec ciągu lub jeśli długość jest mniejsza niż zero. Poniższy przykład kończy się niepowodzeniem z powodu błędu "Parametry indeksu i długości muszą odwoływać się do lokalizacji w ciągu. Parametr indeksu: "0", parametr długości: "11", długość parametru ciągu: '10'.".

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) zwraca podciąg w parametrze.

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| substringOutput | String | dwa |

## <a name="take"></a>Wypełnij

`take(originalValue, numberToTake)`

Zwraca ciąg zawierający określoną liczbę znaków od początku ciągu lub tablicy o określoną liczbę elementów od początku tablicy.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| originalValue |Yes |tablicy lub ciągu |Tablicy lub ciągu, aby móc elementy z. |
| numberToTake |Yes |int |Liczba elementów lub znaki do wykonania. Jeśli ta wartość jest mniejsza lub równa 0, zwracana jest pusta tablica lub ciąg. Jeśli jest większa niż długość danej tablicy lub ciągu, zwracane są wszystkie elementy w tablicy lub ciągu. |

### <a name="return-value"></a>Wartość zwracana

Tablica lub ciąg.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) przyjmuje określonej liczby elementów z tablicy i znaków z ciągu.

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| arrayOutput | Tablica | ["one", "two"] |
| stringOutput | String | włączone |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

Konwertuje określony ciąg na małe litery.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToChange |Yes |string |Wartość do przekonwertowania na małe litery. |

### <a name="return-value"></a>Wartość zwracana

Przekonwertowanie ciągu na małe litery.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) konwertuje wartość parametru na małe litery i wielkie litery.

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| toLowerOutput | String | Raz dwa trzy |
| toUpperOutput | String | RAZ DWA TRZY |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

Konwertuje określony ciąg na wielkie litery.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToChange |Yes |string |Wartość do przekonwertowania na wielkie litery. |

### <a name="return-value"></a>Wartość zwracana

Przekonwertowanie ciągu na wielkie litery.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) konwertuje wartość parametru na małe litery i wielkie litery.

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| toLowerOutput | String | Raz dwa trzy |
| toUpperOutput | String | RAZ DWA TRZY |

## <a name="trim"></a>TRIM

`trim (stringToTrim)`

Usuwa wszystkie wiodące i końcowe białe znaki z określonego ciągu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToTrim |Yes |string |Wartość można przycięcia. |

### <a name="return-value"></a>Wartość zwracana

Ciąg bez początkowe i końcowe białe znaki.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) przycina odstępu z parametru.

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| Wróć | String | Raz dwa trzy |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

Tworzy ciąg deterministyczne wyznaczania wartości skrótu na podstawie wartości, podane jako parametry. 

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| baseString |Yes |string |Wartość używana w funkcji wyznaczania wartości skrótu, aby utworzyć unikatowy ciąg. |
| dodatkowe parametry zgodnie z potrzebami |Nie |string |Możesz dodać dowolną liczbę ciągów, w razie potrzeby można utworzyć wartości, który określa poziom unikatowości. |

### <a name="remarks"></a>Uwagi

Ta funkcja jest przydatne, gdy zachodzi potrzeba utworzenia unikatowej nazwy dla zasobu. Możesz podać wartości parametrów, które ograniczają zakres unikatowości dla wyniku. Można określić, czy nazwa jest unikatowa w dół do subskrypcji, grupy zasobów lub wdrażania. 

Zwracana wartość nie jest losowy ciąg, ale raczej wynik funkcji skrótu. Zwrócona wartość jest 13 znaków. Nie jest unikatowa w skali globalnej. Można połączyć wartości z prefiksem z konwencji nazewnictwa do utworzenia nazwę opisową. Poniższy przykład pokazuje formacie zwróconej wartości. Rzeczywista wartość zmienia się przez podane parametry.

    tcvhiyu5h2o5o

Poniższe przykłady pokazują, jak używać uniqueString do tworzenia unikatową wartość dla często używanych poziomów.

Unikatowe ograniczone do subskrypcji

```json
"[uniqueString(subscription().subscriptionId)]"
```

Unikatowa w zakresie grupy zasobów

```json
"[uniqueString(resourceGroup().id)]"
```

Unikatowe ograniczone do wdrożenia dla grupy zasobów

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

Poniższy przykład przedstawia sposób tworzenia unikatowej nazwy dla konta magazynu, w oparciu o grupy zasobów. W grupie zasobów nazwę nie jest unikatowa, jeśli zbudowany w taki sam sposób.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

Jeśli musisz utworzyć nową nazwę unikatową za każdym razem wdrożyć szablon, a nie był zamierzony na aktualizację zasobu, można użyć [utcNow](#utcnow) funkcji z uniqueString. Tej metody można użyć w środowisku testowym. Aby uzyskać przykład, zobacz [utcNow](#utcnow).

### <a name="return-value"></a>Wartość zwracana

Ciąg zawierający 13 znaków.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) zwraca wyniki z uniquestring:

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

## <a name="uri"></a>identyfikator URI

`uri (baseUri, relativeUri)`

Tworzy bezwzględny identyfikator URI, łącząc baseUri i ciąg relativeUri.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| baseUri |Yes |string |Ciąg podstawowy identyfikator uri. |
| relativeUri |Yes |string |Względny identyfikator uri ciąg do dodania do ciągu podstawowy identyfikator uri. |

Wartość **baseUri** parametr może zawierać określonego pliku, ale tylko ścieżki podstawowej stosowane jest przy konstruowaniu identyfikatora URI. Na przykład przekazanie `http://contoso.com/resources/azuredeploy.json` jako parametr baseUri skutkuje podstawowy identyfikator URI `http://contoso.com/resources/`.

### <a name="return-value"></a>Wartość zwracana

Ciąg reprezentujący bezwzględny identyfikator URI dla wartości względne i podstawowa.

### <a name="examples"></a>Przykłady

Poniższy przykład pokazuje, jak utworzyć łącze do zagnieżdżonych szablonów na podstawie wartości szablonu nadrzędnego.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) pokazuje, jak używać identyfikatora uri, uriComponent i uriComponentToString:

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| uriOutput | String | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | String | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | String | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

Koduje identyfikatora URI.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| stringToEncode |Yes |string |Wartość do zakodowania. |

### <a name="return-value"></a>Wartość zwracana

Ciąg identyfikatora URI zakodowana wartość.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) pokazuje, jak używać identyfikatora uri, uriComponent i uriComponentToString:

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| uriOutput | String | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | String | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | String | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

Zwraca ciąg identyfikatora URI zakodowana wartość.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Yes |string |Wartość do przekonwertowania na ciąg kodowany w identyfikator URI. |

### <a name="return-value"></a>Wartość zwracana

Dekodowany ciąg identyfikatora URI zakodowana wartość.

### <a name="examples"></a>Przykłady

Następujące [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) pokazuje, jak używać identyfikatora uri, uriComponent i uriComponentToString:

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi będą:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| uriOutput | String | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | String | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | String | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Zwraca bieżącą wartość daty/godziny (UTC) w określonym formacie. Jeśli format nie zostanie podany, jest używany format ISO 8601 (yyyyMMddTHHmmssZ). **Tej funkcji można używać tylko wartości domyślnej dla parametru.**

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| format |Nie |string |Wartość do przekonwertowania na ciąg kodowany w identyfikator URI. Użyj jednej [ciągi w standardowym formacie](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [niestandardowe ciągi formatujące](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Uwagi

Tej funkcji w wyrażeniu można używać tylko dla wartości domyślnej parametru. Użycie tej funkcji w jakimkolwiek innym miejscu w szablonie zwraca błąd. Funkcja nie jest dozwolona w innych części szablonu, ponieważ zwraca inną wartość w każdym razem, gdy jest wywoływana. Wdrażanie tego samego szablonu z tymi samymi parametrami w takich sytuacjach przydałaby generowane niezawodnie takie same wyniki.

Jeśli używasz [opcji, aby przeprowadzić ponowne wdrożenie wcześniej pomyślnego wdrożenia](resource-group-template-deploy-rest.md#redeploy-when-deployment-fails)i wcześniejsze wdrożenie zawiera parametr, który używa utcNow, parametr nie jest ponownie oceniane. Zamiast tego wartość tego parametru z wcześniejszych wdrożenia jest automatycznie ponownie we wdrożeniu wycofywania.

Należy zachować ostrożność, ponownego wdrażania szablonu, która korzysta z funkcji utcNow dla wartości domyślnej. Podczas ponownego wdrażania i niepodania wartości dla parametru funkcji są ponownie oceniane. Jeśli chcesz zaktualizowanie istniejącego zasobu, zamiast tworzyć nowy są przekazywane w wartości parametru z wcześniejsze wdrożenie.

### <a name="return-value"></a>Wartość zwracana

Bieżąca wartość daty/godziny UTC.

### <a name="examples"></a>Przykłady

Następującego przykładowego szablonu zawiera różne formaty dla wartości daty/godziny.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcValue": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        },
        "utcShortValue": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "utcCustomValue": {
            "type": "string",
            "defaultValue": "[utcNow('M d')]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "utcOutput": {
            "type": "string",
            "value": "[parameters('utcValue')]"
        },
        "utcShortOutput": {
            "type": "string",
            "value": "[parameters('utcShortValue')]"
        },
        "utcCustomOutput": {
            "type": "string",
            "value": "[parameters('utcCustomValue')]"
        }
    }
}
```

Dane wyjściowe z poprzedniego przykładu różni się dla każdego wdrożenia, ale będzie wyglądać podobnie do:

| Name (Nazwa) | Typ | Wartość |
| ---- | ---- | ----- |
| utcOutput | string | 20190305T175318Z |
| utcShortOutput | string | 03/05/2019 |
| utcCustomOutput | string | 3 5 |

Następny przykład pokazuje, jak użyć wartości z funkcji, ustawiając wartość tagu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "rgName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "name": "[parameters('rgName')]",
            "location": "westeurope",
            "tags":{
                "createdDate": "[parameters('utcShort')]"
            },
            "properties":{}
        }
    ],
    "outputs": {
        "utcShort": {
            "type": "string",
            "value": "[parameters('utcShort')]"
        }
    }
}
```

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać opis sekcje szablonu usługi Azure Resource Manager, zobacz [tworzenia usługi Azure Resource Manager](resource-group-authoring-templates.md).
* Aby scalić wiele szablonów, zobacz [przy użyciu szablonów połączonych z usługą Azure Resource Manager](resource-group-linked-templates.md).
* Do iteracji określoną liczbę razy podczas tworzenia dla typu zasobów, zobacz [tworzenie wielu wystąpień zasobów w usłudze Azure Resource Manager](resource-group-create-multiple.md).
* Aby zobaczyć, jak wdrożyć szablon został utworzony, zobacz [wdrażania aplikacji przy użyciu szablonu usługi Azure Resource Manager](resource-group-template-deploy.md).

