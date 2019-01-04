---
title: Zmienne szablonu usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób definiowania zmiennych w szablonach usługi Azure Resource Manager za pomocą składni deklaratywnej JSON.
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
ms.date: 12/18/2018
ms.author: tomfitz
ms.openlocfilehash: f6c629182fdcce83c566869860480d9c70488797
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53712750"
---
# <a name="variables-section-of-azure-resource-manager-templates"></a>Sekcję zmiennych szablonów usługi Azure Resource Manager
W sekcji zmiennych konstruujesz wartości, których można użyć w szablonie. Nie trzeba zdefiniować zmienne, ale często upraszczają działania do szablonu, zmniejszając złożonych wyrażeń.

## <a name="define-and-use-a-variable"></a>Definiowanie i użyj zmiennej

Poniższy przykład przedstawia definicję zmiennej. Tworzy wartość ciągu dla nazwy konta magazynu. Aby uzyskać wartości parametru i połączyć ją z unikatowym ciągiem używa kilka funkcji szablonu.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Podczas definiowania zasobu jest używana zmienna.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

## <a name="available-definitions"></a>Dostępne definicje

Poprzedni przykład pokazano sposób definiowania zmiennej. Można użyć dowolnego z następujących definicji:

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

Typy złożone JSON służy do definiowania powiązanych wartości dla środowiska. 

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

W parametrach utworzysz wartość, która wskazuje, która Konfiguracja wartości do użycia.

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

Możesz pobrać bieżące ustawienia za pomocą:

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="use-copy-element-in-variable-definition"></a>Użyj elementu kopii w definicji zmiennej

Możesz użyć **kopiowania** składni, aby utworzyć zmienną z tablicą z kilku elementów. Wynik jest przewidzieć liczbę elementów. Każdy element zawiera właściwości w ramach **wejściowych** obiektu. Można użyć kopii w zmiennej, lub aby utworzyć zmienną. Podczas definiowania zmiennej i stosowania **kopiowania** w ramach tej zmiennej, należy utworzyć obiekt, który ma właściwości tablicy. Kiedy używasz **kopiowania** na najwyższym poziomie i zdefiniować jedną lub więcej zmiennych w nim, można utworzyć co najmniej jeden tablic. W poniższym przykładzie przedstawiono oba podejścia:

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

Zmienna **dysku tablicy na obiekcie** zawiera następujący obiekt z tablicy o nazwie **dysków**:

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

Zmienna **dysków top poziom tablicy** zawiera następującą tablicę:

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

W przypadku używania kopii do tworzenia zmiennych, można również określić więcej niż jeden obiekt. W poniższym przykładzie zdefiniowano dwie tablice jako zmienne. Jeden o nazwie **dysków top poziom tablicy** i zawiera pięć elementów. Druga o nazwie **w różnych tablicy** i ma trzy elementy.

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

Ta metoda działa dobrze w przypadku, gdy trzeba pobrać wartości parametrów i upewnij się, że są one w poprawnym formacie dla wartości szablonu. Poniższy przykład formatuje wartości parametrów do użycia podczas definiowania reguły zabezpieczeń:

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

## <a name="example-templates"></a>Przykładowe szablony

Te przykładowe szablony pokazują niektóre scenariusze dotyczące używania zmiennych. Wdróż je do testowania, w jaki sposób zmienne są obsługiwane w różnych scenariuszach. 

|Szablon  |Opis  |
|---------|---------|
| [definicje zmiennych](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Demonstruje różne rodzaje zmiennych. Szablon nie wdroży żadnych zasobów. On tworzy wartości zmiennych i zwraca te wartości. |
| [Zmienna konfiguracyjna](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Zademonstrowano użycie zmiennej, która definiuje wartości konfiguracji. Szablon nie wdroży żadnych zasobów. On tworzy wartości zmiennych i zwraca te wartości. |
| [reguł zabezpieczeń sieciowych](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) i [pliku parametrów](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Tworzy tablicę w poprawnym formacie przypisywania zabezpieczeń reguły do sieciowej grupy zabezpieczeń. |


## <a name="next-steps"></a>Kolejne kroki
* Aby wyświetlić pełną listę szablonów dla wielu różnych rozwiązań, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/).
* Aby uzyskać szczegółowe informacje na temat funkcji, możesz użyć w szablonie, zobacz [funkcje szablonu usługi Azure Resource Manager](resource-group-template-functions.md).
* Aby uzyskać zalecenia dotyczące tworzenia szablonów, zobacz [najlepszych rozwiązań dla szablonu usługi Azure Resource Manager](template-best-practices.md).
* Może być konieczne użycie zasobów, które istnieją w innej grupie zasobów. Ten scenariusz jest typowy podczas pracy z kontami magazynu lub sieci wirtualne, które są współdzielone przez więcej niż jednej grupy zasobów. Aby uzyskać więcej informacji, zobacz [funkcja resourceId](resource-group-template-functions-resource.md#resourceid).