---
title: Operacje wdrażania z usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Opisuje sposób wyświetlania operacje wdrażania usługi Azure Resource Manager z portalu, programu PowerShell, interfejsu wiersza polecenia Azure i interfejsu API REST.
services: azure-resource-manager,virtual-machines
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-multiple
ms.workload: infrastructure
ms.date: 04/23/2018
ms.author: tomfitz
ms.openlocfilehash: 523ea3bf5d41231ab3281f9d8eb1fac8c3dfb55f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="view-deployment-operations-with-azure-resource-manager"></a>Operacje wdrażania widoku z usługi Azure Resource Manager

Można wyświetlić operacje wdrożenia za pośrednictwem portalu Azure. Może być najbardziej interesujące wyświetlanie operacje, gdy otrzymano wystąpił błąd podczas wdrażania, ten artykuł skupia się na wyświetlanie operacje, które nie powiodły. Portal zawiera interfejs, który umożliwia łatwe znajdowanie błędów i ustalić potencjalne rozwiązania.

Wdrożenia można rozwiązać, analizując dzienniki inspekcji lub operacje wdrażania. W tym artykule przedstawiono obie metody. Aby uzyskać pomoc przy rozwiązywaniu problemów z błędami konkretnego wdrożenia, zobacz [Rozwiąż typowe błędy podczas wdrażania zasobów na platformie Azure za pomocą Menedżera zasobów Azure](resource-manager-common-deployment-errors.md).

## <a name="portal"></a>Portal
Aby wyświetlić operacje wdrażania, użyj następujących kroków:

1. Dla grupy zasobów objętego wdrożenia Zwróć uwagę, stan poprzedniego wdrożenia. Możesz wybrać ten stan, aby uzyskać więcej szczegółów.
   
    ![Stan wdrożenia](./media/resource-manager-deployment-operations/deployment-status.png)
2. Zobaczysz niedawną historię wdrażania. Wybierz wdrożenie, które zakończyły się niepowodzeniem.
   
    ![Stan wdrożenia](./media/resource-manager-deployment-operations/select-deployment.png)
3. Wybierz łącze, aby wyświetlić opis przyczyny niepowodzenia wdrożenia. Na poniższej ilustracji rekord DNS nie jest unikatowa.  
   
    ![Wyświetl wdrożenia nie powiodło się](./media/resource-manager-deployment-operations/view-error.png)
   
    Ten komunikat o błędzie powinny wystarczyć można zacząć Rozwiązywanie problemów. Jeśli potrzebujesz więcej szczegółowych informacji o zadania, które zostały ukończone, można wyświetlić operacje, jak pokazano w poniższych krokach.
4. Można wyświetlić wszystkie operacje wdrażania. Wybierz żadnej operacji, aby zobaczyć więcej szczegółów.
   
    ![Wyświetlanie operacji](./media/resource-manager-deployment-operations/view-operations.png)
   
    W takim przypadku zobaczysz, że zostały pomyślnie utworzone konto magazynu, sieci wirtualnej i zestawu dostępności. Publiczny adres IP nie powiodło się, a nie podjęto inne zasoby.
5. Można wyświetlać zdarzenia dla wdrożenia, wybierając **zdarzenia**.
   
    ![Wyświetl zdarzenia](./media/resource-manager-deployment-operations/view-events.png)
6. Zobacz wszystkie zdarzenia dla wdrożenia i zaznacz jeden więcej szczegółów. Zwróć uwagę identyfikatorów korelacji. Ta wartość może być przydatne podczas pracy z pomocą techniczną w celu wdrożenia rozwiązania.
   
    ![Zobacz zdarzenia](./media/resource-manager-deployment-operations/see-all-events.png)

## <a name="powershell"></a>PowerShell
1. Aby uzyskać ogólny stan wdrożenia, należy użyć **Get-AzureRmResourceGroupDeployment** polecenia. 

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup
  ```

   Alternatywnie można filtrować wyniki dla tych wdrożeń, które nie powiodły.

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
  ```
   
