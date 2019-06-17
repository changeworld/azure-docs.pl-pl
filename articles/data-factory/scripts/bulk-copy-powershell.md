---
title: 'Skrypt programu PowerShell: Zbiorcze kopiowanie danych przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft'
description: Ten skrypt programu PowerShell pokazuje, jak skopiować dane z magazynu danych źródłowych do docelowego magazynu danych w trybie zbiorczym za pomocą usługi Azure Data Factory.
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
ms.openlocfilehash: d2db5bced78a00c8acabc150752fe65e9515dff1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60480642"
---
# <a name="powershell-script---copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>PowerShell script — zbiorcze kopiowanie wielu tabel przy użyciu usługi Azure Data Factory

Ten przykładowy skrypt programu PowerShell kopiuje dane z wielu tabel w bazie danych Azure SQL, do usługi Azure SQL data warehouse.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Zobacz [samouczek: kopiowanie masowe](../tutorial-bulk-copy.md#prerequisites) wymagań wstępnych dotyczących uruchamiania tego przykładu.

## <a name="sample-script"></a>Przykładowy skrypt

> [!IMPORTANT]
> Ten skrypt tworzy pliki w formacie JSON, które definiują jednostek usługi Data Factory (połączone usługi, zestaw danych i potok) na dysku twardym w folderze c:\.

[!code-powershell[main](../../../powershell_scripts/data-factory/bulk-copy-from-sql-databse-to-sql-data-warehouse/bulk-copy-from-sql-database-to-sql-data-warehouse.ps1 "Bulk copy from Azure SQL Database => Azure SQL Data Warehouse")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po uruchomieniu przykładowy skrypt służy następujące polecenie do usunięcia grupy zasobów i wszystkie skojarzone z nią zasoby:

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
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-azdatafactoryv2linkedservice) | Tworzy połączonej usługi w fabryce danych. Połączona usługa łączy magazyn danych lub obliczeniowych z fabryką danych. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/set-azdatafactoryv2dataset) | Tworzy zestaw danych w usłudze data factory. Zestaw danych reprezentuje dane wejściowe i wyjściowe dla działania w potoku. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-azdatafactoryv2pipeline) | Tworzy potok w fabryce danych. Potok zawiera jedno lub więcej działań, które wykonuje określonej operacji. W tym potoku działanie kopiowania kopiuje dane z jednej lokalizacji do innej lokalizacji w usłudze Azure Blob Storage. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-azdatafactoryv2pipeline) | Tworzy uruchomienia potoku. Innymi słowy uruchamia potok. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-azdatafactoryv2activityrun) | Pobiera szczegóły uruchomienia działania (działanie uruchamiania) w potoku. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Więcej przykładowych skryptów programu PowerShell do fabryki danych platformy Azure można znaleźć w [skrypty programu PowerShell do fabryki danych Azure](../samples-powershell.md).
