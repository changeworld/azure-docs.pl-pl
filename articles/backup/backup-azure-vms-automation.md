---
title: Wdrażanie i zarządzanie nimi kopii zapasowych maszyn wirtualnych wdrożonych przez Menedżera zasobów, przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Wdrażanie i zarządzanie nimi kopii zapasowych na platformie Azure dla maszyn wirtualnych, wdrożone usługi Resource Manager przy użyciu programu PowerShell
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
ms.assetid: 68606e4f-536d-4eac-9f80-8a198ea94d52
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/20/2017
ms.author: markgal;trinadhk;pullabhk
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bac1e679aa46b280596ab09ba40da780c81cac5d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="use-azurermrecoveryservicesbackup-cmdlets-to-back-up-virtual-machines"></a>Tworzenie kopii zapasowych maszyn wirtualnych przy użyciu poleceń cmdlet AzureRM.RecoveryServices.Backup

W tym artykule przedstawiono sposób użycia poleceń cmdlet programu Azure PowerShell kopii zapasowej i odzyskiwania z magazynu usług odzyskiwania Azure maszyny wirtualnej (VM). Magazyn usług odzyskiwania jest zasobem usługi Azure Resource Manager i jest używany do ochrony danych i zasobów w usługach zarówno Azure Backup i Azure Site Recovery. Magazyn usług odzyskiwania służy do ochrony Azure Service Manager wdrożone maszyny wirtualne i usługi Azure Resource Manager wdrożone maszyny wirtualne.

