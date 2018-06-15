---
title: Utwórz subskrypcję tematu Azure Service Bus i reguł, przy użyciu szablonu usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Tworzenie przestrzeni nazw usługi Service Bus z tematu, subskrypcji i reguły przy użyciu szablonu usługi Azure Resource Manager
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 9e0aaf58-0214-4bca-bd00-d29c08f9b1bc
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 04/11/2018
ms.author: sethm
ms.openlocfilehash: 50fd07e4c979cfb415589ba721adb7998cfbe7bd
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31410713"
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Tworzenie przestrzeni nazw usługi Service Bus z tematu, subskrypcji i reguły przy użyciu szablonu usługi Azure Resource Manager

W tym artykule pokazano, jak użyć szablonu usługi Azure Resource Manager, który tworzy przestrzeń nazw usługi Service Bus z tematu, subskrypcji i reguły (filtru). Wyjaśniono sposób do określania zasobów, do których są wdrażane i sposób definiowania parametrów, które są określone, gdy wdrożenie jest wykonywane. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [Tworzenie szablonów usługi Azure Resource Manager][Authoring Azure Resource Manager templates].

Aby uzyskać więcej informacji dotyczących rozwiązań i wzorców w konwencji nazewnictwa zasobów platformy Azure, zobacz [konwencje nazewnictwa dla zasobów platformy Azure zalecane][Recommended naming conventions for Azure resources].

Zakończenie szablonu, zobacz [przestrzeni nazw usługi Service Bus z tematu, subskrypcji i reguły] [ Service Bus namespace with topic, subscription, and rule] szablonu.

> [!NOTE]
> Następujące szablony usługi Azure Resource Manager są dostępne do pobrania i wdrożenia.
> 
> * [Tworzenie przestrzeni nazw usługi Service Bus z regułą kolejki i autoryzacji](service-bus-resource-manager-namespace-auth-rule.md)
> * [Tworzenie przestrzeni nazw usługi Service Bus z kolejki](service-bus-resource-manager-namespace-queue.md)
> * [Tworzenie przestrzeni nazw usługi Service Bus](service-bus-resource-manager-namespace.md)
> * [Tworzenie przestrzeni nazw usługi Service Bus z tematów i subskrypcji](service-bus-resource-manager-namespace-topic.md)
> 
> Aby sprawdzić najnowsze szablony, odwiedź stronę [szablonów Szybki Start Azure] [ Azure Quickstart Templates] galerii i wyszukiwania dla usługi Service Bus.
> 
> 

## <a name="what-will-you-deploy"></a>Co chcesz wdrożyć?

W przypadku tego szablonu można wdrożyć przestrzeni nazw usługi Service Bus z tematu, subskrypcji i reguły (filtru).

[Tematy usługi Service Bus i subskrypcje](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) Podaj jeden do wielu formę komunikacji w *publikowania/subskrypcji* wzorca. Korzystając z tematów i subskrypcji, składniki aplikacji rozproszonej nie komunikują się bezpośrednio ze sobą, lecz wymieniają komunikaty za pośrednictwem temat, który działa jako pośrednik. Subskrypcja tematu przypomina wirtualną kolejkę, która odbiera kopie komunikatów wysłanych do tematu. Filtr subskrypcji umożliwia określenie, które komunikaty wysyłane do tematu są wyświetlane w subskrypcji określonego tematu.

## <a name="what-are-rules-filters"></a>Co to są reguły (filtry)?

W wielu scenariuszach wiadomości, które mają określone parametry muszą być przetwarzane na różne sposoby. Aby włączyć to przetwarzanie niestandardowych, można skonfigurować subskrypcje, aby znaleźć wiadomości, które zostały określone właściwości, a następnie wykonaj zmiany w tych właściwości. Mimo że subskrypcje usługi Service Bus, zobacz wszystkich wiadomości wysłanych do tematu, można kopiować tylko podzbiór tych wiadomości do kolejki subskrypcji wirtualnego. Jest to realizowane przy użyciu filtrów subskrypcji. Aby dowiedzieć się więcej na temat reguł (filtry), zobacz [reguł i akcje](service-bus-queues-topics-subscriptions.md#rules-and-actions).

Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk:

[![Wdrażanie na platformie Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

Z usługi Azure Resource Manager, należy zdefiniować parametrów dla wartości, które chcesz określić podczas wdrażania szablonu. Szablon zawiera sekcję o nazwie `Parameters` obejmującą wszystkie wartości parametrów. Parametr powinien obejmować wartości, które różnią się w zależności od wdrażanego projektu lub środowiska, w którym odbywa się wdrożenie. Nie należy definiować parametrów dla wartości, które pozostają niezmienione. Każda wartość parametru używana w szablonie definiuje wdrażane zasoby.

Szablon definiuje następujące parametry:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
Nazwa przestrzeni nazw usługi Service Bus do utworzenia.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName
Nazwa tematu utworzone w przestrzeni nazw usługi Service Bus.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName
Nazwa subskrypcji utworzone w przestrzeni nazw usługi Service Bus.

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
Tworzy standardowe przestrzeni nazw usługi Service Bus typu **wiadomości**, z tematów i subskrypcji i zasadami.

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
                        "filter": {
                            "sqlExpression": "StoreName = 'Store1'"
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
Po utworzeniu i wdrożeniu zasobów przy użyciu usługi Azure Resource Manager, Dowiedz się, jak nimi zarządzać, przeglądając następujące artykuły:

* [Zarządzanie usługi Azure Service Bus](service-bus-management-libraries.md)
* [Zarządzanie usługi Service Bus przy użyciu programu PowerShell](service-bus-manage-with-ps.md)
* [Zarządzanie zasobami usługi Service Bus za pomocą Eksploratora magistrali usług](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Recommended naming conventions for Azure resources]: ../guidance/guidance-naming-conventions.md
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md

