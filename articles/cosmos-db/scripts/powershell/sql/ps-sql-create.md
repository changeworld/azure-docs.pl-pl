---
title: Skrypt programu PowerShell do tworzenia bazy danych i kontenera interfejsu SQL usługi Azure Cosmos DB DB
description: Skrypt programu Azure PowerShell — usługa Azure Cosmos DB tworzy bazę danych i kontener interfejsu API SQL
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 03/17/2020
ms.author: mjbrown
ms.openlocfilehash: 719e2cd831a982c62ab965cd7dc8a37c4cb41265
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365622"
---
# <a name="create-a-database-and-container-for-azure-cosmos-db---sql-api"></a>Tworzenie bazy danych i kontenera dla usługi Azure Cosmos DB — interfejs API SQL

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt tworzy konto usługi Cosmos dla interfejsu API SQL (Core) w dwóch regionach o spójności na poziomie sesji, bazy danych i kontenerze z kluczem partycji, niestandardowymi zasadami `multipleWriteLocations=true`indeksowania, unikatowymi zasadami klucza, ttl, dedykowaną przepływnością i ostatnim modułem zapisu wygrywa zasady rozwiązywania konfliktów z niestandardową ścieżką rozwiązywania konfliktów, która będzie używana podczas .

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-create.ps1 "Create an account, database, and container for SQL API")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
|**Azure Cosmos DB**| |
| [Nowe konto AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Tworzy nowe konto usługi Cosmos DB. |
| [Zestaw-AzCosmosDBSqlDatabase](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbsqldatabase) | Tworzy nową lub aktualizuje istniejącą bazę danych SQL usługi Cosmos DB. |
| [Nowy-AzCosmosDBSqlUniqueKey](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekey) | Tworzy nowy obiekt Unikatowy klucz sql usługi DB usługi Cosmos. |
| [Nowy-AzCosmosDBSqlUniqueKeyPolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekeypolicy) | Tworzy nowy obiekt Unikatowy klucz cyfrowy usługi Cosmos DB SQL. |
| [Nowy-AzCosmosDBSqlIncludedPathIndex](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpathindex) | Tworzy nowy obiekt typu PSIndexes używany jako parametr dla Set-AzCosmosDBSqlIncludedPath. |
| [Nowy-AzCosmosDBSqlIncludedPath](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpath) | Tworzy nowy obiekt typu PSIncludedPath używany jako parametr dla New-AzCosmosDBSqlIndexingPolicy. |
| [Nowy-AzCosmosDBSqlIndexingPolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlindexingpolicy) | Tworzy nowy obiekt typu PSSqlIndexingPolicy używany jako parametr dla Set-AzCosmosDBSqlContainer. |
| [Nowy-AzCosmosDBSqlConflictRerozwiązywapolicja](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlconflictresolutionpolicy) | Tworzy nowy obiekt typu PSSqlConflictResolutionPolicy używany jako parametr dla Set-AzCosmosDBSqlContainer. |
| [Set-AzCosmosDBSqlContainer](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbsqlcontainer) | Tworzy nowy lub aktualizuje istniejący kontener SQL usługi Cosmos DB. |
|**Grupy zasobów platformy Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Więcej przykładowych skryptów programu PowerShell dla usługi Azure Cosmos DB można znaleźć w artykule [Azure Cosmos DB PowerShell scripts](../../../powershell-samples.md) (Skrypty programu PowerShell dla usługi Azure Cosmos DB).
