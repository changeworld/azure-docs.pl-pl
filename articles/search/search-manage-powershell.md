---
title: Zarządzane usługi Azure Search za pomocą skryptów programu Powershell | Dokumentacja firmy Microsoft
description: Zarządzanie usługą Azure Search za pomocą skryptów programu PowerShell. Tworzenie lub aktualizowanie usługi Azure Search i zarządzanie nimi klucze administratora usługi Azure Search
author: HeidiSteen
manager: cgronlun
tags: azure-resource-manager
services: search
ms.service: search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/15/2016
ms.author: heidist
ms.openlocfilehash: 1d0024af3bbf9edfe8c43032878a8b61e35cea9c
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39000994"
---
# <a name="manage-your-azure-search-service-with-powershell"></a>Zarządzanie usługą Azure Search przy użyciu programu PowerShell
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [Program PowerShell](search-manage-powershell.md)
> 
> 

W tym temacie opisano poleceń programu PowerShell do wykonywania wielu zadań zarządzania dla usług Azure Search. Omówimy proces tworzenia usługi wyszukiwania, skalowanie i zarządzania jego klucze interfejsu API.
Te polecenia równoległe opcji zarządzania dostępnych w [interfejsu API REST zarządzania usługi Azure Search](https://docs.microsoft.com/rest/api/searchmanagement).

## <a name="prerequisites"></a>Wymagania wstępne
* Konieczne jest posiadanie programu Azure PowerShell 1.0 lub nowszy. Aby uzyskać instrukcje, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).
* Użytkownik musi być zalogowany do subskrypcji platformy Azure w programie PowerShell, zgodnie z poniższym opisem.

Najpierw musisz zalogować się do platformy Azure za pomocą następującego polecenia:

    Connect-AzureRmAccount

W oknie dialogowym logowania do programu Microsoft Azure, należy określić adres e-mail swojego konta platformy Azure i jego hasło.

Alternatywnie możesz [logowania nieinteraktywnego przy użyciu nazwy głównej usługi](../azure-resource-manager/resource-group-authenticate-service-principal.md).

Jeśli masz wiele subskrypcji platformy Azure, musisz ustawić subskrypcję platformy Azure. Aby wyświetlić listę bieżące subskrypcje, uruchom następujące polecenie.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Aby określić subskrypcję, uruchom następujące polecenie. W poniższym przykładzie jest nazwa subskrypcji `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

## <a name="commands-to-help-you-get-started"></a>Polecenia, aby pomóc Ci rozpocząć pracę.
    $serviceName = "your-service-name-lowercase-with-dashes"
    $sku = "free" # or "basic" or "standard" for paid services
    $location = "West US"
    # You can get a list of potential locations with
    # (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Search'}).Locations
    $resourceGroupName = "YourResourceGroup" 
    # If you don't already have this resource group, you can create it with 
    # New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Register the ARM provider idempotently. This must be done once per subscription
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Search"

    # Create a new search service
    # This command will return once the service is fully created
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateUri "https://gallery.azure.com/artifact/20151001/Microsoft.Search.1.0.9/DeploymentTemplates/searchServiceDefaultTemplate.json" `
        -NameFromTemplate $serviceName `
        -Sku $sku `
        -Location $location `
        -PartitionCount 1 `
        -ReplicaCount 1

    # Get information about your new service and store it in $resource
    $resource = Get-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19

    # View your resource
    $resource

    # Get the primary admin API key
    $primaryKey = (Invoke-AzureRmResourceAction `
        -Action listAdminKeys `
        -ResourceId $resource.ResourceId `
        -ApiVersion 2015-08-19).PrimaryKey

    # Regenerate the secondary admin API Key
    $secondaryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/regenerateAdminKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action secondary).SecondaryKey

    # Create a query key for read only access to your indexes
    $queryKeyDescription = "query-key-created-from-powershell"
    $queryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/createQueryKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action $queryKeyDescription).Key

    # View your query key
    $queryKey

    # Delete query key
    Remove-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices/deleteQueryKey/$($queryKey)" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19

    # Scale your service up
    # Note that this will only work if you made a non "free" service
    # This command will not return until the operation is finished
    # It can take 15 minutes or more to provision the additional resources
    $resource.Properties.ReplicaCount = 2
    $resource | Set-AzureRmResource

    # Delete your service
    # Deleting your service will delete all indexes and data in the service
    $resource | Remove-AzureRmResource

## <a name="next-steps"></a>Następne kroki
Teraz, gdy usługa zostanie utworzony, można wykonać kolejne kroki: tworzenie [indeksu](search-what-is-an-index.md), [tworzenie zapytań względem indeksu](search-query-overview.md), a na koniec tworzenie i zarządzanie nimi własnych aplikacji wyszukiwania, która korzysta z usługi Azure Search.

* [Tworzenie indeksu usługi Azure Search w witrynie Azure portal](search-create-index-portal.md)
* [Tworzenie zapytań względem indeksu usługi Azure Search przy użyciu Eksploratora wyszukiwania w witrynie Azure portal](search-explorer.md)
* [Skonfiguruj indeksator do ładowania danych z innych usług](search-indexer-overview.md)
* [Jak używać usługi Azure Search na platformie .NET](search-howto-dotnet-sdk.md)
* [Analizowanie ruchu usługi Azure Search](search-traffic-analytics.md)

