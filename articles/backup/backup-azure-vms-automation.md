---
title: Generowanie kopii zapasowych i odzyskiwanie maszyn wirtualnych platformy Azure za pomocą programu PowerShell
description: W tym artykule opisano sposób tworzenia kopii zapasowych i odzyskiwania maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup za pomocą programu PowerShell
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 733a06a84aa170f1361ea74d126ec9752586fce2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247984"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>Tworzenie kopii zapasowych i przywracanie maszyn wirtualnych platformy Azure za pomocą programu PowerShell

W tym artykule wyjaśniono, jak wykonać kopię zapasową i przywrócić maszynę wirtualną platformy Azure w magazynie usług [odzyskiwania kopii zapasowych platformy Azure](backup-overview.md) przy użyciu poleceń cmdlet programu PowerShell.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> * Utwórz magazyn usług odzyskiwania i ustaw kontekst przechowalni.
> * Definiowanie zasad tworzenia kopii zapasowych
> * Stosowanie zasad tworzenia kopii zapasowych w celu ochrony wielu maszyn wirtualnych
> * Wyzwalanie zadania tworzenia kopii zapasowej na żądanie dla chronionych maszyn wirtualnych Przed utworzeniem kopii zapasowej (lub ochrony) maszyny wirtualnej należy wykonać [wymagania wstępne,](backup-azure-arm-vms-prepare.md) aby przygotować środowisko do ochrony maszyn wirtualnych.

## <a name="before-you-start"></a>Przed rozpoczęciem

