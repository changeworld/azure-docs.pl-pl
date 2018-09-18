---
title: Eksportuj bazę danych Azure SQL database do pliku BACPAC | Dokumentacja firmy Microsoft
description: Eksportuj bazę danych Azure SQL database do pliku BACPAC przy użyciu witryny Azure portal
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: load & move data
ms.date: 09/14/2018
ms.author: carlrab
ms.topic: conceptual
ms.openlocfilehash: c117b3d56e6b032bff5983a707621d649e608d98
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45732163"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Eksportuj bazę danych Azure SQL database do pliku BACPAC

Gdy trzeba wyeksportować bazę danych do archiwizacji lub przenoszenia do innej platformy, możesz wyeksportować schemat bazy danych i dane mają być [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) pliku. Plik BACPAC jest plikiem ZIP z rozszerzeniem pliku BACPAC zawierające metadane i dane z bazy danych programu SQL Server. Plik BACPAC można przechowywanych w usłudze Azure blob storage lub w magazynie lokalnym w lokalizacji lokalnej i później zaimportować do usługi Azure SQL Database lub do instalacji lokalnych programu SQL Server. 

> [!IMPORTANT] 
> Usługa Azure SQL Database zautomatyzowanego eksportu zostało wycofane z dniem 1 marca 2017 r. Możesz użyć [długoterminowego przechowywania kopii zapasowych](sql-database-long-term-retention.md
) lub [usługi Azure Automation](https://github.com/Microsoft/azure-docs/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md) okresowo zarchiwizować SQL bazy danych przy użyciu programu PowerShell, zgodnie z harmonogramem wybranym. Przykład można pobrać [przykładowy skrypt programu PowerShell](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export) z usługi Github.
>

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Uwagi dotyczące eksportowania bazy danych Azure SQL

* Eksportu był spójny transakcyjnie, należy upewnić się, że nie zapis działanie odbywa się podczas eksportowania lub które są eksportowane [spójnej transakcyjnie kopii](sql-database-copy.md) bazy danych Azure SQL.
* Jeśli są eksportowane do magazynu obiektów blob, maksymalny rozmiar pliku BACPAC wynosi 200 GB. Do archiwizacji większy plik BACPAC, należy wyeksportować w magazynie lokalnym.
* Eksportowanie pliku BACPAC do usługi Azure premium storage przy użyciu metod omówionych w tym artykule nie jest obsługiwane.
* Jeśli operacja eksportowania z usługi Azure SQL Database przekracza 20 godzin, mogą zostać anulowane. Aby zwiększyć wydajność podczas eksportu, możesz wykonywać następujące czynności:
  * Tymczasowo zwiększyć rozmiar obliczeń.
  * Zaprzestanie wszystkich odczytu i zapisu działania podczas eksportowania.
  * Użyj [indeksu klastrowanego](https://msdn.microsoft.com/library/ms190457.aspx) przy użyciu wartości innych niż null na wszystkich dużych tabel. Bez klastrowanych indeksów eksportu może się niepowodzeniem, jeśli zajmuje więcej czasu niż 6-i 12 godzin. Jest to spowodowane usługa export musi wykonać skanowanie tabeli próbował wyeksportować całą tabelę. Dobrym sposobem, aby określić, jeśli tabele są zoptymalizowane pod kątem eksportu jest uruchomienie **DBCC SHOW_STATISTICS** i upewnij się, że *RANGE_HI_KEY* nie ma wartości null, a jego wartość ma dobrą dystrybucji. Aby uzyskać więcej informacji, zobacz [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> Pliki Bacpac nie są przeznaczone do służyć do tworzenia kopii zapasowych i przywracanie operacji. Usługa Azure SQL Database automatycznie tworzy kopie zapasowe dla każdej bazy danych użytkownika. Aby uzyskać więcej informacji, zobacz [omówienie ciągłości działania](sql-database-business-continuity.md) i [kopie zapasowe bazy danych SQL](sql-database-automated-backups.md).  
> 

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Eksportuj do pliku BACPAC przy użyciu witryny Azure portal

Aby wyeksportować bazę danych za pomocą [witryny Azure portal](https://portal.azure.com), otwórz stronę bazy danych i kliknij przycisk **wyeksportować** na pasku narzędzi. Określ nazwę pliku BACPAC, podaj konto magazynu platformy Azure i kontener eksportu i podaj poświadczenia do połączenia z bazą danych źródłowych.  

![Eksportowanie bazy danych](./media/sql-database-export/database-export.png)

Aby monitorować postęp operacji eksportowania, otwórz stronę serwera logicznego zawierającym bazę danych, eksportowania. Przewiń w dół do **operacji** a następnie kliknij przycisk **Import/Export** historii.

![Eksportowanie historii](./media/sql-database-export/export-history.png)
![eksportowanie historii stanu](./media/sql-database-export/export-history2.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Eksportuj do pliku BACPAC przy użyciu narzędzia SQLPackage

Aby wyeksportować bazę danych SQL za pomocą [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) narzędzie wiersza polecenia, zobacz [wyeksportować parametry i właściwości](https://msdn.microsoft.com/library/hh550080.aspx#Export Parameters and Properties). Narzędzia SQLPackage jest dostarczany z najnowszymi wersjami programów [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) i [programu SQL Server Data Tools dla programu Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), można również pobrać najnowszą wersję [SqlPackage ](https://www.microsoft.com/download/details.aspx?id=53876) bezpośrednio z Centrum pobierania Microsoft.

Firma Microsoft zaleca korzystanie z narzędzia SQLPackage w celu zapewnienia skalowalności i wydajności w większości środowisk produkcyjnych. Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).

Ten przykład pokazuje, jak wyeksportować bazę danych przy użyciu uwierzytelniania usługi Active Directory Universal SqlPackage.exe:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Eksportuj do pliku BACPAC przy użyciu programu SQL Server Management Studio (SSMS)

Najnowsza wersja programu SQL Server Management Studio oferują również kreatora można wyeksportować z usługi Azure SQL Database do pliku BACPAC. Zobacz [eksportowania aplikacji warstwy danych](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Eksportuj do pliku BACPAC przy użyciu programu PowerShell

Użyj [New AzureRmSqlDatabaseExport](/powershell/module/azurerm.sql/new-azurermsqldatabaseexport) polecenia cmdlet, aby przesłać żądanie eksportu bazy danych do usługi Azure SQL Database. W zależności od rozmiaru bazy danych operacji eksportowania może potrwać trochę czasu.

 ```powershell
 $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
   -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
   -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
 ```

Aby sprawdzić stan żądania eksportu, należy użyć [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) polecenia cmdlet. Uruchomiony natychmiast po wykonaniu żądania zwykle zwraca **stan: w toku**. Po wyświetleniu **stan: powodzenie** Eksportowanie zostało ukończone.

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

* Informacje na temat długoterminowego przechowywania kopii zapasowych z kopii zapasowej usługi Azure SQL database jako alternatywa wyeksportować bazę danych dla celów archiwizacji, zobacz [długoterminowego przechowywania kopii zapasowych](sql-database-long-term-retention.md).
- Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).
* Aby dowiedzieć się więcej na temat Importowanie pliku BACPAC do bazy danych programu SQL Server, zobacz [zaimportować BACPCAC do bazy danych programu SQL Server](https://msdn.microsoft.com/library/hh710052.aspx).
* Aby dowiedzieć się więcej na temat Eksportowanie pliku BACPAC z bazy danych programu SQL Server, zobacz [eksportowania aplikacji warstwy danych](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) i [migrować pierwszą bazę danych](sql-database-migrate-your-sql-server-database.md).
* Jeśli eksportujesz z programu SQL Server jako prelude do migracji do usługi Azure SQL Database, zobacz [migracji bazy danych programu SQL Server do usługi Azure SQL Database](sql-database-cloud-migrate.md).
* Aby dowiedzieć się, jak zarządzać i udostępniania kluczy magazynu i dostępu współdzielonego signitures bezpieczne, zobacz [Przewodnik po zabezpieczeniach magazynu Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
