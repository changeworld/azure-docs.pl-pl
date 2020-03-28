---
title: Skrypt programu PowerShell do zmiany priorytetu trybu failover dla jednowładnego konta usługi Azure Cosmos
description: Przykład skryptu programu Azure PowerShell — zmiana priorytetu trybu failover lub wyzwalania pracy awaryjnej dla konta jednowładnikowego usługi Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: a81938675e72d9ec3a18c920121951e38580b91e
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366120"
---
# <a name="change-failover-priority-or-trigger-failover-for-an-azure-cosmos-db-single-master-account-using-powershell"></a>Zmienianie priorytetu trybu failover lub wyzwalania pracy awaryjnej dla konta jednowładnikowego usługi Azure Cosmos DB przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Przykładowy skrypt

> [!NOTE]
> Wszelkie zmiany w `failoverPriority=0` regionie z wyzwalaczy ręcznej pracy awaryjnej i można zrobić tylko na koncie skonfigurowanym do ręcznego trybu failover. Zmiany we wszystkich innych regionach po prostu zmienia priorytet pracy awaryjnej dla konta usługi Cosmos.
> [!NOTE]
> W tym przykładzie pokazano przy użyciu konta interfejsu API SQL (Core). Aby użyć tego przykładu dla innych interfejsów API, skopiuj powiązane właściwości i zastosuj do skryptu specyficznego dla interfejsu API

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-failover-priority-update.ps1 "Update failover priority for an Azure Cosmos account or trigger a manual failover")]

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
| [Aktualizacja-AzCosmosDBAccountFailoverPriority](https://docs.microsoft.com/powershell/module/az.cosmosdb/update-azcosmosdbaccountfailoverpriority) | Zaktualizuj kolejność priorytetów trybu failover regionów konta usługi Cosmos DB. |
|**Grupy zasobów platformy Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Więcej przykładowych skryptów programu PowerShell dla usługi Azure Cosmos DB można znaleźć w artykule [Azure Cosmos DB PowerShell scripts](../../../powershell-samples.md) (Skrypty programu PowerShell dla usługi Azure Cosmos DB).
