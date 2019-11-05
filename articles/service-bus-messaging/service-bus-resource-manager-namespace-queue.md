---
title: Tworzenie Azure Service Bus przestrzeni nazw i kolejki przy użyciu szablonu Azure Resource Manager Microsoft Docs
description: Tworzenie Service Bus przestrzeni nazw i kolejki przy użyciu szablonu Azure Resource Manager
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: a6bfb5fd-7b98-4588-8aa1-9d5f91b599b6
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/23/2019
ms.author: spelluru
ms.openlocfilehash: 1c6a4202b944b2eb95008964eb1040f176645334
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73482389"
---
# <a name="create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Tworzenie Service Bus przestrzeni nazw i kolejki przy użyciu szablonu Azure Resource Manager

W tym artykule pokazano, jak używać szablonu Azure Resource Manager, który tworzy przestrzeń nazw Service Bus i kolejkę w tej przestrzeni nazw. W tym artykule wyjaśniono, jak określić, które zasoby są wdrażane, oraz jak definiować parametry, które są określone podczas wdrażania. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [Tworzenie szablonów Azure Resource Manager][Authoring Azure Resource Manager templates].

Aby zapoznać się z pełnym szablonem, zapoznaj się z [obszarem nazw Service Bus i szablonem kolejki][Service Bus namespace and queue template] w witrynie GitHub.

> [!NOTE]
> Następujące szablony Azure Resource Manager są dostępne do pobrania i wdrożenia.
> 
> * [Tworzenie przestrzeni nazw Service Bus przy użyciu reguły kolejki i autoryzacji](service-bus-resource-manager-namespace-auth-rule.md)
> * [Tworzenie przestrzeni nazw Service Bus przy użyciu tematu i subskrypcji](service-bus-resource-manager-namespace-topic.md)
> * [Tworzenie przestrzeni nazw Service Bus](service-bus-resource-manager-namespace.md)
> * [Tworzenie przestrzeni nazw Service Bus z tematem, subskrypcją i regułą](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Aby sprawdzić najnowsze szablony, odwiedź Galerię [szablonów szybkiego startu platformy Azure][Azure Quickstart Templates] i Wyszukaj **Service Bus**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-will-you-deploy"></a>Co chcesz wdrożyć?

Za pomocą tego szablonu należy wdrożyć przestrzeń nazw Service Bus z kolejką.

[Kolejki Service Bus](service-bus-queues-topics-subscriptions.md#queues) oferują dostarczanie komunikatów First In, First Out (FIFO) do jednego lub większej liczby konkurujących klientów.

Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk:

[![Wdrażanie na platformie Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

Przy użyciu usługi Azure Resource Manager można zdefiniować parametry dla wartości, które mają zostać uwzględnione podczas wdrażania szablonu. Szablon zawiera sekcję o nazwie `Parameters`, która zawiera wszystkie wartości parametrów. Należy zdefiniować parametr dla tych wartości, które będą się różnić w zależności od wdrażanego projektu lub w oparciu o środowisko, w którym wdrażasz. Nie należy definiować parametrów dla wartości, które zawsze pozostają takie same. Każda wartość parametru używana w szablonie definiuje wdrażane zasoby.

Szablon zawiera definicje następujących parametrów.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
Nazwa przestrzeni nazw Service Bus, która ma zostać utworzona.

```json
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName
Nazwa kolejki utworzonej w przestrzeni nazw Service Bus.

```json
"serviceBusQueueName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion
Wersja szablonu interfejsu API Service Bus.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```

## <a name="resources-to-deploy"></a>Zasoby wymagające wdrożenia
Tworzy standardową przestrzeń nazw Service Bus typu **Messaging**z kolejką.

```json
{
    "resources": [{
        "apiVersion": "2017-04-01",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
            "name": "Standard"
        },
        "properties": {},
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]"
            }
        }]
    }]
}
```

Aby poznać składnię i właściwości JSON, zobacz [przestrzenie nazw](/azure/templates/microsoft.servicebus/namespaces) i [kolejki](/azure/templates/microsoft.servicebus/namespaces/queues).

## <a name="commands-to-run-deployment"></a>Polecenia umożliwiające uruchomienie wdrożenia
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z poniższym tematem, który pokazuje, jak utworzyć regułę autoryzacji dla przestrzeni nazw/kolejki: [Tworzenie reguły autoryzacji Service Bus dla przestrzeni nazw i kolejki przy użyciu szablonu Azure Resource Manager](service-bus-resource-manager-namespace-auth-rule.md)

Dowiedz się, jak zarządzać tymi zasobami, wyświetlając następujące artykuły:

* [Zarządzanie usługą Service Bus za pomocą programu PowerShell](service-bus-manage-with-ps.md)
* [Zarządzanie zasobami Service Bus za pomocą Eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
