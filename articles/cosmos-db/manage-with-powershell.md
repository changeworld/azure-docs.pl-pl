---
title: Tworzenie Azure Cosmos DB i zarządzanie nimi przy użyciu programu PowerShell
description: Za pomocą programu Azure PowerShell Zarządzaj kontami Azure Cosmos DB, bazami danych, kontenerami i przepływem pracy.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 08/05/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 45f5e21e05cf627d418cb66418cf305833a73891
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965104"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Zarządzanie Azure Cosmos DB zasobami interfejsu API SQL przy użyciu programu PowerShell

W poniższym przewodniku opisano sposób użycia programu PowerShell do tworzenia skryptów i automatyzowania zarządzania zasobami Azure Cosmos DB, w tym konta, bazy danych, kontenera i przepływności. Zarządzanie Azure Cosmos DB jest obsługiwane za pomocą polecenia cmdlet AzResource bezpośrednio dla Azure Cosmos DB dostawcy zasobów. Aby wyświetlić wszystkie właściwości, które mogą być zarządzane za pomocą programu PowerShell dla dostawcy zasobów Azure Cosmos DB, zobacz [Azure Cosmos DB schematu dostawcy zasobów](/azure/templates/microsoft.documentdb/allversions)

W przypadku zarządzania różnymi platformami Azure Cosmos DB można korzystać z [interfejsu][rp-rest-api] [wiersza polecenia platformy Azure](manage-with-cli.md)lub [Azure Portal](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Wprowadzenie

Postępuj zgodnie z instrukcjami w temacie [jak zainstalować i skonfigurować Azure PowerShell][powershell-install-configure] , aby zainstalować i zalogować się do konta platformy Azure w programie PowerShell.

* Jeśli chcesz, wykonaj następujące polecenia, bez wymagania potwierdzenia użytkownika, dołącz `-Force` flagi do polecenia.
* Następujące polecenia są synchroniczne.

## <a name="azure-cosmos-accounts"></a>Konta usługi Azure Cosmos

W poniższych sekcjach pokazano, jak zarządzać kontem usługi Azure Cosmos, w tym:

* [Utwórz konto usługi Azure Cosmos](#create-account)
* [Aktualizowanie konta usługi Azure Cosmos](#update-account)
* [Wyświetlanie listy wszystkich kont usługi Azure Cosmos w ramach subskrypcji](#list-accounts)
* [Pobierz konto usługi Azure Cosmos](#get-account)
* [Usuwanie konta usługi Azure Cosmos](#delete-account)
* [Aktualizowanie tagów dla konta usługi Azure Cosmos](#update-tags)
* [Lista kluczy dla konta usługi Azure Cosmos](#list-keys)
* [Ponowne generowanie kluczy dla konta usługi Azure Cosmos](#regenerate-keys)
* [Wyświetlanie listy parametrów połączenia dla konta usługi Azure Cosmos](#list-connection-strings)
* [Modyfikowanie priorytetu trybu failover dla konta usługi Azure Cosmos](#modify-failover-priority)

### <a id="create-account"></a>Utwórz konto usługi Azure Cosmos

To polecenie tworzy konto bazy danych Azure Cosmos DB z [wieloma regionami][distribute-data-globally], ograniczone-nieodświeżone [zasady spójności](consistency-levels.md).

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US 2"
$accountName = "mycosmosaccount" # must be lowercase and < 31 characters .

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 }
)

$consistencyPolicy = @{
    "defaultConsistencyLevel"="BoundedStaleness";
    "maxIntervalInSeconds"=300;
    "maxStalenessPrefix"=100000
}

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="false"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

* `$accountName`Nazwa konta usługi Azure Cosmos. Musi być małymi literami, akceptuje alfanumeryczne i znak "-" oraz od 3 do 31 znaków.
* `$location`Lokalizacja zasobu konta usługi Azure Cosmos.
* `$locations`Regiony repliki dla konta bazy danych. Dla każdego konta bazy danych musi istnieć jeden region zapisu z wartością priorytetu trybu failover równą 0.
* `$consistencyPolicy`Domyślny poziom spójności konta usługi Azure Cosmos. Aby uzyskać więcej informacji, zobacz [poziomy spójności w usłudze Azure Cosmos DB](consistency-levels.md).
* `$CosmosDBProperties`Wartości właściwości przesłane do dostawcy Azure Resource Manager Cosmos DB, aby zainicjować obsługę konta.

Konta usługi Azure Cosmos można skonfigurować za pomocą zapory IP oraz punktów końcowych usługi Virtual Network. Aby uzyskać informacje dotyczące sposobu konfigurowania zapory protokołu IP dla Azure Cosmos DB, zobacz [Konfigurowanie zapory IP](how-to-configure-firewall.md).  Aby uzyskać więcej informacji na temat włączania punktów końcowych usługi dla Azure Cosmos DB, zobacz [Konfigurowanie dostępu z sieci wirtualnych](how-to-configure-vnet-service-endpoint.md).

### <a id="list-accounts"></a>Wyświetlanie listy wszystkich kont usługi Azure Cosmos w ramach subskrypcji

To polecenie umożliwia wyświetlenie listy wszystkich kont usługi Azure Cosmos w ramach subskrypcji.

```azurepowershell-interactive
# List Azure Cosmos Accounts

Get-AzResource -ResourceType Microsoft.DocumentDb/databaseAccounts | ft
```

### <a id="get-account"></a>Pobierz właściwości konta usługi Azure Cosmos

To polecenie umożliwia uzyskanie właściwości istniejącego konta usługi Azure Cosmos.

```azurepowershell-interactive
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

### <a id="update-account"></a>Aktualizowanie konta usługi Azure Cosmos

To polecenie umożliwia aktualizowanie właściwości konta bazy danych Azure Cosmos DB. Dostępne są następujące właściwości:

* Dodawanie lub usuwanie regionów
* Zmiana domyślnych zasad spójności
* Zmienianie filtru zakresu adresów IP
* Zmiana konfiguracji Virtual Network
* Włączanie wielu wzorców

> [!NOTE]
> To polecenie umożliwia dodanie i usunięcie regionów, ale nie pozwala na modyfikowanie priorytetów trybu failover ani Zmienianie regionu przy użyciu `failoverPriority=0`. Aby zmodyfikować priorytet trybu failover, zobacz [Modyfikowanie priorytetu trybu failover dla konta usługi Azure Cosmos](#modify-failover-priority).

```azurepowershell-interactive
# Get an Azure Cosmos Account (assume it has two regions currently West US 2 and East US 2) and add a third region

$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 },
    @{ "locationName"="South Central US"; "failoverPriority"=2 }
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="delete-account"></a>Usuwanie konta usługi Azure Cosmos

To polecenie umożliwia usunięcie istniejącego konta usługi Azure Cosmos.

```azurepowershell-interactive
# Delete an Azure Cosmos Account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName
```

### <a id="update-tags"></a>Aktualizowanie tagów konta usługi Azure Cosmos

W poniższym przykładzie opisano sposób ustawiania [tagów zasobów platformy Azure][azure-resource-tags] dla konta usługi Azure Cosmos.

> [!NOTE]
> To polecenie może być łączone z polecenia tworzenia lub aktualizacji, dodając `-Tags` flagę odpowiadającego mu parametru.

```azurepowershell-interactive
# Update tags for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$tags = @{
    "dept" = "Finance";
    "environment" = "Production"
}

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -Tags $tags
```

### <a id="list-keys"></a> Wyświetl listę kluczy konta

Podczas tworzenia konta usługi Azure Cosmos DB, Usługa generuje dwa klucze wzorca dostępu, których można użyć do uwierzytelniania podczas uzyskiwania dostępu do konta usługi Azure Cosmos DB. Zapewniając dwa klucze dostępu, usługi Azure Cosmos DB pozwala na ponowne generowanie kluczy nie zakłóceń z kontem usługi Azure Cosmos DB. Dostępne są również klucze tylko do odczytu w celu uwierzytelniania operacji tylko do odczytu. Istnieją dwa klucze odczytu i zapisu (podstawowych i pomocniczych) i dwa klucze tylko do odczytu (podstawowych i pomocniczych).

```azurepowershell-interactive
# List keys for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listKeys `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="list-connection-strings"></a> Lista parametrów połączenia

W przypadku kont bazy danych MongoDB przy użyciu następującego polecenia można pobrać parametry połączenia, które umożliwiają połączenie aplikacji bazy danych MongoDB z kontem bazy danych.

```azurepowershell-interactive
# List connection strings for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listConnectionStrings `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="regenerate-keys"></a>Ponowne generowanie kluczy konta

Klucze dostępu do konta usługi Azure Cosmos powinny być okresowo generowane ponownie, aby zapewnić lepszą ochronę połączeń. Podstawowy i pomocniczy klucz dostępu są przypisane do konta. Pozwala to klientom na zachowanie dostępu podczas ponownego generowania drugiego. Istnieją cztery typy kluczy dla konta usługi Azure Cosmos (podstawowa, pomocnicza, PrimaryReadonly i SecondaryReadonly)

```azurepowershell-interactive
# Regenerate the primary key for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keyKind = @{ "keyKind"="Primary" }

$keys = Invoke-AzResourceAction -Action regenerateKey `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $keyKind

Select-Object $keys
```

### <a id="modify-failover-priority"></a>Modyfikowanie priorytetu trybu failover

W przypadku wieloregionowych kont baz danych można zmienić kolejność, w której konto Cosmos będzie wspierać pomocnicze repliki do odczytu, jeśli w podstawowej replice zapisu występuje regionalna praca awaryjna. Modyfikowanie `failoverPriority=0` może również służyć do inicjowania testowania odzyskiwania po awarii w celu zaplanowania odzyskiwania po awarii.

W poniższym przykładzie Załóżmy, że konto ma bieżący priorytet `West US 2 = 0` trybu failover i `East US 2 = 1` i przerzuca regiony.

> [!CAUTION]
> Zmiana `locationName` dla`failoverPriority=0` zostanie wyzwolona ręczny tryb failover dla konta usługi Azure Cosmos. Wszystkie inne zmiany priorytetów nie będą wyzwalać trybu failover.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverRegions = @(
    @{ "locationName"="East US 2"; "failoverPriority"=0 },
    @{ "locationName"="West US 2"; "failoverPriority"=1 }
)

$failoverPolicies = @{
    "failoverPolicies"= $failoverRegions
}

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a name="azure-cosmos-database"></a>Baza danych Cosmos Azure

W poniższych sekcjach pokazano, jak zarządzać bazą danych usługi Azure Cosmos, w tym:

* [Tworzenie bazy danych usługi Azure Cosmos](#create-db)
* [Tworzenie bazy danych usługi Azure Cosmos z udostępnioną przepływność](#create-db-ru)
* [Uzyskiwanie przepływności bazy danych Azure Cosmos Database](#get-db-ru)
* [Wyświetl listę wszystkich baz danych usługi Azure Cosmos na koncie](#list-db)
* [Pobieranie pojedynczej bazy danych usługi Azure Cosmos](#get-db)
* [Usuwanie bazy danych usługi Azure Cosmos](#delete-db)

### <a id="create-db"></a>Tworzenie bazy danych usługi Azure Cosmos

```azurepowershell-interactive
# Create an Azure Cosmos database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{"id"=$databaseName}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="create-db-ru"></a>Tworzenie bazy danych usługi Azure Cosmos z udostępnioną przepływność

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database2"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="get-db-ru"></a>Uzyskiwanie przepływności bazy danych Azure Cosmos Database

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$databaseThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/settings"
$databaseThroughputResourceName = $accountName + "/sql/" + $databaseName + "/throughput"

Get-AzResource -ResourceType $databaseThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseThroughputResourceName  | Select-Object Properties
```

### <a id="list-db"></a>Pobierz wszystkie bazy danych usługi Azure Cosmos na konto

```azurepowershell-interactive
# Get all databases in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceName = $accountName + "/sql/"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName  | Select-Object Properties
```

### <a id="get-db"></a>Pobieranie pojedynczej bazy danych usługi Azure Cosmos

```azurepowershell-interactive
# Get a single database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-db"></a>Usuwanie bazy danych usługi Azure Cosmos

```azurepowershell-interactive
# Delete a database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName
Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="azure-cosmos-container"></a>Kontener usługi Azure Cosmos

W poniższych sekcjach pokazano, jak zarządzać kontenerem usługi Azure Cosmos, w tym:

* [Tworzenie kontenera usługi Azure Cosmos](#create-container)
* [Tworzenie kontenera usługi Azure Cosmos z dużym kluczem partycji](#create-container-big-pk)
* [Uzyskiwanie przepływności kontenera usługi Azure Cosmos](#get-container-ru)
* [Tworzenie kontenera usługi Azure Cosmos z udostępnioną przepływność](#create-container-ru)
* [Tworzenie kontenera usługi Azure Cosmos za pomocą indeksowania niestandardowego](#create-container-custom-index)
* [Tworzenie kontenera usługi Azure Cosmos z wyłączonym indeksem](#create-container-no-index)
* [Tworzenie kontenera usługi Azure Cosmos z unikatowym kluczem i czasem wygaśnięcia](#create-container-unique-key-ttl)
* [Tworzenie kontenera usługi Azure Cosmos za pomocą rozwiązywania konfliktów](#create-container-lww)
* [Wyświetl listę wszystkich kontenerów usługi Azure Cosmos w bazie danych](#list-containers)
* [Pobieranie jednego kontenera usługi Azure Cosmos w bazie danych](#get-container)
* [Usuwanie kontenera usługi Azure Cosmos](#delete-container)

### <a id="create-container"></a>Tworzenie kontenera usługi Azure Cosmos

```azurepowershell-interactive
# Create an Azure Cosmos container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-big-pk"></a>Tworzenie kontenera usługi Azure Cosmos o dużym rozmiarze klucza partycji

```azurepowershell-interactive
# Create an Azure Cosmos container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash";
            "version" = 2
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="get-container-ru"></a>Uzyskiwanie przepływności kontenera usługi Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$containerThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers/settings"
$containerThroughputResourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName + "/throughput"

Get-AzResource -ResourceType $containerThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerThroughputResourceName  | Select-Object Properties
```

### <a id="create-container-ru"></a>Tworzenie kontenera usługi Azure Cosmos z udostępnioną przepływność

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container2"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties 
```

### <a id="create-container-custom-index"></a>Tworzenie kontenera usługi Azure Cosmos za pomocą niestandardowych zasad indeksu

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container3"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @(@{
                "path"="/myPathToNotIndex/*"
            })
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-no-index"></a>Tworzenie kontenera usługi Azure Cosmos z wyłączonym indeksem

```azurepowershell-interactive
# Create an Azure Cosmos container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container4"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "indexingPolicy"=@{
            "indexingMode"="none"
        }
    };
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-unique-key-ttl"></a>Tworzenie kontenera usługi Azure Cosmos z unikatowymi zasadami kluczy i czasem wygaśnięcia

```azurepowershell-interactive
# Create a container with a unique key policy and TTL
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @()
        };
        "uniqueKeyPolicy"= @{
            "uniqueKeys"= @(@{
                "paths"= @(
                    "/myUniqueKey1";
                    "/myUniqueKey2"
                )
            })
        };
        "defaultTtl"= 100;
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-lww"></a>Tworzenie kontenera usługi Azure Cosmos za pomocą rozwiązywania konfliktów

Aby utworzyć zasady rozwiązywania konfliktów w celu użycia procedury składowanej, `"mode"="custom"` należy ustawić i ustawić ścieżkę rozpoznawania jako nazwę `"conflictResolutionPath"="myResolverStoredProcedure"`procedury składowanej. Aby zapisać wszystkie konflikty w ConflictsFeed i obsłudze oddzielnie, `"mode"="custom"` ustaw i`"conflictResolutionPath"=""`

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container6"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "conflictResolutionPolicy"=@{
            "mode"="lastWriterWins";
            "conflictResolutionPath"="/myResolutionPath"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="list-containers"></a>Wyświetl listę wszystkich kontenerów usługi Azure Cosmos w bazie danych

```azurepowershell-interactive
# List all Azure Cosmos containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="get-container"></a>Pobieranie jednego kontenera usługi Azure Cosmos w bazie danych

```azurepowershell-interactive
# Get a single Azure Cosmos container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-container"></a>Usuwanie kontenera usługi Azure Cosmos

```azurepowershell-interactive
# Delete an Azure Cosmos container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="next-steps"></a>Następne kroki

* [Wszystkie przykłady programu PowerShell](powershell-samples.md)
* [Jak zarządzać kontem usługi Azure Cosmos](how-to-manage-database-account.md)
* [Tworzenie kontenera usługi Azure Cosmos](how-to-create-container.md)
* [Skonfiguruj czas wygaśnięcia w Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
