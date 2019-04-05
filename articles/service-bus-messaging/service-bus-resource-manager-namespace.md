---
title: Tworzenie przestrzeni nazw komunikatów usługi Service Bus, przy użyciu szablonu usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Szablon usługi Azure Resource Manager umożliwia utworzenie przestrzeni nazw komunikatów usługi Service Bus
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/23/2019
ms.author: spelluru
ms.openlocfilehash: 4471c9d5b6c09bcf4d9100cccfa725f36cf9a3f8
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045086"
---
# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Tworzenie przestrzeni nazw usługi Service Bus przy użyciu szablonu usługi Azure Resource Manager
W tym przewodniku Szybki Start utworzysz szablonu usługi Azure Resource Manager, który tworzy przestrzeń nazw usługi Service Bus typu **komunikatów** z **standardowa** jednostki SKU. Artykuł definiuje również parametry, które są określone dla wykonywania wdrożenia. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb. Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [Tworzenie szablonów usługi Azure Resource Manager][Authoring Azure Resource Manager templates]. Aby uzyskać kompletny szablon, zobacz [szablon przestrzeni nazw usługi Service Bus] [ Service Bus namespace template] w witrynie GitHub.

> [!NOTE]
> Następujące szablony usługi Azure Resource Manager są dostępne do pobrania i wdrożenia. 
> 
> * [Tworzenie przestrzeni nazw usługi Service Bus przy użyciu kolejki](service-bus-resource-manager-namespace-queue.md)
> * [Tworzenie przestrzeni nazw usługi Service Bus z tematem i subskrypcją](service-bus-resource-manager-namespace-topic.md)
> * [Tworzenie przestrzeni nazw usługi Service Bus z regułą kolejki i autoryzacji](service-bus-resource-manager-namespace-auth-rule.md)
> * [Tworzenie przestrzeni nazw usługi Service Bus przy użyciu tematu, subskrypcji i reguły](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Aby sprawdzić najnowsze szablony, odwiedź stronę [szablony szybkiego startu platformy Azure] [ Azure Quickstart Templates] galerii i wyszukiwania dla usługi Service Bus.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="quick-deployment"></a>Szybkie wdrażanie
Do uruchomienia przykładu bez się zapisywania wszelkich JSON, a następnie uruchamiając polecenie programu PowerShell/interfejsu wiersza polecenia, wybierz poniższy przycisk:

[![Deploy na platformie Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

Aby utworzyć i wdrożyć szablon ręcznie, wykonaj następujące sekcje w tym artykule.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków tego przewodnika Szybki start jest potrzebna subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

Jeśli chcesz używać **programu Azure PowerShell** do wdrożenia szablonu usługi Resource Manager [Instalowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

Jeśli chcesz używać **wiersza polecenia platformy Azure** do wdrożenia szablonu usługi Resource Manager [interfejsu wiersza polecenia platformy Azure Zainstaluj]( /cli/azure/install-azure-cli).

## <a name="create-the-resource-manager-template-json"></a>Utwórz kod JSON szablonu usługi Resource Manager 
Utwórz plik JSON o nazwie **MyServiceBusNamespace.json** o następującej zawartości: 

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
        "serviceBusSku": {
            "type": "string",
            "allowedValues": [
                "Basic",
                "Standard",
                "Premium"
            ],
            "defaultValue": "Standard",
            "metadata": {
                "description": "The messaging tier for service Bus namespace"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('serviceBusSku')]"
            }
        }
    ]
}
```

Ten szablon tworzy standardową przestrzeń nazw usługi Service Bus. Składnię JSON i właściwości, zobacz [przestrzenie nazw](/azure/templates/microsoft.servicebus/namespaces) odwołanie do szablonu.

## <a name="create-the-parameters-json"></a>Utwórz parametry JSON
Szablon utworzony w poprzednim kroku zawiera sekcję o nazwie `Parameters`. Można zdefiniować parametry dla tych wartości, które różnią się na podstawie projektu wdrażania lub w zależności od środowiska docelowego. Ten szablon definiuje następujące parametry: **serviceBusNamespaceName**, **serviceBusSku**, i **lokalizacji**. Aby dowiedzieć się więcej na temat jednostek SKU usługi Service Bus, zobacz [jednostek usługi Service Bus SKU](https://azure.microsoft.com/pricing/details/service-bus/) do utworzenia.

Utwórz plik JSON o nazwie **MyServiceBusNamespace-Parameters.json** o następującej zawartości: 

> [!NOTE] 
> Określ nazwę przestrzeni nazw usługi Service Bus. 


```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "value": "<Specify a name for the Service Bus namespace>"
    },
    "serviceBusSku": {
      "value": "Standard"
    },
    "location": {
        "value": "East US"
    }
  }
}
```


## <a name="use-azure-powershell-to-deploy-the-template"></a>Wdrażanie szablonu przy użyciu programu Azure PowerShell

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
1. Uruchom program Azure PowerShell

2. Uruchom następujące polecenia, aby zalogować się na platformie Azure:

   ```azurepowershell
   Login-AzAccount
   ```
3. Jeśli masz wydawać następujące polecenia, aby ustawić bieżącego kontekstu subskrypcji:

   ```azurepowershell
   Select-AzSubscription -SubscriptionName "<YourSubscriptionName>" 
   ```

### <a name="deploy-resources"></a>Wdrażanie zasobów
Aby wdrożyć zasoby przy użyciu programu Azure PowerShell, przejdź do folderu, w którym zapisano pliki w formacie JSON i uruchom następujące polecenia:

> [!IMPORTANT]
> Określ nazwę grupy zasobów platformy Azure jako wartość dla $resourceGroupName przed uruchomieniem polecenia. 

1. Zadeklaruj zmienną dla nazwy grupy zasobów, a następnie określić wartość. 

    ```azurepowershell
    $resourceGroupName = "<Specify a name for the Azure resource group>"
    ```
2. Utwórz grupę zasobów platformy Azure.

    ```azurepowershell
    New-AzResourceGroup $resourceGroupName -location 'East US'
    ```
3. Wdrażanie szablonu usługi Resource Manager. Określ nazwy samego wdrożenia i grupy zasobów, plik JSON dla szablonu pliku JSON dla parametrów

    ```azurepowershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName $resourceGroupName -TemplateFile MyServiceBusNamespace.json -TemplateParameterFile MyServiceBusNamespace-Parameters.json
    ```

## <a name="use-azure-cli-to-deploy-the-template"></a>Wdrażanie szablonu przy użyciu wiersza polecenia platformy Azure

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

1. Uruchom następujące polecenia, aby zalogować się na platformie Azure:

    ```azurecli
    az login
    ```
2. Ustawianie kontekstu bieżącej subskrypcji. Zastąp wartość `MyAzureSub` nazwą subskrypcji platformy Azure, której chcesz użyć:

    ```azurecli
    az account set --subscription <Name of your Azure subscription>
    ``` 

### <a name="deploy-resources"></a>Wdrażanie zasobów
Wdrażanie zasobów przy użyciu wiersza polecenia platformy Azure, przejdź do folderu, plikami JSON i uruchom następujące polecenia:

> [!IMPORTANT]
> Określ nazwę dla grupy zasobów platformy Azure w grupie az polecenia create. .

1. Utwórz grupę zasobów platformy Azure. 
    ```azurecli
    az group create --name <YourResourceGroupName> --location eastus
    ```

2. Wdrażanie szablonu usługi Resource Manager. Określ nazwy grupy zasobów, wdrażania, plik JSON dla szablonu, plik JSON dla parametrów.

    ```azurecli
    az group deployment create --name <Specify a name for the deployment> --resource-group <YourResourceGroupName> --template-file MyServiceBusNamespace.json --parameters @MyServiceBusNamespace-Parameters.json
    ```

## <a name="next-steps"></a>Kolejne kroki
W tym artykule utworzono przestrzeń nazw usługi Service Bus. Zobacz inne Przewodniki Szybki Start dowiesz się, jak utworzyć kolejki, tematy/subskrypcje i ich używać: 

- [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
- [Wprowadzenie do tematów usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace template]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Service Bus pricing and billing]: https://azure.microsoft.com/pricing/details/service-bus/
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
