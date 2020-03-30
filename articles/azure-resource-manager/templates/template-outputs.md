---
title: Dane wyjściowe w szablonach
description: W tym artykule opisano sposób definiowania wartości danych wyjściowych w szablonie usługi Azure Resource Manager.
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 203bfc66e9515ef14a5fe1315ef5b9ee07075041
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460028"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Dane wyjściowe w szablonie usługi Azure Resource Manager

W tym artykule opisano sposób definiowania wartości danych wyjściowych w szablonie usługi Azure Resource Manager. Dane wyjściowe są używane, gdy trzeba zwrócić wartości z wdrożonych zasobów.

## <a name="define-output-values"></a>Definiowanie wartości wyjściowych

W poniższym przykładzie pokazano, jak zwrócić identyfikator zasobu dla publicznego adresu IP:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

## <a name="conditional-output"></a>Wyjście warunkowe

W sekcji dane wyjściowe można warunkowo zwrócić wartość. Zazwyczaj warunek jest używany w danych wyjściowych, gdy [już warunkowo wdrożony](conditional-resource-deployment.md) zasób. W poniższym przykładzie pokazano, jak warunkowo zwrócić identyfikator zasobu dla publicznego adresu IP na podstawie tego, czy nowy został wdrożony:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Prosty przykład danych wyjściowych warunkowych można znaleźć w [szablonie danych wyjściowych warunkowych](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

## <a name="dynamic-number-of-outputs"></a>Dynamiczna liczba wyjść

W niektórych scenariuszach nie znasz liczby wystąpień wartości, które należy zwrócić podczas tworzenia szablonu. Można zwrócić zmienną liczbę wartości przy użyciu elementu **kopiowania.**

```json
"outputs": {
  "storageEndpoints": {
    "type": "array",
    "copy": {
      "count": "[parameters('storageCount')]",
      "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
    }
  }
}
```

Aby uzyskać więcej informacji, zobacz [Iteracja danych wyjściowych w szablonach usługi Azure Resource Manager](copy-outputs.md).

## <a name="linked-templates"></a>Połączone szablony

Aby pobrać wartość danych wyjściowych z połączonego szablonu, użyj funkcji [odwołania](template-functions-resource.md#reference) w szablonie nadrzędnym. Składnia szablonu nadrzędnego jest:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

Podczas uzyskiwania właściwości wyjściowej z połączonego szablonu nazwa właściwości nie może zawierać kreski.

W poniższym przykładzie pokazano, jak ustawić adres IP na moduł równoważenia obciążenia, pobierając wartość z połączonego szablonu.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Nie można użyć `reference` tej funkcji w sekcji wyjścia [zagnieżdżonego szablonu](linked-templates.md#nested-template). Aby zwrócić wartości wdrożonego zasobu w szablonie zagnieżdżonym, przekonwertuj szablon zagnieżdżony na szablon połączony.

## <a name="get-output-values"></a>Uzyskaj wartości wyjściowe

Gdy wdrożenie zakończy się pomyślnie, wartości wyjściowe są automatycznie zwracane w wynikach wdrożenia.

Aby uzyskać wartości wyjściowe z historii wdrażania, można użyć skryptu.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>Przykładowe szablony

Poniższe przykłady pokazują scenariusze przy użyciu danych wyjściowych.

|Szablon  |Opis  |
|---------|---------|
|[Kopiowanie zmiennych](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Tworzy złożone zmienne i wyprowadza te wartości. Nie wdraża żadnych zasobów. |
|[Publiczny adres IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Tworzy publiczny adres IP i wyprowadza identyfikator zasobu. |
|[Moduł równoważenia obciążenia](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Łącza do poprzedniego szablonu. Używa identyfikatora zasobu w danych wyjściowych podczas tworzenia modułu równoważenia obciążenia. |

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o dostępnych właściwościach produktów wyjściowych, zobacz [Opis struktury i składni szablonów usługi Azure Resource Manager](template-syntax.md).
