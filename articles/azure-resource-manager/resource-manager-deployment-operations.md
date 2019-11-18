---
title: Historia wdrożenia
description: Opisuje sposób wyświetlania Azure Resource Manager operacji wdrażania przy użyciu portalu, programu PowerShell, interfejsu wiersza polecenia platformy Azure i usługi API REST.
tags: top-support-issue
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: d8daf7191bb22f7c7057f6ef6b220a18868872cc
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149569"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Wyświetlanie historii wdrożenia za pomocą Azure Resource Manager

Azure Resource Manager umożliwia wyświetlanie historii wdrożenia i sprawdzanie określonych operacji w poprzednich wdrożeniach. Możesz zobaczyć wdrożone zasoby i uzyskać informacje o błędach.

Aby uzyskać pomoc dotyczącą rozwiązywania określonych błędów wdrażania, zobacz [Rozwiązywanie typowych błędów podczas wdrażania zasobów na platformie Azure za pomocą Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="portal"></a>Portal

Aby uzyskać szczegółowe informacje na temat wdrożenia z historii wdrażania.

1. Wybierz grupę zasobów, którą chcesz przeanalizować.

1. Wybierz link w obszarze **wdrożenia**.

   ![Wybierz historię wdrożenia](./media/resource-manager-deployment-operations/select-deployment-history.png)

1. Wybierz jedno z wdrożeń z historii wdrażania.

   ![Wybieranie wdrożenia](./media/resource-manager-deployment-operations/select-details.png)

1. Zostanie wyświetlone podsumowanie wdrożenia zawierające listę wdrożonych zasobów.

    ![Podsumowanie wdrożenia](./media/resource-manager-deployment-operations/view-deployment-summary.png)

1. Aby wyświetlić szablon używany do wdrożenia, wybierz opcję **szablon**. Możesz pobrać szablon, aby użyć go ponownie.

    ![Pokaż szablon](./media/resource-manager-deployment-operations/show-template-from-history.png)

1. Jeśli wdrożenie nie powiodło się, zostanie wyświetlony komunikat o błędzie. Wybierz komunikat o błędzie, aby uzyskać więcej szczegółów.

    ![Wyświetl niepowodzenie wdrożenia](./media/resource-manager-deployment-operations/show-error.png)

1. Zostanie wyświetlony szczegółowy komunikat o błędzie.

    ![Wyświetl szczegóły błędu](./media/resource-manager-deployment-operations/show-details.png)

1. Identyfikator korelacji służy do śledzenia powiązanych zdarzeń i może być przydatny podczas pracy z pomocą techniczną w celu rozwiązywania problemów z wdrożeniem.

    ![Pobierz identyfikator korelacji](./media/resource-manager-deployment-operations/get-correlation-id.png)

1. Aby dowiedzieć się więcej na temat kroku, który się nie powiódł, wybierz pozycję **szczegóły operacji**.

    ![Wybierz operacje wdrażania](./media/resource-manager-deployment-operations/select-deployment-operations.png)

1. Zobaczysz szczegóły dotyczące tego kroku wdrożenia.

    ![Pokaż szczegóły operacji](./media/resource-manager-deployment-operations/show-operation-details.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby uzyskać ogólny stan wdrożenia, użyj polecenia **Get-AzResourceGroupDeployment** .

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Lub można filtrować wyniki tylko dla tych wdrożeń, które zakończyły się niepowodzeniem.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
```

Identyfikator korelacji służy do śledzenia powiązanych zdarzeń i może być przydatny podczas pracy z pomocą techniczną w celu rozwiązywania problemów z wdrożeniem. Aby uzyskać identyfikator korelacji, użyj:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName azuredeploy).CorrelationId
```

Każde wdrożenie zawiera wiele operacji. Każda operacja reprezentuje krok w procesie wdrażania. Aby sprawdzić, co się stało z wdrożeniem, zazwyczaj należy wyświetlić szczegółowe informacje o operacjach wdrażania. Stan operacji można zobaczyć przy użyciu elementu **Get-AzResourceGroupDeploymentOperation**.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName azuredeploy
```

Zwracające wiele operacji przy użyciu każdego z nich w następującym formacie:

```powershell
Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
OperationId    : A3EB2DA598E0A780
Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2019-05-13T21:42:40.7151512Z;
                duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
```

Aby uzyskać więcej informacji na temat operacji nie powiodło się, można pobrać właściwości dla operacji o **stanu**.

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
```

Zwraca wszystkie operacje zakończone niepowodzeniem z każdym z nich w następującym formacie:

```powershell
provisioningOperation : Create
provisioningState     : Failed
timestamp             : 2019-05-13T21:42:40.7151512Z
duration              : PT3.1449887S
trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
statusCode            : BadRequest
statusMessage         : @{error=}
targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                       Microsoft.Network/publicIPAddresses/myPublicIP;
                       resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
```

Zwróć uwagę na serviceRequestId i trackingId dla operacji. ServiceRequestId może być przydatne podczas pracy z pomocą techniczną w celu rozwiązywania problemów z wdrożeniem. Użyjesz trackingId w następnym kroku, aby skoncentrować się na określonej operacji.

Aby uzyskać komunikat o stanie dla konkretnej nieudanej operacji, użyj następującego polecenia:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
```

Które zwraca:

```powershell
code           message                                                                        details
----           -------                                                                        -------
DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
```

Każda operacja wdrożenia na platformie Azure zawiera zawartość żądania i odpowiedzi. Podczas wdrażania można użyć parametru **DeploymentDebugLogLevel** w celu określenia, czy żądanie i/lub odpowiedź są rejestrowane.

Te informacje są uzyskiwane z dziennika i zapisywane lokalnie przy użyciu następujących poleceń programu PowerShell:

```powershell
(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby uzyskać ogólny stan wdrożenia, użyj polecenia **Pokaż wdrożenie grupy platformy Azure** .

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment
```
  
Identyfikator korelacji służy do śledzenia powiązanych zdarzeń i może być przydatny podczas pracy z pomocą techniczną w celu rozwiązywania problemów z wdrożeniem.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
```

Aby wyświetlić operacje dotyczące wdrożenia, użyj:

```azurecli-interactive
az group deployment operation list -g ExampleGroup -n ExampleDeployment
```

## <a name="rest"></a>REST

Aby uzyskać informacje na temat wdrożenia, należy użyć operacji [Pobierz informacje o wdrożeniu szablonu](https://docs.microsoft.com/rest/api/resources/deployments) .

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

W odpowiedzi Zanotuj w szczególności elementy **provisioningState**, **Identyfikator korelacji**i **Błędy** . **Identyfikator korelacji** służy do śledzenia powiązanych zdarzeń i może być przydatny podczas pracy z pomocą techniczną w celu rozwiązywania problemów z wdrożeniem.

```json
{ 
 ...
 "properties": {
   "provisioningState":"Failed",
   "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
   ...
   "error":{
     "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
     "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
   }  
 }
}
```

Aby uzyskać informacje o wdrożeniach, użyj [listy wszystkie operacje wdrażania szablonu](https://docs.microsoft.com/rest/api/resources/deployments). 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```
   
Odpowiedź zawiera informacje na temat żądania i/lub odpowiedzi w zależności od tego, co zostało określone we właściwości **debugSetting** podczas wdrażania.

```json
{
 ...
 "properties": 
 {
   ...
   "request":{
     "content":{
       "location":"West US",
       "properties":{
         "accountType": "Standard_LRS"
       }
     }
   },
   "response":{
     "content":{
       "error":{
         "message":"Conflict","code":"Conflict"
       }
     }
   }
 }
}
```

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać pomoc dotyczącą rozwiązywania określonych błędów wdrażania, zobacz [Rozwiązywanie typowych błędów podczas wdrażania zasobów na platformie Azure za pomocą Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Aby dowiedzieć się więcej o korzystaniu z dzienników aktywności do monitorowania innych typów akcji, zobacz [Wyświetlanie dzienników aktywności w celu zarządzania zasobami platformy Azure](resource-group-audit.md).
* Aby sprawdzić poprawność wdrożenia przed jego wykonaniem, zobacz [wdrażanie grupy zasobów przy użyciu szablonu Azure Resource Manager](resource-group-template-deploy.md).

