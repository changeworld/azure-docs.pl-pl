---
title: Skrypt programu PowerShell do aktualizacji programu RU/s dla interfejsu API gremlin usługi Azure Cosmos DB
description: Skrypt programu Azure PowerShell — usługa Azure Cosmos DB Update RU/s dla interfejsu API gremlin
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: 61c06fca0fe2f2449f67890dd962905642605716
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366021"
---
# <a name="update-rus-for-a-database-or-graph-for-azure-cosmos-db---gremlin-api"></a>Aktualizowanie usług RU/s dla bazy danych lub wykresu dla usługi Azure Cosmos DB — interfejs API Gremlin

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-ru-update.ps1 "Update throughput on a database or graph for Gremlin API")]

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
| [Zestaw-AzCosmosDBGremlinBaza danych](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbgremlindatabase) | Tworzy lub aktualizuje bazę danych api gremlin. |
| [Get-AzCosmosDBGremlinGraph](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbgremlingraph) | Pobiera wykres interfejsu API Gremlin. |
| [Zestaw-AzCosmosDBGremlinGraph](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbgremlingraph) | Tworzy lub aktualizuje wykres api gremlin. |
|**Grupy zasobów platformy Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Więcej przykładowych skryptów programu PowerShell dla usługi Azure Cosmos DB można znaleźć w artykule [Azure Cosmos DB PowerShell scripts](../../../powershell-samples.md) (Skrypty programu PowerShell dla usługi Azure Cosmos DB).