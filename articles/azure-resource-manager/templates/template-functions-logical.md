---
title: Funkcje szablonu - logiczne
description: W tym artykule opisano funkcje używane w szablonie usługi Azure Resource Manager do określania wartości logicznych.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: f058baa32e5f93a4177913287a5e9873fa7a9acb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156314"
---
# <a name="logical-functions-for-arm-templates"></a>Funkcje logiczne dla szablonów ARM

Menedżer zasobów udostępnia kilka funkcji do dokonywania porównań w szablonach usługi Azure Resource Manager (ARM).

* [I](#and)
* [bool](#bool)
* [if](#if)
* [Nie](#not)
* [Lub](#or)

## <a name="and"></a>i

`and(arg1, arg2, ...)`

Sprawdza, czy wszystkie wartości parametrów są prawdziwe.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| argument1 |Tak |wartość logiczna |Pierwsza wartość, aby sprawdzić, czy jest true. |
| argument 2 |Tak |wartość logiczna |Druga wartość, aby sprawdzić, czy jest true. |
| dodatkowe argumenty |Nie |wartość logiczna |Dodatkowe argumenty, aby sprawdzić, czy są prawdziwe. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **wartość True,** jeśli wszystkie wartości są prawdziwe; w przeciwnym razie **False**.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) pokazuje, jak używać funkcji logicznych.

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

Dane wyjściowe z poprzedniego przykładu to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| orazExampleOutput | Wartość logiczna | False |
| lubWydajplejuchna | Wartość logiczna | True |
| notExampleOutput | Wartość logiczna | False |

## <a name="bool"></a>bool

`bool(arg1)`

Konwertuje parametr na wartość logiczną.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| argument1 |Tak |ciąg lub int |Wartość do konwersji na wartość logiczną. |

### <a name="return-value"></a>Wartość zwracana
Wartość logiczna przekonwertowana.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) pokazuje, jak używać bool z ciągiem lub całkowitej liczby.

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

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| trueString | Wartość logiczna | True |
| falseString | Wartość logiczna | False |
| trueInt (trueInt) | Wartość logiczna | True |
| falseInt (FalseInt) | Wartość logiczna | False |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Zwraca wartość na podstawie tego, czy warunek jest prawdziwy czy fałszywy.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| Warunek |Tak |wartość logiczna |Wartość, aby sprawdzić, czy jest to prawda lub false. |
| Truevalue |Tak | ciąg, int, obiekt lub tablica |Wartość zwracana, gdy warunek jest spełniony. |
| Falsevalue |Tak | ciąg, int, obiekt lub tablica |Wartość zwracana, gdy warunek jest false. |

### <a name="return-value"></a>Wartość zwracana

Zwraca drugi parametr, gdy pierwszy parametr jest **True**; w przeciwnym razie zwraca trzeci parametr.

### <a name="remarks"></a>Uwagi

Gdy warunek jest **True**, tylko wartość true jest obliczana. Gdy warunek jest **False**, tylko wartość false jest obliczana. Za **pomocą** if funkcji, można dołączyć wyrażenia, które są tylko warunkowo prawidłowe. Na przykład można odwołać się do zasobu, który istnieje w jednym warunku, ale nie w drugim warunku. Przykład warunkowej oceny wyrażeń jest pokazany w poniższej sekcji.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) pokazuje, jak korzystać z `if` funkcji.

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

Dane wyjściowe z poprzedniego przykładu to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| yesOutput (Pl) | Ciąg | tak |
| noOutput (nieprzejście) | Ciąg | nie |
| ObjectOutput | Obiekt | { "test": "value1" } |

Poniższy [przykładowy szablon](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) pokazuje, jak używać tej funkcji z wyrażeniami, które są tylko warunkowo prawidłowe.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "logAnalytics": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "condition": "[not(empty(parameters('logAnalytics')))]",
            "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2017-03-30",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
                },
                "protectedSettings": {
                    "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
                }
            }
        }
    ],
    "outputs": {
        "mgmtStatus": {
            "type": "string",
            "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
        }
    }
}
```

## <a name="not"></a>not

`not(arg1)`

Konwertuje wartość logiczną na jego przeciwległą wartość.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| argument1 |Tak |wartość logiczna |Wartość do konwersji. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **wartość True,** gdy parametr jest **False**. Zwraca **wartość False,** gdy parametr jest **True**.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) pokazuje, jak używać funkcji logicznych.

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

Dane wyjściowe z poprzedniego przykładu to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| orazExampleOutput | Wartość logiczna | False |
| lubWydajplejuchna | Wartość logiczna | True |
| notExampleOutput | Wartość logiczna | False |

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) **nie** używa z [równym](template-functions-comparison.md#equals).

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

Dane wyjściowe z poprzedniego przykładu to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| checkNotEquals (Nie ma kwalifikacji) | Wartość logiczna | True |

## <a name="or"></a>lub

`or(arg1, arg2, ...)`

Sprawdza, czy wartość parametru jest true.

### <a name="parameters"></a>Parametry

| Parametr | Wymagany | Typ | Opis |
|:--- |:--- |:--- |:--- |
| argument1 |Tak |wartość logiczna |Pierwsza wartość, aby sprawdzić, czy jest true. |
| argument 2 |Tak |wartość logiczna |Druga wartość, aby sprawdzić, czy jest true. |
| dodatkowe argumenty |Nie |wartość logiczna |Dodatkowe argumenty, aby sprawdzić, czy są prawdziwe. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **wartość True,** jeśli dowolna wartość jest prawdziwa; w przeciwnym razie **False**.

### <a name="examples"></a>Przykłady

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) pokazuje, jak używać funkcji logicznych.

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

Dane wyjściowe z poprzedniego przykładu to:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| orazExampleOutput | Wartość logiczna | False |
| lubWydajplejuchna | Wartość logiczna | True |
| notExampleOutput | Wartość logiczna | False |

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać opis sekcji w szablonie usługi Azure Resource Manager, zobacz [Tworzenie szablonów usługi Azure Resource Manager](template-syntax.md).
* Aby scalić wiele szablonów, zobacz [Używanie połączonych szablonów z usługą Azure Resource Manager](linked-templates.md).
* Aby iterować określoną liczbę razy podczas tworzenia typu zasobu, zobacz [Tworzenie wielu wystąpień zasobów w usłudze Azure Resource Manager](copy-resources.md).
* Aby zobaczyć, jak wdrożyć utworzony szablon, zobacz [Wdrażanie aplikacji za pomocą szablonu usługi Azure Resource Manager](deploy-powershell.md).