2. Dla każdego wdrożenia zawiera wiele operacji. Każda operacja reprezentuje krokiem w procesie wdrażania. Aby dowiedzieć się, co poszło źle z wdrożeniem, zazwyczaj należy wyświetlić szczegółowe informacje o operacji wdrażania. Można wyświetlić stan operacji z **Get-AzureRmResourceGroupDeploymentOperation**.

  ```powershell 
  Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName vmDeployment
  ```

    Polecenie to zwraca wiele operacji z każdym z nich w następującym formacie:

  ```powershell
  Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
  OperationId    : A3EB2DA598E0A780
  Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                   duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                   serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
  PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                   serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
  ```

3. Aby uzyskać więcej informacji na temat operacji nie powiodło się, można pobrać właściwości dla operacji o **stanu**.

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
  ```
   
    Polecenie to zwraca wszystkie nieudane operacje z każdym z nich w następującym formacie:

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

    Zanotuj serviceRequestId i trackingId dla tej operacji. ServiceRequestId mogą być pomocne podczas pracy z pomocą techniczną w celu wdrożenia rozwiązania. Aby skoncentrować się na określonej operacji użyje trackingId w następnym kroku.
4. Aby uzyskać komunikatu o stanie o określonej operacji nie powiodło się, użyj następującego polecenia:

  ```powershell
  ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
  ```

    Polecenie to zwraca:

  ```powershell
  code           message                                                                        details
  ----           -------                                                                        -------
  DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
  ```
4. Każda operacja wdrażania na platformie Azure jest przechowywana zawartość żądania i odpowiedzi. Zawartość żądania jest co wysłanych do usługi Azure podczas wdrażania (na przykład utworzyć Maszynę wirtualną, dysk systemu operacyjnego i innych zasobów). Treść odpowiedzi jest Azure wysyłane z żądania wdrożenia. Podczas wdrażania można używać **DeploymentDebugLogLevel** parametr, aby określić, czy żądania i/lub odpowiedzi są przechowywane w dzienniku. 

  Uzyskiwanie informacji z dziennika i zapisać go lokalnie, używając następujących poleceń programu PowerShell:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
  ```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

1. Ogólny stan wdrożenia z **Pokaż wdrożenia grupy azure** polecenia.

  ```azurecli
  az group deployment show -g ExampleGroup -n ExampleDeployment
  ```
  
1. Jedna z wartości zwracane jest **correlationId**. Ta wartość służy do śledzenia zdarzenia powiązane i mogą być przydatne podczas pracy z pomocą techniczną w celu wdrożenia rozwiązania.

  ```azurecli
  az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
  ```

1. Aby wyświetlić operacje we wdrożeniu, należy użyć:

  ```azurecli
  az group deployment operation list -g ExampleGroup -n ExampleDeployment
  ```

## <a name="rest"></a>REST

1. Uzyskiwanie informacji o wdrażaniu z [uzyskać informacje na temat wdrażania szablonu](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Get) operacji.

  ```http
  GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
  ```

    W odpowiedzi, należy pamiętać, w szczególności **provisioningState**, **correlationId**, i **błąd** elementów. **CorrelationId** służy do śledzenia zdarzenia powiązane i mogą być przydatne podczas pracy z pomocą techniczną w celu wdrożenia rozwiązania.

  ```json
  { 
    ...
    "properties": {
      "provisioningState":"Failed",
      "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
      ...
      "error":{
        "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
        "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
      }  
    }
  }
  ```

2. Uzyskiwanie informacji o wdrożeniach z [listy wszystkich operacji wdrożenia szablonu](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_List). 

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
* Aby uzyskać pomoc przy rozwiązywaniu problemów z błędami konkretnego wdrożenia, zobacz [Rozwiąż typowe błędy podczas wdrażania zasobów na platformie Azure za pomocą Menedżera zasobów Azure](resource-manager-common-deployment-errors.md).
* Aby dowiedzieć się więcej o używaniu Dzienniki aktywności do monitorowania innych typów działań, zobacz [wyświetlać dzienniki aktywności do zarządzania zasobami Azure](resource-group-audit.md).
* Aby sprawdzić poprawność wdrożenia przed jego wykonaniem, zobacz [wdrażanie grupy zasobów za pomocą szablonu usługi Azure Resource Manager](resource-group-template-deploy.md).

