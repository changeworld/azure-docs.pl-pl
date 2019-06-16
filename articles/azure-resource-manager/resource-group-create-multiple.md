---
title: Wdrażanie wielu wystąpień zasobów platformy Azure | Dokumentacja firmy Microsoft
description: Użyj operacji kopiowania i tablic w szablonie usługi Azure Resource Manager do iteracji wielokrotnie podczas wdrażania zasobów.
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: tomfitz
ms.openlocfilehash: 99fd4215de4dd118558acc008fcfa6490ea0093d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66807366"
---
# <a name="resource-property-or-variable-iteration-in-azure-resource-manager-templates"></a>Zasób, właściwości lub zmiennej iteracji w szablonach usługi Azure Resource Manager

W tym artykule pokazano, jak utworzyć więcej niż jedno wystąpienie zasobu, zmiennej lub właściwości w szablonie usługi Azure Resource Manager. Aby utworzyć wiele wystąpień, Dodaj `copy` obiektu do szablonu.

W przypadku użycia z zasobem, Kopiuj obiekt ma następujący format:

```json
"copy": {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "mode": "serial" <or> "parallel",
    "batchSize": <number-to-deploy-serially>
}
```

W przypadku użycia za pomocą zmiennej lub właściwości, Kopiuj obiekt ma następujący format:

```json
"copy": [
  {
      "name": "<name-of-loop>",
      "count": <number-of-iterations>,
      "input": <values-for-the-property-or-variable>
  }
]
```

Oba te zastosowania są opisane bardziej szczegółowo w tym artykule. Aby zapoznać się z samouczkiem, zobacz [samouczek: tworzenie wielu wystąpień zasobów przy użyciu szablonów usługi Resource Manager](./resource-manager-tutorial-create-multiple-instances.md).

