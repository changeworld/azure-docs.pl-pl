---
title: Zmienne w szablonach Azure Resource Manager
description: Opisuje sposób definiowania zmiennych w szablonie Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: tomfitz
ms.openlocfilehash: 5ed6bb58f2f45de557f2127fdc8abd5cdf2ef965
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384036"
---
# <a name="variables-in-azure-resource-manager-template"></a>Zmienne w Azure Resource Manager szablonie

W tym artykule opisano sposób definiowania i używania zmiennych w szablonie Azure Resource Manager. Do uproszczenia szablonu używane są zmienne. Zamiast powtarzać skomplikowane wyrażenia w całym szablonie, należy zdefiniować zmienną, która zawiera wyrażenie złożone. Następnie należy odwołać się do tej zmiennej w zależności od potrzeb w szablonie.

Menedżer zasobów rozpoznaje zmienne przed rozpoczęciem operacji wdrażania. Wszędzie tam, gdzie zmienna jest używana w szablonie, Menedżer zasobów zastępuje ją rozwiązanymi wartościami.

## <a name="define-variable"></a>Zdefiniuj zmienną

W poniższym przykładzie przedstawiono definicję zmiennej. Tworzy wartość ciągu dla nazwy konta magazynu. Używa kilka funkcji szablonu do uzyskania wartości parametru i łączy ją z unikatowym ciągiem.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Nie można użyć funkcji [Reference](resource-group-template-functions-resource.md#reference) ani żadnej z funkcji [list](resource-group-template-functions-resource.md#list) w sekcji zmienne. Te funkcje uzyskują stan środowiska uruchomieniowego zasobu i nie można ich wykonać przed wdrożeniem, gdy zmienne są rozwiązane.

## <a name="use-variable"></a>Użyj zmiennej

W szablonie należy odwołać się do wartości parametru za pomocą funkcji [zmienne](resource-group-template-functions-deployment.md#variables) . Poniższy przykład pokazuje, jak używać zmiennej dla właściwości zasobu.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="configuration-variables"></a>Zmienne konfiguracyjne

Można zdefiniować zmienne, które przechowują powiązane wartości w celu skonfigurowania środowiska. Zmienna jest definiowana jako obiekt z wartościami. Poniższy przykład pokazuje obiekt, który przechowuje wartości dla dwóch środowisk — **test** i **prod**.

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

W parametrach można utworzyć wartość wskazującą, które wartości konfiguracji mają być używane.

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

Aby pobrać ustawienia dla określonego środowiska, użyj razem zmiennej i parametru.

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="example-templates"></a>Przykładowe szablony

W poniższych przykładach pokazano scenariusze dotyczące używania zmiennych.

|Szablon  |Opis  |
|---------|---------|
| [Definicje zmiennych](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Pokazuje różne typy zmiennych. Szablon nie wdraża żadnych zasobów. Konstruuje wartości zmiennych i zwraca te wartości. |
| [Zmienna konfiguracyjna](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Demonstruje użycie zmiennej, która definiuje wartości konfiguracyjne. Szablon nie wdraża żadnych zasobów. Konstruuje wartości zmiennych i zwraca te wartości. |
| [reguły zabezpieczeń sieciowych](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) i [plik parametrów](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Konstruuje tablicę w prawidłowym formacie do przypisywania reguł zabezpieczeń do sieciowej grupy zabezpieczeń. |

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o dostępnych właściwościach zmiennych, zobacz [Opis struktury i składni szablonów Azure Resource Manager](resource-group-authoring-templates.md).
* Aby zapoznać się z zaleceniami dotyczącymi tworzenia zmiennych, zobacz [najlepsze rozwiązania — zmienne](template-best-practices.md#variables).
