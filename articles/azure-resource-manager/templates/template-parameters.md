---
title: Parametry w szablonach
description: W tym artykule opisano sposób definiowania parametrów w szablonie usługi Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 89c6984c587e8dae59c1825a99d4f8da1c06dafb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122427"
---
# <a name="parameters-in-azure-resource-manager-templates"></a>Parametry w szablonach usługi Azure Resource Manager

W tym artykule opisano sposób definiowania i używania parametrów w szablonie usługi Azure Resource Manager. Podając różne wartości parametrów, można ponownie użyć szablonu dla różnych środowisk.

Menedżer zasobów rozpoznaje wartości parametrów przed rozpoczęciem operacji wdrażania. Wszędzie tam, gdzie parametr jest używany w szablonie, Menedżer zasobów zastępuje go wartością rozwiązaną.

## <a name="define-parameter"></a>Definiuj parametr

W poniższym przykładzie przedstawiono prostą definicję parametru. Definiuje parametr o nazwie **storageSKU**. Parametr jest wartością ciągu i akceptuje tylko wartości, które są prawidłowe dla jego zamierzonego zastosowania. Parametr używa wartości domyślnej, gdy podczas wdrażania nie podano żadnej wartości.

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

## <a name="use-parameter"></a>Użyj parametru

W szablonie odwołujesz się do wartości parametru za pomocą funkcji [parametrów.](template-functions-deployment.md#parameters) W poniższym przykładzie wartość parametru jest używana do ustawiania jednostki SKU dla konta magazynu.

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

## <a name="template-functions"></a>Funkcje szablonów

Określając wartość domyślną parametru, można użyć większości funkcji szablonu. Można użyć innej wartości parametru, aby utworzyć wartość domyślną. Poniższy szablon pokazuje użycie funkcji w wartości domyślnej. Gdy nie podano nazwy witryny, tworzy unikatową wartość ciągu i dołącza ją do **witryny**. Jeśli nie podano nazwy planu hosta, przyjmuje wartość dla lokacji i dołącza **-plan**.

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

Nie można użyć funkcji [odwołania](template-functions-resource.md#reference) ani żadnej z funkcji [listy](template-functions-resource.md#list) w sekcji parametry. Te funkcje uzyskać stan środowiska wykonawczego zasobu i nie można wykonać przed wdrożeniem, gdy parametry są rozpoznawane.

## <a name="objects-as-parameters"></a>Obiekty jako parametry

Może być łatwiejsze do organizowania powiązanych wartości, przekazując je jako obiekt. Takie podejście zmniejsza również liczbę parametrów w szablonie.

Poniższy przykład pokazuje parametr, który jest obiektem. Wartość domyślna pokazuje oczekiwane właściwości obiektu.

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

Odwołujesz się do właściwości obiektu przy użyciu operatora kropki.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2015-06-15",
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

Poniższe przykłady pokazują scenariusze przy użyciu parametrów.

|Szablon  |Opis  |
|---------|---------|
|[parametry z funkcjami dla wartości domyślnych](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Pokazuje, jak używać funkcji szablonu podczas definiowania wartości domyślnych parametrów. Szablon nie wdraża żadnych zasobów. Konstruuje wartości parametrów i zwraca te wartości. |
|[obiekt parametru](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Demonstruje przy użyciu obiektu dla parametru. Szablon nie wdraża żadnych zasobów. Konstruuje wartości parametrów i zwraca te wartości. |


## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o dostępnych właściwościach parametrów, zobacz [Opis struktury i składni szablonów usługi Azure Resource Manager](template-syntax.md).
* Aby dowiedzieć się więcej o przekazywaniu wartości parametrów jako pliku, zobacz [Tworzenie pliku parametrów Menedżera zasobów](parameter-files.md).
* Aby uzyskać zalecenia dotyczące tworzenia parametrów, zobacz [Najważniejsze wskazówki - parametry](template-best-practices.md#parameters).
