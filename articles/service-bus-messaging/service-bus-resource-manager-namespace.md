---
title: Tworzenie nazw komunikatów magistrali usług przy użyciu szablonu usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Szablon usługi Azure Resource Manager umożliwia tworzenie przestrzeni nazw usługi magistrali komunikatów
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 04/11/2018
ms.author: sethm
ms.openlocfilehash: e7e811b86d1ea0454b964fb297cb05b6a4734abd
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31411838"
---
# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Tworzenie przestrzeni nazw usługi Service Bus przy użyciu szablonu usługi Azure Resource Manager

W tym artykule opisano, jak szablon Menedżera zasobów Azure, która tworzy przestrzeń nazw magistrali usług typu **wiadomości** z wersji Standard. Artykuł również definiuje parametry, które są określone, aby wykonać wdrożenie. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [Tworzenie szablonów usługi Azure Resource Manager][Authoring Azure Resource Manager templates].

Zakończenie szablonu, zobacz [szablonu przestrzeni nazw usługi Service Bus] [ Service Bus namespace template] w witrynie GitHub.

> [!NOTE]
> Następujące szablony usługi Azure Resource Manager są dostępne do pobrania i wdrożenia. 
> 
> * [Tworzenie przestrzeni nazw usługi Service Bus z kolejki](service-bus-resource-manager-namespace-queue.md)
> * [Tworzenie przestrzeni nazw usługi Service Bus z tematów i subskrypcji](service-bus-resource-manager-namespace-topic.md)
> * [Tworzenie przestrzeni nazw usługi Service Bus z regułą kolejki i autoryzacji](service-bus-resource-manager-namespace-auth-rule.md)
> * [Tworzenie przestrzeni nazw usługi Service Bus z tematu, subskrypcji i reguły](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Aby sprawdzić najnowsze szablony, odwiedź stronę [szablonów Szybki Start Azure] [ Azure Quickstart Templates] galerii i wyszukiwania dla usługi Service Bus.
> 
> 

## <a name="what-will-you-deploy"></a>Co chcesz wdrożyć?

W przypadku tego szablonu wdrażania przestrzeni nazw usługi Service Bus z [standardowa lub Premium](https://azure.microsoft.com/pricing/details/service-bus/) jednostki SKU.

Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk:

[![Wdrażanie na platformie Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

Przy użyciu usługi Azure Resource Manager można zdefiniować parametry dla wartości, które mają zostać uwzględnione podczas wdrażania szablonu. Szablon zawiera sekcję o nazwie `Parameters` zawiera wszystkie wartości parametru. Parametr powinien obejmować wartości, które różnią się w zależności od wdrażanego projektu lub środowiska, w którym odbywa się wdrożenie. Nie należy definiować parametrów dla wartości, które pozostają niezmienione. Każda wartość parametru używana w szablonie definiuje wdrażane zasoby.

Ten szablon definiuje następujące parametry:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

Nazwa przestrzeni nazw usługi Service Bus do utworzenia.

```json
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebussku"></a>serviceBusSKU

Nazwa usługi Service Bus [SKU](https://azure.microsoft.com/pricing/details/service-bus/) do utworzenia.

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

Szablon definiuje wartości, które są dozwolone dla tego parametru (standardowy lub Premium). Jeśli wartość nie zostanie określona, Menedżer zasobów przypisuje wartość domyślną (Standard).

Aby uzyskać więcej informacji na temat cen usługi Service Bus, zobacz [usługi Service Bus cennik i rozliczenia][Service Bus pricing and billing].

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

Tworzy standardowe przestrzeni nazw usługi Service Bus typu **wiadomości**.

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
Po utworzeniu i wdrożeniu zasobów przy użyciu usługi Azure Resource Manager, Dowiedz się, jak nimi zarządzać przeczytaj następujące artykuły:

* [Zarządzanie usługi Service Bus przy użyciu programu PowerShell](service-bus-manage-with-ps.md)
* [Zarządzanie zasobami usługi Service Bus za pomocą Eksploratora magistrali usług](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace template]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Service Bus pricing and billing]: service-bus-pricing-billing.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
