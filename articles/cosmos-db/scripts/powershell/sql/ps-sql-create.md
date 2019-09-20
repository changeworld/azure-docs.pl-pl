---
title: Skrypt Azure PowerShell — Azure Cosmos DB tworzenia bazy danych i kontenera interfejsu API SQL (rdzeń)
description: Skrypt Azure PowerShell — Azure Cosmos DB tworzenia bazy danych i kontenera interfejsu API SQL (rdzeń)
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 09/20/2019
ms.author: mjbrown
ms.openlocfilehash: 29b0e81dd6eac62a18307788bb023ac3d802f11a
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71154851"
---
# <a name="create-a-database-and-container-for-azure-cosmos-db---sql-core-api"></a>Tworzenie bazy danych i kontenera dla interfejsu API Azure Cosmos DB-SQL (rdzeń)

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt tworzy konto Cosmos dla interfejsu API języka SQL (rdzeń) w dwóch regionach z spójnością poziomu sesji, bazą danych o udostępnionej przepływności i kontenerem z kluczem partycji, niestandardowymi zasadami indeksowania, unikatowymi zasadami kluczy, czasem wygaśnięcia, dedykowaną przepływność i ostatnim składnikiem zapisywania. zasady rozwiązywania konfliktów z niestandardową ścieżką rozwiązywania konfliktów, `multipleWriteLocations=true`która będzie używana w przypadku.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-create.ps1 "Create an account, database, and container for SQL (Core) API")]

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
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | Tworzy zasób. |
|**Grupy zasobów platformy Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Więcej przykładowych skryptów programu PowerShell dla usługi Azure Cosmos DB można znaleźć w artykule [Azure Cosmos DB PowerShell scripts](../../../powershell-samples.md) (Skrypty programu PowerShell dla usługi Azure Cosmos DB).
