---
title: Strukturę szablonu usługi Azure Resource Manager i składnię | Dokumentacja firmy Microsoft
description: W tym artykule opisano, struktury i właściwości szablonów usługi Azure Resource Manager przy użyciu składni deklaratywnej JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/09/2019
ms.author: tomfitz
ms.openlocfilehash: 264db79f5c934603004eb595930b44abc622efd5
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2019
ms.locfileid: "59470971"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Omówienie struktury i składni szablonów usługi Azure Resource Manager

W tym artykule opisano strukturę szablonu usługi Azure Resource Manager. Przedstawia on różne części szablonu i właściwości, które są dostępne w tych sekcjach. Szablon składa się z kodu JSON i wyrażeń, których można używać do tworzenia wartości na potrzeby wdrożenia.

Ten artykuł jest przeznaczony dla użytkowników, którzy posiadają pewną znajomość szablonów usługi Resource Manager. Zawiera ona szczegółowe informacje dotyczące struktury i składni szablonu. Jeśli chcesz, aby zapoznać się z wprowadzeniem do tworzenia szablonu, zobacz [Tworzenie pierwszego szablonu usługi Azure Resource Manager](resource-manager-create-first-template.md).

## <a name="template-format"></a>Format szablonu

W swojej najprostszej strukturze szablon zawiera następujące elementy:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| Nazwa elementu | Wymagane | Opis |
|:--- |:--- |:--- |
| $schema |Yes |Lokalizacja pliku schematu JSON, który zawiera opis wersji języka szablonu.<br><br> Dla wdrożenia grupy zasobów użyj polecenia: `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>W przypadku wdrożeń w subskrypcji należy użyć: `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#` |
| contentversion — |Yes |Wersja szablonu (na przykład 1.0.0.0). Możesz podać dowolną wartość dla tego elementu. Użyj tej wartości, aby udokumentować znaczące zmiany w szablonie. Podczas wdrażania zasobów przy użyciu szablonu, ta wartość może służyć do upewnij się, że używany jest odpowiedni szablon. |
| apiProfile |Nie | Wersja interfejsu API, która służy jako zbiór wersje interfejsu API dla typów zasobów. Użyj tej wartości, aby uniknąć konieczności określania wersji interfejsu API dla każdego zasobu w szablonie. Gdy określać wersję interfejsu API w profilu, a nie określona wersja interfejsu API dla typu zasobu, Menedżer zasobów używa wersji interfejsu API dla tego typu zasobu, która jest zdefiniowana w profilu.<br><br>Właściwości profilu interfejsu API jest szczególnie przydatne w przypadku wdrażania szablonu w różnych środowiskach, takich jak Azure Stack i globalnej platformy Azure. Wersja profilu interfejs API umożliwia upewnij się, że Twój szablon automatycznie używa wersji, które są obsługiwane w obu środowiskach. Aby uzyskać listę bieżących wersji interfejsu API w profilu i zasobów, wersje interfejsów API, zdefiniowaną w profilu, zobacz [profilu interfejsu API](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Aby uzyskać więcej informacji, zobacz [śledzenia wersji przy użyciu interfejsu API profilów](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parameters](#parameters) |Nie |Wartości, które znajdują się po wykonaniu wdrożenia do dostosowywania wdrażania zasobów. |
| [Zmienne](#variables) |Nie |Wartości, które są używane jako fragmenty JSON w szablonie, aby uprościć wyrażeń języka szablonu. |
| [functions](#functions) |Nie |Funkcje zdefiniowane przez użytkownika, które są dostępne w ramach szablonu. |
| [zasoby](#resources) |Yes |Typy zasobów, które są wdrożone lub zaktualizowane w grupie zasobów lub subskrypcji. |
| [wyjścia](#outputs) |Nie |Wartości, które są zwracane po wdrożeniu. |

Każdy element ma właściwości, które można ustawić. W tym artykule opisano części szablonu bardziej szczegółowo.

## <a name="syntax"></a>Składnia

Podstawowa składnia szablonu jest JSON. Jednak można użyć wyrażenia rozszerzenie wartości JSON, dostępnych w ramach szablonu.  Wyrażenia pierwszym i ostatnim znakiem nawiasy: `[` i `]`, odpowiednio. Wartość wyrażenie jest oceniane podczas wdrażania szablonu. Wyrażenie może zwrócić ciąg, liczba całkowita, wartość logiczna, tablicy lub obiektu. Poniższy przykład pokazuje wyrażenie wartości domyślnej parametru:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

W wyrażeniu składni `resourceGroup()` wywołuje jedną z funkcji udostępnianych przez Menedżera zasobów do użycia w ramach szablonu. Po prostu, tak jak w języku JavaScript, wywołania funkcji są sformatowane jako `functionName(arg1,arg2,arg3)`. Składnia `.location` pobiera jednej właściwości z obiektu zwróconego przez tę funkcję.

Szablon funkcji i ich parametrów jest rozróżniana wielkość liter. Na przykład usługi Resource Manager rozpoznaje **variables('var1')** i **VARIABLES('VAR1')** taka sama. Podczas oceny, chyba że funkcja modyfikuje wyraźnie przypadek (na przykład toUpper lub toLower), funkcja zachowuje wielkość liter. Niektóre typy zasobów może mieć wymagań dotyczących przypadków niezależnie od tego, jak są obliczane funkcje.

Być ciągiem literału, rozpoczynać się w nawiasach `[`, ale nie jest interpretowany jako wyrażenie, Dodaj nawias dodatkowych można uruchomić ciąg z `[[`.

Aby przekazać wartość ciągu jako parametr do funkcji, należy używać cudzysłowów.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

Jako znak ucieczki dla cudzysłowów w wyrażeniu, takie jak dodanie obiektu JSON w szablonie, należy użyć ukośnik odwrotny.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

Wyrażenie szablonu nie może przekraczać 24 576 znaków.

Aby uzyskać pełną listę funkcji szablonów, zobacz [funkcje szablonu usługi Azure Resource Manager](resource-group-template-functions.md). 

## <a name="parameters"></a>Parametry

W sekcji parametrów szablonu należy określić wartości, które należy wprowadzić podczas wdrażania zasobów. Te wartości parametrów umożliwiają dostosowanie wdrożenia, podając wartości, które są dostosowane do określonego środowiska (na przykład deweloperskim, testowym i produkcyjnym). Nie musisz podać parametry w szablonie, ale bez parametrów szablonu będzie zawsze wdrażać te same zasoby z tej samej nazwy, lokalizacji i właściwości.

Wszystko jest ograniczona do 256 parametrów w szablonie. Aby zmniejszyć liczbę parametrów, należy za pomocą obiektów, które zawierają wiele właściwości, jak pokazano w tym artykule.

### <a name="available-properties"></a>Dostępne właściwości

Dostępne właściwości parametru są:

```json
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
}
```

| Nazwa elementu | Wymagane | Opis |
|:--- |:--- |:--- |
| parameterName |Yes |Nazwa parametru. Musi być prawidłowym identyfikatorem języka JavaScript. |
| type |Yes |Typ wartości parametru. Dozwolone typy i wartości są **ciąg**, **securestring**, **int**, **bool**, **obiektu**, **secureObject**, i **tablicy**. |
| defaultValue |Nie |Wartość domyślna parametru, jeśli podano żadnej wartości dla parametru. |
| allowedValues |Nie |Tablica dozwolonych wartości dla parametru upewnić się, że podano odpowiednie wartości. |
| wartość minValue |Nie |Wartość minimalna dla parametrów typu int, ta wartość jest włącznie. |
| maxValue |Nie |Maksymalna wartość dla parametrów typu int, ta wartość jest włącznie. |
| Element minLength |Nie |Minimalna długość ciągu, bezpieczny ciąg i Tablica parametrów typu, ta wartość jest włącznie. |
| Element maxLength |Nie |Maksymalna długość ciągu, bezpieczny ciąg i Tablica parametrów typu, ta wartość jest włącznie. |
| description |Nie |Opis parametru, który jest widoczny dla użytkowników za pośrednictwem portalu. Aby uzyskać więcej informacji, zobacz [komentarzy w szablonach](#comments). |

### <a name="define-and-use-a-parameter"></a>Definiowanie i korzystanie z parametru

Poniższy przykład pokazuje definicji parametru proste. Określa nazwę parametru i określa, że ma wartość ciągu. Parametr akceptuje tylko wartości, które mają sens dla zamierzonego użycia. Określa wartość domyślną, gdy wartość nie została podana podczas wdrażania. Na koniec ten parametr zawiera opis jego użycia.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }   
}
```

W szablonie możesz odwołać się do wartość parametru przy użyciu następującej składni:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

### <a name="template-functions-with-parameters"></a>Funkcje szablonu z parametrami

Podczas określania wartości domyślnej parametru, można użyć większości funkcji szablonu. Inną wartość parametru można użyć do tworzenia wartości domyślnej. Następujący szablon przedstawia korzystania z funkcji w wartości domyślne:

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

Nie można użyć `reference` funkcji w sekcji parametrów. Parametry są oceniane przed wdrożeniem, więc `reference` funkcji nie można pobrać stanu środowiska uruchomieniowego zasobu. 

### <a name="objects-as-parameters"></a>Obiektów jako parametrów

Może być łatwiejsze do organizowania powiązanych wartości, przekazując je jako obiekt. Takie podejście zmniejsza się liczba parametrów w szablonie.

Zdefiniuj parametr w szablonie, a następnie podaj obiekt JSON zamiast pojedynczej wartości podczas wdrażania. 

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

Następnie odwoływać właściwości podrzędnych parametru za pomocą operatora kropki.

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

### <a name="parameter-example-templates"></a>Parametr przykładowych szablonów

Te przykładowe szablony pokazują niektóre scenariusze użycia parametrów. Wdróż je, aby przetestować sposób obsługi parametrów w różnych scenariuszach.

|Szablon  |Opis  |
|---------|---------|
|[Parametry, z funkcjami dla wartości domyślnych](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Pokazuje, jak używać funkcji szablonu, podczas definiowania wartości domyślne parametrów. Szablon nie wdroży żadnych zasobów. On tworzy wartości parametrów i zwraca te wartości. |
|[Parametr obiektu](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Demonstruje użycie obiektu dla parametru. Szablon nie wdroży żadnych zasobów. On tworzy wartości parametrów i zwraca te wartości. |

## <a name="variables"></a>Zmienne

W sekcji zmiennych konstruujesz wartości, których można użyć w szablonie. Nie trzeba zdefiniować zmienne, ale często upraszczają działania do szablonu, zmniejszając złożonych wyrażeń.

### <a name="available-definitions"></a>Dostępne definicje

Poniższy przykład przedstawia dostępne opcje do definiowania zmiennej:

```json
"variables": {
  "<variable-name>": "<variable-value>",
  "<variable-name>": { 
    <variable-complex-type-value> 
  },
  "<variable-object-name>": {
    "copy": [
      {
        "name": "<name-of-array-property>",
        "count": <number-of-iterations>,
        "input": <object-or-value-to-repeat>
      }
    ]
  },
  "copy": [
    {
      "name": "<variable-array-name>",
      "count": <number-of-iterations>,
      "input": <object-or-value-to-repeat>
    }
  ]
}
```

Aby uzyskać informacje o używaniu `copy` Aby utworzyć kilka wartości zmiennej, zobacz [zmiennej iteracji](resource-group-create-multiple.md#variable-iteration).

### <a name="define-and-use-a-variable"></a>Definiowanie i użyj zmiennej

Poniższy przykład przedstawia definicję zmiennej. Tworzy wartość ciągu dla nazwy konta magazynu. Używa kilka funkcji szablonu, aby uzyskać wartości parametru i łączy ją z unikatowym ciągiem.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Podczas definiowania zasobu jest używana zmienna.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

### <a name="configuration-variables"></a>Zmienne konfiguracji

Typy złożone JSON służy do definiowania powiązanych wartości dla środowiska.

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

W parametrach utworzysz wartość, która wskazuje, która Konfiguracja wartości do użycia.

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

Możesz pobrać bieżące ustawienia za pomocą:

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

### <a name="variable-example-templates"></a>Przykład zmiennej szablonów

Te przykładowe szablony pokazują niektóre scenariusze dotyczące używania zmiennych. Wdróż je do testowania, w jaki sposób zmienne są obsługiwane w różnych scenariuszach. 

|Szablon  |Opis  |
|---------|---------|
| [definicje zmiennych](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Demonstruje różne rodzaje zmiennych. Szablon nie wdroży żadnych zasobów. On tworzy wartości zmiennych i zwraca te wartości. |
| [Zmienna konfiguracyjna](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Zademonstrowano użycie zmiennej, która definiuje wartości konfiguracji. Szablon nie wdroży żadnych zasobów. On tworzy wartości zmiennych i zwraca te wartości. |
| [reguł zabezpieczeń sieciowych](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) i [pliku parametrów](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Tworzy tablicę w poprawnym formacie przypisywania zabezpieczeń reguły do sieciowej grupy zabezpieczeń. |


## <a name="functions"></a>Funkcje

W ramach szablonu można utworzyć własne funkcje. Te funkcje są dostępne do użycia w szablonie. Zazwyczaj można zdefiniować złożone wyrażenie, które nie chcesz powtarzać w szablonie. Tworzyć funkcje zdefiniowane przez użytkownika z wyrażeń i [funkcje](resource-group-template-functions.md) , są obsługiwane w szablonach.

Podczas definiowania funkcji użytkownika, istnieją pewne ograniczenia:

* Funkcja nie może uzyskać dostęp do zmiennych.
* Funkcja może używać tylko parametry, które są zdefiniowane w funkcji. Kiedy używasz [parametrów funkcji](resource-group-template-functions-deployment.md#parameters) w funkcji zdefiniowanej przez użytkownika, są ograniczone do parametrów dla tej funkcji.
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
W sekcji zasobów można zdefiniować zasoby, które są wdrożone lub aktualizowane.

### <a name="available-properties"></a>Dostępne właściwości

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
| warunek | Nie | Wartość logiczna wskazująca, czy zasób zostanie udostępniony podczas tego wdrożenia. Gdy `true`, zasób jest tworzony podczas wdrażania. Gdy `false`, zasób jest pomijana dla tego wdrożenia. Zobacz [warunek](#condition). |
| apiVersion |Yes |Wersja interfejsu API REST na potrzeby tworzenia zasobu. Aby określić dostępne wartości, zobacz [odwołanie do szablonu](/azure/templates/). |
| type |Yes |Typ zasobu. Ta wartość jest kombinacją przestrzeń nazw dostawcy zasobów i typu zasobu (takie jak **magazyn.Microsoft/kontamagazynu**). Aby określić dostępne wartości, zobacz [odwołanie do szablonu](/azure/templates/). |
| name |Yes |Nazwa zasobu. Musi spełniać ograniczenia składnika identyfikatora URI zdefiniowane w RFC3986. Ponadto usługi platformy Azure, które uwidaczniają nazwę zasobu, aby poza strony zweryfikować nazwę aby upewnić się, że nie jest próba podszywały się pod innego tożsamości. |
| location |Różna |Obsługiwane lokalizacje geograficzne podane zasobu. Można wybrać jedną z dostępnych lokalizacji, ale zazwyczaj warto wybrać taki, który znajduje się w pobliżu użytkowników. Zazwyczaj także warto umieścić zasoby, które współdziałają ze sobą w tym samym regionie. Większość typów zasobów wymaga lokalizacji, ale niektóre typy (takie jak przypisania roli) nie wymagają lokalizacji. |
| tags |Nie |Tagi, które są skojarzone z zasobem. Stosowanie tagów w celu logicznego uporządkowania zasobów w ramach subskrypcji. |
| Komentarze |Nie |Notatki do dokumentowania zasobów w szablonie. Aby uzyskać więcej informacji, zobacz [komentarzy w szablonach](resource-group-authoring-templates.md#comments). |
| kopiuj |Nie |Jeśli potrzebna jest więcej niż jedno wystąpienie, liczba zasobów do utworzenia. Domyślnym trybem jest równoległe. Określ tryb serial, gdy nie mają wszystkie lub zasoby w celu wdrożenia w tym samym czasie. Aby uzyskać więcej informacji, zobacz [utworzyć kilka wystąpień zasobów w usłudze Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Nie |Zasoby, które należy wdrożyć przed wdrożeniem tego zasobu. Menedżer zasobów ocenia zależności między zasobami i ich wdrażania w odpowiedniej kolejności. Gdy zasoby nie są zależne od siebie, są one wdrożone równolegle. Wartość może być zasobem listę rozdzielonych przecinkami nazw lub unikatowych identyfikatorów zasobów. Tylko Wyświetla listę zasobów, które są wdrażane w tym szablonie. Zasoby, które nie są zdefiniowane w tym szablonie musi już istnieć. Należy unikać Dodawanie zależności niepotrzebne, jak długo będą powolne wdrożenie i utworzyć zależności cykliczne. Aby uzyskać wskazówki dotyczące Ustawianie zależności, zobacz [Definiowanie zależności w szablonach usługi Azure Resource Manager](resource-group-define-dependencies.md). |
| properties |Nie |Ustawienia konfiguracji specyficznych dla zasobów. Wartości właściwości są takie same jak wartość podana w treści żądania dla operacji interfejsu API REST (metodę PUT) w celu utworzenia zasobu. Można również określić tablicy kopiowania, aby utworzyć kilka wystąpień z właściwością. Aby określić dostępne wartości, zobacz [odwołanie do szablonu](/azure/templates/). |
| sku | Nie | Niektóre zasoby Zezwalaj na wartości, które definiują jednostki SKU do wdrożenia. Na przykład można określić typu nadmiarowości konta magazynu. |
| rodzaj | Nie | Niektóre zasoby zezwala na wartość, która definiuje typ zasobu, które można wdrożyć. Na przykład można określić typ usługi Cosmos DB do tworzenia. |
| Plan | Nie | Niektóre zasoby Zezwalaj na wartości, które definiują plan do wdrożenia. Na przykład można określić obrazu portalu marketplace dla maszyny wirtualnej. | 
| zasoby |Nie |Zasoby podrzędne, które są zależne od zasobów, w trakcie definiowania. Podaj tylko typy zasobów, które są dozwolone w schemacie zasobu nadrzędnego. W pełni kwalifikowany typ zasobu podrzędnego obejmuje typ zasobu nadrzędnego, takie jak **Microsoft.Web/sites/extensions**. Zależność od zasobu nadrzędnego nie jest implikowane. Musisz jawnie zdefiniować tej zależności. |

### <a name="condition"></a>Warunek

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

Jeśli używasz [odwołania](resource-group-template-functions-resource.md#reference) lub [listy](resource-group-template-functions-resource.md#list) z zasobem warunkowo wdrożeniu funkcji szacowania funkcji będzie nawet, jeśli zasób nie jest wdrożona. Wystąpi błąd, jeśli funkcja odwołuje się do zasobu, który nie istnieje. Użyj [Jeśli](resource-group-template-functions-logical.md#if) funkcję, aby upewnić się, czy funkcja jest oceniane tylko warunki, gdy zasób jest wdrażany. Zobacz [Jeśli funkcja](resource-group-template-functions-logical.md#if) przykładowego szablonu, który używa, jeśli i odwołania z warunkowo wdrożony zasób.

### <a name="resource-names"></a>Nazwy zasobów

Ogólnie rzecz biorąc pracować z trzy typy nazw zasobów w usłudze Resource Manager:

* Nazwy zasobów, które muszą być unikatowe.
* Nazwy zasobów, które nie muszą być unikatowe, ale możesz zdecydować się na Podaj nazwę, która może pomóc w zidentyfikowaniu zasobu.
* Nazwy zasobów, które mogą być ogólne.

Podaj **unikatowy zasób Nazwa** dla wszystkich typów zasobów, która ma punkt końcowy dostępu do danych. Niektóre typowe typy zasobów, które wymagają unikatowej nazwy obejmują:

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

Dla niektórych typów zasobów, możesz chcieć zapewnić **Nazwa do identyfikacji**, ale nazwa nie musi być unikatowa. Dla tych typów zasobów Podaj nazwę opisującą jego użycia lub właściwości.

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

Dla zasobu typów, które przede wszystkim dostępu za pomocą innego zasobu, można użyć **nazwę rodzajową** jest zakodowane w szablonie. Na przykład można ustawić standardowy ogólnych nazwy dla reguł zapory na serwerze SQL server:

```json
{
  "type": "firewallrules",
  "name": "AllowAllWindowsAzureIps",
  ...
}
```

### <a name="resource-location"></a>Lokalizacja zasobu

Podczas wdrażania szablonu, należy podać lokalizację każdego zasobu. Różne typy zasobów są obsługiwane w różnych lokalizacjach. Obsługiwane lokalizacje dla typu zasobu, można znaleźć [dostawcy zasobów platformy Azure i ich typy](resource-manager-supported-services.md).

Użyj parametru, aby określić lokalizację dla zasobów, a wartość domyślna równa `resourceGroup().location`.

Poniższy przykład przedstawia konta magazynu, który jest wdrożony w miejscu określonym jako parametr:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('storage', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

### <a name="child-resources"></a>Zasoby podrzędne

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

## <a name="outputs"></a>Dane wyjściowe

W sekcji danych wyjściowych należy określić wartości, które są zwracane z wdrożenia. Zwykle zwracane wartości od zasobów, które zostały wdrożone.

### <a name="available-properties"></a>Dostępne właściwości

Poniższy przykład pokazuje strukturę definicję danych wyjściowych:

```json
"outputs": {
  "<outputName>" : {
    "condition": "<boolean-value-whether-to-output-value>",
    "type" : "<type-of-output-value>",
    "value": "<output-value-expression>"
  }
}
```

| Nazwa elementu | Wymagane | Opis |
|:--- |:--- |:--- |
| outputName |Yes |Nazwa wartości danych wyjściowych. Musi być prawidłowym identyfikatorem języka JavaScript. |
| warunek |Nie | Wartość logiczna wskazująca, czy to danych wyjściowych wartość jest zwracana. Gdy `true`, wartość jest uwzględniona w danych wyjściowych dla wdrożenia. Gdy `false`, wartość wyjściowa jest pomijana dla tego wdrożenia. Jeśli nie zostanie określony, wartością domyślną jest `true`. |
| type |Yes |Typ wartości danych wyjściowych. Wartości wyjściowe obsługują te same typy jako parametrów wejściowych szablonu. |
| wartość |Yes |Wyrażenie języka szablonu, który jest obliczany i zwracany, jako wartość danych wyjściowych. |

### <a name="define-and-use-output-values"></a>Definiowanie i korzystanie z wartości danych wyjściowych

Poniższy przykład pokazuje, jak zwraca identyfikator zasobu dla publicznego adresu IP:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

W kolejnym przykładzie pokazano sposób zwracania warunkowo, czy jest to nowy, jeden został wdrożony na podstawie Identyfikatora zasobu, opłata za publiczny adres IP:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Dla prostego przykładu warunkowych danych wyjściowych, zobacz [szablonu warunkowych danych wyjściowych](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

Po wdrożeniu można pobrać wartość za pomocą skryptu. W przypadku programu PowerShell użyj polecenia:

```powershell
(Get-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

Możesz pobrać wartość danych wyjściowych z dołączonego szablonu, za pomocą [odwołania](resource-group-template-functions-resource.md#reference) funkcji. Aby uzyskać wartość wyjściową dołączonego szablonu, pobrać wartość właściwości przy użyciu składni, takich jak: `"[reference('deploymentName').outputs.propertyName.value]"`.

Podczas pobierania właściwości danych wyjściowych z dołączonego szablonu, nazwy właściwości nie może zawierać kreskę.

Poniższy przykład pokazuje, jak skonfigurować adres IP modułu równoważenia obciążenia, poprzez pobranie wartości z połączonych szablonu.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Nie można użyć `reference` funkcji w danych wyjściowych części [zagnieżdżonych szablonów](resource-group-linked-templates.md#link-or-nest-a-template). Aby zwrócić wartości dla zasobów wdrożonych w zagnieżdżonych szablonów, należy przekonwertować zagnieżdżony szablon do dołączonego szablonu.

### <a name="output-example-templates"></a>Dane wyjściowe przykładowych szablonów

|Szablon  |Opis  |
|---------|---------|
|[Skopiuj zmienne](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Tworzy zmienne złożone i wysyła te wartości. Nie należy wdrażać żadnych zasobów. |
|[Publiczny adres IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Tworzy publiczny adres IP, a następnie generuje identyfikator zasobu. |
|[Moduł równoważenia obciążenia](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Zawiera łącza do Powyższy szablon. Używa Identyfikatora zasobu w danych wyjściowych, podczas tworzenia modułu równoważenia obciążenia. |


<a id="comments" />

## <a name="comments-and-metadata"></a>Komentarze i metadane

Masz kilka opcji dodawania komentarzy i metadane szablonu.

Możesz dodać `metadata` obiektu praktycznie dowolnym miejscu w szablonie. Menedżer zasobów ignoruje obiektu, ale edytora JSON może ostrzega użytkownika, że właściwość jest nieprawidłowa. W obiekcie Zdefiniuj właściwości, których potrzebujesz.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

Aby uzyskać **parametry**, Dodaj `metadata` obiekt z `description` właściwości.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

Podczas wdrażania szablonu za pośrednictwem portalu, tekst, który należy podać w opisie automatycznie służy jako wskazówka dla tego parametru.

![Pokaż podpowiedź do parametru](./media/resource-group-authoring-templates/show-parameter-tip.png)

Aby uzyskać **zasobów**, Dodaj `comments` elementu lub obiektu metadanych. Poniższy przykład pokazuje element komentarze i obiektu metadanych.

```json
"resources": [
  {
    "comments": "Storage account used to store VM disks",
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

Aby uzyskać **generuje**, dodanie obiektu metadanych do wartości danych wyjściowych.

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
    "metadata": {
      "comments": "Return the fully qualified domain name"
    }
  },
```

Nie można dodać obiektu metadanych funkcje zdefiniowane przez użytkownika.

Komentarze w tekście, można użyć `//` , ale ta składnia nie działa w przypadku wszystkich narzędzi. Nie można użyć wiersza polecenia platformy Azure, aby wdrożyć szablon przy użyciu komentarze w tekście. Ponadto edytor szablonów portalu nie można użyć do pracy nad szablonów za pomocą komentarze w tekście. Jeśli dodasz ten styl komentarza, upewnij się, narzędzia, których używasz komentarze JSON w tekście pomocy technicznej.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  "dependsOn": [ // storage account and network interface must be deployed first
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

W programie VS Code można ustawić tryb języka na format JSON za pomocą komentarzy. Komentarze w tekście są już oznaczone jako nieprawidłowe. Aby zmienić tryb:

1. Otwórz zaznaczenie tryb języka (Ctrl + K M)

1. Wybierz **JSON z komentarzami**.

   ![Wybierz tryb języka](./media/resource-group-authoring-templates/select-json-comments.png)

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>Kolejne kroki
* Aby wyświetlić pełną listę szablonów dla wielu różnych rozwiązań, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/).
* Aby uzyskać szczegółowe informacje na temat funkcji, możesz użyć w szablonie, zobacz [funkcje szablonu usługi Azure Resource Manager](resource-group-template-functions.md).
* Aby połączyć kilka szablonów, podczas wdrażania, zobacz [przy użyciu szablonów połączonych z usługą Azure Resource Manager](resource-group-linked-templates.md).
* Aby uzyskać zalecenia dotyczące tworzenia szablonów, zobacz [najlepszych rozwiązań dla szablonu usługi Azure Resource Manager](template-best-practices.md).
* Aby uzyskać zalecenia dotyczące tworzenia szablonów usługi Resource Manager, korzystające ze wszystkimi środowiskami platformy Azure i usługi Azure Stack, zobacz [szablony Tworzenie usługi Azure Resource Manager w celu zachowania spójności chmury](templates-cloud-consistency.md).
