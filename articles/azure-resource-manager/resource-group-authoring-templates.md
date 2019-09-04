---
title: Struktura i składnia szablonu Azure Resource Manager | Microsoft Docs
description: Opisuje strukturę i właściwości szablonów Azure Resource Manager przy użyciu deklaracyjnej składni JSON.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: tomfitz
ms.openlocfilehash: 53b2f9783b33c859ca2c5de5f35353b8482ea5c7
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70275138"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Zrozumienie struktury i składni szablonów Azure Resource Manager

W tym artykule opisano strukturę szablonu Azure Resource Manager. Przedstawia różne sekcje szablonu i właściwości, które są dostępne w tych sekcjach. Szablon składa się z notacji JSON i wyrażeń, których można użyć do konstruowania wartości dla wdrożenia.

Ten artykuł jest przeznaczony dla użytkowników, którzy mają pewną wiedzę z szablonami Menedżer zasobów. Zawiera szczegółowe informacje na temat struktury i składni szablonu. Jeśli chcesz utworzyć szablon, zobacz [Tworzenie pierwszego szablonu Azure Resource Manager](resource-manager-create-first-template.md).

## <a name="template-format"></a>Format szablonu

W najprostszym strukturze szablon zawiera następujące elementy:

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
| $schema |Tak |Lokalizacja pliku schematu JSON opisującego wersję języka szablonu.<br><br> W przypadku wdrożeń grup zasobów Użyj:`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>W przypadku wdrożeń subskrypcji Użyj:`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#` |
| contentVersion |Tak |Wersja szablonu (na przykład 1.0.0.0). Możesz podać dowolną wartość dla tego elementu. Użyj tej wartości, aby udokumentować znaczące zmiany w szablonie. W przypadku wdrażania zasobów przy użyciu szablonu Ta wartość może być używana do upewnienia się, że odpowiedni szablon jest używany. |
| apiProfile |Nie | Wersja interfejsu API, która służy jako kolekcja wersji interfejsu API dla typów zasobów. Użyj tej wartości, aby uniknąć konieczności określania wersji interfejsu API dla każdego zasobu w szablonie. W przypadku określenia wersji profilu interfejsu API i nieokreślenia wersji interfejsu API dla typu zasobu Menedżer zasobów używa wersji interfejsu API dla tego typu zasobu, który jest zdefiniowany w profilu.<br><br>Właściwość profil interfejsu API jest szczególnie przydatna w przypadku wdrażania szablonu w różnych środowiskach, takich jak Azure Stack i globalny platformę Azure. Użyj wersji profilu interfejsu API, aby upewnić się, że szablon automatycznie używa wersji, które są obsługiwane w obu środowiskach. Listę bieżących wersji profilu interfejsu API i wersje interfejsu API zasobów zdefiniowane w profilu znajdują się w temacie [profil interfejsu API](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Aby uzyskać więcej informacji, zobacz [śledzenie wersji przy użyciu profilów interfejsu API](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parameters](#parameters) |Nie |Wartości, które są dostarczane, gdy wdrożenie jest wykonywane w celu dostosowania wdrożenia zasobów. |
| [variables](#variables) |Nie |Wartości, które są używane jako fragmenty JSON w szablonie, aby uprościć wyrażenia języka szablonów. |
| [functions](#functions) |Nie |Funkcje zdefiniowane przez użytkownika, które są dostępne w ramach szablonu. |
| [resources](#resources) |Tak |Typy zasobów wdrożone lub zaktualizowane w grupie zasobów lub subskrypcji. |
| [outputs](#outputs) |Nie |Wartości, które są zwracane po wdrożeniu. |

Każdy element ma właściwości, które można ustawić. W tym artykule opisano bardziej szczegółowe sekcje szablonu.

## <a name="parameters"></a>Parametry

W sekcji Parametry szablonu należy określić, które wartości można wprowadzać podczas wdrażania zasobów. Te wartości parametrów umożliwiają dostosowanie wdrożenia przez zapewnienie wartości, które są dostosowane do określonego środowiska (na przykład deweloperskich, testowych i produkcyjnych). Nie musisz podawać parametrów w szablonie, ale bez parametrów szablon zawsze wdraża te same zasoby z tymi samymi nazwami, lokalizacjami i właściwościami.

W szablonie jest ograniczona do 256 parametrów. Możesz zmniejszyć liczbę parametrów za pomocą obiektów, które zawierają wiele właściwości, jak pokazano w tym artykule.

### <a name="available-properties"></a>Dostępne właściwości

Dostępne właściwości dla parametru to:

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
| parameterName |Tak |Nazwa parametru. Musi być prawidłowym identyfikatorem języka JavaScript. |
| type |Tak |Typ wartości parametru. Dozwolone typy i wartości to **String**, **SecureString**, **int**, **bool**, **Object**, **secureobject**i **Array**. |
| defaultValue |Nie |Wartość domyślna parametru, jeśli nie podano wartości dla parametru. |
| allowedValues |Nie |Tablica dozwolonych wartości parametru, aby upewnić się, że podano odpowiednią wartość. |
| minValue |Nie |Minimalna wartość parametrów typu int, ta wartość jest dopuszczalna. |
| maxValue |Nie |Maksymalna wartość parametrów typu int, ta wartość jest dopuszczalna. |
| minLength |Nie |Minimalna długość dla parametrów typu String, Secure String i Array, ta wartość jest włącznie. |
| maxLength |Nie |Maksymalna długość parametrów ciągu, bezpiecznego ciągu i typu tablicy, ta wartość jest włącznie. |
| description |Nie |Opis parametru, który jest wyświetlany użytkownikom w portalu. Aby uzyskać więcej informacji, zobacz [Komentarze w szablonach](#comments). |

### <a name="define-and-use-a-parameter"></a>Definiowanie i używanie parametru

Poniższy przykład pokazuje prostą definicję parametru. Definiuje nazwę parametru i określa, że pobiera wartość ciągu. Parametr akceptuje tylko wartości, które mają sens dla zamierzonego użycia. Określa wartość domyślną, gdy podczas wdrażania nie podano żadnej wartości. Na koniec parametr zawiera opis użycia.

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

W szablonie należy odwołać się do wartości parametru o następującej składni:

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

Podczas określania wartości domyślnej dla parametru, można użyć większości funkcji szablonu. Możesz użyć innej wartości parametru do skompilowania wartości domyślnej. Poniższy szablon demonstruje użycie funkcji w wartości domyślnej:

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

Nie można użyć `reference` funkcji w sekcji Parameters. Parametry są oceniane przed wdrożeniem, `reference` aby funkcja nie mogła pobrać stanu środowiska uruchomieniowego zasobu. 

### <a name="objects-as-parameters"></a>Obiekty jako parametry

Można łatwiej organizować powiązane wartości, przekazując je w postaci obiektu. Takie podejście zmniejsza również liczbę parametrów w szablonie.

Zdefiniuj parametr w szablonie i Określ obiekt JSON zamiast pojedynczej wartości podczas wdrażania. 

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

Następnie należy odwołać się do podwłaściwości parametru przy użyciu operatora kropki.

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

### <a name="parameter-example-templates"></a>Przykładowe szablony parametrów

Te przykładowe szablony przedstawiają niektóre scenariusze używania parametrów. Wdróż je w celu przetestowania sposobu obsługi parametrów w różnych scenariuszach.

|Szablon  |Opis  |
|---------|---------|
|[parametry z funkcjami dla wartości domyślnych](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Pokazuje, jak używać funkcji szablonu podczas definiowania wartości domyślnych dla parametrów. Szablon nie wdraża żadnych zasobów. Konstruuje wartości parametrów i zwraca te wartości. |
|[Parameter — obiekt](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Demonstruje użycie obiektu dla parametru. Szablon nie wdraża żadnych zasobów. Konstruuje wartości parametrów i zwraca te wartości. |

## <a name="variables"></a>Zmienne

W sekcji zmienne można skonstruować wartości, które mogą być używane w całym szablonie. Nie musisz definiować zmiennych, ale często upraszczamy szablon przez zredukowanie złożonych wyrażeń.

### <a name="available-definitions"></a>Dostępne definicje

W poniższym przykładzie przedstawiono dostępne opcje definiowania zmiennej:

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

Aby uzyskać informacje o `copy` używaniu do tworzenia kilku wartości dla zmiennej, zobacz [zmienna iteracja](resource-group-create-multiple.md#variable-iteration).

### <a name="define-and-use-a-variable"></a>Definiowanie i używanie zmiennej

W poniższym przykładzie przedstawiono definicję zmiennej. Tworzy wartość ciągu dla nazwy konta magazynu. Używa kilka funkcji szablonu do uzyskania wartości parametru i łączy ją z unikatowym ciągiem.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Zmienna jest używana podczas definiowania zasobu.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

### <a name="configuration-variables"></a>Zmienne konfiguracyjne

Za pomocą złożonych typów JSON można definiować powiązane wartości dla środowiska.

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

Bieżące ustawienia są pobierane z:

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

### <a name="variable-example-templates"></a>Przykładowe szablony zmiennych

Te przykładowe szablony przedstawiają niektóre scenariusze używania zmiennych. Wdróż je w celu przetestowania sposobu obsługi zmiennych w różnych scenariuszach. 

|Szablon  |Opis  |
|---------|---------|
| [Definicje zmiennych](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Pokazuje różne typy zmiennych. Szablon nie wdraża żadnych zasobów. Konstruuje wartości zmiennych i zwraca te wartości. |
| [Zmienna konfiguracyjna](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Demonstruje użycie zmiennej, która definiuje wartości konfiguracyjne. Szablon nie wdraża żadnych zasobów. Konstruuje wartości zmiennych i zwraca te wartości. |
| [reguły zabezpieczeń sieciowych](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) i [plik parametrów](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Konstruuje tablicę w prawidłowym formacie do przypisywania reguł zabezpieczeń do sieciowej grupy zabezpieczeń. |


## <a name="functions"></a>Funkcje

W ramach szablonu można tworzyć własne funkcje. Te funkcje są dostępne do użycia w szablonie. Zwykle definiuje się wyrażenie złożone, które nie ma być powtarzane w całym szablonie. Funkcje zdefiniowane przez użytkownika można utworzyć na podstawie wyrażeń i [funkcji](resource-group-template-functions.md) obsługiwanych w szablonach.

Podczas definiowania funkcji użytkownika istnieją pewne ograniczenia:

* Funkcja nie może uzyskać dostępu do zmiennych.
* Funkcja może używać tylko parametrów, które są zdefiniowane w funkcji. Gdy używana jest [Funkcja Parameters](resource-group-template-functions-deployment.md#parameters) w funkcji zdefiniowanej przez użytkownika, są ograniczone do parametrów tej funkcji.
* Funkcja nie może wywoływać innych funkcji zdefiniowanych przez użytkownika.
* Funkcja nie może używać [funkcji Reference](resource-group-template-functions-resource.md#reference).
* Parametry funkcji nie mogą mieć wartości domyślnych.

Funkcje wymagają wartości przestrzeni nazw, aby uniknąć konfliktów nazw z funkcjami szablonu. W poniższym przykładzie pokazano funkcję zwracającą nazwę konta magazynu:

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

Wywoływanie funkcji z:

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
W sekcji Resources (zasoby) zdefiniujesz wdrożone lub zaktualizowane zasoby.

### <a name="available-properties"></a>Dostępne właściwości

Należy zdefiniować zasoby o następującej strukturze:

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
| condition | Nie | Wartość logiczna wskazująca, czy zasób zostanie zainicjowany podczas tego wdrożenia. Gdy `true`zasób jest tworzony podczas wdrażania. Gdy `false`zasób jest pomijany dla tego wdrożenia. Zobacz [wdrażanie warunkowe](conditional-resource-deployment.md). |
| apiVersion |Tak |Wersja interfejsu API REST do użycia podczas tworzenia zasobu. Aby określić dostępne wartości, zobacz [Dokumentacja szablonu](/azure/templates/). |
| type |Tak |Typ zasobu. Ta wartość jest kombinacją przestrzeni nazw dostawcy zasobów i typu zasobu (np **. Microsoft. Storage/storageAccounts**). Aby określić dostępne wartości, zobacz [Dokumentacja szablonu](/azure/templates/). W przypadku zasobu podrzędnego format typu zależy od tego, czy jest on zagnieżdżony w obrębie zasobu nadrzędnego, czy zdefiniowany poza zasobem nadrzędnym. Zobacz [Set Name i Type dla zasobów podrzędnych](child-resource-name-type.md). |
| name |Tak |Nazwa zasobu. Nazwa musi następować zgodnie z ograniczeniami składnika URI zdefiniowanymi w RFC3986. Ponadto usługi platformy Azure, które uwidaczniają nazwę zasobu podmiotom zewnętrznym, sprawdzają poprawność nazwy, aby upewnić się, że nie jest próbą sfałszowania innej tożsamości. W przypadku zasobu podrzędnego format nazwy zależy od tego, czy jest on zagnieżdżony w obrębie zasobu nadrzędnego, czy zdefiniowany poza zasobem nadrzędnym. Zobacz [Set Name i Type dla zasobów podrzędnych](child-resource-name-type.md). |
| location |Różna |Obsługiwane lokalizacje geograficzne podanego zasobu. Można wybrać dowolną z dostępnych lokalizacji, ale zazwyczaj warto ją wybrać blisko użytkowników. Zwykle warto również umieścić zasoby, które współpracują ze sobą w tym samym regionie. Większość typów zasobów wymaga lokalizacji, ale niektóre typy (takie jak przypisanie roli) nie wymagają lokalizacji. Zobacz [Ustawianie lokalizacji zasobu](resource-location.md) |
| tags |Nie |Tagi, które są skojarzone z zasobem. Zastosuj Tagi, aby logicznie organizować zasoby w ramach subskrypcji. |
| komentarz |Nie |Twoje notatki umożliwiające dokumentowanie zasobów w szablonie. Aby uzyskać więcej informacji, zobacz [Komentarze w szablonach](resource-group-authoring-templates.md#comments). |
| kopiuj |Nie |Jeśli potrzebujesz więcej niż jednego wystąpienia, liczba zasobów do utworzenia. Domyślny tryb jest równoległy. Określ tryb seryjny, gdy nie chcesz, aby wszystkie lub zasoby zostały wdrożone w tym samym czasie. Aby uzyskać więcej informacji, zobacz [Tworzenie kilku wystąpień zasobów w Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Nie |Zasoby, które muszą zostać wdrożone przed wdrożeniem tego zasobu. Menedżer zasobów oblicza zależności między zasobami i wdraża je w odpowiedniej kolejności. Gdy zasoby nie są od siebie zależne, są wdrażane równolegle. Wartość może być rozdzielaną przecinkami listą nazw zasobów lub unikatowych identyfikatorów zasobów. Tylko zasoby, które są wdrożone w tym szablonie. Zasoby, które nie są zdefiniowane w tym szablonie, muszą już istnieć. Należy unikać dodawania niepotrzebnych zależności, ponieważ mogą one spowalniać wdrożenie i tworzyć zależności cykliczne. Aby uzyskać wskazówki dotyczące ustawiania zależności, zobacz [Definiowanie zależności w szablonach Azure Resource Manager](resource-group-define-dependencies.md). |
| properties |Nie |Ustawienia konfiguracji dotyczące zasobów. Wartości właściwości są takie same jak wartości podane w treści żądania dla operacji interfejsu API REST (Metoda PUT), aby utworzyć zasób. Możesz również określić tablicę kopiowania, aby utworzyć kilka wystąpień właściwości. Aby określić dostępne wartości, zobacz [Dokumentacja szablonu](/azure/templates/). |
| sku | Nie | Niektóre zasoby umożliwiają wartości, które definiują jednostkę SKU do wdrożenia. Na przykład można określić typ nadmiarowości dla konta magazynu. |
| Natur | Nie | Niektóre zasoby umożliwiają wartości, która definiuje typ wdrażanego zasobu. Na przykład można określić typ Cosmos DB, który ma zostać utworzony. |
| zamierza | Nie | Niektóre zasoby umożliwiają wartości definiujące plan do wdrożenia. Na przykład można określić obraz z witryny Marketplace dla maszyny wirtualnej. | 
| zasoby |Nie |Zasoby podrzędne, które są zależne od definiowanego zasobu. Podaj tylko typy zasobów, które są dozwolone przez schemat zasobu nadrzędnego. Nie jest implikowana zależność od zasobu nadrzędnego. Należy jawnie zdefiniować tę zależność. Zobacz [Set Name i Type dla zasobów podrzędnych](child-resource-name-type.md). |

### <a name="resource-names"></a>Nazwy zasobów

Ogólnie rzecz biorąc, pracujesz z trzema typami nazw zasobów w Menedżer zasobów:

* Nazwy zasobów, które muszą być unikatowe.
* Nazwy zasobów, które nie muszą być unikatowe, ale wybierasz nazwę, która może ułatwić identyfikację zasobu.
* Nazwy zasobów, które mogą być ogólne.

Podaj **unikatową nazwę zasobu** dla dowolnego typu zasobu, który ma punkt końcowy dostępu do danych. Niektóre popularne typy zasobów, które wymagają unikatowej nazwy, obejmują:

* Azure Storage<sup>1</sup> 
* Funkcje aplikacji internetowych w usłudze Azure App Service
* SQL Server
* W usłudze Azure Key Vault
* Azure Cache for Redis
* Azure Batch
* Azure Traffic Manager
* Azure Search
* Azure HDInsight

<sup>1</sup> nazwy kont magazynu muszą również zawierać małe litery, 24 znaki i nie zawierać żadnych łączników.

Podczas ustawiania nazwy można ręcznie utworzyć unikatową nazwę lub użyć funkcji [uniqueString ()](resource-group-template-functions-string.md#uniquestring) w celu wygenerowania nazwy. Możesz również dodać prefiks lub sufiks do wyniku **uniqueString** . Modyfikowanie unikatowej nazwy może ułatwić łatwą identyfikację typu zasobu na podstawie nazwy. Na przykład można wygenerować unikatową nazwę konta magazynu za pomocą następującej zmiennej:

```json
"variables": {
  "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

W przypadku niektórych typów zasobów można podać **nazwę do identyfikacji**, ale nazwa nie musi być unikatowa. W przypadku tych typów zasobów Podaj nazwę opisującą jego użycie lub charakterystykę.

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

W przypadku typów zasobów, do których użytkownik uzyskuje dostęp przez inny zasób, można użyć **nazwy ogólnej** , która jest zakodowana w szablonie. Na przykład można ustawić standardową nazwę generyczną dla reguł zapory na serwerze SQL:

```json
{
  "type": "firewallrules",
  "name": "AllowAllWindowsAzureIps",
  ...
}
```

## <a name="outputs"></a>outputs

W sekcji danych wyjściowych należy określić wartości, które są zwracane z wdrożenia. Zazwyczaj zwracane są wartości z zasobów, które zostały wdrożone.

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
| condition |Nie | Wartość logiczna wskazująca, czy ta wartość wyjściowa jest zwracana. Gdy `true`wartość jest uwzględniona w danych wyjściowych dla wdrożenia. Gdy `false`wartość wyjściowa jest pomijana dla tego wdrożenia. Gdy nie zostanie określony, wartość domyślna to `true`. |
| type |Yes |Typ wartości danych wyjściowych. Wartości wyjściowe obsługują te same typy jako parametrów wejściowych szablonu. W przypadku określenia elementu **SecureString** dla typu danych wyjściowych wartość nie jest wyświetlana w historii wdrożenia i nie można jej pobrać z innego szablonu. Aby użyć wartości klucza tajnego w więcej niż jednym szablonie, należy zapisać klucz tajny w Key Vault i odwołać się do wpisu tajnego w pliku parametrów. Aby uzyskać więcej informacji, zobacz [używanie Azure Key Vault do przekazywania zabezpieczonej wartości parametrów podczas wdrażania](resource-manager-keyvault-parameter.md). |
| value |Yes |Wyrażenie języka szablonu, który jest obliczany i zwracany, jako wartość danych wyjściowych. |

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

W następnym przykładzie pokazano, jak warunkowo zwrócić identyfikator zasobu dla publicznego adresu IP na podstawie tego, czy został wdrożony nowy:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Aby uzyskać prosty przykład danych wyjściowych warunkowych, zobacz [warunkowy szablon danych wyjściowych](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

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

Poniższy przykład pokazuje, jak ustawić adres IP dla modułu równoważenia obciążenia przez pobranie wartości z połączonego szablonu.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Nie można użyć `reference` funkcji w danych wyjściowych części [zagnieżdżonych szablonów](resource-group-linked-templates.md#link-or-nest-a-template). Aby zwrócić wartości dla zasobów wdrożonych w zagnieżdżonych szablonów, należy przekonwertować zagnieżdżony szablon do dołączonego szablonu.

### <a name="output-example-templates"></a>Przykładowe szablony danych wyjściowych

|Szablon  |Opis  |
|---------|---------|
|[Skopiuj zmienne](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Tworzy zmienne złożone i wysyła te wartości. Nie należy wdrażać żadnych zasobów. |
|[Publiczny adres IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Tworzy publiczny adres IP, a następnie generuje identyfikator zasobu. |
|[Moduł równoważenia obciążenia](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Zawiera łącza do Powyższy szablon. Używa Identyfikatora zasobu w danych wyjściowych, podczas tworzenia modułu równoważenia obciążenia. |


<a id="comments" />

## <a name="comments-and-metadata"></a>Komentarze i metadane

Istnieje kilka opcji dodawania komentarzy i metadanych do szablonu.

`metadata` Obiekt można dodać niemal w dowolnym miejscu w szablonie. Menedżer zasobów ignoruje obiekt, ale Edytor JSON może ostrzec, że właściwość jest nieprawidłowa. W obiekcie Zdefiniuj potrzebne właściwości.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

Dla **parametrów**Dodaj `metadata` obiekt z `description` właściwością.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

Podczas wdrażania szablonu za pomocą portalu, tekst w opisie jest automatycznie używany jako Porada dla tego parametru.

![Pokaż poradę dotyczącą parametrów](./media/resource-group-authoring-templates/show-parameter-tip.png)

W przypadku **zasobów**Dodaj `comments` element lub obiekt metadanych. W poniższym przykładzie pokazano zarówno element komentarzy, jak i obiekt metadanych.

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

W **przypadku danych wyjściowych**Dodaj obiekt metadanych do wartości wyjściowej.

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

Nie można dodać obiektu metadanych do funkcji zdefiniowanych przez użytkownika.

W przypadku komentarzy wbudowanych można użyć `//` , ale ta składnia nie działa z wszystkimi narzędziami. Nie można użyć interfejsu wiersza polecenia platformy Azure, aby wdrożyć szablon z wbudowanymi komentarzami. Nie można używać edytora szablonów portalu do pracy nad szablonami z wbudowanymi komentarzami. W przypadku dodania tego stylu komentarza upewnij się, że narzędzia, z których korzystasz, obsługują wbudowane Komentarze JSON.

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

W VS Code można ustawić tryb języka na JSON z komentarzami. Komentarze w tekście nie są już oznaczone jako nieprawidłowe. Aby zmienić tryb:

1. Otwórz zaznaczenie trybu języka (Ctrl + K M)

1. Wybierz pozycję **JSON z komentarzami**.

   ![Wybierz tryb języka](./media/resource-group-authoring-templates/select-json-comments.png)

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>Następne kroki
* Aby wyświetlić pełną listę szablonów dla wielu różnych rozwiązań, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/).
* Aby uzyskać szczegółowe informacje na temat funkcji, możesz użyć w szablonie, zobacz [funkcje szablonu usługi Azure Resource Manager](resource-group-template-functions.md).
* Aby połączyć kilka szablonów podczas wdrażania, zobacz [Używanie połączonych szablonów z Azure Resource Manager](resource-group-linked-templates.md).
* Zalecenia dotyczące tworzenia szablonów można znaleźć w temacie [Azure Resource Manager Best Practices Template](template-best-practices.md).
* Aby zapoznać się z zaleceniami dotyczącymi tworzenia Menedżer zasobów szablonów, których można używać we wszystkich środowiskach platformy Azure i Azure Stack, zobacz [opracowywanie szablonów Azure Resource Manager dla spójności chmury](templates-cloud-consistency.md).
