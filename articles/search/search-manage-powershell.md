---
title: Zarządzanie za pomocą skryptów środowiska Powershell usługi Azure Search | Dokumentacja firmy Microsoft
description: Zarządzanie za pomocą skryptów środowiska PowerShell usługi Azure Search. Tworzenie lub aktualizowanie usługi Azure Search i zarządzania kluczami administratora usługi Azure Search
author: HeidiSteen
manager: cgronlun
tags: azure-resource-manager
services: search
ms.service: search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/15/2016
ms.author: heidist
ms.openlocfilehash: 8f83f2652f7bd34f53dde1674c022f8792ae7658
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="manage-your-azure-search-service-with-powershell"></a>Zarządzanie usługą Azure Search przy użyciu programu PowerShell
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [Program PowerShell](search-manage-powershell.md)
> 
> 

W tym temacie opisano poleceń programu PowerShell do wykonywania wielu zadań zarządzania dla usługi Azure Search. Firma Microsoft przeprowadzi tworzenia usługi wyszukiwania, jej skalowania i zarządzania jego kluczy interfejsu API.
Te polecenia równoległe opcji zarządzania dostępnych w [interfejsu API REST usługi Azure Search zarządzania](https://docs.microsoft.com/rest/api/searchmanagement).

## <a name="prerequisites"></a>Wymagania wstępne
* Musi mieć programu Azure PowerShell 1.0 lub nowszego. Aby uzyskać instrukcje, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).
* Należy być zalogowanym do subskrypcji platformy Azure w programie PowerShell, zgodnie z poniższym opisem.

Po pierwsze, należy najpierw zaloguj się do platformy Azure za pomocą tego polecenia:

    Connect-AzureRmAccount

Określ adres e-mail konta platformy Azure i jego hasło w oknie dialogowym logowania do programu Microsoft Azure.

Alternatywnie możesz [logowania nieinteraktywnie przy użyciu nazwy głównej usługi](../azure-resource-manager/resource-group-authenticate-service-principal.md).

Jeśli masz wiele subskrypcji Azure, należy ustawić subskrypcji platformy Azure. Aby wyświetlić listę bieżące subskrypcje, uruchom następujące polecenie.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Aby określić subskrypcję, uruchom następujące polecenie. W poniższym przykładzie Nazwa subskrypcji jest `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

## <a name="commands-to-help-you-get-started"></a>Polecenia ułatwiające rozpoczęcie pracy
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
Teraz, gdy usługa jest tworzona, należy wykonać kolejne kroki: tworzenie [indeksu](search-what-is-an-index.md), [tworzenie zapytań względem indeksu](search-query-overview.md), a na koniec tworzenie i zarządzanie nimi własnych aplikacji wyszukiwania, która używa usługi Azure Search.

* [Tworzenie indeksu usługi Azure Search w portalu Azure](search-create-index-portal.md)
* [Zapytanie indeksu usługi Azure Search przy użyciu Eksploratora wyszukiwania w portalu Azure](search-explorer.md)
* [Skonfiguruj indeksator do ładowania danych z innych usług](search-indexer-overview.md)
* [Jak używać usługi Azure Search na platformie .NET](search-howto-dotnet-sdk.md)
* [Analiza ruchu w sieci usługi Azure Search](search-traffic-analytics.md)

