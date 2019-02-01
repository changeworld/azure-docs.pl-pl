---
title: Generuje szablon usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób definiowania danych wyjściowych dla szablonów usługi Azure Resource Manager za pomocą składni deklaratywnej JSON.
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
ms.date: 12/18/2018
ms.author: tomfitz
ms.openlocfilehash: 29181b19498b6735651869b6499c4a1cda5a4c3a
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55488858"
---
# <a name="outputs-section-in-azure-resource-manager-templates"></a>Sekcję danych wyjściowych w szablonach usługi Azure Resource Manager

W sekcji danych wyjściowych należy określić wartości, które są zwracane z wdrożenia. Na przykład można zwrócić identyfikator URI do dostępu do wdrożonych zasobów.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="define-and-use-output-values"></a>Definiowanie i korzystanie z wartości danych wyjściowych

Poniższy przykład pokazuje, jak zwraca identyfikator zasobu dla publicznego adresu IP:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

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

Na przykład można ustawić adresu IP modułu równoważenia obciążenia, poprzez pobranie wartości z połączonych szablonu.

```json
"publicIPAddress": {
    "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Nie można użyć `reference` funkcji w danych wyjściowych części [zagnieżdżonych szablonów](resource-group-linked-templates.md#link-or-nest-a-template). Aby zwrócić wartości dla zasobów wdrożonych w zagnieżdżonych szablonów, należy przekonwertować zagnieżdżony szablon do dołączonego szablonu.

## <a name="available-properties"></a>Dostępne właściwości

Poniższy przykład pokazuje strukturę definicję danych wyjściowych:

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
| type |Yes |Typ wartości danych wyjściowych. Wartości wyjściowe obsługują te same typy jako parametrów wejściowych szablonu. |
| wartość |Yes |Wyrażenie języka szablonu, który jest obliczany i zwracany, jako wartość danych wyjściowych. |


## <a name="example-templates"></a>Przykładowe szablony

|Szablon  |Opis  |
|---------|---------|
|[Skopiuj zmienne](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Tworzy zmienne złożone i wysyła te wartości. Nie należy wdrażać żadnych zasobów. |
|[Publiczny adres IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Tworzy publiczny adres IP, a następnie generuje identyfikator zasobu. |
|[Moduł równoważenia obciążenia](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Zawiera łącza do Powyższy szablon. Używa Identyfikatora zasobu w danych wyjściowych, podczas tworzenia modułu równoważenia obciążenia. |


## <a name="next-steps"></a>Kolejne kroki
* Aby wyświetlić pełną listę szablonów dla wielu różnych rozwiązań, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/).
* Aby uzyskać szczegółowe informacje na temat funkcji, możesz użyć w szablonie, zobacz [funkcje szablonu usługi Azure Resource Manager](resource-group-template-functions.md).
* Aby uzyskać zalecenia dotyczące tworzenia szablonów, zobacz [najlepszych rozwiązań dla szablonu usługi Azure Resource Manager](template-best-practices.md).
