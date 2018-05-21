---
title: Strukturę szablonu usługi Azure Resource Manager i składni | Dokumentacja firmy Microsoft
description: Opis struktury i właściwości szablonów usługi Azure Resource Manager za pomocą składni deklaratywnej JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2018
ms.author: tomfitz
ms.openlocfilehash: 4fa610f144277b73bb6d555d46e63a01c413e07e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Struktura i składni szablonów usługi Azure Resource Manager
W tym artykule opisano strukturę szablonu usługi Azure Resource Manager. Stanowi różne sekcje szablonu i właściwości, które są dostępne w tych sekcjach. Szablon składa się z kodu JSON i wyrażeń, które służy do tworzenia wartości na potrzeby wdrożenia. Samouczek krok po kroku dotyczące tworzenia szablonu, zobacz [Tworzenie pierwszego szablonu usługi Azure Resource Manager](resource-manager-create-first-template.md).

## <a name="template-format"></a>Format szablonu
W swojej najprostszej strukturze szablonu zawiera następujące elementy:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  },
    "variables": {  },
    "functions": {  },
    "resources": [  ],
    "outputs": {  }
}
```

| Nazwa elementu | Wymagane | Opis |
|:--- |:--- |:--- |
| $schema |Yes |Lokalizacja pliku schematu JSON, który zawiera opis wersji języka szablonu. Użyj adres URL wyświetlany w poprzednim przykładzie. |
| contentVersion |Yes |Wersja szablonu (na przykład 1.0.0.0). Musisz podać wartości dla tego elementu. Podczas wdrażania zasobów przy użyciu szablonu, ta wartość może służyć do upewnij się, że używany jest odpowiedniego szablonu. |
| parameters |Nie |Wartości, które są podane podczas wdrażania jest wykonywany w celu dostosowania wdrożenia zasobów. |
| zmienne |Nie |Wartości, które są używane jako fragmenty JSON w szablonie, aby uprościć wyrażeń języka szablonu. |
| functions |Nie |Funkcje zdefiniowane przez użytkownika, które są dostępne w ramach szablonu. |
| zasoby |Yes |Typy zasobów, które są wdrożone lub zaktualizowane w grupie zasobów. |
| wyjścia |Nie |Wartości, które są zwracane po wdrożeniu. |

Każdy element zawiera właściwości, które można ustawić. Poniżej przedstawiono przykład zawierający pełnej składni szablonu:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  
        "<parameter-name>" : {
            "type" : "<type-of-parameter-value>",
            "defaultValue": "<default-value-of-parameter>",
            "allowedValues": [ "<array-of-allowed-values>" ],
            "minValue": <minimum-value-for-int>,
            "maxValue": <maximum-value-for-int>,
            "minLength": <minimum-length-for-string-or-array>,
            "maxLength": <maximum-length-for-string-or-array-parameters>,
            "metadata": {
                "description": "<description-of-the parameter>" 
            }
        }
    },
    "variables": {
        "<variable-name>": "<variable-value>",
        "<variable-object-name>": {
            <variable-complex-type-value>
        },
        "<variable-object-name>": {
            "copy": [
                {
                    "name": "<name-of-array-property>",
                    "count": <number-of-iterations>,
                    "input": {
                        <properties-to-repeat>
                    }
                }
            ]
        },
        "copy": [
            {
                "name": "<variable-array-name>",
                "count": <number-of-iterations>,
                "input": {
                    <properties-to-repeat>
                }
            }
        ]
    },
    "functions": [
      {
        "namespace": "<namespace-for-your-function>",
        "members": {
          "<function-name>": {
            "parameters": [
              {
                "name": "<parameter-name>",
                "type": "<type-of-parameter-value>"
              }
            ],
            "output": {
              "type": "<type-of-output-value>",
              "value": "<function-expression>"
            }
          }
        }
      }
    ],
    "resources": [
      {
          "condition": "<boolean-value-whether-to-deploy>",
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
              "count": "<number-of-iterations>",
              "mode": "<serial-or-parallel>",
              "batchSize": "<number-to-deploy-serially>"
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
          "resources": [
              "<array-of-child-resources>"
          ]
      }
    ],
    "outputs": {
        "<outputName>" : {
            "type" : "<type-of-output-value>",
            "value": "<output-value-expression>"
        }
    }
}
```

W tym artykule opisano części szablonu większej liczby szczegółów.

## <a name="syntax"></a>Składnia
Podstawowa składnia szablonu jest JSON. Jednak wyrażeń i funkcji rozszerzyć dostępnych w szablonie wartości JSON.  Wyrażenia są zapisywane w literałach ciągu JSON, których pierwszy i ostatnie znaki są nawiasy: `[` i `]`odpowiednio. Wartość wyrażenia jest oceniane podczas wdrażania szablonu. Podczas zapisywania jako literału ciągu, wynik obliczania wyrażenia może być innego typu JSON, takich jak tablicy lub liczba całkowita, w zależności od rzeczywistej wyrażenia.  Mieć literałem rozpoczynać nawiasu `[`, ale nie została ona interpretowana jako wyrażenie, Dodaj dodatkowe nawiasu zacząć ciąg z `[[`.

Zazwyczaj umożliwia wyrażenia funkcji wykonywać operacje związane z konfigurowaniem wdrażania. Po prostu, tak jak w języku JavaScript, wywołania funkcji są sformatowane jako `functionName(arg1,arg2,arg3)`. Możesz odwoływać się do właściwości przy użyciu operatorów kropka i [Indeks].

Poniższy przykład przedstawia użycie kilku funkcji podczas tworzenia wartości:

```json
"variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
}
```

Aby uzyskać pełną listę funkcji szablonu, zobacz [funkcje szablonu usługi Azure Resource Manager](resource-group-template-functions.md). 

