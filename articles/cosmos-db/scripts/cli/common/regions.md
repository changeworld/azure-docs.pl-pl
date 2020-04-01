---
title: Dodawanie regionów, zmienianie priorytetu trybu failover, wyzwalanie pracy awaryjnej dla konta usługi Azure Cosmos
description: Dodawanie regionów, zmienianie priorytetu trybu failover, wyzwalanie pracy awaryjnej dla konta usługi Azure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: b7b6be0ce781debcb19b5c0fb7b6a4b0123ef366
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275549"
---
# <a name="add-regions-change-failover-priority-trigger-failover-for-an-azure-cosmos-account-using-azure-cli"></a>Dodawanie regionów, zmienianie priorytetu trybu failover, wyzwalanie pracy awaryjnej dla konta usługi Azure Cosmos przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać interfejsu wiersza polecenia lokalnie, w tym temacie wymaga, aby uruchomić interfejsu wiersza polecenia platformy Azure w wersji 2.0.73 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt pokazuje trzy operacje.

- Dodaj region do istniejącego konta usługi Azure Cosmos.
- Zmienianie regionalnego priorytetu pracy awaryjnej (dotyczy kont przy użyciu automatycznego trybu failover)
- Wyzwalanie ręcznej pracy awaryjnej z regionów podstawowych do pomocniczych (dotyczy kont z ręcznym trybem failover)

> [!NOTE]
> Dodawanie i usuwanie operacji regionu na koncie usługi Cosmos nie można wykonać podczas zmiany innych właściwości.

> [!NOTE]
> W tym przykładzie pokazano przy użyciu konta interfejsu API SQL (Core), ale te operacje są identyczne we wszystkich interfejsach API bazy danych w usłudze Cosmos DB.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/regions.sh "Regional operations for Cosmos DB.")]

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
| [az cosmosdb update](/cli/azure/cosmosdb#az-cosmosdb-update) | Aktualizuje konto usługi Azure Cosmos DB (dodaj lub usuń region). |
| [az cosmosdb zmiana priorytetu pracy awaryjnej](/cli/azure/cosmosdb#az-cosmosdb-failover-priority-change) | Aktualizuj priorytet pracy awaryjnej lub wyzwalanie pracy awaryjnej na koncie usługi Azure Cosmos DB. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia bazy danych usługi Azure Cosmos DB, zobacz [dokumentację interfejsu wiersza polecenia usługi Azure Cosmos DB.](/cli/azure/cosmosdb)

Wszystkie przykłady skryptów interfejsu wiersza polecenia usługi Azure Cosmos DB można znaleźć w [repozytorium github bazy danych usługi Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
