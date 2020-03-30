---
title: Temat Tworzenie obszaru nazw usługi Azure Service Bus przy użyciu szablonu
description: 'Szybki start: tworzenie obszaru nazw usługi Service Bus z tematem i subskrypcją przy użyciu szablonu usługi Azure Resource Manager'
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: d3d55200-5c60-4b5f-822d-59974cafff0e
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: c94b670a33f7640d2d6f428287b3ba0fab766bc5
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384878"
---
# <a name="quickstart-create-a-service-bus-namespace-with-topic-and-subscription-using-an-azure-resource-manager-template"></a>Szybki start: tworzenie obszaru nazw usługi Service Bus z tematem i subskrypcją przy użyciu szablonu usługi Azure Resource Manager

W tym artykule pokazano, jak używać szablonu usługi Azure Resource Manager, który tworzy obszar nazw usługi Service Bus oraz temat i subskrypcję w tej przestrzeni nazw. W tym artykule wyjaśniono, jak określić, które zasoby są wdrażane i jak zdefiniować parametry, które są określone podczas wdrażania. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [Tworzenie szablonów usługi Azure Resource Manager][Authoring Azure Resource Manager templates].

Aby uzyskać pełny szablon, zobacz [obszar nazw usługi Service Bus z tematem i szablonem subskrypcji.][Service Bus namespace with topic and subscription]

> [!NOTE]
> Następujące szablony usługi Azure Resource Manager są dostępne do pobrania i wdrożenia.
> 
> * [Tworzenie przestrzeni nazw usługi Service Bus](service-bus-resource-manager-namespace.md)
> * [Tworzenie obszaru nazw usługi Service Bus z kolejką](service-bus-resource-manager-namespace-queue.md)
> * [Tworzenie obszaru nazw usługi Service Bus z regułą kolejki i autoryzacji](service-bus-resource-manager-namespace-auth-rule.md)
> * [Tworzenie obszaru nazw usługi Service Bus z tematem, subskrypcją i regułą](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Aby sprawdzić dostępność najnowszych szablonów, odwiedź galerię [szablonów szybki start platformy Azure][Azure Quickstart Templates] i wyszukaj usługę Service **Bus**.

## <a name="what-do-you-deploy"></a>Co wdrażasz?

Za pomocą tego szablonu można wdrożyć obszar nazw usługi Service Bus z tematem i subskrypcją.

[Tematów usługi Service Bus i subskrypcji](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) zapewniają jeden do wielu form komunikacji, w wzorzec *publikowania/subskrybowania.*

Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk:

[![Wdrażanie na platformie Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

Przy użyciu usługi Azure Resource Manager można zdefiniować parametry dla wartości, które mają zostać uwzględnione podczas wdrażania szablonu. Szablon zawiera sekcję `Parameters` o nazwie, która zawiera wszystkie wartości parametrów. Zdefiniuj parametr dla tych wartości, które różnią się w zależności od projektu, który wdrażasz lub na podstawie środowiska, w które wdrażasz. Nie należy definiować parametrów dla wartości, które pozostają niezmienione. Każda wartość parametru używana w szablonie definiuje wdrażane zasoby.

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

Tworzy standardową przestrzeń nazw usługi Service Bus typu **Wiadomości**, z tematem i subskrypcją.

```json
"resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
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
                "path": "[parameters('serviceBusTopicName')]",
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {}
            }]
        }]
    }]
```

Aby zapoznać się ze składnią i właściwościami JSON, zobacz [obszary nazw,](/azure/templates/microsoft.servicebus/namespaces) [tematy](/azure/templates/microsoft.servicebus/namespaces/topics)i [subskrypcje](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions).

## <a name="commands-to-run-deployment"></a>Polecenia umożliwiające uruchomienie wdrożenia

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
az group deployment create \<my-resource-group\> --name \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy utworzono i wdrożyłeś zasoby przy użyciu usługi Azure Resource Manager, dowiedz się, jak zarządzać tymi zasobami, wyświetlając te artykuły:

* [Zarządzanie magistralą usług za pomocą programu PowerShell](service-bus-manage-with-ps.md)
* [Zarządzanie zasobami usługi Service Bus za pomocą Eksploratora magistrali usług](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Service Bus namespace with topic and subscription]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/
