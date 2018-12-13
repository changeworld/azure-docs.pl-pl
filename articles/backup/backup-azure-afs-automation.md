---
title: Wdrażanie i zarządzanie kopiami zapasowymi udziałów plików platformy Azure przy użyciu programu PowerShell
description: Wdrażanie i zarządzanie nimi kopii zapasowych na platformie Azure, udziałów plików platformy Azure przy użyciu programu PowerShell
services: backup
author: pvrk
manager: shivamg
keywords: PowersShell; Usługa Azure files kopii zapasowych. Przywracanie plików platformy Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: pullabhk
ms.assetid: 80da8ece-2cce-40dd-8dce-79960b6ae073
ms.openlocfilehash: 90623981f67bbed15ade743192525676e58a0a83
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318694"
---
# <a name="use-powershell-to-back-up-and-restore-azure-file-shares"></a>Użyj programu PowerShell do tworzenia kopii i przywracanie udziałów plików platformy Azure

W tym artykule pokazano, jak tworzyć kopie zapasowe i odzyskiwanie udziału plików platformy Azure z magazynu usługi Recovery Services za pomocą poleceń cmdlet programu Azure PowerShell. Magazyn usługi Recovery Services jest zasobem usługi Azure Resource Manager, umożliwia ochronę danych i zasoby usługi Azure Backup i Azure Site Recovery.

## <a name="concepts"></a>Pojęcia

Jeśli użytkownik nie jest zaznajomiony z usługą Azure Backup z omówieniem usługi, zapoznaj się z artykułem [co to jest usługa Azure Backup?](backup-introduction-to-azure-backup.md). Przed rozpoczęciem upewnij się, wykonanie Uwaga w wersji zapoznawczej możliwości tworzenia kopii zapasowej udziałów plików platformy Azure udokumentowane [tutaj](backup-azure-files.md).

Aby skutecznie za pomocą programu PowerShell, jest niezbędne do zrozumienia hierarchii obiektów i z których ma rozpoczynać się.

