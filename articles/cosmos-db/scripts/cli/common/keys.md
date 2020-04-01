---
title: Praca z kluczami kont i ciągami połączeń dla konta usługi Azure Cosmos
description: Praca z kluczami kont i ciągami połączeń dla konta usługi Azure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: e766f52d729a4f916eefd2b148d926d929b4f540
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275556"
---
# <a name="work-with-account-keys-and-connection-strings-for-an-azure-cosmos-account-using-azure-cli"></a>Praca z kluczami kont i ciągami połączeń dla konta usługi Azure Cosmos przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać interfejsu wiersza polecenia lokalnie, w tym temacie wymaga, aby uruchomić interfejsu wiersza polecenia platformy Azure w wersji 2.0.73 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt pokazuje cztery operacje.

- Wyświetl listę wszystkich kluczy konta
- Lista kluczy kont tylko do odczytu
- Lista ciągów połączeń
- Ponowne generowanie kluczy konta

> [!NOTE]
> W tym przykładzie pokazano przy użyciu konta interfejsu API SQL (Core), ale operacje klucza konta i parametry połączenia są identyczne we wszystkich interfejsach API bazy danych w usłudze Cosmos DB.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/keys.sh "Keys and connection string operations for Cosmos DB.")]

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
| [lista klawiszy az cosmosdb](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) | Wyświetl listę kluczy dla konta usługi Azure Cosmos DB. |
| [az cosmosdb list-read-only-keys az cosmosdb list-read-only-keys az cosmosdb list-read-only-keys AZ](/cli/azure/cosmosdb#az-cosmosdb-list-read-only-keys) | Wyświetl listę kluczy tylko do odczytu dla konta usługi Azure Cosmos DB. |
| [az cosmosdb list-connection-strings](/cli/azure/cosmosdb#az-cosmosdb-list-connection-strings) | Wyświetl listę ciągów połączeń dla konta usługi Azure Cosmos DB. |
| [az cosmosdb regenerate-key](/cli/azure/cosmosdb#az-cosmosdb-regenerate-key) | Ponowne generowanie kluczy dla konta usługi Azure Cosmos DB. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia bazy danych usługi Azure Cosmos DB, zobacz [dokumentację interfejsu wiersza polecenia usługi Azure Cosmos DB.](/cli/azure/cosmosdb)

Wszystkie przykłady skryptów interfejsu wiersza polecenia usługi Azure Cosmos DB można znaleźć w [repozytorium github bazy danych usługi Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
