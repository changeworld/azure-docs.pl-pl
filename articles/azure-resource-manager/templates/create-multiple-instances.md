---
title: Wdróż wiele wystąpień zasobów
description: Użyj operacji kopiowania i tablic w szablonie Azure Resource Manager, aby wielokrotnie wykonywać iteracje podczas wdrażania zasobów.
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: ed822862abee3fd05d3236d7c12562cd86768086
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479594"
---
# <a name="resource-property-or-variable-iteration-in-azure-resource-manager-templates"></a>Iteracja zasobu, właściwości lub zmiennej w szablonach Azure Resource Manager

W tym artykule pokazano, jak utworzyć więcej niż jedno wystąpienie zasobu, zmiennej lub właściwości w szablonie Azure Resource Manager. Aby utworzyć wiele wystąpień, Dodaj obiekt `copy` do szablonu.

Gdy jest używany z zasobem, obiekt Copy ma następujący format:

```json
"copy": {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "mode": "serial" <or> "parallel",
    "batchSize": <number-to-deploy-serially>
}
```

W przypadku użycia z zmienną lub właściwością obiekt Copy ma następujący format:

```json
"copy": [
  {
      "name": "<name-of-loop>",
      "count": <number-of-iterations>,
      "input": <values-for-the-property-or-variable>
  }
]
```

Oba zastosowania zostały szczegółowo opisane w tym artykule. Aby zapoznać się z samouczkiem, zobacz [Samouczek: Tworzenie wielu wystąpień zasobów przy użyciu szablonów Menedżer zasobów](template-tutorial-create-multiple-instances.md).

Jeśli musisz określić, czy zasób został wdrożony w ogóle, zobacz [warunek elementu](conditional-resource-deployment.md).

## <a name="copy-limits"></a>Limity kopiowania

Aby określić liczbę iteracji, należy podać wartość właściwości Count. Liczba nie może przekraczać 800.

Liczba nie może być liczbą ujemną. Jeśli szablon jest wdrażany z Azure PowerShell 2,6 lub nowszym, interfejs wiersza polecenia platformy Azure 2.0.74 lub nowszy albo interfejs API REST w wersji **2019-05-10** lub nowszej, można ustawić liczbę na zero. We wcześniejszych wersjach programu PowerShell, interfejsu wiersza polecenia i interfejsie API REST nie są obsługiwane wartości zerowe.

Należy zachować ostrożność przy użyciu [wdrożenia trybu kompletnego](deployment-modes.md) z kopią. W przypadku ponownego wdrożenia z trybem kompletnym do grupy zasobów wszystkie zasoby, które nie są określone w szablonie po usunięciu pętli kopiowania, zostaną usunięte.

Limity liczby są takie same, niezależnie od tego, czy są używane z zasobem, zmienną lub właściwością.

## <a name="resource-iteration"></a>Iteracja zasobu

Jeśli chcesz utworzyć więcej niż jedno wystąpienie zasobu we wdrożeniu, Dodaj element `copy` do typu zasobu. W elemencie Copy (Kopiuj) Określ liczbę iteracji i nazwę tej pętli.

Zasób do utworzenia kilka razy przyjmuje następujący format:

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

Należy zauważyć, że nazwa każdego zasobu zawiera funkcję `copyIndex()`, która zwraca bieżącą iterację w pętli. Funkcja `copyIndex()` rozpoczyna liczenie od zera. Tak więc, Poniższy przykład:

```json
"name": "[concat('storage', copyIndex())]",
```

Tworzy następujące nazwy:

* storage0
* storage1
* storage2.

Aby przesunąć wartość indeksu, możesz przekazać wartość do funkcji copyIndex(). Liczba iteracji jest nadal określona w elemencie Copy, ale wartość funkcji copyindex jest przesunięta przez określoną wartość. Tak więc, Poniższy przykład:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Tworzy następujące nazwy:

* storage1
* storage2
* storage3