![Hierarchia obiektów usług odzyskiwania](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Aby wyświetlić Dokumentacja poleceń cmdlet AzureRm.RecoveryServices.Backup programu PowerShell, zobacz [usługi Azure Backup — poleceń cmdlet usług odzyskiwania](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) w bibliotece platformy Azure.

## <a name="setup-and-registration"></a>Konfiguracja i rejestracja

> [!NOTE]
> Jak wspomniano [tutaj](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.13.0), obsługa nowych funkcji w kończy się z modułu AzureRM w listopada 2018 r. W związku z tym firma Microsoft świadczy pomoc techniczną dla kopii zapasowej udziałów plików platformy Azure przy użyciu nowego modułu "Az" PS. Planujemy także zostać dołączone do wersji ogólnie dostępnej wersji Az modułu.

Aby rozpocząć:

1. [Pobierz najnowszą wersję programu PowerShell "Az"](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azurermps-6.13.0) (minimalna wymagana wersja to: 0.7.0)

2. Znajdowanie dostępnych poleceń cmdlet programu PowerShell kopia zapasowa Azure, wpisując następujące polecenie:

    ```powershell
    Get-Command *azrecoveryservices*
    ```
    Aliasy i polecenia cmdlet dla usługi Kopia zapasowa Azure, usługi Azure Site Recovery i magazyn usługi Recovery Services są wyświetlane. Poniższy rysunek jest przykładem zostaną wyświetlone. Nie jest pełną listę poleceń cmdlet.

    ![Lista usług Recovery Services](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. Zaloguj się do subskrypcji platformy Azure, konta przy użyciu **Connect AzAccount**. To polecenie cmdlet powoduje wyświetlenie strony wyświetli monit o podanie poświadczeń konta:

    * Alternatywnie można uwzględnić poświadczeń konta jako parametru w **Connect AzAccount** polecenia cmdlet, za pomocą **— poświadczeń** parametru.
    * Jeśli jesteś partnerem CSP, Praca w imieniu dzierżawy, określenia klienta jako dzierżawca, za pomocą ich identyfikatora dzierżawy lub dzierżawy podstawowej nazwy domeny. Na przykład: **Połącz AzAccount-dzierżawy "fabrikam.com"**

4. Skojarz subskrypcję, której chcesz użyć przy użyciu konta, ponieważ konto może mieć kilka subskrypcji:

    ```powershell
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Jeśli używasz usługi Azure Backup po raz pierwszy, musisz użyć **AzResourceProvider rejestru** polecenia cmdlet, aby zarejestrować dostawcę usługi Azure Recovery Service w ramach subskrypcji.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Aby sprawdzić, czy dostawców pomyślnie zarejestrowane, używając następujących poleceń:
    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
    W danych wyjściowych polecenia **RegistrationState** należy zmienić na **zarejestrowanej**. Jeśli nie, uruchom **AzResourceProvider rejestru** ponownie polecenie cmdlet.

Następujące zadania można zautomatyzować za pomocą programu PowerShell:

* Tworzenie magazynu usługi Recovery Services
* Konfigurowanie tworzenia kopii zapasowej udziałów plików platformy Azure
* Wyzwalanie zadania tworzenia kopii zapasowej
* Monitorowanie zadania tworzenia kopii zapasowej
* Przywróć udział plików platformy Azure
* Przywracanie pojedynczych plików platformy Azure z udziału plików platformy Azure

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Następujące kroki przeprowadzą Cię przez proces tworzenia magazynu usługi Recovery Services.

1. Magazyn usługi Recovery Services jest zasobu usługi Resource Manager, dlatego należy go umieścić w grupie zasobów. Możesz użyć istniejącej grupy zasobów lub Utwórz grupę zasobów za pomocą **New AzResourceGroup** polecenia cmdlet. Podczas tworzenia grupy zasobów, określ nazwę i lokalizację grupy zasobów.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Użyj **New AzRecoveryServicesVault** polecenia cmdlet, aby utworzyć magazyn usługi Recovery Services. Pamiętaj określić lokalizację tego samego magazynu, która była używana dla grupy zasobów.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Określenie typu nadmiarowości magazynu, które mają być używane; Możesz użyć [magazyn lokalnie nadmiarowy (LRS)](../storage/common/storage-redundancy-lrs.md) lub [magazyn geograficznie nadmiarowy (GRS)](../storage/common/storage-redundancy-grs.md). Poniższy przykład pokazuje, że ustawiono opcję - BackupStorageRedundancy testvault GeoRedundant.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Wyświetlanie magazynów w ramach subskrypcji

Aby wyświetlić wszystkie magazyny w ramach subskrypcji, użyj **Get AzRecoveryServicesVault**:

```powershell
Get-AzRecoveryServicesVault
```

Rezultat jest podobny do poniższego przykładu, zwróć uwagę, że znajdują się skojarzone parametrów ResourceGroupName i lokalizacji.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

Wiele poleceń cmdlet usługi Azure Backup wymaga obiektu magazynu usługi Recovery Services jako danych wejściowych.

Użyj **AzRecoveryServicesVaultContext zestaw** można ustawić kontekst magazynu. Po ustawieniu kontekst magazynu ma zastosowanie do wszystkich kolejnych poleceń cmdlet. W poniższym przykładzie ustawiono kontekst magazynu dla magazynu, *testvault*.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

> [!NOTE]
> Planujemy wycofana ustawienie kontekst magazynu zgodnie z wytycznymi programu Azure PowerShell. Zamiast tego zaleca się użytkownikom na przekazywanie Identyfikatora magazynu zgodnie z poniższymi

Alternatywnie możesz można magazynu/pobierania identyfikator magazynu, do której chcesz wykonać operacji programu PowerShell i przekazywać je do odpowiednich poleceń.

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-backup-for-an-azure-file-share"></a>Skonfigurowanie kopii zapasowej dla udziału plików platformy Azure

### <a name="create-protection-policy"></a>Utwórz zasady ochrony

Zasady tworzenia kopii zapasowej ochrony jest skojarzony z co najmniej jedne zasady przechowywania. Zasady przechowywania Określa, jak długo punkt odzyskiwania jest przechowywana, przed usunięciem. Użyj **Get AzRecoveryServicesBackupRetentionPolicyObject** Aby wyświetlić domyślne zasady przechowywania.  Podobnie można użyć **Get AzRecoveryServicesBackupSchedulePolicyObject** uzyskać domyślne zasady harmonogramu. **New AzRecoveryServicesBackupProtectionPolicy** polecenie cmdlet tworzy obiekt programu PowerShell, który zawiera informacje o zasadach tworzenia kopii zapasowej. Obiekty zasad harmonogram i okres przechowywania są używane jako dane wejściowe **New AzRecoveryServicesBackupProtectionPolicy** polecenia cmdlet. Poniższy przykład zasad harmonogram i zasady przechowywania są przechowywane w zmiennych. W przykładzie użyto tych zmiennych, aby zdefiniować parametry podczas tworzenia zasad ochrony *NewPolicy*.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Dane wyjściowe są podobne do poniższego przykładu:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```

### <a name="enable-protection"></a>Włączanie ochrony

Po zdefiniowaniu zasad ochrony można włączyć ochrony dla udziału plików platformy Azure z tymi zasadami.

Najpierw Pobierz obiekt odpowiednich zasad za pomocą **Get AzRecoveryServicesBackupProtectionPolicy** polecenia cmdlet. Można użyć tego polecenia cmdlet, aby uzyskać szczegółowe zasady lub aby wyświetlić zasady skojarzone z typem obciążenia.

Poniższy przykład pobiera zasady dla obciążeń typu migracji.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

Dane wyjściowe są podobne do poniższego przykładu:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> Strefa czasowa BackupTime pola w programie PowerShell jest czasem UTC. Podczas wykonywania kopii zapasowej jest wyświetlany w witrynie Azure portal, czas jest dostosowywany do lokalnej strefy czasowej.
>
>

Następujące zasady pobiera zasady kopii zapasowych o nazwie "dailyafs"

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

Użyj **AzRecoveryServicesBackupProtection Włącz** Aby włączyć ochronę elementu z danymi zasadami. Gdy zasady zostało skojarzone z magazynem, tworzenia kopii zapasowej przepływ pracy zostanie wyzwolony w czasie, zdefiniowane w harmonogram zasad.

Poniższy przykład umożliwia ochronę udziału plików platformy Azure, "testAzureFileShare", w obszarze magazynu konta "testStorageAcct", przy użyciu dailyafs zasad.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

Polecenie czeka, aż zostanie ukończona zadania konfiguracji ochrony, a także zapewnia podobnych danych wyjściowych, jak pokazano poniżej.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

### <a name="trigger-an-on-demand-backup"></a>Wyzwalanie tworzenia kopii zapasowej na żądanie

Użyj **AzRecoveryServicesBackupItem kopii zapasowej** wyzwalanie zadania tworzenia kopii zapasowej udziału plików platformy Azure chronionych. Pobierz konta i pliku udziału magazynu znajdujący się w nim przy użyciu następujących poleceń i wyzwalanie tworzenia kopii zapasowej na żądanie.

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

Polecenie zwraca zadanie, które mają być śledzone o identyfikatorze jak w poniższym przykładzie.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Firma Microsoft korzysta z migawki udziału plików platformy Azure podczas wykonywania kopii zapasowych i dlatego zwykle ukończeniu zadania przez razem, gdy polecenie jest zwracane te dane wyjściowe

### <a name="modify-protection-policy"></a>Zmodyfikuj zasady ochrony

Jeśli chcesz zmienić zasady, o które jest chronione udziału plików platformy Azure, użyj **AzRecoveryServicesBackupProtection Włącz** przy użyciu odpowiedniego elementu kopii zapasowej i nowych zasad ochrony.

Poniższy przykład zmienia zasady ochrony "testAzureFS" z "dailyafs" do "monthlyafs"

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-sharesazure-files"></a>Przywracanie udziałów plików platformy Azure / Azure plików

Możesz przywrócić cały udział plików, do jego oryginalnej lub alternatywnej lokalizacji. Podobnie poszczególnych plików z udziału plików można przywrócić również.

### <a name="fetching-recovery-points"></a>Pobieranie punktów odzyskiwania

Użyj **Get AzRecoveryServicesBackupRecoveryPoint** polecenia cmdlet, aby wyświetlić listę wszystkich punktów odzyskiwania dla elementu kopii zapasowej. W poniższym skrypcie zmiennej, **$rp**, jest tablicą punkty odzyskiwania dla wybranego elementu kopii zapasowej z ostatnich siedmiu dni. Tablica jest posortowana w kolejności odwrotnej względem czasie przy użyciu najnowszego punktu odzyskiwania pod indeksem 0. Użyj standardowych indeksowanie tablicy programu PowerShell, aby wybrać punkt odzyskiwania. W tym przykładzie $rp [0] wybiera najnowszego punktu odzyskiwania.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

Dane wyjściowe są podobne do poniższego przykładu:

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```

Po wybraniu punktu odzyskiwania istotne, należy przejść do Przywróć udział plików/plik z lokalizacją alternatywny/oryginalny, jak wyjaśniono poniżej.

### <a name="restore-azure-file-shares-to-an-alternate-location"></a>Przywracanie udziałów plików platformy Azure do lokalizacji alternatywnej

#### <a name="restoring-an-azure-file-share"></a>Przywrócenie udziału plików platformy Azure

Określenie alternatywnej lokalizacji, podając następujące informacje:

* ***TargetStorageAccountName***: Konto magazynu, do którego zostanie przywrócona zawartość kopii zapasowej. Docelowe konto magazynu powinny należeć do tej samej lokalizacji co magazyn.
* ***TargetFileShareName***: Udziały plików w ramach docelowe konto magazynu, do którego zostanie przywrócona zawartość kopii zapasowej
* ***TargetFolder***: Folderu w udziale plików, do którego dane są przywracane. Jeśli zawartość kopii zapasowej powinny zostać przywrócone do folderu głównego, należy podać wartości folder docelowy jako pusty ciąg
* ***Wykonanie polecenia ResolveConflict***: Instrukcja w przypadku konfliktu z przywróconych danych. Akceptuje "Zastąp" lub "pomija"

Podaj te parametry do polecenia restore do przywrócenia kopii zapasowej udziału plików do lokalizacji alternatywnej.

````powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
````

Polecenie zwraca zadanie o identyfikatorze mają być śledzone, jak pokazano w poniższym przykładzie.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
````

#### <a name="restoring-an-azure-file"></a>Przywracanie plików platformy Azure

W przypadku chcesz przywrócić poszczególnych plików, a nie cały udział plików, ten plik należy jednoznacznie zidentyfikować, podając następujące parametry.

* ***TargetStorageAccountName***: Konto magazynu, do którego zostanie przywrócona zawartość kopii zapasowej. Docelowe konto magazynu powinny należeć do tej samej lokalizacji co magazyn.
* ***TargetFileShareName***: Udziały plików w ramach docelowe konto magazynu, do którego zostanie przywrócona zawartość kopii zapasowej
* ***TargetFolder***: Folderu w udziale plików, do którego dane są przywracane. Jeśli zawartość kopii zapasowej powinny zostać przywrócone do folderu głównego, należy podać wartości folder docelowy jako pusty ciąg
* ***SourceFilePath***: Ścieżka bezwzględna plików, można przywrócić w udziale plików, w postaci ciągu. Jest to tej samej ścieżki, które są używane w ```Get-AzStorageFile``` PS polecenia cmdlet.
* ***SourceFileType***: Czy katalog lub plik jest zaznaczony. Akceptuje "Directory" lub "File"
* ***Wykonanie polecenia ResolveConflict***: Instrukcja w przypadku konfliktu z przywróconych danych. Akceptuje "Zastąp" lub "pomija"

Jak widać, dodatkowe parametry odnoszą się tylko do pojedynczych plików do przywrócenia.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Spowoduje to zwrócenie również zadania, które mają być śledzone za pomocą Identyfikatora, jak pokazano powyżej.

### <a name="restore-azure-file-shares-to-original-location"></a>Przywracanie udziałów plików platformy Azure do oryginalnej lokalizacji

W przypadku oryginalnej lokalizacji przywracania wszystkie/docelowego związane z parametrów nie musi być określony. Tylko ```ResolveConflict``` musi zostać dostarczona

#### <a name="overwrite-an-azure-file-share"></a>Zastąp udziału plików platformy Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Zastąpienia pliku platformy Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Śledzenie kopii zapasowej i przywracanie zadania

Operacje i przywracania kopii zapasowych na żądanie Zwróć wraz z Identyfikatorem zadania, jak pokazano [powyżej](#trigger-an-on-demand-backup). Użyj ```Get-AzRecoveryServicesBackupJobDetails``` polecenia cmdlet, aby śledzić postęp zadania i pobrać szczegółowe informacje.

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support
```
