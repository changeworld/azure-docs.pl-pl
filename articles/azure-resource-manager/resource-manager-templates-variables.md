---
title: Zmiennych szablonu usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Opisuje sposób definiowania zmiennych w szablonach usługi Azure Resource Manager za pomocą składni deklaratywnej JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: tomfitz
ms.openlocfilehash: 08728a3c0b4d4578939004e2d1b1ee2d30a682ab
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359292"
---
# <a name="variables-section-of-azure-resource-manager-templates"></a>Zmienne sekcji szablonów usługi Azure Resource Manager
W sekcji variables można skonstruować wartości, które mogą być używane w szablonie. Nie trzeba zdefiniować zmienne, ale one często uprościć szablonu zmniejszając złożonych wyrażeń.

## <a name="define-and-use-a-variable"></a>Definiowanie i użyj zmiennej

Poniższy przykład przedstawia definicji zmiennej. Tworzy wartość ciągu dla nazwy konta magazynu. Aby uzyskać wartość parametru i połączyć ją z unikatowym ciągiem używa kilka funkcji szablonu.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Podczas definiowania zasobu należy użyć zmiennej.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

## <a name="available-definitions"></a>Definicje dostępne

Poprzednim przykładzie pokazano jedną z metod do zdefiniowania zmiennej. Można użyć dowolnej z poniższymi definicjami:

```json
"variables": {
    "<variable-name>": "<variable-value>",
    "<variable-name>": { 
        <variable-complex-type-value> 
    },
    "<variable-object-name>": {
        "copy": [
            {
                "name": "<name-of-array-property>",
                "count": <number-of-iterations>,
                "input": {
                    <properties-to-repeat>
                }
            }
        ]
    },
    "copy": [
        {
            "name": "<variable-array-name>",
            "count": <number-of-iterations>,
            "input": {
                <properties-to-repeat>
            }
        }
    ]
}
```

## <a name="configuration-variables"></a>Zmienne konfiguracji

Typy złożone JSON umożliwia definiowanie powiązanych wartości dla środowiska. 

```json
"variables": {
    "environmentSettings": {
        "test": {
            "instanceSize": "Small",
            "instanceCount": 1
        },
        "prod": {
            "instanceSize": "Large",
            "instanceCount": 4
        }
    }
},
```

Parametry tworzenia wartości wskazującej, która Konfiguracja wartości do użycia.

```json
"parameters": {
    "environmentName": {
        "type": "string",
        "allowedValues": [
          "test",
          "prod"
        ]
    }
},
```

Możesz pobrać bieżące ustawienia z:

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="use-copy-element-in-variable-definition"></a>Użyj elementu kopiowania w definicji zmiennej

Można użyć **kopiowania** składni, aby utworzyć zmienną z tablicą wiele elementów. Wynik jest podanie liczby elementów. Każdy element zawiera właściwości w **wejściowych** obiektu. Możesz użyć kopii w zmiennej lub można utworzyć zmiennej. Gdy Zdefiniuj zmienną i używać **kopiowania** w tej zmiennej, Utwórz obiekt, który ma właściwości tablicy. Jeśli używasz **kopiowania** na najwyższym poziomie i określić jedną lub więcej zmiennych w niej utworzyć co najmniej jeden tablice. W poniższym przykładzie przedstawiono obu podejść:

```json
"variables": {
    "disk-array-on-object": {
        "copy": [
            {
                "name": "disks",
                "count": 3,
                "input": {
                    "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
                    "diskSizeGB": "1",
                    "diskIndex": "[copyIndex('disks')]"
                }
            }
        ]
    },
    "copy": [
        {
            "name": "disks-top-level-array",
            "count": 3,
            "input": {
                "name": "[concat('myDataDisk', copyIndex('disks-top-level-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('disks-top-level-array')]"
            }
        }
    ]
},
```

Zmienna **dysku tablicy na obiekt** zawiera następujący obiekt z tablicy o nazwie **dysków**:

```json
{
  "disks": [
    {
      "name": "myDataDisk1",
      "diskSizeGB": "1",
      "diskIndex": 0
    },
    {
      "name": "myDataDisk2",
      "diskSizeGB": "1",
      "diskIndex": 1
    },
    {
      "name": "myDataDisk3",
      "diskSizeGB": "1",
      "diskIndex": 2
    }
  ]
}
```

Zmienna **dysków top poziom tablicy** zawiera tablicę następujące:

```json
[
  {
    "name": "myDataDisk1",
    "diskSizeGB": "1",
    "diskIndex": 0
  },
  {
    "name": "myDataDisk2",
    "diskSizeGB": "1",
    "diskIndex": 1
  },
  {
    "name": "myDataDisk3",
    "diskSizeGB": "1",
    "diskIndex": 2
  }
]
```

Można również określić więcej niż jeden obiekt, w przypadku używania kopii do tworzenia zmiennych. W poniższym przykładzie zdefiniowano dwie tablice jako zmienne. Jedną o nazwie **dysków top poziom tablicy** i ma pięć elementów. Druga o nazwie **a inną tablicy** i ma trzy elementy.

