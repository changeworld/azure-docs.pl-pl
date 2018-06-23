---
title: Wdrażanie wielu wystąpień zasobów platformy Azure | Dokumentacja firmy Microsoft
description: Użyj operacji kopiowania i tablic w szablonie usługi Azure Resource Manager w celu wykonania iteracji wiele razy podczas wdrażania zasobów.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''
ms.assetid: 94d95810-a87b-460f-8e82-c69d462ac3ca
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2018
ms.author: tomfitz
ms.openlocfilehash: 580ecc98913dc35e2d1e21f1dcfa19936bb59826
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36337961"
---
# <a name="deploy-multiple-instances-of-a-resource-or-property-in-azure-resource-manager-templates"></a>Wdrażanie wielu wystąpień zasobów lub właściwości w szablonach Menedżera zasobów Azure
W tym artykule przedstawiono warunkowo wdrażanie zasobu i porady dotyczące iteracji po do szablonu usługi Azure Resource Manager, aby utworzyć wiele wystąpień zasobu.

## <a name="conditionally-deploy-resource"></a>Warunkowo wdrażanie zasobów

Jeśli podczas wdrażania należy zdecydować, aby utworzyć jedno wystąpienie lub żadnych wystąpień zasobu, użyj `condition` elementu. Wartość dla tego elementu jest rozpoznawany jako PRAWDA lub FAŁSZ. Gdy ma wartość true, zasób zostanie wdrożona. Gdy wartość jest równa false, zasób nie jest wdrożona. Na przykład aby określić, czy nowe konto magazynu jest wdrażana, czy istniejące konto magazynu jest używana, należy użyć:

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

## <a name="resource-iteration"></a>Iteracja zasobów
Jeśli podczas wdrażania należy zdecydować, aby utworzyć co najmniej jednego wystąpienia zasobu, należy dodać `copy` elementu Typ zasobu. Copy element służy do określenia liczby iteracji i nazwę tej pętli. Wartość licznika musi być dodatnią liczbą całkowitą i nie może przekraczać 800. 

Zasobu do utworzenia wielokrotnie ma następujący format:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        }
    ],
    "outputs": {}
}
```

Należy zauważyć, że nazwa każdego zasobu zawiera `copyIndex()` funkcji, która zwraca bieżącą iterację w pętli. `copyIndex()` jest liczony od zera. Zatem w poniższym przykładzie:

```json
"name": "[concat('storage', copyIndex())]",
```

Tworzy następujące nazwy:

* storage0
* storage1
* storage2.

Aby przesunąć wartość indeksu, można przekazać wartość w funkcji copyIndex(). Liczba iteracji, aby wykonać nadal jest określona w elemencie kopiowania, ale wartość copyIndex zostanie przesunięty o określonej wartości. Zatem w poniższym przykładzie:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Tworzy następujące nazwy:

* storage1
* storage2
* storage3

Operacja kopiowania jest przydatne podczas pracy z tablicami, ponieważ można wykonać iterację każdego elementu w tablicy. Użyj `length` funkcji w macierzy, aby określić liczbę elementów w iteracji, i `copyIndex` można pobrać bieżącego indeksu tablicy. Zatem w poniższym przykładzie:

```json
"parameters": { 
  "org": { 
     "type": "array", 
     "defaultValue": [ 
         "contoso", 
         "fabrikam", 
         "coho" 
      ] 
  }
}, 
"resources": [ 
  { 
      "name": "[concat('storage', parameters('org')[copyIndex()])]", 
      "copy": { 
         "name": "storagecopy", 
         "count": "[length(parameters('org'))]" 
      }, 
      ...
  } 
]
```

Tworzy następujące nazwy:

* storagecontoso
* storagefabrikam
* storagecoho

Domyślnie usługi Resource Manager tworzy zasoby równolegle. W związku z tym nie jest gwarantowana kolejność, w którym są tworzone. Można określić, że zasoby są wdrażane w sekwencji. Na przykład podczas aktualizacji do środowiska produkcyjnego, można tak skonfigurować aktualizacje tylko pewne są aktualizowane w dowolnym momencie.

Aby wdrożyć kolejno wielu wystąpień zasobu, należy ustawić `mode` do **serial** i `batchSize` liczby wystąpień do wdrożenia w czasie. W trybie serial Resource Manager tworzy zależność w wystąpieniach wcześniej w pętli, więc nie uruchamia jedno zadanie wsadowe, do chwili zakończenia poprzedniej wsadowym.

Na przykład pojedynczo wdrożenie kont magazynu, dwa w czasie, należy użyć:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 4,
                "mode": "serial",
                "batchSize": 2
            }
        }
    ],
    "outputs": {}
}
``` 

