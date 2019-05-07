---
title: Tworzenie kopii zapasowej i przywracanie usługi Azure Files przy użyciu usługi Kopia zapasowa Azure i programu PowerShell
description: Tworzenie kopii zapasowej i przywracanie usługi Azure Files przy użyciu usługi Kopia zapasowa Azure i programu PowerShell.
author: pvrk
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: pullabhk
ms.openlocfilehash: 986414d0bac24d0c7e37b34df473346742fa97fd
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204180"
---
# <a name="back-up-and-restore-azure-files-with-powershell"></a>Tworzenie kopii zapasowej i przywracanie usługi Azure Files przy użyciu programu PowerShell

W tym artykule opisano sposób używania programu Azure PowerShell do tworzenia kopii i odzyskiwania usługi Azure Files pliku udziału za pomocą [kopia zapasowa Azure](backup-overview.md) magazyn usługi Recovery Services. 

W tym samouczku wyjaśniono:

> [!div class="checklist"]
> * Konfigurowanie programu PowerShell i zarejestrować dostawcę usługi Azure Recovery Services.
> * Utwórz magazyn usługi Recovery Services.
> * Konfigurowanie kopii zapasowej udziału plików platformy Azure.
> * Uruchom zadanie tworzenia kopii zapasowej.
> * Przywracanie kopii zapasowej udziału plików platformy Azure lub pojedynczy plik z udziału.
> * Monitorowanie kopii zapasowych i przywracania zadania.


## <a name="before-you-start"></a>Przed rozpoczęciem

- [Dowiedz się więcej](backup-azure-recovery-services-vault-overview.md) dotyczących magazynów usługi Recovery Services.
- Przeczytaj o możliwości w wersji zapoznawczej [tworzenia kopii zapasowej udziałów plików platformy Azure](backup-azure-files.md).
- Przejrzyj hierarchii obiektów programu PowerShell dla usługi Recovery Services.


## <a name="recovery-services-object-hierarchy"></a>Hierarchia obiektów usług odzyskiwania

Hierarchia obiektów jest podsumowywane na poniższym diagramie.

