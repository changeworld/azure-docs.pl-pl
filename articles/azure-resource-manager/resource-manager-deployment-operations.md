---
title: Operacji wdrażania przy użyciu usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Opisuje sposób wyświetlania operacji wdrażania usługi Azure Resource Manager przy użyciu portalu, programu PowerShell i wiersza polecenia platformy Azure oraz interfejsu API REST.
services: azure-resource-manager,virtual-machines
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-multiple
ms.workload: infrastructure
ms.date: 09/28/2018
ms.author: tomfitz
ms.openlocfilehash: 9bb6491565f685e8ca3d7a6271747a5df3629e81
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2019
ms.locfileid: "56269080"
---
# <a name="view-deployment-operations-with-azure-resource-manager"></a>Wyświetlanie operacji wdrażania przy użyciu usługi Azure Resource Manager

Można wyświetlić operacje wdrażania w witrynie Azure portal. Może być najbardziej interesujące oglądania operacje, które otrzymał wystąpił błąd podczas wdrażania, w tym artykule koncentruje się na wyświetlanie działań, które nie powiodły. Portal udostępnia interfejs, który pozwala na łatwe znajdowanie błędów i określić potencjalne rozwiązania.

Wdrożenia można rozwiązać, analizując dzienniki inspekcji lub operacje wdrażania. W tym artykule przedstawiono obie metody. Aby uzyskać pomoc dotyczącą Rozwiązywanie błędów danego wdrożenia, zobacz [Rozwiązywanie typowych problemów podczas wdrażania zasobów na platformie Azure przy użyciu usługi Azure Resource Manager](resource-manager-common-deployment-errors.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="portal"></a>Portal

Aby wyświetlić operacje wdrażania, użyj następujących kroków:

1. Dla grupy zasobów jest zaangażowane we wdrożeniu Zwróć uwagę, stan ostatniego wdrożenia. Możesz wybrać ten stan, aby uzyskać więcej szczegółów.
   
    ![Stan wdrożenia](./media/resource-manager-deployment-operations/deployment-status.png)
2. Zobaczysz niedawną historię wdrożenia. Wybierz wdrożenie, który uległ awarii.
   
    ![Stan wdrożenia](./media/resource-manager-deployment-operations/select-deployment.png)
3. Wybierz łącze, aby wyświetlić opis przyczyny niepowodzenia wdrożenia. Na poniższej ilustracji rekord DNS nie jest unikatowa.  
   
    ![Wyświetl wdrożenie nie powiodło się](./media/resource-manager-deployment-operations/view-error.png)
   
    Ten komunikat o błędzie powinien być wystarczająco dużo, aby można było rozpocząć rozwiązywanie problemów z. Jednak jeśli potrzebujesz więcej informacji dotyczących zadań, które zostały ukończone, można wyświetlić operacje, jak pokazano w poniższych krokach.
4. Można wyświetlić wszystkich operacji wdrożenia. Wybierz każdej operacji, aby zobaczyć więcej szczegółów.
   
    ![Wyświetlanie operacji](./media/resource-manager-deployment-operations/view-operations.png)
   
    W takim przypadku zobaczysz, że konta magazynu, sieci wirtualnej i zestaw dostępności zostały pomyślnie utworzone. Publiczny adres IP nie powiodło się, a nie podjęto próby innych zasobów.
5. Można wyświetlać zdarzenia dla wdrożenia, wybierając **zdarzenia**.
   
    ![Wyświetl zdarzenia](./media/resource-manager-deployment-operations/view-events.png)
6. Zobacz wszystkie zdarzenia dla wdrożenia i zaznacz dowolny, aby uzyskać więcej informacji. Zwróć uwagę identyfikatorów korelacji. Ta wartość może być przydatne podczas pracy z usługą pomocy technicznej, aby Rozwiązywanie problemów z wdrażaniem.
   
    ![Zobacz zdarzenia](./media/resource-manager-deployment-operations/see-all-events.png)

## <a name="powershell"></a>PowerShell
1. Aby uzyskać ogólny stan wdrożenia, użyj **Get AzResourceGroupDeployment** polecenia. 

  ```powershell
  Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
  ```

   Alternatywnie można filtrować wyniki tych wdrożeń, które nie powiodło się.

  ```powershell
  Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
  ```
   
2. Aby uzyskać identyfikator korelacji, należy użyć:

  ```powershell
  (Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName azuredeploy).CorrelationId
  ```

3. Każde wdrożenie obejmuje wiele operacji. Każda operacja stanowi krok w procesie wdrażania. Aby dowiedzieć się, czym jest problem z wdrożeniem, zazwyczaj chcesz zobaczyć szczegółowe informacje o operacji wdrażania. Można wyświetlić stan operacji przy użyciu **Get AzResourceGroupDeploymentOperation**.

  ```powershell 
  Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName vmDeployment
  ```

    Zwraca wiele operacji z każdą z nich w następującym formacie:

  ```powershell
  Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
  OperationId    : A3EB2DA598E0A780
  Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                   duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                   serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
  PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                   serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
  ```

4. Aby uzyskać więcej informacji na temat operacji nie powiodło się, można pobrać właściwości dla operacji o **stanu**.

  ```powershell
  (Get-AzResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
  ```
   
    Zwraca wszystkie operacje zakończone niepowodzeniem z każdą z nich w następującym formacie:

  ```powershell
  provisioningOperation : Create
  provisioningState     : Failed
  timestamp             : 2016-06-14T21:54:55.1468068Z
  duration              : PT3.1449887S
  trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
  serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
  statusCode            : BadRequest
  statusMessage         : @{error=}
  targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                          Microsoft.Network/publicIPAddresses/myPublicIP;
                          resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
  ```

    Należy pamiętać, serviceRequestId i trackingId dla tej operacji. ServiceRequestId mogą być pomocne podczas pracy z usługą pomocy technicznej, aby Rozwiązywanie problemów z wdrażaniem. Identyfikator śledzenia w następnym kroku użyje skoncentrować się na określonej operacji.
5. Aby uzyskać komunikat o stanie określonej operacji nie powiodło się, użyj następującego polecenia:

  ```powershell
  ((Get-AzResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
  ```

    Zwraca:

  ```powershell
  code           message                                                                        details
  ----           -------                                                                        -------
  DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
  ```
6. Każda operacja wdrażania na platformie Azure obejmuje zawartości żądania i odpowiedzi. Zawartość żądania jest, co zostanie wysłane na platformie Azure podczas wdrażania (na przykład można utworzyć Maszynę wirtualną, dysk systemu operacyjnego i innych zasobów). Zawartość odpowiedzi jest Azure odsyłane spod żądania wdrożenia. Podczas wdrażania, można użyć **DeploymentDebugLogLevel** parametru do określenia, czy żądania i/lub odpowiedzi są przechowywane w dzienniku. 

  Pobieranie informacji z dziennika, a następnie zapisz go lokalnie, używając następujących poleceń programu PowerShell:

  ```powershell
  (Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

  (Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
  ```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

1. Uzyskaj ogólny stan wdrożenia za pomocą **grupy azure deployment show** polecenia.

  ```azurecli
  az group deployment show -g ExampleGroup -n ExampleDeployment
  ```
  
2. Jedna z wartości zwracane jest **correlationId**. Ta wartość służy do śledzenia powiązane zdarzenia i może być przydatne podczas pracy z pomocy technicznej, aby Rozwiązywanie problemów z wdrażaniem.

  ```azurecli
  az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
  ```

3. Aby wyświetlić operacje dotyczące wdrożenia, należy użyć:

  ```azurecli
  az group deployment operation list -g ExampleGroup -n ExampleDeployment
  ```

## <a name="rest"></a>REST

1. Uzyskaj informacje o wdrażaniu przy użyciu [uzyskać informacje na temat wdrożenia szablonu](https://docs.microsoft.com/rest/api/resources/deployments) operacji.

  ```http
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

2. Uzyskać informacje na temat wdrożenia z [listy wszystkich operacji wdrożenia szablonu](https://docs.microsoft.com/rest/api/resources/deployments). 

  ```http
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

