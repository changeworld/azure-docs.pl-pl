---
title: Konfigurowanie długoterminowego przechowywania kopii zapasowych — usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak do przechowywania automatycznych kopii zapasowych w magazynie usługi Azure Recovery Services i przywracania z magazynu usługi Azure Recovery Services
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma,carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: efdcc4c8424fc1c330ac901bb42ec89897241d79
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316612"
---
# <a name="configure-long-term-backup-retention-using-azure-recovery-services-vault"></a>Konfigurowanie długoterminowego przechowywania kopii zapasowych przy użyciu magazynu usługi Azure Recovery Services

Można skonfigurować magazyn usługi Azure Recovery Services, aby przechowywać kopie zapasowe bazy danych Azure SQL, a następnie Odzyskaj bazę danych przy użyciu kopii zapasowych przechowywane w magazynie przy użyciu witryny Azure portal lub programu PowerShell.

> [!NOTE]
> W ramach wstępną wersję zapoznawczą długoterminowego przechowywania kopii zapasowych w październiku 2016 r kopie zapasowe są przechowywane w magazynie usług odzyskiwania Azure Services. Ta aktualizacja usuwa tę zależność, ale zgodności z poprzednimi wersjami oryginalny interfejs API jest obsługiwane do 31 maja 2018 r. Jeśli zajdzie potrzeba interakcji z kopii zapasowych w magazynie usługi Azure Recovery Services, zobacz [długoterminowego przechowywania kopii zapasowych przy użyciu magazynu usług odzyskiwania Azure Services](sql-database-long-term-backup-retention-configure-vault.md).

## <a name="azure-portal"></a>Azure Portal

Poniższe sekcje pokazują, jak skonfigurować magazyn usługi Azure Recovery Services, wyświetlanie kopii zapasowych w magazynie i przywracania z magazynu za pomocą witryny Azure portal.

### <a name="configure-the-vault-register-the-server-and-select-databases"></a>Konfigurowanie magazynu, zarejestrować serwer, a następnie wybierz bazy danych

Skonfiguruj magazyn usługi Azure Recovery Services do [przechowywania automatycznych kopii zapasowych](sql-database-long-term-retention.md) przez okres dłuższy niż okres przechowywania w warstwie usługi.

1. Otwórz **programu SQL Server** strony dla serwera.

   ![Strona serwera SQL](./media/sql-database-get-started-portal/sql-server-blade.png)

2. Kliknij pozycję **Długoterminowe przechowywanie kopii zapasowych**.

   ![link długoterminowego przechowywania kopii zapasowych](./media/sql-database-get-started-backup-recovery/long-term-backup-retention-link.png)

3. Na **długoterminowego przechowywania kopii zapasowych** strony dla serwera, przejrzyj i zaakceptuj warunki dotyczące wersji zapoznawczej (chyba że ma już zrobione — lub ta funkcja nie jest już dostępna w wersji zapoznawczej).

   ![akceptowanie warunków wersji zapoznawczej](./media/sql-database-get-started-backup-recovery/accept-the-preview-terms.png)

