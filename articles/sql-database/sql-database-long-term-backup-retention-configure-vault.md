---
title: Skonfiguruj długoterminowego przechowywania kopii zapasowych — baza danych SQL Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak do przechowywania automatycznych kopii zapasowych w magazynie usług odzyskiwania Azure i przywrócić z magazynu usług odzyskiwania Azure
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 05/08/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: f26f9b6f1b0ddef348c39df5aa4badc5df93b725
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="configure-and-restore-from-azure-sql-database-long-term-backup-retention-using-azure-recovery-services-vault"></a>Konfigurowanie i Przywróć z bazy danych SQL Azure długoterminowego przechowywania kopii zapasowych za pomocą magazynu usług odzyskiwania Azure

Można skonfigurować magazyn usług odzyskiwania Azure do przechowywania kopii zapasowych bazy danych Azure SQL, a następnie odzyskać bazę danych za pomocą kopii zapasowej przechowywane w magazynie przy użyciu portalu Azure lub programu PowerShell.

> [!NOTE]
> W ramach początkowa wersja Preview długoterminowego przechowywania kopii zapasowych w października 2016 kopie zapasowe są przechowywane w magazynie usług odzyskiwania usług Azure. Ta aktualizacja usuwa to zależność, ale dla zgodności z poprzednimi wersjami oryginalnego interfejsu API jest obsługiwane do 31 maja 2018. Jeśli potrzebujesz wchodzić w interakcje z kopii zapasowych w magazynie Azure Services Recovery, zobacz [długoterminowego przechowywania kopii zapasowych za pomocą usługi Azure Recovery Services vault](sql-database-long-term-backup-retention-configure-vault.md). 


## <a name="azure-portal"></a>Azure Portal

Poniższe sekcje pokazują, jak skonfigurować magazyn usług odzyskiwania Azure, wyświetlanie kopii zapasowych w magazynie i przywrócenie z magazynem za pomocą portalu Azure.

### <a name="configure-the-vault-register-the-server-and-select-databases"></a>Konfigurowanie magazynu, zarejestrować serwer i wybierz baz danych

Skonfiguruj Magazyn usług odzyskiwania Azure do [przechowywania automatycznych kopii zapasowych](sql-database-long-term-retention.md) przez okres dłuższy niż okres przechowywania warstwę usług. 

1. Otwórz **programu SQL Server** strony serwera.

   ![Strona serwera SQL](./media/sql-database-get-started-portal/sql-server-blade.png)

2. Kliknij pozycję **Długoterminowe przechowywanie kopii zapasowych**.

   ![link długoterminowego przechowywania kopii zapasowych](./media/sql-database-get-started-backup-recovery/long-term-backup-retention-link.png)

3. Na **długoterminowego przechowywania kopii zapasowych** serwera Przejrzyj i zaakceptuj warunki podglądu (chyba że użytkownik jeszcze - lub ta funkcja nie jest już w wersji zapoznawczej).

   ![akceptowanie warunków wersji zapoznawczej](./media/sql-database-get-started-backup-recovery/accept-the-preview-terms.png)

