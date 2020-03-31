---
title: Tworzenie obszaru nazw usługi Azure Service Bus przy użyciu szablonu
description: Tworzenie obszaru nazw usługi Service Bus Messaging za pomocą szablonu usługi Azure Resource Manager
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
ms.date: 06/21/2019
ms.author: spelluru
ms.openlocfilehash: 5febdd63ab6f854ca3244f8449f6f715a75e735f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264479"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Tworzenie obszaru nazw usługi Service Bus przy użyciu szablonu usługi Azure Resource Manager

Dowiedz się, jak wdrożyć szablon usługi Azure Resource Manager w celu utworzenia obszaru nazw usługi Service Bus. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb. Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [dokumentację usługi Azure Resource Manager](/azure/azure-resource-manager/).

Następujące szablony są również dostępne do tworzenia obszarów nazw usługi Service Bus:

* [Tworzenie obszaru nazw usługi Service Bus z kolejką](./service-bus-resource-manager-namespace-queue.md)
* [Tworzenie obszaru nazw usługi Service Bus z tematem i subskrypcją](./service-bus-resource-manager-namespace-topic.md)
* [Tworzenie obszaru nazw usługi Service Bus z regułą kolejki i autoryzacji](./service-bus-resource-manager-namespace-auth-rule.md)
* [Tworzenie obszaru nazw usługi Service Bus z tematem, subskrypcją i regułą](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="create-a-service-bus-namespace"></a>Tworzenie obszaru nazw magistrali usług

W tym przewodniku Szybki start można użyć [istniejącego szablonu Usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json) z [szablonów szybki start platformy Azure:](https://azure.microsoft.com/resources/templates/)

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

Aby znaleźć więcej przykładów szablonów, zobacz [Szablony szybki start platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular).

Aby utworzyć obszar nazw magistrali usług przez wdrożenie szablonu:

1. Wybierz **pozycję Wypróbuj z** następującego bloku kodu, a następnie postępuj zgodnie z instrukcjami, aby zalogować się do powłoki usługi Azure Cloud.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    Nazwa grupy zasobów jest nazwą obszaru nazw magistrali usług z **dołączeną rg.**

2. Wybierz przycisk **Kopiuj**, aby skopiować skrypt programu PowerShell.
3. Kliknij prawym przyciskiem myszy konsolę powłoki, a następnie wybierz polecenie **Wklej**.

Utworzenie centrum zdarzeń zajmuje kilka chwil.

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

Aby wyświetlić wdrożony obszar nazw magistrali usług, można otworzyć grupę zasobów z witryny Azure Portal lub użyć następującego skryptu programu Azure PowerShell. Jeśli powłoka chmury jest nadal otwarta, nie trzeba kopiować/uruchamiać pierwszego i drugiego wiersza następującego skryptu.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

Program Azure PowerShell jest używany do wdrażania szablonu w tym samouczku. Aby uzyskać inne metody wdrażania szablonów, zobacz:

* [Za pomocą portalu Azure](../azure-resource-manager/templates/deploy-portal.md).
* [Za pomocą narzędzia Azure CLI](../azure-resource-manager/templates/deploy-cli.md).
* [Za pomocą interfejsu API REST](../azure-resource-manager/templates/deploy-rest.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów. Jeśli powłoka chmury jest nadal otwarta, nie trzeba kopiować/uruchamiać pierwszego i drugiego wiersza następującego skryptu.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono obszar nazw usługi Service Bus. Zobacz inne przewodniki Szybki start, aby dowiedzieć się, jak tworzyć kolejki, tematy/subskrypcje i używać ich:

* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Wprowadzenie do tematów usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
