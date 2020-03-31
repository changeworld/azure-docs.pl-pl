---
title: Kopiowanie danych zbiorczo przy użyciu programu PowerShell
description: Ten skrypt programu PowerShell pokazuje, jak zbiorczo kopiować dane z magazynu danych źródłowych do docelowego magazynu danych za pomocą usługi Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.custom: seo-lt-2019
ms.date: 10/31/2017
ms.openlocfilehash: efc79f82a2181099f832da0d4a17fc370bf4f7f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929864"
---
# <a name="powershell-script---copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Skrypt programu PowerShell — kopiowanie wielu tabel zbiorczo przy użyciu usługi Azure Data Factory

Ten przykładowy skrypt programu PowerShell kopiuje dane z wielu tabel w bazie danych SQL platformy Azure do magazynu danych SQL platformy Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Zobacz [samouczek: kopia zbiorcza](../tutorial-bulk-copy.md#prerequisites) dla wymagań wstępnych do uruchomienia tego przykładu.

## <a name="sample-script"></a>Przykładowy skrypt

> [!IMPORTANT]
> Ten skrypt tworzy pliki JSON definiujące jednostki Data Factory (usługa połączona, zestaw danych i potok) na dysku twardym w c:\ Folder.

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
| [Zestaw-AzDataFactoryV2](/powershell/module/az.datafactory/set-azdatafactoryv2) | Tworzenie fabryki danych. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-azdatafactoryv2linkedservice) | Tworzy usługę połączony w fabryce danych. Połączona usługa łączy magazyn danych lub obliczeń z fabryką danych. |
| [Zestaw danych Zestawu AzDataFactoryV2](/powershell/module/az.datafactory/set-azdatafactoryv2dataset) | Tworzy zestaw danych w fabryce danych. Zestaw danych reprezentuje dane wejściowe/wyjściowe dla działania w potoku. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-azdatafactoryv2pipeline) | Tworzy potok w fabryce danych. Potok zawiera co najmniej jedną operację, która wykonuje określoną operację. W tym potoku działanie kopiowania kopiuje dane z jednej lokalizacji do innej lokalizacji w usłudze Azure Blob Storage. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-azdatafactoryv2pipeline) | Tworzy przebieg dla potoku. Innymi słowy uruchamia potoku. |
| [Get-AzDataFactoryV2AktypcjaRun](/powershell/module/az.datafactory/get-azdatafactoryv2activityrun) | Pobiera szczegółowe informacje na temat uruchamiania działania (przebiegu działania) w potoku. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów usługi Azure Data Factory PowerShell można znaleźć w [skryptach usługi Azure Data Factory PowerShell.](../samples-powershell.md)
