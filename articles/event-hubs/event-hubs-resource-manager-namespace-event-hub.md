---
title: Tworzenie grupy przestrzeni nazw i konsumentów usługi Azure Event Hubs za pomocą szablonu | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: e3a7b2a7ad866fe6c70c638dc5203b9a31c6b5b3
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426638"
---
# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>Tworzenie przestrzeni nazw usługi Event Hubs za pomocą grupy Centrum i odbiorcę zdarzeń przy użyciu szablonu usługi Azure Resource Manager
Azure Event Hubs to wysoce skalowalna platforma do strumieniowego przesyłania danych i usługa pozyskiwania danych, która umożliwia odbieranie i przetwarzanie milionów zdarzeń na sekundę. Ten przewodnik Szybki Start przedstawia sposób tworzenia Centrum zdarzeń przy użyciu szablonu usługi Azure Resource Manager.

Szablon usługi Azure Resource Manager umożliwia utworzenie przestrzeni nazw typu [usługi Event Hubs](event-hubs-what-is-event-hubs.md)z jednym Centrum zdarzeń i grupie użytkowników. W tym artykule opisano, jak zdefiniować zasoby, które są wdrażane i jak zdefiniować parametry, które są określone, gdy wdrożenie jest wykonywane. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb. Aby uzyskać informacje dotyczące tworzenia szablonów, zobacz [tworzenia usługi Azure Resource Manager][Authoring Azure Resource Manager templates].

Aby uzyskać kompletny szablon, zobacz [szablon grupy Centrum i odbiorcę zdarzeń] [ Event Hub and consumer group template] w witrynie GitHub.

> [!NOTE]
> Aby sprawdzić najnowsze szablony, odwiedź galerię [Szablony szybkiego startu platformy Azure][Azure Quickstart Templates] i wyszukaj hasło Event Hubs.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków tego przewodnika Szybki start jest potrzebna subskrypcja platformy Azure. Jeśli nie masz, [Utwórz bezpłatne konto] [], przed przystąpieniem do wykonywania.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli używasz **programu Azure PowerShell** do wdrożenia szablonu usługi Resource Manager lokalnie, należy uruchomić najnowszą wersję programu PowerShell, aby ukończyć ten przewodnik Szybki Start. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

Jeśli zdecydujesz się zainstalować i korzystać z **wiersza polecenia platformy Azure** do wdrożenia szablonu usługi Resource Manager lokalnie, ten samouczek wymaga czy korzystasz z wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby sprawdzić wersję. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="what-will-you-deploy"></a>Co chcesz wdrożyć?

Korzystając z tego szablonu możesz wdrożyć przestrzeni nazw usługi Event Hubs z Centrum zdarzeń i grupy odbiorców.

Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk:

