---
title: Tworzenie kopii zapasowych i odzyskiwanie maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup przy użyciu programu PowerShell
description: Opisuje sposób tworzenia kopii zapasowej i odzyskiwanie maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup przy użyciu programu PowerShell
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: raynew
ms.openlocfilehash: 85e7b40778305395bb0f4a9403b4aeafc4607654
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565698"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>Tworzenie kopii zapasowej i przywracanie maszyn wirtualnych platformy Azure przy użyciu programu PowerShell

W tym artykule wyjaśniono, jak utworzyć kopię zapasową i przywrócić Maszynę wirtualną platformy Azure w [kopia zapasowa Azure](backup-overview.md) magazyn usługi Recovery Services za pomocą poleceń cmdlet programu PowerShell.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Utwórz magazyn usługi Recovery Services i Ustaw kontekst magazynu.
> * Definiowanie zasad tworzenia kopii zapasowych
> * Stosowanie zasad tworzenia kopii zapasowych w celu ochrony wielu maszyn wirtualnych
> * Wyzwalacz zadania tworzenia kopii zapasowej na żądanie dla chronionych maszyn wirtualnych przed można utworzyć kopię zapasową (lub ochronę) maszyny wirtualnej, należy wykonać [wymagania wstępne](backup-azure-arm-vms-prepare.md) do przygotowania środowiska na potrzeby ochrony maszyn wirtualnych.

## <a name="before-you-start"></a>Przed rozpoczęciem

