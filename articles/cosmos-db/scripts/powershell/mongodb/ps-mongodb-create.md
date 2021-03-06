---
title: Skrypt programu PowerShell do tworzenia bazy danych i kolekcji w interfejsie API usługi Azure Cosmos DB dla usługi MongoDB
description: Skrypt programu Azure PowerShell — usługa Azure Cosmos DB tworzy bazę danych i kolekcję interfejsu API mongodb
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: 59e30de7a0f069cfc69db715115f776034e94909
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365872"
---
# <a name="create-a-database-and-collection-for-azure-cosmos-db---mongodb-api"></a>Tworzenie bazy danych i kolekcji dla usługi Azure Cosmos DB — interfejs API mongodb

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-create.ps1 "Create a database and collection for MongoDB API")]

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
| [Set-AzCosmosDBMongoDBBaba danych](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbmongodbdatabase) | Tworzy nową lub aktualizuje istniejącą bazę danych interfejsu API usługi Cosmos DB MongoDB. |
| [Nowy-AzCosmosDBMongoDBIndex](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbmongodbindex) | Tworzy nowy indeks interfejsu API mongodb. |
| [Zestaw-AzCosmosDBMongoDBKollection](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbmongodbcollection) | Tworzy nową lub aktualizuje istniejącą kolekcję SQL usługi Cosmos DB. |
|**Grupy zasobów platformy Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Więcej przykładowych skryptów programu PowerShell dla usługi Azure Cosmos DB można znaleźć w artykule [Azure Cosmos DB PowerShell scripts](../../../powershell-samples.md) (Skrypty programu PowerShell dla usługi Azure Cosmos DB).