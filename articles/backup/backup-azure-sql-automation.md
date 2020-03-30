---
title: Baza danych SQL w kopii zapasowej maszyny Wirtualnej platformy Azure & przywracania za pomocą programu PowerShell
description: Tworzenie kopii zapasowych i przywracanie baz danych SQL na maszynach wirtualnych platformy Azure przy użyciu usługi Azure Backup i programu PowerShell.
ms.topic: conceptual
ms.date: 03/15/2019
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 9608b02869b1d41d901ec77a42cfaa6d882040e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131825"
---
# <a name="back-up-and-restore-sql-databases-in-azure-vms-with-powershell"></a>Tworzenie kopii zapasowych i przywracanie baz danych SQL na maszynach wirtualnych platformy Azure za pomocą programu PowerShell

W tym artykule opisano sposób używania programu Azure PowerShell do tworzenia kopii zapasowych i odzyskiwania bazy danych SQL w ramach maszyny Wirtualnej platformy Azure przy użyciu magazynu usług [odzyskiwania kopii zapasowych platformy Azure.](backup-overview.md)

W tym artykule wyjaśniono, jak:

> [!div class="checklist"]
>
> * Skonfiguruj program PowerShell i zarejestruj dostawcę usług azure recovery.
> * Utwórz magazyn usługi Recovery Services.
> * Konfigurowanie kopii zapasowej dla bazy danych SQL w ramach maszyny Wirtualnej platformy Azure.
> * Uruchamianie zadania tworzenia kopii zapasowej.
> * Przywracanie kopii zapasowej bazy danych SQL.
> * Monitoruj zadania tworzenia kopii zapasowych i przywracania.

## <a name="before-you-start"></a>Przed rozpoczęciem

