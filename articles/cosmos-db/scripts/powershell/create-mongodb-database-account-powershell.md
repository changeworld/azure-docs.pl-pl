---
title: Program Azure PowerShell script — Tworzenie konta interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB
description: Skrypt programu Azure PowerShell przykładowy — Tworzenie konta interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.devlang: PowerShell
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 05/29/2019
ms.reviewer: sngun
ms.openlocfilehash: 1fc66a7933b5642e29a6f8ddfba86357b397bd2d
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474864"
---
# <a name="create-an-azure-cosmos-db-account-with-azure-cosmos-dbs-api-for-mongodb-using-powershell"></a>Tworzenie konta usługi Azure Cosmos DB przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB za pomocą programu PowerShell

Ten przykładowy skrypt programu PowerShell tworzy konto Cosmos przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB. 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../../powershell_scripts/cosmosdb/create-and-configure-mongodb-database/create-and-configure-mongodb-database.ps1 "Create an Azure Cosmos DB account")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [Nowe AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | Tworzy serwer logiczny hostujący bazę danych lub pulę elastyczną. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Więcej przykładowych skryptów programu PowerShell dla usługi Azure Cosmos DB można znaleźć w artykule [Azure Cosmos DB PowerShell scripts](../../powershell-samples.md) (Skrypty programu PowerShell dla usługi Azure Cosmos DB).
