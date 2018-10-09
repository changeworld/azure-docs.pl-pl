---
title: Importowanie pliku BACPAC do utworzenia usługi Azure SQL database | Dokumentacja firmy Microsoft
description: Utwórz bazę danych SQL newAzure przez zaimportowanie pliku BACPAC.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 09/14/2018
ms.openlocfilehash: e828b288f2c3ab86a74709682eb7f96f8baf73ab
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2018
ms.locfileid: "48869486"
---
# <a name="import-a-bacpac-file-to-a-new-azure-sql-database"></a>Importowanie pliku BACPAC do nowej bazy danych SQL Azure

Kiedy należy zaimportować bazę danych z archiwum lub podczas migracji z innej platformy, można importować schemat bazy danych i danych z [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) pliku. Plik BACPAC jest plikiem ZIP z rozszerzeniem pliku BACPAC zawierające metadane i dane z bazy danych programu SQL Server. Można zaimportować plik BACPAC z magazynu obiektów blob platformy Azure (tylko w przypadku magazynu standardowego) lub z magazynu lokalnego w lokalizacji lokalnej. Aby zapewnić maksymalną szybkość importu, firma Microsoft zaleca, określ wyższej warstwy usługi i obliczenia rozmiaru, na przykład P6, a następnie Skaluj w dół zgodnie z potrzebami, po pomyślnym importu. Ponadto poziom zgodności bazy danych, po zaimportowaniu opiera się na poziom zgodności bazy danych źródłowych. 

> [!IMPORTANT] 
> Po przeprowadzeniu migracji bazy danych do usługi Azure SQL Database, istnieje możliwość obsługi bazy danych w jego bieżący poziom zgodności (poziom 100 AdventureWorks2008R2 bazy danych) lub na wyższym poziomie. Aby uzyskać więcej informacji o implikacjach i opcjach związanych z używaniem bazy danych na określonym poziomie zgodności, zobacz [ALTER DATABASE Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) (Instrukcja ALTER DATABASE — poziom zgodności). Zapoznaj się też z tematem [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql), aby uzyskać informacje o dodatkowych ustawieniach na poziomie bazy danych związanych z poziomem zgodności.   >

## <a name="import-from-a-bacpac-file-using-azure-portal"></a>Importuj z pliku BACPAC przy użyciu witryny Azure portal

