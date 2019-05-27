---
title: Utwórz regułę autoryzacji usługi Service Bus przy użyciu szablonu usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Utwórz regułę autoryzacji usługi Service Bus dla przestrzeni nazw i kolejki przy użyciu szablonu usługi Azure Resource Manager
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 7f1443a0-5fa8-4d90-8637-1a977ef0b1f0
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: f2c82c8ff353889f06dfc1c2ff5c3f316013c54b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66171240"
---
# <a name="create-a-service-bus-authorization-rule-for-namespace-and-queue-using-an-azure-resource-manager-template"></a>Utwórz regułę autoryzacji usługi Service Bus dla przestrzeni nazw i kolejki przy użyciu szablonu usługi Azure Resource Manager

W tym artykule przedstawiono sposób używania szablonu usługi Azure Resource Manager, który tworzy [reguły autoryzacji](service-bus-authentication-and-authorization.md#shared-access-signature-authentication) dla przestrzeni nazw usługi Service Bus i kolejki. W niniejszym artykule wyjaśniono, jak określić, jakie zasoby są wdrożone i jak zdefiniować parametry, które są określone, gdy wdrożenie jest wykonywane. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [tworzenia usługi Azure Resource Manager][Authoring Azure Resource Manager templates].

Aby uzyskać kompletny szablon, zobacz [szablonu reguły autoryzacji usługi Service Bus] [ Service Bus auth rule template] w witrynie GitHub.

> [!NOTE]
> Następujące szablony usługi Azure Resource Manager są dostępne do pobrania i wdrożenia.
> 
> * [Tworzenie przestrzeni nazw usługi Service Bus](service-bus-resource-manager-namespace.md)
> * [Tworzenie przestrzeni nazw usługi Service Bus przy użyciu kolejki](service-bus-resource-manager-namespace-queue.md)
> * [Tworzenie przestrzeni nazw usługi Service Bus z tematem i subskrypcją](service-bus-resource-manager-namespace-topic.md)
> * [Tworzenie przestrzeni nazw usługi Service Bus przy użyciu tematu, subskrypcji i reguły](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Aby sprawdzić najnowsze szablony, odwiedź stronę [szablony szybkiego startu platformy Azure] [ Azure Quickstart Templates] galerii i wyszukaj **usługi Service Bus**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-will-you-deploy"></a>Co chcesz wdrożyć?

Korzystając z tego szablonu możesz wdrożyć usługi Service Bus reguły autoryzacji dla przestrzeni nazw i jednostki obsługi komunikatów (w tym przypadku kolejki).

Ten szablon używa [sygnatury dostępu współdzielonego (SAS)](service-bus-sas.md) do uwierzytelniania. Sygnatury dostępu Współdzielonego umożliwia aplikacjom uwierzytelnianie usługi Service Bus przy użyciu klucza dostępu, skonfigurowanym w przestrzeni nazw lub na jednostki obsługi komunikatów (kolejki lub tematu) za pomocą którego określone prawa są skojarzone. Można następnie użyć tego klucza do wygenerowania tokenu sygnatury dostępu Współdzielonego, której klienci z kolei mogą używać do uwierzytelniania w usłudze Service Bus.

Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk:

[![Wdrażanie na platformie Azure](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

Przy użyciu usługi Azure Resource Manager można zdefiniować parametry dla wartości, które mają zostać uwzględnione podczas wdrażania szablonu. Szablon zawiera sekcję o nazwie `Parameters` zawierający wszystkie wartości parametru. Należy zdefiniować parametr dla tych wartości, które będą się różnić, na podstawie projektu, który jest wdrażany lub opartych na środowisku, w których jest wdrażany z. Nie należy definiować parametrów dla wartości, które zawsze pozostają takie same. Każda wartość parametru używana w szablonie definiuje wdrażane zasoby.

Szablon zawiera definicje następujących parametrów.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
Nazwa tworzonej przestrzeni nazw usługi Service Bus.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="namespaceauthorizationrulename"></a>namespaceAuthorizationRuleName
Nazwa reguły autoryzacji dla przestrzeni nazw.

```json
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName
Nazwa kolejki w przestrzeni nazw usługi Service Bus.

```json
"serviceBusQueueName": {
"type": "string"
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
Tworzy standardową przestrzeń nazw usługi Service Bus typu **komunikatów**i reguły autoryzacji usługi Service Bus dla przestrzeni nazw i jednostki.

```json
"resources": [
        {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[variables('location')]",
            "kind": "Messaging",
            "sku": {
                "name": "Standard",
            },
            "resources": [
                {
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusQueueName')]",
                    "type": "Queues",
                    "dependsOn": [
                        "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('serviceBusQueueName')]"
                    },
                    "resources": [
                        {
                            "apiVersion": "[variables('sbVersion')]",
                            "name": "[parameters('queueAuthorizationRuleName')]",
                            "type": "authorizationRules",
                            "dependsOn": [
                                "[parameters('serviceBusQueueName')]"
                            ],
                            "properties": {
                                "Rights": ["Listen"]
                            }
                        }
                    ]
                }
            ]
        }, {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[variables('namespaceAuthRuleName')]",
            "type": "Microsoft.ServiceBus/namespaces/authorizationRules",
            "dependsOn": ["[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"],
            "location": "[resourceGroup().location]",
            "properties": {
                "Rights": ["Send"]
            }
        }
    ]
```

Składnię JSON i właściwości, zobacz [przestrzenie nazw](/azure/templates/microsoft.servicebus/namespaces), [kolejek](/azure/templates/microsoft.servicebus/namespaces/queues), i [reguł autoryzacji](/azure/templates/microsoft.servicebus/namespaces/authorizationrules).

## <a name="commands-to-run-deployment"></a>Polecenia umożliwiające uruchomienie wdrożenia
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu i wdrożeniu zasobów przy użyciu usługi Azure Resource Manager, Dowiedz się, jak można nimi zarządzać, wyświetlając następujące artykuły:

* [Zarządzanie usługą Service Bus za pomocą programu PowerShell](service-bus-powershell-how-to-provision.md)
* [Zarządzanie zasobami usługi Service Bus za pomocą Eksploratora usługi Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases)
* [Usługa Service Bus, uwierzytelnianie i autoryzacja](service-bus-authentication-and-authorization.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Service Bus auth rule template]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/
