---
title: Program Azure PowerShell script — Utwórz kontener przy użyciu klucza duża partycja na koncie usługi Azure Cosmos
description: Skrypt programu Azure PowerShell przykładowy — Utwórz kontener przy użyciu klucza duża partycja na koncie usługi Azure Cosmos
author: markjbrown
ms.service: cosmos-db
ms.topic: samples
ms.date: 07/03/2019
ms.author: mjbrown
ms.openlocfilehash: c4e8f26c59312b5216a6834e34c5bbc2bf58563a
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566806"
---
# <a name="create-a-container-with-a-large-partition-key-in-an-azure-cosmos-account-using-powershell"></a>Utwórz kontener przy użyciu klucza duża partycja na koncie usługi Azure Cosmos przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-container-large-partition-key.ps1 "Create a container with a large partition key in an Azure Cosmos account")]

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
| [Nowe AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | Tworzy zasób. |
|**Grupy zasobów platformy Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Więcej przykładowych skryptów programu PowerShell dla usługi Azure Cosmos DB można znaleźć w artykule [Azure Cosmos DB PowerShell scripts](../../../powershell-samples.md) (Skrypty programu PowerShell dla usługi Azure Cosmos DB).