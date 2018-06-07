---
title: Eksportuj do pliku pliku BACPAC bazy danych Azure SQL | Dokumentacja firmy Microsoft
description: Eksportowanie bazy danych Azure SQL w pliku pliku BACPAC za pomocą portalu Azure
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: load & move data
ms.date: 04/01/2018
ms.author: carlrab
ms.topic: conceptual
ms.openlocfilehash: c2f29d8c660e3d39f91bcdd97209d2e88f5ae864
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647956"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Eksportuj do pliku pliku BACPAC bazy danych Azure SQL

Gdy trzeba wyeksportować bazę danych do archiwizacji lub przenoszenia do innej platformie, możesz wyeksportować schemat bazy danych i danych na [pliku BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) pliku. Plik pliku BACPAC to plik ZIP z rozszerzeniem pliku BACPAC zawierający metadane i dane z bazy danych programu SQL Server. Plik pliku BACPAC można przechowywanych w magazynie obiektów blob platformy Azure lub w magazynie lokalnym w lokalizacji lokalnej i później zaimportować do bazy danych SQL Azure lub do instalacji lokalnej programu SQL Server. 

> [!IMPORTANT] 
> Azure bazy danych zautomatyzowanego eksportu danych SQL została wycofana w 1 marca 2017 r. Można użyć [długoterminowego przechowywania kopii zapasowych](sql-database-long-term-retention.md
) lub [usługi Automatyzacja Azure](https://github.com/Microsoft/azure-docs/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md) okresowo archiwizacji SQL bazy danych zgodnie z harmonogramem wybranym przy użyciu programu PowerShell. Dla przykładu, Pobierz [przykładowy skrypt programu PowerShell](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export) z usługi Github.
>

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Zagadnienia dotyczące podczas eksportowania bazy danych Azure SQL

* Eksportu być spójna transakcyjnie, pamiętaj, że nie zapis działanie ma miejsce podczas eksportowania, lub są eksportowane [spójna transakcyjnie kopiowania](sql-database-copy.md) bazy danych Azure SQL.
* Jeśli są eksportowane do magazynu obiektów blob, maksymalny rozmiar pliku pliku BACPAC jest 200 GB. Archiwizowanie pliku BACPAC rozmiar pliku, wyeksportować do magazynu lokalnego.
* Eksportowanie pliku BACPAC pliku do magazynu Azure premium za pomocą metod omówionych w tym artykule nie jest obsługiwane.
* Jeśli operacja eksportu z bazy danych SQL Azure przekracza 20 godzin, mogą zostać anulowane. Aby zwiększyć wydajność podczas eksportu, można:
  * Tymczasowo zwiększyć poziom obsługi.
  * Zaprzestanie wszystkie odczytu i zapisu działania podczas eksportowania.
  * Użyj [indeksu klastrowanego](https://msdn.microsoft.com/library/ms190457.aspx) o wartości innej niż null na wszystkich dużych tabel. Bez indeksy klastrowane eksportu może nie powieść, jeśli trwa dłużej niż 6 – 12 godzin. Jest to spowodowane usługi eksportu musi wykonać skanowanie tabeli, aby spróbować eksportować całą tabelę. Dobry sposób, aby określić, czy tabele są zoptymalizowane do eksportu jest uruchomienie **DBCC SHOW_STATISTICS** i upewnij się, że *RANGE_HI_KEY* nie ma wartości null, a jego wartość ma dobrej dystrybucji. Aby uzyskać więcej informacji, zobacz [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> BACPACs nie mają być używane do utworzenia kopii zapasowej i przywracanie operacji. Baza danych SQL Azure automatycznie tworzy kopie zapasowe dla każdej bazy danych użytkownika. Aby uzyskać więcej informacji, zobacz [omówienie ciągłości działalności biznesowej](sql-database-business-continuity.md) i [kopie zapasowe bazy danych SQL](sql-database-automated-backups.md).  
> 

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Wyeksportuj do pliku BACPAC pliku przy użyciu portalu Azure

Aby wyeksportować bazy danych przy użyciu [portalu Azure](https://portal.azure.com), otwórz stronę bazy danych i kliknij przycisk **wyeksportować** na pasku narzędzi. Określ nazwę pliku BACPAC, podaj konto magazynu Azure i kontener eksportu i podaj poświadczenia do połączenia z bazą danych źródła.  

![Eksportowanie bazy danych](./media/sql-database-export/database-export.png)

Aby monitorować postęp operacji eksportowania, otwórz stronę dla serwera logicznego zawierającego bazę danych eksportowane. Przewiń w dół do **operacji** , a następnie kliknij przycisk **importu/eksportu** historii.

![Eksportuj historii](./media/sql-database-export/export-history.png)
![stan historii eksportu](./media/sql-database-export/export-history2.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Eksportuj do pliku BACPAC pliku przy użyciu narzędzia SQLPackage

Aby wyeksportować bazy danych SQL za pomocą [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) narzędzie wiersza polecenia, zobacz [wyeksportować parametrów i właściwości](https://msdn.microsoft.com/library/hh550080.aspx#Export Parameters and Properties). Narzędzie SQLPackage jest dostarczany z najnowszej wersji [programu SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) i [programu SQL Server Data Tools dla programu Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), można również pobrać najnowszą wersję [SqlPackage ](https://www.microsoft.com/download/details.aspx?id=53876) bezpośrednio z Centrum pobierania Microsoft.

Firma Microsoft zaleca użycie narzędzia SQLPackage na skalowalność i wydajność w większości środowisk produkcyjnych. Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).

W tym przykładzie pokazano, jak można wyeksportować bazy danych przy użyciu SqlPackage.exe z uniwersalnych uwierzytelnianie usługi Active Directory:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Wyeksportuj do pliku BACPAC pliku przy użyciu programu SQL Server Management Studio (SSMS)

Najnowsza wersja programu SQL Server Management Studio udostępniają kreatora można wyeksportować do pliku pliku BACPAC bazy danych SQL Azure. Zobacz [eksportowania aplikacji warstwy danych](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Wyeksportuj do pliku BACPAC pliku przy użyciu programu PowerShell

Użyj [AzureRmSqlDatabaseExport nowy](/powershell/module/azurerm.sql/new-azurermsqldatabaseexport) polecenia cmdlet, aby przesłać żądanie eksportu bazy danych do usługi baza danych SQL Azure. W zależności od rozmiaru bazy danych operacji eksportowania może potrwać trochę czasu.

 ```powershell
 $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
   -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
   -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
 ```

Aby sprawdzić stan żądania eksportu, użyj [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) polecenia cmdlet. Uruchomiona natychmiast po odebraniu żądania zwykle zwraca **stan: w toku**. Po wyświetleniu **stanu: zakończyło się pomyślnie** zakończeniu eksportu.

```powershell
$exportStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")   
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>Kolejne kroki

* Informacje na temat długoterminowego przechowywania kopii zapasowych kopii zapasowej bazy danych Azure SQL zamiast eksportowanie bazy danych do archiwizowania, zobacz [długoterminowego przechowywania kopii zapasowych](sql-database-long-term-retention.md).
- Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).
* Aby dowiedzieć się więcej na temat importowania pliku BACPAC bazy danych programu SQL Server, zobacz [zaimportować do bazy danych programu SQL Server BACPCAC](https://msdn.microsoft.com/library/hh710052.aspx).
* Aby dowiedzieć się więcej na temat Eksportowanie pliku BACPAC z bazy danych programu SQL Server, zobacz [eksportowania aplikacji warstwy danych](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) i [migracji pierwszą bazę danych](sql-database-migrate-your-sql-server-database.md).
* Jeśli zostaną wyeksportowane z programu SQL Server jako prelude do migracji bazy danych SQL Azure, zobacz [migrację bazy danych programu SQL Server do bazy danych SQL Azure](sql-database-cloud-migrate.md).
* Aby dowiedzieć się, jak zarządzać i udostępniania magazynu kluczy i dostępu współdzielonego signitures bezpiecznego, zobacz [przewodnik zabezpieczeń magazynu Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
