---
title: 'PowerShell: Migrowanie wystąpienia zarządzanego programu SQL Server do programu SQL'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzać migrację z lokalnego wystąpienia zarządzanego programu SQL Server do bazy danych SQL azure przy użyciu programu Azure PowerShell i usługi migracji bazy danych platformy Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 9ea9f55681b93e79eec836f5808d2c6feaa6bb29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650728"
---
# <a name="migrate-sql-server-to-sql-database-managed-instance-with-powershell--azure-database-migration-service"></a>Migrowanie wystąpienia zarządzanego programu SQL Server do bazy danych SQL za pomocą usługi migracji usługi PowerShell & Usługi migracji bazy danych azure

W tym artykule należy przeprowadzić migrację bazy danych **Adventureworks2016** przywróconej do lokalnego wystąpienia programu SQL Server 2005 lub wyższej do wystąpienia zarządzanego usługi Azure SQL Database przy użyciu programu Microsoft Azure PowerShell. Bazy danych z lokalnego wystąpienia programu SQL Server można migrować z `Az.DataMigration` lokalnego wystąpienia programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database przy użyciu modułu w programie Microsoft Azure PowerShell.

W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
>
> * Utwórz grupę zasobów.
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji w wystąpieniu usługi migracji bazy danych platformy Azure.
> * Uruchamianie migracji.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ten artykuł zawiera szczegółowe informacje na temat wykonywania migracji w trybie online i offline.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać następujące kroki, potrzebujesz:

