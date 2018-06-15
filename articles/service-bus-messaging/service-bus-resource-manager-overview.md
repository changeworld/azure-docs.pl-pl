---
title: Tworzenie zasobów Azure Service Bus przy użyciu szablonów usługi Resource Manager | Dokumentacja firmy Microsoft
description: Szablony usługi Azure Resource Manager umożliwia zautomatyzowanie tworzenie zasobów usługi Service Bus
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 24f6a207-0fa4-49cf-8a58-963f9e2fd655
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 04/11/2018
ms.author: sethm
ms.openlocfilehash: c8d84de608ccf3d9a9293c20c07c10a00b73da68
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31598289"
---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Tworzenie zasobów usługi Service Bus przy użyciu szablonów usługi Azure Resource Manager

W tym artykule opisano sposób tworzenia i wdrażania zasobów usługi Service Bus przy użyciu szablonów usługi Azure Resource Manager, programu PowerShell i dostawcy zasobów usługi Service Bus.

Szablony usługi Azure Resource Manager pomagają zdefiniować zasobów do wdrożenia rozwiązania, aby określić parametry i zmienne, które umożliwią wprowadzanie wartości dla różnych środowisk. Szablon jest zapisywany w formacie JSON i składa się z wyrażeń, które służy do tworzenia wartości na potrzeby wdrożenia. Aby uzyskać szczegółowe informacje na temat pisania szablonów usługi Azure Resource Manager i omówione w formacie szablonu, zobacz [struktury i składni szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

> [!NOTE]
> Przykłady w tym artykule pokazano, jak używać usługi Azure Resource Manager do tworzenia nazw usługi Service Bus i jednostki obsługi komunikatów (kolejki). Inne przykłady szablonów można znaleźć [galerię szablonów Szybki Start Azure] [ Azure Quickstart Templates gallery] i wyszukaj **usługi Service Bus**.
>
>

## <a name="service-bus-resource-manager-templates"></a>Szablony Menedżera zasobów magistrali usług

Te szablony usługi magistrali usługi Azure Resource Manager są dostępne do pobrania i wdrożenia. Kliknij poniższe łącza, aby uzyskać szczegółowe informacje o każdym z nich, wraz z łączami do szablonów w witrynie GitHub:

* [Tworzenie przestrzeni nazw usługi Service Bus](service-bus-resource-manager-namespace.md)
* [Tworzenie przestrzeni nazw usługi Service Bus z kolejki](service-bus-resource-manager-namespace-queue.md)
* [Tworzenie przestrzeni nazw usługi Service Bus z tematów i subskrypcji](service-bus-resource-manager-namespace-topic.md)
* [Tworzenie przestrzeni nazw usługi Service Bus z regułą kolejki i autoryzacji](service-bus-resource-manager-namespace-auth-rule.md)
* [Tworzenie przestrzeni nazw usługi Service Bus z tematu, subskrypcji i reguły](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>Wdrażanie przy użyciu programu PowerShell

Poniższa procedura opisuje sposób wdrażania szablonu usługi Azure Resource Manager, który tworzy przestrzeni nazw usługi Service Bus warstwy standardowa i kolejką w tej przestrzeni nazw przy użyciu programu PowerShell. Ten przykład jest oparty na [tworzenie przestrzeni nazw usługi Service Bus z kolejką](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) szablonu. Przybliżony przepływu pracy jest następujący:

1. Instalowanie programu PowerShell.
2. Utwórz szablon i (opcjonalnie) pliku parametrów.
3. W programie PowerShell należy zalogować się do konta platformy Azure.
4. Utwórz nową grupę zasobów, jeśli jeszcze nie istnieje.
5. Testowanie wdrożenia.
6. W razie potrzeby można ustawić trybu wdrożenia.
7. Wdrażanie szablonu.

Aby uzyskać pełne informacje na temat wdrażania szablonów usługi Azure Resource Manager, zobacz [wdrożenie zasobów przy użyciu szablonów usługi Azure Resource Manager][Deploy resources with Azure Resource Manager templates].

### <a name="install-powershell"></a>Instalowanie programu PowerShell

Instalowanie programu Azure PowerShell zgodnie z instrukcjami w [wprowadzenie do programu Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Tworzenie szablonu

Klonowanie repozytorium lub kopiowania [201 magistrali usług — Tworzenie kolejki](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) szablonu z serwisu GitHub:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
      }
    }
  },
  "variables": {
    "defaultSASKeyName": "RootManageSharedAccessKey",
    "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]",
    "sbVersion": "2017-04-01"
  },
  "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('serviceBusNamespaceName')]",
      "type": "Microsoft.ServiceBus/Namespaces",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "type": "Queues",
          "dependsOn": [
            "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "lockDuration": "PT5M",
            "maxSizeInMegabytes": "1024",
            "requiresDuplicateDetection": "false",
            "requiresSession": "false",
            "defaultMessageTimeToLive": "P10675199DT2H48M5.4775807S",
            "deadLetteringOnMessageExpiration": "false",
            "duplicateDetectionHistoryTimeWindow": "PT10M",
            "maxDeliveryCount": "10",
            "autoDeleteOnIdle": "P10675199DT2H48M5.4775807S",
            "enablePartitioning": "false",
            "enableExpress": "false"
          }
        }
      ]
    }
  ],
  "outputs": {
    "NamespaceConnectionString": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
    },
    "SharedAccessPolicyPrimaryKey": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
    }
  }
}
```

### <a name="create-a-parameters-file-optional"></a>Utwórz plik parametrów (opcjonalnie)

Aby użyć pliku następujące parametry opcjonalne, skopiuj [201 magistrali usług — Tworzenie kolejki](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) pliku. Zastąp wartość `serviceBusNamespaceName` z nazwą przestrzeni nazw usługi Service Bus chcesz utworzyć w tym wdrożeniu i zastąp wartość `serviceBusQueueName` nazwą kolejki, w którym chcesz utworzyć.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2017-04-01"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [parametry](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) artykułu.

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Logowanie do platformy Azure i ustaw subskrypcji platformy Azure

Z wiersza polecenia programu PowerShell, uruchom następujące polecenie:

```powershell
Connect-AzureRmAccount
```

Zostanie wyświetlony monit logowania do konta platformy Azure. Po zalogowaniu, uruchom następujące polecenie, aby wyświetlić dostępne subskrypcji:

```powershell
Get-AzureRMSubscription
```

To polecenie zwraca listę dostępnych subskrypcji platformy Azure. Wybierz subskrypcję dla bieżącej sesji, uruchamiając następujące polecenie. Zastąp `<YourSubscriptionId>` o identyfikatorze GUID dla subskrypcji platformy Azure, którego chcesz użyć:

```powershell
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Ustaw grupę zasobów

