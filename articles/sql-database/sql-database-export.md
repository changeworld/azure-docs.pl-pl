---
title: Eksportowanie pojedynczej lub puli bazy danych do pliku BACPAC
description: Eksportowanie bazy danych SQL platformy Azure do pliku BACPAC przy użyciu portalu Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.topic: conceptual
ms.openlocfilehash: 0bc72f0ad58829a3ff6545b5c4741ddc20916c31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061627"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Eksportowanie bazy danych Azure SQL Database do pliku BACPAC

Jeśli trzeba wyeksportować bazę danych do archiwizacji lub przeniesienia na inną platformę, można wyeksportować schemat bazy danych i dane do pliku [BACPAC.](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) Plik BACPAC to plik ZIP z rozszerzeniem BACPAC zawierający metadane i dane z bazy danych PROGRAMU SQL Server. Plik BACPAC może być przechowywany w magazynie obiektów Blob platformy Azure lub w magazynie lokalnym w lokalizacji lokalnej, a później importowany z powrotem do usługi Azure SQL Database lub do lokalnej instalacji programu SQL Server.

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Zagadnienia dotyczące eksportowania bazy danych SQL platformy Azure

- Aby eksport był spójny transakcyjnie, należy upewnić się, że podczas eksportowania nie występuje żadne działanie zapisu lub eksportowanie z [transakcyjnie spójnej kopii](sql-database-copy.md) bazy danych SQL platformy Azure.
- W przypadku eksportowania do magazynu obiektów blob maksymalny rozmiar pliku BACPAC wynosi 200 GB. Aby zarchiwizować większy plik BACPAC, wyeksportuj go do magazynu lokalnego.
- Eksportowanie pliku BACPAC do magazynu w wersji Premium platformy Azure przy użyciu metod omówionych w tym artykule nie jest obsługiwane.
- Magazyn za zaporą nie jest obecnie obsługiwany.
- Jeśli operacja eksportowania z usługi Azure SQL Database przekracza 20 godzin, może zostać anulowana. Aby zwiększyć wydajność podczas eksportowania, można:

  - Tymczasowo zwiększ rozmiar obliczeń.
  - Zaprzestanie wszystkich działań odczytu i zapisu podczas eksportowania.
  - Użyj [indeksu klastrowanego](https://msdn.microsoft.com/library/ms190457.aspx) z wartościami niezerowymi we wszystkich dużych tabelach. Bez indeksów klastrowanych eksport może zakończyć się niepowodzeniem, jeśli trwa dłużej niż 6-12 godzin. Dzieje się tak, ponieważ usługa eksportu musi wykonać skanowanie tabeli, aby spróbować wyeksportować całą tabelę. Dobrym sposobem, aby ustalić, czy tabele są zoptymalizowane pod kątem eksportu jest uruchomienie **DBCC SHOW_STATISTICS** i upewnij się, że *RANGE_HI_KEY* nie jest null i jego wartość ma dobry rozkład. Aby uzyskać szczegółowe informacje, zobacz [SHOW_STATISTICS DBCC](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> BACPACs nie są przeznaczone do wykonywania kopii zapasowych i przywracania operacji. Usługa Azure SQL Database automatycznie tworzy kopie zapasowe dla każdej bazy danych użytkowników. Aby uzyskać szczegółowe informacje, zobacz [omówienie ciągłości biznesowej](sql-database-business-continuity.md) i [kopie zapasowe bazy danych SQL](sql-database-automated-backups.md).

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Eksportowanie do pliku BACPAC przy użyciu portalu Azure

Eksportowanie BACPAC bazy danych z [wystąpienia zarządzanego](sql-database-managed-instance.md) przy użyciu programu Azure PowerShell nie jest obecnie obsługiwane. Zamiast tego należy użyć programu SQL Server Management Studio lub SQLPackage.

> [!NOTE]
> Maszyny przetwarzające żądania importu/eksportu przesłane za pośrednictwem portalu Azure lub programu PowerShell muszą przechowywać plik BACPAC, a także pliki tymczasowe generowane przez platformę aplikacji warstwy danych (DacFX). Wymagane miejsce na dysku różni się znacznie w poszczególnych bazach danych o tym samym rozmiarze i może wymagać miejsca na dysku do 3 razy większej niż rozmiar bazy danych. Komputery z żądaniem importu/eksportu mają tylko 450 GB miejsca na dysku lokalnym. W rezultacie niektóre żądania mogą zakończyć się niepowodzeniem z błędem `There is not enough space on the disk`. W takim przypadku obejście jest uruchomienie sqlpackage.exe na komputerze z wystarczającą ilością miejsca na dysku lokalnym. Zachęcamy za pomocą [SqlPackage](#export-to-a-bacpac-file-using-the-sqlpackage-utility) do importowania/eksportowania baz danych większych niż 150 GB, aby uniknąć tego problemu.

1. Aby wyeksportować bazę danych przy użyciu [witryny Azure Portal,](https://portal.azure.com)otwórz stronę bazy danych i kliknij pozycję **Eksportuj** na pasku narzędzi.

   ![Eksport bazy danych](./media/sql-database-export/database-export1.png)

2. Określ nazwę pliku BACPAC, wybierz istniejące konto magazynu platformy Azure i kontener dla eksportu, a następnie podaj odpowiednie poświadczenia dostępu do źródłowej bazy danych. W tym miejscu potrzebny jest **logowanie administratora programu** SQL Server, nawet jeśli jesteś administratorem platformy Azure, ponieważ bycie administratorem platformy Azure nie oznacza posiadania uprawnień administratora programu SQL Server.

    ![Eksport bazy danych](./media/sql-database-export/database-export2.png)

3. Kliknij przycisk **OK**.

4. Aby monitorować postęp operacji eksportowania, otwórz stronę serwera bazy danych SQL zawierającą eksportowanej bazy danych. W obszarze **Ustawienia,** a następnie kliknij pozycję **Importuj/Eksportuj historię**.

   ![historia eksportu](./media/sql-database-export/export-history.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Eksportowanie do pliku BACPAC przy użyciu narzędzia SQLPackage

Aby wyeksportować bazę danych SQL za pomocą narzędzia wiersza polecenia [SqlPackage,](https://docs.microsoft.com/sql/tools/sqlpackage) zobacz [Eksportowanie parametrów i właściwości](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). Narzędzie SQLPackage jest dostarczane z najnowszymi wersjami [programu SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) i SQL Server Data Tools for Visual [Studio](https://msdn.microsoft.com/library/mt204009.aspx)lub można pobrać najnowszą wersję [programu SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) bezpośrednio z centrum pobierania firmy Microsoft.

Zaleca się użycie narzędzia SQLPackage do skalowania i wydajności w większości środowisk produkcyjnych. Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).

W tym przykładzie pokazano, jak wyeksportować bazę danych przy użyciu programu SqlPackage.exe z uniwersalnym uwierzytelnianiem usługi Active Directory:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Eksportowanie do pliku BACPAC przy użyciu programu SQL Server Management Studio (SSMS)

Najnowsze wersje programu SQL Server Management Studio udostępnia kreatora do eksportowania bazy danych SQL azure do pliku BACPAC. Zobacz [eksportowanie aplikacji warstwy danych](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Eksportowanie do pliku BACPAC przy użyciu programu PowerShell

> [!NOTE]
> [Wystąpienie zarządzane](sql-database-managed-instance.md) nie obsługuje obecnie eksportowania bazy danych do pliku BACPAC przy użyciu programu Azure PowerShell. Aby wyeksportować wystąpienie zarządzane do pliku BACPAC, użyj programu SQL Server Management Studio lub SQLPackage.

Polecenie cmdlet [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) służy do przesyłania żądania eksportu bazy danych do usługi Azure SQL Database. W zależności od rozmiaru bazy danych operacja eksportowania może zająć trochę czasu.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Aby sprawdzić stan żądania eksportu, należy użyć polecenia cmdlet [Get-AzSqlDatabaseImportExportStatus.](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) Uruchamianie tego natychmiast po żądaniu zwykle zwraca **Stan: InProgress**. Po wyświetleniu **stanu: Eksport zakończył się pomyślnie.**

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

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o długoterminowym przechowywania kopii zapasowych pojedynczych baz danych i branych baz danych jako alternatywy dla eksportowanej bazy danych do celów archiwalnych, zobacz [Długoterminowe przechowywanie kopii zapasowych](sql-database-long-term-retention.md). Za pomocą zadań programu SQL Agent można zaplanować [tworzenie kopii zapasowych bazy danych tylko](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) do kopiowania jako alternatywę dla długoterminowego przechowywania kopii zapasowych.
- Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).
- Aby dowiedzieć się więcej o imporcie bacpac do bazy danych programu SQL Server, zobacz [Importowanie BACPAC do bazy danych programu SQL Server](https://msdn.microsoft.com/library/hh710052.aspx).
- Aby dowiedzieć się więcej o eksportowaniu BACPAC z bazy danych programu SQL Server, zobacz [Eksportowanie aplikacji warstwy danych](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- Aby dowiedzieć się więcej o używaniu usługi migracji danych do migracji bazy danych, zobacz [Migrowanie programu SQL Server do bazy danych SQL Database w trybie offline przy użyciu usługi DMS](../dms/tutorial-sql-server-to-azure-sql.md).
- Jeśli eksportujesz z programu SQL Server jako preludium do migracji do bazy danych SQL Azure, zobacz [Migrowanie bazy danych programu SQL Server do bazy danych SQL Database.](sql-database-single-database-migrate.md)
- Aby dowiedzieć się, jak bezpiecznie zarządzać kluczami magazynu i sygnaturami dostępu współdzielonego oraz udostępniać je, zobacz [Przewodnik po zabezpieczeniach usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