4. Aby skonfigurować długoterminowe przechowywanie kopii zapasowych, wybierz tę bazę danych w siatce, a następnie kliknij przycisk **Konfiguruj** na pasku narzędzi.

   ![wybieranie bazy danych do długoterminowego przechowywania kopii zapasowych](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

5. Na **Konfiguruj** kliknij **Skonfiguruj wymagane ustawienia** w obszarze **magazyn usługi odzyskiwania**.

   ![konfigurowanie linku do magazynu](./media/sql-database-get-started-backup-recovery/configure-vault-link.png)

6. Na **magazyn usługi Recovery services** wybierz istniejący magazyn, jeśli istnieje. W przeciwnym razie, jeśli nie można odnaleźć magazynu usługi Recovery Services powiązanego z subskrypcją, kliknij, aby zakończyć przepływ i utworzyć magazyn usługi Recovery Services.

   ![Tworzenie linku do magazynu](./media/sql-database-get-started-backup-recovery/create-new-vault-link.png)

7. Na **Magazyny usługi Recovery Services** kliknij **Dodaj**.

   ![Dodawanie linku do magazynu](./media/sql-database-get-started-backup-recovery/add-new-vault-link.png)

8. Na **magazyn usługi Recovery Services** Podaj prawidłową nazwę magazynu usługi Recovery Services.

   ![nazwa nowego magazynu](./media/sql-database-get-started-backup-recovery/new-vault-name.png)

9. Wybierz subskrypcję i grupę zasobów, a następnie wybierz lokalizację magazynu. Gdy skończysz, kliknij pozycję **Utwórz**.

   ![Tworzenie magazynu](./media/sql-database-get-started-backup-recovery/create-new-vault.png)

   > [!IMPORTANT]
   > Magazyn musi znajdować się w tym samym regionie co serwer bazy danych SQL i musi używać tej samej grupie zasobów co serwer bazy danych SQL.

10. Po utworzeniu nowego magazynu wykonaj niezbędne kroki, aby powrócić do **magazyn usługi Recovery services** strony.

11. Na **magazyn usługi Recovery services** stronie, kliknij magazyn, a następnie kliknij przycisk **wybierz**.

   ![wybieranie istniejącego magazynu](./media/sql-database-get-started-backup-recovery/select-existing-vault.png)

12. Na **Konfiguruj** strony, podaj prawidłową nazwę nowych zasad przechowywania, zmodyfikuj domyślne zasady przechowywania, zgodnie z potrzebami, a następnie kliknij przycisk **OK**.

   ![definiowanie zasad przechowywania](./media/sql-database-get-started-backup-recovery/define-retention-policy.png)

   > [!NOTE]
   > Nazwy zasad przechowywania nie zezwalaj na niektórych znaków, łącznie ze spacjami.

13. Na **długoterminowego przechowywania kopii zapasowych** stronie bazy danych, kliknij przycisk **Zapisz** a następnie kliknij przycisk **OK** Aby zastosować zasady długoterminowego przechowywania kopii zapasowych do wszystkich wybranych baz danych.

   ![definiowanie zasad przechowywania](./media/sql-database-get-started-backup-recovery/save-retention-policy.png)

14. Kliknij pozycję **Zapisz**, aby włączyć opcję długoterminowego przechowywania kopii zapasowych przy użyciu nowych zasad w skonfigurowanym magazynie usługi Azure Recovery Services.

   ![definiowanie zasad przechowywania](./media/sql-database-get-started-backup-recovery/enable-long-term-retention.png)

> [!IMPORTANT]
> Skonfigurowane kopie zapasowe zostaną wyświetlone w magazynie w ciągu następnych siedmiu dni. Pracę z tym samouczkiem można kontynuować po wyświetleniu kopii zapasowych w magazynie.

### <a name="view-backups-in-long-term-retention-using-azure-portal"></a>Wyświetlanie kopii zapasowych podlegających długoterminowemu przechowywaniu przy użyciu witryny Azure portal

Wyświetlanie informacji o kopie zapasowe bazy danych w [długoterminowego przechowywania kopii zapasowych](sql-database-long-term-retention.md).

1. W witrynie Azure portal Otwórz magazynu usług odzyskiwania Azure na kopie zapasowe bazy danych (Przejdź do **wszystkie zasoby** i wybierz ją z listy zasobów dla subskrypcji) aby wyświetlić ilość miejsca używanego przez kopie zapasowe bazy danych w Magazyn.

   ![wyświetlanie magazynu usługi recovery services z kopiami zapasowymi](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault-with-data.png)

2. Otwórz **bazy danych SQL** strona bazy danych.

   ![Nowa strona bazy danych przykładowych](./media/sql-database-get-started-portal/new-sample-db-blade.png)

3. Na pasku narzędzi kliknij pozycję **Przywróć**.

   ![pasek narzędzi przywracania](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

4. Na stronie przywracania kliknij **długoterminowe**.

5. W obszarze kopii zapasowych magazynu platformy Azure kliknij pozycję **Wybierz kopię zapasową**, aby wyświetlić dostępne kopie zapasowych bazy danych podlegające długoterminowemu przechowywaniu kopii zapasowych.

   ![kopie zapasowe w magazynie](./media/sql-database-get-started-backup-recovery/view-backups-in-vault.png)

### <a name="restore-a-database-from-a-backup-in-long-term-backup-retention-using-the-azure-portal"></a>Przywracanie bazy danych z kopii zapasowych podlegających długoterminowemu przechowywaniu kopii zapasowych przy użyciu witryny Azure portal

Przywróceniu bazy danych do nowej bazy danych z kopii zapasowej w magazynie usługi Azure Recovery Services.

1. Na **kopii zapasowych w magazynie Azure** kliknij kopię zapasową do przywrócenia, a następnie kliknij przycisk **wybierz**.

   ![wybieranie kopii zapasowej w magazynie](./media/sql-database-get-started-backup-recovery/select-backup-in-vault.png)

2. W polu tekstowym **Nazwa bazy danych** podaj nazwę przywracanej bazy danych.

   ![nazwa nowej nazwy danych](./media/sql-database-get-started-backup-recovery/new-database-name.png)

3. Kliknij przycisk **OK**, aby przywrócić bazę danych z kopii zapasowej w magazynie do nowej bazy danych.

4. Na pasku narzędzi kliknij ikonę powiadomienia, aby wyświetlić stan zadania przywracania.

   ![postęp zadania przywracania z magazynu](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Po zakończeniu zadania przywracania Otwórz **baz danych SQL** strony, aby wyświetlić nowo przywróconą bazę danych.

   ![baza danych przywrócona z magazynu](./media/sql-database-get-started-backup-recovery/restored-database-from-vault.png)

> [!NOTE]
> W tym miejscu możesz połączyć się z przywróconą bazą danych przy użyciu programu SQL Server Management Studio, aby wykonać niezbędne zadania, takie jak [wyodrębnienie bitu danych z przywróconej bazy danych w celu skopiowania do istniejącej bazy danych lub usunięcie istniejącej bazę danych i zmiana nazwy przywróconej bazy danych na istniejącą nazwę bazy danych](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Poniższe sekcje pokazują, jak skonfigurować magazyn usługi Azure Recovery Services, wyświetlanie kopii zapasowych w magazynie i przywracania z magazynu przy użyciu programu PowerShell.

### <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Użyj [New AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) do utworzenia magazynu usługi recovery services.

> [!IMPORTANT]
> Magazyn musi znajdować się w tym samym regionie co serwer bazy danych SQL i musi używać tej samej grupie zasobów co serwer bazy danych SQL.

```PowerShell
# Create a recovery services vault

#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$serverLocation = (Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location
$recoveryServiceVaultName = "{new-vault-name}"

$vault = New-AzRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $ResourceGroupName -Location $serverLocation
Set-AzRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault
```

### <a name="set-your-server-to-use-the-recovery-vault-for-its-long-term-retention-backups"></a>Skonfiguruj serwer na potrzeby magazynu usługi recovery jego długoterminowego przechowywania kopii zapasowych

Użyj [AzSqlServerBackupLongTermRetentionVault zestaw](/powershell/module/az.sql/set-azsqlserverbackuplongtermretentionvault) polecenia cmdlet, aby skojarzyć magazyn usług odzyskiwania utworzone wcześniej z określonym serwerem Azure SQL.

```PowerShell
# Set your server to use the vault to for long-term backup retention

Set-AzSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id
```

### <a name="create-a-retention-policy"></a>Tworzenie zasad przechowywania

Zasady przechowywania określają czas przechowywania kopii zapasowej bazy danych. Użyj [Get AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-Azrecoveryservicesbackupretentionpolicyobject) polecenia cmdlet, aby uzyskać domyślne zasady przechowywania do użycia jako szablon do tworzenia zasad. W tym szablonie okres przechowywania ustawiono przez 2 lata. Następnie uruchom [New AzRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/new-Azrecoveryservicesbackupprotectionpolicy) Aby utworzyć zasady.

> [!NOTE]
> Niektóre polecenia cmdlet wymagają, aby ustawić kontekst magazynu, przed uruchomieniem ([AzRecoveryServicesVaultContext zestaw](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext)), to polecenie cmdlet w kilku powiązanych fragmentach kodu. Ustaw kontekst, ponieważ zasady są częścią magazynu. Można utworzyć wiele zasad przechowywania dla każdego magazynu, a następnie zastosować żądane zasady do określonych baz danych.

```PowerShell
# Retrieve the default retention policy for the AzureSQLDatabase workload type
$retentionPolicy = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase

# Set the retention value to two years (you can set to any time between 1 week and 10 years)
$retentionPolicy.RetentionDurationType = "Years"
$retentionPolicy.RetentionCount = 2
$retentionPolicyName = "my2YearRetentionPolicy"

# Set the vault context to the vault you are creating the policy for
Set-AzRecoveryServicesVaultContext -Vault $vault

# Create the new policy
$policy = New-AzRecoveryServicesBackupProtectionPolicy -name $retentionPolicyName -WorkloadType AzureSQLDatabase -retentionPolicy $retentionPolicy
$policy
```

### <a name="configure-a-database-to-use-the-previously-defined-retention-policy"></a>Konfigurowanie bazy danych do korzystania z wcześniej zdefiniowanej zasady przechowywania

Użyj [AzSqlDatabaseBackupLongTermRetentionPolicy zestaw](/powershell/module/az.sql/set-azsqldatabasebackuplongtermretentionpolicy) polecenia cmdlet, aby zastosować nowe zasady z określoną bazą danych.

```PowerShell
# Enable long-term retention for a specific SQL database
$policyState = "enabled"
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id
```

### <a name="view-backup-info-and-backups-in-long-term-retention"></a>Wyświetlanie informacji o kopiach zapasowych i kopii zapasowych podlegających długoterminowemu przechowywaniu

Wyświetlanie informacji o kopie zapasowe bazy danych w [długoterminowego przechowywania kopii zapasowych](sql-database-long-term-retention.md).

Aby wyświetlić informacje o kopii zapasowej, użyj następujących poleceń cmdlet:

- [Get-AzRecoveryServicesBackupContainer](/powershell/module/azurerm.recoveryservices.backup/get-Azrecoveryservicesbackupcontainer)
- [Get-AzRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/get-Azrecoveryservicesbackupitem)
- [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/azurerm.recoveryservices.backup/get-Azrecoveryservicesbackuprecoverypoint)

```PowerShell
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$databaseNeedingRestore = $databaseName

# Set the vault context to the vault we want to restore from
#$vault = Get-AzRecoveryServicesVault -ResourceGroupName $resourceGroupName
Set-AzRecoveryServicesVaultContext -Vault $vault

# the following commands find the container associated with the server 'myserver' under resource group 'myresourcegroup'
$container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with a specific database
$item = Get-AzRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name $databaseNeedingRestore

# Get all available backups for the previously indicated database
# Optionally, set the -StartDate and -EndDate parameters to return backups within a specific time period
$availableBackups = Get-AzRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups
```

### <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>Przywracanie bazy danych z kopii zapasowej podlegającej długoterminowemu przechowywaniu kopii zapasowych

Przywracania z długoterminowym przechowywaniem kopii zapasowych używa [AzSqlDatabase przywracania](/powershell/module/az.sql/restore-azsqldatabase) polecenia cmdlet.

```PowerShell
# Restore the most recent backup: $availableBackups[0]
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$restoredDatabaseName = "{new-database-name}"
$edition = "Basic"
$performanceLevel = "Basic"

$restoredDb = Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $availableBackups[0].Id -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $restoredDatabaseName -Edition $edition -ServiceObjectiveName $performanceLevel
$restoredDb
```

> [!NOTE]
> W tym miejscu możesz nawiązać przywróconej bazy danych przy użyciu programu SQL Server Management Studio, aby wykonać niezbędne zadania, np. w przypadku wyodrębnienie bitu danych z przywróconej bazy danych do skopiowania do istniejącej bazy danych lub usuń istniejącą bazę danych i zmiana nazwy przywróconej bazy danych do istniejącej nazwy bazy danych. Zobacz [punktu w czasie przywracania](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="how-to-cleanup-backups-in-recovery-services-vault"></a>Jak oczyszczania tworzenia kopii zapasowych w magazynie usługi Recovery Services

Począwszy od 1 lipca 2018 interfejsu API w wersji 1 od lewej do prawej jest przestarzała i wszystkie istniejące kopie zapasowe w magazynach usługi Recovery zostały zmigrowane do kontenerów magazynów od lewej do prawej, które są zarządzane przez usługę SQL Database. Aby upewnić się, że już nie jest naliczana za oryginalnej kopii zapasowych, ich zostały usunięte z magazynów po migracji. Jednak jeśli blokady są umieszczane w magazynie usługi tworzenia kopii zapasowych będą nadal istnieje. Aby uniknąć niepotrzebnych opłat, można ręcznie usunąć stare kopie zapasowe z magazynu usługi Recovery za pomocą poniższego skryptu.

```PowerShell
<#
.EXAMPLE
    .\Drop-LtrV1Backup.ps1 -SubscriptionId “{vault_sub_id}” -ResourceGroup “{vault_resource_group}” -VaultName “{vault_name}”
#>
[CmdletBinding()]
Param (
    [Parameter(Mandatory = $true, HelpMessage="The vault subscription ID")]
    $SubscriptionId,

    [Parameter(Mandatory = $true, HelpMessage="The vault resource group name")]
    $ResourceGroup,

    [Parameter(Mandatory = $true, HelpMessage="The vault name")]
    $VaultName
)

Login-AzAccount

Select-AzSubscription -SubscriptionId $SubscriptionId

$vaults = Get-AzRecoveryServicesVault
$vault = $vaults | where { $_.Name -eq $VaultName }

Set-AzRecoveryServicesVaultContext -Vault $vault

$containers = Get-AzRecoveryServicesBackupContainer -ContainerType AzureSQL

ForEach ($container in $containers)
{
   $canDeleteContainer = $true
   $ItemCount = 0
   Write-Host "Working on container" $container.Name
   $items = Get-AzRecoveryServicesBackupItem -container $container -WorkloadType AzureSQLDatabase
   ForEach ($item in $items)
   {
    write-host "Deleting item" $item.name
    Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints -item $item -Force
   }

   Write-Host "Deleting container" $container.Name
   Unregister-AzRecoveryServicesBackupContainer -Container $container
}
```

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o automatycznych kopiach zapasowych generowanych przez usługi, zobacz artykuł dotyczący [automatycznych kopii zapasowych](sql-database-automated-backups.md)
- Aby dowiedzieć się więcej o długoterminowym przechowywaniu kopii zapasowych, zobacz temat dotyczący [długoterminowego przechowywania kopii zapasowych](sql-database-long-term-retention.md)
- Aby dowiedzieć się więcej o przywracaniu z kopii zapasowych, zobacz temat dotyczący [przywracania z kopii zapasowej](sql-database-recovery-using-backups.md)