![Hierarchia obiektów usług odzyskiwania](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Przegląd **Az.RecoveryServices** [informacje o poleceniach cmdlet](/powershell/module/az.recoveryservices) odwołania w bibliotece platformy Azure.


## <a name="set-up-and-install"></a>Konfigurowanie i instalowanie

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Konfigurowanie programu PowerShell w następujący sposób:

1. [Pobierz najnowszą wersję programu Az PowerShell](/powershell/azure/install-az-ps). Minimalna wymagana wersja to 1.0.0.

2. Znajdowanie poleceń cmdlet programu Azure PowerShell w kopii zapasowej za pomocą następującego polecenia:

    ```powershell
    Get-Command *azrecoveryservices*
    ```
3. Przejrzyj aliasów i poleceń cmdlet dla usługi Kopia zapasowa Azure, usługi Azure Site Recovery i magazyn usługi Recovery Services są wyświetlane. Poniżej przedstawiono przykładowy wygląd ekranu. Nie jest pełną listę poleceń cmdlet.

    ![Listę poleceń cmdlet usługi Recovery Services](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. Zaloguj się do konta platformy Azure za pomocą **Connect AzAccount**.
4. Na stronie sieci web, która pojawia się zostanie wyświetlony monit wprowadź poświadczenia konta.

    - Alternatywnie można uwzględnić poświadczeń konta jako parametru w **Connect AzAccount** polecenia cmdlet z **-poświadczeń**.
    - Jeśli jesteś partnerem CSP pracującym w imieniu dzierżawy, należy określić klienta jako dzierżawca, przy użyciu nazwy domeny głównej identyfikator dzierżawy lub jednego dzierżawcy. Na przykład **Connect AzAccount-dzierżawy** fabrikam.com.

4. Skojarz subskrypcję, której chcesz użyć przy użyciu konta, ponieważ konto może mieć wiele subskrypcji.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Jeśli używasz usługi Azure Backup po raz pierwszy, użyj **AzResourceProvider rejestru** polecenia cmdlet, aby zarejestrować dostawcę usługi Azure Recovery Services w ramach subskrypcji.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Sprawdź pomyślnie zarejestrowano dostawców:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
7. W danych wyjściowych polecenia Sprawdź, czy **RegistrationState** zmieni się na **zarejestrowanej**. Jeśli nie działa ona **AzResourceProvider rejestru** ponownie polecenie cmdlet.



## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Wykonaj następujące kroki, aby utworzyć magazyn usługi Recovery Services.

- Magazyn usługi Recovery Services jest zasobem usługi Resource Manager, należy go umieścić w grupie zasobów. Można użyć istniejącej grupy zasobów lub utworzyć nową grupę zasobów o **New AzResourceGroup** polecenia cmdlet. Podczas tworzenia grupy zasobów, określ nazwę i lokalizację grupy zasobów. 

1. Magazyn jest umieszczany w grupie zasobów. Jeśli nie masz istniejącego zasobu, grupy, Utwórz nową grupę za pomocą [New AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). W tym przykładzie utworzymy nową grupę zasobów w regionie zachodnie stany USA.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```
2. Użyj [New AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) polecenie cmdlet do tworzenia w magazynie. Określ lokalizację tego samego magazynu, która była używana dla grupy zasobów.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Określenie typu nadmiarowości magazynu magazynu.

   - Możesz użyć [magazyn lokalnie nadmiarowy](../storage/common/storage-redundancy-lrs.md) lub [magazyn geograficznie nadmiarowy](../storage/common/storage-redundancy-grs.md).
   - Poniższy przykład ustawia **- BackupStorageRedundancy** opcja dla[AzRecoveryServicesBackupProperties zestaw](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd dla **testvault** równa  **GeoRedundant**.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>Wyświetlanie magazynów w ramach subskrypcji

Aby wyświetlić wszystkie magazyny w ramach subskrypcji, użyj [Get AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

Rezultat jest podobny do następującego. Należy pamiętać, grupy zasobów i lokalizacji znajdują.

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

- Wiele poleceń cmdlet usługi Azure Backup wymaga obiektu magazynu usługi Recovery Services jako danych wejściowych, więc jest to wygodne przechowywanie obiektu magazynu w zmiennej.
- Kontekst magazynu to typ danych chronionych w magazynie. Ustaw ją za pomocą [AzRecoveryServicesVaultContext zestaw](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Po ustawieniu kontekst ma zastosowanie do wszystkich kolejnych poleceń cmdlet.


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

- Zasady tworzenia kopii zapasowych jest skojarzony z co najmniej jedne zasady przechowywania. Zasady przechowywania Określa, jak długo punkt odzyskiwania jest przechowywana, przed usunięciem.
- Domyślne zasady tworzenia kopii zapasowej przechowywania przy użyciu widoku [Get AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
- Widok, używając harmonogram zasad tworzenia kopii zapasowej domyślne [Get AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
-  Możesz użyć [New AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) polecenia cmdlet, aby utworzyć nowe zasady kopii zapasowych. Wprowadzona obiektów zasad harmonogram i okres przechowywania.

Poniższy przykład zasad harmonogram i zasady przechowywania są przechowywane w zmiennych. Następnie używa tych zmiennej jako parametry dla nowych zasad (**NewAFSPolicy**). **NewAFSPolicy** przyjmuje codzienne wykonywanie kopii zapasowych oraz przechowujące ją przez 30 dni.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

Rezultat jest podobny do następującego.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```



## <a name="enable-backup"></a>Włącz wykonywanie kopii zapasowej

Po zdefiniowaniu zasad tworzenia kopii zapasowej, można włączyć ochrony dla udziału plików platformy Azure za pomocą zasad.

### <a name="retrieve-a-backup-policy"></a>Pobieranie zasad tworzenia kopii zapasowej

Pobierz obiekt odpowiednich zasad, z [Get AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Użyj następującego polecenia cmdlet, aby uzyskać szczegółowe zasady lub aby wyświetlić zasady skojarzone z typem obciążenia.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Pobieranie zasad dla typu obciążenia

Poniższy przykład pobiera zasady dla typu obciążenia **migracji**.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

Rezultat jest podobny do następującego.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```
> [!NOTE]
> Strefa czasowa **BackupTime** pola w programie PowerShell jest uniwersalny czas koordynowany (UTC). Podczas wykonywania kopii zapasowej jest wyświetlany w witrynie Azure portal, czas jest dopasowywana do lokalnej strefy czasowej.

### <a name="retrieve-a-specific-policy"></a>Pobieranie określonych zasad

Następujące zasady pobiera zasady kopii zapasowych o nazwie **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Włącz wykonywanie kopii zapasowej i Zastosuj zasady

Włącz ochronę za pomocą [AzRecoveryServicesBackupProtection Włącz](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Po zasady są skojarzone z magazynem, tworzenie kopii zapasowych są wyzwalane zgodnie z harmonogramem zasad.

Poniższy przykład umożliwia włączenie ochrony udziału plików platformy Azure **testAzureFileShare** na koncie magazynu **testStorageAcct**, za pomocą zasad **dailyafs**.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

Polecenie czeka, aż zakończone zadania konfiguracji ochrony oraz udostępnia dane wyjściowe podobne, jak pokazano.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="trigger-an-on-demand-backup"></a>Wyzwalanie tworzenia kopii zapasowej na żądanie

Użyj [AzRecoveryServicesBackupItem kopii zapasowej](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) do uruchamiania kopii zapasowej na żądanie dla udziału plików platformy Azure chronionych.

1. Pobierz konto magazynu i udziału plików z kontenera w magazynie, który przechowuje dane kopii zapasowej za pomocą [Get AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Aby uruchomić zadanie tworzenia kopii zapasowej, można uzyskać informacji na temat maszyny Wirtualnej przy użyciu [Get AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Uruchom tworzenie kopii zapasowej na żądanie przy użyciu[AzRecoveryServicesBackupItem kopii zapasowej](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Uruchom kopii zapasowej na żądanie w następujący sposób:
    
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

Aby zmienić zasady tworzenia kopii zapasowej udziału plików platformy Azure, użyj [AzRecoveryServicesBackupProtection Włącz](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Określ odpowiedni element kopii zapasowej i nowe zasady kopii zapasowych.

Następujący przykład zmienia **testAzureFS** zasady ochrony na podstawie **dailyafs** do **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-files"></a>Przywracanie plików i udziałów plików platformy Azure

Możesz przywrócić cały udział plików lub określonych plików w udziale. Można przywrócić do oryginalnej lokalizacji lub do lokalizacji alternatywnej. 

### <a name="fetch-recovery-points"></a>Pobierz punkty odzyskiwania

Użyj [Get AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) Aby wyświetlić listę wszystkich punktów odzyskiwania dla elementu kopii zapasowej.

W poniższym skrypcie:

- Zmienna **$rp** jest tablicą, punkty odzyskiwania dla wybranego elementu kopii zapasowej z ostatnich siedmiu dni.
- Tablica jest posortowana w odwrotnej kolejności czasie przy użyciu najnowszego punktu odzyskiwania w indeksie **0**.
- Użyj standardowych indeksowanie tablicy programu PowerShell, aby wybrać punkt odzyskiwania.
- W tym przykładzie **$rp [0]** wybiera najnowszego punktu odzyskiwania.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

Rezultat jest podobny do następującego.

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
Po wybraniu punktu odzyskiwania odpowiednie możesz przywrócić udziału plików lub plików do lokalizacji oryginalnej lub alternatywnej lokalizacji.

### <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Przywróć udział plików platformy Azure do lokalizacji alternatywnej

Użyj [AzRecoveryServicesBackupItem przywracania](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) można przywrócić do wybranego punktu odzyskiwania. Określ parametry, aby zidentyfikować w alternatywnej lokalizacji: 

- **TargetStorageAccountName**: Konto magazynu, do którego zostanie przywrócona zawartość kopii zapasowej. Docelowe konto magazynu musi być w tej samej lokalizacji co magazyn.
- **TargetFileShareName**: Konto zostanie przywrócona zawartość kopii zapasowej udziałów plików w ramach magazynu docelowego.
- **TargetFolder**: Folderu w udziale plików, do którego dane są przywracane. Jeśli zawartość kopii zapasowej można przywrócić do folderu głównego, należy podać wartości folder docelowy jako pusty ciąg.
- **ResolveConflict**: Instrukcja, jeśli występuje konflikt z przywróconych danych. Akceptuje **zastąpić** lub **Pomiń**.

Uruchom polecenie cmdlet z parametrami w następujący sposób:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

Polecenie zwraca zadanie o identyfikatorze mają być śledzone, jak pokazano w poniższym przykładzie.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

### <a name="restore-an-azure-file-to-an-alternate-location"></a>Przywracanie plików platformy Azure do lokalizacji alternatywnej

Użyj [AzRecoveryServicesBackupItem przywracania](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) można przywrócić do wybranego punktu odzyskiwania. Określ te parametry, aby zidentyfikować lokalizacji alternatywnej, a do unikatowego identyfikowania plików, które mają zostać przywrócone.

* **TargetStorageAccountName**: Konto magazynu, do którego zostanie przywrócona zawartość kopii zapasowej. Docelowe konto magazynu musi być w tej samej lokalizacji co magazyn.
* **TargetFileShareName**: Konto zostanie przywrócona zawartość kopii zapasowej udziałów plików w ramach magazynu docelowego.
* **TargetFolder**: Folderu w udziale plików, do którego dane są przywracane. Jeśli zawartość kopii zapasowej można przywrócić do folderu głównego, należy podać wartości folder docelowy jako pusty ciąg.
* **SourceFilePath**: Ścieżka bezwzględna plików, można przywrócić w udziale plików, w postaci ciągu. Ta ścieżka jest tej samej ścieżki, które są używane w **Get AzStorageFile** polecenia cmdlet programu PowerShell.
* **SourceFileType**: Czy katalog lub plik jest zaznaczony. Akceptuje **katalogu** lub **pliku**.
* **ResolveConflict**: Instrukcja, jeśli występuje konflikt z przywróconych danych. Akceptuje **zastąpić** lub **Pomiń**.

Dodatkowe parametry (SourceFilePath i SourceFileType) są związane tylko z poszczególnych plików, który chcesz przywrócić.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

To polecenie zwraca zadanie o identyfikatorze mają być śledzone, jak pokazano w poprzedniej sekcji.

### <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Przywracanie plików i udziałów plików platformy Azure do oryginalnej lokalizacji

Podczas przywracania do oryginalnej lokalizacji nie trzeba określić parametry dotyczące przeznaczenia i docelowej. Tylko **ResolveConflict** musi zostać podana.

#### <a name="overwrite-an-azure-file-share"></a>Zastąp udziału plików platformy Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Zastąpienia pliku platformy Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Śledzenie kopii zapasowej i przywracanie zadania

Operacje i przywracania kopii zapasowych na żądanie zwrócenie zadania wraz z Identyfikatora, tak jak możesz [uruchomiono kopii zapasowej na żądanie](#trigger-an-on-demand-backup). Użyj [Get AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) polecenia cmdlet, aby śledzić postęp zadania i szczegółowe informacje.

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
## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o](backup-azure-files.md) tworzenia kopii zapasowych usługi Azure Files w witrynie Azure portal.
