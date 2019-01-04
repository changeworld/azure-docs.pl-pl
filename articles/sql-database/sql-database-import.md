---
title: Importowanie pliku BACPAC do utworzenia usługi Azure SQL database | Dokumentacja firmy Microsoft
description: Utwórz bazę danych SQL newAzure przez zaimportowanie pliku BACPAC.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: douglaslMS
ms.author: douglasl
ms.reviewer: carlrab
manager: craigg
ms.date: 12/05/2018
ms.openlocfilehash: 9e79aa2315118bcd9ce4328e74d51d7a22ea6247
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53744568"
---
# <a name="quickstart-import-a-bacpac-file-to-a-new-azure-sql-database"></a>Szybki start: Importowanie pliku BACPAC do nowej bazy danych SQL Azure

Można przeprowadzić migrację bazy danych programu SQL Server do usługi Azure SQL database za pomocą [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) pliku (pliku zip z `.bacpac` rozszerzenia, które ma metadanych i danych w bazie danych). Można zaimportować plik BACPAC z magazynu obiektów blob platformy Azure (tylko w przypadku magazynu standardowego) lub z magazynu lokalnego w lokalizacji lokalnej. Aby zapewnić maksymalną szybkość importowania, możesz określić wyższej warstwy usługi i obliczenia rozmiaru (na przykład P6). Następnie można skalować w dół po import zakończy się pomyślnie. Poziom zgodności zaimportowanej bazy danych opiera się na poziomie zgodności bazy danych źródłowych.

> [!IMPORTANT]
> Po zaimportowaniu bazy danych, istnieje możliwość obsługi bazy danych w jego bieżący poziom zgodności (poziom 100 AdventureWorks2008R2 bazy danych) lub na wyższym poziomie. Aby uzyskać więcej informacji o implikacjach i opcjach związanych z używaniem bazy danych na określonym poziomie zgodności, zobacz [ALTER DATABASE Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) (Instrukcja ALTER DATABASE — poziom zgodności). Zapoznaj się też z tematem [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql), aby uzyskać informacje o dodatkowych ustawieniach na poziomie bazy danych związanych z poziomem zgodności.

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>Importuj z pliku BACPAC w witrynie Azure portal

W tej sekcji przedstawiono sposób, w [witryny Azure portal](https://portal.azure.com), aby utworzyć bazę danych Azure SQL database z pliku BACPAC przechowywanego w usłudze Azure blob storage. Portal *tylko* magazynu obiektów blob obsługuje importowanie pliku BACPAC na platformie Azure.

> [!NOTE]
> [Wystąpienie usługi Azure SQL Database Managed](sql-database-managed-instance.md) obsługuje importowania z pliku BACPAC przy użyciu innych metod, w tym artykule, ale obecnie nie obsługuje migracji w witrynie Azure portal.

Aby zaimportować bazę danych w witrynie Azure portal, otwórz stronę serwer logiczny, który będzie hostować importu i, na pasku narzędzi wybierz **Importuj bazę danych**.  

   ![Importowanie bazy danych](./media/sql-database-import/import.png)

Wybierz konto magazynu, kontener i plik BACPAC, który chcesz zaimportować. Określ nowy rozmiar bazy danych (zwykle taka sama jak źródło), a poświadczenia SQL serwera docelowego. 

### <a name="monitor-imports-progress"></a>Monitoruj Postęp importowania

Aby monitorować postęp importowania, otwórz stronę serwera logicznego zaimportowanej bazy danych, a w obszarze **ustawienia**, wybierz opcję **Historia importowania/eksportowania**. Jeśli operacja się powiedzie, importu ma **Ukończono** stanu.

Aby sprawdzić, baza danych znajduje się na żywo na serwerze, wybierz **baz danych SQL** i sprawdź Nowa baza danych jest **Online**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Importuj z pliku BACPAC przy użyciu narzędzia SqlPackage

Aby zaimportować bazę danych SQL za pomocą [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) narzędzie wiersza polecenia, zobacz [zaimportować parametry i właściwości](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage ma najnowsze [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) i [programu SQL Server Data Tools dla programu Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Możesz również pobrać najnowsze [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) z Centrum pobierania Microsoft.

Skalowalność i wydajność zaleca przy użyciu narzędzia SqlPackage w większości środowisk produkcyjnych. Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).

Następujące polecenie SqlPackage importuje **AdventureWorks2008R2** bazy danych z magazynu lokalnego do serwera logicznego usługi Azure SQL Database o nazwie **mynewserver20170403**. Tworzy nową bazę danych o nazwie **myMigratedDatabase** z **Premium** warstwy usług i **P6** cel usługi. Zmień te wartości jako odpowiednie dla danego środowiska.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Serwer logiczny usługi Azure SQL Database nasłuchuje na porcie 1433. Aby nawiązać połączenie z serwerem logicznym spoza firmowej zapory, zapora musi mieć tego portu, Otwórz.
>

Ten przykład przedstawia sposób importowania bazy danych przy użyciu narzędzia SqlPackage przy użyciu uwierzytelniania usługi Active Directory Universal.

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-from-a-bacpac-file-using-powershell"></a>Importuj z pliku BACPAC przy użyciu programu PowerShell

Użyj [New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) polecenia cmdlet, aby przesłać żądanie importu bazy danych do usługi Azure SQL Database. W zależności od rozmiaru bazy danych importowania może potrwać trochę czasu.

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport 
    -ResourceGroupName "<your_resource_group>" `
    -ServerName "<your_server>" `
    -DatabaseName "<your_database>" `
    -DatabaseMaxSizeBytes "<database_size_in_bytes>" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "<your_resource_group>" -StorageAccountName "<your_storage_account").Value[0] `
    -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "<your_server_admin_account_user_id>" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "<your_server_admin_account_password>" -AsPlainText -Force)

 ```

 Możesz użyć [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) polecenia cmdlet, aby sprawdzić postęp importowania. Uruchomienie tego polecenia cmdlet natychmiast po wykonaniu żądania zwykle zwraca **stanu: W toku**. Importowanie zostało zakończone, gdy pojawi się **stanu: Pomyślnie**.

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
Aby uzyskać inny przykładowy skrypt, zobacz [Importowanie bazy danych z pliku BACPAC](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Ograniczenia

Importowanie do bazy danych w puli elastycznej nie jest obsługiwane. Można zaimportować danych do pojedynczej bazy danych, a następnie przenieść bazę danych do puli.

## <a name="import-using-wizards"></a>Importowanie przy użyciu kreatorów

Można również użyć tych kreatorów.

- [Importuj Kreatora aplikacji warstwy danych w programie SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [Kreator programu SQL Server importowania i eksportowania](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak nawiązać połączenie i wykonywać zapytania zaimportowanej bazy danych SQL, zobacz [Szybki Start: Usługa Azure SQL Database: Użyj programu SQL Server Management Studio do nawiązywania połączeń i wykonywanie zapytań dotyczących danych](sql-database-connect-query-ssms.md).
- Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).
- Aby uzyskać informacje dotyczące całego programu SQL Server proces migracji bazy danych, w tym zalecenia dotyczące wydajności, zobacz [migracji bazy danych programu SQL Server do usługi Azure SQL Database](sql-database-cloud-migrate.md).
- Aby dowiedzieć się, jak zarządzać i udostępniania kluczy magazynu i dostępu współdzielonego podpisów bezpieczne, zobacz [Przewodnik po zabezpieczeniach magazynu Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
