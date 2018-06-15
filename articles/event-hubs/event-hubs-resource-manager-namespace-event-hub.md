---
title: Tworzenie grupy przestrzeni nazw i konsumentów Azure Event Hubs przy użyciu szablonu | Dokumentacja firmy Microsoft
description: Tworzenie przestrzeni nazw usługi Event Hubs przy użyciu Centrum zdarzeń i grupy odbiorców za pomocą szablonów usługi Azure Resource Manager
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 04/30/2018
ms.author: sethm
ms.openlocfilehash: 4b4dc5be9697bb96aec658fccbdf13b299e79e9e
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
ms.locfileid: "32311264"
---
# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>Tworzenie przestrzeni nazw usługi Event Hubs z grupy koncentratora i odbiorców zdarzeń przy użyciu szablonu usługi Azure Resource Manager

W tym artykule pokazano, jak szablon Menedżera zasobów Azure umożliwia tworzenie przestrzeni nazw typu [usługi Event Hubs](event-hubs-what-is-event-hubs.md)— ze sprzętem i Centrum zdarzeń jednej grupie użytkowników. Artykuł przedstawia sposób do definiowania zasobów, do których są wdrażane i sposób definiowania parametrów, które są określone, gdy wdrożenie jest wykonywane. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

Aby uzyskać informacje dotyczące tworzenia szablonów, zobacz [szablonów Authoring Azure Resource Manager][Authoring Azure Resource Manager templates].

Zakończenie szablonu, zobacz [szablonu zdarzenia koncentratora i konsumentów grupy] [ Event Hub and consumer group template] w witrynie GitHub.

> [!NOTE]
> Aby sprawdzić najnowsze szablony, odwiedź galerię [Szablony szybkiego startu platformy Azure][Azure Quickstart Templates] i wyszukaj hasło Event Hubs.
> 
> 

## <a name="what-will-you-deploy"></a>Co chcesz wdrożyć?

W przypadku tego szablonu można wdrożyć do przestrzeni nazw usługi Event Hubs z Centrum zdarzeń i grupy odbiorców.

Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk:

[![Wdrażanie na platformie Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

Przy użyciu usługi Azure Resource Manager można zdefiniować parametry dla wartości, które mają zostać uwzględnione podczas wdrażania szablonu. Szablon zawiera sekcję o nazwie `Parameters` obejmującą wszystkie wartości parametrów. Należy zdefiniować parametr dla wartości, które będą się różnić na podstawie projektu, który jest wdrażany lub opartych na środowisku, w której wdrażasz. Nie należy definiować parametrów dla wartości, które pozostają niezmienione. Każda wartość parametru szablonu określa zasoby, które zostały wdrożone.

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

Nazwa grupy odbiorców utworzone dla Centrum zdarzeń.

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

Tworzy nazw typu **EventHubs**, z Centrum zdarzeń i grupy odbiorców:

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

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

## <a name="next-steps"></a>Kolejne kroki

Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Omówienie usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
