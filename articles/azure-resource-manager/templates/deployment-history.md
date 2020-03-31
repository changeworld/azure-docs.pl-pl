---
title: Historia wdrażania
description: W tym artykule opisano sposób wyświetlania operacji wdrażania usługi Azure Resource Manager za pomocą portalu, programu PowerShell, interfejsu wiersza polecenia platformy Azure i interfejsu API REST.
tags: top-support-issue
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: b0f196f86bed05094b04bfc20c7cef2248a91c65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460300"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Wyświetlanie historii wdrażania za pomocą usługi Azure Resource Manager

Usługa Azure Resource Manager umożliwia wyświetlanie historii wdrażania i badanie określonych operacji w poprzednich wdrożeniach. Możesz zobaczyć zasoby, które zostały wdrożone i uzyskać informacje o wszelkich błędach.

Aby uzyskać pomoc dotyczącą rozwiązywania określonych błędów wdrażania, zobacz [Rozwiązywanie typowych błędów podczas wdrażania zasobów na platformie Azure za pomocą usługi Azure Resource Manager.](common-deployment-errors.md)

## <a name="get-deployments-and-correlation-id"></a>Pobierz wdrożeń i identyfikator korelacji

Można wyświetlić szczegółowe informacje o wdrożeniu za pośrednictwem witryny Azure portal, PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST. Każde wdrożenie ma identyfikator korelacji, który jest używany do śledzenia powiązanych zdarzeń. Może być przydatne podczas pracy z pomocą techniczną w celu rozwiązania problemu wdrożenia.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wybierz grupę zasobów, którą chcesz zbadać.

1. Wybierz łącze w obszarze **Wdrożenia**.

   ![Wybieranie historii wdrażania](./media/deployment-history/select-deployment-history.png)

1. Wybierz jedno z wdrożeń z historii wdrażania.

   ![Wybierz wdrożenie](./media/deployment-history/select-details.png)

