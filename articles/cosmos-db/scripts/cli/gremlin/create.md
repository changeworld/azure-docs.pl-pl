---
title: Tworzenie bazy danych i wykresu Gremlin dla usługi Azure Cosmos DB
description: Tworzenie bazy danych i wykresu Gremlin dla usługi Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 46914952e59e5f4fc3d5cd78f80842de09b11184
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275402"
---
# <a name="create-an-azure-cosmos-gremlin-api-account-database-and-graph-using-azure-cli"></a>Tworzenie konta, bazy danych i wykresu interfejsu API usługi Azure Cosmos Gremlin przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać interfejsu wiersza polecenia lokalnie, w tym temacie wymaga, aby uruchomić interfejsu wiersza polecenia platformy Azure w wersji 2.0.73 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/gremlin/create.sh "Create an Azure Cosmos DB Gremlin API account, database, and graph.")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Tworzy konto usługi Azure Cosmos DB. |
| [az cosmosdb gremlin bazy danych utworzyć](/cli/azure/cosmosdb/gremlin/database#az-cosmosdb-gremlin-database-create) | Tworzy bazę danych azure cosmos gremlin. |
| [az cosmosdb gremlin wykres tworzenia](/cli/azure/cosmosdb/gremlin/graph#az-cosmosdb-gremlin-graph-create) | Tworzy wykres Azure Cosmos Gremlin. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia bazy danych usługi Azure Cosmos DB, zobacz [dokumentację interfejsu wiersza polecenia usługi Azure Cosmos DB.](/cli/azure/cosmosdb)

Wszystkie przykłady skryptów interfejsu wiersza polecenia usługi Azure Cosmos DB można znaleźć w [repozytorium github bazy danych usługi Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
