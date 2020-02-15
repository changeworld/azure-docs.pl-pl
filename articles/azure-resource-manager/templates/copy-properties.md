---
title: Definiowanie wielu wystąpień właściwości
description: Użyj operacji kopiowania w szablonie Azure Resource Manager, aby wykonać iterację wielokrotnie podczas tworzenia właściwości w zasobie.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: b1e31f981f361b4cfbe7e7930f2c70bfce8b8656
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210868"
---
# <a name="property-iteration-in-azure-resource-manager-templates"></a>Iteracja właściwości w szablonach Azure Resource Manager

W tym artykule pokazano, jak utworzyć więcej niż jedno wystąpienie właściwości w szablonie Azure Resource Manager. Dodając element **copy** do sekcji Właściwości zasobu w szablonie, można dynamicznie ustawić liczbę elementów dla właściwości podczas wdrażania. Należy również unikać powtarzania składni szablonu.

Można również używać kopiowania z [zasobami](copy-resources.md) i [zmiennymi](copy-variables.md).

## <a name="property-iteration"></a>Iteracja właściwości

Element Copy ma następujący format ogólny:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

W polu **Nazwa**Podaj nazwę właściwości zasobu, którą chcesz utworzyć. Właściwość **Count** określa liczbę iteracji dla właściwości.

Właściwość **Input** określa właściwości, które mają być powtarzane. Tworzysz tablicę elementów skonstruowanych na podstawie wartości we właściwości **wejściowej** .

Poniższy przykład pokazuje, jak zastosować `copy` do właściwości datadisks na maszynie wirtualnej:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 16,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2017-03-30",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "diskSizeGB": 1023,
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty"
              }
            }
          ]
        }
      }
    }
  ]
}
```

Należy zauważyć, że w przypadku używania `copyIndex` wewnątrz iteracji właściwości należy podać nazwę iteracji.

> [!NOTE]
> Iteracja właściwości obsługuje również argument przesunięcia. Przesunięcie musi następować po nazwie iteracji, takiej jak funkcji copyindex ("datadisks", 1).
>

Menedżer zasobów rozszerza tablicę `copy` podczas wdrażania. Nazwa tablicy zmieni się na nazwę właściwości. Wartości wejściowe stają się właściwościami obiektu. Wdrożony szablon zostanie:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

Kopiuj element jest tablicą, więc można określić więcej niż jedną właściwość zasobu.

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "examleLB",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

Iteracji zasobów i właściwości można używać razem. Odwołuje się do iteracji właściwości według nazwy.

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "apiVersion": "2018-04-01",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

## <a name="copy-limits"></a>Limity kopiowania

Liczba nie może przekraczać 800.

Liczba nie może być liczbą ujemną. Jeśli szablon jest wdrażany z Azure PowerShell 2,6 lub nowszym, interfejs wiersza polecenia platformy Azure 2.0.74 lub nowszy albo interfejs API REST w wersji **2019-05-10** lub nowszej, można ustawić liczbę na zero. We wcześniejszych wersjach programu PowerShell, interfejsu wiersza polecenia i interfejsie API REST nie są obsługiwane wartości zerowe.

## <a name="example-templates"></a>Przykładowe szablony

Poniższy przykład przedstawia typowy scenariusz tworzenia więcej niż jednej wartości właściwości.

|Szablon  |Opis  |
|---------|---------|
|[Wdrożenie maszyny wirtualnej z zmienną liczbą dysków z danymi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Wdraża kilka dysków danych z maszyną wirtualną. |

## <a name="next-steps"></a>Następne kroki

* Aby przejść przez samouczek, zobacz [Samouczek: Tworzenie wielu wystąpień zasobów przy użyciu szablonów Menedżer zasobów](template-tutorial-create-multiple-instances.md).
* Aby poznać inne zastosowania elementu Copy, zobacz [iteracja zasobów w szablonach Azure Resource Manager](copy-resources.md) i [iteracji zmiennych w szablonach Azure Resource Manager](copy-variables.md).
* Jeśli chcesz dowiedzieć się więcej na temat sekcji szablonu, zobacz [Tworzenie szablonów Azure Resource Manager](template-syntax.md).
* Aby dowiedzieć się, jak wdrożyć szablon, zobacz [wdrażanie aplikacji przy użyciu szablonu Azure Resource Manager](deploy-powershell.md).

