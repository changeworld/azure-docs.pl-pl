---
title: Wdrażanie wielu wystąpień zasobów
description: Użyj operacji kopiowania i tablic w szablonie usługi Azure Resource Manager, aby wdrożyć typ zasobu wiele razy.
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: e65ab93c21daffa0053e53d953fe95fa9f28e2a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153322"
---
# <a name="resource-iteration-in-arm-templates"></a>Iteracja zasobów w szablonach ARM

W tym artykule pokazano, jak utworzyć więcej niż jedno wystąpienie zasobu w szablonie usługi Azure Resource Manager (ARM). Dodając element **kopiowania** do sekcji zasobów szablonu, można dynamicznie ustawić liczbę zasobów do wdrożenia. Można również unikać konieczności powtarzania składni szablonu.

Można również użyć kopiowania z [właściwościami,](copy-properties.md) [zmiennymi](copy-variables.md) i [wyjściowymi](copy-outputs.md).

Jeśli chcesz określić, czy zasób jest w ogóle wdrażany, zobacz [element warunku](conditional-resource-deployment.md).

## <a name="resource-iteration"></a>Iteracja zasobów

Element kopiowania ma następujący ogólny format:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

Właściwość **name** jest dowolną wartością, która identyfikuje pętlę. Właściwość **count** określa liczbę iteracji, które mają dla typu zasobu.

Użyj **właściwości mode** i **batchSize,** aby określić, czy zasoby są wdrażane równolegle lub w sekwencji. Te właściwości są opisane w [szeregowym lub równoległym](#serial-or-parallel).

Poniższy przykład tworzy liczbę kont magazynu określonych w **storageCount** parametru.

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
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
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
    "outputs": {}
}
```

Należy zauważyć, że nazwa `copyIndex()` każdego zasobu zawiera funkcję, która zwraca bieżącą iterację w pętli. Funkcja `copyIndex()` rozpoczyna liczenie od zera. Tak więc, następujący przykład:

```json
"name": "[concat('storage', copyIndex())]",
```

Tworzy następujące nazwy:

* pamięć masowa0
* przechowywanie1
* przechowywania2.

Aby przesunąć wartość indeksu, możesz przekazać wartość do funkcji copyIndex(). Liczba iteracji jest nadal określona w elemencie kopiowania, ale wartość copyIndex jest równoważona przez określoną wartość. Tak więc, następujący przykład:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Tworzy następujące nazwy:

* przechowywanie1
* pamięć masowa2
* przechowywanie3

Operacja kopiowania jest przydatna podczas pracy z tablicami, ponieważ można iterować za pośrednictwem każdego elementu w tablicy. Użyj `length` funkcji w tablicy, aby określić liczbę `copyIndex` iteracji i pobrać bieżący indeks w tablicy.

Poniższy przykład tworzy jedno konto magazynu dla każdej nazwy podanej w parametrze.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageNames": {
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
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('storageNames')[copyIndex()], uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[length(parameters('storageNames'))]"
      }
    }
  ],
  "outputs": {}
}
```

Jeśli chcesz zwrócić wartości z wdrożonych zasobów, możesz użyć [kopii w sekcji dane wyjściowe](copy-outputs.md).

## <a name="serial-or-parallel"></a>Szeregowe lub równoległe

Domyślnie Menedżer zasobów tworzy zasoby równolegle. Nie ma zastosowania do liczby zasobów wdrożonych równolegle, innych niż całkowity limit 800 zasobów w szablonie. Kolejność ich tworzenia nie jest gwarantowana.

Można jednak określić, że zasoby są wdrażane w sekwencji. Na przykład podczas aktualizowania środowiska produkcyjnego, można rozłożyć aktualizacje, więc tylko określona liczba są aktualizowane w dowolnym momencie. Aby seryjnie wdrożyć więcej niż jedno `mode` wystąpienie `batchSize` zasobu, ustawić **na szeregowe** i do liczby wystąpień do wdrożenia w czasie. W trybie szeregowym Menedżer zasobów tworzy zależność od wcześniejszych wystąpień w pętli, więc nie uruchamia jednej partii, dopóki nie zostanie ukończona poprzednia partia.

Na przykład, aby seryjnie wdrożyć konta magazynu dwa naraz, należy użyć:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Właściwość mode akceptuje również **równoległe**, co jest wartością domyślną.

## <a name="depend-on-resources-in-a-loop"></a>Polegaj na zasobach w pętli

