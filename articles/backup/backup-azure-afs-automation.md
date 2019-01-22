---
title: Wdrażanie i zarządzanie kopiami zapasowymi udziałów plików platformy Azure przy użyciu programu PowerShell
description: Wdrażanie i zarządzanie nimi kopii zapasowych na platformie Azure, udziałów plików platformy Azure przy użyciu programu PowerShell
services: backup
author: pvrk
manager: shivamg
keywords: Program PowerShell; Usługa Azure Files backup; Przywracanie plików platformy Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: pullabhk
ms.assetid: 80da8ece-2cce-40dd-8dce-79960b6ae073
ms.openlocfilehash: bbeccd03fffb699c95d52d50ec5c45e38b43ef51
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54430402"
---
# <a name="use-powershell-to-back-up-and-restore-azure-file-shares"></a>Użyj programu PowerShell do tworzenia kopii i przywracanie udziałów plików platformy Azure

W tym artykule pokazano, jak tworzyć kopie zapasowe i odzyskiwanie udziału plików platformy Azure z magazynu usługi Recovery Services za pomocą poleceń cmdlet programu Azure PowerShell. Magazyn usługi Recovery Services jest zasobem usługi Azure Resource Manager, który służy do ochrony danych i zasobów w usłudze Azure Backup i Azure Site Recovery.

## <a name="concepts"></a>Pojęcia

Jeśli nie znasz z usługą Azure Backup z omówieniem usługi, zobacz [co to jest usługa Azure Backup?](backup-introduction-to-azure-backup.md). Przed rozpoczęciem wykonywania tej procedury, zobacz możliwości w wersji zapoznawczej, które są używane do kopii zapasowej Azure pliku udziałów w [udziały plików kopii zapasowej Azure](backup-azure-files.md).

Skutecznie przy użyciu programu PowerShell, jest niezbędne do hierarchii obiektów i gdzie należy zacząć od zrozumienia.

