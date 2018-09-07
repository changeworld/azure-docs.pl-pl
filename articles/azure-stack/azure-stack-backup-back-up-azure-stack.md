---
title: Tworzenie kopii zapasowej usługi Azure Stack | Dokumentacja firmy Microsoft
description: Wykonaj kopię zapasową na żądanie w usłudze Azure Stack, przy użyciu kopii zapasowej na miejscu.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: a11de2a4580515f6a358438a706e5be3f5543e28
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "44025314"
---
# <a name="back-up-azure-stack"></a>Tworzenie kopii zapasowych usługi Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Wykonaj kopię zapasową na żądanie w usłudze Azure Stack, przy użyciu kopii zapasowej na miejscu. Aby uzyskać instrukcje dotyczące konfigurowania środowiska PowerShell, zobacz [Instalowanie programu PowerShell dla usługi Azure Stack ](azure-stack-powershell-install.md). Aby zalogować się do usługi Azure Stack, zobacz [korzystanie z portalu administratora w usłudze Azure Stack](azure-stack-manage-portals.md).

## <a name="start-azure-stack-backup"></a>Rozpocznij wykonywanie kopii zapasowej usługi Azure Stack

### <a name="start-a-new-backup-without-job-progress-tracking"></a>Uruchom nową kopię zapasową bez śledzenia postępu zadań
Użyj Start AzSBackup, aby natychmiast rozpocząć nową kopię zapasową nie śledzenia postępu zadań.

```powershell
   Start-AzsBackup -Force
```

### <a name="start-azure-stack-backup-with-job-progress-tracking"></a>Kopia zapasowa Azure Stack rozpoczynać się śledzenia postępu zadań
Użyj Start AzSBackup, aby uruchomić nową kopię zapasową za pomocą zmiennej - AsJob do śledzenia postępu zadania tworzenia kopii zapasowej.

```powershell
    $backupjob = Start-AzsBackup -Force -AsJob 
    "Start time: " + $backupjob.PSBeginTime;While($backupjob.State -eq "Running"){("Job is currently: " `
    + $backupjob.State+" ;Duration: " + (New-TimeSpan -Start ($backupjob.PSBeginTime) `
    -End (Get-Date)).Minutes);Start-Sleep -Seconds 30};$backupjob.Output

    if($backupjob.State -eq "Completed"){Get-AzsBackup | where {$_.BackupId -eq $backupjob.Output.BackupId}}
```

## <a name="confirm-backup-has-completed"></a>Upewnij się, że kopii zapasowej zostało ukończone

### <a name="confirm-backup-has-completed-using-powershell"></a>Upewnij się, że kopii zapasowej zostało ukończone, przy użyciu programu PowerShell
Aby upewnić się, że tej kopii zapasowej zakończyła się pomyślnie, należy użyć następujących poleceń programu PowerShell:

```powershell
   Get-AzsBackup
```

Wynik powinien wyglądać następujące dane wyjściowe:

```powershell
    BackupDataVersion : 1.0.1
    BackupId          : <backup ID>
    RoleStatus        : {NRP, SRP, CRP, KeyVaultInternalControlPlane...}
    Status            : Succeeded
    CreatedDateTime   : 7/6/2018 6:46:24 AM
    TimeTakenToCreate : PT20M32.364138S
    DeploymentID      : <deployment ID>
    StampVersion      : 1.1807.0.41
    OemVersion        : 
    Id                : /subscriptions/<subscription ID>/resourceGroups/System.local/providers/Microsoft.Backup.Admin/backupLocations/local/backups/<backup ID>
    Name              : local/<local name>
    Type              : Microsoft.Backup.Admin/backupLocations/backups
    Location          : local
    Tags              : {}
```

### <a name="confirm-backup-has-completed-in-the-administration-portal"></a>Upewnij się, że kopii zapasowej zostało ukończone w portalu administracyjnym
Użyj portalu administracyjnego usługi Azure Stack, aby upewnij się, że tej kopii zapasowej została ukończona pomyślnie, wykonując następujące czynności:

1. Otwórz [portalu administratora usługi Azure Stack](azure-stack-manage-portals.md).
2. Wybierz **wszystkich usług**, a następnie w obszarze **administracji** wybierz kategorię > **tworzenie kopii zapasowych**. Wybierz **konfiguracji** w **tworzenie kopii zapasowych** bloku.
3. Znajdź **nazwa** i **Data ukończenia** kopii zapasowej w **dostępnych kopii zapasowych** listy.
4. Sprawdź **stanu** jest **Powodzenie**.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat przepływu pracy do odzyskiwania od zdarzenia utraty danych. Zobacz [sprawności po utracie danych w wyniku katastrofy](azure-stack-backup-recover-data.md).
