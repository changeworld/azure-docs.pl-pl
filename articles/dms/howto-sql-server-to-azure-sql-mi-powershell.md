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
ms.date: 04/29/2019
ms.openlocfilehash: 96ee3f5e1b3cfe67cb75e50c6247e41f0d901393
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64867917"
---
# <a name="migrate-sql-server-on-premises-to-an-azure-sql-database-managed-instance-using-azure-powershell"></a>Migrowanie lokalnego programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database przy użyciu programu Azure PowerShell
W tym artykule, wykonywana jest migracja **Adventureworks2016** bazy danych przywrócony do lokalnego wystąpienia programu SQL Server 2005 lub nad usługą Azure SQL Database wystąpienie zarządzane przy użyciu programu Microsoft Azure PowerShell. Można migrować bazy danych z lokalnego wystąpienia programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database przy użyciu `Az.DataMigration` modułu w programie Microsoft Azure PowerShell.

W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
>
> * Utwórz grupę zasobów.
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji w wystąpieniu usługi Azure Database Migration Service.
> * Uruchamianie migracji.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ten artykuł zawiera szczegóły dotyczące sposobu przeprowadzania migracji w trybie online i offline.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać te kroki, potrzebne są:

* [SQL Server 2016 lub nowszego](https://www.microsoft.com/sql-server/sql-server-downloads) (w każdej wersji).
* Lokalna kopia **AdventureWorks2016** bazy danych, która jest dostępna do pobrania [tutaj](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017).
* Aby włączyć protokół TCP/IP, która jest domyślnie wyłączona, za pomocą instalacji programu SQL Server Express. Włącz protokół TCP/IP, postępując zgodnie z artykułem [Włączanie lub wyłączanie protokołu sieciowego serwera](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Aby skonfigurować swoje [Windows zapory dla dostępu aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
* Wystąpienie zarządzane usługi Azure SQL Database. Można utworzyć wystąpienia zarządzanego usługi Azure SQL Database, postępując zgodnie z szczegółowo w artykule [Tworzenie wystąpienia zarządzanego usługi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Aby pobrać i zainstalować [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 lub nowszej.
* Usługi Azure Virtual Network (VNet) utworzone za pomocą modelu wdrażania usługi Azure Resource Manager, który zapewnia łączność lokacja lokacja na serwerach źródłowych z lokalnych Azure Database Migration Service przy użyciu [usługiExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Ukończone oceny migracji bazy danych i schemat w środowisku lokalnym przy użyciu Data Migration Assistant, zgodnie z opisem w artykule [wykonywania oceny migracji programu SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem).
* Aby pobrać i zainstalować `Az.DataMigration` modułu (wersja 0.7.2 lub nowszej) z galerii programu PowerShell przy użyciu [polecenia cmdlet programu PowerShell Install-Module](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1).
* Aby upewnić się, że poświadczenia używane do połączenia z wystąpieniem programu SQL Server mają [serwera kontroli](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) uprawnień.
* Aby upewnić się, że poświadczenia używane do połączenia z obiektem docelowym usługi Azure SQL Database wystąpienia zarządzanego ma uprawnienie Kontrola bazy danych na wystąpienie usługi Azure SQL Database managed docelowych baz danych.

    > [!IMPORTANT]
    > Do migracji online, musi już mieć Konfigurowanie poświadczeń usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz artykuł [korzystanie z portalu do tworzenia aplikacji i usługi jednostki, które mogą uzyskiwać dostęp do zasobów usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Zaloguj się do subskrypcji usługi Microsoft Azure

Zaloguj się do subskrypcji platformy Azure przy użyciu programu PowerShell. Aby uzyskać więcej informacji, zobacz artykuł [Zaloguj się przy użyciu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupę zasobów platformy Azure to logiczny kontener, w których Azure zasoby są wdrażane i zarządzane.

Utwórz grupę zasobów za pomocą [ `New-AzResourceGroup` ](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) polecenia.

Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w *wschodnie stany USA* regionu.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Utwórz wystąpienie usługi Azure Database Migration Service

Można utworzyć nowego wystąpienia usługi Azure Database Migration Service przy użyciu `New-AzDataMigrationService` polecenia cmdlet.
To polecenie cmdlet oczekuje następujących wymaganych parametrów:

* *Nazwa grupy zasobów platformy Azure*. Możesz użyć [ `New-AzResourceGroup` ](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) polecenie, aby utworzyć grupę zasobów platformy Azure, jak przedstawiono wcześniej, a następnie podaj jej nazwę jako parametr.
* *Nazwa usługi*. Ciąg, który odnosi się do żądanego unikatową nazwę usługi dla usługi Azure Database Migration Service.
* *Lokalizacja*. Określa lokalizację usługi. Określ lokalizację centrum danych platformy Azure, takich jak zachodnie stany USA lub Azja południowo-wschodnia.
* *Jednostka SKU*. Ten parametr odnosi się do nazwy jednostek Sku usługi DMS. Obecnie obsługiwane nazwy jednostek Sku są *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*.
* *Identyfikator podsieci wirtualnej*. Można użyć polecenia cmdlet [ `New-AzVirtualNetworkSubnetConfig` ](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) utworzyć podsieć.

Poniższy przykład tworzy usługi o nazwie *MyDMS* w grupie zasobów *MyDMSResourceGroup* na terenie *wschodnie stany USA* regionem przy użyciu sieci wirtualnej o nazwie  *MyVNET* i podsieć o nazwie *MySubnet*.

> [!IMPORTANT]
> Poniższy fragment kodu jest migracji w trybie offline, który nie wymaga wystąpienia usługi Azure Database Migration Service oparte na warstwy Premium. Do migracji do usługi online wartość parametru - Sku musi zawierać warstwy Premium.

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

Można utworzyć obiekt informacje o połączeniu z bazą danych przy użyciu `New-AzDmsConnInfo` polecenia cmdlet, którego oczekuje, że następujące parametry:

* *Wartością servertype ustawioną*. Połączenia z bazą danych żądanego typu, na przykład SQL, Oracle lub MySQL. Użyj programu SQL dla programu SQL Server i Azure SQL.
* *Źródło danych*. Nazwa lub adres IP wystąpienia programu SQL Server lub wystąpienia usługi Azure SQL Database.
* *Wartość AuthType*. Typ uwierzytelniania dla połączenia, który może być SqlAuthentication lub WindowsAuthentication.
* *TrustServerCertificate*. Ten parametr ustawia wartość wskazującą, czy kanał jest szyfrowany podczas pomijanie zalet łańcucha certyfikatów w celu zweryfikowania relacji zaufania. Wartość może być `$true` lub `$false`.

Poniższy przykład tworzy obiekt informacje o połączeniu dla źródła programu SQL Server o nazwie *MySourceSQLServer* przy użyciu uwierzytelniania sql:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

W kolejnym przykładzie pokazano tworzenie informacje o połączeniu dla serwera wystąpienia zarządzanego Azure SQL Database o nazwie "targetmanagedinstance.database.windows.net" przy użyciu uwierzytelniania sql:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Podaj baz danych dla projektu migracji

Utwórz listę `AzDataMigrationDatabaseInfo` obiektów, które określa baz danych w ramach projektu usługi Azure Database Migration Service, które można podać jako parametr do tworzenia projektu. Można użyć polecenia cmdlet `New-AzDataMigrationDatabaseInfo` utworzyć `AzDataMigrationDatabaseInfo`.

Poniższy przykład tworzy `AzDataMigrationDatabaseInfo` projektu dla **AdventureWorks2016** bazy danych i dodaje go do listy można podać jako parametr do utworzenia projektu.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Utwórz obiekt projektu

Na koniec można utworzyć projekt usługi Azure Database Migration Service o nazwie *MyDMSProject* na terenie *wschodnie stany USA* przy użyciu `New-AzDataMigrationProject` i dodać wcześniej utworzony połączenia źródłowe i docelowe i Lista baz danych do migracji.

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

Następnie utwórz i Rozpocznij zadanie usługi Azure Database Migration Service. To zadanie wymaga informacji o poświadczeniach połączenia dla zarówno źródłowy i docelowy, a także listę tabel bazy danych do migracji i już informacjami z projektu utworzonego jako warunek wstępny.

### <a name="create-credential-parameters-for-source-and-target"></a>Tworzenie poświadczeń parametry źródłowe i docelowe

Utwórz połączenie poświadczeń zabezpieczeń jako [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) obiektu.

W poniższym przykładzie pokazano tworzenie *PSCredential* obiektów dla połączenia źródłowego i docelowego, zapewniając hasła jako zmiennych ciągu *$sourcePassword* i *$ Hasło_docelowe*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Utwórz obiekt kopii zapasowej udziału plików

Teraz utworzyć udział plików obiekt reprezentujący lokalny udział sieciowy SMB, do którego usługa Azure Database Migration Service może potrwać źródła kopii zapasowych bazy danych przy użyciu `New-AzDmsFileShare` polecenia cmdlet.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Utwórz obiekt wybranej bazy danych

Następnym krokiem jest wybranie źródłowej i docelowej bazy danych przy użyciu `New-AzDmsSelectedDB` polecenia cmdlet.

Poniższy przykład jest migrowany pojedynczej bazy danych programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Jeśli całego wystąpienia programu SQL Server wymaga lift-and-shift do usługi Azure SQL Database, wystąpienia zarządzanego, a następnie pętli, aby wykonać wszystkie bazy danych ze źródła znajduje się poniżej. W poniższym przykładzie $Server, $SourceUserName i $SourcePassword, szczegółowo źródła programu SQL Server.

```powershell
$Query = "(select name as Database_Name from master.sys.databases where Database_id>4)";
$Databases= (Invoke-Sqlcmd -ServerInstance "$Server" -Username $SourceUserName
-Password $SourcePassword -database master -Query $Query)
$selectedDbs=@()
foreach($DataBase in $Databases.Database_Name)
    {
      $SourceDB=$DataBase
      $TargetDB=$DataBase
      
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
                                              -Name $SourceDB `
                                              -TargetDatabaseName $TargetDB `
                                              -BackupFileShare $backupFileShare
      }
```

### <a name="sas-uri-for-azure-storage-container"></a>URI sygnatury dostępu Współdzielonego dla kontenera usługi Azure Storage

Utwórz zmienną, która zawiera identyfikator URI sygnatury dostępu Współdzielonego, który zapewnia dostęp do kontenera konta magazynu, do którego usługa przekazuje pliki kopii zapasowej Azure Database Migration Service.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="additional-configuration-requirements"></a>Dodatkowe wymagania dotyczące konfiguracji

Istnieje kilka dodatkowych wymagań, które należy spełnić, ale będą się różnić w zależności od tego, czy przeprowadzania migracji do usługi w trybie offline lub online.

#### <a name="offline-migrations"></a>Migracja w trybie offline

Tylko migracji w trybie offline należy wykonać następujące zadania dodatkowe czynności konfiguracyjne.

* **Wybierz identyfikatory logowania**. Utwórz listę identyfikatorów logowania do migracji, jak pokazano w poniższym przykładzie:

    ```powershell
    $selectedLogins = @(“user1”, “user2”)
    ```

    > [!IMPORTANT]
    > Obecnie usługa Azure Database Migration Service obsługuje Migrowanie nazw logowania SQL.

* **Wybierz zadania agenta**. Utwórz listę agenta zadania powinny być migrowane, jak pokazano w poniższym przykładzie:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > Obecnie usługa Azure Database Migration Service obsługuje zadania przy użyciu języka T-SQL-kroki podsystemu w zadaniu.

#### <a name="online-migrations"></a>Migracja online

Tylko migracji online należy wykonać następujące zadania dodatkowe czynności konfiguracyjne.

* **Konfigurowanie aplikacji usługi Azure Active Directory**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **Konfigurowanie zasobów usługi Storage**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>Tworzenie i uruchamianie zadania migracji

Użyj `New-AzDataMigrationTask` polecenia cmdlet, aby utworzyć i uruchomić zadanie migracji.

#### <a name="specify-parameters"></a>Określ parametry

##### <a name="common-parameters"></a>Wspólne parametry

Niezależnie od tego, czy przeprowadzasz migrację w trybie offline lub online `New-AzDataMigrationTask` aplet polecenia oczekuje następujących parametrów:

* *TaskType*. Typ tworzonego zadania migracji dla programu SQL Server do wystąpienia zarządzanego Azure SQL Database migracji typu *MigrateSqlServerSqlDbMi* oczekuje. 
* *Nazwa grupy zasobów*. Nazwa grupy zasobów platformy Azure, w której chcesz utworzyć zadanie.
* *ServiceName*. Wystąpienie usługi Azure Database Migration Service w której chcesz utworzyć zadanie.
* *ProjectName*. Nazwa projektu Azure Database Migration Service, w której chcesz utworzyć zadanie. 
* *TaskName*. Nazwa zadania, które ma zostać utworzony. 
* *SourceConnection*. Obiekt AzDmsConnInfo reprezentującą połączenie ze źródłem programu SQL Server.
* *TargetConnection*. Obiekt AzDmsConnInfo reprezentujący docelowym wystąpienia zarządzanego Azure SQL Database połączenie.
* *SourceCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) obiektu do łączenia się z serwera źródłowego.
* *TargetCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) obiekt do nawiązywania połączenia z serwerem docelowym.
* *SelectedDatabase*. Obiekt AzDataMigrationSelectedDB reprezentujący mapowania bazy danych źródłowych i docelowych.
* *BackupFileShare*. Azure Database Migration Service może zająć źródła kopii zapasowych bazy danych do udziału sieci lokalnej reprezentujących obiekt udziału plików.
* *BackupBlobSasUri*. Identyfikator URI sygnatury dostępu Współdzielonego, który zapewnia dostęp do kontenera konta magazynu, do którego usługa przekazuje pliki kopii zapasowej Azure Database Migration Service. Dowiedz się, jak uzyskać identyfikator URI sygnatury dostępu Współdzielonego dla kontenera obiektów blob.
* *SelectedLogins*. Lista wybranych logowania się do migracji.
* *SelectedAgentJobs*. Lista zadań agenta wybranych do migracji.

##### <a name="additional-parameters"></a>Dodatkowe parametry

`New-AzDataMigrationTask` Aplet polecenia oczekuje również parametry specyficzne dla typu migracji w trybie offline lub online, które wykonujesz.

* **Migracja offline**. Dla migracji w trybie offline `New-AzDataMigrationTask` aplet polecenia oczekuje również następujące parametry:

  * *SelectedLogins*. Lista wybranych logowania się do migracji.
  * *SelectedAgentJobs*. Lista zadań agenta wybranych do migracji.

* **Migracja online**. Migracji online `New-AzDataMigrationTask` aplet polecenia oczekuje również następujące parametry.

* *AzureActiveDirectoryApp*. Aplikacja usługi Active Directory.
* *StorageResourceID*. Identyfikator zasobu konta magazynu.

#### <a name="create-and-start-an-offline-migration-task"></a>Tworzenie i uruchamianie zadań migracji w trybie offline

Poniższy przykład tworzy i uruchamia zadanie migracji w trybie offline o nazwie **myDMSTask**:

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

#### <a name="create-and-start-an-online-migration-task"></a>Tworzenie i uruchamianie zadania migracji w trybie online

Poniższy przykład tworzy i uruchamia zadanie migracji online o nazwie **myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
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
  -SelectedDatabase $selectedDbs `
  -AzureActiveDirectoryApp $app `
  -StorageResourceId $storageResourceId
```

## <a name="monitor-the-migration"></a>Monitorowanie migracji

Aby monitorować migrację, należy wykonać poniższe zadania.

1. Skonsolidować wszystkie szczegóły migracji w zmiennej o nazwie $CheckTask.

    Łączenie migracji szczegóły, takie jak właściwości, stan i informacje o bazie danych związane z migracją, użyj poniższego fragmentu kodu:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Użyj `$CheckTask` zmienną, aby uzyskać bieżący stan zadania migracji.

    Aby użyć `$CheckTask` zmiennej, aby uzyskać bieżący stan zadania migracji, możesz monitorować zadania migracji, uruchamianie, badając właściwość stanu zadania, jak pokazano w poniższym przykładzie:

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      write-host "migration task running"
    }
    Else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      write-host "Migration task is completed Successfully"
    }
    Else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation"  -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      write-host “Migration Task Failed”
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>Wykonywanie migracji jednorazowej (online migracje tylko)

Migracja online odbywa się tworzenie pełnej kopii zapasowej i przywracania baz danych, a następnie pracy dotyczące przywracania przechowywane w BackupFileShare dzienniki transakcji.

Jeśli baza danych w wystąpieniu zarządzanym usługi Azure SQL Database są aktualizowane przy użyciu najnowszych danych, jest synchronizowany ze źródłowej bazy danych można wykonywać decyzji o jednorazowej migracji.

Poniższy przykład ukończy cutover\migration. Użytkownicy, wywołaj polecenie według własnego uznania.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Usuwanie wystąpienia usługi Azure Database Migration Service

Po zakończeniu migracji można usunąć wystąpienia usługi Azure Database Migration Service:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Dodatkowe zasoby

Dla informacji na temat dodatkowych scenariuszy migracji (pary źródłowy i docelowy), zobacz Microsoft [Przewodnik po migracji bazy danych](https://datamigration.microsoft.com/).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o usłudze Azure Database Migration Service w artykule [co to jest Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
