---
title: Tworzenie subskrypcji i reguły tematu usługi Service Bus przy użyciu szablonu platformy Azure
description: Tworzenie obszaru nazw usługi Service Bus z tematem, subskrypcją i regułą przy użyciu szablonu usługi Azure Resource Manager
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
ms.date: 11/27/2019
ms.author: spelluru
ms.openlocfilehash: 6cbaf447dfcf06ae11f2282d7d847978297af8b8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384895"
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Tworzenie obszaru nazw usługi Service Bus z tematem, subskrypcją i regułą przy użyciu szablonu usługi Azure Resource Manager

W tym artykule pokazano, jak używać szablonu usługi Azure Resource Manager, który tworzy obszar nazw usługi Service Bus z tematem, subskrypcją i regułą (filtrem). W tym artykule wyjaśniono, jak określić, które zasoby są wdrażane i jak zdefiniować parametry, które są określone podczas wdrażania. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [Tworzenie szablonów usługi Azure Resource Manager][Authoring Azure Resource Manager templates].

Aby uzyskać więcej informacji na temat praktyki i wzorców na temat konwencji nazewnictwa zasobów platformy Azure, zobacz [Zalecane konwencje nazewnictwa zasobów platformy Azure.][Recommended naming conventions for Azure resources]

Aby uzyskać pełny szablon, zobacz [obszar nazw usługi Service Bus z tematem, subskrypcją i szablonem reguły.][Service Bus namespace with topic, subscription, and rule]

> [!NOTE]
> Następujące szablony usługi Azure Resource Manager są dostępne do pobrania i wdrożenia.
> 
> * [Tworzenie obszaru nazw usługi Service Bus z regułą kolejki i autoryzacji](service-bus-resource-manager-namespace-auth-rule.md)
> * [Tworzenie obszaru nazw usługi Service Bus z kolejką](service-bus-resource-manager-namespace-queue.md)
> * [Tworzenie przestrzeni nazw usługi Service Bus](service-bus-resource-manager-namespace.md)
> * [Tworzenie obszaru nazw usługi Service Bus z tematem i subskrypcją](service-bus-resource-manager-namespace-topic.md)
> 
> Aby sprawdzić dostępność najnowszych szablonów, odwiedź galerię [szablonów szybki start platformy Azure][Azure Quickstart Templates] i wyszukaj usługę Service Bus.

## <a name="what-do-you-deploy"></a>Co wdrażasz?

Za pomocą tego szablonu można wdrożyć obszar nazw usługi Service Bus z tematem, subskrypcją i regułą (filtrem).

[Tematów usługi Service Bus i subskrypcji](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) zapewniają jeden do wielu form komunikacji, w wzorzec *publikowania/subskrybowania.* Podczas korzystania z tematów i subskrypcji składniki aplikacji rozproszonej nie komunikują się bezpośrednio ze sobą, zamiast tego wymieniają wiadomości za pośrednictwem tematu, który działa jako pośrednik. Subskrypcja tematu przypomina kolejkę wirtualną, która odbiera kopie wiadomości, które zostały wysłane do tematu. Filtr w ramach subskrypcji umożliwia określenie, które wiadomości wysyłane do tematu powinny być wyświetlane w ramach subskrypcji określonego tematu.

## <a name="what-are-rules-filters"></a>Co to są reguły (filtry)?

W wielu scenariuszach wiadomości, które mają określone cechy muszą być przetwarzane na różne sposoby. Aby włączyć to przetwarzanie niestandardowe, można skonfigurować subskrypcje, aby znaleźć komunikaty, które mają określone właściwości, a następnie wykonać modyfikacje tych właściwości. Mimo że subskrypcje usługi Service Bus widzą wszystkie wiadomości wysyłane do tematu, można skopiować tylko podzbiór tych wiadomości do kolejki subskrypcji wirtualnej. Jest to realizowane przy użyciu filtrów subskrypcji. Aby dowiedzieć się więcej o regułach (filtrach), zobacz [Reguły i akcje](service-bus-queues-topics-subscriptions.md#rules-and-actions).

Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk:

[![Wdrażanie na platformie Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

Za pomocą usługi Azure Resource Manager zdefiniuj parametry wartości, które chcesz określić podczas wdrażania szablonu. Szablon zawiera sekcję o nazwie `Parameters` obejmującą wszystkie wartości parametrów. Zdefiniuj parametr dla tych wartości, które różnią się w zależności od wdrażanego projektu lub na podstawie środowiska, w które wdrażasz. Nie należy definiować parametrów dla wartości, które pozostają niezmienione. Każda wartość parametru używana w szablonie definiuje wdrażane zasoby.

Szablon definiuje następujące parametry:

### <a name="servicebusnamespacename"></a>usługaNamespaceName

Nazwa obszaru nazw usługi Service Bus do utworzenia.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>usługaBusTopicName

Nazwa tematu utworzonego w obszarze nazw usługi Service Bus.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>usługaBusSubscriptionName

Nazwa subskrypcji utworzonej w obszarze nazw usługi Service Bus.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusrulename"></a>usługaBusRuleName

Nazwa reguły(filtru) utworzonej w obszarze nazw usługi Service Bus.

```json
   "serviceBusRuleName": {
   "type": "string",
  }
```

### <a name="servicebusapiversion"></a>usługaBusApiVersion

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

Tworzy standardową przestrzeń nazw usługi Service Bus typu **Wiadomości**, z tematem i subskrypcją oraz regułami.

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

Aby zapoznać się ze składnią i właściwościami JSON, zobacz [obszary nazw,](/azure/templates/microsoft.servicebus/namespaces) [tematy,](/azure/templates/microsoft.servicebus/namespaces/topics) [subskrypcje](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions)i [reguły](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions/rules).

## <a name="commands-to-run-deployment"></a>Polecenia umożliwiające uruchomienie wdrożenia

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zarządzać tymi zasobami, przeglądając następujące artykuły:

* [Zarządzanie usługą Azure Service Bus](service-bus-management-libraries.md)
* [Zarządzanie magistralą usług za pomocą programu PowerShell](service-bus-manage-with-ps.md)
* [Zarządzanie zasobami usługi Service Bus za pomocą Eksploratora magistrali usług](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Recommended naming conventions for Azure resources]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
