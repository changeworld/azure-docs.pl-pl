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
ms.openlocfilehash: 08335f676437a32aa2240298be4680633eff16ba
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432974"
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
Użyj Start AzSBackup, aby uruchomić nową kopię zapasową za pomocą **- AsJob** parametru i zapisz go jako zmienną, aby śledzić postępy zadania tworzenia kopii zapasowej.

> [!NOTE]
> Zadanie tworzenia kopii zapasowej będą wyświetlane jako pomyślnie ukończone w portalu około 10 – 15 minut, zanim zostaną ukończone zadania.
>
> W związku z tym stanu rzeczywistego lepiej obserwuje się za pomocą poniższego kodu.

> [!IMPORTANT]
> Początkowa 1 Opóźnienie milisekund został wprowadzony, ponieważ kod jest zbyt szybkie poprawnie zarejestrować zadania i będzie działać bez **PSBeginTime** i z kolei bez **stanu** zadania.

```powershell
    $BackupJob = Start-AzsBackup -Force -AsJob
    While (!$BackupJob.PSBeginTime) {
        Start-Sleep -Milliseconds 1
    }
    Write-Host "Start time: $($BackupJob.PSBeginTime)"
    While ($BackupJob.State -eq "Running") {
        Write-Host "Job is currently: $($BackupJob.State) - Duration: $((New-TimeSpan -Start ($BackupJob.PSBeginTime) -End (Get-Date)).ToString().Split(".")[0])"
        Start-Sleep -Seconds 30
    }

    If ($BackupJob.State -eq "Completed") {
        Get-AzsBackup | Where-Object {$_.BackupId -eq $BackupJob.Output.BackupId}
        $Duration = $BackupJob.Output.TimeTakenToCreate
        $Pattern = '^P?T?((?<Years>\d+)Y)?((?<Months>\d+)M)?((?<Weeks>\d+)W)?((?<Days>\d+)D)?(T((?<Hours>\d+)H)?((?<Minutes>\d+)M)?((?<Seconds>\d*(\.)?\d*)S)?)$'
        If ($Duration -match $Pattern) {
            If (!$Matches.ContainsKey("Hours")) {
                $Hours = ""
            } 
            Else {
                $Hours = ($Matches.Hours).ToString + 'h '
            }
            $Minutes = ($Matches.Minutes)
            $Seconds = [math]::round(($Matches.Seconds))
            $Runtime = '{0}{1:00}m {2:00}s' -f $Hours, $Minutes, $Seconds
        }
        Write-Host "BackupJob: $($BackupJob.Output.BackupId) - Completed with Status: $($BackupJob.Output.Status) - It took: $($Runtime) to run" -ForegroundColor Green
    }
    ElseIf ($BackupJob.State -ne "Completed") {
        $BackupJob
        $BackupJob.Output
    }
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
