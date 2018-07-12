---
title: Tworzenie kopii zapasowej usługi Azure Stack | Dokumentacja firmy Microsoft
description: Wykonaj kopię zapasową na żądanie w usłudze Azure Stack, przy użyciu kopii zapasowej na miejscu.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/08/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 2570423a3cae2a15f0cfd294f1d91e6730748f68
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972607"
---
# <a name="back-up-azure-stack"></a>Tworzenie kopii zapasowych usługi Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Wykonaj kopię zapasową na żądanie w usłudze Azure Stack, przy użyciu kopii zapasowej na miejscu. Aby uzyskać instrukcje dotyczące konfigurowania środowiska PowerShell, zobacz [Instalowanie programu PowerShell dla usługi Azure Stack ](azure-stack-powershell-install.md). Aby zalogować się do usługi Azure Stack, zobacz [skonfigurować środowisko operatora i zaloguj się do usługi Azure Stack](azure-stack-powershell-configure-admin.md).

## <a name="start-azure-stack-backup"></a>Rozpocznij wykonywanie kopii zapasowej usługi Azure Stack

Użyj Start AzSBackup, aby uruchomić nową kopię zapasową za pomocą zmiennej - AsJob do śledzenia postępu. 

```powershell
    $backupjob = Start-AzsBackup -Force -AsJob
    "Start time: " + $backupjob.PSBeginTime;While($backupjob.State -eq "Running"){("Job is currently: " + $backupjob.State+" ;Duration: " + (New-TimeSpan -Start ($backupjob.PSBeginTime) -End (Get-Date)).Minutes);Start-Sleep -Seconds 30};$backupjob.Output
```

## <a name="confirm-backup-completed-via-powershell"></a>Upewnij się, kopia zapasowa została wykonana za pomocą programu PowerShell

```powershell
    if($backupjob.State -eq "Completed"){Get-AzsBackup | where {$_.BackupId -eq $backupjob.Output.BackupId}}
```

- Wynik powinien wyglądać następujące dane wyjściowe:

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

## <a name="confirm-backup-completed-in-the-administration-portal"></a>Upewnij się, kopia zapasowa została wykonana w portalu administracyjnym

1. Otwórz w portalu administratora usługi Azure Stack w [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).
2. Wybierz **więcej usług** > **tworzenie kopii zapasowych**. Wybierz **konfiguracji** w **tworzenie kopii zapasowych** bloku.
3. Znajdź **nazwa** i **Data ukończenia** kopii zapasowej w **dostępnych kopii zapasowych** listy.
4. Sprawdź **stanu** jest **Powodzenie**.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej na temat przepływu pracy do odzyskiwania od zdarzenia utraty danych. Zobacz [sprawności po utracie danych w wyniku katastrofy](azure-stack-backup-recover-data.md).
