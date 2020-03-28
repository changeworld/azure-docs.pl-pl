---
title: Tworzenie usługi Azure Cosmos DB i zarządzanie nim przy użyciu programu PowerShell
description: Użyj usługi Azure Powershell zarządzać kontami usługi Azure Cosmos, baz danych, kontenerów i przepływności.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/26/2020
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: c8e833a4ba18520d8e354398cfd0d00525594d15
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365766"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Zarządzanie zasobami interfejsu SQL usługi Azure Cosmos DB za pomocą programu PowerShell

W poniższym przewodniku opisano sposób użycia programu PowerShell do tworzenia skryptów i automatyzowania zarządzania zasobami usługi Azure Cosmos DB, w tym kontem, bazą danych, kontenerem i przepływnością.

> [!NOTE]
> Przykłady w tym `Get-AzResource` `Set-AzResource` artykule użyj i poleceń cmdlet programu Powershell dla operacji zasobów platformy Azure, a także poleceń cmdlet zarządzania [usługi Az.CosmosDB.](https://docs.microsoft.com/powershell/module/az.cosmosdb) `Az.CosmosDB`polecenia cmdlet są nadal w wersji zapoznawczej i mogą ulec zmianie, zanim będą ogólnie dostępne. Zobacz stronę odwołania interfejsu API [usługi Az.CosmosDB,](https://docs.microsoft.com/powershell/module/az.cosmosdb) aby uzyskać wszelkie aktualizacje poleceń.

Aby wyświetlić wszystkie właściwości, którymi `Get-Resource` / `Set-AzResource` można zarządzać za pomocą poleceń cmdlet programu PowerShell, zobacz [schemat dostawcy zasobów usługi Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)

W przypadku zarządzania wieloma platformami usługi Azure `Az` Cosmos DB można używać poleceń cmdlet `Az.CosmosDB` i poleceń z [wieloplatformowym programem Powershell,](https://docs.microsoft.com/powershell/scripting/install/installing-powershell)a także [interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure,](manage-with-cli.md)interfejsu API [REST][rp-rest-api]lub portalu [Azure.](create-sql-api-dotnet.md#create-account)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Wprowadzenie

Postępuj zgodnie z instrukcjami w [jak zainstalować i skonfigurować program Azure PowerShell,][powershell-install-configure] aby zainstalować i zalogować się do konta platformy Azure w programie Powershell.

* `Set-AzureResource`jest używany poniżej. Poprosi o potwierdzenie użytkownika.  Jeśli wolisz wykonać bez konieczności potwierdzenia użytkownika, dołącz flagę `-Force` do polecenia.

## <a name="azure-cosmos-accounts"></a>Konta usługi Azure Cosmos

W poniższych sekcjach pokazano, jak zarządzać kontem usługi Azure Cosmos, w tym:

* [Tworzenie konta usługi Azure Cosmos](#create-account)
* [Aktualizowanie konta usługi Azure Cosmos](#update-account)
* [Wyświetlanie listy wszystkich kont usługi Azure Cosmos w ramach subskrypcji](#list-accounts)
* [Pobierz konto usługi Azure Cosmos](#get-account)
* [Usuwanie konta usługi Azure Cosmos](#delete-account)
* [Aktualizowanie tagów dla konta usługi Azure Cosmos](#update-tags)
* [Lista kluczy dla konta usługi Azure Cosmos](#list-keys)
* [Ponowne generowanie kluczy dla konta usługi Azure Cosmos](#regenerate-keys)
* [Wyświetlanie listy ciągów połączeń dla konta usługi Azure Cosmos](#list-connection-strings)
* [Modyfikowanie priorytetu trybu failover dla konta usługi Azure Cosmos](#modify-failover-priority)
* [Wyzwalanie ręcznego trybu failover dla konta usługi Azure Cosmos](#trigger-manual-failover)

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Tworzenie konta usługi Azure Cosmos

To polecenie tworzy konto bazy danych usługi Azure Cosmos DB z [wieloma regionami,][distribute-data-globally] [automatyczną funkcją failover](how-to-manage-database-account.md#automatic-failover) i [zasadą spójności](consistency-levels.md)ograniczonego i nieaktualności.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$locations = @("West US 2", "East US 2")
$accountName = "mycosmosaccount"
$apiKind = "GlobalDocumentDB"
$consistencyLevel = "BoundedStaleness"
$maxStalenessInterval = 300
$maxStalenessPrefix = 100000

New-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
    -Location $locations -Name $accountName `
    -ApiKind $apiKind -EnableAutomaticFailover:$true `
    -DefaultConsistencyLevel $consistencyLevel `
    -MaxStalenessIntervalInSeconds $maxStalenessInterval `
    -MaxStalenessPrefix $maxStalenessPrefix
```

* `$resourceGroupName`Grupa zasobów platformy Azure, do której można wdrożyć konto usługi Cosmos. Musi już istnieć.
* `$locations`Regiony dla konta bazy danych, począwszy od regionu zapisu i uporządkowane według priorytetu trybu failover.
* `$accountName`Nazwa konta usługi Azure Cosmos. Musi być unikatowa, mała litera, zawierać tylko znaki alfanumeryczne i "-" oraz od 3 do 31 znaków długości.
* `$apiKind`Typ konta usługi Cosmos do utworzenia. Aby uzyskać więcej informacji, zobacz [interfejsy API w usłudze Cosmos DB](introduction.md#develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis).
* `$consistencyPolicy`, `$maxStalenessInterval`i `$maxStalenessPrefix` domyślny poziom spójności i ustawienia konta usługi Azure Cosmos. Aby uzyskać więcej informacji, zobacz [Poziomy spójności w usłudze Azure Cosmos DB](consistency-levels.md).

Konta usługi Azure Cosmos można skonfigurować z zaporą IP, punktami końcowymi usługi sieci wirtualnej i prywatnymi punktami końcowymi. Aby uzyskać informacje dotyczące konfigurowania zapory IP dla usługi Azure Cosmos DB, zobacz [Konfigurowanie zapory IP](how-to-configure-firewall.md). Aby uzyskać informacje na temat włączania punktów końcowych usługi dla usługi Azure Cosmos DB, zobacz [Konfigurowanie dostępu z sieci wirtualnych](how-to-configure-vnet-service-endpoint.md). Aby uzyskać informacje na temat włączania prywatnych punktów końcowych dla usługi Azure Cosmos DB, zobacz [Konfigurowanie dostępu z prywatnych punktów końcowych.](how-to-configure-private-endpoints.md)

### <a name="list-all-azure-cosmos-accounts-in-a-resource-group"></a><a id="list-accounts"></a>Wyświetlanie listy wszystkich kont usługi Azure Cosmos w grupie zasobów

To polecenie zawiera listę wszystkich kont usługi Azure Cosmos w grupie zasobów.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName
```

### <a name="get-the-properties-of-an-azure-cosmos-account"></a><a id="get-account"></a>Pobierz właściwości konta usługi Azure Cosmos

To polecenie umożliwia uzyskanie właściwości istniejącego konta usługi Azure Cosmos.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName
```

### <a name="update-an-azure-cosmos-account"></a><a id="update-account"></a>Aktualizowanie konta usługi Azure Cosmos

To polecenie umożliwia zaktualizowanie właściwości konta bazy danych usługi Azure Cosmos DB. Właściwości, które mogą być aktualizowane są następujące:

* Dodawanie lub usuwanie regionów
* Zmienianie domyślnych zasad spójności
* Zmiana filtra zakresu ip
* Zmiana konfiguracji sieci wirtualnej
* Włączanie funkcji Multi-master

> [!NOTE]
> Nie można jednocześnie dodawać `locations` ani usuwać regionów i zmieniać innych właściwości konta usługi Azure Cosmos. Modyfikowanie regionów musi być wykonywane jako oddzielna operacja od wszelkich innych zmian na koncie.
> [!NOTE]
> To polecenie umożliwia dodawanie i usuwanie regionów, ale nie pozwala na modyfikowanie priorytetów pracy awaryjnej ani wyzwalanie ręcznego trybu failover. Zobacz [Modyfikowanie priorytetu trybu failover](#modify-failover-priority) i [ręczne uruchamianie trybu failover wyzwalania](#trigger-manual-failover).

```azurepowershell-interactive
# Create account with two regions
$resourceGroupName = "myResourceGroup"
$locations = @("West US 2", "East US 2")
$accountName = "mycosmosaccount"
$apiKind = "GlobalDocumentDB"
$consistencyLevel = "Session"
$enableAutomaticFailover = $true

New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Location $locations -Name $accountName `
    -ApiKind $apiKind -EnableAutomaticFailover:$enableAutomaticFailover `
    -DefaultConsistencyLevel $consistencyLevel

# Region operations
$resourceType = "Microsoft.DocumentDb/databaseAccounts"
$apiVersion = "2020-03-01"

# Add a region to the account
$locations2 = @("West US 2", "East US 2", "South Central US")
$locationObjects2 = @()
$i = 0
ForEach ($location in $locations2) { $locationObjects2 += @{ locationName = "$location"; failoverPriority = $i++ } }
$accountProperties = @{
    databaseAccountOfferType = "Standard";
    locations = $locationObjects2;
    enableAutomaticFailover = $enableAutomaticFailover;
}

Set-AzResource -ResourceType $resourceType `
    -ResourceGroupName $resourceGroupName `
    -ApiVersion $apiVersion -Name $accountName `
    -PropertyObject $accountProperties

Write-Host "Set-AzResource returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
Write-Host "When region was added, press any key to continue."
$HOST.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown") | OUT-NULL
$HOST.UI.RawUI.Flushinputbuffer()

# Remove a region from the account
$locations3 = @("West US 2", "South Central US")
$locationObjects3 = @()
$i = 0
ForEach ($location in $locations3) { $locationObjects3 += @{ locationName = "$location"; failoverPriority = $i++ } }
$accountProperties = @{
    databaseAccountOfferType = "Standard";
    locations = $locationObjects3;
    enableAutomaticFailover = $enableAutomaticFailover;
}

Set-AzResource -ResourceType $resourceType `
    -ResourceGroupName $resourceGroupName `
    -ApiVersion $apiVersion -Name $accountName `
    -PropertyObject $accountProperties

Write-Host "Set-AzResource returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
```
### <a name="enable-multiple-write-regions-for-an-azure-cosmos-account"></a><a id="multi-master"></a>Włączanie wielu regionów zapisu dla konta usługi Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $false
$enableMultiMaster = $true

# First disable automatic failover - cannot have both automatic
# failover and multi-master on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover

# Now enable multi-master
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster
```

### <a name="delete-an-azure-cosmos-account"></a><a id="delete-account"></a>Usuwanie konta usługi Azure Cosmos

To polecenie usuwa istniejące konto usługi Azure Cosmos.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -PassThru
```

### <a name="update-tags-of-an-azure-cosmos-account"></a><a id="update-tags"></a>Aktualizowanie tagów konta usługi Azure Cosmos

To polecenie ustawia [tagi zasobów platformy Azure][azure-resource-tags] dla konta usługi Azure Cosmos. Tagi można ustawić zarówno przy `New-AzCosmosDBAccount` tworzeniu konta, jak `Update-AzCosmosDBAccount`i przy aktualizacji konta za pomocą .

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$tags = @{dept = "Finance"; environment = "Production";}

Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -Tag $tags
```

### <a name="list-account-keys"></a><a id="list-keys"></a>Lista kluczy konta

Podczas tworzenia konta usługi Azure Cosmos usługa generuje dwa główne klucze dostępu, które mogą być używane do uwierzytelniania, gdy dostęp do konta usługi Azure Cosmos jest dostępny. Generowane są również klucze tylko do odczytu do uwierzytelniania operacji tylko do odczytu.
Udostępniając dwa klucze dostępu, usługa Azure Cosmos DB umożliwia ponowne generowanie i obracanie jednego klucza naraz bez przerw w koncie usługi Azure Cosmos.
Konta usługi Cosmos DB mają dwa klucze odczytu i zapisu (podstawowy i pomocniczy) i dwa klucze tylko do odczytu (podstawowy i pomocniczy).

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -Type "Keys"
```

### <a name="list-connection-strings"></a><a id="list-connection-strings"></a>Lista ciągów połączeń

Następujące polecenie pobiera parametry połączenia, aby połączyć aplikacje z kontem usługi Cosmos DB.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -Type "ConnectionStrings"
```

### <a name="regenerate-account-keys"></a><a id="regenerate-keys"></a>Ponowne generowanie kluczy konta

Klucze dostępu do konta usługi Azure Cosmos powinny być okresowo regenerowane, aby zapewnić bezpieczeństwo połączeń. Podstawowe i pomocnicze klucze dostępu są przypisane do konta. Dzięki temu klienci mogą zachować dostęp, gdy jeden klucz naraz jest generowany ponownie.
Istnieją cztery typy kluczy dla konta usługi Azure Cosmos (podstawowy, pomocniczy, primaryreadonly i secondaryreadonly)

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup" # Resource Group must already exist
$accountName = "mycosmosaccount" # Must be all lower case
$keyKind = "primary" # Other key kinds: secondary, primaryReadOnly, secondaryReadOnly

New-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -KeyKind $keyKind
```

### <a name="enable-automatic-failover"></a><a id="enable-automatic-failover"></a>Włączanie automatycznego trybu failover

Następujące polecenie ustawia konto usługi Cosmos DB do pracy awaryjnej automatycznie do jego regionu pomocniczego, jeśli region podstawowy staną się niedostępne.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $true
$enableMultiMaster = $false

# First disable multi-master - cannot have both automatic
# failover and multi-master on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster

# Now enable automatic failover
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover
```

### <a name="modify-failover-priority"></a><a id="modify-failover-priority"></a>Modyfikowanie priorytetu trybu failover

W przypadku kont skonfigurowanych za pomocą funkcji Automatyczna funkcja failover można zmienić kolejność podwyższania przez usługę Cosmos replik pomocniczych do replik podstawowych, jeśli podstawowy stanie się niedostępny.

W poniższym przykładzie załóżmy, `West US 2 = 0` `East US 2 = 1`że `South Central US = 2`bieżący priorytet pracy awaryjnej to , . Polecenie zmieni to `West US 2 = 0`na `South Central US = 1` `East US 2 = 2`, .

> [!CAUTION]
> Zmiana lokalizacji `failoverPriority=0` spowoduje wyzwolenie ręcznego trybu failover dla konta usługi Azure Cosmos. Wszelkie inne zmiany priorytetu nie wyzwoli pracy awaryjnej.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("West US 2", "South Central US", "East US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="trigger-manual-failover"></a><a id="trigger-manual-failover"></a>Ręczne uruchamianie awaryjne wyzwalania

W przypadku kont skonfigurowanych przy ręcznym trybie failover można w `failoverPriority=0`trybie failover i podwyższyć wartość repliki pomocniczej do podstawowej, modyfikując do pliku . Ta operacja może służyć do inicjowania odzyskiwania po awarii wiertła do testowania planowania odzyskiwania po awarii.

W poniższym przykładzie załóżmy, że `West US 2 = 0` konto `East US 2 = 1` ma bieżący priorytet trybu failover i przerzuć regiony.

> [!CAUTION]
> Zmiana `locationName` `failoverPriority=0` spowoduje wyzwolenie ręcznego trybu failover dla konta usługi Azure Cosmos. Żadna inna zmiana priorytetu nie spowoduje przerój awaryjny.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("East US 2", "West US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

## <a name="azure-cosmos-db-database"></a>Baza danych usługi Azure Cosmos DB

W poniższych sekcjach pokazano, jak zarządzać bazą danych usługi Azure Cosmos DB, w tym:

* [Tworzenie bazy danych usługi Azure Cosmos DB](#create-db)
* [Tworzenie bazy danych usługi Azure Cosmos DB z udostępnioną przepływnością](#create-db-ru)
* [Uzyskaj przepływność bazy danych usługi Azure Cosmos DB](#get-db-ru)
* [Wyświetlanie listy wszystkich baz danych usługi Azure Cosmos DB na koncie](#list-db)
* [Pobierz jedną bazę danych usługi Azure Cosmos DB](#get-db)
* [Usuwanie bazy danych usługi Azure Cosmos DB](#delete-db)

### <a name="create-an-azure-cosmos-db-database"></a><a id="create-db"></a>Tworzenie bazy danych usługi Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Set-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-an-azure-cosmos-db-database-with-shared-throughput"></a><a id="create-db-ru"></a>Tworzenie bazy danych usługi Azure Cosmos DB z udostępnioną przepływnością

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$databaseRUs = 400

Set-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -Throughput $databaseRUs
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-database"></a><a id="get-db-ru"></a>Uzyskaj przepływność bazy danych usługi Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabaseThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="get-all-azure-cosmos-db-databases-in-an-account"></a><a id="list-db"></a>Pobierz wszystkie bazy danych usługi Azure Cosmos DB na koncie

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName
```

### <a name="get-a-single-azure-cosmos-db-database"></a><a id="get-db"></a>Pobierz jedną bazę danych usługi Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="delete-an-azure-cosmos-db-database"></a><a id="delete-db"></a>Usuwanie bazy danych usługi Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Remove-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

## <a name="azure-cosmos-db-container"></a>Kontener usługi Azure Cosmos DB

W poniższych sekcjach pokazano, jak zarządzać kontenerem usługi Azure Cosmos DB, w tym:

* [Tworzenie kontenera usługi Azure Cosmos DB](#create-container)
* [Tworzenie kontenera usługi Azure Cosmos DB z dużym kluczem partycji](#create-container-big-pk)
* [Uzyskaj przepływność kontenera usługi Azure Cosmos DB](#get-container-ru)
* [Tworzenie kontenera usługi Azure Cosmos DB z niestandardowym indeksowania](#create-container-custom-index)
* [Tworzenie kontenera usługi Azure Cosmos DB z wyłączonym indeksem](#create-container-no-index)
* [Tworzenie kontenera usługi Azure Cosmos DB z unikatowym kluczem i ttl](#create-container-unique-key-ttl)
* [Tworzenie kontenera usługi Azure Cosmos DB z rozwiązywaniem konfliktów](#create-container-lww)
* [Wyświetlanie listy wszystkich kontenerów usługi Azure Cosmos DB w bazie danych](#list-containers)
* [Pobierz pojedynczy kontener usługi Azure Cosmos DB w bazie danych](#get-container)
* [Usuwanie kontenera usługi Azure Cosmos DB](#delete-container)

### <a name="create-an-azure-cosmos-db-container"></a><a id="create-container"></a>Tworzenie kontenera usługi Azure Cosmos DB

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath
```

### <a name="create-an-azure-cosmos-db-container-with-a-large-partition-key-size"></a><a id="create-container-big-pk"></a>Tworzenie kontenera usługi Azure Cosmos DB o dużym rozmiarze klucza partycji

```azurepowershell-interactive
# Create an Azure Cosmos DB container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -PartitionKeyVersion 2
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-container"></a><a id="get-container-ru"></a>Uzyskaj przepływność kontenera usługi Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainerThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="create-an-azure-cosmos-db-container-with-custom-index-policy"></a><a id="create-container-custom-index"></a>Tworzenie kontenera usługi Azure Cosmos DB z niestandardowymi zasadami indeksu

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$indexPathIncluded = "/*"
$indexPathExcluded = "/myExcludedPath/*"

$includedPathIndex = New-AzCosmosDBSqlIncludedPathIndex -DataType String -Kind Range
$includedPath = New-AzCosmosDBSqlIncludedPath -Path $indexPathIncluded -Index $includedPathIndex

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IncludedPath $includedPath `
    -ExcludedPath $indexPathExcluded `
    -IndexingMode Consistent `
    -Automatic $true

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-indexing-turned-off"></a><a id="create-container-no-index"></a>Tworzenie kontenera usługi Azure Cosmos DB z wyłączonym indeksem

```azurepowershell-interactive
# Create an Azure Cosmos DB container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IndexingMode None

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-unique-key-policy-and-ttl"></a><a id="create-container-unique-key-ttl"></a>Tworzenie kontenera usługi Azure Cosmos DB z unikatowymi zasadami klucza i ttl

```azurepowershell-interactive
# Create a container with a unique key policy and TTL of one day
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$uniqueKeyPath = "/myUniqueKeyPath"
$ttlInSeconds = 86400 # Set this to -1 (or don't use it at all) to never expire

$uniqueKey = New-AzCosmosDBSqlUniqueKey `
    -Path $uniqueKeyPath

$uniqueKeyPolicy = New-AzCosmosDBSqlUniqueKeyPolicy `
    -UniqueKey $uniqueKey

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -UniqueKeyPolicy $uniqueKeyPolicy `
    -TtlInSeconds $ttlInSeconds
```

### <a name="create-an-azure-cosmos-db-container-with-conflict-resolution"></a><a id="create-container-lww"></a>Tworzenie kontenera usługi Azure Cosmos DB z rozwiązywaniem konfliktów

Aby utworzyć zasady rozwiązywania konfliktów w `"mode"="custom"` celu użycia procedury składowanej, ustaw `"conflictResolutionPath"="myResolverStoredProcedure"`i ustaw ścieżkę rozpoznawania jako nazwę procedury składowanej, . Aby zapisać wszystkie konflikty w kanale ConflictsFeed i obsługiwać oddzielnie, `"mode"="custom"``"conflictResolutionPath"=""`

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionPath = "/myResolutionPath"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type LastWriterWins `
    -Path $conflictResolutionPath

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```

### <a name="list-all-azure-cosmos-db-containers-in-a-database"></a><a id="list-containers"></a>Wyświetlanie listy wszystkich kontenerów usługi Azure Cosmos DB w bazie danych

```azurepowershell-interactive
# List all Azure Cosmos DB containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName
```

### <a name="get-a-single-azure-cosmos-db-container-in-a-database"></a><a id="get-container"></a>Pobierz pojedynczy kontener usługi Azure Cosmos DB w bazie danych

```azurepowershell-interactive
# Get a single Azure Cosmos DB container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="delete-an-azure-cosmos-db-container"></a><a id="delete-container"></a>Usuwanie kontenera usługi Azure Cosmos DB

```azurepowershell-interactive
# Delete an Azure Cosmos DB container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Remove-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

## <a name="next-steps"></a>Następne kroki

* [Wszystkie przykłady programu PowerShell](powershell-samples.md)
* [Jak zarządzać kontem usługi Azure Cosmos](how-to-manage-database-account.md)
* [Tworzenie kontenera usługi Azure Cosmos DB](how-to-create-container.md)
* [Konfigurowanie czasu na żywo w usłudze Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
