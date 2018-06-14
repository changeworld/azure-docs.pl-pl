---
title: Za pomocą programu PowerShell do zarządzania zasobami Azure Event Hubs | Dokumentacja firmy Microsoft
description: Użyj modułu programu PowerShell do tworzenia i obsługi usługi Event Hubs
services: event-hubs
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: de86b8241166d4e0bd03beb22550464457e3db5e
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2017
ms.locfileid: "26782221"
---
# <a name="use-powershell-to-manage-event-hubs-resources"></a>Zarządzanie zasobami usługi Event Hubs przy użyciu programu PowerShell

Microsoft Azure PowerShell jest środowiskiem skryptów, które umożliwia kontrolowanie i automatyzowania wdrażania i zarządzania usługami Azure. W tym artykule opisano sposób użycia [modułu programu PowerShell Menedżera zasobów centrów zdarzeń](/powershell/module/azurerm.eventhub) obsługiwać i zarządzać nim jednostek usługi Event Hubs (obszary nazw, centra zdarzeń w poszczególnych i grupy konsumentów) przy użyciu konsoli programu Azure PowerShell lokalnej lub skryptu.

Można również zarządzać zasobami usługi Event Hubs przy użyciu szablonów usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz artykuł [tworzenie przestrzeni nazw usługi Event Hubs z grupy koncentratora i odbiorców zdarzeń przy użyciu szablonu usługi Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem, potrzebne są następujące:

* Subskrypcja platformy Azure. Aby uzyskać więcej informacji na temat uzyskiwania subskrypcji, zobacz [opcji zakupu][purchase options], [oferty Członkowskie][member offers], lub [bezpłatne konto][free account].
* Komputer z programem Azure PowerShell. Aby uzyskać instrukcje, zobacz [wprowadzenie do poleceń cmdlet programu Azure PowerShell](/powershell/azure/get-started-azureps).
* Ogólny opis skryptów programu PowerShell, pakietami NuGet i .NET Framework.

## <a name="get-started"></a>Rozpoczęcie pracy

Pierwszym krokiem jest zalogować się do swojego konta platformy Azure i subskrypcji platformy Azure przy użyciu programu PowerShell. Postępuj zgodnie z instrukcjami [wprowadzenie do poleceń cmdlet programu Azure PowerShell](/powershell/azure/get-started-azureps) logować się do konta platformy Azure, następnie pobrać i uzyskać dostęp do zasobów w Twojej subskrypcji platformy Azure.

## <a name="provision-an-event-hubs-namespace"></a>Należy do przestrzeni nazw usługi Event Hubs

Podczas pracy z przestrzeni nazw usługi Event Hubs, można użyć [Get-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/get-azurermeventhubnamespace), [AzureRmEventHubNamespace nowy](/powershell/module/azurerm.eventhub/new-azurermeventhubnamespace), [AzureRmEventHubNamespace Usuń](/powershell/module/azurerm.eventhub/remove-azurermeventhubnamespace), i [AzureRmEventHubNamespace zestaw](/powershell/module/azurerm.eventhub/set-azurermeventhubnamespace) polecenia cmdlet.

W tym przykładzie powoduje utworzenie kilku zmiennych lokalnych w skrypcie; `$Namespace` i `$Location`.

* `$Namespace`to nazwa przestrzeni nazw usługi Event Hubs, z którą chcemy, aby pracować.
* `$Location`Określa centrum danych, w którym firma Microsoft będzie udostępniać przestrzeni nazw.
* `$CurrentNamespace`przechowuje przestrzeń nazw odwołania, którego możemy pobrać (lub Utwórz).

W skrypcie rzeczywiste `$Namespace` i `$Location` mogą być przekazywane jako parametry.

Ta część skrypt wykonuje następujące czynności:

1. Próbuje pobrać przestrzeni nazw usługi Event Hubs przy użyciu określonej nazwy.
2. Przestrzeń nazw zostanie znaleziony, raporty, co znaleziono.
3. Jeśli przestrzeń nazw nie zostanie znaleziony, tworzy przestrzeń nazw, a następnie pobiera nowo utworzonej przestrzeni nazw.

    ```powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzureRmEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
    }
    ```

## <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Aby utworzyć Centrum zdarzeń, sprawdzania przestrzeni nazw przy użyciu skryptu w poprzedniej sekcji. Następnie należy użyć [AzureRmEventHub nowy](/powershell/module/azurerm.eventhub/new-azurermeventhub) , aby utworzyć Centrum zdarzeń:

```powershell
# Check if event hub already exists
$CurrentEH = Get-AzureRMEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName

if($CurrentEH)
{
    Write-Host "The event hub $EventHubName already exists in the $Location region:"
    # Report what was found
    Get-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
}
else
{
    Write-Host "The $EventHubName event hub does not exist."
    Write-Host "Creating the $EventHubName event hub in the $Location region..."
    New-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -Location $Location -MessageRetentionInDays 3
    $CurrentEH = Get-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
    Write-Host "The $EventHubName event hub in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="create-a-consumer-group"></a>Tworzenie grupy odbiorców

Aby utworzyć grupy odbiorców w ramach Centrum zdarzeń, wykonaj przestrzeni nazw i zdarzenia koncentratora sprawdza w poprzedniej sekcji za pomocą skryptów. Następnie należy użyć [AzureRmEventHubConsumerGroup nowy](/powershell/module/azurerm.eventhub/new-azurermeventhubconsumergroup) , aby utworzyć grupy odbiorców w ramach Centrum zdarzeń. Na przykład:

```powershell
# Check if consumer group already exists
$CurrentCG = Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName -ErrorAction Ignore

if($CurrentCG)
{
    Write-Host "The consumer group $ConsumerGroupName in event hub $EventHubName already exists in the $Location region:"
    # Report what was found
    Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
}
else
{
    Write-Host "The $ConsumerGroupName consumer group does not exist."
    Write-Host "Creating the $ConsumerGroupName consumer group in the $Location region..."
    New-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
    $CurrentCG = Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
    Write-Host "The $ConsumerGroupName consumer group in event hub $EventHubName in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

#### <a name="set-user-metadata"></a>Zestaw metadanych użytkownika

Po wykonaniu skrypty w poprzednich sekcjach, można użyć [AzureRmEventHubConsumerGroup zestaw](/powershell/module/azurerm.eventhub/set-azurermeventhubconsumergroup) polecenia cmdlet, aby zaktualizować właściwości grupy odbiorców, jak w poniższym przykładzie:

```powershell
# Set some user metadata on the CG
Write-Host "Setting the UserMetadata field to 'Testing'"
Set-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName -UserMetadata "Testing"
# Show result
Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
```

## <a name="remove-event-hub"></a>Usuń Centrum zdarzeń

Aby usunąć centra zdarzeń został utworzony, można użyć `Remove-*` poleceń cmdlet, jak w poniższym przykładzie:

```powershell
# Clean up
Remove-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
Remove-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
Remove-AzureRmEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
```

## <a name="next-steps"></a>Kolejne kroki

- Zobacz pełną dokumentację modułu programu PowerShell Menedżera zasobów centrów zdarzeń [tutaj](/powershell/module/azurerm.eventhub). Ta strona zawiera listę wszystkich dostępnych poleceń cmdlet.
- Aby uzyskać informacje dotyczące korzystania z szablonów usługi Azure Resource Manager, zobacz artykuł [tworzenie przestrzeni nazw usługi Event Hubs z grupy koncentratora i odbiorców zdarzeń przy użyciu szablonu usługi Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub.md).
- Informacje o [biblioteki zarządzania .NET centra zdarzeń](event-hubs-management-libraries.md).

[purchase options]: http://azure.microsoft.com/pricing/purchase-options/
[member offers]: http://azure.microsoft.com/pricing/member-offers/
[free account]: http://azure.microsoft.com/pricing/free-trial/
