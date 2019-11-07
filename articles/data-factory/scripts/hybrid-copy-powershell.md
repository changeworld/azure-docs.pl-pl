---
title: 'Skrypt programu PowerShell: kopiowanie danych ze środowiska lokalnego na platformę Azure przy użyciu Data Factory '
description: Ten skrypt programu PowerShell kopiuje dane z lokalnej bazy danych SQL Server do innego Blob Storage platformy Azure.
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
ms.openlocfilehash: d7f8d67291c0b6b2a384331c014fdd5cd247ceae
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684348"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-on-premises-to-azure"></a>Korzystanie z programu PowerShell do tworzenia potoku usługi Data Factory w celu kopiowania danych ze środowiska lokalnego na platformę Azure

Ten przykładowy skrypt programu PowerShell tworzy potok w Azure Data Factory, który kopiuje dane z lokalnej bazy danych SQL Server do Blob Storage platformy Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- **SQL Server**. W tym przykładzie jest używana lokalna baza danych SQL Server jako **źródłowy** magazyn danych.
- **Konto usługi Azure Storage**. W tym przykładzie używasz magazynu obiektów blob platformy Azure jako **miejsca docelowego/ujścia** danych. Jeśli nie masz konta usługi Azure Storage, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu](../../storage/common/storage-quickstart-create-account.md).
- **Własne środowisko Integration Runtime**. Pobierz plik MSI z [Centrum pobierania](https://www.microsoft.com/download/details.aspx?id=39717) i uruchom go, aby zainstalować własne środowisko Integration Runtime na komputerze.  

### <a name="create-sample-database-in-sql-server"></a>Tworzenie przykładowej bazy danych w SQL Server
1. W lokalnej bazie danych SQL Server Utwórz tabelę o nazwie **EMP** , używając następującego skryptu SQL: 

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. Wstaw przykładowe dane do tabeli:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>Przykładowy skrypt

> [!IMPORTANT]
> Ten skrypt tworzy pliki JSON, które definiują Data Factory jednostki (połączone usługi, zestawy danych i potok) na dysku twardym w c:\ system32\drivers\etc.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-onprem-sql-server-to-azure-blob/copy-from-onprem-sql-server-to-azure-blob.ps1 "Copy from on-premises SQL Server -> Azure Blob Storage")]


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
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Tworzenie fabryki danych. |
| [New-AzDataFactoryV2LinkedServiceEncryptCredential](/powershell/module/az.datafactory/new-Azdatafactoryv2linkedserviceencryptedcredential) | Szyfruje poświadczenia w połączonej usłudze i generuje nową definicję połączonej usługi z zaszyfrowanym poświadczeniem. 
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Tworzy połączoną usługę w fabryce danych. Połączona usługa łączy magazyn danych lub dane obliczeniowe z fabryką danych. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Tworzy zestaw danych w fabryce danych. Zestaw danych reprezentuje dane wejściowe/wyjściowe dla działania w potoku. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Tworzy potok w fabryce danych. Potok zawiera jedną lub więcej działań, które wykonują określoną operację. W tym potoku działanie kopiowania kopiuje dane z jednej lokalizacji do innej lokalizacji w Blob Storage platformy Azure. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Tworzy przebieg dla potoku. Innymi słowy, uruchamia potok. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Pobiera szczegółowe informacje o przebiegu działania (przebieg działania) w potoku. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów programu Azure Data Factory PowerShell można znaleźć w [przykładach Azure Data Factory programu PowerShell](../samples-powershell.md).
