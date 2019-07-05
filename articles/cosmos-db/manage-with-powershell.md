---
title: Tworzenie i zarządzanie nimi przy użyciu programu PowerShell usługi Azure Cosmos DB
description: Użyciu programu Azure Powershell Zarządzanie kontami usługi Azure Cosmos DB, bazy danych, kontenerów i przepływności.
author: markjbrown
ms.service: cosmos-db
ms.topic: samples
ms.date: 07/03/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 40f041f1b41077824aa3141f6196901b51415c35
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565920"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Zarządzanie zasobami interfejsu API SQL usługi Azure Cosmos DB przy użyciu programu PowerShell

Następującymi wskazówkami w tym artykule opisano sposób za pomocą skryptu programu PowerShell i automatyzacja zarządzania zasobów usługi Azure Cosmos DB, w tym konta, bazy danych, kontenerów i przepływności. Zarządzania usługi Azure Cosmos DB odbywa się za pomocą polecenia cmdlet AzResource bezpośrednio na potrzeby dostawcy zasobów usługi Azure Cosmos DB. Aby wyświetlić wszystkie właściwości, które mogą być zarządzane przy użyciu programu PowerShell dla dostawcy zasobów usługi Azure Cosmos DB, zobacz [schematu dostawcy zasobów usługi Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)

