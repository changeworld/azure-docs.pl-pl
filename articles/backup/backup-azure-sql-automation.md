---
title: 'Usługa Azure Backup: Tworzenie kopii zapasowej i przywracanie bazy danych SQL w maszynach wirtualnych platformy Azure przy użyciu usługi Kopia zapasowa Azure i programu PowerShell'
description: Tworzenie kopii zapasowej i przywracania bazy danych SQL w maszynach wirtualnych platformy Azure przy użyciu usługi Kopia zapasowa Azure i programu PowerShell.
services: backup
author: pvrk
manager: vijayts
keywords: Azure Backup; SQL;
ms.service: backup
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pullabhk
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 6a2e065466ab4426a6472b64fae19d264ff8dd81
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734222"
---
# <a name="back-up-and-restore-sql-databases-in-azure--vms-with-powershell"></a>Tworzenie kopii zapasowej i przywracanie bazy danych SQL w maszynach wirtualnych platformy Azure przy użyciu programu PowerShell

W tym artykule opisano sposób użycia programu Azure PowerShell do tworzenia kopii i odzyskiwanie bazy danych SQL w obrębie maszyny Wirtualnej platformy Azure za pomocą [kopia zapasowa Azure](backup-overview.md) magazyn usługi Recovery Services.

W tym samouczku wyjaśniono:

> [!div class="checklist"]
> * Konfigurowanie programu PowerShell i zarejestrować dostawcę usługi Azure Recovery Services.
> * Utwórz magazyn usługi Recovery Services.
> * Konfigurowanie kopii zapasowej dla bazy danych SQL w Maszynie wirtualnej platformy Azure.
> * Uruchom zadanie tworzenia kopii zapasowej.
> * Przywracanie kopii zapasowej bazy danych SQL.
> * Monitorowanie kopii zapasowych i przywracania zadania.

## <a name="before-you-start"></a>Przed rozpoczęciem

