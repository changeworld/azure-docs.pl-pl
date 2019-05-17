---
title: Historia wdrożenia za pomocą usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Opisuje sposób wyświetlania operacji wdrażania usługi Azure Resource Manager przy użyciu portalu, programu PowerShell i wiersza polecenia platformy Azure oraz interfejsu API REST.
tags: top-support-issue
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tomfitz
ms.openlocfilehash: 58d22e3fcae5c30e5d7dcc39b317afeef4a693ee
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606033"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Wyświetl historię wdrożenia za pomocą usługi Azure Resource Manager

Usługa Azure Resource Manager pozwala na wyświetlanie historii wdrożenia i zbadać określonych operacji w ciągu ostatnich wdrożeń. Zapoznaj się z zasobami, które zostały wdrożone i uzyskać informacje na temat błędów.

Aby uzyskać pomoc dotyczącą Rozwiązywanie błędów danego wdrożenia, zobacz [Rozwiązywanie typowych problemów podczas wdrażania zasobów na platformie Azure przy użyciu usługi Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="portal"></a>Portal

Aby uzyskać szczegółowe informacje dotyczące wdrożenia z historii wdrożenia.

1. Wybierz grupę zasobów, które chcesz zbadać.

1. Wybierz link w obszarze **wdrożeń**.

   ![Wybierz pozycję Historia wdrażania](./media/resource-manager-deployment-operations/select-deployment-history.png)

1. Wybierz jedno z wdrożeń z historii wdrożenia.

   ![Wybieranie pozycji wdrożenie](./media/resource-manager-deployment-operations/select-details.png)

1. Zostanie wyświetlone podsumowanie wdrożenia, łącznie z listą zasobów, które zostały wdrożone.

    ![Podsumowanie wdrożenia](./media/resource-manager-deployment-operations/view-deployment-summary.png)

1. Aby wyświetlić Szablon użyty do wdrożenia, wybierz **szablonu**. Możesz pobrać szablonu Aby użyć go ponownie.

    ![Wyświetlanie szablonu](./media/resource-manager-deployment-operations/show-template-from-history.png)

1. Jeśli wdrożenie nie powiodło się, zobaczysz komunikat o błędzie. Wybieranie komunikatu o błędzie, aby uzyskać więcej informacji.

    ![Wyświetl wdrożenie nie powiodło się](./media/resource-manager-deployment-operations/show-error.png)

1. Wyświetlany jest komunikat szczegółowy komunikat o błędzie.

    ![Wyświetl szczegóły błędu](./media/resource-manager-deployment-operations/show-details.png)

1. Identyfikator korelacji jest używane do śledzenia powiązane zdarzenia i może być przydatne podczas pracy z pomocy technicznej, aby Rozwiązywanie problemów z wdrażaniem.

    ![Pobierz identyfikator korelacji](./media/resource-manager-deployment-operations/get-correlation-id.png)

1. Aby dowiedzieć się więcej na temat tego kroku, który nie powiodło się, zaznaczyć **szczegóły operacji**.

    ![Wybierz operacje wdrażania](./media/resource-manager-deployment-operations/select-deployment-operations.png)

1. Możesz wyświetlić szczegóły dla tego kroku wdrożenia.

    ![Pokaż szczegóły operacji](./media/resource-manager-deployment-operations/show-operation-details.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby uzyskać ogólny stan wdrożenia, użyj **Get AzResourceGroupDeployment** polecenia.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Alternatywnie można filtrować wyniki tych wdrożeń, które nie powiodło się.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
```

Identyfikator korelacji jest używane do śledzenia powiązane zdarzenia i może być przydatne podczas pracy z pomocy technicznej, aby Rozwiązywanie problemów z wdrażaniem. Aby uzyskać identyfikator korelacji, należy użyć:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName azuredeploy).CorrelationId
```

Każde wdrożenie obejmuje wiele operacji. Każda operacja stanowi krok w procesie wdrażania. Aby dowiedzieć się, czym jest problem z wdrożeniem, zazwyczaj chcesz zobaczyć szczegółowe informacje o operacji wdrażania. Można wyświetlić stan operacji przy użyciu **Get AzResourceGroupDeploymentOperation**.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName azuredeploy
```

Zwraca wiele operacji z każdą z nich w następującym formacie:

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

Zwraca wszystkie operacje zakończone niepowodzeniem z każdą z nich w następującym formacie:

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

Należy pamiętać, serviceRequestId i trackingId dla tej operacji. ServiceRequestId mogą być pomocne podczas pracy z usługą pomocy technicznej, aby Rozwiązywanie problemów z wdrażaniem. Identyfikator śledzenia w następnym kroku użyjemy skoncentrowanie się na określonej operacji.

Aby uzyskać komunikat o stanie określonej operacji nie powiodło się, użyj następującego polecenia:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
```

Zwraca:

```powershell
code           message                                                                        details
----           -------                                                                        -------
DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
```

Każda operacja wdrażania na platformie Azure obejmuje zawartości żądania i odpowiedzi. Podczas wdrażania, można użyć **DeploymentDebugLogLevel** parametru do określenia, czy żądania i/lub odpowiedzi są rejestrowane.

Pobieranie informacji z dziennika, a następnie zapisz go lokalnie, używając następujących poleceń programu PowerShell:

```powershell
(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby uzyskać ogólny stan wdrożenia, użyj **grupy azure deployment show** polecenia.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment
```
  
Identyfikator korelacji jest używane do śledzenia powiązane zdarzenia i może być przydatne podczas pracy z pomocy technicznej, aby Rozwiązywanie problemów z wdrażaniem.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
```

Aby wyświetlić operacje dotyczące wdrożenia, należy użyć:

```azurecli-interactive
az group deployment operation list -g ExampleGroup -n ExampleDeployment
```

## <a name="rest"></a>REST

Aby uzyskać informacje na temat wdrożenia, należy użyć [uzyskać informacje na temat wdrożenia szablonu](https://docs.microsoft.com/rest/api/resources/deployments) operacji.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

W odpowiedzi, należy pamiętać, w szczególności **provisioningState**, **correlationId**, i **błąd** elementów. **CorrelationId** służy do śledzenia powiązane zdarzenia i może być przydatne podczas pracy z pomocy technicznej, aby Rozwiązywanie problemów z wdrażaniem.

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

Aby uzyskać informacje na temat wdrożeń, użyj [listy wszystkich operacji wdrożenia szablonu](https://docs.microsoft.com/rest/api/resources/deployments). 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```
   
Odpowiedź zawiera informacje żądania i/lub odpowiedzi, w zależności od określonych w **debugSetting** właściwości podczas wdrażania.

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

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać pomoc dotyczącą Rozwiązywanie błędów danego wdrożenia, zobacz [Rozwiązywanie typowych problemów podczas wdrażania zasobów na platformie Azure przy użyciu usługi Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Aby dowiedzieć się, korzystając z tych dzienników do monitorowania innych typów akcji, zobacz [wyświetlanie dzienników aktywności do zarządzania zasobami platformy Azure](resource-group-audit.md).
* Aby sprawdzić poprawność wdrożenia przed jego wykonaniem, zobacz [wdrażanie grupy zasobów za pomocą szablonu usługi Azure Resource Manager](resource-group-template-deploy.md).

