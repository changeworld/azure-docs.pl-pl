---
title: Skrypt interfejsu wiersza polecenia platformy Azure — tworzenie konta, bazy danych i tabeli interfejsu Cassandra API usługi Azure Cosmos DB | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie konta, bazy danych i tabeli interfejsu API Cassandra usługi Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 72eb26599ce2f57e7178d5741851b6e442a62e23
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019598"
---
# <a name="azure-cosmos-db-create-a-cassandra-api-account-using-azure-cli"></a>Azure Cosmos DB: tworzenie konta interfejsu API Cassandra przy użyciu interfejsu wiersza polecenia platformy Azure

Ten przykładowy skrypt interfejsu wiersza polecenia tworzy konto, bazę danych i tabelę interfejsu API Cassandra usługi Azure Cosmos DB.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/create-cosmosdb-cassandra-account/create-cosmosdb-cassandra-account.sh "Create an Azure Cosmos DB Cassandra API account, database, and table.")]

> [!Note]
> Interfejs wiersza polecenia platformy Azure nie obsługuje bezpośrednio tworzenia tabeli bazy danych Cassandra. Należy użyć witryny Azure Portal lub powłoki języka CQL.

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
| [az cosmosdb database create](/cli/azure/cosmosdb/database#az-cosmosdb-database-create) | Tworzy bazę danych usługi Azure Cosmos DB. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładów skryptów interfejsu wiersza polecenia usługi Azure Cosmos DB można znaleźć w [dokumentacji interfejsu wiersza polecenia usługi Azure Cosmos DB](../cli-samples.md).