[![Wdrażanie na platformie Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

Przy użyciu usługi Azure Resource Manager można zdefiniować parametry dla wartości, które mają zostać uwzględnione podczas wdrażania szablonu. Szablon zawiera sekcję o nazwie `Parameters` obejmującą wszystkie wartości parametrów. Należy zdefiniować parametr dla wartości, które będą się różnić na podstawie projektu, który jest wdrażany lub opartych na środowisku, do którego jest wdrażana. Nie należy definiować parametrów dla wartości, które pozostają niezmienione. Każda wartość parametru w szablonie definiuje zasoby, które są wdrażane.

Szablon definiuje następujące parametry:

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

Nazwa tworzonej przestrzeni nazw usługi Event Hubs.

```json
"eventHubNamespaceName": {
"type": "string"
}
```

### <a name="eventhubname"></a>eventHubName

Nazwa centrum zdarzeń utworzonego w przestrzeni nazw usługi Event Hubs.

```json
"eventHubName": {
"type": "string"
}
```

### <a name="eventhubconsumergroupname"></a>eventHubConsumerGroupName

Nazwa grupy konsumentów utworzone dla Centrum zdarzeń.

```json
"eventHubConsumerGroupName": {
"type": "string"
}
```

### <a name="apiversion"></a>apiVersion

Wersja interfejsu API szablonu.

```json
"apiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>Zasoby wymagające wdrożenia

Tworzy przestrzeń nazw typu **EventHubs**z Centrum zdarzeń i grupy odbiorców:

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]"
               },
               "resources":[  
                  {  
                     "apiVersion":"[variables('ehVersion')]",
                     "name":"[parameters('consumerGroupName')]",
                     "type":"ConsumerGroups",
                     "dependsOn":[  
                        "[parameters('eventHubName')]"
                     ],
                     "properties":{  

                     }
                  }
               ]
            }
         ]
      }
   ],
```

## <a name="commands-to-run-deployment"></a>Polecenia umożliwiające uruchomienie wdrożenia

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```azurepowershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```
### <a name="create-a-storage-account-for-event-processor-host"></a>Tworzenie konta magazynu dla hosta procesora zdarzeń

Host procesora zdarzeń upraszcza odbieranie zdarzeń z usługi Event Hubs przez zarządzanie punktami kontrolnymi i odbiornikami równoległymi. W przypadku obsługi punktów kontrolnych host procesora zdarzeń wymaga konta magazynu. Aby utworzyć konto magazynu i uzyskać jego klucze, uruchom następujące polecenia:

```azurepowershell-interactive
# Create a standard general purpose storage account 
New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name storage_account_name -Location eastus -SkuName Standard_LRS 
e
# Retrieve the storage account key for accessing it
Get-AzureRmStorageAccountKey -ResourceGroupName myResourceGroup -Name storage_account_name
```

Do nawiązania połączenia z centrum zdarzeń i przetwarzania zdarzeń wymagane są parametry połączenia. Aby uzyskać parametry połączenia, uruchom:

```azurepowershell-interactive
Get-AzureRmEventHubKey -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Name RootManageSharedAccessKey
```


## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

### <a name="create-a-storage-account-for-event-processor-host"></a>Tworzenie konta magazynu dla hosta procesora zdarzeń
```azurecli-interactive
# Create a general purpose standard storage account for Event Processor Host
az storage account create --name storageAccountName --resource-group myResourceGroup --location eastus2 --sku Standard_RAGRS --encryption blob

# List the storage account access keys
az storage account keys list --resource-group myResourceGroup --account-name storageAccountName

# Get namespace connection string
az eventhubs namespace authorization-rule keys list --resource-group myResourceGroup --namespace-name namespaceName --name RootManageSharedAccessKey
```

Skopiuj i wklej parametry połączenia do lokalizacji tymczasowej, takiej jak Notatnik, do użycia później.

## <a name="stream-into-event-hubs"></a>Przesyłanie strumieniowe do usługi Event Hubs

Możesz teraz rozpocząć przesyłanie strumieniowe do usługi Event Hubs. Przykłady można pobrać lub sklonować do usługi Git z [repozytorium usługi Event Hubs](https://github.com/Azure/azure-event-hubs)

### <a name="ingest-events"></a>Pozyskiwanie zdarzeń

Aby rozpocząć przesyłanie strumieniowe zdarzeń, pobierz przykład [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) z usługi GitHub lub sklonuj [repozytorium GitHub usługi Event Hubs](https://github.com/Azure/azure-event-hubs) przy użyciu następującego polecenia:

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

Przejdź do folderu \azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleSender i załaduj plik SampleSender.sln do programu Visual Studio.

Następnie dodaj pakiet NuGet [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) do projektu.

W pliku Program.cs zastąp następujące symbole zastępcze swoją nazwą centrum zdarzeń i parametrami połączenia:

```C#
private const string EhConnectionString = "Event Hubs connection string";
private const string EhEntityPath = "Event Hub name";

```

Następnie skompiluj i uruchom przykład. Wyświetlany będzie proces pozyskiwania zdarzeń do centrum zdarzeń:

![][3]

### <a name="receive-and-process-events"></a>Odbieranie i przetwarzanie zdarzeń

Teraz pobierz przykład odbiornika hosta procesora zdarzeń, który będzie odbierać wysłane komunikaty. Pobierz przykład [SampleEphReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) z usługi GitHub lub sklonuj [repozytorium GitHub usługi Event Hubs](https://github.com/Azure/azure-event-hubs) przy użyciu następującego polecenia:

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

Przejdź do folderu \azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleEphReceiver i załaduj plik rozwiązania SampleEphReceiver.sln do programu Visual Studio.

Następnie dodaj do projektu pakiety NuGet [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) i [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/).

W pliku Program.cs zastąp następujące stałe odpowiadającymi im wartościami:

```C#
private const string EventHubConnectionString = "Event Hubs connection string";
private const string EventHubName = "Event Hub name";
private const string StorageContainerName = "Storage account container name";
private const string StorageAccountName = "Storage account name";
private const string StorageAccountKey = "Storage account key";
```

Następnie skompiluj i uruchom przykład. Zobaczysz, jak przykładowa aplikacja będzie odbierać zdarzenia:

![][4]

W witrynie Azure Portal można w następujący sposób wyświetlić szybkość, z jaką zdarzenia są przetwarzane w danej przestrzeni nazw usługi Event Hubs:

![][5]


## <a name="next-steps"></a>Kolejne kroki

W tym artykule utworzoną przestrzeń nazw usługi Event Hubs, a używane przykładowe aplikacje do wysyłania i odbierania zdarzeń z Centrum zdarzeń. Instrukcje krok po kroku do wysyłania zdarzeń do (lub) odbieranie zdarzeń z Centrum zdarzeń zobacz następujące samouczki: 

1. **Wysyłanie zdarzeń do Centrum zdarzeń**: [.NET Standard](event-hubs-dotnet-standard-getstarted-send.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [środowiska Node.js ](event-hubs-node-get-started-send.md), [Przejdź](event-hubs-go-get-started-send.md), [C](event-hubs-c-getstarted-send.md)
2. **Odbieranie zdarzeń z Centrum zdarzeń**: [.NET Standard](event-hubs-dotnet-standard-getstarted-receive-eph.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md), [Java](event-hubs-java-get-started-receive-eph.md), [Python](event-hubs-python-get-started-receive.md), [ Node.js](event-hubs-node-get-started-receive.md), [Przejdź](event-hubs-go-get-started-receive-eph.md), [systemu Apache Storm](event-hubs-storm-getstarted-receive.md)

[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
