---
title: Sekcji parametrów szablonu w usłudze Azure Resource Manager | Dokumentacja firmy Microsoft
description: W tym artykule opisano w sekcji parametrów szablonów usługi Azure Resource Manager przy użyciu składni deklaratywnej JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2019
ms.author: tomfitz
ms.openlocfilehash: dc817302ab39d12ccd1d1a20d4dd72f94352c796
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2019
ms.locfileid: "55695674"
---
# <a name="parameters-section-of-azure-resource-manager-templates"></a>Sekcji parametrów szablonów usługi Azure Resource Manager
W sekcji parametrów szablonu należy określić wartości, które należy wprowadzić podczas wdrażania zasobów. Te wartości parametrów umożliwiają dostosowanie wdrożenia, podając wartości, które są dostosowane do określonego środowiska (na przykład deweloperskim, testowym i produkcyjnym). Nie musisz podać parametry w szablonie, ale bez parametrów szablonu będzie zawsze wdrażać te same zasoby z tej samej nazwy, lokalizacji i właściwości.

Wszystko jest ograniczona do 256 parametrów w szablonie. Aby zmniejszyć liczbę parametrów, należy za pomocą obiektów, które zawierają wiele właściwości, jak pokazano w tym artykule.

## <a name="define-and-use-a-parameter"></a>Definiowanie i korzystanie z parametru

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

## <a name="available-properties"></a>Dostępne właściwości

Poprzedni przykład wykazało, że tylko niektóre właściwości, które można używać w sekcji parametrów. Są dostępne właściwości:

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
| description |Nie |Opis parametru, który jest widoczny dla użytkowników za pośrednictwem portalu. Aby uzyskać więcej informacji, zobacz [komentarzy w szablonach](resource-group-authoring-templates.md#comments). |

## <a name="template-functions-with-parameters"></a>Funkcje szablonu z parametrami

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

## <a name="objects-as-parameters"></a>Obiektów jako parametrów

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

## <a name="example-templates"></a>Przykładowe szablony

Te przykładowe szablony pokazują niektóre scenariusze użycia parametrów. Wdróż je, aby przetestować sposób obsługi parametrów w różnych scenariuszach.

|Szablon  |Opis  |
|---------|---------|
|[Parametry, z funkcjami dla wartości domyślnych](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Pokazuje, jak używać funkcji szablonu, podczas definiowania wartości domyślne parametrów. Szablon nie wdroży żadnych zasobów. On tworzy wartości parametrów i zwraca te wartości. |
|[Parametr obiektu](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Demonstruje użycie obiektu dla parametru. Szablon nie wdroży żadnych zasobów. On tworzy wartości parametrów i zwraca te wartości. |

## <a name="next-steps"></a>Kolejne kroki

* Aby wyświetlić pełną listę szablonów dla wielu różnych rozwiązań, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/).
* Jak wprowadzanie wartości parametrów podczas wdrażania, zobacz [wdrażania aplikacji przy użyciu szablonu usługi Azure Resource Manager](resource-group-template-deploy.md). 
* Aby uzyskać zalecenia dotyczące tworzenia szablonów, zobacz [najlepszych rozwiązań dla szablonu usługi Azure Resource Manager](template-best-practices.md).
* Aby dowiedzieć się, jak za pomocą obiektu parametrów, zobacz [używanie obiektu jako parametr w szablonie usługi Azure Resource Manager](/azure/architecture/building-blocks/extending-templates/objects-as-parameters).
