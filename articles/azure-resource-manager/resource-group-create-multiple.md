---
title: Wdrażanie wielu wystąpień zasobów platformy Azure | Dokumentacja firmy Microsoft
description: Użyj operacji kopiowania i tablic w szablonie usługi Azure Resource Manager do iteracji wielokrotnie podczas wdrażania zasobów.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2018
ms.author: tomfitz
ms.openlocfilehash: 8828ba3c91df7b0a2fde3c42ecd81bd4ee4d17a3
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295941"
---
# <a name="deploy-multiple-instances-of-a-resource-or-property-in-azure-resource-manager-templates"></a>Wdrażanie wielu wystąpień zasobów lub właściwości w szablonach usługi Resource Manager platformy Azure

W tym artykule pokazano, jak przejść do szablonu usługi Azure Resource Manager do tworzenia wielu wystąpień tego zasobu. Jeśli musisz określić, czy zasób został wdrożony na wszystkich, zobacz [element warunek](resource-manager-templates-resources.md#condition).

Aby zapoznać się z samouczkiem, zobacz [samouczek: tworzenie wielu wystąpień zasobów przy użyciu szablonów usługi Resource Manager](./resource-manager-tutorial-create-multiple-instances.md).

## <a name="resource-iteration"></a>Iteracja zasobów

Jeśli podczas wdrażania należy zdecydować, aby utworzyć jeden lub więcej wystąpień zasobu, dodać `copy` elementu z typem zasobu. Copy element służy do określenia liczby iteracji i nazwę dla tej pętli. Wartość licznika musi być dodatnią liczbą całkowitą i nie może przekraczać 800. 

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

Należy zauważyć, że nazwa każdego zasobu zawiera `copyIndex()` funkcji, która zwraca bieżącej iteracji w pętli. `copyIndex()` jest liczony od zera. Dlatego następująco:

```json
"name": "[concat('storage', copyIndex())]",
```

Tworzy następujące nazwy:

* storage0
* storage1
* storage2.

Aby przesunąć wartość indeksu, można przekazać wartość w funkcji copyIndex(). Liczba iteracji, aby wykonać nadal jest określony w elemencie kopii, ale wartość copyIndex jest przesunięty przez określoną wartość. Dlatego następująco:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Tworzy następujące nazwy:

* storage1
* storage2
* storage3

Operacja kopiowania jest przydatne podczas pracy z tablicami, ponieważ można wykonać iterację każdego elementu w tablicy. Użyj `length` funkcji w macierzy, aby określić liczbę iteracji, oraz `copyIndex` można pobrać bieżący indeks w tablicy. Dlatego następująco:

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

Domyślnie Menedżer zasobów tworzy zasoby równolegle. W związku z tym nie jest gwarantowana kolejność, w którym są tworzone. Można określić, że zasoby są wdrażane w sekwencji. Na przykład podczas aktualizowania środowiska produkcyjnego, warto więc przesunąć aktualizacje tylko pewną liczbę są aktualizowane w dowolnym momencie.

Szeregowo wdrażanie wielu wystąpień zasobu, należy ustawić `mode` do **serial** i `batchSize` do liczby wystąpień do wdrożenia w danym momencie. Serial w trybie usługi Resource Manager tworzy zależność w wystąpieniach wcześniej w pętli, więc nie uruchamia jedną partię, aż do zakończenia poprzedniej partii.

Na przykład aby szeregowo wdrożyć kont magazynu, dwa w czasie, należy użyć:

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

Właściwość trybu akceptuje także **równoległe**, która jest wartością domyślną.

## <a name="property-iteration"></a>Właściwość iteracji

Aby utworzyć wiele wartości dla właściwości do zasobu, należy dodać `copy` tablicy w elemencie właściwości. Ta tablica zawiera obiekty, a każdy obiekt ma następujące właściwości:

* Nazwa — Nazwa właściwości do utworzenia wielu wartości
* Liczba — liczba wartości do utworzenia
* dane wejściowe — obiekt zawierający wartości do przypisania do właściwości  

Poniższy przykład pokazuje, jak zastosować `copy` właściwości dataDisks na maszynie wirtualnej:

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

Należy zauważyć, że podczas korzystania `copyIndex` wewnątrz iteracji właściwość, należy podać nazwę iteracji. Nie trzeba podać nazwę, gdy jest używane z zasobu iteracji.

Menedżer zasobów rozszerza `copy` tablicy podczas wdrażania. Nazwa tablicy staje się nazwę właściwości. Wartości wejściowe stają się właściwości obiektu. Wdrożone szablon staje się:

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

Copy element jest tablicą, dzięki czemu można określić więcej niż jednej właściwości zasobu. Dodawanie obiektu dla każdej właściwości do utworzenia.

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

Możesz użyć zasobów i właściwości iteracji. Odwołanie do iteracji właściwość według nazwy.

```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[concat(parameters('vnetname'), copyIndex())]",
    "apiVersion": "2018-04-01",
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

## <a name="variable-iteration"></a>Zmienna iteracji

Aby utworzyć wiele wystąpień w zmiennej, użyj `copy` element w sekcji zmiennych. Można tworzenie wielu wystąpień obiektów za pomocą powiązanych wartości, a następnie przypisać te wartości do wystąpień zasobu. Aby utworzyć obiekt przy użyciu właściwości tablicy lub tablicy, można użyć kopii. W poniższym przykładzie przedstawiono oba podejścia:

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

Każda z tych metod copy element jest tablicą, można określić więcej niż jedną zmienną. Dodawanie obiektu dla każdej zmiennej utworzyć.

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
Należy określić, że zasób został wdrożony po inny zasób za pomocą `dependsOn` elementu. Aby wdrożyć zasobem, który zależy od kolekcji zasobów w pętli, należy podać nazwę pętlę kopiowania w elemencie dependsOn. Poniższy przykład pokazuje, jak wdrożyć trzy konta magazynu przed wdrożeniem maszyny wirtualnej. Pełna definicja maszyny wirtualnej nie jest wyświetlana. Należy zauważyć, że copy element nazwie równa `storagecopy` i element dependsOn dla maszyn wirtualnych jest również ustawiona na `storagecopy`.

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

## <a name="iteration-for-a-child-resource"></a>Iteracji dla zasobu podrzędnego
Nie można używać pętli kopii zasobu podrzędnego. Aby utworzyć wiele wystąpień zasobu, który zazwyczaj zdefiniowane jako zagnieżdżone w obrębie innego zasobu, możesz zamiast tego utworzyć tego zasobu jako zasobem najwyższego poziomu. Należy zdefiniować relację z zasobem nadrzędnym, przez typ i nazwę właściwości.

Na przykład załóżmy, że zwykle zdefiniujesz zestaw danych jako zasoby podrzędne w ramach usługi data factory.

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

Aby utworzyć wiele wystąpień zestawów danych, można go przenieść poza fabryki danych. Zestaw danych musi być tym samym poziomie, ponieważ fabryka danych, ale nadal jest zasobem podrzędnych usługi data factory. Możesz zachować relacji między zestawem danych i usługi data factory przy użyciu typu i nazwy właściwości. Ponieważ nie można wywnioskować typu z pozycji w szablonie, należy podać w pełni kwalifikowanego typu, w formacie: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Aby ustalić relacji nadrzędny/podrzędny z wystąpienia usługi data factory, podaj nazwę dla zestawu danych, która zawiera nazwę zasobu nadrzędnego. Użyj formatu: `{parent-resource-name}/{child-resource-name}`.  

Poniższy przykład pokazuje implementację:

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

## <a name="example-templates"></a>Przykładowe szablony

W poniższych przykładach pokazano typowe scenariusze dotyczące tworzenia wielu zasobów lub właściwości.

|Szablon  |Opis  |
|---------|---------|
|[W magazynie kopii](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Wdraża wiele kont magazynu o numer indeksu w nazwie. |
|[W magazynie kopii szeregowej](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Służy do wdrażania wielu kont magazynu, co w czasie. Nazwa zawiera numer indeksu. |
|[W magazynie kopii z tablicą](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Służy do wdrażania wielu kont magazynu. Nazwa zawiera wartości z tablicy. |
|[Wdrażanie maszyny Wirtualnej z różną liczbą dysków z danymi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Służy do wdrażania wielu dysków z danymi z maszyną wirtualną. |
|[Skopiuj zmienne](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Pokazuje różne sposoby iteracja zmiennych. |
|[Wiele reguł zabezpieczeń](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Wdrażanie wielu reguł zabezpieczeń w usłudze sieciowej grupy zabezpieczeń. Jego tworzy reguły zabezpieczeń z parametru. W przypadku parametru zobacz [wielu plików parametr sieciowej grupy zabezpieczeń](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Kolejne kroki

* Aby wykonać kroki samouczka, zobacz [samouczek: tworzenie wielu wystąpień zasobów przy użyciu szablonów usługi Resource Manager](./resource-manager-tutorial-create-multiple-instances.md).

* Jeśli chcesz dowiedzieć się więcej o części szablonu, zobacz [Tworzenie szablonów usługi Resource Manager platformy Azure](resource-group-authoring-templates.md).
* Aby dowiedzieć się, jak wdrożyć szablon, zobacz [wdrażanie aplikacji przy użyciu szablonu Azure Resource Manager](resource-group-template-deploy.md).

