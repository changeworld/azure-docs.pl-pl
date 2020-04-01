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
ms.date: 03/30/2020
ms.author: spelluru
ms.openlocfilehash: b08253104eeb61f6bb09fde507473d235a996494
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422678"
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

Aby automatycznie uruchomić wdrożenie, kliknij następujący przycisk: Utwórz nową grupę zasobów dla wdrożenia, aby można było łatwo wyczyścić później.

[![Wdrażanie na platformie Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

1. Wybierz **powiadomienia** u góry, aby zobaczyć stan wdrożenia. Poczekaj, aż wdrożenie zakończy się pomyślnie. Następnie wybierz pozycję **Przejdź do grupy zasobów** w komunikacie z powiadomieniem, aby przejść do strony dla grupy zasobów zawierającej obszar nazw usługi Service Bus. 

    ![Powiadomienie z wdrożenia](./media/service-bus-resource-manager-namespace-queue/notification.png)
2. Upewnij się, że na liście zasobów jest widoczna przestrzeń nazw usługi Service Bus. 

    ![Grupa zasobów — obszar nazw](./media/service-bus-resource-manager-namespace-queue/resource-group-namespace.png)
3. Wybierz obszar nazw z listy, aby wyświetlić stronę **Obszar nazw magistrali usług.** 

## <a name="cleanup-resources"></a>Oczyszczanie zasobów

1. W witrynie Azure Portal przejdź do strony **Grupa zasobów** dla grupy zasobów.
2. Wybierz pozycję **Usuń grupę zasobów** na pasku narzędzi. 
3. Wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**. 

    ![Grupa zasobów — usuwanie](./media/service-bus-resource-manager-namespace-queue/resource-group-delete.png)

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
