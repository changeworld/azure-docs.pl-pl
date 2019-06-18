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
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 98b316f8a9c1c8ceba91870af4ff67b1aa854a9b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65785334"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Szybki start: Importowanie pliku BACPAC do bazy danych w usłudze Azure SQL Database

Możesz zaimportować bazę danych programu SQL Server do bazy danych w usłudze Azure SQL Database przy użyciu [BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) pliku. Możesz zaimportować dane z `BACPAC` plików przechowywanych w usłudze Azure Blob storage (tylko w przypadku magazynu standardowego) lub z magazynu lokalnego w lokalizacji lokalnej. Aby zmaksymalizować szybkość importu, podając więcej i szybciej zasobów, Skaluj bazę danych na wyższą warstwę usług obliczeniowych i rozmiar podczas procesu importowania. Następnie można skalować w dół po import zakończy się pomyślnie.

> [!NOTE]
> Poziom zgodności zaimportowanej bazy danych opiera się na poziomie zgodności bazy danych źródłowych.
> [!IMPORTANT]
> Po zaimportowaniu bazy danych, istnieje możliwość obsługi bazy danych w jego bieżący poziom zgodności (poziom 100 AdventureWorks2008R2 bazy danych) lub na wyższym poziomie. Aby uzyskać więcej informacji o implikacjach i opcjach związanych z używaniem bazy danych na określonym poziomie zgodności, zobacz [ALTER DATABASE Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) (Instrukcja ALTER DATABASE — poziom zgodności). Zapoznaj się też z tematem [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql), aby uzyskać informacje o dodatkowych ustawieniach na poziomie bazy danych związanych z poziomem zgodności.

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>Importuj z pliku BACPAC w witrynie Azure portal

