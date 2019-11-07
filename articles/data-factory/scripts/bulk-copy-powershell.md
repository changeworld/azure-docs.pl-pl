---
title: 'Skrypt programu PowerShell: Zbiorcze kopiowanie danych przy użyciu Azure Data Factory '
description: Ten skrypt programu PowerShell pokazuje, jak używać Azure Data Factory do kopiowania danych ze źródłowego magazynu danych do docelowego magazynu danych.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: 8ab373d049e34a6a5528a4e0a3418a3304835d4b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684435"
---
# <a name="powershell-script---copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Skrypt programu PowerShell — kopiowanie wielu tabel zbiorczo przy użyciu Azure Data Factory

Ten przykładowy skrypt programu PowerShell kopiuje dane z wielu tabel w bazie danych SQL Azure do usługi Azure SQL Data Warehouse.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Zobacz [Samouczek: kopia Zbiorcza](../tutorial-bulk-copy.md#prerequisites) dla wymagań wstępnych dotyczących uruchamiania tego przykładu.

## <a name="sample-script"></a>Przykładowy skrypt

> [!IMPORTANT]
> Ten skrypt tworzy pliki JSON, które definiują Data Factory jednostki (połączone usługi, zestawy danych i potok) na dysku twardym w c:\ system32\drivers\etc.

[!code-powershell[main](../../../powershell_scripts/data-factory/bulk-copy-from-sql-databse-to-sql-data-warehouse/bulk-copy-from-sql-database-to-sql-data-warehouse.ps1 "Bulk copy from Azure SQL Database => Azure SQL Data Warehouse")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po uruchomieniu przykładowego skryptu można użyć następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Aby usunąć fabrykę danych z grupy zasobów, uruchom następujące polecenie: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-azdatafactoryv2) | Tworzenie fabryki danych. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-azdatafactoryv2linkedservice) | Tworzy połączoną usługę w fabryce danych. Połączona usługa łączy magazyn danych lub dane obliczeniowe z fabryką danych. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/set-azdatafactoryv2dataset) | Tworzy zestaw danych w fabryce danych. Zestaw danych reprezentuje dane wejściowe/wyjściowe dla działania w potoku. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-azdatafactoryv2pipeline) | Tworzy potok w fabryce danych. Potok zawiera jedną lub więcej działań, które wykonują określoną operację. W tym potoku działanie kopiowania kopiuje dane z jednej lokalizacji do innej lokalizacji w Blob Storage platformy Azure. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-azdatafactoryv2pipeline) | Tworzy przebieg dla potoku. Innymi słowy, uruchamia potok. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-azdatafactoryv2activityrun) | Pobiera szczegółowe informacje o przebiegu działania (przebieg działania) w potoku. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów programu Azure Data Factory PowerShell można znaleźć w [skryptach programu Azure Data Factory PowerShell](../samples-powershell.md).