* [Dowiedz się więcej](backup-azure-recovery-services-vault-overview.md) dotyczących magazynów usługi Recovery Services.
* Przeczytaj o możliwości funkcji [tworzenia kopii zapasowych baz danych SQL w ramach maszyn wirtualnych platformy Azure](backup-azure-sql-database.md#before-you-start).
* Przejrzyj hierarchii obiektów programu PowerShell dla usługi Recovery Services.

### <a name="recovery-services-object-hierarchy"></a>Hierarchia obiektów usług odzyskiwania

Hierarchia obiektów jest podsumowywane na poniższym diagramie.

![Hierarchia obiektów usług odzyskiwania](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Przegląd **Az.RecoveryServices** [informacje o poleceniach cmdlet](/powershell/module/az.recoveryservices) odwołania w bibliotece platformy Azure.

### <a name="set-up-and-install"></a>Konfigurowanie i instalowanie

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Konfigurowanie programu PowerShell w następujący sposób:

1. [Pobierz najnowszą wersję programu Az PowerShell](/powershell/azure/install-az-ps). Minimalna wymagana wersja to 1.5.0.

2. Znajdowanie poleceń cmdlet programu Azure PowerShell w kopii zapasowej za pomocą następującego polecenia:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Przejrzyj aliasów i poleceń cmdlet dla usługi Azure Backup i magazynu usługi Recovery Services. Poniżej przedstawiono przykładowy wygląd ekranu. Nie jest pełną listę poleceń cmdlet.

    ![Listę poleceń cmdlet usługi Recovery Services](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Zaloguj się do konta platformy Azure za pomocą **Connect AzAccount**.
5. Na stronie sieci web, która pojawia się zostanie wyświetlony monit wprowadź poświadczenia konta.

    * Alternatywnie można uwzględnić poświadczeń konta jako parametru w **Connect AzAccount** polecenia cmdlet z **-poświadczeń**.
    * Jeśli jesteś partnerem CSP pracy dla dzierżawy, należy określić klienta jako dzierżawca, przy użyciu nazwy domeny głównej identyfikator dzierżawy lub jednego dzierżawcy. Na przykład **Connect AzAccount-dzierżawy** fabrikam.com.

6. Skojarz subskrypcję, której chcesz użyć przy użyciu konta, ponieważ konto może mieć wiele subskrypcji.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Jeśli używasz usługi Azure Backup po raz pierwszy, użyj **AzResourceProvider rejestru** polecenia cmdlet, aby zarejestrować dostawcę usługi Azure Recovery Services w ramach subskrypcji.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Sprawdź pomyślnie zarejestrowano dostawców:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. W danych wyjściowych polecenia Sprawdź, czy **RegistrationState** zmieni się na **zarejestrowanej**. Jeśli nie działa ona **AzResourceProvider rejestru** ponownie polecenie cmdlet.

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Wykonaj następujące kroki, aby utworzyć magazyn usługi Recovery Services.

Magazyn usługi Recovery Services jest zasobem usługi Resource Manager, należy go umieścić w grupie zasobów. Można użyć istniejącej grupy zasobów lub utworzyć nową grupę zasobów o **New AzResourceGroup** polecenia cmdlet. Podczas tworzenia grupy zasobów, określ nazwę i lokalizację grupy zasobów.

1. Magazyn jest umieszczany w grupie zasobów. Jeśli nie masz istniejącego zasobu, grupy, Utwórz nową grupę za pomocą [New AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). W tym przykładzie utworzymy nową grupę zasobów w regionie zachodnie stany USA.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Użyj [New AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) polecenie cmdlet do tworzenia w magazynie. Określ lokalizację tego samego magazynu, która była używana dla grupy zasobów.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Określenie typu nadmiarowości magazynu magazynu.

    * Możesz użyć [magazyn lokalnie nadmiarowy](../storage/common/storage-redundancy-lrs.md) lub [magazyn geograficznie nadmiarowy](../storage/common/storage-redundancy-grs.md).
    * Poniższy przykład ustawia **- BackupStorageRedundancy** opcja dla[AzRecoveryServicesBackupProperty zestaw](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd dla **testvault** równa  **GeoRedundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Wyświetlanie magazynów w ramach subskrypcji

Aby wyświetlić wszystkie magazyny w ramach subskrypcji, użyj [Get AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

Rezultat jest podobny do następującego. Podano skojarzonej grupy zasobów i lokalizacji.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Ustaw kontekst magazynu

Store obiektu magazynu w zmiennej i ustawić kontekst magazynu.

* Wiele poleceń cmdlet usługi Azure Backup wymaga obiektu magazynu usługi Recovery Services jako danych wejściowych, więc jest to wygodne przechowywanie obiektu magazynu w zmiennej.
* Kontekst magazynu to typ danych chronionych w magazynie. Ustaw ją za pomocą [AzRecoveryServicesVaultContext zestaw](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Po ustawieniu kontekst ma zastosowanie do wszystkich kolejnych poleceń cmdlet.

W poniższym przykładzie ustawiono kontekst magazynu dla **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Pobierz identyfikator magazynu

Planujemy wycofanie kontekst magazynu ustawienia zgodnie z wytycznymi programu Azure PowerShell. Zamiast tego można przechowywać lub pobrać Identyfikatora magazynu i przekaż go do odpowiednich poleceń w następujący sposób:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Konfigurowanie zasad kopii zapasowych

Zasady tworzenia kopii zapasowych określa harmonogramu dla kopii zapasowych i ile punktów odzyskiwania kopii zapasowej powinny być przechowywane:

* Zasady tworzenia kopii zapasowych jest skojarzony z co najmniej jedne zasady przechowywania. Zasady przechowywania Określa, jak długo punkt odzyskiwania jest przechowywana, przed usunięciem.
* Domyślne zasady tworzenia kopii zapasowej przechowywania przy użyciu widoku [Get AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Widok, używając harmonogram zasad tworzenia kopii zapasowej domyślne [Get AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Możesz użyć [New AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) polecenia cmdlet, aby utworzyć nowe zasady kopii zapasowych. Wprowadzona obiektów zasad harmonogram i okres przechowywania.

Poniższy przykład zasad harmonogram i zasady przechowywania są przechowywane w zmiennych. Następnie używa tych zmiennych jako parametry dla nowych zasad (**NewSQLPolicy**). **NewSQLPolicy** wykonuje kopię zapasową codzienne "pełne" przechowujące ją przez 180 dni i wykonuje kopię zapasową dziennika, co 2 godziny

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Rezultat jest podobny do następującego.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 9:00:00 PM      Daily                                    False                True
```

## <a name="enable-backup"></a>Włącz wykonywanie kopii zapasowej

### <a name="registering-the-sql-vm"></a>Rejestrowanie maszyny Wirtualnej SQL

Dla kopii zapasowych maszyn wirtualnych platformy Azure i udziałów plików platformy Azure usługa Backup można nawiązać połączenie z tych zasobów usługi Azure Resource Manager i pobrać odpowiednie szczegóły. Ponieważ SQL aplikacji w Maszynie wirtualnej platformy Azure, usługa Backup wymaga uprawnień dostępu do aplikacji i pobrać wymagane szczegóły. Aby to zrobić, należy *"register"* maszyny Wirtualnej platformy Azure, który zawiera aplikację SQL przy użyciu magazynu usługi Recovery services. Po zarejestrowaniu maszyny Wirtualnej SQL przy użyciu magazynu może chronić baz danych SQL, tylko tym magazynie. Użyj [AzRecoveryServicesBackupContainer rejestru](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS polecenia cmdlet, aby zarejestrować maszyny Wirtualnej.

````powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
````

Polecenie zwróci "kontener kopii zapasowej" tego zasobu i stan zostaną "zarejestrowane"

> [!NOTE]
> Jeśli nie podano parametru force, użytkownik jest proszony o potwierdzenie z tekstem "Czy chcesz wyłączyć ochronę dla tego kontenera". Ignoruj ten tekst i powiedzieć "Y", aby potwierdzić. Jest to znany problem i pracujemy nad Usuń tekst, a wymagania dotyczące parametru force

### <a name="fetching-sql-dbs"></a>Pobieranie baz danych SQL

Po zakończeniu rejestracji usługa Backup będzie można wyświetlić listę wszystkich dostępnych składników SQL na maszynie wirtualnej. Aby wyświetlić wszystkie składniki SQL, ale do wykonania kopii zapasowej na to zgodę magazynu [Get AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) polecenia cmdlet PS

````powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
````

Dane wyjściowe zostaną wyświetlone wszystkie niechronione składniki SQL dla wszystkich maszyn wirtualnych SQL, zarejestrowany w tym magazynie przy użyciu typu elementu i nazwy serwera. Można dalej filtrować określonej maszyny wirtualnej SQL, przekazując "-Container" parametr lub użyj kombinacji "Name" i "ServerName" wraz z ItemType Flaga na unikatowy element SQL.

````powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
````

### <a name="configuring-backup"></a>Konfigurowanie kopii zapasowej

Skoro mamy już wymagane bazy danych SQL i zasad za pomocą którego należy utworzyć kopię, możemy użyć [AzRecoveryServicesBackupProtection Włącz](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) polecenia cmdlet w celu skonfigurowania kopii zapasowej dla tej bazy danych SQL.

````powershell
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
````

Polecenie czeka, aż konfigurowania kopii zapasowej zostało zakończone i zwraca następujące dane wyjściowe.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Pobieranie nowych baz danych SQL

Po zarejestrowaniu komputera usługa Backup powoduje pobranie szczegółów baz danych, następnie dostępne. Jeśli użytkownik później dodaje wystąpień SQL bazy danych SQL do zarejestrowanych maszyny, należy ręcznie wyzwolić usługi tworzenia kopii zapasowej w celu wykonywania świeży zapytania, można pobrać wszystkich niechronionych baz danych (w tym te nowo dodane) ponownie. Użyj [AzRecoveryServicesBackupItem zainicjować](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet maszyny Wirtualnej SQL do wykonania zapytania od nowa. Polecenie ma czekać, aż do zakończenia operacji. Później użyć [Get AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS polecenia cmdlet, aby uzyskać listę najnowszych niechronione składniki SQL Server

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
````

Po pobrane odpowiednie elementy podlegające ochronie, włącz wykonywanie kopii zapasowych zgodnie z instrukcją w [powyżej sekcji](#configuring-backup).
Jeśli jeden nie chcesz ręcznie Wykryj nowych baz danych, można zdecydują się na autoprotection zgodnie z objaśnieniem [poniżej](#enable-autoprotection).

## <a name="enable-autoprotection"></a>Włącz AutoProtection

Użytkownik może skonfigurować kopii zapasowej w taki sposób, że wszystkie bazy danych dodane w przyszłości, są automatycznie chronione za pomocą niektórych zasad. Aby włączyć autoprotection, użyj [AzRecoveryServicesBackupAutoProtection Włącz](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS polecenia cmdlet.

Ponieważ instrukcja do wykonywania kopii zapasowych wszystkich DBs przyszłych operacji odbywa się na SQLInstance poziomu.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $targetPolicy -VaultId $targetvault.ID
```

Gdy celem autoprotection zostanie podany, zapytanie na komputerze, który można pobrać nowo dodanych baz danych odbywa się jako zadanie w tle zaplanowane co 8 godzin.

## <a name="restore-sql-dbs"></a>Przywracanie baz danych SQL

Usługa Azure Backup można przywrócić bazy danych programu SQL Server, które są uruchomione na maszynach wirtualnych platformy Azure w następujący sposób:

1. Przywróć do określonej daty lub czasu (sekundy) przy użyciu kopii zapasowej dziennika transakcji. Usługa Azure Backup automatycznie ustala odpowiedni pełnej różnicowej kopii zapasowej i łańcuch kopii zapasowych dzienników, które są wymagane do przywracania na podstawie wybranego czasu.
2. Przywróć określonej pełna lub różnicowa kopii zapasowej do przywrócenia określony punkt odzyskiwania.

Sprawdź wymagania wstępne wymienione [tutaj](restore-sql-database-azure-vm.md#prerequisites) przed przywróceniem baz danych SQL.

Najpierw pobrać odpowiednią kopię zapasową bazy danych SQL przy użyciu [Get AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS polecenia cmdlet.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
````

### <a name="fetch-the-relevant-restore-time"></a>Czas przywracania odpowiednie pobierania

Zgodnie z powyższymi informacjami, użytkownik będzie mógł przywrócić kopię zapasową bazy danych SQL do pełnej/różnicowej kopii **lub** do dziennika punktu w czasie.

#### <a name="fetch-distinct-recovery-points"></a>Pobierz punkty odzyskiwania odrębne

Użyj [Get AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) można pobrać różne punkty odzyskiwania (pełnej/różnicowej) dla kopii zapasowej bazy danych SQL.

````powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
````

Rezultat jest podobny do poniższego przykładu

````powershell
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
````

Użyj filtru "RecoveryPointId" lub filtru tablicy, aby pobrać punkt odzyskiwania odpowiednie.

````powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
````

#### <a name="fetch-point-in-time-recovery-point"></a>Pobierz punkt odzyskiwania w momencie

Jeśli użytkownik chce, aby przywrócić bazy danych do określonych w momencie, użyj [Get AzRecoveryServicesBackupRecoveryLogChain](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) PS polecenia cmdlet. Polecenie cmdlet zwraca listę dat, które reprezentują godziny rozpoczęcia i zakończenia łańcucha nieprzerwany, zapewniająca ciągłą dziennika dla tego elementu kopii zapasowej SQL. Żądany punkt w czasie, należy w tym zakresie.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

Dane wyjściowe będą podobne do poniższego przykładu.

````powershell
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
````

Powyższe dane wyjściowe oznacza, że można przywrócić tego użytkownika, do dowolnego punktu w czasie między czasem rozpoczęcia wyświetlanych i czas zakończenia. Godziny są w formacie UTC. Skonstruuj dowolnego punktu w czasie w PS, który znajduje się w zakresie przedstawionych powyżej.

> [!NOTE]
> Gdy dziennika w momencie wybrany do przywrócenia, użytkownik nie trzeba określać punkt początkowy, czyli, pełnej kopii zapasowej, z którego jest przywracany bazy danych. Usługa Azure Backup zajmie się planu odzyskiwania całego czyli które pełną kopię zapasową, aby wybrać, co kopie zapasowe, aby zastosować itp.

### <a name="determine-recovery-configuration"></a>Określenie konfiguracji odzyskiwania

W przypadku przywracania bazy danych SQL są obsługiwane w następujących scenariuszach przywracania.

1. Zastępowanie kopii zapasowej SQL DB przy użyciu danych z innego punktu odzyskiwania — OriginalWorkloadRestore
2. Przywracanie bazy danych SQL jako nowej bazy danych, w tym samym wystąpieniu SQL - AlternateWorkloadRestore
3. Przywracanie bazy danych SQL jako nowej bazy danych w innym wystąpieniu programu SQL w innej maszyny Wirtualnej SQL — AlternateWorkloadRestore

Po pobieranie punktu odzyskiwania odpowiednie (distinct lub zaloguj się w momencie), użyj [Get AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS polecenia cmdlet, aby pobrać obiekt konfiguracji odzyskiwania zgodnie z planem odzyskiwania.

#### <a name="original-workload-restore"></a>Oryginalny przywracania obciążenia

Aby zastąpić kopii zapasowej bazy danych przy użyciu danych z punktu odzyskiwania, po prostu określić flagę prawo i punkt odzyskiwania odpowiednie jak pokazano w następujące przykłady zapytań zakończonych.

##### <a name="original-restore-with-distinct-recovery-point"></a>Odrębne punkt odzyskiwania do oryginalnej przywracania

````powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="original-restore-with-log-point-in-time"></a>Oryginalny przywracania dziennika punktu w czasie

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Przywracanie alternatywne obciążenia

> [!IMPORTANT]
> Kopię zapasową bazy danych SQL mogą zostać przywrócone jako nowej bazy danych do innego SQLInstance tylko w maszynie Wirtualnej platformy Azure "zarejestrowane", w tym magazynie.

Zgodnie z powyższymi informacjami, jeśli element docelowy SQLInstance mieści się w innej maszyny Wirtualnej platformy Azure, upewnij się, że jest [zarejestrowany w tym magazynie](#registering-the-sql-vm) i odpowiednie SQLInstance pojawia się jako elementy podlegające ochronie.

````powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
````

Następnie po prostu Przekaż punkt odzyskiwania istotne, wystąpienie SQL docelowe z flagą prawo jak pokazano poniżej.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Odrębne punktu odzyskiwania do alternatywnej przywracania

````powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="alternate-restore-with-log-point-in-time"></a>Alternatywne do przywracania dziennika punktu w czasie

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

Obiekt konfiguracji punktu końcowego odzyskiwania uzyskany z [Get AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS polecenie cmdlet ma wszystkie istotne informacje dotyczące przywracania i jak pokazano poniżej.

````powershell
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
````

Można edytować przywróconej pola nazwy, OverwriteWLIfpresent, NoRecoveryMode i targetPhysicalPath bazy danych. Dowiedz się więcej na docelowe ścieżki plików, jak pokazano poniżej.

````powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath

MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
````

Ustaw odpowiednie właściwości PS jako wartości ciągu, jak pokazano poniżej.

````powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl

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
````

> [!IMPORTANT]
> Upewnij się, że obiekt konfiguracji końcowej odzyskiwania ma wszystkie niezbędne i odpowiednie wartości, ponieważ operacja przywracania będzie zależeć od obiektu konfiguracji.

### <a name="restore-with-relevant-configuration"></a>Przywracanie przy użyciu odpowiednich konfiguracji

Gdy obiekt konfiguracji odzyskiwania istotne jest uzyskane i zweryfikowane, użyć [AzRecoveryServicesBackupItem przywracania](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS polecenia cmdlet, aby rozpocząć proces przywracania.

````powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
````

Operacja przywracania zwraca zadanie, które mają być śledzone.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
````

## <a name="manage-sql-backups"></a>Zarządzanie kopiami zapasowymi SQL

### <a name="on-demand-backup"></a>Kopii zapasowej na żądanie

Po włączeniu kopii zapasowej dla bazy danych, użytkownik może także wyzwolić kopii zapasowej na żądanie dla bazy danych za pomocą [AzRecoveryServicesBackupItem kopii zapasowej](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS polecenia cmdlet. Poniższy przykład wyzwala tworzenie pełnej kopii zapasowej bazy danych SQL z włączoną kompresją, a pełna kopia zapasowa ma być przechowywana przez 60 dni.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
````

Polecenie tworzenia kopii zapasowych ad hoc zwraca zadanie, które mają być śledzone.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
````

W przypadku utraty danych wyjściowych, lub jeśli chcesz pobrać odpowiedni identyfikator zadania [listę zadań](#track-azure-backup-jobs) z usługi Azure Backup usługi i śledzić go i jego szczegóły.

### <a name="change-policy-for-backup-items"></a>Zmiana zasad dla elementów kopii zapasowych

Użytkownika można zmodyfikować istniejące zasady lub zmienić zasady elementu kopii zapasowej z zasada 1 na Zasada2. Aby przełączyć zasady dla elementu kopii zapasowej, po prostu pobierania odpowiednich zasad i utworzyć kopię zapasową elementu i użyj [AzRecoveryServices Włącz](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) polecenia elementu kopii zapasowej jako parametr.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
````

Polecenie czeka, aż konfigurowania kopii zapasowej zostało zakończone i zwraca następujące dane wyjściowe.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>Zarejestruj ponownie maszyn wirtualnych SQL

> [!WARNING]
> Pamiętaj przeczytać ten tekst [dokumentu](backup-sql-server-azure-troubleshoot.md#re-registration-failures) , aby zrozumieć objawy błędu i powoduje, że przed podjęciem próby wykonania ponownej rejestracji

Aby wyzwolić ponowną rejestrację maszynę Wirtualną programu SQL, należy pobrać odpowiedni kontener kopii zapasowych i przekazać go do polecenia cmdlet register.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
````

### <a name="stop-protection"></a>Zatrzymaj ochronę

#### <a name="retain-data"></a>Przechowywanie danych

Jeśli użytkownik zamierza Zatrzymaj ochronę, mogą używać [AzRecoveryServicesBackupProtection Wyłącz](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS polecenia cmdlet. Spowoduje to zatrzymanie zaplanowanych kopii zapasowych, ale dane kopii zapasowej do teraz są zachowywane nieskończoność.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Usuwanie danych kopii zapasowej

Aby całkowicie usunąć przechowywać dane kopii zapasowej w magazynie, wystarczy dodać atrybut "-RemoveRecoveryPoints flagi/przełącznik, aby [Wyłącz polecenia ochrona](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

#### <a name="disable-auto-protection"></a>Wyłącz automatyczną ochronę

Jeśli autoprotection została skonfigurowana na SQLInstance, użytkownik może je wyłączyć za pomocą [AzRecoveryServicesBackupAutoProtection Wyłącz](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS polecenia cmdlet.

````powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
````

#### <a name="unregister-sql-vm"></a>Wyrejestruj maszyny Wirtualnej SQL

Jeśli wszystkie bazy danych programu SQL server [są już nie istnieją dane chronione i nie kopii zapasowej](#delete-backup-data), użytkownik nie można wyrejestrować maszyny Wirtualnej SQL z tego magazynu. Dopiero wtedy użytkownik może chronić baz danych do innego magazynu. Użyj [AzRecoveryServicesBackupContainer Wyrejestruj](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS polecenia cmdlet, aby wyrejestrować maszyny Wirtualnej SQL.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
````

### <a name="track-azure-backup-jobs"></a>Śledź zadania tworzenia kopii zapasowej platformy Azure

Należy zauważyć, że kopia zapasowa Azure tylko śledzi zadania użytkownika wyzwalane w kopii zapasowej programu SQL. Zaplanowane kopie zapasowe (w tym kopie zapasowe dziennika) nie są widoczne w portalu/programu powershell. Jednakże, jeśli zaplanowane zadania kończą się niepowodzeniem, [alert kopii zapasowej](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) jest wygenerowany i wyświetlana w portalu. [Korzystanie z usługi Azure Monitor](backup-azure-monitoring-use-azuremonitor.md) śledzić wszystkie zaplanowane zadania i inne istotne informacje.

Użytkownicy mogą śledzić operacji ad hoc/użytkownika wyzwalane za pomocą identyfikatora JobID, które są zwracane w [dane wyjściowe](#on-demand-backup) zadań asynchronicznych, takie jak Kopia zapasowa. Użyj [Get AzRecoveryServicesBackupJobDetail](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail) PS polecenia cmdlet, aby śledzić zadania i jego szczegóły.

````powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
````

Aby uzyskać listę zadań ad hoc i ich stany z usługi Azure Backup, należy użyć [Get AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS polecenia cmdlet. Poniższy przykład zwraca wszystkie zadania SQL w toku.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Aby anulować zadanie w toku, należy użyć [Stop AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS polecenia cmdlet.

## <a name="managing-sql-always-on-availability-groups"></a>Zarządzanie zawsze na grup dostępności serwera SQL

SQL zawsze włączone grupy dostępności, upewnij się, że [zarejestrować wszystkie węzły](#registering-the-sql-vm) grupy dostępności (grupy dostępności). Po zakończeniu rejestracji dla wszystkich węzłów obiektu grupy dostępności SQL logicznie jest tworzony w elementy podlegające ochronie. Bazy danych w obszarze grupy dostępności SQL będzie wyświetlane jako "Baza danych SQL". Węzły będą wyświetlani jako autonomicznego wystąpienia i bazy danych SQL domyślne w ramach nich będzie wyświetlana jako także baz danych SQL.

Na przykład, załóżmy, że SQL grupy dostępności zawiera dwa węzły: bazy danych grupy dostępności SQL "sql-server-0" i "sql-server-1" do 1. Gdy oba te węzły są zarejestrowane, jeśli użytkownik [Wyświetla listę elementów, które można objąć ochroną](#fetching-sql-dbs), wyświetlane są następujące składniki

1. Obiekt grupy dostępności SQL — które można objąć ochroną typu jako SQLAvailabilityGroup elementu
2. AG SQL DB — typ elementu z możliwością objęcia ochroną jako baza danych SQL
3. Typ elementów podlegających ochronie SQL-server-0 - SQLInstance
4. Typ elementów podlegających ochronie SQL-server-1 - SQLInstance
5. Wszelkie domyślne baz danych SQL (master, model, msdb) w obszarze sql-server-0 - elementów podlegających ochronie wpisać jako baza danych SQL
6. Wszelkie domyślne baz danych SQL (master, model, msdb) w obszarze sql-server-1 - elementów podlegających ochronie wpisać jako baza danych SQL

SQL server — 0, 1 programu sql server będzie również wyświetlana jako "AzureVMAppContainer" kiedy [kopii zapasowej kontenery są wymienione](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0).

Wystarczy pobrać odpowiedniej bazy danych SQL, aby [Włącz wykonywanie kopii zapasowej](#configuring-backup) i [kopię zapasową adhoc](#on-demand-backup) i [Przywracanie poleceń cmdlet PS](#restore-sql-dbs) są identyczne.