## <a name="parameters"></a>Parametry
W sekcji Parametry szablonu można określić wartości, które można wprowadzić podczas wdrażania zasobów. Wartości tych parametrów umożliwiają dostosowanie wdrożenie, podając wartości, które są dostosowane określonym środowisku (na przykład deweloperów, testowego i produkcyjnego). Nie musisz podać parametry w szablonie, ale bez parametrów szablonu będzie zawsze wdrażać te same zasoby z tej samej nazwy, lokalizacji i właściwości.

W poniższym przykładzie przedstawiono definicję parametru prosty:

```json
"parameters": {
  "siteNamePrefix": {
    "type": "string",
    "metadata": {
      "description": "The name prefix of the web app that you wish to create."
    }
  },
},
```

Aby uzyskać informacji na temat definiowania parametrów, zobacz [sekcji parametrów szablonów usługi Azure Resource Manager](resource-manager-templates-parameters.md).

## <a name="variables"></a>Zmienne
W sekcji variables można skonstruować wartości, które mogą być używane w szablonie. Nie trzeba zdefiniować zmienne, ale one często uprościć szablonu zmniejszając złożonych wyrażeń.

W poniższym przykładzie przedstawiono prosty definicji zmiennej:

```json
"variables": {
  "webSiteName": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
},
```

Aby uzyskać informacji na temat definiowania zmiennych, zobacz [sekcji zmiennych szablonów usługi Azure Resource Manager](resource-manager-templates-variables.md).

## <a name="functions"></a>Funkcje

W szablonie można tworzyć własnych funkcji. Te funkcje są dostępne do użycia w szablonie. Zazwyczaj należy zdefiniować złożone wyrażenie, które nie chcesz powtarzać w szablonie. Tworzenie funkcji zdefiniowanej przez użytkownika z wyrażeń i [funkcje](resource-group-template-functions.md) obsługiwane w szablonach.

Podczas definiowania funkcji użytkownika, istnieją pewne ograniczenia:

* Funkcja nie może uzyskać dostępu do zmiennych.
* Nie można użyć funkcji [odwołania funkcja](resource-group-template-functions-resource.md#reference).
* Parametrów funkcji nie może mieć wartości domyślne.

Funkcji wymagają wartości przestrzeni nazw, aby uniknąć konfliktu nazw z szablonu funkcji. Poniższy przykład przedstawia funkcję, która zwraca nazwę konta magazynu:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

Wywołania funkcji z:

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="resources"></a>Zasoby
W sekcji zasobów można zdefiniować zasoby, które są wdrożone lub aktualizowane. W tej sekcji można uzyskać skomplikowane, ponieważ należy zrozumieć typy, które jest wdrażany w celu zapewnienia właściwych wartości.

```json
"resources": [
  {
    "apiVersion": "2016-08-01",
    "name": "[variables('webSiteName')]",
    "type": "Microsoft.Web/sites",
    "location": "[resourceGroup().location]",
    "properties": {
      "serverFarmId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Web/serverFarms/<plan-name>"
    }
  }
],
```

Aby uzyskać więcej informacji, zobacz [sekcja zasobów szablonów usługi Azure Resource Manager](resource-manager-templates-resources.md).

## <a name="outputs"></a>Dane wyjściowe
W sekcji danych wyjściowych można określić wartości, które są zwracane z wdrożenia. Na przykład można zwrócić identyfikator URI do uzyskania dostępu do zasobu wdrożone.

```json
"outputs": {
  "newHostName": {
    "type": "string",
    "value": "[reference(variables('webSiteName')).defaultHostName]"
  }
}
```

Aby uzyskać więcej informacji, zobacz [danych wyjściowych sekcji szablonów usługi Azure Resource Manager](resource-manager-templates-outputs.md).

## <a name="template-limits"></a>Limity szablonu

Limit rozmiaru szablon 1 MB, a każdy plik parametrów do 64 KB. Limit 1 MB jest stosowana do stanu końcowego szablonu zostanie rozwinięty definicje interakcyjnych zasobów i wartości zmiennych i parametrów. 

Możesz również są ograniczone do:

* Parametry 256
* zmienne 256
* 800 zasoby (w tym liczba kopii)
* 64 wartości danych wyjściowych
* 24 576 znaków w wyrażeniu szablonu

Niektóre limity szablonu może przekroczyć przy użyciu szablonu zagnieżdżonego. Aby uzyskać więcej informacji, zobacz [przy użyciu szablonów połączonych w przypadku wdrażania zasobów Azure](resource-group-linked-templates.md). Aby zmniejszyć liczbę parametrów, zmiennych lub dane wyjściowe, można połączyć kilka wartości do obiektu. Aby uzyskać więcej informacji, zobacz [obiektów jako parametry](resource-manager-objects-as-parameters.md).

## <a name="next-steps"></a>Kolejne kroki
* Aby wyświetlić pełną listę szablonów dla wielu różnych rozwiązań, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/).
* Aby uzyskać więcej informacji o funkcje, których można użyć z w ramach szablonu, zobacz [funkcje szablonów usługi Azure Resource Manager](resource-group-template-functions.md).
* Aby połączyć wiele szablonów podczas wdrażania, zobacz [za pomocą szablonów połączonych z usługą Azure Resource Manager](resource-group-linked-templates.md).
* Może być konieczne użycie zasobów, które istnieją w innej grupie zasobów. Ten scenariusz jest typowy podczas pracy z kontami magazynu lub sieci wirtualne, które są współdzielone przez wiele grup zasobów. Aby uzyskać więcej informacji, zobacz [funkcja resourceId](resource-group-template-functions-resource.md#resourceid).
