---
title: Dane wyjściowe w szablonach
description: Opisuje sposób definiowania wartości wyjściowych w szablonie Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 61120b9941a6a20812ea046265ecbe13014d769e
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689142"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Dane wyjściowe w szablonie Azure Resource Manager

W tym artykule opisano sposób definiowania wartości wyjściowych w szablonie Azure Resource Manager. Dane wyjściowe są używane, gdy trzeba zwrócić wartości ze wdrożonych zasobów.

## <a name="define-output-values"></a>Definiowanie wartości wyjściowych

Poniższy przykład pokazuje, jak zwrócić identyfikator zasobu dla publicznego adresu IP:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

## <a name="conditional-output"></a>Dane wyjściowe warunkowe

W sekcji dane wyjściowe można warunkowo zwrócić wartość. Zazwyczaj warunek jest używany w danych wyjściowych, gdy [warunkowo wdrożono](conditional-resource-deployment.md) zasób. Poniższy przykład pokazuje, jak warunkowo zwrócić identyfikator zasobu dla publicznego adresu IP na podstawie tego, czy został wdrożony nowy:

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

## <a name="linked-templates"></a>Połączone szablony

Aby pobrać wartość wyjściową z połączonego szablonu, użyj funkcji [Reference](resource-group-template-functions-resource.md#reference) w szablonie nadrzędnym. Składnia w szablonie nadrzędnym jest następująca:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

Podczas pobierania właściwości wyjściowej z połączonego szablonu, nazwa właściwości nie może zawierać kreski.

Poniższy przykład pokazuje, jak ustawić adres IP dla modułu równoważenia obciążenia przez pobranie wartości z połączonego szablonu.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Nie można użyć funkcji `reference` w sekcji dane wyjściowe [szablonu zagnieżdżonego](resource-group-linked-templates.md#nested-template). Aby zwrócić wartości wdrożonego zasobu w zagnieżdżonym szablonie, przekonwertuj zagnieżdżony szablon na połączony szablon.

## <a name="get-output-values"></a>Pobierz wartości wyjściowe

Po pomyślnym wdrożeniu wartości wyjściowe są automatycznie zwracane w wynikach wdrożenia.

Aby uzyskać wartości wyjściowe z historii wdrożenia, można użyć skryptu.

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az group deployment show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>Przykładowe szablony

W poniższych przykładach przedstawiono scenariusze używania danych wyjściowych.

|Szablon  |Opis  |
|---------|---------|
|[Kopiuj zmienne](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Tworzy złożone zmienne i wyprowadza te wartości. Nie wdraża żadnych zasobów. |
|[Publiczny adres IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Tworzy publiczny adres IP i wyprowadza identyfikator zasobu. |
|[Moduł równoważenia obciążenia](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Linki do poprzedniego szablonu. Używa identyfikatora zasobu w danych wyjściowych podczas tworzenia modułu równoważenia obciążenia. |

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o dostępnych właściwościach danych wyjściowych, zobacz [Opis struktury i składni szablonów Azure Resource Manager](resource-group-authoring-templates.md).