Jeśli nie masz istniejący zasób grupy, Utwórz nową grupę zasobów o ** New-AzureRmResourceGroup ** polecenia. Podaj nazwę grupy zasobów i lokalizacji, w której chcesz użyć. Na przykład:

```powershell
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

Jeśli to się powiedzie, zostanie wyświetlone podsumowanie nowej grupy zasobów.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Testowanie wdrożenia

Sprawdzanie poprawności wdrożenia, uruchamiając `Test-AzureRmResourceGroupDeployment` polecenia cmdlet. Podczas testowania wdrożenia, należy podać parametry, dokładnie tak jak w przypadku wykonywania wdrożenia.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Tworzenie wdrożenia

Aby utworzyć nowe wdrożenie, uruchom `New-AzureRmResourceGroupDeployment` polecenia cmdlet i podaj niezbędne parametry, po wyświetleniu monitu. Parametry zawierają nazwę dla danego wdrożenia, nazwę grupy zasobów, a ścieżka lub adres URL do pliku szablonu. Jeśli **tryb** parametr nie zostanie określony, wartością domyślną **przyrostowe** jest używany. Aby uzyskać więcej informacji, zobacz [przyrostowe i pełne wdrożeń](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments).

Polecenie wyświetla monit o podanie trzy parametry w oknie programu PowerShell:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Aby zamiast tego określ plik parametrów, należy użyć następującego polecenia:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Umożliwia także parametry wbudowanego po uruchomieniu polecenia cmdlet wdrażania. Polecenie wygląda następująco:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Do uruchomienia [pełną](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) wdrożenia, ustaw **tryb** parametr **Complete**:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>Weryfikacja wdrażania
Jeśli zasoby zostały pomyślnie wdrożone, zostanie wyświetlone podsumowanie wdrożenia w oknie programu PowerShell:

```powershell
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2017 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2017-04-01

```

## <a name="next-steps"></a>Kolejne kroki
Teraz przedstawiono podstawowy przepływ pracy i polecenia służące do wdrażania szablonu usługi Azure Resource Manager. Bardziej szczegółowe informacje można znaleźć w następujących łączy:

* [Omówienie usługi Azure Resource Manager][Azure Resource Manager overview]
* [Wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i programu Azure PowerShell][Deploy resources with Azure Resource Manager templates]
* [Tworzenie szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)

[Azure Resource Manager overview]: ../azure-resource-manager/resource-group-overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/resource-group-template-deploy.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus
