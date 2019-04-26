---
title: Tworzenie Centrum zdarzeń za pomocą grupy odbiorców — usługa Azure Event Hubs | Dokumentacja firmy Microsoft
description: Tworzenie przestrzeni nazw usługi Event Hubs z Centrum zdarzeń i grupy odbiorców za pomocą szablonów usługi Azure Resource Manager
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/16/2018
ms.author: shvija
ms.openlocfilehash: d5dc65dc225d11a996d9b9d3c329151a17321fb6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60343498"
---
# <a name="quickstart-create-an-event-hub-using-azure-resource-manager-template"></a>Szybki start: Tworzenie Centrum zdarzeń przy użyciu szablonu usługi Azure Resource Manager
Azure Event Hubs to platforma do pozyskiwania i strumieniowego przesyłania danych, która umożliwia odbieranie i przetwarzanie milionów zdarzeń na sekundę. Usługa Event Hubs pozwala przetwarzać i przechowywać zdarzenia, dane lub dane telemetryczne generowane przez rozproszone oprogramowanie i urządzenia. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Aby zapoznać się ze szczegółowym omówieniem usługi Event Hubs, zobacz [Omówienie usługi Event Hubs](event-hubs-about.md) i [Funkcje usługi Event Hubs](event-hubs-features.md).

W tym przewodniku Szybki Start opisano tworzenie Centrum zdarzeń przy użyciu szablonu usługi Azure Resource Manager. Szablon usługi Azure Resource Manager umożliwia utworzenie przestrzeni nazw typu [usługi Event Hubs](event-hubs-what-is-event-hubs.md)z jednym Centrum zdarzeń i grupie użytkowników. W tym artykule opisano, jak zdefiniować zasoby, które są wdrażane i jak zdefiniować parametry, które są określone, gdy wdrożenie jest wykonywane. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb. Aby uzyskać informacje dotyczące tworzenia szablonów, zobacz [tworzenia usługi Azure Resource Manager][Authoring Azure Resource Manager templates]. Aby zapoznać się z właściwościami i składnią języka JSON, których należy użyć w szablonie, zobacz [Typy zasobów Microsoft.EventHub](/azure/templates/microsoft.eventhub/allversions).

> [!NOTE]
> Aby uzyskać kompletny szablon, zobacz [szablon grupy Centrum i odbiorcę zdarzeń] [ Event Hub and consumer group template] w witrynie GitHub. Ten szablon utworzono grupę konsumentów, oprócz przestrzeń nazw Centrum zdarzeń i Centrum zdarzeń. Aby sprawdzić najnowsze szablony, odwiedź galerię [Szablony szybkiego startu platformy Azure][Azure Quickstart Templates] i wyszukaj hasło Event Hubs.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Do wykonania kroków tego przewodnika Szybki start jest potrzebna subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

Jeśli chcesz używać **programu Azure PowerShell** do wdrożenia szablonu usługi Resource Manager [Instalowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Jeśli chcesz używać **wiersza polecenia platformy Azure** do wdrożenia szablonu usługi Resource Manager [interfejsu wiersza polecenia platformy Azure Zainstaluj]( /cli/azure/install-azure-cli).

## <a name="create-the-resource-manager-template-json"></a>Utwórz kod JSON szablonu usługi Resource Manager
Utwórz plik JSON o nazwie MyEventHub.json o następującej zawartości i zapisz go do folderu (na przykład: C:\EventHubsQuickstarts\ResourceManagerTemplate).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "eventhub-namespace-name": {
            "type": "String"
        },
        "eventhub_name": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.EventHub/namespaces",
            "sku": {
                "name": "Standard",
                "tier": "Standard",
                "capacity": 1
            },
            "name": "[parameters('eventhub-namespace-name')]",
            "apiVersion": "2017-04-01",
            "location": "East US",
            "tags": {},
            "scale": null,
            "properties": {
                "isAutoInflateEnabled": false,
                "maximumThroughputUnits": 0
            },
            "dependsOn": []
        },
        {
            "type": "Microsoft.EventHub/namespaces/eventhubs",
            "name": "[concat(parameters('eventhub-namespace-name'), '/', parameters('eventhub_name'))]",
            "apiVersion": "2017-04-01",
            "location": "East US",
            "scale": null,
            "properties": {
                "messageRetentionInDays": 7,
                "partitionCount": 1,
                "status": "Active"
            },
            "dependsOn": [
                "[resourceId('Microsoft.EventHub/namespaces', parameters('eventhub-namespace-name'))]"
            ]
        }
    ]
}
```

## <a name="create-the-parameters-json"></a>Utwórz parametry JSON
Utwórz plik JSON o nazwie MyEventHub-Parameters.json, zawierający parametry szablonu usługi Azure Resource Manager. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
        "eventhub-namespace-name": {
            "value": "<specify a name for the event hub namespace>"
        },
        "eventhub_name": {
            "value": "<Specify a name for the event hub in the namespace>"
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

### <a name="provision-resources"></a>Inicjowanie zasobów
Wdrażanie/obsługę administracyjną zasobów przy użyciu programu Azure PowerShell i przejdź do folderu C:\EventHubsQuickStart\ARM\, uruchom następujące polecenia:

> [!IMPORTANT]
> Określ nazwę grupy zasobów platformy Azure jako wartość dla $resourceGroupName przed uruchomieniem polecenia. 

```azurepowershell
$resourceGroupName = "<Specify a name for the Azure resource group>"

# Create an Azure resource group
New-AzResourceGroup $resourceGroupName -location 'East US'

# Deploy the Resource Manager template. Specify the names of deployment itself, resource group, JSON file for the template, JSON file for parameters
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName $resourceGroupName -TemplateFile MyEventHub.json -TemplateParameterFile MyEventHub-Parameters.json
```

## <a name="use-azure-cli-to-deploy-the-template"></a>Wdrażanie szablonu przy użyciu wiersza polecenia platformy Azure

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Poniższe kroki nie są wymagane, jeśli uruchamiasz polecenia w usłudze Cloud Shell. Jeśli korzystasz z interfejsu wiersza polecenia lokalnie, wykonaj następujące kroki, aby zalogować się do platformy Azure i ustawić bieżącą subskrypcję:

Uruchom następujące polecenia, aby zalogować się na platformie Azure:

```azurecli
az login
```

Ustawianie kontekstu bieżącej subskrypcji. Zastąp wartość `MyAzureSub` nazwą subskrypcji platformy Azure, której chcesz użyć:

```azurecli
az account set --subscription <Name of your Azure subscription>
``` 

### <a name="provision-resources"></a>Inicjowanie zasobów
Wdrażanie zasobów przy użyciu wiersza polecenia platformy Azure, przejdź do folderu C:\EventHubsQuickStart\ARM\ i uruchom następujące polecenia:

> [!IMPORTANT]
> Określ nazwę dla grupy zasobów platformy Azure w grupie az polecenia create. .

```azurecli
# Create an Azure resource group
az group create --name <YourResourceGroupName> --location eastus

# # Deploy the Resource Manager template. Specify the names of resource group, deployment, JSON file for the template, JSON file for parameters
az group deployment create --name <Specify a name for the deployment> --resource-group <YourResourceGroupName> --template-file MyEventHub.json --parameters @MyEventHub-Parameters.json
```

Gratulacje! Użyto szablonu usługi Azure Resource Manager do utworzenia przestrzeni nazw usługi Event Hubs i Centrum zdarzeń w tej przestrzeni nazw.

## <a name="next-steps"></a>Kolejne kroki

W ramach tego artykułu utworzono przestrzeń nazw usługi Event Hubs oraz użyto aplikacji przykładowych do wysyłania zdarzeń do centrum zdarzeń i odbierania ich z niego. Aby uzyskać instrukcje krok po kroku wysyłania zdarzeń do (lub) odbieranie zdarzeń z Centrum zdarzeń, zobacz **wysyłania i odbierania zdarzeń** samouczków: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (tylko Wyślij)](event-hubs-c-getstarted-send.md)
- [Apache Storm (tylko reecive)](event-hubs-storm-getstarted-receive.md)


[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
