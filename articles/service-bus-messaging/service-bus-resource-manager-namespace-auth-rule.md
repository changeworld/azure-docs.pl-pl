---
title: Tworzenie reguły autoryzacji usługi Service Bus przy użyciu szablonu platformy Azure
description: Tworzenie reguły autoryzacji usługi Service Bus dla obszaru nazw i kolejki przy użyciu szablonu usługi Azure Resource Manager
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
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: 1bfb2d2d946a85c1d051315fb29a5a63f7a00871
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384929"
---
# <a name="create-a-service-bus-authorization-rule-for-namespace-and-queue-using-an-azure-resource-manager-template"></a>Tworzenie reguły autoryzacji usługi Service Bus dla obszaru nazw i kolejki przy użyciu szablonu usługi Azure Resource Manager

W tym artykule pokazano, jak używać szablonu usługi Azure Resource Manager, który tworzy [regułę autoryzacji](service-bus-authentication-and-authorization.md#shared-access-signature) dla obszaru nazw usługi Service Bus i kolejki. W tym artykule wyjaśniono, jak określić, które zasoby są wdrażane i jak zdefiniować parametry, które są określone podczas wdrażania. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [Tworzenie szablonów usługi Azure Resource Manager][Authoring Azure Resource Manager templates].

Aby uzyskać pełny szablon, zobacz [szablon reguły autoryzacji usługi Service Bus][Service Bus auth rule template] w usłudze GitHub.

> [!NOTE]
> Następujące szablony usługi Azure Resource Manager są dostępne do pobrania i wdrożenia.
> 
> * [Tworzenie przestrzeni nazw usługi Service Bus](service-bus-resource-manager-namespace.md)
> * [Tworzenie obszaru nazw usługi Service Bus z kolejką](service-bus-resource-manager-namespace-queue.md)
> * [Tworzenie obszaru nazw usługi Service Bus z tematem i subskrypcją](service-bus-resource-manager-namespace-topic.md)
> * [Tworzenie obszaru nazw usługi Service Bus z tematem, subskrypcją i regułą](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Aby sprawdzić dostępność najnowszych szablonów, odwiedź galerię [szablonów szybki start platformy Azure][Azure Quickstart Templates] i wyszukaj usługę Service **Bus**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-will-you-deploy"></a>Co chcesz wdrożyć?

Za pomocą tego szablonu można wdrożyć regułę autoryzacji usługi Service Bus dla obszaru nazw i jednostki obsługi wiadomości (w tym przypadku kolejki).

Ten szablon używa [sygnatury dostępu współdzielonego (SAS)](service-bus-sas.md) do uwierzytelniania. Usługa SAS umożliwia aplikacjom uwierzytelnienie się w usłudze Service Bus przy użyciu klucza dostępu skonfigurowanego w obszarze nazw lub w jednostce obsługi wiadomości (kolejki lub tematu), z którą są skojarzone określone prawa. Następnie można użyć tego klucza do generowania tokenu sygnatury dostępu Współdzielonego, który klienci mogą z kolei używać do uwierzytelniania w usłudze Service Bus.

Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk:

[![Wdrażanie na platformie Azure](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

Przy użyciu usługi Azure Resource Manager można zdefiniować parametry dla wartości, które mają zostać uwzględnione podczas wdrażania szablonu. Szablon zawiera sekcję `Parameters` o nazwie, która zawiera wszystkie wartości parametrów. Należy zdefiniować parametr dla tych wartości, które będą się różnić w zależności od projektu, który wdrażasz lub na podstawie środowiska, w które wdrażasz. Nie należy definiować parametrów dla wartości, które zawsze pozostaną takie same. Każda wartość parametru używana w szablonie definiuje wdrażane zasoby.

Szablon zawiera definicje następujących parametrów.

### <a name="servicebusnamespacename"></a>usługaNamespaceName

Nazwa obszaru nazw usługi Service Bus do utworzenia.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="namespaceauthorizationrulename"></a>nazwaspaceAucharizacjaRuleName

Nazwa reguły autoryzacji dla obszaru nazw.

```json
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### <a name="servicebusqueuename"></a>usługaBusQueueName

Nazwa kolejki w obszarze nazw usługi Service Bus.

```json
"serviceBusQueueName": {
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

Tworzy standardową przestrzeń nazw usługi Service Bus typu **Wiadomości**i regułę autoryzacji usługi Service Bus dla obszaru nazw i encji.

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

Aby zapoznać się ze składnią i właściwościami JSON, zobacz [obszary nazw,](/azure/templates/microsoft.servicebus/namespaces)kolejki i [Reguły](/azure/templates/microsoft.servicebus/namespaces/queues) [autoryzacji](/azure/templates/microsoft.servicebus/namespaces/authorizationrules).

## <a name="commands-to-run-deployment"></a>Polecenia umożliwiające uruchomienie wdrożenia

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy utworzono i wdrożyłeś zasoby przy użyciu usługi Azure Resource Manager, dowiedz się, jak zarządzać tymi zasobami, wyświetlając te artykuły:

* [Zarządzanie magistralą usług za pomocą programu PowerShell](service-bus-powershell-how-to-provision.md)
* [Zarządzanie zasobami usługi Service Bus za pomocą Eksploratora magistrali usług](https://github.com/paolosalvatori/ServiceBusExplorer/releases)
* [Uwierzytelnianie i autoryzacja w usłudze Service Bus](service-bus-authentication-and-authorization.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Service Bus auth rule template]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/
