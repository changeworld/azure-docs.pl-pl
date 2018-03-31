---
title: Automatyzacja Azure rozwiązania Cosmos DB - zarządzania przy użyciu programu Powershell | Dokumentacja firmy Microsoft
description: Użyj programu Powershell Azure Zarządzanie konta bazy danych Azure rozwiązania Cosmos.
services: cosmos-db
author: dmakwana
manager: jhubbard
editor: ''
tags: azure-resource-manager
documentationcenter: ''
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2017
ms.author: dimakwan
ms.openlocfilehash: 12c7800ee2a823cca5394841cae86eeacc20fb1b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="create-an-azure-cosmos-db-account-using-powershell"></a>Tworzenie konta bazy danych rozwiązania Cosmos Azure przy użyciu programu PowerShell

Następujący przewodnik zawiera opis polecenia do automatyzacji zarządzania konta bazy danych DB rozwiązania Cosmos Azure przy użyciu programu Azure Powershell. Zawiera także polecenia do zarządzania klucze konta i priorytetów trybu failover w [konta bazy danych w przypadku][scaling-globally]. Aktualizacja konta bazy danych służy do modyfikowania zasad zgodności i Dodaj lub usuń regionów. Do zarządzania i platform konta bazy danych rozwiązania Cosmos platformy Azure, możesz użyć dowolnej [interfejsu wiersza polecenia Azure](cli-samples.md), [interfejsu API REST dostawcy zasobów][rp-rest-api], lub [portalu Azure ](create-sql-api-dotnet.md#create-account).

## <a name="getting-started"></a>Wprowadzenie

Postępuj zgodnie z instrukcjami [jak instalowanie i konfigurowanie programu Azure PowerShell] [ powershell-install-configure] do zainstalowania i zaloguj się do konta usługi Azure Resource Manager w programie Powershell.

### <a name="notes"></a>Uwagi

* Jeśli chcesz wykonać następujące polecenia, bez konieczności potwierdzenie przez użytkownika, dołącz `-Force` flagi do polecenia.
* Poniższe polecenia są synchroniczne.

## <a id="create-documentdb-account-powershell"></a> Tworzenie konta usługi Azure rozwiązania Cosmos bazy danych

To polecenie umożliwia tworzenie konta bazy danych Azure DB rozwiązania Cosmos. Konfigurowanie nowego konta bazy danych jako albo jednym regionie lub [w przypadku] [ scaling-globally] niektóre [spójności zasad](consistency-levels.md).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name>  -Location "<resource-group-location>" -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>` Nazwa lokalizacji zapisu regionu konta bazy danych. Ta lokalizacja musi mieć wartość 0 priorytet trybu failover. Musi istnieć dokładnie jeden region zapisu na konto bazy danych.
* `<read-region-location>` Nazwa lokalizacji obszaru odczytu konta bazy danych. Ta lokalizacja musi być większa niż 0. wartość priorytetu trybu failover. Może istnieć więcej niż jeden odczytu regiony dla danego konta bazy danych.
* `<ip-range-filter>` Określa zbiór adresów IP i zakresów adresów IP w postaci CIDR do uwzględnienia jako lista dozwolonych adresów IP klienta, konto bazy danych. Zakresy adresów IP musi być przecinkami oddzielone i nie może zawierać spacji. Aby uzyskać więcej informacji, zobacz [Obsługa zapory DB rozwiązania Cosmos Azure](firewall-support.md)
* `<default-consistency-level>` Domyślny poziom spójności konta bazy danych Azure rozwiązania Cosmos. Aby uzyskać więcej informacji, zobacz [poziomów spójności w usłudze Azure DB rozwiązania Cosmos](consistency-levels.md).
* `<max-interval>` W przypadku użycia z ograniczonym nieaktualności, ta wartość przedstawia ilość czasu nieaktualności (w sekundach), dopuszczalne. Dopuszczalnego zakresu dla tej wartości to 1-100.
* `<max-staleness-prefix>` W przypadku użycia z ograniczonym nieaktualności, ta wartość reprezentuje liczbę żądań starych dopuszczalne. Dopuszczalnego zakresu dla tej wartości to 1 – 2 147 483 647.
* `<resource-group-name>` Nazwa [grupy zasobów platformy Azure] [ azure-resource-groups] do której należy do nowego konta bazy danych Azure DB rozwiązania Cosmos.
* `<resource-group-location>` Lokalizacja grupy zasobów platformy Azure, do którego należy nowe konto bazy danych Azure DB rozwiązania Cosmos.
* `<database-account-name>` Nazwa bazy danych Azure rozwiązania Cosmos konto bazy danych ma zostać utworzony. Można używać tylko małe litery, cyfry, '-' znaków i musi zawierać od 3 do 50 znaków.

Przykład: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Location "West US" -Name "docdb-test" -Properties $CosmosDBProperties

### <a name="notes"></a>Uwagi
* Powyższy przykład tworzy konto bazy danych z dwóch regionach. Istnieje również możliwość utworzenia konta bazy danych z jednego regionu (który jest oznaczony jako regionu zapisu i ma wartość priorytetu trybu failover, 0) lub więcej niż dwóch regionach. Aby uzyskać więcej informacji, zobacz [konta bazy danych w przypadku][scaling-globally].
* Lokalizacje musi być regionów, w których bazy danych Azure rozwiązania Cosmos jest ogólnie dostępna. Bieżąca lista regionów znajduje się na [strony regiony platformy Azure](https://azure.microsoft.com/regions/#services).

## <a id="update-documentdb-account-powershell"></a> Aktualizacja konta bazy danych Azure DB rozwiązania Cosmos

To polecenie umożliwia zaktualizowanie bazy danych Azure rozwiązania Cosmos właściwościach konta bazy danych. W tym zasad zgodności i lokalizacje, w których istnieje konto bazy danych w.

> [!NOTE]
> To polecenie umożliwia dodawanie i usuwanie regionów, ale nie zezwala na modyfikowanie priorytetów trybu failover. Aby zmodyfikować priorytetów trybu failover, zobacz [poniżej](#modify-failover-priority-powershell).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name> -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>` Nazwa lokalizacji zapisu regionu konta bazy danych. Ta lokalizacja musi mieć wartość 0 priorytet trybu failover. Musi istnieć dokładnie jeden region zapisu na konto bazy danych.
* `<read-region-location>` Nazwa lokalizacji obszaru odczytu konta bazy danych. Ta lokalizacja musi być większa niż 0. wartość priorytetu trybu failover. Może istnieć więcej niż jeden odczytu regiony dla danego konta bazy danych.
* `<default-consistency-level>` Domyślny poziom spójności konta bazy danych Azure rozwiązania Cosmos. Aby uzyskać więcej informacji, zobacz [poziomów spójności w usłudze Azure DB rozwiązania Cosmos](consistency-levels.md).
* `<ip-range-filter>` Określa zbiór adresów IP i zakresów adresów IP w postaci CIDR do uwzględnienia jako lista dozwolonych adresów IP klienta, konto bazy danych. Zakresy adresów IP musi być przecinkami oddzielone i nie może zawierać spacji. Aby uzyskać więcej informacji, zobacz [Obsługa zapory DB rozwiązania Cosmos Azure](firewall-support.md)
* `<max-interval>` W przypadku użycia z ograniczonym nieaktualności, ta wartość przedstawia ilość czasu nieaktualności (w sekundach), dopuszczalne. Dopuszczalnego zakresu dla tej wartości to 1-100.
* `<max-staleness-prefix>` W przypadku użycia z ograniczonym nieaktualności, ta wartość reprezentuje liczbę żądań starych dopuszczalne. Dopuszczalnego zakresu dla tej wartości to 1 – 2 147 483 647.
* `<resource-group-name>` Nazwa [grupy zasobów platformy Azure] [ azure-resource-groups] do której należy do nowego konta bazy danych Azure DB rozwiązania Cosmos.
* `<resource-group-location>` Lokalizacja grupy zasobów platformy Azure, do którego należy nowe konto bazy danych Azure DB rozwiązania Cosmos.
* `<database-account-name>` Nazwa bazy danych Azure rozwiązania Cosmos konto bazy danych do zaktualizowania.

Przykład: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Properties $CosmosDBProperties

## <a id="delete-documentdb-account-powershell"></a> Usuń konto bazy danych Azure DB rozwiązania Cosmos

To polecenie umożliwia usunięcie istniejącego konta bazy danych Azure DB rozwiązania Cosmos.

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"
    
* `<resource-group-name>` Nazwa [grupy zasobów platformy Azure] [ azure-resource-groups] do której należy do nowego konta bazy danych Azure DB rozwiązania Cosmos.
* `<database-account-name>` Nazwa bazy danych Azure rozwiązania Cosmos konto bazy danych do usunięcia.

Przykład:

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="get-documentdb-properties-powershell"></a> Pobierz właściwości konta bazy danych z bazy danych Azure rozwiązania Cosmos

To polecenie umożliwia uzyskiwanie właściwości istniejącego konta bazy danych Azure DB rozwiązania Cosmos.

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` Nazwa [grupy zasobów platformy Azure] [ azure-resource-groups] do której należy do nowego konta bazy danych Azure DB rozwiązania Cosmos.
* `<database-account-name>` Nazwa bazy danych Azure rozwiązania Cosmos konta bazy danych.

Przykład:

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="update-tags-powershell"></a> Tagi aktualizacja konta bazy danych Azure rozwiązania Cosmos bazy danych

Poniższy przykład zawiera opis sposobu ustawiania [tagi zasobów Azure] [ azure-resource-tags] dla Twojej bazy danych rozwiązania Cosmos Azure bazy danych konta.

> [!NOTE]
> To polecenie może być łączone z poleceń create i update przez dołączenie `-Tags` flagę odpowiadającego mu parametru.

Przykład:

    $tags = @{"dept" = "Finance”; environment = “Production”}
    Set-AzureRmResource -ResourceType “Microsoft.DocumentDB/databaseAccounts”  -ResourceGroupName "rg-test" -Name "docdb-test" -Tags $tags

## <a id="list-account-keys-powershell"></a> Lista kluczy konta

Podczas tworzenia konta bazy danych Azure rozwiązania Cosmos usługi generuje dwa klucze dostępu do głównego, które mogą być używane do uwierzytelniania podczas uzyskiwania dostępu do konta bazy danych Azure rozwiązania Cosmos. Zapewniając dwa klucze dostępu do bazy danych rozwiązania Cosmos Azure umożliwia ponowne generowanie kluczy nie przeszkód do swojego konta bazy danych Azure rozwiązania Cosmos. Dostępne są tylko do odczytu klucze do uwierzytelniania operacji tylko do odczytu. Istnieją dwa klucze odczytu i zapisu (podstawowych i pomocniczych) i dwa klucze tylko do odczytu (podstawowych i pomocniczych).

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` Nazwa [grupy zasobów platformy Azure] [ azure-resource-groups] do której należy do nowego konta bazy danych Azure DB rozwiązania Cosmos.
* `<database-account-name>` Nazwa bazy danych Azure rozwiązania Cosmos konta bazy danych.

Przykład:

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="list-connection-strings-powershell"></a> Lista parametrów połączenia

Dla konta bazy danych MongoDB można pobrać parametry połączenia do łączenie aplikacji z bazy danych MongoDB konto bazy danych przy użyciu następującego polecenia.

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` Nazwa [grupy zasobów platformy Azure] [ azure-resource-groups] do której należy do nowego konta bazy danych Azure DB rozwiązania Cosmos.
* `<database-account-name>` Nazwa bazy danych Azure rozwiązania Cosmos konta bazy danych.

Przykład:

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="regenerate-account-key-powershell"></a> Ponowne wygenerowanie klucza konta

Należy zmienić klucze dostępu do konta bazy danych Azure rozwiązania Cosmos okresowo, aby zabezpieczyć połączenia. Dwa klucze dostępu są przypisywane do utrzymania połączeń przy użyciu jednego klucza dostępu, jednocześnie ponownie generując drugi klucz dostępu konta bazy danych Azure rozwiązania Cosmos.

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"keyKind"="<key-kind>"}

* `<resource-group-name>` Nazwa [grupy zasobów platformy Azure] [ azure-resource-groups] do której należy do nowego konta bazy danych Azure DB rozwiązania Cosmos.
* `<database-account-name>` Nazwa bazy danych Azure rozwiązania Cosmos konta bazy danych.
* `<key-kind>` Jeden z czterech typów kluczy: ["Primary" | " Pomocniczy "|" PrimaryReadonly "|" SecondaryReadonly"], który chcesz ponownie wygenerować.

Przykład:

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"keyKind"="Primary"}

## <a id="modify-failover-priority-powershell"></a> Modyfikowanie priorytetów trybu Failover konta bazy danych Azure rozwiązania Cosmos bazy danych

Dla konta w przypadku bazy danych można zmienić priorytet trybu failover różnych regionów, w których istnieje konto bazy danych Azure DB rozwiązania Cosmos w. Aby uzyskać więcej informacji na tryb failover w konta bazy danych DB rozwiązania Cosmos Azure, zobacz [dystrybucji danych globalnie z bazy danych Azure rozwiązania Cosmos][distribute-data-globally].

    $failoverPolicies = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0},@{"locationName"="<read-region-location>"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"failoverPolicies"=$failoverPolicies}

* `<write-region-location>` Nazwa lokalizacji zapisu regionu konta bazy danych. Ta lokalizacja musi mieć wartość 0 priorytet trybu failover. Musi istnieć dokładnie jeden region zapisu na konto bazy danych.
* `<read-region-location>` Nazwa lokalizacji obszaru odczytu konta bazy danych. Ta lokalizacja musi być większa niż 0. wartość priorytetu trybu failover. Może istnieć więcej niż jeden odczytu regiony dla danego konta bazy danych.
* `<resource-group-name>` Nazwa [grupy zasobów platformy Azure] [ azure-resource-groups] do której należy do nowego konta bazy danych Azure DB rozwiązania Cosmos.
* `<database-account-name>` Nazwa bazy danych Azure rozwiązania Cosmos konta bazy danych.

Przykład:

    $failoverPolicies = @(@{"locationName"="East US"; "failoverPriority"=0},@{"locationName"="West US"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"failoverPolicies"=$failoverPolicies}

## <a name="next-steps"></a>Kolejne kroki

* Aby połączyć się przy użyciu platformy .NET, zobacz [Connect i zapytania z platformą .NET](create-sql-api-dotnet.md).
* Nawiązywanie połączenia przy użyciu środowiska Node.js, zobacz [Connect i zapytania przy użyciu środowiska Node.js i aplikacji bazy danych MongoDB](create-mongodb-nodejs.md).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
