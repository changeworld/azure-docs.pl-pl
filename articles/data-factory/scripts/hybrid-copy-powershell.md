---
title: Kopiowanie danych z lokalnego programu Azure przy użyciu programu PowerShell
description: Ten skrypt programu PowerShell kopiuje dane z lokalnej bazy danych programu SQL Server do innej usługi Azure Blob Storage.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.author: jingwang
author: linda33wj
manager: shwang
ms.custom: seo-lt-2019
ms.date: 10/31/2017
ms.openlocfilehash: 10555defc4888af66bb88d19190b6543aa8ae0c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75974702"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-on-premises-to-azure"></a>Tworzenie potoku fabryki danych za pomocą programu PowerShell w celu kopiowania danych z lokalnego programu Azure

Ten przykładowy skrypt programu PowerShell tworzy potok w usłudze Azure Data Factory, który kopiuje dane z lokalnej bazy danych programu SQL Server do usługi Azure Blob Storage.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- **SQL Server**. Lokalna baza danych programu SQL Server jest używana jako **źródłowego** magazynu danych w tym przykładzie.
- **Konto usługi Azure Storage**. Użyj magazynu obiektów blob platformy Azure jako magazynu danych **miejsce docelowe/ujście** w tym przykładzie. Jeśli nie masz konta magazynu platformy Azure, zobacz [artykuł Tworzenie konta magazynu,](../../storage/common/storage-account-create.md) aby uzyskać kroki, aby je utworzyć.
- **Środowisko uruchomieniowe integracji hostowanego samodzielnie**. Pobierz plik MSI z [centrum pobierania](https://www.microsoft.com/download/details.aspx?id=39717) i uruchom go, aby zainstalować własny hostowany środowisko wykonawcze integracji na komputerze.  

### <a name="create-sample-database-in-sql-server"></a>Tworzenie przykładowej bazy danych w programie SQL Server
1. W lokalnej bazie danych programu SQL Server utwórz tabelę o nazwie **emp** przy użyciu następującego skryptu SQL:

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

2. Wstaw niektóre przykładowe dane do tabeli:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>Przykładowy skrypt

> [!IMPORTANT]
> Ten skrypt tworzy pliki JSON definiujące jednostki Data Factory (usługa połączona, zestaw danych i potok) na dysku twardym w c:\ Folder.

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
| [Zestaw-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Tworzenie fabryki danych. |
| [Nowy-AzDataFactoryV2LinkedServiceEncryptCredential](/powershell/module/az.datafactory/new-Azdatafactoryv2linkedserviceencryptedcredential) | Szyfruje poświadczenia w połączonej usłudze i generuje nową definicję usługi połączonej z zaszyfrowanymi poświadczeniami.
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Tworzy usługę połączony w fabryce danych. Połączona usługa łączy magazyn danych lub obliczeń z fabryką danych. |
| [Zestaw danych Zestawu AzDataFactoryV2](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Tworzy zestaw danych w fabryce danych. Zestaw danych reprezentuje dane wejściowe/wyjściowe dla działania w potoku. |
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Tworzy potok w fabryce danych. Potok zawiera co najmniej jedną operację, która wykonuje określoną operację. W tym potoku działanie kopiowania kopiuje dane z jednej lokalizacji do innej lokalizacji w usłudze Azure Blob Storage. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Tworzy przebieg dla potoku. Innymi słowy uruchamia potoku. |
| [Get-AzDataFactoryV2AktypcjaRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Pobiera szczegółowe informacje na temat uruchamiania działania (przebiegu działania) w potoku.
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów usługi Azure Data Factory PowerShell można znaleźć w [przykładach usługi Azure Data Factory PowerShell.](../samples-powershell.md)
