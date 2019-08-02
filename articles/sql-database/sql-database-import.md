---
title: Importowanie pliku BACPAC do utworzenia usługi Azure SQL database | Dokumentacja firmy Microsoft
description: Utwórz bazę danych SQL newAzure przez zaimportowanie pliku BACPAC.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/20/2019
ms.openlocfilehash: ca098eba8e0cbad0d0240bd7819a401c502a869d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568026"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Szybki start: Importowanie pliku BACPAC do bazy danych w Azure SQL Database

Bazę danych SQL Server można zaimportować do bazy danych w Azure SQL Database przy użyciu pliku [BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) . Dane można zaimportować z `BACPAC` pliku przechowywanego w usłudze Azure Blob Storage (tylko w przypadku magazynu w warstwie Standardowa) lub z magazynu lokalnego w lokalizacji lokalnej. Aby zmaksymalizować szybkość importowania, zapewniając większe i szybsze zasoby, Skaluj bazę danych do wyższej warstwy usług i rozmiaru obliczeń podczas procesu importowania. Po pomyślnym zakończeniu importowania można skalować w dół.

> [!NOTE]
> Poziom zgodności zaimportowanej bazy danych opiera się na poziomie zgodności bazy danych źródłowych.
> [!IMPORTANT]
> Po zaimportowaniu bazy danych, istnieje możliwość obsługi bazy danych w jego bieżący poziom zgodności (poziom 100 AdventureWorks2008R2 bazy danych) lub na wyższym poziomie. Aby uzyskać więcej informacji o implikacjach i opcjach związanych z używaniem bazy danych na określonym poziomie zgodności, zobacz [ALTER DATABASE Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) (Instrukcja ALTER DATABASE — poziom zgodności). Zapoznaj się też z tematem [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql), aby uzyskać informacje o dodatkowych ustawieniach na poziomie bazy danych związanych z poziomem zgodności.

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>Importuj z pliku BACPAC w witrynie Azure portal

