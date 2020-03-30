---
title: Przywracanie plików platformy Azure za pomocą programu PowerShell
description: W tym artykule dowiesz się, jak przywrócić usługi Azure Files przy użyciu usługi Azure Backup i programu PowerShell.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 99aeaa6173bb5336e6e1719a9fc0df0c668374e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086824"
---
# <a name="restore-azure-files-with-powershell"></a>Przywracanie plików platformy Azure za pomocą programu PowerShell

W tym artykule wyjaśniono, jak przywrócić cały udział plików lub określonych plików z punktu przywracania utworzonego przez usługę [Azure Backup](backup-overview.md) przy użyciu programu Azure Powershell.

Można przywrócić cały udział plików lub określone pliki w udziale. Można przywrócić do oryginalnej lokalizacji lub do lokalizacji alternatywnej.

> [!WARNING]
> Upewnij się, że wersja PS jest uaktualniona do minimalnej wersji dla "Az.RecoveryServices 2.6.0" dla kopii zapasowych AFS. Aby uzyskać więcej informacji, zapoznaj się [z sekcją](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups) przedstawiającą wymagania dotyczące tej zmiany.

## <a name="fetch-recovery-points"></a>Pobieranie punktów odzyskiwania

Użyj [Get-AzRecoveryServicesBackupRecoveryPoint,](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) aby wyświetlić listę wszystkich punktów odzyskiwania dla elementu kopii zapasowej.

W następującym skrypcie:

* Zmienna **$rp** jest tablicą punktów odzyskiwania dla wybranego elementu kopii zapasowej z ostatnich siedmiu dni.
* Tablica jest sortowana w odwrotnej kolejności czasu z najnowszym punktem odzyskiwania w indeksie **0**.
* Użyj standardowego indeksowania tablicy programu PowerShell, aby wybrać punkt odzyskiwania.
* W tym przykładzie **$rp[0]** wybiera najnowszy punkt odzyskiwania.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

Dane wyjściowe są podobne do następujących.

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

Po wybraniu odpowiedniego punktu odzyskiwania należy przywrócić udział lub plik do oryginalnej lokalizacji lub do lokalizacji alternatywnej.

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Przywracanie udziału plików platformy Azure do lokalizacji alternatywnej

Użyj [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) przywrócić do wybranego punktu odzyskiwania. Określ następujące parametry, aby zidentyfikować alternatywną lokalizację:

* **TargetStorageAccountName:** konto magazynu, na którym jest przywracana kopia zapasowa zawartości. Docelowe konto magazynu musi znajdować się w tej samej lokalizacji co magazyn.
* **TargetFileShareName**: Udziały plików w ramach docelowego konta magazynu, do którego przywracana jest kopia zapasowa zawartości.
* **TargetFolder**: Folder pod udziałem plików, do którego dane są przywracane. Jeśli kopia zapasowa ma zostać przywrócona do folderu głównego, należy podać wartości folderu docelowego jako pusty ciąg.
* **ResolveConflict**: Instrukcja, jeśli występuje konflikt z przywróconymi danymi. Akceptuje **zastąpienie** lub **pominięcie**.

Uruchom polecenie cmdlet z następującymi parametrami:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

Polecenie zwraca zadanie z identyfikatorem do śledzenia, jak pokazano w poniższym przykładzie.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>Przywracanie pliku platformy Azure do lokalizacji alternatywnej

Użyj [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) przywrócić do wybranego punktu odzyskiwania. Określ te parametry, aby zidentyfikować alternatywną lokalizację i jednoznacznie zidentyfikować plik, który chcesz przywrócić.

* **TargetStorageAccountName:** konto magazynu, na którym jest przywracana kopia zapasowa zawartości. Docelowe konto magazynu musi znajdować się w tej samej lokalizacji co magazyn.
* **TargetFileShareName**: Udziały plików w ramach docelowego konta magazynu, do którego przywracana jest kopia zapasowa zawartości.
* **TargetFolder**: Folder pod udziałem plików, do którego dane są przywracane. Jeśli kopia zapasowa ma zostać przywrócona do folderu głównego, należy podać wartości folderu docelowego jako pusty ciąg.
* **SourceFilePath**: Bezwzględna ścieżka pliku, który ma zostać przywrócony w udziale plików, jako ciąg. Ta ścieżka jest tą samą ścieżką, która jest używana w połącze cmdlet **Programu Get-AzStorageFile** PowerShell.
* **SourceFileType**: Czy wybrano katalog, czy plik. Akceptuje **katalog** lub **plik**.
* **ResolveConflict**: Instrukcja, jeśli występuje konflikt z przywróconymi danymi. Akceptuje **zastąpienie** lub **pominięcie**.

Dodatkowe parametry (SourceFilePath i SourceFileType) są powiązane tylko z poszczególnymi plikami, które chcesz przywrócić.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

To polecenie zwraca zadanie z identyfikatorem do śledzenia, jak pokazano w poprzedniej sekcji.

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Przywracanie udziałów plików i plików platformy Azure do oryginalnej lokalizacji

Po przywróceniu do oryginalnej lokalizacji nie trzeba określać parametrów związanych z miejscem docelowym i docelowym. Należy podać **tylko ResolveConflict.**

#### <a name="overwrite-an-azure-file-share"></a>Zastępowanie udziału plików platformy Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Zastępowanie pliku platformy Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o](restore-afs.md) przywracaniu plików platformy Azure w witrynie Azure portal.
