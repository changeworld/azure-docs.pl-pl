---
title: Tworzenie tabeli interfejsu API tabeli dla usługi Azure Cosmos DB
description: Tworzenie tabeli interfejsu API tabeli dla usługi Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: aea0697471d62e5119bbeea6a618c6e962983054
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275479"
---
# <a name="create-an-azure-cosmos-table-api-account-and-table-using-azure-cli"></a>Tworzenie konta i tabeli interfejsu API tabeli usługi Azure Cosmos przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać interfejsu wiersza polecenia lokalnie, w tym temacie wymaga, aby uruchomić interfejsu wiersza polecenia platformy Azure w wersji 2.0.73 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/create.sh "Create an Azure Cosmos DB Table API account and table.")]

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
| [az group delete](/cli/azure/resource#az-resource-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia bazy danych usługi Azure Cosmos DB, zobacz [dokumentację interfejsu wiersza polecenia usługi Azure Cosmos DB.](/cli/azure/cosmosdb)

Wszystkie przykłady skryptów interfejsu wiersza polecenia usługi Azure Cosmos DB można znaleźć w [repozytorium github bazy danych usługi Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
