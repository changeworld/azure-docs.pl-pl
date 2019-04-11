---
title: Usługa Azure Resource Manager funkcje szablonu - logiczne | Dokumentacja firmy Microsoft
description: Opisuje funkcje do używania szablonu usługi Azure Resource Manager w celu określenia wartości logiczne.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/09/2019
ms.author: tomfitz
ms.openlocfilehash: 9065c6bc71a153ae94ddc20d5b41a152094fc111
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2019
ms.locfileid: "59470155"
---
# <a name="logical-functions-for-azure-resource-manager-templates"></a>Funkcje logiczne dla szablonów usługi Azure Resource Manager

Resource Manager udostępnia kilka funkcji składania porównania w szablonach.

* [i](#and)
* [bool](#bool)
* [if](#if)
* [nie](#not)
* [lub](#or)

## <a name="and"></a>i

`and(arg1, arg2, ...)`

Sprawdza, czy wszystkie wartości parametrów są spełnione.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |wartość logiczna |Pierwsza wartość, aby sprawdzić, czy ma wartość true. |
| argument2 |Yes |wartość logiczna |Druga wartość, aby sprawdzić, czy ma wartość true. |
| dodatkowe argumenty |Nie |wartość logiczna |Dodatkowe argumenty, aby sprawdzić, czy są spełnione. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **True** Jeśli wszystkie wartości są true; w przeciwnym razie **False**.

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
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="bool"></a>bool

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
| trueString | Bool | True |
| falseString | Bool | False |
| trueInt | Bool | True |
| falseInt | Bool | False |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Zwraca wartość, na podstawie warunku jest wartość PRAWDA lub FAŁSZ.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| warunek |Yes |wartość logiczna |Wartość, aby sprawdzić, czy jest wartość PRAWDA lub FAŁSZ. |
| trueValue |Yes | ciąg, int, obiekt lub tablica |Wartość zwracana, gdy warunek jest prawdziwy. |
| falseValue |Yes | ciąg, int, obiekt lub tablica |Wartość zwracana, gdy warunek jest fałszywy. |

### <a name="return-value"></a>Wartość zwracana

Zwraca drugi parametr, gdy pierwszy parametr jest **True**; w przeciwnym razie zwraca wartość trzeciego parametru.

### <a name="remarks"></a>Uwagi

Gdy warunek jest **True**, jest oceniana wartość true. Gdy warunek jest **False**, jest oceniana wartość false. Za pomocą **Jeśli** funkcji może zawierać wyrażeń, które obowiązują tylko warunkowo. Na przykład możesz odwoływać się z zasobem, który istnieje w ramach jednego warunku, ale nie w ramach innych warunków. W poniższej sekcji przedstawiono przykładowy warunkowo obliczenia wyrażenia.

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
| yesOutput | String | tak |
| noOutput | String | nie |
| objectOutput | Obiekt | { "test": "value1" } |

Następujące [przykładowy szablon](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) pokazuje, jak użyć tej funkcji za pomocą wyrażeń, które obowiązują tylko warunkowo.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "condition": "[greaterOrEquals(parameters('logAnalytics'), '0')]",
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
                    "workspaceId": "[if(greaterOrEquals(parameters('logAnalytics'), '0'), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
                },
                "protectedSettings": {
                    "workspaceKey": "[if(greaterOrEquals(parameters('logAnalytics'), '0'), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
                }
            }
        }
    ],
    "outputs": {
        "mgmtStatus": {
            "type": "string",
            "value": "[if(greaterOrEquals(parameters('logAnalytics'), '0'), 'Enabled monitoring for VM!', 'Nothing to enable')]"
        }
    }
}
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
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

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
| checkNotEquals | Bool | True |

## <a name="or"></a>lub

`or(arg1, arg2, ...)`

Sprawdza, czy wszystkie wartości parametru to true.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |wartość logiczna |Pierwsza wartość, aby sprawdzić, czy ma wartość true. |
| argument2 |Yes |wartość logiczna |Druga wartość, aby sprawdzić, czy ma wartość true. |
| dodatkowe argumenty |Nie |wartość logiczna |Dodatkowe argumenty, aby sprawdzić, czy są spełnione. |

### <a name="return-value"></a>Wartość zwracana

Zwraca **True** Jeśli dowolna wartość PRAWDA; w przeciwnym razie **False**.

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
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać opis sekcje szablonu usługi Azure Resource Manager, zobacz [tworzenia usługi Azure Resource Manager](resource-group-authoring-templates.md).
* Aby scalić wiele szablonów, zobacz [przy użyciu szablonów połączonych z usługą Azure Resource Manager](resource-group-linked-templates.md).
* Do iteracji określoną liczbę razy podczas tworzenia dla typu zasobów, zobacz [tworzenie wielu wystąpień zasobów w usłudze Azure Resource Manager](resource-group-create-multiple.md).
* Aby zobaczyć, jak wdrożyć szablon został utworzony, zobacz [wdrażania aplikacji przy użyciu szablonu usługi Azure Resource Manager](resource-group-template-deploy.md).