Jeśli musisz określić, czy zasób został wdrożony na wszystkich, zobacz [element warunek](resource-group-authoring-templates.md#condition).

## <a name="copy-limits"></a>Limity kopiowania

Aby określić liczbę iteracji, podaj wartość dla właściwości. Liczba nie może przekraczać 800.

Liczba nie może być liczbą ujemną. Jeśli wdrażanie szablonu przy użyciu wersji interfejsu API REST **2019-05-10** lub później, możesz ustawić liczbę zero. Wcześniejszych wersjach interfejsu API REST nie obsługują wartości zero dla liczby. Obecnie wiersza polecenia platformy Azure lub programu PowerShell nie obsługują zero, Count, ale ta obsługa zostanie dodana w przyszłej wersji.

Limity dotyczące liczby są takie same, czy używane z zasobów, zmiennej lub właściwości.

## <a name="resource-iteration"></a>Iteracja zasobów

Jeśli podczas wdrażania należy zdecydować, aby utworzyć jeden lub więcej wystąpień zasobu, dodać `copy` elementu z typem zasobu. W elemencie kopiowania Określ liczbę iteracji i nazwę dla tej pętli.

Zasób, aby utworzyć kilka razy ma następujący format:

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

Należy zauważyć, że nazwa każdego zasobu zawiera `copyIndex()` funkcji, która zwraca bieżącej iteracji w pętli. Funkcja `copyIndex()` rozpoczyna liczenie od zera. Dlatego następująco:

```json
"name": "[concat('storage', copyIndex())]",
```

Tworzy następujące nazwy:

* storage0
* storage1
* storage2.

Aby przesunąć wartość indeksu, możesz przekazać wartość do funkcji copyIndex(). Liczba iteracji jest nadal określony w elemencie kopii, ale wartość copyIndex jest przesunięty przez określoną wartość. Dlatego następująco:

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

Domyślnie Menedżer zasobów tworzy zasoby równolegle. Nie jest gwarantowana kolejność, w którym są tworzone. Można określić, że zasoby są wdrażane w sekwencji. Na przykład podczas aktualizowania środowiska produkcyjnego, warto więc przesunąć aktualizacje tylko pewną liczbę są aktualizowane w dowolnym momencie.

Aby szeregowo wdrożyć więcej niż jedno wystąpienie zasobu, należy ustawić `mode` do **serial** i `batchSize` do liczby wystąpień do wdrożenia w danym momencie. Serial w trybie usługi Resource Manager tworzy zależność w wystąpieniach wcześniej w pętli, więc nie uruchamia jedną partię, aż do zakończenia poprzedniej partii.

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

Aby dowiedzieć się, jak przy użyciu kopiowania przy użyciu zagnieżdżonych szablonów, zobacz [przy użyciu kopii](resource-group-linked-templates.md#using-copy).

## <a name="property-iteration"></a>Właściwość iteracji

Aby utworzyć więcej niż jedną wartość dla właściwości do zasobu, należy dodać `copy` tablicy w elemencie właściwości. Ta tablica zawiera obiekty, a każdy obiekt ma następujące właściwości:

* Nazwa — Nazwa właściwości do utworzenia wielu wartości
* Liczba — liczba wartości do utworzenia.
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
      ],
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

Aby utworzyć wiele wystąpień w zmiennej, użyj `copy` właściwości w sekcji zmiennych. Utwórz tablicę elementów skonstruowany na podstawie wartości w `input` właściwości. Możesz użyć `copy` właściwości w ramach zmiennej lub na najwyższym poziomie w sekcji zmiennych. Korzystając z `copyIndex` wewnątrz zmiennej iteracji, należy podać nazwę iteracji.

Prosty przykład tworzenia tablicy wartości ciągu, można zobaczyć [kopiowania tablicy szablonu](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/copy-array/azuredeploy.json).

Poniższy przykład pokazuje kilka różnych sposobów tworzenia zmiennych tablicowych z elementami dynamicznie skonstruowany. Widoczny jest sposób kopii wewnątrz zmiennej należy użyć do tworzenia tablic obiektów i ciągów. Pokazano również, jak na potrzeby kopiowania na najwyższym poziomie tworzenie tablic obiektów, ciągi i liczby całkowite.

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

Typ zmiennej, która zostanie utworzona, zależy od obiektu wejściowego. Na przykład zmienna o nazwie **top-poziomu-object tablica** w poprzednim przykładzie zwraca:

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

A zmiennej o nazwie **top-poziomu-ciągu tablica** zwraca:

```json
[
  "myDataDisk1",
  "myDataDisk2",
  "myDataDisk3",
  "myDataDisk4",
  "myDataDisk5"
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
Nie można używać pętli kopii zasobu podrzędnego. Aby utworzyć więcej niż jednego wystąpienia z zasobem, który zazwyczaj zdefiniowane jako zagnieżdżone w obrębie innego zasobu, możesz zamiast tego utworzyć tego zasobu jako zasobem najwyższego poziomu. Należy zdefiniować relację z zasobem nadrzędnym, przez typ i nazwę właściwości.

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
  ]
```

Aby utworzyć więcej niż jeden zestaw danych, można go przenieść poza fabryki danych. Zestaw danych musi być tym samym poziomie, ponieważ fabryka danych, ale nadal jest zasobem podrzędnych usługi data factory. Możesz zachować relacji między zestawem danych i usługi data factory przy użyciu typu i nazwy właściwości. Ponieważ nie można wywnioskować typu z pozycji w szablonie, należy podać w pełni kwalifikowanego typu, w formacie: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

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
  },
  ...
}]
```

## <a name="example-templates"></a>Przykładowe szablony

W poniższych przykładach pokazano typowe scenariusze dotyczące tworzenia więcej niż jedno wystąpienie zasobu lub właściwości.

|Szablon  |Opis  |
|---------|---------|
|[W magazynie kopii](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Wdraża więcej niż jedno konto magazynu przy użyciu numer indeksu w nazwie. |
|[W magazynie kopii szeregowej](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Wdraża kilka kont magazynu w jednym momencie. Nazwa zawiera numer indeksu. |
|[W magazynie kopii z tablicą](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Wdraża kilka kont magazynu. Nazwa zawiera wartości z tablicy. |
|[Wdrażanie maszyny Wirtualnej z różną liczbą dysków z danymi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Wdraża kilka dysków z danymi z maszyną wirtualną. |
|[Skopiuj zmienne](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Pokazuje różne sposoby iteracja zmiennych. |
|[Wiele reguł zabezpieczeń](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Wdraża kilka reguły zabezpieczeń sieciowej grupy zabezpieczeń. Jego tworzy reguły zabezpieczeń z parametru. W przypadku parametru zobacz [wielu plików parametr sieciowej grupy zabezpieczeń](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Kolejne kroki

* Aby wykonać kroki samouczka, zobacz [samouczek: tworzenie wielu wystąpień zasobów przy użyciu szablonów usługi Resource Manager](./resource-manager-tutorial-create-multiple-instances.md).

* Jeśli chcesz dowiedzieć się więcej o części szablonu, zobacz [Tworzenie szablonów usługi Resource Manager platformy Azure](resource-group-authoring-templates.md).
* Aby dowiedzieć się, jak wdrożyć szablon, zobacz [wdrażanie aplikacji przy użyciu szablonu Azure Resource Manager](resource-group-template-deploy.md).