Właściwość mode również akceptuje **równoległych**, która jest wartością domyślną.

## <a name="property-iteration"></a>Właściwość iteracji

Aby utworzyć wiele wartości dla właściwości do zasobu, należy dodać `copy` tablicy w elemencie właściwości. Ta tablica zawiera obiekty, a każdy obiekt ma następujące właściwości:

* Nazwa — Nazwa właściwości do utworzenia wielu wartości
* Liczba — liczba wartości w celu utworzenia
* dane wejściowe - obiekt, który zawiera wartości, które można przypisać do właściwości  

Poniższy przykład przedstawia sposób zastosowania `copy` właściwości dataDisks na maszynie wirtualnej:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "copy": [{
          "name": "dataDisks",
          "count": 3,
          "input": {
              "lun": "[copyIndex('dataDisks')]",
              "createOption": "Empty",
              "diskSizeGB": "1023"
          }
      }],
      ...
```

Zwróć uwagę, że przy użyciu `copyIndex` wewnątrz iteracji właściwości, należy podać nazwę iteracji. Nie trzeba podać nazwę w przypadku użycia z zasobów iteracji.

Menedżer zasobów rozszerza `copy` tablicy podczas wdrażania. Nazwa tablicy staje się nazwa właściwości. Wartości wejściowe stają się właściwości obiektu. Wdrożone szablon staje się:

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
              "diskSizeGB": "1023"
          },
          {
              "lun": 1,
              "createOption": "Empty",
              "diskSizeGB": "1023"
          },
          {
              "lun": 2,
              "createOption": "Empty",
              "diskSizeGB": "1023"
          }
      }],
      ...
```

Copy element jest tablicą, można określić więcej niż jedną właściwość dla zasobu. Dodaj obiekt, dla każdej właściwości do utworzenia.

```json
{
    "name": "string",
    "type": "Microsoft.Network/loadBalancers",
    "apiVersion": "2017-10-01",
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

Iteracja zasobów i właściwości można użyć razem. Odwołanie do iteracji właściwości według nazwy.

```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[concat(parameters('vnetname'), copyIndex())]",
    "apiVersion": "2016-06-01",
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

## <a name="variable-iteration"></a>Zmiennej iteracji

Aby utworzyć wiele wystąpień w zmiennej, użyj `copy` element w sekcji zmiennych. Można utworzyć wiele wystąpień obiektów z wartościami pokrewne i Przypisz te wartości wystąpienia zasobu. Kopiuj służy do tworzenia obiekt o właściwości tablicy lub tablica. W poniższym przykładzie przedstawiono obu podejść:

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
        }
      ]
    },
    "copy": [
      {
        "name": "disks-top-level-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('disks-top-level-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('disks-top-level-array')]"
        }
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
    "exampleArray": {
      "value": "[variables('disks-top-level-array')]",
      "type" : "array"
    }
  }
}
```

Albo podejście copy element jest tablicą, można określić więcej niż jedną zmienną. Dodaj obiekt, dla każdej zmiennej utworzyć.

```json
"copy": [
  {
    "name": "first-variable",
    "count": 5,
    "input": {
      "demoProperty": "[concat('myProperty', copyIndex('first-variable'))]",
    }
  },
  {
    "name": "second-variable",
    "count": 3,
    "input": {
      "demoProperty": "[concat('myProperty', copyIndex('second-variable'))]",
    }
  },
]
```

## <a name="depend-on-resources-in-a-loop"></a>Są zależne od zasobów w pętli
Określ, czy zasób jest wdrażane za pomocą po inny zasób `dependsOn` elementu. Aby wdrożyć z zasobem, który jest zależny od kolekcji zasobów w pętli, podaj nazwę pętlę kopiowania w elemencie dependsOn. Poniższy przykład przedstawia sposób wdrażania trzy konta magazynu przed wdrożeniem maszyny wirtualnej. Pełnej definicji maszyny wirtualnej nie jest widoczne. Zwróć uwagę, że copy element ma nazwę ustawioną `storagecopy` element dependsOn dla maszyn wirtualnych jest również ustawiono `storagecopy`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        },
        {
            "apiVersion": "2015-06-15", 
            "type": "Microsoft.Compute/virtualMachines", 
            "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
            "dependsOn": ["storagecopy"],
            ...
        }
    ],
    "outputs": {}
}
```