Operacja kopiowania jest przydatna podczas pracy z tablicami, ponieważ można wykonać iterację każdego elementu w tablicy. Użyj funkcji `length` w tablicy, aby określić liczbę iteracji i `copyIndex`, aby pobrać bieżący indeks tablicy. Tak więc, Poniższy przykład:

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

Domyślnie Menedżer zasobów tworzy zasoby równolegle. Nie ma żadnego limitu liczby zasobów wdrożonych równolegle, poza całkowitym limitem 800 zasobów w szablonie. Kolejność, w której są tworzone, nie jest gwarantowana.

Można jednak określić, że zasoby są wdrażane w sekwencji. Na przykład podczas aktualizowania środowiska produkcyjnego warto rozłożyć aktualizacje, aby w dowolnym momencie zaktualizować tylko określoną liczbę. Aby przeprowadzić szeregowo wdrożenie więcej niż jednego wystąpienia zasobu, należy ustawić `mode` na **port szeregowy** i `batchSize` do liczby wystąpień do wdrożenia w danym momencie. W trybie serial Menedżer zasobów tworzy zależność od wcześniejszych wystąpień w pętli, dlatego nie uruchamia jednej partii do momentu zakończenia poprzedniej partii.

Na przykład aby przeprowadzić sekwencyjne wdrażanie kont magazynu dwa naraz, należy użyć:

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

Właściwość Mode akceptuje również **Parallel**, która jest wartością domyślną.

