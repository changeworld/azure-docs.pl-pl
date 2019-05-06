---
title: Skrypt programu Azure PowerShell — Tworzenie konta interfejsu API tabeli usługi Azure Cosmos DB
description: Skrypt programu Azure PowerShell przykładowy — Tworzenie konta interfejsu API tabeli usługi Azure Cosmos DB
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: PowerShell
ms.topic: sample
ms.date: 08/08/2018
ms.reviewer: sngun
ms.openlocfilehash: 1b82d8440862f67794da2b61db5b691d7e8385ad
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080800"
---
# <a name="azure-cosmos-db-create-a-table-api-account-using-powershell"></a>Usługa Azure Cosmos DB tworzenie konta interfejsu API tabel przy użyciu programu PowerShell

Ten przykładowy skrypt programu PowerShell tworzy konto interfejsu API tabel usługi Azure Cosmos DB. 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../../powershell_scripts/cosmosdb/create-and-configure-Table-database/create-and-configure-Table-database.ps1 "Create an Azure Cosmos DB account")]

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
