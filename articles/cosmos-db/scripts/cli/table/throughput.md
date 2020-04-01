---
title: Aktualizowanie usług RU/s dla tabeli interfejsu API tabeli dla usługi Azure Cosmos DB
description: Aktualizowanie usług RU/s dla tabeli interfejsu API tabeli dla usługi Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 8cc04b766ba63fb522417310177a539ea04fcdd6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275381"
---
# <a name="update-rus-for-a-table-api-table-for-azure-cosmos-db-azure-cli"></a>Aktualizowanie usług RU/s dla tabeli interfejsu API tabeli dla interfejsu wiersza polecenia platformy Azure usługi Azure DB usługi Azure

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać interfejsu wiersza polecenia lokalnie, w tym temacie wymaga, aby uruchomić interfejsu wiersza polecenia platformy Azure w wersji 2.0.73 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt tworzy tabelę interfejsu API tabeli, a następnie aktualizuje przepływność tabeli.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/throughput.sh "Update RU/s for a Table API table.")]

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
| [az cosmosdb tabela utworzyć](/cli/azure/cosmosdb/table#az-cosmosdb-table-create) | Tworzy tabelę interfejsu API tabeli usługi Azure Cosmos. |
| [Aktualizacja przepływności tabeli az cosmosdb](/cli/azure/cosmosdb/table/throughput#az-cosmosdb-table-throughput-update) | Aktualizowanie usług RU/s dla tabeli interfejsu API tabeli usługi Azure Cosmos. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia bazy danych usługi Azure Cosmos DB, zobacz [dokumentację interfejsu wiersza polecenia usługi Azure Cosmos DB.](/cli/azure/cosmosdb)

Wszystkie przykłady skryptów interfejsu wiersza polecenia usługi Azure Cosmos DB można znaleźć w [repozytorium github bazy danych usługi Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