Można określić, że zasób jest `dependsOn` wdrażany po innym zasobie przy użyciu elementu. Aby wdrożyć zasób, który zależy od zbierania zasobów w pętli, podaj nazwę pętli kopiowania w dependsOn elementu. W poniższym przykładzie pokazano, jak wdrożyć trzy konta magazynu przed wdrożeniem maszyny wirtualnej. Pełna definicja maszyny wirtualnej nie jest wyświetlana. Należy zauważyć, że element `storagecopy` kopiowania ma ustawioną nazwę i dependsOn element dla maszyny wirtualnej jest również ustawiona na `storagecopy`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

## <a name="iteration-for-a-child-resource"></a>Iteracja zasobu podrzędnego

Nie można użyć pętli kopiowania dla zasobu podrzędnego. Aby utworzyć więcej niż jedno wystąpienie zasobu, które zwykle definiuje się jako zagnieżdżone w innym zasobie, należy zamiast tego utworzyć ten zasób jako zasób najwyższego poziomu. Relację z zasobem nadrzędnym można zdefiniować za pomocą właściwości typu i nazwy.

Załóżmy na przykład, że zwykle definiuje się zestaw danych jako zasób podrzędny w fabryce danych.

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

Aby utworzyć więcej niż jeden zestaw danych, przenieś go poza fabrykę danych. Zestaw danych musi znajdować się na tym samym poziomie co fabryka danych, ale nadal jest zasobem podrzędnym fabryki danych. Relacja między zestawem danych a fabryką danych można zachować za pomocą właściwości typu i nazwy. Ponieważ nie można już wywnioskować typu ze swojej pozycji w szablonie, należy podać w pełni kwalifikowany typ w formacie: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Aby ustanowić relację nadrzędny/podrzędny z wystąpieniem fabryki danych, podaj nazwę zestawu danych zawierającego nazwę zasobu nadrzędnego. Użyj formatu: `{parent-resource-name}/{child-resource-name}`.

Poniższy przykład przedstawia implementację:

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

## <a name="copy-limits"></a>Limity kopiowania

Liczba nie może przekroczyć 800.

Liczba nie może być liczbą ujemną. Jeśli wdrożysz szablon z programem Azure PowerShell 2.6 lub nowszym, interfejsem wiersza polecenia platformy Azure 2.0.74 lub nowszym lub interfejsem API REST w wersji **2019-05-10** lub nowszej, można ustawić liczbę na zero. Starsze wersje programu PowerShell, interfejsu wiersza polecenia i interfejsu API REST nie obsługują zero dla liczby.

Należy zachować ostrożność podczas [wdrażania trybu pełnego](deployment-modes.md) z kopią. Jeśli ponownie wdrożyć w trybie pełnego do grupy zasobów, wszystkie zasoby, które nie są określone w szablonie po rozwiązaniu pętli kopiowania są usuwane.

## <a name="example-templates"></a>Przykładowe szablony

Poniższe przykłady pokazują typowe scenariusze tworzenia więcej niż jednego wystąpienia zasobu lub właściwości.

|Szablon  |Opis  |
|---------|---------|
|[Kopiowanie magazynu](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Wdraża więcej niż jedno konto magazynu z numerem indeksu w nazwie. |
|[Magazyn kopii szeregowych](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Wdraża kilka kont magazynu jeden na raz. Nazwa zawiera numer indeksu. |
|[Kopiowanie magazynu z macierzą](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Wdraża kilka kont magazynu. Nazwa zawiera wartość z tablicy. |
|[Wdrażanie maszyn wirtualnych ze zmienną liczbą dysków z danymi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Wdraża kilka dysków danych z maszyną wirtualną. |
|[Wiele reguł zabezpieczeń](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Wdraża kilka reguł zabezpieczeń w sieciowej grupie zabezpieczeń. Konstruuje reguły zabezpieczeń z parametru. Dla parametru, zobacz [wiele pliku parametrów NSG](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Następne kroki

* Aby przejść przez samouczek, zobacz [Samouczek: tworzenie wielu wystąpień zasobów przy użyciu szablonów ARM](template-tutorial-create-multiple-instances.md).
* Aby uzyskać inne zastosowania elementu kopiowania, zobacz:
  * [Iteracja właściwości w szablonach ARM](copy-properties.md)
  * [Iteracja zmiennych w szablonach ARM](copy-variables.md)
  * [Iteracja wyjściowa w szablonach ARM](copy-outputs.md)
* Aby uzyskać informacje dotyczące używania kopiowania z szablonami zagnieżdżanym, zobacz [Używanie kopii](linked-templates.md#using-copy).
* Jeśli chcesz dowiedzieć się więcej o sekcjach szablonu, zobacz [Tworzenie szablonów ARM](template-syntax.md).
* Aby dowiedzieć się, jak wdrożyć szablon, zobacz [Wdrażanie aplikacji z szablonem ARM](deploy-powershell.md).

