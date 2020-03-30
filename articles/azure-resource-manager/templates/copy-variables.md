---
title: Definiowanie wielu wystąpień zmiennej
description: Użyj operacji kopiowania w szablonie usługi Azure Resource Manager, aby iterować wiele razy podczas tworzenia zmiennej.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: ed0c2d87c48a18b0a065f6c76e1e69142a9df048
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153305"
---
# <a name="variable-iteration-in-arm-templates"></a>Iteracja zmiennych w szablonach ARM

W tym artykule pokazano, jak utworzyć więcej niż jedną wartość dla zmiennej w szablonie usługi Azure Resource Manager (ARM). Dodając element **kopiowania** do sekcji zmiennych szablonu, można dynamicznie ustawić liczbę elementów dla zmiennej podczas wdrażania. Można również unikać konieczności powtarzania składni szablonu.

Można również użyć kopiowania z [zasobami, właściwości w zasobie](copy-properties.md)i [danych wyjściowych](copy-outputs.md). [resources](copy-resources.md)

## <a name="variable-iteration"></a>Iteracja zmiennej

Element kopiowania ma następujący ogólny format:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

Właściwość **name** jest dowolną wartością, która identyfikuje pętlę. Właściwość **count** określa liczbę iteracji, które mają dla zmiennej.

Właściwość **input** określa właściwości, które mają zostać powtórzone. Można utworzyć tablicę elementów zbudowanych na podstawie wartości we właściwości **wejściowej.** Może to być pojedyncza właściwość (jak ciąg) lub obiekt z kilkoma właściwościami.

W poniższym przykładzie pokazano, jak utworzyć tablicę wartości ciągu:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
     },
    "variables": {
        "copy": [
            {
                "name": "stringArray",
                "count": "[parameters('itemCount')]",
                "input": "[concat('item', copyIndex('stringArray', 1))]"
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('stringArray')]"
        }
    }
}
```

Poprzedni szablon zwraca tablicę z następującymi wartościami:

```json
[
    "item1",
    "item2",
    "item3",
    "item4",
    "item5"
]
```

W następnym przykładzie pokazano, jak utworzyć tablicę obiektów o trzech właściwościach - nazwa, diskSizeGB i diskIndex.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "copy": [
            {
                "name": "objectArray",
                "count": "[parameters('itemCount')]",
                "input": {
                    "name": "[concat('myDataDisk', copyIndex('objectArray', 1))]",
                    "diskSizeGB": "1",
                    "diskIndex": "[copyIndex('objectArray')]"
                }
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('objectArray')]"
        }
    }
}
```

W poprzednim przykładzie zwraca tablicę z następującymi wartościami:

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
    },
    {
        "name": "myDataDisk4",
        "diskSizeGB": "1",
        "diskIndex": 3
    },
    {
        "name": "myDataDisk5",
        "diskSizeGB": "1",
        "diskIndex": 4
    }
]
```

> [!NOTE]
> Iteracja zmiennej obsługuje argument przesunięcia. Przesunięcie musi pochodzić po nazwie iteracji, takich jak copyIndex('diskNames', 1). Jeśli nie podasz wartości przesunięcia, domyślnie jest to 0 dla pierwszego wystąpienia.
>

Można również użyć elementu kopiowania w zmiennej. Poniższy przykład tworzy obiekt, który ma tablicę jako jedną z jego wartości.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "topLevelObject": {
            "sampleProperty": "sampleValue",
            "copy": [
                {
                    "name": "disks",
                    "count": "[parameters('itemCount')]",
                    "input": {
                        "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
                        "diskSizeGB": "1",
                        "diskIndex": "[copyIndex('disks')]"
                    }
                }
            ]
        }
    },
    "resources": [],
    "outputs": {
        "objectResult": {
            "type": "object",
            "value": "[variables('topLevelObject')]"
        }
    }
}
```

W poprzednim przykładzie zwraca obiekt o następujących wartościach:

```json
{
    "sampleProperty": "sampleValue",
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
        },
        {
            "name": "myDataDisk4",
            "diskSizeGB": "1",
            "diskIndex": 3
        },
        {
            "name": "myDataDisk5",
            "diskSizeGB": "1",
            "diskIndex": 4
        }
    ]
}
```

W następnym przykładzie przedstawiono różne sposoby używania kopii ze zmiennymi.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

## <a name="copy-limits"></a>Limity kopiowania

Liczba nie może przekroczyć 800.

Liczba nie może być liczbą ujemną. Jeśli wdrożysz szablon z programem Azure PowerShell 2.6 lub nowszym, interfejsem wiersza polecenia platformy Azure 2.0.74 lub nowszym lub interfejsem API REST w wersji **2019-05-10** lub nowszej, można ustawić liczbę na zero. Starsze wersje programu PowerShell, interfejsu wiersza polecenia i interfejsu API REST nie obsługują zero dla liczby.

## <a name="example-templates"></a>Przykładowe szablony

Poniższe przykłady pokazują typowe scenariusze tworzenia więcej niż jednej wartości dla zmiennej.

|Szablon  |Opis  |
|---------|---------|
|[Kopiowanie zmiennych](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Pokazuje różne sposoby iteracji zmiennych. |
|[Wiele reguł zabezpieczeń](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Wdraża kilka reguł zabezpieczeń w sieciowej grupie zabezpieczeń. Konstruuje reguły zabezpieczeń z parametru. Dla parametru, zobacz [wiele pliku parametrów NSG](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Następne kroki

* Aby przejść przez samouczek, zobacz [Samouczek: tworzenie wielu wystąpień zasobów przy użyciu szablonów ARM](template-tutorial-create-multiple-instances.md).
* Aby uzyskać inne zastosowania elementu kopiowania, zobacz:
  * [Iteracja zasobów w szablonach ARM](copy-resources.md)
  * [Iteracja właściwości w szablonach ARM](copy-properties.md)
  * [Iteracja wyjściowa w szablonach ARM](copy-outputs.md)
* Jeśli chcesz dowiedzieć się więcej o sekcjach szablonu, zobacz [Tworzenie szablonów ARM](template-syntax.md).
* Aby dowiedzieć się, jak wdrożyć szablon, zobacz [Wdrażanie aplikacji z szablonem ARM](deploy-powershell.md).