Dla wielu platform zarządzania usługi Azure Cosmos DB, możesz użyć [wiersza polecenia platformy Azure](manage-with-cli.md), [interfejsu API REST][rp-rest-api], lub [witryny Azure portal](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Wprowadzenie

Postępuj zgodnie z instrukcjami w [jak zainstalować i skonfigurować program Azure PowerShell][powershell-install-configure] do instalowania i zaloguj się do konta platformy Azure w programie Powershell.

* Jeśli chcesz, wykonaj następujące polecenia, bez wymagania potwierdzenia użytkownika, dołącz `-Force` flagi do polecenia.
* Następujące polecenia są synchroniczne.

## <a name="azure-cosmos-accounts"></a>Konta usługi Azure Cosmos

Poniższe sekcje pokazują, jak zarządzać kontem usługi Azure Cosmos w tym:

* [Tworzenie konta usługi Azure Cosmos](#create-account)
* [Zaktualizować konto usługi Azure Cosmos](#update-account)
* [Konto usługi Azure Cosmos](#get-account)
* [Usuwanie konta usługi Azure Cosmos](#delete-account)
* [Aktualizacji tagów dla konta usługi Azure Cosmos](#update-tags)
* [Wyświetl listę kluczy dla konta usługi Azure Cosmos](#list-keys)
* [Wygeneruj ponownie klucze konta usługi Azure Cosmos](#regenerate-keys)
* [Lista parametrów połączenia dla konta usługi Azure Cosmos](#list-connection-strings)
* [Modyfikowanie priorytetu trybu failover dla konta usługi Azure Cosmos](#modify-failover-priority)

### <a id="create-account"></a> Tworzenie konta usługi Azure Cosmos

To polecenie umożliwia utworzenie konta bazy danych usługi Azure Cosmos DB przy użyciu [wielu regionów][distribute-data-globally], powiązana nieaktualność [zasad spójności](consistency-levels.md).

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US 2"
$accountName = "mycosmosaccount" # must be lower case.

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

* `$accountName` Nazwa konta usługi Azure Cosmos. Musi zawierać tylko litery, akceptuje alfanumeryczne i "-" znaków i od 3 do 31 znaków.
* `$location` Lokalizacja dla zasobów konta usługi Azure Cosmos.
* `$locations` Regiony repliki dla konta bazy danych. Musi to być jeden region zapisu na konto bazy danych o wartości 0 priorytet trybu failover.
* `$consistencyPolicy` Domyślny poziom spójności konto usługi Cosmos Azure. Aby uzyskać więcej informacji, zobacz [poziomy spójności w usłudze Azure Cosmos DB](consistency-levels.md).
* `$CosmosDBProperties` Wartości właściwości są przekazywane do dostawcy Cosmos DB usługi Azure Resource Manager do aprowizacji konta.

Azure Cosmos, konta mogą być konfigurowane z zapory adresów IP, a także sieci wirtualnej usługi punktów końcowych. Aby uzyskać informacje na temat konfigurowania zapory adresów IP dla usługi Azure Cosmos DB, zobacz [Konfigurowanie zapory adresów IP](how-to-configure-firewall.md).  Aby uzyskać więcej informacji o sposobie włączania punktów końcowych usługi dla usługi Azure Cosmos DB, zobacz [Konfigurowanie dostępu z sieci wirtualnych](how-to-configure-vnet-service-endpoint.md) .

### <a id="get-account"></a> Pobierz właściwości konta usługi Azure Cosmos

To polecenie umożliwia pobieranie właściwości istniejącego konta usługi Azure Cosmos.

```azurepowershell-interactive
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

### <a id="update-account"></a> Zaktualizować konto usługi Azure Cosmos

To polecenie umożliwia aktualizowanie właściwości konta bazy danych Azure Cosmos DB. Właściwości, które mogą być aktualizowane są następujące:

* Dodawanie lub usuwanie regionów
* Zmiana domyślnej spójności zasad
* Zmiana zasad trybu Failover
* Zmiana filtru zakresu adresów IP
* Zmiana konfiguracji sieci wirtualnej
* Włączanie Multi-Master

> [!NOTE]
> To polecenie umożliwia dodawanie i usuwanie regionów, ale nie pozwala na modyfikowanie priorytetów trybu failover. Aby zmodyfikować priorytet trybu failover, zobacz [zmodyfikować priorytet trybu failover dla konta usługi Azure Cosmos](#modify-failover-priority).

```azurepowershell-interactive
# Update an Azure Cosmos Account and set Consistency level to Session

$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$account.Properties.consistencyPolicy = $consistencyPolicy
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="delete-account"></a> Usuwanie konta usługi Azure Cosmos

To polecenie umożliwia usunięcie istniejącego konta usługi Azure Cosmos.

```azurepowershell-interactive
# Delete an Azure Cosmos Account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName
```

### <a id="update-tags"></a> Aktualizacji tagów konta usługi Azure Cosmos

Na poniższym przykładzie opisano sposób ustawiania [tagów zasobów platformy Azure][azure-resource-tags] dla konta usługi Azure Cosmos.

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

### <a id="regenerate-keys"></a> Ponowne generowanie kluczy konta

Klucze dostępu do konta usługi Azure Cosmos należy okresowo generowane do zabezpieczania połączeń. Klucze podstawowe i pomocnicze dostępu są przypisane do konta. Dzięki temu klienci zachować dostęp, podczas gdy druga zostanie ponownie wygenerowany. Istnieją cztery typy kluczy dla konta usługi Azure Cosmos (podstawowego, pomocniczego, PrimaryReadonly i SecondaryReadonly)

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

### <a id="modify-failover-priority"></a> Modyfikowanie priorytetem trybu Failover

Dla multiregionalne konta baz danych możesz zmienić kolejność, w którym konta usługi Cosmos spowoduje podwyższenie poziomu repliki pomocniczej odczytu powinny być regionalnej pracy awaryjnej jest wykonywane dla repliki podstawowej zapisu. Gdy region z `failoverPriority=0` jest zmodyfikowany, to polecenie można również zainicjować odzyskiwanie po awarii do testowania, planowania odzyskiwania po awarii.

Na poniższym przykładzie przyjęto założenie, to konto ma bieżący priorytet trybu failover westus = 0 i eastus = 1, a następnie przerzuć regionów.

> [!CAUTION]
> Ta operacja spowoduje wyzwolenie ręcznej pracy awaryjnej dla konta usługi Azure Cosmos.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverPolicies = @(
    @{ "locationName"="East US"; "failoverPriority"=0 },
    @{ "locationName"="West US"; "failoverPriority"=1 }
)

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a name="azure-cosmos-database"></a>Azure Cosmos Database

W poniższych sekcjach przedstawiono sposób zarządzania bazy danych Azure Cosmos w tym:

* [Utwórz bazę danych Azure Cosmos](#create-db)
* [Utwórz bazę danych Azure Cosmos za pomocą udostępnionej przepływności](#create-db-ru)
* [Uzyskiwanie informacji o przepływności bazy danych Azure Cosmos](#get-db-ru)
* [Lista wszystkich Azure Cosmos baz danych na koncie usługi](#get-all-db)
* [Pobieranie pojedynczej bazy danych Azure Cosmos](#get-db)
* [Usuń bazę danych Azure Cosmos](#delete-db)

### <a id="create-db"></a>Utwórz bazę danych Azure Cosmos

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

### <a id="create-db-ru"></a>Utwórz bazę danych Azure Cosmos za pomocą udostępnionej przepływności

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

### <a id="get-db-ru"></a>Uzyskiwanie informacji o przepływności bazy danych Azure Cosmos

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

### <a id="get-all-db"></a>Pobierz wszystkie bazy danych Azure Cosmos na koncie usługi

```azurepowershell-interactive
# Get all databases in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceName = $accountName + "/sql/"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName  | Select-Object Properties
```

### <a id="get-db"></a>Pobieranie pojedynczej bazy danych Azure Cosmos

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

### <a id="delete-db"></a>Usuń bazę danych Azure Cosmos

```azurepowershell-interactive
# Delete a database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName
Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="azure-cosmos-container"></a>Azure Cosmos Container

Poniższe sekcje pokazują, jak zarządzać kontenera usługi Azure Cosmos w tym:

* [Tworzenie kontenera usługi Azure Cosmos](#create-container)
* [Uzyskiwanie informacji o przepływności kontenera usługi Azure Cosmos](#get-container-ru)
* [Tworzenie kontenera usługi Azure Cosmos za pomocą udostępnionej przepływności](#create-container-ru)
* [Tworzenie kontenera usługi Azure Cosmos przy użyciu niestandardowych indeksowania](#create-container-custom-index)
* [Tworzenie kontenera usługi Azure Cosmos za pomocą indeksowania wyłączone](#create-container-no-index)
* [Tworzenie kontenera usługi Azure Cosmos z unikatowym kluczem i czas wygaśnięcia](#create-container-unique-key-ttl)
* [Tworzenie kontenera usługi Azure Cosmos za pomocą Rozwiązywanie konfliktów](#create-container-lww)
* [Listę wszystkich kontenerów w usłudze Azure Cosmos w bazie danych](#list-all-container)
* [Pobierz jeden kontener usługi Azure Cosmos w bazie danych](#get-container)
* [Usuń kontener usługi Azure Cosmos](#delete-container)

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

### <a id="get-container-ru"></a>Uzyskiwanie informacji o przepływności kontenera usługi Azure Cosmos

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

### <a id="create-container-ru"></a>Tworzenie kontenera usługi Azure Cosmos za pomocą udostępnionej przepływności

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

### <a id="create-container-custom-index"></a>Tworzenie kontenera usługi Azure Cosmos za pomocą zasad niestandardowych indeksu

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

### <a id="create-container-no-index"></a>Tworzenie kontenera usługi Azure Cosmos za pomocą indeksowania wyłączone

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

### <a id="create-container-unique-key-ttl"></a>Tworzenie kontenera usługi Azure Cosmos przy użyciu zasady unikatowych kluczy i czas wygaśnięcia

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

### <a id="create-container-lww"></a>Tworzenie kontenera usługi Azure Cosmos za pomocą Rozwiązywanie konfliktów

Aby utworzyć zasady rozwiązywania konfliktów, aby użyć procedury składowanej, ustaw `"mode"="custom"` i ustawiona ścieżka rozpoznawania jako nazwa procedury składowanej `"conflictResolutionPath"="myResolverStoredProcedure"`. Aby zapisać wszystkie konflikty ConflictsFeed i obsługiwać oddzielnie, ustaw `"mode"="custom"` i `"conflictResolutionPath"=""`

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

### <a id="list-all-container"></a>Listę wszystkich kontenerów w usłudze Azure Cosmos w bazie danych

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

### <a id="get-container"></a>Pobierz jeden kontener usługi Azure Cosmos w bazie danych

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

### <a id="delete-container"></a>Usuń kontener usługi Azure Cosmos

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

## <a name="next-steps"></a>Kolejne kroki

* [Wszystkie przykłady programu PowerShell](powershell-samples.md)
* [Jak zarządzać kontem usługi Azure Cosmos](how-to-manage-database-account.md)
* [Tworzenie kontenera usługi Azure Cosmos](how-to-create-container.md)
* [Konfigurowanie czasu wygaśnięcia w usłudze Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
