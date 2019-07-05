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
ms.date: 06/21/2019
ms.author: spelluru
ms.openlocfilehash: 4162775153a48dc8ea28e06f7c99f9927b9c602a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444769"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Tworzenie przestrzeni nazw usługi Service Bus przy użyciu szablonu usługi Azure Resource Manager

Dowiedz się, jak wdrożyć szablonu usługi Azure Resource Manager w celu utworzenia przestrzeni nazw usługi Service Bus. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb. Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [dokumentacji usługi Azure Resource Manager](/azure/azure-resource-manager/).

Poniższe szablony są również dostępne dla tworzenia przestrzeni nazw usługi Service Bus:

* [Tworzenie przestrzeni nazw usługi Service Bus przy użyciu kolejki](./service-bus-resource-manager-namespace-queue.md)
* [Tworzenie przestrzeni nazw usługi Service Bus z tematem i subskrypcją](./service-bus-resource-manager-namespace-topic.md)
* [Tworzenie przestrzeni nazw usługi Service Bus z regułą kolejki i autoryzacji](./service-bus-resource-manager-namespace-auth-rule.md)
* [Tworzenie przestrzeni nazw usługi Service Bus przy użyciu tematu, subskrypcji i reguły](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-a-service-bus-namespace"></a>Utwórz przestrzeń nazw magistrali usług

W tym przewodniku Szybki Start użyjesz [istniejący szablon usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json) z [szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/):

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

Aby uzyskać więcej przykładów szablonu, zobacz [szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular).

Aby utworzyć przestrzeń nazw magistrali usług przez wdrożenie szablonu:

1. Wybierz **wypróbuj** z następujący blok kodu i postępuj zgodnie z instrukcjami, aby zarejestrować się w usłudze Azure Cloud shell.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    Nazwa grupy zasobów jest nazwa przestrzeni nazw magistrali usług przy użyciu **rg** dołączane.

2. Wybierz przycisk **Kopiuj**, aby skopiować skrypt programu PowerShell.
3. Kliknij prawym przyciskiem myszy konsoli shell, a następnie wybierz **Wklej**.

Może potrwać kilka minut, aby utworzyć Centrum zdarzeń.

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

Aby wyświetlić przestrzeń nazw magistrali wdrożonej usługi, możesz Otwórz grupę zasobów w witrynie Azure portal lub użyj następującego skryptu programu Azure PowerShell. Jeśli w usłudze Cloud shell jest wciąż otwarty, nie trzeba kopiowania/wykonywania pierwszym i drugim wierszem poniższy skrypt.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

Program Azure PowerShell jest używany do wdrożenia szablonu w ramach tego samouczka. Aby uzyskać inne metody wdrażania szablonu zobacz:

* [Za pomocą witryny Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md).
* [Przy użyciu wiersza polecenia platformy Azure](../azure-resource-manager/resource-group-template-deploy-cli.md).
* [Za pomocą interfejsu API REST](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów. Jeśli w usłudze Cloud shell jest wciąż otwarty, nie trzeba kopiowania/wykonywania pierwszym i drugim wierszem poniższy skrypt.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule utworzono przestrzeń nazw usługi Service Bus. Zobacz inne Przewodniki Szybki Start dowiesz się, jak utworzyć kolejki, tematy/subskrypcje i ich używać:

* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Rozpoczynanie pracy z obsługą tematów usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
