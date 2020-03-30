---
title: Zmienne w szablonach
description: W tym artykule opisano sposób definiowania zmiennych w szablonie usługi Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: cf135959d30702ea58b7a1d4fdd82625a39245d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483819"
---
# <a name="variables-in-azure-resource-manager-template"></a>Zmienne w szablonie usługi Azure Resource Manager

W tym artykule opisano sposób definiowania i używania zmiennych w szablonie usługi Azure Resource Manager. Aby uprościć szablon, używasz zmiennych. Zamiast powtarzać skomplikowane wyrażenia w całym szablonie, należy zdefiniować zmienną zawierającą skomplikowane wyrażenie. Następnie odwołujesz się do tej zmiennej w razie potrzeby w całym szablonie.

Menedżer zasobów rozpoznaje zmienne przed rozpoczęciem operacji wdrażania. Wszędzie tam, gdzie zmienna jest używana w szablonie, Menedżer zasobów zastępuje ją wartością rozwiązaną.

## <a name="define-variable"></a>Zdefiniuj zmienną

W poniższym przykładzie przedstawiono definicję zmiennej. Tworzy wartość ciągu dla nazwy konta magazynu. Używa kilku funkcji szablonu, aby uzyskać wartość parametru i łączy ją z unikatowym ciągiem.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Nie można użyć funkcji [odwołania](template-functions-resource.md#reference) ani żadnej z funkcji [listy](template-functions-resource.md#list) w sekcji zmiennych. Te funkcje uzyskać stan środowiska wykonawczego zasobu i nie można wykonać przed wdrożeniem, gdy zmienne są rozpoznawane.

## <a name="use-variable"></a>Użyj zmiennej

W szablonie odwołujesz się do wartości parametru przy użyciu funkcji [zmiennych.](template-functions-deployment.md#variables) W poniższym przykładzie pokazano, jak używać zmiennej dla właściwości zasobu.

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

Można zdefiniować zmienne, które przechowują powiązane wartości do konfigurowania środowiska. Zmienną definiuje się jako obiekt z wartościami. W poniższym przykładzie pokazano obiekt, który przechowuje wartości dla dwóch środowisk - **test** i **prod**.

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

W parametrach należy utworzyć wartość wskazującą, których wartości konfiguracji użyć.

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

Aby pobrać ustawienia dla określonego środowiska, należy użyć zmiennej i parametru razem.

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="example-templates"></a>Przykładowe szablony

Poniższe przykłady pokazują scenariusze przy użyciu zmiennych.

|Szablon  |Opis  |
|---------|---------|
| [definicje zmiennych](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Pokazuje różne typy zmiennych. Szablon nie wdraża żadnych zasobów. Konstruuje wartości zmiennych i zwraca te wartości. |
| [zmienna konfiguracja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Demonstruje użycie zmiennej definiującej wartości konfiguracyjne. Szablon nie wdraża żadnych zasobów. Konstruuje wartości zmiennych i zwraca te wartości. |
| [reguły zabezpieczeń sieci](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) i [plik parametrów](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Konstruuje tablicę w odpowiednim formacie do przypisywania reguł zabezpieczeń do sieciowej grupy zabezpieczeń. |

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o dostępnych właściwościach zmiennych, zobacz [Opis struktury i składni szablonów usługi Azure Resource Manager](template-syntax.md).
* Aby uzyskać zalecenia dotyczące tworzenia zmiennych, zobacz [Najlepsze rozwiązania - zmienne](template-best-practices.md#variables).
