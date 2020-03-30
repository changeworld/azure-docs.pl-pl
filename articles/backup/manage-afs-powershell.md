---
title: Zarządzanie kopiami zapasowymi udziału plików platformy Azure za pomocą programu PowerShell
description: Dowiedz się, jak zarządzać udziałami plików platformy Azure i monitorować je w usłudze Azure Backup.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: a9dc421db740963fc5cd11e868eb383694376ce1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083167"
---
# <a name="manage-azure-file-share-backups-with-powershell"></a>Zarządzanie kopiami zapasowymi udziału plików platformy Azure za pomocą programu PowerShell

W tym artykule opisano sposób używania programu Azure PowerShell do zarządzania i monitorowania udziałów plików platformy Azure, które są archiwizowane przez usługę Azure Backup.

> [!WARNING]
> Upewnij się, że wersja PS jest uaktualniona do minimalnej wersji dla "Az.RecoveryServices 2.6.0" dla kopii zapasowych AFS. Aby uzyskać więcej informacji, zapoznaj się [z sekcją](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups) przedstawiającą wymagania dotyczące tej zmiany.

## <a name="modify-the-protection-policy"></a>Modyfikowanie zasad ochrony

Aby zmienić zasady używane do tworzenia kopii zapasowej udziału plików platformy Azure, użyj [enable-AzRecoveryServiceBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Określ odpowiedni element kopii zapasowej i nowe zasady tworzenia kopii zapasowej.

Poniższy przykład zmienia **testAzureFS** zasady ochrony z **dailyafs** **do monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="track-backup-and-restore-jobs"></a>Śledzenie zadań tworzenia kopii zapasowych i przywracania

Operacje tworzenia kopii zapasowych i przywracania na żądanie zwracają zadanie wraz z identyfikatorem, jak pokazano po [uruchomieniu kopii zapasowej na żądanie](backup-azure-afs-automation.md#trigger-an-on-demand-backup). Użyj polecenia cmdlet [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) do śledzenia postępu zadania i szczegółów.

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

## <a name="stop-protection-on-a-file-share"></a>Zatrzymywanie ochrony udziału plików

Istnieją dwa sposoby na zatrzymanie ochrony udziałów plików platformy Azure:

* Zatrzymaj wszystkie przyszłe zadania tworzenia kopii zapasowych i *usuń* wszystkie punkty odzyskiwania
* Zatrzymaj wszystkie przyszłe zadania tworzenia kopii *zapasowych,* ale zostaw punkty odzyskiwania

Może istnieć koszt związany z opuszczeniem punktów odzyskiwania w magazynie, ponieważ podstawowe migawki utworzone przez usługę Azure Backup zostaną zachowane. Jednak zaletą pozostawienia punktów odzyskiwania jest można przywrócić udział plików później, w razie potrzeby. Aby uzyskać informacje na temat kosztów opuszczenia punktów odzyskiwania, zobacz [szczegóły cen](https://azure.microsoft.com/pricing/details/storage/files/). Jeśli zdecydujesz się usunąć wszystkie punkty odzyskiwania, nie możesz przywrócić udziału plików.

## <a name="stop-protection-and-retain-recovery-points"></a>Zatrzymaj ochronę i zachowaj punkty odzyskiwania

Aby zatrzymać ochronę podczas zachowywania danych, należy użyć polecenia cmdlet [Disable-AzRecoveryServicesBackupProtection.](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0)

Poniższy przykład zatrzymuje ochronę udziału plików *afsfileshare,* ale zachowuje wszystkie punkty odzyskiwania:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID
```

```output
WorkloadName     Operation         Status         StartTime                 EndTime                   JobID
------------     ---------         ------         ---------                 -------                   -----
afsfileshare     DisableBackup     Completed      1/26/2020 2:43:59 PM      1/26/2020 2:44:21 PM      98d9f8a1-54f2-4d85-8433-c32eafbd793f
```

Atrybut Identyfikator zadania w danych wyjściowych odpowiada identyfikatorowi zadania zadania utworzonego przez usługę tworzenia kopii zapasowej dla operacji "ochrona zatrzymania". Aby śledzić stan zadania, należy użyć polecenia cmdlet [Get-AzRecoveryServicesBackupJob.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-3.3.0)

## <a name="stop-protection-without-retaining-recovery-points"></a>Zatrzymaj ochronę bez zatrzymywania punktów odzyskiwania

Aby zatrzymać ochronę bez zachowywania punktów odzyskiwania, należy użyć polecenia cmdlet [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0) i dodaj parametr **-RemoveRecoveryPoints.**

Poniższy przykład zatrzymuje ochronę udziału plików *afsfileshare* bez zachowywania punktów odzyskiwania:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID -RemoveRecoveryPoints
```

```output
WorkloadName     Operation            Status         StartTime                 EndTime                   JobID
------------     ---------            ------         ---------                 -------                   -----
afsfileshare     DeleteBackupData     Completed      1/26/2020 2:50:57 PM      1/26/2020 2:51:39 PM      b1a61c0b-548a-4687-9d15-9db1cc5bcc85
```

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o](manage-afs-backup.md) zarządzaniu kopiami zapasowymi udziałów plików platformy Azure w witrynie Azure portal.
