---
title: Zasoby szablonu usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Opis sekcji zasobów szablonów usługi Azure Resource Manager przy użyciu składni deklaratywnej JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2018
ms.author: tomfitz
ms.openlocfilehash: 2f850c25250c59a5fd62964d53b6b9d37ff4cf49
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55491403"
---
# <a name="resources-section-of-azure-resource-manager-templates"></a>Sekcja Zasoby szablonów usługi Azure Resource Manager

W sekcji zasobów można zdefiniować zasoby, które są wdrożone lub aktualizowane. W tej sekcji można uzyskać skomplikowane, ponieważ należy zapoznać się z typami, które wdrażasz podaj odpowiednie wartości.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="available-properties"></a>Dostępne właściwości

Możesz zdefiniować zasoby o następującej strukturze:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "comments": "<your-reference-notes>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Nazwa elementu | Wymagane | Opis |
|:--- |:--- |:--- |
| warunek | Nie | Wartość logiczna wskazująca, czy zasób zostanie udostępniony podczas tego wdrożenia. Gdy `true`, zasób jest tworzony podczas wdrażania. Gdy `false`, zasób jest pomijana dla tego wdrożenia. |
| apiVersion |Yes |Wersja interfejsu API REST na potrzeby tworzenia zasobu. |
| type |Yes |Typ zasobu. Ta wartość jest kombinacją przestrzeń nazw dostawcy zasobów i typu zasobu (takie jak **magazyn.Microsoft/kontamagazynu**). |
| name |Yes |Nazwa zasobu. Musi spełniać ograniczenia składnika identyfikatora URI zdefiniowane w RFC3986. Ponadto usługi platformy Azure, które uwidaczniają nazwę zasobu, aby poza strony zweryfikować nazwę aby upewnić się, że nie jest próba podszywały się pod innego tożsamości. |
| location |Różna |Obsługiwane lokalizacje geograficzne podane zasobu. Można wybrać jedną z dostępnych lokalizacji, ale zazwyczaj warto wybrać taki, który znajduje się w pobliżu użytkowników. Zazwyczaj także warto umieścić zasoby, które współdziałają ze sobą w tym samym regionie. Większość typów zasobów wymaga lokalizacji, ale niektóre typy (takie jak przypisania roli) nie wymagają lokalizacji. |
| tags |Nie |Tagi, które są skojarzone z zasobem. Stosowanie tagów w celu logicznego uporządkowania zasobów w ramach subskrypcji. |
| Komentarze |Nie |Notatki do dokumentowania zasobów w szablonie |
| kopiuj |Nie |Jeśli potrzebna jest więcej niż jedno wystąpienie, liczba zasobów do utworzenia. Domyślnym trybem jest równoległe. Określ tryb serial, gdy nie mają wszystkie lub zasoby w celu wdrożenia w tym samym czasie. Aby uzyskać więcej informacji, zobacz [utworzyć kilka wystąpień zasobów w usłudze Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Nie |Zasoby, które należy wdrożyć przed wdrożeniem tego zasobu. Menedżer zasobów ocenia zależności między zasobami i ich wdrażania w odpowiedniej kolejności. Gdy zasoby nie są zależne od siebie, są one wdrożone równolegle. Wartość może być zasobem listę rozdzielonych przecinkami nazw lub unikatowych identyfikatorów zasobów. Tylko Wyświetla listę zasobów, które są wdrażane w tym szablonie. Zasoby, które nie są zdefiniowane w tym szablonie musi już istnieć. Należy unikać Dodawanie zależności niepotrzebne, jak długo będą powolne wdrożenie i utworzyć zależności cykliczne. Aby uzyskać wskazówki dotyczące Ustawianie zależności, zobacz [Definiowanie zależności w szablonach usługi Azure Resource Manager](resource-group-define-dependencies.md). |
| properties |Nie |Ustawienia konfiguracji specyficznych dla zasobów. Wartości właściwości są takie same jak wartość podana w treści żądania dla operacji interfejsu API REST (metodę PUT) w celu utworzenia zasobu. Można również określić tablicy kopiowania, aby utworzyć kilka wystąpień z właściwością. |
| jednostka SKU | Nie | Niektóre zasoby Zezwalaj na wartości, które definiują jednostki SKU do wdrożenia. Na przykład można określić typu nadmiarowości konta magazynu. |
| rodzaj | Nie | Niektóre zasoby zezwala na wartość, która definiuje typ zasobu, które można wdrożyć. Na przykład można określić typ usługi Cosmos DB do tworzenia. |
| Plan | Nie | Niektóre zasoby Zezwalaj na wartości, które definiują plan do wdrożenia. Na przykład można określić obrazu portalu marketplace dla maszyny wirtualnej. | 
| zasoby |Nie |Zasoby podrzędne, które są zależne od zasobów, w trakcie definiowania. Podaj tylko typy zasobów, które są dozwolone w schemacie zasobu nadrzędnego. W pełni kwalifikowany typ zasobu podrzędnego obejmuje typ zasobu nadrzędnego, takie jak **Microsoft.Web/sites/extensions**. Zależność od zasobu nadrzędnego nie jest implikowane. Musisz jawnie zdefiniować tej zależności. |

## <a name="condition"></a>Warunek

Jeśli podczas wdrażania należy zdecydować, czy należy utworzyć zasób, użyj `condition` elementu. Wartość dla tego elementu jest rozpoznawana jako wartość true lub false. Zasób jest tworzony, gdy ma wartość true. Gdy wartość jest równa false, zasób nie jest tworzony. Wartość można zastosować tylko do całego zasobu.

Zazwyczaj ta wartość służy w sytuacji, gdy chcesz utworzyć nowy zasób lub użyć istniejącego. Na przykład aby określić, czy nowe konto magazynu jest wdrożony lub istniejące konto magazynu jest używane, należy użyć:

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

Dla szablonu kompletny przykład, który używa `condition` elementu, zobacz [maszyny Wirtualnej przy użyciu nowej lub istniejącej sieci wirtualnej, magazynu i publiczny adres IP](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

## <a name="resource-specific-values"></a>Wartości specyficznych dla zasobów

**ApiVersion**, **typu**, i **właściwości** elementy są różne dla każdego typu zasobu. **Jednostki sku**, **rodzaj**, i **plan** elementy są dostępne w przypadku niektórych typów zasobów, ale nie wszystkich. Aby określić wartości tych właściwości, zobacz [odwołanie do szablonu](/azure/templates/).

## <a name="resource-names"></a>Nazwy zasobów

Ogólnie rzecz biorąc pracować z trzy typy nazw zasobów w usłudze Resource Manager:

* Nazwy zasobów, które muszą być unikatowe.
* Nazwy zasobów, które nie muszą być unikatowe, ale możesz zdecydować się na Podaj nazwę, która może pomóc w zidentyfikowaniu zasobu.
* Nazwy zasobów, które mogą być ogólne.

### <a name="unique-resource-names"></a>Unikatowy zasób nazwy

Podaj nazwę zasobu unikatowe dla wszystkich typów zasobów, która ma punkt końcowy dostępu do danych. Niektóre typowe typy zasobów, które wymagają unikatowej nazwy obejmują:

* Azure Storage<sup>1</sup> 
* Funkcje aplikacji internetowych w usłudze Azure App Service
* Oprogramowanie SQL Server
* W usłudze Azure Key Vault
* Azure Cache for Redis
* Azure Batch
* Azure Traffic Manager
* Azure Search
* Azure HDInsight

<sup>1</sup> nazwy kont magazynu muszą być również małymi literami, 24 znaków lub mniej, a nie ma żadnych łączników.

Podczas ustawiania nazwy, można ręcznie utworzyć unikatową nazwę lub użyć [uniqueString()](resource-group-template-functions-string.md#uniquestring) funkcję, aby wygenerować nazwę. Możesz również chcieć dodać prefiks lub sufiks **uniqueString** wynik. Modyfikowanie unikatową nazwę może pomóc Ci łatwo zidentyfikować typ zasobu na podstawie nazwy. Na przykład można wygenerować unikatową nazwę konta magazynu przy użyciu następującą zmienną:

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>Nazwy zasobów w celu identyfikacji
Niektóre typy zasobów, które mają nazwy, ale ich nazwy nie muszą być unikatowe. Dla tych typów zasobów możesz podać nazwę, która identyfikuje kontekst zasobu i typu zasobu.

```json
"parameters": {
    "vmName": { 
        "type": "string",
        "defaultValue": "demoLinuxVM",
        "metadata": {
            "description": "The name of the VM to create."
        }
    }
}
```

### <a name="generic-resource-names"></a>Nazwy zasobów ogólnych
Dla typów zasobów, przede wszystkim dostępu za pomocą innego zasobu można użyć nazwy ogólnej, który jest ustalony w szablonie. Na przykład można ustawić standardowy ogólnych nazwy dla reguł zapory na serwerze SQL server:

```json
{
    "type": "firewallrules",
    "name": "AllowAllWindowsAzureIps",
    ...
}
```

## <a name="location"></a>Lokalizacja
Podczas wdrażania szablonu, należy podać lokalizację każdego zasobu. Różne typy zasobów są obsługiwane w różnych lokalizacjach. Aby wyświetlić listę lokalizacji, które są dostępne dla subskrypcji dla określonego typu zasobów, użyj programu Azure PowerShell lub wiersza polecenia platformy Azure. 

W poniższym przykładzie użyto programu PowerShell można pobrać lokalizacji dla `Microsoft.Web\sites` typ zasobu:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

W poniższym przykładzie użyto interfejsu wiersza polecenia platformy Azure można pobrać lokalizacji dla `Microsoft.Web\sites` typ zasobu:

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

Po ustaleniu obsługiwane lokalizacje dla zasobów, ustawienia tej lokalizacji, w tym szablonie. Ustaw tę wartość najprościej jest utworzyć grupę zasobów w lokalizacji, która obsługuje typy zasobów i wartość w każdej lokalizacji `[resourceGroup().location]`. Ponowne wdrażanie szablonu do grup zasobów w różnych lokalizacjach i nie zmienić żadnych wartości w szablonie lub parametrów. 

Poniższy przykład przedstawia konta magazynu, który jest wdrożony w tej samej lokalizacji co grupa zasobów:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

Jeśli zachodzi potrzeba umieszczaj lokalizację w szablonie, należy podać nazwę jednego z obsługiwanych regionów. Poniższy przykład przedstawia konta magazynu, który zawsze jest wdrożony w północno-środkowe stany USA:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storageloc', uniqueString(resourceGroup().id))]",
      "location": "North Central US",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

## <a name="tags"></a>Tagi
[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

### <a name="add-tags-to-your-template"></a>Dodawanie tagów do szablonu

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="child-resources"></a>Zasoby podrzędne

W niektórych typów zasobów można także zdefiniować tablicę zasoby podrzędne. Zasoby podrzędne są zasoby, które istnieją tylko w kontekście innego zasobu. Na przykład bazę danych SQL nie może istnieć bez programu SQL server, więc baza danych jest elementem podrzędnym serwerze. Można zdefiniować bazy danych w ramach definicji dla serwera.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

Gdy zagnieżdżony, typ jest ustawiona na `databases` , ale jego typ zasobu pełną `Microsoft.Sql/servers/databases`. Nie podano `Microsoft.Sql/servers/` ponieważ zakłada się od typu zasobu nadrzędnego. Nazwa zasobu podrzędnego jest równa `exampledatabase` , ale Pełna nazwa zawiera nazwę nadrzędnej. Nie podano `exampleserver` ponieważ zakłada się, od zasobu nadrzędnego.

Format typu zasobu podrzędnego jest następujący: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

Format nazwy zasobów podrzędnych jest następujący: `{parent-resource-name}/{child-resource-name}`

Ale nie trzeba zdefiniować bazy danych na serwerze. Można zdefiniować zasób podrzędny na najwyższym poziomie. Może być użycie tej metody, jeśli zasób nadrzędny nie jest wdrożony w tym samym szablonie, lub jeśli chcesz użyć `copy` utworzyć więcej niż jednego zasobu podrzędnego. W przypadku tej metody musisz podać typ zasobu pełnego i nazwy zasobów podrzędnych zawierają nazwę zasobu nadrzędnego.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

Podczas tworzenia w pełni kwalifikowane odwołanie do zasobu, w kolejności łączenie segmentów z typu, a nazwa nie jest po prostu składa się z dwóch. Zamiast tego po przestrzeni nazw, należy użyć sekwencji *Nazwatypu/* pary z co najmniej określonych do bardziej konkretny od pozostałych:

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

Na przykład:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` jest poprawna `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` jest nieprawidłowy



## <a name="next-steps"></a>Kolejne kroki
* Aby wyświetlić pełną listę szablonów dla wielu różnych rozwiązań, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/).
* Aby uzyskać szczegółowe informacje na temat funkcji, możesz użyć w szablonie, zobacz [funkcje szablonu usługi Azure Resource Manager](resource-group-template-functions.md).
* Aby uzyskać zalecenia dotyczące tworzenia szablonów, zobacz [najlepszych rozwiązań dla szablonu usługi Azure Resource Manager](template-best-practices.md).
* Może być konieczne użycie zasobów, które istnieją w innej grupie zasobów. Ten scenariusz jest typowy podczas pracy z kontami magazynu lub sieci wirtualne, które są współużytkowane przez wiele grup zasobów. Aby uzyskać więcej informacji, zobacz [funkcja resourceId](resource-group-template-functions-resource.md#resourceid).
* Aby uzyskać informacji na temat ograniczeń nazw zasobów, zobacz [zalecane konwencje nazewnictwa dla zasobów platformy Azure](../guidance/guidance-naming-conventions.md).