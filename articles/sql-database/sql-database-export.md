---
title: Eksportowanie jednej lub w puli usługi Azure SQL database do pliku BACPAC | Dokumentacja firmy Microsoft
description: Eksportuj bazę danych Azure SQL database do pliku BACPAC przy użyciu witryny Azure portal
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 03/11/2019
ms.openlocfilehash: c87979760730cbe8f57d8f65463c94d08888aa2b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65762751"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Eksportuj bazę danych Azure SQL database do pliku BACPAC

Gdy trzeba wyeksportować bazę danych do archiwizacji lub przenoszenia do innej platformy, możesz wyeksportować schemat bazy danych i dane mają być [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) pliku. Plik BACPAC jest plikiem ZIP z rozszerzeniem pliku BACPAC zawierające metadane i dane z bazy danych programu SQL Server. Plik BACPAC mogą być przechowywane w usłudze Azure Blob storage lub w magazynie lokalnym w lokalizacji lokalnej i później zaimportowane wstecz do usługi Azure SQL Database lub SQL Server w środowisku lokalnym instalacji.

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Uwagi dotyczące eksportowania bazy danych Azure SQL

- Eksportu był spójny transakcyjnie, należy upewnić się, że nie zapis działanie odbywa się podczas eksportowania lub które są eksportowane [spójnej transakcyjnie kopii](sql-database-copy.md) bazy danych Azure SQL.
- Jeśli są eksportowane do magazynu obiektów blob, maksymalny rozmiar pliku BACPAC wynosi 200 GB. Do archiwizacji większy plik BACPAC, należy wyeksportować w magazynie lokalnym.
- Eksportowanie pliku BACPAC do usługi Azure premium storage przy użyciu metod omówionych w tym artykule nie jest obsługiwane.
- Magazyn za zaporą nie jest obecnie obsługiwane.
- Jeśli operacja eksportowania z usługi Azure SQL Database przekracza 20 godzin, mogą zostać anulowane. Aby zwiększyć wydajność podczas eksportu, możesz wykonywać następujące czynności:

  - Tymczasowo zwiększyć rozmiar obliczeń.
  - Zaprzestanie wszystkich odczytu i zapisu działania podczas eksportowania.
  - Użyj [indeksu klastrowanego](https://msdn.microsoft.com/library/ms190457.aspx) przy użyciu wartości innych niż null na wszystkich dużych tabel. Bez klastrowanych indeksów eksportu może się niepowodzeniem, jeśli zajmuje więcej czasu niż 6-i 12 godzin. Jest to spowodowane usługa export musi wykonać skanowanie tabeli próbował wyeksportować całą tabelę. Dobrym sposobem, aby określić, jeśli tabele są zoptymalizowane pod kątem eksportu jest uruchomienie **DBCC SHOW_STATISTICS** i upewnij się, że *RANGE_HI_KEY* nie ma wartości null, a jego wartość ma dobrą dystrybucji. Aby uzyskać więcej informacji, zobacz [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> Pliki Bacpac nie są przeznaczone do służyć do tworzenia kopii zapasowych i przywracanie operacji. Usługa Azure SQL Database automatycznie tworzy kopie zapasowe dla każdej bazy danych użytkownika. Aby uzyskać więcej informacji, zobacz [omówienie ciągłości działania](sql-database-business-continuity.md) i [kopie zapasowe bazy danych SQL](sql-database-automated-backups.md).

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Eksportuj do pliku BACPAC przy użyciu witryny Azure portal

> [!NOTE]
> [Wystąpienie zarządzane](sql-database-managed-instance.md) aktualnie nie obsługuje eksportowanie bazy danych do pliku BACPAC przy użyciu witryny Azure portal. Aby wyeksportować do pliku BACPAC wystąpienia zarządzanego, należy użyć programu SQL Server Management Studio lub SQLPackage.

1. Aby wyeksportować bazę danych za pomocą [witryny Azure portal](https://portal.azure.com), otwórz stronę bazy danych i kliknij przycisk **wyeksportować** na pasku narzędzi.

   ![Eksportowanie bazy danych](./media/sql-database-export/database-export1.png)

2. Określ nazwę pliku BACPAC, wybierz istniejące konto magazynu platformy Azure i kontenerów do wyeksportowania, a następnie podaj odpowiednie poświadczenia dostępu do źródłowej bazy danych.

    ![Eksportowanie bazy danych](./media/sql-database-export/database-export2.png)

3. Kliknij przycisk **OK**.

4. Aby monitorować postęp operacji eksportowania, otwórz stronę dla serwera bazy danych SQL zawierającym bazę danych, eksportowania. W obszarze do **ustawienia** a następnie kliknij przycisk **Historia importowania/eksportowania**.

   ![Eksportowanie historii](./media/sql-database-export/export-history.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Eksportuj do pliku BACPAC przy użyciu narzędzia SQLPackage

Aby wyeksportować bazę danych SQL za pomocą [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) narzędzie wiersza polecenia, zobacz [wyeksportować parametry i właściwości](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). Narzędzia SQLPackage jest dostarczany z najnowszymi wersjami programów [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) i [programu SQL Server Data Tools dla programu Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), można również pobrać najnowszą wersję [SqlPackage ](https://www.microsoft.com/download/details.aspx?id=53876) bezpośrednio z Centrum pobierania Microsoft.

Firma Microsoft zaleca korzystanie z narzędzia SQLPackage w celu zapewnienia skalowalności i wydajności w większości środowisk produkcyjnych. Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).

Ten przykład pokazuje, jak wyeksportować bazę danych przy użyciu uwierzytelniania usługi Active Directory Universal SqlPackage.exe:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Eksportuj do pliku BACPAC przy użyciu programu SQL Server Management Studio (SSMS)

Najnowsza wersja programu SQL Server Management Studio udostępnia kreatora można wyeksportować bazę danych Azure SQL database do pliku BACPAC. Zobacz [eksportowania aplikacji warstwy danych](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Eksportuj do pliku BACPAC przy użyciu programu PowerShell

> [!NOTE]
> [Wystąpienie zarządzane](sql-database-managed-instance.md) aktualnie nie obsługuje eksportowanie bazy danych do pliku BACPAC przy użyciu programu Azure PowerShell. Aby wyeksportować do pliku BACPAC wystąpienia zarządzanego, należy użyć programu SQL Server Management Studio lub SQLPackage.

Użyj [New AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) polecenia cmdlet, aby przesłać żądanie eksportu bazy danych do usługi Azure SQL Database. W zależności od rozmiaru bazy danych operacji eksportowania może potrwać trochę czasu.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Aby sprawdzić stan żądania eksportu, należy użyć [Get AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) polecenia cmdlet. Uruchomiony natychmiast po wykonaniu żądania zwykle zwraca **stanu: InProgress**. Po wyświetleniu **stanu: Pomyślnie** Eksportowanie zostało ukończone.

```powershell
$exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o długoterminowym przechowywaniem kopii zapasowych pojedynczej bazy danych i baz danych w puli jako alternatywa wyeksportować bazę danych dla celów archiwizacji, zobacz [długoterminowego przechowywania kopii zapasowych](sql-database-long-term-retention.md). Można użyć zadania agenta programu SQL, aby zaplanować [kopie zapasowe bazy danych tylko do kopiowania](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) jako alternatywę dla długoterminowego przechowywania kopii zapasowych.
- Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).
- Aby dowiedzieć się więcej na temat Importowanie pliku BACPAC do bazy danych programu SQL Server, zobacz [Importowanie pliku BACPAC do bazy danych programu SQL Server](https://msdn.microsoft.com/library/hh710052.aspx).
- Aby dowiedzieć się więcej na temat Eksportowanie pliku BACPAC z bazy danych programu SQL Server, zobacz [eksportowania aplikacji warstwy danych](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- Aby dowiedzieć się więcej o korzystaniu z usługi migracji danych do migracji bazy danych, zobacz [migracji programu SQL Server do usługi Azure SQL Database w trybie offline przy użyciu usługi DMS](../dms/tutorial-sql-server-to-azure-sql.md).
- Jeśli eksportujesz z programu SQL Server jako prelude do migracji do usługi Azure SQL Database, zobacz [migracji bazy danych programu SQL Server do usługi Azure SQL Database](sql-database-single-database-migrate.md).
- Aby dowiedzieć się, jak zarządzać i udostępniania kluczy magazynu i dostępu współdzielonego podpisów bezpieczne, zobacz [Przewodnik po zabezpieczeniach magazynu Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