[Azure Portal](https://portal.azure.com) obsługuje *tylko* Tworzenie pojedynczej bazy danych w programie Azure SQL Database i *tylko* z pliku BACPAC przechowywanego w usłudze Azure Blob Storage.

Migrowanie bazy danych do [wystąpienia zarządzanego](sql-database-managed-instance.md) z pliku BACPAC przy użyciu Azure PowerShell nie jest obecnie obsługiwane. Zamiast tego użyj SQL Server Management Studio lub sqlpackage.

> [!NOTE]
> Komputery przetwarzające żądania importu/eksportu przesłane za pomocą Azure Portal lub PowerShell muszą przechowywać plik BACPAC oraz pliki tymczasowe generowane przez strukturę aplikacji warstwy danych (DacFX). Wymagane miejsce na dysku zmienia się w różnych bazach danych o takim samym rozmiarze i może wymagać wolnego miejsca na dysku do 3 razy większym niż rozmiar bazy danych. Maszyny, na których jest uruchomione żądanie importu/eksportu, mają tylko miejsce na dysku lokalnym 450GB. W rezultacie niektóre żądania mogą zakończyć się niepowodzeniem z powodu `There is not enough space on the disk`błędu. W takim przypadku obejście ma na celu uruchomienie programu sqlpackage. exe na komputerze z wystarczającą ilością miejsca na dysku lokalnym. Zachęcamy do [](#import-from-a-bacpac-file-using-sqlpackage) używania sqlpackage do importowania/eksportowania baz danych większych niż 150 GB, aby uniknąć tego problemu.
 
1. Aby zaimportować plik BACPAC do nowej pojedynczej bazy danych przy użyciu Azure Portal, Otwórz odpowiednią stronę serwera bazy danych, a następnie na pasku narzędzi wybierz pozycję **Importuj bazę danych**.  

   ![Import1 bazy danych](./media/sql-database-import/import1.png)

2. Wybierz konto magazynu i kontener dla pliku BACPAC, a następnie wybierz plik BACPAC, z którego ma zostać zaimportowany.
3. Określ nowy rozmiar bazy danych (zwykle taka sama jak źródło), a poświadczenia SQL serwera docelowego. Aby uzyskać listę możliwych wartości dla nowej bazy danych Azure SQL, zobacz [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![Import2 bazy danych](./media/sql-database-import/import2.png)

4. Kliknij przycisk **OK**.

5. Aby monitorować postęp importowania, Otwórz stronę serwera bazy danych, a następnie w obszarze **Ustawienia**wybierz pozycję **historia importowania/eksportowania**. Jeśli operacja się powiedzie, importu ma **Ukończono** stanu.

   ![Stan importowania bazy danych](./media/sql-database-import/import-status.png)

6. Aby sprawdzić, czy baza danych jest aktywna na serwerze bazy danych, wybierz opcję **bazy danych SQL** i sprawdź, czy nowa baza danych jest w **trybie online**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Importuj z pliku BACPAC przy użyciu narzędzia SqlPackage

Aby zaimportować bazę danych SQL Server przy użyciu [](https://docs.microsoft.com/sql/tools/sqlpackage) narzędzia wiersza polecenia sqlpackage, zobacz [Importowanie parametrów i właściwości](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). Element sqlpackage ma najnowsze [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) i [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Możesz również pobrać najnowsze [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) z Centrum pobierania Microsoft.

W przypadku skalowania i wydajności zalecamy użycie elementu sqlpackage w większości środowisk produkcyjnych, a nie przy użyciu Azure Portal. Blog zespołu ds. pomocy technicznej SQL Server o migracji przy `BACPAC` użyciu plików można znaleźć w temacie [Migrowanie z SQL Server do Azure SQL Database przy użyciu plików BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Skalowalność i wydajność zaleca przy użyciu narzędzia SqlPackage w większości środowisk produkcyjnych. Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).

Następujące polecenie sqlpackage importuje bazę danych **AdventureWorks2008R2** z magazynu lokalnego do serwera Azure SQL Database o nazwie **mynewserver20170403**. Tworzy nową bazę danych o nazwie **myMigratedDatabase** z **Premium** warstwy usług i **P6** cel usługi. Zmień te wartości jako odpowiednie dla danego środowiska.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Aby nawiązać połączenie z serwerem SQL Database zarządzania pojedynczą bazą danych za zaporą firmową, zapora musi mieć otwarty port 1433. Aby nawiązać połączenie z wystąpieniem zarządzanym, musisz mieć [połączenie typu punkt-lokacja](sql-database-managed-instance-configure-p2s.md) lub połączenie z usługą Express Route.
>

Ten przykład przedstawia sposób importowania bazy danych przy użyciu narzędzia SqlPackage przy użyciu uwierzytelniania usługi Active Directory Universal.

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-into-a-single-database-from-a-bacpac-file-using-powershell"></a>Importowanie do pojedynczej bazy danych z pliku BACPAC przy użyciu programu PowerShell

> [!NOTE]
> [Wystąpienie zarządzane](sql-database-managed-instance.md) nie obsługuje obecnie migrowania bazy danych do bazy danych wystąpienia z pliku BACPAC przy użyciu Azure PowerShell. Aby zaimportować do wystąpienia zarządzanego, użyj SQL Server Management Studio lub sqlpackage.

> [!NOTE]
> Komputery przetwarzające żądania importu/eksportu przesłane za pomocą portalu lub programu PowerShell muszą przechowywać plik BACPAC oraz pliki tymczasowe generowane przez strukturę aplikacji warstwy danych (DacFX). Wymagane miejsce na dysku zmienia się znacznie w baz danych z tym samym rozmiarem i może trwać do 3 razy rozmiaru bazy danych. Maszyny, na których jest uruchomione żądanie importu/eksportu, mają tylko miejsce na dysku lokalnym 450GB. W związku z tym niektóre żądania mogą zakończyć się niepowodzeniem z powodu błędu "nie ma wystarczającej ilości miejsca na dysku". W takim przypadku obejście ma na celu uruchomienie programu sqlpackage. exe na komputerze z wystarczającą ilością miejsca na dysku lokalnym. Podczas importowania/eksportowania baz danych o rozmiarze większym [](#import-from-a-bacpac-file-using-sqlpackage) niż 150 GB należy użyć sqlpackage, aby uniknąć tego problemu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

Użyj polecenia cmdlet [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) , aby przesłać żądanie importu bazy danych do usługi Azure SQL Database. W zależności od rozmiaru bazy danych importowania może potrwać trochę czasu.

 ```powershell
 $importRequest = New-AzSqlDatabaseImport 
    -ResourceGroupName "<your_resource_group>" `
    -ServerName "<your_server>" `
    -DatabaseName "<your_database>" `
    -DatabaseMaxSizeBytes "<database_size_in_bytes>" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey -ResourceGroupName "<your_resource_group>" -StorageAccountName "<your_storage_account").Value[0] `
    -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "<your_server_admin_account_user_id>" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "<your_server_admin_account_password>" -AsPlainText -Force)

 ```

 Aby sprawdzić postęp importowania, można użyć polecenia cmdlet [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) . Uruchomienie polecenia cmdlet natychmiast po żądaniu zwykle zwraca **stan: W toku**. Importowanie zostało zakończone, gdy zobaczysz **stan: Powiodło**się.

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
> Aby uzyskać inny przykładowy skrypt, zobacz [Importowanie bazy danych z pliku BACPAC](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Ograniczenia

Importowanie do bazy danych w puli elastycznej nie jest obsługiwane. Dane można importować do pojedynczej bazy danych, a następnie przenieść ją do puli elastycznej.

## <a name="import-using-wizards"></a>Importowanie przy użyciu kreatorów

Można również użyć tych kreatorów.

- [Importuj Kreatora aplikacji warstwy danych w programie SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [Kreator programu SQL Server importowania i eksportowania](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak nawiązać połączenie z zaimportowanym [SQL Database i wykonać zapytania, zobacz Szybki Start: Azure SQL Database: Użyj SQL Server Management Studio, aby nawiązać połączenie](sql-database-connect-query-ssms.md)i wykonać zapytania dotyczące danych.
- Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).
- Aby uzyskać informacje dotyczące całego programu SQL Server proces migracji bazy danych, w tym zalecenia dotyczące wydajności, zobacz [migracji bazy danych programu SQL Server do usługi Azure SQL Database](sql-database-single-database-migrate.md).
- Aby dowiedzieć się, jak zarządzać i udostępniania kluczy magazynu i dostępu współdzielonego podpisów bezpieczne, zobacz [Przewodnik po zabezpieczeniach magazynu Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
