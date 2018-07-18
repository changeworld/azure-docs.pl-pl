---
title: Strukturę szablonu usługi Azure Resource Manager i składnię | Dokumentacja firmy Microsoft
description: W tym artykule opisano, struktury i właściwości szablonów usługi Azure Resource Manager przy użyciu składni deklaratywnej JSON.
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
ms.date: 05/30/2018
ms.author: tomfitz
ms.openlocfilehash: e1964b7f46259e54c65aeb46aa795713922c3504
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114616"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Omówienie struktury i składni szablonów usługi Azure Resource Manager
W tym artykule opisano strukturę szablonu usługi Azure Resource Manager. Przedstawia on różne części szablonu i właściwości, które są dostępne w tych sekcjach. Szablon składa się z kodu JSON i wyrażeń, których można używać do tworzenia wartości na potrzeby wdrożenia. Aby uzyskać samouczek krok po kroku dotyczące tworzenia szablonu, zobacz [Tworzenie pierwszego szablonu usługi Azure Resource Manager](resource-manager-create-first-template.md).

## <a name="template-format"></a>Format szablonu
W swojej najprostszej strukturze szablon zawiera następujące elementy:

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
| $schema |Yes |Lokalizacja pliku schematu JSON, który zawiera opis wersji języka szablonu. Użyj adresu URL w poprzednim przykładzie. |
| contentversion — |Yes |Wersja szablonu (na przykład 1.0.0.0). Możesz podać dowolną wartość dla tego elementu. Użyj tej wartości, aby udokumentować znaczące zmiany w szablonie. Podczas wdrażania zasobów przy użyciu szablonu, ta wartość może służyć do upewnij się, że używany jest odpowiedni szablon. |
| parameters |Nie |Wartości, które znajdują się po wykonaniu wdrożenia do dostosowywania wdrażania zasobów. |
| Zmienne |Nie |Wartości, które są używane jako fragmenty JSON w szablonie, aby uprościć wyrażeń języka szablonu. |
| functions |Nie |Funkcje zdefiniowane przez użytkownika, które są dostępne w ramach szablonu. |
| zasoby |Yes |Typy zasobów, które są wdrożone lub zaktualizowane w grupie zasobów. |
| wyjścia |Nie |Wartości, które są zwracane po wdrożeniu. |

Każdy element ma właściwości, które można ustawić. Poniższy przykład przedstawia pełną składnię dla szablonu:

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

W tym artykule opisano części szablonu bardziej szczegółowo.

## <a name="syntax"></a>Składnia
Podstawowa składnia szablonu jest JSON. Jednak wyrażeń i funkcji rozszerzyć wartości JSON, dostępnych w ramach szablonu.  Wyrażenia są zapisywane w ramach Literały ciągu JSON pierwszego którego i ostatnie znaki są nawiasy: `[` i `]`, odpowiednio. Wartość wyrażenie jest oceniane podczas wdrażania szablonu. Podczas zapisywania jako literał ciągu, wynikiem obliczenia wyrażenia może być innego typu JSON, takich jak tablica lub liczba całkowita, w zależności od rzeczywistego wyrażenia.  Być ciągiem literału, rozpoczynać się w nawiasach `[`, ale nie jest interpretowany jako wyrażenie, Dodaj nawias dodatkowych można uruchomić ciąg z `[[`.

Zazwyczaj należy użyć wyrażeń z funkcji do wykonywania operacji związanych z konfigurowaniem wdrażania. Po prostu, tak jak w języku JavaScript, wywołania funkcji są sformatowane jako `functionName(arg1,arg2,arg3)`. Właściwości odwoływać się za pomocą operatorów [Indeks] i kropka.

Poniższy przykład pokazuje, jak używać kilku funkcji, podczas tworzenia wartości:

```json
"variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
}
```

Aby uzyskać pełną listę funkcji szablonów, zobacz [funkcje szablonu usługi Azure Resource Manager](resource-group-template-functions.md). 

## <a name="parameters"></a>Parametry
W sekcji parametrów szablonu należy określić wartości, które należy wprowadzić podczas wdrażania zasobów. Te wartości parametrów umożliwiają dostosowanie wdrożenia, podając wartości, które są dostosowane do określonego środowiska (na przykład deweloperskim, testowym i produkcyjnym). Nie musisz podać parametry w szablonie, ale bez parametrów szablonu będzie zawsze wdrażać te same zasoby z tej samej nazwy, lokalizacji i właściwości.

Poniższy przykład przedstawia definicją parametru proste:

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

Aby dowiedzieć się, jak Definiowanie parametrów, zobacz [sekcji parametrów szablonów usługi Azure Resource Manager](resource-manager-templates-parameters.md).

