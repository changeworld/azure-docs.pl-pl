---
title: Wdrażanie kopii zapasowych maszyn wirtualnych wdrożonych przy użyciu usługi Resource Manager oraz zarządzanie nimi przy użyciu programu PowerShell
description: Wdrażanie i zarządzanie nimi kopii zapasowych na platformie Azure maszyn wirtualnych wdrożonych przez Menedżera zasobów przy użyciu programu PowerShell
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: raynew
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b9c3dadb328bb49f3cb1897bb64390ed75620b3d
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2019
ms.locfileid: "56311889"
---
# <a name="use-powershell-to-back-up-and-restore-virtual-machines"></a>Tworzenie kopii zapasowej i przywracanie maszyn wirtualnych przy użyciu programu PowerShell

W tym artykule pokazano, jak tworzyć kopie zapasowe i odzyskiwanie maszyn wirtualnych (VM) z magazynu usługi Recovery Services za pomocą poleceń cmdlet programu Azure PowerShell. Magazyn usługi Recovery Services jest zasobem usługi Azure Resource Manager, umożliwia ochronę danych i zasoby usługi Azure Backup i Azure Site Recovery.

> [!NOTE]
> Platforma Azure oferuje dwa modele wdrażania związane z tworzeniem i pracą z zasobami: [Usługi Resource Manager i Model Klasyczny](../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł jest przeznaczony dla maszyn wirtualnych utworzonych za pomocą modelu usługi Resource Manager.
>
>

Ten artykuł przeprowadzi przy użyciu programu PowerShell, aby chronić maszynę Wirtualną i przywrócić dane z punktu odzyskiwania.

## <a name="concepts"></a>Pojęcia

Jeśli użytkownik nie jest zaznajomiony z usługą Azure Backup z omówieniem usługi, zapoznaj się z artykułem [co to jest usługa Azure Backup?](backup-introduction-to-azure-backup.md) Przed rozpoczęciem upewnij się, że obejmują wstępne w usłudze Azure Backup i ograniczenia dotyczące bieżącego rozwiązania tworzenia kopii zapasowych maszyn wirtualnych.

Aby skutecznie za pomocą programu PowerShell, jest niezbędne do zrozumienia hierarchii obiektów i z których ma rozpoczynać się.

![Hierarchia obiektów usług odzyskiwania](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Aby wyświetlić Dokumentacja poleceń cmdlet AzureRm.RecoveryServices.Backup programu PowerShell, zobacz [usługi Azure Backup — poleceń cmdlet usług odzyskiwania](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) w bibliotece platformy Azure.

## <a name="setup-and-registration"></a>Konfiguracja i rejestracja

Aby rozpocząć:

1. [Pobierz najnowszą wersję programu PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) (minimalna wymagana wersja to: 1.4.0)

2. Znajdowanie dostępnych poleceń cmdlet programu PowerShell kopia zapasowa Azure, wpisując następujące polecenie:

    ```powershell
    Get-Command *azurermrecoveryservices*
    ```    
    Aliasy i polecenia cmdlet dla usługi Kopia zapasowa Azure, usługi Azure Site Recovery i magazyn usługi Recovery Services są wyświetlane. Poniższy rysunek jest przykładem zostaną wyświetlone. Nie jest pełną listę poleceń cmdlet.

    ![Lista usług Recovery Services](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Zaloguj się do subskrypcji platformy Azure, konta przy użyciu **Connect-AzureRmAccount**. To polecenie cmdlet powoduje wyświetlenie strony wyświetli monit o podanie poświadczeń konta:

    * Alternatywnie można uwzględnić poświadczeń konta jako parametru w **Connect-AzureRmAccount** polecenia cmdlet, za pomocą **-poświadczeń** parametru.
    * Jeśli jesteś partnerem CSP, Praca w imieniu dzierżawy, określenia klienta jako dzierżawca, za pomocą ich identyfikatora dzierżawy lub dzierżawy podstawowej nazwy domeny. Na przykład: **Connect-AzureRmAccount-dzierżawy "fabrikam.com"**

4. Skojarz subskrypcję, której chcesz użyć przy użyciu konta, ponieważ konto może mieć kilka subskrypcji:

    ```powershell
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Jeśli używasz usługi Azure Backup po raz pierwszy, musisz użyć **[Register-AzureRmResourceProvider](https://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** polecenia cmdlet, aby zarejestrować dostawcę usługi Azure Recovery Service w ramach subskrypcji.

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Aby sprawdzić, czy dostawców pomyślnie zarejestrowane, używając następujących poleceń:
    ```powershell
    Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
    W danych wyjściowych polecenia **RegistrationState** należy zmienić na **zarejestrowanej**. Jeśli nie, uruchom **[Register-AzureRmResourceProvider](https://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** ponownie polecenie cmdlet.

Następujące zadania można zautomatyzować za pomocą programu PowerShell:

* [Tworzenie magazynu usługi Recovery Services](backup-azure-vms-automation.md#create-a-recovery-services-vault)
* [Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure](backup-azure-vms-automation.md#back-up-azure-vms)
* [Wyzwalanie zadania tworzenia kopii zapasowej](backup-azure-vms-automation.md#trigger-a-backup)
* [Monitorowanie zadania tworzenia kopii zapasowej](backup-azure-vms-automation.md#monitoring-a-backup-job)
* [Przywracanie maszyny Wirtualnej platformy Azure](backup-azure-vms-automation.md#restore-an-azure-vm)

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Następujące kroki przeprowadzą Cię przez proces tworzenia magazynu usługi Recovery Services. Magazyn usługi Recovery Services jest inny niż magazynu kopii zapasowych.

1. Magazyn usługi Recovery Services jest zasobu usługi Resource Manager, dlatego należy go umieścić w grupie zasobów. Możesz użyć istniejącej grupy zasobów lub Utwórz grupę zasobów za pomocą **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup)** polecenia cmdlet. Podczas tworzenia grupy zasobów, określ nazwę i lokalizację grupy zasobów.  

    ```powershell
    New-AzureRmResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Użyj **[New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault)** polecenia cmdlet, aby utworzyć magazyn usługi Recovery Services. Pamiętaj określić lokalizację tego samego magazynu, która była używana dla grupy zasobów.

    ```powershell
    New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Określenie typu nadmiarowości magazynu, które mają być używane; Możesz użyć [magazyn lokalnie nadmiarowy (LRS)](../storage/common/storage-redundancy-lrs.md) lub [magazyn geograficznie nadmiarowy (GRS)](../storage/common/storage-redundancy-grs.md). Poniższy przykład pokazuje, że ustawiono opcję - BackupStorageRedundancy testvault GeoRedundant.

    ```powershell
    $vault1 = Get-AzureRmRecoveryServicesVault -Name "testvault"
    Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Wiele poleceń cmdlet usługi Azure Backup wymaga obiektu magazynu usługi Recovery Services jako danych wejściowych. Z tego powodu wygodne jest przechowywanie obiektu magazynu usługi Backup Recovery Services w zmiennej.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Wyświetlanie magazynów w ramach subskrypcji

Aby wyświetlić wszystkie magazyny w ramach subskrypcji, użyj  **[Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault)**:

```powershell
Get-AzureRmRecoveryServicesVault
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

Przed włączeniem ochrony na maszynie Wirtualnej, użyj **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** można ustawić kontekst magazynu. Po ustawieniu kontekst magazynu ma zastosowanie do wszystkich kolejnych poleceń cmdlet. W poniższym przykładzie ustawiono kontekst magazynu dla magazynu, *testvault*.

```powershell
Get-AzureRmRecoveryServicesVault -Name "testvault" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>Tworzenie zasad ochrony

Podczas tworzenia magazynu usługi Recovery Services jest on dostarczany z domyślnymi zasadami ochrony i przechowywania. Domyślne zasady ochrony wyzwalają zadanie tworzenia kopii zapasowej każdego dnia o określonej godzinie. Domyślne zasady przechowywania zachowują codzienny punkt odzyskiwania przez 30 dni. Aby zapewnić szybką ochronę maszyny Wirtualnej i edytować zasady, które później za pomocą różnych szczegółów, można użyć domyślne zasady.

Użyj **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupprotectionpolicy)** umożliwiającą wyświetlenie zasad ochrony dostępne w magazynie. Można użyć tego polecenia cmdlet, aby uzyskać szczegółowe zasady lub aby wyświetlić zasady skojarzone z typem obciążenia. Poniższy przykład pobiera zasady dla obciążeń typu AzureVM.

```powershell
Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
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

Zasady tworzenia kopii zapasowej ochrony jest skojarzony z co najmniej jedne zasady przechowywania. Zasady przechowywania Określa, jak długo punkt odzyskiwania jest przechowywana, przed usunięciem. Użyj **[Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupretentionpolicyobject)** Aby wyświetlić domyślne zasady przechowywania.  Podobnie można użyć **[Get AzureRmRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupschedulepolicyobject)** uzyskać domyślne zasady harmonogramu. **[New AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** polecenie cmdlet tworzy obiekt programu PowerShell, który zawiera informacje o zasadach tworzenia kopii zapasowej. Obiekty zasad harmonogram i okres przechowywania są używane jako dane wejściowe **[New AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** polecenia cmdlet. Poniższy przykład zasad harmonogram i zasady przechowywania są przechowywane w zmiennych. W przykładzie użyto tych zmiennych, aby zdefiniować parametry podczas tworzenia zasad ochrony *NewPolicy*.

```powershell
$schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Dane wyjściowe są podobne do poniższego przykładu:

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Włączanie ochrony

Po zdefiniowaniu zasad ochrony, nadal musisz włączyć zasady dla elementu. Użyj **[Enable-AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection)** do włączenia ochrony. Włączenie ochrony wymaga dwóch obiektów - element i zasad. Gdy zasady zostało skojarzone z magazynem, tworzenia kopii zapasowej przepływ pracy zostanie wyzwolony w czasie, zdefiniowane w harmonogram zasad.

Poniższe przykłady włączyć ochrony dla elementu V2VM, za pomocą zasad NewPolicy. Przykłady różnią się zależnie od tego, czy maszyna wirtualna jest zaszyfrowana i co typ szyfrowania.

Aby włączyć ochronę na **niezaszyfrowane maszyn wirtualnych usługi Resource Manager**:

```powershell
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Aby włączyć ochronę na **zaszyfrowanych maszyn wirtualnych (szyfrowane przy użyciu BEK i KEK)**, należy przyznać uprawnienia usługi Azure Backup do odczytu klucze i wpisy tajne z magazynu kluczy.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Aby włączyć ochronę na **zaszyfrowanych maszyn wirtualnych (szyfrowane tylko przy użyciu klucza szyfrowania bloków)**, musisz udzielić uprawnień usługi Azure Backup na odczyt kluczy tajnych w magazynie kluczy.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Jeśli używasz chmury Azure Government, należy użyć ff281ffe-705c-4f53-9f37-a40e6f2c68f3 wartość dla parametru **- ServicePrincipalName** w [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) polecenia cmdlet.
>
>

Aby włączyć ochronę na klasycznej maszyny Wirtualnej:

```powershell
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>Zmodyfikuj zasady ochrony

Aby zmodyfikować zasady ochrony, użyj [Set-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy) do modyfikowania obiektów SchedulePolicy lub zasadach RetentionPolicy.

Poniższy przykład zmienia czas przechowywania punktu odzyskiwania do 365 dni.

```powershell
$retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

## <a name="trigger-a-backup"></a>Wyzwalanie tworzenia kopii zapasowej

Użyj **[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** wyzwalanie zadania tworzenia kopii zapasowej. Jeśli tworzenie początkowej kopii zapasowej, jest pełna kopia zapasowa. Kolejne kopie zapasowe wykonać kopie przyrostowe. Należy użyć **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** można ustawić kontekst magazynu przed wyzwoleniem zadania tworzenia kopii zapasowej. W poniższym przykładzie założono, że kontekst magazynu został już ustawiony.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
$job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
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

## <a name="monitoring-a-backup-job"></a>Monitorowanie zadania tworzenia kopii zapasowej

Możesz monitorować długotrwałych operacji, takich jak zadania tworzenia kopii zapasowej bez korzystania z witryny Azure portal. Aby uzyskać stan zadania w toku, użyj **[Get-AzureRmRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob)** polecenia cmdlet. To polecenie cmdlet pobiera zadania tworzenia kopii zapasowej dla określonego magazynu i Magazyn ten jest określony w kontekst magazynu. Poniższy przykład pobiera stan zadania w toku jako tablicę i przechowuje stan w zmiennej $joblist.

```powershell
$joblist = Get-AzureRmRecoveryservicesBackupJob –Status "InProgress"
$joblist[0]
```

Dane wyjściowe są podobne do poniższego przykładu:

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Zamiast sondowania te zadania na ukończenie — czyli niepotrzebnych dodatkowego kodu — użyj **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** polecenia cmdlet. To polecenie cmdlet wstrzymuje wykonywanie, dopóki zadanie zostanie ukończone lub określona wartość limitu czasu zostanie osiągnięty.

```powershell
Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Przywracanie maszyny Wirtualnej platformy Azure

Jest to istotna różnica między przywracania maszyny Wirtualnej przy użyciu witryny Azure portal i przywracania maszyny Wirtualnej przy użyciu programu PowerShell. Przy użyciu programu PowerShell operacja przywracania została zakończona, po utworzeniu dyski i informacje o konfiguracji z punktu odzyskiwania. Operacja przywracania nie tworzy maszynę wirtualną. Aby utworzyć maszynę wirtualną z dysku, zobacz sekcję [tworzenie maszyny Wirtualnej z przywróconych dysków](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Jeśli nie chcesz przywrócić całą maszynę Wirtualną, ale chcesz przywrócić lub odzyskać kilka plików z kopii zapasowej maszyny Wirtualnej platformy Azure, zapoznaj się [pliku sekcji odzyskiwania](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup).

> [!Tip]
> Operacja przywracania nie powoduje utworzenia maszyny wirtualnej.
>
>

Na poniższym rysunku przedstawiono hierarchię obiektów z RecoveryServicesVault do BackupRecoveryPoint.

![Hierarchia obiektów usług odzyskiwania przedstawiający BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Aby przywrócić dane kopii zapasowej, zidentyfikuj elementu kopii zapasowej i punkt odzyskiwania, która przechowuje dane w momencie. Użyj **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** przywrócić dane z magazynu do Twojego konta.

Podstawowe kroki, aby przywrócić Maszynę wirtualną platformy Azure są:

* Wybierz maszynę wirtualną.
* Wybierz punkt odzyskiwania.
* Przywróć dyski.
* Tworzenie maszyny Wirtualnej z dysków przechowywane.

### <a name="select-the-vm"></a>Wybierz maszynę Wirtualną

Aby uzyskać obiekt programu PowerShell, który identyfikuje element tej kopii zapasowej, uruchom z kontenera w magazynie, a metodą w dół hierarchii obiektów. Aby wybrać kontener, który reprezentuje maszynę Wirtualną, użyj **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** polecenia cmdlet i przekazać ją do **[ Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** polecenia cmdlet.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Wybierz punkt odzyskiwania

Użyj **[Get AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** polecenia cmdlet, aby wyświetlić listę wszystkich punktów odzyskiwania dla elementu kopii zapasowej. Następnie wybierz punkt odzyskiwania do przywrócenia. Jeśli wiesz, punkt odzyskiwania, którego chcesz użyć, jest dobrym rozwiązaniem wybierz najbardziej aktualną RecoveryPointType = AppConsistent punkt na liście.

W poniższym skrypcie zmiennej, **$rp**, jest tablicą punkty odzyskiwania dla wybranego elementu kopii zapasowej z ostatnich siedmiu dni. Tablica jest posortowana w kolejności odwrotnej względem czasie przy użyciu najnowszego punktu odzyskiwania pod indeksem 0. Użyj standardowych indeksowanie tablicy programu PowerShell, aby wybrać punkt odzyskiwania. W tym przykładzie $rp [0] wybiera najnowszego punktu odzyskiwania.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
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

Użyj **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** polecenia cmdlet, aby przywrócić elementu kopii zapasowej danych i konfiguracji punktu odzyskiwania. Po zidentyfikowaniu punkt odzyskiwania, użyj go jako wartość pozycji **- RecoveryPoint** parametru. W powyższym przykładzie **$rp [0]** został punkt odzyskiwania, które chcesz użyć. W poniższym przykładowym kodzie **$rp [0]** jest punkt odzyskiwania na potrzeby przywracania na dysku.

Aby przywrócić dyski i informacje o konfiguracji:

```powershell
$restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
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
$restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks"
```

**VMConfig.JSON** plik zostanie przywrócony do konta magazynu i dyski zarządzane zostaną przywrócone z określoną docelową grupą zasobów.

Dane wyjściowe są podobne do poniższego przykładu:

```powershell
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Użyj **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** polecenia cmdlet, aby czekać na ukończenie zadania przywracania.

```powershell
Wait-AzureRmRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Po zakończeniu zadania przywracania, użyj **[Get AzureRmRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjobdetails)** polecenia cmdlet, aby uzyskać szczegółowe informacje o operacji przywracania. Właściwość JobDetails ma informacje wymagane do odbudowywania maszyny Wirtualnej.

```powershell
$restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
$details = Get-AzureRmRecoveryServicesBackupJobDetails -Job $restorejob
```

Po przywróceniu dyski, przejdź do następnej sekcji, aby utworzyć maszynę Wirtualną.

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

Wystarczy go wdrożyć szablon umożliwiający utworzenie nowej maszyny Wirtualnej, zgodnie z objaśnieniem [tutaj](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy#deploy-a-template-from-an-external-source).

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobURI -storageAccountType Standard_GRS
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
   Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzureStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. Aby utworzyć konfigurację maszyny Wirtualnej, należy użyć pliku konfiguracji JSON.

   ```powershell
   $vm = New-AzureRmVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Dołącz dysk systemu operacyjnego i dysków z danymi. Ten krok obejmuje przykładów dla różnych zarządzane i konfiguracje maszyn wirtualnych szyfrowane. Skorzystaj z przykładu, który odpowiada konfiguracji maszyny Wirtualnej.

   * **Maszyny wirtualne niezarządzane i niezaszyfrowane** — skorzystaj z poniższego przykładu for niezarządzanego niezaszyfrowane maszyn wirtualnych.

       ```powershell
       Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
       ```

   * **Niezarządzane i zaszyfrowanych maszyn wirtualnych przy użyciu usługi Azure AD (tylko w przypadku klucza szyfrowania bloków)** -For niezarządzanego zaszyfrowanych maszyn wirtualnych z usługą Azure AD (szyfrowane tylko przy użyciu klucza szyfrowania bloków), należy przywrócić klucz tajny do magazynu kluczy, zanim będzie możliwe dołączenie dysków. Aby uzyskać więcej informacji, zobacz [przywracanie zaszyfrowanych maszyn wirtualnych z punktu odzyskiwania usługi Azure Backup](backup-azure-restore-key-secret.md). Poniższy przykład pokazuje, jak dołączyć dyski systemu operacyjnego i danych dla szyfrowanych maszyn wirtualnych. Podczas ustawiania dysku systemu operacyjnego, upewnij się wspomnieć o odpowiedni typ systemu operacyjnego.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
      {
       $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **Niezarządzane i zaszyfrowanych maszyn wirtualnych przy użyciu usługi Azure AD (BEK i KEK)** — w przypadku niezarządzanych zaszyfrowanych maszyn wirtualnych z usługą Azure AD (szyfrowane przy użyciu BEK i KEK), Przywracanie klucza i wpisu tajnego do magazynu kluczy przed dołączeniem dyski. Aby uzyskać więcej informacji, zobacz [przywracanie zaszyfrowanych maszyn wirtualnych z punktu odzyskiwania usługi Azure Backup](backup-azure-restore-key-secret.md). Poniższy przykład pokazuje, jak dołączyć dyski systemu operacyjnego i danych dla szyfrowanych maszyn wirtualnych.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
      ```

   * **Niezarządzane i zaszyfrowanych maszyn wirtualnych bez usługi Azure AD (tylko w przypadku klucza szyfrowania bloków)** — w przypadku niezarządzanych zaszyfrowanych maszyn wirtualnych bez usługi Azure AD (szyfrowane tylko przy użyciu klucza szyfrowania bloków), jeśli źródło **magazynu kluczy/wpisów tajnych są niedostępne** przywrócić wpisy tajne usługi key vault za pomocą procedury w [przywrócić maszynę wirtualną z protokołem szyfrowania z punktu odzyskiwania usługi Azure Backup](backup-azure-restore-key-secret.md). Następnie wykonaj następujące skrypty do ustawiania szczegółów szyfrowania w przywróconej obiekcie blob systemu operacyjnego (ten krok nie jest wymagana dla obiektu blob danych). $Dekurl mogą być pobierane z przywróconej magazynu kluczy.<br>

   Poniższego skryptu musi być wykonywane tylko wtedy, gdy magazyn kluczy/wpisów tajnych źródła nie jest dostępna.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzureStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```

    Po **wpisów tajnych są dostępne** i szczegółów szyfrowania również są ustawione w obiekcie Blob systemu operacyjnego, podłącz odpowiednie dyski za pomocą skryptu podane poniżej.<br>

    Jeśli dostępne są już magazyn kluczy lub kluczy tajnych źródła, następnie powyższy skrypt muszą nie można wykonać.

      ```powershell
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
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
      $osBlob = Get-AzureStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```
   Po **klucza lub kluczy tajnych są dostępne** i szczegółów szyfrowania ustawionych w obiekcie Blob systemu operacyjnego, podłącz odpowiednie dyski za pomocą skryptu podane poniżej.

    Jeśli źródło/klucz/wpisów tajnych usługi keyVault są dostępne, następnie powyższy skrypt muszą nie można wykonać.

    ```powershell
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
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
      $diskupdateconfig = New-AzureRmDiskUpdateConfig -EncryptionSettingsEnabled $true
      $diskupdateconfig = Set-AzureRmDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
      Update-AzureRmDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
      ```

    Po wpisy tajne są dostępne i szczegółów szyfrowania są ustawione na dysku systemu operacyjnego, aby dołączyć przywróconych dysków zarządzanych, zobacz [dołączanie dysku danych do maszyny Wirtualnej Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md).

  * **Zarządzana i zaszyfrowanych maszyn wirtualnych bez usługi Azure AD (BEK i KEK)** — w przypadku zarządzanych zaszyfrowanych maszyn wirtualnych bez usługi Azure AD (szyfrowane przy użyciu BEK i KEK), jeśli źródło **keyVault/klucz/wpis tajny nie są dostępne** Przywracanie klucza i wpisy tajne klucza Magazyn, korzystając z procedury w [przywrócić maszynę wirtualną z protokołem szyfrowania z punktu odzyskiwania usługi Azure Backup](backup-azure-restore-key-secret.md). Następnie wykonaj następujące skrypty można ustawić szczegółów szyfrowania w przywróconego dysku systemu operacyjnego (ten krok nie jest wymagany dla dysku z danymi). $Dekurl i $kekurl mogą być pobierane z przywróconej magazynu kluczy.

  Poniższego skryptu musi być wykonywane tylko wtedy, gdy źródłowy magazyn kluczy/klucz/wpis tajny nie jest dostępna.

  ```powershell
     $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
     $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
     $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
     $diskupdateconfig = New-AzureRmDiskUpdateConfig -EncryptionSettingsEnabled $true
     $diskupdateconfig = Set-AzureRmDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
     $diskupdateconfig = Set-AzureRmDiskUpdateKeyEncryptionKey -DiskUpdate $diskupdateconfig -KeyUrl $kekUrl -SourceVaultId $keyVaultId  
     Update-AzureRmDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    Po klucza lub kluczy tajnych są dostępne i szczegółów szyfrowania są ustawione na dysku systemu operacyjnego, aby dołączyć przywróconych dysków zarządzanych, zobacz [dołączanie dysku danych do maszyny Wirtualnej Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md).

5. Określ ustawienia sieciowe.

    ```powershell
    $nicName="p1234"
    $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzureRmVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzureRmVirtualNetwork
    $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. Utwórz maszynę wirtualną.

    ```powershell  
    New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. Wypchnij ADE rozszerzenia.

  * **Dla maszyny Wirtualnej z usługą Azure AD** — Użyj następującego polecenia, aby ręcznie włączyć szyfrowanie dla dysków z danymi  

    **Tylko klucz szyfrowania bloków**

      ```powershell  
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

    **BEK i KEK**

      ```powershell  
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

  * **Dla maszyny Wirtualnej bez usługi Azure AD** — Użyj następującego polecenia, aby ręcznie włączyć szyfrowanie dla dysków z danymi.

    Jeśli na wypadek, gdyby podczas wykonywania polecenia organ AADClientID, musisz zaktualizować programu Azure PowerShell.

    **Tylko klucz szyfrowania bloków**

      ```powershell  
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK i KEK**

      ```powershell  
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
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

Aby uzyskać obiekt programu PowerShell, który identyfikuje element tej kopii zapasowej, uruchom z kontenera w magazynie, a metodą w dół hierarchii obiektów. Aby wybrać kontener, który reprezentuje maszynę Wirtualną, użyj **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** polecenia cmdlet i przekazać ją do **[ Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** polecenia cmdlet.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Wybierz punkt odzyskiwania

Użyj **[Get AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** polecenia cmdlet, aby wyświetlić listę wszystkich punktów odzyskiwania dla elementu kopii zapasowej. Następnie wybierz punkt odzyskiwania do przywrócenia. Jeśli wiesz, punkt odzyskiwania, którego chcesz użyć, jest dobrym rozwiązaniem wybierz najbardziej aktualną RecoveryPointType = AppConsistent punkt na liście.

W poniższym skrypcie zmiennej, **$rp**, jest tablicą punkty odzyskiwania dla wybranego elementu kopii zapasowej z ostatnich siedmiu dni. Tablica jest posortowana w kolejności odwrotnej względem czasie przy użyciu najnowszego punktu odzyskiwania pod indeksem 0. Użyj standardowych indeksowanie tablicy programu PowerShell, aby wybrać punkt odzyskiwania. W tym przykładzie $rp [0] wybiera najnowszego punktu odzyskiwania.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

Dane wyjściowe są podobne do poniższego przykładu:

```
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
> Dyski są instalowane jako dyski iSCSI dołączona do maszyny, na którym skrypt jest uruchamiany. Instalowanie następuje natychmiast i nie powodują naliczania żadnych opłat.
>
>

```powershell
Get-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

Dane wyjściowe są podobne do poniższego przykładu:

```powershell
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Uruchom skrypt na maszynie, w której chcesz odzyskać pliki. Aby wykonać skrypt, należy wprowadzić podane hasło. Po są dołączone dyski, należy użyć Eksploratora plików Windows, aby przeglądać nowe woluminy i pliki. Aby uzyskać więcej informacji można znaleźć w artykule kopii zapasowej, [odzyskiwanie plików z kopii zapasowej maszyny wirtualnej platformy Azure](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Odinstaluj dyski

Po skopiowaniu wymaganych plików, użyj **[AzureRmRecoveryServicesBackupRPMountScript Wyłącz](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/disable-azurermrecoveryservicesbackuprpmountscript?view=azurermps-5.0.0)** odinstalował dyski. Pamiętaj odinstalowanie dysków, dzięki czemu dostęp do plików punkt odzyskiwania zostanie usunięty.

```powershell
Disable-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

## <a name="next-steps"></a>Kolejne kroki

Jeśli wolisz, skontaktuj się z zasobami platformy Azure przy użyciu programu PowerShell, zapoznaj się z artykułem PowerShell [wdrażanie i zarządzanie nimi kopii zapasowych dla systemu Windows Server](backup-client-automation.md). Jeśli zarządzasz kopii zapasowych programu DPM, zapoznaj się z artykułem [wdrażanie i zarządzanie nimi kopii zapasowej programu DPM](backup-dpm-automation.md). Ma oba z tych artykułów wersji wdrożeń usługi Resource Manager i model klasyczny.  