* [Dowiedz się więcej](backup-azure-recovery-services-vault-overview.md) o magazynach usług odzyskiwania.
* [Przejrzyj](backup-architecture.md#architecture-built-in-azure-vm-backup) architekturę kopii zapasowej maszyny Wirtualnej platformy Azure, [dowiedz się więcej o](backup-azure-vms-introduction.md) procesie tworzenia kopii zapasowej i [przejrzyj](backup-support-matrix-iaas.md) pomoc techniczną, ograniczenia i wymagania wstępne.
* Przejrzyj hierarchię obiektów programu PowerShell dla usług odzyskiwania.

## <a name="recovery-services-object-hierarchy"></a>Hierarchia obiektów usług odzyskiwania

Hierarchia obiektów jest podsumowana na poniższym diagramie.

![Hierarchia obiektów usług odzyskiwania](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Review the **Az.RecoveryServices** [cmdlet reference](https://docs.microsoft.com/powershell/module/Az.RecoveryServices/?view=azps-1.4.0) reference in the Azure library.

## <a name="set-up-and-register"></a>Konfigurowanie i rejestrowanie

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby rozpocząć:

1. [Pobierz najnowszą wersję programu PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)

2. Znajdź polecenia cmdlet programu Azure Backup PowerShell dostępne przez wpisanie następującego polecenia:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

    Pojawią się aliasy i polecenia cmdlet dla usługi Azure Backup, Usługi Azure Site Recovery i magazynu usług odzyskiwania. Poniższa ilustracja jest przykładem tego, co zobaczysz. Nie jest to pełna lista poleceń cmdlet.

    ![lista usług odzyskiwania](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Zaloguj się do konta platformy Azure przy użyciu **connect-AzAccount**. To polecenie cmdlet wyświetla stronę internetową z monitem o podanie poświadczeń konta:

    * Alternatywnie można dołączyć poświadczenia konta jako parametr w połącz **połączenie-AzAccount** polecenia cmdlet, przy użyciu **parametru -Credential.**
    * Jeśli jesteś partnerem CSP pracującym w imieniu dzierżawcy, określ klienta jako dzierżawcę, używając ich identyfikatora dzierżawcy lub nazwy domeny podstawowej dzierżawcy. Na przykład: **Connect-AzAccount -Tenant "fabrikam.com"**

4. Skojarz subskrypcję, której chcesz użyć z kontem, ponieważ konto może mieć kilka subskrypcji:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Jeśli używasz usługi Azure Backup po raz pierwszy, należy użyć polecenia cmdlet **[Register-AzResourceProvider,](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** aby zarejestrować dostawcę usługi Azure Recovery Service w ramach subskrypcji.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Można sprawdzić, czy dostawcy zarejestrowali się pomyślnie, używając następujących poleceń:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

    W danych wyjściowych **polecenia, RegistrationState** należy zmienić na **Zarejestrowany**. Jeśli nie, po prostu uruchom polecenie cmdlet **[Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** ponownie.

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Poniższe kroki prowadzą przez tworzenie magazynu usług odzyskiwania. Magazyn usług odzyskiwania różni się od magazynu kopii zapasowych.

1. Magazyn usług odzyskiwania jest zasobem Menedżera zasobów, więc należy umieścić go w grupie zasobów. Można użyć istniejącej grupy zasobów lub utworzyć grupę zasobów za pomocą polecenia cmdlet **[New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)** Podczas tworzenia grupy zasobów określ nazwę i lokalizację grupy zasobów.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Użyj polecenia cmdlet [New-AzRecoveryServicesVault,](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault?view=azps-1.4.0) aby utworzyć magazyn usług odzyskiwania. Należy określić tę samą lokalizację dla przechowalni, która została użyta dla grupy zasobów.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Określ typ nadmiarowości magazynu do użycia; można użyć [magazynu lokalnie nadmiarowego (LRS)](../storage/common/storage-redundancy-lrs.md) lub [magazynu geograficznie nadmiarowego (GRS).](../storage/common/storage-redundancy-grs.md) Poniższy przykład pokazuje -BackupStorageRedundancy opcja dla testvault jest ustawiona na GeoRedundant.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperty  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Wiele poleceń cmdlet usługi Azure Backup wymaga obiektu magazynu usługi Recovery Services jako danych wejściowych. Z tego powodu wygodne jest przechowywanie obiektu magazynu usługi Backup Recovery Services w zmiennej.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Wyświetlanie magazynów w ramach subskrypcji

Aby wyświetlić wszystkie magazyny w ramach subskrypcji, należy użyć [funkcji Get-AzRecoveryServicesVault:](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)

```powershell
Get-AzRecoveryServicesVault
```

Dane wyjściowe są podobne do poniższego przykładu, należy zauważyć, że są dostarczane skojarzone ResourceGroupName i Location.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="back-up-azure-vms"></a>Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure

Użyj magazynu usług odzyskiwania, aby chronić swoje maszyny wirtualne. Przed zastosowaniem ochrony należy ustawić kontekst przechowalni (typ danych chronionych w przechowalni) i zweryfikować zasady ochrony. Zasady ochrony to harmonogram po uruchomieniu zadań tworzenia kopii zapasowej i czas przechowywania każdej migawki kopii zapasowej.

### <a name="set-vault-context"></a>Ustawianie kontekstu przechowalni

Przed włączeniem ochrony na maszynie wirtualnej, należy użyć [Set-AzRecoveryServicesVaultContext,](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0) aby ustawić kontekst przechowalni. Po ustawieniu kontekst magazynu ma zastosowanie do wszystkich kolejnych poleceń cmdlet. Poniższy przykład ustawia kontekst przechowalni dla przechowalni, *testvault*.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "Contoso-docs-rg" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Pobieranie identyfikatora przechowalni

Planujemy przestarzałe ustawienie kontekstu magazynu zgodnie z wytycznymi programu Azure PowerShell. Zamiast tego można przechowywać lub pobierać identyfikator przechowalni i przekazać go do odpowiednich poleceń. Jeśli więc nie ustawisz kontekstu przechowalni lub chcesz określić polecenie do uruchomienia dla określonego magazynu, przekaż identyfikator przechowalni jako "-vaultID" do wszystkich odpowiednich poleceń w następujący sposób:

```powershell
$targetVault = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault"
$targetVault.ID
```

Lub

```powershell
$targetVaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

### <a name="modifying-storage-replication-settings"></a>Modyfikowanie ustawień replikacji magazynu

Polecenie [Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty) umożliwia ustawienie konfiguracji replikacji magazynu w magazynie na LRS/GRS

```powershell
Set-AzRecoveryServicesBackupProperty -Vault $targetVault -BackupStorageRedundancy GeoRedundant/LocallyRedundant
```

> [!NOTE]
> Nadmiarowość magazynu można modyfikować tylko wtedy, gdy nie ma żadnych elementów kopii zapasowej chronionych do magazynu.

### <a name="create-a-protection-policy"></a>Tworzenie zasad ochrony

Podczas tworzenia magazynu usługi Recovery Services jest on dostarczany z domyślnymi zasadami ochrony i przechowywania. Domyślne zasady ochrony wyzwalają zadanie tworzenia kopii zapasowej każdego dnia o określonej godzinie. Domyślne zasady przechowywania zachowują codzienny punkt odzyskiwania przez 30 dni. Za pomocą zasad domyślnych można szybko chronić maszynę wirtualną i edytować zasady później z różnymi szczegółami.

Użyj **[Get-AzRecoveryServicesBackupProtectionPolicy,](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)** aby wyświetlić zasady ochrony dostępne w magazynie. Tego polecenia cmdlet można użyć, aby uzyskać określone zasady lub wyświetlić zasady skojarzone z typem obciążenia. Poniższy przykład pobiera zasady dla typu obciążenia AzureVM.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

Dane wyjściowe są podobne do poniższego przykładu:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> Strefa czasowa pola BackupTime w programie PowerShell to UTC. Jednak gdy czas tworzenia kopii zapasowej jest wyświetlany w witrynie Azure portal, czas jest dostosowany do lokalnej strefy czasowej.
>
>

Zasady ochrony kopii zapasowej jest skojarzony z co najmniej jedną zasadą przechowywania. Zasady przechowywania określa, jak długo punkt odzyskiwania jest przechowywany przed jego usunięciem.

* Użyj [Get-AzRecoveryServicesBackupRetentionPolicyObject,](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) aby wyświetlić domyślne zasady przechowywania.
* Podobnie można użyć [Get-AzRecoveryServiceSBackupSchedulePolicyObject,](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) aby uzyskać domyślne zasady harmonogramu.
* Polecenie cmdlet [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) tworzy obiekt programu PowerShell, który przechowuje informacje o zasadach kopii zapasowej.
* Obiekty zasad harmonogramu i przechowywania są używane jako dane wejściowe do polecenia cmdlet New-AzRecoveryServiceBackupProtectionPolicy.

Domyślnie godzina rozpoczęcia jest zdefiniowana w obiekcie zasad harmonogramu. Użyj poniższego przykładu, aby zmienić czas rozpoczęcia na żądany czas rozpoczęcia. Żądany czas rozpoczęcia powinien być również w czasie UTC. Poniższy przykład zakłada, że żądana godzina rozpoczęcia to 01:00 AM UTC dla codziennych kopii zapasowych.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM" -VaultId $targetVault.ID
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Musisz podać czas rozpoczęcia tylko w 30 minut wielokrotności. W powyższym przykładzie może to być tylko "01:00:00" lub "02:30:00". Czas rozpoczęcia nie może być "01:15:00"

W poniższym przykładzie przechowuje zasady harmonogramu i zasady przechowywania w zmiennych. W przykładzie użyto tych zmiennych do zdefiniowania parametrów podczas tworzenia zasad ochrony *NewPolicy*.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM" -VaultId $targetVault.ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultId $targetVault.ID
```

Dane wyjściowe są podobne do poniższego przykładu:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Włączanie ochrony

Po zdefiniowaniu zasad ochrony nadal należy włączyć zasady dla elementu. Użyj [Enable-AzRecoveryServicesBackupProtection,](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) aby włączyć ochronę. Włączenie ochrony wymaga dwóch obiektów - elementu i zasad. Po skojarzeniu zasad z magazynem przepływ pracy kopii zapasowej jest wyzwalany w czasie zdefiniowanym w harmonogramie zasad.

> [!IMPORTANT]
> Podczas korzystania z PS, aby włączyć tworzenie kopii zapasowych dla wielu maszyn wirtualnych naraz, upewnij się, że jedna zasada nie ma więcej niż 100 maszyn wirtualnych skojarzonych z nim. Jest to [zalecana najlepsza praktyka.](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq#is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy) Obecnie klient PS nie jawnie zablokować, jeśli istnieje więcej niż 100 maszyn wirtualnych, ale czek jest planowane do dodania w przyszłości.

Poniższe przykłady umożliwiają ochronę elementu V2VM przy użyciu zasad NewPolicy. Przykłady różnią się w zależności od tego, czy maszyna wirtualna jest szyfrowana i jakiego typu szyfrowania.

Aby włączyć ochronę na **niezaszyfrowanych maszynach wirtualnych Menedżera zasobów:**

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Aby włączyć ochronę na zaszyfrowanych maszynach wirtualnych (szyfrowanych przy użyciu bek i KEK), należy udzielić usługi Azure Backup uprawnienia do odczytu kluczy i wpisów tajnych z magazynu kluczy.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Aby włączyć ochronę na **zaszyfrowanych maszynach wirtualnych (tylko szyfrowane przy użyciu bek)**, należy przyznać usługi Azure Backup uprawnienia do odczytu wpisów tajnych z magazynu kluczy.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

> [!NOTE]
> Jeśli używasz chmury azure dla instytucji rządowych, a następnie użyć wartości ff281ffe-705c-4f53-9f37-a40e6f2c68f3 dla parametru ServicePrincipalName w [Set-AzKeyVaultAccesPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) połączenie cmdlet.
>

## <a name="monitoring-a-backup-job"></a>Monitorowanie zadania tworzenia kopii zapasowej

Można monitorować długotrwałe operacje, takie jak zadania tworzenia kopii zapasowych, bez korzystania z witryny Azure portal. Aby uzyskać stan zadania w toku, należy użyć polecenia cmdlet [Get-AzRecoveryservicesBackupJob.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) To polecenie cmdlet pobiera zadania tworzenia kopii zapasowych dla określonego magazynu, a ten magazyn jest określony w kontekście przechowalni. Poniższy przykład pobiera stan zadania w toku jako tablicy i przechowuje stan w zmiennej $joblist.

```powershell
$joblist = Get-AzRecoveryservicesBackupJob –Status "InProgress" -VaultId $targetVault.ID
$joblist[0]
```

Dane wyjściowe są podobne do poniższego przykładu:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Zamiast sondowania tych zadań do ukończenia — co jest niepotrzebne dodatkowy kod — należy użyć polecenia cmdlet [Wait-AzRecoveryServicesBackupJob.](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) To polecenie cmdlet wstrzymuje wykonanie do momentu zakończenia zadania lub osiągnięcia określonej wartości limitu czasu.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200 -VaultId $targetVault.ID
```

## <a name="manage-azure-vm-backups"></a>Zarządzanie kopiami zapasowymi maszyn wirtualnych platformy Azure

### <a name="modify-a-protection-policy"></a>Modyfikowanie zasad ochrony

Aby zmodyfikować zasady ochrony, należy użyć [Set-AzRecoveryServiceBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) do modyfikowania SchedulePolicy lub RetentionPolicy obiektów.

#### <a name="modifying-scheduled-time"></a>Modyfikowanie zaplanowanego czasu

Podczas tworzenia zasad ochrony jest domyślnie przypisywany czas rozpoczęcia. Poniższe przykłady pokazują, jak zmodyfikować godzinę rozpoczęcia zasad ochrony.

````powershell
$SchPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z" (This is the time that the customer wants to start the backup)
$UtcTime = $UtcTime.ToUniversalTime()
$SchPol.ScheduleRunTimes[0] = $UtcTime
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -SchedulePolicy $SchPol -VaultId $targetVault.ID
````

#### <a name="modifying-retention"></a>Modyfikowanie retencji

W poniższym przykładzie zmienia retencji punktu odzyskiwania do 365 dni.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol -VaultId $targetVault.ID
```

#### <a name="configuring-instant-restore-snapshot-retention"></a>Konfigurowanie retencji migawek natychmiastowego przywracania

> [!NOTE]
> Począwszy od Az PS w wersji 1.6.0 można zaktualizować okres przechowywania migawki natychmiastowego przywracania w zasadach przy użyciu programu Powershell

````powershell
$bkpPol = Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
$bkpPol.SnapshotRetentionInDays=7
Set-AzRecoveryServicesBackupProtectionPolicy -policy $bkpPol -VaultId $targetVault.ID
````

Wartość domyślna będzie 2, użytkownik może ustawić wartość z min 1 i max 5. W przypadku cotygodniowych zasad tworzenia kopii zapasowych okres jest ustawiony na 5 i nie można go zmienić.

### <a name="trigger-a-backup"></a>Wyzwalanie kopii zapasowej

Użyj [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) wyzwolić zadanie tworzenia kopii zapasowej. Jeśli jest to początkowa kopia zapasowa, jest to pełna kopia zapasowa. Kolejne kopie zapasowe są kopiowane przyrostową. W poniższym przykładzie przyjmuje kopię zapasową maszyny Wirtualnej, która ma zostać zachowana przez 60 dni.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
$job = Backup-AzRecoveryServicesBackupItem -Item $item -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

Dane wyjściowe są podobne do poniższego przykładu:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> Strefa czasowa pól StartTime i EndTime w programie PowerShell to UTC. Jednak gdy czas jest wyświetlany w witrynie Azure portal, czas jest dostosowany do lokalnej strefy czasowej.
>
>

### <a name="change-policy-for-backup-items"></a>Zmienianie zasad dotyczących elementów kopii zapasowej

Użytkownik może zmodyfikować istniejące zasady lub zmienić zasady elementu kopii zapasowej z Policy1 na Policy2. Aby przełączyć zasady dla elementu kopii zapasowej, pobierz odpowiednie zasady i utwór kopii zapasowej i użyj polecenia [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) z elementem kopii zapasowej jako parametrem.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName> -VaultId $targetVault.ID
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType AzureVM -BackupManagementType AzureVM -Name "<BackupItemName>" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1 -VaultId $targetVault.ID
````

Polecenie czeka, aż kopia zapasowa konfiguracji zostanie ukończona i zwraca następujące dane wyjściowe.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
TestVM           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>Zatrzymywanie ochrony

#### <a name="retain-data"></a>Zachowywanie danych

Jeśli użytkownik chce zatrzymać ochronę, można użyć polecenia cmdlet [PS Disable-AzRecoveryServicesBackupProtection.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) Spowoduje to zatrzymanie zaplanowanych kopii zapasowych, ale dane, które do tej pory są przechowywane na zawsze.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Usuwanie danych kopii zapasowej

Aby całkowicie usunąć przechowywane dane kopii zapasowej w przechowalni, wystarczy dodać flagę /przełączenie "-RemoveRecoveryPoints" do [polecenia ochrony "wyłącz".](#retain-data)

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

## <a name="restore-an-azure-vm"></a>Przywracanie maszyny Wirtualnej platformy Azure

Istnieje istotna różnica między przywracanie maszyny Wirtualnej przy użyciu witryny Azure portal i przywracanie maszyny Wirtualnej przy użyciu programu PowerShell. W programie PowerShell operacja przywracania została zakończona po utworzeniu dysków i informacji o konfiguracji z punktu odzyskiwania. Operacja przywracania nie tworzy maszyny wirtualnej. Aby utworzyć maszynę wirtualną z dysku, zobacz sekcję [Utwórz maszynę wirtualną z przywróconych dysków](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Jeśli nie chcesz przywracać całej maszyny Wirtualnej, ale chcesz przywrócić lub odzyskać kilka plików z kopii zapasowej maszyny Wirtualnej platformy Azure, zapoznaj się z [sekcją odzyskiwania plików](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup).

> [!Tip]
> Operacja przywracania nie tworzy maszyny wirtualnej.
>
>

Na poniższej ilustracji przedstawiono hierarchię obiektów od recoveryservicesVault do BackupRecoveryPoint.

![Hierarchia obiektów usług odzyskiwania z programem BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Aby przywrócić dane kopii zapasowej, należy zidentyfikować element kopii zapasowej i punkt odzyskiwania, który przechowuje dane punktu w czasie. Użyj [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) przywrócić dane z magazynu do konta.

Podstawowe kroki przywracania maszyny Wirtualnej platformy Azure to:

* Wybierz maszynę wirtualną.
* Wybierz punkt odzyskiwania.
* Przywróć dyski.
* Utwórz maszynę wirtualną z dysków przechowywanych.

### <a name="select-the-vm"></a>Wybierz maszynę wirtualną

Aby uzyskać obiekt programu PowerShell, który identyfikuje odpowiedni element kopii zapasowej, należy rozpocząć od kontenera w magazynie i pracować w dół hierarchii obiektów. Aby wybrać kontener, który reprezentuje maszynę wirtualną, należy użyć polecenia cmdlet [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) i potoku, który jest poleceń cmdlet [Get-AzRecoveryServicesBackupItem.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem)

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point"></a>Wybieranie punktu odzyskiwania

Użyj polecenia cmdlet [Get-AzRecoveryServicesBackupRecoveryPoint,](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) aby wyświetlić listę wszystkich punktów odzyskiwania dla elementu kopii zapasowej. Następnie wybierz punkt odzyskiwania do przywrócenia. Jeśli nie masz pewności, który punkt odzyskiwania użyć, jest dobrą praktyką, aby wybrać najnowszy RecoveryPointType = AppConsistent punkt na liście.

W poniższym skrypcie zmienna **$rp**jest tablicą punktów odzyskiwania dla wybranego elementu kopii zapasowej z ostatnich siedmiu dni. Tablica jest sortowana w odwrotnej kolejności czasu z najnowszym punktem odzyskiwania w indeksie 0. Użyj standardowego indeksowania tablicy programu PowerShell, aby wybrać punkt odzyskiwania. W tym przykładzie $rp[0] wybiera najnowszy punkt odzyskiwania.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

Dane wyjściowe są podobne do poniższego przykładu:

```output
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

### <a name="restore-the-disks"></a>Przywracanie dysków

Użyj polecenia cmdlet [Restore-AzRecoveryServicesBackupItemItem,](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) aby przywrócić dane i konfigurację elementu kopii zapasowej do punktu odzyskiwania. Po zidentyfikowaniu punktu odzyskiwania użyj go jako wartości dla **parametru -RecoveryPoint.** W powyższej próbce **$rp[0]** był punktem odzyskiwania do wykorzystania. W poniższym przykładowym kodzie **$rp[0]** jest punktem odzyskiwania używanym do przywracania dysku.

Aby przywrócić dyski i informacje o konfiguracji:

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -VaultId $targetVault.ID
$restorejob
```

#### <a name="restore-managed-disks"></a>Przywracanie dysków zarządzanych

> [!NOTE]
> Jeśli wycofana maszyna wirtualna zarządzała dyskami i chcesz przywrócić je jako dyski zarządzane, wprowadziliśmy możliwość z modułu Programu Azure PowerShell RM w wersji 6.7.0. w dalszej
>
>

Podaj dodatkowy parametr **TargetResourceGroupName,** aby określić RG, do którego zostaną przywrócone dyski zarządzane.

> [!NOTE]
> Zdecydowanie zaleca się użycie parametru **TargetResourceGroupName** do przywracania dysków zarządzanych, ponieważ powoduje to znaczną poprawę wydajności. Ponadto od modułu 1.0 usługi Azure Powershell Az parametr ten jest obowiązkowy w przypadku przywracania z dyskami zarządzanymi
>
>

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID
```

Plik **VMConfig.JSON** zostanie przywrócony do konta magazynu, a dyski zarządzane zostaną przywrócone do określonego docelowego RG.

Dane wyjściowe są podobne do poniższego przykładu:

```output
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Użyj polecenia cmdlet [Wait-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) czekać na zadanie przywracania do wykonania.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Po zakończeniu zadania przywracania użyj polecenia cmdlet [Get-AzRecoveryServicesBackupJobDetails,](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) aby uzyskać szczegółowe informacje na temat operacji przywracania. Właściwość JobDetails ma informacje potrzebne do odbudowy maszyny Wirtualnej.

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob -VaultId $targetVault.ID
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob -VaultId $targetVault.ID
```

Po przywróceniu dysków przejdź do następnej sekcji, aby utworzyć maszynę wirtualną.

## <a name="replace-disks-in-azure-vm"></a>Zastępowanie dysków na maszynie Wirtualnej platformy Azure

Aby zastąpić dyski i informacje o konfiguracji, wykonaj poniższe czynności:

* Krok 1: [Przywracanie dysków](backup-azure-vms-automation.md#restore-the-disks)
* Krok 2: [Odłącz dysk danych za pomocą programu PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk#detach-a-data-disk-using-powershell)
* Krok 3: [Dołączanie dysku danych do maszyny Wirtualnej systemu Windows za pomocą programu PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps)

## <a name="create-a-vm-from-restored-disks"></a>Tworzenie maszyny wirtualnej z przywróconych dysków

Po przywróceniu dysków należy wykonać następujące czynności, aby utworzyć i skonfigurować maszynę wirtualną z dysku.

> [!NOTE]
>
> 1. Wymagany jest moduł AzureAz 3.0.0 lub nowszy. <br>
> 2. Aby utworzyć zaszyfrowane maszyny wirtualne z przywróconych dysków, rola platformy Azure musi mieć uprawnienia do wykonywania **akcji, Microsoft.KeyVault/vaults/deploy/action**. Jeśli twoja rola nie ma tego uprawnienia, utwórz rolę niestandardową za pomocą tej akcji. Aby uzyskać więcej informacji, zobacz [Role niestandardowe w usłudze Azure RBAC](../role-based-access-control/custom-roles.md). <br>
> 3. Po przywróceniu dysków można teraz uzyskać szablon wdrożenia, którego można bezpośrednio użyć do utworzenia nowej maszyny Wirtualnej. Nie więcej różnych poleceń cmdlet PS do tworzenia zarządzanych/niezarządzanych maszyn wirtualnych, które są szyfrowane/niezaszyfrowane.<br>
> <br>

### <a name="create-a-vm-using-the-deployment-template"></a>Tworzenie maszyny wirtualnej przy użyciu szablonu wdrożenia

Wynikowe szczegóły zadania podać identyfikator URI szablonu, który może być wyszukiwane i wdrażane.

```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $templateBlobURI = $properties["Template Blob Uri"]
```

Szablon nie jest bezpośrednio dostępny, ponieważ znajduje się pod kontem magazynu klienta i danym kontenerem. Aby uzyskać dostęp do tego szablonu, potrzebujemy pełnego adresu URL (wraz z tymczasowym tokenem Sygnatury dostępu współdzielonego).

1. Najpierw wyodrębnić nazwę szablonu z templateBlobURI. Format jest wymieniony poniżej. Operacji podziału w programie Powershell można użyć do wyodrębnienia ostatecznej nazwy szablonu z tego adresu URL.

```http
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

2. Następnie pełny adres URL może być generowany, jak wyjaśniono [tutaj](https://docs.microsoft.com/azure/azure-resource-manager/templates/secure-template-with-sas-token?tabs=azure-powershell#provide-sas-token-during-deployment).

```powershell
Set-AzCurrentStorageAccount -Name $storageAccountName -ResourceGroupName <StorageAccount RG name>
$templateBlobFullURI = New-AzStorageBlobSASToken -Container $containerName -Blob <templateName> -Permission r -FullUri
```

3. Wdrożyć szablon, aby utworzyć nową maszynę wirtualną, jak wyjaśniono [w tym miejscu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobFullURI -storageAccountType Standard_GRS
```

### <a name="create-a-vm-using-the-config-file"></a>Tworzenie maszyny Wirtualnej przy użyciu pliku konfiguracyjnego

W poniższej sekcji wymieniono kroki niezbędne do utworzenia maszyny Wirtualnej przy użyciu pliku "VMConfig".

> [!NOTE]
> Zdecydowanie zaleca się użycie szablonu wdrożenia opisanego powyżej w celu utworzenia maszyny Wirtualnej. Ta sekcja (punkty 1-6) zostanie wkrótce przestarzała.

1. Kwerenda przywrócona właściwości dysku dla szczegółów zadania.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Ustaw kontekst magazynu platformy Azure i przywróć plik konfiguracyjny JSON.

   ```powershell
   Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. Użyj pliku konfiguracyjnego JSON, aby utworzyć konfigurację maszyny Wirtualnej.

   ```powershell
   $vm = New-AzVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Podłącz dysk systemu operacyjnego i dyski z danymi. Ten krok zawiera przykłady dla różnych zarządzanych i zaszyfrowanych konfiguracji maszyn wirtualnych. Użyj przykładu, który pasuje do konfiguracji maszyny Wirtualnej.

* **Niezarządzanie i niezaszyfrowane maszyny wirtualne** — użyj poniższego przykładu dla niezarządzanych, niezaszyfrowanych maszyn wirtualnych.

```powershell
       Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
```

* **Niezarządzane i szyfrowane maszyny wirtualne z usługą Azure AD (tylko BEK)** — w przypadku niezarządzanych, zaszyfrowanych maszyn wirtualnych z usługą Azure AD (szyfrowanych tylko przy użyciu bek), należy przywrócić klucz tajny do magazynu kluczy, zanim będzie można dołączyć dyski. Aby uzyskać więcej informacji, zobacz [Przywracanie zaszyfrowanej maszyny wirtualnej z punktu odzyskiwania kopii zapasowej platformy Azure](backup-azure-restore-key-secret.md). W poniższym przykładzie pokazano, jak dołączyć dyski systemu operacyjnego i danych dla zaszyfrowanych maszyn wirtualnych. Podczas ustawiania dysku systemu operacyjnego należy wspomnieć o odpowiednim typie systemu operacyjnego.

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

* **Niezarządzane i szyfrowane maszyny wirtualne z usługą Azure AD (BEK i KEK)** — w przypadku niezarządzanych, zaszyfrowanych maszyn wirtualnych z usługą Azure AD (szyfrowanych przy użyciu bek i KEK) przywróć klucz i klucz tajny do magazynu kluczy przed dołączeniem dysków. Aby uzyskać więcej informacji, zobacz [Przywracanie zaszyfrowanej maszyny wirtualnej z punktu odzyskiwania kopii zapasowej platformy Azure](backup-azure-restore-key-secret.md). W poniższym przykładzie pokazano, jak dołączyć dyski systemu operacyjnego i danych dla zaszyfrowanych maszyn wirtualnych.

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

* **Niezarządzane i szyfrowane maszyny wirtualne bez usługi Azure AD (tylko BEK)** — w przypadku niezarządzanych, zaszyfrowanych maszyn wirtualnych bez usługi Azure AD (tylko szyfrowanych przy użyciu funkcji BEK), jeśli klucze źródłowe **Vault/secret nie są dostępne,** przywracaj wpisy tajne do magazynu kluczy, korzystając z procedury w [obszarze Przywracanie niezaszyfrowanej maszyny wirtualnej z punktu odzyskiwania kopii zapasowej platformy Azure.](backup-azure-restore-key-secret.md) Następnie wykonaj następujące skrypty, aby ustawić szczegóły szyfrowania na przywróconym obiekcie operacyjnym (ten krok nie jest wymagany dla obiektu blob danych). $dekurl można pobrać z przywróconego keyVault.

Poniższy skrypt musi być wykonywany tylko wtedy, gdy klucz źródłowyVault/secret nie jest dostępny.

```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
```

Po **udostępnienie wpisów tajnych** i szczegóły szyfrowania są również ustawione na os blob, dołączyć dyski przy użyciu skryptu podanego poniżej.

Jeśli klucz źródłowyVault/secrets są już dostępne, powyższy skrypt nie musi być wykonywany.

```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
```

* **Niezarządzane i szyfrowane maszyny wirtualne bez usługi Azure AD (BEK i KEK)** — w przypadku niezarządzanych, zaszyfrowanych maszyn wirtualnych bez usługi Azure AD (szyfrowanych przy użyciu usługi BEK & KEK), jeśli **klucz źródłowyVault/key/secret nie są dostępne,** przywraca klucz i klucze tajne do magazynu kluczy przy użyciu procedury w [obszarze Przywracanie niezaszyfrowanej maszyny wirtualnej z punktu odzyskiwania kopii zapasowej platformy Azure](backup-azure-restore-key-secret.md). Następnie wykonaj następujące skrypty, aby ustawić szczegóły szyfrowania na przywróconym obiekcie operacyjnym (ten krok nie jest wymagany dla obiektu blob danych). $dekurl i $kekurl można pobrać z przywróconego keyVault.

Poniższy skrypt musi być wykonywany tylko wtedy, gdy klucz źródłowyVault/key/secret nie jest dostępny.

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

Po **klucz/klucz tajny są dostępne** i szczegóły szyfrowania są ustawione na os blob, dołączyć dyski przy użyciu skryptu podanego poniżej.

Jeśli klucz źródłowyVault/key/secrets są dostępne, powyższy skrypt nie musi być wykonywany.

```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
```

* **Zarządzane i niezaszyfrowane maszyny wirtualne** — w przypadku zarządzanych niezaszyfrowanych maszyn wirtualnych dołącz przywrócone dyski zarządzane. Aby uzyskać szczegółowe informacje, zobacz [Dołączanie dysku danych do maszyny Wirtualnej systemu Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md).

* **Zarządzane i szyfrowane maszyny wirtualne z usługą Azure AD (tylko BEK)** — w przypadku zarządzanych zaszyfrowanych maszyn wirtualnych z usługą Azure AD (szyfrowanych tylko przy użyciu bek) dołącz przywrócone dyski zarządzane. Aby uzyskać szczegółowe informacje, zobacz [Dołączanie dysku danych do maszyny Wirtualnej systemu Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md).

* **Zarządzane i szyfrowane maszyny wirtualne z usługą Azure AD (BEK i KEK)** — w przypadku zarządzanych zaszyfrowanych maszyn wirtualnych z usługą Azure AD (szyfrowanych przy użyciu bek i KEK) dołącz przywrócone dyski zarządzane. Aby uzyskać szczegółowe informacje, zobacz [Dołączanie dysku danych do maszyny Wirtualnej systemu Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md).

* **Zarządzane i szyfrowane maszyny wirtualne bez usługi Azure AD (tylko BEK)** — w przypadku zarządzanych, zaszyfrowanych maszyn wirtualnych bez usługi Azure AD (tylko szyfrowane przy użyciu usługi BEK), jeśli klucze źródłowe **Vault/secret nie są dostępne,** przywracają wpisy tajne do magazynu kluczy, korzystając z procedury w [obszarze Przywracanie niezaszyfrowanej maszyny wirtualnej z punktu odzyskiwania kopii zapasowej platformy Azure.](backup-azure-restore-key-secret.md) Następnie wykonaj następujące skrypty, aby ustawić szczegóły szyfrowania na przywróconym dysku systemu operacyjnego (ten krok nie jest wymagany dla dysku danych). $dekurl można pobrać z przywróconego keyVault.

Poniższy skrypt musi być wykonywany tylko wtedy, gdy klucz źródłowyVault/secret nie jest dostępny.  

```powershell
$dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
$keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
$diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
$encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
$encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
$encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
$encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
$encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
```

Po udostępnieniu wpisów tajnych i ustawieniu szczegółów szyfrowania na dysku systemu operacyjnego, aby dołączyć przywrócone dyski zarządzane, zobacz [Dołączanie dysku danych do maszyny Wirtualnej systemu Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md).

* **Zarządzane i szyfrowane maszyny wirtualne bez usługi Azure AD (BEK i KEK)** — dla zarządzanych, zaszyfrowanych maszyn wirtualnych bez usługi Azure AD (szyfrowanych przy użyciu usługi BEK & KEK), jeśli **źródło keyVault/key/secret nie są dostępne,** przywraca klucz i klucze tajne do przechowywania kluczy przy użyciu procedury w [obszarze Przywracanie niezaszyfrowanej maszyny wirtualnej z punktu odzyskiwania kopii zapasowej platformy Azure.](backup-azure-restore-key-secret.md) Następnie wykonaj następujące skrypty, aby ustawić szczegóły szyfrowania na przywróconym dysku systemu operacyjnego (ten krok nie jest wymagany dla dysków z danymi). $dekurl i $kekurl można pobrać z przywróconego keyVault.

Poniższy skrypt musi być wykonywany tylko wtedy, gdy klucz źródłowyVault/key/secret nie jest dostępny.

```powershell
$dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
$kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
$keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
$diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
$encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
$encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
$encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
$encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
$encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
$encryptionSettingsElement.KeyEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndKeyReference
$encryptionSettingsElement.KeyEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
$encryptionSettingsElement.KeyEncryptionKey.SourceVault.Id = $keyVaultId
$encryptionSettingsElement.KeyEncryptionKey.KeyUrl = $kekUrl
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
```

Po udostępnieniu klucza/wpisów tajnych i ustawieniu szczegółów szyfrowania na dysku systemu operacyjnego, aby dołączyć przywrócone dyski zarządzane, zobacz [Dołączanie dysku danych do maszyny Wirtualnej systemu Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md).

5. Ustaw ustawienia sieci.

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

7. Naciśnij rozszerzenie ADE.
   Jeśli rozszerzenia ADE nie są wypychane, dyski danych zostaną oznaczone jako niezaszyfrowane, więc wykonanie poniższych kroków jest obowiązkowe:

   * **Dla maszyny Wirtualnej z usługą Azure AD** — użyj następującego polecenia, aby ręcznie włączyć szyfrowanie dysków danych  

     **Tylko BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **BEK i KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **Dla maszyny Wirtualnej bez usługi Azure AD** — użyj następującego polecenia, aby ręcznie włączyć szyfrowanie dysków danych.

     Jeśli podczas wykonywania polecenia prosi o AADClientID, następnie należy zaktualizować program Azure PowerShell.

     **Tylko BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK i KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

> [!NOTE]
> Upewnij się, że ręcznie usunąć pliki JASON utworzone w ramach zaszyfrowanego procesu przywracania maszyny Wirtualnej dysku.

## <a name="restore-files-from-an-azure-vm-backup"></a>Przywracanie plików z kopii zapasowej maszyny Wirtualnej platformy Azure

Oprócz przywracania dysków można również przywrócić poszczególne pliki z kopii zapasowej maszyny Wirtualnej platformy Azure. Funkcja przywracania plików zapewnia dostęp do wszystkich plików w punkcie odzyskiwania. Zarządzaj plikami za pomocą Eksploratora plików tak samo jak w przypadku zwykłych plików.

Podstawowe kroki przywracania pliku z kopii zapasowej maszyny Wirtualnej platformy Azure to:

* Wybierz maszynę wirtualną
* Wybieranie punktu odzyskiwania
* Montowanie dysków punktu odzyskiwania
* Kopiowanie wymaganych plików
* Odinstalowywszę dysk

### <a name="select-the-vm"></a>Wybierz maszynę wirtualną

Aby uzyskać obiekt programu PowerShell, który identyfikuje odpowiedni element kopii zapasowej, należy rozpocząć od kontenera w magazynie i pracować w dół hierarchii obiektów. Aby wybrać kontener, który reprezentuje maszynę wirtualną, należy użyć polecenia cmdlet [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) i potoku, który jest poleceń cmdlet [Get-AzRecoveryServicesBackupItem.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem)

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point"></a>Wybieranie punktu odzyskiwania

Użyj polecenia cmdlet [Get-AzRecoveryServicesBackupRecoveryPoint,](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) aby wyświetlić listę wszystkich punktów odzyskiwania dla elementu kopii zapasowej. Następnie wybierz punkt odzyskiwania do przywrócenia. Jeśli nie masz pewności, który punkt odzyskiwania użyć, jest dobrą praktyką, aby wybrać najnowszy RecoveryPointType = AppConsistent punkt na liście.

W poniższym skrypcie zmienna **$rp**jest tablicą punktów odzyskiwania dla wybranego elementu kopii zapasowej z ostatnich siedmiu dni. Tablica jest sortowana w odwrotnej kolejności czasu z najnowszym punktem odzyskiwania w indeksie 0. Użyj standardowego indeksowania tablicy programu PowerShell, aby wybrać punkt odzyskiwania. W tym przykładzie $rp[0] wybiera najnowszy punkt odzyskiwania.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

Dane wyjściowe są podobne do poniższego przykładu:

```output
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

### <a name="mount-the-disks-of-recovery-point"></a>Montowanie dysków punktu odzyskiwania

Użyj polecenia cmdlet [Get-AzRecoveryServicesBackupRPRPMountScript,](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript) aby uzyskać skrypt do zainstalowania wszystkich dysków punktu odzyskiwania.

> [!NOTE]
> Dyski są montowane jako dyski podłączone przez iSCSI do komputera, na którym jest uruchamiany skrypt. Montaż następuje natychmiast i nie ponosisz żadnych opłat.
>
>

```powershell
Get-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

Dane wyjściowe są podobne do poniższego przykładu:

```output
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Uruchom skrypt na komputerze, na którym chcesz odzyskać pliki. Aby wykonać skrypt, należy wprowadzić podane hasło. Po dołączeniu dysków użyj Eksploratora plików Windows, aby przeglądać nowe woluminy i pliki. Aby uzyskać więcej informacji, zobacz artykuł Kopia [zapasowa, Odzyskiwanie plików z kopii zapasowej maszyny wirtualnej platformy Azure](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Odinstalowywszę dyski

Po skopiowaniu wymaganych plików użyj [disable-AzRecoveryServicesBackupRPMountScript,](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) aby odinstalować dyski. Pamiętaj, aby odinstalować dyski, aby dostęp do plików punktu odzyskiwania został usunięty.

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

## <a name="next-steps"></a>Następne kroki

Jeśli wolisz używać programu PowerShell do angażowania się w zasoby platformy Azure, zobacz artykuł programu PowerShell [Wdrażanie i zarządzanie kopią zapasową dla systemu Windows Server](backup-client-automation.md). Jeśli zarządzasz kopiami zapasowymi programu DPM, zobacz artykuł [Wdrażanie i zarządzanie tworzeniem kopii zapasowych dla programu DPM](backup-dpm-automation.md).
