---
title: Tworzenie zasobów usługi Azure Service Bus przy użyciu szablonów
description: Automatyzacja tworzenia zasobów usługi Service Bus za pomocą szablonów usługi Azure Resource Manager
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
ms.openlocfilehash: 9bc784ee57b9bde393408cbefa9a197aebc59b08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264462"
---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Tworzenie zasobów usługi Service Bus przy użyciu szablonów usługi Azure Resource Manager

W tym artykule opisano sposób tworzenia i wdrażania zasobów usługi Service Bus przy użyciu szablonów usługi Azure Resource Manager, programu PowerShell i dostawcy zasobów usługi Service Bus.

Szablony usługi Azure Resource Manager ułatwiają definiowanie zasobów do wdrożenia dla rozwiązania oraz określanie parametrów i zmiennych, które umożliwiają wprowadzanie wartości dla różnych środowisk. Szablon jest napisany w JSON i składa się z wyrażeń, które można użyć do konstruowania wartości dla wdrożenia. Aby uzyskać szczegółowe informacje na temat pisania szablonów usługi Azure Resource Manager oraz omówienia formatu [szablonu, zobacz strukturę i składnię szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md).

> [!NOTE]
> Przykłady w tym artykule pokazują, jak używać usługi Azure Resource Manager do tworzenia obszaru nazw usługi Service Bus i jednostki obsługi wiadomości (kolejki). Aby uzyskać inne przykłady szablonów, odwiedź [galerię szablonów szybki start platformy Azure][Azure Quickstart Templates gallery] i wyszukaj usługę Service **Bus**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-bus-resource-manager-templates"></a>Szablony Menedżera zasobów usługi Service Bus

Te szablony usługi Azure Resource Manager usługi Service Bus są dostępne do pobrania i wdrożenia. Kliknij poniższe łącza, aby uzyskać szczegółowe informacje o każdym z nich, z łączami do szablonów w usłudze GitHub:

