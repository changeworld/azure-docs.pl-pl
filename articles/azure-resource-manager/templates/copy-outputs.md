---
title: Zdefiniuj wiele wystąpień wartości wyjściowej
description: Użyj operacji kopiowania w szablonie Azure Resource Manager, aby wykonać iterację wiele razy podczas zwracania wartości z wdrożenia.
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: db5c548c7bd4c60357d3656b1273b0192c497459
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624776"
---
# <a name="output-iteration-in-azure-resource-manager-templates"></a>Iteracja danych wyjściowych w szablonach Azure Resource Manager

W tym artykule pokazano, jak utworzyć więcej niż jedną wartość danych wyjściowych w szablonie Azure Resource Manager. Po dodaniu elementu **copy** do sekcji dane wyjściowe szablonu można dynamicznie zwrócić liczbę elementów podczas wdrażania.

Można również użyć kopiowania z [zasobami](copy-resources.md), [właściwości w zasobie](copy-properties.md)i [zmiennych](copy-variables.md).

## <a name="outputs-iteration"></a>Iteracja danych wyjściowych

Element Copy ma następujący format ogólny:

```json
"copy": [
  {
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

Właściwość **Count** określa liczbę iteracji dla wartości wyjściowej.

Właściwość **Input** określa właściwości, które mają być powtarzane. Tworzysz tablicę elementów skonstruowanych na podstawie wartości we właściwości **wejściowej** . Może to być pojedyncza Właściwość (na przykład ciąg) lub obiekt z kilkoma właściwościami.

Poniższy przykład tworzy zmienną liczbę kont magazynu i zwraca punkt końcowy dla każdego konta magazynu:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageEndpoints": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
            }
        }
    }
}
```

Poprzedni szablon zwraca tablicę z następującymi wartościami:

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

Następny przykład zwraca trzy właściwości z nowych kont magazynu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageInfo": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": {
                    "id": "[reference(concat(copyIndex(), variables('baseName')), '2019-04-01', 'Full').resourceId]",
                    "blobEndpoint": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]",
                    "status": "[reference(concat(copyIndex(), variables('baseName'))).statusOfPrimary]"
                }
            }
        }
    }
}
```

Poprzedni przykład zwraca tablicę o następujących wartościach:

```json
[
    {
        "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
        "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    },
    {
        "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
        "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    }
]
```

## <a name="next-steps"></a>Następne kroki

* Aby przejść przez samouczek, zobacz [Samouczek: Tworzenie wielu wystąpień zasobów przy użyciu szablonów Menedżer zasobów](template-tutorial-create-multiple-instances.md).
* Aby poznać inne zastosowania elementu Copy, zobacz:
  * [Iteracja zasobów w szablonach Azure Resource Manager](copy-resources.md)
  * [Iteracja właściwości w szablonach Azure Resource Manager](copy-properties.md)
  * [Iteracja zmiennej w szablonach Azure Resource Manager](copy-variables.md)
* Jeśli chcesz dowiedzieć się więcej na temat sekcji szablonu, zobacz [Tworzenie szablonów Azure Resource Manager](template-syntax.md).
* Aby dowiedzieć się, jak wdrożyć szablon, zobacz [wdrażanie aplikacji przy użyciu szablonu Azure Resource Manager](deploy-powershell.md).

