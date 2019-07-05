---
title: Tworzenie subskrypcji tematu usługi Azure Service Bus i regułę za pomocą szablonu usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Tworzenie przestrzeni nazw usługi Service Bus przy użyciu tematu, subskrypcji i reguły za pomocą szablonu usługi Azure Resource Manager
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 9e0aaf58-0214-4bca-bd00-d29c08f9b1bc
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/23/2019
ms.author: spelluru
ms.openlocfilehash: 8be34a85cb1a1863ee8a78c7b97af66627612fea
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444744"
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Tworzenie przestrzeni nazw usługi Service Bus przy użyciu tematu, subskrypcji i reguły za pomocą szablonu usługi Azure Resource Manager

W tym artykule przedstawiono sposób używania szablonu usługi Azure Resource Manager, który tworzy przestrzeń nazw usługi Service Bus przy użyciu tematu, subskrypcji i reguły (filtr). W niniejszym artykule wyjaśniono, jak określić, jakie zasoby są wdrożone i jak zdefiniować parametry, które są określone, gdy wdrożenie jest wykonywane. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [tworzenia usługi Azure Resource Manager][Authoring Azure Resource Manager templates].

Aby uzyskać więcej informacji na temat praktyk i wzorców dla konwencje nazewnictwa zasobów platformy Azure, zobacz [zalecane konwencje nazewnictwa dla zasobów platformy Azure][Recommended naming conventions for Azure resources].

Aby uzyskać kompletny szablon, zobacz [przestrzeń nazw magistrali usług przy użyciu tematu, subskrypcji i reguły][Service Bus namespace with topic, subscription, and rule] szablonu.

> [!NOTE]
> Następujące szablony usługi Azure Resource Manager są dostępne do pobrania i wdrożenia.
> 
> * [Tworzenie przestrzeni nazw usługi Service Bus z regułą kolejki i autoryzacji](service-bus-resource-manager-namespace-auth-rule.md)
> * [Tworzenie przestrzeni nazw usługi Service Bus przy użyciu kolejki](service-bus-resource-manager-namespace-queue.md)
> * [Tworzenie przestrzeni nazw usługi Service Bus](service-bus-resource-manager-namespace.md)
> * [Tworzenie przestrzeni nazw usługi Service Bus z tematem i subskrypcją](service-bus-resource-manager-namespace-topic.md)
> 
> Aby sprawdzić najnowsze szablony, odwiedź stronę [szablony szybkiego startu platformy Azure][Azure Quickstart Templates] galerii i wyszukiwania dla usługi Service Bus.
> 
> 

## <a name="what-do-you-deploy"></a>Co to są wdrażane?

Korzystając z tego szablonu możesz wdrożyć przestrzeni nazw usługi Service Bus przy użyciu tematu, subskrypcji i reguły (filtr).

[Tematy usługi Service Bus i subskrypcje](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) zapewniają formę komunikacji, jeden do wielu w *publikowania/subskrybowania* wzorca. Podczas korzystania z tematów i subskrypcji składniki aplikacji rozproszonej nie komunikują się bezpośrednio ze sobą, lecz wymieniają komunikaty za pośrednictwem tematu, który działa jako pośrednik. Subskrypcja tematu przypomina wirtualną kolejkę, która odbiera kopie komunikatów wysłanych do tematu. Filtr subskrypcji umożliwia określenie, które komunikaty wysyłane do tematu są wyświetlane w subskrypcji określonego tematu.

## <a name="what-are-rules-filters"></a>Co to są zasady (filtrów)?

W wielu scenariuszach wiadomości, które mają szczególne cechy muszą zostać przetworzone w różny sposób. Aby włączyć to niestandardowe przetwarzanie, można skonfigurować subskrypcje, aby znaleźć wiadomości, które mają szczególne właściwości, a następnie dokonaj zmian do tych właściwości. Mimo że subskrypcje usługi Service Bus, zobacz wszystkie wiadomości wysyłane do tematu, można kopiować tylko podzbiór tych komunikatów do kolejki wirtualne subskrypcji. To osiągnąć, za pomocą filtrów subskrypcji. Aby dowiedzieć się więcej na temat reguł (filtry), zobacz [reguły i akcje](service-bus-queues-topics-subscriptions.md#rules-and-actions).

Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk:

[![Wdrażanie na platformie Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

Za pomocą usługi Azure Resource Manager, zdefiniuj parametry dla wartości, które chcesz określić podczas wdrażania szablonu. Szablon zawiera sekcję o nazwie `Parameters` obejmującą wszystkie wartości parametrów. Na podstawie projektu, który jest wdrażany lub opartych na środowisku, w których jest wdrażany z, należy zdefiniować parametr dla tych wartości, które różnią się. Nie należy definiować parametrów dla wartości, które pozostają niezmienione. Każda wartość parametru używana w szablonie definiuje wdrażane zasoby.

Szablon definiuje następujące parametry:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
Nazwa tworzonej przestrzeni nazw usługi Service Bus.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName
Nazwa tematu, utworzone w przestrzeni nazw usługi Service Bus.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName
Nazwa subskrypcji utworzonych w przestrzeni nazw usługi Service Bus.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```
### <a name="servicebusrulename"></a>serviceBusRuleName
Nazwa rule(filter) utworzone w przestrzeni nazw usługi Service Bus.

```json
   "serviceBusRuleName": {
   "type": "string",
  }
```
### <a name="servicebusapiversion"></a>serviceBusApiVersion
Wersja interfejsu API usługi Service Bus szablonu.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```
## <a name="resources-to-deploy"></a>Zasoby wymagające wdrożenia
Tworzy standardową przestrzeń nazw usługi Service Bus typu **komunikatów**z tematów i subskrypcji oraz reguły.

```json
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "sku": {
            "name": "Standard",
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filterType": "SqlFilter",
                        "sqlFilter": {
                            "sqlExpression": "StoreName = 'Store1'",
                            "requiresPreprocessing": "false"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

Składnię JSON i właściwości, zobacz [przestrzenie nazw](/azure/templates/microsoft.servicebus/namespaces), [tematy](/azure/templates/microsoft.servicebus/namespaces/topics), [subskrypcje](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions), i [reguły](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions/rules).

## <a name="commands-to-run-deployment"></a>Polecenia umożliwiające uruchomienie wdrożenia
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```powershell
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujący temat, który pokazuje, jak utworzyć filtr subskrypcji: [Tworzenie przestrzeni nazw usługi Service Bus przy użyciu tematu, subskrypcji i reguły za pomocą szablonu usługi Azure Resource Manager](service-bus-resource-manager-namespace-topic-with-rule.md)

Dowiedz się, jak zarządzać tymi zasobami, wyświetlając następujące artykuły:

* [Zarządzanie usługą Azure Service Bus](service-bus-management-libraries.md)
* [Zarządzanie usługą Service Bus za pomocą programu PowerShell](service-bus-manage-with-ps.md)
* [Zarządzanie zasobami usługi Service Bus za pomocą Eksploratora usługi Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Recommended naming conventions for Azure resources]: ../guidance/guidance-naming-conventions.md
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md