> [!NOTE]
> Platforma Azure ma dwa modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Resource Manager i model klasyczny](../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule jest przeznaczona do użytku z maszyn wirtualnych utworzonych przy użyciu modelu Resource Manager.
>
>

W tym artykule przedstawiono przy użyciu programu PowerShell, aby chronić maszyny Wirtualnej i przywrócić dane z punktu odzyskiwania.

## <a name="concepts"></a>Pojęcia
Jeśli nie masz doświadczenia w usłudze Kopia zapasowa Azure, omówienie usługi, zapoznaj się [co to jest kopia zapasowa Azure?](backup-introduction-to-azure-backup.md) Przed rozpoczęciem upewnij się, że opisano podstawowe informacje o wymaganiach wstępnych potrzebne do pracy z usługi Kopia zapasowa Azure, ograniczenia i bieżące rozwiązanie tworzenia kopii zapasowej maszyny Wirtualnej.

Efektywne przy użyciu programu PowerShell, należy zrozumieć hierarchii obiektów i z których ma rozpoczynać się.

![Hierarchia obiektów usługi odzyskiwania](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Aby wyświetlić dokumentacji poleceń cmdlet programu AzureRm.RecoveryServices.Backup PowerShell, zobacz [kopia zapasowa Azure - polecenia cmdlet usług odzyskiwania](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) w bibliotece platformy Azure.

## <a name="setup-and-registration"></a>Instalację i rejestrację
Aby rozpocząć:

1. [Pobierz najnowszą wersję programu PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (minimalna wymagana wersja to: 1.4.0)

2. Znajdowanie dostępnych poleceń cmdlet programu PowerShell usługi Azure Backup, wpisując następujące polecenie:
    ```PS
    PS C:\> Get-Command *azurermrecoveryservices*
    CommandType     Name                                               Version    Source
    -----------     ----                                               -------    ------
    Cmdlet          Backup-AzureRmRecoveryServicesBackupItem           1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Disable-AzureRmRecoveryServicesBackupProtection    1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Enable-AzureRmRecoveryServicesBackupProtection     1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupContainer         1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupItem              1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupJob               1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupJobDetails        1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupManagementServer  1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
    Cmdlet          Get-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRMRecoveryServicesBackupRecoveryPoint     1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupRetentionPolic... 1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupSchedulePolicy... 1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
    Cmdlet          Get-AzureRmRecoveryServicesVaultSettingsFile       1.4.0      AzureRM.RecoveryServices
    Cmdlet          New-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          New-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
    Cmdlet          Remove-AzureRmRecoveryServicesProtectionPolicy     1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Remove-AzureRmRecoveryServicesVault                1.4.0      AzureRM.RecoveryServices
    Cmdlet          Restore-AzureRMRecoveryServicesBackupItem          1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Set-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
    Cmdlet          Set-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Set-AzureRmRecoveryServicesVaultContext            1.4.0      AzureRM.RecoveryServices
    Cmdlet          Stop-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Unregister-AzureRmRecoveryServicesBackupContainer  1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Unregister-AzureRmRecoveryServicesBackupManagem... 1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Wait-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
    ```
3. Przy użyciu konta logowania do platformy Azure **Login-AzureRmAccount**. To polecenie cmdlet powoduje wyświetlenie strony sieci web wyświetla monit o podanie poświadczeń konta: 
    - Alternatywnie można uwzględnić poświadczeń konta jako parametru w **Login-AzureRmAccount** polecenia cmdlet, za pomocą **-Credential** parametru.
    - W przypadku dostawcy usług Kryptograficznych partnera pracy imieniu dzierżawcy określenia klienta dzierżawcy, przy użyciu nazwy domeny głównej dla identyfikatora dzierżawcy lub dzierżawcy. Na przykład: **Login-AzureRmAccount-dzierżawy "fabrikam.com"**
4. Skojarz subskrypcję, której chcesz użyć przy użyciu konta, ponieważ konto może zawierać kilka subskrypcji:

    ```PS
    PS C:\> Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Jeśli korzystasz z usługi Kopia zapasowa Azure po raz pierwszy, należy użyć **[AzureRmResourceProvider rejestru](http://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** polecenia cmdlet, aby zarejestrować dostawcę usług odzyskiwania Azure w ramach subskrypcji.

    ```PS
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Backup"
    ```

6. Można sprawdzić, czy dostawców zarejestrowana pomyślnie, używając następujących poleceń:
    ```PS
    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Backup"
    ``` 
W danych wyjściowych polecenia **RegistrationState** należy przypisać **zarejestrowanej**. Jeśli nie, wystarczy ponownie uruchomić **[AzureRmResourceProvider rejestru](http://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** polecenia cmdlet przedstawionych powyżej.

Następujące zadania można zautomatyzować przy użyciu programu PowerShell:

* [Tworzenie magazynu usług odzyskiwania](backup-azure-vms-automation.md#create-a-recovery-services-vault)
* [Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure](backup-azure-vms-automation.md#back-up-azure-vms)
* [Wyzwalacz zadania tworzenia kopii zapasowej](backup-azure-vms-automation.md#trigger-a-backup-job)
* [Monitor zadania tworzenia kopii zapasowej](backup-azure-vms-automation.md#monitoring-a-backup-job)
* [Przywracanie maszyny Wirtualnej platformy Azure](backup-azure-vms-automation.md#restore-an-azure-vm)

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Poniższe kroki prowadzi przez proces tworzenia magazynu usług odzyskiwania. Magazyn usług odzyskiwania są inne niż magazynu kopii zapasowych.

1. Magazyn usług odzyskiwania jest zasobem Menedżera zasobów, dlatego należy umieścić w grupie zasobów. Użyj istniejącej grupy zasobów lub Utwórz nową grupę zasobów o **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup)** polecenia cmdlet. Podczas tworzenia grupy zasobów, określ nazwę i lokalizację dla grupy zasobów.  

    ```PS
    PS C:\> New-AzureRmResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Użyj **[AzureRmRecoveryServicesVault nowy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault)** , aby utworzyć magazyn usług odzyskiwania. Należy określić tę samą lokalizację dla magazynu, które było używane dla grupy zasobów.

    ```PS
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Określ typ nadmiarowość magazynu mają być używane; można użyć [lokalnie nadmiarowego magazynu (LRS)](../storage/common/storage-redundancy-lrs.md) lub [z magazynu geograficznie nadmiarowego magazynu (GRS)](../storage/common/storage-redundancy-grs.md). W poniższym przykładzie przedstawiono, że opcja - BackupStorageRedundancy testvault jest ustawiona na GeoRedundant.

    ```PS
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault -Name "testvault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Wiele poleceń cmdlet narzędzia Kopia zapasowa Azure wymaga obiektu magazynu usług odzyskiwania jako danych wejściowych. Z tego powodu jest wygodne do przechowywania obiektów magazynu usług odzyskiwania kopii zapasowej w zmiennej.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Wyświetlanie magazynów w ramach subskrypcji
Użyj **[Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault)** Aby wyświetlić listę wszystkich magazynów w bieżącej subskrypcji. Można użyć tego polecenia, aby sprawdzić, czy został utworzony nowy magazyn lub aby wyświetlić dostępne magazyny w subskrypcji.

Uruchom polecenie, Get-AzureRmRecoveryServicesVault, aby wyświetlić wszystkie magazyny do subskrypcji. W poniższym przykładzie przedstawiono informacje wyświetlane dla każdego magazynu.

```
PS C:\> Get-AzureRmRecoveryServicesVault
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="back-up-azure-vms"></a>Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure
Magazyn usług odzyskiwania umożliwia ochronę maszyn wirtualnych. Przed zastosowaniem ochrony Ustaw kontekst magazynu (typ danych chronionych w magazynie) i sprawdź zasady ochrony. Zasady ochrony jest Zaplanuj, kiedy uruchomić zadania tworzenia kopii zapasowej i jak długo są przechowywane każdego migawki kopii zapasowej.

### <a name="set-vault-context"></a>Ustaw kontekst magazynu
Przed ponownym włączeniem ochrony na maszynie Wirtualnej, użyj **[AzureRmRecoveryServicesVaultContext zestaw](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** można ustawić kontekstu magazynu. Po ustawieniu kontekst magazynu ma zastosowanie do wszystkich kolejnych poleceń cmdlet. Poniższy przykład przedstawia kontekst magazynu dla magazynu, *testvault*.

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name "testvault" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>Tworzenie zasad ochrony
Podczas tworzenia magazynu usług odzyskiwania jest dostarczany z domyślną ochronę i zasady przechowywania. Domyślne zasady ochrony wyzwala zadanie tworzenia kopii zapasowej każdego dnia o określonej godzinie. Domyślne zasady przechowywania zachowuje punkt odzyskiwania codziennie przez 30 dni. Zasada domyślna służy do szybko zapewnić ochronę maszyny Wirtualnej i edytować zasady później przy użyciu różnych szczegółów.

Użyj **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupprotectionpolicy)** Aby wyświetlić zasady ochrony w magazynie. Aby uzyskać szczegółowe zasady lub wyświetlić zasady skojarzone z typem obciążenie, można użyć tego polecenia cmdlet. Poniższy przykład pobiera zasady dla obciążeń typu AzureVM.

```
PS C:\> Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> Strefa czasowa BackupTime pola w programie PowerShell jest czasem UTC. Podczas wykonywania kopii zapasowej jest wyświetlana w portalu Azure, czas jest dostosowany do sieci lokalnej strefie czasowej.
>
>

Zasady tworzenia kopii zapasowej ochrony jest skojarzony z co najmniej jedne zasady przechowywania. Zasady przechowywania określają, jak długo punkt odzyskiwania jest przechowywana, przed usunięciem. Użyj **[Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupretentionpolicyobject)** Aby wyświetlić domyślne zasady przechowywania.  Podobnie można użyć **[Get-AzureRmRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupschedulepolicyobject)** uzyskać domyślne zasady harmonogramu. **[AzureRmRecoveryServicesBackupProtectionPolicy nowy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** polecenie cmdlet tworzy obiekt programu PowerShell, która przechowuje informacje o zasadach tworzenia kopii zapasowej. Harmonogram i przechowywania obiektów zasady są używane jako dane wejściowe **[AzureRmRecoveryServicesBackupProtectionPolicy nowy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** polecenia cmdlet. Poniższy przykład zasad harmonogramu i zasady przechowywania są przechowywane w zmiennych. W przykładzie użyto tych zmiennych definiuje parametry podczas tworzenia zasad ochrony, *NewPolicy*.

```
PS C:\> $schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```


### <a name="enable-protection"></a>Włączanie ochrony
Po zdefiniowaniu zasad tworzenia kopii zapasowej ochrony nadal należy włączyć zasady dla elementu. Użyj **[AzureRmRecoveryServicesBackupProtection Włącz](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection)** do włączenia ochrony. Włączenie ochrony wymaga dwóch obiektów - element i zasady. Gdy zasady został skojarzony z magazynem, tworzenia kopii zapasowej przepływ pracy zostanie wyzwolony w czasie zdefiniowane w harmonogram zasad.

Poniższy przykład umożliwia włączenie ochrony dla elementu V2VM, za pomocą zasad, NewPolicy. Aby włączyć ochronę na niezaszyfrowane maszyn wirtualnych Menedżera zasobów

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Aby włączyć ochronę na zaszyfrowanych maszyn wirtualnych (szyfrowane przy użyciu BEK i KEK), należy nadać uprawnienia usługi Kopia zapasowa Azure do odczytu z magazynu kluczy kluczy i kluczy tajnych.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Aby włączyć ochronę na szyfrowane maszyn wirtualnych (szyfrowane tylko przy użyciu BEK), należy nadać uprawnienia usługi Kopia zapasowa Azure na odczyt kluczy tajnych w magazynie kluczy.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Jeśli korzystasz z chmury Azure dla instytucji rządowych, użyj ff281ffe-705c-4f53-9f37-a40e6f2c68f3 wartość dla parametru **- ServicePrincipalName** w [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) polecenia cmdlet.
>
>

Klasycznych maszyn wirtualnych

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>Modyfikowanie zasad ochrony
Aby zmodyfikować zasady ochrony, użyj [AzureRmRecoveryServicesBackupProtectionPolicy zestaw](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy) Aby zmodyfikować obiekty SchedulePolicy lub RetentionPolicy.

Poniższy przykład umożliwia zmianę przechowywania punktu odzyskiwania do 365 dni.

```
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol.DailySchedule.DurationCountInDays = 365
PS C:\> $pol= Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

## <a name="trigger-a-backup"></a>Wyzwalacz kopią zapasową
Można użyć **[AzureRmRecoveryServicesBackupItem kopii zapasowej](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** do wyzwalania zadania tworzenia kopii zapasowej. Jeśli jest początkowa kopia zapasowa jest pełna kopia zapasowa. Tworzenie kolejnych kopii zapasowych zająć przyrostowej kopii. Należy użyć **[AzureRmRecoveryServicesVaultContext zestaw](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** można ustawić kontekstu magazynem przed wyzwalając zadanie tworzenia kopii zapasowej. W poniższym przykładzie założono, że magazynu kontekst został ustawiony.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup               InProgress            4/23/2016 5:00:30 PM                       cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> Strefa czasowa wartości StartTime i EndTime pól w programie PowerShell jest czasem UTC. Jednak gdy czas jest wyświetlana w portalu Azure, czas jest dostosowywana do sieci lokalnej strefie czasowej.
>
>

## <a name="monitoring-a-backup-job"></a>Monitorowanie zadania tworzenia kopii zapasowej
Długotrwałe operacje, takie jak zadania tworzenia kopii zapasowej, można monitorować, bez korzystania z portalu Azure. Aby uzyskać stan zadania w toku, należy użyć **[Get-AzureRmRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob)** polecenia cmdlet. To polecenie cmdlet pobiera zadania tworzenia kopii zapasowej dla określonego magazynu i że magazyn jest określona w kontekście magazynu. Poniższy przykład pobiera stan zadania w toku jako tablica i zapisuje stan w zmiennej $joblist.

```
PS C:\> $joblist = Get-AzureRmRecoveryservicesBackupJob –Status "InProgress"
PS C:\> $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Zamiast sondowania te zadania na zakończenie — czyli niepotrzebnych dodatkowy kod - użyj **[AzureRmRecoveryServicesBackupJob oczekiwania](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** polecenia cmdlet. To polecenie cmdlet wstrzymuje wykonywanie aż do zadanie zostało ukończone lub osiągnięto wartość określonego limitu czasu.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Przywracanie maszyny Wirtualnej platformy Azure
Ma klucza różnicy między przywracanie maszyny Wirtualnej przy użyciu portalu Azure i przywracanie maszyny Wirtualnej przy użyciu programu PowerShell. Przy użyciu programu PowerShell operacja przywracania została zakończona, po utworzeniu dyski i informacje o konfiguracji z punktu odzyskiwania. Jeśli chcesz przywrócić lub odzyskać kilka plików z kopii zapasowej maszyny Wirtualnej platformy Azure, zobacz [pliku sekcji odzyskiwania](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)

> [!NOTE]
> Operacja przywracania nie powoduje utworzenia maszyny wirtualnej.
>
>

Aby utworzyć maszynę wirtualną z dysku, zobacz sekcję [utworzyć maszynę Wirtualną z dysków przywróconej](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Podstawowe kroki, aby przywrócić maszyny Wirtualnej platformy Azure są:

* Wybierz maszynę Wirtualną
* Wybierz punkt odzyskiwania
* Przywracanie dysków
* Utworzyć maszynę Wirtualną z przechowywanych dysków

Na poniższym rysunku przedstawiono hierarchii obiektów z RecoveryServicesVault do BackupRecoveryPoint.

![Hierarchia obiektów usług odzyskiwania przedstawiający BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Aby przywrócić dane kopii zapasowej, Zidentyfikuj element kopii zapasowej i punktu odzyskiwania, która przechowuje dane w momencie. Użyj **[AzureRmRecoveryServicesBackupItem przywracania](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** polecenia cmdlet, aby przywrócić dane z magazynu do konta klienta.

### <a name="select-the-vm"></a>Wybierz maszynę Wirtualną
Aby uzyskać obiekt programu PowerShell, który identyfikuje element prawo kopii zapasowej, uruchom z kontenera w magazynie, a metodą w dół hierarchii obiektów. Aby wybrać kontener, który reprezentuje maszyny Wirtualnej, użyj **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** polecenia cmdlet i przekazać do **[Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** polecenia cmdlet.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Wybierz punkt odzyskiwania
Użyj **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** polecenia cmdlet, aby wyświetlić listę wszystkich punktów odzyskiwania dla kopii zapasowej elementu. Następnie wybierz punkt odzyskiwania, aby przywrócić. Jeśli nie wiesz, którego punktu odzyskiwania chcesz użyć, jest dobrym rozwiązaniem, aby wybrać najnowszych RecoveryPointType = punktu AppConsistent na liście.

W poniższym skrypcie zmiennej **$rp**, jest tablicą punkty odzyskiwania dla wybranego elementu kopii zapasowej z ostatnich siedmiu dni. Tablica jest sortowany w kolejności odwrotnej czasu z najnowszego punktu odzyskiwania pod indeksem 0. Użyj standardowych indeksowanie tablicy środowiska PowerShell i wybierz punkt odzyskiwania. W tym przykładzie $rp [0] wybiera najnowszego punktu odzyskiwania.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
PS C:\> $rp[0]
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
Użyj **[AzureRmRecoveryServicesBackupItem przywracania](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** polecenia cmdlet do przywrócenia kopii zapasowej elementu konfiguracji danych i do punktu odzyskiwania. Po zidentyfikowaniu punkt odzyskiwania, użyj go jako wartość **- RecoveryPoint** parametru. W poprzednim przykładowym kodzie **$rp [0]** został punktu odzyskiwania chcesz użyć. W poniższym przykładowym kodzie **$rp [0]** jest punkt odzyskiwania do użycia podczas przywracania na dysku.

Aby przywrócić informacje o dyskach i konfiguracji:

```
PS C:\> $restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
PS C:\> $restorejob
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Użyj **[AzureRmRecoveryServicesBackupJob oczekiwania](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** polecenia cmdlet, aby czekać na ukończenie zadania przywracania.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Po ukończeniu zadania przywracania, użyj **[Get-AzureRmRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjobdetails)** polecenia cmdlet, aby uzyskać szczegółowe informacje o operacji przywracania. Właściwość JobDetails zawiera informacje potrzebne do odbudowywania maszyny Wirtualnej.

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails -Job $restorejob
```

Po przywróceniu dyski, przejdź do następnej sekcji, aby utworzyć maszynę Wirtualną.

## <a name="create-a-vm-from-restored-disks"></a>Utwórz maszynę Wirtualną z przywróconą dysków
Po przywróceniu dyski te kroki umożliwiają tworzenie i konfigurowanie maszyny wirtualnej z dysku.

> [!NOTE]
> Aby utworzyć zaszyfrowany maszyn wirtualnych z przywróconą dysków, roli użytkownika Azure musi mieć uprawnienia do wykonania tej akcji, **Microsoft.KeyVault/vaults/deploy/action**. Jeśli rola użytkownika nie ma to uprawnienie, utworzyć niestandardową rolę za pomocą tej akcji. Aby uzyskać więcej informacji, zobacz [niestandardowych ról w Azure RBAC](../active-directory/role-based-access-control-custom-roles.md).
>
>

1. Wyślij zapytanie do właściwości dysku przywróconej szczegóły zadania.

  ```
  PS C:\> $properties = $details.properties
  PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
  PS C:\> $containerName = $properties["Config Blob Container Name"]
  PS C:\> $blobName = $properties["Config Blob Name"]
  ```

2. Ustaw kontekst magazynu Azure i przywrócić plik JSON konfiguracji.

    ```
    PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
    PS C:\> $destination_path = "C:\vmconfig.json"
    PS C:\> Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path
    PS C:\> $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
    ```

3. Plik JSON konfiguracji umożliwia utworzenie konfiguracji maszyny Wirtualnej.

    ```
   PS C:\> $vm = New-AzureRmVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
    ```

4. Dołączanie dysku systemu operacyjnego i dysków z danymi. W zależności od konfiguracji maszyn wirtualnych można znaleźć w odpowiedniej sekcji w celu wyświetlenia odpowiednich poleceń cmdlet:

    #### <a name="non-managed-non-encrypted-vms"></a>Które nie są zarządzane, niezaszyfrowane maszyny wirtualne

    Skorzystaj z poniższego przykładu niezarządzanych, niezaszyfrowane maszyn wirtualnych.

    ```
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
    PS C:\> foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
    {
    $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
    }
    ```

    #### <a name="non-managed-encrypted-vms-bek-only"></a>Zaszyfrowane, które nie są zarządzane maszyn wirtualnych (tylko BEK)

    Dla niezarządzanych, zaszyfrowane maszyn wirtualnych (szyfrowane tylko przy użyciu BEK) należy przywrócić klucz tajny do magazynu kluczy, przed dołączeniem dysków. Aby uzyskać więcej informacji, zobacz artykuł, [przywrócić zaszyfrowanych maszyny wirtualnej z punktu odzyskiwania kopia zapasowa Azure](backup-azure-restore-key-secret.md). Poniższy przykład pokazuje, jak dołączyć systemu operacyjnego i dysków z danymi dla zaszyfrowanych maszyn wirtualnych.

    ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

    #### <a name="non-managed-encrypted-vms-bek-and-kek"></a>Maszyny wirtualne zaszyfrowane, które nie są zarządzane (BEK i KEK)

    Dla niezarządzanych, zaszyfrowane maszyn wirtualnych (szyfrowane przy użyciu BEK i KEK) należy przywrócić klucz i klucz tajny do magazynu kluczy, przed dołączeniem dysków. Aby uzyskać więcej informacji, zobacz artykuł, [przywrócić zaszyfrowanych maszyny wirtualnej z punktu odzyskiwania kopia zapasowa Azure](backup-azure-restore-key-secret.md). Poniższy przykład pokazuje, jak dołączyć systemu operacyjnego i dysków z danymi dla zaszyfrowanych maszyn wirtualnych.

    ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

    #### <a name="managed-non-encrypted-vms"></a>Zarządzane niezaszyfrowane maszyny wirtualne

    Zarządzanych maszyn wirtualnych z systemem innym niż szyfrowane należy utworzyć dyski zarządzanego z magazynu obiektów blob, a następnie podłącz odpowiednie dyski. Aby uzyskać szczegółowe informacje, zapoznaj się z artykułem [dołączenie dysku danych do maszyny Wirtualnej systemu Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md). Następujący przykładowy kod przedstawia sposób dołączenia dysków z danymi dla zarządzanych niezaszyfrowane maszyn wirtualnych.

    ```
    PS C:\> $storageType = "StandardLRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
    }
    ```

    #### <a name="managed-encrypted-vms-bek-only"></a>Zarządzane, zaszyfrowanych maszyn wirtualnych (tylko BEK)

    Zarządzanych zaszyfrowanych maszyn wirtualnych (szyfrowane tylko przy użyciu BEK) należy utworzyć dyski zarządzanego z magazynu obiektów blob, a następnie podłącz odpowiednie dyski. Aby uzyskać szczegółowe informacje, zapoznaj się z artykułem [dołączenie dysku danych do maszyny Wirtualnej systemu Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md). Następujący przykładowy kod pokazuje, jak dołączyć dyski danych dla zarządzanych zaszyfrowanych maszyn wirtualnych.

     ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> $storageType = "StandardLRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

    #### <a name="managed-encrypted-vms-bek-and-kek"></a>Zarządzane, zaszyfrowanych maszyn wirtualnych (BEK i KEK)

    Zarządzanych zaszyfrowanych maszyn wirtualnych (szyfrowane przy użyciu BEK i KEK) należy utworzyć dyski zarządzanego z magazynu obiektów blob, a następnie podłącz odpowiednie dyski. Aby uzyskać szczegółowe informacje, zapoznaj się z artykułem [dołączenie dysku danych do maszyny Wirtualnej systemu Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md). Następujący przykładowy kod pokazuje, jak dołączyć dyski danych dla zarządzanych zaszyfrowanych maszyn wirtualnych.

     ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> $storageType = "StandardLRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

5. Określ ustawienia sieciowe.

    ```
    PS C:\> $nicName="p1234"
    PS C:\> $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    PS C:\> $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    PS C:\> $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    PS C:\> $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```
6. Utwórz maszynę wirtualną.

    ```    
    PS C:\> New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

## <a name="restore-files-from-an-azure-vm-backup"></a>Przywracanie plików z kopii zapasowej maszyny Wirtualnej Azure

Oprócz przywracaniu dysków, można przywrócić pojedyncze pliki z kopii zapasowej maszyny Wirtualnej platformy Azure. Przywracanie plików funkcji daje dostęp do wszystkich plików w punkcie odzyskiwania i może zarządzać nimi za pomocą Eksploratora plików w sposób jak normalne pliki.

Podstawowe kroki, aby przywrócić plik z kopii zapasowej maszyny Wirtualnej platformy Azure są:

* Wybierz maszynę Wirtualną
* Wybierz punkt odzyskiwania
* Zainstaluj dyskach punktu odzyskiwania
* Skopiuj wymagane pliki
* Odinstaluj obraz dysku


### <a name="select-the-vm"></a>Wybierz maszynę Wirtualną
Aby uzyskać obiekt programu PowerShell, który identyfikuje element prawo kopii zapasowej, uruchom z kontenera w magazynie, a metodą w dół hierarchii obiektów. Aby wybrać kontener, który reprezentuje maszyny Wirtualnej, użyj **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** polecenia cmdlet i przekazać do **[Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** polecenia cmdlet.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Wybierz punkt odzyskiwania
Użyj **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** polecenia cmdlet, aby wyświetlić listę wszystkich punktów odzyskiwania dla kopii zapasowej elementu. Następnie wybierz punkt odzyskiwania, aby przywrócić. Jeśli nie wiesz, którego punktu odzyskiwania chcesz użyć, jest dobrym rozwiązaniem, aby wybrać najnowszych RecoveryPointType = punktu AppConsistent na liście.

W poniższym skrypcie zmiennej **$rp**, jest tablicą punkty odzyskiwania dla wybranego elementu kopii zapasowej z ostatnich siedmiu dni. Tablica jest sortowany w kolejności odwrotnej czasu z najnowszego punktu odzyskiwania pod indeksem 0. Użyj standardowych indeksowanie tablicy środowiska PowerShell i wybierz punkt odzyskiwania. W tym przykładzie $rp [0] wybiera najnowszego punktu odzyskiwania.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
PS C:\> $rp[0]
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

Użyj **[Get-AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprpmountscript)** polecenia cmdlet, aby uzyskać skrypt do zainstalowania na dyskach punktu odzyskiwania.

> [!NOTE]
> Dyski są instalowane jako dołączone dyski iSCSI do komputera, na którym skrypt jest uruchamiany. Dlatego jest niemal natychmiastowe i nie wpływa negatywnie jakichkolwiek opłat
>
>

```
PS C:\> Get-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]

OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```
Uruchom skrypt na komputerze, w której mają zostać odzyskane pliki. Należy podać hasło pokazanym powyżej można wykonać skryptu. Po są dołączone dyski, należy użyć w Eksploratorze plików systemu Windows, aby przejrzeć nowe woluminy i pliki. Więcej informacji można znaleźć w [pliku dokumentacją dotyczącą odzyskiwania](backup-azure-restore-files-from-vm.md)

### <a name="unmount-the-disks"></a>Odinstaluj dyski
Gdy wymagane pliki zostaną skopiowane, odinstaluj dyski za pomocą **[AzureRmRecoveryServicesBackupRPMountScript Wyłącz](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/disable-azurermrecoveryservicesbackuprpmountscript?view=azurermps-5.0.0)** polecenia cmdlet. Jest to zdecydowanie zalecane, ponieważ pozwala sprawdzić, usunięcie dostępu do plików punkt odzyskiwania

```
PS C:\> Disable-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```


## <a name="next-steps"></a>Kolejne kroki
Jeśli wolisz współpracować z zasobów platformy Azure przy użyciu programu PowerShell, zobacz artykuł programu PowerShell, [wdrażanie i zarządzanie kopia zapasowa systemu Windows Server](backup-client-automation.md). Jeśli zarządzasz kopii zapasowych programu DPM, zapoznaj się z artykułem [wdrażanie i zarządzanie kopii zapasowej programu DPM](backup-dpm-automation.md). Mieć obu tych artykułach wersji dla wdrożenia usługi Resource Manager oraz wdrożenia klasycznego.  