<a id="looping-on-a-nested-resource" />

## <a name="iteration-for-a-child-resource"></a>Iteracje dla zasobu podrzędnego
Nie można używać pętli kopii zasobu podrzędnego. Aby utworzyć wiele wystąpień z zasobem, który zazwyczaj zdefiniowane jako zagnieżdżony w ramach innego zasobu, należy zamiast tego utworzyć tego zasobu jako zasób najwyższego poziomu. Można zdefiniować relacji z zasobem nadrzędnej za pośrednictwem typem i nazwą właściwości.

Na przykład załóżmy, że zazwyczaj Definiowanie zestawu danych jako zasób podrzędnych w fabryce danych.

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
    "resources": [
    {
        "type": "datasets",
        "name": "exampleDataSet",
        "dependsOn": [
            "exampleDataFactory"
        ],
        ...
    }
}]
```

Aby utworzyć wiele wystąpień zestawów danych, przenieś go poza fabryki danych. Zestaw danych musi być na tym samym poziomie jako fabryki danych, ale nadal jest zasobem podrzędnych fabryki danych. Możesz zachować relacji między zestawu danych i fabryki danych za pośrednictwem typem i nazwą właściwości. Ponieważ nie można wywnioskować typu z jej położenie w szablonie, należy podać pełną typu w formacie: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Aby ustalić relacji nadrzędny/podrzędny z wystąpienia fabryki danych, należy podać nazwę dla zestawu danych, która zawiera nazwę zasobu nadrzędnego. Użyj formatu: `{parent-resource-name}/{child-resource-name}`.  

W poniższym przykładzie przedstawiono implementacji:

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
},
{
    "type": "Microsoft.DataFactory/datafactories/datasets",
    "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
    "dependsOn": [
        "exampleDataFactory"
    ],
    "copy": { 
        "name": "datasetcopy", 
        "count": "3" 
    } 
    ...
}]
```

## <a name="example-templates"></a>Przykład szablonów

Poniżej przedstawiono typowe scenariusze dotyczące tworzenia wielu zasobów lub właściwości.

|Szablon  |Opis  |
|---------|---------|
|[Magazyn kopii](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Wdraża wiele kont magazynu o numerze indeksu w nazwie. |
|[Magazyn kopii szeregowe](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Wdraża wiele kont magazynu co w czasie. Nazwa zawiera numer indeksu. |
|[Skopiuj magazynu z tablicy](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Wdraża wiele kont magazynu. Nazwa zawiera wartość z tablicy. |
|[Maszyna wirtualna z nowej lub istniejącej sieci wirtualnej, magazynu i publicznego adresu IP](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions) |Warunkowo wdraża nowych lub istniejących zasobów z maszyną wirtualną. |
|[Wdrażanie maszyny Wirtualnej ze zmienną liczbą dysków z danymi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Wdraża wiele dysków z danymi z maszyną wirtualną. |
|[Skopiuj zmiennych](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Przedstawia różne sposoby iteracja na zmiennych. |
|[Wiele reguł zabezpieczeń](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Wdraża wiele reguł zabezpieczeń grupy zabezpieczeń sieci. Tworzy ona zasady zabezpieczeń z parametrem. W parametrze, zobacz [wielu pliku parametrów NSG](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Kolejne kroki
* Jeśli chcesz dowiedzieć się więcej o części szablonu, zobacz [Authoring Azure Resource Manager szablony](resource-group-authoring-templates.md).
* Aby dowiedzieć się, jak wdrożyć szablon, zobacz [wdrażanie aplikacji przy użyciu szablonu usługi Resource Manager Azure](resource-group-template-deploy.md).