4. Aby skonfigurować długoterminowego przechowywania kopii zapasowych, wybierz tę bazę danych w siatce, a następnie kliknij przycisk **Konfiguruj** na pasku narzędzi.

   ![wybieranie bazy danych do długoterminowego przechowywania kopii zapasowych](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

5. Na **Konfiguruj** kliknij przycisk **Skonfiguruj wymagane ustawienia** w obszarze **magazyn usług odzyskiwania**.

   ![konfigurowanie linku do magazynu](./media/sql-database-get-started-backup-recovery/configure-vault-link.png)

6. Na **magazyn usług odzyskiwania** wybierz istniejącego magazynu, jeśli istnieje. W przeciwnym razie, jeśli nie można odnaleźć magazynu usługi Recovery Services powiązanego z subskrypcją, kliknij, aby zakończyć przepływ i utworzyć magazyn usługi Recovery Services.

   ![Utwórz łącze do magazynu](./media/sql-database-get-started-backup-recovery/create-new-vault-link.png)

7. Na **Magazyny usług odzyskiwania** kliknij przycisk **Dodaj**.

   ![Dodawanie linku do magazynu](./media/sql-database-get-started-backup-recovery/add-new-vault-link.png)
   
8. Na **magazyn usług odzyskiwania** Podaj prawidłową nazwę magazynu usług odzyskiwania.

   ![nazwa nowego magazynu](./media/sql-database-get-started-backup-recovery/new-vault-name.png)

9. Wybierz subskrypcję i grupę zasobów, a następnie wybierz lokalizację magazynu. Gdy skończysz, kliknij pozycję **Utwórz**.

   ![Tworzenie magazynu](./media/sql-database-get-started-backup-recovery/create-new-vault.png)

   > [!IMPORTANT]
   > Magazyn musi znajdować się w tym samym regionie co serwer logiczny Azure SQL i musi używać tej samej grupy zasobów co serwer logiczny.
   >

10. Po utworzeniu nowego magazynu, należy wykonać czynności niezbędnych do zwrócenia do **magazyn usług odzyskiwania** strony.

11. Na **magazyn usług odzyskiwania** strony, kliknij magazyn, a następnie kliknij przycisk **wybierz**.

   ![wybieranie istniejącego magazynu](./media/sql-database-get-started-backup-recovery/select-existing-vault.png)

12. Na **Konfiguruj** , podaj prawidłową nazwę dla nowej zasady przechowywania, zmodyfikować domyślne zasady przechowywania, zależnie od potrzeb, a następnie kliknij przycisk **OK**.

   ![definiowanie zasad przechowywania](./media/sql-database-get-started-backup-recovery/define-retention-policy.png)
   
   >[!NOTE]
   >Nazwy zasad przechowywania nie zezwalaj na niektórych znaków, łącznie ze spacjami.

13. Na **długoterminowego przechowywania kopii zapasowych** stron dla bazy danych, kliknij przycisk **zapisać** , a następnie kliknij przycisk **OK** do stosowania zasad długoterminowego przechowywania kopii zapasowych do wszystkich wybranych baz danych.

   ![definiowanie zasad przechowywania](./media/sql-database-get-started-backup-recovery/save-retention-policy.png)

14. Kliknij pozycję **Zapisz**, aby włączyć opcję długoterminowego przechowywania kopii zapasowych przy użyciu nowych zasad w skonfigurowanym magazynie usługi Azure Recovery Services.

   ![definiowanie zasad przechowywania](./media/sql-database-get-started-backup-recovery/enable-long-term-retention.png)

> [!IMPORTANT]
> Skonfigurowane kopie zapasowe zostaną wyświetlone w magazynie w ciągu następnych siedmiu dni. Pracę z tym samouczkiem można kontynuować po wyświetleniu kopii zapasowych w magazynie.
>

### <a name="view-backups-in-long-term-retention-using-azure-portal"></a>Wyświetl kopie zapasowe w przechowywania długoterminowego za pomocą portalu Azure

Wyświetl informacje o kopii zapasowych bazy danych w [długoterminowego przechowywania kopii zapasowych](sql-database-long-term-retention.md). 

1. W portalu Azure Otwórz magazyn usług odzyskiwania Azure na kopie zapasowe bazy danych (Przejdź do **wszystkie zasoby** i wybierz ją z listy zasobów dla Twojej subskrypcji) do wyświetlania ilości miejsca używanego przez kopie zapasowe bazy danych w Magazyn.

   ![wyświetlanie magazynu usługi recovery services z kopiami zapasowymi](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault-with-data.png)

2. Otwórz **bazy danych SQL** stron dla bazy danych.

   ![Nowa strona bazy danych przykładowych](./media/sql-database-get-started-portal/new-sample-db-blade.png)

3. Na pasku narzędzi kliknij pozycję **Przywróć**.

   ![pasek narzędzi przywracania](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

4. Na stronie przywracania kliknij **długoterminowej**.

5. W obszarze kopii zapasowych magazynu platformy Azure kliknij pozycję **Wybierz kopię zapasową**, aby wyświetlić dostępne kopie zapasowych bazy danych podlegające długoterminowemu przechowywaniu kopii zapasowych.

   ![kopie zapasowe w magazynie](./media/sql-database-get-started-backup-recovery/view-backups-in-vault.png)

### <a name="restore-a-database-from-a-backup-in-long-term-backup-retention-using-the-azure-portal"></a>Przywracanie bazy danych z kopii zapasowej w długoterminowego przechowywania kopii zapasowych przy użyciu portalu Azure

Możesz przywrócić bazę danych nową bazę danych z kopii zapasowej w magazynie usług odzyskiwania Azure.

1. Na **kopii zapasowych magazynu Azure** kliknij opcję tworzenia kopii zapasowej do przywrócenia, a następnie kliknij przycisk **wybierz**.

   ![wybieranie kopii zapasowej w magazynie](./media/sql-database-get-started-backup-recovery/select-backup-in-vault.png)

2. W polu tekstowym **Nazwa bazy danych** podaj nazwę przywracanej bazy danych.

   ![nazwa nowej nazwy danych](./media/sql-database-get-started-backup-recovery/new-database-name.png)

3. Kliknij przycisk **OK**, aby przywrócić bazę danych z kopii zapasowej w magazynie do nowej bazy danych.

4. Na pasku narzędzi kliknij ikonę powiadomienia, aby wyświetlić stan zadania przywracania.

   ![postęp zadania przywracania z magazynu](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Po zakończeniu zadania przywracania, otwórz **baz danych SQL** strony w celu wyświetlenia nowo przywróconej bazy danych.

   ![baza danych przywrócona z magazynu](./media/sql-database-get-started-backup-recovery/restored-database-from-vault.png)

> [!NOTE]
> W tym miejscu możesz połączyć się z przywróconą bazą danych przy użyciu programu SQL Server Management Studio, aby wykonać niezbędne zadania, takie jak [wyodrębnienie bitu danych z przywróconej bazy danych w celu skopiowania do istniejącej bazy danych lub usunięcie istniejącej bazę danych i zmiana nazwy przywróconej bazy danych na istniejącą nazwę bazy danych](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="powershell"></a>PowerShell

Poniższe sekcje pokazują, jak skonfigurować magazyn usług odzyskiwania Azure, wyświetlanie kopii zapasowych w magazynie i przywracania z magazynu przy użyciu programu PowerShell.

### <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Użyj [AzureRmRecoveryServicesVault nowy](/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault) utworzyć magazyn usług odzyskiwania.

> [!IMPORTANT]
> Magazyn musi znajdować się w tym samym regionie co serwer logiczny Azure SQL i musi używać tej samej grupy zasobów co serwer logiczny.

```PowerShell
# Create a recovery services vault

#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location
$recoveryServiceVaultName = "{new-vault-name}"

$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $ResourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault
```

### <a name="set-your-server-to-use-the-recovery-vault-for-its-long-term-retention-backups"></a>Ustawienia programu server używane w magazynie odzyskiwania dla jego długoterminowego przechowywania kopii zapasowych

Użyj [AzureRmSqlServerBackupLongTermRetentionVault zestaw](/powershell/module/azurerm.sql/set-azurermsqlserverbackuplongtermretentionvault) polecenia cmdlet, aby skojarzyć magazyn usług odzyskiwania wcześniej utworzonej z określonym serwerem Azure SQL.

```PowerShell
# Set your server to use the vault to for long-term backup retention 

Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id
```

### <a name="create-a-retention-policy"></a>Tworzenie zasad przechowywania

Zasady przechowywania określają czas przechowywania kopii zapasowej bazy danych. Użyj [Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupretentionpolicyobject) polecenia cmdlet, aby uzyskać domyślne zasady przechowywania do użycia jako szablon do tworzenia zasad. W tym szablonie okres przechowywania ustawiono przez 2 lata. Następnie uruchom [AzureRmRecoveryServicesBackupProtectionPolicy nowy](/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy) na koniec Utwórz zasady. 

> [!NOTE]
> Niektóre polecenia cmdlet wymaga kontekstu magazynem przed uruchomieniem ([AzureRmRecoveryServicesVaultContext zestaw](/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)), zobacz tego polecenia cmdlet w kilku pokrewnych fragmentów. Ustaw kontekst, ponieważ zasady jest częścią magazynu. Można utworzyć wiele zasad przechowywania dla każdego magazynu, a następnie zastosować żądane zasady do określonych baz danych. 


```PowerShell
# Retrieve the default retention policy for the AzureSQLDatabase workload type
$retentionPolicy = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase

# Set the retention value to two years (you can set to any time between 1 week and 10 years)
$retentionPolicy.RetentionDurationType = "Years"
$retentionPolicy.RetentionCount = 2
$retentionPolicyName = "my2YearRetentionPolicy"

# Set the vault context to the vault you are creating the policy for
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Create the new policy
$policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name $retentionPolicyName -WorkloadType AzureSQLDatabase -retentionPolicy $retentionPolicy
$policy
```

### <a name="configure-a-database-to-use-the-previously-defined-retention-policy"></a>Konfigurowanie bazy danych do korzystania z wcześniej zdefiniowanej zasady przechowywania

Użyj [AzureRmSqlDatabaseBackupLongTermRetentionPolicy zestaw](/powershell/module/azurerm.sql/set-azurermsqldatabasebackuplongtermretentionpolicy) polecenia cmdlet, aby zastosować nowe zasady do określonej bazy danych.

```PowerShell
# Enable long-term retention for a specific SQL database
$policyState = "enabled"
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id
```

### <a name="view-backup-info-and-backups-in-long-term-retention"></a>Wyświetlanie informacji o kopiach zapasowych i kopii zapasowych podlegających długoterminowemu przechowywaniu

Wyświetl informacje o kopii zapasowych bazy danych w [długoterminowego przechowywania kopii zapasowych](sql-database-long-term-retention.md). 

Aby wyświetlić informacje o kopii zapasowej, użyj następujących poleceń cmdlet:

- [Get-AzureRmRecoveryServicesBackupContainer](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)
- [Get-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)
- [Get-AzureRmRecoveryServicesBackupRecoveryPoint](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)

```PowerShell
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$databaseNeedingRestore = $databaseName

# Set the vault context to the vault we want to restore from
#$vault = Get-AzureRmRecoveryServicesVault -ResourceGroupName $resourceGroupName
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# the following commands find the container associated with the server 'myserver' under resource group 'myresourcegroup'
$container = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with a specific database
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name $databaseNeedingRestore

# Get all available backups for the previously indicated database
# Optionally, set the -StartDate and -EndDate parameters to return backups within a specific time period
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups
```

### <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>Przywracanie bazy danych z kopii zapasowej podlegającej długoterminowemu przechowywaniu kopii zapasowych

Przywracanie z długoterminowego przechowywania kopii zapasowych używa [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) polecenia cmdlet.

```PowerShell
# Restore the most recent backup: $availableBackups[0]
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$restoredDatabaseName = "{new-database-name}"
$edition = "Basic"
$performanceLevel = "Basic"

$restoredDb = Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $availableBackups[0].Id -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $restoredDatabaseName -Edition $edition -ServiceObjectiveName $performanceLevel
$restoredDb
```


> [!NOTE]
> W tym miejscu możesz nawiązać przywróconej bazy danych przy użyciu programu SQL Server Management Studio do wykonywania wymaganych zadań, np. wyodrębnienie bit danych z przywróconej bazy danych ma zostać skopiowany do istniejącej bazy danych lub usuń istniejącą bazę danych i zmienić nazwę przywróconej Baza danych do nazwy istniejącej bazy danych. Zobacz [punktu w czasie przywracania](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o automatycznych kopiach zapasowych generowanych przez usługi, zobacz artykuł dotyczący [automatycznych kopii zapasowych](sql-database-automated-backups.md)
- Aby dowiedzieć się więcej o długoterminowym przechowywaniu kopii zapasowych, zobacz temat dotyczący [długoterminowego przechowywania kopii zapasowych](sql-database-long-term-retention.md)
- Aby dowiedzieć się więcej o przywracaniu z kopii zapasowych, zobacz temat dotyczący [przywracania z kopii zapasowej](sql-database-recovery-using-backups.md)
