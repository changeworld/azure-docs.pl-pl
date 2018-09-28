---
title: Tworzenie przestrzeni nazw komunikatów usługi Service Bus, przy użyciu szablonu usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Szablon usługi Azure Resource Manager umożliwia utworzenie przestrzeni nazw komunikatów usługi Service Bus
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 09/11/2018
ms.author: spelluru
ms.openlocfilehash: 6f3f44394ab11c1b66be3af976dbd1f7d23de96e
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405788"
---
# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Tworzenie przestrzeni nazw usługi Service Bus przy użyciu szablonu usługi Azure Resource Manager

W tym artykule opisano sposób używania szablonu usługi Azure Resource Manager, który tworzy przestrzeń nazw usługi Service Bus typu **komunikatów** przy użyciu standardowej jednostki SKU. Artykuł definiuje również parametry, które są określone dla wykonywania wdrożenia. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [Tworzenie szablonów usługi Azure Resource Manager][Authoring Azure Resource Manager templates].

Aby uzyskać kompletny szablon, zobacz [szablon przestrzeni nazw usługi Service Bus] [ Service Bus namespace template] w witrynie GitHub.

> [!NOTE]
> Następujące szablony usługi Azure Resource Manager są dostępne do pobrania i wdrożenia. 
> 
> * [Tworzenie przestrzeni nazw usługi Service Bus przy użyciu kolejki](service-bus-resource-manager-namespace-queue.md)
> * [Tworzenie przestrzeni nazw usługi Service Bus z tematem i subskrypcją](service-bus-resource-manager-namespace-topic.md)
> * [Tworzenie przestrzeni nazw usługi Service Bus z regułą kolejki i autoryzacji](service-bus-resource-manager-namespace-auth-rule.md)
> * [Tworzenie przestrzeni nazw usługi Service Bus przy użyciu tematu, subskrypcji i reguły](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Aby sprawdzić najnowsze szablony, odwiedź stronę [szablony szybkiego startu platformy Azure] [ Azure Quickstart Templates] galerii i wyszukiwania dla usługi Service Bus.
> 
> 

## <a name="what-will-you-deploy"></a>Co chcesz wdrożyć?

Korzystając z tego szablonu wdrażania przestrzeni nazw usługi Service Bus za pomocą [Standard lub Premium](https://azure.microsoft.com/pricing/details/service-bus/) jednostki SKU.

Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk:

[![Wdrażanie na platformie Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

Przy użyciu usługi Azure Resource Manager można zdefiniować parametry dla wartości, które mają zostać uwzględnione podczas wdrażania szablonu. Szablon zawiera sekcję o nazwie `Parameters` zawierający wszystkie wartości parametru. Parametr powinien obejmować wartości, które różnią się w zależności od wdrażanego projektu lub środowiska, w którym odbywa się wdrożenie. Nie należy definiować parametrów dla wartości, które pozostają niezmienione. Każda wartość parametru używana w szablonie definiuje wdrażane zasoby.

Ten szablon definiuje następujące parametry:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

Nazwa tworzonej przestrzeni nazw usługi Service Bus.

```json
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebussku"></a>serviceBusSKU

Nazwa usługi Service Bus [jednostki SKU](https://azure.microsoft.com/pricing/details/service-bus/) do utworzenia.

```json
"serviceBusSku": { 
    "type": "string", 
    "allowedValues": [ 
        "Standard",
        "Premium" 
    ], 
    "defaultValue": "Standard", 
    "metadata": { 
        "description": "The messaging tier for service Bus namespace" 
    } 

```

Szablon definiuje wartości, które są dozwolone dla tego parametru (standardowa / Premium). Jeśli nie określono wartości, Menedżer zasobów przypisuje wartość domyślną (metoda standardowa).

Aby uzyskać więcej informacji na temat cen usługi Service Bus, zobacz [usługi Service Bus, cen i rozliczeń][Service Bus pricing and billing].

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

### <a name="service-bus-namespace"></a>Przestrzeń nazw magistrali usług

Tworzy standardową przestrzeń nazw usługi Service Bus typu **komunikatów**.

```json
"resources": [
    {
        "apiVersion": "[parameters('serviceBusApiVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "Standard",
        },
        "properties": {
        }
    }
]
```

## <a name="commands-to-run-deployment"></a>Polecenia umożliwiające uruchomienie wdrożenia

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu i wdrożeniu zasobów przy użyciu usługi Azure Resource Manager, Dowiedz się, jak można nimi zarządzać, czytając następujące artykuły:

* [Zarządzanie usługą Service Bus za pomocą programu PowerShell](service-bus-manage-with-ps.md)
* [Zarządzanie zasobami usługi Service Bus za pomocą Eksploratora usługi Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace template]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Service Bus pricing and billing]: service-bus-pricing-billing.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
