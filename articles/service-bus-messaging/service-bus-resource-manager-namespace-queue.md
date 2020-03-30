---
title: Tworzenie obszaru nazw usługi Azure Service Bus i kolejki przy użyciu szablonu platformy Azure
description: 'Szybki start: tworzenie obszaru nazw usługi Service Bus i kolejki przy użyciu szablonu usługi Azure Resource Manager'
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: a6bfb5fd-7b98-4588-8aa1-9d5f91b599b6
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: quickstart
ms.custom: subject-armqs
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 12/20/2019
ms.author: spelluru
ms.openlocfilehash: 8605dae05b7f5270513b645367248090006c04a8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384912"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Szybki start: tworzenie obszaru nazw usługi Service Bus i kolejki przy użyciu szablonu usługi Azure Resource Manager

W tym artykule pokazano, jak używać szablonu usługi Azure Resource Manager, który tworzy obszar nazw usługi Service Bus i kolejki w tym obszarze nazw. W tym artykule wyjaśniono, jak określić, które zasoby są wdrażane i jak zdefiniować parametry, które są określone podczas wdrażania. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Brak

## <a name="create-a-service-bus-namespace-and-a-queue"></a>Tworzenie obszaru nazw usługi Service Bus i kolejki

### <a name="review-the-template"></a>Przejrzyj szablon

Szablon używany w tym przewodniku Szybki start pochodzi z [szablonów szybki start platformy Azure](https://azure.microsoft.com/resources/templates/201-servicebus-create-queue).

:::code language="json" source="~/quickstart-templates/201-servicebus-create-queue/azuredeploy.json" range="1-75" highlight="31-63":::

Zasoby zdefiniowane w szablonie obejmują:

- [**Microsoft.ServiceBus/przestrzenie nazw**](/azure/templates/microsoft.servicebus/namespaces)
- [**Microsoft.ServiceBus/przestrzenie nazw/kolejki**](/azure/templates/microsoft.servicebus/namespaces/queues)

> [!NOTE]
> Następujące szablony usługi Azure Resource Manager są dostępne do pobrania i wdrożenia.
>
> * [Tworzenie obszaru nazw usługi Service Bus z regułą kolejki i autoryzacji](service-bus-resource-manager-namespace-auth-rule.md)
> * [Tworzenie obszaru nazw usługi Service Bus z tematem i subskrypcją](service-bus-resource-manager-namespace-topic.md)
> * [Tworzenie przestrzeni nazw usługi Service Bus](service-bus-resource-manager-namespace.md)
> * [Tworzenie obszaru nazw usługi Service Bus z tematem, subskrypcją i regułą](service-bus-resource-manager-namespace-topic-with-rule.md)

Więcej szablonów można znaleźć w [szablonach szybki start platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular)

### <a name="deploy-the-template"></a>Wdrożenie szablonu

Za pomocą tego szablonu można wdrożyć obszar nazw usługi Service Bus z kolejką.

[Kolejki usługi Service Bus](service-bus-queues-topics-subscriptions.md#queues) oferują dostarczanie wiadomości FIFO (First In, First Out) jednemu lub większej liczbie konkurujących konsumentów.

Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk:

[![Wdrażanie na platformie Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="next-steps"></a>Następne kroki

Zobacz następujący temat, który pokazuje, jak utworzyć regułę autoryzacji dla obszaru nazw/kolejki:

[Tworzenie reguły autoryzacji usługi Service Bus dla obszaru nazw i kolejki przy użyciu szablonu usługi Azure Resource Manager](service-bus-resource-manager-namespace-auth-rule.md)

Dowiedz się, jak zarządzać tymi zasobami, przeglądając następujące artykuły:

* [Zarządzanie magistralą usług za pomocą programu PowerShell](service-bus-manage-with-ps.md)
* [Zarządzanie zasobami usługi Service Bus za pomocą Eksploratora magistrali usług](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
