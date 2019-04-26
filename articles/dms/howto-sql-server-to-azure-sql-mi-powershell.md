---
title: Migrowanie programu SQL Server do wystąpienia zarządzanego usługi Azure SQL bazy danych za pomocą usługi Database Migration Service i programu PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się przeprowadzić migrację z lokalnego programu SQL Server do wystąpienia zarządzanego Azure SQL DB przy użyciu programu Azure PowerShell.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: ed0d65a0f00bd5ebc3227a249beec6bafd791347
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60532544"
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-database-managed-instance-using-azure-powershell"></a>Migrowanie lokalnego programu SQL Server do bazy danych wystąpienia zarządzanego Azure SQL przy użyciu programu Azure PowerShell
W tym artykule, wykonywana jest migracja **Adventureworks2012** bazy danych przywróconej do lokalnego wystąpienia programu SQL Server 2005 lub nowszego do wystąpienia zarządzanego bazy danych SQL Azure za pomocą programu Microsoft Azure PowerShell. Można migrować bazy danych z lokalnego wystąpienia programu SQL Server do wystąpienia zarządzanego Azure SQL Database przy użyciu `Az.DataMigration` modułu w programie Microsoft Azure PowerShell.

W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
> * Utwórz grupę zasobów.
> * Tworzenie wystąpienia usługi Azure Database Migration Service.
> * Utwórz projekt migracji w wystąpieniu usługi Azure Database Migration Service.
> * Uruchamianie migracji.

## <a name="prerequisites"></a>Wymagania wstępne
Aby wykonać te kroki, potrzebne są:

