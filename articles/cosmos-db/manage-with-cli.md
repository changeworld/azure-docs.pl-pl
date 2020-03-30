---
title: Zarządzanie zasobami usługi Azure Cosmos DB przy użyciu interfejsu wiersza polecenia platformy Azure
description: Użyj interfejsu wiersza polecenia platformy Azure do zarządzania kontem, bazą danych i kontenerami usługi Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: mjbrown
ms.openlocfilehash: 325840f8961fac49e599f1aa567ad8d4137820b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251884"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Zarządzanie zasobami usługi Azure Cosmos przy użyciu interfejsu wiersza polecenia platformy Azure

W poniższym przewodniku opisano typowe polecenia służące do automatyzowania zarządzania kontami, bazami danych i kontenerami usługi Azure Cosmos DB przy użyciu interfejsu wiersza polecenia platformy Azure. Strony odwołań dla wszystkich poleceń interfejsu wiersza polecenia bazy danych usługi Azure Cosmos DB są dostępne w [odwołaniu interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/cli/azure/cosmosdb) Można również znaleźć więcej przykładów w [przykładach interfejsu wiersza polecenia platformy Azure dla usługi Azure Cosmos DB](cli-samples.md), w tym jak tworzyć konta usługi Cosmos DB, bazy danych i kontenerów dla mongodb, Gremlin, Cassandra i table API.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>Tworzenie konta usługi Azure Cosmos DB

Utwórz konto usługi Azure Cosmos DB z interfejsem API SQL, spójność sesji w regionach Zachodnie stany USA 2 i wschodnie stany USA 2:

> [!IMPORTANT]
> Nazwa konta usługi Azure Cosmos musi być małe i mniej niż 31 znaków.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount' #needs to be lower case and less than 31 characters

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --default-consistency-level Session \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False
```

## <a name="add-or-remove-regions"></a>Add or remove regions (Dodawanie lub usuwanie regionów)

Utwórz konto usługi Azure Cosmos z dwoma regionami, dodaj region i usuń region.

> [!NOTE]
> Nie można jednocześnie dodawać `locations` ani usuwać regionów i zmieniać innych właściwości konta usługi Azure Cosmos. Modyfikowanie regionów musi być wykonywane jako oddzielna operacja niż jakakolwiek inna zmiana zasobu konta.
> [!NOTE]
> To polecenie umożliwia dodawanie i usuwanie regionów, ale nie pozwala na modyfikowanie priorytetów pracy awaryjnej ani wyzwalanie ręcznego trybu failover. Zobacz [Ustawianie priorytetu pracy awaryjnej](#set-failover-priority) i [ręczne uruchamianie trybu failover wyzwalania](#trigger-manual-failover).

```azurecli-interactive
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount' # must be lower case and <31 characters

# Create an account with 2 regions
az cosmosdb create --name $accountName --resource-group $resourceGroupName \
    --locations regionName= "West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName= "East US 2" failoverPriority=1 isZoneRedundant=False

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName= "West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName= "East US 2" failoverPriority=1 isZoneRedundant=False \
    --locations regionName= "South Central US" failoverPriority=2 isZoneRedundant=False

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName= "West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName= "East US 2" failoverPriority=1 isZoneRedundant=False
```

## <a name="enable-multiple-write-regions"></a>Włączanie wielu regionów zapisu

Włączanie multi-master dla konta usługi Cosmos

```azurecli-interactive
# Update an Azure Cosmos account from single to multi-master
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-multiple-write-locations true
```

## <a name="set-failover-priority"></a>Ustawianie priorytetu trybu failover

Ustawianie priorytetu trybu failover dla konta usługi Azure Cosmos skonfigurowanego do automatycznego trybu failover

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Make South Central US the next region to fail over to instead of East US 2
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'West US 2'=0 'South Central US'=1 'East US 2'=2
```

## <a name="enable-automatic-failover"></a>Włączanie automatycznego trybu failover

```azurecli-interactive
# Enable automatic failover on an existing account
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-automatic-failover true
```

## <a name="trigger-manual-failover"></a>Ręczne uruchamianie awaryjne wyzwalania

> [!CAUTION]
> Zmiana regionu z priorytetem = 0 spowoduje ręczne przejście awaryjne dla konta usługi Azure Cosmos. Żadna inna zmiana priorytetu nie spowoduje przerój awaryjny.

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Trigger a manual failover to promote East US 2 as new write region
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'East US 2'=0 'South Central US'=1 'West US 2'=2
```

## <a name="list-all-account-keys"></a><a id="list-account-keys"></a>Wyświetl listę wszystkich kluczy konta

Pobierz wszystkie klucze dla konta usługi Cosmos.

```azurecli-interactive
# List all account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
   -n $accountName \
   -g $resourceGroupName
```

## <a name="list-read-only-account-keys"></a>Lista kluczy kont tylko do odczytu

Pobierz klucze tylko do odczytu dla konta usługi Cosmos.

```azurecli-interactive
# List read-only account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type read-only-keys
```

## <a name="list-connection-strings"></a>Lista ciągów połączeń

Pobierz parametry połączenia dla konta usługi Cosmos.

```azurecli-interactive
# List connection strings
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type connection-strings
```

## <a name="regenerate-account-key"></a>Ponowne generowanie klucza konta

Ponownie wygeneruj nowy klucz dla konta usługi Cosmos.

```azurecli-interactive
# Regenerate secondary account keys
# key-kind values: primary, primaryReadonly, secondary, secondaryReadonly
az cosmosdb keys regenerate \
    -n $accountName \
    -g $resourceGroupName \
    --key-kind secondary
```

## <a name="create-a-database"></a>Tworzenie bazy danych

Tworzenie bazy danych usługi Cosmos.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName
```

## <a name="create-a-database-with-shared-throughput"></a>Tworzenie bazy danych z udostępnioną przepływnością

Tworzenie bazy danych usługi Cosmos z udostępnionej przepływności.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
throughput=400

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $throughput
```

## <a name="change-the-throughput-of-a-database"></a>Zmienianie przepływności bazy danych

Zwiększ przepływność bazy danych usługi Cosmos o 1000 ru/s.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql database throughput show \
    -g $resourceGroupName -a $accountName -n $databaseName \
    --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql database throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $newRU
```

## <a name="create-a-container"></a>Tworzenie kontenera

Utwórz kontener usługi Cosmos z domyślnymi zasadami indeksu, kluczem partycji i ru/s 400.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput
```

## <a name="create-a-container-with-ttl"></a>Tworzenie kontenera z ttl

Utwórz kontener usługi Cosmos z włączoną funkcją czasu wygaśnięcia.

```azurecli-interactive
# Create an Azure Cosmos container with TTL of one day
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'
databaseName = 'database1'
containerName = 'container1'

az cosmosdb sql container update \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $containerName \
    --ttl = 86400
```

## <a name="create-a-container-with-a-custom-index-policy"></a>Tworzenie kontenera z niestandardowymi zasadami indeksu

Utwórz kontener usługi Cosmos z niestandardowymi zasadami indeksu, indeksem przestrzennym, indeksem złożonym, kluczem partycji i ru/s 400.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

# Generate a unique 10 character alphanumeric string to ensure unique resource names
uniqueId=$(env LC_CTYPE=C tr -dc 'a-z0-9' < /dev/urandom | fold -w 10 | head -n 1)

# Define the index policy for the container, include spatial and composite indexes
idxpolicy=$(cat << EOF
{
    "indexingMode": "consistent",
    "includedPaths": [
        {"path": "/*"}
    ],
    "excludedPaths": [
        { "path": "/headquarters/employees/?"}
    ],
    "spatialIndexes": [
        {"path": "/*", "types": ["Point"]}
    ],
    "compositeIndexes":[
        [
            { "path":"/name", "order":"ascending" },
            { "path":"/age", "order":"descending" }
        ]
    ]
}
EOF
)
# Persist index policy to json file
echo "$idxpolicy" > "idxpolicy-$uniqueId.json"


az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput \
    --idx @idxpolicy-$uniqueId.json

# Clean up temporary index policy file
rm -f "idxpolicy-$uniqueId.json"
```

## <a name="change-the-throughput-of-a-container"></a>Zmienianie przepływności kontenera

Zwiększ przepustowość kontenera usługi Cosmos o 1000 ru/s.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql container throughput show \
    -g $resourceGroupName -a $accountName -d $databaseName \
    -n $containerName --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql container throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $containerName \
    --throughput $newRU
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz:

- [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
- [Odwołanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Dodatkowe przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Cosmos DB](cli-samples.md)