1. Zostanie wyświetlone podsumowanie wdrożenia, w tym identyfikator korelacji.

    ![Podsumowanie wdrożenia](./media/deployment-history/show-correlation-id.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Aby wyświetlić listę wszystkich wdrożeń dla grupy zasobów, należy użyć polecenia [Get-AzResourceGroupDeployment.](/powershell/module/az.resources/Get-AzResourceGroupDeployment)

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Aby uzyskać określone wdrożenie z grupy zasobów, dodaj parametr **DeploymentName.**

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

Aby uzyskać identyfikator korelacji, użyj:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wyświetlić listę wdrożenia dla grupy zasobów, użyj [listy grup wdrożeniowych AZ](/cli/azure/group/deployment?view=azure-cli-latest#az-deployment-group-list).

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

Aby uzyskać określone wdrożenie, użyj [pokazu grupy wdrażania az](/cli/azure/group/deployment?view=azure-cli-latest#az-deployment-group-show).

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

Aby uzyskać identyfikator korelacji, użyj:

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[Protokół HTTP](#tab/http)

Aby wyświetlić listę wdrożeń dla grupy zasobów, należy użyć następującej operacji. Aby uzyskać najnowszy numer wersji interfejsu API do użycia w żądaniu, zobacz [Wdrożenia — Lista według grupy zasobów](/rest/api/resources/deployments/listbyresourcegroup).

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

Aby uzyskać określone wdrożenie. wykonać następującą operację. Aby uzyskać najnowszy numer wersji interfejsu API do użycia w żądaniu, zobacz [Wdrożenia - Pobierz](/rest/api/resources/deployments/get).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

Odpowiedź zawiera identyfikator korelacji.

```json
{
 ...
 "properties": {
   "mode": "Incremental",
   "provisioningState": "Failed",
   "timestamp": "2019-11-26T14:18:36.4518358Z",
   "duration": "PT26.2091817S",
   "correlationId": "47ff4228-bf2e-4ee5-a008-0b07da681230",
   ...
 }
}
```

---

## <a name="get-deployment-operations-and-error-message"></a>Pobierz operacje wdrażania i komunikat o błędzie

Każde wdrożenie może obejmować wiele operacji. Aby uzyskać więcej informacji na temat wdrożenia, zobacz operacje wdrażania. Gdy wdrożenie nie powiedzie się, operacje wdrażania zawierają komunikat o błędzie.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W podsumowaniu wdrożenia wybierz pozycję **Szczegóły operacji**.

    ![Wybierz operacje wdrażania](./media/deployment-history/get-operation-details.png)

1. Zobaczysz szczegóły dotyczące tego kroku wdrożenia. W przypadku wystąpienia błędu szczegóły zawierają komunikat o błędzie.

    ![Pokaż szczegóły operacji](./media/deployment-history/see-operation-details.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Aby wyświetlić operacje wdrażania do wdrożenia w grupie zasobów, należy użyć polecenia [Get-AzResourceGroupDeploymentOperation.](/powershell/module/az.resources/get-azdeploymentoperation)

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

Aby wyświetlić operacje nie powiodło się, filtruj operacje ze **stanem Niepowodzenie.**

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

Aby uzyskać komunikat o stanie operacji nieudanych, użyj następującego polecenia:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wyświetlić operacje wdrażania dla wdrożenia w grupie zasobów, użyj polecenia [listy operacji grupy wdrażania AZ.](/cli/azure/group/deployment/operation?view=azure-cli-latest#az-deployment-group-operation-list)

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeployment
```

Aby wyświetlić operacje nie powiodło się, filtruj operacje ze **stanem Niepowodzenie.**

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

Aby uzyskać komunikat o stanie operacji nieudanych, użyj następującego polecenia:

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[Protokół HTTP](#tab/http)

Aby uzyskać operacje wdrażania, należy użyć następującej operacji. Aby uzyskać najnowszy numer wersji interfejsu API do użycia w żądaniu, zobacz [Operacje wdrażania — lista](/rest/api/resources/deploymentoperations/list).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

Odpowiedź zawiera komunikat o błędzie.

```json
{
  "value": [
    {
      "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Resources/deployments/exampledeploy/operations/13EFD9907103D640",
      "operationId": "13EFD9907103D640",
      "properties": {
        "provisioningOperation": "Create",
        "provisioningState": "Failed",
        "timestamp": "2019-11-26T14:18:36.3177613Z",
        "duration": "PT21.0580179S",
        "trackingId": "9d3cdac4-54f8-486c-94bd-10c20867b8bc",
        "serviceRequestId": "01a9d0fe-896b-4c94-a30f-60b70a8f1ad9",
        "statusCode": "BadRequest",
        "statusMessage": {
          "error": {
            "code": "InvalidAccountType",
            "message": "The AccountType Standard_LRS1 is invalid. For more information, see - https://aka.ms/storageaccountskus"
          }
        },
        "targetResource": {
          "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/storageq2czadzfgizc2",
          "resourceType": "Microsoft.Storage/storageAccounts",
          "resourceName": "storageq2czadzfgizc2"
        }
      }
    },
    ...
  ]
}
```

---

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać pomoc dotyczącą rozwiązywania określonych błędów wdrażania, zobacz [Rozwiązywanie typowych błędów podczas wdrażania zasobów na platformie Azure za pomocą usługi Azure Resource Manager.](common-deployment-errors.md)
* Aby dowiedzieć się więcej o używaniu dzienników aktywności do monitorowania innych typów akcji, zobacz [Wyświetlanie dzienników aktywności w celu zarządzania zasobami platformy Azure.](../management/view-activity-logs.md)
* Aby sprawdzić poprawność wdrożenia przed jego [wykonaniem, zobacz Wdrażanie grupy zasobów za pomocą szablonu usługi Azure Resource Manager](deploy-powershell.md).