- [SQL Server 2016 lub nowszego](https://www.microsoft.com/sql-server/sql-server-downloads) (w każdej wersji)
- Aby włączyć protokół TCP/IP, która jest domyślnie wyłączona, za pomocą instalacji programu SQL Server Express. Włącz protokół TCP/IP, postępując zgodnie z artykułem [Włączanie lub wyłączanie protokołu sieciowego serwera](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Aby skonfigurować swoje [Windows zapory dla dostępu aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Wystąpienie usługi Azure SQL Database. Można utworzyć wystąpienie usługi Azure SQL Database, postępując zgodnie z szczegółowo w artykule [utworzyć bazę danych Azure SQL database w witrynie Azure portal](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 lub nowszej.
- Aby zostały utworzone z siecią Wirtualną przy użyciu modelu wdrażania usługi Azure Resource Manager, który zapewnia łączność lokacja lokacja na serwerach źródłowych z lokalnych Azure Database Migration Service przy użyciu [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Aby ukończyć oceny migracji bazy danych i schemat w środowisku lokalnym przy użyciu Data Migration Assistant, zgodnie z opisem w artykule [wykonywania oceny migracji programu SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)
- Aby pobrać i zainstalować moduł Az.DataMigration z galerii programu PowerShell przy użyciu [polecenia cmdlet Install-Module programu PowerShell](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)
- Aby upewnić się, że poświadczenia używane do połączenia z wystąpieniem programu SQL Server ma [serwera kontroli](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) uprawnień.
- Aby upewnić się, że poświadczenia używane do połączenia z obiektem docelowym usługi Azure SQL DB wystąpienia ma uprawnienie Kontrola bazy danych na docelowych baz danych Azure SQL Database.
- Subskrypcja platformy Azure. Jeśli nie masz, Utwórz [bezpłatne](https://azure.microsoft.com/free/) konta przed przystąpieniem do wykonywania.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Zaloguj się do subskrypcji usługi Microsoft Azure
Użyj instrukcjami w artykule [Zaloguj się przy użyciu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps) zalogować się do subskrypcji platformy Azure przy użyciu programu PowerShell.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów, aby można było utworzyć maszynę wirtualną.

Utwórz grupę zasobów za pomocą [New AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) polecenia. 

Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w *EastUS* regionu.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```
## <a name="create-an-instance-of-the-azure-database-migration-service"></a>Utwórz wystąpienie usługi Azure Database Migration Service 
Można utworzyć nowego wystąpienia usługi Azure Database Migration Service przy użyciu `New-AzDataMigrationService` polecenia cmdlet. To polecenie cmdlet oczekuje następujących wymaganych parametrów:
- *Nazwa grupy zasobów platformy Azure*. Możesz użyć [New AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) polecenie, aby utworzyć grupę zasobów platformy Azure, jak przedstawiono wcześniej, a następnie podaj jej nazwę jako parametr.
- *Nazwa usługi*. Ciąg, który odnosi się do żądanego unikatową nazwę usługi dla usługi Azure Database Migration Service 
- *Lokalizacja*. Określa lokalizację usługi. Określ lokalizację centrum danych platformy Azure, takich jak zachodnie stany USA lub Azja południowo-wschodnia
- *Jednostka SKU*. Ten parametr odnosi się do nazwy jednostek Sku usługi DMS. Obecnie obsługiwane nazwy jednostek Sku są *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*
- *Identyfikator podsieci wirtualnej*. Można użyć polecenia cmdlet [New AzVirtualNetworkSubnetConfig](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) utworzyć podsieć. 

Poniższy przykład tworzy usługi o nazwie *MyDMS* w grupie zasobów *MyDMSResourceGroup* na terenie *wschodnie stany USA* regionem przy użyciu sieci wirtualnej o nazwie  *MyVNET* i podsieci o nazwie *MySubnet*.

```powershell
 $vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji
Po utworzeniu wystąpienia usługi Azure Database Migration Service, Utwórz projekt migracji. Projekt usługi Azure Database Migration Service wymaga informacji o połączeniu dla zarówno wystąpień źródłowym i docelowym, a także wykaz baz danych, które chcesz przeprowadzić migrację w ramach projektu.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Utwórz obiekt informacje o połączeniu z bazą danych dla połączeń źródłowe i docelowe
Można utworzyć obiekt informacje o połączeniu z bazą danych przy użyciu `New-AzDmsConnInfo` polecenia cmdlet. To polecenie cmdlet oczekuje, że następujące parametry:
- *Wartością servertype ustawioną*. Połączenia z bazą danych żądanego typu, na przykład SQL, Oracle lub MySQL. Użyj programu SQL dla programu SQL Server i Azure SQL.
- *Źródło danych*. Nazwa lub adres IP wystąpienia programu SQL Server lub bazie danych Azure SQL.
- *Wartość AuthType*. Typ uwierzytelniania dla połączenia, który może być SqlAuthentication lub WindowsAuthentication.
- *TrustServerCertificate* parametr ustawia wartość wskazującą, czy kanał jest szyfrowany podczas pomijanie zalet łańcucha certyfikatów w celu zweryfikowania relacji zaufania. Wartość może być prawdziwe lub fałszywe.

Poniższy przykład tworzy obiekt informacje o połączeniu dla źródła programu SQL Server o nazwie MySourceSQLServer przy użyciu uwierzytelniania sql: 

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

W kolejnym przykładzie pokazano tworzenie informacje o połączeniu dla serwera usługi Azure SQL Database Managed Instance o nazwie "targetmanagedinstance.database.windows.net" przy użyciu uwierzytelniania sql:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Podaj baz danych dla projektu migracji
Utwórz listę `AzDataMigrationDatabaseInfo` obiektów, które określa baz danych w ramach usługi Azure Database Migration projekt, który można podać jako parametr do tworzenia projektu. Polecenia cmdlet `New-AzDataMigrationDatabaseInfo` może służyć do tworzenia AzDataMigrationDatabaseInfo. 

Poniższy przykład tworzy `AzDataMigrationDatabaseInfo` projektu dla **AdventureWorks** bazy danych i dodaje go do listy można podać jako parametr do utworzenia projektu.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Utwórz obiekt projektu
Na koniec możesz utworzyć projekt migracji bazy danych Azure o nazwie *MyDMSProject* na terenie *wschodnie stany USA* przy użyciu `New-AzDataMigrationProject` i dodawanie utworzonej wcześniej połączenia źródłowe i docelowe i listy bazy danych do migracji.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLMI `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Tworzenie i uruchamianie zadania migracji
Na koniec Utwórz i uruchom zadanie migracji bazy danych Azure. Zadanie migracji bazy danych Azure wymaga informacji o poświadczeniach połączenia dla źródła i docelowych i listy tabel bazy danych do zmigrowania oprócz tych informacji, które już są dostarczane z projektu utworzonego jako warunek wstępny. 

### <a name="create-credential-parameters-for-source-and-target"></a>Tworzenie poświadczeń parametry źródłowe i docelowe
Poświadczenia zabezpieczeń połączenia mogą być tworzone jako [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) obiektu. 

W poniższym przykładzie pokazano tworzenie *PSCredential* obiektów dla połączeń elementy źródłowe i docelowe, zapewniając hasła jako zmiennych ciągu *$sourcePassword* i *$ Hasło_docelowe*. 

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-backup-fileshare-object"></a>Tworzenie kopii zapasowej udziału plików obiektu
Teraz należy utworzyć obiekt FileShare reprezentujący lokalny udział sieciowy SMB, że usługi Azure Database Migration Service może potrwać źródła kopii zapasowych bazy danych za pomocą polecenia cmdlet New-AzDmsFileShare.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Utwórz obiekt wybranej bazy danych
Następnym krokiem jest do wybrania źródłowej i docelowej bazy danych za pomocą polecenia cmdlet New-AzDmsSelectedDB, jak pokazano w poniższym przykładzie:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

### <a name="sas-uri-for-azure-storage-container"></a>URI sygnatury dostępu Współdzielonego dla kontenera usługi Azure Storage
Utwórz zmienną, która zawiera identyfikator URI sygnatury dostępu Współdzielonego, który zapewnia dostęp do kontenera konta magazynu, do którego usługa przekazuje pliki kopii zapasowej Azure Database Migration Service.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="select-logins"></a>Wybieranie identyfikatorów logowania
Utwórz listę identyfikatorów logowania do migracji, jak pokazano w poniższym przykładzie:  Uwaga: obecnie DMS obsługuje migrację logowania SQL. 

```powershell
$selectedLogins = @("user1", "user2")
```

### <a name="select-agent-jobs"></a>Wybierz zadania agenta
Utwórz listę zadań agenta do migracji, jak pokazano w poniższym przykładzie:

>[!NOTE]
>Obecnie usługa DMS obsługuje zadania przy użyciu języka T-SQL podsystemu kroki w zadaniu tylko.

```powershell
$selectedAgentJobs = @("agentJob1", "agentJob2")
```

### <a name="create-and-start-a-migration-task"></a>Tworzenie i uruchamianie zadania migracji

Użyj `New-AzDataMigrationTask` polecenia cmdlet, aby utworzyć i uruchomić zadanie migracji. To polecenie cmdlet oczekuje, że następujące parametry:
- *TaskType*. Typ tworzonego zadania migracji dla programu SQL Server do wystąpienia zarządzanego Azure SQL Database migracji typu *MigrateSqlServerSqlDbMi* oczekuje. 
- *Nazwa grupy zasobów*. Nazwa grupy zasobów platformy Azure, w której chcesz utworzyć zadanie.
- *ServiceName*. Wystąpienie usługi Azure Database Migration Service w której chcesz utworzyć zadanie.
- *ProjectName*. Nazwa projektu Azure Database Migration Service, w której chcesz utworzyć zadanie. 
- *TaskName*. Nazwa zadania, które ma zostać utworzony. 
- *SourceConnection*. Obiekt AzDmsConnInfo reprezentującą połączenie ze źródłem programu SQL Server.
- *TargetConnection*. Obiekt AzDmsConnInfo reprezentujący docelowym wystąpienia zarządzanego Azure SQL Database połączenie.
- *SourceCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) obiektu do łączenia się z serwera źródłowego.
- *TargetCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) obiekt do nawiązywania połączenia z serwerem docelowym.
- *SelectedDatabase*. Obiekt AzDataMigrationSelectedDB reprezentujący mapowania bazy danych źródłowych i docelowych.
- *BackupFileShare*. Azure Database Migration Service może zająć źródła kopii zapasowych bazy danych do udziału sieci lokalnej reprezentujących obiekt udziału plików.
- *BackupBlobSasUri*. Identyfikator URI sygnatury dostępu Współdzielonego, który zapewnia dostęp do kontenera konta magazynu, do którego usługa przekazuje pliki kopii zapasowej Azure Database Migration Service. Dowiedz się, jak uzyskać identyfikator URI sygnatury dostępu Współdzielonego dla kontenera obiektów blob.
- *SelectedLogins*. Lista wybranych logowania się do migracji.
- *SelectedAgentJobs*. Lista zadań agenta wybranych do migracji.

Poniższy przykład tworzy i uruchamia zadanie migracji o nazwie myDMSTask:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```

## <a name="monitor-the-migration"></a>Monitorowanie migracji
Możesz monitorować zadania migracji, uruchamianie, badając właściwość stanu zadania, jak pokazano w poniższym przykładzie:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="deleting-the-dms-instance"></a>Usuwanie wystąpienia usługi DMS
Po zakończeniu migracji można usunąć wystąpienia usługi Azure DMS:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="next-steps"></a>Kolejne kroki
- Przejrzyj wskazówki dotyczące migracji w programie Microsoft [Przewodnik po migracji bazy danych](https://datamigration.microsoft.com/).