* [Tworzenie przestrzeni nazw usługi Service Bus](service-bus-resource-manager-namespace.md)
* [Tworzenie obszaru nazw usługi Service Bus z kolejką](service-bus-resource-manager-namespace-queue.md)
* [Tworzenie obszaru nazw usługi Service Bus z tematem i subskrypcją](service-bus-resource-manager-namespace-topic.md)
* [Tworzenie obszaru nazw usługi Service Bus z regułą kolejki i autoryzacji](service-bus-resource-manager-namespace-auth-rule.md)
* [Tworzenie obszaru nazw usługi Service Bus z tematem, subskrypcją i regułą](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>Wdrażanie przy użyciu programu PowerShell

W poniższej procedurze opisano sposób używania programu PowerShell do wdrażania szablonu usługi Azure Resource Manager, który tworzy obszar nazw usługi Service Bus warstwy standardowej, oraz kolejki w tym obszarze nazw. W tym przykładzie jest oparty na [Tworzenie obszaru nazw usługi Service Bus z szablonem kolejki.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) Przybliżony przepływ pracy jest następujący:

1. Zainstaluj program PowerShell.
2. Utwórz szablon i (opcjonalnie) plik parametrów.
3. W programie PowerShell zaloguj się do swojego konta platformy Azure.
4. Utwórz nową grupę zasobów, jeśli jej nie ma.
5. Przetestuj wdrożenie.
6. W razie potrzeby ustaw tryb wdrażania.
7. Wdrażanie szablonu.

Aby uzyskać pełne informacje dotyczące wdrażania szablonów usługi Azure Resource Manager, zobacz [Wdrażanie zasobów za pomocą szablonów usługi Azure Resource Manager][Deploy resources with Azure Resource Manager templates].

### <a name="install-powershell"></a>Instalowanie programu PowerShell

Zainstaluj program Azure PowerShell, postępując zgodnie z instrukcjami w [programie Wprowadzenie do programu Azure PowerShell.](/powershell/azure/get-started-azureps)

### <a name="create-a-template"></a>Tworzenie szablonu

Sklonuj repozytorium lub skopiuj szablon [201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) z gitHub:

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

Aby użyć pliku parametrów opcjonalnych, skopiuj plik [201-servicebus-create-queue.](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) Zastąp `serviceBusNamespaceName` wartość nazwą obszaru nazw usługi Service Bus, który chcesz utworzyć `serviceBusQueueName` w tym wdrożeniu, i zastąp wartość nazwą kolejki, którą chcesz utworzyć.

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

Aby uzyskać więcej informacji, zobacz [parametry](../azure-resource-manager/templates/parameter-files.md) artykułu.

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Zaloguj się na platformę Azure i ustaw subskrypcję platformy Azure

Z monitu programu PowerShell uruchom następujące polecenie:

```powershell
Connect-AzAccount
```

Zostanie wyświetlony monit o zalogowanie się do konta platformy Azure. Po zalogowaniu się uruchom następujące polecenie, aby wyświetlić dostępne subskrypcje:

```powershell
Get-AzSubscription
```

To polecenie zwraca listę dostępnych subskrypcji platformy Azure. Wybierz subskrypcję dla bieżącej sesji, uruchamiając następujące polecenie. Zamień `<YourSubscriptionId>` identyfikator GUID dla subskrypcji platformy Azure, której chcesz użyć:

```powershell
Set-AzContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Ustawianie grupy zasobów

Jeśli nie masz istniejącej grupy zasobów, utwórz nową grupę zasobów za pomocą polecenia **New-AzResourceGroup.** Podaj nazwę grupy zasobów i lokalizację, której chcesz użyć. Przykład:

```powershell
New-AzResourceGroup -Name MyDemoRG -Location "West US"
```

Jeśli się powiedzie, zostanie wyświetlone podsumowanie nowej grupy zasobów.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Testowanie wdrożenia

Sprawdź poprawność wdrożenia, `Test-AzResourceGroupDeployment` uruchamiając polecenie cmdlet. Podczas testowania wdrożenia należy podać parametry dokładnie tak, jak podczas wykonywania wdrożenia.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Tworzenie wdrożenia

Aby utworzyć nowe wdrożenie, `New-AzResourceGroupDeployment` uruchom polecenie cmdlet i podaj niezbędne parametry po wyświetleniu monitu. Parametry obejmują nazwę wdrożenia, nazwę grupy zasobów oraz ścieżkę lub adres URL do pliku szablonu. Jeśli parametr **Mode** nie jest określony, używana jest wartość domyślna **przyrostowa.** Aby uzyskać więcej informacji, zobacz [Przyrostowe i kompletne wdrożenia](../azure-resource-manager/templates/deployment-modes.md).

Następujące polecenie monituje o trzy parametry w oknie programu PowerShell:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Zamiast tego należy określić plik parametrów, należy użyć następującego polecenia:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Po uruchomieniu polecenia cmdlet wdrożenia można również użyć parametrów wbudowanych. Polecenie to wygląda następująco:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Aby uruchomić [pełne](../azure-resource-manager/templates/deployment-modes.md) wdrożenie, ustaw parametr **Tryb** na **Zakończ:**

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia
Jeśli zasoby zostaną pomyślnie wdrożone, podsumowanie wdrożenia jest wyświetlane w oknie programu PowerShell:

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

## <a name="next-steps"></a>Następne kroki
Teraz widzisz podstawowy przepływ pracy i polecenia dotyczące wdrażania szablonu usługi Azure Resource Manager. Aby uzyskać bardziej szczegółowe informacje, odwiedź następujące łącza:

* [Omówienie usługi Azure Resource Manager][Azure Resource Manager overview]
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)][Deploy resources with Azure Resource Manager templates]
* [Tworzenie szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Typy zasobów usługi Microsoft.ServiceBus](/azure/templates/microsoft.servicebus/allversions)

[Azure Resource Manager overview]: ../azure-resource-manager/management/overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/templates/deploy-powershell.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus
