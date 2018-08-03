---
title: Wdrażanie kopii zapasowych maszyn wirtualnych wdrożonych przy użyciu usługi Resource Manager oraz zarządzanie nimi przy użyciu programu PowerShell
description: Wdrażanie i zarządzanie nimi kopii zapasowych na platformie Azure maszyn wirtualnych wdrożonych przez Menedżera zasobów przy użyciu programu PowerShell
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 6/26/2018
ms.author: markgal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e88ff8ff591e7f7ce64f4dd01ec20a8167bb3c98
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438328"
---
# <a name="use-azurermrecoveryservicesbackup-cmdlets-to-back-up-virtual-machines"></a>Użyj poleceń cmdlet AzureRM.RecoveryServices.Backup do tworzenia kopii zapasowych maszyn wirtualnych

W tym artykule przedstawiono sposób tworzenia kopii i odzyskiwanie maszyn wirtualnych (VM) z magazynu usługi Recovery Services za pomocą poleceń cmdlet programu Azure PowerShell. Magazyn usługi Recovery Services jest zasobem usługi Azure Resource Manager i jest używany do ochrony danych i zasobów w usługach Azure Backup i Azure Site Recovery services. Magazyn usługi Recovery Services można użyć do ochrony maszyn wirtualnych wdrożonych w usłudze Azure Service Manager i maszyn wirtualnych wdrożonych przez usługi Azure Resource Manager.

> [!NOTE]
> Platforma Azure ma dwa modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Resource Manager i model klasyczny](../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł jest przeznaczony dla maszyn wirtualnych utworzonych za pomocą modelu usługi Resource Manager.
>
>

Ten artykuł przeprowadzi przy użyciu programu PowerShell, aby chronić maszynę Wirtualną i przywrócić dane z punktu odzyskiwania.

## <a name="concepts"></a>Pojęcia
Jeśli użytkownik nie jest zaznajomiony z usługą Azure Backup z omówieniem usługi, zapoznaj się z [co to jest usługa Azure Backup?](backup-introduction-to-azure-backup.md) Przed rozpoczęciem upewnij się, że opisano podstawowe informacje o wymaganiach wstępnych, potrzebne do pracy w usłudze Azure Backup i ograniczenia dotyczące bieżącego rozwiązania tworzenia kopii zapasowych maszyn wirtualnych.

Aby skutecznie za pomocą programu PowerShell, jest niezbędne do zrozumienia hierarchii obiektów i z których ma rozpoczynać się.

![Hierarchia obiektów usług odzyskiwania](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Aby wyświetlić Dokumentacja poleceń cmdlet AzureRm.RecoveryServices.Backup programu PowerShell, zobacz [usługi Azure Backup — poleceń cmdlet usług odzyskiwania](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) w bibliotece platformy Azure.

## <a name="setup-and-registration"></a>Konfiguracja i rejestracja
Aby rozpocząć:

1. [Pobierz najnowszą wersję programu PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (minimalna wymagana wersja to: 1.4.0)

2. Znajdowanie dostępnych poleceń cmdlet programu PowerShell kopia zapasowa Azure, wpisując następujące polecenie:
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
3. Zaloguj się do subskrypcji platformy Azure, konta przy użyciu **Connect-AzureRmAccount**. To polecenie cmdlet powoduje wyświetlenie strony wyświetli monit o podanie poświadczeń konta: 
    - Alternatywnie można uwzględnić poświadczeń konta jako parametru w **Connect-AzureRmAccount** polecenia cmdlet, za pomocą **-poświadczeń** parametru.
    - Jeśli jesteś partnerem CSP, Praca w imieniu dzierżawy, określenia klienta jako dzierżawca, za pomocą ich identyfikatora dzierżawy lub dzierżawy podstawowej nazwy domeny. Na przykład: **Connect-AzureRmAccount-dzierżawy "fabrikam.com"**
4. Skojarz subskrypcję, której chcesz użyć przy użyciu konta, ponieważ konto może mieć kilka subskrypcji:

    ```PS
    PS C:\> Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Jeśli używasz usługi Azure Backup po raz pierwszy, musisz użyć **[Register-AzureRmResourceProvider](http://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** polecenia cmdlet, aby zarejestrować dostawcę usługi Azure Recovery Service w ramach subskrypcji.

    ```PS
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Aby sprawdzić, czy dostawców pomyślnie zarejestrowane, używając następujących poleceń:
    ```PS
    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ``` 
W danych wyjściowych polecenia **RegistrationState** należy przypisać **zarejestrowanej**. Jeśli nie, wystarczy ponownie uruchomić **[Register-AzureRmResourceProvider](http://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** polecenia cmdlet przedstawionych powyżej.

Następujące zadania można zautomatyzować za pomocą programu PowerShell:

* [Tworzenie magazynu usługi Recovery Services](backup-azure-vms-automation.md#create-a-recovery-services-vault)
* [Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure](backup-azure-vms-automation.md#back-up-azure-vms)
* [Wyzwalanie zadania tworzenia kopii zapasowej](backup-azure-vms-automation.md#trigger-a-backup-job)
* [Monitorowanie zadania tworzenia kopii zapasowej](backup-azure-vms-automation.md#monitoring-a-backup-job)
* [Przywracanie maszyny Wirtualnej platformy Azure](backup-azure-vms-automation.md#restore-an-azure-vm)

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Następujące kroki przeprowadzą Cię przez proces tworzenia magazynu usługi Recovery Services. Magazyn usługi Recovery Services jest inny niż magazynu kopii zapasowych.

1. Magazyn usługi Recovery Services jest zasobu usługi Resource Manager, dlatego należy go umieścić w grupie zasobów. Możesz użyć istniejącej grupy zasobów lub Utwórz grupę zasobów za pomocą **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup)** polecenia cmdlet. Podczas tworzenia grupy zasobów, określ nazwę i lokalizację grupy zasobów.  

    ```PS
    PS C:\> New-AzureRmResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Użyj **[New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault)** polecenia cmdlet, aby utworzyć magazyn usługi Recovery Services. Pamiętaj określić lokalizację tego samego magazynu, która była używana dla grupy zasobów.

    ```PS
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Określenie typu nadmiarowości magazynu, które mają być używane; Możesz użyć [magazyn lokalnie nadmiarowy (LRS)](../storage/common/storage-redundancy-lrs.md) lub [magazyn geograficznie nadmiarowy (GRS)](../storage/common/storage-redundancy-grs.md). Poniższy przykład pokazuje, że ustawiono opcję - BackupStorageRedundancy testvault GeoRedundant.

    ```PS
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault -Name "testvault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Wiele poleceń cmdlet usługi Azure Backup wymaga obiektu magazynu usługi Recovery Services jako danych wejściowych. Z tego powodu wygodne jest przechowywanie obiektu magazynu usługi Backup Recovery Services w zmiennej.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Wyświetlanie magazynów w ramach subskrypcji
Użyj **[Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault)** Aby wyświetlić listę wszystkich magazynów w bieżącej subskrypcji. Można użyć tego polecenia, aby sprawdzić, czy został utworzony nowy magazyn lub aby wyświetlić dostępne magazyny w ramach subskrypcji.

Uruchom polecenie, Get-AzureRmRecoveryServicesVault, aby wyświetlić wszystkie magazyny w ramach subskrypcji. Poniższy przykład pokazuje informacje wyświetlane dla każdego magazynu.

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
Użyj magazynu usługi Recovery Services, aby chronić maszyny wirtualne. Przed zastosowaniem ochrony ustawić kontekst magazynu (typ danych chronionych w magazynie) i sprawdź zasady ochrony. Zasady ochrony jest harmonogram uruchamiania zadań tworzenia kopii zapasowej i jak długo są przechowywane każdego migawki kopii zapasowej.

### <a name="set-vault-context"></a>Ustaw kontekst magazynu
Przed włączeniem ochrony na maszynie Wirtualnej, użyj **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** można ustawić kontekst magazynu. Po ustawieniu kontekst magazynu ma zastosowanie do wszystkich kolejnych poleceń cmdlet. W poniższym przykładzie ustawiono kontekst magazynu dla magazynu, *testvault*.

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name "testvault" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>Tworzenie zasad ochrony
Podczas tworzenia magazynu usługi Recovery Services jest on dostarczany z domyślnymi zasadami ochrony i przechowywania. Domyślne zasady ochrony wyzwalają zadanie tworzenia kopii zapasowej każdego dnia o określonej godzinie. Domyślne zasady przechowywania zachowują codzienny punkt odzyskiwania przez 30 dni. Aby zapewnić szybką ochronę maszyny Wirtualnej i edytować zasady, które później za pomocą różnych szczegółów, można użyć domyślne zasady.

Użyj **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupprotectionpolicy)** umożliwiającą wyświetlenie zasad ochrony w magazynie. Można użyć tego polecenia cmdlet, aby uzyskać szczegółowe zasady lub aby wyświetlić zasady skojarzone z typem obciążenia. Poniższy przykład pobiera zasady dla obciążeń typu AzureVM.

```
PS C:\> Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> Strefa czasowa BackupTime pola w programie PowerShell jest czasem UTC. Podczas wykonywania kopii zapasowej jest wyświetlany w witrynie Azure portal, czas jest dostosowywany do lokalnej strefy czasowej.
>
>

Zasady tworzenia kopii zapasowej ochrony jest skojarzony z co najmniej jedne zasady przechowywania. Zasady przechowywania Określa, jak długo punkt odzyskiwania jest przechowywana, przed usunięciem. Użyj **[Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupretentionpolicyobject)** Aby wyświetlić domyślne zasady przechowywania.  Podobnie można użyć **[Get AzureRmRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupschedulepolicyobject)** uzyskać domyślne zasady harmonogramu. **[New AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** polecenie cmdlet tworzy obiekt programu PowerShell, który zawiera informacje o zasadach tworzenia kopii zapasowej. Obiekty zasad harmonogram i okres przechowywania są używane jako dane wejściowe **[New AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** polecenia cmdlet. Poniższy przykład zasad harmonogram i zasady przechowywania są przechowywane w zmiennych. W przykładzie użyto tych zmiennych, aby zdefiniować parametry podczas tworzenia zasad ochrony *NewPolicy*.

```
PS C:\> $schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```


### <a name="enable-protection"></a>Włączanie ochrony
Po zdefiniowaniu zasad tworzenia kopii zapasowej ochrony, nadal musisz włączyć zasady dla elementu. Użyj **[Enable-AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection)** do włączenia ochrony. Włączenie ochrony wymaga dwóch obiektów - element i zasad. Gdy zasady zostało skojarzone z magazynem, tworzenia kopii zapasowej przepływ pracy zostanie wyzwolony w czasie, zdefiniowane w harmonogram zasad.

Poniższy przykład umożliwia włączenie ochrony dla elementu V2VM, za pomocą zasad NewPolicy. Aby włączyć ochronę na maszynach wirtualnych usługi Resource Manager z protokołem szyfrowania

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Aby włączyć ochronę na zaszyfrowanych maszyn wirtualnych (szyfrowane przy użyciu BEK i KEK), należy przyznać uprawnienia usługi Azure Backup do odczytu klucze i wpisy tajne z magazynu kluczy.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Aby włączyć ochronę w zaszyfrowanych maszyn wirtualnych (szyfrowane tylko przy użyciu klucza szyfrowania bloków), należy przyznać uprawnienia usługi Azure Backup na odczyt kluczy tajnych w magazynie kluczy.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Jeśli używasz chmury Azure Government, należy użyć ff281ffe-705c-4f53-9f37-a40e6f2c68f3 wartość dla parametru **- ServicePrincipalName** w [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) polecenia cmdlet.
>
>

Dla klasycznych maszyn wirtualnych

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>Zmodyfikuj zasady ochrony
Aby zmodyfikować zasady ochrony, użyj [Set-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy) do modyfikowania obiektów SchedulePolicy lub zasadach RetentionPolicy.

Poniższy przykład zmienia czas przechowywania punktu odzyskiwania do 365 dni.

```
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol.DailySchedule.DurationCountInDays = 365
PS C:\> $pol= Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

## <a name="trigger-a-backup"></a>Wyzwalanie tworzenia kopii zapasowej
Możesz użyć **[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** wyzwalanie zadania tworzenia kopii zapasowej. Jeśli tworzenie początkowej kopii zapasowej, jest pełna kopia zapasowa. Kolejne kopie zapasowe wykonać kopie przyrostowe. Należy użyć **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** można ustawić kontekst magazynu przed wyzwoleniem zadania tworzenia kopii zapasowej. W poniższym przykładzie założono, że ustawiono kontekst magazynu.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup               InProgress            4/23/2016 5:00:30 PM                       cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> Strefa czasowa StartTime i EndTime pól w programie PowerShell jest czasem UTC. Gdy czas jest wyświetlany w witrynie Azure portal, czas jest dostosowywany do lokalnej strefy czasowej.
>
>

## <a name="monitoring-a-backup-job"></a>Monitorowanie zadania tworzenia kopii zapasowej
Możesz monitorować długotrwałych operacji, takich jak zadania tworzenia kopii zapasowej bez korzystania z witryny Azure portal. Aby uzyskać stan zadania w toku, użyj **[Get-AzureRmRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob)** polecenia cmdlet. To polecenie cmdlet pobiera zadania tworzenia kopii zapasowej dla określonego magazynu i Magazyn ten jest określony w kontekst magazynu. Poniższy przykład pobiera stan zadania w toku jako tablicę i przechowuje stan w zmiennej $joblist.

```
PS C:\> $joblist = Get-AzureRmRecoveryservicesBackupJob –Status "InProgress"
PS C:\> $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Zamiast sondowania te zadania na ukończenie — czyli niepotrzebnych dodatkowego kodu — użyj **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** polecenia cmdlet. To polecenie cmdlet wstrzymuje wykonywanie, dopóki zadanie zostanie ukończone lub określona wartość limitu czasu zostanie osiągnięty.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Przywracanie maszyny Wirtualnej platformy Azure
Brak klucza różnica między przywracania maszyny Wirtualnej przy użyciu witryny Azure portal i przywracania maszyny Wirtualnej przy użyciu programu PowerShell. Przy użyciu programu PowerShell operacja przywracania została zakończona, po utworzeniu dyski i informacje o konfiguracji z punktu odzyskiwania. Możesz przywrócić lub odzyskać kilku plików z kopii zapasowej maszyny Wirtualnej platformy Azure, zapoznaj się [sekcji odzyskiwania plików](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)

> [!NOTE]
> Operacja przywracania nie powoduje utworzenia maszyny wirtualnej.
>
>

Aby utworzyć maszynę wirtualną z dysku, zobacz sekcję [tworzenie maszyny Wirtualnej z przywróconych dysków](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Podstawowe kroki, aby przywrócić Maszynę wirtualną platformy Azure są:

* Wybierz maszynę Wirtualną
* Wybierz punkt odzyskiwania
* Przywróć dyski
* Tworzenie maszyny Wirtualnej z dysków przechowywane

Na poniższym rysunku przedstawiono hierarchię obiektów z RecoveryServicesVault do BackupRecoveryPoint.

![Hierarchia obiektów usług odzyskiwania przedstawiający BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Aby przywrócić dane kopii zapasowej, zidentyfikuj elementu kopii zapasowej i punkt odzyskiwania, która przechowuje dane w momencie. Użyj **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** polecenia cmdlet, aby przywrócić dane z magazynu do konta klienta.

### <a name="select-the-vm"></a>Wybierz maszynę Wirtualną
Aby uzyskać obiekt programu PowerShell, który identyfikuje element tej kopii zapasowej, uruchom z kontenera w magazynie, a metodą w dół hierarchii obiektów. Aby wybrać kontener, który reprezentuje maszynę Wirtualną, użyj **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** polecenia cmdlet i przekazać ją do **[ Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** polecenia cmdlet.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Wybierz punkt odzyskiwania
Użyj **[Get AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** polecenia cmdlet, aby wyświetlić listę wszystkich punktów odzyskiwania dla elementu kopii zapasowej. Następnie wybierz punkt odzyskiwania do przywrócenia. Jeśli wiesz, punkt odzyskiwania, którego chcesz użyć, jest dobrym rozwiązaniem wybierz najbardziej aktualną RecoveryPointType = AppConsistent punkt na liście.

W poniższym skrypcie zmiennej, **$rp**, jest tablicą punkty odzyskiwania dla wybranego elementu kopii zapasowej z ostatnich siedmiu dni. Tablica jest posortowana w kolejności odwrotnej względem czasie przy użyciu najnowszego punktu odzyskiwania pod indeksem 0. Użyj standardowych indeksowanie tablicy programu PowerShell, aby wybrać punkt odzyskiwania. W tym przykładzie $rp [0] wybiera najnowszego punktu odzyskiwania.

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



### <a name="restore-the-disks"></a>Przywróć dyski
Użyj **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** polecenia cmdlet, aby przywrócić elementu kopii zapasowej danych i konfiguracji punktu odzyskiwania. Po zidentyfikowaniu punkt odzyskiwania, użyj go jako wartość pozycji **- RecoveryPoint** parametru. W poprzednim kodzie przykładowe **$rp [0]** został punkt odzyskiwania, które chcesz użyć. W poniższym przykładowym kodzie **$rp [0]** jest punkt odzyskiwania na potrzeby przywracania na dysku.

Aby przywrócić dyski i informacje o konfiguracji:

```
PS C:\> $restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
PS C:\> $restorejob
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Użyj **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** polecenia cmdlet, aby czekać na ukończenie zadania przywracania.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Po zakończeniu zadania przywracania, użyj **[Get AzureRmRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjobdetails)** polecenia cmdlet, aby uzyskać szczegółowe informacje o operacji przywracania. Właściwość JobDetails ma informacje wymagane do odbudowywania maszyny Wirtualnej.

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails -Job $restorejob
```

Po przywróceniu dyski, przejdź do następnej sekcji, aby utworzyć maszynę Wirtualną.

## <a name="create-a-vm-from-restored-disks"></a>Tworzenie maszyny Wirtualnej z przywróconych dysków
Po przywróceniu dyski należy użyć tych kroków do tworzenia i konfigurowania maszyny wirtualnej z dysku.

> [!NOTE]
> Aby utworzyć zaszyfrowanych maszyn wirtualnych z przywróconych dysków, rolę na platformie Azure musi mieć uprawnienia do wykonania akcji, **Microsoft.KeyVault/vaults/deploy/action**. Jeśli Twoja rola nie ma to uprawnienie, należy utworzyć rolę niestandardową, za pomocą tej akcji. Aby uzyskać więcej informacji, zobacz [niestandardowych ról RBAC platformy Azure](../role-based-access-control/custom-roles.md).
>
>

1. Tworzenie zapytań o właściwości przywróconego dysku, aby uzyskać szczegóły zadania.

  ```
  PS C:\> $properties = $details.properties
  PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
  PS C:\> $containerName = $properties["Config Blob Container Name"]
  PS C:\> $blobName = $properties["Config Blob Name"]
  ```

2. Ustaw kontekst magazynu platformy Azure i przywrócić pliku konfiguracji JSON.

    ```
    PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
    PS C:\> $destination_path = "C:\vmconfig.json"
    PS C:\> Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path
    PS C:\> $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
    ```

3. Aby utworzyć konfigurację maszyny Wirtualnej, należy użyć pliku konfiguracji JSON.

    ```
   PS C:\> $vm = New-AzureRmVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
    ```

4. Dołącz dysk systemu operacyjnego i dysków z danymi. W zależności od konfiguracji maszyn wirtualnych można znaleźć w odpowiedniej sekcji w celu wyświetlenia odpowiednich poleceń cmdlet:

    #### <a name="non-managed-non-encrypted-vms"></a>Niezarządzane niezaszyfrowane maszyn wirtualnych

    Skorzystaj z poniższego przykładu for niezarządzanego niezaszyfrowane maszyn wirtualnych.

    ```
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
    PS C:\> foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
    {
    $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
    }
    ```

    #### <a name="non-managed-encrypted-vms-bek-only"></a>Niezarządzane zaszyfrowanych maszyn wirtualnych (tylko w przypadku klucza szyfrowania bloków)

    Niezarządzanych, szyfrowanych maszyn wirtualnych (szyfrowane tylko przy użyciu klucza szyfrowania bloków) należy przywrócić klucz tajny do magazynu kluczy, zanim będzie możliwe dołączenie dysków. Aby uzyskać więcej informacji, zobacz artykuł [przywracanie zaszyfrowanych maszyn wirtualnych z punktu odzyskiwania usługi Azure Backup](backup-azure-restore-key-secret.md). Poniższy przykład pokazuje, jak dołączyć dyski systemu operacyjnego i danych dla szyfrowanych maszyn wirtualnych.

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

    #### <a name="non-managed-encrypted-vms-bek-and-kek"></a>Niezarządzane zaszyfrowanych maszyn wirtualnych (BEK i KEK)

    Dla niezarządzanych zaszyfrowanych maszyn wirtualnych (szyfrowane przy użyciu BEK i KEK) musisz Przywracanie klucza i wpisu tajnego do magazynu kluczy, zanim będzie możliwe dołączenie dysków. Aby uzyskać więcej informacji, zobacz artykuł [przywracanie zaszyfrowanych maszyn wirtualnych z punktu odzyskiwania usługi Azure Backup](backup-azure-restore-key-secret.md). Poniższy przykład pokazuje, jak dołączyć dyski systemu operacyjnego i danych dla szyfrowanych maszyn wirtualnych.

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

    #### <a name="managed-non-encrypted-vms"></a>Managed-szyfrowanych maszyn wirtualnych

    Zarządzane maszyny wirtualne nie są szyfrowane należy utworzyć dysków zarządzanych z magazynu obiektów blob, a następnie podłącz odpowiednie dyski. Aby uzyskać szczegółowe informacje, zapoznaj się z artykułem [dołączanie dysku danych do maszyny Wirtualnej Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md). Następujący przykładowy kod pokazuje, jak dołączyć dyski danych w przypadku zarządzanych maszyn wirtualnych, które są niezaszyfrowane.

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

    #### <a name="managed-encrypted-vms-bek-only"></a>Zarządzanych zaszyfrowanych maszyn wirtualnych (tylko w przypadku klucza szyfrowania bloków)

    Zarządzane zaszyfrowane maszyny wirtualne (szyfrowane tylko przy użyciu klucza szyfrowania bloków) należy utworzyć dysków zarządzanych z magazynu obiektów blob, a następnie podłącz odpowiednie dyski. Aby uzyskać szczegółowe informacje, zapoznaj się z artykułem [dołączanie dysku danych do maszyny Wirtualnej Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md). Następujący przykładowy kod pokazuje, jak dołączyć dyski z danymi dla zarządzanych zaszyfrowanych maszyn wirtualnych.

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

    #### <a name="managed-encrypted-vms-bek-and-kek"></a>Zarządzanych zaszyfrowanych maszyn wirtualnych (BEK i KEK)

    Zarządzane zaszyfrowane maszyny wirtualne (szyfrowane przy użyciu BEK i KEK) należy utworzyć dysków zarządzanych z magazynu obiektów blob, a następnie podłącz odpowiednie dyski. Aby uzyskać szczegółowe informacje, zapoznaj się z artykułem [dołączanie dysku danych do maszyny Wirtualnej Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md). Następujący przykładowy kod pokazuje, jak dołączyć dyski z danymi dla zarządzanych zaszyfrowanych maszyn wirtualnych.

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
    PS C:\> $virtualNetwork = New-AzureRmVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    PS C:\> $virtualNetwork | Set-AzureRmVirtualNetwork
    PS C:\> $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    PS C:\> $subnetindex=0
    PS C:\> $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    PS C:\> $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```
6. Utwórz maszynę wirtualną.

    ```    
    PS C:\> New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

## <a name="restore-files-from-an-azure-vm-backup"></a>Przywracanie plików z kopii zapasowej maszyny Wirtualnej platformy Azure

Oprócz przywracanie dysków, można przywrócić pojedyncze pliki z kopii zapasowej maszyny Wirtualnej platformy Azure. Przywracanie plików funkcji daje dostęp do wszystkich plików w punkcie odzyskiwania, i może zarządzać nimi za pomocą Eksploratora plików w sposób jak normalne pliki.

Podstawowe kroki, aby przywrócić plik z kopii zapasowej maszyny Wirtualnej platformy Azure są:

* Wybierz maszynę Wirtualną
* Wybierz punkt odzyskiwania
* Zainstaluj dyskach punktu odzyskiwania
* Skopiuj wymagane pliki
* Odinstaluj dysk


### <a name="select-the-vm"></a>Wybierz maszynę Wirtualną
Aby uzyskać obiekt programu PowerShell, który identyfikuje element tej kopii zapasowej, uruchom z kontenera w magazynie, a metodą w dół hierarchii obiektów. Aby wybrać kontener, który reprezentuje maszynę Wirtualną, użyj **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** polecenia cmdlet i przekazać ją do **[ Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** polecenia cmdlet.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Wybierz punkt odzyskiwania
Użyj **[Get AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** polecenia cmdlet, aby wyświetlić listę wszystkich punktów odzyskiwania dla elementu kopii zapasowej. Następnie wybierz punkt odzyskiwania do przywrócenia. Jeśli wiesz, punkt odzyskiwania, którego chcesz użyć, jest dobrym rozwiązaniem wybierz najbardziej aktualną RecoveryPointType = AppConsistent punkt na liście.

W poniższym skrypcie zmiennej, **$rp**, jest tablicą punkty odzyskiwania dla wybranego elementu kopii zapasowej z ostatnich siedmiu dni. Tablica jest posortowana w kolejności odwrotnej względem czasie przy użyciu najnowszego punktu odzyskiwania pod indeksem 0. Użyj standardowych indeksowanie tablicy programu PowerShell, aby wybrać punkt odzyskiwania. W tym przykładzie $rp [0] wybiera najnowszego punktu odzyskiwania.

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

Użyj **[Get AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprpmountscript)** polecenia cmdlet, aby uzyskać skrypt, aby zainstalować wszystkie dyski punktu odzyskiwania.

> [!NOTE]
> Dyski są instalowane jako dyski iSCSI dołączona do maszyny, na którym skrypt jest uruchamiany. Dlatego jest niemal natychmiastowe i nie są naliczane opłaty
>
>

```
PS C:\> Get-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]

OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```
Uruchom skrypt na maszynie, w której chcesz odzyskać pliki. Musisz wprowadzić hasło pokazanych powyżej, aby wykonać skrypt. Po są dołączone dyski, należy użyć Eksploratora plików Windows, aby przejrzeć nowe woluminy i pliki. Aby uzyskać więcej informacji zobacz [pliku dokumentacji odzyskiwania](backup-azure-restore-files-from-vm.md)

### <a name="unmount-the-disks"></a>Odinstaluj dyski
Po skopiowaniu plików wymaganych odinstalowanie dysków za pomocą **[AzureRmRecoveryServicesBackupRPMountScript Wyłącz](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/disable-azurermrecoveryservicesbackuprpmountscript?view=azurermps-5.0.0)** polecenia cmdlet. To zdecydowanie zaleca się, ponieważ daje to pewność, że usunięcie dostępu do plików punkt odzyskiwania

```
PS C:\> Disable-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```


## <a name="next-steps"></a>Kolejne kroki
Jeśli wolisz, skontaktuj się z zasobami platformy Azure przy użyciu programu PowerShell, zapoznaj się z artykułem PowerShell [wdrażanie i zarządzanie nimi kopii zapasowych dla systemu Windows Server](backup-client-automation.md). Jeśli zarządzasz kopii zapasowych programu DPM, zapoznaj się z artykułem [wdrażanie i zarządzanie nimi kopii zapasowej programu DPM](backup-dpm-automation.md). Ma oba z tych artykułów wersji wdrożeń usługi Resource Manager i model klasyczny.  