Ten artykuł zawiera wskazówki dotyczące tworzenia usługi Azure SQL database z pliku BACPAC przechowywanego w usłudze Azure blob storage przy użyciu [witryny Azure portal](https://portal.azure.com). Importowanie przy użyciu portalu Azure, tylko obsługuje importowanie pliku BACPAC z magazynu obiektów blob platformy Azure.

Aby zaimportować bazę danych przy użyciu witryny Azure portal, otwórz stronę serwera (nie strona dla bazy danych) bazy danych do skojarzenia, a następnie kliknij przycisk **zaimportować** na pasku narzędzi. Określ konto magazynu i kontener, a następnie wybierz plik BACPAC, który chcesz zaimportować. Wybierz rozmiar nowej bazy danych (zwykle taka sama jak źródło) i podaj miejsce docelowe poświadczeń programu SQL Server.  

   ![Importowanie bazy danych](./media/sql-database-import/import.png)

Aby monitorować postęp operacji importu, otwórz stronę zawierającą importowaną bazą danych serwera logicznego. Przewiń w dół do **operacji** a następnie kliknij przycisk **Import/Export** historii.

> [!NOTE]
> [Wystąpienie usługi Azure SQL Database Managed](sql-database-managed-instance.md) obsługiwane importowania z pliku BACPAC przy użyciu innych metod, w tym artykule, ale obecnie nie obsługuje migracji za pomocą witryny Azure portal.

### <a name="monitor-the-progress-of-an-import-operation"></a>Monitoruj postęp operacji importu

Aby monitorować postęp operacji importu, otwórz stronę serwera logicznego w którym baza danych jest importowany zaimportowane. Przewiń w dół do **operacji** a następnie kliknij przycisk **Import/Export** historii.
   
   ![Importowanie](./media/sql-database-import/import-history.png) ![stan importowania](./media/sql-database-import/import-status.png)

Aby sprawdzić, baza danych znajduje się na żywo na serwerze, kliknij przycisk **baz danych SQL** i sprawdź Nowa baza danych jest **Online**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Importuj z pliku BACPAC przy użyciu narzędzia SQLPackage

Aby zaimportować bazę danych SQL za pomocą [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) narzędzie wiersza polecenia, zobacz [zaimportować parametry i właściwości](https://docs.microsoft.com/sql/tools/sqlpackage#Import Parameters and Properties). Narzędzia SQLPackage jest dostarczany z najnowszymi wersjami programów [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) i [programu SQL Server Data Tools dla programu Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), można również pobrać najnowszą wersję [SqlPackage ](https://www.microsoft.com/download/details.aspx?id=53876) bezpośrednio z Centrum pobierania Microsoft.

Firma Microsoft zaleca korzystanie z narzędzia SQLPackage w celu zapewnienia skalowalności i wydajności w większości środowisk produkcyjnych. Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).

Zobacz następujące polecenie SQLPackage na przykład skrypt dla sposób importowania **AdventureWorks2008R2** bazy danych z magazynu lokalnego do usługi Azure SQL Database serwer logiczny, o nazwie **mynewserver20170403** w tym przykładzie. Ten skrypt pokazuje Tworzenie nowej bazy danych o nazwie **myMigratedDatabase**, za pomocą jednej z warstw usługi **Premium**i cel usługi dla **P6**. Zmień te wartości odpowiednio do danego środowiska.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=ServerAdmin;Password=<change_to_your_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Serwer logiczny usługi Azure SQL Database nasłuchuje na porcie 1433. Jeśli próbujesz nawiązać połączenie z serwerem logicznym usługi Azure SQL Database za pośrednictwem zapory firmowej, to aby połączenie się powiodło, ten port musi być otwarty w zaporze firmowej.
>

Ten przykład pokazuje, jak importować bazę danych przy użyciu uwierzytelniania usługi Active Directory Universal SqlPackage.exe:

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-from-a-bacpac-file-using-powershell"></a>Importuj z pliku BACPAC przy użyciu programu PowerShell

Użyj [New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) polecenia cmdlet, aby przesłać żądanie importu bazy danych do usługi Azure SQL Database. W zależności od rozmiaru bazy danych operacji importowania może potrwać trochę czasu.

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MyImportSample" `
    -DatabaseMaxSizeBytes "262144000" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "myResourceGroup" -StorageAccountName $storageaccountname).Value[0] `
    -StorageUri "http://$storageaccountname.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "ServerAdmin" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "ASecureP@assw0rd" -AsPlainText -Force)

 ```

Aby sprawdzić stan żądania importu, użyj [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) polecenia cmdlet. Uruchomiony natychmiast po wykonaniu żądania zwykle zwraca **stan: w toku**. Po wyświetleniu **stan: powodzenie** Importowanie zostało zakończone.

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
- Importowanie do bazy danych w puli elastycznej nie jest obsługiwane. Można zaimportować danych do pojedynczej bazy danych, a następnie przenieść bazę danych do puli.

## <a name="import-using-other-methods"></a>Importowanie przy użyciu innych metod

Można również użyć tych kreatorów:

- [Importuj Kreatora aplikacji warstwy danych w programie SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [Kreator programu SQL Server importowania i eksportowania](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się, jak nawiązać połączenie i wykonywać zapytania zaimportowanej bazy danych SQL, zobacz [nawiązywanie połączenia z bazą danych SQL za pomocą programu SQL Server Management Studio i wykonywanie przykładowego zapytania T-SQL](sql-database-connect-query-ssms.md).
* Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).
* Aby uzyskać informacje dotyczące całego programu SQL Server proces migracji bazy danych, w tym zalecenia dotyczące wydajności, zobacz [migracji bazy danych programu SQL Server do usługi Azure SQL Database](sql-database-cloud-migrate.md).
* Aby dowiedzieć się, jak zarządzać i udostępniania kluczy magazynu i dostępu współdzielonego signitures bezpieczne, zobacz [Przewodnik po zabezpieczeniach magazynu Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide). 


  