* [SQL Server 2016 lub powyżej](https://www.microsoft.com/sql-server/sql-server-downloads) (dowolna wersja).
* Lokalna kopia bazy danych **AdventureWorks2016,** która jest dostępna do pobrania [tutaj](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017).
* Aby włączyć protokół TCP/IP, który jest domyślnie wyłączony z instalacją PROGRAMU SQL Server Express. Włącz protokół TCP/IP, wykonując artykuł [Włącz lub wyłącz protokół sieciowy serwera](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Aby skonfigurować [Zaporę systemu Windows w celu uzyskania dostępu do aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Subskrypcja platformy Azure. Jeśli go nie masz, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Wystąpienie zarządzanej usługi Azure SQL Database. Wystąpienie zarządzanego usługi Azure SQL Database można utworzyć, postępając zgodnie ze szczegółami w artykule [Tworzenie wystąpienia zarządzanego usługi Azure SQL Database.](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)
* Aby pobrać i zainstalować [Asystenta migracji danych](https://www.microsoft.com/download/details.aspx?id=53595) w wersji 3.3 lub nowszej.
* Sieć wirtualna platformy Microsoft Azure utworzona przy użyciu modelu wdrażania usługi Azure Resource Manager, który zapewnia usłudze migracji bazy danych platformy Azure łączność między lokacjami z lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)
* Ukończona ocena lokalnej bazy danych i migracji schematu przy użyciu Asystenta migracji danych, jak opisano w artykule [Wykonywanie oceny migracji programu SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem).
* Aby pobrać i `Az.DataMigration` zainstalować moduł (wersja 0.7.2 lub nowsza) z Galerii programu PowerShell przy użyciu [polecenia cmdlet install-module PowerShell](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1).
* Aby upewnić się, że poświadczenia używane do łączenia się ze źródłowym wystąpieniem programu SQL Server mają uprawnienie [SERWER CONTROL SERVER.](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql)
* Aby upewnić się, że poświadczenia używane do łączenia się z docelowym wystąpieniem zarządzanym usługi Azure SQL Database ma uprawnienie BAZA DANYCH CONTROL w bazach danych zarządzanych docelowej bazy danych wystąpień usługi Azure SQL Database.

    > [!IMPORTANT]
    > W przypadku migracji online musisz już skonfigurować poświadczenia usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz artykuł [Tworzenie aplikacji i jednostki usługi Azure AD, która może uzyskiwać dostęp do zasobów, można utworzyć aplikację portalu](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)za pomocą portalu.

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Logowanie się do subskrypcji platformy Microsoft Azure

Zaloguj się do subskrypcji platformy Azure przy użyciu programu PowerShell. Aby uzyskać więcej informacji, zobacz artykuł [Logowanie się za pomocą programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to kontener logiczny, w którym zasoby platformy Azure są wdrażane i zarządzane.

Utwórz grupę zasobów [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) za pomocą polecenia.

Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w regionie *wschodnie stany USA.*

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Tworzenie wystąpienia usługi migracji bazy danych platformy Azure

Można utworzyć nowe wystąpienie usługi migracji `New-AzDataMigrationService` bazy danych platformy Azure przy użyciu polecenia cmdlet.
To polecenie cmdlet oczekuje następujących wymaganych parametrów:

* *Nazwa grupy zasobów platformy Azure*. Polecenia można [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) użyć do utworzenia grupy zasobów platformy Azure, jak wcześniej pokazano i podać jej nazwę jako parametr.
* *Nazwa usługi*. Ciąg odpowiadający żądanej unikatowej nazwie usługi dla usługi migracji bazy danych Azure.
* *Lokalizacja*. Określa lokalizację usługi. Określ lokalizację centrum danych platformy Azure, taką jak Zachodnie stany USA lub Azja Południowo-Wschodnia.
* *Sku*. Ten parametr odpowiada nazwie Sku DMS. Obecnie obsługiwane nazwy Sku to *Basic_1vCore* *, Basic_2vCores* *, GeneralPurpose_4vCores*.
* *Identyfikator podsieci wirtualnej*. Za pomocą polecenia cmdlet [`New-AzVirtualNetworkSubnetConfig`](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) można utworzyć podsieć.

Poniższy przykład tworzy usługę o nazwie *MyDMS* w grupie zasobów *MyDMSResourceGroup* znajdującej się w regionie *Wschodnie stany USA* przy użyciu sieci wirtualnej o nazwie *MyVNET* i podsieci o nazwie *MySubnet*.

> [!IMPORTANT]
> Poniższy fragment kodu dotyczy migracji w trybie offline, która nie wymaga wystąpienia usługi migracji bazy danych Platformy Azure na podstawie jednostki SKU w usłudze Premium. W przypadku migracji online wartość parametru -Sku musi zawierać jednostkę SKU w ramach premium.

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

Po utworzeniu wystąpienia usługi migracji bazy danych platformy Azure utwórz projekt migracji. Projekt usługi migracji bazy danych Azure wymaga informacji o połączeniu dla wystąpień źródłowych i docelowych, a także listy baz danych, które mają zostać migrowane w ramach projektu.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Tworzenie obiektu Informacje o połączeniu bazy danych dla połączeń źródłowych i docelowych

Obiekt Informacje o połączeniu bazy danych `New-AzDmsConnInfo` można utworzyć przy użyciu polecenia cmdlet, które oczekuje następujących parametrów:

* *ServerType*. Typ żądanego połączenia z bazą danych, na przykład SQL, Oracle lub MySQL. Użyj sql dla sql server i sql sql.
* *Źródło danych*. Nazwa lub adres IP wystąpienia programu SQL Server lub wystąpienia usługi Azure SQL Database.
* *AuthType*. Typ uwierzytelniania dla połączenia, który może być sqlauthentication lub WindowsAuthentication.
* *Certyfikat TrustServerCertificate*. Ten parametr ustawia wartość, która wskazuje, czy kanał jest szyfrowany podczas pomijania przechodzenia łańcucha certyfikatów w celu sprawdzenia zaufania. Wartość może `$true` być `$false`lub .

Poniższy przykład tworzy obiekt Informacje o połączeniu dla źródłowego programu SQL Server o nazwie *MySourceSQLServer* przy użyciu uwierzytelniania sql:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

W następnym przykładzie pokazano tworzenie informacji o połączeniu dla serwera wystąpienia zarządzanego usługi Azure SQL Database o nazwie "targetmanagedinstance.database.windows.net" przy użyciu uwierzytelniania sql:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Dostarczanie baz danych dla projektu migracji

Utwórz listę `AzDataMigrationDatabaseInfo` obiektów, która określa bazy danych w ramach projektu usługi migracji bazy danych Azure Database, które mogą być dostarczane jako parametr do tworzenia projektu. Do utworzenia `AzDataMigrationDatabaseInfo`polecenia `New-AzDataMigrationDatabaseInfo` cmdlet można użyć polecenia cmdlet .

Poniższy przykład `AzDataMigrationDatabaseInfo` tworzy projekt dla **adventureworks2016** bazy danych i dodaje go do listy, które mają być dostarczane jako parametr do tworzenia projektu.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Tworzenie obiektu projektu

Na koniec można utworzyć projekt usługi migracji bazy danych azure o nazwie *MyDMSProject* znajduje się we *wschodnich stanach ZJEDNOCZONYCH* przy użyciu `New-AzDataMigrationProject` i dodać wcześniej utworzone połączenia źródłowe i docelowe oraz listę baz danych do migracji.

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

Następnie utwórz i uruchom zadanie usługi migracji bazy danych platformy Azure. To zadanie wymaga informacji o poświadczeniach połączenia zarówno dla źródła, jak i obiektu docelowego, a także listy tabel bazy danych do migracji i informacji już dostarczonych wraz z projektem utworzonych jako warunek wstępny.

### <a name="create-credential-parameters-for-source-and-target"></a>Tworzenie parametrów poświadczeń dla źródła i obiektu docelowego

Tworzenie poświadczeń zabezpieczeń połączenia jako obiektu [PSCredential.](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0)

Poniższy przykład przedstawia tworzenie *obiektów PSCredential* dla połączeń źródłowych i docelowych, zapewniając hasła jako zmienne ciągu *$sourcePassword* i *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Tworzenie kopii zapasowej obiektu FileShare

Teraz utwórz obiekt FileShare reprezentujący lokalny udział sieciowy SMB, do którego usługa `New-AzDmsFileShare` migracji bazy danych Azure Database może wykonać źródłowe kopie zapasowe bazy danych przy użyciu polecenia cmdlet.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Tworzenie zaznaczonego obiektu bazy danych

Następnym krokiem jest wybranie źródłowych i `New-AzDmsSelectedDB` docelowych baz danych przy użyciu polecenia cmdlet.

Poniższy przykład dotyczy migracji pojedynczej bazy danych z programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Jeśli całe wystąpienie programu SQL Server wymaga lift-and-shift do wystąpienia zarządzanego usługi Azure SQL Database, a następnie pętla do podjęcia wszystkich baz danych ze źródła znajduje się poniżej. W poniższym przykładzie dla $Server, $SourceUserName i $SourcePassword podaj źródłowe szczegóły programu SQL Server.

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

### <a name="sas-uri-for-azure-storage-container"></a>Identyfikator URI sygnatury dostępu Współdzielonego dla kontenera usługi Azure Storage

Utwórz zmienną zawierającą identyfikator URI sygnatury dostępu współdzielonego, który zapewnia usłudze migracji bazy danych Azure dostęp do kontenera konta magazynu, do którego usługa przekazuje pliki kopii zapasowej.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="additional-configuration-requirements"></a>Dodatkowe wymagania konfiguracyjne

Istnieje kilka dodatkowych wymagań, które należy spełnić, ale różnią się one w zależności od tego, czy przeprowadzasz migrację offline, czy online.

#### <a name="offline-migrations"></a>Migracje w trybie offline

Tylko w przypadku migracji w trybie offline wykonaj następujące dodatkowe zadania konfiguracyjne.

* **Wybierz loginy**. Utwórz listę loginów do migracji, jak pokazano w poniższym przykładzie:

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > Obecnie usługa migracji bazy danych Platformy Azure obsługuje tylko migrację logowania SQL.

* **Wybierz zadania agenta**. Utwórz listę zadań agenta, które mają zostać migrowane, jak pokazano w poniższym przykładzie:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > Obecnie usługa migracji bazy danych Azure obsługuje tylko zadania z krokami zadania podsystemu T-SQL.

#### <a name="online-migrations"></a>Migracje online

Tylko w przypadku migracji online wykonaj następujące dodatkowe zadania konfiguracyjne.

* **Konfigurowanie aplikacji usługi Azure Active Directory**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **Konfigurowanie zasobu magazynu**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>Tworzenie i uruchamianie zadania migracji

Użyj `New-AzDataMigrationTask` polecenia cmdlet, aby utworzyć i rozpocząć zadanie migracji.

#### <a name="specify-parameters"></a>Określanie parametrów

##### <a name="common-parameters"></a>Typowe parametry

Niezależnie od tego, czy przeprowadzasz migrację `New-AzDataMigrationTask` w trybie offline, czy online, polecenie cmdlet oczekuje następujących parametrów:

* *Typ zadania*. Typ zadania migracji do utworzenia dla programu SQL Server do usługi Azure SQL Database Managed Instance typu *MigrateSqlServerSqlDbMi jest oczekiwany.* 
* *Nazwa grupy zasobów*. Nazwa grupy zasobów platformy Azure, w której ma być utworzone zadanie.
* *Nazwa serwisu*. Usługa migracji bazy danych platformy Azure wystąpienie, w którym można utworzyć zadanie.
* *ProjectName*. Nazwa projektu usługi migracji bazy danych azure, w którym można utworzyć zadanie. 
* *Nazwa zadania*. Nazwa zadania, które ma zostać utworzone. 
* *ŹródłoZłączenie*. Obiekt AzDmsConnInfo reprezentujący źródłowe połączenie programu SQL Server.
* *TargetConnection*. Obiekt AzDmsConnInfo reprezentujący docelowe połączenie wystąpienia zarządzanego bazy danych SQL platformy Azure.
* *ŹródłoCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) obiektu do łączenia się z serwerem źródłowym.
* *TargetCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) obiektu do łączenia się z serwerem docelowym.
* *SelectedDatabase*. AzDataMigrationSelectedDB obiekt reprezentujący mapowanie źródłowej i docelowej bazy danych.
* *BackupFileShare*. Obiekt FileShare reprezentujący udział sieci lokalnej, do których usługa migracji bazy danych Azure może wykonać źródłowe kopie zapasowe bazy danych.
* *BackupBlobSasUri*. Identyfikator URI sygnatury dostępu współdzielonego, który zapewnia usłudze migracji bazy danych platformy Azure dostęp do kontenera konta magazynu, do którego usługa przekazuje pliki kopii zapasowej. Dowiedz się, jak uzyskać identyfikator URI sygnatury dostępu współdzielonego dla kontenera obiektów blob.
* *SelectedLogins*. Lista wybranych loginów do migracji.
* *SelectedAgentJobs*. Lista wybranych zadań agenta do migracji.

##### <a name="additional-parameters"></a>Dodatkowe parametry

Polecenie `New-AzDataMigrationTask` cmdlet oczekuje również parametrów, które są unikatowe dla typu migracji, w trybie offline lub online, które wykonujesz.

* **Migracje w trybie offline**. W przypadku migracji `New-AzDataMigrationTask` w trybie offline polecenie cmdlet oczekuje również następujących parametrów:

  * *SelectedLogins*. Lista wybranych loginów do migracji.
  * *SelectedAgentJobs*. Lista wybranych zadań agenta do migracji.

* **Migracje online**. W przypadku migracji `New-AzDataMigrationTask` online polecenie cmdlet oczekuje również następujących parametrów.

* *Usługa AzureActiveDirectoryApp*. Aplikacja usługi Active Directory.
* *StorageResourceID*. Identyfikator zasobu konta magazynu.

#### <a name="create-and-start-an-offline-migration-task"></a>Tworzenie i uruchamianie zadania migracji w trybie offline

Poniższy przykład tworzy i uruchamia zadanie migracji w trybie offline o nazwie **myDMSTask:**

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

#### <a name="create-and-start-an-online-migration-task"></a>Tworzenie i uruchamianie zadania migracji online

Poniższy przykład tworzy i uruchamia zadanie migracji online o nazwie **myDMSTask:**

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

Aby monitorować migrację, wykonaj następujące zadania.

1. Skonsoliduj wszystkie szczegóły migracji w zmienną zwaną $CheckTask.

    Aby połączyć szczegóły migracji, takie jak właściwości, informacje o stanie i bazie danych skojarzone z migracją, użyj następującego fragmentu kodu:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Użyj `$CheckTask` zmiennej, aby uzyskać bieżący stan zadania migracji.

    Aby użyć `$CheckTask` zmiennej do uzyskania bieżącego stanu zadania migracji, można monitorować uruchomione zadanie migracji, wykonując kwerendę właściwości stanu zadania, jak pokazano w poniższym przykładzie:

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      Write-Host "migration task running"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      Write-Host "Migration task is completed Successfully"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation" -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      Write-Host "Migration Task Failed"
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>Wykonywanie cutover (tylko migracje online)

W ramach migracji online wykonywana jest pełna kopia zapasowa i przywracanie baz danych, a następnie prace nad przywróceniem dzienników transakcji przechowywanych w udostępnianiu backupfileshare.

Gdy baza danych w wystąpieniu zarządzanym usługi Azure SQL Database jest aktualizowana o najnowsze dane i jest zsynchronizowana ze źródłową bazą danych, można wykonać przecięcie.

Poniższy przykład zakończy cutover\migration. Użytkownicy powołują się na to polecenie według własnego uznania.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Usuwanie wystąpienia usługi migracji bazy danych platformy Azure

Po zakończeniu migracji można usunąć wystąpienie usługi migracji bazy danych Azure:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Zasoby dodatkowe

Aby uzyskać informacje na temat dodatkowych scenariuszy migracji (par źródłowych/docelowych), zobacz [Przewodnik migracji bazy danych](https://datamigration.microsoft.com/)firmy Microsoft .

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usłudze migracji bazy danych platformy Azure w artykule [Co to jest usługa migracji bazy danych platformy Azure?](https://docs.microsoft.com/azure/dms/dms-overview).