Aby uzyskać informacje na temat używania kopiowania z szablonami zagnieżdżonymi, zobacz [using Copy](linked-templates.md#using-copy).

## <a name="property-iteration"></a>Iteracja właściwości

Aby utworzyć więcej niż jedną wartość właściwości zasobu, Dodaj tablicę `copy`ną w elemencie Properties. Ta tablica zawiera obiekty, a każdy obiekt ma następujące właściwości:

* Nazwa — nazwa właściwości, w której ma zostać utworzona kilka wartości
* Count — liczba wartości do utworzenia.
* Input-obiekt, który zawiera wartości do przypisania do właściwości

Poniższy przykład pokazuje, jak zastosować `copy` do właściwości datadisks na maszynie wirtualnej:

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

Należy zauważyć, że w przypadku używania `copyIndex` wewnątrz iteracji właściwości należy podać nazwę iteracji. Nie musisz podawać nazwy, jeśli jest używana z iteracją zasobu.

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

Kopiuj element jest tablicą, więc można określić więcej niż jedną właściwość zasobu. Dodaj obiekt dla każdej właściwości, która ma zostać utworzona.

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

Iteracji zasobów i właściwości można używać razem. Odwołuje się do iteracji właściwości według nazwy.

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

## <a name="variable-iteration"></a>Iteracja zmiennej

Aby utworzyć wiele wystąpień zmiennej, użyj właściwości `copy` w sekcji zmienne. Tworzysz tablicę elementów skonstruowanych na podstawie wartości właściwości `input`. Można użyć właściwości `copy` w ramach zmiennej lub na najwyższym poziomie sekcji zmienne. W przypadku używania `copyIndex` wewnątrz iteracji zmiennej należy podać nazwę iteracji.

Aby zapoznać się z prostym przykładem tworzenia tablicy wartości ciągów, zobacz [copy Array Template](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/copy-array/azuredeploy.json).

W poniższym przykładzie przedstawiono kilka różnych sposobów tworzenia zmiennych tablicowych z dynamicznie skonstruowanymi elementami. Pokazuje, jak używać kopiowania wewnątrz zmiennej do tworzenia tablic obiektów i ciągów. Pokazano również, jak używać kopiowania na najwyższym poziomie do tworzenia tablic obiektów, ciągów i liczb całkowitych.

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

Typ zmiennej, która jest tworzona, zależy od obiektu wejściowego. Na przykład zmienna o nazwie **"najwyższego poziomu** " w powyższym przykładzie zwraca:

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

I zmienna o nazwie **Array-Level-String** zwraca:

```json
[
  "myDataDisk1",
  "myDataDisk2",
  "myDataDisk3",
  "myDataDisk4",
  "myDataDisk5"
]
```

## <a name="depend-on-resources-in-a-loop"></a>Zależą od zasobów w pętli

Należy określić, że zasób zostanie wdrożony po innym zasobie przy użyciu elementu `dependsOn`. Aby wdrożyć zasób zależny od kolekcji zasobów w pętli, podaj nazwę pętli kopiowania w elemencie dependsOn. Poniższy przykład pokazuje, jak wdrożyć trzy konta magazynu przed wdrożeniem maszyny wirtualnej. Pełna definicja maszyny wirtualnej nie jest wyświetlana. Zwróć uwagę, że element Copy ma nazwę ustawioną na `storagecopy` i element dependsOn dla Virtual Machines jest również ustawiony na `storagecopy`.

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

## <a name="iteration-for-a-child-resource"></a>Iteracja zasobu podrzędnego
Nie można użyć pętli kopiowania dla zasobu podrzędnego. Aby utworzyć więcej niż jedno wystąpienie zasobu, który jest zwykle definiowany jako zagnieżdżony w innym zasobie, należy zamiast tego utworzyć ten zasób jako zasób najwyższego poziomu. Relację z zasobem nadrzędnym definiuje się przy użyciu właściwości typu i nazwy.

Załóżmy na przykład, że zwykle zdefiniujesz zestaw danych jako zasób podrzędny w fabryce danych.

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

Aby utworzyć więcej niż jeden zestaw danych, przenieś go poza fabrykę danych. Zestaw danych musi znajdować się na tym samym poziomie co Fabryka danych, ale nadal jest zasobem podrzędnym fabryki danych. Relację między zestawem danych a fabryką danych można zachować za pomocą właściwości Typ i nazwa. Ponieważ typ nie może być już wywnioskowany na podstawie jego pozycji w szablonie, należy podać w formacie w pełni kwalifikowany typ: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Aby ustanowić relację nadrzędną/podrzędną z wystąpieniem fabryki danych, podaj nazwę zestawu danych, który zawiera nazwę zasobu nadrzędnego. Użyj formatu: `{parent-resource-name}/{child-resource-name}`.

W poniższym przykładzie przedstawiono implementację:

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

W poniższych przykładach przedstawiono typowe scenariusze tworzenia więcej niż jednego wystąpienia zasobu lub właściwości.

|Szablon  |Opis  |
|---------|---------|
|[Kopiuj magazyn](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Wdraża więcej niż jedno konto magazynu o numerze indeksu w nazwie. |
|[Magazyn kopii seryjnych](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Wdraża kilka kont magazynu jeden w czasie. Nazwa zawiera numer indeksu. |
|[Kopiuj magazyn z tablicą](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Wdraża kilka kont magazynu. Nazwa zawiera wartość z tablicy. |
|[Wdrożenie maszyny wirtualnej z zmienną liczbą dysków z danymi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Wdraża kilka dysków danych z maszyną wirtualną. |
|[Skopiuj zmienne](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Ilustruje różne sposoby iteracji na zmiennych. |
|[Wiele reguł zabezpieczeń](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Wdraża kilka reguł zabezpieczeń w sieciowej grupie zabezpieczeń. Konstruuje reguły zabezpieczeń z parametru. Dla parametru zobacz [wiele plików parametrów sieciowej grupy zabezpieczeń](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Następne kroki

* Aby przejść przez samouczek, zobacz [Samouczek: Tworzenie wielu wystąpień zasobów przy użyciu szablonów Menedżer zasobów](template-tutorial-create-multiple-instances.md).

* Jeśli chcesz dowiedzieć się więcej na temat sekcji szablonu, zobacz [Tworzenie szablonów Azure Resource Manager](template-syntax.md).
* Aby dowiedzieć się, jak wdrożyć szablon, zobacz [wdrażanie aplikacji przy użyciu szablonu Azure Resource Manager](deploy-powershell.md).

