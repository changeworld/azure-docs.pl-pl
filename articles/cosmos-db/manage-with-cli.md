---
title: Zarządzanie zasobami usługi Azure Cosmos DB przy użyciu wiersza polecenia platformy Azure
description: Użyj wiersza polecenia platformy Azure do zarządzania kontem usługi Azure Cosmos DB, bazy danych i kontenerów.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 4/8/2019
ms.author: mjbrown
ms.openlocfilehash: 1d19e58b2d1381725de490b68d9e4d00a2ca4cb6
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59495485"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Zarządzanie zasobami usługi Azure Cosmos przy użyciu wiersza polecenia platformy Azure

Następującymi wskazówkami w tym artykule opisano typowe polecenia do automatyzowania zarządzania kontami usługi Azure Cosmos DB, bazy danych i kontenerów za pomocą narzędzia wiersza polecenia platformy Azure. Strony referencyjne dla wszystkich poleceń dostępnych w interfejsie wiersza polecenia usługi Azure Cosmos DB są dostępne w [dokumentacji dotyczącej interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/cosmosdb). Można także znaleźć więcej przykładów w [przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Cosmos DB](cli-samples.md), w tym jak utworzyć i zarządzać kont usługi Cosmos DB, bazy danych i kontenerów dla bazy danych MongoDB, Gremlin, Cassandra i interfejsu API tabel.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>Tworzenie konta usługi Azure Cosmos DB

Aby utworzyć konto usługi Azure Cosmos DB z interfejsem API SQL, spójność sesji w regionach wschodnie stany USA i zachodnie stany USA, uruchom następujące polecenie:

```azurecli-interactive
az cosmosdb create \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations EastUS=0 WestUS=1 \
   --enable-multiple-write-locations false
```

> [!IMPORTANT]
> Nazwa konta usługi Azure Cosmos musi być mała.

## <a name="create-a-database"></a>Tworzenie bazy danych

Aby utworzyć bazę danych Cosmos DB, uruchom następujące polecenie:

```azurecli-interactive
az cosmosdb database create \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup
```

## <a name="create-a-container"></a>Tworzenie kontenera

Aby utworzyć kontenera usługi Cosmos DB przy użyciu jednostek RU/s, 400, jak i klucz partycji, uruchom następujące polecenie:

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

## <a name="change-the-throughput-of-a-container"></a>Zmień przepływność kontenera

Aby zmienić przepływność kontenera usługi Cosmos DB na 1000 jednostek RU/s, uruchom następujące polecenie:

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

Aby pobrać klucze służące do Twojego konta usługi Cosmos, uruchom następujące polecenie:

```azurecli-interactive
# List account keys
az cosmosdb list-keys \
   --name  mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="list-connection-strings"></a>Lista parametrów połączenia

Aby uzyskać parametry połączenia dla konta usługi Cosmos, uruchom następujące polecenie:

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="regenerate-account-key"></a>Ponowne generowanie klucza konta

Aby ponownie wygenerować nowy klucz podstawowy dla konta usługi Cosmos, uruchom następujące polecenie:

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --key-kind primary
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure zobacz:

- [Zainstaluj interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
- [Odwołanie do wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Więcej przykładów interfejsu wiersza polecenia platformy Azure dla usługi Azure Cosmos DB](cli-samples.md)
