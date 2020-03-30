---
title: Skrypt programu PowerShell do tworzenia przestrzeni kluczy i tabeli interfejsu API usługi Azure Cosmos DB Cassandra
description: Skrypt programu Azure PowerShell — usługa Azure Cosmos DB tworzy przestrzeń kluczy i tabelę interfejsu API cassandra
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: 1fcded19731d412a4892e231f4445c51b330b1b4
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366141"
---
# <a name="create-a-keyspace-and-table-for-azure-cosmos-db---cassandra-api"></a>Tworzenie przestrzeni kluczy i tabeli dla usługi Azure Cosmos DB — interfejs API Cassandra

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-create.ps1 "Create a keyspace and table for Cassandra API")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
|**Zasoby platformy Azure**| |
| [Nowe źródło AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | Tworzy zasób. |
|**Azure Cosmos DB**| |
| [Set-AzCosmosDBCassandraKeyspace](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbcassandrakeyspace) | Tworzy lub aktualizuje przestrzeń kluczy interfejsu API usługi Cosmos DB Cassandra. |
| [Nowy-AzCosmosDBCassandraClusterKey](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbcassandraclusterkey) | Tworzy nowy klucz klastra CosmosDB Cassandra. |
| [Nowy-AzCosmosDBCassandraColumn](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbcassandracolumn) | Tworzy nową kolumnę CosmosDB Cassandra. |
| [Nowy-AzCosmosDBCassandraSchema](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbcassandraschema) | Tworzy nowy schemat Cassandra usługi CosmosDB. |
| [Set-AzCosmosDBCassandraTable](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbcassandratable) | Tworzy lub aktualizuje tabelę interfejsu API usługi Cosmos DB Cassandra. |
|**Grupy zasobów platformy Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Więcej przykładowych skryptów programu PowerShell dla usługi Azure Cosmos DB można znaleźć w artykule [Azure Cosmos DB PowerShell scripts](../../../powershell-samples.md) (Skrypty programu PowerShell dla usługi Azure Cosmos DB).