[Witryny Azure portal](https://portal.azure.com) *tylko* obsługuje tworzenie pojedynczej bazy danych w usłudze Azure SQL Database i *tylko* z pliku BACPAC przechowywanego w usłudze Azure Blob storage.

> [!NOTE]
> [Wystąpienie zarządzane](sql-database-managed-instance.md) aktualnie nie obsługuje migracji bazy danych do wystąpienia bazy danych z pliku BACPAC przy użyciu witryny Azure portal. Aby zaimportować do wystąpienia zarządzanego, należy użyć programu SQL Server Management Studio lub SQLPackage.

1. Aby zaimportować z pliku BACPAC do nowej pojedynczej bazy danych przy użyciu witryny Azure portal, otwórz stronę z odpowiednią bazą danych serwera, a następnie na pasku narzędzi wybierz **Importuj bazę danych**.  

   ![Import1 bazy danych](./media/sql-database-import/import1.png)

2. Wybierz konto magazynu i kontener dla pliku BACPAC, a następnie wybierz plik BACPAC, z którego chcesz zaimportować.
3. Określ nowy rozmiar bazy danych (zwykle taka sama jak źródło), a poświadczenia SQL serwera docelowego. Aby uzyskać listę możliwych wartości dla nowej bazy danych Azure SQL, zobacz [Create Database](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![Import2 bazy danych](./media/sql-database-import/import2.png)

4. Kliknij przycisk **OK**.

5. Aby monitorować postęp importowania, otwórz stronę serwera bazy danych, a w obszarze **ustawienia**, wybierz opcję **Historia importowania/eksportowania**. Jeśli operacja się powiedzie, importu ma **Ukończono** stanu.

   ![Stan importu bazy danych](./media/sql-database-import/import-status.png)

6. Aby sprawdzić, baza danych znajduje się na żywo na serwerze bazy danych, wybierz **baz danych SQL** i sprawdź Nowa baza danych jest **Online**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Importuj z pliku BACPAC przy użyciu narzędzia SqlPackage

Aby zaimportować bazę danych programu SQL Server za pomocą [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) narzędzie wiersza polecenia, zobacz [zaimportować parametry i właściwości](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage ma najnowsze [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) i [programu SQL Server Data Tools dla programu Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Możesz również pobrać najnowsze [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) z Centrum pobierania Microsoft.

Skalowalność i wydajność zalecamy przy użyciu narzędzia SqlPackage w większości środowisk produkcyjnych, a nie przy użyciu witryny Azure portal. Dla programu SQL Server Customer Advisory Team blog o migracji za pomocą `BACPAC` plików, zobacz [migracji z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Skalowalność i wydajność zaleca przy użyciu narzędzia SqlPackage w większości środowisk produkcyjnych. Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).

Następujące polecenie SqlPackage importuje **AdventureWorks2008R2** bazy danych z magazynu lokalnego do serwera Azure SQL Database o nazwie **mynewserver20170403**. Tworzy nową bazę danych o nazwie **myMigratedDatabase** z **Premium** warstwy usług i **P6** cel usługi. Zmień te wartości jako odpowiednie dla danego środowiska.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Połączyć się z serwerem bazy danych SQL, zarządzanie pojedynczej bazy danych spoza firmowej zapory, zapora musi mieć port 1433 Otwórz. Aby połączyć z wystąpienia zarządzanego, konieczne jest posiadanie [połączenia punkt lokacja](sql-database-managed-instance-configure-p2s.md) lub połączenia expressroute.
>

Ten przykład przedstawia sposób importowania bazy danych przy użyciu narzędzia SqlPackage przy użyciu uwierzytelniania usługi Active Directory Universal.

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-into-a-single-database-from-a-bacpac-file-using-powershell"></a>Importowanie do pojedynczej bazy danych z pliku BACPAC przy użyciu programu PowerShell

> [!NOTE]
> [Wystąpienie zarządzane](sql-database-managed-instance.md) aktualnie nie obsługuje migracji bazy danych do wystąpienia bazy danych z pliku BACPAC przy użyciu programu Azure PowerShell. Aby zaimportować do wystąpienia zarządzanego, należy użyć programu SQL Server Management Studio lub SQLPackage.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł programu PowerShell usługi Azure Resource Manager jest nadal obsługiwane przez usługę Azure SQL Database, ale wszystkie przyszłego rozwoju jest Az.Sql modułu. Dla tych poleceń cmdlet, zobacz [elementu AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w Az module, a w modułach AzureRm są zasadniczo identyczne.

Użyj [New AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) polecenia cmdlet, aby przesłać żądanie importu bazy danych do usługi Azure SQL Database. W zależności od rozmiaru bazy danych importowania może potrwać trochę czasu.

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

 Możesz użyć [Get AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) polecenia cmdlet, aby sprawdzić postęp importowania. Uruchomienie tego polecenia cmdlet natychmiast po wykonaniu żądania zwykle zwraca **stanu: InProgress**. Importowanie zostało zakończone, gdy pojawi się **stanu: Pomyślnie**.

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

Importowanie do bazy danych w puli elastycznej nie jest obsługiwane. Możesz importować dane do pojedynczej bazy danych i następnie przenieść bazę danych do puli elastycznej.

## <a name="import-using-wizards"></a>Importowanie przy użyciu kreatorów

Można również użyć tych kreatorów.

- [Importuj Kreatora aplikacji warstwy danych w programie SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [Kreator programu SQL Server importowania i eksportowania](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak nawiązać połączenie i wykonywać zapytania zaimportowanej bazy danych SQL, zobacz [Szybki Start: Azure SQL Database: Użyj programu SQL Server Management Studio do nawiązywania połączeń i wykonywanie zapytań dotyczących danych](sql-database-connect-query-ssms.md).
- Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).
- Aby uzyskać informacje dotyczące całego programu SQL Server proces migracji bazy danych, w tym zalecenia dotyczące wydajności, zobacz [migracji bazy danych programu SQL Server do usługi Azure SQL Database](sql-database-single-database-migrate.md).
- Aby dowiedzieć się, jak zarządzać i udostępniania kluczy magazynu i dostępu współdzielonego podpisów bezpieczne, zobacz [Przewodnik po zabezpieczeniach magazynu Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