```json
"variables": {
    "copy": [
        {
            "name": "disks-top-level-array",
            "count": 5,
            "input": {
                "name": "[concat('oneDataDisk', copyIndex('disks-top-level-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('disks-top-level-array')]"
            }
        },
        {
            "name": "a-different-array",
            "count": 3,
            "input": {
                "name": "[concat('twoDataDisk', copyIndex('a-different-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('a-different-array')]"
            }
        }
    ]
},
```

Ta metoda działa również w przypadku, gdy trzeba podjąć wartości parametrów i upewnij się, że są one w poprawnym formacie dla wartości szablonu. Poniższy przykład formaty wartości parametrów do użycia podczas definiowania reguły zabezpieczeń:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "securityRules": {
      "type": "array"
    }
  },
  "variables": {
    "copy": [
      {
        "name": "securityRules",
        "count": "[length(parameters('securityRules'))]",
        "input": {
          "name": "[parameters('securityRules')[copyIndex('securityRules')].name]",
          "properties": {
            "description": "[parameters('securityRules')[copyIndex('securityRules')].description]",
            "priority": "[parameters('securityRules')[copyIndex('securityRules')].priority]",
            "protocol": "[parameters('securityRules')[copyIndex('securityRules')].protocol]",
            "sourcePortRange": "[parameters('securityRules')[copyIndex('securityRules')].sourcePortRange]",
            "destinationPortRange": "[parameters('securityRules')[copyIndex('securityRules')].destinationPortRange]",
            "sourceAddressPrefix": "[parameters('securityRules')[copyIndex('securityRules')].sourceAddressPrefix]",
            "destinationAddressPrefix": "[parameters('securityRules')[copyIndex('securityRules')].destinationAddressPrefix]",
            "access": "[parameters('securityRules')[copyIndex('securityRules')].access]",
            "direction": "[parameters('securityRules')[copyIndex('securityRules')].direction]"
          }
        }
      }
    ]
  },
  "resources": [
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "NSG1",
      "location": "[resourceGroup().location]",
      "properties": {
        "securityRules": "[variables('securityRules')]"
      }
    }
  ],
  "outputs": {}
}
```

## <a name="recommendations"></a>Zalecenia
Następujące informacje mogą być pomocne podczas pracy ze zmiennymi:

* Użyj zmienne dla wartości, które trzeba użyć więcej niż raz w szablonie. Jeśli wartość jest używana tylko raz, wartość ustalony ułatwia szablonu do odczytu.
* Nie można użyć [odwołania](resource-group-template-functions-resource.md#reference) działać w **zmienne** sekcji szablonu. **Odwołania** funkcja pochodzi wartość ze stanu środowiska uruchomieniowego zasobu. Zmienne są jednak rozpoznane podczas początkowej analizy szablonu. Konstrukcja wartości wymagających **odwołania** bezpośrednio w funkcji **zasobów** lub **generuje** sekcji szablonu.
* Obejmują zmienne dla nazw zasobów, które muszą być unikatowe.

## <a name="example-templates"></a>Przykład szablonów

Te szablony przykładowe pokazują niektóre scenariusze dotyczące używania zmiennych. Ich do testowania obsługi zmiennych w różnych scenariuszy wdrażania. 

|Szablon  |Opis  |
|---------|---------|
| [definicje zmiennych](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Przedstawia różne typy zmiennych. Szablon nie wdrażać żadnych zasobów. Konstruuje wartości zmiennej, a zwraca tych wartości. |
| [zmiennej konfiguracyjnej](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Pokazuje użycie zmiennej, która definiuje wartości konfiguracji. Szablon nie wdrażać żadnych zasobów. Konstruuje wartości zmiennej, a zwraca tych wartości. |
| [reguły zabezpieczeń sieci](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) i [pliku parametrów](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Tworzy tablicę w poprawnym formacie przypisywania zasad zabezpieczeń do grupy zabezpieczeń sieci. |


## <a name="next-steps"></a>Kolejne kroki
* Aby wyświetlić pełną listę szablonów dla wielu różnych rozwiązań, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/).
* Aby uzyskać więcej informacji o funkcje, których można użyć z w ramach szablonu, zobacz [funkcje szablonów usługi Azure Resource Manager](resource-group-template-functions.md).
* Aby połączyć wiele szablonów podczas wdrażania, zobacz [za pomocą szablonów połączonych z usługą Azure Resource Manager](resource-group-linked-templates.md).
* Może być konieczne użycie zasobów, które istnieją w innej grupie zasobów. Ten scenariusz jest typowy podczas pracy z kontami magazynu lub sieci wirtualne, które są współdzielone przez wiele grup zasobów. Aby uzyskać więcej informacji, zobacz [funkcja resourceId](resource-group-template-functions-resource.md#resourceid).