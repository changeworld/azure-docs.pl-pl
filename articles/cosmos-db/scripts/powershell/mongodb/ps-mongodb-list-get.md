---
title: Skrypt programu PowerShell do listy i uzyskania operacji w interfejsie API usługi Azure Cosmos DB dla usługi MongoDB
description: Skrypt programu Azure PowerShell — lista usługi Azure Cosmos DB i operacje dla interfejsu API usługi MongoDB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: 686b140915e90adefa15084c2e8317404b91ed09
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365897"
---
# <a name="list-and-get-databases-and-graphs-for-azure-cosmos-db---mongodb-api"></a>Lista i pobierz bazy danych i wykresy dla usługi Azure Cosmos DB — interfejs API MongoDB

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-list-get.ps1 "List or get databases or collections for MongoDB API")]

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
| [Konto Get-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Wyświetla listę kont usługi Cosmos DB lub pobiera określone konto usługi Cosmos DB. |
| [Get-AzCosmosDBMongoDBBaba danych](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbmongodbdatabase) | Wyświetla listę baz danych interfejsu API mongodb usługi Cosmos DB w koncie lub pobiera określoną bazę danych interfejsu API mogodb usługi Cosmos DB na koncie. |
| [Get-AzCosmosDBMongoDBKollection](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbmongodbcollection) | Wyświetla listę kolekcji interfejsu API usługi Cosmos DB MongoDB lub pobiera określoną kolekcję interfejsu API usługi Cosmos DB MongoDB w bazie danych |
|**Grupy zasobów platformy Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Więcej przykładowych skryptów programu PowerShell dla usługi Azure Cosmos DB można znaleźć w artykule [Azure Cosmos DB PowerShell scripts](../../../powershell-samples.md) (Skrypty programu PowerShell dla usługi Azure Cosmos DB).