## <a name="variables"></a>Zmienne
W sekcji zmiennych konstruujesz wartości, których można użyć w szablonie. Nie trzeba zdefiniować zmienne, ale często upraszczają działania do szablonu, zmniejszając złożonych wyrażeń.

Poniższy przykład przedstawia definicję zmiennej proste:

```json
"variables": {
  "webSiteName": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
},
```

Aby uzyskać informacji na temat definiowania zmiennych, zobacz [sekcji zmiennych szablonów usługi Azure Resource Manager](resource-manager-templates-variables.md).

## <a name="functions"></a>Funkcje

W ramach szablonu można utworzyć własne funkcje. Te funkcje są dostępne do użycia w szablonie. Zazwyczaj można zdefiniować złożone wyrażenie, które nie chcesz powtarzać w szablonie. Tworzyć funkcje zdefiniowane przez użytkownika z wyrażeń i [funkcje](resource-group-template-functions.md) , są obsługiwane w szablonach.

Podczas definiowania funkcji użytkownika, istnieją pewne ograniczenia:

* Funkcja nie może uzyskać dostęp do zmiennych.
* Funkcja nie może uzyskać dostęp do parametrów szablonu. Oznacza to, że [parametrów funkcji](resource-group-template-functions-deployment.md#parameters) jest ograniczony do parametrów funkcji.
* Funkcja nie można wywołać inne funkcje zdefiniowane przez użytkownika.
* Nie można użyć funkcji [odwoływać się do funkcji](resource-group-template-functions-resource.md#reference).
* Parametry funkcji nie może mieć wartości domyślne.

Funkcje wymagają wartości przestrzeni nazw w celu uniknięcia konfliktu z funkcje szablonu nazw. Funkcja, która zwraca nazwę konta magazynu można znaleźć w poniższym przykładzie:

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

Możesz wywołać funkcję za pomocą:

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
W sekcji zasobów można zdefiniować zasoby, które są wdrożone lub aktualizowane. W tej sekcji można uzyskać skomplikowane, ponieważ należy zapoznać się z typami, które wdrażasz podaj odpowiednie wartości.

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

Aby uzyskać więcej informacji, zobacz [sekcji zasobów usługi Azure Resource Manager](resource-manager-templates-resources.md).

## <a name="outputs"></a>Dane wyjściowe
W sekcji danych wyjściowych należy określić wartości, które są zwracane z wdrożenia. Na przykład można zwrócić identyfikator URI do dostępu do wdrożonych zasobów.

```json
"outputs": {
  "newHostName": {
    "type": "string",
    "value": "[reference(variables('webSiteName')).defaultHostName]"
  }
}
```

Aby uzyskać więcej informacji, zobacz [wyprowadza części szablonów usługi Azure Resource Manager](resource-manager-templates-outputs.md).

## <a name="template-limits"></a>Limity szablonu

Limit rozmiaru szablonu do 1 MB, a każdy plik parametrów do 64 KB. Aby stan końcowy szablonu obowiązuje limit 1 MB, po została rozszerzona o definicjach iteracyjne zasobów i wartości dla zmiennych i parametrów. 

Możesz również są ograniczone do:

* Parametry 256
* zmienne 256
* 800 zasoby (w tym liczba kopii)
* wartości wyjściowe 64
* 24 576 znaków w wyrażeniu szablonu

Pewne ograniczenia szablonu może przekroczyć przy użyciu zagnieżdżonych szablonów. Aby uzyskać więcej informacji, zobacz [podczas wdrażania zasobów platformy Azure, za pomocą połączonymi szablonami](resource-group-linked-templates.md). Aby zmniejszyć liczbę parametrów, zmienne ani danych wyjściowych, możesz połączyć kilka wartości do obiektu. Aby uzyskać więcej informacji, zobacz [obiektów jako parametrów](resource-manager-objects-as-parameters.md).

## <a name="next-steps"></a>Kolejne kroki
* Aby wyświetlić pełną listę szablonów dla wielu różnych rozwiązań, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/).
* Aby uzyskać szczegółowe informacje na temat funkcji, możesz użyć w szablonie, zobacz [funkcje szablonu usługi Azure Resource Manager](resource-group-template-functions.md).
* Aby połączyć wiele szablonów podczas wdrażania, zobacz [przy użyciu szablonów połączonych z usługą Azure Resource Manager](resource-group-linked-templates.md).
* Aby uzyskać zalecenia dotyczące tworzenia szablonów usługi Resource Manager, używających globalnej platformy Azure, chmurach suwerennych platformy Azure i usługi Azure Stack, zobacz [szablony Tworzenie usługi Azure Resource Manager w celu zachowania spójności chmury](templates-cloud-consistency.md).