![Hierarchia obiektów usług odzyskiwania](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Aby wyświetlić **AzureRm.RecoveryServices.Backup** Dokumentacja poleceń cmdlet programu PowerShell, zobacz [usługi Azure Backup — polecenia cmdlet usługi Recovery Services](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) w bibliotece platformy Azure.

## <a name="setup-and-registration"></a>Konfiguracja i rejestracja

> [!NOTE]
> Jak wspomniano w [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0), obsługa nowych funkcji w usłudze AzureRM modułu nazwą kończącą się listopada 2018 r. Pomoc techniczna jest świadczona w celu wykonania kopii zapasowej udziałów plików platformy Azure za pomocą nowego modułu Az PowerShell, która jest teraz ogólnie dostępna.

Wykonaj następujące kroki, aby rozpocząć.

1. [Pobierz najnowszą wersję programu Az PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azurermps-6.13.0). Minimalna wymagana wersja to 1.0.0.

2. Znajdź **programu Kopia zapasowa Azure PowerShell** dostępne, wprowadzając następujące polecenie cmdlet.

    ```powershell
    Get-Command *azrecoveryservices*
    ```
    Aliasy i polecenia cmdlet dla usługi Kopia zapasowa Azure, usługi Azure Site Recovery i magazyn usługi Recovery Services są wyświetlane. Poniższy rysunek jest przykładem zostanie wyświetlony. Nie jest pełną listę poleceń cmdlet.

    ![Listę poleceń cmdlet usługi Recovery Services](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. Zaloguj się do konta platformy Azure przy użyciu **Connect AzAccount**. To polecenie cmdlet powoduje wyświetlenie strony sieci web, która wyświetli monit o podanie poświadczeń konta:

    * Alternatywnie można uwzględnić poświadczeń konta jako parametru w **Connect AzAccount** polecenia cmdlet przy użyciu **-poświadczeń** parametru.
    * Jeśli jesteś partnerem CSP pracującym w imieniu dzierżawy, określ klienta jako dzierżawca, za pomocą ich identyfikatora dzierżawy lub dzierżawy podstawowej nazwy domeny. Na przykład **Connect AzAccount-dzierżawy** fabrikam.com.

4. Skojarz subskrypcję, której chcesz użyć przy użyciu konta, ponieważ konto może mieć wiele subskrypcji.

    ```powershell
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Jeśli używasz usługi Azure Backup po raz pierwszy, użyj **AzResourceProvider rejestru** polecenia cmdlet, aby zarejestrować dostawcę usługi Azure Recovery Services w ramach subskrypcji.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Sprawdź, czy dostawców pomyślnie zarejestrowana przy użyciu następującego polecenia.
    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
    W danych wyjściowych polecenia **RegistrationState** zmieni się na **zarejestrowanej**. Jeśli nie widzisz tej zmiany, uruchom **AzResourceProvider rejestru** ponownie polecenie cmdlet.

Następujące zadania można zautomatyzować za pomocą programu PowerShell:

* Utwórz magazyn usługi Recovery Services.
* Konfigurowanie kopii zapasowej udziałów plików platformy Azure.
* Wyzwalanie zadania tworzenia kopii zapasowej.
* Monitorowanie zadania tworzenia kopii zapasowej.
* Przywróć udział plików platformy Azure.
* Przywracanie pojedynczych plików platformy Azure z udziału plików platformy Azure.

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Wykonaj następujące kroki, aby utworzyć magazyn usługi Recovery Services.

1. Magazyn usługi Recovery Services jest zasobem usługi Resource Manager, należy go umieścić w grupie zasobów. Można użyć istniejącej grupy zasobów lub utworzyć nową grupę zasobów o **New AzResourceGroup** polecenia cmdlet. Podczas tworzenia grupy zasobów, określ nazwę i lokalizację grupy zasobów.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Użyj **New AzRecoveryServicesVault** polecenia cmdlet, aby utworzyć magazyn usługi Recovery Services. Określ lokalizację tego samego magazynu, która była używana dla grupy zasobów.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Określenie typu nadmiarowości magazynu do użycia. Możesz użyć [magazyn lokalnie nadmiarowy](../storage/common/storage-redundancy-lrs.md) lub [magazyn geograficznie nadmiarowy](../storage/common/storage-redundancy-grs.md). W poniższym przykładzie przedstawiono **- BackupStorageRedundancy** opcja dla **testvault** równa **GeoRedundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Wyświetlanie magazynów w ramach subskrypcji

Aby wyświetlić wszystkie magazyny w ramach subskrypcji, użyj **Get AzRecoveryServicesVault**.

```powershell
Get-AzRecoveryServicesVault
```

Dane wyjściowe będą podobne do poniższego przykładu. Należy zauważyć, że skojarzone **ResourceGroupName** i **lokalizacji** są dostarczane.

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

Użyj **AzRecoveryServicesVaultContext zestaw** można ustawić kontekst magazynu. Po ustawieniu kontekst magazynu ma zastosowanie do wszystkich kolejnych poleceń cmdlet. W poniższym przykładzie ustawiono kontekst magazynu dla **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

> [!NOTE]
> Planujemy wycofana ustawieniu kontekst magazynu zgodnie z wytycznymi programu Azure PowerShell. Zamiast tego zaleca się, że użytkownicy przekazać identyfikator magazynu zgodnie z opisem w poniższych instrukcji.

Alternatywnie magazynu lub pobrać identyfikator magazynu, do której chcesz wykonać operacji programu PowerShell i przekazywać je do odpowiednich poleceń.

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-backup-for-an-azure-file-share"></a>Skonfigurowanie kopii zapasowej dla udziału plików platformy Azure

### <a name="create-a-protection-policy"></a>Tworzenie zasad ochrony

Zasady tworzenia kopii zapasowej ochrony jest skojarzony z co najmniej jedne zasady przechowywania. Zasady przechowywania Określa, jak długo punkt odzyskiwania jest przechowywana, przed usunięciem. Użyj **Get AzRecoveryServicesBackupRetentionPolicyObject** Aby wyświetlić domyślne zasady przechowywania. 

Podobnie, można użyć **Get AzRecoveryServicesBackupSchedulePolicyObject** uzyskać domyślne zasady harmonogramu. **New AzRecoveryServicesBackupProtectionPolicy** polecenie cmdlet tworzy obiekt programu PowerShell, który zawiera informacje o zasadach tworzenia kopii zapasowej. Obiekty zasad harmonogram i okres przechowywania są używane jako dane wejściowe **New AzRecoveryServicesBackupProtectionPolicy** polecenia cmdlet. 

Poniższy przykład zasad harmonogram i zasady przechowywania są przechowywane w zmiennych. W przykładzie użyto tych zmiennych, aby zdefiniować parametry podczas **NewPolicy** tworzone są zasady ochrony.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Dane wyjściowe będą podobne do poniższego przykładu.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```

**NewAFSPolicy** przyjmuje codzienne wykonywanie kopii zapasowych oraz przechowujące ją przez 30 dni.

### <a name="enable-protection"></a>Włączanie ochrony

Po zdefiniowaniu zasad ochrony, aby umożliwić ochronę udziału plików platformy Azure z tymi zasadami.

Najpierw Pobierz obiekt odpowiednich zasad za pomocą **Get AzRecoveryServicesBackupProtectionPolicy** polecenia cmdlet. Użyj następującego polecenia cmdlet, aby wyświetlić zasady skojarzone z typem obciążenia lub uzyskania określonych zasad.

Poniższy przykład pobiera zasady dla typu obciążenia **migracji**.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

Dane wyjściowe będą podobne do poniższego przykładu.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> Strefa czasowa **BackupTime** pola w programie PowerShell jest uniwersalny czas koordynowany (UTC). Podczas wykonywania kopii zapasowej jest wyświetlany w witrynie Azure portal, czas jest dopasowywana do lokalnej strefy czasowej.
>
>

Następujące zasady pobiera zasady kopii zapasowych o nazwie **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

Użyj **AzRecoveryServicesBackupProtection Włącz** Aby włączyć ochronę elementu z danymi zasadami. Po skojarzone z magazynem zasad tworzenia kopii zapasowej przepływ pracy zostanie wyzwolony w czasie, zdefiniowane w harmonogram zasad.

Poniższy przykład umożliwia włączenie ochrony udziału plików platformy Azure **testAzureFileShare** na koncie magazynu **testStorageAcct** z zasadami **dailyafs**.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

Polecenie czeka, aż zakończone zadania konfiguracji ochrony oraz udostępnia dane wyjściowe podobne, jak pokazano.

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

Polecenie zwraca zadanie o identyfikatorze mają być śledzone, jak pokazano w poniższym przykładzie.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Migawki udziału plików platformy Azure są używane podczas tworzenia kopii zapasowych są wykonywane, więc zazwyczaj zadanie zostanie ukończone do czasu, polecenie zwraca następujące dane wyjściowe.

### <a name="modify-the-protection-policy"></a>Zmodyfikuj zasady ochrony

Aby zmienić zasady, z którą jest chroniony udziału plików platformy Azure, użyj **AzRecoveryServicesBackupProtection Włącz** przy użyciu odpowiedniego elementu kopii zapasowej i nowych zasad ochrony.

Następujący przykład zmienia **testAzureFS** zasady ochrony na podstawie **dailyafs** do **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-azure-files"></a>Przywracanie udziałów plików platformy Azure i usługi Azure files

Możesz przywrócić cały udział plików do lokalizacji oryginalnej lub alternatywnej lokalizacji. Podobnie pojedyncze pliki z udziału plików można przywracać, zbyt.

### <a name="fetch-recovery-points"></a>Pobierz punkty odzyskiwania

Użyj **Get AzRecoveryServicesBackupRecoveryPoint** polecenia cmdlet, aby wyświetlić listę wszystkich punktów odzyskiwania dla elementu kopii zapasowej. W poniższym skrypcie zmiennej **$rp** jest tablicą, punkty odzyskiwania dla wybranego elementu kopii zapasowej z ostatnich siedmiu dni. Tablica jest posortowana w odwrotnej kolejności czasie przy użyciu najnowszego punktu odzyskiwania w indeksie **0**. Użyj standardowych indeksowanie tablicy programu PowerShell, aby wybrać punkt odzyskiwania. W tym przykładzie **$rp [0]** wybiera najnowszego punktu odzyskiwania.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

Dane wyjściowe będą podobne do poniższego przykładu.

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

Po wybraniu punktu odzyskiwania odpowiednie przywrócić udziału plików lub plików do lokalizacji alternatywnej lub oryginalnej lokalizacji zgodnie z opisem w tym miejscu.

### <a name="restore-azure-file-shares-to-an-alternate-location"></a>Przywracanie udziałów plików platformy Azure do lokalizacji alternatywnej

#### <a name="restore-an-azure-file-share"></a>Przywróć udział plików platformy Azure

Określenie alternatywnej lokalizacji, podając następujące informacje:

* **TargetStorageAccountName**: Konto magazynu, do którego zostanie przywrócona zawartość kopii zapasowej. Docelowe konto magazynu musi być w tej samej lokalizacji co magazyn.
* **TargetFileShareName**: Konto zostanie przywrócona zawartość kopii zapasowej udziałów plików w ramach magazynu docelowego.
* **TargetFolder**: Folderu w udziale plików, do którego dane są przywracane. Jeśli zawartość kopii zapasowej można przywrócić do folderu głównego, należy podać wartości folder docelowy jako pusty ciąg.
* **ResolveConflict**: Instrukcja, jeśli występuje konflikt z przywróconych danych. Akceptuje **zastąpić** lub **Pomiń**.

Podaj te parametry do polecenia restore, aby przywrócić udziału plików kopii zapasowej do lokalizacji alternatywnej.

````powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
````

Polecenie zwraca zadanie o identyfikatorze mają być śledzone, jak pokazano w poniższym przykładzie.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
````

#### <a name="restore-an-azure-file"></a>Przywracanie plików platformy Azure

Aby przywrócić poszczególnych plików, a nie cały udział plików, identyfikują poszczególnych plików, podając następujące parametry:

* **TargetStorageAccountName**: Konto magazynu, do którego zostanie przywrócona zawartość kopii zapasowej. Docelowe konto magazynu musi być w tej samej lokalizacji co magazyn.
* **TargetFileShareName**: Konto zostanie przywrócona zawartość kopii zapasowej udziałów plików w ramach magazynu docelowego.
* **TargetFolder**: Folderu w udziale plików, do którego dane są przywracane. Jeśli zawartość kopii zapasowej można przywrócić do folderu głównego, należy podać wartości folder docelowy jako pusty ciąg.
* **SourceFilePath**: Ścieżka bezwzględna plików, można przywrócić w udziale plików, w postaci ciągu. Ta ścieżka jest tej samej ścieżki, które są używane w **Get AzStorageFile** polecenia cmdlet programu PowerShell.
* **SourceFileType**: Czy katalog lub plik jest zaznaczony. Akceptuje **katalogu** lub **pliku**.
* **ResolveConflict**: Instrukcja, jeśli występuje konflikt z przywróconych danych. Akceptuje **zastąpić** lub **Pomiń**.

Dodatkowe parametry są powiązane tylko pojedynczy plik, który ma zostać przywrócone.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

To polecenie zwraca również wartość zadania o identyfikatorze mają być śledzone, jak przedstawiono wcześniej.

### <a name="restore-azure-file-shares-to-the-original-location"></a>Przywracanie udziałów plików platformy Azure do oryginalnej lokalizacji

Podczas przywracania do oryginalnej lokalizacji wszystkie parametry dotyczące przeznaczenia i docelowy nie trzeba określać. Tylko **ResolveConflict** musi zostać podana.

#### <a name="overwrite-an-azure-file-share"></a>Zastąp udziału plików platformy Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Zastąpienia pliku platformy Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Śledzenie kopii zapasowej i przywracanie zadania

Operacje i przywracania kopii zapasowych na żądanie zwrócenie zadania oraz Identyfikatora, jak pokazano w poprzedniej sekcji ["Wyzwól kopię zapasową na żądanie".](#trigger-an-on-demand-backup) Użyj **Get AzRecoveryServicesBackupJobDetails** polecenia cmdlet, aby śledzić postęp zadania i pobrać więcej szczegółów.

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
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```