* [Dowiedz się więcej](backup-azure-recovery-services-vault-overview.md) o magazynach usług odzyskiwania.
* Przeczytaj o funkcjach tworzenia [kopii zapasowych db SQL w maszynach wirtualnych platformy Azure.](backup-azure-sql-database.md#before-you-start)
* Przejrzyj hierarchię obiektów programu PowerShell dla usług odzyskiwania.

### <a name="recovery-services-object-hierarchy"></a>Hierarchia obiektów usług odzyskiwania

Hierarchia obiektów jest podsumowana na poniższym diagramie.

![Hierarchia obiektów usług odzyskiwania](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Review the **Az.RecoveryServices** [cmdlet reference](/powershell/module/az.recoveryservices) reference in the Azure library.

### <a name="set-up-and-install"></a>Konfigurowanie i instalowanie

Skonfiguruj program PowerShell w następujący sposób:

1. [Pobierz najnowszą wersję programu Az PowerShell](/powershell/azure/install-az-ps). Minimalna wymagana wersja to 1.5.0.

2. Znajdź polecenia cmdlet programu Azure Backup PowerShell za pomocą tego polecenia:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Przejrzyj aliasy i polecenia cmdlet dla usługi Azure Backup i magazynu usług odzyskiwania. Oto przykład tego, co możesz zobaczyć. Nie jest to pełna lista poleceń cmdlet.

    ![Lista poleceń cmdlet usług odzyskiwania](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Zaloguj się do konta platformy Azure za pomocą **connect-azaccount**.
5. Na wyświetlonej stronie sieci Web zostanie wyświetlony monit o wprowadzenie poświadczeń konta.

    * Alternatywnie można uwzględnić poświadczenia konta jako parametr w **połącz-azkonta** z **poleceniem -Credential**.
    * Jeśli jesteś partnerem CSP pracującym dla dzierżawcy, określ klienta jako dzierżawcę, używając ich identyfikatora dzierżawcy lub nazwy domeny podstawowej dzierżawcy. Przykładem jest **Connect-AzAccount -Tenant** fabrikam.com.

6. Skojarz subskrypcję, której chcesz użyć z kontem, ponieważ konto może mieć kilka subskrypcji.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Jeśli używasz usługi Azure Backup po raz pierwszy, użyj polecenia cmdlet **Register-AzResourceProvider,** aby zarejestrować dostawcę usług Azure Recovery Services w ramach subskrypcji.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Sprawdź, czy dostawcy zarejestrowali się pomyślnie:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. W danych wyjściowych polecenia sprawdź, czy **stan rejestracji** zmienia się na **Zarejestrowany**. Jeśli tak nie jest, uruchom ponownie polecenie cmdlet **Register-AzResourceProvider.**

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Wykonaj następujące kroki, aby utworzyć magazyn usług odzyskiwania.

Magazyn usług odzyskiwania jest zasobem Menedżera zasobów, więc należy umieścić go w grupie zasobów. Można użyć istniejącej grupy zasobów lub utworzyć grupę zasobów za pomocą polecenia cmdlet **New-AzResourceGroup.** Podczas tworzenia grupy zasobów określ nazwę i lokalizację grupy zasobów.

1. Przechowalnia jest umieszczana w grupie zasobów. Jeśli nie masz istniejącej grupy zasobów, utwórz nową grupę za pomocą [grupy New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). W tym przykładzie tworzymy nową grupę zasobów w regionie Zachodnie stany USA.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Użyj polecenia cmdlet [New-AzRecoveryServicesVault,](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) aby utworzyć przechowalnię. Określ tę samą lokalizację dla przechowalni, która została użyta dla grupy zasobów.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Określ typ nadmiarowości, który ma być używany w magazynie magazynu.

    * Można użyć [magazynu lokalnie nadmiarowego](../storage/common/storage-redundancy-lrs.md) lub [magazynu geograficznie nadmiarowego](../storage/common/storage-redundancy-grs.md).
    * W poniższym przykładzie ustawia opcję **-BackupStorageRedundancy** dla[polecenia Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd dla **testvault** ustawionego na **GeoRedundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Wyświetlanie magazynów w ramach subskrypcji

Aby wyświetlić wszystkie magazyny w ramach subskrypcji, należy użyć funkcji [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

Dane wyjściowe są podobne do następujących. Podana jest skojarzona grupa zasobów i lokalizacja.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Ustawianie kontekstu przechowalni

Przechowuj obiekt przechowalni w zmiennej i ustawia kontekst przechowalni.

* Wiele poleceń cmdlet usługi Azure Backup wymaga obiektu magazynu usług odzyskiwania jako danych wejściowych, dzięki czemu wygodnie jest przechowywać obiekt przechowalni w zmiennej.
* Kontekst magazynu to typ danych chronionych w magazynie. Ustaw go za pomocą [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Po ustawieniu kontekstu stosuje się do wszystkich kolejnych poleceń cmdlet.

W poniższym przykładzie ustawia kontekst przechowalni dla **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Pobieranie identyfikatora przechowalni

Planujemy przestarzałe ustawienie kontekstu magazynu zgodnie z wytycznymi programu Azure PowerShell. Zamiast tego można przechowywać lub pobierać identyfikator przechowalni i przekazać go do odpowiednich poleceń, w następujący sposób:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Konfigurowanie zasad kopii zapasowych

Zasady tworzenia kopii zapasowych określają harmonogram tworzenia kopii zapasowych i czas, przez jaki powinny być przechowywane punkty odzyskiwania kopii zapasowej:

* Zasady tworzenia kopii zapasowych są skojarzone z co najmniej jedną zasadą przechowywania. Zasady przechowywania określa, jak długo punkt odzyskiwania jest przechowywany przed jego usunięciem.
* Wyświetlanie domyślnego przechowywania zasad kopii zapasowej przy użyciu [funkcji Get-AzRecoveryServiceBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Wyświetlanie domyślnego harmonogramu zasad tworzenia kopii zapasowych przy użyciu [programu Get-AzRecoveryServiceSChedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Polecenie cmdlet [New-AzRecoveryServicesBackupProtectionPolicy do](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) utworzenia nowej zasady tworzenia kopii zapasowych. Wprowadź harmonogram i przechowywania obiektów zasad.

Domyślnie godzina rozpoczęcia jest zdefiniowana w obiekcie zasad harmonogramu. Użyj poniższego przykładu, aby zmienić czas rozpoczęcia na żądany czas rozpoczęcia. Żądany czas rozpoczęcia powinien być również w czasie UTC. Poniższy przykład zakłada, że żądana godzina rozpoczęcia to 01:00 AM UTC dla codziennych kopii zapasowych.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Musisz podać czas rozpoczęcia tylko w 30 minut wielokrotności. W powyższym przykładzie może to być tylko "01:00:00" lub "02:30:00". Czas rozpoczęcia nie może być "01:15:00"

W poniższym przykładzie przechowuje zasady harmonogramu i zasady przechowywania w zmiennych. Następnie używa tych zmiennych jako parametrów dla nowej polityki (**NewSQLPolicy**). **NewSQLPolicy** wykonuje codzienną kopię zapasową "Full", zachowuje ją przez 180 dni i wykonuje kopię zapasową dziennika co 2 godziny

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Dane wyjściowe są podobne do następujących.

```output
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 01:30:00 AM      Daily                                    False                True
```

## <a name="enable-backup"></a>Włączanie tworzenia kopii zapasowych

### <a name="registering-the-sql-vm"></a>Rejestrowanie maszyny Wirtualnej SQL

W przypadku kopii zapasowych maszyn wirtualnych platformy Azure i udziałów plików azure usługa Kopia zapasowa może łączyć się z tymi zasobami usługi Azure Resource Manager i pobierać odpowiednie szczegóły. Ponieważ SQL jest aplikacją w ramach maszyny Wirtualnej platformy Azure, usługa kopia zapasowa potrzebuje uprawnień dostępu do aplikacji i pobrać niezbędne szczegóły. Aby to zrobić, należy *"zarejestrować"* maszyny Wirtualnej platformy Azure, która zawiera aplikację SQL z magazynu usług odzyskiwania. Po zarejestrowaniu maszyny Wirtualnej SQL w magazynie można chronić rejestratory SQL tylko do tego magazynu. Użyj polecenia cmdlet [Register-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS, aby zarejestrować maszynę wirtualną.

```powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

Polecenie zwróci "kontener kopii zapasowej" tego zasobu, a stan zostanie "zarejestrowany"

> [!NOTE]
> Jeśli parametr force nie zostanie podany, użytkownik zostanie poproszony o potwierdzenie za pomocą tekstu "Czy chcesz wyłączyć ochronę dla tego kontenera". Zignoruj ten tekst i powiedz "Y", aby potwierdzić. Jest to znany problem i pracujemy nad usunięciem tekstu i wymogu parametru force.

### <a name="fetching-sql-dbs"></a>Pobieranie bloków DBs SQL

Po zakończeniu rejestracji usługa kopia zapasowa będzie mogła wyświetlić listę wszystkich dostępnych składników SQL w ramach maszyny Wirtualnej. Aby wyświetlić wszystkie składniki SQL, których kopia zapasowa nie została utworzona w tym magazynie, użyj polecenia cmdlet [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS

```powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
```

Dane wyjściowe pokażą wszystkie niechronione składniki SQL we wszystkich maszynach wirtualnych SQL zarejestrowanych w tym magazynie z typem elementu i serwerem. Można dodatkowo filtrować do określonej maszyny Wirtualnej SQL, przekazując parametr '-Container' lub użyć kombinacji "Name" i "ServerName" wraz z flagą ItemType, aby uzyskać unikatowy element SQL.

```powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
```

### <a name="configuring-backup"></a>Konfigurowanie kopii zapasowej

Teraz, gdy mamy wymagane SQL DB i zasady, z którymi musi być kopią zapasową, możemy użyć [enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) cmdlet skonfigurować kopię zapasową dla tego bazy danych SQL.

```output
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
```

Polecenie czeka, aż kopia zapasowa konfiguracji zostanie ukończona i zwraca następujące dane wyjściowe.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Pobieranie nowych bloków DBs SQL

Po zarejestrowaniu urządzenia usługa tworzenia kopii zapasowych pobierze wówczas szczegóły db dostępnych. Jeśli użytkownik doda SQL DBs/SQL wystąpień do zarejestrowanego komputera później, trzeba ręcznie wyzwolić usługę tworzenia kopii zapasowych, aby wykonać świeże "zapytanie", aby uzyskać wszystkie niechronione DBs (w tym nowo dodane) ponownie. Użyj parametru cmdlet [Initialize-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS na maszynie wirtualnej SQL, aby wykonać nowe zapytanie. Polecenie czeka na zakończenie operacji. Później użyj polecenia cmdlet [Get-AzRecoveryServicesBackupProtectableItititem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS, aby uzyskać listę najnowszych niechronionych składników SQL

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
```

Po pobraniu odpowiednich elementów chronionych włącz kopie zapasowe zgodnie z instrukcjami opisanymi w [powyższej sekcji](#configuring-backup).
Jeśli ktoś nie chce ręcznie wykryć nowych DBs, mogą zdecydować się na autoochronę, jak wyjaśniono [poniżej](#enable-autoprotection).

## <a name="enable-autoprotection"></a>Włącz automatyczną ochrony

Użytkownik może skonfigurować tworzenie kopii zapasowych w taki sposób, aby wszystkie bazy danych dodane w przyszłości były automatycznie chronione za pomocą określonych zasad. Aby włączyć automatyczną protekcję, użyj polecenia cmdlet [PS Enable-AzRecoveryServicesBackupAutoProtection.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0)

Ponieważ instrukcja jest do kopii zapasowej wszystkich przyszłych DBs, operacja jest wykonywana na poziomie SQLInstance.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $targetvault.ID
```

Po podaniu zamiaru automatycznej ochrony, zapytanie do komputera, aby pobrać nowo dodane DB odbywa się jako zaplanowane zadanie w tle co 8 godzin.

## <a name="restore-sql-dbs"></a>Przywracanie db SQL

Usługa Azure Backup może przywrócić bazy danych programu SQL Server, które są uruchomione na maszynach wirtualnych platformy Azure w następujący sposób:

* Przywróć do określonej daty lub godziny (do drugiej) przy użyciu kopii zapasowych dziennika transakcji. Usługa Azure Backup automatycznie określa odpowiednią pełną różnicową kopię zapasową i łańcuch kopii zapasowych dziennika, które są wymagane do przywrócenia na podstawie wybranego czasu.
* Przywróć określoną pełną lub różnicową kopię zapasową, aby przywrócić do określonego punktu odzyskiwania.

Sprawdź wymagania wstępne wymienione [w tym miejscu](restore-sql-database-azure-vm.md#prerequisites) przed przywróceniem kontrolerów DBs SQL.

Najpierw pobierz odpowiednie kopii zapasowej bazy danych SQL przy użyciu polecenia cmdlet [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
```

### <a name="fetch-the-relevant-restore-time"></a>Pobieranie odpowiedniego czasu przywracania

Jak opisano powyżej, użytkownik może przywrócić kopię zapasową bazy danych SQL do pełnej/różnicowej kopii **LUB** do dziennika point-in-time.

#### <a name="fetch-distinct-recovery-points"></a>Pobieranie różnych punktów odzyskiwania

Użyj [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) do pobierania różnych (Full/różnicowe) punktów odzyskiwania dla kopii zapasowej bazy danych SQL.

```powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
```

Dane wyjściowe są podobne do poniższego przykładu

```output
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
```

Użyj filtru "RecoveryPointId" lub filtru tablicowego, aby pobrać odpowiedni punkt odzyskiwania.

```powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
```

#### <a name="fetch-point-in-time-recovery-point"></a>Pobieranie punktu odzyskiwania punktu w czasie

Jeśli użytkownik chce przywrócić bazę danych do określonego punktu w czasie, należy użyć polecenia cmdlet [PS Get-AzRecoveryServicesBackupRecoveryLogChain](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) PS. Polecenie cmdlet zwraca listę dat, które reprezentują godziny rozpoczęcia i zakończenia nieprzerwanego, ciągłego łańcucha dziennika dla tego elementu kopii zapasowej SQL. Żądany punkt w czasie powinien mieszcząć się w tym zakresie.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

Dane wyjściowe będą podobne do poniższego przykładu.

```output
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
```

Powyższe dane wyjściowe oznacza, że użytkownik może przywrócić do dowolnego punktu w czasie między wyświetlanym czasem rozpoczęcia i czasu zakończenia. Czasy są w UTC. Skonstruuj dowolny punkt w czasie w PS, który mieści się w zakresie pokazanym powyżej.

> [!NOTE]
> Gdy w dzienniku w czasie wybrany do przywrócenia, użytkownik nie musi określić punkt początkowy, czyli pełną kopię zapasową, z której baza danych jest przywracana. Usługa Azure Backup zajmie się całym planem odzyskiwania, czyli pełną kopią zapasową do wyboru, jakie kopie zapasowe dziennika mają być stosowane itp.

### <a name="determine-recovery-configuration"></a>Określanie konfiguracji odzyskiwania

W przypadku przywracania bazy danych SQL obsługiwane są następujące scenariusze przywracania.

* Zastępowanie kopii zapasowej bazy danych SQL DB danymi z innego punktu odzyskiwania - OriginalWorkloadRestore
* Przywracanie bazy danych SQL jako nowej bazy danych w tym samym wystąpieniu SQL — AlternateWorkloadRestore
* Przywracanie bazy danych SQL jako nowej bazy danych w innym wystąpieniu SQL w innym sql VM - AlternateWorkloadRestore
* Przywracanie bazy danych SQL jako plików .bak -RestoreAsFiles

Po pobraniu odpowiedniego punktu odzyskiwania (distinct lub log point-in-time) użyj polecenia cmdlet [PS programu Get-AzRecoveryServicesBackupWorkLoadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS, aby pobrać obiekt konfiguracji odzyskiwania zgodnie z żądanym planem odzyskiwania.

#### <a name="original-workload-restore"></a>Przywracanie oryginalnego obciążenia

Aby zastąpić zapasowy baz danych danymi z punktu odzyskiwania, wystarczy określić odpowiednią flagę i odpowiedni punkt odzyskiwania, jak pokazano w poniższych przykładach.

##### <a name="original-restore-with-distinct-recovery-point"></a>Oryginalne przywracanie z odrębnym punktem odzyskiwania

```powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="original-restore-with-log-point-in-time"></a>Oryginalne przywracanie z log point-in-time

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Przywracanie obciążenia alternatywnego

> [!IMPORTANT]
> Kopię zapasową bazy danych SQL można przywrócić jako nowy baz danych do innego SQLInstance tylko w maszynie Wirtualnej platformy Azure "zarejestrowany" do tego magazynu.

Jak opisano powyżej, jeśli docelowa instance SQL znajduje się w innej maszynie wirtualnej platformy Azure, upewnij się, że jest [zarejestrowany w tym magazynie](#registering-the-sql-vm) i odpowiednie SQLInstance pojawia się jako element chroniony.

```powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
```

Następnie wystarczy przekazać odpowiedni punkt odzyskiwania, docelowe wystąpienie SQL z prawej flagi, jak pokazano poniżej.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Alternatywne przywracanie z odrębnym punktem odzyskiwania

```powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="alternate-restore-with-log-point-in-time"></a>Alternatywne przywracanie z log point-in-time

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="restore-as-files"></a>Przywracanie jako plików

Aby przywrócić dane kopii zapasowej jako pliki .bak zamiast bazy danych, wybierz opcję **Przywróć jako pliki.** Kopii zapasowej bazy danych SQL można przywrócić do dowolnej maszyny Wirtualnej docelowej, która jest zarejestrowana w tym magazynie.

```powershell
$TargetContainer= Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName "VM name" -VaultId $vaultID
```

##### <a name="restore-as-files-with-distinct-recovery-point"></a>Przywracanie jako plików z odrębnym punktem odzyskiwania

```powershell
$FileRestoreWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-latest-full"></a>Przywracanie jako plików z logiem w czasie z najnowszej pełnej

```powershell
$FileRestoreWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-a-specified-full"></a>Przywracanie jako plików z log point-in-time z określonej pełnej

Jeśli chcesz nadać określoną pełną wersję, która powinna być używana do przywracania, użyj następującego polecenia:

```powershell
$FileRestoreWithLogAndSpecificFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -FromFull $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

Obiekt konfiguracji punktu odzyskiwania końcowego uzyskany z polecenia cmdlet [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS ma wszystkie istotne informacje do przywrócenia i jest jak pokazano poniżej.

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

Można edytować przywróconą nazwę bazy danych, OverwriteWLIfpresent, NoRecoveryMode i targetPhysicalPath pola. Uzyskaj więcej szczegółów dotyczących ścieżek plików docelowych, jak pokazano poniżej.

```powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath
```

```output
MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
```

Ustaw odpowiednie właściwości PS jako wartości ciągu, jak pokazano poniżej.

```powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl
```

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

> [!IMPORTANT]
> Upewnij się, że ostateczny obiekt konfiguracji odzyskiwania ma wszystkie niezbędne i prawidłowe wartości, ponieważ operacja przywracania będzie oparta na obiekcie konfigura.

### <a name="restore-with-relevant-configuration"></a>Przywracanie z odpowiednią konfiguracją

Po uzyskaniu i zweryfikowaniu odpowiedniego obiektu konfiguracji odzyskiwania użyj polecenia cmdlet [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS, aby rozpocząć proces przywracania.

```powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
```

Operacja przywracania zwraca zadanie do śledzenia.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
```

## <a name="manage-sql-backups"></a>Zarządzanie kopiami zapasowymi SQL

### <a name="on-demand-backup"></a>Kopia zapasowa na żądanie

Po włączeniu kopii zapasowej dla bazy danych użytkownik może również wyzwolić kopię zapasową na żądanie dla bazy danych przy użyciu polecenia cmdlet [Backup-AzRecoveryServicesBacksBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS. Poniższy przykład wyzwala pełną kopię zapasową w bazy danych SQL z włączoną kompresją, a pełna kopia zapasowa powinna zostać zachowana przez 60 dni.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

Polecenie kopii zapasowej na żądanie zwraca zadanie do śledzenia.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
```

Jeśli dane wyjściowe są tracone lub jeśli chcesz uzyskać odpowiedni identyfikator zadania, [pobierz listę zadań](#track-azure-backup-jobs) z usługi Azure Backup, a następnie śledź ją i jej szczegóły.

### <a name="change-policy-for-backup-items"></a>Zmienianie zasad dotyczących elementów kopii zapasowej

Użytkownik może zmodyfikować istniejące zasady lub zmienić zasady elementu kopii zapasowej z Policy1 na Policy2. Aby przełączyć zasady dla elementu kopii zapasowej, pobierz odpowiednie zasady i utwór kopii zapasowej i użyj polecenia [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) z elementem kopii zapasowej jako parametrem.

```powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
```

Polecenie czeka, aż kopia zapasowa konfiguracji zostanie ukończona i zwraca następujące dane wyjściowe.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>Ponowne rejestrowanie maszyn wirtualnych SQL

> [!WARNING]
> Upewnij się, że przeczytałem ten [dokument,](backup-sql-server-azure-troubleshoot.md#re-registration-failures) aby zrozumieć objawy i przyczyny awarii przed podjęciem próby ponownej rejestracji

Aby wyzwolić ponowną rejestrację maszyny Wirtualnej SQL, pobierz odpowiedni kontener kopii zapasowej i przekaż go do polecenia cmdlet rejestru.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
```

### <a name="stop-protection"></a>Zatrzymywanie ochrony

#### <a name="retain-data"></a>Zachowywanie danych

Jeśli użytkownik chce zatrzymać ochronę, można użyć polecenia cmdlet [PS Disable-AzRecoveryServicesBackupProtection.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) Spowoduje to zatrzymanie zaplanowanych kopii zapasowych, ale dane, które do tej pory są przechowywane na zawsze.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
```

#### <a name="delete-backup-data"></a>Usuwanie danych kopii zapasowej

Aby całkowicie usunąć przechowywane dane kopii zapasowej w przechowalni, wystarczy dodać flagę /przełączenie "-RemoveRecoveryPoints" do [polecenia ochrony "wyłącz".](#retain-data)

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
```

#### <a name="disable-auto-protection"></a>Wyłącz automatyczną ochronę

Jeśli autoprotekcja została skonfigurowana na SQLInstance, użytkownik może go wyłączyć za pomocą polecenia cmdlet PS [Disable-AzRecoveryServicesBackupAutoProtection.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0)

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
```

#### <a name="unregister-sql-vm"></a>Wyrejestruj maszynę wirtualną SQL

Jeśli wszystkie db serwera SQL [nie są już chronione i nie istnieje żadnych danych kopii zapasowej,](#delete-backup-data)użytkownik może wyrejestrować maszynę wirtualną SQL z tego magazynu. Tylko wtedy użytkownik może chronić DBs do innego magazynu. Użyj polecenia cmdlet [wyrejestruj-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS, aby wyrejestrować maszynę wirtualną SQL.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
```

### <a name="track-azure-backup-jobs"></a>Śledzenie zadań tworzenia kopii zapasowych platformy Azure

Należy pamiętać, że usługa Azure Backup śledzi tylko zadania wyzwalane przez użytkownika w kopii zapasowej SQL. Zaplanowane kopie zapasowe (w tym kopie zapasowe dziennika) nie są widoczne w portalu/programie Powershell. Jeśli jednak zaplanowane zadania nie powiodą się, [alert kopii zapasowej](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) jest generowany i wyświetlany w portalu. [Użyj usługi Azure Monitor,](backup-azure-monitoring-use-azuremonitor.md) aby śledzić wszystkie zaplanowane zadania i inne istotne informacje.

Użytkownicy mogą śledzić operacje wyzwalane na żądanie/użytkownika za pomocą jobID, który jest zwracany w [danych wyjściowych](#on-demand-backup) zadań asynchronicznych, takich jak kopia zapasowa. Użyj polecenia cmdlet [Get-AzRecoveryServicesBackupJobDetail](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail) PS do śledzenia zadania i jego szczegółów.

```powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
```

Aby uzyskać listę zadań na żądanie i ich statusy z usługi Azure Backup, należy użyć polecenia cmdlet [PS Get-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS. Poniższy przykład zwraca wszystkie zadania SQL w toku.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Aby anulować zadanie w toku, należy użyć polecenia cmdlet [STOP-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS.

## <a name="managing-sql-always-on-availability-groups"></a>Zarządzanie grupami dostępności zawsze włączonymi SQL

W przypadku grup dostępności zawsze włączone sql, upewnij się, [że zarejestrować wszystkie węzły](#registering-the-sql-vm) grupy dostępności (AG). Po zakończeniu rejestracji dla wszystkich węzłów obiekt grupy dostępności SQL jest logicznie tworzony w obszarze elementów chronionych. Bazy danych w ramach SQL AG zostaną wyświetlone jako "SQLDatabase". Węzły będą wyświetlane jako autonomiczne wystąpienia, a domyślne bazy danych SQL pod nimi będą również wyświetlane jako bazy danych SQL.

Załóżmy na przykład, że usługa SQL AG ma dwa węzły: 'sql-server-0' i 'sql-server-1' oraz 1 SQL AG DB. Po zarejestrowaniu obu tych węzłów, jeśli użytkownik [wymieni elementy podlegające ochronie,](#fetching-sql-dbs)wyświetla następujące składniki:

* Obiekt SQL AG — typ elementu chronionego jako SQLAvailabilityGroup
* Baza danych AG SQL — typ elementu chronionego jako baza danych SQLDatabase
* sql-server-0 - typ elementu chronionego jako INstance SQL
* sql-server-1 - typ elementu chronionego jako SQLInstance
* Wszelkie domyślne dc SQL (master, model, msdb) w sql-server-0 - typ elementu chronionego jako SQLDatabase
* Wszelkie domyślne dc SQL (master, model, msdb) w sql-server-1 - typ elementu chronionego jako SQLDatabase

sql-server-0, sql-server-1 będzie również wymieniony jako "AzureVMAppContainer", gdy [kontenery kopii zapasowych są wymienione](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0).

Wystarczy pobrać odpowiednią bazę danych SQL, aby [włączyć tworzenie kopii zapasowych,](#configuring-backup) a polecenia [cmdlet ps](#restore-sql-dbs) na [żądanie](#on-demand-backup) są identyczne.