- [Dowiedz się więcej](backup-azure-recovery-services-vault-overview.md) dotyczących magazynów usługi Recovery Services.
- [Przejrzyj](backup-architecture.md#architecture-direct-backup-of-azure-vms) architektura do tworzenia kopii zapasowych maszyn wirtualnych platformy Azure, [Dowiedz się więcej o](backup-azure-vms-introduction.md) procesu tworzenia kopii zapasowej i [Przejrzyj](backup-support-matrix-iaas.md) pomocy technicznej, ograniczenia i wymagania wstępne.
- Przejrzyj hierarchii obiektów programu PowerShell dla usługi Recovery Services.

## <a name="recovery-services-object-hierarchy"></a>Hierarchia obiektów usług odzyskiwania

Hierarchia obiektów jest podsumowywane na poniższym diagramie.

![Hierarchia obiektów usług odzyskiwania](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Przegląd **Az.RecoveryServices** [informacje o poleceniach cmdlet](https://docs.microsoft.com/powershell/module/Az.RecoveryServices/?view=azps-1.4.0) odwołania w bibliotece platformy Azure.

## <a name="set-up-and-register"></a>Konfigurowanie i rejestrowanie

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby rozpocząć:

1. [Pobierz najnowszą wersję programu PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)

2. Znajdowanie dostępnych poleceń cmdlet programu PowerShell kopia zapasowa Azure, wpisując następujące polecenie:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

    Aliasy i polecenia cmdlet dla usługi Kopia zapasowa Azure, usługi Azure Site Recovery i magazyn usługi Recovery Services są wyświetlane. Poniższy rysunek jest przykładem zostaną wyświetlone. Nie jest pełną listę poleceń cmdlet.

    ![Lista usług Recovery Services](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Zaloguj się do subskrypcji platformy Azure, konta przy użyciu **Connect AzAccount**. To polecenie cmdlet powoduje wyświetlenie strony wyświetli monit o podanie poświadczeń konta:

    * Alternatywnie można uwzględnić poświadczeń konta jako parametru w **Connect AzAccount** polecenia cmdlet, za pomocą **— poświadczeń** parametru.
    * Jeśli jesteś partnerem CSP, Praca w imieniu dzierżawy, określenia klienta jako dzierżawca, za pomocą ich identyfikatora dzierżawy lub dzierżawy podstawowej nazwy domeny. Na przykład: **Połącz AzAccount-dzierżawy "fabrikam.com"**

4. Skojarz subskrypcję, której chcesz użyć przy użyciu konta, ponieważ konto może mieć kilka subskrypcji:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Jeśli używasz usługi Azure Backup po raz pierwszy, musisz użyć **[AzResourceProvider rejestru](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** polecenia cmdlet, aby zarejestrować dostawcę usługi Azure Recovery Service w ramach subskrypcji.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Aby sprawdzić, czy dostawców pomyślnie zarejestrowane, używając następujących poleceń:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

    W danych wyjściowych polecenia **RegistrationState** należy zmienić na **zarejestrowanej**. Jeśli nie, uruchom **[AzResourceProvider rejestru](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** ponownie polecenie cmdlet.


## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Następujące kroki przeprowadzą Cię przez proces tworzenia magazynu usługi Recovery Services. Magazyn usługi Recovery Services jest inny niż magazynu kopii zapasowych.

1. Magazyn usługi Recovery Services jest zasobu usługi Resource Manager, dlatego należy go umieścić w grupie zasobów. Możesz użyć istniejącej grupy zasobów lub Utwórz grupę zasobów za pomocą **[New AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)** polecenia cmdlet. Podczas tworzenia grupy zasobów, określ nazwę i lokalizację grupy zasobów.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Użyj [New AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault?view=azps-1.4.0) polecenia cmdlet, aby utworzyć magazyn usługi Recovery Services. Pamiętaj określić lokalizację tego samego magazynu, która była używana dla grupy zasobów.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Określenie typu nadmiarowości magazynu, które mają być używane; Możesz użyć [magazyn lokalnie nadmiarowy (LRS)](../storage/common/storage-redundancy-lrs.md) lub [magazyn geograficznie nadmiarowy (GRS)](../storage/common/storage-redundancy-grs.md). Poniższy przykład pokazuje, że ustawiono opcję - BackupStorageRedundancy testvault GeoRedundant.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperty  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Wiele poleceń cmdlet usługi Azure Backup wymaga obiektu magazynu usługi Recovery Services jako danych wejściowych. Z tego powodu wygodne jest przechowywanie obiektu magazynu usługi Backup Recovery Services w zmiennej.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Wyświetlanie magazynów w ramach subskrypcji

Aby wyświetlić wszystkie magazyny w ramach subskrypcji, użyj [Get AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0):

```powershell
Get-AzRecoveryServicesVault
```

Rezultat jest podobny do poniższego przykładu, zwróć uwagę, że znajdują się skojarzone parametrów ResourceGroupName i lokalizacji.

```
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="back-up-azure-vms"></a>Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure

Użyj magazynu usługi Recovery Services, aby chronić maszyny wirtualne. Przed zastosowaniem ochrony ustawić kontekst magazynu (typ danych chronionych w magazynie) i sprawdź zasady ochrony. Zasady ochrony jest harmonogram uruchamiania zadań tworzenia kopii zapasowej i jak długo są przechowywane każdego migawki kopii zapasowej.

### <a name="set-vault-context"></a>Ustaw kontekst magazynu

Przed włączeniem ochrony na maszynie Wirtualnej, użyj [AzRecoveryServicesVaultContext zestaw](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0) można ustawić kontekst magazynu. Po ustawieniu kontekst magazynu ma zastosowanie do wszystkich kolejnych poleceń cmdlet. W poniższym przykładzie ustawiono kontekst magazynu dla magazynu, *testvault*.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="modifying-storage-replication-settings"></a>Modyfikowanie ustawień replikacji magazynu

Użyj [AzRecoveryServicesBackupProperty zestaw](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty) polecenie, aby ustawić konfigurację magazynu replikacji magazynu LRS/GRS

```powershell
$vault= Get-AzRecoveryServicesVault -name "testvault"
Set-AzRecoveryServicesBackupProperty -Vault $vault -BackupStorageRedundancy GeoRedundant/LocallyRedundant
```

> [!NOTE]
> Nadmiarowość magazynu mogą być modyfikowane tylko wtedy, gdy istnieją żadnych elementów kopii zapasowych chronionych w magazynie.

### <a name="create-a-protection-policy"></a>Tworzenie zasad ochrony

Podczas tworzenia magazynu usługi Recovery Services jest on dostarczany z domyślnymi zasadami ochrony i przechowywania. Domyślne zasady ochrony wyzwalają zadanie tworzenia kopii zapasowej każdego dnia o określonej godzinie. Domyślne zasady przechowywania zachowują codzienny punkt odzyskiwania przez 30 dni. Aby zapewnić szybką ochronę maszyny Wirtualnej i edytować zasady, które później za pomocą różnych szczegółów, można użyć domyślne zasady.

Użyj **[Get AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)** umożliwiającą wyświetlenie zasad ochrony dostępne w magazynie. Można użyć tego polecenia cmdlet, aby uzyskać szczegółowe zasady lub aby wyświetlić zasady skojarzone z typem obciążenia. Poniższy przykład pobiera zasady dla obciążeń typu AzureVM.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
```

Dane wyjściowe są podobne do poniższego przykładu:

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> Strefa czasowa BackupTime pola w programie PowerShell jest czasem UTC. Podczas wykonywania kopii zapasowej jest wyświetlany w witrynie Azure portal, czas jest dostosowywany do lokalnej strefy czasowej.
>
>

Zasady tworzenia kopii zapasowej ochrony jest skojarzony z co najmniej jedne zasady przechowywania. Zasady przechowywania Określa, jak długo punkt odzyskiwania jest przechowywana, przed usunięciem.

- Użyj [Get AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) Aby wyświetlić domyślne zasady przechowywania.
- Podobnie można użyć [Get AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) uzyskać domyślne zasady harmonogramu.
- [New AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) polecenie cmdlet tworzy obiekt programu PowerShell, który zawiera informacje o zasadach tworzenia kopii zapasowej.
- Obiekty zasad harmonogram i okres przechowywania są używane jako dane wejściowe do polecenia cmdlet New-AzRecoveryServicesBackupProtectionPolicy.

Domyślnie czas rozpoczęcia jest zdefiniowany w obiekcie zasad harmonogramu. Skorzystaj z następującego przykładu, aby zmienić czas rozpoczęcia na żądany czas rozpoczęcia. Czas rozpoczęcia żądanego należy również w formacie UTC. Poniższym przykładzie przyjęto założenie, czas rozpoczęcia żądanego to 01:00 czasu UTC dla codziennych kopii zapasowych.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

Poniższy przykład zasad harmonogram i zasady przechowywania są przechowywane w zmiennych. W przykładzie użyto tych zmiennych, aby zdefiniować parametry podczas tworzenia zasad ochrony *NewPolicy*.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Dane wyjściowe są podobne do poniższego przykładu:

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Włączanie ochrony

Po zdefiniowaniu zasad ochrony, nadal musisz włączyć zasady dla elementu. Użyj [AzRecoveryServicesBackupProtection Włącz](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) do włączenia ochrony. Włączenie ochrony wymaga dwóch obiektów - element i zasad. Gdy zasady zostało skojarzone z magazynem, tworzenia kopii zapasowej przepływ pracy zostanie wyzwolony w czasie, zdefiniowane w harmonogram zasad.

Poniższe przykłady włączyć ochrony dla elementu V2VM, za pomocą zasad NewPolicy. Przykłady różnią się zależnie od tego, czy maszyna wirtualna jest zaszyfrowana i co typ szyfrowania.

Aby włączyć ochronę na **niezaszyfrowane maszyn wirtualnych usługi Resource Manager**:

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Aby włączyć ochronę na zaszyfrowanych maszyn wirtualnych (szyfrowane przy użyciu BEK i KEK), musisz udzielić uprawnień usługi Azure Backup do odczytu klucze i wpisy tajne z magazynu kluczy.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Aby włączyć ochronę na **zaszyfrowanych maszyn wirtualnych (szyfrowane tylko przy użyciu klucza szyfrowania bloków)** , musisz udzielić uprawnień usługi Azure Backup na odczyt kluczy tajnych w magazynie kluczy.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Jeśli używasz chmury Azure Government, należy użyć ff281ffe-705c-4f53-9f37-a40e6f2c68f3 wartość parametru elementu ServicePrincipalName w [AzKeyVaultAccessPolicy zestaw](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) polecenia cmdlet.
>

## <a name="monitoring-a-backup-job"></a>Monitorowanie zadania tworzenia kopii zapasowej

Możesz monitorować długotrwałych operacji, takich jak zadania tworzenia kopii zapasowej bez korzystania z witryny Azure portal. Aby uzyskać stan zadania w toku, użyj [Get AzRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) polecenia cmdlet. To polecenie cmdlet pobiera zadania tworzenia kopii zapasowej dla określonego magazynu i Magazyn ten jest określony w kontekst magazynu. Poniższy przykład pobiera stan zadania w toku jako tablicę i przechowuje stan w zmiennej $joblist.

```powershell
$joblist = Get-AzRecoveryservicesBackupJob –Status "InProgress"
$joblist[0]
```

Dane wyjściowe są podobne do poniższego przykładu:

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Zamiast sondowania te zadania na ukończenie — czyli niepotrzebnych dodatkowego kodu — użyj [AzRecoveryServicesBackupJob oczekiwania](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) polecenia cmdlet. To polecenie cmdlet wstrzymuje wykonywanie, dopóki zadanie zostanie ukończone lub określona wartość limitu czasu zostanie osiągnięty.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="manage-azure-vm-backups"></a>Zarządzanie kopiami zapasowymi maszyn wirtualnych platformy Azure

### <a name="modify-a-protection-policy"></a>Zmodyfikuj zasady ochrony

Aby zmodyfikować zasady ochrony, użyj [AzRecoveryServicesBackupProtectionPolicy zestaw](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) do modyfikowania obiektów SchedulePolicy lub zasadach RetentionPolicy.

#### <a name="modifying-scheduled-time"></a>Modyfikowanie zaplanowanym czasie

Podczas tworzenia zasad ochrony przypisano czas rozpoczęcia domyślnie. Poniższy przykład pokazuje, jak zmienić czas rozpoczęcia zasad ochrony.

````powershell
$SchPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z" (This is the time that the customer wants to start the backup)
$UtcTime = $UtcTime.ToUniversalTime()
$SchPol.ScheduleRunTimes[0] = $UtcTime
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -SchedulePolicy $SchPol
````

#### <a name="modifying-retention"></a>Modyfikowanie przechowywania

Poniższy przykład zmienia czas przechowywania punktu odzyskiwania do 365 dni.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

#### <a name="configuring-instant-restore-snapshot-retention"></a>Konfigurowanie przechowywania migawek natychmiastowe Przywracanie

> [!NOTE]
> Z poziomu PS Az wersji 1.6.0 lub nowszy jeden zaktualizować okresu przechowywania natychmiastowe Przywracanie migawki w zasadach przy użyciu programu Powershell

````powershell
PS C:\> $bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=7
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
````

Wartością domyślną będzie mieć wartość 2, użytkownik może ustawić wartość minimalna 1 i maksymalnie 5. Zasady kopii zapasowych co tydzień, okresie jest równa 5 i nie można zmienić.

### <a name="trigger-a-backup"></a>Wyzwalanie tworzenia kopii zapasowej

Użyj [AzRecoveryServicesBackupItem kopii zapasowej](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) wyzwalanie zadania tworzenia kopii zapasowej. Jeśli tworzenie początkowej kopii zapasowej, jest pełna kopia zapasowa. Kolejne kopie zapasowe wykonać kopie przyrostowe. Poniższy przykład pobiera maszyny Wirtualnej kopii zapasowej będą przechowywane przez 60 dni.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
$job = Backup-AzRecoveryServicesBackupItem -Item $item -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

Dane wyjściowe są podobne do poniższego przykładu:

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> Strefa czasowa StartTime i EndTime pól w programie PowerShell jest czasem UTC. Gdy czas jest wyświetlany w witrynie Azure portal, czas jest dostosowywany do lokalnej strefy czasowej.
>
>

### <a name="change-policy-for-backup-items"></a>Zmiana zasad dla elementów kopii zapasowych

Użytkownika można zmodyfikować istniejące zasady lub zmienić zasady elementu kopii zapasowej z zasada 1 na Zasada2. Aby przełączyć zasady dla elementu kopii zapasowej, po prostu pobierania odpowiednich zasad i utworzyć kopię zapasową elementu i użyj [AzRecoveryServices Włącz](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) polecenia elementu kopii zapasowej jako parametr.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType AzureVM -BackupManagementType AzureVM -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
````

Polecenie czeka, aż konfigurowania kopii zapasowej zostało zakończone i zwraca następujące dane wyjściowe.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
TestVM           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>Zatrzymaj ochronę

#### <a name="retain-data"></a>Przechowywanie danych

Jeśli użytkownik zamierza Zatrzymaj ochronę, mogą używać [AzRecoveryServicesBackupProtection Wyłącz](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS polecenia cmdlet. Spowoduje to zatrzymanie zaplanowanych kopii zapasowych, ale dane kopii zapasowej do teraz są zachowywane nieskończoność.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Usuwanie danych kopii zapasowej

Aby całkowicie usunąć przechowywać dane kopii zapasowej w magazynie, wystarczy dodać atrybut "-RemoveRecoveryPoints flagi/przełącznik, aby [Wyłącz polecenia ochrona](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

## <a name="restore-an-azure-vm"></a>Przywracanie maszyny Wirtualnej platformy Azure

Jest to istotna różnica między przywracania maszyny Wirtualnej przy użyciu witryny Azure portal i przywracania maszyny Wirtualnej przy użyciu programu PowerShell. Przy użyciu programu PowerShell operacja przywracania została zakończona, po utworzeniu dyski i informacje o konfiguracji z punktu odzyskiwania. Operacja przywracania nie tworzy maszynę wirtualną. Aby utworzyć maszynę wirtualną z dysku, zobacz sekcję [tworzenie maszyny Wirtualnej z przywróconych dysków](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Jeśli nie chcesz przywrócić całą maszynę Wirtualną, ale chcesz przywrócić lub odzyskać kilka plików z kopii zapasowej maszyny Wirtualnej platformy Azure, zapoznaj się [pliku sekcji odzyskiwania](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup).

> [!Tip]
> Operacja przywracania nie powoduje utworzenia maszyny wirtualnej.
>
>

Na poniższym rysunku przedstawiono hierarchię obiektów z RecoveryServicesVault do BackupRecoveryPoint.

![Hierarchia obiektów usług odzyskiwania przedstawiający BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Aby przywrócić dane kopii zapasowej, zidentyfikuj elementu kopii zapasowej i punkt odzyskiwania, która przechowuje dane w momencie. Użyj [AzRecoveryServicesBackupItem przywracania](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) przywrócić dane z magazynu do Twojego konta.

Podstawowe kroki, aby przywrócić Maszynę wirtualną platformy Azure są:

* Wybierz maszynę wirtualną.
* Wybierz punkt odzyskiwania.
* Przywróć dyski.
* Tworzenie maszyny Wirtualnej z dysków przechowywane.

### <a name="select-the-vm"></a>Wybierz maszynę Wirtualną

Aby uzyskać obiekt programu PowerShell, który identyfikuje element tej kopii zapasowej, uruchom z kontenera w magazynie, a metodą w dół hierarchii obiektów. Aby wybrać kontener, który reprezentuje maszynę Wirtualną, użyj [Get AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) polecenia cmdlet i przekazać ją do [Get AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) polecenia cmdlet.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Wybierz punkt odzyskiwania

Użyj [Get AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) polecenia cmdlet, aby wyświetlić listę wszystkich punktów odzyskiwania dla elementu kopii zapasowej. Następnie wybierz punkt odzyskiwania do przywrócenia. Jeśli wiesz, punkt odzyskiwania, którego chcesz użyć, jest dobrym rozwiązaniem wybierz najbardziej aktualną RecoveryPointType = AppConsistent punkt na liście.

W poniższym skrypcie zmiennej, **$rp**, jest tablicą punkty odzyskiwania dla wybranego elementu kopii zapasowej z ostatnich siedmiu dni. Tablica jest posortowana w kolejności odwrotnej względem czasie przy użyciu najnowszego punktu odzyskiwania pod indeksem 0. Użyj standardowych indeksowanie tablicy programu PowerShell, aby wybrać punkt odzyskiwania. W tym przykładzie $rp [0] wybiera najnowszego punktu odzyskiwania.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

Dane wyjściowe są podobne do poniższego przykładu:

```powershell
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="restore-the-disks"></a>Przywróć dyski

Użyj [AzRecoveryServicesBackupItem przywracania](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) polecenia cmdlet, aby przywrócić elementu kopii zapasowej danych i konfiguracji punktu odzyskiwania. Po zidentyfikowaniu punkt odzyskiwania, użyj go jako wartość pozycji **- RecoveryPoint** parametru. W powyższym przykładzie **$rp [0]** został punkt odzyskiwania, które chcesz użyć. W poniższym przykładowym kodzie **$rp [0]** jest punkt odzyskiwania na potrzeby przywracania na dysku.

Aby przywrócić dyski i informacje o konfiguracji:

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
$restorejob
```

#### <a name="restore-managed-disks"></a>Przywracanie dysków zarządzanych

> [!NOTE]
> Jeśli chcesz przywrócić je jako dyski zarządzane dyski zarządzane kopii maszyny Wirtualnej, firma Microsoft wprowadza możliwość z modułu usługi Azure RM PowerShell v 6.7.0. lub nowszy
>
>

Zapewnia dodatkowy parametr **TargetResourceGroupName** do określenia grupą zasobów, do którego zostanie przywrócona dysków zarządzanych.

> [!NOTE]
> Zdecydowanie zaleca się używać **TargetResourceGroupName** parametr przywracanie dysków zarządzanych, ponieważ powoduje to znaczne ulepszenia wydajności. Ponadto z programu Azure Powershell Az modułu 1.0 lub nowszy, ten parametr jest obowiązkowy w przypadku przywracania z dyskami zarządzanymi
>
>


```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks"
```

**VMConfig.JSON** plik zostanie przywrócony do konta magazynu i dyski zarządzane zostaną przywrócone z określoną docelową grupą zasobów.

Dane wyjściowe są podobne do poniższego przykładu:

```powershell
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Użyj [AzRecoveryServicesBackupJob oczekiwania](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) polecenia cmdlet, aby czekać na ukończenie zadania przywracania.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Po zakończeniu zadania przywracania, użyj [Get AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) polecenia cmdlet, aby uzyskać szczegółowe informacje o operacji przywracania. Właściwość JobDetails ma informacje wymagane do odbudowywania maszyny Wirtualnej.

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob
```

Po przywróceniu dyski, przejdź do następnej sekcji, aby utworzyć maszynę Wirtualną.

## <a name="replace-disks-in-azure-vm"></a>Zastąp dysków w maszynie Wirtualnej platformy Azure

Aby zastąpić dysków i informacje o konfiguracji, wykonaj następujące czynności:

- Krok 1: [Przywróć dyski](backup-azure-vms-automation.md#restore-the-disks)
- Krok 2: [Dołączanie dysku danych przy użyciu programu PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk#detach-a-data-disk-using-powershell)
- Krok 3: [Dołączanie dysku danych do maszyny Wirtualnej z Windows przy użyciu programu PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps)


## <a name="create-a-vm-from-restored-disks"></a>Tworzenie maszyny Wirtualnej z przywróconych dysków

Po przywróceniu dyski należy użyć następujące kroki, tworzenie i konfigurowanie maszyny wirtualnej z dysku.

> [!NOTE]
> Aby utworzyć zaszyfrowanych maszyn wirtualnych z przywróconych dysków, rolę na platformie Azure musi mieć uprawnienia do wykonania akcji, **Microsoft.KeyVault/vaults/deploy/action**. Jeśli Twoja rola nie ma to uprawnienie, należy utworzyć rolę niestandardową, za pomocą tej akcji. Aby uzyskać więcej informacji, zobacz [niestandardowych ról RBAC platformy Azure](../role-based-access-control/custom-roles.md).
>
>

> [!NOTE]
> Po przywróceniu dysków, możesz teraz uzyskać Szablon wdrożenia, które bezpośrednio służy do tworzenia nowej maszyny Wirtualnej. Nie ma więcej różnych PS polecenia cmdlet służące do tworzenia zarządzanych/niezarządzanych maszyny wirtualne, które są szyfrowane niezaszyfrowane.

Szczegóły zadania wynikowe zawiera identyfikator URI, który można tworzyć zapytania i wdrożyć szablon.

```powershell
   $properties = $details.properties
   $templateBlobURI = $properties["Template Blob Uri"]
```

Wystarczy go wdrożyć szablon umożliwiający utworzenie nowej maszyny Wirtualnej, zgodnie z objaśnieniem [tutaj](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobURI -storageAccountType Standard_GRS
```

W poniższej sekcji przedstawiono kroki niezbędne do utworzenia maszyny Wirtualnej przy użyciu pliku "VMConfig".

> [!NOTE]
> Zdecydowanie zaleca się użycie szablonu wdrożenia szczegóły przedstawiono powyżej, aby utworzyć Maszynę wirtualną. Wkrótce zostaną wycofane w tej sekcji (punkty 1 – 6).

1. Tworzenie zapytań o właściwości przywróconego dysku, aby uzyskać szczegóły zadania.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Ustaw kontekst magazynu platformy Azure i przywrócić pliku konfiguracji JSON.

   ```powershell
   Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. Aby utworzyć konfigurację maszyny Wirtualnej, należy użyć pliku konfiguracji JSON.

   ```powershell
   $vm = New-AzVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Dołącz dysk systemu operacyjnego i dysków z danymi. Ten krok obejmuje przykładów dla różnych zarządzane i konfiguracje maszyn wirtualnych szyfrowane. Skorzystaj z przykładu, który odpowiada konfiguracji maszyny Wirtualnej.

   * **Maszyny wirtualne niezarządzane i niezaszyfrowane** — skorzystaj z poniższego przykładu for niezarządzanego niezaszyfrowane maszyn wirtualnych.

       ```powershell
       Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
       ```

   * **Niezarządzane i zaszyfrowanych maszyn wirtualnych przy użyciu usługi Azure AD (tylko w przypadku klucza szyfrowania bloków)** -For niezarządzanego zaszyfrowanych maszyn wirtualnych z usługą Azure AD (szyfrowane tylko przy użyciu klucza szyfrowania bloków), należy przywrócić klucz tajny do magazynu kluczy, zanim będzie możliwe dołączenie dysków. Aby uzyskać więcej informacji, zobacz [przywracanie zaszyfrowanych maszyn wirtualnych z punktu odzyskiwania usługi Azure Backup](backup-azure-restore-key-secret.md). Poniższy przykład pokazuje, jak dołączyć dyski systemu operacyjnego i danych dla szyfrowanych maszyn wirtualnych. Podczas ustawiania dysku systemu operacyjnego, upewnij się wspomnieć o odpowiedni typ systemu operacyjnego.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
      {
       $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **Niezarządzane i zaszyfrowanych maszyn wirtualnych przy użyciu usługi Azure AD (BEK i KEK)** — w przypadku niezarządzanych zaszyfrowanych maszyn wirtualnych z usługą Azure AD (szyfrowane przy użyciu BEK i KEK), Przywracanie klucza i wpisu tajnego do magazynu kluczy przed dołączeniem dyski. Aby uzyskać więcej informacji, zobacz [przywracanie zaszyfrowanych maszyn wirtualnych z punktu odzyskiwania usługi Azure Backup](backup-azure-restore-key-secret.md). Poniższy przykład pokazuje, jak dołączyć dyski systemu operacyjnego i danych dla szyfrowanych maszyn wirtualnych.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
      ```

   * **Niezarządzane i zaszyfrowanych maszyn wirtualnych bez usługi Azure AD (tylko w przypadku klucza szyfrowania bloków)** — w przypadku niezarządzanych zaszyfrowanych maszyn wirtualnych bez usługi Azure AD (szyfrowane tylko przy użyciu klucza szyfrowania bloków), jeśli źródło **magazynu kluczy/wpisów tajnych są niedostępne** przywrócić wpisy tajne usługi key vault za pomocą procedury w [przywrócić maszynę wirtualną z protokołem szyfrowania z punktu odzyskiwania usługi Azure Backup](backup-azure-restore-key-secret.md). Następnie wykonaj następujące skrypty do ustawiania szczegółów szyfrowania w przywróconej obiekcie blob systemu operacyjnego (ten krok nie jest wymagana dla obiektu blob danych). $Dekurl mogą być pobierane z przywróconej magazynu kluczy.<br>

   Poniższego skryptu musi być wykonywane tylko wtedy, gdy magazyn kluczy/wpisów tajnych źródła nie jest dostępna.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```

    Po **wpisów tajnych są dostępne** i szczegółów szyfrowania również są ustawione w obiekcie Blob systemu operacyjnego, podłącz odpowiednie dyski za pomocą skryptu podane poniżej.<br>

    Jeśli dostępne są już magazyn kluczy lub kluczy tajnych źródła, następnie powyższy skrypt muszą nie można wykonać.

      ```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **Niezarządzane i zaszyfrowanych maszyn wirtualnych bez usługi Azure AD (BEK i KEK)** — w przypadku niezarządzanych zaszyfrowanych maszyn wirtualnych bez usługi Azure AD (szyfrowane przy użyciu BEK i KEK), jeśli źródło **keyVault/klucz/wpis tajny nie są dostępne** Przywracanie klucza i klucze tajne usługi key vault za pomocą procedury w [przywrócić maszynę wirtualną z protokołem szyfrowania z punktu odzyskiwania usługi Azure Backup](backup-azure-restore-key-secret.md). Następnie wykonaj następujące skrypty do ustawiania szczegółów szyfrowania w przywróconej obiekcie blob systemu operacyjnego (ten krok nie jest wymagana dla obiektu blob danych). $Dekurl i $kekurl mogą być pobierane z przywróconej magazynu kluczy.

   Poniższego skryptu musi być wykonywane tylko wtedy, gdy źródłowy magazyn kluczy/klucz/wpis tajny nie jest dostępna.

    ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""},""keyEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""keyUrl"":""$kekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```
   Po **klucza lub kluczy tajnych są dostępne** i szczegółów szyfrowania ustawionych w obiekcie Blob systemu operacyjnego, podłącz odpowiednie dyski za pomocą skryptu podane poniżej.

    Jeśli źródło/klucz/wpisów tajnych usługi keyVault są dostępne, następnie powyższy skrypt muszą nie można wykonać.

    ```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **Maszyny wirtualne zarządzane i niezaszyfrowane** — zarządzane maszyny wirtualne nie są szyfrowane, dołączanie przywróconych dysków zarządzanych. Aby uzyskać szczegółowe informacje, zobacz [dołączanie dysku danych do maszyny Wirtualnej Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md).

   * **Zarządzana i zaszyfrowanych maszyn wirtualnych z usługą Azure AD (tylko w przypadku klucza szyfrowania bloków)** — w przypadku zarządzanych zaszyfrowanych maszyn wirtualnych z usługą Azure AD (szyfrowane tylko przy użyciu klucza szyfrowania bloków), Dołącz przywróconych dysków zarządzanych. Aby uzyskać szczegółowe informacje, zobacz [dołączanie dysku danych do maszyny Wirtualnej Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md).

   * **Zarządzana i zaszyfrowanych maszyn wirtualnych z usługą Azure AD (BEK i KEK)** — w przypadku zarządzanych zaszyfrowanych maszyn wirtualnych z usługą Azure AD (szyfrowane przy użyciu BEK i KEK), Dołącz przywróconych dysków zarządzanych. Aby uzyskać szczegółowe informacje, zobacz [dołączanie dysku danych do maszyny Wirtualnej Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md).

   * **Zarządzane i zaszyfrowanych maszyn wirtualnych bez usługi Azure AD (tylko w przypadku klucza szyfrowania bloków)** — dla zarządzanych, zaszyfrowanych maszyn wirtualnych bez usługi Azure AD (szyfrowane tylko przy użyciu klucza szyfrowania bloków), jeśli źródło **magazynu kluczy/wpisów tajnych są niedostępne** przywrócić wpisy tajne usługi key vault przy użyciu procedury w programie [przywrócić maszynę wirtualną z protokołem szyfrowania z punktu odzyskiwania usługi Azure Backup](backup-azure-restore-key-secret.md). Następnie wykonaj następujące skrypty można ustawić szczegółów szyfrowania w przywróconego dysku systemu operacyjnego (ten krok nie jest wymagany dla dysku z danymi). $Dekurl mogą być pobierane z przywróconej magazynu kluczy.

     Poniższego skryptu musi być wykonywane tylko wtedy, gdy magazyn kluczy/wpisów tajnych źródła nie jest dostępna.  

     ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
      $diskupdateconfig = Set-AzDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
      Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
      ```

     Po wpisy tajne są dostępne i szczegółów szyfrowania są ustawione na dysku systemu operacyjnego, aby dołączyć przywróconych dysków zarządzanych, zobacz [dołączanie dysku danych do maszyny Wirtualnej Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md).

   * **Zarządzana i zaszyfrowanych maszyn wirtualnych bez usługi Azure AD (BEK i KEK)** — w przypadku zarządzanych zaszyfrowanych maszyn wirtualnych bez usługi Azure AD (szyfrowane przy użyciu BEK i KEK), jeśli źródło **keyVault/klucz/wpis tajny nie są dostępne** Przywracanie klucza i wpisy tajne klucza Magazyn, korzystając z procedury w [przywrócić maszynę wirtualną z protokołem szyfrowania z punktu odzyskiwania usługi Azure Backup](backup-azure-restore-key-secret.md). Następnie wykonaj następujące skrypty można ustawić szczegółów szyfrowania w przywróconego dysku systemu operacyjnego (ten krok nie jest wymagany dla dysku z danymi). $Dekurl i $kekurl mogą być pobierane z przywróconej magazynu kluczy.

   Poniższego skryptu musi być wykonywane tylko wtedy, gdy źródłowy magazyn kluczy/klucz/wpis tajny nie jest dostępna.

   ```powershell
     $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
     $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
     $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
     $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
     $diskupdateconfig = Set-AzDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
     $diskupdateconfig = Set-AzDiskUpdateKeyEncryptionKey -DiskUpdate $diskupdateconfig -KeyUrl $kekUrl -SourceVaultId $keyVaultId  
     Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    Po klucza lub kluczy tajnych są dostępne i szczegółów szyfrowania są ustawione na dysku systemu operacyjnego, aby dołączyć przywróconych dysków zarządzanych, zobacz [dołączanie dysku danych do maszyny Wirtualnej Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md).

5. Określ ustawienia sieciowe.

    ```powershell
    $nicName="p1234"
    $pip = New-AzPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzVirtualNetwork
    $vnet = Get-AzVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. Utwórz maszynę wirtualną.

    ```powershell  
    New-AzVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. Wypchnij ADE rozszerzenia.

   * **Dla maszyny Wirtualnej z usługą Azure AD** — Użyj następującego polecenia, aby ręcznie włączyć szyfrowanie dla dysków z danymi  

     **Tylko klucz szyfrowania bloków**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **BEK i KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **Dla maszyny Wirtualnej bez usługi Azure AD** — Użyj następującego polecenia, aby ręcznie włączyć szyfrowanie dla dysków z danymi.

     Jeśli na wypadek, gdyby podczas wykonywania polecenia organ AADClientID, musisz zaktualizować programu Azure PowerShell.

     **Tylko klucz szyfrowania bloków**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK i KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```


## <a name="restore-files-from-an-azure-vm-backup"></a>Przywracanie plików z kopii zapasowej maszyny Wirtualnej platformy Azure

Oprócz przywracanie dysków, można przywrócić pojedyncze pliki z kopii zapasowej maszyny Wirtualnej platformy Azure. Funkcje przywracania plików zapewnia dostęp do wszystkich plików w punkcie odzyskiwania. Zarządzanie plikami, które za pomocą Eksploratora plików, podobnie jak w przypadku zwykłych plików.

Podstawowe kroki, aby przywrócić plik z kopii zapasowej maszyny Wirtualnej platformy Azure są:

* Wybierz maszynę Wirtualną
* Wybierz punkt odzyskiwania
* Zainstaluj dyskach punktu odzyskiwania
* Skopiuj wymagane pliki
* Odinstaluj dysk

### <a name="select-the-vm"></a>Wybierz maszynę Wirtualną

Aby uzyskać obiekt programu PowerShell, który identyfikuje element tej kopii zapasowej, uruchom z kontenera w magazynie, a metodą w dół hierarchii obiektów. Aby wybrać kontener, który reprezentuje maszynę Wirtualną, użyj [Get AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) polecenia cmdlet i przekazać ją do [Get AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) polecenia cmdlet.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Wybierz punkt odzyskiwania

Użyj [Get AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) polecenia cmdlet, aby wyświetlić listę wszystkich punktów odzyskiwania dla elementu kopii zapasowej. Następnie wybierz punkt odzyskiwania do przywrócenia. Jeśli wiesz, punkt odzyskiwania, którego chcesz użyć, jest dobrym rozwiązaniem wybierz najbardziej aktualną RecoveryPointType = AppConsistent punkt na liście.

W poniższym skrypcie zmiennej, **$rp**, jest tablicą punkty odzyskiwania dla wybranego elementu kopii zapasowej z ostatnich siedmiu dni. Tablica jest posortowana w kolejności odwrotnej względem czasie przy użyciu najnowszego punktu odzyskiwania pod indeksem 0. Użyj standardowych indeksowanie tablicy programu PowerShell, aby wybrać punkt odzyskiwania. W tym przykładzie $rp [0] wybiera najnowszego punktu odzyskiwania.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

Dane wyjściowe są podobne do poniższego przykładu:

```powershell
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>Zainstaluj dyskach punktu odzyskiwania

Użyj [Get AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript) polecenia cmdlet, aby uzyskać skrypt, aby zainstalować wszystkie dyski punktu odzyskiwania.

> [!NOTE]
> Dyski są instalowane jako dyski iSCSI dołączona do maszyny, na którym skrypt jest uruchamiany. Instalowanie następuje natychmiast i nie powodują naliczania żadnych opłat.
>
>

```powershell
Get-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

Dane wyjściowe są podobne do poniższego przykładu:

```powershell
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Uruchom skrypt na maszynie, w której chcesz odzyskać pliki. Aby wykonać skrypt, należy wprowadzić podane hasło. Po są dołączone dyski, należy użyć Eksploratora plików Windows, aby przeglądać nowe woluminy i pliki. Aby uzyskać więcej informacji można znaleźć w artykule kopii zapasowej, [odzyskiwanie plików z kopii zapasowej maszyny wirtualnej platformy Azure](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Odinstaluj dyski

Po skopiowaniu wymaganych plików, użyj [AzRecoveryServicesBackupRPMountScript Wyłącz](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) odinstalował dyski. Pamiętaj odinstalowanie dysków, dzięki czemu dostęp do plików punkt odzyskiwania zostanie usunięty.

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

## <a name="next-steps"></a>Kolejne kroki

Jeśli wolisz, skontaktuj się z zasobami platformy Azure przy użyciu programu PowerShell, zapoznaj się z artykułem PowerShell [wdrażanie i zarządzanie nimi kopii zapasowych dla systemu Windows Server](backup-client-automation.md). Jeśli zarządzasz kopii zapasowych programu DPM, zapoznaj się z artykułem [wdrażanie i zarządzanie nimi kopii zapasowej programu DPM](backup-dpm-automation.md).
