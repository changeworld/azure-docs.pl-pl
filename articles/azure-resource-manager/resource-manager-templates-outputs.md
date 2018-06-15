---
title: Generuje szablonu usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Opisuje sposób zdefiniowania danych wyjściowych dla szablonów usługi Azure Resource Manager za pomocą składni deklaratywnej JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2017
ms.author: tomfitz
ms.openlocfilehash: e3c5a581b02f1dd7b7415ebd93de0e425ac2f8ae
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358369"
---
# <a name="outputs-section-in-azure-resource-manager-templates"></a>Sekcja danych wyjściowych w szablonach usługi Azure Resource Manager
W sekcji danych wyjściowych można określić wartości, które są zwracane z wdrożenia. Na przykład można zwrócić identyfikator URI do uzyskania dostępu do zasobu wdrożone.

## <a name="define-and-use-output-values"></a>Definiowanie i korzystanie z wartościami danych wyjściowych

Poniższy przykład przedstawia sposób zwrócenia identyfikator zasobu do publicznego adresu IP:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Po wdrożeniu można pobrać wartość przy użyciu skryptu. W przypadku programu PowerShell użyj polecenia:

```powershell
(Get-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

Można pobrać wartość wyjściowa szablonu połączone za pomocą [odwołania](resource-group-template-functions-resource.md#reference) funkcji. Można pobrać wartości danych wyjściowych z połączonego szablonu, pobrać wartość właściwości składnię: `"[reference('<name-of-deployment>').outputs.<property-name>.value]"`.

Na przykład należy określić adres IP modułu równoważenia obciążenia przy odczytywania wartości z połączonych szablonu.

```json
"publicIPAddress": {
    "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Nie można użyć `reference` funkcji w sekcji danych wyjściowych [szablon zagnieżdżony](resource-group-linked-templates.md#link-or-nest-a-template). Aby zwrócić wartości dla wdrożonych zasobów w szablonie zagnieżdżonych, przekonwertować szablon zagnieżdżony połączonego szablonu.

## <a name="available-properties"></a>Dostępne właściwości

W poniższym przykładzie przedstawiono struktura definicji danych wyjściowych:

```json
"outputs": {
    "<outputName>" : {
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| Nazwa elementu | Wymagane | Opis |
|:--- |:--- |:--- |
| outputName |Yes |Nazwa wartości danych wyjściowych. Musi być prawidłowym identyfikatorem języka JavaScript. |
| type |Yes |Typ wartości danych wyjściowych. Dane wyjściowe wartości obsługuje te same typy tablic jako parametrów wejściowych szablonu. |
| wartość |Yes |Wyrażenia języka szablonu, który jest obliczany i zwracany, jako wartość wyjściowa. |

## <a name="recommendations"></a>Zalecenia

Jeśli szablon umożliwia tworzenie publicznych adresów IP, należy uwzględnić sekcji danych wyjściowych, która zwraca szczegółowe informacje dotyczące adresu IP i w pełni kwalifikowaną nazwę (FQDN). Można użyć wartości danych wyjściowych można łatwo pobrać szczegółowe informacje dotyczące publicznych adresów IP i nazw FQDN po wdrożeniu.

```json
"outputs": {
    "fqdn": {
        "value": "[reference(parameters('publicIPAddresses_name')).dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]",
        "type": "string"
    }
}
```

## <a name="example-templates"></a>Przykład szablonów


|Szablon  |Opis  |
|---------|---------|
|[Skopiuj zmiennych](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Tworzy zmienne złożone i zapisuje te wartości. Nie powoduje wdrożenia żadnych zasobów. |
|[Publiczny adres IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Tworzy publiczny adres IP i danych wyjściowych z identyfikatorem zasobu. |
|[Moduł równoważenia obciążenia](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Łącza do poprzedniego szablonu. Używa Identyfikatora zasobu w danych wyjściowych podczas tworzenia modułu równoważenia obciążenia. |


## <a name="next-steps"></a>Kolejne kroki
* Aby wyświetlić pełną listę szablonów dla wielu różnych rozwiązań, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/).
* Aby uzyskać więcej informacji o funkcje, których można użyć z w ramach szablonu, zobacz [funkcje szablonów usługi Azure Resource Manager](resource-group-template-functions.md).
* Aby połączyć wiele szablonów podczas wdrażania, zobacz [za pomocą szablonów połączonych z usługą Azure Resource Manager](resource-group-linked-templates.md).
* Może być konieczne użycie zasobów, które istnieją w innej grupie zasobów. Ten scenariusz jest typowy podczas pracy z kontami magazynu lub sieci wirtualne, które są współdzielone przez wiele grup zasobów. Aby uzyskać więcej informacji, zobacz [funkcja resourceId](resource-group-template-functions-resource.md#resourceid).