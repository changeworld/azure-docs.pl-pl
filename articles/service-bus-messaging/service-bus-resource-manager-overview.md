---
title: Tworzenie zasobów usługi Azure Service Bus przy użyciu szablonów usługi Resource Manager | Dokumentacja firmy Microsoft
description: Aby zautomatyzować tworzenie zasobów usługi Service Bus przy użyciu szablonów usługi Azure Resource Manager
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 24f6a207-0fa4-49cf-8a58-963f9e2fd655
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 09/11/2018
ms.author: spelluru
ms.openlocfilehash: 196b00f1268eada20d0e35473dc6eb43c9e48df6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59045273"
---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Tworzenie zasobów magistrali usług przy użyciu szablonów usługi Azure Resource Manager

W tym artykule opisano sposób tworzenia i wdrażania zasobów usługi Service Bus przy użyciu szablonów usługi Azure Resource Manager, programu PowerShell i dostawcy zasobów usługi Service Bus.

Szablony usługi Azure Resource Manager pomagającym w zdefiniowaniu zasobów do wdrożenia rozwiązania, aby określić parametry i zmienne, które umożliwią wprowadzanie wartości dla różnych środowisk. Szablon jest zapisywany w formacie JSON i składa się z wyrażeń, które można używać do tworzenia wartości na potrzeby wdrożenia. Aby uzyskać szczegółowe informacje dotyczące pisania szablonów usługi Azure Resource Manager i dyskusji o formacie szablonu, zobacz [struktury i składni szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

> [!NOTE]
> Przykłady w niniejszym artykule pokazano, jak używać usługi Azure Resource Manager do tworzenia przestrzeni nazw usługi Service Bus i jednostki obsługi komunikatów (kolejki). Inne przykłady szablonów można znaleźć [galerii szablonów szybkiego startu platformy Azure] [ Azure Quickstart Templates gallery] i wyszukaj **usługi Service Bus**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-bus-resource-manager-templates"></a>Szablony usługi Service Bus Resource Manager

Te szablony usługi Service Bus usługi Azure Resource Manager są dostępne do pobrania i wdrożenia. Kliknij poniższe łącza, aby uzyskać szczegółowe informacje o każdym z nich, wraz z łączami do szablonów w witrynie GitHub:

* [Tworzenie przestrzeni nazw usługi Service Bus](service-bus-resource-manager-namespace.md)
* [Tworzenie przestrzeni nazw usługi Service Bus przy użyciu kolejki](service-bus-resource-manager-namespace-queue.md)
* [Tworzenie przestrzeni nazw usługi Service Bus z tematem i subskrypcją](service-bus-resource-manager-namespace-topic.md)
* [Tworzenie przestrzeni nazw usługi Service Bus z regułą kolejki i autoryzacji](service-bus-resource-manager-namespace-auth-rule.md)
* [Tworzenie przestrzeni nazw usługi Service Bus przy użyciu tematu, subskrypcji i reguły](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>Wdrażanie przy użyciu programu PowerShell

Poniższa procedura opisuje sposób używania programu PowerShell do wdrożenia szablonu usługi Azure Resource Manager, który pokazuje tworzenie przestrzeni nazw usługi Service Bus w warstwie standardowa i kolejki w obrębie tej przestrzeni nazw. Ten przykład jest oparty na [tworzenie przestrzeni nazw usługi Service Bus z kolejką](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) szablonu. Przybliżony przepływ pracy jest w następujący sposób:

1. Instalowanie programu PowerShell.
2. Tworzenie szablonu i (opcjonalnie) w pliku parametrów.
3. W programie PowerShell Zaloguj się do konta platformy Azure.
4. Utwórz nową grupę zasobów, jeśli nie istnieje.
5. Testowanie wdrażania.
6. Jeśli to konieczne, ustaw tryb wdrożenia.
7. Wdróż szablon.

Aby uzyskać pełne informacje na temat wdrażania szablonów usługi Azure Resource Manager, zobacz [wdrażanie zasobów przy użyciu szablonów usługi Azure Resource Manager][Deploy resources with Azure Resource Manager templates].

### <a name="install-powershell"></a>Instalowanie programu PowerShell

Zainstaluj program Azure PowerShell, postępując zgodnie z instrukcjami wyświetlanymi w [wprowadzenie do programu Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Tworzenie szablonu

Klonowanie repozytorium lub kopiowania [201-servicebus Utwórz — kolejkę](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) szablon z serwisu GitHub:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
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

### <a name="create-a-parameters-file-optional"></a>Tworzenie pliku parametrów (opcjonalnie)

Aby skorzystać z pliku parametrów opcjonalnych, skopiuj [201-servicebus Utwórz — kolejkę](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) pliku. Zastąp wartość `serviceBusNamespaceName` nazwą przestrzeni nazw magistrali usług, które chcesz utworzyć w tym wdrożeniu i zastąp wartość `serviceBusQueueName` o nazwie kolejka, w której chcesz utworzyć.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Logowanie do platformy Azure i ustaw subskrypcję platformy Azure

W wierszu polecenia programu PowerShell uruchom następujące polecenie:

```powershell
Connect-AzAccount
```

Monit logowania do konta platformy Azure. Po zalogowaniu, uruchom następujące polecenie, aby wyświetlić dostępne subskrypcji:

```powershell
Get-AzSubscription
```

To polecenie zwraca listę dostępnych subskrypcji platformy Azure. Wybierz subskrypcję dla bieżącej sesji, uruchamiając następujące polecenie. Zastąp `<YourSubscriptionId>` o identyfikatorze GUID subskrypcji platformy Azure, której chcesz użyć:

```powershell
Set-AzContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Ustaw grupę zasobów

Jeśli nie masz istniejącego zasobu, grupy, Utwórz nową grupę zasobów za pomocą **New AzResourceGroup** polecenia. Podaj nazwę grupy zasobów i lokalizacji, w której chcesz użyć. Na przykład:

```powershell
New-AzResourceGroup -Name MyDemoRG -Location "West US"
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

Sprawdź poprawność wdrożenia, uruchamiając `Test-AzResourceGroupDeployment` polecenia cmdlet. Podczas testowania wdrożenia, dokładnie tak jak podczas wykonywania wdrożenia należy podać parametry.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Tworzenie wdrożenia

Aby utworzyć nowe wdrożenie, uruchom `New-AzResourceGroupDeployment` polecenia cmdlet i podaj wymagane parametry po wyświetleniu monitu. Parametry zawierają nazwę dla danego wdrożenia, nazwę grupy zasobów, a ścieżka lub adres URL do pliku szablonu. Jeśli **tryb** parametr nie jest określony, wartością domyślną **przyrostowe** jest używany. Aby uzyskać więcej informacji, zobacz [przyrostowe i pełne wdrożeń](../azure-resource-manager/deployment-modes.md).

Następujące polecenie wyświetli monit o podanie trzech parametrów w oknie programu PowerShell:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Aby określić plik parametrów zamiast tego, użyj następującego polecenia:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Umożliwia także wbudowane parametry po uruchomieniu polecenia cmdlet wdrażania usług. Polecenie to w następujący sposób:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Do uruchomienia [pełną](../azure-resource-manager/deployment-modes.md) wdrożenia, ustawić **tryb** parametr **Complete**:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia
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
Teraz wiesz, podstawowych przepływów pracy i polecenia do wdrożenia szablonu usługi Azure Resource Manager. Aby uzyskać szczegółowe informacje skorzystaj z następujących linków:

* [Omówienie usługi Azure Resource Manager][Azure Resource Manager overview]
* [Wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i programu Azure PowerShell][Deploy resources with Azure Resource Manager templates]
* [Tworzenie szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
* [Typy zasobów elementu Microsoft.ServiceBus](/azure/templates/microsoft.servicebus/allversions)

[Azure Resource Manager overview]: ../azure-resource-manager/resource-group-overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/resource-group-template-deploy.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus
