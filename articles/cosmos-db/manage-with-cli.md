---
title: Zarządzanie zasobami Azure Cosmos DB przy użyciu interfejsu wiersza polecenia platformy Azure
description: Użyj interfejsu wiersza polecenia platformy Azure, aby zarządzać kontem Azure Cosmos DB, bazą danych i kontenerami.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: 9ec049311fc158b13bba45deb2974d7cdd531f90
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815038"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Zarządzanie zasobami usługi Azure Cosmos za pomocą interfejsu wiersza polecenia platformy Azure

W poniższym przewodniku opisano typowe polecenia służące do automatyzowania zarządzania kontami Azure Cosmos DB, bazami danych i kontenerami przy użyciu interfejsu wiersza polecenia platformy Azure. Strony referencyjne dla wszystkich poleceń dostępnych w interfejsie wiersza polecenia usługi Azure Cosmos DB są dostępne w [dokumentacji dotyczącej interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/cosmosdb). Więcej przykładów można znaleźć w przykładach [interfejsu wiersza polecenia platformy Azure dla Azure Cosmos DB](cli-samples.md), w tym tworzenia i zarządzania kontami Cosmos DB, bazami danych i kontenerami dla MongoDB, Gremlin, Cassandra i interfejs API tabel.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>Tworzenie konta usługi Azure Cosmos DB

Aby utworzyć konto Azure Cosmos DB przy użyciu interfejsu API SQL, spójności sesji w regionach Wschodnie stany USA i zachodnie stany USA, uruchom następujące polecenie:

```azurecli-interactive
az cosmosdb create \
   --name mycosmosdbaccount # must be lowercase and < 31 characters \
   --resource-group myResourceGroup \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations regionName=EastUS failoverPriority=0 isZoneRedundant=False \
   --locations regionName=WestUS failoverPriority=1 isZoneRedundant=False \
   --enable-multiple-write-locations false
```

> [!IMPORTANT]
> Nazwa konta usługi Azure Cosmos musi być małymi literami.

## <a name="create-a-database"></a>Tworzenie bazy danych

Aby utworzyć bazę danych Cosmos DB, uruchom następujące polecenie:

```azurecli-interactive
az cosmosdb database create \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup
```

## <a name="create-a-container"></a>Tworzenie kontenera

Aby utworzyć kontener Cosmos DB z atrybutami RU/s z 400 i kluczem partycji, uruchom następujące polecenie:

```azurecli-interactive
# Create a container
az cosmosdb collection create \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --partition-key-path /myPartitionKey \
   --throughput 400
```

## <a name="change-the-throughput-of-a-container"></a>Zmiana przepływności kontenera

Aby zmienić przepływność kontenera Cosmos DB na 1000 RU/s, uruchom następujące polecenie:

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --throughput 1000
```

## <a name="list-account-keys"></a>Wyświetl listę kluczy konta

Aby uzyskać klucze dla konta usługi Cosmos, uruchom następujące polecenie:

```azurecli-interactive
# List account keys
az cosmosdb keys list \
   --name  mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="list-connection-strings"></a>Wyświetlanie listy parametrów połączenia

Aby uzyskać parametry połączenia dla konta usługi Cosmos, uruchom następujące polecenie:

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="regenerate-account-key"></a>Ponownie Wygeneruj klucz konta

Aby ponownie wygenerować nowy klucz podstawowy dla konta usługi Cosmos, uruchom następujące polecenie:

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --key-kind primary
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz:

- [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
- [Dokumentacja interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Dodatkowe przykłady interfejsu wiersza polecenia platformy Azure dla Azure Cosmos DB](cli-samples.md)
